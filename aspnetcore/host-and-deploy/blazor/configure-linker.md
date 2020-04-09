---
title: Configure o Linker para ASP.NET CoreBlazor
author: guardrex
description: Aprenda a controlar o Linker de Língua Blazor Intermediária (IL) ao construir um aplicativo.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 109da5ef400c3b9d64ccf3ceb33a5387ea6b5618
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218655"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Configurar o vinculador para o Blazor do ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

O Blazor WebAssembly executa o link [de Idioma Intermediário (IL)](/dotnet/standard/managed-code#intermediate-language--execution) durante uma compilação para cortar il desnecessário dos conjuntos de saída do aplicativo. O linker é desativado ao construir na configuração Debug. Os aplicativos devem ser configurados na configuração de versão para ativar o linker. Recomendamos a construção em Release ao implantar seus aplicativos Blazor WebAssembly. 

Vincular um aplicativo otimiza para o tamanho, mas pode ter efeitos prejudiciais. Aplicativos que usam reflexão ou recursos dinâmicos relacionados podem quebrar quando cortados porque o linker não sabe sobre esse comportamento dinâmico e não pode determinar em geral quais tipos são necessários para reflexão em tempo de execução. Para aparar tais aplicativos, o linker deve ser informado sobre quaisquer tipos necessários por reflexão no código e em pacotes ou frameworks dos quais o aplicativo dependa. 

Para garantir que o aplicativo aparado funcione corretamente uma vez implantado, é importante testar compilações de versão do aplicativo com freqüência durante o desenvolvimento.

O link para aplicativos Blazor pode ser configurado usando estes recursos do MSBuild:

* Configure o link globalmente com uma [propriedade MSBuild](#control-linking-with-an-msbuild-property).
* Controle a vinculação em uma base por conjunto com um [arquivo de configuração](#control-linking-with-a-configuration-file).

## <a name="control-linking-with-an-msbuild-property"></a>Controle a vinculação com uma propriedade MSBuild

O linking é ativado quando `Release` um aplicativo é incorporado na configuração. Para alterar isso, `BlazorWebAssemblyEnableLinking` configure a propriedade MSBuild no arquivo do projeto:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Controlar a vinculação com um arquivo de configuração

Controle a vinculação por assembly fornecendo um arquivo de configuração XML e especificando o arquivo como um item MSBuild no arquivo de projeto:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

*LinkerConfig.xml*:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

Para obter mais informações, consulte [exemplos de arquivos Link xml (repositório gitHub mono/linker)](https://github.com/mono/linker#link-xml-file-examples).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Adicione um arquivo de configuração do linker XML a uma biblioteca

Para configurar o linker de uma biblioteca específica, adicione um arquivo de configuração do linker XML na biblioteca como um recurso incorporado. O recurso incorporado deve ter o mesmo nome da montagem.

No exemplo a seguir, o arquivo *LinkerConfig.xml* é especificado como um recurso incorporado que tem o mesmo nome do conjunto da biblioteca:

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Configure o linker para internacionalização

Por padrão, a configuração de linker do Blazor para aplicativos Blazor WebAssembly elimina as informações de internacionalização, exceto para locais explicitamente solicitados. A remoção desses conjuntos minimiza o tamanho do aplicativo.

Para controlar quais conjuntos I18N são `<MonoLinkerI18NAssemblies>` retidos, defina a propriedade MSBuild no arquivo do projeto:

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| Valor da região     | Montagem da região mono    |
| ---------------- | ----------------------- |
| `all`            | Todas as assembléias incluídas |
| `cjk`            | *I18N.CJK.dll*          |
| `mideast`        | *I18N.MidEast.dll*      |
| `none` (padrão) | Nenhum                    |
| `other`          | *I18N.Other.dll*        |
| `rare`           | *I18N.Rare.dll*         |
| `west`           | *I18N.West.dll*         |

Use uma comma para separar múltiplos valores (por exemplo, `mideast,west`).

Para obter mais informações, consulte [I18N: Pnetlib Internationalization Framework Library (repositório mono/mono GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).

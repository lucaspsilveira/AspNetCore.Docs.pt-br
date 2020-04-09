---
title: SDK do Razor do ASP.NET Core
author: Rick-Anderson
description: Saiba como as Páginas Razor no ASP.NET Core tornam a codificação de cenários centrados em página mais fácil e mais produtiva do que com o uso de MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2284131ce2d45ec6bc01ce38f91e2c951b108605
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80321005"
---
# <a name="aspnet-core-razor-sdk"></a>SDK do Razor do ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Visão geral

O [!INCLUDE[](~/includes/2.1-SDK.md)] inclui o SDK do MSBuild `Microsoft.NET.Sdk.Razor` (SDK do Razor). O SDK do Razor:

::: moniker range=">= aspnetcore-3.0"

* É necessário construir, empacotar e publicar projetos contendo arquivos [Razor](xref:mvc/views/razor) para ASP.NET projetos baseados em MVC ou [Blazor.](xref:blazor/index)
* Inclui um conjunto de alvos, propriedades e itens predefinidos que permitem personalizar a compilação de arquivos Razor (*.cshtml* ou *.razor).*

O Razor SDK `Content` inclui itens `Include` com atributos definidos para os `**\*.cshtml` padrões e `**\*.razor` globbing. Arquivos correspondentes são publicados.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Padroniza a experiência de criação, empacotamento e publicação de projetos que contêm arquivos [Razor](xref:mvc/views/razor) para projetos baseados no ASP.NET Core MVC.
* Inclui um conjunto de destinos, propriedades e itens predefinidos que permitem personalizar a compilação de arquivos Razor.

O Razor SDK `Content` inclui um `Include` item com `**\*.cshtml` um atributo definido para o padrão globbing. Arquivos correspondentes são publicados.

::: moniker-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Use o Razor SDK

A maioria dos aplicativos web não são obrigados a referenciar explicitamente o Razor SDK.

::: moniker range=">= aspnetcore-3.0"

Para usar o Razor SDK para construir bibliotecas de classe contendo visualizações de navalha ou páginas de barbear, recomendamos começar com o modelo de projeto da biblioteca de classe Razor (RCL). Uma RCL usada para construir arquivos Blazor *(.razor)* requer minimamente uma referência ao pacote [Microsoft.AspNetCore.Components.](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) Uma RCL que é usada para construir visualizações ou páginas do Razor `netcoreapp3.0` (arquivos *.cshtml)* requer minimamente segmentação ou posterior e tem um `FrameworkReference` metapacote do [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) em seu arquivo de projeto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Para usar o SDK do Razor para criar bibliotecas de classe contendo exibições Razor ou Páginas Razor:

* Use `Microsoft.NET.Sdk.Razor` em vez de `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Normalmente, uma referência `Microsoft.AspNetCore.Mvc` de pacote é necessária para receber dependências adicionais que são necessárias para construir e compilar páginas de barbear e visualizações de navalha. No mínimo, seu projeto deve adicionar referências de pacote a:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  O `Microsoft.AspNetCore.Razor.Design` pacote fornece as tarefas de compilação da Navalha e os alvos para o projeto.

  Os pacotes anteriores são incluídos em `Microsoft.AspNetCore.Mvc`. A marcação a seguir mostra um arquivo de projeto que usa o Razor SDK para criar arquivos Razor para um aplicativo ASP.NET Core Razor Pages:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Os `Microsoft.AspNetCore.Razor.Design` `Microsoft.AspNetCore.Mvc.Razor.Extensions` pacotes estão incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). No entanto, `Microsoft.AspNetCore.App` a referência do pacote sem versão fornece um metapacote para o aplicativo que não inclui a versão mais recente de `Microsoft.AspNetCore.Razor.Design`. Os projetos devem fazer `Microsoft.AspNetCore.Razor.Design` referência `Microsoft.AspNetCore.Mvc`a uma versão consistente de (ou ) para que as correções de tempo de compilação mais recentes para Razor sejam incluídas. Para obter mais informações, consulte [este problema do GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Propriedades

As seguintes propriedades controlam o comportamento do SDK do Razor como parte de um build de projeto:

* `RazorCompileOnBuild`&ndash; Quando `true`, compila e emite a montagem da Navalha como parte da construção do projeto. Usa `true` como padrão.
* `RazorCompileOnPublish`&ndash; Quando `true`, compila e emite a montagem da Navalha como parte da publicação do projeto. Usa `true` como padrão.

As propriedades e itens na tabela a seguir são usados para configurar entradas e saída para o Razor SDK.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Começando com ASP.NET Core 3.0, MVC Views ou Razor `RazorCompileOnBuild` Pages `RazorCompileOnPublish` não são atendidos por padrão se as propriedades mSBuild ou MSBuild no arquivo do projeto estiverem desativadas. Os aplicativos devem adicionar uma referência explícita ao pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) se o aplicativo depender de compilação em tempo de execução para processar arquivos *.cshtml.*

::: moniker-end

| Itens | Descrição |
| ----- | ----------- |
| `RazorGenerate` | Elementos de itens (arquivos *.cshtml)* que são entradas para geração de código. |
| `RazorComponent` | Elementos de itens *(arquivos .razor)* que são entradas para a geração de código de componente razor. |
| `RazorCompile` | Elementos de itens *(arquivos .cs)* que são entradas para alvos de compilação razor. Use `ItemGroup` isso para especificar arquivos adicionais a serem compilados no conjunto Razor. |
| `RazorTargetAssemblyAttribute` | Os elementos de item usados para a codificação geram atributos para o assembly Razor. Por exemplo:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elementos de itens adicionados como recursos incorporados ao conjunto Razor gerado. |

::: moniker range=">= aspnetcore-3.0"

| Propriedade | Descrição |
| -------- | ----------- |
| `RazorTargetName` | Nome do arquivo (sem extensão) do assembly produzido pelo Razor. |
| `RazorOutputPath` | O diretório de saída do Razor. |
| `RazorCompileToolset` | Usado para determinar o conjunto de ferramentas usado para criar o assembly do Razor. Os valores válidos são `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | O padrão é `true`. When `true`, inclui *arquivos web.config*, *.json*e *.cshtml* como conteúdo no projeto. Quando referenciados `Microsoft.NET.Sdk.Web`via , arquivos em *wwwroot* e arquivos config também estão incluídos. |
| `EnableDefaultRazorGenerateItems` | Quando `true`, inclui arquivos *.cshtml* de itens de `Content` em itens de `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true`, gera um arquivo *.cs* contendo `RazorAssemblyAttribute` atributos especificados e inclui o arquivo na saída de compilação. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando `true`, adiciona um conjunto padrão de atributos de assembly em `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true`, `RazorGenerate` copia os arquivos *(.cshtml)* para o diretório de publicação. Normalmente, os arquivos Razor não são necessários para um aplicativo publicado se eles participam de compilação em tempo de compilação ou tempo de publicação. Usa `false` como padrão. |
| `PreserveCompilationReferences` | Quando `true`, copia os itens do assembly de referência no diretório de publicação. Normalmente, os conjuntos de referência não são necessários para um aplicativo publicado se a compilação razor ocorrer em tempo de compilação ou tempo de publicação. Defina `true` para se o aplicativo publicado exigir compilação em tempo de execução. Por exemplo, defina `true` o valor para se o aplicativo modificar arquivos *.cshtml* em tempo de execução ou usar visualizações incorporadas. Usa `false` como padrão. |
| `IncludeRazorContentInPack` | Quando `true`, todos os itens de conteúdo Razor (arquivos *.cshtml)* são marcados para inclusão no pacote NuGet gerado. Usa `false` como padrão. |
| `EmbedRazorGenerateSources` | Quando `true`, adiciona itens de RazorGenerate (*.cshtml*) como arquivos incorporados ao assembly Razor gerado. Usa `false` como padrão. |
| `UseRazorBuildServer` | Quando `true`, usa um processo de servidor de build persistente para descarregar o trabalho de geração de código. Seu valor padrão é `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true`, o SDK gera atributos adicionais usados pelo MVC em tempo de execução para executar a detecção de peças do aplicativo. |
| `DefaultWebContentItemExcludes` | Um padrão globbing para elementos de itens `Content` que devem ser excluídos do grupo de itens em projetos direcionados à Web ou Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | Quando `true`, *arquivos .config* e *.json* não são copiados para o diretório de saída de compilação. |
| `AddRazorSupportForMvc` | Quando `true`, configura o Razor SDK para adicionar suporte à configuração MVC necessária ao criar aplicativos contendo exibições de MVC ou páginas de barbear. Esta propriedade está implicitamente definida para projetos .NET Core 3.0 ou posteriores direcionados ao Web SDK |
| `RazorLangVersion` | A versão da Linguagem da Navalha para o alvo. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Propriedade | Descrição |
| -------- | ----------- |
| `RazorTargetName` | Nome do arquivo (sem extensão) do assembly produzido pelo Razor. |
| `RazorOutputPath` | O diretório de saída do Razor. |
| `RazorCompileToolset` | Usado para determinar o conjunto de ferramentas usado para criar o assembly do Razor. Os valores válidos são `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | O padrão é `true`. When `true`, inclui *arquivos web.config*, *.json*e *.cshtml* como conteúdo no projeto. Quando referenciados `Microsoft.NET.Sdk.Web`via , arquivos em *wwwroot* e arquivos config também estão incluídos. |
| `EnableDefaultRazorGenerateItems` | Quando `true`, inclui arquivos *.cshtml* de itens de `Content` em itens de `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true`, gera um arquivo *.cs* contendo `RazorAssemblyAttribute` atributos especificados e inclui o arquivo na saída de compilação. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando `true`, adiciona um conjunto padrão de atributos de assembly em `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true`, `RazorGenerate` copia os arquivos *(.cshtml)* para o diretório de publicação. Normalmente, os arquivos Razor não são necessários para um aplicativo publicado se eles participam de compilação em tempo de compilação ou tempo de publicação. Usa `false` como padrão. |
| `CopyRefAssembliesToPublishDirectory` | Quando `true`, copia os itens do assembly de referência no diretório de publicação. Normalmente, os conjuntos de referência não são necessários para um aplicativo publicado se a compilação razor ocorrer em tempo de compilação ou tempo de publicação. Defina `true` para se o aplicativo publicado exigir compilação em tempo de execução. Por exemplo, defina `true` o valor para se o aplicativo modificar arquivos *.cshtml* em tempo de execução ou usar visualizações incorporadas. Usa `false` como padrão. |
| `IncludeRazorContentInPack` | Quando `true`, todos os itens de conteúdo Razor (arquivos *.cshtml)* são marcados para inclusão no pacote NuGet gerado. Usa `false` como padrão. |
| `EmbedRazorGenerateSources` | Quando `true`, adiciona itens de RazorGenerate (*.cshtml*) como arquivos incorporados ao assembly Razor gerado. Usa `false` como padrão. |
| `UseRazorBuildServer` | Quando `true`, usa um processo de servidor de build persistente para descarregar o trabalho de geração de código. Seu valor padrão é `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true`, o SDK gera atributos adicionais usados pelo MVC em tempo de execução para executar a detecção de peças do aplicativo. |
| `DefaultWebContentItemExcludes` | Um padrão globbing para elementos de itens `Content` que devem ser excluídos do grupo de itens em projetos direcionados à Web ou Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | Quando `true`, *arquivos .config* e *.json* não são copiados para o diretório de saída de compilação. |
| `AddRazorSupportForMvc` | Quando `true`, configura o Razor SDK para adicionar suporte à configuração MVC necessária ao criar aplicativos contendo exibições de MVC ou páginas de barbear. Esta propriedade está implicitamente definida para projetos .NET Core 3.0 ou posteriores direcionados ao Web SDK |
| `RazorLangVersion` | A versão da Linguagem da Navalha para o alvo. |

::: moniker-end

Para saber mais sobre as propriedades, confira [Propriedades do MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Destinos

O SDK do Razor define dois destinos primários:

* `RazorGenerate`&ndash; O código gera arquivos `RazorGenerate` *.cs* a partir de elementos do item. Use `RazorGenerateDependsOn` a propriedade para especificar alvos adicionais que podem ser executados antes ou depois deste destino.
* `RazorCompile`&ndash; Compila arquivos *.cs gerados* em um conjunto de Navalha. Use `RazorCompileDependsOn` o para especificar alvos adicionais que podem ser executados antes ou depois deste destino.
* `RazorComponentGenerate`&ndash; O código gera arquivos `RazorComponent` *.cs* para elementos do item. Use `RazorComponentGenerateDependsOn` a propriedade para especificar alvos adicionais que podem ser executados antes ou depois deste destino.

### <a name="runtime-compilation-of-razor-views"></a>Compilação de runtime de modos de exibição do Razor

* Por padrão, o SDK do Razor não publica assemblies de referência que são necessários para realizar compilação no runtime. Isso resulta em falhas de compilação quando o modelo de aplicativo se baseia na compilação em runtime&mdash;, por exemplo, o aplicativo usa exibições inseridas ou muda as exibições depois que o aplicativo é publicado. Defina `CopyRefAssembliesToPublishDirectory` como `true` para continuar publicando assemblies de referência.

* Para um aplicativo web, certifique-se `Microsoft.NET.Sdk.Web` de que seu aplicativo está mirando o SDK.

## <a name="razor-language-version"></a>Versão em linguagem de navalha

Ao direcionar `Microsoft.NET.Sdk.Web` o SDK, a versão em idioma Razor é inferida a partir da versão de framework de destino do aplicativo. Para projetos direcionados ao `Microsoft.NET.Sdk.Razor` SDK ou no caso raro de que o aplicativo requer uma versão em `<RazorLangVersion>` idioma Razor diferente do valor inferido, uma versão pode ser configurada definindo a propriedade no arquivo de projeto do aplicativo:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

A versão em idioma de Razor é fortemente integrada com a versão do tempo de execução para o que foi construído. Direcionar uma versão de idioma que não foi projetada para o tempo de execução não é suportado e provavelmente produz erros de compilação.

## <a name="additional-resources"></a>Recursos adicionais

* [Adições ao formato csproj para .NET Core](/dotnet/core/tools/csproj)
* [Itens comuns do projeto MSBuild](/visualstudio/msbuild/common-msbuild-project-items)

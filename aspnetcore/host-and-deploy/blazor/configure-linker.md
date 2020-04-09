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
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="3baed-103">Configurar o vinculador para o Blazor do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3baed-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="3baed-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3baed-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="3baed-105">O Blazor WebAssembly executa o link [de Idioma Intermediário (IL)](/dotnet/standard/managed-code#intermediate-language--execution) durante uma compilação para cortar il desnecessário dos conjuntos de saída do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3baed-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="3baed-106">O linker é desativado ao construir na configuração Debug.</span><span class="sxs-lookup"><span data-stu-id="3baed-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="3baed-107">Os aplicativos devem ser configurados na configuração de versão para ativar o linker.</span><span class="sxs-lookup"><span data-stu-id="3baed-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="3baed-108">Recomendamos a construção em Release ao implantar seus aplicativos Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="3baed-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="3baed-109">Vincular um aplicativo otimiza para o tamanho, mas pode ter efeitos prejudiciais.</span><span class="sxs-lookup"><span data-stu-id="3baed-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="3baed-110">Aplicativos que usam reflexão ou recursos dinâmicos relacionados podem quebrar quando cortados porque o linker não sabe sobre esse comportamento dinâmico e não pode determinar em geral quais tipos são necessários para reflexão em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="3baed-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="3baed-111">Para aparar tais aplicativos, o linker deve ser informado sobre quaisquer tipos necessários por reflexão no código e em pacotes ou frameworks dos quais o aplicativo dependa.</span><span class="sxs-lookup"><span data-stu-id="3baed-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="3baed-112">Para garantir que o aplicativo aparado funcione corretamente uma vez implantado, é importante testar compilações de versão do aplicativo com freqüência durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="3baed-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="3baed-113">O link para aplicativos Blazor pode ser configurado usando estes recursos do MSBuild:</span><span class="sxs-lookup"><span data-stu-id="3baed-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="3baed-114">Configure o link globalmente com uma [propriedade MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="3baed-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="3baed-115">Controle a vinculação em uma base por conjunto com um [arquivo de configuração](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="3baed-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="3baed-116">Controle a vinculação com uma propriedade MSBuild</span><span class="sxs-lookup"><span data-stu-id="3baed-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="3baed-117">O linking é ativado quando `Release` um aplicativo é incorporado na configuração.</span><span class="sxs-lookup"><span data-stu-id="3baed-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="3baed-118">Para alterar isso, `BlazorWebAssemblyEnableLinking` configure a propriedade MSBuild no arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="3baed-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="3baed-119">Controlar a vinculação com um arquivo de configuração</span><span class="sxs-lookup"><span data-stu-id="3baed-119">Control linking with a configuration file</span></span>

<span data-ttu-id="3baed-120">Controle a vinculação por assembly fornecendo um arquivo de configuração XML e especificando o arquivo como um item MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="3baed-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="3baed-121">*LinkerConfig.xml*:</span><span class="sxs-lookup"><span data-stu-id="3baed-121">*LinkerConfig.xml*:</span></span>

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

<span data-ttu-id="3baed-122">Para obter mais informações, consulte [exemplos de arquivos Link xml (repositório gitHub mono/linker)](https://github.com/mono/linker#link-xml-file-examples).</span><span class="sxs-lookup"><span data-stu-id="3baed-122">For more information, see [Link xml file examples (mono/linker GitHub repository)](https://github.com/mono/linker#link-xml-file-examples).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="3baed-123">Adicione um arquivo de configuração do linker XML a uma biblioteca</span><span class="sxs-lookup"><span data-stu-id="3baed-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="3baed-124">Para configurar o linker de uma biblioteca específica, adicione um arquivo de configuração do linker XML na biblioteca como um recurso incorporado.</span><span class="sxs-lookup"><span data-stu-id="3baed-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="3baed-125">O recurso incorporado deve ter o mesmo nome da montagem.</span><span class="sxs-lookup"><span data-stu-id="3baed-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="3baed-126">No exemplo a seguir, o arquivo *LinkerConfig.xml* é especificado como um recurso incorporado que tem o mesmo nome do conjunto da biblioteca:</span><span class="sxs-lookup"><span data-stu-id="3baed-126">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="3baed-127">Configure o linker para internacionalização</span><span class="sxs-lookup"><span data-stu-id="3baed-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="3baed-128">Por padrão, a configuração de linker do Blazor para aplicativos Blazor WebAssembly elimina as informações de internacionalização, exceto para locais explicitamente solicitados.</span><span class="sxs-lookup"><span data-stu-id="3baed-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="3baed-129">A remoção desses conjuntos minimiza o tamanho do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3baed-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="3baed-130">Para controlar quais conjuntos I18N são `<MonoLinkerI18NAssemblies>` retidos, defina a propriedade MSBuild no arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="3baed-130">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="3baed-131">Valor da região</span><span class="sxs-lookup"><span data-stu-id="3baed-131">Region Value</span></span>     | <span data-ttu-id="3baed-132">Montagem da região mono</span><span class="sxs-lookup"><span data-stu-id="3baed-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="3baed-133">Todas as assembléias incluídas</span><span class="sxs-lookup"><span data-stu-id="3baed-133">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="3baed-134">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="3baed-134">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="3baed-135">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="3baed-135">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="3baed-136">`none` (padrão)</span><span class="sxs-lookup"><span data-stu-id="3baed-136">`none` (default)</span></span> | <span data-ttu-id="3baed-137">Nenhum</span><span class="sxs-lookup"><span data-stu-id="3baed-137">None</span></span>                    |
| `other`          | <span data-ttu-id="3baed-138">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="3baed-138">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="3baed-139">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="3baed-139">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="3baed-140">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="3baed-140">*I18N.West.dll*</span></span>         |

<span data-ttu-id="3baed-141">Use uma comma para separar múltiplos valores (por exemplo, `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="3baed-141">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="3baed-142">Para obter mais informações, consulte [I18N: Pnetlib Internationalization Framework Library (repositório mono/mono GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="3baed-142">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

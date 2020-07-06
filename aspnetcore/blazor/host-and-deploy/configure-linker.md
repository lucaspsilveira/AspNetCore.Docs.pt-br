---
title: Configurar o vinculador para ASP.NET CoreBlazor
author: guardrex
description: Saiba como controlar o vinculador de linguagem intermediária (IL) ao criar um Blazor aplicativo.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: 568efe9971aefc11841c42789ac7f2af3004003f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402696"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="fdbd9-103">Configurar o vinculador para ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="fdbd9-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="fdbd9-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fdbd9-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor WebAssembly<span data-ttu-id="fdbd9-105">executa a vinculação de [Il (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) durante uma compilação para cortar o Il desnecessário dos assemblies de saída do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fdbd9-105"> performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="fdbd9-106">O vinculador está desabilitado ao compilar na configuração de depuração.</span><span class="sxs-lookup"><span data-stu-id="fdbd9-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="fdbd9-107">Os aplicativos devem compilar na configuração de versão para habilitar o vinculador.</span><span class="sxs-lookup"><span data-stu-id="fdbd9-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="fdbd9-108">É recomendável criar em versão ao implantar seus Blazor WebAssembly aplicativos.</span><span class="sxs-lookup"><span data-stu-id="fdbd9-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="fdbd9-109">A vinculação de um aplicativo otimiza o tamanho, mas pode ter efeitos prejudiciais.</span><span class="sxs-lookup"><span data-stu-id="fdbd9-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="fdbd9-110">Os aplicativos que usam reflexão ou recursos dinâmicos relacionados podem interromper quando cortados porque o vinculador não conhece esse comportamento dinâmico e não pode determinar em geral quais tipos são necessários para reflexão em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="fdbd9-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="fdbd9-111">Para cortar esses aplicativos, o vinculador deve ser informado sobre quaisquer tipos exigidos pela reflexão no código e em pacotes ou estruturas das quais o aplicativo depende.</span><span class="sxs-lookup"><span data-stu-id="fdbd9-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="fdbd9-112">Para garantir que o aplicativo cortado funcione corretamente depois de implantado, é importante testar as compilações de versão do aplicativo com frequência durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="fdbd9-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="fdbd9-113">A vinculação de Blazor aplicativos pode ser configurada usando estes recursos do MSBuild:</span><span class="sxs-lookup"><span data-stu-id="fdbd9-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="fdbd9-114">Configure o vínculo global com uma [Propriedade do MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="fdbd9-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="fdbd9-115">Controle a vinculação em uma base por assembly com um [arquivo de configuração](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="fdbd9-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="fdbd9-116">Vinculação de controle com uma propriedade do MSBuild</span><span class="sxs-lookup"><span data-stu-id="fdbd9-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="fdbd9-117">A vinculação é habilitada quando um aplicativo é criado na `Release` configuração.</span><span class="sxs-lookup"><span data-stu-id="fdbd9-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="fdbd9-118">Para alterar isso, configure a `BlazorWebAssemblyEnableLinking` Propriedade MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="fdbd9-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="fdbd9-119">Controlar a vinculação com um arquivo de configuração</span><span class="sxs-lookup"><span data-stu-id="fdbd9-119">Control linking with a configuration file</span></span>

<span data-ttu-id="fdbd9-120">Controle a vinculação por assembly fornecendo um arquivo de configuração XML e especificando o arquivo como um item MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="fdbd9-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="fdbd9-121">`LinkerConfig.xml`:</span><span class="sxs-lookup"><span data-stu-id="fdbd9-121">`LinkerConfig.xml`:</span></span>

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

<span data-ttu-id="fdbd9-122">Para obter mais informações e exemplos, consulte [formatos de dados (repositório do GitHub mono/Linker)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="fdbd9-122">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="fdbd9-123">Adicionar um arquivo de configuração do vinculador XML a uma biblioteca</span><span class="sxs-lookup"><span data-stu-id="fdbd9-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="fdbd9-124">Para configurar o vinculador para uma biblioteca específica, adicione um arquivo de configuração do vinculador XML à biblioteca como um recurso incorporado.</span><span class="sxs-lookup"><span data-stu-id="fdbd9-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="fdbd9-125">O recurso inserido deve ter o mesmo nome que o assembly.</span><span class="sxs-lookup"><span data-stu-id="fdbd9-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="fdbd9-126">No exemplo a seguir, o `LinkerConfig.xml` arquivo é especificado como um recurso inserido que tem o mesmo nome que o assembly da biblioteca:</span><span class="sxs-lookup"><span data-stu-id="fdbd9-126">In the following example, the `LinkerConfig.xml` file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="fdbd9-127">Configurar o vinculador para internacionalização</span><span class="sxs-lookup"><span data-stu-id="fdbd9-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="fdbd9-128">Por padrão, a Blazor configuração do vinculador para Blazor WebAssembly aplicativos retira informações de internacionalização, exceto pelas localidades explicitamente solicitadas.</span><span class="sxs-lookup"><span data-stu-id="fdbd9-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="fdbd9-129">Remover esses assemblies minimiza o tamanho do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fdbd9-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="fdbd9-130">Para controlar quais assemblies de I18N são retidos, defina a `<BlazorWebAssemblyI18NAssemblies>` Propriedade MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="fdbd9-130">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="fdbd9-131">Valor da região</span><span class="sxs-lookup"><span data-stu-id="fdbd9-131">Region Value</span></span>     | <span data-ttu-id="fdbd9-132">Assembly de região mono</span><span class="sxs-lookup"><span data-stu-id="fdbd9-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="fdbd9-133">Todos os assemblies incluídos</span><span class="sxs-lookup"><span data-stu-id="fdbd9-133">All assemblies included</span></span> |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| <span data-ttu-id="fdbd9-134">`none` (padrão)</span><span class="sxs-lookup"><span data-stu-id="fdbd9-134">`none` (default)</span></span> | <span data-ttu-id="fdbd9-135">Nenhum</span><span class="sxs-lookup"><span data-stu-id="fdbd9-135">None</span></span>                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

<span data-ttu-id="fdbd9-136">Use uma vírgula para separar vários valores (por exemplo, `mideast,west` ).</span><span class="sxs-lookup"><span data-stu-id="fdbd9-136">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="fdbd9-137">Para obter mais informações, consulte [i18n: Pnetlib Internacionalization Framework library (repositório do GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="fdbd9-137">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fdbd9-138">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fdbd9-138">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
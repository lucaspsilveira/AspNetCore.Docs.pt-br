---
<span data-ttu-id="8a0b6-101">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-101">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-103">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-104">'Identity'</span></span>
- <span data-ttu-id="8a0b6-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-106">'Razor'</span></span>
- <span data-ttu-id="8a0b6-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-107">'SignalR' uid:</span></span> 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="8a0b6-108">Configurar o vinculador para ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="8a0b6-108">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="8a0b6-109">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8a0b6-109">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="8a0b6-110">O Webassembly executa a vinculação de [linguagem intermediária (Il)](/dotnet/standard/managed-code#intermediate-language--execution) durante uma compilação para cortar o Il desnecessário dos assemblies de saída do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a0b6-110"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="8a0b6-111">O vinculador está desabilitado ao compilar na configuração de depuração.</span><span class="sxs-lookup"><span data-stu-id="8a0b6-111">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="8a0b6-112">Os aplicativos devem compilar na configuração de versão para habilitar o vinculador.</span><span class="sxs-lookup"><span data-stu-id="8a0b6-112">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="8a0b6-113">É recomendável criar em versão ao implantar seus Blazor aplicativos Webassembly.</span><span class="sxs-lookup"><span data-stu-id="8a0b6-113">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="8a0b6-114">A vinculação de um aplicativo otimiza o tamanho, mas pode ter efeitos prejudiciais.</span><span class="sxs-lookup"><span data-stu-id="8a0b6-114">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="8a0b6-115">Os aplicativos que usam reflexão ou recursos dinâmicos relacionados podem interromper quando cortados porque o vinculador não conhece esse comportamento dinâmico e não pode determinar em geral quais tipos são necessários para reflexão em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="8a0b6-115">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="8a0b6-116">Para cortar esses aplicativos, o vinculador deve ser informado sobre quaisquer tipos exigidos pela reflexão no código e em pacotes ou estruturas das quais o aplicativo depende.</span><span class="sxs-lookup"><span data-stu-id="8a0b6-116">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="8a0b6-117">Para garantir que o aplicativo cortado funcione corretamente depois de implantado, é importante testar as compilações de versão do aplicativo com frequência durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a0b6-117">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="8a0b6-118">A vinculação de Blazor aplicativos pode ser configurada usando estes recursos do MSBuild:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-118">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="8a0b6-119">Configure o vínculo global com uma [Propriedade do MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="8a0b6-119">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="8a0b6-120">Controle a vinculação em uma base por assembly com um [arquivo de configuração](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="8a0b6-120">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="8a0b6-121">Vinculação de controle com uma propriedade do MSBuild</span><span class="sxs-lookup"><span data-stu-id="8a0b6-121">Control linking with an MSBuild property</span></span>

<span data-ttu-id="8a0b6-122">A vinculação é habilitada quando um aplicativo é criado na `Release` configuração.</span><span class="sxs-lookup"><span data-stu-id="8a0b6-122">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="8a0b6-123">Para alterar isso, configure a `BlazorWebAssemblyEnableLinking` Propriedade MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-123">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="8a0b6-124">Controlar a vinculação com um arquivo de configuração</span><span class="sxs-lookup"><span data-stu-id="8a0b6-124">Control linking with a configuration file</span></span>

<span data-ttu-id="8a0b6-125">Controle a vinculação por assembly fornecendo um arquivo de configuração XML e especificando o arquivo como um item MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-125">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="8a0b6-126">*LinkerConfig. xml*:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-126">*LinkerConfig.xml*:</span></span>

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

<span data-ttu-id="8a0b6-127">Para obter mais informações e exemplos, consulte [formatos de dados (repositório do GitHub mono/Linker)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="8a0b6-127">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="8a0b6-128">Adicionar um arquivo de configuração do vinculador XML a uma biblioteca</span><span class="sxs-lookup"><span data-stu-id="8a0b6-128">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="8a0b6-129">Para configurar o vinculador para uma biblioteca específica, adicione um arquivo de configuração do vinculador XML à biblioteca como um recurso incorporado.</span><span class="sxs-lookup"><span data-stu-id="8a0b6-129">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="8a0b6-130">O recurso inserido deve ter o mesmo nome que o assembly.</span><span class="sxs-lookup"><span data-stu-id="8a0b6-130">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="8a0b6-131">No exemplo a seguir, o arquivo *LinkerConfig. xml* é especificado como um recurso inserido que tem o mesmo nome que o assembly da biblioteca:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-131">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="8a0b6-132">Configurar o vinculador para internacionalização</span><span class="sxs-lookup"><span data-stu-id="8a0b6-132">Configure the linker for internationalization</span></span>

<span data-ttu-id="8a0b6-133">Por padrão, a Blazor configuração do vinculador para Blazor aplicativos Webassembly retira informações de internacionalização, exceto as localidades explicitamente solicitadas.</span><span class="sxs-lookup"><span data-stu-id="8a0b6-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="8a0b6-134">Remover esses assemblies minimiza o tamanho do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a0b6-134">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="8a0b6-135">Para controlar quais assemblies de I18N são retidos, defina a `<BlazorWebAssemblyI18NAssemblies>` Propriedade MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-135">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="8a0b6-136">Valor da região</span><span class="sxs-lookup"><span data-stu-id="8a0b6-136">Region Value</span></span>     | <span data-ttu-id="8a0b6-137">Assembly de região mono</span><span class="sxs-lookup"><span data-stu-id="8a0b6-137">Mono region assembly</span></span>    |
| ---
<span data-ttu-id="8a0b6-138">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-138">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-139">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-140">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-141">'Identity'</span></span>
- <span data-ttu-id="8a0b6-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-143">'Razor'</span></span>
- <span data-ttu-id="8a0b6-144">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-144">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a0b6-145">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-145">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-146">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-147">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-148">'Identity'</span></span>
- <span data-ttu-id="8a0b6-149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-149">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-150">'Razor'</span></span>
- <span data-ttu-id="8a0b6-151">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-151">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a0b6-152">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-152">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-153">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-154">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-155">'Identity'</span></span>
- <span data-ttu-id="8a0b6-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-157">'Razor'</span></span>
- <span data-ttu-id="8a0b6-158">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a0b6-159">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-159">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-160">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-161">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-162">'Identity'</span></span>
- <span data-ttu-id="8a0b6-163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-163">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-164">'Razor'</span></span>
- <span data-ttu-id="8a0b6-165">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a0b6-166">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-166">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-167">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-168">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-169">'Identity'</span></span>
- <span data-ttu-id="8a0b6-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-171">'Razor'</span></span>
- <span data-ttu-id="8a0b6-172">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a0b6-173">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-173">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-174">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-175">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-176">'Identity'</span></span>
- <span data-ttu-id="8a0b6-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-178">'Razor'</span></span>
- <span data-ttu-id="8a0b6-179">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-179">'SignalR' uid:</span></span> 

<span data-ttu-id="8a0b6-180">-------- | título do---: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-180">-------- | --- title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-181">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-182">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-183">'Identity'</span></span>
- <span data-ttu-id="8a0b6-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-185">'Razor'</span></span>
- <span data-ttu-id="8a0b6-186">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a0b6-187">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-187">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-188">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-189">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-190">'Identity'</span></span>
- <span data-ttu-id="8a0b6-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-192">'Razor'</span></span>
- <span data-ttu-id="8a0b6-193">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a0b6-194">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-194">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-195">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-196">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-197">'Identity'</span></span>
- <span data-ttu-id="8a0b6-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-199">'Razor'</span></span>
- <span data-ttu-id="8a0b6-200">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a0b6-201">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-201">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-202">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-203">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-204">'Identity'</span></span>
- <span data-ttu-id="8a0b6-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-206">'Razor'</span></span>
- <span data-ttu-id="8a0b6-207">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a0b6-208">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-208">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-209">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-210">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-211">'Identity'</span></span>
- <span data-ttu-id="8a0b6-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-213">'Razor'</span></span>
- <span data-ttu-id="8a0b6-214">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a0b6-215">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-215">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-216">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-217">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-218">'Identity'</span></span>
- <span data-ttu-id="8a0b6-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-220">'Razor'</span></span>
- <span data-ttu-id="8a0b6-221">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a0b6-222">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-222">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-223">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-224">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-225">'Identity'</span></span>
- <span data-ttu-id="8a0b6-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-227">'Razor'</span></span>
- <span data-ttu-id="8a0b6-228">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a0b6-229">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-229">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-230">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-231">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-232">'Identity'</span></span>
- <span data-ttu-id="8a0b6-233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-233">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-234">'Razor'</span></span>
- <span data-ttu-id="8a0b6-235">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a0b6-236">Título: ' configurar o vinculador para ASP.NET Core Blazor ' autor: Descrição: ' saiba como controlar o vinculador de linguagem intermediária (Il) ao criar um Blazor aplicativo. '</span><span class="sxs-lookup"><span data-stu-id="8a0b6-236">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="8a0b6-237">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a0b6-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a0b6-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-238">'Blazor'</span></span>
- <span data-ttu-id="8a0b6-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-239">'Identity'</span></span>
- <span data-ttu-id="8a0b6-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a0b6-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a0b6-241">'Razor'</span></span>
- <span data-ttu-id="8a0b6-242">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a0b6-242">'SignalR' uid:</span></span> 

<span data-ttu-id="8a0b6-243">------------ | | `all`            | Todos os assemblies incluídos | | `cjk`             |  *I18n. CJK. dll* | | `mideast`         |  *I18n. Oriente Médio. dll* | | `none`(padrão) | Nenhum | | `other`           |  *I18n. Other. dll* | | `rare`            |  *I18n. Raras. dll* | | `west`            |  *I18n. West. dll*         |</span><span class="sxs-lookup"><span data-stu-id="8a0b6-243">------------ | | `all`            | All assemblies included | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (default) | None                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span></span>

<span data-ttu-id="8a0b6-244">Use uma vírgula para separar vários valores (por exemplo, `mideast,west` ).</span><span class="sxs-lookup"><span data-stu-id="8a0b6-244">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="8a0b6-245">Para obter mais informações, consulte [i18n: Pnetlib Internacionalization Framework library (repositório do GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="8a0b6-245">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a0b6-246">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8a0b6-246">Additional resources</span></span>

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>

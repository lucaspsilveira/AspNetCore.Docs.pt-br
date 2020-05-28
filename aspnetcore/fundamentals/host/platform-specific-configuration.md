---
<span data-ttu-id="e5c6e-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e5c6e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e5c6e-102">'Blazor'</span></span>
- <span data-ttu-id="e5c6e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e5c6e-103">'Identity'</span></span>
- <span data-ttu-id="e5c6e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e5c6e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e5c6e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e5c6e-105">'Razor'</span></span>
- <span data-ttu-id="e5c6e-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="e5c6e-106">'SignalR' uid:</span></span> 

---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="e5c6e-107">Usar assemblies de inicialização de hospedagem no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e5c6e-107">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="e5c6e-108">Por [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="e5c6e-108">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e5c6e-109">Uma <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementação (inicialização de hospedagem) adiciona aprimoramentos a um aplicativo na inicialização por meio de um assembly externo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-109">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="e5c6e-110">Por exemplo, uma biblioteca externa pode usar uma implementação de inicialização de hospedagem para fornecer serviços ou provedores de configuração adicionais a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-110">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="e5c6e-111">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e5c6e-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="e5c6e-112">Atributo HostingStartup</span><span class="sxs-lookup"><span data-stu-id="e5c6e-112">HostingStartup attribute</span></span>

<span data-ttu-id="e5c6e-113">Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica a presença de um assembly de inicialização de hospedagem para ativar em runtime.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-113">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="e5c6e-114">O assembly de entrada ou o assembly que contém a classe `Startup` é automaticamente examinado para o atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-114">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e5c6e-115">A lista de assemblies a ser pesquisada para os atributos `HostingStartup` é carregada no runtime da configuração em [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-115">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="e5c6e-116">A lista de assemblies para excluir da descoberta é carregada de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-116">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="e5c6e-117">No exemplo a seguir, o namespace do assembly de inicialização de hospedagem é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-117">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="e5c6e-118">A classe que contém o código de inicialização de hospedagem é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-118">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e5c6e-119">O atributo `HostingStartup` normalmente está localizado no arquivo de classe de implementação `IHostingStartup` do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-119">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="e5c6e-120">Descobrir assemblies de inicialização de hospedagem carregados</span><span class="sxs-lookup"><span data-stu-id="e5c6e-120">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="e5c6e-121">Para descobrir os assemblies de inicialização de hospedagem carregados, habilite o registro em log e verifique os logs do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-121">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="e5c6e-122">Erros que ocorrem quando os assemblies carregados são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-122">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="e5c6e-123">Os assemblies de inicialização de hospedagem carregados são registrados em log no nível Depuração e todos os erros são registrados.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-123">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="e5c6e-124">Desabilitar o carregamento automático de assemblies de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="e5c6e-124">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="e5c6e-125">Para desabilitar o carregamento automático de assemblies de inicialização de hospedagem, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-125">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="e5c6e-126">Para impedir o carregamento de todos os assemblies de inicialização de hospedagem, defina o seguinte para `true` ou `1`:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-126">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="e5c6e-127">Impedir a configuração do host de inicialização de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-127">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="e5c6e-128">A variável de ambiente `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="e5c6e-129">Para evitar o carregamento de assemblies específicos de inicialização de hospedagem, defina uma das opções a seguir como uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para excluir na inicialização:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-129">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="e5c6e-130">Hospedando a definição de configuração de host de assemblies de inicialização:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-130">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="e5c6e-131">A variável de ambiente `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="e5c6e-132">Se a configuração do host e a variável de ambiente estiverem definidas, a configuração do host controlará o comportamento.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-132">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="e5c6e-133">A desabilitação de assemblies de inicialização de hospedagem usando a configuração do host ou a variável de ambiente desabilita o assembly globalmente e poderá desabilitar várias características de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-133">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="e5c6e-134">Project</span><span class="sxs-lookup"><span data-stu-id="e5c6e-134">Project</span></span>

<span data-ttu-id="e5c6e-135">Crie uma inicialização de hospedagem com qualquer um dos seguintes tipos de projeto:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-135">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="e5c6e-136">Biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="e5c6e-136">Class library</span></span>](#class-library)
* [<span data-ttu-id="e5c6e-137">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="e5c6e-137">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="e5c6e-138">Biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="e5c6e-138">Class library</span></span>

<span data-ttu-id="e5c6e-139">Uma melhoria da inicialização de hospedagem pode ser fornecida em uma biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-139">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="e5c6e-140">A biblioteca contém um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-140">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e5c6e-141">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclui um Razor aplicativo de páginas, *HostingStartupApp*e uma biblioteca de classes, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-141">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="e5c6e-142">A biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-142">The class library:</span></span>

* <span data-ttu-id="e5c6e-143">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-143">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e5c6e-144">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço à configuração do aplicativo usando o provedor de configuração na memória ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-144">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="e5c6e-145">Inclui um atributo `HostingStartup` que identifica o namespace e a classe de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-145">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="e5c6e-146">O `ServiceKeyInjection` método da classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> usa um <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para adicionar aprimoramentos a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-146">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="e5c6e-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e5c6e-148">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem da biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-148">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="e5c6e-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="e5c6e-150">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) também inclui um projeto de pacote do NuGet que fornece uma inicialização de hospedagem separada, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="e5c6e-151">O pacote tem as mesmas características da biblioteca de classes descrita anteriormente.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-151">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="e5c6e-152">O pacote:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-152">The package:</span></span>

* <span data-ttu-id="e5c6e-153">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-153">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e5c6e-154">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço para a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-154">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="e5c6e-155">Inclui um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-155">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e5c6e-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e5c6e-157">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem do pacote:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-157">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="e5c6e-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="e5c6e-159">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="e5c6e-159">Console app without an entry point</span></span>

<span data-ttu-id="e5c6e-160">*Essa abordagem só está disponível para aplicativos .NET Core, não para .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="e5c6e-160">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="e5c6e-161">Uma melhoria de inicialização de hospedagem dinâmica que não requer uma referência de tempo de compilação para a ativação pode ser fornecida em um aplicativo de console sem um ponto de entrada que contenha um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-161">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="e5c6e-162">Publicar o aplicativo de console produz um assembly de inicialização de hospedagem que pode ser consumido do repositório de runtime.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-162">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="e5c6e-163">Um aplicativo de console sem um ponto de entrada é usado nesse processo porque:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-163">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="e5c6e-164">Um arquivo de dependências é necessário para consumir a inicialização de hospedagem no assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-164">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="e5c6e-165">Um arquivo de dependências é um ativo de aplicativo executável que é produzido ao publicar um aplicativo, não uma biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-165">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="e5c6e-166">Uma biblioteca não pode ser adicionada diretamente ao [repositório de pacotes de runtime](/dotnet/core/deploying/runtime-store), o que exige um projeto executável que tem como alvo o runtime compartilhado.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-166">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="e5c6e-167">Na criação de uma inicialização de hospedagem dinâmica:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-167">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="e5c6e-168">Um assembly de inicialização de hospedagem é criado no aplicativo de console sem um ponto de entrada que:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-168">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="e5c6e-169">Inclui uma classe que contém a implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-169">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="e5c6e-170">Inclui um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) para identificar a classe de implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-170">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="e5c6e-171">O aplicativo de console é publicado para obter as dependências da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-171">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="e5c6e-172">Uma consequência de publicar o aplicativo de console é que as dependências não utilizadas são cortadas do arquivo de dependências.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-172">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="e5c6e-173">O arquivo de dependências é modificado para definir o local de runtime do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-173">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="e5c6e-174">O assembly de inicialização de hospedagem e seu arquivo de dependências são colocados no repositório do pacote de runtime.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-174">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="e5c6e-175">Para descobrir o assembly de inicialização de hospedagem e seu arquivo de dependências, eles são listados em um par de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-175">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="e5c6e-176">O assembly de aplicativo do console referencia o pacote [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="e5c6e-176">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="e5c6e-177">Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica uma classe como uma implementação de `IHostingStartup` para carregar e executar ao compilar o <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="e5c6e-177">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="e5c6e-178">No seguinte exemplo, o namespace é `StartupEnhancement` e a classe é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-178">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e5c6e-179">Uma classe implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-179">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="e5c6e-180">O método da classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> usa um <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para adicionar aprimoramentos a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-180">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="e5c6e-181">`IHostingStartup.Configure` no assembly de inicialização de hospedagem é chamado pelo runtime antes de `Startup.Configure` no código do usuário, o que permite que o código de usuário substitua qualquer configuração fornecida pelo assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-181">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="e5c6e-182">Ao criar um projeto `IHostingStartup`, o arquivo de dependências (*.deps.json*) define o local `runtime` do assembly como a pasta *bin*:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-182">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="e5c6e-183">Apenas uma parte do arquivo é mostrada.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-183">Only part of the file is shown.</span></span> <span data-ttu-id="e5c6e-184">O nome do assembly no exemplo é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-184">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="e5c6e-185">Configuração fornecida pela inicialização da hospedagem</span><span class="sxs-lookup"><span data-stu-id="e5c6e-185">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="e5c6e-186">Existem duas abordagens para lidar com a configuração, dependendo se você deseja que a configuração da inicialização da hospedagem tenha precedência ou que a configuração do aplicativo tenha precedência:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-186">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="e5c6e-187">Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> para carregar a configuração depois que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo forem executados.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-187">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e5c6e-188">A configuração de inicialização de hospedagem tem prioridade sobre a configuração do aplicativo que usa essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-188">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="e5c6e-189">Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> para carregar a configuração antes que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo sejam executados.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-189">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e5c6e-190">Os valores de configuração do aplicativo têm prioridade sobre aqueles fornecidos pela inicialização da hospedagem que usa essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-190">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="e5c6e-191">Especificar o assembly de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="e5c6e-191">Specify the hosting startup assembly</span></span>

<span data-ttu-id="e5c6e-192">Para uma biblioteca de classes ou inicialização de hospedagem fornecida pelo aplicativo de console, especifique o nome do assembly de inicialização de hospedagem na variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-192">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="e5c6e-193">A variável de ambiente é uma lista de assemblies delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-193">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="e5c6e-194">Apenas assemblies de inicialização de hospedagem são examinados quanto ao atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-194">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e5c6e-195">Para o aplicativo de exemplo, *HostingStartupApp*, para descobrir as inicializações de hospedagem descritas anteriormente, a variável de ambiente é definida como o seguinte valor:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-195">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="e5c6e-196">Um assembly de inicialização de hospedagem também pode ser definido usando a configuração hospedando a configuração de host assemblies de inicialização:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-196">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="e5c6e-197">Quando vários conjuntos de inicialização de hospedagem estão presentes, seus <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> métodos são executados na ordem em que os assemblies são listados.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-197">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="e5c6e-198">Ativação</span><span class="sxs-lookup"><span data-stu-id="e5c6e-198">Activation</span></span>

<span data-ttu-id="e5c6e-199">As opções para ativação da inicialização de hospedagem são:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-199">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="e5c6e-200">[Store](#runtime-store)em tempo de execução: a ativação não requer uma referência de tempo de compilação para ativação.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-200">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="e5c6e-201">O aplicativo de exemplo coloca os arquivos de dependências e o assembly de inicialização de hospedagem em uma pasta, *implantação*, para facilitar a implantação da inicialização de hospedagem em um ambiente multicomputador.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-201">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="e5c6e-202">A pasta *implantação* também inclui um script do PowerShell que cria ou modifica variáveis de ambiente no sistema de implantação para habilitar a inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-202">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="e5c6e-203">Referência de tempo de compilação necessária para a ativação</span><span class="sxs-lookup"><span data-stu-id="e5c6e-203">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="e5c6e-204">Pacote NuGet</span><span class="sxs-lookup"><span data-stu-id="e5c6e-204">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="e5c6e-205">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="e5c6e-205">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="e5c6e-206">Repositório de runtime</span><span class="sxs-lookup"><span data-stu-id="e5c6e-206">Runtime store</span></span>

<span data-ttu-id="e5c6e-207">A implementação de inicialização de hospedagem é colocada no [repositório de runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-207">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="e5c6e-208">Uma referência de tempo de compilação para o assembly não é exigida pelo aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-208">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="e5c6e-209">Depois que a inicialização de hospedagem é compilada, um repositório de runtime é gerado, usando o arquivo de projeto do manifesto e o comando do [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-209">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="e5c6e-210">No aplicativo de exemplo (projeto *RuntimeStore*) é usado o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-210">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="e5c6e-211">Para o runtime descobrir o repositório de runtime, o local do repositório de runtime é adicionado à variável de ambiente `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-211">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="e5c6e-212">**Modificar e colocar o arquivo de dependências da inicialização de hospedagem**</span><span class="sxs-lookup"><span data-stu-id="e5c6e-212">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="e5c6e-213">Para ativar o aprimoramento sem uma referência de pacote ao aprimoramento, especifique as dependências adicionais do runtime com `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-213">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="e5c6e-214">`additionalDeps` permite que você:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-214">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="e5c6e-215">Amplie o grafo de biblioteca do aplicativo, fornecendo um conjunto de arquivos *.deps.json* adicionais a serem mesclados com o arquivo *.deps.json* próprio do aplicativo na inicialização.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-215">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="e5c6e-216">Torne o assembly de inicialização de hospedagem detectável e carregável.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-216">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="e5c6e-217">A abordagem recomendada para gerar o arquivo de dependências adicionais é:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-217">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="e5c6e-218">Executar o `dotnet publish` no arquivo de manifesto do repositório de runtime mencionado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-218">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="e5c6e-219">Remova a referência de manifesto das bibliotecas e a `runtime` seção do arquivo *. deps. JSON* resultante.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-219">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="e5c6e-220">No projeto de exemplo, a propriedade `store.manifest/1.0.0` é removida das seções `targets` e `libraries`:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-220">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="e5c6e-221">Coloque o arquivo *.deps.json* no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-221">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="e5c6e-222">`{ADDITIONAL DEPENDENCIES PATH}`: Local adicionado à `DOTNET_ADDITIONAL_DEPS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-222">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="e5c6e-223">`{SHARED FRAMEWORK NAME}`: Estrutura compartilhada necessária para este arquivo de dependências adicional.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-223">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="e5c6e-224">`{SHARED FRAMEWORK VERSION}`: Versão mínima da estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-224">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="e5c6e-225">`{ENHANCEMENT ASSEMBLY NAME}`: O nome do assembly do aprimoramento.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-225">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="e5c6e-226">No aplicativo de exemplo (projeto *RuntimeStore*), o arquivo de dependências adicionais é colocado no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-226">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="e5c6e-227">Para o runtime descobrir o local do repositório de runtime, o local do arquivo de dependências adicionais é adicionado à variável de ambiente `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-227">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="e5c6e-228">No aplicativo de exemplo (projeto *RuntimeStore*), crie o repositório de tempo de execução e gere o arquivo de dependências adicionais usando um script [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-228">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="e5c6e-229">Para obter exemplos de como definir variáveis de ambiente para vários sistemas operacionais, confira [Usar vários ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-229">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="e5c6e-230">**Implantação**</span><span class="sxs-lookup"><span data-stu-id="e5c6e-230">**Deployment**</span></span>

<span data-ttu-id="e5c6e-231">Para facilitar a implantação de uma inicialização de hospedagem em um ambiente multicomputador, o aplicativo de exemplo cria uma pasta *implantação* na saída publicada que contém:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-231">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="e5c6e-232">O repositório de runtime de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-232">The hosting startup runtime store.</span></span>
* <span data-ttu-id="e5c6e-233">O arquivo de dependências de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-233">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="e5c6e-234">Um script do PowerShell que cria ou modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` e `DOTNET_ADDITIONAL_DEPS` para dar suporte à ativação da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-234">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="e5c6e-235">Execute o script de um prompt de comando do PowerShell administrativo no sistema de implantação.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-235">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="e5c6e-236">Pacote NuGet</span><span class="sxs-lookup"><span data-stu-id="e5c6e-236">NuGet package</span></span>

<span data-ttu-id="e5c6e-237">Uma melhoria da inicialização de hospedagem pode ser fornecida em pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-237">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="e5c6e-238">O pacote tem um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-238">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="e5c6e-239">Os tipos de inicialização de hospedagem fornecidos pelo pacote são disponibilizados para o aplicativo usando qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-239">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="e5c6e-240">O arquivo de projeto do aplicativo aprimorado faz uma referência de pacote para a inicialização de hospedagem no arquivo de projeto do aplicativo (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-240">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="e5c6e-241">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-241">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e5c6e-242">Essa abordagem se aplica a um pacote de assembly de inicialização de hospedagem publicado para [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-242">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="e5c6e-243">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de runtime](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-243">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="e5c6e-244">Para obter mais informações sobre pacotes do NuGet e o repositório de runtime, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-244">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="e5c6e-245">Como criar um pacote do NuGet com ferramentas de plataforma cruzada</span><span class="sxs-lookup"><span data-stu-id="e5c6e-245">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="e5c6e-246">Publicando pacotes</span><span class="sxs-lookup"><span data-stu-id="e5c6e-246">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="e5c6e-247">Repositório de pacotes de runtime</span><span class="sxs-lookup"><span data-stu-id="e5c6e-247">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="e5c6e-248">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="e5c6e-248">Project bin folder</span></span>

<span data-ttu-id="e5c6e-249">Uma melhoria da inicialização de hospedagem pode ser fornecida por um assemby implantado na *lixeira* no aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-249">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="e5c6e-250">Os tipos de inicialização de hospedagem fornecidos pelo assembly são disponibilizados para o aplicativo usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-250">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="e5c6e-251">O arquivo de projeto do aplicativo aprimorado faz uma referência de assembly para a inicialização de hospedagem (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-251">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="e5c6e-252">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-252">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e5c6e-253">Essa abordagem se aplica quando o cenário de implantação demanda fazer uma referência de tempo de compilação ao assembly da inicialização de hospedagem (arquivo *.dll*) e mover o assembly para:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-253">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="e5c6e-254">o projeto de consumo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-254">The consuming project.</span></span>
  * <span data-ttu-id="e5c6e-255">um local acessível ao projeto de consumo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-255">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="e5c6e-256">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de runtime](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-256">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="e5c6e-257">Ao ter como destino o .NET Framework, o assembly é carregável no contexto de carga padrão, o que, no .NET Framework, significa que ele fica em um dos seguintes locais:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-257">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="e5c6e-258">Caminho base do aplicativo: a pasta *bin* onde o executável do aplicativo (*. exe*) está localizado.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-258">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="e5c6e-259">GAC (cache de assembly global): o GAC armazena os assemblies que vários aplicativos .NET Framework compartilham.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-259">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="e5c6e-260">Para obter mais informações, consulte [como: instalar um assembly no cache de assembly global](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) na documentação do .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-260">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="e5c6e-261">Código de exemplo</span><span class="sxs-lookup"><span data-stu-id="e5c6e-261">Sample code</span></span>

<span data-ttu-id="e5c6e-262">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample)) demonstra cenários de implementação de inicialização de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-262">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="e5c6e-263">Dois assemblies de inicialização de hospedagem (bibliotecas de classes) definem um par chave-valor de configuração na memória cada:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-263">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="e5c6e-264">Pacote do NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="e5c6e-264">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="e5c6e-265">Biblioteca de classes (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="e5c6e-265">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="e5c6e-266">Uma inicialização de hospedagem é ativada de um assembly implantado pelo repositório de runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-266">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="e5c6e-267">O assembly adiciona dois middlewares ao aplicativo na inicialização que fornecem informações de diagnóstico sobre:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-267">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="e5c6e-268">Serviços registrados</span><span class="sxs-lookup"><span data-stu-id="e5c6e-268">Registered services</span></span>
  * <span data-ttu-id="e5c6e-269">Endereço (esquema, host, base do caminho, caminho, cadeia de caracteres de consulta)</span><span class="sxs-lookup"><span data-stu-id="e5c6e-269">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="e5c6e-270">Conexão (IP remoto, porta remota, IP local, porta local, certificado do cliente)</span><span class="sxs-lookup"><span data-stu-id="e5c6e-270">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="e5c6e-271">Cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="e5c6e-271">Request headers</span></span>
  * <span data-ttu-id="e5c6e-272">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="e5c6e-272">Environment variables</span></span>

<span data-ttu-id="e5c6e-273">Para executar o exemplo:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-273">To run the sample:</span></span>

<span data-ttu-id="e5c6e-274">**Ativação de um pacote do NuGet**</span><span class="sxs-lookup"><span data-stu-id="e5c6e-274">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="e5c6e-275">Compile o pacote *HostingStartupPackage* com o comando [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-275">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="e5c6e-276">Adicione o nome do assembly do pacote do *HostingStartupPackage* para a variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-276">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e5c6e-277">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-277">Compile and run the app.</span></span> <span data-ttu-id="e5c6e-278">Uma referência de pacote está presente no aplicativo aprimorado (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-278">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="e5c6e-279">Um `<PropertyGroup>` no arquivo de projeto do aplicativo especifica a saída do projeto de pacote (*../HostingStartupPackage/bin/Debug*) como uma origem de pacote.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-279">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="e5c6e-280">Isso permite que o aplicativo use o pacote sem carregar o pacote em [NuGet.org](https://www.nuget.org/). Para obter mais informações, consulte as observações no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-280">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="e5c6e-281">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` do pacote.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-281">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e5c6e-282">Se você fizer alterações no projeto *HostingStartupPackage* e recompilá-lo, limpe os caches de pacote do NuGet locais para garantir que o *HostingStartupApp* receba o pacote atualizado e não um pacote obsoleto do cache local.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-282">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="e5c6e-283">Para limpar os caches locais do NuGet, execute o seguinte comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="e5c6e-283">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="e5c6e-284">**Ativação de uma biblioteca de classes**</span><span class="sxs-lookup"><span data-stu-id="e5c6e-284">**Activation from a class library**</span></span>

1. <span data-ttu-id="e5c6e-285">Compile a biblioteca de classes *HostingStartupLibrary* com o comando [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-285">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="e5c6e-286">Adicione nome do assembly da biblioteca de classes do *HostingStartupLibrary* à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-286">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e5c6e-287">A pasta *lixeira* implanta o assembly da biblioteca de classes para o aplicativo ao copiar o arquivo *HostingStartupLibrary.dll* da saída compilada da biblioteca de classes para a pasta *lixeira/Depurar* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-287">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="e5c6e-288">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-288">Compile and run the app.</span></span> <span data-ttu-id="e5c6e-289">Um `<ItemGroup>` no arquivo de projeto do aplicativo referencia o assembly da biblioteca de classes (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-289">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="e5c6e-290">Para mais informações, consulte as notas no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-290">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="e5c6e-291">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` da biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-291">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e5c6e-292">**Ativação de um assembly implantado pelo repositório de runtime**</span><span class="sxs-lookup"><span data-stu-id="e5c6e-292">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="e5c6e-293">O projeto *StartupDiagnostics* usa o [PowerShell](/powershell/scripting/powershell-scripting) para modificar seu arquivo *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-293">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="e5c6e-294">O PowerShell é instalado por padrão em um sistema operacional Windows começando no Windows 7 SP1 e no Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-294">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="e5c6e-295">Para obter o PowerShell em outras plataformas, consulte [Instalando várias versões do PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-295">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="e5c6e-296">Execute o script *build.ps1* na pasta *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-296">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="e5c6e-297">O script:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-297">The script:</span></span>
   * <span data-ttu-id="e5c6e-298">Gera o `StartupDiagnostics` pacote na pasta *obj\packages*</span><span class="sxs-lookup"><span data-stu-id="e5c6e-298">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="e5c6e-299">Gera o repositório de runtime para `StartupDiagnostics` na pasta de *armazenamento*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-299">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="e5c6e-300">O comando `dotnet store` no script usa o `win7-x64` [RID (identificador de runtime)](/dotnet/core/rid-catalog) para uma inicialização de host implantada no Windows.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-300">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="e5c6e-301">Ao fornecer a inicialização de hospedagem para um runtime diferente, substitua pelo RID correto na linha 37 do script.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-301">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="e5c6e-302">O armazenamento em tempo de execução para `StartupDiagnostics` posteriormente seria movido para o armazenamento de tempo de execução do usuário ou do sistema no computador em que o assembly será consumido.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-302">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="e5c6e-303">O local de instalação do armazenamento em tempo de execução do usuário para o `StartupDiagnostics` assembly é *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/startupdiagnostics. dll*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-303">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="e5c6e-304">Gera o `additionalDeps` para `StartupDiagnostics` na pasta *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="e5c6e-304">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="e5c6e-305">As dependências adicionais posteriormente seriam movidas para as dependências adicionais do usuário ou do sistema.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-305">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="e5c6e-306">O `StartupDiagnostics` local de instalação das dependências adicionais do usuário é *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. NetCore. app/3.0.0/StartupDiagnostics. deps. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-306">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="e5c6e-307">Coloca o arquivo *deploy.ps1* na pasta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-307">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="e5c6e-308">Execute o script *deploy.ps1* na pasta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-308">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="e5c6e-309">O script acrescenta:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-309">The script appends:</span></span>
   * <span data-ttu-id="e5c6e-310">`StartupDiagnostics` à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-310">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="e5c6e-311">O caminho de dependências de inicialização de hospedagem (na pasta de *implantação* do projeto RuntimeStore) para a `DOTNET_ADDITIONAL_DEPS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-311">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="e5c6e-312">O caminho de armazenamento em tempo de execução (na pasta de *implantação* do projeto RuntimeStore) para a `DOTNET_SHARED_STORE` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-312">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="e5c6e-313">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-313">Run the sample app.</span></span>
1. <span data-ttu-id="e5c6e-314">Solicite o ponto de extremidade `/services` para ver os serviços registrados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-314">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="e5c6e-315">Solicite o ponto de extremidade `/diag` para ver as informações de diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-315">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e5c6e-316">Uma <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementação (inicialização de hospedagem) adiciona aprimoramentos a um aplicativo na inicialização por meio de um assembly externo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-316">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="e5c6e-317">Por exemplo, uma biblioteca externa pode usar uma implementação de inicialização de hospedagem para fornecer serviços ou provedores de configuração adicionais a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-317">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="e5c6e-318">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e5c6e-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="e5c6e-319">Atributo HostingStartup</span><span class="sxs-lookup"><span data-stu-id="e5c6e-319">HostingStartup attribute</span></span>

<span data-ttu-id="e5c6e-320">Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica a presença de um assembly de inicialização de hospedagem para ativar em runtime.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-320">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="e5c6e-321">O assembly de entrada ou o assembly que contém a classe `Startup` é automaticamente examinado para o atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-321">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e5c6e-322">A lista de assemblies a ser pesquisada para os atributos `HostingStartup` é carregada no runtime da configuração em [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-322">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="e5c6e-323">A lista de assemblies para excluir da descoberta é carregada de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-323">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="e5c6e-324">Para obter mais informações, consulte [Host da Web: assemblies de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-assemblies) e [Host da Web: assemblies de exclusão da inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-324">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="e5c6e-325">No exemplo a seguir, o namespace do assembly de inicialização de hospedagem é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-325">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="e5c6e-326">A classe que contém o código de inicialização de hospedagem é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-326">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e5c6e-327">O atributo `HostingStartup` normalmente está localizado no arquivo de classe de implementação `IHostingStartup` do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-327">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="e5c6e-328">Descobrir assemblies de inicialização de hospedagem carregados</span><span class="sxs-lookup"><span data-stu-id="e5c6e-328">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="e5c6e-329">Para descobrir os assemblies de inicialização de hospedagem carregados, habilite o registro em log e verifique os logs do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-329">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="e5c6e-330">Erros que ocorrem quando os assemblies carregados são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-330">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="e5c6e-331">Os assemblies de inicialização de hospedagem carregados são registrados em log no nível Depuração e todos os erros são registrados.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-331">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="e5c6e-332">Desabilitar o carregamento automático de assemblies de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="e5c6e-332">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="e5c6e-333">Para desabilitar o carregamento automático de assemblies de inicialização de hospedagem, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-333">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="e5c6e-334">Para impedir o carregamento de todos os assemblies de inicialização de hospedagem, defina o seguinte para `true` ou `1`:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-334">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="e5c6e-335">Configuração do host [Impedir inicialização de hospedagem](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-335">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="e5c6e-336">A variável de ambiente `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="e5c6e-337">Para evitar o carregamento de assemblies específicos de inicialização de hospedagem, defina uma das opções a seguir como uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para excluir na inicialização:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-337">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="e5c6e-338">Configuração do host [Assemblies de exclusão de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-338">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="e5c6e-339">A variável de ambiente `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="e5c6e-340">Se a configuração do host e a variável de ambiente estiverem definidas, a configuração do host controlará o comportamento.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-340">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="e5c6e-341">A desabilitação de assemblies de inicialização de hospedagem usando a configuração do host ou a variável de ambiente desabilita o assembly globalmente e poderá desabilitar várias características de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-341">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="e5c6e-342">Project</span><span class="sxs-lookup"><span data-stu-id="e5c6e-342">Project</span></span>

<span data-ttu-id="e5c6e-343">Crie uma inicialização de hospedagem com qualquer um dos seguintes tipos de projeto:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-343">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="e5c6e-344">Biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="e5c6e-344">Class library</span></span>](#class-library)
* [<span data-ttu-id="e5c6e-345">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="e5c6e-345">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="e5c6e-346">Biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="e5c6e-346">Class library</span></span>

<span data-ttu-id="e5c6e-347">Uma melhoria da inicialização de hospedagem pode ser fornecida em uma biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-347">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="e5c6e-348">A biblioteca contém um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-348">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e5c6e-349">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclui um Razor aplicativo de páginas, *HostingStartupApp*e uma biblioteca de classes, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-349">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="e5c6e-350">A biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-350">The class library:</span></span>

* <span data-ttu-id="e5c6e-351">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-351">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e5c6e-352">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço à configuração do aplicativo usando o provedor de configuração na memória ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-352">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="e5c6e-353">Inclui um atributo `HostingStartup` que identifica o namespace e a classe de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-353">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="e5c6e-354">O `ServiceKeyInjection` método da classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> usa um <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para adicionar aprimoramentos a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-354">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="e5c6e-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e5c6e-356">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem da biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-356">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="e5c6e-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="e5c6e-358">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) também inclui um projeto de pacote do NuGet que fornece uma inicialização de hospedagem separada, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-358">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="e5c6e-359">O pacote tem as mesmas características da biblioteca de classes descrita anteriormente.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-359">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="e5c6e-360">O pacote:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-360">The package:</span></span>

* <span data-ttu-id="e5c6e-361">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-361">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e5c6e-362">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço para a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-362">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="e5c6e-363">Inclui um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-363">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e5c6e-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e5c6e-365">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem do pacote:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-365">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="e5c6e-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="e5c6e-367">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="e5c6e-367">Console app without an entry point</span></span>

<span data-ttu-id="e5c6e-368">*Essa abordagem só está disponível para aplicativos .NET Core, não para .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="e5c6e-368">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="e5c6e-369">Uma melhoria de inicialização de hospedagem dinâmica que não requer uma referência de tempo de compilação para a ativação pode ser fornecida em um aplicativo de console sem um ponto de entrada que contenha um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-369">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="e5c6e-370">Publicar o aplicativo de console produz um assembly de inicialização de hospedagem que pode ser consumido do repositório de runtime.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-370">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="e5c6e-371">Um aplicativo de console sem um ponto de entrada é usado nesse processo porque:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-371">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="e5c6e-372">Um arquivo de dependências é necessário para consumir a inicialização de hospedagem no assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-372">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="e5c6e-373">Um arquivo de dependências é um ativo de aplicativo executável que é produzido ao publicar um aplicativo, não uma biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-373">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="e5c6e-374">Uma biblioteca não pode ser adicionada diretamente ao [repositório de pacotes de runtime](/dotnet/core/deploying/runtime-store), o que exige um projeto executável que tem como alvo o runtime compartilhado.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-374">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="e5c6e-375">Na criação de uma inicialização de hospedagem dinâmica:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-375">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="e5c6e-376">Um assembly de inicialização de hospedagem é criado no aplicativo de console sem um ponto de entrada que:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-376">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="e5c6e-377">Inclui uma classe que contém a implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-377">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="e5c6e-378">Inclui um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) para identificar a classe de implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-378">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="e5c6e-379">O aplicativo de console é publicado para obter as dependências da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-379">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="e5c6e-380">Uma consequência de publicar o aplicativo de console é que as dependências não utilizadas são cortadas do arquivo de dependências.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-380">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="e5c6e-381">O arquivo de dependências é modificado para definir o local de runtime do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-381">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="e5c6e-382">O assembly de inicialização de hospedagem e seu arquivo de dependências são colocados no repositório do pacote de runtime.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-382">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="e5c6e-383">Para descobrir o assembly de inicialização de hospedagem e seu arquivo de dependências, eles são listados em um par de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-383">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="e5c6e-384">O assembly de aplicativo do console referencia o pacote [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="e5c6e-384">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="e5c6e-385">Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica uma classe como uma implementação de `IHostingStartup` para carregar e executar ao compilar o <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="e5c6e-385">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="e5c6e-386">No seguinte exemplo, o namespace é `StartupEnhancement` e a classe é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-386">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e5c6e-387">Uma classe implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-387">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="e5c6e-388">O método da classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> usa um <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para adicionar aprimoramentos a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-388">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="e5c6e-389">`IHostingStartup.Configure` no assembly de inicialização de hospedagem é chamado pelo runtime antes de `Startup.Configure` no código do usuário, o que permite que o código de usuário substitua qualquer configuração fornecida pelo assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-389">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="e5c6e-390">Ao criar um projeto `IHostingStartup`, o arquivo de dependências (*.deps.json*) define o local `runtime` do assembly como a pasta *bin*:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-390">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="e5c6e-391">Apenas uma parte do arquivo é mostrada.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-391">Only part of the file is shown.</span></span> <span data-ttu-id="e5c6e-392">O nome do assembly no exemplo é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-392">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="e5c6e-393">Configuração fornecida pela inicialização da hospedagem</span><span class="sxs-lookup"><span data-stu-id="e5c6e-393">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="e5c6e-394">Existem duas abordagens para lidar com a configuração, dependendo se você deseja que a configuração da inicialização da hospedagem tenha precedência ou que a configuração do aplicativo tenha precedência:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-394">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="e5c6e-395">Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> para carregar a configuração depois que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo forem executados.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-395">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e5c6e-396">A configuração de inicialização de hospedagem tem prioridade sobre a configuração do aplicativo que usa essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-396">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="e5c6e-397">Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> para carregar a configuração antes que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo sejam executados.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-397">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e5c6e-398">Os valores de configuração do aplicativo têm prioridade sobre aqueles fornecidos pela inicialização da hospedagem que usa essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-398">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="e5c6e-399">Especificar o assembly de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="e5c6e-399">Specify the hosting startup assembly</span></span>

<span data-ttu-id="e5c6e-400">Para uma biblioteca de classes ou inicialização de hospedagem fornecida pelo aplicativo de console, especifique o nome do assembly de inicialização de hospedagem na variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-400">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="e5c6e-401">A variável de ambiente é uma lista de assemblies delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-401">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="e5c6e-402">Apenas assemblies de inicialização de hospedagem são examinados quanto ao atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-402">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e5c6e-403">Para o aplicativo de exemplo, *HostingStartupApp*, para descobrir as inicializações de hospedagem descritas anteriormente, a variável de ambiente é definida como o seguinte valor:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-403">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="e5c6e-404">Um assembly de inicialização de hospedagem também pode ser definido usando a configuração do host [Assemblies de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-assemblies).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-404">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="e5c6e-405">Quando vários conjuntos de inicialização de hospedagem estão presentes, seus <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> métodos são executados na ordem em que os assemblies são listados.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-405">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="e5c6e-406">Ativação</span><span class="sxs-lookup"><span data-stu-id="e5c6e-406">Activation</span></span>

<span data-ttu-id="e5c6e-407">As opções para ativação da inicialização de hospedagem são:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-407">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="e5c6e-408">[Store](#runtime-store)em tempo de execução: a ativação não requer uma referência de tempo de compilação para ativação.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-408">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="e5c6e-409">O aplicativo de exemplo coloca os arquivos de dependências e o assembly de inicialização de hospedagem em uma pasta, *implantação*, para facilitar a implantação da inicialização de hospedagem em um ambiente multicomputador.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-409">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="e5c6e-410">A pasta *implantação* também inclui um script do PowerShell que cria ou modifica variáveis de ambiente no sistema de implantação para habilitar a inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-410">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="e5c6e-411">Referência de tempo de compilação necessária para a ativação</span><span class="sxs-lookup"><span data-stu-id="e5c6e-411">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="e5c6e-412">Pacote NuGet</span><span class="sxs-lookup"><span data-stu-id="e5c6e-412">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="e5c6e-413">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="e5c6e-413">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="e5c6e-414">Repositório de runtime</span><span class="sxs-lookup"><span data-stu-id="e5c6e-414">Runtime store</span></span>

<span data-ttu-id="e5c6e-415">A implementação de inicialização de hospedagem é colocada no [repositório de runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-415">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="e5c6e-416">Uma referência de tempo de compilação para o assembly não é exigida pelo aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-416">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="e5c6e-417">Depois que a inicialização de hospedagem é compilada, um repositório de runtime é gerado, usando o arquivo de projeto do manifesto e o comando do [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-417">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="e5c6e-418">No aplicativo de exemplo (projeto *RuntimeStore*) é usado o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-418">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="e5c6e-419">Para o runtime descobrir o repositório de runtime, o local do repositório de runtime é adicionado à variável de ambiente `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-419">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="e5c6e-420">**Modificar e colocar o arquivo de dependências da inicialização de hospedagem**</span><span class="sxs-lookup"><span data-stu-id="e5c6e-420">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="e5c6e-421">Para ativar o aprimoramento sem uma referência de pacote ao aprimoramento, especifique as dependências adicionais do runtime com `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-421">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="e5c6e-422">`additionalDeps` permite que você:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-422">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="e5c6e-423">Amplie o grafo de biblioteca do aplicativo, fornecendo um conjunto de arquivos *.deps.json* adicionais a serem mesclados com o arquivo *.deps.json* próprio do aplicativo na inicialização.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-423">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="e5c6e-424">Torne o assembly de inicialização de hospedagem detectável e carregável.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-424">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="e5c6e-425">A abordagem recomendada para gerar o arquivo de dependências adicionais é:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-425">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="e5c6e-426">Executar o `dotnet publish` no arquivo de manifesto do repositório de runtime mencionado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-426">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="e5c6e-427">Remova a referência de manifesto das bibliotecas e a `runtime` seção do arquivo *. deps. JSON* resultante.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-427">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="e5c6e-428">No projeto de exemplo, a propriedade `store.manifest/1.0.0` é removida das seções `targets` e `libraries`:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-428">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="e5c6e-429">Coloque o arquivo *.deps.json* no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-429">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="e5c6e-430">`{ADDITIONAL DEPENDENCIES PATH}`: Local adicionado à `DOTNET_ADDITIONAL_DEPS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-430">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="e5c6e-431">`{SHARED FRAMEWORK NAME}`: Estrutura compartilhada necessária para este arquivo de dependências adicional.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-431">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="e5c6e-432">`{SHARED FRAMEWORK VERSION}`: Versão mínima da estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-432">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="e5c6e-433">`{ENHANCEMENT ASSEMBLY NAME}`: O nome do assembly do aprimoramento.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-433">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="e5c6e-434">No aplicativo de exemplo (projeto *RuntimeStore*), o arquivo de dependências adicionais é colocado no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-434">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="e5c6e-435">Para o runtime descobrir o local do repositório de runtime, o local do arquivo de dependências adicionais é adicionado à variável de ambiente `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-435">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="e5c6e-436">No aplicativo de exemplo (projeto *RuntimeStore*), crie o repositório de tempo de execução e gere o arquivo de dependências adicionais usando um script [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-436">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="e5c6e-437">Para obter exemplos de como definir variáveis de ambiente para vários sistemas operacionais, confira [Usar vários ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-437">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="e5c6e-438">**Implantação**</span><span class="sxs-lookup"><span data-stu-id="e5c6e-438">**Deployment**</span></span>

<span data-ttu-id="e5c6e-439">Para facilitar a implantação de uma inicialização de hospedagem em um ambiente multicomputador, o aplicativo de exemplo cria uma pasta *implantação* na saída publicada que contém:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-439">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="e5c6e-440">O repositório de runtime de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-440">The hosting startup runtime store.</span></span>
* <span data-ttu-id="e5c6e-441">O arquivo de dependências de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-441">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="e5c6e-442">Um script do PowerShell que cria ou modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` e `DOTNET_ADDITIONAL_DEPS` para dar suporte à ativação da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-442">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="e5c6e-443">Execute o script de um prompt de comando do PowerShell administrativo no sistema de implantação.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-443">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="e5c6e-444">Pacote NuGet</span><span class="sxs-lookup"><span data-stu-id="e5c6e-444">NuGet package</span></span>

<span data-ttu-id="e5c6e-445">Uma melhoria da inicialização de hospedagem pode ser fornecida em pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-445">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="e5c6e-446">O pacote tem um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-446">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="e5c6e-447">Os tipos de inicialização de hospedagem fornecidos pelo pacote são disponibilizados para o aplicativo usando qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-447">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="e5c6e-448">O arquivo de projeto do aplicativo aprimorado faz uma referência de pacote para a inicialização de hospedagem no arquivo de projeto do aplicativo (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-448">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="e5c6e-449">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-449">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e5c6e-450">Essa abordagem se aplica a um pacote de assembly de inicialização de hospedagem publicado para [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-450">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="e5c6e-451">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de runtime](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-451">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="e5c6e-452">Para obter mais informações sobre pacotes do NuGet e o repositório de runtime, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-452">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="e5c6e-453">Como criar um pacote do NuGet com ferramentas de plataforma cruzada</span><span class="sxs-lookup"><span data-stu-id="e5c6e-453">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="e5c6e-454">Publicando pacotes</span><span class="sxs-lookup"><span data-stu-id="e5c6e-454">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="e5c6e-455">Repositório de pacotes de runtime</span><span class="sxs-lookup"><span data-stu-id="e5c6e-455">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="e5c6e-456">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="e5c6e-456">Project bin folder</span></span>

<span data-ttu-id="e5c6e-457">Uma melhoria da inicialização de hospedagem pode ser fornecida por um assemby implantado na *lixeira* no aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-457">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="e5c6e-458">Os tipos de inicialização de hospedagem fornecidos pelo assembly são disponibilizados para o aplicativo usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-458">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="e5c6e-459">O arquivo de projeto do aplicativo aprimorado faz uma referência de assembly para a inicialização de hospedagem (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-459">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="e5c6e-460">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-460">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e5c6e-461">Essa abordagem se aplica quando o cenário de implantação demanda fazer uma referência de tempo de compilação ao assembly da inicialização de hospedagem (arquivo *.dll*) e mover o assembly para:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-461">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="e5c6e-462">o projeto de consumo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-462">The consuming project.</span></span>
  * <span data-ttu-id="e5c6e-463">um local acessível ao projeto de consumo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-463">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="e5c6e-464">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de runtime](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-464">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="e5c6e-465">Ao ter como destino o .NET Framework, o assembly é carregável no contexto de carga padrão, o que, no .NET Framework, significa que ele fica em um dos seguintes locais:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-465">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="e5c6e-466">Caminho base do aplicativo: a pasta *bin* onde o executável do aplicativo (*. exe*) está localizado.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-466">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="e5c6e-467">GAC (cache de assembly global): o GAC armazena os assemblies que vários aplicativos .NET Framework compartilham.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-467">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="e5c6e-468">Para obter mais informações, consulte [como: instalar um assembly no cache de assembly global](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) na documentação do .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-468">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="e5c6e-469">Código de exemplo</span><span class="sxs-lookup"><span data-stu-id="e5c6e-469">Sample code</span></span>

<span data-ttu-id="e5c6e-470">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample)) demonstra cenários de implementação de inicialização de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-470">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="e5c6e-471">Dois assemblies de inicialização de hospedagem (bibliotecas de classes) definem um par chave-valor de configuração na memória cada:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-471">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="e5c6e-472">Pacote do NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="e5c6e-472">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="e5c6e-473">Biblioteca de classes (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="e5c6e-473">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="e5c6e-474">Uma inicialização de hospedagem é ativada de um assembly implantado pelo repositório de runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-474">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="e5c6e-475">O assembly adiciona dois middlewares ao aplicativo na inicialização que fornecem informações de diagnóstico sobre:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-475">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="e5c6e-476">Serviços registrados</span><span class="sxs-lookup"><span data-stu-id="e5c6e-476">Registered services</span></span>
  * <span data-ttu-id="e5c6e-477">Endereço (esquema, host, base do caminho, caminho, cadeia de caracteres de consulta)</span><span class="sxs-lookup"><span data-stu-id="e5c6e-477">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="e5c6e-478">Conexão (IP remoto, porta remota, IP local, porta local, certificado do cliente)</span><span class="sxs-lookup"><span data-stu-id="e5c6e-478">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="e5c6e-479">Cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="e5c6e-479">Request headers</span></span>
  * <span data-ttu-id="e5c6e-480">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="e5c6e-480">Environment variables</span></span>

<span data-ttu-id="e5c6e-481">Para executar o exemplo:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-481">To run the sample:</span></span>

<span data-ttu-id="e5c6e-482">**Ativação de um pacote do NuGet**</span><span class="sxs-lookup"><span data-stu-id="e5c6e-482">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="e5c6e-483">Compile o pacote *HostingStartupPackage* com o comando [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-483">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="e5c6e-484">Adicione o nome do assembly do pacote do *HostingStartupPackage* para a variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-484">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e5c6e-485">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-485">Compile and run the app.</span></span> <span data-ttu-id="e5c6e-486">Uma referência de pacote está presente no aplicativo aprimorado (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-486">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="e5c6e-487">Um `<PropertyGroup>` no arquivo de projeto do aplicativo especifica a saída do projeto de pacote (*../HostingStartupPackage/bin/Debug*) como uma origem de pacote.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-487">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="e5c6e-488">Isso permite que o aplicativo use o pacote sem carregar o pacote em [NuGet.org](https://www.nuget.org/). Para obter mais informações, consulte as observações no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-488">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="e5c6e-489">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` do pacote.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-489">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e5c6e-490">Se você fizer alterações no projeto *HostingStartupPackage* e recompilá-lo, limpe os caches de pacote do NuGet locais para garantir que o *HostingStartupApp* receba o pacote atualizado e não um pacote obsoleto do cache local.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-490">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="e5c6e-491">Para limpar os caches locais do NuGet, execute o seguinte comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="e5c6e-491">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="e5c6e-492">**Ativação de uma biblioteca de classes**</span><span class="sxs-lookup"><span data-stu-id="e5c6e-492">**Activation from a class library**</span></span>

1. <span data-ttu-id="e5c6e-493">Compile a biblioteca de classes *HostingStartupLibrary* com o comando [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-493">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="e5c6e-494">Adicione nome do assembly da biblioteca de classes do *HostingStartupLibrary* à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-494">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e5c6e-495">A pasta *lixeira* implanta o assembly da biblioteca de classes para o aplicativo ao copiar o arquivo *HostingStartupLibrary.dll* da saída compilada da biblioteca de classes para a pasta *lixeira/Depurar* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-495">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="e5c6e-496">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-496">Compile and run the app.</span></span> <span data-ttu-id="e5c6e-497">Um `<ItemGroup>` do arquivo de projeto do aplicativo referencia o assembly da biblioteca de classes (*.\lixeira\Depurar\netcoreapp2.1\HostingStartupLibrary.dll*) (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-497">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="e5c6e-498">Para mais informações, consulte as notas no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-498">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="e5c6e-499">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` da biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-499">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e5c6e-500">**Ativação de um assembly implantado pelo repositório de runtime**</span><span class="sxs-lookup"><span data-stu-id="e5c6e-500">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="e5c6e-501">O projeto *StartupDiagnostics* usa o [PowerShell](/powershell/scripting/powershell-scripting) para modificar seu arquivo *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-501">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="e5c6e-502">O PowerShell é instalado por padrão em um sistema operacional Windows começando no Windows 7 SP1 e no Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-502">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="e5c6e-503">Para obter o PowerShell em outras plataformas, consulte [Instalando várias versões do PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="e5c6e-503">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="e5c6e-504">Execute o script *build.ps1* na pasta *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-504">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="e5c6e-505">O script:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-505">The script:</span></span>
   * <span data-ttu-id="e5c6e-506">Gera o `StartupDiagnostics` pacote na pasta *obj\packages*</span><span class="sxs-lookup"><span data-stu-id="e5c6e-506">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="e5c6e-507">Gera o repositório de runtime para `StartupDiagnostics` na pasta de *armazenamento*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-507">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="e5c6e-508">O comando `dotnet store` no script usa o `win7-x64` [RID (identificador de runtime)](/dotnet/core/rid-catalog) para uma inicialização de host implantada no Windows.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-508">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="e5c6e-509">Ao fornecer a inicialização de hospedagem para um runtime diferente, substitua pelo RID correto na linha 37 do script.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-509">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="e5c6e-510">O armazenamento em tempo de execução para `StartupDiagnostics` posteriormente seria movido para o armazenamento de tempo de execução do usuário ou do sistema no computador em que o assembly será consumido.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-510">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="e5c6e-511">O local de instalação do armazenamento em tempo de execução do usuário para o `StartupDiagnostics` assembly é *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/startupdiagnostics. dll*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-511">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="e5c6e-512">Gera o `additionalDeps` para `StartupDiagnostics` na pasta *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="e5c6e-512">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="e5c6e-513">As dependências adicionais posteriormente seriam movidas para as dependências adicionais do usuário ou do sistema.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-513">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="e5c6e-514">O `StartupDiagnostics` local de instalação das dependências adicionais do usuário é *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. NetCore. app/2.2.0/StartupDiagnostics. deps. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-514">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="e5c6e-515">Coloca o arquivo *deploy.ps1* na pasta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-515">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="e5c6e-516">Execute o script *deploy.ps1* na pasta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-516">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="e5c6e-517">O script acrescenta:</span><span class="sxs-lookup"><span data-stu-id="e5c6e-517">The script appends:</span></span>
   * <span data-ttu-id="e5c6e-518">`StartupDiagnostics` à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-518">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="e5c6e-519">O caminho de dependências de inicialização de hospedagem (na pasta de *implantação* do projeto RuntimeStore) para a `DOTNET_ADDITIONAL_DEPS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-519">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="e5c6e-520">O caminho de armazenamento em tempo de execução (na pasta de *implantação* do projeto RuntimeStore) para a `DOTNET_SHARED_STORE` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-520">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="e5c6e-521">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-521">Run the sample app.</span></span>
1. <span data-ttu-id="e5c6e-522">Solicite o ponto de extremidade `/services` para ver os serviços registrados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-522">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="e5c6e-523">Solicite o ponto de extremidade `/diag` para ver as informações de diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="e5c6e-523">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

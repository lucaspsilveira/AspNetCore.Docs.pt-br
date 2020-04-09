---
title: Usar assemblies de inicialização de hospedagem no ASP.NET Core
author: rick-anderson
description: Descubra como aprimorar um aplicativo ASP.NET Core por meio de um assembly externo usando uma implementação de IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: ac667b0205f5daad395d86fbe129beb509a044a6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417615"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="2f717-103">Usar assemblies de inicialização de hospedagem no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2f717-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="2f717-104">Por [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="2f717-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2f717-105">Uma <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementação (hosting startup) adiciona melhorias a um aplicativo na inicialização a partir de um conjunto externo.</span><span class="sxs-lookup"><span data-stu-id="2f717-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="2f717-106">Por exemplo, uma biblioteca externa pode usar uma implementação de inicialização de hospedagem para fornecer serviços ou provedores de configuração adicionais a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="2f717-107">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2f717-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="2f717-108">Atributo HostingStartup</span><span class="sxs-lookup"><span data-stu-id="2f717-108">HostingStartup attribute</span></span>

<span data-ttu-id="2f717-109">Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica a presença de um assembly de inicialização de hospedagem para ativar em runtime.</span><span class="sxs-lookup"><span data-stu-id="2f717-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="2f717-110">O assembly de entrada ou o assembly que contém a classe `Startup` é automaticamente examinado para o atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="2f717-111">A lista de assemblies a ser pesquisada para os atributos `HostingStartup` é carregada no runtime da configuração em [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="2f717-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="2f717-112">A lista de assemblies para excluir da descoberta é carregada de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="2f717-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="2f717-113">No exemplo a seguir, o namespace do assembly de inicialização de hospedagem é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="2f717-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="2f717-114">A classe que contém o código de inicialização de hospedagem é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="2f717-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="2f717-115">O atributo `HostingStartup` normalmente está localizado no arquivo de classe de implementação `IHostingStartup` do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="2f717-116">Descobrir assemblies de inicialização de hospedagem carregados</span><span class="sxs-lookup"><span data-stu-id="2f717-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="2f717-117">Para descobrir os assemblies de inicialização de hospedagem carregados, habilite o registro em log e verifique os logs do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="2f717-118">Erros que ocorrem quando os assemblies carregados são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="2f717-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="2f717-119">Os assemblies de inicialização de hospedagem carregados são registrados em log no nível Depuração e todos os erros são registrados.</span><span class="sxs-lookup"><span data-stu-id="2f717-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="2f717-120">Desabilitar o carregamento automático de assemblies de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="2f717-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="2f717-121">Para desabilitar o carregamento automático de assemblies de inicialização de hospedagem, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="2f717-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="2f717-122">Para impedir o carregamento de todos os assemblies de inicialização de hospedagem, defina o seguinte para `true` ou `1`:</span><span class="sxs-lookup"><span data-stu-id="2f717-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="2f717-123">Impedir a configuração de configuração do host Host host:</span><span class="sxs-lookup"><span data-stu-id="2f717-123">Prevent Hosting Startup host configuration setting:</span></span>

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

  * <span data-ttu-id="2f717-124">A variável de ambiente `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="2f717-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="2f717-125">Para evitar o carregamento de assemblies específicos de inicialização de hospedagem, defina uma das opções a seguir como uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para excluir na inicialização:</span><span class="sxs-lookup"><span data-stu-id="2f717-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="2f717-126">Configuração de configuração do host Exclui conjuntos de inicialização:</span><span class="sxs-lookup"><span data-stu-id="2f717-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

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

  * <span data-ttu-id="2f717-127">A variável de ambiente `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="2f717-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="2f717-128">Se a configuração do host e a variável de ambiente estiverem definidas, a configuração do host controlará o comportamento.</span><span class="sxs-lookup"><span data-stu-id="2f717-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="2f717-129">A desabilitação de assemblies de inicialização de hospedagem usando a configuração do host ou a variável de ambiente desabilita o assembly globalmente e poderá desabilitar várias características de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="2f717-130">Project</span><span class="sxs-lookup"><span data-stu-id="2f717-130">Project</span></span>

<span data-ttu-id="2f717-131">Crie uma inicialização de hospedagem com qualquer um dos seguintes tipos de projeto:</span><span class="sxs-lookup"><span data-stu-id="2f717-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="2f717-132">Biblioteca de classe</span><span class="sxs-lookup"><span data-stu-id="2f717-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="2f717-133">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="2f717-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="2f717-134">Biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="2f717-134">Class library</span></span>

<span data-ttu-id="2f717-135">Uma melhoria da inicialização de hospedagem pode ser fornecida em uma biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="2f717-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="2f717-136">A biblioteca contém um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="2f717-137">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclui um aplicativo Razor Pages, *HostingStartupApp* e uma biblioteca de classes, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="2f717-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="2f717-138">A biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="2f717-138">The class library:</span></span>

* <span data-ttu-id="2f717-139">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="2f717-140">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço à configuração do aplicativo usando o provedor de configuração na memória ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="2f717-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="2f717-141">Inclui um atributo `HostingStartup` que identifica o namespace e a classe de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="2f717-142">O `ServiceKeyInjection` método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> da classe <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> usa um para adicionar melhorias a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="2f717-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f717-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="2f717-144">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem da biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="2f717-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="2f717-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f717-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="2f717-146">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) também inclui um projeto de pacote do NuGet que fornece uma inicialização de hospedagem separada, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="2f717-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="2f717-147">O pacote tem as mesmas características da biblioteca de classes descrita anteriormente.</span><span class="sxs-lookup"><span data-stu-id="2f717-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="2f717-148">O pacote:</span><span class="sxs-lookup"><span data-stu-id="2f717-148">The package:</span></span>

* <span data-ttu-id="2f717-149">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="2f717-150">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço para a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="2f717-151">Inclui um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="2f717-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f717-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="2f717-153">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem do pacote:</span><span class="sxs-lookup"><span data-stu-id="2f717-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="2f717-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f717-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="2f717-155">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="2f717-155">Console app without an entry point</span></span>

<span data-ttu-id="2f717-156">*Essa abordagem só está disponível para aplicativos .NET Core, não para .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="2f717-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="2f717-157">Uma melhoria de inicialização de hospedagem dinâmica que não requer uma referência de tempo de compilação para a ativação pode ser fornecida em um aplicativo de console sem um ponto de entrada que contenha um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="2f717-158">Publicar o aplicativo de console produz um assembly de inicialização de hospedagem que pode ser consumido do repositório de runtime.</span><span class="sxs-lookup"><span data-stu-id="2f717-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="2f717-159">Um aplicativo de console sem um ponto de entrada é usado nesse processo porque:</span><span class="sxs-lookup"><span data-stu-id="2f717-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="2f717-160">Um arquivo de dependências é necessário para consumir a inicialização de hospedagem no assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="2f717-161">Um arquivo de dependências é um ativo de aplicativo executável que é produzido ao publicar um aplicativo, não uma biblioteca.</span><span class="sxs-lookup"><span data-stu-id="2f717-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="2f717-162">Uma biblioteca não pode ser adicionada diretamente ao [repositório de pacotes de runtime](/dotnet/core/deploying/runtime-store), o que exige um projeto executável que tem como alvo o runtime compartilhado.</span><span class="sxs-lookup"><span data-stu-id="2f717-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="2f717-163">Na criação de uma inicialização de hospedagem dinâmica:</span><span class="sxs-lookup"><span data-stu-id="2f717-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="2f717-164">Um assembly de inicialização de hospedagem é criado no aplicativo de console sem um ponto de entrada que:</span><span class="sxs-lookup"><span data-stu-id="2f717-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="2f717-165">Inclui uma classe que contém a implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="2f717-166">Inclui um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) para identificar a classe de implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="2f717-167">O aplicativo de console é publicado para obter as dependências da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="2f717-168">Uma consequência de publicar o aplicativo de console é que as dependências não utilizadas são cortadas do arquivo de dependências.</span><span class="sxs-lookup"><span data-stu-id="2f717-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="2f717-169">O arquivo de dependências é modificado para definir o local de runtime do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="2f717-170">O assembly de inicialização de hospedagem e seu arquivo de dependências são colocados no repositório do pacote de runtime.</span><span class="sxs-lookup"><span data-stu-id="2f717-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="2f717-171">Para descobrir o assembly de inicialização de hospedagem e seu arquivo de dependências, eles são listados em um par de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2f717-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="2f717-172">O assembly de aplicativo do console referencia o pacote [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="2f717-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="2f717-173">Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica uma `IHostingStartup` classe como uma implementação de para carregamento e execução ao construir o <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="2f717-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="2f717-174">No seguinte exemplo, o namespace é `StartupEnhancement` e a classe é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="2f717-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="2f717-175">Uma classe implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="2f717-176">O método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> da classe <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> usa um para adicionar melhorias a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="2f717-177">`IHostingStartup.Configure` no assembly de inicialização de hospedagem é chamado pelo runtime antes de `Startup.Configure` no código do usuário, o que permite que o código de usuário substitua qualquer configuração fornecida pelo assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="2f717-178">Ao criar um projeto `IHostingStartup`, o arquivo de dependências (*.deps.json*) define o local `runtime` do assembly como a pasta *bin*:</span><span class="sxs-lookup"><span data-stu-id="2f717-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="2f717-179">Apenas uma parte do arquivo é mostrada.</span><span class="sxs-lookup"><span data-stu-id="2f717-179">Only part of the file is shown.</span></span> <span data-ttu-id="2f717-180">O nome do assembly no exemplo é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="2f717-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="2f717-181">Configuração fornecida pela inicialização da hospedagem</span><span class="sxs-lookup"><span data-stu-id="2f717-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="2f717-182">Existem duas abordagens para lidar com a configuração, dependendo se você deseja que a configuração da inicialização da hospedagem tenha precedência ou que a configuração do aplicativo tenha precedência:</span><span class="sxs-lookup"><span data-stu-id="2f717-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="2f717-183">Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> para carregar a configuração depois que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo forem executados.</span><span class="sxs-lookup"><span data-stu-id="2f717-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="2f717-184">A configuração de inicialização de hospedagem tem prioridade sobre a configuração do aplicativo que usa essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="2f717-185">Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> para carregar a configuração antes que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo sejam executados.</span><span class="sxs-lookup"><span data-stu-id="2f717-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="2f717-186">Os valores de configuração do aplicativo têm prioridade sobre aqueles fornecidos pela inicialização da hospedagem que usa essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="2f717-187">Especificar o assembly de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="2f717-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="2f717-188">Para uma biblioteca de classes ou inicialização de hospedagem fornecida pelo aplicativo de console, especifique o nome do assembly de inicialização de hospedagem na variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="2f717-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="2f717-189">A variável de ambiente é uma lista de assemblies delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="2f717-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="2f717-190">Apenas assemblies de inicialização de hospedagem são examinados quanto ao atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="2f717-191">Para o aplicativo de exemplo, *HostingStartupApp*, para descobrir as inicializações de hospedagem descritas anteriormente, a variável de ambiente é definida como o seguinte valor:</span><span class="sxs-lookup"><span data-stu-id="2f717-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="2f717-192">Um conjunto de inicialização de hospedagem também pode ser definido usando a configuração de configuração de host Assemblys hosting assemblys:</span><span class="sxs-lookup"><span data-stu-id="2f717-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

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

<span data-ttu-id="2f717-193">Quando várias montagens de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> inicialização estão presentes, seus métodos são executados na ordem em que os conjuntos são listados.</span><span class="sxs-lookup"><span data-stu-id="2f717-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="2f717-194">Ativação</span><span class="sxs-lookup"><span data-stu-id="2f717-194">Activation</span></span>

<span data-ttu-id="2f717-195">As opções para ativação da inicialização de hospedagem são:</span><span class="sxs-lookup"><span data-stu-id="2f717-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="2f717-196">[Repositório de runtime](#runtime-store) &ndash; A ativação não requer uma referência de tempo de compilação para a ativação.</span><span class="sxs-lookup"><span data-stu-id="2f717-196">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="2f717-197">O aplicativo de exemplo coloca os arquivos de dependências e o assembly de inicialização de hospedagem em uma pasta, *implantação*, para facilitar a implantação da inicialização de hospedagem em um ambiente multicomputador.</span><span class="sxs-lookup"><span data-stu-id="2f717-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="2f717-198">A pasta *implantação* também inclui um script do PowerShell que cria ou modifica variáveis de ambiente no sistema de implantação para habilitar a inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="2f717-199">Referência de tempo de compilação necessária para a ativação</span><span class="sxs-lookup"><span data-stu-id="2f717-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="2f717-200">Pacote NuGet</span><span class="sxs-lookup"><span data-stu-id="2f717-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="2f717-201">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="2f717-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="2f717-202">Repositório de runtime</span><span class="sxs-lookup"><span data-stu-id="2f717-202">Runtime store</span></span>

<span data-ttu-id="2f717-203">A implementação de inicialização de hospedagem é colocada no [repositório de runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="2f717-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="2f717-204">Uma referência de tempo de compilação para o assembly não é exigida pelo aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="2f717-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="2f717-205">Depois que a inicialização de hospedagem é compilada, um repositório de runtime é gerado, usando o arquivo de projeto do manifesto e o comando do [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="2f717-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="2f717-206">No aplicativo de exemplo (projeto *RuntimeStore*) é usado o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2f717-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="2f717-207">Para o runtime descobrir o repositório de runtime, o local do repositório de runtime é adicionado à variável de ambiente `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="2f717-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="2f717-208">**Modificar e colocar o arquivo de dependências da inicialização de hospedagem**</span><span class="sxs-lookup"><span data-stu-id="2f717-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="2f717-209">Para ativar o aprimoramento sem uma referência de pacote ao aprimoramento, especifique as dependências adicionais do runtime com `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="2f717-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="2f717-210">`additionalDeps` permite que você:</span><span class="sxs-lookup"><span data-stu-id="2f717-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="2f717-211">Amplie o grafo de biblioteca do aplicativo, fornecendo um conjunto de arquivos *.deps.json* adicionais a serem mesclados com o arquivo *.deps.json* próprio do aplicativo na inicialização.</span><span class="sxs-lookup"><span data-stu-id="2f717-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="2f717-212">Torne o assembly de inicialização de hospedagem detectável e carregável.</span><span class="sxs-lookup"><span data-stu-id="2f717-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="2f717-213">A abordagem recomendada para gerar o arquivo de dependências adicionais é:</span><span class="sxs-lookup"><span data-stu-id="2f717-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="2f717-214">Executar o `dotnet publish` no arquivo de manifesto do repositório de runtime mencionado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="2f717-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="2f717-215">Remova a referência manifesto das `runtime` bibliotecas e da seção do arquivo *.deps.json* resultante.</span><span class="sxs-lookup"><span data-stu-id="2f717-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="2f717-216">No projeto de exemplo, a propriedade `store.manifest/1.0.0` é removida das seções `targets` e `libraries`:</span><span class="sxs-lookup"><span data-stu-id="2f717-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="2f717-217">Coloque o arquivo *.deps.json* no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="2f717-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="2f717-218">`{ADDITIONAL DEPENDENCIES PATH}`&ndash; Localização adicionada `DOTNET_ADDITIONAL_DEPS` à variável ambiente.</span><span class="sxs-lookup"><span data-stu-id="2f717-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="2f717-219">`{SHARED FRAMEWORK NAME}`&ndash; Estrutura compartilhada necessária para este arquivo de dependências adicionais.</span><span class="sxs-lookup"><span data-stu-id="2f717-219">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="2f717-220">`{SHARED FRAMEWORK VERSION}`&ndash; Versão de quadro compartilhado mínimo.</span><span class="sxs-lookup"><span data-stu-id="2f717-220">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="2f717-221">`{ENHANCEMENT ASSEMBLY NAME}`&ndash; O nome de montagem do aprimoramento.</span><span class="sxs-lookup"><span data-stu-id="2f717-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="2f717-222">No aplicativo de exemplo (projeto *RuntimeStore*), o arquivo de dependências adicionais é colocado no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="2f717-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="2f717-223">Para o runtime descobrir o local do repositório de runtime, o local do arquivo de dependências adicionais é adicionado à variável de ambiente `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="2f717-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="2f717-224">No aplicativo de exemplo (projeto *RuntimeStore*), crie o repositório de tempo de execução e gere o arquivo de dependências adicionais usando um script [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="2f717-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="2f717-225">Para obter exemplos de como definir variáveis de ambiente para vários sistemas operacionais, confira [Usar vários ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="2f717-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="2f717-226">**Implantação**</span><span class="sxs-lookup"><span data-stu-id="2f717-226">**Deployment**</span></span>

<span data-ttu-id="2f717-227">Para facilitar a implantação de uma inicialização de hospedagem em um ambiente multicomputador, o aplicativo de exemplo cria uma pasta *implantação* na saída publicada que contém:</span><span class="sxs-lookup"><span data-stu-id="2f717-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="2f717-228">O repositório de runtime de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="2f717-229">O arquivo de dependências de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="2f717-230">Um script do PowerShell que cria ou modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` e `DOTNET_ADDITIONAL_DEPS` para dar suporte à ativação da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="2f717-231">Execute o script de um prompt de comando do PowerShell administrativo no sistema de implantação.</span><span class="sxs-lookup"><span data-stu-id="2f717-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="2f717-232">Pacote NuGet</span><span class="sxs-lookup"><span data-stu-id="2f717-232">NuGet package</span></span>

<span data-ttu-id="2f717-233">Uma melhoria da inicialização de hospedagem pode ser fornecida em pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="2f717-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="2f717-234">O pacote tem um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="2f717-235">Os tipos de inicialização de hospedagem fornecidos pelo pacote são disponibilizados para o aplicativo usando qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="2f717-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="2f717-236">O arquivo de projeto do aplicativo aprimorado faz uma referência de pacote para a inicialização de hospedagem no arquivo de projeto do aplicativo (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="2f717-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="2f717-237">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="2f717-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="2f717-238">Essa abordagem se aplica a um pacote de assembly de inicialização de hospedagem publicado para [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="2f717-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="2f717-239">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de runtime](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="2f717-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="2f717-240">Para obter mais informações sobre pacotes do NuGet e o repositório de runtime, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="2f717-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="2f717-241">Como criar um pacote do NuGet com ferramentas de plataforma cruzada</span><span class="sxs-lookup"><span data-stu-id="2f717-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="2f717-242">Publicando pacotes</span><span class="sxs-lookup"><span data-stu-id="2f717-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="2f717-243">Repositório de pacotes de runtime</span><span class="sxs-lookup"><span data-stu-id="2f717-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="2f717-244">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="2f717-244">Project bin folder</span></span>

<span data-ttu-id="2f717-245">Uma melhoria da inicialização de hospedagem pode ser fornecida por um assemby implantado na *lixeira* no aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="2f717-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="2f717-246">Os tipos de inicialização de hospedagem fornecidos pelo assembly são disponibilizados para o aplicativo usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="2f717-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="2f717-247">O arquivo de projeto do aplicativo aprimorado faz uma referência de assembly para a inicialização de hospedagem (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="2f717-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="2f717-248">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="2f717-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="2f717-249">Essa abordagem se aplica quando o cenário de implantação demanda fazer uma referência de tempo de compilação ao assembly da inicialização de hospedagem (arquivo *.dll*) e mover o assembly para:</span><span class="sxs-lookup"><span data-stu-id="2f717-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="2f717-250">o projeto de consumo.</span><span class="sxs-lookup"><span data-stu-id="2f717-250">The consuming project.</span></span>
  * <span data-ttu-id="2f717-251">um local acessível ao projeto de consumo.</span><span class="sxs-lookup"><span data-stu-id="2f717-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="2f717-252">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de runtime](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="2f717-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="2f717-253">Ao ter como destino o .NET Framework, o assembly é carregável no contexto de carga padrão, o que, no .NET Framework, significa que ele fica em um dos seguintes locais:</span><span class="sxs-lookup"><span data-stu-id="2f717-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="2f717-254">caminho base do aplicativo &ndash; a pasta *bin* onde o executável do aplicativo(*.exe*) está localizado.</span><span class="sxs-lookup"><span data-stu-id="2f717-254">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="2f717-255">GAC (cache de assembly global) &ndash; o GAC armazena assemblies que vários aplicativos .NET Framework compartilham.</span><span class="sxs-lookup"><span data-stu-id="2f717-255">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="2f717-256">Para obter mais informações, [consulte Como instalar um conjunto no cache de montagem global](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) na documentação do .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="2f717-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="2f717-257">Código de exemplo</span><span class="sxs-lookup"><span data-stu-id="2f717-257">Sample code</span></span>

<span data-ttu-id="2f717-258">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample)) demonstra cenários de implementação de inicialização de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="2f717-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="2f717-259">Dois assemblies de inicialização de hospedagem (bibliotecas de classes) definem um par chave-valor de configuração na memória cada:</span><span class="sxs-lookup"><span data-stu-id="2f717-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="2f717-260">Pacote do NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="2f717-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="2f717-261">Biblioteca de classes (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="2f717-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="2f717-262">Uma inicialização de hospedagem é ativada de um assembly implantado pelo repositório de runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="2f717-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="2f717-263">O assembly adiciona dois middlewares ao aplicativo na inicialização que fornecem informações de diagnóstico sobre:</span><span class="sxs-lookup"><span data-stu-id="2f717-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="2f717-264">Serviços registrados</span><span class="sxs-lookup"><span data-stu-id="2f717-264">Registered services</span></span>
  * <span data-ttu-id="2f717-265">Endereço (esquema, host, base do caminho, caminho, cadeia de caracteres de consulta)</span><span class="sxs-lookup"><span data-stu-id="2f717-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="2f717-266">Conexão (IP remoto, porta remota, IP local, porta local, certificado do cliente)</span><span class="sxs-lookup"><span data-stu-id="2f717-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="2f717-267">Cabeçalhos da solicitação</span><span class="sxs-lookup"><span data-stu-id="2f717-267">Request headers</span></span>
  * <span data-ttu-id="2f717-268">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="2f717-268">Environment variables</span></span>

<span data-ttu-id="2f717-269">Para executar o exemplo:</span><span class="sxs-lookup"><span data-stu-id="2f717-269">To run the sample:</span></span>

<span data-ttu-id="2f717-270">**Ativação de um pacote do NuGet**</span><span class="sxs-lookup"><span data-stu-id="2f717-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="2f717-271">Compile o pacote *HostingStartupPackage* com o comando [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="2f717-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="2f717-272">Adicione o nome do assembly do pacote do *HostingStartupPackage* para a variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="2f717-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="2f717-273">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-273">Compile and run the app.</span></span> <span data-ttu-id="2f717-274">Uma referência de pacote está presente no aplicativo aprimorado (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="2f717-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="2f717-275">Um `<PropertyGroup>` no arquivo de projeto do aplicativo especifica a saída do projeto de pacote (*../HostingStartupPackage/bin/Debug*) como uma origem de pacote.</span><span class="sxs-lookup"><span data-stu-id="2f717-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="2f717-276">Isso permite que o aplicativo use o pacote sem carregar o pacote para [nuget.org](https://www.nuget.org/). Para obter mais informações, consulte as notas no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="2f717-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="2f717-277">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` do pacote.</span><span class="sxs-lookup"><span data-stu-id="2f717-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="2f717-278">Se você fizer alterações no projeto *HostingStartupPackage* e recompilá-lo, limpe os caches de pacote do NuGet locais para garantir que o *HostingStartupApp* receba o pacote atualizado e não um pacote obsoleto do cache local.</span><span class="sxs-lookup"><span data-stu-id="2f717-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="2f717-279">Para limpar os caches locais do NuGet, execute o seguinte comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="2f717-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="2f717-280">**Ativação de uma biblioteca de classes**</span><span class="sxs-lookup"><span data-stu-id="2f717-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="2f717-281">Compile a biblioteca de classes *HostingStartupLibrary* com o comando [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="2f717-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="2f717-282">Adicione nome do assembly da biblioteca de classes do *HostingStartupLibrary* à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="2f717-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="2f717-283">A pasta *lixeira* implanta o assembly da biblioteca de classes para o aplicativo ao copiar o arquivo *HostingStartupLibrary.dll* da saída compilada da biblioteca de classes para a pasta *lixeira/Depurar* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="2f717-284">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-284">Compile and run the app.</span></span> <span data-ttu-id="2f717-285">Um `<ItemGroup>` arquivo de projeto do aplicativo faz referência ao conjunto da biblioteca de classe *(.\bin\Debug\debug\netcoreapp3.0\HostingStartupLibrary.dll)*(uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="2f717-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="2f717-286">Para mais informações, consulte as notas no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="2f717-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="2f717-287">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` da biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="2f717-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="2f717-288">**Ativação de um assembly implantado pelo repositório de runtime**</span><span class="sxs-lookup"><span data-stu-id="2f717-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="2f717-289">O projeto *StartupDiagnostics* usa o [PowerShell](/powershell/scripting/powershell-scripting) para modificar seu arquivo *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="2f717-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="2f717-290">O PowerShell é instalado por padrão em um sistema operacional Windows começando no Windows 7 SP1 e no Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="2f717-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="2f717-291">Para obter o PowerShell em outras plataformas, consulte [Instalando várias versões do PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="2f717-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="2f717-292">Execute o script *build.ps1* na pasta *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="2f717-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="2f717-293">O script:</span><span class="sxs-lookup"><span data-stu-id="2f717-293">The script:</span></span>
   * <span data-ttu-id="2f717-294">Gera `StartupDiagnostics` o pacote na pasta *obj\packages.*</span><span class="sxs-lookup"><span data-stu-id="2f717-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="2f717-295">Gera o repositório de runtime para `StartupDiagnostics` na pasta de *armazenamento*.</span><span class="sxs-lookup"><span data-stu-id="2f717-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="2f717-296">O comando `dotnet store` no script usa o `win7-x64` [RID (identificador de runtime)](/dotnet/core/rid-catalog) para uma inicialização de host implantada no Windows.</span><span class="sxs-lookup"><span data-stu-id="2f717-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="2f717-297">Ao fornecer a inicialização de hospedagem para um runtime diferente, substitua pelo RID correto na linha 37 do script.</span><span class="sxs-lookup"><span data-stu-id="2f717-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="2f717-298">A loja de `StartupDiagnostics` tempo de execução para posteriormente seria movida para a loja de tempo de execução do usuário ou do sistema na máquina onde o conjunto será consumido.</span><span class="sxs-lookup"><span data-stu-id="2f717-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="2f717-299">O local de instalação da `StartupDiagnostics` loja de tempo de execução do usuário para o conjunto é *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="2f717-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="2f717-300">Gera `additionalDeps` o `StartupDiagnostics` for na pasta *AdicionalDeps.*</span><span class="sxs-lookup"><span data-stu-id="2f717-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="2f717-301">As dependências adicionais seriam posteriormente movidas para as dependências adicionais do usuário ou do sistema.</span><span class="sxs-lookup"><span data-stu-id="2f717-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="2f717-302">A `StartupDiagnostics` localização de instalação adicional do usuário é *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="2f717-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="2f717-303">Coloca o arquivo *deploy.ps1* na pasta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="2f717-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="2f717-304">Execute o script *deploy.ps1* na pasta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="2f717-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="2f717-305">O script acrescenta:</span><span class="sxs-lookup"><span data-stu-id="2f717-305">The script appends:</span></span>
   * <span data-ttu-id="2f717-306">`StartupDiagnostics` à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="2f717-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="2f717-307">O caminho de dependências de inicialização de inicialização (na pasta de *implantação* do projeto RuntimeStore) para a `DOTNET_ADDITIONAL_DEPS` variável ambiente.</span><span class="sxs-lookup"><span data-stu-id="2f717-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="2f717-308">O caminho da loja de tempo de *deployment* execução (na `DOTNET_SHARED_STORE` pasta de implantação do projeto RuntimeStore) para a variável ambiente.</span><span class="sxs-lookup"><span data-stu-id="2f717-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="2f717-309">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="2f717-309">Run the sample app.</span></span>
1. <span data-ttu-id="2f717-310">Solicite o ponto de extremidade `/services` para ver os serviços registrados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="2f717-311">Solicite o ponto de extremidade `/diag` para ver as informações de diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="2f717-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2f717-312">Uma <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementação (hosting startup) adiciona melhorias a um aplicativo na inicialização a partir de um conjunto externo.</span><span class="sxs-lookup"><span data-stu-id="2f717-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="2f717-313">Por exemplo, uma biblioteca externa pode usar uma implementação de inicialização de hospedagem para fornecer serviços ou provedores de configuração adicionais a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="2f717-314">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2f717-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="2f717-315">Atributo HostingStartup</span><span class="sxs-lookup"><span data-stu-id="2f717-315">HostingStartup attribute</span></span>

<span data-ttu-id="2f717-316">Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica a presença de um assembly de inicialização de hospedagem para ativar em runtime.</span><span class="sxs-lookup"><span data-stu-id="2f717-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="2f717-317">O assembly de entrada ou o assembly que contém a classe `Startup` é automaticamente examinado para o atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="2f717-318">A lista de assemblies a ser pesquisada para os atributos `HostingStartup` é carregada no runtime da configuração em [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="2f717-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="2f717-319">A lista de assemblies para excluir da descoberta é carregada de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="2f717-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="2f717-320">Para obter mais informações, consulte [Host da Web: assemblies de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-assemblies) e [Host da Web: assemblies de exclusão da inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="2f717-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="2f717-321">No exemplo a seguir, o namespace do assembly de inicialização de hospedagem é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="2f717-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="2f717-322">A classe que contém o código de inicialização de hospedagem é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="2f717-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="2f717-323">O atributo `HostingStartup` normalmente está localizado no arquivo de classe de implementação `IHostingStartup` do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="2f717-324">Descobrir assemblies de inicialização de hospedagem carregados</span><span class="sxs-lookup"><span data-stu-id="2f717-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="2f717-325">Para descobrir os assemblies de inicialização de hospedagem carregados, habilite o registro em log e verifique os logs do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="2f717-326">Erros que ocorrem quando os assemblies carregados são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="2f717-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="2f717-327">Os assemblies de inicialização de hospedagem carregados são registrados em log no nível Depuração e todos os erros são registrados.</span><span class="sxs-lookup"><span data-stu-id="2f717-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="2f717-328">Desabilitar o carregamento automático de assemblies de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="2f717-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="2f717-329">Para desabilitar o carregamento automático de assemblies de inicialização de hospedagem, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="2f717-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="2f717-330">Para impedir o carregamento de todos os assemblies de inicialização de hospedagem, defina o seguinte para `true` ou `1`:</span><span class="sxs-lookup"><span data-stu-id="2f717-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="2f717-331">Configuração do host [Impedir inicialização de hospedagem](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="2f717-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="2f717-332">A variável de ambiente `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="2f717-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="2f717-333">Para evitar o carregamento de assemblies específicos de inicialização de hospedagem, defina uma das opções a seguir como uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para excluir na inicialização:</span><span class="sxs-lookup"><span data-stu-id="2f717-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="2f717-334">Configuração do host [Assemblies de exclusão de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="2f717-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="2f717-335">A variável de ambiente `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="2f717-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="2f717-336">Se a configuração do host e a variável de ambiente estiverem definidas, a configuração do host controlará o comportamento.</span><span class="sxs-lookup"><span data-stu-id="2f717-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="2f717-337">A desabilitação de assemblies de inicialização de hospedagem usando a configuração do host ou a variável de ambiente desabilita o assembly globalmente e poderá desabilitar várias características de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="2f717-338">Project</span><span class="sxs-lookup"><span data-stu-id="2f717-338">Project</span></span>

<span data-ttu-id="2f717-339">Crie uma inicialização de hospedagem com qualquer um dos seguintes tipos de projeto:</span><span class="sxs-lookup"><span data-stu-id="2f717-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="2f717-340">Biblioteca de classe</span><span class="sxs-lookup"><span data-stu-id="2f717-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="2f717-341">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="2f717-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="2f717-342">Biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="2f717-342">Class library</span></span>

<span data-ttu-id="2f717-343">Uma melhoria da inicialização de hospedagem pode ser fornecida em uma biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="2f717-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="2f717-344">A biblioteca contém um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="2f717-345">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclui um aplicativo Razor Pages, *HostingStartupApp* e uma biblioteca de classes, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="2f717-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="2f717-346">A biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="2f717-346">The class library:</span></span>

* <span data-ttu-id="2f717-347">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="2f717-348">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço à configuração do aplicativo usando o provedor de configuração na memória ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="2f717-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="2f717-349">Inclui um atributo `HostingStartup` que identifica o namespace e a classe de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="2f717-350">O `ServiceKeyInjection` método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> da classe <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> usa um para adicionar melhorias a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="2f717-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f717-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="2f717-352">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem da biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="2f717-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="2f717-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f717-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="2f717-354">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) também inclui um projeto de pacote do NuGet que fornece uma inicialização de hospedagem separada, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="2f717-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="2f717-355">O pacote tem as mesmas características da biblioteca de classes descrita anteriormente.</span><span class="sxs-lookup"><span data-stu-id="2f717-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="2f717-356">O pacote:</span><span class="sxs-lookup"><span data-stu-id="2f717-356">The package:</span></span>

* <span data-ttu-id="2f717-357">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="2f717-358">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço para a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="2f717-359">Inclui um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="2f717-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f717-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="2f717-361">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem do pacote:</span><span class="sxs-lookup"><span data-stu-id="2f717-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="2f717-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f717-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="2f717-363">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="2f717-363">Console app without an entry point</span></span>

<span data-ttu-id="2f717-364">*Essa abordagem só está disponível para aplicativos .NET Core, não para .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="2f717-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="2f717-365">Uma melhoria de inicialização de hospedagem dinâmica que não requer uma referência de tempo de compilação para a ativação pode ser fornecida em um aplicativo de console sem um ponto de entrada que contenha um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="2f717-366">Publicar o aplicativo de console produz um assembly de inicialização de hospedagem que pode ser consumido do repositório de runtime.</span><span class="sxs-lookup"><span data-stu-id="2f717-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="2f717-367">Um aplicativo de console sem um ponto de entrada é usado nesse processo porque:</span><span class="sxs-lookup"><span data-stu-id="2f717-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="2f717-368">Um arquivo de dependências é necessário para consumir a inicialização de hospedagem no assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="2f717-369">Um arquivo de dependências é um ativo de aplicativo executável que é produzido ao publicar um aplicativo, não uma biblioteca.</span><span class="sxs-lookup"><span data-stu-id="2f717-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="2f717-370">Uma biblioteca não pode ser adicionada diretamente ao [repositório de pacotes de runtime](/dotnet/core/deploying/runtime-store), o que exige um projeto executável que tem como alvo o runtime compartilhado.</span><span class="sxs-lookup"><span data-stu-id="2f717-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="2f717-371">Na criação de uma inicialização de hospedagem dinâmica:</span><span class="sxs-lookup"><span data-stu-id="2f717-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="2f717-372">Um assembly de inicialização de hospedagem é criado no aplicativo de console sem um ponto de entrada que:</span><span class="sxs-lookup"><span data-stu-id="2f717-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="2f717-373">Inclui uma classe que contém a implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="2f717-374">Inclui um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) para identificar a classe de implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="2f717-375">O aplicativo de console é publicado para obter as dependências da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="2f717-376">Uma consequência de publicar o aplicativo de console é que as dependências não utilizadas são cortadas do arquivo de dependências.</span><span class="sxs-lookup"><span data-stu-id="2f717-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="2f717-377">O arquivo de dependências é modificado para definir o local de runtime do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="2f717-378">O assembly de inicialização de hospedagem e seu arquivo de dependências são colocados no repositório do pacote de runtime.</span><span class="sxs-lookup"><span data-stu-id="2f717-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="2f717-379">Para descobrir o assembly de inicialização de hospedagem e seu arquivo de dependências, eles são listados em um par de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2f717-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="2f717-380">O assembly de aplicativo do console referencia o pacote [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="2f717-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="2f717-381">Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica uma `IHostingStartup` classe como uma implementação de para carregamento e execução ao construir o <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="2f717-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="2f717-382">No seguinte exemplo, o namespace é `StartupEnhancement` e a classe é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="2f717-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="2f717-383">Uma classe implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="2f717-384">O método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> da classe <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> usa um para adicionar melhorias a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="2f717-385">`IHostingStartup.Configure` no assembly de inicialização de hospedagem é chamado pelo runtime antes de `Startup.Configure` no código do usuário, o que permite que o código de usuário substitua qualquer configuração fornecida pelo assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="2f717-386">Ao criar um projeto `IHostingStartup`, o arquivo de dependências (*.deps.json*) define o local `runtime` do assembly como a pasta *bin*:</span><span class="sxs-lookup"><span data-stu-id="2f717-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="2f717-387">Apenas uma parte do arquivo é mostrada.</span><span class="sxs-lookup"><span data-stu-id="2f717-387">Only part of the file is shown.</span></span> <span data-ttu-id="2f717-388">O nome do assembly no exemplo é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="2f717-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="2f717-389">Configuração fornecida pela inicialização da hospedagem</span><span class="sxs-lookup"><span data-stu-id="2f717-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="2f717-390">Existem duas abordagens para lidar com a configuração, dependendo se você deseja que a configuração da inicialização da hospedagem tenha precedência ou que a configuração do aplicativo tenha precedência:</span><span class="sxs-lookup"><span data-stu-id="2f717-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="2f717-391">Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> para carregar a configuração depois que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo forem executados.</span><span class="sxs-lookup"><span data-stu-id="2f717-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="2f717-392">A configuração de inicialização de hospedagem tem prioridade sobre a configuração do aplicativo que usa essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="2f717-393">Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> para carregar a configuração antes que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo sejam executados.</span><span class="sxs-lookup"><span data-stu-id="2f717-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="2f717-394">Os valores de configuração do aplicativo têm prioridade sobre aqueles fornecidos pela inicialização da hospedagem que usa essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="2f717-395">Especificar o assembly de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="2f717-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="2f717-396">Para uma biblioteca de classes ou inicialização de hospedagem fornecida pelo aplicativo de console, especifique o nome do assembly de inicialização de hospedagem na variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="2f717-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="2f717-397">A variável de ambiente é uma lista de assemblies delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="2f717-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="2f717-398">Apenas assemblies de inicialização de hospedagem são examinados quanto ao atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="2f717-399">Para o aplicativo de exemplo, *HostingStartupApp*, para descobrir as inicializações de hospedagem descritas anteriormente, a variável de ambiente é definida como o seguinte valor:</span><span class="sxs-lookup"><span data-stu-id="2f717-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="2f717-400">Um assembly de inicialização de hospedagem também pode ser definido usando a configuração do host [Assemblies de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-assemblies).</span><span class="sxs-lookup"><span data-stu-id="2f717-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="2f717-401">Quando várias montagens de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> inicialização estão presentes, seus métodos são executados na ordem em que os conjuntos são listados.</span><span class="sxs-lookup"><span data-stu-id="2f717-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="2f717-402">Ativação</span><span class="sxs-lookup"><span data-stu-id="2f717-402">Activation</span></span>

<span data-ttu-id="2f717-403">As opções para ativação da inicialização de hospedagem são:</span><span class="sxs-lookup"><span data-stu-id="2f717-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="2f717-404">[Repositório de runtime](#runtime-store) &ndash; A ativação não requer uma referência de tempo de compilação para a ativação.</span><span class="sxs-lookup"><span data-stu-id="2f717-404">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="2f717-405">O aplicativo de exemplo coloca os arquivos de dependências e o assembly de inicialização de hospedagem em uma pasta, *implantação*, para facilitar a implantação da inicialização de hospedagem em um ambiente multicomputador.</span><span class="sxs-lookup"><span data-stu-id="2f717-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="2f717-406">A pasta *implantação* também inclui um script do PowerShell que cria ou modifica variáveis de ambiente no sistema de implantação para habilitar a inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="2f717-407">Referência de tempo de compilação necessária para a ativação</span><span class="sxs-lookup"><span data-stu-id="2f717-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="2f717-408">Pacote NuGet</span><span class="sxs-lookup"><span data-stu-id="2f717-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="2f717-409">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="2f717-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="2f717-410">Repositório de runtime</span><span class="sxs-lookup"><span data-stu-id="2f717-410">Runtime store</span></span>

<span data-ttu-id="2f717-411">A implementação de inicialização de hospedagem é colocada no [repositório de runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="2f717-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="2f717-412">Uma referência de tempo de compilação para o assembly não é exigida pelo aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="2f717-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="2f717-413">Depois que a inicialização de hospedagem é compilada, um repositório de runtime é gerado, usando o arquivo de projeto do manifesto e o comando do [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="2f717-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="2f717-414">No aplicativo de exemplo (projeto *RuntimeStore*) é usado o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2f717-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="2f717-415">Para o runtime descobrir o repositório de runtime, o local do repositório de runtime é adicionado à variável de ambiente `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="2f717-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="2f717-416">**Modificar e colocar o arquivo de dependências da inicialização de hospedagem**</span><span class="sxs-lookup"><span data-stu-id="2f717-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="2f717-417">Para ativar o aprimoramento sem uma referência de pacote ao aprimoramento, especifique as dependências adicionais do runtime com `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="2f717-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="2f717-418">`additionalDeps` permite que você:</span><span class="sxs-lookup"><span data-stu-id="2f717-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="2f717-419">Amplie o grafo de biblioteca do aplicativo, fornecendo um conjunto de arquivos *.deps.json* adicionais a serem mesclados com o arquivo *.deps.json* próprio do aplicativo na inicialização.</span><span class="sxs-lookup"><span data-stu-id="2f717-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="2f717-420">Torne o assembly de inicialização de hospedagem detectável e carregável.</span><span class="sxs-lookup"><span data-stu-id="2f717-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="2f717-421">A abordagem recomendada para gerar o arquivo de dependências adicionais é:</span><span class="sxs-lookup"><span data-stu-id="2f717-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="2f717-422">Executar o `dotnet publish` no arquivo de manifesto do repositório de runtime mencionado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="2f717-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="2f717-423">Remova a referência manifesto das `runtime` bibliotecas e da seção do arquivo *.deps.json* resultante.</span><span class="sxs-lookup"><span data-stu-id="2f717-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="2f717-424">No projeto de exemplo, a propriedade `store.manifest/1.0.0` é removida das seções `targets` e `libraries`:</span><span class="sxs-lookup"><span data-stu-id="2f717-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="2f717-425">Coloque o arquivo *.deps.json* no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="2f717-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="2f717-426">`{ADDITIONAL DEPENDENCIES PATH}`&ndash; Localização adicionada `DOTNET_ADDITIONAL_DEPS` à variável ambiente.</span><span class="sxs-lookup"><span data-stu-id="2f717-426">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="2f717-427">`{SHARED FRAMEWORK NAME}`&ndash; Estrutura compartilhada necessária para este arquivo de dependências adicionais.</span><span class="sxs-lookup"><span data-stu-id="2f717-427">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="2f717-428">`{SHARED FRAMEWORK VERSION}`&ndash; Versão de quadro compartilhado mínimo.</span><span class="sxs-lookup"><span data-stu-id="2f717-428">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="2f717-429">`{ENHANCEMENT ASSEMBLY NAME}`&ndash; O nome de montagem do aprimoramento.</span><span class="sxs-lookup"><span data-stu-id="2f717-429">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="2f717-430">No aplicativo de exemplo (projeto *RuntimeStore*), o arquivo de dependências adicionais é colocado no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="2f717-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="2f717-431">Para o runtime descobrir o local do repositório de runtime, o local do arquivo de dependências adicionais é adicionado à variável de ambiente `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="2f717-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="2f717-432">No aplicativo de exemplo (projeto *RuntimeStore*), crie o repositório de tempo de execução e gere o arquivo de dependências adicionais usando um script [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="2f717-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="2f717-433">Para obter exemplos de como definir variáveis de ambiente para vários sistemas operacionais, confira [Usar vários ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="2f717-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="2f717-434">**Implantação**</span><span class="sxs-lookup"><span data-stu-id="2f717-434">**Deployment**</span></span>

<span data-ttu-id="2f717-435">Para facilitar a implantação de uma inicialização de hospedagem em um ambiente multicomputador, o aplicativo de exemplo cria uma pasta *implantação* na saída publicada que contém:</span><span class="sxs-lookup"><span data-stu-id="2f717-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="2f717-436">O repositório de runtime de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="2f717-437">O arquivo de dependências de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="2f717-438">Um script do PowerShell que cria ou modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` e `DOTNET_ADDITIONAL_DEPS` para dar suporte à ativação da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2f717-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="2f717-439">Execute o script de um prompt de comando do PowerShell administrativo no sistema de implantação.</span><span class="sxs-lookup"><span data-stu-id="2f717-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="2f717-440">Pacote NuGet</span><span class="sxs-lookup"><span data-stu-id="2f717-440">NuGet package</span></span>

<span data-ttu-id="2f717-441">Uma melhoria da inicialização de hospedagem pode ser fornecida em pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="2f717-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="2f717-442">O pacote tem um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f717-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="2f717-443">Os tipos de inicialização de hospedagem fornecidos pelo pacote são disponibilizados para o aplicativo usando qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="2f717-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="2f717-444">O arquivo de projeto do aplicativo aprimorado faz uma referência de pacote para a inicialização de hospedagem no arquivo de projeto do aplicativo (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="2f717-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="2f717-445">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="2f717-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="2f717-446">Essa abordagem se aplica a um pacote de assembly de inicialização de hospedagem publicado para [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="2f717-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="2f717-447">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de runtime](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="2f717-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="2f717-448">Para obter mais informações sobre pacotes do NuGet e o repositório de runtime, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="2f717-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="2f717-449">Como criar um pacote do NuGet com ferramentas de plataforma cruzada</span><span class="sxs-lookup"><span data-stu-id="2f717-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="2f717-450">Publicando pacotes</span><span class="sxs-lookup"><span data-stu-id="2f717-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="2f717-451">Repositório de pacotes de runtime</span><span class="sxs-lookup"><span data-stu-id="2f717-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="2f717-452">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="2f717-452">Project bin folder</span></span>

<span data-ttu-id="2f717-453">Uma melhoria da inicialização de hospedagem pode ser fornecida por um assemby implantado na *lixeira* no aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="2f717-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="2f717-454">Os tipos de inicialização de hospedagem fornecidos pelo assembly são disponibilizados para o aplicativo usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="2f717-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="2f717-455">O arquivo de projeto do aplicativo aprimorado faz uma referência de assembly para a inicialização de hospedagem (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="2f717-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="2f717-456">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="2f717-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="2f717-457">Essa abordagem se aplica quando o cenário de implantação demanda fazer uma referência de tempo de compilação ao assembly da inicialização de hospedagem (arquivo *.dll*) e mover o assembly para:</span><span class="sxs-lookup"><span data-stu-id="2f717-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="2f717-458">o projeto de consumo.</span><span class="sxs-lookup"><span data-stu-id="2f717-458">The consuming project.</span></span>
  * <span data-ttu-id="2f717-459">um local acessível ao projeto de consumo.</span><span class="sxs-lookup"><span data-stu-id="2f717-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="2f717-460">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de runtime](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="2f717-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="2f717-461">Ao ter como destino o .NET Framework, o assembly é carregável no contexto de carga padrão, o que, no .NET Framework, significa que ele fica em um dos seguintes locais:</span><span class="sxs-lookup"><span data-stu-id="2f717-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="2f717-462">caminho base do aplicativo &ndash; a pasta *bin* onde o executável do aplicativo(*.exe*) está localizado.</span><span class="sxs-lookup"><span data-stu-id="2f717-462">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="2f717-463">GAC (cache de assembly global) &ndash; o GAC armazena assemblies que vários aplicativos .NET Framework compartilham.</span><span class="sxs-lookup"><span data-stu-id="2f717-463">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="2f717-464">Para obter mais informações, [consulte Como instalar um conjunto no cache de montagem global](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) na documentação do .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="2f717-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="2f717-465">Código de exemplo</span><span class="sxs-lookup"><span data-stu-id="2f717-465">Sample code</span></span>

<span data-ttu-id="2f717-466">O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample)) demonstra cenários de implementação de inicialização de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="2f717-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="2f717-467">Dois assemblies de inicialização de hospedagem (bibliotecas de classes) definem um par chave-valor de configuração na memória cada:</span><span class="sxs-lookup"><span data-stu-id="2f717-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="2f717-468">Pacote do NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="2f717-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="2f717-469">Biblioteca de classes (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="2f717-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="2f717-470">Uma inicialização de hospedagem é ativada de um assembly implantado pelo repositório de runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="2f717-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="2f717-471">O assembly adiciona dois middlewares ao aplicativo na inicialização que fornecem informações de diagnóstico sobre:</span><span class="sxs-lookup"><span data-stu-id="2f717-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="2f717-472">Serviços registrados</span><span class="sxs-lookup"><span data-stu-id="2f717-472">Registered services</span></span>
  * <span data-ttu-id="2f717-473">Endereço (esquema, host, base do caminho, caminho, cadeia de caracteres de consulta)</span><span class="sxs-lookup"><span data-stu-id="2f717-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="2f717-474">Conexão (IP remoto, porta remota, IP local, porta local, certificado do cliente)</span><span class="sxs-lookup"><span data-stu-id="2f717-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="2f717-475">Cabeçalhos da solicitação</span><span class="sxs-lookup"><span data-stu-id="2f717-475">Request headers</span></span>
  * <span data-ttu-id="2f717-476">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="2f717-476">Environment variables</span></span>

<span data-ttu-id="2f717-477">Para executar o exemplo:</span><span class="sxs-lookup"><span data-stu-id="2f717-477">To run the sample:</span></span>

<span data-ttu-id="2f717-478">**Ativação de um pacote do NuGet**</span><span class="sxs-lookup"><span data-stu-id="2f717-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="2f717-479">Compile o pacote *HostingStartupPackage* com o comando [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="2f717-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="2f717-480">Adicione o nome do assembly do pacote do *HostingStartupPackage* para a variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="2f717-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="2f717-481">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-481">Compile and run the app.</span></span> <span data-ttu-id="2f717-482">Uma referência de pacote está presente no aplicativo aprimorado (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="2f717-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="2f717-483">Um `<PropertyGroup>` no arquivo de projeto do aplicativo especifica a saída do projeto de pacote (*../HostingStartupPackage/bin/Debug*) como uma origem de pacote.</span><span class="sxs-lookup"><span data-stu-id="2f717-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="2f717-484">Isso permite que o aplicativo use o pacote sem carregar o pacote para [nuget.org](https://www.nuget.org/). Para obter mais informações, consulte as notas no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="2f717-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="2f717-485">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` do pacote.</span><span class="sxs-lookup"><span data-stu-id="2f717-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="2f717-486">Se você fizer alterações no projeto *HostingStartupPackage* e recompilá-lo, limpe os caches de pacote do NuGet locais para garantir que o *HostingStartupApp* receba o pacote atualizado e não um pacote obsoleto do cache local.</span><span class="sxs-lookup"><span data-stu-id="2f717-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="2f717-487">Para limpar os caches locais do NuGet, execute o seguinte comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="2f717-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="2f717-488">**Ativação de uma biblioteca de classes**</span><span class="sxs-lookup"><span data-stu-id="2f717-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="2f717-489">Compile a biblioteca de classes *HostingStartupLibrary* com o comando [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="2f717-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="2f717-490">Adicione nome do assembly da biblioteca de classes do *HostingStartupLibrary* à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="2f717-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="2f717-491">A pasta *lixeira* implanta o assembly da biblioteca de classes para o aplicativo ao copiar o arquivo *HostingStartupLibrary.dll* da saída compilada da biblioteca de classes para a pasta *lixeira/Depurar* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="2f717-492">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-492">Compile and run the app.</span></span> <span data-ttu-id="2f717-493">Um `<ItemGroup>` do arquivo de projeto do aplicativo referencia o assembly da biblioteca de classes (*.\lixeira\Depurar\netcoreapp2.1\HostingStartupLibrary.dll*) (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="2f717-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="2f717-494">Para mais informações, consulte as notas no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="2f717-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="2f717-495">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` da biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="2f717-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="2f717-496">**Ativação de um assembly implantado pelo repositório de runtime**</span><span class="sxs-lookup"><span data-stu-id="2f717-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="2f717-497">O projeto *StartupDiagnostics* usa o [PowerShell](/powershell/scripting/powershell-scripting) para modificar seu arquivo *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="2f717-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="2f717-498">O PowerShell é instalado por padrão em um sistema operacional Windows começando no Windows 7 SP1 e no Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="2f717-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="2f717-499">Para obter o PowerShell em outras plataformas, consulte [Instalando várias versões do PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="2f717-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="2f717-500">Execute o script *build.ps1* na pasta *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="2f717-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="2f717-501">O script:</span><span class="sxs-lookup"><span data-stu-id="2f717-501">The script:</span></span>
   * <span data-ttu-id="2f717-502">Gera `StartupDiagnostics` o pacote na pasta *obj\packages.*</span><span class="sxs-lookup"><span data-stu-id="2f717-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="2f717-503">Gera o repositório de runtime para `StartupDiagnostics` na pasta de *armazenamento*.</span><span class="sxs-lookup"><span data-stu-id="2f717-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="2f717-504">O comando `dotnet store` no script usa o `win7-x64` [RID (identificador de runtime)](/dotnet/core/rid-catalog) para uma inicialização de host implantada no Windows.</span><span class="sxs-lookup"><span data-stu-id="2f717-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="2f717-505">Ao fornecer a inicialização de hospedagem para um runtime diferente, substitua pelo RID correto na linha 37 do script.</span><span class="sxs-lookup"><span data-stu-id="2f717-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="2f717-506">A loja de `StartupDiagnostics` tempo de execução para posteriormente seria movida para a loja de tempo de execução do usuário ou do sistema na máquina onde o conjunto será consumido.</span><span class="sxs-lookup"><span data-stu-id="2f717-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="2f717-507">O local de instalação da `StartupDiagnostics` loja de tempo de execução do usuário para o conjunto é *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="2f717-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="2f717-508">Gera `additionalDeps` o `StartupDiagnostics` for na pasta *AdicionalDeps.*</span><span class="sxs-lookup"><span data-stu-id="2f717-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="2f717-509">As dependências adicionais seriam posteriormente movidas para as dependências adicionais do usuário ou do sistema.</span><span class="sxs-lookup"><span data-stu-id="2f717-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="2f717-510">A `StartupDiagnostics` localização de instalação adicional do usuário é *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="2f717-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="2f717-511">Coloca o arquivo *deploy.ps1* na pasta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="2f717-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="2f717-512">Execute o script *deploy.ps1* na pasta *deployment*.</span><span class="sxs-lookup"><span data-stu-id="2f717-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="2f717-513">O script acrescenta:</span><span class="sxs-lookup"><span data-stu-id="2f717-513">The script appends:</span></span>
   * <span data-ttu-id="2f717-514">`StartupDiagnostics` à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="2f717-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="2f717-515">O caminho de dependências de inicialização de inicialização (na pasta de *implantação* do projeto RuntimeStore) para a `DOTNET_ADDITIONAL_DEPS` variável ambiente.</span><span class="sxs-lookup"><span data-stu-id="2f717-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="2f717-516">O caminho da loja de tempo de *deployment* execução (na `DOTNET_SHARED_STORE` pasta de implantação do projeto RuntimeStore) para a variável ambiente.</span><span class="sxs-lookup"><span data-stu-id="2f717-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="2f717-517">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="2f717-517">Run the sample app.</span></span>
1. <span data-ttu-id="2f717-518">Solicite o ponto de extremidade `/services` para ver os serviços registrados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f717-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="2f717-519">Solicite o ponto de extremidade `/diag` para ver as informações de diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="2f717-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

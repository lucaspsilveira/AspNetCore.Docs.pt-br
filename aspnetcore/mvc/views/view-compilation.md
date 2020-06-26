---
title: Razorcompilação de arquivo no ASP.NET Core
author: rick-anderson
description: Saiba como a compilação de Razor arquivos ocorre em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/view-compilation
ms.openlocfilehash: 71487ff2d5d7d7cf96835778f386e5f30fa32254
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405439"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Razor<span data-ttu-id="28156-103">compilação de arquivo no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28156-103"> file compilation in ASP.NET Core</span></span>

<span data-ttu-id="28156-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="28156-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

Razor<span data-ttu-id="28156-105">arquivos com uma extensão *. cshtml* são compilados em tempo de compilação e publicação usando o [ Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="28156-105"> files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="28156-106">A compilação em tempo de execução pode ser habilitada opcionalmente Configurando seu projeto.</span><span class="sxs-lookup"><span data-stu-id="28156-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="28156-107">ocorrida</span><span class="sxs-lookup"><span data-stu-id="28156-107"> compilation</span></span>

<span data-ttu-id="28156-108">A compilação de arquivos em tempo de compilação e em tempo de publicação Razor é habilitada por padrão pelo Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="28156-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="28156-109">Quando habilitada, a compilação de tempo de execução complementa a compilação em tempo de compilação, permitindo que Razor os arquivos sejam atualizados se eles forem editados.</span><span class="sxs-lookup"><span data-stu-id="28156-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="28156-110">Habilitar a compilação em tempo de execução na criação do projeto</span><span class="sxs-lookup"><span data-stu-id="28156-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="28156-111">As Razor páginas e os modelos de projeto MVC incluem uma opção para habilitar a compilação em tempo de execução quando o projeto é criado.</span><span class="sxs-lookup"><span data-stu-id="28156-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="28156-112">Essa opção tem suporte no ASP.NET Core 3,1 e posterior.</span><span class="sxs-lookup"><span data-stu-id="28156-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28156-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28156-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28156-114">Na caixa de diálogo **criar um novo aplicativo web ASP.NET Core** :</span><span class="sxs-lookup"><span data-stu-id="28156-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="28156-115">Selecione o modelo de **projeto do aplicativo Web ou do** **aplicativo Web (Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="28156-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="28156-116">Marque a caixa de seleção **habilitar Razor compilação de tempo de execução** .</span><span class="sxs-lookup"><span data-stu-id="28156-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="28156-117">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="28156-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="28156-118">Use a `-rrc` `--razor-runtime-compilation` opção de modelo ou.</span><span class="sxs-lookup"><span data-stu-id="28156-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="28156-119">Por exemplo, o comando a seguir cria um novo Razor projeto de páginas com compilação em tempo de execução habilitada:</span><span class="sxs-lookup"><span data-stu-id="28156-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="28156-120">Habilitar a compilação em tempo de execução em um projeto existente</span><span class="sxs-lookup"><span data-stu-id="28156-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="28156-121">Para habilitar a compilação em tempo de execução para todos os ambientes em um projeto existente:</span><span class="sxs-lookup"><span data-stu-id="28156-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="28156-122">Instale o [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Pacote NuGet do RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="28156-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="28156-123">Atualize o método do projeto `Startup.ConfigureServices` para incluir uma chamada para <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="28156-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="28156-124">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="28156-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="28156-125">Habilitar condicionalmente a compilação em tempo de execução em um projeto existente</span><span class="sxs-lookup"><span data-stu-id="28156-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="28156-126">A compilação em tempo de execução pode ser habilitada de modo que esteja disponível apenas para desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="28156-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="28156-127">Habilitar condicionalmente dessa maneira garante que a saída publicada:</span><span class="sxs-lookup"><span data-stu-id="28156-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="28156-128">Usa exibições compiladas.</span><span class="sxs-lookup"><span data-stu-id="28156-128">Uses compiled views.</span></span>
* <span data-ttu-id="28156-129">Não habilita os inspetores de arquivo na produção.</span><span class="sxs-lookup"><span data-stu-id="28156-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="28156-130">Para habilitar a compilação em tempo de execução somente no ambiente de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="28156-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="28156-131">Instale o [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Pacote NuGet do RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="28156-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="28156-132">Modifique a seção de perfil de inicialização `environmentVariables` no *launchSettings.jsem*:</span><span class="sxs-lookup"><span data-stu-id="28156-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="28156-133">Verifique se `ASPNETCORE_ENVIRONMENT` está definido como `"Development"` .</span><span class="sxs-lookup"><span data-stu-id="28156-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="28156-134">Defina `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` como `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="28156-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="28156-135">No exemplo a seguir, a compilação em tempo de execução está habilitada no ambiente de desenvolvimento para os `IIS Express` `RazorPagesApp` perfis de inicialização e:</span><span class="sxs-lookup"><span data-stu-id="28156-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="28156-136">Nenhuma alteração de código é necessária na classe do projeto `Startup` .</span><span class="sxs-lookup"><span data-stu-id="28156-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="28156-137">Em tempo de execução, ASP.NET Core procura um [atributo HostingStartup no nível de assembly](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) no `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="28156-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="28156-138">O `HostingStartup` atributo especifica o código de inicialização do aplicativo a ser executado.</span><span class="sxs-lookup"><span data-stu-id="28156-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="28156-139">Esse código de inicialização habilita a compilação em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="28156-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="28156-140">Habilitar a compilação em tempo de execução para uma Razor biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="28156-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="28156-141">Considere um cenário no qual um Razor projeto de páginas referencia uma [ Razor biblioteca de classes (RCL)](xref:razor-pages/ui-class) chamada *MyClassLib*.</span><span class="sxs-lookup"><span data-stu-id="28156-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="28156-142">O RCL contém um arquivo *_Layout. cshtml* que todos os projetos de página e MVC da sua equipe Razor consomem.</span><span class="sxs-lookup"><span data-stu-id="28156-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="28156-143">Você deseja habilitar a compilação em tempo de execução para o arquivo *_Layout. cshtml* nesse RCL.</span><span class="sxs-lookup"><span data-stu-id="28156-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="28156-144">Faça as seguintes alterações no Razor projeto de páginas:</span><span class="sxs-lookup"><span data-stu-id="28156-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="28156-145">Habilite a compilação em tempo de execução com as instruções em [habilitar condicionalmente a compilação em tempo de execução em um projeto existente](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="28156-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="28156-146">Configure as opções de compilação de tempo de execução em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="28156-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="28156-147">No código anterior, um caminho absoluto para o *MyClassLib* RCL é construído.</span><span class="sxs-lookup"><span data-stu-id="28156-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="28156-148">A [API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) é usada para localizar diretórios e arquivos nesse caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="28156-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="28156-149">Por fim, a `PhysicalFileProvider` instância é adicionada a uma coleção de provedores de arquivos, que permite o acesso aos arquivos *. cshtml* do RCL.</span><span class="sxs-lookup"><span data-stu-id="28156-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28156-150">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="28156-150">Additional resources</span></span>

* <span data-ttu-id="28156-151">Propriedades [RazorCompileOnBuild e RazorCompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="28156-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razor<span data-ttu-id="28156-152">arquivos com uma extensão *. cshtml* são compilados em tempo de compilação e publicação usando o [ Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="28156-152"> files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="28156-153">A compilação do runtime pode ser opcionalmente habilitada através da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="28156-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="28156-154">ocorrida</span><span class="sxs-lookup"><span data-stu-id="28156-154"> compilation</span></span>

<span data-ttu-id="28156-155">A compilação de arquivos em tempo de compilação e em tempo de publicação Razor é habilitada por padrão pelo Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="28156-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="28156-156">Quando habilitada, a compilação de tempo de execução complementa a compilação em tempo de compilação, permitindo que Razor os arquivos sejam atualizados se eles forem editados.</span><span class="sxs-lookup"><span data-stu-id="28156-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="28156-157">Compilação de runtime</span><span class="sxs-lookup"><span data-stu-id="28156-157">Runtime compilation</span></span>

<span data-ttu-id="28156-158">Para habilitar a compilação em tempo de execução para todos os ambientes e modos de configuração:</span><span class="sxs-lookup"><span data-stu-id="28156-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="28156-159">Instale o [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Pacote NuGet do RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="28156-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="28156-160">Atualize o método do projeto `Startup.ConfigureServices` para incluir uma chamada para <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="28156-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="28156-161">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="28156-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="28156-162">Habilitar condicionalmente a compilação em tempo de execução</span><span class="sxs-lookup"><span data-stu-id="28156-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="28156-163">A compilação em tempo de execução pode ser habilitada de modo que esteja disponível apenas para desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="28156-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="28156-164">Habilitar condicionalmente dessa maneira garante que a saída publicada:</span><span class="sxs-lookup"><span data-stu-id="28156-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="28156-165">Usa exibições compiladas.</span><span class="sxs-lookup"><span data-stu-id="28156-165">Uses compiled views.</span></span>
* <span data-ttu-id="28156-166">É menor em tamanho.</span><span class="sxs-lookup"><span data-stu-id="28156-166">Is smaller in size.</span></span>
* <span data-ttu-id="28156-167">Não habilita os inspetores de arquivo na produção.</span><span class="sxs-lookup"><span data-stu-id="28156-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="28156-168">Para habilitar a compilação em tempo de execução com base no ambiente e no modo de configuração:</span><span class="sxs-lookup"><span data-stu-id="28156-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="28156-169">Referenciar condicionalmente o [Microsoft. AspNetCore. Mvc. Razor . Pacote RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) com base no `Configuration` valor ativo:</span><span class="sxs-lookup"><span data-stu-id="28156-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="28156-170">Atualize o método do projeto `Startup.ConfigureServices` para incluir uma chamada para `AddRazorRuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="28156-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="28156-171">Execute condicionalmente `AddRazorRuntimeCompilation` para que ele seja executado somente no modo de depuração quando a `ASPNETCORE_ENVIRONMENT` variável for definida como `Development` :</span><span class="sxs-lookup"><span data-stu-id="28156-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="28156-172">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="28156-172">Additional resources</span></span>

* <span data-ttu-id="28156-173">Propriedades [RazorCompileOnBuild e RazorCompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="28156-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="28156-174">Consulte o [exemplo de compilação em tempo de execução no GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obter um exemplo que mostra como fazer trabalhos de compilação de tempo de execução entre projetos.</span><span class="sxs-lookup"><span data-stu-id="28156-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="28156-175">Um Razor arquivo é compilado em tempo de execução, quando a Razor página associada ou a exibição MVC é invocada.</span><span class="sxs-lookup"><span data-stu-id="28156-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> Razor<span data-ttu-id="28156-176">os arquivos são compilados em tempo de compilação e publicação usando o [ Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="28156-176"> files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="28156-177">ocorrida</span><span class="sxs-lookup"><span data-stu-id="28156-177"> compilation</span></span>

<span data-ttu-id="28156-178">A compilação de arquivos em tempo de compilação e publicação Razor é habilitada por padrão pelo Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="28156-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="28156-179">RazorA edição de arquivos depois que eles são atualizados tem suporte no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="28156-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="28156-180">Por padrão, somente os *Views.dll* compilados e nenhum arquivo *. cshtml* ou fazem referência a assemblies necessários para compilar Razor arquivos são implantados com seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="28156-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="28156-181">A ferramenta de pré-compilação foi preterida e será removida no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="28156-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="28156-182">É recomendável migrar para o [ Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="28156-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="28156-183">O Razor SDK só será eficaz quando nenhuma propriedade específica de pré-compilação for definida no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="28156-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="28156-184">Por exemplo, definir a propriedade do arquivo *. csproj* `MvcRazorCompileOnPublish` para `true` desabilitar o Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="28156-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="28156-185">Compilação de runtime</span><span class="sxs-lookup"><span data-stu-id="28156-185">Runtime compilation</span></span>

<span data-ttu-id="28156-186">A compilação em tempo de compilação é complementada pela compilação de arquivos em tempo de execução Razor .</span><span class="sxs-lookup"><span data-stu-id="28156-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="28156-187">ASP.NET Core MVC recompilará Razor os arquivos quando o conteúdo de um arquivo *. cshtml* for alterado.</span><span class="sxs-lookup"><span data-stu-id="28156-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28156-188">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="28156-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

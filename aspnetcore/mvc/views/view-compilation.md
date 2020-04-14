---
title: Compilação de arquivo do Razor no ASP.NET Core
author: rick-anderson
description: Saiba como a compilação de arquivos do Razor ocorre em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277257"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="08073-103">Compilação de arquivo do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08073-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="08073-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="08073-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="08073-105">Os arquivos razor com uma extensão *.cshtml* são compilados em tempo de compilação e publicação usando o [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="08073-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="08073-106">A compilação em tempo de execução pode ser opcionalmente ativada pela configuração do seu projeto.</span><span class="sxs-lookup"><span data-stu-id="08073-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="08073-107">Compilação do Razor</span><span class="sxs-lookup"><span data-stu-id="08073-107">Razor compilation</span></span>

<span data-ttu-id="08073-108">A compilação de tempo de compilação de arquivos Razor e tempo de compilação de tempo de compilação de arquivos Razor é habilitada por padrão pelo Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="08073-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="08073-109">Quando ativada, a compilação em tempo de execução complementa a compilação de tempo de compilação, permitindo que os arquivos Razor sejam atualizados se forem editados.</span><span class="sxs-lookup"><span data-stu-id="08073-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="08073-110">Habilite a compilação em tempo de execução na criação do projeto</span><span class="sxs-lookup"><span data-stu-id="08073-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="08073-111">Os modelos de projeto Razor Pages e MVC incluem uma opção para ativar a compilação em tempo de execução quando o projeto for criado.</span><span class="sxs-lookup"><span data-stu-id="08073-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="08073-112">Esta opção é suportada em ASP.NET Core 3.1 e posterior.</span><span class="sxs-lookup"><span data-stu-id="08073-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="08073-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08073-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="08073-114">No Criar uma nova caixa de diálogo **de aplicativo web ASP.NET Core:**</span><span class="sxs-lookup"><span data-stu-id="08073-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="08073-115">Selecione o **aplicativo da Web** ou o modelo de projeto Do aplicativo da Web **(Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="08073-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="08073-116">Selecione a caixa **de seleção De seleção De seleção De habilitação Razor runtime.**</span><span class="sxs-lookup"><span data-stu-id="08073-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="08073-117">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="08073-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="08073-118">Use `-rrc` a `--razor-runtime-compilation` opção ou modelo.</span><span class="sxs-lookup"><span data-stu-id="08073-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="08073-119">Por exemplo, o comando a seguir cria um novo projeto Razor Pages com compilação em tempo de execução ativada:</span><span class="sxs-lookup"><span data-stu-id="08073-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="08073-120">Habilite a compilação em tempo de execução em um projeto existente</span><span class="sxs-lookup"><span data-stu-id="08073-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="08073-121">Para habilitar a compilação em tempo de execução para todos os ambientes de um projeto existente:</span><span class="sxs-lookup"><span data-stu-id="08073-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="08073-122">Instale o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="08073-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="08073-123">Atualize o `Startup.ConfigureServices` método do projeto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>para incluir uma chamada para .</span><span class="sxs-lookup"><span data-stu-id="08073-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="08073-124">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="08073-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="08073-125">Habilitar condicionalmente a compilação em tempo de execução em um projeto existente</span><span class="sxs-lookup"><span data-stu-id="08073-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="08073-126">A compilação em tempo de execução pode ser ativada de tal forma que esteja disponível apenas para o desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="08073-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="08073-127">Habilitar condicionalmente dessa forma garante que a saída publicada:</span><span class="sxs-lookup"><span data-stu-id="08073-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="08073-128">Usa visualizações compiladas.</span><span class="sxs-lookup"><span data-stu-id="08073-128">Uses compiled views.</span></span>
* <span data-ttu-id="08073-129">Não permite observadores de arquivos em produção.</span><span class="sxs-lookup"><span data-stu-id="08073-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="08073-130">Para habilitar a compilação em tempo de execução apenas no ambiente desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="08073-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="08073-131">Instale o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="08073-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="08073-132">Modifique a `environmentVariables` seção de perfil de lançamento em *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="08073-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="08073-133">Verificar `ASPNETCORE_ENVIRONMENT` está `"Development"`definido como .</span><span class="sxs-lookup"><span data-stu-id="08073-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="08073-134">Defina `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` como `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="08073-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="08073-135">No exemplo a seguir, a compilação em tempo `IIS Express` `RazorPagesApp` de execução é habilitada no ambiente Desenvolvimento para os perfis e de lançamento:</span><span class="sxs-lookup"><span data-stu-id="08073-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="08073-136">Não são necessárias mudanças `Startup` de código na classe do projeto.</span><span class="sxs-lookup"><span data-stu-id="08073-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="08073-137">Em tempo de execução, ASP.NET Core procura um [atributo HostingStartup em nível de montagem](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) em `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="08073-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="08073-138">O `HostingStartup` atributo especifica o código de inicialização do aplicativo para ser executado.</span><span class="sxs-lookup"><span data-stu-id="08073-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="08073-139">Esse código de inicialização permite a compilação em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="08073-139">That startup code enables runtime compilation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08073-140">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="08073-140">Additional resources</span></span>

* <span data-ttu-id="08073-141">[Propriedades RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="08073-141">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="08073-142">Consulte a [amostra de compilação em tempo de execução no GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obter uma amostra que mostra fazer a compilação em tempo de execução funcionar entre projetos.</span><span class="sxs-lookup"><span data-stu-id="08073-142">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="08073-143">Os arquivos razor com uma extensão *.cshtml* são compilados em tempo de compilação e publicação usando o [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="08073-143">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="08073-144">A compilação do runtime pode ser opcionalmente habilitada através da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="08073-144">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="08073-145">Compilação do Razor</span><span class="sxs-lookup"><span data-stu-id="08073-145">Razor compilation</span></span>

<span data-ttu-id="08073-146">A compilação de tempo de compilação de arquivos Razor e tempo de compilação de tempo de compilação de arquivos Razor é habilitada por padrão pelo Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="08073-146">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="08073-147">Quando ativada, a compilação em tempo de execução complementa a compilação de tempo de compilação, permitindo que os arquivos Razor sejam atualizados se forem editados.</span><span class="sxs-lookup"><span data-stu-id="08073-147">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="08073-148">Compilação de runtime</span><span class="sxs-lookup"><span data-stu-id="08073-148">Runtime compilation</span></span>

<span data-ttu-id="08073-149">Para habilitar a compilação em tempo de execução para todos os ambientes e modos de configuração:</span><span class="sxs-lookup"><span data-stu-id="08073-149">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="08073-150">Instale o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="08073-150">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="08073-151">Atualize o `Startup.ConfigureServices` método do projeto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>para incluir uma chamada para .</span><span class="sxs-lookup"><span data-stu-id="08073-151">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="08073-152">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="08073-152">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="08073-153">Habilitar condicionalmente a compilação em tempo de execução</span><span class="sxs-lookup"><span data-stu-id="08073-153">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="08073-154">A compilação em tempo de execução pode ser ativada de tal forma que esteja disponível apenas para o desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="08073-154">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="08073-155">Habilitar condicionalmente dessa forma garante que a saída publicada:</span><span class="sxs-lookup"><span data-stu-id="08073-155">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="08073-156">Usa visualizações compiladas.</span><span class="sxs-lookup"><span data-stu-id="08073-156">Uses compiled views.</span></span>
* <span data-ttu-id="08073-157">É menor em tamanho.</span><span class="sxs-lookup"><span data-stu-id="08073-157">Is smaller in size.</span></span>
* <span data-ttu-id="08073-158">Não permite observadores de arquivos em produção.</span><span class="sxs-lookup"><span data-stu-id="08073-158">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="08073-159">Para habilitar a compilação em tempo de execução com base no ambiente e no modo de configuração:</span><span class="sxs-lookup"><span data-stu-id="08073-159">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="08073-160">Consulte condicionalmente o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) com base no valor ativo: `Configuration`</span><span class="sxs-lookup"><span data-stu-id="08073-160">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="08073-161">Atualize o `Startup.ConfigureServices` método do projeto `AddRazorRuntimeCompilation`para incluir uma chamada para .</span><span class="sxs-lookup"><span data-stu-id="08073-161">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="08073-162">Executar condicionalmente `AddRazorRuntimeCompilation` de modo que ele só `ASPNETCORE_ENVIRONMENT` é executado `Development`no modo Debug quando a variável é definida como :</span><span class="sxs-lookup"><span data-stu-id="08073-162">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="08073-163">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="08073-163">Additional resources</span></span>

* <span data-ttu-id="08073-164">[Propriedades RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="08073-164">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="08073-165">Consulte a [amostra de compilação em tempo de execução no GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obter uma amostra que mostra fazer a compilação em tempo de execução funcionar entre projetos.</span><span class="sxs-lookup"><span data-stu-id="08073-165">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="08073-166">Um arquivo do Razor é compilado em runtime, quando o modo de exibição do MVC ou da Página do Razor associada é chamado.</span><span class="sxs-lookup"><span data-stu-id="08073-166">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="08073-167">Os arquivos do Razor são compilados em tempo de build e de publicação usando o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="08073-167">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="08073-168">Compilação do Razor</span><span class="sxs-lookup"><span data-stu-id="08073-168">Razor compilation</span></span>

<span data-ttu-id="08073-169">A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="08073-169">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="08073-170">Há suporte para edição de arquivos do Razor depois que eles são atualizados em tempo de build.</span><span class="sxs-lookup"><span data-stu-id="08073-170">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="08073-171">Por padrão, somente os arquivos *Views.dll* compilados, mas nenhum arquivo *.cshtml* ou assembly de referência necessário para compilar arquivos do Razor, são implantados com seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="08073-171">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="08073-172">A ferramenta de pré-compilação foi preterida e será removida no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="08073-172">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="08073-173">É recomendado migrar para o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="08073-173">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="08073-174">O SDK do Razor é eficaz somente quando não há propriedades específicas de pré-compilação definidas no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="08073-174">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="08073-175">Por exemplo, definir a propriedade `MvcRazorCompileOnPublish` do arquivo *.csproj* como `true` desabilita o SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="08073-175">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="08073-176">Compilação de runtime</span><span class="sxs-lookup"><span data-stu-id="08073-176">Runtime compilation</span></span>

<span data-ttu-id="08073-177">A compilação de tempo de build é complementada pela compilação de runtime de arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="08073-177">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="08073-178">O ASP.NET Core MVC recompilará arquivos do Razor quando o conteúdo de um arquivo *.cshtml* for alterado.</span><span class="sxs-lookup"><span data-stu-id="08073-178">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08073-179">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="08073-179">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

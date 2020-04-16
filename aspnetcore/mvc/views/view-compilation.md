---
title: Compilação de arquivo do Razor no ASP.NET Core
author: rick-anderson
description: Saiba como a compilação de arquivos do Razor ocorre em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d871ab960de28a565280d9e4cb2c597832e2455
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440929"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="723e4-103">Compilação de arquivo do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="723e4-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="723e4-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="723e4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="723e4-105">Os arquivos razor com uma extensão *.cshtml* são compilados em tempo de compilação e publicação usando o [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="723e4-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="723e4-106">A compilação em tempo de execução pode ser opcionalmente ativada pela configuração do seu projeto.</span><span class="sxs-lookup"><span data-stu-id="723e4-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="723e4-107">Compilação do Razor</span><span class="sxs-lookup"><span data-stu-id="723e4-107">Razor compilation</span></span>

<span data-ttu-id="723e4-108">A compilação de tempo de compilação de arquivos Razor e tempo de compilação de tempo de compilação de arquivos Razor é habilitada por padrão pelo Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="723e4-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="723e4-109">Quando ativada, a compilação em tempo de execução complementa a compilação de tempo de compilação, permitindo que os arquivos Razor sejam atualizados se forem editados.</span><span class="sxs-lookup"><span data-stu-id="723e4-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="723e4-110">Habilite a compilação em tempo de execução na criação do projeto</span><span class="sxs-lookup"><span data-stu-id="723e4-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="723e4-111">Os modelos de projeto Razor Pages e MVC incluem uma opção para ativar a compilação em tempo de execução quando o projeto for criado.</span><span class="sxs-lookup"><span data-stu-id="723e4-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="723e4-112">Esta opção é suportada em ASP.NET Core 3.1 e posterior.</span><span class="sxs-lookup"><span data-stu-id="723e4-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="723e4-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="723e4-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="723e4-114">No Criar uma nova caixa de diálogo **de aplicativo web ASP.NET Core:**</span><span class="sxs-lookup"><span data-stu-id="723e4-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="723e4-115">Selecione o **aplicativo da Web** ou o modelo de projeto Do aplicativo da Web **(Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="723e4-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="723e4-116">Selecione a caixa **de seleção De seleção De seleção De habilitação Razor runtime.**</span><span class="sxs-lookup"><span data-stu-id="723e4-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="723e4-117">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="723e4-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="723e4-118">Use `-rrc` a `--razor-runtime-compilation` opção ou modelo.</span><span class="sxs-lookup"><span data-stu-id="723e4-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="723e4-119">Por exemplo, o comando a seguir cria um novo projeto Razor Pages com compilação em tempo de execução ativada:</span><span class="sxs-lookup"><span data-stu-id="723e4-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="723e4-120">Habilite a compilação em tempo de execução em um projeto existente</span><span class="sxs-lookup"><span data-stu-id="723e4-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="723e4-121">Para habilitar a compilação em tempo de execução para todos os ambientes de um projeto existente:</span><span class="sxs-lookup"><span data-stu-id="723e4-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="723e4-122">Instale o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="723e4-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="723e4-123">Atualize o `Startup.ConfigureServices` método do projeto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>para incluir uma chamada para .</span><span class="sxs-lookup"><span data-stu-id="723e4-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="723e4-124">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="723e4-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="723e4-125">Habilitar condicionalmente a compilação em tempo de execução em um projeto existente</span><span class="sxs-lookup"><span data-stu-id="723e4-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="723e4-126">A compilação em tempo de execução pode ser ativada de tal forma que esteja disponível apenas para o desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="723e4-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="723e4-127">Habilitar condicionalmente dessa forma garante que a saída publicada:</span><span class="sxs-lookup"><span data-stu-id="723e4-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="723e4-128">Usa visualizações compiladas.</span><span class="sxs-lookup"><span data-stu-id="723e4-128">Uses compiled views.</span></span>
* <span data-ttu-id="723e4-129">Não permite observadores de arquivos em produção.</span><span class="sxs-lookup"><span data-stu-id="723e4-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="723e4-130">Para habilitar a compilação em tempo de execução apenas no ambiente desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="723e4-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="723e4-131">Instale o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="723e4-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="723e4-132">Modifique a `environmentVariables` seção de perfil de lançamento em *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="723e4-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="723e4-133">Verificar `ASPNETCORE_ENVIRONMENT` está `"Development"`definido como .</span><span class="sxs-lookup"><span data-stu-id="723e4-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="723e4-134">Defina `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` como `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="723e4-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="723e4-135">No exemplo a seguir, a compilação em tempo `IIS Express` `RazorPagesApp` de execução é habilitada no ambiente Desenvolvimento para os perfis e de lançamento:</span><span class="sxs-lookup"><span data-stu-id="723e4-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="723e4-136">Não são necessárias mudanças `Startup` de código na classe do projeto.</span><span class="sxs-lookup"><span data-stu-id="723e4-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="723e4-137">Em tempo de execução, ASP.NET Core procura um [atributo HostingStartup em nível de montagem](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) em `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="723e4-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="723e4-138">O `HostingStartup` atributo especifica o código de inicialização do aplicativo para ser executado.</span><span class="sxs-lookup"><span data-stu-id="723e4-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="723e4-139">Esse código de inicialização permite a compilação em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="723e4-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="723e4-140">Habilite a compilação em tempo de execução para uma Biblioteca de Classe de Barbear</span><span class="sxs-lookup"><span data-stu-id="723e4-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="723e4-141">Considere um cenário no qual um projeto Razor Pages faz referência a uma [Biblioteca de Classe de Navalha (RCL)](xref:razor-pages/ui-class) chamada *MyClassLib*.</span><span class="sxs-lookup"><span data-stu-id="723e4-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="723e4-142">A RCL contém um arquivo *_Layout.cshtml* que todos os projetos mvc e razor pages da sua equipe consomem.</span><span class="sxs-lookup"><span data-stu-id="723e4-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="723e4-143">Você deseja ativar a compilação em tempo de execução para o arquivo *_Layout.cshtml* nessa RCL.</span><span class="sxs-lookup"><span data-stu-id="723e4-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="723e4-144">Faça as seguintes alterações no projeto Páginas de Barbear:</span><span class="sxs-lookup"><span data-stu-id="723e4-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="723e4-145">Habilite a compilação em tempo de execução com as instruções em [Conditionally habilitar compilação em tempo de execução em um projeto existente](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="723e4-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="723e4-146">Configure as opções `Startup.ConfigureServices`de compilação em tempo de execução em:</span><span class="sxs-lookup"><span data-stu-id="723e4-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="723e4-147">No código anterior, um caminho absoluto para o *MyClassLib* RCL é construído.</span><span class="sxs-lookup"><span data-stu-id="723e4-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="723e4-148">A [API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) é usada para localizar diretórios e arquivos nesse caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="723e4-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="723e4-149">Finalmente, `PhysicalFileProvider` a instância é adicionada a uma coleção de provedores de arquivos, que permite o acesso aos arquivos *.cshtml* da RCL.</span><span class="sxs-lookup"><span data-stu-id="723e4-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="723e4-150">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="723e4-150">Additional resources</span></span>

* <span data-ttu-id="723e4-151">[Propriedades RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="723e4-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="723e4-152">Os arquivos razor com uma extensão *.cshtml* são compilados em tempo de compilação e publicação usando o [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="723e4-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="723e4-153">A compilação do runtime pode ser opcionalmente habilitada através da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="723e4-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="723e4-154">Compilação do Razor</span><span class="sxs-lookup"><span data-stu-id="723e4-154">Razor compilation</span></span>

<span data-ttu-id="723e4-155">A compilação de tempo de compilação de arquivos Razor e tempo de compilação de tempo de compilação de arquivos Razor é habilitada por padrão pelo Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="723e4-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="723e4-156">Quando ativada, a compilação em tempo de execução complementa a compilação de tempo de compilação, permitindo que os arquivos Razor sejam atualizados se forem editados.</span><span class="sxs-lookup"><span data-stu-id="723e4-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="723e4-157">Compilação de runtime</span><span class="sxs-lookup"><span data-stu-id="723e4-157">Runtime compilation</span></span>

<span data-ttu-id="723e4-158">Para habilitar a compilação em tempo de execução para todos os ambientes e modos de configuração:</span><span class="sxs-lookup"><span data-stu-id="723e4-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="723e4-159">Instale o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="723e4-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="723e4-160">Atualize o `Startup.ConfigureServices` método do projeto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>para incluir uma chamada para .</span><span class="sxs-lookup"><span data-stu-id="723e4-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="723e4-161">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="723e4-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="723e4-162">Habilitar condicionalmente a compilação em tempo de execução</span><span class="sxs-lookup"><span data-stu-id="723e4-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="723e4-163">A compilação em tempo de execução pode ser ativada de tal forma que esteja disponível apenas para o desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="723e4-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="723e4-164">Habilitar condicionalmente dessa forma garante que a saída publicada:</span><span class="sxs-lookup"><span data-stu-id="723e4-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="723e4-165">Usa visualizações compiladas.</span><span class="sxs-lookup"><span data-stu-id="723e4-165">Uses compiled views.</span></span>
* <span data-ttu-id="723e4-166">É menor em tamanho.</span><span class="sxs-lookup"><span data-stu-id="723e4-166">Is smaller in size.</span></span>
* <span data-ttu-id="723e4-167">Não permite observadores de arquivos em produção.</span><span class="sxs-lookup"><span data-stu-id="723e4-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="723e4-168">Para habilitar a compilação em tempo de execução com base no ambiente e no modo de configuração:</span><span class="sxs-lookup"><span data-stu-id="723e4-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="723e4-169">Consulte condicionalmente o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) com base no valor ativo: `Configuration`</span><span class="sxs-lookup"><span data-stu-id="723e4-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="723e4-170">Atualize o `Startup.ConfigureServices` método do projeto `AddRazorRuntimeCompilation`para incluir uma chamada para .</span><span class="sxs-lookup"><span data-stu-id="723e4-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="723e4-171">Executar condicionalmente `AddRazorRuntimeCompilation` de modo que ele só `ASPNETCORE_ENVIRONMENT` é executado `Development`no modo Debug quando a variável é definida como :</span><span class="sxs-lookup"><span data-stu-id="723e4-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="723e4-172">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="723e4-172">Additional resources</span></span>

* <span data-ttu-id="723e4-173">[Propriedades RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="723e4-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="723e4-174">Consulte a [amostra de compilação em tempo de execução no GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obter uma amostra que mostra fazer a compilação em tempo de execução funcionar entre projetos.</span><span class="sxs-lookup"><span data-stu-id="723e4-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="723e4-175">Um arquivo do Razor é compilado em runtime, quando o modo de exibição do MVC ou da Página do Razor associada é chamado.</span><span class="sxs-lookup"><span data-stu-id="723e4-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="723e4-176">Os arquivos do Razor são compilados em tempo de build e de publicação usando o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="723e4-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="723e4-177">Compilação do Razor</span><span class="sxs-lookup"><span data-stu-id="723e4-177">Razor compilation</span></span>

<span data-ttu-id="723e4-178">A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="723e4-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="723e4-179">Há suporte para edição de arquivos do Razor depois que eles são atualizados em tempo de build.</span><span class="sxs-lookup"><span data-stu-id="723e4-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="723e4-180">Por padrão, somente os arquivos *Views.dll* compilados, mas nenhum arquivo *.cshtml* ou assembly de referência necessário para compilar arquivos do Razor, são implantados com seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="723e4-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="723e4-181">A ferramenta de pré-compilação foi preterida e será removida no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="723e4-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="723e4-182">É recomendado migrar para o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="723e4-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="723e4-183">O SDK do Razor é eficaz somente quando não há propriedades específicas de pré-compilação definidas no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="723e4-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="723e4-184">Por exemplo, definir a propriedade `MvcRazorCompileOnPublish` do arquivo *.csproj* como `true` desabilita o SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="723e4-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="723e4-185">Compilação de runtime</span><span class="sxs-lookup"><span data-stu-id="723e4-185">Runtime compilation</span></span>

<span data-ttu-id="723e4-186">A compilação de tempo de build é complementada pela compilação de runtime de arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="723e4-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="723e4-187">O ASP.NET Core MVC recompilará arquivos do Razor quando o conteúdo de um arquivo *.cshtml* for alterado.</span><span class="sxs-lookup"><span data-stu-id="723e4-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="723e4-188">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="723e4-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

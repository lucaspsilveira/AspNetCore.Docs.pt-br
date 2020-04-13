---
title: Compilação de arquivo do Razor no ASP.NET Core
author: rick-anderson
description: Saiba como a compilação de arquivos do Razor ocorre em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 0afd39fdb5a6f570e0e78ad54f6c436460bad3a6
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223953"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="ba82e-103">Compilação de arquivo do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ba82e-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="ba82e-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ba82e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba82e-105">Os arquivos razor com uma extensão *.cshtml* são compilados em tempo de compilação e publicação usando o [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="ba82e-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="ba82e-106">A compilação do runtime pode ser opcionalmente habilitada através da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ba82e-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="ba82e-107">Compilação do Razor</span><span class="sxs-lookup"><span data-stu-id="ba82e-107">Razor compilation</span></span>

<span data-ttu-id="ba82e-108">A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="ba82e-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="ba82e-109">Quando habilitada, a compilação de runtime complementa a compilação de tempo de build, permitindo que os arquivos do Razor sejam atualizados se eles forem editados.</span><span class="sxs-lookup"><span data-stu-id="ba82e-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="ba82e-110">Compilação de runtime</span><span class="sxs-lookup"><span data-stu-id="ba82e-110">Runtime compilation</span></span>

<span data-ttu-id="ba82e-111">Para habilitar a compilação em tempo de execução para todos os ambientes e modos de configuração:</span><span class="sxs-lookup"><span data-stu-id="ba82e-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="ba82e-112">Instale o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="ba82e-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="ba82e-113">Atualize o `Startup.ConfigureServices` método do projeto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>para incluir uma chamada para .</span><span class="sxs-lookup"><span data-stu-id="ba82e-113">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="ba82e-114">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ba82e-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="ba82e-115">Habilitar condicionalmente a compilação em tempo de execução</span><span class="sxs-lookup"><span data-stu-id="ba82e-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="ba82e-116">A compilação em tempo de execução pode ser ativada de tal forma que esteja disponível apenas para o desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="ba82e-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="ba82e-117">Habilitar condicionalmente dessa forma garante que a saída publicada:</span><span class="sxs-lookup"><span data-stu-id="ba82e-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="ba82e-118">Usa visualizações compiladas.</span><span class="sxs-lookup"><span data-stu-id="ba82e-118">Uses compiled views.</span></span>
* <span data-ttu-id="ba82e-119">É menor em tamanho.</span><span class="sxs-lookup"><span data-stu-id="ba82e-119">Is smaller in size.</span></span>
* <span data-ttu-id="ba82e-120">Não permite observadores de arquivos em produção.</span><span class="sxs-lookup"><span data-stu-id="ba82e-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="ba82e-121">Para habilitar a compilação em tempo de execução com base no ambiente e no modo de configuração:</span><span class="sxs-lookup"><span data-stu-id="ba82e-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="ba82e-122">Consulte condicionalmente o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) com base no valor ativo: `Configuration`</span><span class="sxs-lookup"><span data-stu-id="ba82e-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="ba82e-123">Atualize o `Startup.ConfigureServices` método do projeto `AddRazorRuntimeCompilation`para incluir uma chamada para .</span><span class="sxs-lookup"><span data-stu-id="ba82e-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="ba82e-124">Executar condicionalmente `AddRazorRuntimeCompilation` de modo que ele só `ASPNETCORE_ENVIRONMENT` é executado `Development`no modo Debug quando a variável é definida como :</span><span class="sxs-lookup"><span data-stu-id="ba82e-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="ba82e-125">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ba82e-125">Additional resources</span></span>

* <span data-ttu-id="ba82e-126">[Propriedades RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="ba82e-126">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="ba82e-127">Consulte a [amostra de compilação em tempo de execução no GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obter uma amostra que mostra fazer a compilação em tempo de execução funcionar entre projetos.</span><span class="sxs-lookup"><span data-stu-id="ba82e-127">See the [runtimecompilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ba82e-128">Um arquivo do Razor é compilado em runtime, quando o modo de exibição do MVC ou da Página do Razor associada é chamado.</span><span class="sxs-lookup"><span data-stu-id="ba82e-128">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="ba82e-129">Os arquivos do Razor são compilados em tempo de build e de publicação usando o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="ba82e-129">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="ba82e-130">Compilação do Razor</span><span class="sxs-lookup"><span data-stu-id="ba82e-130">Razor compilation</span></span>

<span data-ttu-id="ba82e-131">A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="ba82e-131">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="ba82e-132">Há suporte para edição de arquivos do Razor depois que eles são atualizados em tempo de build.</span><span class="sxs-lookup"><span data-stu-id="ba82e-132">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="ba82e-133">Por padrão, somente os arquivos *Views.dll* compilados, mas nenhum arquivo *.cshtml* ou assembly de referência necessário para compilar arquivos do Razor, são implantados com seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ba82e-133">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ba82e-134">A ferramenta de pré-compilação foi preterida e será removida no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="ba82e-134">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="ba82e-135">É recomendado migrar para o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="ba82e-135">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="ba82e-136">O SDK do Razor é eficaz somente quando não há propriedades específicas de pré-compilação definidas no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="ba82e-136">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="ba82e-137">Por exemplo, definir a propriedade `MvcRazorCompileOnPublish` do arquivo *.csproj* como `true` desabilita o SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="ba82e-137">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="ba82e-138">Compilação de runtime</span><span class="sxs-lookup"><span data-stu-id="ba82e-138">Runtime compilation</span></span>

<span data-ttu-id="ba82e-139">A compilação de tempo de build é complementada pela compilação de runtime de arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="ba82e-139">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="ba82e-140">O ASP.NET Core MVC recompilará arquivos do Razor quando o conteúdo de um arquivo *.cshtml* for alterado.</span><span class="sxs-lookup"><span data-stu-id="ba82e-140">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ba82e-141">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ba82e-141">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

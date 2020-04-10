---
title: Compilação de arquivo do Razor no ASP.NET Core
author: rick-anderson
description: Saiba como a compilação de arquivos do Razor ocorre em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 7f329ffb4c63e8699663f49720145984bb8802fd
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994601"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="b7b09-103">Compilação de arquivo do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7b09-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="b7b09-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b7b09-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b7b09-105">Os arquivos razor com uma extensão *.cshtml* são compilados em tempo de compilação e publicação usando o [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="b7b09-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="b7b09-106">A compilação do runtime pode ser opcionalmente habilitada através da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7b09-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="b7b09-107">Compilação do Razor</span><span class="sxs-lookup"><span data-stu-id="b7b09-107">Razor compilation</span></span>

<span data-ttu-id="b7b09-108">A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="b7b09-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="b7b09-109">Quando habilitada, a compilação de runtime complementa a compilação de tempo de build, permitindo que os arquivos do Razor sejam atualizados se eles forem editados.</span><span class="sxs-lookup"><span data-stu-id="b7b09-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="b7b09-110">Compilação de runtime</span><span class="sxs-lookup"><span data-stu-id="b7b09-110">Runtime compilation</span></span>

<span data-ttu-id="b7b09-111">Para habilitar a compilação em tempo de execução para todos os ambientes e modos de configuração:</span><span class="sxs-lookup"><span data-stu-id="b7b09-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="b7b09-112">Instale o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="b7b09-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="b7b09-113">Atualize o `Startup.ConfigureServices` método do projeto `AddRazorRuntimeCompilation`para incluir uma chamada para .</span><span class="sxs-lookup"><span data-stu-id="b7b09-113">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="b7b09-114">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b7b09-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="b7b09-115">Habilitar condicionalmente a compilação em tempo de execução</span><span class="sxs-lookup"><span data-stu-id="b7b09-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="b7b09-116">A compilação em tempo de execução pode ser ativada de tal forma que esteja disponível apenas para o desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="b7b09-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="b7b09-117">Habilitar condicionalmente dessa forma garante que a saída publicada:</span><span class="sxs-lookup"><span data-stu-id="b7b09-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="b7b09-118">Usa visualizações compiladas.</span><span class="sxs-lookup"><span data-stu-id="b7b09-118">Uses compiled views.</span></span>
* <span data-ttu-id="b7b09-119">É menor em tamanho.</span><span class="sxs-lookup"><span data-stu-id="b7b09-119">Is smaller in size.</span></span>
* <span data-ttu-id="b7b09-120">Não permite observadores de arquivos em produção.</span><span class="sxs-lookup"><span data-stu-id="b7b09-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="b7b09-121">Para habilitar a compilação em tempo de execução com base no ambiente e no modo de configuração:</span><span class="sxs-lookup"><span data-stu-id="b7b09-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="b7b09-122">Consulte condicionalmente o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) com base no valor ativo: `Configuration`</span><span class="sxs-lookup"><span data-stu-id="b7b09-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="b7b09-123">Atualize o `Startup.ConfigureServices` método do projeto `AddRazorRuntimeCompilation`para incluir uma chamada para .</span><span class="sxs-lookup"><span data-stu-id="b7b09-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="b7b09-124">Executar condicionalmente `AddRazorRuntimeCompilation` de modo que ele só `ASPNETCORE_ENVIRONMENT` é executado `Development`no modo Debug quando a variável é definida como :</span><span class="sxs-lookup"><span data-stu-id="b7b09-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="b7b09-125">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b7b09-125">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b7b09-126">Um arquivo do Razor é compilado em runtime, quando o modo de exibição do MVC ou da Página do Razor associada é chamado.</span><span class="sxs-lookup"><span data-stu-id="b7b09-126">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="b7b09-127">Os arquivos do Razor são compilados em tempo de build e de publicação usando o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="b7b09-127">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="b7b09-128">Compilação do Razor</span><span class="sxs-lookup"><span data-stu-id="b7b09-128">Razor compilation</span></span>

<span data-ttu-id="b7b09-129">A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="b7b09-129">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="b7b09-130">Há suporte para edição de arquivos do Razor depois que eles são atualizados em tempo de build.</span><span class="sxs-lookup"><span data-stu-id="b7b09-130">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="b7b09-131">Por padrão, somente os arquivos *Views.dll* compilados, mas nenhum arquivo *.cshtml* ou assembly de referência necessário para compilar arquivos do Razor, são implantados com seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b7b09-131">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b7b09-132">A ferramenta de pré-compilação foi preterida e será removida no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="b7b09-132">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="b7b09-133">É recomendado migrar para o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="b7b09-133">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="b7b09-134">O SDK do Razor é eficaz somente quando não há propriedades específicas de pré-compilação definidas no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="b7b09-134">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="b7b09-135">Por exemplo, definir a propriedade `MvcRazorCompileOnPublish` do arquivo *.csproj* como `true` desabilita o SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="b7b09-135">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="b7b09-136">Compilação de runtime</span><span class="sxs-lookup"><span data-stu-id="b7b09-136">Runtime compilation</span></span>

<span data-ttu-id="b7b09-137">A compilação de tempo de build é complementada pela compilação de runtime de arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="b7b09-137">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="b7b09-138">O ASP.NET Core MVC recompilará arquivos do Razor quando o conteúdo de um arquivo *.cshtml* for alterado.</span><span class="sxs-lookup"><span data-stu-id="b7b09-138">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7b09-139">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b7b09-139">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
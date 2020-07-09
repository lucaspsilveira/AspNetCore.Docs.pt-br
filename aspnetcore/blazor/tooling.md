---
title: Ferramentas para ASP.NET CoreBlazor
author: guardrex
description: Saiba mais sobre as ferramentas disponíveis para criar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems-set-one
ms.openlocfilehash: 30a76eda0e94ee7bb2b2d3db918bc029865bdf1a
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147652"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="05bde-103">Ferramentas para ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="05bde-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="05bde-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="05bde-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="os-windows"

1. <span data-ttu-id="05bde-105">Instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="05bde-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="05bde-106">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="05bde-106">Create a new project.</span></span>

1. <span data-ttu-id="05bde-107">Selecione \*\* Blazor aplicativo\*\*.</span><span class="sxs-lookup"><span data-stu-id="05bde-107">Select **Blazor App**.</span></span> <span data-ttu-id="05bde-108">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="05bde-108">Select **Next**.</span></span>

1. <span data-ttu-id="05bde-109">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="05bde-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="05bde-110">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="05bde-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="05bde-111">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="05bde-111">Select **Create**.</span></span>

1. <span data-ttu-id="05bde-112">Para obter uma Blazor WebAssembly experiência, escolha o modelo de \*\* Blazor WebAssembly aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="05bde-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="05bde-113">Para obter uma Blazor Server experiência, escolha o modelo de \*\* Blazor Server aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="05bde-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="05bde-114">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="05bde-114">Select **Create**.</span></span>

   <span data-ttu-id="05bde-115">Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="05bde-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="05bde-116">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="05bde-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="05bde-117">Para obter mais informações sobre como confiar no certificado de desenvolvimento ASP.NET Core HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="05bde-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="os-linux"

1. <span data-ttu-id="05bde-118">Instale a versão mais recente do [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="05bde-118">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="05bde-119">Se você já instalou o SDK, poderá determinar a versão instalada executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="05bde-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="05bde-120">Instale a versão mais recente do [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="05bde-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="05bde-121">Instale a extensão mais recente [do C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="05bde-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="05bde-122">Para obter uma Blazor WebAssembly experiência, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="05bde-122">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="05bde-123">Para obter uma Blazor Server experiência, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="05bde-123">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="05bde-124">Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="05bde-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="05bde-125">Abra a pasta `WebApplication1` no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="05bde-125">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="05bde-126">O IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="05bde-126">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="05bde-127">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="05bde-127">Select **Yes**.</span></span>

1. <span data-ttu-id="05bde-128">Pressione <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="05bde-128">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="05bde-129">Confiar em um certificado de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="05bde-129">Trust a development certificate</span></span>

<span data-ttu-id="05bde-130">Não há uma maneira centralizada de confiar em um certificado no Linux.</span><span class="sxs-lookup"><span data-stu-id="05bde-130">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="05bde-131">Normalmente, uma das seguintes abordagens é adotada:</span><span class="sxs-lookup"><span data-stu-id="05bde-131">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="05bde-132">Exclua a URL do aplicativo na lista de exclusões do navegador.</span><span class="sxs-lookup"><span data-stu-id="05bde-132">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="05bde-133">Confiar em todos os certificados autoassinados para `localhost` .</span><span class="sxs-lookup"><span data-stu-id="05bde-133">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="05bde-134">Adicione o certificado à lista de certificados confiáveis no navegador.</span><span class="sxs-lookup"><span data-stu-id="05bde-134">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="05bde-135">Para obter mais informações, consulte as diretrizes fornecidas pelo seu navegador e distribuição do Linux.</span><span class="sxs-lookup"><span data-stu-id="05bde-135">For more information, see the guidance provided by your browser and Linux distro.</span></span>

::: zone-end

::: zone pivot="os-macos"

1. <span data-ttu-id="05bde-136">Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="05bde-136">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="05bde-137">Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.</span><span class="sxs-lookup"><span data-stu-id="05bde-137">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="05bde-138">Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="05bde-138">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="05bde-139">Para obter uma Blazor WebAssembly experiência, escolha o modelo de \*\* Blazor WebAssembly aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="05bde-139">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="05bde-140">Para obter uma Blazor Server experiência, escolha o modelo de \*\* Blazor Server aplicativo\*\* .</span><span class="sxs-lookup"><span data-stu-id="05bde-140">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="05bde-141">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="05bde-141">Select **Next**.</span></span>

   <span data-ttu-id="05bde-142">Para obter informações sobre os dois Blazor modelos de hospedagem *Blazor WebAssembly* e *Blazor Server* , consulte <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="05bde-142">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="05bde-143">Confirme se a **autenticação** está definida como **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="05bde-143">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="05bde-144">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="05bde-144">Select **Next**.</span></span>

1. <span data-ttu-id="05bde-145">No campo **nome do projeto** , nomeie o aplicativo `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="05bde-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="05bde-146">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="05bde-146">Select **Create**.</span></span>

1. <span data-ttu-id="05bde-147">Selecione **executar**  >  **Iniciar sem depuração** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="05bde-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="05bde-148">Execute o aplicativo com **executar**  >  **Iniciar Depuração** ou o botão Executar (&#9654;) para executar o aplicativo *com o depurador*.</span><span class="sxs-lookup"><span data-stu-id="05bde-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="05bde-149">Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.</span><span class="sxs-lookup"><span data-stu-id="05bde-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="05bde-150">As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="05bde-150">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="05bde-151">Para obter mais informações sobre como confiar no certificado de desenvolvimento ASP.NET Core HTTPS, consulte <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="05bde-151">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

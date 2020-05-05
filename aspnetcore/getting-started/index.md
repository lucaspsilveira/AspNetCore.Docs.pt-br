---
title: Introdução ao ASP.NET Core
author: rick-anderson
description: Um breve tutorial que cria e executa um aplicativo básico Olá, Mundo usando o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: getting-started
ms.openlocfilehash: 565cde233d6fc5f085ae1709e167e73980d5c3c2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776227"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="054f6-103">Tutorial: introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="054f6-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="054f6-104">Este tutorial mostra como criar e executar um aplicativo Web ASP.NET Core usando o CLI do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="054f6-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="054f6-105">Você aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="054f6-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="054f6-106">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="054f6-106">Create a web app project.</span></span>
> * <span data-ttu-id="054f6-107">Confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="054f6-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="054f6-108">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="054f6-108">Run the app.</span></span>
> * <span data-ttu-id="054f6-109">Editar uma página do Razor.</span><span class="sxs-lookup"><span data-stu-id="054f6-109">Edit a Razor page.</span></span>

<span data-ttu-id="054f6-110">No final, você terá um aplicativo Web de trabalho em execução no seu computador local.</span><span class="sxs-lookup"><span data-stu-id="054f6-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Página inicial do aplicativo Web](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="054f6-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="054f6-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="054f6-113">Criar um projeto do aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="054f6-113">Create a web app project</span></span>

<span data-ttu-id="054f6-114">Abra um shell de comando e insira o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="054f6-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="054f6-115">O comando anterior:</span><span class="sxs-lookup"><span data-stu-id="054f6-115">The preceding command:</span></span>

* <span data-ttu-id="054f6-116">Cria um novo aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="054f6-116">Creates a new web app.</span></span>  
* <span data-ttu-id="054f6-117">O `-o aspnetcoreapp` parâmetro cria um diretório chamado *aspnetcoreapp* com os arquivos de origem para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="054f6-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="054f6-118">Confiar no certificado de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="054f6-118">Trust the development certificate</span></span>

<span data-ttu-id="054f6-119">Confie no certificado de desenvolvimento HTTPS:</span><span class="sxs-lookup"><span data-stu-id="054f6-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="054f6-120">Windows</span><span class="sxs-lookup"><span data-stu-id="054f6-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="054f6-121">O comando anterior exibe a caixa de diálogo a seguir:</span><span class="sxs-lookup"><span data-stu-id="054f6-121">The preceding command displays the following dialog:</span></span>

![Caixa de diálogo de aviso de segurança](~/getting-started/_static/cert.png)

<span data-ttu-id="054f6-123">Selecione **Sim** se você concordar com confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="054f6-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="054f6-124">macOS</span><span class="sxs-lookup"><span data-stu-id="054f6-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="054f6-125">O comando anterior exibe a mensagem a seguir:</span><span class="sxs-lookup"><span data-stu-id="054f6-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="054f6-126">*A confiança do certificado de desenvolvimento https foi solicitada. Se o certificado ainda não for confiável, executaremos o seguinte comando:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="054f6-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="054f6-127">Esse comando pode solicitar que você insira sua senha para instalar o certificado no conjunto de chaves do sistema.</span><span class="sxs-lookup"><span data-stu-id="054f6-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="054f6-128">Insira sua senha se você concordar em confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="054f6-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="054f6-129">Linux</span><span class="sxs-lookup"><span data-stu-id="054f6-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="054f6-130">Consulte a documentação para sua distribuição do Linux sobre como confiar no certificado de desenvolvimento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="054f6-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="054f6-131">Para obter mais informações, confira [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="054f6-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="054f6-132">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="054f6-132">Run the app</span></span>

<span data-ttu-id="054f6-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="054f6-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="054f6-134">Depois que o shell de comando indicar que o aplicativo foi iniciado, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="054f6-134">After the command shell indicates that the app has started, browse to `https://localhost:5001`.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="054f6-135">Editar uma Razor página</span><span class="sxs-lookup"><span data-stu-id="054f6-135">Edit a Razor page</span></span>

<span data-ttu-id="054f6-136">Abra *pages/index. cshtml* e modifique e salve a página com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="054f6-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="054f6-137">Navegue até `https://localhost:5001`, atualize a página e verifique se as alterações são exibidas.</span><span class="sxs-lookup"><span data-stu-id="054f6-137">Browse to `https://localhost:5001`, refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="054f6-138">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="054f6-138">Next steps</span></span>

<span data-ttu-id="054f6-139">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="054f6-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="054f6-140">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="054f6-140">Create a web app project.</span></span>
> * <span data-ttu-id="054f6-141">Confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="054f6-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="054f6-142">Execute o projeto.</span><span class="sxs-lookup"><span data-stu-id="054f6-142">Run the project.</span></span>
> * <span data-ttu-id="054f6-143">Faça uma alteração.</span><span class="sxs-lookup"><span data-stu-id="054f6-143">Make a change.</span></span>

<span data-ttu-id="054f6-144">Para saber mais sobre o ASP.NET Core, veja o caminho de aprendizado recomendados na introdução:</span><span class="sxs-lookup"><span data-stu-id="054f6-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>

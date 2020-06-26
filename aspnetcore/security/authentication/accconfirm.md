---
title: Confirmação de conta e de recuperação de senha no ASP.NET Core
author: rick-anderson
description: Saiba como criar um aplicativo ASP.NET Core com confirmação de email e redefinição de senha.
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: bf599487fdc3e574f72f1a3d35278cc9c2ce7513
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404646"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="bf2e4-103">Confirmação de conta e de recuperação de senha no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf2e4-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="bf2e4-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="bf2e4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="bf2e4-105">Este tutorial mostra como criar um aplicativo ASP.NET Core com confirmação de email e redefinição de senha.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="bf2e4-106">Este tutorial **não** é um tópico inicial.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="bf2e4-107">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-107">You should be familiar with:</span></span>

* [<span data-ttu-id="bf2e4-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf2e4-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="bf2e4-109">Autenticação</span><span class="sxs-lookup"><span data-stu-id="bf2e4-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="bf2e4-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="bf2e4-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="bf2e4-111">Consulte [este arquivo PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) para a versão ASP.NET Core 1,1.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="bf2e4-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="bf2e4-112">Prerequisites</span></span>

[<span data-ttu-id="bf2e4-113">SDK do .NET Core 3,0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bf2e4-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="bf2e4-114">Criar e testar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="bf2e4-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="bf2e4-115">Execute os comandos a seguir para criar um aplicativo Web com autenticação.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="bf2e4-116">Execute o aplicativo, selecione o link **registrar** e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="bf2e4-117">Depois de registrado, você será redirecionado para a `/Identity/Account/RegisterConfirmation` página para, que contém um link para simular a confirmação de email:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="bf2e4-118">Selecione o `Click here to confirm your account` link.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="bf2e4-119">Selecione o link de **logon** e entre com as mesmas credenciais.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="bf2e4-120">Selecione o `Hello YourEmail@provider.com!` link, que o redireciona para a `/Identity/Account/Manage/PersonalData` página.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="bf2e4-121">Selecione a guia **dados pessoais** à esquerda e, em seguida, selecione **excluir**.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="bf2e4-122">Configurar um provedor de email</span><span class="sxs-lookup"><span data-stu-id="bf2e4-122">Configure an email provider</span></span>

<span data-ttu-id="bf2e4-123">Neste tutorial, [SendGrid](https://sendgrid.com) é usado para enviar email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="bf2e4-124">Você precisa de uma conta e chave do SendGrid para enviar email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="bf2e4-125">Você pode usar outros provedores de email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-125">You can use other email providers.</span></span> <span data-ttu-id="bf2e4-126">Recomendamos que você use o SendGrid ou outro serviço de email para enviar email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="bf2e4-127">O SMTP é difícil de proteger e configurar corretamente.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="bf2e4-128">A conta SendGrid pode exigir a [adição de um remetente](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-128">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="bf2e4-129">Crie uma classe para buscar a chave de email seguro.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-129">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="bf2e4-130">Para este exemplo, crie *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-130">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="bf2e4-131">Configurar segredos de usuário do SendGrid</span><span class="sxs-lookup"><span data-stu-id="bf2e4-131">Configure SendGrid user secrets</span></span>

<span data-ttu-id="bf2e4-132">Defina `SendGridUser` e `SendGridKey` com a [ferramenta Secret-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-132">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="bf2e4-133">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-133">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="bf2e4-134">No Windows, o Gerenciador de segredo armazena pares de chaves/valores em um *secrets.jsno* arquivo no `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` diretório.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-134">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="bf2e4-135">O conteúdo do *secrets.jsno* arquivo não está criptografado.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-135">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="bf2e4-136">A marcação a seguir mostra o *secrets.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-136">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="bf2e4-137">O `SendGridKey` valor foi removido.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-137">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="bf2e4-138">Para obter mais informações, consulte o [padrão de opções](xref:fundamentals/configuration/options) e [configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-138">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="bf2e4-139">Instalar o SendGrid</span><span class="sxs-lookup"><span data-stu-id="bf2e4-139">Install SendGrid</span></span>

<span data-ttu-id="bf2e4-140">Este tutorial mostra como adicionar notificações por email por meio do [SendGrid](https://sendgrid.com/), mas você pode enviar emails usando o SMTP e outros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-140">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="bf2e4-141">Instale o `SendGrid` pacote NuGet:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-141">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf2e4-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf2e4-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf2e4-143">No console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-143">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="bf2e4-144">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="bf2e4-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bf2e4-145">No console do, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-145">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="bf2e4-146">Consulte Introdução ao [SendGrid gratuitamente](https://sendgrid.com/free/) para se registrar para uma conta gratuita do SendGrid.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-146">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="bf2e4-147">Implementar IEmailSender</span><span class="sxs-lookup"><span data-stu-id="bf2e4-147">Implement IEmailSender</span></span>

<span data-ttu-id="bf2e4-148">Para implementar `IEmailSender` , crie os *Serviços/EmailSender. cs* com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-148">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="bf2e4-149">Configurar a inicialização para o email de suporte</span><span class="sxs-lookup"><span data-stu-id="bf2e4-149">Configure startup to support email</span></span>

<span data-ttu-id="bf2e4-150">Adicione o seguinte código ao `ConfigureServices` método no arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="bf2e4-150">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="bf2e4-151">Adicionar `EmailSender` como um serviço transitório.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-151">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="bf2e4-152">Registre a `AuthMessageSenderOptions` instância de configuração.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-152">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="bf2e4-153">Registrar, confirmar email e Redefinir senha</span><span class="sxs-lookup"><span data-stu-id="bf2e4-153">Register, confirm email, and reset password</span></span>

<span data-ttu-id="bf2e4-154">Execute o aplicativo Web e teste a confirmação da conta e o fluxo de recuperação de senha.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-154">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="bf2e4-155">Executar o aplicativo e registrar um novo usuário</span><span class="sxs-lookup"><span data-stu-id="bf2e4-155">Run the app and register a new user</span></span>
* <span data-ttu-id="bf2e4-156">Verifique seu email para o link de confirmação da conta.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-156">Check your email for the account confirmation link.</span></span> <span data-ttu-id="bf2e4-157">Consulte [depurar email](#debug) se você não receber o email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-157">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="bf2e4-158">Clique no link para confirmar seu email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-158">Click the link to confirm your email.</span></span>
* <span data-ttu-id="bf2e4-159">Entre com seu email e senha.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-159">Sign in with your email and password.</span></span>
* <span data-ttu-id="bf2e4-160">Saia.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-160">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="bf2e4-161">Testar redefinição de senha</span><span class="sxs-lookup"><span data-stu-id="bf2e4-161">Test password reset</span></span>

* <span data-ttu-id="bf2e4-162">Se você estiver conectado, selecione **logout**.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-162">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="bf2e4-163">Selecione o link **fazer logon** e selecione o link **esqueceu sua senha?** .</span><span class="sxs-lookup"><span data-stu-id="bf2e4-163">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="bf2e4-164">Insira o email que você usou para registrar a conta.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-164">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="bf2e4-165">Um email com um link para redefinir sua senha é enviado.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-165">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="bf2e4-166">Verifique seu email e clique no link para redefinir sua senha.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-166">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="bf2e4-167">Depois que a senha for redefinida com êxito, você poderá entrar com seu email e nova senha.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-167">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="bf2e4-168">Alterar o tempo limite de email e atividade</span><span class="sxs-lookup"><span data-stu-id="bf2e4-168">Change email and activity timeout</span></span>

<span data-ttu-id="bf2e4-169">O tempo limite de inatividade padrão é de 14 dias.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-169">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="bf2e4-170">O código a seguir define o tempo limite de inatividade como 5 dias:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-170">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="bf2e4-171">Alterar todas as vidas de token de proteção de dados</span><span class="sxs-lookup"><span data-stu-id="bf2e4-171">Change all data protection token lifespans</span></span>

<span data-ttu-id="bf2e4-172">O código a seguir altera todos os tokens de proteção de dados período de tempo limite para 3 horas:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-172">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="bf2e4-173">Os Identity tokens de usuário internos (consulte [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) têm um [tempo limite de um dia](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-173">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="bf2e4-174">Alterar o ciclo de vida do token de email</span><span class="sxs-lookup"><span data-stu-id="bf2e4-174">Change the email token lifespan</span></span>

<span data-ttu-id="bf2e4-175">A vida útil do token padrão dos [ Identity tokens de usuário](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) é de [um dia](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-175">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="bf2e4-176">Esta seção mostra como alterar o ciclo de vida do token de email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-176">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="bf2e4-177">Adicione um [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizado e <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="bf2e4-177">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="bf2e4-178">Adicione o provedor personalizado ao contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-178">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="bf2e4-179">Reenviar confirmação de email</span><span class="sxs-lookup"><span data-stu-id="bf2e4-179">Resend email confirmation</span></span>

<span data-ttu-id="bf2e4-180">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-180">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="bf2e4-181">Depurar email</span><span class="sxs-lookup"><span data-stu-id="bf2e4-181">Debug email</span></span>

<span data-ttu-id="bf2e4-182">Se você não puder obter emails funcionando:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-182">If you can't get email working:</span></span>

* <span data-ttu-id="bf2e4-183">Defina um ponto de interrupção em `EmailSender.Execute` para verificar `SendGridClient.SendEmailAsync` é chamado.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-183">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="bf2e4-184">Crie um [aplicativo de console para enviar emails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando código semelhante para `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="bf2e4-184">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="bf2e4-185">Examine a página [atividade de email](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="bf2e4-185">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="bf2e4-186">Verifique sua pasta de spam.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-186">Check your spam folder.</span></span>
* <span data-ttu-id="bf2e4-187">Experimente outro alias de email em um provedor de email diferente (Microsoft, Yahoo, Gmail, etc.)</span><span class="sxs-lookup"><span data-stu-id="bf2e4-187">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="bf2e4-188">Tente enviar para contas de email diferentes.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-188">Try sending to different email accounts.</span></span>

<span data-ttu-id="bf2e4-189">**Uma prática recomendada de segurança** é **não** usar segredos de produção em teste e desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-189">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="bf2e4-190">Se você publicar o aplicativo no Azure, defina os segredos do SendGrid como configurações de aplicativo no portal do aplicativo Web do Azure.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-190">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="bf2e4-191">O sistema de configuração é configurado para ler chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-191">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="bf2e4-192">Combinar contas de logon sociais e locais</span><span class="sxs-lookup"><span data-stu-id="bf2e4-192">Combine social and local login accounts</span></span>

<span data-ttu-id="bf2e4-193">Para concluir esta seção, primeiro você deve habilitar um provedor de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-193">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="bf2e4-194">Consulte [Facebook, Google e autenticação de provedor externo](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-194">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="bf2e4-195">Você pode combinar contas locais e sociais clicando no link de email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-195">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="bf2e4-196">Na sequência a seguir, " RickAndMSFT@gmail.com " é criado primeiro como um logon local; no entanto, você pode criar a conta como um logon social primeiro e, em seguida, adicionar um logon local.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-196">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicativo Web: RickAndMSFT@gmail.com usuário autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="bf2e4-198">Clique no link **gerenciar** .</span><span class="sxs-lookup"><span data-stu-id="bf2e4-198">Click on the **Manage** link.</span></span> <span data-ttu-id="bf2e4-199">Observe o 0 externo (logons sociais) associado a essa conta.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-199">Note the 0 external (social logins) associated with this account.</span></span>

![Gerenciar modo de exibição](accconfirm/_static/manage.png)

<span data-ttu-id="bf2e4-201">Clique no link para outro serviço de logon e aceite as solicitações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-201">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="bf2e4-202">Na imagem a seguir, o Facebook é o provedor de autenticação externa:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-202">In the following image, Facebook is the external authentication provider:</span></span>

![Gerenciar seus logons externos exibir listando o Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="bf2e4-204">As duas contas foram combinadas.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-204">The two accounts have been combined.</span></span> <span data-ttu-id="bf2e4-205">Você pode entrar com qualquer uma das contas.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-205">You are able to sign in with either account.</span></span> <span data-ttu-id="bf2e4-206">Talvez você queira que os usuários adicionem contas locais caso o serviço de autenticação de logon social esteja inativo ou, provavelmente, tenha perdido o acesso à sua conta social.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-206">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="bf2e4-207">Habilitar confirmação de conta depois que um site tiver usuários</span><span class="sxs-lookup"><span data-stu-id="bf2e4-207">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="bf2e4-208">Habilitar a confirmação de conta em um site com usuários bloqueia todos os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-208">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="bf2e4-209">Os usuários existentes são bloqueados porque suas contas não são confirmadas.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-209">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="bf2e4-210">Para contornar o bloqueio de usuário existente, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-210">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="bf2e4-211">Atualize o banco de dados para marcar todos os usuários existentes como sendo confirmados.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-211">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="bf2e4-212">Confirme os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-212">Confirm existing users.</span></span> <span data-ttu-id="bf2e4-213">Por exemplo, enviar emails por lote com links de confirmação.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-213">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="bf2e4-214">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="bf2e4-214">Prerequisites</span></span>

[<span data-ttu-id="bf2e4-215">SDK do .NET Core 2,2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bf2e4-215">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="bf2e4-216">Criar um aplicativo Web e ScaffoldIdentity</span><span class="sxs-lookup"><span data-stu-id="bf2e4-216">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="bf2e4-217">Execute os comandos a seguir para criar um aplicativo Web com autenticação.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-217">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="bf2e4-218">Testar novo registro de usuário</span><span class="sxs-lookup"><span data-stu-id="bf2e4-218">Test new user registration</span></span>

<span data-ttu-id="bf2e4-219">Execute o aplicativo, selecione o link **registrar** e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-219">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="bf2e4-220">Neste ponto, a única validação no email é com o [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-220">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="bf2e4-221">Depois de enviar o registro, você está conectado ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-221">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="bf2e4-222">Posteriormente no tutorial, o código será atualizado para que novos usuários não possam entrar até que seu email seja validado.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-222">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="bf2e4-223">Observe que o campo da tabela `EmailConfirmed` é `False` .</span><span class="sxs-lookup"><span data-stu-id="bf2e4-223">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="bf2e4-224">Você pode querer usar esse email novamente na próxima etapa quando o aplicativo enviar um email de confirmação.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-224">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="bf2e4-225">Clique com o botão direito do mouse na linha e selecione **excluir**.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-225">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="bf2e4-226">A exclusão do alias de email torna mais fácil as etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-226">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="bf2e4-227">Exigir confirmação de email</span><span class="sxs-lookup"><span data-stu-id="bf2e4-227">Require email confirmation</span></span>

<span data-ttu-id="bf2e4-228">É uma prática recomendada confirmar o email de um novo registro de usuário.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-228">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="bf2e4-229">A confirmação por email ajuda a verificar se eles não estão representando outra pessoa (ou seja, se eles não se registraram no email de outra pessoa).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-229">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="bf2e4-230">Suponha que você tenha um fórum de discussão e queira impedir " yli@example.com " de se registrar como " nolivetto@contoso.com ".</span><span class="sxs-lookup"><span data-stu-id="bf2e4-230">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="bf2e4-231">Sem confirmação por email, " nolivetto@contoso.com " pode receber emails indesejados do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-231">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="bf2e4-232">Suponha que o usuário se registrou acidentalmente como " ylo@example.com " e não tenha notado a grafia incorreta de "Yli".</span><span class="sxs-lookup"><span data-stu-id="bf2e4-232">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="bf2e4-233">Eles não poderão usar a recuperação de senha porque o aplicativo não tem seu email correto.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-233">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="bf2e4-234">A confirmação por email fornece proteção limitada de bots.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-234">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="bf2e4-235">A confirmação por email não fornece proteção contra usuários mal-intencionados com muitas contas de email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-235">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="bf2e4-236">Em geral, você deseja impedir que novos usuários enviem dados para seu site antes de terem um email confirmado.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-236">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="bf2e4-237">Atualize `Startup.ConfigureServices` para exigir um email confirmado:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-237">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="bf2e4-238">`config.SignIn.RequireConfirmedEmail = true;`impede que os usuários registrados façam logon até que seu email seja confirmado.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-238">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="bf2e4-239">Configurar provedor de email</span><span class="sxs-lookup"><span data-stu-id="bf2e4-239">Configure email provider</span></span>

<span data-ttu-id="bf2e4-240">Neste tutorial, [SendGrid](https://sendgrid.com) é usado para enviar email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-240">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="bf2e4-241">Você precisa de uma conta e chave do SendGrid para enviar email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-241">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="bf2e4-242">Você pode usar outros provedores de email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-242">You can use other email providers.</span></span> <span data-ttu-id="bf2e4-243">O ASP.NET Core 2. x inclui `System.Net.Mail` , que permite enviar emails do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-243">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="bf2e4-244">Recomendamos que você use o SendGrid ou outro serviço de email para enviar email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-244">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="bf2e4-245">O SMTP é difícil de proteger e configurar corretamente.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-245">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="bf2e4-246">Crie uma classe para buscar a chave de email seguro.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-246">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="bf2e4-247">Para este exemplo, crie *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-247">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="bf2e4-248">Configurar segredos de usuário do SendGrid</span><span class="sxs-lookup"><span data-stu-id="bf2e4-248">Configure SendGrid user secrets</span></span>

<span data-ttu-id="bf2e4-249">Defina `SendGridUser` e `SendGridKey` com a [ferramenta Secret-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-249">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="bf2e4-250">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-250">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="bf2e4-251">No Windows, o Gerenciador de segredo armazena pares de chaves/valores em um *secrets.jsno* arquivo no `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` diretório.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-251">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="bf2e4-252">O conteúdo do *secrets.jsno* arquivo não está criptografado.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-252">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="bf2e4-253">A marcação a seguir mostra o *secrets.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-253">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="bf2e4-254">O `SendGridKey` valor foi removido.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-254">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="bf2e4-255">Para obter mais informações, consulte o [padrão de opções](xref:fundamentals/configuration/options) e [configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-255">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="bf2e4-256">Instalar o SendGrid</span><span class="sxs-lookup"><span data-stu-id="bf2e4-256">Install SendGrid</span></span>

<span data-ttu-id="bf2e4-257">Este tutorial mostra como adicionar notificações por email por meio do [SendGrid](https://sendgrid.com/), mas você pode enviar emails usando o SMTP e outros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-257">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="bf2e4-258">Instale o `SendGrid` pacote NuGet:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-258">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf2e4-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf2e4-259">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf2e4-260">No console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-260">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="bf2e4-261">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="bf2e4-261">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bf2e4-262">No console do, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-262">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="bf2e4-263">Consulte Introdução ao [SendGrid gratuitamente](https://sendgrid.com/free/) para se registrar para uma conta gratuita do SendGrid.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-263">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="bf2e4-264">Implementar IEmailSender</span><span class="sxs-lookup"><span data-stu-id="bf2e4-264">Implement IEmailSender</span></span>

<span data-ttu-id="bf2e4-265">Para implementar `IEmailSender` , crie os *Serviços/EmailSender. cs* com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-265">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="bf2e4-266">Configurar a inicialização para o email de suporte</span><span class="sxs-lookup"><span data-stu-id="bf2e4-266">Configure startup to support email</span></span>

<span data-ttu-id="bf2e4-267">Adicione o seguinte código ao `ConfigureServices` método no arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="bf2e4-267">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="bf2e4-268">Adicionar `EmailSender` como um serviço transitório.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-268">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="bf2e4-269">Registre a `AuthMessageSenderOptions` instância de configuração.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-269">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="bf2e4-270">Habilitar confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="bf2e4-270">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="bf2e4-271">O modelo tem o código para confirmação de conta e recuperação de senha.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-271">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="bf2e4-272">Localize o `OnPostAsync` método em *áreas/ Identity /pages/Account/Register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-272">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="bf2e4-273">Impedir que usuários registrados recentemente façam logon automaticamente comentando a seguinte linha:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-273">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="bf2e4-274">O método Complete é mostrado com a linha alterada realçada:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-274">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="bf2e4-275">Registrar, confirmar email e Redefinir senha</span><span class="sxs-lookup"><span data-stu-id="bf2e4-275">Register, confirm email, and reset password</span></span>

<span data-ttu-id="bf2e4-276">Execute o aplicativo Web e teste a confirmação da conta e o fluxo de recuperação de senha.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-276">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="bf2e4-277">Executar o aplicativo e registrar um novo usuário</span><span class="sxs-lookup"><span data-stu-id="bf2e4-277">Run the app and register a new user</span></span>
* <span data-ttu-id="bf2e4-278">Verifique seu email para o link de confirmação da conta.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-278">Check your email for the account confirmation link.</span></span> <span data-ttu-id="bf2e4-279">Consulte [depurar email](#debug) se você não receber o email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-279">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="bf2e4-280">Clique no link para confirmar seu email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-280">Click the link to confirm your email.</span></span>
* <span data-ttu-id="bf2e4-281">Entre com seu email e senha.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-281">Sign in with your email and password.</span></span>
* <span data-ttu-id="bf2e4-282">Saia.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-282">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="bf2e4-283">Exibir a página Gerenciar</span><span class="sxs-lookup"><span data-stu-id="bf2e4-283">View the manage page</span></span>

<span data-ttu-id="bf2e4-284">Selecione seu nome de usuário no navegador: ![ janela do navegador com nome de usuário](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="bf2e4-284">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="bf2e4-285">A página Gerenciar é exibida com a guia **perfil** selecionada.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-285">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="bf2e4-286">O **email** mostra uma caixa de seleção indicando que o email foi confirmado.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-286">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="bf2e4-287">Testar redefinição de senha</span><span class="sxs-lookup"><span data-stu-id="bf2e4-287">Test password reset</span></span>

* <span data-ttu-id="bf2e4-288">Se você estiver conectado, selecione **logout**.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-288">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="bf2e4-289">Selecione o link **fazer logon** e selecione o link **esqueceu sua senha?** .</span><span class="sxs-lookup"><span data-stu-id="bf2e4-289">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="bf2e4-290">Insira o email que você usou para registrar a conta.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-290">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="bf2e4-291">Um email com um link para redefinir sua senha é enviado.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-291">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="bf2e4-292">Verifique seu email e clique no link para redefinir sua senha.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-292">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="bf2e4-293">Depois que a senha for redefinida com êxito, você poderá entrar com seu email e nova senha.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-293">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="bf2e4-294">Alterar o tempo limite de email e atividade</span><span class="sxs-lookup"><span data-stu-id="bf2e4-294">Change email and activity timeout</span></span>

<span data-ttu-id="bf2e4-295">O tempo limite de inatividade padrão é de 14 dias.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-295">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="bf2e4-296">O código a seguir define o tempo limite de inatividade como 5 dias:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-296">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="bf2e4-297">Alterar todas as vidas de token de proteção de dados</span><span class="sxs-lookup"><span data-stu-id="bf2e4-297">Change all data protection token lifespans</span></span>

<span data-ttu-id="bf2e4-298">O código a seguir altera todos os tokens de proteção de dados período de tempo limite para 3 horas:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-298">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="bf2e4-299">Os Identity tokens de usuário internos (consulte [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) têm um [tempo limite de um dia](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-299">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="bf2e4-300">Alterar o ciclo de vida do token de email</span><span class="sxs-lookup"><span data-stu-id="bf2e4-300">Change the email token lifespan</span></span>

<span data-ttu-id="bf2e4-301">A vida útil do token padrão dos [ Identity tokens de usuário](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) é de [um dia](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-301">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="bf2e4-302">Esta seção mostra como alterar o ciclo de vida do token de email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-302">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="bf2e4-303">Adicione um [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizado e <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="bf2e4-303">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="bf2e4-304">Adicione o provedor personalizado ao contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-304">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="bf2e4-305">Reenviar confirmação de email</span><span class="sxs-lookup"><span data-stu-id="bf2e4-305">Resend email confirmation</span></span>

<span data-ttu-id="bf2e4-306">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-306">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="bf2e4-307">Depurar email</span><span class="sxs-lookup"><span data-stu-id="bf2e4-307">Debug email</span></span>

<span data-ttu-id="bf2e4-308">Se você não puder obter emails funcionando:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-308">If you can't get email working:</span></span>

* <span data-ttu-id="bf2e4-309">Defina um ponto de interrupção em `EmailSender.Execute` para verificar `SendGridClient.SendEmailAsync` é chamado.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-309">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="bf2e4-310">Crie um [aplicativo de console para enviar emails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando código semelhante para `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="bf2e4-310">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="bf2e4-311">Examine a página [atividade de email](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="bf2e4-311">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="bf2e4-312">Verifique sua pasta de spam.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-312">Check your spam folder.</span></span>
* <span data-ttu-id="bf2e4-313">Experimente outro alias de email em um provedor de email diferente (Microsoft, Yahoo, Gmail, etc.)</span><span class="sxs-lookup"><span data-stu-id="bf2e4-313">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="bf2e4-314">Tente enviar para contas de email diferentes.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-314">Try sending to different email accounts.</span></span>

<span data-ttu-id="bf2e4-315">**Uma prática recomendada de segurança** é **não** usar segredos de produção em teste e desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-315">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="bf2e4-316">Se você publicar o aplicativo no Azure, poderá definir os segredos do SendGrid como configurações do aplicativo no portal do aplicativo Web do Azure.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-316">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="bf2e4-317">O sistema de configuração é configurado para ler chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-317">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="bf2e4-318">Combinar contas de logon sociais e locais</span><span class="sxs-lookup"><span data-stu-id="bf2e4-318">Combine social and local login accounts</span></span>

<span data-ttu-id="bf2e4-319">Para concluir esta seção, primeiro você deve habilitar um provedor de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-319">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="bf2e4-320">Consulte [Facebook, Google e autenticação de provedor externo](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="bf2e4-320">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="bf2e4-321">Você pode combinar contas locais e sociais clicando no link de email.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-321">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="bf2e4-322">Na sequência a seguir, " RickAndMSFT@gmail.com " é criado primeiro como um logon local; no entanto, você pode criar a conta como um logon social primeiro e, em seguida, adicionar um logon local.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-322">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicativo Web: RickAndMSFT@gmail.com usuário autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="bf2e4-324">Clique no link **gerenciar** .</span><span class="sxs-lookup"><span data-stu-id="bf2e4-324">Click on the **Manage** link.</span></span> <span data-ttu-id="bf2e4-325">Observe o 0 externo (logons sociais) associado a essa conta.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-325">Note the 0 external (social logins) associated with this account.</span></span>

![Gerenciar modo de exibição](accconfirm/_static/manage.png)

<span data-ttu-id="bf2e4-327">Clique no link para outro serviço de logon e aceite as solicitações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-327">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="bf2e4-328">Na imagem a seguir, o Facebook é o provedor de autenticação externa:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-328">In the following image, Facebook is the external authentication provider:</span></span>

![Gerenciar seus logons externos exibir listando o Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="bf2e4-330">As duas contas foram combinadas.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-330">The two accounts have been combined.</span></span> <span data-ttu-id="bf2e4-331">Você pode entrar com qualquer uma das contas.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-331">You are able to sign in with either account.</span></span> <span data-ttu-id="bf2e4-332">Talvez você queira que os usuários adicionem contas locais caso o serviço de autenticação de logon social esteja inativo ou, provavelmente, tenha perdido o acesso à sua conta social.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-332">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="bf2e4-333">Habilitar confirmação de conta depois que um site tiver usuários</span><span class="sxs-lookup"><span data-stu-id="bf2e4-333">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="bf2e4-334">Habilitar a confirmação de conta em um site com usuários bloqueia todos os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-334">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="bf2e4-335">Os usuários existentes são bloqueados porque suas contas não são confirmadas.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-335">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="bf2e4-336">Para contornar o bloqueio de usuário existente, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="bf2e4-336">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="bf2e4-337">Atualize o banco de dados para marcar todos os usuários existentes como sendo confirmados.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-337">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="bf2e4-338">Confirme os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-338">Confirm existing users.</span></span> <span data-ttu-id="bf2e4-339">Por exemplo, enviar emails por lote com links de confirmação.</span><span class="sxs-lookup"><span data-stu-id="bf2e4-339">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

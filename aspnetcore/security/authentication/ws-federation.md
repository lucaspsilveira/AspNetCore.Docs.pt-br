---
title: Autenticar usuários com o WS-Federation no ASP.NET Core
author: chlowell
description: Este tutorial demonstra como usar o WS-Federation em um aplicativo ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/ws-federation
ms.openlocfilehash: ce0c484e84bc2ddb4a1d287246c63663f3875924
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768423"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="6d172-103">Autenticar usuários com o WS-Federation no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d172-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="6d172-104">Este tutorial demonstra como permitir que os usuários entrem com um provedor de autenticação do WS-Federation, como Serviços de Federação do Active Directory (AD FS) (ADFS) ou [Azure Active Directory](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="6d172-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="6d172-105">Ele usa o aplicativo de exemplo ASP.NET Core 2,0 descrito no [Facebook, no Google e na autenticação de provedor externo](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="6d172-105">It uses the ASP.NET Core 2.0 sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="6d172-106">Para os aplicativos ASP.NET Core 2,0, o suporte ao WS-Federation é fornecido pela [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span><span class="sxs-lookup"><span data-stu-id="6d172-106">For ASP.NET Core 2.0 apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="6d172-107">Esse componente é movido de [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) e compartilha muitas das mecânicas desse componente.</span><span class="sxs-lookup"><span data-stu-id="6d172-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="6d172-108">No entanto, os componentes diferem de algumas maneiras importantes.</span><span class="sxs-lookup"><span data-stu-id="6d172-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="6d172-109">Por padrão, o novo middleware:</span><span class="sxs-lookup"><span data-stu-id="6d172-109">By default, the new middleware:</span></span>

* <span data-ttu-id="6d172-110">Não permite logons não solicitados.</span><span class="sxs-lookup"><span data-stu-id="6d172-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="6d172-111">Esse recurso do protocolo WS-Federation é vulnerável a ataques XSRF.</span><span class="sxs-lookup"><span data-stu-id="6d172-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="6d172-112">No entanto, ele pode ser habilitado `AllowUnsolicitedLogins` com a opção.</span><span class="sxs-lookup"><span data-stu-id="6d172-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="6d172-113">Não verifica cada postagem de formulário para mensagens de entrada.</span><span class="sxs-lookup"><span data-stu-id="6d172-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="6d172-114">Somente as solicitações para `CallbackPath` o são verificadas quanto a entradas `CallbackPath` . o padrão `/signin-wsfed` é, mas pode ser alterado por meio da propriedade herdada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) .</span><span class="sxs-lookup"><span data-stu-id="6d172-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="6d172-115">Esse caminho pode ser compartilhado com outros provedores de autenticação habilitando a opção [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .</span><span class="sxs-lookup"><span data-stu-id="6d172-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="6d172-116">Registrar o aplicativo com Active Directory</span><span class="sxs-lookup"><span data-stu-id="6d172-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="6d172-117">Serviços de Federação do Active Directory</span><span class="sxs-lookup"><span data-stu-id="6d172-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="6d172-118">Abra o **Assistente Adicionar confiança** de terceira parte confiável do servidor no console de gerenciamento do ADFS:</span><span class="sxs-lookup"><span data-stu-id="6d172-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Adicionar Assistente de confiança de terceira parte confiável: Bem-vindo](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="6d172-120">Escolha inserir dados manualmente:</span><span class="sxs-lookup"><span data-stu-id="6d172-120">Choose to enter data manually:</span></span>

![Adicionar Assistente de confiança de terceira parte confiável: selecionar fonte de dados](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="6d172-122">Insira um nome de exibição para a terceira parte confiável.</span><span class="sxs-lookup"><span data-stu-id="6d172-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="6d172-123">O nome não é importante para o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6d172-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="6d172-124">[Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) não tem suporte para criptografia de token, portanto, não configure um certificado de criptografia de token:</span><span class="sxs-lookup"><span data-stu-id="6d172-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Adicionar Assistente de confiança de terceira parte confiável: configurar certificado](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="6d172-126">Habilite o suporte para o protocolo passivo do WS-Federation, usando a URL do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6d172-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="6d172-127">Verifique se a porta está correta para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="6d172-127">Verify the port is correct for the app:</span></span>

![Adicionar Assistente de confiança de terceira parte confiável: configurar URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="6d172-129">Deve ser uma URL HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6d172-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="6d172-130">IIS Express pode fornecer um certificado autoassinado ao hospedar o aplicativo durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="6d172-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="6d172-131">O Kestrel requer configuração manual de certificado.</span><span class="sxs-lookup"><span data-stu-id="6d172-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="6d172-132">Consulte a [documentação do Kestrel](xref:fundamentals/servers/kestrel) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="6d172-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="6d172-133">Clique em **Avançar** no restante do assistente e **feche** no final.</span><span class="sxs-lookup"><span data-stu-id="6d172-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="6d172-134">ASP.NET Core Identity requer uma declaração de **ID de nome** .</span><span class="sxs-lookup"><span data-stu-id="6d172-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="6d172-135">Adicione um da caixa de diálogo **Editar regras de declaração** :</span><span class="sxs-lookup"><span data-stu-id="6d172-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Editar regras de declaração](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="6d172-137">No **Assistente Adicionar regra de declaração de transformação**, deixe o modelo **Enviar atributos LDAP padrão como declarações** selecionado e clique em **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6d172-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="6d172-138">Adicione um mapeamento de regra ao atributo **Sam-Account-Name** LDAP para a declaração de saída **ID de nome** :</span><span class="sxs-lookup"><span data-stu-id="6d172-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Assistente para Adicionar regra de declaração de transformação: configurar regra de declaração](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="6d172-140">Clique em **concluir** > **OK** na janela **Editar regras de declaração** .</span><span class="sxs-lookup"><span data-stu-id="6d172-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="6d172-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="6d172-141">Azure Active Directory</span></span>

* <span data-ttu-id="6d172-142">Navegue até a folha de registros do aplicativo do locatário do AAD.</span><span class="sxs-lookup"><span data-stu-id="6d172-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="6d172-143">Clique em **novo registro de aplicativo**:</span><span class="sxs-lookup"><span data-stu-id="6d172-143">Click **New application registration**:</span></span>

![Azure Active Directory: Registros de aplicativo](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="6d172-145">Insira um nome para o registro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6d172-145">Enter a name for the app registration.</span></span> <span data-ttu-id="6d172-146">Isso não é importante para o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6d172-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="6d172-147">Insira a URL na qual o aplicativo escutará como a **URL de logon**:</span><span class="sxs-lookup"><span data-stu-id="6d172-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: criar registro de aplicativo](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="6d172-149">Clique em **pontos de extremidade** e anote a URL do documento de metadados de **Federação** .</span><span class="sxs-lookup"><span data-stu-id="6d172-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="6d172-150">Este é o middleware do `MetadataAddress`WS-Federation:</span><span class="sxs-lookup"><span data-stu-id="6d172-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: pontos de extremidade](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="6d172-152">Navegue até o novo registro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6d172-152">Navigate to the new app registration.</span></span> <span data-ttu-id="6d172-153">Clique em **configurações** > **Propriedades** e anote o **URI da ID do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="6d172-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="6d172-154">Este é o middleware do `Wtrealm`WS-Federation:</span><span class="sxs-lookup"><span data-stu-id="6d172-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: Propriedades de registro do aplicativo](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="6d172-156">Usar o WS-Federation sem ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="6d172-156">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="6d172-157">O middleware do WS-Federation pode ser usado sem Identityo.</span><span class="sxs-lookup"><span data-stu-id="6d172-157">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="6d172-158">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="6d172-158">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="6d172-159">Adicione o WS-Federation como um provedor de logon externo para ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="6d172-159">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="6d172-160">Adicione uma dependência em [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="6d172-160">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="6d172-161">Adicionar WS-Federation a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6d172-161">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="6d172-162">Fazer logon com o WS-Federation</span><span class="sxs-lookup"><span data-stu-id="6d172-162">Log in with WS-Federation</span></span>

<span data-ttu-id="6d172-163">Navegue até o aplicativo e clique no link **fazer logon** no cabeçalho de navegação.</span><span class="sxs-lookup"><span data-stu-id="6d172-163">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="6d172-164">Há uma opção para fazer logon com WsFederation: ![logon na página](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="6d172-164">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="6d172-165">Com o ADFS como o provedor, o botão redireciona para uma página de entrada do ADFS: ![página de entrada do ADFS](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="6d172-165">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="6d172-166">Com Azure Active Directory como o provedor, o botão redireciona para uma página de entrada do AAD: ![página de entrada do AAD](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="6d172-166">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="6d172-167">Uma entrada bem-sucedida para um novo usuário redireciona para a página de registro do usuário do aplicativo: ![página de registro](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="6d172-167">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>
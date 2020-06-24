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
ms.openlocfilehash: 62b8e33d8b7eb17a65a7a54df2a9aa298acdfe36
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292796"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="3296f-103">Autenticar usuários com o WS-Federation no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3296f-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="3296f-104">Este tutorial demonstra como permitir que os usuários entrem com um provedor de autenticação do WS-Federation, como Serviços de Federação do Active Directory (AD FS) (ADFS) ou [Azure Active Directory](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="3296f-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="3296f-105">Ele usa o aplicativo de exemplo ASP.NET Core descrito no [Facebook, no Google e na autenticação de provedor externo](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="3296f-105">It uses the ASP.NET Core sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="3296f-106">Para aplicativos ASP.NET Core, o suporte ao WS-Federation é fornecido pela [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span><span class="sxs-lookup"><span data-stu-id="3296f-106">For ASP.NET Core apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="3296f-107">Esse componente é movido de [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) e compartilha muitas das mecânicas desse componente.</span><span class="sxs-lookup"><span data-stu-id="3296f-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="3296f-108">No entanto, os componentes diferem de algumas maneiras importantes.</span><span class="sxs-lookup"><span data-stu-id="3296f-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="3296f-109">Por padrão, o novo middleware:</span><span class="sxs-lookup"><span data-stu-id="3296f-109">By default, the new middleware:</span></span>

* <span data-ttu-id="3296f-110">Não permite logons não solicitados.</span><span class="sxs-lookup"><span data-stu-id="3296f-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="3296f-111">Esse recurso do protocolo WS-Federation é vulnerável a ataques XSRF.</span><span class="sxs-lookup"><span data-stu-id="3296f-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="3296f-112">No entanto, ele pode ser habilitado com a `AllowUnsolicitedLogins` opção.</span><span class="sxs-lookup"><span data-stu-id="3296f-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="3296f-113">Não verifica cada postagem de formulário para mensagens de entrada.</span><span class="sxs-lookup"><span data-stu-id="3296f-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="3296f-114">Somente as solicitações para o `CallbackPath` são verificadas quanto a entradas. o `CallbackPath` padrão é `/signin-wsfed` , mas pode ser alterado por meio da propriedade herdada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) .</span><span class="sxs-lookup"><span data-stu-id="3296f-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="3296f-115">Esse caminho pode ser compartilhado com outros provedores de autenticação habilitando a opção [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .</span><span class="sxs-lookup"><span data-stu-id="3296f-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="3296f-116">Registrar o aplicativo com Active Directory</span><span class="sxs-lookup"><span data-stu-id="3296f-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="3296f-117">Serviços de Federação do Active Directory (AD FS)</span><span class="sxs-lookup"><span data-stu-id="3296f-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="3296f-118">Abra o **Assistente Adicionar confiança** de terceira parte confiável do servidor no console de gerenciamento do ADFS:</span><span class="sxs-lookup"><span data-stu-id="3296f-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Adicionar Assistente de confiança de terceira parte confiável: Bem-vindo](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="3296f-120">Escolha inserir dados manualmente:</span><span class="sxs-lookup"><span data-stu-id="3296f-120">Choose to enter data manually:</span></span>

![Adicionar Assistente de confiança de terceira parte confiável: selecionar fonte de dados](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="3296f-122">Insira um nome de exibição para a terceira parte confiável.</span><span class="sxs-lookup"><span data-stu-id="3296f-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="3296f-123">O nome não é importante para o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3296f-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="3296f-124">[Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) não tem suporte para criptografia de token, portanto, não configure um certificado de criptografia de token:</span><span class="sxs-lookup"><span data-stu-id="3296f-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Adicionar Assistente de confiança de terceira parte confiável: configurar certificado](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="3296f-126">Habilite o suporte para o protocolo passivo do WS-Federation, usando a URL do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3296f-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="3296f-127">Verifique se a porta está correta para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="3296f-127">Verify the port is correct for the app:</span></span>

![Adicionar Assistente de confiança de terceira parte confiável: configurar URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="3296f-129">Deve ser uma URL HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3296f-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="3296f-130">IIS Express pode fornecer um certificado autoassinado ao hospedar o aplicativo durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="3296f-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="3296f-131">O Kestrel requer configuração manual de certificado.</span><span class="sxs-lookup"><span data-stu-id="3296f-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="3296f-132">Consulte a [documentação do Kestrel](xref:fundamentals/servers/kestrel) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="3296f-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="3296f-133">Clique em **Avançar** no restante do assistente e **feche** no final.</span><span class="sxs-lookup"><span data-stu-id="3296f-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="3296f-134">ASP.NET Core Identity requer uma declaração de **ID de nome** .</span><span class="sxs-lookup"><span data-stu-id="3296f-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="3296f-135">Adicione um da caixa de diálogo **Editar regras de declaração** :</span><span class="sxs-lookup"><span data-stu-id="3296f-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Editar regras de declaração](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="3296f-137">No **Assistente Adicionar regra de declaração de transformação**, deixe o modelo **Enviar atributos LDAP padrão como declarações** selecionado e clique em **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3296f-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="3296f-138">Adicione um mapeamento de regra ao atributo **Sam-Account-Name** LDAP para a declaração de saída **ID de nome** :</span><span class="sxs-lookup"><span data-stu-id="3296f-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Assistente para Adicionar regra de declaração de transformação: configurar regra de declaração](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="3296f-140">Clique em **concluir**  >  **OK** na janela **Editar regras de declaração** .</span><span class="sxs-lookup"><span data-stu-id="3296f-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="3296f-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="3296f-141">Azure Active Directory</span></span>

* <span data-ttu-id="3296f-142">Navegue até a folha de registros do aplicativo do locatário do AAD.</span><span class="sxs-lookup"><span data-stu-id="3296f-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="3296f-143">Clique em **novo registro de aplicativo**:</span><span class="sxs-lookup"><span data-stu-id="3296f-143">Click **New application registration**:</span></span>

![Azure Active Directory: Registros de aplicativo](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="3296f-145">Insira um nome para o registro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3296f-145">Enter a name for the app registration.</span></span> <span data-ttu-id="3296f-146">Isso não é importante para o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3296f-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="3296f-147">Insira a URL na qual o aplicativo escutará como a **URL de logon**:</span><span class="sxs-lookup"><span data-stu-id="3296f-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: criar registro de aplicativo](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="3296f-149">Clique em **pontos de extremidade** e anote a URL do documento de metadados de **Federação** .</span><span class="sxs-lookup"><span data-stu-id="3296f-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="3296f-150">Este é o middleware do WS-Federation `MetadataAddress` :</span><span class="sxs-lookup"><span data-stu-id="3296f-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: pontos de extremidade](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="3296f-152">Navegue até o novo registro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3296f-152">Navigate to the new app registration.</span></span> <span data-ttu-id="3296f-153">Clique em **expor uma API**.</span><span class="sxs-lookup"><span data-stu-id="3296f-153">Click **Expose an API**.</span></span> <span data-ttu-id="3296f-154">Clique em ID do aplicativo URI **definir**  >  **salvar**.</span><span class="sxs-lookup"><span data-stu-id="3296f-154">Click Application ID URI **Set** > **Save**.</span></span> <span data-ttu-id="3296f-155">Anote o URI da **ID do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="3296f-155">Make note of the  **Application ID URI**.</span></span> <span data-ttu-id="3296f-156">Este é o middleware do WS-Federation `Wtrealm` :</span><span class="sxs-lookup"><span data-stu-id="3296f-156">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: Propriedades de registro do aplicativo](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="3296f-158">Usar o WS-Federation sem ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="3296f-158">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="3296f-159">O middleware do WS-Federation pode ser usado sem o Identity .</span><span class="sxs-lookup"><span data-stu-id="3296f-159">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="3296f-160">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3296f-160">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="3296f-161">Adicione o WS-Federation como um provedor de logon externo para ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="3296f-161">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="3296f-162">Adicione uma dependência em [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="3296f-162">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="3296f-163">Adicionar WS-Federation a `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3296f-163">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="3296f-164">Fazer logon com o WS-Federation</span><span class="sxs-lookup"><span data-stu-id="3296f-164">Log in with WS-Federation</span></span>

<span data-ttu-id="3296f-165">Navegue até o aplicativo e clique no link **fazer logon** no cabeçalho de navegação.</span><span class="sxs-lookup"><span data-stu-id="3296f-165">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="3296f-166">Há uma opção para fazer logon com WsFederation: ![ logon na página](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="3296f-166">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="3296f-167">Com o ADFS como o provedor, o botão redireciona para uma página de entrada do ADFS: ![ página de entrada do ADFS](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="3296f-167">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="3296f-168">Com Azure Active Directory como o provedor, o botão redireciona para uma página de entrada do AAD: página de ![ entrada do AAD](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="3296f-168">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="3296f-169">Uma entrada bem-sucedida para um novo usuário redireciona para a página de registro do usuário do aplicativo: página de ![ registro](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="3296f-169">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>

---
title: Autenticar usuários com o WS-Federation no ASP.NET Core
author: chlowell
description: Este tutorial demonstra como usar o WS-Federation em um aplicativo ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/ws-federation
ms.openlocfilehash: 91dab7764e6915ccacacf73f74861faaee532777
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406219"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>Autenticar usuários com o WS-Federation no ASP.NET Core

Este tutorial demonstra como permitir que os usuários entrem com um provedor de autenticação do WS-Federation, como Serviços de Federação do Active Directory (AD FS) (ADFS) ou [Azure Active Directory](/azure/active-directory/) (AAD). Ele usa o aplicativo de exemplo ASP.NET Core descrito no [Facebook, no Google e na autenticação de provedor externo](xref:security/authentication/social/index).

Para aplicativos ASP.NET Core, o suporte ao WS-Federation é fornecido pela [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation). Esse componente é movido de [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) e compartilha muitas das mecânicas desse componente. No entanto, os componentes diferem de algumas maneiras importantes.

Por padrão, o novo middleware:

* Não permite logons não solicitados. Esse recurso do protocolo WS-Federation é vulnerável a ataques XSRF. No entanto, ele pode ser habilitado com a `AllowUnsolicitedLogins` opção.
* Não verifica cada postagem de formulário para mensagens de entrada. Somente as solicitações para o `CallbackPath` são verificadas quanto a entradas. o `CallbackPath` padrão é `/signin-wsfed` , mas pode ser alterado por meio da propriedade herdada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) . Esse caminho pode ser compartilhado com outros provedores de autenticação habilitando a opção [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .

## <a name="register-the-app-with-active-directory"></a>Registrar o aplicativo com Active Directory

### <a name="active-directory-federation-services"></a>Serviços de Federação do Active Directory (AD FS)

* Abra o **Assistente Adicionar confiança** de terceira parte confiável do servidor no console de gerenciamento do ADFS:

![Adicionar Assistente de confiança de terceira parte confiável: Bem-vindo](ws-federation/_static/AdfsAddTrust.png)

* Escolha inserir dados manualmente:

![Adicionar Assistente de confiança de terceira parte confiável: selecionar fonte de dados](ws-federation/_static/AdfsSelectDataSource.png)

* Insira um nome de exibição para a terceira parte confiável. O nome não é importante para o aplicativo ASP.NET Core.

* [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) não tem suporte para criptografia de token, portanto, não configure um certificado de criptografia de token:

![Adicionar Assistente de confiança de terceira parte confiável: configurar certificado](ws-federation/_static/AdfsConfigureCert.png)

* Habilite o suporte para o protocolo passivo do WS-Federation, usando a URL do aplicativo. Verifique se a porta está correta para o aplicativo:

![Adicionar Assistente de confiança de terceira parte confiável: configurar URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Deve ser uma URL HTTPS. IIS Express pode fornecer um certificado autoassinado ao hospedar o aplicativo durante o desenvolvimento. O Kestrel requer configuração manual de certificado. Consulte a [documentação do Kestrel](xref:fundamentals/servers/kestrel) para obter mais detalhes.

* Clique em **Avançar** no restante do assistente e **feche** no final.

* ASP.NET Core Identity requer uma declaração de **ID de nome** . Adicione um da caixa de diálogo **Editar regras de declaração** :

![Editar regras de declaração](ws-federation/_static/EditClaimRules.png)

* No **Assistente Adicionar regra de declaração de transformação**, deixe o modelo **Enviar atributos LDAP padrão como declarações** selecionado e clique em **Avançar**. Adicione um mapeamento de regra ao atributo **Sam-Account-Name** LDAP para a declaração de saída **ID de nome** :

![Assistente para Adicionar regra de declaração de transformação: configurar regra de declaração](ws-federation/_static/AddTransformClaimRule.png)

* Clique em **concluir**  >  **OK** na janela **Editar regras de declaração** .

### <a name="azure-active-directory"></a>Azure Active Directory

* Navegue até a folha de registros do aplicativo do locatário do AAD. Clique em **novo registro de aplicativo**:

![Azure Active Directory: Registros de aplicativo](ws-federation/_static/AadNewAppRegistration.png)

* Insira um nome para o registro do aplicativo. Isso não é importante para o aplicativo ASP.NET Core.
* Insira a URL na qual o aplicativo escutará como a **URL de logon**:

![Azure Active Directory: criar registro de aplicativo](ws-federation/_static/AadCreateAppRegistration.png)

* Clique em **pontos de extremidade** e anote a URL do documento de metadados de **Federação** . Este é o middleware do WS-Federation `MetadataAddress` :

![Azure Active Directory: pontos de extremidade](ws-federation/_static/AadFederationMetadataDocument.png)

* Navegue até o novo registro do aplicativo. Clique em **expor uma API**. Clique em ID do aplicativo URI **definir**  >  **salvar**. Anote o URI da **ID do aplicativo**. Este é o middleware do WS-Federation `Wtrealm` :

![Azure Active Directory: Propriedades de registro do aplicativo](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a>Usar o WS-Federation sem ASP.NET CoreIdentity

O middleware do WS-Federation pode ser usado sem o Identity . Por exemplo:
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a>Adicione o WS-Federation como um provedor de logon externo para ASP.NET CoreIdentity

* Adicione uma dependência em [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) ao projeto.
* Adicionar WS-Federation a `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>Fazer logon com o WS-Federation

Navegue até o aplicativo e clique no link **fazer logon** no cabeçalho de navegação. Há uma opção para fazer logon com WsFederation: ![ logon na página](ws-federation/_static/WsFederationButton.png)

Com o ADFS como o provedor, o botão redireciona para uma página de entrada do ADFS: ![ página de entrada do ADFS](ws-federation/_static/AdfsLoginPage.png)

Com Azure Active Directory como o provedor, o botão redireciona para uma página de entrada do AAD: página de ![ entrada do AAD](ws-federation/_static/AadSignIn.png)

Uma entrada bem-sucedida para um novo usuário redireciona para a página de registro do usuário do aplicativo: página de ![ registro](ws-federation/_static/Register.png)

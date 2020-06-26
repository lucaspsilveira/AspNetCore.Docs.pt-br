---
title: Configuração de logon externo da conta da Microsoft com o ASP.NET Core
author: rick-anderson
description: Este exemplo demonstra a integração do conta Microsoft autenticação de usuário em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/microsoft-logins
ms.openlocfilehash: df3e738880902e3005221c6047b6be9e924f2929
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406128"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Configuração de logon externo da conta da Microsoft com o ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este exemplo mostra como permitir que os usuários entrem com seus conta Microsoft pessoais, corporativos ou de estudante usando o projeto ASP.NET Core 3,0 criado na [página anterior](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Criar o aplicativo no portal do desenvolvedor da Microsoft

* Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) ao projeto.
* Navegue até a página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) e crie ou entre em uma conta Microsoft:

Se você não tiver um conta Microsoft, selecione **criar um**. Depois de entrar, você será redirecionado para a página de **registros de aplicativo** :

* Selecionar **novo registro**
* Insira um **Nome**.
* Selecione uma opção para **tipos de conta com suporte**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* Em **URI de redirecionamento**, insira a URL de desenvolvimento com `/signin-microsoft` anexado. Por exemplo, `https://localhost:5001/signin-microsoft`. O esquema de autenticação da Microsoft configurado mais adiante neste exemplo tratará automaticamente as solicitações na `/signin-microsoft` rota para implementar o fluxo OAuth.
* Escolha **Registrar**

### <a name="create-client-secret"></a>Criar segredo do cliente

* No painel esquerdo, selecione **certificados & segredos**.
* Em **segredos do cliente**, selecione **novo segredo do cliente**

  * Adicione uma descrição para o segredo do cliente.
  * Selecione o botão **Adicionar**.

* Em **segredos do cliente**, copie o valor do segredo do cliente.

O segmento URI `/signin-microsoft` é definido como o retorno de chamada padrão do provedor de autenticação da Microsoft. Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação da Microsoft por meio da propriedade herdada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .

## <a name="store-the-microsoft-client-id-and-secret"></a>Armazenar a ID e o segredo do cliente Microsoft

Armazene configurações confidenciais, como a ID do cliente da Microsoft e os valores secretos com o [Gerenciador de segredo](xref:security/app-secrets). Para este exemplo, use as seguintes etapas:

1. Inicialize o projeto para o armazenamento secreto de acordo com as instruções em [habilitar armazenamento secreto](xref:security/app-secrets#enable-secret-storage).
1. Armazene as configurações confidenciais no repositório de segredo local com as chaves secretas `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Configurar a autenticação da conta da Microsoft

Adicione o serviço de conta da Microsoft ao `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Para obter mais informações sobre as opções de configuração com suporte da autenticação da conta da Microsoft, consulte a referência da API do [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) . Isso pode ser usado para solicitar informações diferentes sobre o usuário.

## <a name="sign-in-with-microsoft-account"></a>Conta de Entrar com a conta da Microsoft

Execute o aplicativo e clique em **fazer logon**. É exibida uma opção para entrar com a Microsoft. Ao clicar em Microsoft, você será redirecionado para a Microsoft para autenticação. Depois de entrar com sua conta da Microsoft, você será solicitado a permitir que o aplicativo acesse suas informações:

Toque em **Sim** e você será Redirecionado de volta para o site da Web onde você pode definir seu email.

Agora você está conectado usando suas credenciais da Microsoft:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solução de problemas

* Se o provedor de conta da Microsoft o redireciona para uma página de erro de entrada, observe os parâmetros título e descrição da cadeia de caracteres de consulta diretamente seguindo o `#` (hashtag) no URI.

  Embora a mensagem de erro pareça indicar um problema com a autenticação da Microsoft, a causa mais comum é o URI do aplicativo não corresponder a nenhum dos **URIs de redirecionamento** especificados para a plataforma **da Web** .
* Se Identity não estiver configurado chamando `services.AddIdentity` em `ConfigureServices` , a tentativa de autenticar resultará em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*. O modelo de projeto usado neste exemplo garante que isso seja feito.
* Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você obterá *uma operação de banco de dados com falha ao processar o erro de solicitação* . Toque em **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com a Microsoft. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site no aplicativo Web do Azure, crie novos segredos de cliente no portal do desenvolvedor da Microsoft.

* Defina `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret` como configurações de aplicativo no portal do Azure. O sistema de configuração é configurado para ler chaves de variáveis de ambiente.

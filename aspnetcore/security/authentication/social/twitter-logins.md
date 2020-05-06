---
title: Configuração de entrada externa do Twitter com o ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário da conta do Twitter em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/twitter-logins
ms.openlocfilehash: c6498704214de5e805c9bf57033529d4acc5fd3e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775785"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Configuração de entrada externa do Twitter com o ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este exemplo mostra como permitir que os usuários [entrem com sua conta do Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) usando um exemplo ASP.NET Core projeto 3,0 criado na [página anterior](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Criar o aplicativo no Twitter

* Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) ao projeto.

* Navegue até [https://apps.twitter.com/](https://apps.twitter.com/) e entre. Se você ainda não tiver uma conta do Twitter, use o link **[inscrever-se agora](https://twitter.com/signup)** para criar uma.

* Selecione **criar um aplicativo**. Preencha o **nome do aplicativo**, a **Descrição do aplicativo** e o URI do **site** público (isso pode ser temporário até que você registre o nome de domínio):

* Marque a caixa ao lado de **Habilitar entrada com o Twitter**

* Microsoft. AspNetCore.Identity exige que os usuários tenham um endereço de email por padrão. Vá para a guia **permissões** , clique no botão **Editar** e marque a caixa ao lado de **solicitar endereço de email dos usuários**.

* Insira seu URI de desenvolvimento `/signin-twitter` com anexado no campo **URLs de retorno de chamada** (por `https://webapp128.azurewebsites.net/signin-twitter`exemplo:). O esquema de autenticação do Twitter configurado mais adiante neste exemplo tratará automaticamente `/signin-twitter` as solicitações na rota para implementar o fluxo OAuth.

  > [!NOTE]
  > O segmento `/signin-twitter` URI é definido como o retorno de chamada padrão do provedor de autenticação do Twitter. Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação do Twitter por meio da propriedade [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) herdada da classe [twitteroptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .

* Preencha o restante do formulário e selecione **criar**. Novos detalhes do aplicativo são exibidos:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Armazenar a chave e o segredo da API do consumidor do Twitter

Armazene configurações confidenciais, como a chave de API do consumidor do Twitter e o segredo com o [Gerenciador de segredo](xref:security/app-secrets). Para este exemplo, use as seguintes etapas:

1. Inicialize o projeto para o armazenamento secreto de acordo com as instruções em [habilitar armazenamento secreto](xref:security/app-secrets#enable-secret-storage).
1. Armazene as configurações confidenciais no repositório de segredo local com as chaves `Authentication:Twitter:ConsumerKey` de `Authentication:Twitter:ConsumerSecret`segredos e:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Esses tokens podem ser encontrados na guia **chaves e tokens de acesso** após a criação de um novo aplicativo do Twitter:

## <a name="configure-twitter-authentication"></a>Configurar a autenticação do Twitter

Adicione o serviço do Twitter no `ConfigureServices` método no arquivo *Startup.cs* :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte a referência de API do [twitteroptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) para obter mais informações sobre as opções de configuração com suporte pela autenticação do Twitter. Isso pode ser usado para solicitar informações diferentes sobre o usuário.

## <a name="sign-in-with-twitter"></a>Entrar com o Twitter

Execute o aplicativo e selecione **fazer logon**. É exibida uma opção para entrar com o Twitter:

Clicar no **Twitter** redireciona para o Twitter para autenticação:

Depois de inserir suas credenciais do Twitter, você será Redirecionado de volta para o site da Web onde você pode definir seu email.

Agora você está conectado usando suas credenciais do Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Solução de problemas

* **Somente ASP.NET Core 2. x:** Se Identity não estiver configurado chamando `services.AddIdentity` em `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*. O modelo de projeto usado neste exemplo garante que isso seja feito.
* Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você obterá *uma operação de banco de dados com falha ao processar o erro de solicitação* . Toque em **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com o Twitter. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site no aplicativo Web do Azure, você deve redefinir o `ConsumerSecret` no portal do desenvolvedor do Twitter.

* Defina `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret` como configurações de aplicativo no portal do Azure. O sistema de configuração é configurado para ler chaves de variáveis de ambiente.

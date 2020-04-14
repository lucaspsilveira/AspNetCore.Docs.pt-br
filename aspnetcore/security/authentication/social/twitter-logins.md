---
title: Configuração de login externo do Twitter com ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação do usuário da conta do Twitter em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277282"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Configuração de login externo do Twitter com ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Esta amostra mostra como permitir que os usuários [entrem com sua conta no Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) usando uma amostra ASP.NET projeto Core 3.0 criado na página [anterior](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Crie o aplicativo no Twitter

* Adicione o pacote [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet ao projeto.

* Navegue [https://apps.twitter.com/](https://apps.twitter.com/) e faça login. Se você ainda não tem uma conta no Twitter, use o link **['Cadastre-se' agora](https://twitter.com/signup)** para criar uma.

* Selecione **Criar um aplicativo**. Preencha o **nome do aplicativo,** **a descrição do aplicativo** e o URI do **site** público (isso pode ser temporário até que você registre o nome de domínio):

* Verifique a caixa ao lado **de Ativar entrar no Twitter**

* Microsoft.AspNetCore.Identity exige que os usuários tenham um endereço de e-mail por padrão. Vá para a guia **Permissões,** clique no botão **Editar** e verifique a caixa ao lado de **Solicitar endereço de e-mail dos usuários**.

* Insira seu `/signin-twitter` URI de desenvolvimento com anexo no campo `https://webapp128.azurewebsites.net/signin-twitter` **URLs de retorno** de chamada (por exemplo: ). O esquema de autenticação do Twitter configurado posteriormente nesta amostra lidará automaticamente com solicitações na `/signin-twitter` rota para implementar o fluxo OAuth.

  > [!NOTE]
  > O segmento `/signin-twitter` URI é definido como o retorno de chamada padrão do provedor de autenticação do Twitter. Você pode alterar o URI de retorno de chamada padrão enquanto configura o middleware de autenticação do Twitter através da propriedade [Herdada RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)

* Preencha o restante do formulário e selecione **Criar**. Novos detalhes do aplicativo são exibidos:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Armazene a chave e o segredo da API do consumidor do Twitter

Armazene configurações sensíveis, como a chave de API do consumidor do Twitter e secreta com [o Secret Manager](xref:security/app-secrets). Para esta amostra, use as seguintes etapas:

1. Inicialize o projeto para armazenamento secreto de acordo com as instruções em [Habilitar o armazenamento secreto](xref:security/app-secrets#enable-secret-storage).
1. Armazene as configurações sensíveis na loja `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret`secreta local com as chaves de segredos e:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Esses tokens podem ser encontrados na guia **Chaves e Tokens de Acesso** depois de criar um novo aplicativo do Twitter:

## <a name="configure-twitter-authentication"></a>Configurar a autenticação do Twitter

Adicione o serviço `ConfigureServices` do Twitter no método em *Startup.cs* arquivo:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte a referência da API [do TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) para obter mais informações sobre as opções de configuração suportadas pela autenticação do Twitter. Isso pode ser usado para solicitar diferentes informações sobre o usuário.

## <a name="sign-in-with-twitter"></a>Faça login com o Twitter

Execute o aplicativo e **selecione Fazer login**. Uma opção para fazer login no Twitter aparece:

Clicar no **Twitter** redireciona para o Twitter para autenticação:

Depois de inserir suas credenciais do Twitter, você é redirecionado de volta para o site onde você pode definir seu e-mail.

Agora você está logado usando suas credenciais do Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Solução de problemas

* **somente ASP.NET Core 2.x:** Se a identidade não estiver `services.AddIdentity` `ConfigureServices`configurada por chamada, tentar autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*. O modelo de projeto usado nesta amostra garante que isso seja feito.
* Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você terá falha da operação de banco de dados A durante o processamento do erro *de solicitação.* Toque **em Aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com o Twitter. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site no aplicativo web Do `ConsumerSecret` Azure, você deve redefinir o portal do desenvolvedor do Twitter.

* Defina `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret` as configurações do aplicativo no portal Azure. O sistema de configuração está configurado para ler chaves das variáveis do ambiente.

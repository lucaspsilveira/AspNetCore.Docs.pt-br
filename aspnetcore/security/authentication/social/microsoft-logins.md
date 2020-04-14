---
title: Configuração de login externo da Conta Microsoft com ASP.NET Core
author: rick-anderson
description: Esta amostra demonstra a integração da autenticação do usuário da conta Microsoft em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 32315267e0672b0747917228f08591a15e4449f8
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277243"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Configuração de login externo da Conta Microsoft com ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Esta amostra mostra como permitir que os usuários entrem com sua conta Microsoft usando o projeto ASP.NET Core 3.0 criado na [página anterior](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Crie o aplicativo no Microsoft Developer Portal

* Adicione o pacote [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet ao projeto.
* Navegue até o [portal Azure -](https://go.microsoft.com/fwlink/?linkid=2083908) Página de registros de aplicativos e crie ou faça login em uma conta da Microsoft:

Se você não tiver uma conta Microsoft, selecione **Criar uma**. Após fazer login, você é redirecionado para a página de registros do **Aplicativo:**

* Selecione **Novo registro**
* Digite um **nome**.
* Selecione uma opção para **tipos de conta suportados**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* Em **Redirecionar URI,** insira sua URL de desenvolvimento com `/signin-microsoft` anexo. Por exemplo, `https://localhost:5001/signin-microsoft`. O esquema de autenticação da Microsoft configurado posteriormente nesta amostra lidará automaticamente com solicitações na `/signin-microsoft` rota para implementar o fluxo OAuth.
* Selecionar **Registro**

### <a name="create-client-secret"></a>Criar segredo do cliente

* No painel esquerdo, selecione **Certificados & segredos**.
* Em **segredos do Cliente,** selecione **Novo segredo do cliente**

  * Adicione uma descrição para o segredo do cliente.
  * Selecione o botão **Adicionar.**

* Em **segredos do cliente,** copie o valor do segredo do cliente.

O segmento `/signin-microsoft` URI é definido como o retorno de chamada padrão do provedor de autenticação da Microsoft. Você pode alterar o URI de retorno de chamada padrão enquanto configura o middleware de autenticação da Microsoft através da propriedade [Herdada RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)

## <a name="store-the-microsoft-client-id-and-secret"></a>Armazene o ID do cliente da Microsoft e o segredo

Armazene configurações sensíveis, como o ID do cliente da Microsoft e valores secretos com [o Secret Manager](xref:security/app-secrets). Para esta amostra, use as seguintes etapas:

1. Inicialize o projeto para armazenamento secreto de acordo com as instruções em [Habilitar o armazenamento secreto](xref:security/app-secrets#enable-secret-storage).
1. Armazene as configurações sensíveis na loja `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret`secreta local com as chaves secretas e:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Configurar a autenticação da conta microsoft

Adicione o serviço da `Startup.ConfigureServices`Conta Microsoft ao:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Para obter mais informações sobre as opções de configuração suportadas pela autenticação da Conta Microsoft, consulte a referência da API do [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) Isso pode ser usado para solicitar diferentes informações sobre o usuário.

## <a name="sign-in-with-microsoft-account"></a>Faça login com a Conta Microsoft

Execute o aplicativo e clique **em Fazer login**. Uma opção para fazer login com a Microsoft aparece. Quando você clica na Microsoft, você é redirecionado para a Microsoft para autenticação. Depois de fazer login com sua Conta Microsoft, você será solicitado a permitir que o aplicativo acesse suas informações:

Toque **em Sim** e você será redirecionado de volta para o site onde você pode definir seu e-mail.

Agora você está logado usando suas credenciais da Microsoft:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solução de problemas

* Se o provedor da Conta Microsoft redirecionar você para uma página de erro, observe `#` os parâmetros de seqüência de consulta de erro e de descrição seguindo diretamente a (hashtag) no Uri.

  Embora a mensagem de erro pareça indicar um problema com a autenticação da Microsoft, a causa mais comum é o seu aplicativo Uri não corresponder a nenhuma das **URIs de redirecionamento** especificadas para a plataforma **Web.**
* Se a identidade não estiver `services.AddIdentity` `ConfigureServices`configurada por chamada, tentar autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*. O modelo de projeto usado nesta amostra garante que isso seja feito.
* Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você terá falha da operação de banco de dados A durante o processamento do erro *de solicitação.* Toque **em Aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com a Microsoft. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site no aplicativo web Do Azure, crie um novo cliente no Portal de Desenvolvedores da Microsoft.

* Defina `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` as configurações do aplicativo no portal Azure. O sistema de configuração está configurado para ler chaves das variáveis do ambiente.

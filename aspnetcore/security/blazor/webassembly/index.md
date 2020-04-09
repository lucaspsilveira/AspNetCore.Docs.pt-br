---
title: WebAssembly Blazor do núcleo de ASP.NET seguro
author: guardrex
description: Saiba como Blazor proteger aplicativos WebAssemlby como SPAs (Single Page Applications, aplicações de página única).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: be286d770cd8d6e5cf7885b91be8654f74ffd743
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80538979"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>WebAssembly Blazor do núcleo de ASP.NET seguro

Por [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

BlazorOs aplicativos WebAssembly são protegidos da mesma forma que os SPAs (Single Page Applications, aplicativos de página única). Existem várias abordagens para autenticar usuários para SPAs, mas a abordagem mais comum e abrangente é usar uma implementação baseada no [protocolo OAuth 2.0](https://oauth.net/), como [o Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Biblioteca de autenticação

BlazorO WebAssembly suporta autenticar e autorizar aplicativos usando `Microsoft.AspNetCore.Components.WebAssembly.Authentication` OIDC através da biblioteca. A biblioteca fornece um conjunto de primitivos para autenticação perfeita contra ASP.NET backends core. A biblioteca integra ASP.NET Identidade Central com suporte à autorização de API construído em cima do [Identity Server](https://identityserver.io/). A biblioteca pode autenticar-se contra qualquer Provedor de Identidade (IP) de terceiros que suporte o OIDC, que são chamados de Provedores OpenID (OP).

O suporte de Blazor autenticação no WebAssembly é construído em cima da biblioteca *oidc-client.js,* que é usada para lidar com os detalhes do protocolo de autenticação subjacente.

Existem outras opções para autenticar SPAs, como o uso de cookies do SameSite. No entanto, Blazor o design de engenharia do WebAssembly é estabelecido no OAuth e o OIDC como a melhor opção para autenticação em Blazor aplicativos WebAssembly. [A autenticação baseada em tokens](xref:security/anti-request-forgery#token-based-authentication) com base em [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) foi escolhida em vez [de autenticação baseada em cookies](xref:security/anti-request-forgery#cookie-based-authentication) por razões funcionais e de segurança:

* O uso de um protocolo baseado em tokens oferece uma área de superfície de ataque menor, já que os tokens não são enviados em todas as solicitações.
* Os pontos finais do servidor não exigem proteção contra [csrf (Cross-Site Request Forgery, solicitação](xref:security/anti-request-forgery) de solicitação de site) porque os tokens são enviados explicitamente. Isso permite que Blazor você hospede aplicativos do WebAssembly ao lado de aplicativos de páginas MVC ou Razor.
* Os tokens têm permissões mais estreitas que os cookies. Por exemplo, os tokens não podem ser usados para gerenciar a conta de usuário ou alterar a senha de um usuário, a menos que essa funcionalidade seja explicitamente implementada.
* Os tokens têm uma vida útil curta, uma hora por padrão, o que limita a janela de ataque. Os tokens também podem ser revogados a qualquer momento.
* JWTs autônomos oferecem garantias ao cliente e ao servidor sobre o processo de autenticação. Por exemplo, um cliente tem os meios para detectar e validar que os tokens que recebe são legítimos e foram emitidos como parte de um determinado processo de autenticação. Se um terceiro tentar trocar um token no meio do processo de autenticação, o cliente poderá detectar o token comutado e evitar usá-lo.
* Os tokens com OAuth e OIDC não dependem que o agente do usuário se comporte corretamente para garantir que o aplicativo esteja seguro.
* Protocolos baseados em tokens, como OAuth e OIDC, permitem autenticar e autorizar aplicativos hospedados e autônomos com o mesmo conjunto de características de segurança.

## <a name="authentication-process-with-oidc"></a>Processo de autenticação com OIDC

A `Microsoft.AspNetCore.Components.WebAssembly.Authentication` biblioteca oferece vários primitivos para implementar autenticação e autorização usando OIDC. Em termos gerais, a autenticação funciona da seguinte forma:

* Quando um usuário anônimo seleciona o botão [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) de login ou solicita uma página com o atributo aplicado, o usuário é redirecionado para a página de login do aplicativo (`/authentication/login`).
* Na página de login, a biblioteca de autenticação se prepara para um redirecionamento para o ponto final da autorização. O ponto final da Blazor autorização está fora do aplicativo WebAssembly e pode ser hospedado em uma origem separada. O ponto final é responsável por determinar se o usuário está autenticado e por emitir um ou mais tokens em resposta. A biblioteca de autenticação fornece um retorno de chamada de login para receber a resposta de autenticação.
  * Se o usuário não for autenticado, o usuário será redirecionado para o sistema de autenticação subjacente, que geralmente é ASP.NET Identidade Central.
  * Se o usuário já foi autenticado, o ponto final da autorização gera os tokens apropriados`/authentication/login-callback`e redireciona o navegador de volta para o ponto final de retorno de chamada de login ().
* Quando Blazor o aplicativo WebAssembly carrega o ponto`/authentication/login-callback`final de retorno de chamada de login (), a resposta de autenticação é processada.
  * Se o processo de autenticação for concluído com sucesso, o usuário será autenticado e enviado opcionalmente de volta à URL protegida original que o usuário solicitou.
  * Se o processo de autenticação falhar por qualquer motivo,`/authentication/login-failed`o usuário será enviado para a página de falha de login (), e um erro será exibido.

## <a name="support-prerendering-with-authentication"></a>Suporte pré-renderização com autenticação

Depois de seguir a orientação Blazor em um dos tópicos do aplicativo WebAssembly hospedado, use as seguintes instruções para criar um aplicativo que:

* Prerenderiza caminhos para os quais a autorização não é necessária.
* Não prerenderiza caminhos para os quais a autorização é necessária.

Na classe do `Program` aplicativo Cliente *(Program.cs),* fatorar registros comuns de `ConfigureCommonServices`serviços em um método separado (por exemplo, ):

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        services.AddBaseAddressHttpClient();
        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

No aplicativo `Startup.ConfigureServices`Server, registre os seguintes serviços adicionais:

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

No método do `Startup.Configure` aplicativo Server, substitua por: `endpoints.MapFallbackToFile("index.html")` `endpoints.MapFallbackToPage("/_Host")`

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

No aplicativo Servidor, crie uma pasta *Páginas* se ela não existir. Crie uma página *_Host.cshtml* dentro da pasta *Páginas* do aplicativo Servidor. Cole o conteúdo do arquivo *wwwroot/index.html* do aplicativo Cliente no arquivo *Páginas/_Host.cshtml.* Atualize o conteúdo do arquivo:

* Adicione `@page "_Host"` ao topo do arquivo.
* Substitua `<app>Loading...</app>` a tag pelo seguinte:

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Opções para aplicativos hospedados e provedores de login de terceiros

Ao autenticar e autorizar um Blazor aplicativo WebAssembly hospedado com um provedor de terceiros, existem várias opções disponíveis para autenticar o usuário. Qual você escolhe depende do seu cenário.

Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Autenticar usuários para chamar apenas APIs de terceiros protegidos

Autenticar o usuário com um fluxo OAuth do lado do cliente contra o provedor de API de terceiros:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 Neste cenário:

* O servidor que hospeda o aplicativo não desempenha um papel.
* As APIs no servidor não podem ser protegidas.
* O aplicativo só pode chamar APIs de terceiros protegidos.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Autenticar usuários com um provedor de terceiros e chamar APIs protegidas no servidor host e em terceiros

Configurar identidade com um provedor de login de terceiros. Obtenha os tokens necessários para acesso à API de terceiros e armazene-os.

Quando um usuário faz login, a Identidade coleta tokens de acesso e atualização como parte do processo de autenticação. Nesse ponto, existem algumas abordagens disponíveis para fazer chamadas de API para APIs de terceiros.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Use um token de acesso ao servidor para recuperar o token de acesso de terceiros

Use o token de acesso gerado no servidor para recuperar o token de acesso de terceiros a partir de um ponto final da API do servidor. A partir daí, use o token de acesso de terceiros para chamar recursos de API de terceiros diretamente da Identidade no cliente.

Não recomendamos essa abordagem. Essa abordagem requer o tratamento do token de acesso de terceiros como se fosse gerado para um cliente público. Em termos OAuth, o aplicativo público não tem um segredo de cliente porque não é confiável para armazenar segredos com segurança, e o token de acesso é produzido para um cliente confidencial. Um cliente confidencial é um cliente que tem um segredo de cliente e é assumido ser capaz de armazenar segredos com segurança.

* O token de acesso de terceiros pode receber escopos adicionais para realizar operações sensíveis com base no fato de que o terceiro emitiu o token para um cliente mais confiável.
* Da mesma forma, os tokens de atualização não devem ser emitidos a um cliente que não é confiável, pois isso dá ao cliente acesso ilimitado, a menos que outras restrições sejam colocadas em prática.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Faça chamadas de API do cliente para a API do servidor, a fim de chamar APIs de terceiros

Faça uma chamada de API do cliente para a API do servidor. A partir do servidor, recupere o token de acesso para o recurso de API de terceiros e emita qualquer chamada necessária.

Embora essa abordagem exija um salto de rede extra através do servidor para chamar uma API de terceiros, ela resulta em uma experiência mais segura:

* O servidor pode armazenar tokens de atualização e garantir que o aplicativo não perca o acesso a recursos de terceiros.
* O aplicativo não pode vazar tokens de acesso do servidor que possam conter permissões mais sensíveis.

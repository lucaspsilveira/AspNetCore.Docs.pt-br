---
title: Webassembly Blazor ASP.NET Core seguro
author: guardrex
description: Saiba como proteger Blazor aplicativos WebAssemlby como spas (aplicativos de página única).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: e8ea5e6b6d7e28906e6109e6730ac25f190b4191
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767994"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a>Webassembly Blazor ASP.NET Core seguro

Por [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

BlazorOs aplicativos Webassembly são protegidos da mesma maneira que os aplicativos de página única (SPAs). Há várias abordagens para autenticar usuários no SPAs, mas a abordagem mais comum e abrangente é usar uma implementação baseada no [protocolo OAuth 2,0](https://oauth.net/), como o [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Biblioteca de autenticação

BlazorO Webassembly dá suporte à autenticação e à autorização de aplicativos `Microsoft.AspNetCore.Components.WebAssembly.Authentication` usando o OIDC por meio da biblioteca. A biblioteca fornece um conjunto de primitivos para autenticação direta contra back-ends ASP.NET Core. A biblioteca integra ASP.NET Core Identity com suporte à autorização de API criado na parte superior do [ Identity servidor](https://identityserver.io/). A biblioteca pode se autenticar em qualquer provedor Identity de terceiros (IP) que ofereça suporte a OIDC, que são chamadas de provedores de OPENID (op).

O suporte de autenticação Blazor no Webassembly é criado sobre a biblioteca *oidc-Client. js* , que é usada para lidar com os detalhes do protocolo de autenticação subjacente.

Existem outras opções para autenticar SPAs, como o uso de cookies SameSite. No entanto, o design Blazor de engenharia do Webassembly é liquidado em OAuth e OIDC como a melhor Blazor opção para autenticação em aplicativos Webassembly. [A autenticação baseada em token](xref:security/anti-request-forgery#token-based-authentication) baseada em [tokens Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) foi escolhida pela [autenticação baseada em cookie](xref:security/anti-request-forgery#cookie-based-authentication) para motivos funcionais e de segurança:

* O uso de um protocolo baseado em token oferece uma área de superfície de ataque menor, pois os tokens não são enviados em todas as solicitações.
* Os pontos de extremidade do servidor não exigem proteção contra [falsificação de solicitação entre sites (CSRF)](xref:security/anti-request-forgery) porque os tokens são enviados explicitamente. Isso permite que você hospede Blazor aplicativos Webassembly juntamente com aplicativos Razor MVC ou páginas.
* Tokens têm permissões mais estreitas do que cookies. Por exemplo, os tokens não podem ser usados para gerenciar a conta de usuário ou alterar a senha de um usuário, a menos que essa funcionalidade seja explicitamente implementada.
* Os tokens têm um tempo de vida curto, uma hora por padrão, o que limita a janela de ataque. Os tokens também podem ser revogados a qualquer momento.
* As JWTs independentes oferecem garantias ao cliente e ao servidor sobre o processo de autenticação. Por exemplo, um cliente tem os meios para detectar e validar que os tokens recebidos são legítimos e foram emitidos como parte de um determinado processo de autenticação. Se um terceiro tentar alternar um token no meio do processo de autenticação, o cliente poderá detectar o token comutado e evitar usá-lo.
* Tokens com OAuth e OIDC não dependem do agente do usuário se comportarem corretamente para garantir que o aplicativo seja seguro.
* Protocolos baseados em token, como OAuth e OIDC, permitem autenticar e autorizar aplicativos hospedados e autônomos com o mesmo conjunto de características de segurança.

## <a name="authentication-process-with-oidc"></a>Processo de autenticação com OIDC

A `Microsoft.AspNetCore.Components.WebAssembly.Authentication` biblioteca oferece vários primitivos para implementar a autenticação e a autorização usando o OIDC. Em termos gerais, a autenticação funciona da seguinte maneira:

* Quando um usuário anônimo seleciona o botão de logon ou solicita uma página com [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) o atributo aplicado, o usuário é redirecionado para a página de logon do`/authentication/login`aplicativo ().
* Na página de logon, a biblioteca de autenticação se prepara para um redirecionamento para o ponto de extremidade de autorização. O ponto de extremidade de autorização está Blazor fora do aplicativo Webassembly e pode ser hospedado em uma origem separada. O ponto de extremidade é responsável por determinar se o usuário está autenticado e para emitir um ou mais tokens em resposta. A biblioteca de autenticação fornece um retorno de chamada de logon para receber a resposta de autenticação.
  * Se o usuário não estiver autenticado, o usuário será redirecionado para o sistema de autenticação subjacente, que geralmente Identityé ASP.NET Core.
  * Se o usuário já foi autenticado, o ponto de extremidade de autorização gera os tokens apropriados e redireciona o navegador de volta para o ponto`/authentication/login-callback`de extremidade de retorno de chamada de logon ().
* Quando o Blazor aplicativo Webassembly carrega o ponto de extremidade de`/authentication/login-callback`retorno de chamada de logon (), a resposta de autenticação é processada.
  * Se o processo de autenticação for concluído com êxito, o usuário será autenticado e, opcionalmente, enviado de volta para a URL protegida original solicitada pelo usuário.
  * Se o processo de autenticação falhar por algum motivo, o usuário será enviado à página de falha de`/authentication/login-failed`logon () e um erro será exibido.

## <a name="additional-resources"></a>Recursos adicionais

* Os artigos nesta *visão geral* fornecem informações sobre como autenticar usuários em Blazor aplicativos Webassembly em relação a provedores específicos.
* <xref:security/blazor/webassembly/additional-scenarios>

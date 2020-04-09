---
title: Visão geral da Autenticação do Núcleo ASP.NET
author: mjrousos
description: Saiba mais sobre autenticação no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
uid: security/authentication/index
ms.openlocfilehash: 404904ecfa30d1fe7e47f0daaa423ddd6f1b06e8
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434324"
---
# <a name="overview-of-aspnet-core-authentication"></a>Visão geral da autenticação ASP.NET Core

Por [Mike Rousos](https://github.com/mjrousos)

Autenticação é o processo de determinação da identidade de um usuário. [Autorização](xref:security/authorization/introduction) é o processo de determinar se um usuário tem acesso a um recurso. Em ASP.NET Core, a autenticação `IAuthenticationService`é manuseada pelo , que é usado pelo [middleware](xref:fundamentals/middleware/index)de autenticação . O serviço de autenticação usa manipuladores de autenticação registrados para concluir ações relacionadas à autenticação. Exemplos de ações relacionadas à autenticação incluem:

* Autenticando um usuário.
* Respondendo quando um usuário não autenticado tenta acessar um recurso restrito.

Os manipuladores de autenticação registrados e suas opções de configuração são chamados de "esquemas".

Os esquemas de autenticação são especificados registrando serviços de autenticação em `Startup.ConfigureServices`:

* Ao chamar um método de extensão `services.AddAuthentication` específico `AddJwtBearer` do `AddCookie`esquema após uma chamada para (como ou , por exemplo). Esses métodos de extensão usam [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) para registrar esquemas com configurações apropriadas.
* Menos comumente, chamando [AutenticaçãoBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) diretamente.

Por exemplo, o código a seguir registra serviços de autenticação e manipuladores para esquemas de autenticação de cookies e JWT:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

O `AddAuthentication` parâmetro `JwtBearerDefaults.AuthenticationScheme` é o nome do esquema a ser usado por padrão quando um esquema específico não é solicitado.

Se vários esquemas forem usados, as políticas de autorização (ou atributos de autorização) podem [especificar o esquema de autenticação (ou esquemas)](xref:security/authorization/limitingidentitybyscheme) de que dependem para autenticar o usuário. No exemplo acima, o esquema de autenticação de cookies`CookieAuthenticationDefaults.AuthenticationScheme` poderia ser usado especificando seu nome `AddCookie`( por padrão, embora um nome diferente pudesse ser fornecido ao ligar ).

Em alguns casos, `AddAuthentication` a chamada é feita automaticamente por outros métodos de extensão. Por exemplo, ao usar `AddAuthentication` ASP.NET Identidade [Central,](xref:security/authentication/identity)é chamado internamente.

O middleware Autenticação `Startup.Configure` é adicionado <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> chamando o método `IApplicationBuilder`de extensão no aplicativo . A `UseAuthentication` chamada registra o middleware que usa os esquemas de autenticação previamente registrados. Ligue `UseAuthentication` antes de qualquer middleware que dependa da autenticação dos usuários. Ao usar o roteamento `UseAuthentication` de ponto final, a chamada deve ser:

* Depois `UseRouting`, para que as informações de rota estejam disponíveis para decisões de autenticação.
* Antes `UseEndpoints`, para que os usuários sejam autenticados antes de acessar os pontos finais.

## <a name="authentication-concepts"></a>Conceitos de autenticação

### <a name="authentication-scheme"></a>Esquema de autenticação

Um esquema de autenticação é um nome que corresponde a:

* Um manipulador de autenticação.
* Opções para configurar essa instância específica do manipulador.

Os esquemas são úteis como um mecanismo para se referir à autenticação, desafio e proibição de comportamentos do manipulador associado. Por exemplo, uma política de autorização pode usar nomes de esquema para especificar qual esquema de autenticação (ou esquemas) deve ser usado para autenticar o usuário. Ao configurar a autenticação, é comum especificar o esquema de autenticação padrão. O esquema padrão é usado a menos que um recurso solicite um esquema específico. Também é possível:

* Especifique diferentes esquemas padrão para usar para autenticar, desafiar e proibir ações.
* Combine vários esquemas em um usando [esquemas de políticas.](xref:security/authentication/policyschemes)

### <a name="authentication-handler"></a>Manipulador de autenticação

Um manipulador de autenticação:

* É um tipo que implementa o comportamento de um esquema.
* É derivado <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> ou <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.
* A responsabilidade é exclusiva de autenticar os usuários.

Com base na configuração do esquema de autenticação e no contexto de solicitação recebida, os manipuladores de autenticação:

* Construa <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objetos representando a identidade do usuário se a autenticação for bem sucedida.
* Retornar 'sem resultado' ou 'falha' se a autenticação não for bem sucedida.
* Tenha métodos para desafiar e proibir ações para quando os usuários tentarem acessar recursos:
  * Eles não estão autorizados a acessar (proibido).
  * Quando eles não são autenticados (desafio).

### <a name="authenticate"></a>Authenticate

A ação autenticada de um esquema de autenticação é responsável pela construção da identidade do usuário com base no contexto da solicitação. Ele retorna <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> uma indicação de se a autenticação foi bem sucedida e, se for o caso, a identidade do usuário em um bilhete de autenticação. Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>. Exemplos autenticados incluem:

* Um esquema de autenticação de cookies que constrói a identidade do usuário a partir de cookies.
* Um esquema de portador JWT desserializando e validando um token do portador JWT para construir a identidade do usuário.

### <a name="challenge"></a>Desafio

Um desafio de autenticação é invocado pela Autorização quando um usuário não autenticado solicita um ponto final que requer autenticação. Um desafio de autenticação é emitido, por exemplo, quando um usuário anônimo solicita um recurso restrito ou clica em um link de login. A autorização invoca um desafio usando o esquema de autenticação especificado ou o padrão se nenhum for especificado. Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>. Exemplos de desafios de autenticação incluem:

* Um esquema de autenticação de cookies redirecionando o usuário para uma página de login.
* Um esquema de portador JWT devolvendo `www-authenticate: bearer` um resultado de 401 com um cabeçalho.

Uma ação de desafio deve permitir que o usuário saiba qual mecanismo de autenticação usar para acessar o recurso solicitado.

### <a name="forbid"></a>Proibir

A ação proibida de um esquema de autenticação é chamada pela Autorização quando um usuário autenticado tenta acessar um recurso que não tem permissão para acessar. Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>. Os exemplos de autenticação que proíbem incluem:
* Um esquema de autenticação de cookies redirecionando o usuário para uma página indicando acesso foi proibido.
* Um esquema de portador JWT devolvendo um resultado de 403.
* Um esquema de autenticação personalizado redirecionando para uma página onde o usuário pode solicitar acesso ao recurso.

Uma ação proibida pode deixar o usuário saber:

* Eles são autenticados.
* Eles não têm permissão para acessar o recurso solicitado.

Veja os links a seguir para diferenças entre desafio e proibição:

* [Desafie e proíba com um manipulador de recursos operacional.](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler)
* [Diferenças entre desafio e proibição.](xref:security/authorization/secure-data#challenge)

## <a name="authentication-providers-per-tenant"></a>Provedores de autenticação por inquilino

ASP.NET framework Core não possui uma solução incorporada para autenticação de vários inquilinos.
Embora seja certamente possível que os clientes escrevam um, usando os recursos incorporados, recomendamos que os clientes olhem para [o Orchard Core](https://www.orchardcore.net/) para este fim.

Orchard Core é:

* Uma estrutura de aplicativos modulares e multilocatários de código aberto construída com ASP.NET Core.
* Um sistema de gerenciamento de conteúdo (CMS) construído em cima dessa estrutura de aplicativos.

Consulte a fonte [do Orchard Core](https://github.com/OrchardCMS/OrchardCore) para obter um exemplo de provedores de autenticação por inquilino.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>

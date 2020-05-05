---
title: Impedir ataques de redirecionamento abertos no ASP.NET Core
author: ardalis
description: Mostra como evitar ataques de redirecionamento abertos em um aplicativo ASP.NET Core
ms.author: riande
ms.date: 07/07/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/preventing-open-redirects
ms.openlocfilehash: ad4c9806146567b6ef1f5e78eaeca96cb649c1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774386"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>Impedir ataques de redirecionamento abertos no ASP.NET Core

Um aplicativo Web que redireciona para uma URL especificada por meio da solicitação, como a QueryString ou os dados de formulário, pode ser adulterado para redirecionar os usuários para uma URL externa e mal-intencionada. Essa violação é chamada de ataque de redirecionamento aberto.

Sempre que a lógica do aplicativo redireciona para uma URL especificada, você deve verificar se a URL de redirecionamento não foi violada. ASP.NET Core tem funcionalidade interna para ajudar a proteger aplicativos contra ataques de redirecionamento aberto (também conhecidos como redirecionamento aberto).

## <a name="what-is-an-open-redirect-attack"></a>O que é um ataque de redirecionamento aberto?

Os aplicativos Web geralmente redirecionam os usuários para uma página de logon quando acessam recursos que exigem autenticação. O redirecionamento normalmente inclui um `returnUrl` parâmetro QueryString para que o usuário possa ser retornado para a URL solicitada originalmente após ter feito logon com êxito. Depois que o usuário é autenticado, ele é redirecionado para a URL que ele solicitou originalmente.

Como a URL de destino é especificada na QueryString da solicitação, um usuário mal-intencionado pode adulterar a QueryString. Uma QueryString violada pode permitir que o site redirecione o usuário para um site externo e mal-intencionado. Essa técnica é chamada de ataque de redirecionamento aberto (ou redirecionamento).

### <a name="an-example-attack"></a>Um ataque de exemplo

Um usuário mal-intencionado pode desenvolver um ataque destinado a permitir que o usuário mal-intencionado acesse as credenciais de um usuário ou informações confidenciais. Para iniciar o ataque, o usuário mal-intencionado convencer o usuário a clicar em um link para a página de logon do site com `returnUrl` um valor de QueryString adicionado à URL. Por exemplo, considere um aplicativo no `contoso.com` que inclui uma página de logon `http://contoso.com/Account/LogOn?returnUrl=/Home/About`em. O ataque segue estas etapas:

1. O usuário clica em um link mal-intencionado `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` para (a segunda URL é "Contoso**1**. com", não "contoso.com").
2. O usuário faz logon com êxito.
3. O usuário é redirecionado (pelo site) para `http://contoso1.com/Account/LogOn` (um site mal-intencionado que é exatamente como um site real).
4. O usuário faz logon novamente (concedendo ao site mal-intencionado suas credenciais) e é Redirecionado de volta para o site real.

O usuário provavelmente acredita que a primeira tentativa de fazer logon falhou e que a segunda tentativa é bem-sucedida. O usuário provavelmente ainda não sabe que suas credenciais estão comprometidas.

![Abrir processo de ataque de redirecionamento](preventing-open-redirects/_static/open-redirection-attack-process.png)

Além das páginas de logon, alguns sites fornecem páginas de redirecionamento ou pontos de extremidade. Imagine que seu aplicativo tenha uma página com um redirecionamento aberto `/Home/Redirect`. Um invasor pode criar, por exemplo, um link em um email que vá para `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`. Um usuário típico irá examinar a URL e vê-la começar com o nome do site. Confiando nele, eles clicarão no link. O redirecionamento aberto, em seguida, enviaria o usuário para o site de phishing, que parece ser idêntico ao seu, e o usuário provavelmente fazer logon no que eles acreditam ser seu site.

## <a name="protecting-against-open-redirect-attacks"></a>Protegendo contra ataques de redirecionamento abertos

Ao desenvolver aplicativos Web, trate todos os dados fornecidos pelo usuário como não confiáveis. Se seu aplicativo tiver funcionalidade que redireciona o usuário com base no conteúdo da URL, verifique se esses redirecionamentos só são feitos localmente no seu aplicativo (ou em uma URL conhecida, e não qualquer URL que possa ser fornecida na QueryString).

### <a name="localredirect"></a>LocalRedirect

Use o `LocalRedirect` método auxiliar da classe base `Controller` :

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect`gerará uma exceção se uma URL não local for especificada. Caso contrário, ele se comporta exatamente como o `Redirect` método.

### <a name="islocalurl"></a>IsLocalUrl

Use o método [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) para testar URLs antes de redirecionar:

O exemplo a seguir mostra como verificar se uma URL é local antes do redirecionamento.

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

O `IsLocalUrl` método protege os usuários de serem redirecionados inadvertidamente para um site mal-intencionado. Você pode registrar em log os detalhes da URL que foi fornecida quando uma URL não local é fornecida em uma situação em que você esperava uma URL local. As URLs de redirecionamento de log podem ajudar no diagnóstico de ataques de redirecionamento.

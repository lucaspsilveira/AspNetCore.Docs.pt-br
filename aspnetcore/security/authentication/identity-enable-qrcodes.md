---
title: Habilitar geração de código QR para aplicativos do autenticador TOTP no ASP.NET Core
author: rick-anderson
description: Descubra como habilitar a geração de código QR para aplicativos do autenticador TOTP que funcionam com ASP.NET Core autenticação de dois fatores.
ms.author: riande
ms.date: 08/14/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: 42ddddeaa329ac5ff5b2b40cbf9ebffa68f6d4cf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774425"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>Habilitar geração de código QR para aplicativos do autenticador TOTP no ASP.NET Core

::: moniker range="<= aspnetcore-2.0"

Os códigos QR requerem ASP.NET Core 2,0 ou posterior.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

O ASP.NET Core é fornecido com suporte para aplicativos autenticadores para autenticação individual. Os aplicativos autenticadores de autenticação de dois fatores (2FA), usando um algoritmo de senha única (TOTP) baseado em tempo, são a abordagem recomendada do setor para 2FA. 2FA usando TOTP é preferível ao SMS 2FA. Um aplicativo autenticador fornece um código de 6 a 8 dígitos que os usuários devem inserir depois de confirmar seu nome de usuário e senha. Normalmente, um aplicativo autenticador é instalado em um smartphone.

Os modelos de aplicativo Web do ASP.NET Core dão suporte a autenticadores, mas não fornecem suporte para geração de QRCode. Os geradores de QRCode facilitam a configuração do 2FA. Este documento orientará você pela adição da geração de [código QR](https://wikipedia.org/wiki/QR_code) à página de configuração do 2FA.

A autenticação de dois fatores não acontece usando um provedor de autenticação externo, como o [Google](xref:security/authentication/google-logins) ou o [Facebook](xref:security/authentication/facebook-logins). Os logons externos são protegidos por qualquer mecanismo fornecido pelo provedor de logon externo. Considere, por exemplo, que o provedor de autenticação da [Microsoft](xref:security/authentication/microsoft-logins) requer uma chave de hardware ou outra abordagem de 2FA. Se os modelos padrão impuserem a 2FA "local", os usuários precisarão atender a duas abordagens de 2FA, que não é um cenário comumente usado.

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>Adicionando códigos QR à página de configuração do 2FA

Essas instruções usam o https://davidshimjs.github.io/qrcodejs/ *qrcode. js* do repositório.

* Baixe a [biblioteca JavaScript qrcode. js](https://davidshimjs.github.io/qrcodejs/) para a `wwwroot\lib` pasta em seu projeto.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* Siga as instruções em [Scaffold Identity ](xref:security/authentication/scaffold-identity) para gerar */areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.
* Em */areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, localize a `Scripts` seção no final do arquivo:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Em *páginas/conta/gerenciar/EnableAuthenticator. cshtml* (Razor páginas) ou *views/Manage/EnableAuthenticator. cshtml* (MVC), localize `Scripts` a seção no final do arquivo:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* Atualize a `Scripts` seção para adicionar uma referência à `qrcodejs` biblioteca que você adicionou e uma chamada para gerar o código QR. Ele deve ter a seguinte aparência:

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* Exclua o parágrafo que vincula você a estas instruções.

Execute seu aplicativo e verifique se você pode digitalizar o código QR e validar o código que o autenticador comprova.

## <a name="change-the-site-name-in-the-qr-code"></a>Alterar o nome do site no código QR

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

O nome do site no código QR é extraído do nome do projeto que você escolher ao criar inicialmente seu projeto. Você pode alterá-lo procurando o `GenerateQrCodeUri(string email, string unformattedKey)` método no */areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

O nome do site no código QR é extraído do nome do projeto que você escolher ao criar inicialmente seu projeto. Você pode alterá-lo procurando o `GenerateQrCodeUri(string email, string unformattedKey)` método no arquivo *pages/Account/Manage/EnableAuthenticator. cshtml. cs* (Razor páginas) ou no arquivo *Controllers/ManageController. cs* (MVC).

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

O código padrão do modelo tem a seguinte aparência:

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

O segundo parâmetro na chamada para `string.Format` é o nome do seu site, extraído do nome da solução. Ele pode ser alterado para qualquer valor, mas deve sempre ser codificado por URL.

## <a name="using-a-different-qr-code-library"></a>Usando uma biblioteca de código QR diferente

Você pode substituir a biblioteca de códigos QR pela sua biblioteca preferida. O HTML contém um `qrCode` elemento no qual você pode posicionar um código QR por qualquer mecanismo que sua biblioteca forneça.

A URL formatada corretamente para o código QR está disponível no:

* `AuthenticatorUri`Propriedade do modelo.
* `data-url`Propriedade no `qrCodeData` elemento.

## <a name="totp-client-and-server-time-skew"></a>Distorção de tempo de cliente e servidor TOTP

A autenticação do TOTP (senha de uso único baseada em tempo) depende do servidor e do dispositivo autenticador ter um tempo preciso. Somente tokens por último 30 segundos. Se os logons TOTP 2FA estiverem falhando, verifique se o tempo do servidor é preciso e, preferencialmente, sincronizado com um serviço NTP preciso.

::: moniker-end

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
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="fcee8-103">Habilitar geração de código QR para aplicativos do autenticador TOTP no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fcee8-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="fcee8-104">Os códigos QR requerem ASP.NET Core 2,0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="fcee8-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="fcee8-105">O ASP.NET Core é fornecido com suporte para aplicativos autenticadores para autenticação individual.</span><span class="sxs-lookup"><span data-stu-id="fcee8-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="fcee8-106">Os aplicativos autenticadores de autenticação de dois fatores (2FA), usando um algoritmo de senha única (TOTP) baseado em tempo, são a abordagem recomendada do setor para 2FA.</span><span class="sxs-lookup"><span data-stu-id="fcee8-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="fcee8-107">2FA usando TOTP é preferível ao SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="fcee8-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="fcee8-108">Um aplicativo autenticador fornece um código de 6 a 8 dígitos que os usuários devem inserir depois de confirmar seu nome de usuário e senha.</span><span class="sxs-lookup"><span data-stu-id="fcee8-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="fcee8-109">Normalmente, um aplicativo autenticador é instalado em um smartphone.</span><span class="sxs-lookup"><span data-stu-id="fcee8-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="fcee8-110">Os modelos de aplicativo Web do ASP.NET Core dão suporte a autenticadores, mas não fornecem suporte para geração de QRCode.</span><span class="sxs-lookup"><span data-stu-id="fcee8-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="fcee8-111">Os geradores de QRCode facilitam a configuração do 2FA.</span><span class="sxs-lookup"><span data-stu-id="fcee8-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="fcee8-112">Este documento orientará você pela adição da geração de [código QR](https://wikipedia.org/wiki/QR_code) à página de configuração do 2FA.</span><span class="sxs-lookup"><span data-stu-id="fcee8-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="fcee8-113">A autenticação de dois fatores não acontece usando um provedor de autenticação externo, como o [Google](xref:security/authentication/google-logins) ou o [Facebook](xref:security/authentication/facebook-logins).</span><span class="sxs-lookup"><span data-stu-id="fcee8-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="fcee8-114">Os logons externos são protegidos por qualquer mecanismo fornecido pelo provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="fcee8-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="fcee8-115">Considere, por exemplo, que o provedor de autenticação da [Microsoft](xref:security/authentication/microsoft-logins) requer uma chave de hardware ou outra abordagem de 2FA.</span><span class="sxs-lookup"><span data-stu-id="fcee8-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="fcee8-116">Se os modelos padrão impuserem a 2FA "local", os usuários precisarão atender a duas abordagens de 2FA, que não é um cenário comumente usado.</span><span class="sxs-lookup"><span data-stu-id="fcee8-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="fcee8-117">Adicionando códigos QR à página de configuração do 2FA</span><span class="sxs-lookup"><span data-stu-id="fcee8-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="fcee8-118">Essas instruções usam o https://davidshimjs.github.io/qrcodejs/ *qrcode. js* do repositório.</span><span class="sxs-lookup"><span data-stu-id="fcee8-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="fcee8-119">Baixe a [biblioteca JavaScript qrcode. js](https://davidshimjs.github.io/qrcodejs/) para a `wwwroot\lib` pasta em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="fcee8-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="fcee8-120">Siga as instruções em [Scaffold Identity ](xref:security/authentication/scaffold-identity) para gerar */areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fcee8-120">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>
* <span data-ttu-id="fcee8-121">Em */areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, localize a `Scripts` seção no final do arquivo:</span><span class="sxs-lookup"><span data-stu-id="fcee8-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="fcee8-122">Em *páginas/conta/gerenciar/EnableAuthenticator. cshtml* (Razor páginas) ou *views/Manage/EnableAuthenticator. cshtml* (MVC), localize `Scripts` a seção no final do arquivo:</span><span class="sxs-lookup"><span data-stu-id="fcee8-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="fcee8-123">Atualize a `Scripts` seção para adicionar uma referência à `qrcodejs` biblioteca que você adicionou e uma chamada para gerar o código QR.</span><span class="sxs-lookup"><span data-stu-id="fcee8-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="fcee8-124">Ele deve ter a seguinte aparência:</span><span class="sxs-lookup"><span data-stu-id="fcee8-124">It should look as follows:</span></span>

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

* <span data-ttu-id="fcee8-125">Exclua o parágrafo que vincula você a estas instruções.</span><span class="sxs-lookup"><span data-stu-id="fcee8-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="fcee8-126">Execute seu aplicativo e verifique se você pode digitalizar o código QR e validar o código que o autenticador comprova.</span><span class="sxs-lookup"><span data-stu-id="fcee8-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="fcee8-127">Alterar o nome do site no código QR</span><span class="sxs-lookup"><span data-stu-id="fcee8-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="fcee8-128">O nome do site no código QR é extraído do nome do projeto que você escolher ao criar inicialmente seu projeto.</span><span class="sxs-lookup"><span data-stu-id="fcee8-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="fcee8-129">Você pode alterá-lo procurando o `GenerateQrCodeUri(string email, string unformattedKey)` método no */areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="fcee8-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="fcee8-130">O nome do site no código QR é extraído do nome do projeto que você escolher ao criar inicialmente seu projeto.</span><span class="sxs-lookup"><span data-stu-id="fcee8-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="fcee8-131">Você pode alterá-lo procurando o `GenerateQrCodeUri(string email, string unformattedKey)` método no arquivo *pages/Account/Manage/EnableAuthenticator. cshtml. cs* (Razor páginas) ou no arquivo *Controllers/ManageController. cs* (MVC).</span><span class="sxs-lookup"><span data-stu-id="fcee8-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="fcee8-132">O código padrão do modelo tem a seguinte aparência:</span><span class="sxs-lookup"><span data-stu-id="fcee8-132">The default code from the template looks as follows:</span></span>

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

<span data-ttu-id="fcee8-133">O segundo parâmetro na chamada para `string.Format` é o nome do seu site, extraído do nome da solução.</span><span class="sxs-lookup"><span data-stu-id="fcee8-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="fcee8-134">Ele pode ser alterado para qualquer valor, mas deve sempre ser codificado por URL.</span><span class="sxs-lookup"><span data-stu-id="fcee8-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="fcee8-135">Usando uma biblioteca de código QR diferente</span><span class="sxs-lookup"><span data-stu-id="fcee8-135">Using a different QR Code library</span></span>

<span data-ttu-id="fcee8-136">Você pode substituir a biblioteca de códigos QR pela sua biblioteca preferida.</span><span class="sxs-lookup"><span data-stu-id="fcee8-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="fcee8-137">O HTML contém um `qrCode` elemento no qual você pode posicionar um código QR por qualquer mecanismo que sua biblioteca forneça.</span><span class="sxs-lookup"><span data-stu-id="fcee8-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="fcee8-138">A URL formatada corretamente para o código QR está disponível no:</span><span class="sxs-lookup"><span data-stu-id="fcee8-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="fcee8-139">`AuthenticatorUri`Propriedade do modelo.</span><span class="sxs-lookup"><span data-stu-id="fcee8-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="fcee8-140">`data-url`Propriedade no `qrCodeData` elemento.</span><span class="sxs-lookup"><span data-stu-id="fcee8-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="fcee8-141">Distorção de tempo de cliente e servidor TOTP</span><span class="sxs-lookup"><span data-stu-id="fcee8-141">TOTP client and server time skew</span></span>

<span data-ttu-id="fcee8-142">A autenticação do TOTP (senha de uso único baseada em tempo) depende do servidor e do dispositivo autenticador ter um tempo preciso.</span><span class="sxs-lookup"><span data-stu-id="fcee8-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="fcee8-143">Somente tokens por último 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="fcee8-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="fcee8-144">Se os logons TOTP 2FA estiverem falhando, verifique se o tempo do servidor é preciso e, preferencialmente, sincronizado com um serviço NTP preciso.</span><span class="sxs-lookup"><span data-stu-id="fcee8-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end

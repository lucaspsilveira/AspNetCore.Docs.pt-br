---
title: Esquemas de política no ASP.NET Core
author: rick-anderson
description: Os esquemas de política de autenticação facilitam a tarefa de ter um único esquema de autenticação lógica
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/policyschemes
ms.openlocfilehash: a8bde9633f06f41ebcb55480eb2322544db4b4da
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408754"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="a6397-103">Esquemas de política no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6397-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="a6397-104">Os esquemas de diretiva de autenticação facilitam a utilização de um único esquema de autenticação lógica, o que pode usar várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="a6397-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="a6397-105">Por exemplo, um esquema de política pode usar a autenticação do Google para desafios e a autenticação de cookie para todo o resto.</span><span class="sxs-lookup"><span data-stu-id="a6397-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="a6397-106">Os esquemas de política de autenticação o fazem:</span><span class="sxs-lookup"><span data-stu-id="a6397-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="a6397-107">É fácil encaminhar qualquer ação de autenticação para outro esquema.</span><span class="sxs-lookup"><span data-stu-id="a6397-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="a6397-108">Encaminhe dinamicamente com base na solicitação.</span><span class="sxs-lookup"><span data-stu-id="a6397-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="a6397-109">Todos os esquemas de autenticação que usam derivado <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> e [o \<TOptions> AuthenticationHandler](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)associado:</span><span class="sxs-lookup"><span data-stu-id="a6397-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="a6397-110">São esquemas de política automaticamente no ASP.NET Core 2,1 e posterior.</span><span class="sxs-lookup"><span data-stu-id="a6397-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="a6397-111">Pode ser habilitado por meio da configuração das opções do esquema.</span><span class="sxs-lookup"><span data-stu-id="a6397-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="a6397-112">Exemplos</span><span class="sxs-lookup"><span data-stu-id="a6397-112">Examples</span></span>

<span data-ttu-id="a6397-113">O exemplo a seguir mostra um esquema de nível superior que combina esquemas de nível inferior.</span><span class="sxs-lookup"><span data-stu-id="a6397-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="a6397-114">A autenticação do Google é usada para desafios e a autenticação de cookie é usada para todo o resto:</span><span class="sxs-lookup"><span data-stu-id="a6397-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="a6397-115">O exemplo a seguir habilita a seleção dinâmica de esquemas de acordo com a solicitação.</span><span class="sxs-lookup"><span data-stu-id="a6397-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="a6397-116">Ou seja, como misturar cookies e autenticação de API:</span><span class="sxs-lookup"><span data-stu-id="a6397-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]

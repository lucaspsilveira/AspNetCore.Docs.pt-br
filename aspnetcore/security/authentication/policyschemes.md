---
title: Esquemas de política no ASP.NET Core
author: rick-anderson
description: Os esquemas de política de autenticação facilitam a tarefa de ter um único esquema de autenticação lógica
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/policyschemes
ms.openlocfilehash: ddedf62c5e8363bd93c9948fd2d3418abc566539
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767311"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="b9a9e-103">Esquemas de política no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9a9e-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="b9a9e-104">Os esquemas de diretiva de autenticação facilitam a utilização de um único esquema de autenticação lógica, o que pode usar várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="b9a9e-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="b9a9e-105">Por exemplo, um esquema de política pode usar a autenticação do Google para desafios e a autenticação de cookie para todo o resto.</span><span class="sxs-lookup"><span data-stu-id="b9a9e-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="b9a9e-106">Os esquemas de política de autenticação o fazem:</span><span class="sxs-lookup"><span data-stu-id="b9a9e-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="b9a9e-107">É fácil encaminhar qualquer ação de autenticação para outro esquema.</span><span class="sxs-lookup"><span data-stu-id="b9a9e-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="b9a9e-108">Encaminhe dinamicamente com base na solicitação.</span><span class="sxs-lookup"><span data-stu-id="b9a9e-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="b9a9e-109">Todos os esquemas de autenticação que <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> usam derivado e o [\<AuthenticationHandler TOptions associado>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="b9a9e-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="b9a9e-110">São esquemas de política automaticamente no ASP.NET Core 2,1 e posterior.</span><span class="sxs-lookup"><span data-stu-id="b9a9e-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="b9a9e-111">Pode ser habilitado por meio da configuração das opções do esquema.</span><span class="sxs-lookup"><span data-stu-id="b9a9e-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="b9a9e-112">Exemplos</span><span class="sxs-lookup"><span data-stu-id="b9a9e-112">Examples</span></span>

<span data-ttu-id="b9a9e-113">O exemplo a seguir mostra um esquema de nível superior que combina esquemas de nível inferior.</span><span class="sxs-lookup"><span data-stu-id="b9a9e-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="b9a9e-114">A autenticação do Google é usada para desafios e a autenticação de cookie é usada para todo o resto:</span><span class="sxs-lookup"><span data-stu-id="b9a9e-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="b9a9e-115">O exemplo a seguir habilita a seleção dinâmica de esquemas de acordo com a solicitação.</span><span class="sxs-lookup"><span data-stu-id="b9a9e-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="b9a9e-116">Ou seja, como misturar cookies e autenticação de API:</span><span class="sxs-lookup"><span data-stu-id="b9a9e-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]

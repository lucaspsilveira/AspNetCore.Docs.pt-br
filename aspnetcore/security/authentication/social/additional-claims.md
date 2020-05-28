---
<span data-ttu-id="f87bb-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-102">'Blazor'</span></span>
- <span data-ttu-id="f87bb-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-103">'Identity'</span></span>
- <span data-ttu-id="f87bb-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-105">'Razor'</span></span>
- <span data-ttu-id="f87bb-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-106">'SignalR' uid:</span></span> 

---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="f87bb-107">Manter declarações e tokens adicionais de provedores externos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f87bb-107">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f87bb-108">Um aplicativo ASP.NET Core pode estabelecer declarações e tokens adicionais de provedores de autenticação externos, como Facebook, Google, Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="f87bb-108">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="f87bb-109">Cada provedor revela informações diferentes sobre os usuários em sua plataforma, mas o padrão para receber e transformar dados do usuário em declarações adicionais é o mesmo.</span><span class="sxs-lookup"><span data-stu-id="f87bb-109">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="f87bb-110">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f87bb-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f87bb-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="f87bb-111">Prerequisites</span></span>

<span data-ttu-id="f87bb-112">Decida quais provedores de autenticação externa dar suporte no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f87bb-112">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="f87bb-113">Para cada provedor, registre o aplicativo e obtenha uma ID do cliente e um segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="f87bb-113">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="f87bb-114">Para obter mais informações, consulte <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="f87bb-114">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="f87bb-115">O aplicativo de exemplo usa o [provedor de autenticação do Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="f87bb-115">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="f87bb-116">Definir a ID do cliente e o segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="f87bb-116">Set the client ID and client secret</span></span>

<span data-ttu-id="f87bb-117">O provedor de autenticação OAuth estabelece uma relação de confiança com um aplicativo usando uma ID do cliente e o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="f87bb-117">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="f87bb-118">Os valores de ID do cliente e segredo do cliente são criados para o aplicativo pelo provedor de autenticação externa quando o aplicativo é registrado com o provedor.</span><span class="sxs-lookup"><span data-stu-id="f87bb-118">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="f87bb-119">Cada provedor externo que o aplicativo usa deve ser configurado independentemente com a ID do cliente do provedor e o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="f87bb-119">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="f87bb-120">Para obter mais informações, consulte os tópicos do provedor de autenticação externa que se aplicam ao seu cenário:</span><span class="sxs-lookup"><span data-stu-id="f87bb-120">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="f87bb-121">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="f87bb-121">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f87bb-122">Autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="f87bb-122">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="f87bb-123">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="f87bb-123">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f87bb-124">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="f87bb-124">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f87bb-125">Outros provedores de autenticação</span><span class="sxs-lookup"><span data-stu-id="f87bb-125">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="f87bb-126">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="f87bb-126">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="f87bb-127">O aplicativo de exemplo configura o provedor de autenticação do Google com uma ID do cliente e o segredo do cliente fornecidos pelo Google:</span><span class="sxs-lookup"><span data-stu-id="f87bb-127">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="f87bb-128">Estabelecer o escopo de autenticação</span><span class="sxs-lookup"><span data-stu-id="f87bb-128">Establish the authentication scope</span></span>

<span data-ttu-id="f87bb-129">Especifique a lista de permissões a serem recuperadas do provedor especificando o <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="f87bb-129">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="f87bb-130">Os escopos de autenticação para provedores externos comuns aparecem na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="f87bb-130">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="f87bb-131">Provedor</span><span class="sxs-lookup"><span data-stu-id="f87bb-131">Provider</span></span>  | <span data-ttu-id="f87bb-132">Escopo</span><span class="sxs-lookup"><span data-stu-id="f87bb-132">Scope</span></span>                                                            |
| ---
<span data-ttu-id="f87bb-133">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-133">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-134">'Blazor'</span></span>
- <span data-ttu-id="f87bb-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-135">'Identity'</span></span>
- <span data-ttu-id="f87bb-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-137">'Razor'</span></span>
- <span data-ttu-id="f87bb-138">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-139">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-139">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-140">'Blazor'</span></span>
- <span data-ttu-id="f87bb-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-141">'Identity'</span></span>
- <span data-ttu-id="f87bb-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-143">'Razor'</span></span>
- <span data-ttu-id="f87bb-144">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-144">'SignalR' uid:</span></span> 

<span data-ttu-id="f87bb-145">----- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-145">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-146">'Blazor'</span></span>
- <span data-ttu-id="f87bb-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-147">'Identity'</span></span>
- <span data-ttu-id="f87bb-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-149">'Razor'</span></span>
- <span data-ttu-id="f87bb-150">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-151">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-152">'Blazor'</span></span>
- <span data-ttu-id="f87bb-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-153">'Identity'</span></span>
- <span data-ttu-id="f87bb-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-155">'Razor'</span></span>
- <span data-ttu-id="f87bb-156">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-157">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-158">'Blazor'</span></span>
- <span data-ttu-id="f87bb-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-159">'Identity'</span></span>
- <span data-ttu-id="f87bb-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-161">'Razor'</span></span>
- <span data-ttu-id="f87bb-162">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-163">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-163">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-164">'Blazor'</span></span>
- <span data-ttu-id="f87bb-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-165">'Identity'</span></span>
- <span data-ttu-id="f87bb-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-167">'Razor'</span></span>
- <span data-ttu-id="f87bb-168">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-169">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-170">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-170">'Blazor'</span></span>
- <span data-ttu-id="f87bb-171">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-171">'Identity'</span></span>
- <span data-ttu-id="f87bb-172">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-172">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-173">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-173">'Razor'</span></span>
- <span data-ttu-id="f87bb-174">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-174">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-175">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-176">'Blazor'</span></span>
- <span data-ttu-id="f87bb-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-177">'Identity'</span></span>
- <span data-ttu-id="f87bb-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-179">'Razor'</span></span>
- <span data-ttu-id="f87bb-180">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-181">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-182">'Blazor'</span></span>
- <span data-ttu-id="f87bb-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-183">'Identity'</span></span>
- <span data-ttu-id="f87bb-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-185">'Razor'</span></span>
- <span data-ttu-id="f87bb-186">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-187">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-188">'Blazor'</span></span>
- <span data-ttu-id="f87bb-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-189">'Identity'</span></span>
- <span data-ttu-id="f87bb-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-191">'Razor'</span></span>
- <span data-ttu-id="f87bb-192">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-193">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-194">'Blazor'</span></span>
- <span data-ttu-id="f87bb-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-195">'Identity'</span></span>
- <span data-ttu-id="f87bb-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-197">'Razor'</span></span>
- <span data-ttu-id="f87bb-198">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-199">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-200">'Blazor'</span></span>
- <span data-ttu-id="f87bb-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-201">'Identity'</span></span>
- <span data-ttu-id="f87bb-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-203">'Razor'</span></span>
- <span data-ttu-id="f87bb-204">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-205">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-206">'Blazor'</span></span>
- <span data-ttu-id="f87bb-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-207">'Identity'</span></span>
- <span data-ttu-id="f87bb-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-209">'Razor'</span></span>
- <span data-ttu-id="f87bb-210">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-211">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-212">'Blazor'</span></span>
- <span data-ttu-id="f87bb-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-213">'Identity'</span></span>
- <span data-ttu-id="f87bb-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-215">'Razor'</span></span>
- <span data-ttu-id="f87bb-216">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-217">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-218">'Blazor'</span></span>
- <span data-ttu-id="f87bb-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-219">'Identity'</span></span>
- <span data-ttu-id="f87bb-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-221">'Razor'</span></span>
- <span data-ttu-id="f87bb-222">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-223">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-224">'Blazor'</span></span>
- <span data-ttu-id="f87bb-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-225">'Identity'</span></span>
- <span data-ttu-id="f87bb-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-227">'Razor'</span></span>
- <span data-ttu-id="f87bb-228">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-229">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-230">'Blazor'</span></span>
- <span data-ttu-id="f87bb-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-231">'Identity'</span></span>
- <span data-ttu-id="f87bb-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-233">'Razor'</span></span>
- <span data-ttu-id="f87bb-234">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-235">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-236">'Blazor'</span></span>
- <span data-ttu-id="f87bb-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-237">'Identity'</span></span>
- <span data-ttu-id="f87bb-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-239">'Razor'</span></span>
- <span data-ttu-id="f87bb-240">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-241">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-242">'Blazor'</span></span>
- <span data-ttu-id="f87bb-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-243">'Identity'</span></span>
- <span data-ttu-id="f87bb-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-245">'Razor'</span></span>
- <span data-ttu-id="f87bb-246">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-247">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-248">'Blazor'</span></span>
- <span data-ttu-id="f87bb-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-249">'Identity'</span></span>
- <span data-ttu-id="f87bb-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-251">'Razor'</span></span>
- <span data-ttu-id="f87bb-252">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-253">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-254">'Blazor'</span></span>
- <span data-ttu-id="f87bb-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-255">'Identity'</span></span>
- <span data-ttu-id="f87bb-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-257">'Razor'</span></span>
- <span data-ttu-id="f87bb-258">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-259">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-260">'Blazor'</span></span>
- <span data-ttu-id="f87bb-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-261">'Identity'</span></span>
- <span data-ttu-id="f87bb-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-263">'Razor'</span></span>
- <span data-ttu-id="f87bb-264">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-265">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-265">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-266">'Blazor'</span></span>
- <span data-ttu-id="f87bb-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-267">'Identity'</span></span>
- <span data-ttu-id="f87bb-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-269">'Razor'</span></span>
- <span data-ttu-id="f87bb-270">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-271">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-272">'Blazor'</span></span>
- <span data-ttu-id="f87bb-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-273">'Identity'</span></span>
- <span data-ttu-id="f87bb-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-275">'Razor'</span></span>
- <span data-ttu-id="f87bb-276">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-277">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-278">'Blazor'</span></span>
- <span data-ttu-id="f87bb-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-279">'Identity'</span></span>
- <span data-ttu-id="f87bb-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-281">'Razor'</span></span>
- <span data-ttu-id="f87bb-282">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-282">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-283">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-283">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-284">'Blazor'</span></span>
- <span data-ttu-id="f87bb-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-285">'Identity'</span></span>
- <span data-ttu-id="f87bb-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-287">'Razor'</span></span>
- <span data-ttu-id="f87bb-288">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-289">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-289">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-290">'Blazor'</span></span>
- <span data-ttu-id="f87bb-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-291">'Identity'</span></span>
- <span data-ttu-id="f87bb-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-293">'Razor'</span></span>
- <span data-ttu-id="f87bb-294">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-295">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-295">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-296">'Blazor'</span></span>
- <span data-ttu-id="f87bb-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-297">'Identity'</span></span>
- <span data-ttu-id="f87bb-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-299">'Razor'</span></span>
- <span data-ttu-id="f87bb-300">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-301">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-301">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-302">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-302">'Blazor'</span></span>
- <span data-ttu-id="f87bb-303">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-303">'Identity'</span></span>
- <span data-ttu-id="f87bb-304">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-304">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-305">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-305">'Razor'</span></span>
- <span data-ttu-id="f87bb-306">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-306">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-307">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-307">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-308">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-308">'Blazor'</span></span>
- <span data-ttu-id="f87bb-309">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-309">'Identity'</span></span>
- <span data-ttu-id="f87bb-310">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-310">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-311">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-311">'Razor'</span></span>
- <span data-ttu-id="f87bb-312">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-312">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-313">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-313">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-314">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-314">'Blazor'</span></span>
- <span data-ttu-id="f87bb-315">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-315">'Identity'</span></span>
- <span data-ttu-id="f87bb-316">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-316">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-317">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-317">'Razor'</span></span>
- <span data-ttu-id="f87bb-318">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-318">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-319">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-319">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-320">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-320">'Blazor'</span></span>
- <span data-ttu-id="f87bb-321">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-321">'Identity'</span></span>
- <span data-ttu-id="f87bb-322">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-322">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-323">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-323">'Razor'</span></span>
- <span data-ttu-id="f87bb-324">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-324">'SignalR' uid:</span></span> 

<span data-ttu-id="f87bb-325">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="f87bb-325">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="f87bb-326">No aplicativo de exemplo, o escopo do Google `userinfo.profile` é adicionado automaticamente pelo Framework quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> é chamado no <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="f87bb-326">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="f87bb-327">Se o aplicativo exigir escopos adicionais, adicione-os às opções.</span><span class="sxs-lookup"><span data-stu-id="f87bb-327">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="f87bb-328">No exemplo a seguir, o escopo do Google `https://www.googleapis.com/auth/user.birthday.read` é adicionado para recuperar o aniversário de um usuário:</span><span class="sxs-lookup"><span data-stu-id="f87bb-328">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="f87bb-329">Mapear chaves de dados do usuário e criar declarações</span><span class="sxs-lookup"><span data-stu-id="f87bb-329">Map user data keys and create claims</span></span>

<span data-ttu-id="f87bb-330">Nas opções do provedor, especifique um <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada chave/subchave nos dados de usuário JSON do provedor externo para que a identidade do aplicativo seja lida na entrada.</span><span class="sxs-lookup"><span data-stu-id="f87bb-330">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="f87bb-331">Para obter mais informações sobre tipos de declaração, consulte <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="f87bb-331">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="f87bb-332">O aplicativo de exemplo cria `urn:google:locale` declarações de localidade () e de imagem ( `urn:google:picture` ) das `locale` `picture` chaves e nos dados de usuário do Google:</span><span class="sxs-lookup"><span data-stu-id="f87bb-332">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="f87bb-333">No `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , um <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) é conectado ao aplicativo com <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="f87bb-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="f87bb-334">Durante o processo de entrada, o <xref:Microsoft.AspNetCore.Identity.UserManager%601> pode armazenar uma `ApplicationUser` declaração de dados do usuário disponível no <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="f87bb-334">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="f87bb-335">No aplicativo de exemplo, `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) estabelece as declarações de locale ( `urn:google:locale` ) e Picture ( `urn:google:picture` ) para o conectado `ApplicationUser` , incluindo uma declaração para <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="f87bb-335">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="f87bb-336">Por padrão, as declarações de um usuário são armazenadas no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="f87bb-336">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="f87bb-337">Se o cookie de autenticação for muito grande, isso poderá causar falha no aplicativo porque:</span><span class="sxs-lookup"><span data-stu-id="f87bb-337">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="f87bb-338">O navegador detecta que o cabeçalho do cookie é muito longo.</span><span class="sxs-lookup"><span data-stu-id="f87bb-338">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="f87bb-339">O tamanho geral da solicitação é muito grande.</span><span class="sxs-lookup"><span data-stu-id="f87bb-339">The overall size of the request is too large.</span></span>

<span data-ttu-id="f87bb-340">Se uma grande quantidade de dados do usuário for necessária para processar solicitações do usuário:</span><span class="sxs-lookup"><span data-stu-id="f87bb-340">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="f87bb-341">Limite o número e o tamanho das declarações do usuário para o processamento da solicitação apenas para o que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="f87bb-341">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="f87bb-342">Use um personalizado <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> para o middleware de autenticação de cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> para armazenar a identidade entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="f87bb-342">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="f87bb-343">Preserve grandes quantidades de informações de identidade no servidor ao enviar apenas uma pequena chave de identificador de sessão para o cliente.</span><span class="sxs-lookup"><span data-stu-id="f87bb-343">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="f87bb-344">Salvar o token de acesso</span><span class="sxs-lookup"><span data-stu-id="f87bb-344">Save the access token</span></span>

<span data-ttu-id="f87bb-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>define se os tokens de acesso e de atualização devem ser armazenados no <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> após uma autorização bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="f87bb-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="f87bb-346">`SaveTokens`é definido como `false` por padrão para reduzir o tamanho do cookie de autenticação final.</span><span class="sxs-lookup"><span data-stu-id="f87bb-346">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="f87bb-347">O aplicativo de exemplo define o valor de `SaveTokens` para `true` em <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="f87bb-347">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="f87bb-348">Quando `OnPostConfirmationAsync` o é executado, armazene o token de acesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) do provedor externo `ApplicationUser` no `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="f87bb-348">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="f87bb-349">O aplicativo de exemplo salva o token de acesso em `OnPostConfirmationAsync` (novo registro de usuário) e `OnGetCallbackAsync` (usuário registrado anteriormente) em *Account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f87bb-349">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="f87bb-350">Como adicionar tokens personalizados adicionais</span><span class="sxs-lookup"><span data-stu-id="f87bb-350">How to add additional custom tokens</span></span>

<span data-ttu-id="f87bb-351">Para demonstrar como adicionar um token personalizado, que é armazenado como parte do `SaveTokens` , o aplicativo de exemplo adiciona um <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> com o atual <xref:System.DateTime> para um [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="f87bb-351">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="f87bb-352">Criando e adicionando declarações</span><span class="sxs-lookup"><span data-stu-id="f87bb-352">Creating and adding claims</span></span>

<span data-ttu-id="f87bb-353">A estrutura fornece ações comuns e métodos de extensão para criar e adicionar declarações à coleção.</span><span class="sxs-lookup"><span data-stu-id="f87bb-353">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="f87bb-354">Para obter mais informações, consulte o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="f87bb-354">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="f87bb-355">Os usuários podem definir ações personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando o <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstract.</span><span class="sxs-lookup"><span data-stu-id="f87bb-355">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="f87bb-356">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="f87bb-356">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="f87bb-357">Remoção de declarações e ações de declaração</span><span class="sxs-lookup"><span data-stu-id="f87bb-357">Removal of claim actions and claims</span></span>

<span data-ttu-id="f87bb-358">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) remove todas as ações de declaração para o dado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> da coleção.</span><span class="sxs-lookup"><span data-stu-id="f87bb-358">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="f87bb-359">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) exclui uma declaração do dado da <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> identidade.</span><span class="sxs-lookup"><span data-stu-id="f87bb-359">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="f87bb-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>é usado principalmente com o [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para remover declarações geradas por protocolo.</span><span class="sxs-lookup"><span data-stu-id="f87bb-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="f87bb-361">Saída do aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="f87bb-361">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f87bb-362">Um aplicativo ASP.NET Core pode estabelecer declarações e tokens adicionais de provedores de autenticação externos, como Facebook, Google, Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="f87bb-362">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="f87bb-363">Cada provedor revela informações diferentes sobre os usuários em sua plataforma, mas o padrão para receber e transformar dados do usuário em declarações adicionais é o mesmo.</span><span class="sxs-lookup"><span data-stu-id="f87bb-363">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="f87bb-364">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f87bb-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f87bb-365">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="f87bb-365">Prerequisites</span></span>

<span data-ttu-id="f87bb-366">Decida quais provedores de autenticação externa dar suporte no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f87bb-366">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="f87bb-367">Para cada provedor, registre o aplicativo e obtenha uma ID do cliente e um segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="f87bb-367">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="f87bb-368">Para obter mais informações, consulte <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="f87bb-368">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="f87bb-369">O aplicativo de exemplo usa o [provedor de autenticação do Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="f87bb-369">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="f87bb-370">Definir a ID do cliente e o segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="f87bb-370">Set the client ID and client secret</span></span>

<span data-ttu-id="f87bb-371">O provedor de autenticação OAuth estabelece uma relação de confiança com um aplicativo usando uma ID do cliente e o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="f87bb-371">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="f87bb-372">Os valores de ID do cliente e segredo do cliente são criados para o aplicativo pelo provedor de autenticação externa quando o aplicativo é registrado com o provedor.</span><span class="sxs-lookup"><span data-stu-id="f87bb-372">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="f87bb-373">Cada provedor externo que o aplicativo usa deve ser configurado independentemente com a ID do cliente do provedor e o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="f87bb-373">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="f87bb-374">Para obter mais informações, consulte os tópicos do provedor de autenticação externa que se aplicam ao seu cenário:</span><span class="sxs-lookup"><span data-stu-id="f87bb-374">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="f87bb-375">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="f87bb-375">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f87bb-376">Autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="f87bb-376">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="f87bb-377">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="f87bb-377">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f87bb-378">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="f87bb-378">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f87bb-379">Outros provedores de autenticação</span><span class="sxs-lookup"><span data-stu-id="f87bb-379">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="f87bb-380">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="f87bb-380">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="f87bb-381">O aplicativo de exemplo configura o provedor de autenticação do Google com uma ID do cliente e o segredo do cliente fornecidos pelo Google:</span><span class="sxs-lookup"><span data-stu-id="f87bb-381">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="f87bb-382">Estabelecer o escopo de autenticação</span><span class="sxs-lookup"><span data-stu-id="f87bb-382">Establish the authentication scope</span></span>

<span data-ttu-id="f87bb-383">Especifique a lista de permissões a serem recuperadas do provedor especificando o <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="f87bb-383">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="f87bb-384">Os escopos de autenticação para provedores externos comuns aparecem na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="f87bb-384">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="f87bb-385">Provedor</span><span class="sxs-lookup"><span data-stu-id="f87bb-385">Provider</span></span>  | <span data-ttu-id="f87bb-386">Escopo</span><span class="sxs-lookup"><span data-stu-id="f87bb-386">Scope</span></span>                                                            |
| ---
<span data-ttu-id="f87bb-387">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-388">'Blazor'</span></span>
- <span data-ttu-id="f87bb-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-389">'Identity'</span></span>
- <span data-ttu-id="f87bb-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-391">'Razor'</span></span>
- <span data-ttu-id="f87bb-392">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-393">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-394">'Blazor'</span></span>
- <span data-ttu-id="f87bb-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-395">'Identity'</span></span>
- <span data-ttu-id="f87bb-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-397">'Razor'</span></span>
- <span data-ttu-id="f87bb-398">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-398">'SignalR' uid:</span></span> 

<span data-ttu-id="f87bb-399">----- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-399">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-400">'Blazor'</span></span>
- <span data-ttu-id="f87bb-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-401">'Identity'</span></span>
- <span data-ttu-id="f87bb-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-403">'Razor'</span></span>
- <span data-ttu-id="f87bb-404">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-405">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-406">'Blazor'</span></span>
- <span data-ttu-id="f87bb-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-407">'Identity'</span></span>
- <span data-ttu-id="f87bb-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-409">'Razor'</span></span>
- <span data-ttu-id="f87bb-410">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-411">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-412">'Blazor'</span></span>
- <span data-ttu-id="f87bb-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-413">'Identity'</span></span>
- <span data-ttu-id="f87bb-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-415">'Razor'</span></span>
- <span data-ttu-id="f87bb-416">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-417">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-418">'Blazor'</span></span>
- <span data-ttu-id="f87bb-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-419">'Identity'</span></span>
- <span data-ttu-id="f87bb-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-421">'Razor'</span></span>
- <span data-ttu-id="f87bb-422">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-423">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-424">'Blazor'</span></span>
- <span data-ttu-id="f87bb-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-425">'Identity'</span></span>
- <span data-ttu-id="f87bb-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-427">'Razor'</span></span>
- <span data-ttu-id="f87bb-428">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-429">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-430">'Blazor'</span></span>
- <span data-ttu-id="f87bb-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-431">'Identity'</span></span>
- <span data-ttu-id="f87bb-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-433">'Razor'</span></span>
- <span data-ttu-id="f87bb-434">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-435">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-436">'Blazor'</span></span>
- <span data-ttu-id="f87bb-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-437">'Identity'</span></span>
- <span data-ttu-id="f87bb-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-439">'Razor'</span></span>
- <span data-ttu-id="f87bb-440">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-441">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-442">'Blazor'</span></span>
- <span data-ttu-id="f87bb-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-443">'Identity'</span></span>
- <span data-ttu-id="f87bb-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-445">'Razor'</span></span>
- <span data-ttu-id="f87bb-446">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-447">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-448">'Blazor'</span></span>
- <span data-ttu-id="f87bb-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-449">'Identity'</span></span>
- <span data-ttu-id="f87bb-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-451">'Razor'</span></span>
- <span data-ttu-id="f87bb-452">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-453">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-454">'Blazor'</span></span>
- <span data-ttu-id="f87bb-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-455">'Identity'</span></span>
- <span data-ttu-id="f87bb-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-457">'Razor'</span></span>
- <span data-ttu-id="f87bb-458">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-459">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-460">'Blazor'</span></span>
- <span data-ttu-id="f87bb-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-461">'Identity'</span></span>
- <span data-ttu-id="f87bb-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-463">'Razor'</span></span>
- <span data-ttu-id="f87bb-464">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-465">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-466">'Blazor'</span></span>
- <span data-ttu-id="f87bb-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-467">'Identity'</span></span>
- <span data-ttu-id="f87bb-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-469">'Razor'</span></span>
- <span data-ttu-id="f87bb-470">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-471">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-472">'Blazor'</span></span>
- <span data-ttu-id="f87bb-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-473">'Identity'</span></span>
- <span data-ttu-id="f87bb-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-475">'Razor'</span></span>
- <span data-ttu-id="f87bb-476">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-477">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-478">'Blazor'</span></span>
- <span data-ttu-id="f87bb-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-479">'Identity'</span></span>
- <span data-ttu-id="f87bb-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-481">'Razor'</span></span>
- <span data-ttu-id="f87bb-482">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-483">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-484">'Blazor'</span></span>
- <span data-ttu-id="f87bb-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-485">'Identity'</span></span>
- <span data-ttu-id="f87bb-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-487">'Razor'</span></span>
- <span data-ttu-id="f87bb-488">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-489">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-490">'Blazor'</span></span>
- <span data-ttu-id="f87bb-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-491">'Identity'</span></span>
- <span data-ttu-id="f87bb-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-493">'Razor'</span></span>
- <span data-ttu-id="f87bb-494">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-495">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-496">'Blazor'</span></span>
- <span data-ttu-id="f87bb-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-497">'Identity'</span></span>
- <span data-ttu-id="f87bb-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-499">'Razor'</span></span>
- <span data-ttu-id="f87bb-500">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-501">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-502">'Blazor'</span></span>
- <span data-ttu-id="f87bb-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-503">'Identity'</span></span>
- <span data-ttu-id="f87bb-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-505">'Razor'</span></span>
- <span data-ttu-id="f87bb-506">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-507">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-508">'Blazor'</span></span>
- <span data-ttu-id="f87bb-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-509">'Identity'</span></span>
- <span data-ttu-id="f87bb-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-511">'Razor'</span></span>
- <span data-ttu-id="f87bb-512">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-513">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-514">'Blazor'</span></span>
- <span data-ttu-id="f87bb-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-515">'Identity'</span></span>
- <span data-ttu-id="f87bb-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-517">'Razor'</span></span>
- <span data-ttu-id="f87bb-518">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-519">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-520">'Blazor'</span></span>
- <span data-ttu-id="f87bb-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-521">'Identity'</span></span>
- <span data-ttu-id="f87bb-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-523">'Razor'</span></span>
- <span data-ttu-id="f87bb-524">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-525">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-526">'Blazor'</span></span>
- <span data-ttu-id="f87bb-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-527">'Identity'</span></span>
- <span data-ttu-id="f87bb-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-529">'Razor'</span></span>
- <span data-ttu-id="f87bb-530">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-531">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-532">'Blazor'</span></span>
- <span data-ttu-id="f87bb-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-533">'Identity'</span></span>
- <span data-ttu-id="f87bb-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-535">'Razor'</span></span>
- <span data-ttu-id="f87bb-536">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-537">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-538">'Blazor'</span></span>
- <span data-ttu-id="f87bb-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-539">'Identity'</span></span>
- <span data-ttu-id="f87bb-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-541">'Razor'</span></span>
- <span data-ttu-id="f87bb-542">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-543">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-544">'Blazor'</span></span>
- <span data-ttu-id="f87bb-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-545">'Identity'</span></span>
- <span data-ttu-id="f87bb-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-547">'Razor'</span></span>
- <span data-ttu-id="f87bb-548">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-549">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-550">'Blazor'</span></span>
- <span data-ttu-id="f87bb-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-551">'Identity'</span></span>
- <span data-ttu-id="f87bb-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-553">'Razor'</span></span>
- <span data-ttu-id="f87bb-554">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-555">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-556">'Blazor'</span></span>
- <span data-ttu-id="f87bb-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-557">'Identity'</span></span>
- <span data-ttu-id="f87bb-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-559">'Razor'</span></span>
- <span data-ttu-id="f87bb-560">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-561">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-562">'Blazor'</span></span>
- <span data-ttu-id="f87bb-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-563">'Identity'</span></span>
- <span data-ttu-id="f87bb-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-565">'Razor'</span></span>
- <span data-ttu-id="f87bb-566">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-567">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-568">'Blazor'</span></span>
- <span data-ttu-id="f87bb-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-569">'Identity'</span></span>
- <span data-ttu-id="f87bb-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-571">'Razor'</span></span>
- <span data-ttu-id="f87bb-572">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f87bb-573">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f87bb-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f87bb-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-574">'Blazor'</span></span>
- <span data-ttu-id="f87bb-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f87bb-575">'Identity'</span></span>
- <span data-ttu-id="f87bb-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f87bb-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="f87bb-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f87bb-577">'Razor'</span></span>
- <span data-ttu-id="f87bb-578">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f87bb-578">'SignalR' uid:</span></span> 

<span data-ttu-id="f87bb-579">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="f87bb-579">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="f87bb-580">No aplicativo de exemplo, o escopo do Google `userinfo.profile` é adicionado automaticamente pelo Framework quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> é chamado no <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="f87bb-580">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="f87bb-581">Se o aplicativo exigir escopos adicionais, adicione-os às opções.</span><span class="sxs-lookup"><span data-stu-id="f87bb-581">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="f87bb-582">No exemplo a seguir, o escopo do Google `https://www.googleapis.com/auth/user.birthday.read` é adicionado para recuperar o aniversário de um usuário:</span><span class="sxs-lookup"><span data-stu-id="f87bb-582">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="f87bb-583">Mapear chaves de dados do usuário e criar declarações</span><span class="sxs-lookup"><span data-stu-id="f87bb-583">Map user data keys and create claims</span></span>

<span data-ttu-id="f87bb-584">Nas opções do provedor, especifique um <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada chave/subchave nos dados de usuário JSON do provedor externo para que a identidade do aplicativo seja lida na entrada.</span><span class="sxs-lookup"><span data-stu-id="f87bb-584">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="f87bb-585">Para obter mais informações sobre tipos de declaração, consulte <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="f87bb-585">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="f87bb-586">O aplicativo de exemplo cria `urn:google:locale` declarações de localidade () e de imagem ( `urn:google:picture` ) das `locale` `picture` chaves e nos dados de usuário do Google:</span><span class="sxs-lookup"><span data-stu-id="f87bb-586">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="f87bb-587">No `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , um <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) é conectado ao aplicativo com <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="f87bb-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="f87bb-588">Durante o processo de entrada, o <xref:Microsoft.AspNetCore.Identity.UserManager%601> pode armazenar uma `ApplicationUser` declaração de dados do usuário disponível no <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="f87bb-588">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="f87bb-589">No aplicativo de exemplo, `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) estabelece as declarações de locale ( `urn:google:locale` ) e Picture ( `urn:google:picture` ) para o conectado `ApplicationUser` , incluindo uma declaração para <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="f87bb-589">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="f87bb-590">Por padrão, as declarações de um usuário são armazenadas no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="f87bb-590">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="f87bb-591">Se o cookie de autenticação for muito grande, isso poderá causar falha no aplicativo porque:</span><span class="sxs-lookup"><span data-stu-id="f87bb-591">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="f87bb-592">O navegador detecta que o cabeçalho do cookie é muito longo.</span><span class="sxs-lookup"><span data-stu-id="f87bb-592">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="f87bb-593">O tamanho geral da solicitação é muito grande.</span><span class="sxs-lookup"><span data-stu-id="f87bb-593">The overall size of the request is too large.</span></span>

<span data-ttu-id="f87bb-594">Se uma grande quantidade de dados do usuário for necessária para processar solicitações do usuário:</span><span class="sxs-lookup"><span data-stu-id="f87bb-594">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="f87bb-595">Limite o número e o tamanho das declarações do usuário para o processamento da solicitação apenas para o que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="f87bb-595">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="f87bb-596">Use um personalizado <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> para o middleware de autenticação de cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> para armazenar a identidade entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="f87bb-596">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="f87bb-597">Preserve grandes quantidades de informações de identidade no servidor ao enviar apenas uma pequena chave de identificador de sessão para o cliente.</span><span class="sxs-lookup"><span data-stu-id="f87bb-597">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="f87bb-598">Salvar o token de acesso</span><span class="sxs-lookup"><span data-stu-id="f87bb-598">Save the access token</span></span>

<span data-ttu-id="f87bb-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>define se os tokens de acesso e de atualização devem ser armazenados no <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> após uma autorização bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="f87bb-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="f87bb-600">`SaveTokens`é definido como `false` por padrão para reduzir o tamanho do cookie de autenticação final.</span><span class="sxs-lookup"><span data-stu-id="f87bb-600">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="f87bb-601">O aplicativo de exemplo define o valor de `SaveTokens` para `true` em <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="f87bb-601">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="f87bb-602">Quando `OnPostConfirmationAsync` o é executado, armazene o token de acesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) do provedor externo `ApplicationUser` no `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="f87bb-602">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="f87bb-603">O aplicativo de exemplo salva o token de acesso em `OnPostConfirmationAsync` (novo registro de usuário) e `OnGetCallbackAsync` (usuário registrado anteriormente) em *Account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f87bb-603">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="f87bb-604">Como adicionar tokens personalizados adicionais</span><span class="sxs-lookup"><span data-stu-id="f87bb-604">How to add additional custom tokens</span></span>

<span data-ttu-id="f87bb-605">Para demonstrar como adicionar um token personalizado, que é armazenado como parte do `SaveTokens` , o aplicativo de exemplo adiciona um <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> com o atual <xref:System.DateTime> para um [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="f87bb-605">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="f87bb-606">Criando e adicionando declarações</span><span class="sxs-lookup"><span data-stu-id="f87bb-606">Creating and adding claims</span></span>

<span data-ttu-id="f87bb-607">A estrutura fornece ações comuns e métodos de extensão para criar e adicionar declarações à coleção.</span><span class="sxs-lookup"><span data-stu-id="f87bb-607">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="f87bb-608">Para obter mais informações, consulte o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="f87bb-608">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="f87bb-609">Os usuários podem definir ações personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando o <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstract.</span><span class="sxs-lookup"><span data-stu-id="f87bb-609">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="f87bb-610">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="f87bb-610">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="f87bb-611">Remoção de declarações e ações de declaração</span><span class="sxs-lookup"><span data-stu-id="f87bb-611">Removal of claim actions and claims</span></span>

<span data-ttu-id="f87bb-612">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) remove todas as ações de declaração para o dado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> da coleção.</span><span class="sxs-lookup"><span data-stu-id="f87bb-612">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="f87bb-613">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) exclui uma declaração do dado da <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> identidade.</span><span class="sxs-lookup"><span data-stu-id="f87bb-613">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="f87bb-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>é usado principalmente com o [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para remover declarações geradas por protocolo.</span><span class="sxs-lookup"><span data-stu-id="f87bb-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="f87bb-615">Saída do aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="f87bb-615">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f87bb-616">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f87bb-616">Additional resources</span></span>

* <span data-ttu-id="f87bb-617">[dotnet/AspNetCore Engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): o aplicativo de exemplo vinculado está no Branch de engenharia [do repositório do GitHub dotnet/AspNetCore](https://github.com/dotnet/AspNetCore) `master` .</span><span class="sxs-lookup"><span data-stu-id="f87bb-617">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="f87bb-618">A `master` ramificação contém código em desenvolvimento ativo para a próxima versão do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f87bb-618">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="f87bb-619">Para ver uma versão do aplicativo de exemplo para uma versão lançada do ASP.NET Core, use a lista suspensa **Branch** para selecionar um Branch de lançamento (por exemplo `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="f87bb-619">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>

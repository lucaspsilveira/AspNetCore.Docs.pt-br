---
<span data-ttu-id="2cbe4-101">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-102">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-103">'Identity'</span></span>
- <span data-ttu-id="2cbe4-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-105">'Razor'</span></span>
- <span data-ttu-id="2cbe4-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-106">'SignalR' uid:</span></span> 

---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="2cbe4-107">Cache de resposta no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2cbe4-107">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="2cbe4-108">Por [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2cbe4-108">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2cbe4-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2cbe4-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2cbe4-110">O cache de resposta reduz o número de solicitações que um cliente ou proxy faz a um servidor Web.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-110">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="2cbe4-111">O cache de resposta também reduz a quantidade de trabalho que o servidor Web executa para gerar uma resposta.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-111">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="2cbe4-112">O cache de resposta é controlado por cabeçalhos que especificam como você deseja que o cliente, o proxy e o middleware armazenem em cache as respostas.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-112">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="2cbe4-113">O [atributo ResponseCache](#responsecache-attribute) participa da configuração de cabeçalhos de cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-113">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="2cbe4-114">Os clientes e proxies intermediários devem respeitar os cabeçalhos para armazenar em cache as respostas na [especificação de cache HTTP 1,1](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="2cbe4-114">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="2cbe4-115">Para o cache do lado do servidor que segue a especificação de cache HTTP 1,1, use o [middleware de cache de resposta](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="2cbe4-115">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="2cbe4-116">O middleware pode usar as <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> Propriedades para influenciar o comportamento de cache do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-116">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="2cbe4-117">Cache de resposta baseado em HTTP</span><span class="sxs-lookup"><span data-stu-id="2cbe4-117">HTTP-based response caching</span></span>

<span data-ttu-id="2cbe4-118">A [especificação de cache HTTP 1,1](https://tools.ietf.org/html/rfc7234) descreve como os caches da Internet devem se comportar.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-118">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="2cbe4-119">O cabeçalho HTTP primário usado para cache é o [controle de cache](https://tools.ietf.org/html/rfc7234#section-5.2), que é usado para especificar *diretivas*de cache.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-119">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="2cbe4-120">As diretivas controlam o comportamento do cache à medida que as solicitações fazem seu caminho de clientes para servidores e, à medida que as respostas voltam dos servidores para os clientes.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-120">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="2cbe4-121">As solicitações e respostas são movidas por meio de servidores proxy, e os servidores proxy também devem estar em conformidade com a especificação de cache HTTP 1,1.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-121">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="2cbe4-122">`Cache-Control`As diretivas comuns são mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-122">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="2cbe4-123">Diretiva</span><span class="sxs-lookup"><span data-stu-id="2cbe4-123">Directive</span></span>                                                       | <span data-ttu-id="2cbe4-124">Ação</span><span class="sxs-lookup"><span data-stu-id="2cbe4-124">Action</span></span> |
| ---
<span data-ttu-id="2cbe4-125">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-126">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-126">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-127">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-127">'Identity'</span></span>
- <span data-ttu-id="2cbe4-128">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-128">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-129">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-129">'Razor'</span></span>
- <span data-ttu-id="2cbe4-130">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-130">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-131">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-132">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-133">'Identity'</span></span>
- <span data-ttu-id="2cbe4-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-135">'Razor'</span></span>
- <span data-ttu-id="2cbe4-136">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-137">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-138">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-138">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-139">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-139">'Identity'</span></span>
- <span data-ttu-id="2cbe4-140">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-140">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-141">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-141">'Razor'</span></span>
- <span data-ttu-id="2cbe4-142">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-142">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-143">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-144">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-144">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-145">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-145">'Identity'</span></span>
- <span data-ttu-id="2cbe4-146">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-146">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-147">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-147">'Razor'</span></span>
- <span data-ttu-id="2cbe4-148">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-148">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-149">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-150">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-151">'Identity'</span></span>
- <span data-ttu-id="2cbe4-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-153">'Razor'</span></span>
- <span data-ttu-id="2cbe4-154">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-155">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-156">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-157">'Identity'</span></span>
- <span data-ttu-id="2cbe4-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-159">'Razor'</span></span>
- <span data-ttu-id="2cbe4-160">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-161">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-162">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-163">'Identity'</span></span>
- <span data-ttu-id="2cbe4-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-165">'Razor'</span></span>
- <span data-ttu-id="2cbe4-166">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-166">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-167">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-168">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-169">'Identity'</span></span>
- <span data-ttu-id="2cbe4-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-171">'Razor'</span></span>
- <span data-ttu-id="2cbe4-172">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-173">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-174">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-175">'Identity'</span></span>
- <span data-ttu-id="2cbe4-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-177">'Razor'</span></span>
- <span data-ttu-id="2cbe4-178">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-179">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-180">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-181">'Identity'</span></span>
- <span data-ttu-id="2cbe4-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-183">'Razor'</span></span>
- <span data-ttu-id="2cbe4-184">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-185">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-186">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-187">'Identity'</span></span>
- <span data-ttu-id="2cbe4-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-189">'Razor'</span></span>
- <span data-ttu-id="2cbe4-190">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-191">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-192">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-193">'Identity'</span></span>
- <span data-ttu-id="2cbe4-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-195">'Razor'</span></span>
- <span data-ttu-id="2cbe4-196">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-197">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-198">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-199">'Identity'</span></span>
- <span data-ttu-id="2cbe4-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-201">'Razor'</span></span>
- <span data-ttu-id="2cbe4-202">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-203">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-204">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-205">'Identity'</span></span>
- <span data-ttu-id="2cbe4-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-207">'Razor'</span></span>
- <span data-ttu-id="2cbe4-208">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-209">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-210">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-211">'Identity'</span></span>
- <span data-ttu-id="2cbe4-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-213">'Razor'</span></span>
- <span data-ttu-id="2cbe4-214">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-215">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-216">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-217">'Identity'</span></span>
- <span data-ttu-id="2cbe4-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-219">'Razor'</span></span>
- <span data-ttu-id="2cbe4-220">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-221">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-222">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-223">'Identity'</span></span>
- <span data-ttu-id="2cbe4-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-225">'Razor'</span></span>
- <span data-ttu-id="2cbe4-226">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-227">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-228">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-229">'Identity'</span></span>
- <span data-ttu-id="2cbe4-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-231">'Razor'</span></span>
- <span data-ttu-id="2cbe4-232">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-233">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-234">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-235">'Identity'</span></span>
- <span data-ttu-id="2cbe4-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-237">'Razor'</span></span>
- <span data-ttu-id="2cbe4-238">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-239">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-240">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-241">'Identity'</span></span>
- <span data-ttu-id="2cbe4-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-243">'Razor'</span></span>
- <span data-ttu-id="2cbe4-244">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-245">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-246">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-247">'Identity'</span></span>
- <span data-ttu-id="2cbe4-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-249">'Razor'</span></span>
- <span data-ttu-id="2cbe4-250">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-251">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-252">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-253">'Identity'</span></span>
- <span data-ttu-id="2cbe4-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-255">'Razor'</span></span>
- <span data-ttu-id="2cbe4-256">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-257">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-258">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-259">'Identity'</span></span>
- <span data-ttu-id="2cbe4-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-261">'Razor'</span></span>
- <span data-ttu-id="2cbe4-262">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-263">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-264">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-264">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-265">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-265">'Identity'</span></span>
- <span data-ttu-id="2cbe4-266">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-266">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-267">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-267">'Razor'</span></span>
- <span data-ttu-id="2cbe4-268">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-268">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-269">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-270">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-270">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-271">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-271">'Identity'</span></span>
- <span data-ttu-id="2cbe4-272">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-272">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-273">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-273">'Razor'</span></span>
- <span data-ttu-id="2cbe4-274">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-274">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-275">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-276">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-277">'Identity'</span></span>
- <span data-ttu-id="2cbe4-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-279">'Razor'</span></span>
- <span data-ttu-id="2cbe4-280">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-281">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-282">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-283">'Identity'</span></span>
- <span data-ttu-id="2cbe4-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-285">'Razor'</span></span>
- <span data-ttu-id="2cbe4-286">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-287">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-288">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-288">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-289">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-289">'Identity'</span></span>
- <span data-ttu-id="2cbe4-290">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-290">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-291">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-291">'Razor'</span></span>
- <span data-ttu-id="2cbe4-292">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-292">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-293">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-294">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-294">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-295">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-295">'Identity'</span></span>
- <span data-ttu-id="2cbe4-296">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-296">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-297">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-297">'Razor'</span></span>
- <span data-ttu-id="2cbe4-298">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-298">'SignalR' uid:</span></span> 

<span data-ttu-id="2cbe4-299">-------------------------------- | título do---: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-299">-------------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-300">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-301">'Identity'</span></span>
- <span data-ttu-id="2cbe4-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-303">'Razor'</span></span>
- <span data-ttu-id="2cbe4-304">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-304">'SignalR' uid:</span></span> 

<span data-ttu-id="2cbe4-305">--- | | [público](https://tools.ietf.org/html/rfc7234#section-5.2.2.5) | Um cache pode armazenar a resposta.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-305">--- | | [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | A cache may store the response.</span></span> <span data-ttu-id="2cbe4-306">| | [particular](https://tools.ietf.org/html/rfc7234#section-5.2.2.6) | A resposta não deve ser armazenada por um cache compartilhado.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-306">| | [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | The response must not be stored by a shared cache.</span></span> <span data-ttu-id="2cbe4-307">Um cache privado pode armazenar e reutilizar a resposta.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-307">A private cache may store and reuse the response.</span></span> <span data-ttu-id="2cbe4-308">| | [idade máxima](https://tools.ietf.org/html/rfc7234#section-5.2.1.1) | O cliente não aceita uma resposta cuja idade é maior que o número especificado de segundos.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="2cbe4-309">Exemplos: `max-age=60` (60 segundos), `max-age=2592000` (1 mês) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)  |  **Em solicitações**: um cache não deve usar uma resposta armazenada para atender à solicitação.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-309">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="2cbe4-310">O servidor de origem regenera a resposta para o cliente e o middleware atualiza a resposta armazenada em seu cache.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-310">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="2cbe4-311">**Em respostas**: a resposta não deve ser usada para uma solicitação subsequente sem validação no servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-311">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> <span data-ttu-id="2cbe4-312">| | [sem armazenamento](https://tools.ietf.org/html/rfc7234#section-5.2.1.5)  |  **Em solicitações**: um cache não deve armazenar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-312">| | [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="2cbe4-313">**Em respostas**: um cache não deve armazenar nenhuma parte da resposta.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-313">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="2cbe4-314">Outros cabeçalhos de cache que desempenham uma função no Caching são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-314">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="2cbe4-315">parâmetro</span><span class="sxs-lookup"><span data-stu-id="2cbe4-315">Header</span></span>                                                     | <span data-ttu-id="2cbe4-316">Função</span><span class="sxs-lookup"><span data-stu-id="2cbe4-316">Function</span></span> |
| ---
<span data-ttu-id="2cbe4-317">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-318">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-319">'Identity'</span></span>
- <span data-ttu-id="2cbe4-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-321">'Razor'</span></span>
- <span data-ttu-id="2cbe4-322">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-323">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-324">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-324">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-325">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-325">'Identity'</span></span>
- <span data-ttu-id="2cbe4-326">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-326">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-327">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-327">'Razor'</span></span>
- <span data-ttu-id="2cbe4-328">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-328">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-329">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-330">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-330">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-331">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-331">'Identity'</span></span>
- <span data-ttu-id="2cbe4-332">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-332">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-333">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-333">'Razor'</span></span>
- <span data-ttu-id="2cbe4-334">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-334">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-335">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-336">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-336">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-337">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-337">'Identity'</span></span>
- <span data-ttu-id="2cbe4-338">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-338">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-339">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-339">'Razor'</span></span>
- <span data-ttu-id="2cbe4-340">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-340">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-341">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-342">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-342">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-343">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-343">'Identity'</span></span>
- <span data-ttu-id="2cbe4-344">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-344">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-345">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-345">'Razor'</span></span>
- <span data-ttu-id="2cbe4-346">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-346">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-347">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-348">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-348">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-349">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-349">'Identity'</span></span>
- <span data-ttu-id="2cbe4-350">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-350">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-351">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-351">'Razor'</span></span>
- <span data-ttu-id="2cbe4-352">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-352">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-353">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-354">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-355">'Identity'</span></span>
- <span data-ttu-id="2cbe4-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-357">'Razor'</span></span>
- <span data-ttu-id="2cbe4-358">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-359">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-360">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-360">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-361">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-361">'Identity'</span></span>
- <span data-ttu-id="2cbe4-362">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-362">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-363">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-363">'Razor'</span></span>
- <span data-ttu-id="2cbe4-364">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-364">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-365">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-366">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-367">'Identity'</span></span>
- <span data-ttu-id="2cbe4-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-369">'Razor'</span></span>
- <span data-ttu-id="2cbe4-370">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-371">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-372">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-373">'Identity'</span></span>
- <span data-ttu-id="2cbe4-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-375">'Razor'</span></span>
- <span data-ttu-id="2cbe4-376">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-377">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-378">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-379">'Identity'</span></span>
- <span data-ttu-id="2cbe4-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-381">'Razor'</span></span>
- <span data-ttu-id="2cbe4-382">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-383">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-384">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-385">'Identity'</span></span>
- <span data-ttu-id="2cbe4-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-387">'Razor'</span></span>
- <span data-ttu-id="2cbe4-388">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-389">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-390">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-391">'Identity'</span></span>
- <span data-ttu-id="2cbe4-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-393">'Razor'</span></span>
- <span data-ttu-id="2cbe4-394">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-395">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-396">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-397">'Identity'</span></span>
- <span data-ttu-id="2cbe4-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-399">'Razor'</span></span>
- <span data-ttu-id="2cbe4-400">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-400">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-401">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-402">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-403">'Identity'</span></span>
- <span data-ttu-id="2cbe4-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-405">'Razor'</span></span>
- <span data-ttu-id="2cbe4-406">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-407">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-408">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-409">'Identity'</span></span>
- <span data-ttu-id="2cbe4-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-411">'Razor'</span></span>
- <span data-ttu-id="2cbe4-412">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-413">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-414">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-415">'Identity'</span></span>
- <span data-ttu-id="2cbe4-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-417">'Razor'</span></span>
- <span data-ttu-id="2cbe4-418">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-419">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-420">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-421">'Identity'</span></span>
- <span data-ttu-id="2cbe4-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-423">'Razor'</span></span>
- <span data-ttu-id="2cbe4-424">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-424">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-425">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-426">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-427">'Identity'</span></span>
- <span data-ttu-id="2cbe4-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-429">'Razor'</span></span>
- <span data-ttu-id="2cbe4-430">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-431">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-432">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-433">'Identity'</span></span>
- <span data-ttu-id="2cbe4-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-435">'Razor'</span></span>
- <span data-ttu-id="2cbe4-436">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-436">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-437">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-438">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-438">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-439">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-439">'Identity'</span></span>
- <span data-ttu-id="2cbe4-440">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-440">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-441">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-441">'Razor'</span></span>
- <span data-ttu-id="2cbe4-442">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-442">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-443">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-444">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-445">'Identity'</span></span>
- <span data-ttu-id="2cbe4-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-447">'Razor'</span></span>
- <span data-ttu-id="2cbe4-448">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-449">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-450">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-451">'Identity'</span></span>
- <span data-ttu-id="2cbe4-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-453">'Razor'</span></span>
- <span data-ttu-id="2cbe4-454">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-455">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-456">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-456">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-457">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-457">'Identity'</span></span>
- <span data-ttu-id="2cbe4-458">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-458">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-459">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-459">'Razor'</span></span>
- <span data-ttu-id="2cbe4-460">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-460">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-461">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-462">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-462">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-463">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-463">'Identity'</span></span>
- <span data-ttu-id="2cbe4-464">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-464">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-465">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-465">'Razor'</span></span>
- <span data-ttu-id="2cbe4-466">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-466">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-467">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-468">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-469">'Identity'</span></span>
- <span data-ttu-id="2cbe4-470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-470">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-471">'Razor'</span></span>
- <span data-ttu-id="2cbe4-472">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-473">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-474">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-475">'Identity'</span></span>
- <span data-ttu-id="2cbe4-476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-476">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-477">'Razor'</span></span>
- <span data-ttu-id="2cbe4-478">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-478">'SignalR' uid:</span></span> 

<span data-ttu-id="2cbe4-479">----------------------------- | título do---: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-479">----------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-480">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-481">'Identity'</span></span>
- <span data-ttu-id="2cbe4-482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-482">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-483">'Razor'</span></span>
- <span data-ttu-id="2cbe4-484">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-484">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-485">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-486">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-487">'Identity'</span></span>
- <span data-ttu-id="2cbe4-488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-488">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-489">'Razor'</span></span>
- <span data-ttu-id="2cbe4-490">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-490">'SignalR' uid:</span></span> 

<span data-ttu-id="2cbe4-491">---- | | [Idade](https://tools.ietf.org/html/rfc7234#section-5.1) | Uma estimativa da quantidade de tempo em segundos desde que a resposta foi gerada ou validada com êxito no servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> <span data-ttu-id="2cbe4-492">| | [Expira](https://tools.ietf.org/html/rfc7234#section-5.3) | O tempo após o qual a resposta é considerada obsoleta.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-492">| | [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | The time after which the response is considered stale.</span></span> <span data-ttu-id="2cbe4-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4) | Existe para compatibilidade com versões anteriores com caches HTTP/1.0 para comportamento de configuração `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="2cbe4-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="2cbe4-494">Se o `Cache-Control` cabeçalho estiver presente, o `Pragma` cabeçalho será ignorado.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-494">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> <span data-ttu-id="2cbe4-495">| | [Variar](https://tools.ietf.org/html/rfc7231#section-7.1.4) | Especifica que uma resposta armazenada em cache não deve ser enviada, a menos que todos os `Vary` campos de cabeçalho correspondam tanto à solicitação original da resposta em cache quanto à nova solicitação.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="2cbe4-496">Os aspectos do cache baseado em HTTP solicitam diretivas de controle de cache</span><span class="sxs-lookup"><span data-stu-id="2cbe4-496">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="2cbe4-497">A [especificação de cache HTTP 1,1 para o cabeçalho de controle de cache](https://tools.ietf.org/html/rfc7234#section-5.2) requer um cache para honrar um `Cache-Control` cabeçalho válido enviado pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-497">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="2cbe4-498">Um cliente pode fazer solicitações com um `no-cache` valor de cabeçalho e forçar o servidor a gerar uma nova resposta para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-498">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="2cbe4-499">Sempre respeitando `Cache-Control` os cabeçalhos de solicitação do cliente faz sentido se você considerar a meta do cache http.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-499">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="2cbe4-500">Sob a especificação oficial, o Caching destina-se a reduzir a latência e a sobrecarga de rede de satisfazer solicitações em uma rede de clientes, proxies e servidores.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-500">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="2cbe4-501">Não é necessariamente uma maneira de controlar a carga em um servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-501">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="2cbe4-502">Não há nenhum controle de desenvolvedor sobre esse comportamento de cache ao usar o [middleware de cache de resposta](xref:performance/caching/middleware) porque o middleware segue a especificação de cache oficial.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-502">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="2cbe4-503">Os [aprimoramentos planejados para o middleware](https://github.com/dotnet/AspNetCore/issues/2612) são uma oportunidade de configurar o middleware para ignorar o cabeçalho de uma solicitação `Cache-Control` ao decidir atender a uma resposta armazenada em cache.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-503">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="2cbe4-504">Os aprimoramentos planejados oferecem uma oportunidade de controlar melhor a carga do servidor.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-504">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="2cbe4-505">Outra tecnologia de cache no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2cbe4-505">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="2cbe4-506">Cache in-memory</span><span class="sxs-lookup"><span data-stu-id="2cbe4-506">In-memory caching</span></span>

<span data-ttu-id="2cbe4-507">O cache na memória usa a memória do servidor para armazenar dados armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-507">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="2cbe4-508">Esse tipo de cache é adequado para um único servidor ou para vários servidores usando *sessões adesivas*.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-508">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="2cbe4-509">Sessões adesivas significa que as solicitações de um cliente são sempre roteadas para o mesmo servidor para processamento.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-509">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="2cbe4-510">Para obter mais informações, consulte <xref:performance/caching/memory>.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-510">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="2cbe4-511">Cache distribuído</span><span class="sxs-lookup"><span data-stu-id="2cbe4-511">Distributed Cache</span></span>

<span data-ttu-id="2cbe4-512">Use um cache distribuído para armazenar dados na memória quando o aplicativo estiver hospedado em uma nuvem ou farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-512">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="2cbe4-513">O cache é compartilhado entre os servidores que processam solicitações.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-513">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="2cbe4-514">Um cliente pode enviar uma solicitação que é tratada por qualquer servidor no grupo se os dados armazenados em cache para o cliente estiverem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-514">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="2cbe4-515">ASP.NET Core funciona com caches distribuídos SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)e [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .</span><span class="sxs-lookup"><span data-stu-id="2cbe4-515">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="2cbe4-516">Para obter mais informações, consulte <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-516">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="2cbe4-517">Auxiliar de marca de cache</span><span class="sxs-lookup"><span data-stu-id="2cbe4-517">Cache Tag Helper</span></span>

<span data-ttu-id="2cbe4-518">Armazene em cache o conteúdo de uma exibição ou Razor página MVC com o auxiliar de marca de cache.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-518">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="2cbe4-519">O auxiliar de marca de cache usa o cache na memória para armazenar dados.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-519">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="2cbe4-520">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-520">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="2cbe4-521">Auxiliar de Marca de Cache Distribuído</span><span class="sxs-lookup"><span data-stu-id="2cbe4-521">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="2cbe4-522">Armazene em cache o conteúdo de uma exibição ou página do MVC Razor em cenários de nuvem distribuída ou Web farm com o auxiliar de marca de cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-522">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="2cbe4-523">O auxiliar de marca de cache distribuído usa SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)ou [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) para armazenar dados.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-523">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="2cbe4-524">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-524">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="2cbe4-525">Atributo ResponseCache</span><span class="sxs-lookup"><span data-stu-id="2cbe4-525">ResponseCache attribute</span></span>

<span data-ttu-id="2cbe4-526">O <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> especifica os parâmetros necessários para definir os cabeçalhos apropriados no cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-526">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="2cbe4-527">Desabilite o cache para conteúdo que contém informações para clientes autenticados.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-527">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="2cbe4-528">O Caching só deve ser habilitado para conteúdo que não seja alterado com base na identidade de um usuário ou se um usuário estiver conectado.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-528">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="2cbe4-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>varia a resposta armazenada pelos valores da lista de chaves de consulta fornecida.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="2cbe4-530">Quando um único valor de `*` é fornecido, o middleware varia as respostas por todos os parâmetros de cadeia de caracteres de consulta de solicitação.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-530">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="2cbe4-531">O [middleware de cache de resposta](xref:performance/caching/middleware) deve estar habilitado para definir a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> propriedade.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-531">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="2cbe4-532">Caso contrário, uma exceção de tempo de execução será lançada.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-532">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="2cbe4-533">Não há um cabeçalho HTTP correspondente para a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> propriedade.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-533">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="2cbe4-534">A propriedade é um recurso HTTP manipulado pelo middleware de cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-534">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="2cbe4-535">Para que o middleware atenda a uma resposta armazenada em cache, a cadeia de caracteres de consulta e o valor da cadeia de caracteres de consulta devem corresponder a uma solicitação anterior.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-535">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="2cbe4-536">Por exemplo, considere a sequência de solicitações e os resultados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-536">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="2cbe4-537">Solicitação</span><span class="sxs-lookup"><span data-stu-id="2cbe4-537">Request</span></span>                          | <span data-ttu-id="2cbe4-538">Resultado</span><span class="sxs-lookup"><span data-stu-id="2cbe4-538">Result</span></span>                    |
| ---
<span data-ttu-id="2cbe4-539">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-540">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-541">'Identity'</span></span>
- <span data-ttu-id="2cbe4-542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-542">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-543">'Razor'</span></span>
- <span data-ttu-id="2cbe4-544">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-545">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-546">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-547">'Identity'</span></span>
- <span data-ttu-id="2cbe4-548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-548">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-549">'Razor'</span></span>
- <span data-ttu-id="2cbe4-550">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-551">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-552">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-553">'Identity'</span></span>
- <span data-ttu-id="2cbe4-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-555">'Razor'</span></span>
- <span data-ttu-id="2cbe4-556">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-557">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-558">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-559">'Identity'</span></span>
- <span data-ttu-id="2cbe4-560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-560">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-561">'Razor'</span></span>
- <span data-ttu-id="2cbe4-562">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-563">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-564">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-565">'Identity'</span></span>
- <span data-ttu-id="2cbe4-566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-566">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-567">'Razor'</span></span>
- <span data-ttu-id="2cbe4-568">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-569">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-570">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-571">'Identity'</span></span>
- <span data-ttu-id="2cbe4-572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-572">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-573">'Razor'</span></span>
- <span data-ttu-id="2cbe4-574">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-575">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-576">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-577">'Identity'</span></span>
- <span data-ttu-id="2cbe4-578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-578">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-579">'Razor'</span></span>
- <span data-ttu-id="2cbe4-580">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-581">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-582">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-583">'Identity'</span></span>
- <span data-ttu-id="2cbe4-584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-584">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-585">'Razor'</span></span>
- <span data-ttu-id="2cbe4-586">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-586">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-587">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-588">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-589">'Identity'</span></span>
- <span data-ttu-id="2cbe4-590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-590">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-591">'Razor'</span></span>
- <span data-ttu-id="2cbe4-592">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-592">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-593">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-594">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-595">'Identity'</span></span>
- <span data-ttu-id="2cbe4-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-597">'Razor'</span></span>
- <span data-ttu-id="2cbe4-598">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-599">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-600">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-600">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-601">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-601">'Identity'</span></span>
- <span data-ttu-id="2cbe4-602">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-602">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-603">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-603">'Razor'</span></span>
- <span data-ttu-id="2cbe4-604">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-604">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-605">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-606">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-606">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-607">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-607">'Identity'</span></span>
- <span data-ttu-id="2cbe4-608">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-608">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-609">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-609">'Razor'</span></span>
- <span data-ttu-id="2cbe4-610">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-610">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-611">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-612">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-612">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-613">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-613">'Identity'</span></span>
- <span data-ttu-id="2cbe4-614">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-614">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-615">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-615">'Razor'</span></span>
- <span data-ttu-id="2cbe4-616">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-616">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-617">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-618">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-618">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-619">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-619">'Identity'</span></span>
- <span data-ttu-id="2cbe4-620">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-620">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-621">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-621">'Razor'</span></span>
- <span data-ttu-id="2cbe4-622">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-622">'SignalR' uid:</span></span> 

<span data-ttu-id="2cbe4-623">---------------- | título do---: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-623">---------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-624">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-624">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-625">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-625">'Identity'</span></span>
- <span data-ttu-id="2cbe4-626">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-626">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-627">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-627">'Razor'</span></span>
- <span data-ttu-id="2cbe4-628">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-628">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-629">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-630">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-630">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-631">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-631">'Identity'</span></span>
- <span data-ttu-id="2cbe4-632">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-632">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-633">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-633">'Razor'</span></span>
- <span data-ttu-id="2cbe4-634">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-634">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-635">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-636">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-637">'Identity'</span></span>
- <span data-ttu-id="2cbe4-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-639">'Razor'</span></span>
- <span data-ttu-id="2cbe4-640">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-641">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-642">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-642">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-643">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-643">'Identity'</span></span>
- <span data-ttu-id="2cbe4-644">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-644">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-645">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-645">'Razor'</span></span>
- <span data-ttu-id="2cbe4-646">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-646">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-647">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-648">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-648">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-649">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-649">'Identity'</span></span>
- <span data-ttu-id="2cbe4-650">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-650">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-651">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-651">'Razor'</span></span>
- <span data-ttu-id="2cbe4-652">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-652">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-653">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-654">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-654">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-655">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-655">'Identity'</span></span>
- <span data-ttu-id="2cbe4-656">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-656">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-657">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-657">'Razor'</span></span>
- <span data-ttu-id="2cbe4-658">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-658">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-659">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-660">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-660">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-661">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-661">'Identity'</span></span>
- <span data-ttu-id="2cbe4-662">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-662">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-663">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-663">'Razor'</span></span>
- <span data-ttu-id="2cbe4-664">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-664">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-665">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-666">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-666">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-667">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-667">'Identity'</span></span>
- <span data-ttu-id="2cbe4-668">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-668">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-669">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-669">'Razor'</span></span>
- <span data-ttu-id="2cbe4-670">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-670">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-671">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-672">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-672">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-673">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-673">'Identity'</span></span>
- <span data-ttu-id="2cbe4-674">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-674">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-675">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-675">'Razor'</span></span>
- <span data-ttu-id="2cbe4-676">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-676">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cbe4-677">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cbe4-678">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-678">'Blazor'</span></span>
- <span data-ttu-id="2cbe4-679">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-679">'Identity'</span></span>
- <span data-ttu-id="2cbe4-680">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-680">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cbe4-681">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cbe4-681">'Razor'</span></span>
- <span data-ttu-id="2cbe4-682">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="2cbe4-682">'SignalR' uid:</span></span> 

<span data-ttu-id="2cbe4-683">------------- | | `http://example.com?key1=value1` | Retornado do servidor.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-683">------------- | | `http://example.com?key1=value1` | Returned from the server.</span></span> <span data-ttu-id="2cbe4-684">| | `http://example.com?key1=value1` | Retornado do middleware.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-684">| | `http://example.com?key1=value1` | Returned from middleware.</span></span> <span data-ttu-id="2cbe4-685">| | `http://example.com?key1=value2` | Retornado do servidor.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-685">| | `http://example.com?key1=value2` | Returned from the server.</span></span> |

<span data-ttu-id="2cbe4-686">A primeira solicitação é retornada pelo servidor e armazenada em cache no middleware.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-686">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="2cbe4-687">A segunda solicitação é retornada pelo middleware porque a cadeia de caracteres de consulta corresponde à solicitação anterior.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-687">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="2cbe4-688">A terceira solicitação não está no cache do middleware porque o valor da cadeia de caracteres de consulta não corresponde a uma solicitação anterior.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-688">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="2cbe4-689">O <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> é usado para configurar e criar (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> ) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` .</span><span class="sxs-lookup"><span data-stu-id="2cbe4-689">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="2cbe4-690">O `ResponseCacheFilter` executa o trabalho de atualizar os cabeçalhos HTTP e os recursos apropriados da resposta.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-690">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="2cbe4-691">O filtro:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-691">The filter:</span></span>

* <span data-ttu-id="2cbe4-692">Remove todos os cabeçalhos existentes para `Vary` , `Cache-Control` e `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="2cbe4-692">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="2cbe4-693">Grava os cabeçalhos apropriados com base nas propriedades definidas no <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .</span><span class="sxs-lookup"><span data-stu-id="2cbe4-693">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="2cbe4-694">Atualiza o recurso HTTP de resposta do cache se <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> estiver definido.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-694">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="2cbe4-695">Varia</span><span class="sxs-lookup"><span data-stu-id="2cbe4-695">Vary</span></span>

<span data-ttu-id="2cbe4-696">Esse cabeçalho só é gravado quando a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> propriedade é definida.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-696">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="2cbe4-697">A propriedade definida como o `Vary` valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-697">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="2cbe4-698">O exemplo a seguir usa a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> Propriedade:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-698">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="2cbe4-699">Usando o aplicativo de exemplo, exiba os cabeçalhos de resposta com as ferramentas de rede do navegador.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-699">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="2cbe4-700">Os cabeçalhos de resposta a seguir são enviados com a resposta da página Cache1:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-700">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="2cbe4-701">NoStore e Location. None</span><span class="sxs-lookup"><span data-stu-id="2cbe4-701">NoStore and Location.None</span></span>

<span data-ttu-id="2cbe4-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>Substitui a maioria das outras propriedades.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="2cbe4-703">Quando essa propriedade é definida como `true` , o `Cache-Control` cabeçalho é definido como `no-store` .</span><span class="sxs-lookup"><span data-stu-id="2cbe4-703">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="2cbe4-704">Se <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> é definido como `None` :</span><span class="sxs-lookup"><span data-stu-id="2cbe4-704">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="2cbe4-705">`Cache-Control` está definido como `no-store,no-cache`.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-705">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="2cbe4-706">`Pragma` está definido como `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-706">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="2cbe4-707">Se <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> é `false` e <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> é `None` , `Cache-Control` e `Pragma` são definidos como `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="2cbe4-707">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="2cbe4-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>normalmente é definido como `true` para páginas de erro.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="2cbe4-709">A página Cache2 no aplicativo de exemplo produz cabeçalhos de resposta que instruem o cliente a não armazenar a resposta.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-709">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="2cbe4-710">O aplicativo de exemplo retorna a página Cache2 com os seguintes cabeçalhos:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-710">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="2cbe4-711">Localização e duração</span><span class="sxs-lookup"><span data-stu-id="2cbe4-711">Location and Duration</span></span>

<span data-ttu-id="2cbe4-712">Para habilitar o Caching, o <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> deve ser definido como um valor positivo e <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> deve ser `Any` (o padrão) ou `Client` .</span><span class="sxs-lookup"><span data-stu-id="2cbe4-712">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="2cbe4-713">A estrutura define o `Cache-Control` cabeçalho para o valor de local seguido pelo `max-age` da resposta.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-713">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="2cbe4-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>as opções de `Any` e são `Client` transladadas em `Cache-Control` valores de cabeçalho de `public` e `private` , respectivamente.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="2cbe4-715">Conforme observado na seção [NoStore e Location. None](#nostore-and-locationnone) , a configuração <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> para `None` define os `Cache-Control` `Pragma` cabeçalhos e como `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="2cbe4-715">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="2cbe4-716">`Location.Any`( `Cache-Control` definido como `public` ) indica que o *cliente ou qualquer proxy intermediário* pode armazenar em cache o valor, incluindo o [middleware de cache de resposta](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="2cbe4-716">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="2cbe4-717">`Location.Client`( `Cache-Control` definido como `private` ) indica que *somente o cliente* pode armazenar em cache o valor.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-717">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="2cbe4-718">Nenhum cache intermediário deve armazenar em cache o valor, incluindo o [middleware de cache de resposta](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="2cbe4-718">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="2cbe4-719">Os cabeçalhos de controle de cache simplesmente fornecem diretrizes para clientes e proxies intermediários quando e como armazenar em cache as respostas.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-719">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="2cbe4-720">Não há nenhuma garantia de que os clientes e proxies respeitarão a [especificação de cache HTTP 1,1](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="2cbe4-720">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="2cbe4-721">O [middleware de cache de resposta](xref:performance/caching/middleware) sempre segue as regras de cache apresentadas pela especificação.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-721">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="2cbe4-722">O exemplo a seguir mostra o modelo de página Cache3 do aplicativo de exemplo e os cabeçalhos produzidos pela configuração <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> e saindo do <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> valor padrão:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-722">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="2cbe4-723">O aplicativo de exemplo retorna a página Cache3 com o seguinte cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-723">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="2cbe4-724">Perfis de cache</span><span class="sxs-lookup"><span data-stu-id="2cbe4-724">Cache profiles</span></span>

<span data-ttu-id="2cbe4-725">Em vez de duplicar as configurações de cache de resposta em vários atributos de ação do controlador, os perfis de cache podem ser configurados como opções ao configurar MVC/ Razor páginas no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2cbe4-725">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2cbe4-726">Os valores encontrados em um perfil de cache referenciado são usados como os padrões pelo <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> e são substituídos por todas as propriedades especificadas no atributo.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-726">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="2cbe4-727">Configure um perfil de cache.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-727">Set up a cache profile.</span></span> <span data-ttu-id="2cbe4-728">O exemplo a seguir mostra um perfil de cache de 30 segundos no aplicativo de exemplo `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2cbe4-728">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="2cbe4-729">O modelo de página Cache4 do aplicativo de exemplo faz referência ao `Default30` perfil de cache:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-729">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="2cbe4-730">O <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> pode ser aplicado a:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-730">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* Razor<span data-ttu-id="2cbe4-731">Manipuladores de página (classes): atributos não podem ser aplicados a métodos de manipulador.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-731"> Page handlers (classes): Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="2cbe4-732">Controladores MVC (classes).</span><span class="sxs-lookup"><span data-stu-id="2cbe4-732">MVC controllers (classes).</span></span>
* <span data-ttu-id="2cbe4-733">Ações do MVC (métodos): atributos de nível de método substituem as configurações especificadas em atributos de nível de classe.</span><span class="sxs-lookup"><span data-stu-id="2cbe4-733">MVC actions (methods): Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="2cbe4-734">O cabeçalho resultante aplicado à resposta da página Cache4 pelo `Default30` perfil de cache:</span><span class="sxs-lookup"><span data-stu-id="2cbe4-734">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="2cbe4-735">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2cbe4-735">Additional resources</span></span>

* [<span data-ttu-id="2cbe4-736">Armazenando respostas em caches</span><span class="sxs-lookup"><span data-stu-id="2cbe4-736">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="2cbe4-737">Controle de cache</span><span class="sxs-lookup"><span data-stu-id="2cbe4-737">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

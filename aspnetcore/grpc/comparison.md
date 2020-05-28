---
<span data-ttu-id="5317d-101">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-102">'Blazor'</span></span>
- <span data-ttu-id="5317d-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-103">'Identity'</span></span>
- <span data-ttu-id="5317d-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-105">'Razor'</span></span>
- <span data-ttu-id="5317d-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-106">'SignalR' uid:</span></span> 

---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="5317d-107">Comparar serviços gRPC com APIs HTTP</span><span class="sxs-lookup"><span data-stu-id="5317d-107">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="5317d-108">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5317d-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5317d-109">Este artigo explica como os [Serviços gRPCs](https://grpc.io/docs/guides/) se comparam com APIs http com JSON (incluindo ASP.NET Core [APIs Web](xref:web-api/index)).</span><span class="sxs-lookup"><span data-stu-id="5317d-109">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="5317d-110">A tecnologia usada para fornecer uma API para seu aplicativo é uma opção importante, e o gRPC oferece benefícios exclusivos em comparação com as APIs HTTP.</span><span class="sxs-lookup"><span data-stu-id="5317d-110">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="5317d-111">Este artigo discute os pontos fortes e fracos do gRPC e recomenda cenários para usar o gRPC em relação a outras tecnologias.</span><span class="sxs-lookup"><span data-stu-id="5317d-111">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="5317d-112">Comparação de alto nível</span><span class="sxs-lookup"><span data-stu-id="5317d-112">High-level comparison</span></span>

<span data-ttu-id="5317d-113">A tabela a seguir oferece uma comparação de alto nível dos recursos entre as APIs gRPC e HTTP com JSON.</span><span class="sxs-lookup"><span data-stu-id="5317d-113">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="5317d-114">Recurso</span><span class="sxs-lookup"><span data-stu-id="5317d-114">Feature</span></span>          | <span data-ttu-id="5317d-115">gRPC</span><span class="sxs-lookup"><span data-stu-id="5317d-115">gRPC</span></span>                                               | <span data-ttu-id="5317d-116">APIs HTTP com JSON</span><span class="sxs-lookup"><span data-stu-id="5317d-116">HTTP APIs with JSON</span></span>           |
| ---
<span data-ttu-id="5317d-117">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-118">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-118">'Blazor'</span></span>
- <span data-ttu-id="5317d-119">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-119">'Identity'</span></span>
- <span data-ttu-id="5317d-120">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-120">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-121">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-121">'Razor'</span></span>
- <span data-ttu-id="5317d-122">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-122">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-123">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-124">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-124">'Blazor'</span></span>
- <span data-ttu-id="5317d-125">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-125">'Identity'</span></span>
- <span data-ttu-id="5317d-126">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-126">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-127">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-127">'Razor'</span></span>
- <span data-ttu-id="5317d-128">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-128">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-129">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-130">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-130">'Blazor'</span></span>
- <span data-ttu-id="5317d-131">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-131">'Identity'</span></span>
- <span data-ttu-id="5317d-132">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-132">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-133">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-133">'Razor'</span></span>
- <span data-ttu-id="5317d-134">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-134">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-135">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-136">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-136">'Blazor'</span></span>
- <span data-ttu-id="5317d-137">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-137">'Identity'</span></span>
- <span data-ttu-id="5317d-138">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-138">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-139">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-139">'Razor'</span></span>
- <span data-ttu-id="5317d-140">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-140">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-141">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-142">'Blazor'</span></span>
- <span data-ttu-id="5317d-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-143">'Identity'</span></span>
- <span data-ttu-id="5317d-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-145">'Razor'</span></span>
- <span data-ttu-id="5317d-146">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-146">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-147">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-148">'Blazor'</span></span>
- <span data-ttu-id="5317d-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-149">'Identity'</span></span>
- <span data-ttu-id="5317d-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-151">'Razor'</span></span>
- <span data-ttu-id="5317d-152">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-152">'SignalR' uid:</span></span> 

<span data-ttu-id="5317d-153">-------- | título do---: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-154">'Blazor'</span></span>
- <span data-ttu-id="5317d-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-155">'Identity'</span></span>
- <span data-ttu-id="5317d-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-157">'Razor'</span></span>
- <span data-ttu-id="5317d-158">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-159">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-160">'Blazor'</span></span>
- <span data-ttu-id="5317d-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-161">'Identity'</span></span>
- <span data-ttu-id="5317d-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-163">'Razor'</span></span>
- <span data-ttu-id="5317d-164">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-165">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-166">'Blazor'</span></span>
- <span data-ttu-id="5317d-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-167">'Identity'</span></span>
- <span data-ttu-id="5317d-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-169">'Razor'</span></span>
- <span data-ttu-id="5317d-170">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-171">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-172">'Blazor'</span></span>
- <span data-ttu-id="5317d-173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-173">'Identity'</span></span>
- <span data-ttu-id="5317d-174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-174">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-175">'Razor'</span></span>
- <span data-ttu-id="5317d-176">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-177">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-178">'Blazor'</span></span>
- <span data-ttu-id="5317d-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-179">'Identity'</span></span>
- <span data-ttu-id="5317d-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-181">'Razor'</span></span>
- <span data-ttu-id="5317d-182">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-183">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-184">'Blazor'</span></span>
- <span data-ttu-id="5317d-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-185">'Identity'</span></span>
- <span data-ttu-id="5317d-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-187">'Razor'</span></span>
- <span data-ttu-id="5317d-188">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-189">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-190">'Blazor'</span></span>
- <span data-ttu-id="5317d-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-191">'Identity'</span></span>
- <span data-ttu-id="5317d-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-193">'Razor'</span></span>
- <span data-ttu-id="5317d-194">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-195">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-196">'Blazor'</span></span>
- <span data-ttu-id="5317d-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-197">'Identity'</span></span>
- <span data-ttu-id="5317d-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-199">'Razor'</span></span>
- <span data-ttu-id="5317d-200">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-201">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-202">'Blazor'</span></span>
- <span data-ttu-id="5317d-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-203">'Identity'</span></span>
- <span data-ttu-id="5317d-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-205">'Razor'</span></span>
- <span data-ttu-id="5317d-206">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-207">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-208">'Blazor'</span></span>
- <span data-ttu-id="5317d-209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-209">'Identity'</span></span>
- <span data-ttu-id="5317d-210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-210">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-211">'Razor'</span></span>
- <span data-ttu-id="5317d-212">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-213">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-214">'Blazor'</span></span>
- <span data-ttu-id="5317d-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-215">'Identity'</span></span>
- <span data-ttu-id="5317d-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-217">'Razor'</span></span>
- <span data-ttu-id="5317d-218">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-219">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-219">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-220">'Blazor'</span></span>
- <span data-ttu-id="5317d-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-221">'Identity'</span></span>
- <span data-ttu-id="5317d-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-223">'Razor'</span></span>
- <span data-ttu-id="5317d-224">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-225">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-225">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-226">'Blazor'</span></span>
- <span data-ttu-id="5317d-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-227">'Identity'</span></span>
- <span data-ttu-id="5317d-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-229">'Razor'</span></span>
- <span data-ttu-id="5317d-230">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-231">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-231">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-232">'Blazor'</span></span>
- <span data-ttu-id="5317d-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-233">'Identity'</span></span>
- <span data-ttu-id="5317d-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-235">'Razor'</span></span>
- <span data-ttu-id="5317d-236">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-237">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-237">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-238">'Blazor'</span></span>
- <span data-ttu-id="5317d-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-239">'Identity'</span></span>
- <span data-ttu-id="5317d-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-241">'Razor'</span></span>
- <span data-ttu-id="5317d-242">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-243">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-243">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-244">'Blazor'</span></span>
- <span data-ttu-id="5317d-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-245">'Identity'</span></span>
- <span data-ttu-id="5317d-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-247">'Razor'</span></span>
- <span data-ttu-id="5317d-248">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-249">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-249">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-250">'Blazor'</span></span>
- <span data-ttu-id="5317d-251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-251">'Identity'</span></span>
- <span data-ttu-id="5317d-252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-252">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-253">'Razor'</span></span>
- <span data-ttu-id="5317d-254">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-255">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-255">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-256">'Blazor'</span></span>
- <span data-ttu-id="5317d-257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-257">'Identity'</span></span>
- <span data-ttu-id="5317d-258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-258">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-259">'Razor'</span></span>
- <span data-ttu-id="5317d-260">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-261">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-261">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-262">'Blazor'</span></span>
- <span data-ttu-id="5317d-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-263">'Identity'</span></span>
- <span data-ttu-id="5317d-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-265">'Razor'</span></span>
- <span data-ttu-id="5317d-266">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-267">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-267">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-268">'Blazor'</span></span>
- <span data-ttu-id="5317d-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-269">'Identity'</span></span>
- <span data-ttu-id="5317d-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-271">'Razor'</span></span>
- <span data-ttu-id="5317d-272">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-273">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-273">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-274">'Blazor'</span></span>
- <span data-ttu-id="5317d-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-275">'Identity'</span></span>
- <span data-ttu-id="5317d-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-277">'Razor'</span></span>
- <span data-ttu-id="5317d-278">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-279">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-279">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-280">'Blazor'</span></span>
- <span data-ttu-id="5317d-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-281">'Identity'</span></span>
- <span data-ttu-id="5317d-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-283">'Razor'</span></span>
- <span data-ttu-id="5317d-284">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-285">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-285">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-286">'Blazor'</span></span>
- <span data-ttu-id="5317d-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-287">'Identity'</span></span>
- <span data-ttu-id="5317d-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-289">'Razor'</span></span>
- <span data-ttu-id="5317d-290">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-290">'SignalR' uid:</span></span> 

<span data-ttu-id="5317d-291">------------------------- | título do---: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-291">------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-292">'Blazor'</span></span>
- <span data-ttu-id="5317d-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-293">'Identity'</span></span>
- <span data-ttu-id="5317d-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-295">'Razor'</span></span>
- <span data-ttu-id="5317d-296">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-297">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-297">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-298">'Blazor'</span></span>
- <span data-ttu-id="5317d-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-299">'Identity'</span></span>
- <span data-ttu-id="5317d-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-301">'Razor'</span></span>
- <span data-ttu-id="5317d-302">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-303">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-303">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-304">'Blazor'</span></span>
- <span data-ttu-id="5317d-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-305">'Identity'</span></span>
- <span data-ttu-id="5317d-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-307">'Razor'</span></span>
- <span data-ttu-id="5317d-308">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-309">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-309">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-310">'Blazor'</span></span>
- <span data-ttu-id="5317d-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-311">'Identity'</span></span>
- <span data-ttu-id="5317d-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-313">'Razor'</span></span>
- <span data-ttu-id="5317d-314">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-315">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-315">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-316">'Blazor'</span></span>
- <span data-ttu-id="5317d-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-317">'Identity'</span></span>
- <span data-ttu-id="5317d-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-319">'Razor'</span></span>
- <span data-ttu-id="5317d-320">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-321">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-321">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-322">'Blazor'</span></span>
- <span data-ttu-id="5317d-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-323">'Identity'</span></span>
- <span data-ttu-id="5317d-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-325">'Razor'</span></span>
- <span data-ttu-id="5317d-326">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-327">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-327">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-328">'Blazor'</span></span>
- <span data-ttu-id="5317d-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-329">'Identity'</span></span>
- <span data-ttu-id="5317d-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-331">'Razor'</span></span>
- <span data-ttu-id="5317d-332">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-333">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-333">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-334">'Blazor'</span></span>
- <span data-ttu-id="5317d-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-335">'Identity'</span></span>
- <span data-ttu-id="5317d-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-337">'Razor'</span></span>
- <span data-ttu-id="5317d-338">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-339">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-339">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-340">'Blazor'</span></span>
- <span data-ttu-id="5317d-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-341">'Identity'</span></span>
- <span data-ttu-id="5317d-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-343">'Razor'</span></span>
- <span data-ttu-id="5317d-344">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-345">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-345">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-346">'Blazor'</span></span>
- <span data-ttu-id="5317d-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-347">'Identity'</span></span>
- <span data-ttu-id="5317d-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-349">'Razor'</span></span>
- <span data-ttu-id="5317d-350">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-351">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-351">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-352">'Blazor'</span></span>
- <span data-ttu-id="5317d-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-353">'Identity'</span></span>
- <span data-ttu-id="5317d-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-355">'Razor'</span></span>
- <span data-ttu-id="5317d-356">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5317d-357">Título: autor: Descrição: monikerRange: MS. Author: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5317d-357">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5317d-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5317d-358">'Blazor'</span></span>
- <span data-ttu-id="5317d-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5317d-359">'Identity'</span></span>
- <span data-ttu-id="5317d-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5317d-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="5317d-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5317d-361">'Razor'</span></span>
- <span data-ttu-id="5317d-362">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5317d-362">'SignalR' uid:</span></span> 

<span data-ttu-id="5317d-363">--------------- | | Contrato | Necessário (*. proto*) | Opcional (OpenAPI) | | Protocolo | HTTP/2 | HTTP | | Carga | [Protobuf (pequeno, binário)](#performance) | JSON (grande, legível por humanos) | | Prescriptiveness | [Especificação estrita](#strict-specification) | Cancelar.</span><span class="sxs-lookup"><span data-stu-id="5317d-363">--------------- | | Contract         | Required (*.proto*)                                | Optional (OpenAPI)            | | Protocol         | HTTP/2                                             | HTTP                          | | Payload          | [Protobuf (small, binary)](#performance)           | JSON (large, human readable)  | | Prescriptiveness | [Strict specification](#strict-specification)      | Loose.</span></span> <span data-ttu-id="5317d-364">Qualquer HTTP é válido.</span><span class="sxs-lookup"><span data-stu-id="5317d-364">Any HTTP is valid.</span></span>     <span data-ttu-id="5317d-365">| | Streaming | [Cliente, servidor, bidirecional](#streaming) | Cliente, servidor | | Suporte a navegador | [Não (requer grpc-Web)](#limited-browser-support) | Sim | | Segurança | Transporte (TLS) | Transporte (TLS) | | Geração de código de cliente | [Sim](#code-generation) | OpenAPI + ferramentas de terceiros |</span><span class="sxs-lookup"><span data-stu-id="5317d-365">| | Streaming        | [Client, server, bi-directional](#streaming)       | Client, server                | | Browser support  | [No (requires grpc-web)](#limited-browser-support) | Yes                           | | Security         | Transport (TLS)                                    | Transport (TLS)               | | Client code-generation | [Yes](#code-generation)                      | OpenAPI + third-party tooling |</span></span>

## <a name="grpc-strengths"></a><span data-ttu-id="5317d-366">forças gRPCs</span><span class="sxs-lookup"><span data-stu-id="5317d-366">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="5317d-367">Desempenho</span><span class="sxs-lookup"><span data-stu-id="5317d-367">Performance</span></span>

<span data-ttu-id="5317d-368">as mensagens gRPC são serializadas usando [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), um formato de mensagem binária eficiente.</span><span class="sxs-lookup"><span data-stu-id="5317d-368">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="5317d-369">O Protobuf serializa muito rapidamente no servidor e no cliente.</span><span class="sxs-lookup"><span data-stu-id="5317d-369">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="5317d-370">A serialização Protobuf resulta em cargas de mensagens pequenas, importantes em cenários de largura de banda limitada, como aplicativos móveis.</span><span class="sxs-lookup"><span data-stu-id="5317d-370">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="5317d-371">o gRPC foi projetado para HTTP/2, uma revisão importante do HTTP que fornece benefícios de desempenho significativos em relação ao HTTP 1. x:</span><span class="sxs-lookup"><span data-stu-id="5317d-371">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="5317d-372">Enquadramento e compactação binários.</span><span class="sxs-lookup"><span data-stu-id="5317d-372">Binary framing and compression.</span></span> <span data-ttu-id="5317d-373">O protocolo HTTP/2 é compacto e eficiente no envio e no recebimento.</span><span class="sxs-lookup"><span data-stu-id="5317d-373">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="5317d-374">Multiplexação de várias chamadas HTTP/2 em uma única conexão TCP.</span><span class="sxs-lookup"><span data-stu-id="5317d-374">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="5317d-375">A multiplexação elimina o [bloqueio de cabeçalho de linha](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span><span class="sxs-lookup"><span data-stu-id="5317d-375">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="5317d-376">HTTP/2 não é exclusivo para gRPC.</span><span class="sxs-lookup"><span data-stu-id="5317d-376">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="5317d-377">Muitos tipos de solicitação, incluindo APIs HTTP com JSON, podem usar HTTP/2 e se beneficiar de suas melhorias de desempenho.</span><span class="sxs-lookup"><span data-stu-id="5317d-377">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="5317d-378">Geração de código</span><span class="sxs-lookup"><span data-stu-id="5317d-378">Code generation</span></span>

<span data-ttu-id="5317d-379">Todas as estruturas gRPC fornecem suporte de primeira classe para geração de código.</span><span class="sxs-lookup"><span data-stu-id="5317d-379">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="5317d-380">Um arquivo de núcleo para o desenvolvimento de gRPC é o [arquivo. proto](https://developers.google.com/protocol-buffers/docs/proto3), que define o contrato de serviços e mensagens do gRPC.</span><span class="sxs-lookup"><span data-stu-id="5317d-380">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="5317d-381">Desse arquivo, as estruturas do gRPC de código gerarão uma classe base de serviço, mensagens e um cliente completo.</span><span class="sxs-lookup"><span data-stu-id="5317d-381">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="5317d-382">Ao compartilhar o arquivo *. proto* entre o servidor e o cliente, as mensagens e o código do cliente podem ser gerados de ponta a ponta.</span><span class="sxs-lookup"><span data-stu-id="5317d-382">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="5317d-383">A geração de código do cliente elimina a duplicação de mensagens no cliente e no servidor e cria um cliente fortemente tipado para você.</span><span class="sxs-lookup"><span data-stu-id="5317d-383">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="5317d-384">Não ter de escrever um cliente economiza um tempo de desenvolvimento significativo em aplicativos com muitos serviços.</span><span class="sxs-lookup"><span data-stu-id="5317d-384">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="5317d-385">Especificação estrita</span><span class="sxs-lookup"><span data-stu-id="5317d-385">Strict specification</span></span>

<span data-ttu-id="5317d-386">Não existe uma especificação formal para a API HTTP com JSON.</span><span class="sxs-lookup"><span data-stu-id="5317d-386">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="5317d-387">Os desenvolvedores debaterão o melhor formato de URLs, verbos HTTP e códigos de resposta.</span><span class="sxs-lookup"><span data-stu-id="5317d-387">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="5317d-388">A [especificação gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) é prescritiva sobre o formato que um serviço gRPC deve seguir.</span><span class="sxs-lookup"><span data-stu-id="5317d-388">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="5317d-389">o gRPC elimina o debate e economiza tempo para desenvolvedores porque o gRPC é consistente entre plataformas e implementações.</span><span class="sxs-lookup"><span data-stu-id="5317d-389">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="5317d-390">Streaming</span><span class="sxs-lookup"><span data-stu-id="5317d-390">Streaming</span></span>

<span data-ttu-id="5317d-391">O HTTP/2 fornece uma base para fluxos de comunicação de longa duração e em tempo real.</span><span class="sxs-lookup"><span data-stu-id="5317d-391">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="5317d-392">o gRPC fornece suporte de primeira classe para streaming por meio de HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="5317d-392">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="5317d-393">Um serviço gRPC dá suporte A todas as combinações de streaming:</span><span class="sxs-lookup"><span data-stu-id="5317d-393">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="5317d-394">Unário (sem streaming)</span><span class="sxs-lookup"><span data-stu-id="5317d-394">Unary (no streaming)</span></span>
* <span data-ttu-id="5317d-395">Streaming de servidor para cliente</span><span class="sxs-lookup"><span data-stu-id="5317d-395">Server to client streaming</span></span>
* <span data-ttu-id="5317d-396">Transmissão de cliente para servidor</span><span class="sxs-lookup"><span data-stu-id="5317d-396">Client to server streaming</span></span>
* <span data-ttu-id="5317d-397">Streaming bidirecional</span><span class="sxs-lookup"><span data-stu-id="5317d-397">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="5317d-398">Prazo/tempos limite e cancelamento</span><span class="sxs-lookup"><span data-stu-id="5317d-398">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="5317d-399">o gRPC permite que os clientes especifiquem por quanto tempo eles estão dispostos a aguardar a conclusão de um RPC.</span><span class="sxs-lookup"><span data-stu-id="5317d-399">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="5317d-400">O [prazo](https://grpc.io/blog/deadlines) é enviado ao servidor e o servidor pode decidir a ação a ser tomada se exceder o prazo.</span><span class="sxs-lookup"><span data-stu-id="5317d-400">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="5317d-401">Por exemplo, o servidor pode cancelar solicitações de gRPC/HTTP/banco de dados em andamento no tempo limite.</span><span class="sxs-lookup"><span data-stu-id="5317d-401">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="5317d-402">Propagar o prazo e o cancelamento por meio de chamadas gRPC filho ajuda a impor limites de uso de recursos.</span><span class="sxs-lookup"><span data-stu-id="5317d-402">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="5317d-403">cenários gRPC recomendados</span><span class="sxs-lookup"><span data-stu-id="5317d-403">gRPC recommended scenarios</span></span>

<span data-ttu-id="5317d-404">o gRPC é bem adequado para os seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="5317d-404">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="5317d-405">**Microserviços**: o gRPC foi projetado para comunicação de baixa latência e alta taxa de transferência.</span><span class="sxs-lookup"><span data-stu-id="5317d-405">**Microservices**: gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="5317d-406">o gRPC é ótimo para microserviços leves em que a eficiência é fundamental.</span><span class="sxs-lookup"><span data-stu-id="5317d-406">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="5317d-407">**Comunicação ponto a ponto em tempo real**: o gRPC tem excelente suporte para streaming bidirecional.</span><span class="sxs-lookup"><span data-stu-id="5317d-407">**Point-to-point real-time communication**: gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="5317d-408">os serviços gRPCs podem enviar mensagens por push em tempo real sem sondagem.</span><span class="sxs-lookup"><span data-stu-id="5317d-408">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="5317d-409">**Ambientes poliglota**: as ferramentas de gRPC dão suporte a todas as linguagens de desenvolvimento populares, tornando o gRPC uma boa opção para ambientes com vários idiomas.</span><span class="sxs-lookup"><span data-stu-id="5317d-409">**Polyglot environments**: gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="5317d-410">**Ambientes com restrição de rede**: as mensagens gRPC são serializadas com Protobuf, um formato de mensagem leve.</span><span class="sxs-lookup"><span data-stu-id="5317d-410">**Network constrained environments**: gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="5317d-411">Uma mensagem gRPC é sempre menor do que uma mensagem JSON equivalente.</span><span class="sxs-lookup"><span data-stu-id="5317d-411">A gRPC message is always smaller than an equivalent JSON message.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="5317d-412">gRPC fracos</span><span class="sxs-lookup"><span data-stu-id="5317d-412">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="5317d-413">Suporte limitado a navegador</span><span class="sxs-lookup"><span data-stu-id="5317d-413">Limited browser support</span></span>

<span data-ttu-id="5317d-414">É impossível chamar diretamente um serviço gRPC de um navegador hoje.</span><span class="sxs-lookup"><span data-stu-id="5317d-414">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="5317d-415">gRPC usa intensamente recursos HTTP/2 e nenhum navegador fornece o nível de controle necessário em relação a solicitações da Web para dar suporte a um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="5317d-415">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="5317d-416">Por exemplo, os navegadores não permitem que um chamador exija que o HTTP/2 seja usado ou forneça acesso a quadros HTTP/2 subjacentes.</span><span class="sxs-lookup"><span data-stu-id="5317d-416">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="5317d-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) é uma tecnologia adicional da equipe do gRPC que fornece suporte limitado a gRPC no navegador.</span><span class="sxs-lookup"><span data-stu-id="5317d-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides limited gRPC support in the browser.</span></span> <span data-ttu-id="5317d-418">gRPC-Web consiste em duas partes: um cliente JavaScript que dá suporte a todos os navegadores modernos e um proxy gRPC-Web no servidor.</span><span class="sxs-lookup"><span data-stu-id="5317d-418">gRPC-Web consists of two parts: a JavaScript client that supports all modern browsers, and a gRPC-Web proxy on the server.</span></span> <span data-ttu-id="5317d-419">O cliente Web gRPC chama o proxy e o proxy será encaminhado nas solicitações gRPC para o servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="5317d-419">The gRPC-Web client calls the proxy and the proxy will forward on the gRPC requests to the gRPC server.</span></span>

<span data-ttu-id="5317d-420">Nem todos os recursos de gRPC têm suporte no gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="5317d-420">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="5317d-421">Não há suporte para o streaming bidirecional e de cliente, e há suporte limitado para streaming de servidor.</span><span class="sxs-lookup"><span data-stu-id="5317d-421">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

> [!TIP]
> <span data-ttu-id="5317d-422">O .NET Core tem suporte experimental para gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="5317d-422">.NET Core has experimental support for gRPC-Web.</span></span> <span data-ttu-id="5317d-423">Visite <xref:grpc/browser> para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="5317d-423">Visit <xref:grpc/browser> for more information.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="5317d-424">Não legível por humanos</span><span class="sxs-lookup"><span data-stu-id="5317d-424">Not human readable</span></span>

<span data-ttu-id="5317d-425">As solicitações de API HTTP são enviadas como texto e podem ser lidas e criadas por humanos.</span><span class="sxs-lookup"><span data-stu-id="5317d-425">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="5317d-426">as mensagens gRPC são codificadas com Protobuf por padrão.</span><span class="sxs-lookup"><span data-stu-id="5317d-426">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="5317d-427">Embora o Protobuf seja eficiente para enviar e receber, seu formato binário não é legível por humanos.</span><span class="sxs-lookup"><span data-stu-id="5317d-427">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="5317d-428">Protobuf requer a descrição da interface da mensagem especificada no arquivo *. proto* para desserializar corretamente.</span><span class="sxs-lookup"><span data-stu-id="5317d-428">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="5317d-429">Ferramentas adicionais são necessárias para analisar cargas de Protobuf na transmissão e para compor solicitações manualmente.</span><span class="sxs-lookup"><span data-stu-id="5317d-429">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="5317d-430">Recursos como [reflexão de servidor](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) e a [ferramenta de linha de comando gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) existem para auxiliar com mensagens Protobuf binárias.</span><span class="sxs-lookup"><span data-stu-id="5317d-430">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="5317d-431">Além disso, as mensagens Protobuf dão suporte à [conversão de e para JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span><span class="sxs-lookup"><span data-stu-id="5317d-431">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="5317d-432">A conversão interna de JSON fornece uma maneira eficiente de converter mensagens Protobuf de e para o formulário legível por humanos durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="5317d-432">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="5317d-433">Cenários de estrutura alternativos</span><span class="sxs-lookup"><span data-stu-id="5317d-433">Alternative framework scenarios</span></span>

<span data-ttu-id="5317d-434">Outras estruturas são recomendadas em relação ao gRPC nos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="5317d-434">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="5317d-435">**APIs acessíveis ao navegador**: o gRPC não tem suporte total no navegador.</span><span class="sxs-lookup"><span data-stu-id="5317d-435">**Browser accessible APIs**: gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="5317d-436">o gRPC-Web pode oferecer suporte a navegador, mas tem limitações e introduz um proxy de servidor.</span><span class="sxs-lookup"><span data-stu-id="5317d-436">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="5317d-437">**Transmitir comunicação em tempo real**: o gRPC dá suporte à comunicação em tempo real via streaming, mas o conceito de difusão de uma mensagem para conexões registradas não existe.</span><span class="sxs-lookup"><span data-stu-id="5317d-437">**Broadcast real-time communication**: gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="5317d-438">Por exemplo, em um cenário de sala de chat em que novas mensagens de chat devem ser enviadas a todos os clientes na sala de chat, cada chamada de gRPC é necessária para transmitir individualmente novas mensagens de chat para o cliente.</span><span class="sxs-lookup"><span data-stu-id="5317d-438">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="5317d-439">[SignalR](xref:signalr/introduction)é uma estrutura útil para esse cenário.</span><span class="sxs-lookup"><span data-stu-id="5317d-439">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> SignalR<span data-ttu-id="5317d-440">tem o conceito de conexões persistentes e suporte interno para mensagens de difusão.</span><span class="sxs-lookup"><span data-stu-id="5317d-440"> has the concept of persistent connections and built-in support for broadcasting messages.</span></span>
* <span data-ttu-id="5317d-441">**Comunicação entre processos**: um processo deve hospedar um servidor http/2 para aceitar chamadas gRPC de entrada.</span><span class="sxs-lookup"><span data-stu-id="5317d-441">**Inter-process communication**: A process must host an HTTP/2 server to accept incoming gRPC calls.</span></span> <span data-ttu-id="5317d-442">Para o Windows, os [pipes](/dotnet/standard/io/pipe-operations) de comunicação entre processos são um método rápido e leve de comunicação.</span><span class="sxs-lookup"><span data-stu-id="5317d-442">For Windows, inter-process communication [pipes](/dotnet/standard/io/pipe-operations) is a fast, lightweight method of communication.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5317d-443">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5317d-443">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

---
<span data-ttu-id="740fa-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-102">'Blazor'</span></span>
- <span data-ttu-id="740fa-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-103">'Identity'</span></span>
- <span data-ttu-id="740fa-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-105">'Razor'</span></span>
- <span data-ttu-id="740fa-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-106">'SignalR' uid:</span></span> 

---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="740fa-107">Middleware de Reconfiguração de URL no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="740fa-107">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="740fa-108">Por [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="740fa-108">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="740fa-109">Este documento apresenta a reconfiguração de URL com instruções sobre como usar o Middleware de Reconfiguração de URL em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="740fa-109">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="740fa-110">A reconfiguração de URL é o ato de modificar URLs de solicitação com base em uma ou mais regras predefinidas.</span><span class="sxs-lookup"><span data-stu-id="740fa-110">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="740fa-111">A reconfiguração de URL cria uma abstração entre locais de recursos e seus endereços, de forma que os locais e os endereços não estejam totalmente vinculados.</span><span class="sxs-lookup"><span data-stu-id="740fa-111">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="740fa-112">A reconfiguração de URL é útil em vários cenários para:</span><span class="sxs-lookup"><span data-stu-id="740fa-112">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="740fa-113">Mover ou substituir recursos de servidor temporária ou permanentemente e manter localizadores estáveis para esses recursos.</span><span class="sxs-lookup"><span data-stu-id="740fa-113">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="740fa-114">Dividir o processamento de solicitação entre diferentes aplicativos ou áreas de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-114">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="740fa-115">Remover, adicionar ou reorganizar segmentos de URL em solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="740fa-115">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="740fa-116">Otimizar URLs públicas para SEO (Otimização do Mecanismo de Pesquisa).</span><span class="sxs-lookup"><span data-stu-id="740fa-116">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="740fa-117">Permitir o uso de URLs públicas amigáveis para ajudar os visitantes a prever o conteúdo retornado ao solicitar um recurso.</span><span class="sxs-lookup"><span data-stu-id="740fa-117">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="740fa-118">Redirecionar solicitações não seguras para pontos de extremidade seguros.</span><span class="sxs-lookup"><span data-stu-id="740fa-118">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="740fa-119">Impedir o hotlink, em que um site externo usa um ativo estático hospedado em outro site vinculando o ativo ao seu próprio conteúdo.</span><span class="sxs-lookup"><span data-stu-id="740fa-119">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="740fa-120">A reconfiguração de URL pode reduzir o desempenho de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-120">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="740fa-121">Sempre que possível, limite o número e a complexidade das regras.</span><span class="sxs-lookup"><span data-stu-id="740fa-121">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="740fa-122">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="740fa-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="740fa-123">Redirecionamento e reconfiguração de URL</span><span class="sxs-lookup"><span data-stu-id="740fa-123">URL redirect and URL rewrite</span></span>

<span data-ttu-id="740fa-124">A diferença entre os termos *redirecionamento de URL* e *reconfiguração de URL* é sutil, mas tem implicações importantes no fornecimento de recursos aos clientes.</span><span class="sxs-lookup"><span data-stu-id="740fa-124">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="740fa-125">O Middleware de Reconfiguração de URL do ASP.NET Core pode atender às necessidades de ambos.</span><span class="sxs-lookup"><span data-stu-id="740fa-125">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="740fa-126">Um *redirecionamento de URL* envolve uma operação do lado do cliente, em que o cliente é instruído a acessar um recurso em um endereço diferente do que ele solicitou originalmente.</span><span class="sxs-lookup"><span data-stu-id="740fa-126">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="740fa-127">Isso exige uma viagem de ida e volta para o servidor.</span><span class="sxs-lookup"><span data-stu-id="740fa-127">This requires a round trip to the server.</span></span> <span data-ttu-id="740fa-128">A URL de redirecionamento retornada para o cliente é exibida na barra de endereços do navegador quando o cliente faz uma nova solicitação para o recurso.</span><span class="sxs-lookup"><span data-stu-id="740fa-128">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="740fa-129">Se `/resource` é *redirecionado* para `/different-resource`, o servidor responde que o cliente deve obter o recurso em `/different-resource` com um código de status indicando que o redirecionamento é temporário ou permanente.</span><span class="sxs-lookup"><span data-stu-id="740fa-129">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Um ponto de extremidade de serviço WebAPI foi alterado temporariamente da versão 1 (v1) para a versão 2 (v2) no servidor.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="740fa-135">Ao redirecionar solicitações para uma URL diferente, indique se o redirecionamento é permanente ou temporário especificando o código de status com a resposta:</span><span class="sxs-lookup"><span data-stu-id="740fa-135">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="740fa-136">O código de status *301 – Movido Permanentemente* é usado quando o recurso tem uma nova URL permanente e você deseja instruir o cliente que todas as solicitações futuras para o recurso devem usar a nova URL.</span><span class="sxs-lookup"><span data-stu-id="740fa-136">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="740fa-137">*O cliente pode armazenar a resposta em cache e reutilizá-la quando um código de status 301 é recebido.*</span><span class="sxs-lookup"><span data-stu-id="740fa-137">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="740fa-138">O código de status *302 – Encontrado* é usado quando o redirecionamento é temporário ou está geralmente sujeito a alterações.</span><span class="sxs-lookup"><span data-stu-id="740fa-138">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="740fa-139">O código de status 302 indica para o cliente não armazenar a URL e usá-la no futuro.</span><span class="sxs-lookup"><span data-stu-id="740fa-139">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="740fa-140">Para obter mais informações sobre códigos de status, confira [RFC 2616: definições de código de status](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="740fa-140">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="740fa-141">Uma *reconfiguração de URL* é uma operação do servidor que fornece um recurso de um endereço de recurso diferente do que o solicitado pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="740fa-141">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="740fa-142">A reconfiguração de uma URL não exige uma viagem de ida e volta para o servidor.</span><span class="sxs-lookup"><span data-stu-id="740fa-142">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="740fa-143">A URL reconfigurada não é retornada para o cliente e não é exibida na barra de endereços do navegador.</span><span class="sxs-lookup"><span data-stu-id="740fa-143">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="740fa-144">Se `/resource` é *reconfigurado* como `/different-resource`, o servidor efetua fetch *internamente* e retorna o recurso em `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="740fa-144">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="740fa-145">Embora o cliente possa ter a capacidade de recuperar o recurso na URL reconfigurada, ele não é informado de que o recurso existe na URL reconfigurada ao fazer a solicitação e receber a resposta.</span><span class="sxs-lookup"><span data-stu-id="740fa-145">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Um ponto de extremidade de serviço WebAPI foi alterado da versão 1 (v1) para a versão 2 (v2) no servidor.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="740fa-150">Aplicativo de exemplo de reconfiguração de URL</span><span class="sxs-lookup"><span data-stu-id="740fa-150">URL rewriting sample app</span></span>

<span data-ttu-id="740fa-151">Explore as funcionalidades do Middleware de Reconfiguração de URL com o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="740fa-151">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="740fa-152">O aplicativo aplica as regras de redirecionamento e reconfiguração e mostra a URL redirecionada ou reconfigurada para vários cenários.</span><span class="sxs-lookup"><span data-stu-id="740fa-152">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="740fa-153">Quando usar o Middleware de Reconfiguração de URL</span><span class="sxs-lookup"><span data-stu-id="740fa-153">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="740fa-154">Use o Middleware de Reconfiguração de URL quando não for possível usar as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="740fa-154">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="740fa-155">Módulo de Reconfiguração de URL com o IIS no Windows Server</span><span class="sxs-lookup"><span data-stu-id="740fa-155">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="740fa-156">Módulo mod_rewrite do Apache no Apache Server</span><span class="sxs-lookup"><span data-stu-id="740fa-156">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="740fa-157">Reconfiguração de URL no Nginx</span><span class="sxs-lookup"><span data-stu-id="740fa-157">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="740fa-158">Além disso, use o middleware quando o aplicativo estiver hospedado no [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente chamado WebListener).</span><span class="sxs-lookup"><span data-stu-id="740fa-158">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="740fa-159">As principais razões para usar as tecnologias de reconfiguração de URL baseada em servidor no IIS, no Apache e no Nginx são:</span><span class="sxs-lookup"><span data-stu-id="740fa-159">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="740fa-160">O middleware não dá suporte às funcionalidades completas desses módulos.</span><span class="sxs-lookup"><span data-stu-id="740fa-160">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="740fa-161">Algumas funcionalidades dos módulos de servidor não funcionam com projetos ASP.NET Core, como as restrições `IsFile` e `IsDirectory` do módulo de Reconfiguração do IIS.</span><span class="sxs-lookup"><span data-stu-id="740fa-161">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="740fa-162">Nesses cenários, use o middleware.</span><span class="sxs-lookup"><span data-stu-id="740fa-162">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="740fa-163">O desempenho do middleware provavelmente não corresponde ao desempenho dos módulos.</span><span class="sxs-lookup"><span data-stu-id="740fa-163">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="740fa-164">Os parâmetros de comparação são a única maneira de saber com certeza qual abordagem degrada mais o desempenho ou se a degradação de desempenho é insignificante.</span><span class="sxs-lookup"><span data-stu-id="740fa-164">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="740fa-165">Pacote</span><span class="sxs-lookup"><span data-stu-id="740fa-165">Package</span></span>

<span data-ttu-id="740fa-166">O Middleware de Reconfiguração de URL é fornecido pelo pacote [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite), que é incluído implicitamente em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="740fa-166">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="740fa-167">Extensão e opções</span><span class="sxs-lookup"><span data-stu-id="740fa-167">Extension and options</span></span>

<span data-ttu-id="740fa-168">Estabeleça regras de reconfiguração e redirecionamento de URL criando uma instância da classe [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) com métodos de extensão para cada uma das regras de reconfiguração.</span><span class="sxs-lookup"><span data-stu-id="740fa-168">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="740fa-169">Encadeie várias regras na ordem em que deseja que sejam processadas.</span><span class="sxs-lookup"><span data-stu-id="740fa-169">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="740fa-170">As `RewriteOptions` são passadas para o Middleware de Reconfiguração de URL, conforme ele é adicionado ao pipeline de solicitação com <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="740fa-170">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="740fa-171">Redirecionar não www para www</span><span class="sxs-lookup"><span data-stu-id="740fa-171">Redirect non-www to www</span></span>

<span data-ttu-id="740fa-172">Três opções permitem que o aplicativo redirecione solicitações não `www`para `www`:</span><span class="sxs-lookup"><span data-stu-id="740fa-172">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="740fa-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Redirecionar permanentemente a solicitação para o `www` subdomínio se a solicitação for não `www` .</span><span class="sxs-lookup"><span data-stu-id="740fa-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="740fa-174">Redireciona com um código de status [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).</span><span class="sxs-lookup"><span data-stu-id="740fa-174">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="740fa-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirecione a solicitação para o `www` subdomínio se a solicitação de entrada for não `www` .</span><span class="sxs-lookup"><span data-stu-id="740fa-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="740fa-176">Redireciona com um código de status [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect).</span><span class="sxs-lookup"><span data-stu-id="740fa-176">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="740fa-177">Uma sobrecarga permite que você forneça o código de status para a resposta.</span><span class="sxs-lookup"><span data-stu-id="740fa-177">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="740fa-178">Use um campo da classe <xref:Microsoft.AspNetCore.Http.StatusCodes> para uma atribuição de código de status.</span><span class="sxs-lookup"><span data-stu-id="740fa-178">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="740fa-179">Redirecionamento de URL</span><span class="sxs-lookup"><span data-stu-id="740fa-179">URL redirect</span></span>

<span data-ttu-id="740fa-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> para redirecionar as solicitações.</span><span class="sxs-lookup"><span data-stu-id="740fa-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="740fa-181">O primeiro parâmetro contém o regex para correspondência no caminho da URL de entrada.</span><span class="sxs-lookup"><span data-stu-id="740fa-181">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="740fa-182">O segundo parâmetro é a cadeia de caracteres de substituição.</span><span class="sxs-lookup"><span data-stu-id="740fa-182">The second parameter is the replacement string.</span></span> <span data-ttu-id="740fa-183">O terceiro parâmetro, se presente, especifica o código de status.</span><span class="sxs-lookup"><span data-stu-id="740fa-183">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="740fa-184">Se você não especificar o código de status, ele usará como padrão *302 – Encontrado*, que indica que o recurso foi substituído ou movido temporariamente.</span><span class="sxs-lookup"><span data-stu-id="740fa-184">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="740fa-185">Em um navegador com as ferramentas para desenvolvedores habilitadas, faça uma solicitação para o aplicativo de exemplo com o caminho `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="740fa-185">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="740fa-186">O regex corresponde ao caminho de solicitação em `redirect-rule/(.*)` e o caminho é substituído por `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="740fa-186">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="740fa-187">A URL de redirecionamento é enviada novamente ao cliente com um código de status *302 – Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="740fa-187">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="740fa-188">O navegador faz uma nova solicitação na URL de redirecionamento, que é exibida na barra de endereços do navegador.</span><span class="sxs-lookup"><span data-stu-id="740fa-188">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="740fa-189">Como não há nenhuma correspondência de regras no aplicativo de exemplo na URL de redirecionamento:</span><span class="sxs-lookup"><span data-stu-id="740fa-189">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="740fa-190">A segunda solicitação recebe uma resposta *200 – OK* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-190">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="740fa-191">O corpo da resposta mostra a URL de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="740fa-191">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="740fa-192">Uma viagem de ida e volta é feita para o servidor quando uma URL é *redirecionada*.</span><span class="sxs-lookup"><span data-stu-id="740fa-192">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="740fa-193">Tenha cuidado ao estabelecer regras de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="740fa-193">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="740fa-194">As regras de redirecionamento são avaliadas em cada solicitação para o aplicativo, incluindo após um redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="740fa-194">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="740fa-195">É fácil criar acidentalmente um *loop de redirecionamentos infinitos*.</span><span class="sxs-lookup"><span data-stu-id="740fa-195">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="740fa-196">Solicitação original: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="740fa-196">Original Request: `/redirect-rule/1234/5678`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando as solicitações e respostas](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="740fa-198">A parte da expressão contida nos parênteses é chamada um *grupo de captura*.</span><span class="sxs-lookup"><span data-stu-id="740fa-198">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="740fa-199">O ponto (`.`) da expressão significa *corresponder a qualquer caractere*.</span><span class="sxs-lookup"><span data-stu-id="740fa-199">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="740fa-200">O asterisco (`*`) indica *corresponder ao caractere zero precedente ou mais vezes*.</span><span class="sxs-lookup"><span data-stu-id="740fa-200">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="740fa-201">Portanto, os dois últimos segmentos de caminho da URL, `1234/5678`, são capturados pelo grupo de captura `(.*)`.</span><span class="sxs-lookup"><span data-stu-id="740fa-201">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="740fa-202">Qualquer valor que você fornecer na URL de solicitação após `redirect-rule/` é capturado por esse único grupo de captura.</span><span class="sxs-lookup"><span data-stu-id="740fa-202">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="740fa-203">Na cadeia de caracteres de substituição, os grupos capturados são injetados na cadeia de caracteres com o cifrão (`$`) seguido do número de sequência da captura.</span><span class="sxs-lookup"><span data-stu-id="740fa-203">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="740fa-204">O primeiro valor de grupo de captura é obtido com `$1`, o segundo com `$2` e eles continuam em sequência para os grupos de captura no regex.</span><span class="sxs-lookup"><span data-stu-id="740fa-204">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="740fa-205">Há apenas um grupo capturado no regex da regra de redirecionamento no aplicativo de exemplo, para que haja apenas um grupo injetado na cadeia de caracteres de substituição, que é `$1`.</span><span class="sxs-lookup"><span data-stu-id="740fa-205">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="740fa-206">Quando a regra é aplicada, a URL se torna `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="740fa-206">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="740fa-207">Redirecionamento de URL para um ponto de extremidade seguro</span><span class="sxs-lookup"><span data-stu-id="740fa-207">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="740fa-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> para redirecionar solicitações HTTP para o mesmo host e caminho usando o protocolo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="740fa-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="740fa-209">Se o código de status não for fornecido, o middleware usará como padrão *302 – Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="740fa-209">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="740fa-210">Se a porta não for fornecida:</span><span class="sxs-lookup"><span data-stu-id="740fa-210">If the port isn't supplied:</span></span>

* <span data-ttu-id="740fa-211">O middleware usará como padrão `null`.</span><span class="sxs-lookup"><span data-stu-id="740fa-211">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="740fa-212">O esquema será alterado para `https` (protocolo HTTPS), e o cliente acessará o recurso na porta 443.</span><span class="sxs-lookup"><span data-stu-id="740fa-212">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="740fa-213">O exemplo a seguir mostra como definir o código de status como *301 –Movido Permanentemente* e alterar a porta para 5001.</span><span class="sxs-lookup"><span data-stu-id="740fa-213">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="740fa-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> para redirecionar solicitações não seguras para o mesmo host e caminho com o protocolo HTTPS seguro na porta 443.</span><span class="sxs-lookup"><span data-stu-id="740fa-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="740fa-215">O middleware define o código de status como *301 – Movido Permanentemente*.</span><span class="sxs-lookup"><span data-stu-id="740fa-215">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="740fa-216">Ao redirecionar para um ponto de extremidade seguro sem a necessidade de regras de redirecionamento adicionais, recomendamos o uso do Middleware de Redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="740fa-216">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="740fa-217">Para obter mais informações, veja o tópico [Impor HTTPS](xref:security/enforcing-ssl#require-https).</span><span class="sxs-lookup"><span data-stu-id="740fa-217">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="740fa-218">O aplicativo de exemplo pode demonstrar como usar `AddRedirectToHttps` ou `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="740fa-218">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="740fa-219">Adicione o método de extensão às `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="740fa-219">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="740fa-220">Faça uma solicitação não segura para o aplicativo em qualquer URL.</span><span class="sxs-lookup"><span data-stu-id="740fa-220">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="740fa-221">Ignore o aviso de segurança do navegador de que o certificado autoassinado não é confiável ou crie uma exceção para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="740fa-221">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="740fa-222">Solicitação original usando `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="740fa-222">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando as solicitações e respostas](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="740fa-224">Solicitação original usando `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="740fa-224">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando as solicitações e respostas](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="740fa-226">Reconfiguração de URL</span><span class="sxs-lookup"><span data-stu-id="740fa-226">URL rewrite</span></span>

<span data-ttu-id="740fa-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> para criar uma regra para a reconfiguração de URLs.</span><span class="sxs-lookup"><span data-stu-id="740fa-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="740fa-228">O primeiro parâmetro contém o regex para correspondência no caminho da URL de entrada.</span><span class="sxs-lookup"><span data-stu-id="740fa-228">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="740fa-229">O segundo parâmetro é a cadeia de caracteres de substituição.</span><span class="sxs-lookup"><span data-stu-id="740fa-229">The second parameter is the replacement string.</span></span> <span data-ttu-id="740fa-230">O terceiro parâmetro, `skipRemainingRules: {true|false}`, indica para o middleware se ele deve ou não ignorar regras de reconfiguração adicionais se a regra atual é aplicada.</span><span class="sxs-lookup"><span data-stu-id="740fa-230">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="740fa-231">Solicitação original: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="740fa-231">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando a solicitação e resposta](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="740fa-233">O acento circunflexo (`^`) no início da expressão significa que a correspondência começa no início do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="740fa-233">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="740fa-234">No exemplo anterior com a regra de redirecionamento, `redirect-rule/(.*)`, não há nenhum acento circunflexo (`^`) no início do regex.</span><span class="sxs-lookup"><span data-stu-id="740fa-234">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="740fa-235">Portanto, qualquer caractere pode preceder `redirect-rule/` no caminho para uma correspondência com êxito.</span><span class="sxs-lookup"><span data-stu-id="740fa-235">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="740fa-236">Caminho</span><span class="sxs-lookup"><span data-stu-id="740fa-236">Path</span></span>                               | <span data-ttu-id="740fa-237">Correspondência</span><span class="sxs-lookup"><span data-stu-id="740fa-237">Match</span></span> |
| ---
<span data-ttu-id="740fa-238">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-239">'Blazor'</span></span>
- <span data-ttu-id="740fa-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-240">'Identity'</span></span>
- <span data-ttu-id="740fa-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-242">'Razor'</span></span>
- <span data-ttu-id="740fa-243">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-244">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-245">'Blazor'</span></span>
- <span data-ttu-id="740fa-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-246">'Identity'</span></span>
- <span data-ttu-id="740fa-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-248">'Razor'</span></span>
- <span data-ttu-id="740fa-249">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-250">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-251">'Blazor'</span></span>
- <span data-ttu-id="740fa-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-252">'Identity'</span></span>
- <span data-ttu-id="740fa-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-254">'Razor'</span></span>
- <span data-ttu-id="740fa-255">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-256">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-257">'Blazor'</span></span>
- <span data-ttu-id="740fa-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-258">'Identity'</span></span>
- <span data-ttu-id="740fa-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-260">'Razor'</span></span>
- <span data-ttu-id="740fa-261">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-262">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-263">'Blazor'</span></span>
- <span data-ttu-id="740fa-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-264">'Identity'</span></span>
- <span data-ttu-id="740fa-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-266">'Razor'</span></span>
- <span data-ttu-id="740fa-267">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-268">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-269">'Blazor'</span></span>
- <span data-ttu-id="740fa-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-270">'Identity'</span></span>
- <span data-ttu-id="740fa-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-272">'Razor'</span></span>
- <span data-ttu-id="740fa-273">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-274">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-275">'Blazor'</span></span>
- <span data-ttu-id="740fa-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-276">'Identity'</span></span>
- <span data-ttu-id="740fa-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-278">'Razor'</span></span>
- <span data-ttu-id="740fa-279">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-280">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-281">'Blazor'</span></span>
- <span data-ttu-id="740fa-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-282">'Identity'</span></span>
- <span data-ttu-id="740fa-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-284">'Razor'</span></span>
- <span data-ttu-id="740fa-285">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-286">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-287">'Blazor'</span></span>
- <span data-ttu-id="740fa-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-288">'Identity'</span></span>
- <span data-ttu-id="740fa-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-290">'Razor'</span></span>
- <span data-ttu-id="740fa-291">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-292">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-293">'Blazor'</span></span>
- <span data-ttu-id="740fa-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-294">'Identity'</span></span>
- <span data-ttu-id="740fa-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-296">'Razor'</span></span>
- <span data-ttu-id="740fa-297">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-298">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-299">'Blazor'</span></span>
- <span data-ttu-id="740fa-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-300">'Identity'</span></span>
- <span data-ttu-id="740fa-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-302">'Razor'</span></span>
- <span data-ttu-id="740fa-303">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-304">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-305">'Blazor'</span></span>
- <span data-ttu-id="740fa-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-306">'Identity'</span></span>
- <span data-ttu-id="740fa-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-308">'Razor'</span></span>
- <span data-ttu-id="740fa-309">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-309">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-310">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-311">'Blazor'</span></span>
- <span data-ttu-id="740fa-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-312">'Identity'</span></span>
- <span data-ttu-id="740fa-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-314">'Razor'</span></span>
- <span data-ttu-id="740fa-315">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-316">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-317">'Blazor'</span></span>
- <span data-ttu-id="740fa-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-318">'Identity'</span></span>
- <span data-ttu-id="740fa-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-320">'Razor'</span></span>
- <span data-ttu-id="740fa-321">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-322">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-323">'Blazor'</span></span>
- <span data-ttu-id="740fa-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-324">'Identity'</span></span>
- <span data-ttu-id="740fa-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-326">'Razor'</span></span>
- <span data-ttu-id="740fa-327">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-327">'SignalR' uid:</span></span> 

<span data-ttu-id="740fa-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Sim | | `/my-cool-redirect-rule/1234/5678` | Sim | | `/anotherredirect-rule/1234/5678`  | Sim |</span><span class="sxs-lookup"><span data-stu-id="740fa-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="740fa-329">A regra de reconfiguração, `^rewrite-rule/(\d+)/(\d+)`, corresponde apenas a caminhos se eles são iniciados com `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="740fa-329">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="740fa-330">Na tabela a seguir, observe a diferença na correspondência.</span><span class="sxs-lookup"><span data-stu-id="740fa-330">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="740fa-331">Caminho</span><span class="sxs-lookup"><span data-stu-id="740fa-331">Path</span></span>                              | <span data-ttu-id="740fa-332">Correspondência</span><span class="sxs-lookup"><span data-stu-id="740fa-332">Match</span></span> |
| ---
<span data-ttu-id="740fa-333">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-334">'Blazor'</span></span>
- <span data-ttu-id="740fa-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-335">'Identity'</span></span>
- <span data-ttu-id="740fa-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-337">'Razor'</span></span>
- <span data-ttu-id="740fa-338">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-339">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-340">'Blazor'</span></span>
- <span data-ttu-id="740fa-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-341">'Identity'</span></span>
- <span data-ttu-id="740fa-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-343">'Razor'</span></span>
- <span data-ttu-id="740fa-344">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-345">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-346">'Blazor'</span></span>
- <span data-ttu-id="740fa-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-347">'Identity'</span></span>
- <span data-ttu-id="740fa-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-349">'Razor'</span></span>
- <span data-ttu-id="740fa-350">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-351">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-352">'Blazor'</span></span>
- <span data-ttu-id="740fa-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-353">'Identity'</span></span>
- <span data-ttu-id="740fa-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-355">'Razor'</span></span>
- <span data-ttu-id="740fa-356">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-357">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-358">'Blazor'</span></span>
- <span data-ttu-id="740fa-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-359">'Identity'</span></span>
- <span data-ttu-id="740fa-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-361">'Razor'</span></span>
- <span data-ttu-id="740fa-362">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-363">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-364">'Blazor'</span></span>
- <span data-ttu-id="740fa-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-365">'Identity'</span></span>
- <span data-ttu-id="740fa-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-367">'Razor'</span></span>
- <span data-ttu-id="740fa-368">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-369">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-370">'Blazor'</span></span>
- <span data-ttu-id="740fa-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-371">'Identity'</span></span>
- <span data-ttu-id="740fa-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-373">'Razor'</span></span>
- <span data-ttu-id="740fa-374">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-375">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-376">'Blazor'</span></span>
- <span data-ttu-id="740fa-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-377">'Identity'</span></span>
- <span data-ttu-id="740fa-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-379">'Razor'</span></span>
- <span data-ttu-id="740fa-380">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-381">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-382">'Blazor'</span></span>
- <span data-ttu-id="740fa-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-383">'Identity'</span></span>
- <span data-ttu-id="740fa-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-385">'Razor'</span></span>
- <span data-ttu-id="740fa-386">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-387">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-388">'Blazor'</span></span>
- <span data-ttu-id="740fa-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-389">'Identity'</span></span>
- <span data-ttu-id="740fa-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-391">'Razor'</span></span>
- <span data-ttu-id="740fa-392">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-393">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-394">'Blazor'</span></span>
- <span data-ttu-id="740fa-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-395">'Identity'</span></span>
- <span data-ttu-id="740fa-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-397">'Razor'</span></span>
- <span data-ttu-id="740fa-398">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-399">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-400">'Blazor'</span></span>
- <span data-ttu-id="740fa-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-401">'Identity'</span></span>
- <span data-ttu-id="740fa-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-403">'Razor'</span></span>
- <span data-ttu-id="740fa-404">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-405">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-406">'Blazor'</span></span>
- <span data-ttu-id="740fa-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-407">'Identity'</span></span>
- <span data-ttu-id="740fa-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-409">'Razor'</span></span>
- <span data-ttu-id="740fa-410">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-411">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-412">'Blazor'</span></span>
- <span data-ttu-id="740fa-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-413">'Identity'</span></span>
- <span data-ttu-id="740fa-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-415">'Razor'</span></span>
- <span data-ttu-id="740fa-416">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-416">'SignalR' uid:</span></span> 

<span data-ttu-id="740fa-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Sim | | `/my-cool-rewrite-rule/1234/5678` | Não | | `/anotherrewrite-rule/1234/5678`  | Não |</span><span class="sxs-lookup"><span data-stu-id="740fa-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="740fa-418">Após a parte `^rewrite-rule/` da expressão, há dois grupos de captura, `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="740fa-418">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="740fa-419">O `\d` significa *corresponder a um dígito (número)*.</span><span class="sxs-lookup"><span data-stu-id="740fa-419">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="740fa-420">O sinal de adição (`+`) significa *corresponder a um ou mais caracteres anteriores*.</span><span class="sxs-lookup"><span data-stu-id="740fa-420">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="740fa-421">Portanto, a URL precisa conter um número seguido de uma barra "/" seguida de outro número.</span><span class="sxs-lookup"><span data-stu-id="740fa-421">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="740fa-422">Esses grupos de captura são injetados na URL reconfigurada como `$1` e `$2`.</span><span class="sxs-lookup"><span data-stu-id="740fa-422">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="740fa-423">A cadeia de caracteres de substituição da regra de reconfiguração coloca os grupos capturados na cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="740fa-423">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="740fa-424">O caminho solicitado de `/rewrite-rule/1234/5678` foi reconfigurado para obter o recurso em `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="740fa-424">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="740fa-425">Se uma cadeia de consulta estiver presente na solicitação original, ela será preservada quando a URL for reconfigurada.</span><span class="sxs-lookup"><span data-stu-id="740fa-425">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="740fa-426">Não há nenhuma viagem de ida e volta para o servidor para obtenção do recurso.</span><span class="sxs-lookup"><span data-stu-id="740fa-426">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="740fa-427">Se o recurso existir, ele será buscado e retornado para o cliente com um código de status *200 – OK*.</span><span class="sxs-lookup"><span data-stu-id="740fa-427">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="740fa-428">Como o cliente não é redirecionado, a URL na barra de endereços do navegador não é alterada.</span><span class="sxs-lookup"><span data-stu-id="740fa-428">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="740fa-429">Os clientes não conseguem detectar que uma operação de reconfiguração de URL ocorreu no servidor.</span><span class="sxs-lookup"><span data-stu-id="740fa-429">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="740fa-430">Use `skipRemainingRules: true` sempre que possível, porque as regras de correspondência são computacionalmente caras e aumentam o tempo de resposta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-430">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="740fa-431">Para a resposta mais rápida do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="740fa-431">For the fastest app response:</span></span>
>
> * <span data-ttu-id="740fa-432">Ordene as regras de reconfiguração da regra com correspondência mais frequente para a regra com correspondência menos frequente.</span><span class="sxs-lookup"><span data-stu-id="740fa-432">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="740fa-433">Ignore o processamento das regras restantes quando ocorrer uma correspondência e nenhum processamento de regra adicional for necessário.</span><span class="sxs-lookup"><span data-stu-id="740fa-433">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="740fa-434">mod_rewrite do Apache</span><span class="sxs-lookup"><span data-stu-id="740fa-434">Apache mod_rewrite</span></span>

<span data-ttu-id="740fa-435">Aplique as regras do mod_rewrite do Apache com <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="740fa-435">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="740fa-436">Verifique se o arquivo de regras foi implantado com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-436">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="740fa-437">Para obter mais informações e exemplos de regras de mod_rewrite, consulte [mod_rewrite do Apache](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="740fa-437">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="740fa-438">Um <xref:System.IO.StreamReader> é usado para ler as regras do arquivo de regras *ApacheModRewrite.txt*:</span><span class="sxs-lookup"><span data-stu-id="740fa-438">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="740fa-439">O aplicativo de exemplo redireciona solicitações de `/apache-mod-rules-redirect/(.\*)` para `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="740fa-439">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="740fa-440">O código de status da resposta é *302 – Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="740fa-440">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="740fa-441">Solicitação original: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="740fa-441">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando as solicitações e respostas](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="740fa-443">O middleware dá suporte às seguintes variáveis de servidor do mod_rewrite do Apache:</span><span class="sxs-lookup"><span data-stu-id="740fa-443">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="740fa-444">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="740fa-444">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="740fa-445">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="740fa-445">HTTP_ACCEPT</span></span>
* <span data-ttu-id="740fa-446">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="740fa-446">HTTP_CONNECTION</span></span>
* <span data-ttu-id="740fa-447">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="740fa-447">HTTP_COOKIE</span></span>
* <span data-ttu-id="740fa-448">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="740fa-448">HTTP_FORWARDED</span></span>
* <span data-ttu-id="740fa-449">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="740fa-449">HTTP_HOST</span></span>
* <span data-ttu-id="740fa-450">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="740fa-450">HTTP_REFERER</span></span>
* <span data-ttu-id="740fa-451">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="740fa-451">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="740fa-452">HTTPS</span><span class="sxs-lookup"><span data-stu-id="740fa-452">HTTPS</span></span>
* <span data-ttu-id="740fa-453">IPV6</span><span class="sxs-lookup"><span data-stu-id="740fa-453">IPV6</span></span>
* <span data-ttu-id="740fa-454">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="740fa-454">QUERY_STRING</span></span>
* <span data-ttu-id="740fa-455">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="740fa-455">REMOTE_ADDR</span></span>
* <span data-ttu-id="740fa-456">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="740fa-456">REMOTE_PORT</span></span>
* <span data-ttu-id="740fa-457">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="740fa-457">REQUEST_FILENAME</span></span>
* <span data-ttu-id="740fa-458">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="740fa-458">REQUEST_METHOD</span></span>
* <span data-ttu-id="740fa-459">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="740fa-459">REQUEST_SCHEME</span></span>
* <span data-ttu-id="740fa-460">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="740fa-460">REQUEST_URI</span></span>
* <span data-ttu-id="740fa-461">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="740fa-461">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="740fa-462">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="740fa-462">SERVER_ADDR</span></span>
* <span data-ttu-id="740fa-463">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="740fa-463">SERVER_PORT</span></span>
* <span data-ttu-id="740fa-464">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="740fa-464">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="740fa-465">TIME</span><span class="sxs-lookup"><span data-stu-id="740fa-465">TIME</span></span>
* <span data-ttu-id="740fa-466">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="740fa-466">TIME_DAY</span></span>
* <span data-ttu-id="740fa-467">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="740fa-467">TIME_HOUR</span></span>
* <span data-ttu-id="740fa-468">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="740fa-468">TIME_MIN</span></span>
* <span data-ttu-id="740fa-469">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="740fa-469">TIME_MON</span></span>
* <span data-ttu-id="740fa-470">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="740fa-470">TIME_SEC</span></span>
* <span data-ttu-id="740fa-471">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="740fa-471">TIME_WDAY</span></span>
* <span data-ttu-id="740fa-472">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="740fa-472">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="740fa-473">Regras do Módulo de Reconfiguração de URL do IIS</span><span class="sxs-lookup"><span data-stu-id="740fa-473">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="740fa-474">Para usar o mesmo conjunto de regras que se aplica ao Módulo de Reconfiguração de URL do IIS, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="740fa-474">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="740fa-475">Verifique se o arquivo de regras foi implantado com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-475">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="740fa-476">Não instrua o middleware a usar o arquivo *web.config* do aplicativo quando ele estiver em execução no IIS do Windows Server.</span><span class="sxs-lookup"><span data-stu-id="740fa-476">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="740fa-477">Com o IIS, essas regras devem ser armazenadas fora do arquivo *web.config* do aplicativo para evitar conflitos com o módulo de Reconfiguração do IIS.</span><span class="sxs-lookup"><span data-stu-id="740fa-477">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="740fa-478">Para obter mais informações e exemplos de regras do Módulo de Reconfiguração de URL do IIS, consulte [Usando o Módulo de Reconfiguração de URL 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) e [Referência de configuração do Módulo de Reconfiguração de URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="740fa-478">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="740fa-479">Um <xref:System.IO.StreamReader> é usado para ler as regras do arquivo de regras *IISUrlRewrite.xml*:</span><span class="sxs-lookup"><span data-stu-id="740fa-479">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="740fa-480">O aplicativo de exemplo reconfigura as solicitações de `/iis-rules-rewrite/(.*)` para `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="740fa-480">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="740fa-481">A resposta é enviada ao cliente com um código de status *200 – OK*.</span><span class="sxs-lookup"><span data-stu-id="740fa-481">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="740fa-482">Solicitação original: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="740fa-482">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando a solicitação e resposta](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="740fa-484">Caso você tenha um Módulo de Reconfiguração do IIS ativo com regras no nível do servidor configuradas que poderiam afetar o aplicativo de maneiras indesejadas, desabilite o Módulo de Reconfiguração do IIS em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-484">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="740fa-485">Para obter mais informações, consulte [Desabilitando módulos do IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="740fa-485">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="740fa-486">Recursos sem suporte</span><span class="sxs-lookup"><span data-stu-id="740fa-486">Unsupported features</span></span>

<span data-ttu-id="740fa-487">O middleware liberado com o ASP.NET Core 2.x não dá suporte aos seguintes recursos do Módulo de Reconfiguração de URL do IIS:</span><span class="sxs-lookup"><span data-stu-id="740fa-487">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="740fa-488">Regras de saída</span><span class="sxs-lookup"><span data-stu-id="740fa-488">Outbound Rules</span></span>
* <span data-ttu-id="740fa-489">Variáveis de servidor personalizadas</span><span class="sxs-lookup"><span data-stu-id="740fa-489">Custom Server Variables</span></span>
* <span data-ttu-id="740fa-490">Curingas</span><span class="sxs-lookup"><span data-stu-id="740fa-490">Wildcards</span></span>
* <span data-ttu-id="740fa-491">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="740fa-491">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="740fa-492">Variáveis de servidor compatíveis</span><span class="sxs-lookup"><span data-stu-id="740fa-492">Supported server variables</span></span>

<span data-ttu-id="740fa-493">O middleware dá suporte às seguintes variáveis de servidor do Módulo de Reconfiguração de URL do IIS:</span><span class="sxs-lookup"><span data-stu-id="740fa-493">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="740fa-494">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="740fa-494">CONTENT_LENGTH</span></span>
* <span data-ttu-id="740fa-495">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="740fa-495">CONTENT_TYPE</span></span>
* <span data-ttu-id="740fa-496">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="740fa-496">HTTP_ACCEPT</span></span>
* <span data-ttu-id="740fa-497">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="740fa-497">HTTP_CONNECTION</span></span>
* <span data-ttu-id="740fa-498">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="740fa-498">HTTP_COOKIE</span></span>
* <span data-ttu-id="740fa-499">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="740fa-499">HTTP_HOST</span></span>
* <span data-ttu-id="740fa-500">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="740fa-500">HTTP_REFERER</span></span>
* <span data-ttu-id="740fa-501">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="740fa-501">HTTP_URL</span></span>
* <span data-ttu-id="740fa-502">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="740fa-502">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="740fa-503">HTTPS</span><span class="sxs-lookup"><span data-stu-id="740fa-503">HTTPS</span></span>
* <span data-ttu-id="740fa-504">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="740fa-504">LOCAL_ADDR</span></span>
* <span data-ttu-id="740fa-505">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="740fa-505">QUERY_STRING</span></span>
* <span data-ttu-id="740fa-506">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="740fa-506">REMOTE_ADDR</span></span>
* <span data-ttu-id="740fa-507">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="740fa-507">REMOTE_PORT</span></span>
* <span data-ttu-id="740fa-508">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="740fa-508">REQUEST_FILENAME</span></span>
* <span data-ttu-id="740fa-509">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="740fa-509">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="740fa-510">Também obtenha um <xref:Microsoft.Extensions.FileProviders.IFileProvider> por meio de um <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="740fa-510">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="740fa-511">Essa abordagem pode fornecer maior flexibilidade para o local dos arquivos de regras de reconfiguração.</span><span class="sxs-lookup"><span data-stu-id="740fa-511">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="740fa-512">Verifique se os arquivos de regras de reconfiguração são implantados no servidor no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="740fa-512">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="740fa-513">Regra baseada em método</span><span class="sxs-lookup"><span data-stu-id="740fa-513">Method-based rule</span></span>

<span data-ttu-id="740fa-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para implementar sua própria lógica de regra em um método.</span><span class="sxs-lookup"><span data-stu-id="740fa-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="740fa-515">`Add` expõe o <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, que disponibiliza o <xref:Microsoft.AspNetCore.Http.HttpContext> para uso no método.</span><span class="sxs-lookup"><span data-stu-id="740fa-515">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="740fa-516">O [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determina como o processamento adicional de pipeline é feito.</span><span class="sxs-lookup"><span data-stu-id="740fa-516">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="740fa-517">Defina o valor como um dos campos <xref:Microsoft.AspNetCore.Rewrite.RuleResult> descritos na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="740fa-517">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="740fa-518">Ação</span><span class="sxs-lookup"><span data-stu-id="740fa-518">Action</span></span>                                                           |
| ---
<span data-ttu-id="740fa-519">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-520">'Blazor'</span></span>
- <span data-ttu-id="740fa-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-521">'Identity'</span></span>
- <span data-ttu-id="740fa-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-523">'Razor'</span></span>
- <span data-ttu-id="740fa-524">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-525">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-526">'Blazor'</span></span>
- <span data-ttu-id="740fa-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-527">'Identity'</span></span>
- <span data-ttu-id="740fa-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-529">'Razor'</span></span>
- <span data-ttu-id="740fa-530">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-531">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-532">'Blazor'</span></span>
- <span data-ttu-id="740fa-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-533">'Identity'</span></span>
- <span data-ttu-id="740fa-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-535">'Razor'</span></span>
- <span data-ttu-id="740fa-536">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-537">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-538">'Blazor'</span></span>
- <span data-ttu-id="740fa-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-539">'Identity'</span></span>
- <span data-ttu-id="740fa-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-541">'Razor'</span></span>
- <span data-ttu-id="740fa-542">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-543">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-544">'Blazor'</span></span>
- <span data-ttu-id="740fa-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-545">'Identity'</span></span>
- <span data-ttu-id="740fa-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-547">'Razor'</span></span>
- <span data-ttu-id="740fa-548">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-549">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-550">'Blazor'</span></span>
- <span data-ttu-id="740fa-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-551">'Identity'</span></span>
- <span data-ttu-id="740fa-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-553">'Razor'</span></span>
- <span data-ttu-id="740fa-554">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-555">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-556">'Blazor'</span></span>
- <span data-ttu-id="740fa-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-557">'Identity'</span></span>
- <span data-ttu-id="740fa-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-559">'Razor'</span></span>
- <span data-ttu-id="740fa-560">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-561">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-562">'Blazor'</span></span>
- <span data-ttu-id="740fa-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-563">'Identity'</span></span>
- <span data-ttu-id="740fa-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-565">'Razor'</span></span>
- <span data-ttu-id="740fa-566">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-567">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-568">'Blazor'</span></span>
- <span data-ttu-id="740fa-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-569">'Identity'</span></span>
- <span data-ttu-id="740fa-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-571">'Razor'</span></span>
- <span data-ttu-id="740fa-572">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-573">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-574">'Blazor'</span></span>
- <span data-ttu-id="740fa-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-575">'Identity'</span></span>
- <span data-ttu-id="740fa-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-577">'Razor'</span></span>
- <span data-ttu-id="740fa-578">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-579">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-580">'Blazor'</span></span>
- <span data-ttu-id="740fa-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-581">'Identity'</span></span>
- <span data-ttu-id="740fa-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-583">'Razor'</span></span>
- <span data-ttu-id="740fa-584">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-585">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-586">'Blazor'</span></span>
- <span data-ttu-id="740fa-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-587">'Identity'</span></span>
- <span data-ttu-id="740fa-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-589">'Razor'</span></span>
- <span data-ttu-id="740fa-590">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-591">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-592">'Blazor'</span></span>
- <span data-ttu-id="740fa-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-593">'Identity'</span></span>
- <span data-ttu-id="740fa-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-595">'Razor'</span></span>
- <span data-ttu-id="740fa-596">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-597">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-598">'Blazor'</span></span>
- <span data-ttu-id="740fa-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-599">'Identity'</span></span>
- <span data-ttu-id="740fa-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-601">'Razor'</span></span>
- <span data-ttu-id="740fa-602">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-603">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-604">'Blazor'</span></span>
- <span data-ttu-id="740fa-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-605">'Identity'</span></span>
- <span data-ttu-id="740fa-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-607">'Razor'</span></span>
- <span data-ttu-id="740fa-608">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-609">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-610">'Blazor'</span></span>
- <span data-ttu-id="740fa-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-611">'Identity'</span></span>
- <span data-ttu-id="740fa-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-613">'Razor'</span></span>
- <span data-ttu-id="740fa-614">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-614">'SignalR' uid:</span></span> 

<span data-ttu-id="740fa-615">------------------ | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-616">'Blazor'</span></span>
- <span data-ttu-id="740fa-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-617">'Identity'</span></span>
- <span data-ttu-id="740fa-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-619">'Razor'</span></span>
- <span data-ttu-id="740fa-620">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-621">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-622">'Blazor'</span></span>
- <span data-ttu-id="740fa-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-623">'Identity'</span></span>
- <span data-ttu-id="740fa-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-625">'Razor'</span></span>
- <span data-ttu-id="740fa-626">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-627">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-628">'Blazor'</span></span>
- <span data-ttu-id="740fa-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-629">'Identity'</span></span>
- <span data-ttu-id="740fa-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-631">'Razor'</span></span>
- <span data-ttu-id="740fa-632">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-633">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-634">'Blazor'</span></span>
- <span data-ttu-id="740fa-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-635">'Identity'</span></span>
- <span data-ttu-id="740fa-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-637">'Razor'</span></span>
- <span data-ttu-id="740fa-638">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-639">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-640">'Blazor'</span></span>
- <span data-ttu-id="740fa-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-641">'Identity'</span></span>
- <span data-ttu-id="740fa-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-643">'Razor'</span></span>
- <span data-ttu-id="740fa-644">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-645">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-646">'Blazor'</span></span>
- <span data-ttu-id="740fa-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-647">'Identity'</span></span>
- <span data-ttu-id="740fa-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-649">'Razor'</span></span>
- <span data-ttu-id="740fa-650">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-651">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-652">'Blazor'</span></span>
- <span data-ttu-id="740fa-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-653">'Identity'</span></span>
- <span data-ttu-id="740fa-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-655">'Razor'</span></span>
- <span data-ttu-id="740fa-656">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-657">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-658">'Blazor'</span></span>
- <span data-ttu-id="740fa-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-659">'Identity'</span></span>
- <span data-ttu-id="740fa-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-661">'Razor'</span></span>
- <span data-ttu-id="740fa-662">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-663">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-664">'Blazor'</span></span>
- <span data-ttu-id="740fa-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-665">'Identity'</span></span>
- <span data-ttu-id="740fa-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-667">'Razor'</span></span>
- <span data-ttu-id="740fa-668">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-669">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-670">'Blazor'</span></span>
- <span data-ttu-id="740fa-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-671">'Identity'</span></span>
- <span data-ttu-id="740fa-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-673">'Razor'</span></span>
- <span data-ttu-id="740fa-674">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-675">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-676">'Blazor'</span></span>
- <span data-ttu-id="740fa-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-677">'Identity'</span></span>
- <span data-ttu-id="740fa-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-679">'Razor'</span></span>
- <span data-ttu-id="740fa-680">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-681">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-682">'Blazor'</span></span>
- <span data-ttu-id="740fa-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-683">'Identity'</span></span>
- <span data-ttu-id="740fa-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-685">'Razor'</span></span>
- <span data-ttu-id="740fa-686">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-687">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-688">'Blazor'</span></span>
- <span data-ttu-id="740fa-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-689">'Identity'</span></span>
- <span data-ttu-id="740fa-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-691">'Razor'</span></span>
- <span data-ttu-id="740fa-692">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-693">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-694">'Blazor'</span></span>
- <span data-ttu-id="740fa-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-695">'Identity'</span></span>
- <span data-ttu-id="740fa-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-697">'Razor'</span></span>
- <span data-ttu-id="740fa-698">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-699">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-700">'Blazor'</span></span>
- <span data-ttu-id="740fa-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-701">'Identity'</span></span>
- <span data-ttu-id="740fa-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-703">'Razor'</span></span>
- <span data-ttu-id="740fa-704">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-705">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-706">'Blazor'</span></span>
- <span data-ttu-id="740fa-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-707">'Identity'</span></span>
- <span data-ttu-id="740fa-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-709">'Razor'</span></span>
- <span data-ttu-id="740fa-710">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-711">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-712">'Blazor'</span></span>
- <span data-ttu-id="740fa-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-713">'Identity'</span></span>
- <span data-ttu-id="740fa-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-715">'Razor'</span></span>
- <span data-ttu-id="740fa-716">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-717">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-718">'Blazor'</span></span>
- <span data-ttu-id="740fa-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-719">'Identity'</span></span>
- <span data-ttu-id="740fa-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-721">'Razor'</span></span>
- <span data-ttu-id="740fa-722">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-723">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-724">'Blazor'</span></span>
- <span data-ttu-id="740fa-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-725">'Identity'</span></span>
- <span data-ttu-id="740fa-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-727">'Razor'</span></span>
- <span data-ttu-id="740fa-728">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-729">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-730">'Blazor'</span></span>
- <span data-ttu-id="740fa-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-731">'Identity'</span></span>
- <span data-ttu-id="740fa-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-733">'Razor'</span></span>
- <span data-ttu-id="740fa-734">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-735">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-736">'Blazor'</span></span>
- <span data-ttu-id="740fa-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-737">'Identity'</span></span>
- <span data-ttu-id="740fa-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-739">'Razor'</span></span>
- <span data-ttu-id="740fa-740">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-741">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-742">'Blazor'</span></span>
- <span data-ttu-id="740fa-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-743">'Identity'</span></span>
- <span data-ttu-id="740fa-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-745">'Razor'</span></span>
- <span data-ttu-id="740fa-746">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-747">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-748">'Blazor'</span></span>
- <span data-ttu-id="740fa-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-749">'Identity'</span></span>
- <span data-ttu-id="740fa-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-751">'Razor'</span></span>
- <span data-ttu-id="740fa-752">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-753">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-754">'Blazor'</span></span>
- <span data-ttu-id="740fa-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-755">'Identity'</span></span>
- <span data-ttu-id="740fa-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-757">'Razor'</span></span>
- <span data-ttu-id="740fa-758">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-759">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-760">'Blazor'</span></span>
- <span data-ttu-id="740fa-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-761">'Identity'</span></span>
- <span data-ttu-id="740fa-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-763">'Razor'</span></span>
- <span data-ttu-id="740fa-764">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-765">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-766">'Blazor'</span></span>
- <span data-ttu-id="740fa-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-767">'Identity'</span></span>
- <span data-ttu-id="740fa-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-769">'Razor'</span></span>
- <span data-ttu-id="740fa-770">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-771">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-772">'Blazor'</span></span>
- <span data-ttu-id="740fa-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-773">'Identity'</span></span>
- <span data-ttu-id="740fa-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-775">'Razor'</span></span>
- <span data-ttu-id="740fa-776">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-777">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-778">'Blazor'</span></span>
- <span data-ttu-id="740fa-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-779">'Identity'</span></span>
- <span data-ttu-id="740fa-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-781">'Razor'</span></span>
- <span data-ttu-id="740fa-782">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-783">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-784">'Blazor'</span></span>
- <span data-ttu-id="740fa-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-785">'Identity'</span></span>
- <span data-ttu-id="740fa-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-787">'Razor'</span></span>
- <span data-ttu-id="740fa-788">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-789">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-790">'Blazor'</span></span>
- <span data-ttu-id="740fa-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-791">'Identity'</span></span>
- <span data-ttu-id="740fa-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-793">'Razor'</span></span>
- <span data-ttu-id="740fa-794">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-794">'SignalR' uid:</span></span> 

<span data-ttu-id="740fa-795">-------------------------------- | | `RuleResult.ContinueRules`(padrão) | Continue aplicando regras.</span><span class="sxs-lookup"><span data-stu-id="740fa-795">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="740fa-796">| | `RuleResult.EndResponse`             | Interrompa a aplicação de regras e envie a resposta.</span><span class="sxs-lookup"><span data-stu-id="740fa-796">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="740fa-797">| | `RuleResult.SkipRemainingRules`      | Interrompa a aplicação de regras e envie o contexto para o próximo middleware.</span><span class="sxs-lookup"><span data-stu-id="740fa-797">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="740fa-798">O aplicativo de exemplo demonstra um método que redireciona as solicitações para caminhos que terminam com *.xml*.</span><span class="sxs-lookup"><span data-stu-id="740fa-798">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="740fa-799">Se uma solicitação for feita para `/file.xml`, ela será redirecionada para `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="740fa-799">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="740fa-800">O código de status é definido como *301 – Movido Permanentemente*.</span><span class="sxs-lookup"><span data-stu-id="740fa-800">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="740fa-801">Quando o navegador faz uma nova solicitação para */xmlfiles/file.xml*, o Middleware de Arquivo Estático fornece o arquivo para o cliente por meio da pasta *wwwroot/xmlfiles*.</span><span class="sxs-lookup"><span data-stu-id="740fa-801">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="740fa-802">Para um redirecionamento, defina explicitamente o código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="740fa-802">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="740fa-803">Caso contrário, um código de status *200 – OK* será retornado e o redirecionamento não ocorrerá no cliente.</span><span class="sxs-lookup"><span data-stu-id="740fa-803">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="740fa-804">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="740fa-804">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="740fa-805">Essa abordagem também pode reconfigurar as solicitações.</span><span class="sxs-lookup"><span data-stu-id="740fa-805">This approach can also rewrite requests.</span></span> <span data-ttu-id="740fa-806">O aplicativo de exemplo demonstra a reconfiguração do caminho de qualquer solicitação de arquivo de texto para fornecer o arquivo de texto *file.txt* por meio da pasta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="740fa-806">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="740fa-807">O Middleware de Arquivo Estático fornece o arquivo com base no caminho de solicitação atualizado:</span><span class="sxs-lookup"><span data-stu-id="740fa-807">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="740fa-808">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="740fa-808">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="740fa-809">Regra baseada em IRule</span><span class="sxs-lookup"><span data-stu-id="740fa-809">IRule-based rule</span></span>

<span data-ttu-id="740fa-810">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para usar a lógica de regra em uma classe que implementa a interface <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="740fa-810">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="740fa-811">`IRule` fornece maior flexibilidade em comparação ao uso da abordagem de regra baseada em método.</span><span class="sxs-lookup"><span data-stu-id="740fa-811">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="740fa-812">A classe de implementação pode incluir um construtor, que permite passar parâmetros para o método <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="740fa-812">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="740fa-813">Os valores dos parâmetros no aplicativo de exemplo para a `extension` e o `newPath` são verificados para atender a várias condições.</span><span class="sxs-lookup"><span data-stu-id="740fa-813">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="740fa-814">A `extension` precisa conter um valor que precisa ser *.png*, *.jpg* ou *.gif*.</span><span class="sxs-lookup"><span data-stu-id="740fa-814">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="740fa-815">Se o `newPath` não é válido, uma <xref:System.ArgumentException> é gerada.</span><span class="sxs-lookup"><span data-stu-id="740fa-815">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="740fa-816">Se uma solicitação é feita para *image.png*, a solicitação é redirecionada para `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="740fa-816">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="740fa-817">Se uma solicitação é feita para *image.jpg*, a solicitação é redirecionada para `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="740fa-817">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="740fa-818">O código de status é definido como *301 – Movido Permanentemente* e o `context.Result` é definida para parar o processamento de regras e enviar a resposta.</span><span class="sxs-lookup"><span data-stu-id="740fa-818">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="740fa-819">Solicitação original: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="740fa-819">Original Request: `/image.png`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando as solicitações e respostas para image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="740fa-821">Solicitação original: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="740fa-821">Original Request: `/image.jpg`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando as solicitações e respostas para image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="740fa-823">Exemplos do regex</span><span class="sxs-lookup"><span data-stu-id="740fa-823">Regex examples</span></span>

| <span data-ttu-id="740fa-824">Goal</span><span class="sxs-lookup"><span data-stu-id="740fa-824">Goal</span></span> | <span data-ttu-id="740fa-825">Cadeia de caracteres do regex &</span><span class="sxs-lookup"><span data-stu-id="740fa-825">Regex String &</span></span><br><span data-ttu-id="740fa-826">Exemplo de correspondência</span><span class="sxs-lookup"><span data-stu-id="740fa-826">Match Example</span></span> | <span data-ttu-id="740fa-827">Cadeia de caracteres de substituição &</span><span class="sxs-lookup"><span data-stu-id="740fa-827">Replacement String &</span></span><br><span data-ttu-id="740fa-828">Exemplo de saída</span><span class="sxs-lookup"><span data-stu-id="740fa-828">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="740fa-829">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-830">'Blazor'</span></span>
- <span data-ttu-id="740fa-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-831">'Identity'</span></span>
- <span data-ttu-id="740fa-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-833">'Razor'</span></span>
- <span data-ttu-id="740fa-834">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-835">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-836">'Blazor'</span></span>
- <span data-ttu-id="740fa-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-837">'Identity'</span></span>
- <span data-ttu-id="740fa-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-839">'Razor'</span></span>
- <span data-ttu-id="740fa-840">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-840">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-841">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-842">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-842">'Blazor'</span></span>
- <span data-ttu-id="740fa-843">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-843">'Identity'</span></span>
- <span data-ttu-id="740fa-844">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-844">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-845">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-845">'Razor'</span></span>
- <span data-ttu-id="740fa-846">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-847">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-848">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-848">'Blazor'</span></span>
- <span data-ttu-id="740fa-849">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-849">'Identity'</span></span>
- <span data-ttu-id="740fa-850">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-850">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-851">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-851">'Razor'</span></span>
- <span data-ttu-id="740fa-852">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-853">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-854">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-854">'Blazor'</span></span>
- <span data-ttu-id="740fa-855">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-855">'Identity'</span></span>
- <span data-ttu-id="740fa-856">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-856">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-857">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-857">'Razor'</span></span>
- <span data-ttu-id="740fa-858">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-859">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-860">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-860">'Blazor'</span></span>
- <span data-ttu-id="740fa-861">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-861">'Identity'</span></span>
- <span data-ttu-id="740fa-862">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-862">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-863">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-863">'Razor'</span></span>
- <span data-ttu-id="740fa-864">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-865">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-866">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-866">'Blazor'</span></span>
- <span data-ttu-id="740fa-867">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-867">'Identity'</span></span>
- <span data-ttu-id="740fa-868">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-868">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-869">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-869">'Razor'</span></span>
- <span data-ttu-id="740fa-870">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-871">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-872">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-872">'Blazor'</span></span>
- <span data-ttu-id="740fa-873">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-873">'Identity'</span></span>
- <span data-ttu-id="740fa-874">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-874">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-875">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-875">'Razor'</span></span>
- <span data-ttu-id="740fa-876">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-877">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-878">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-878">'Blazor'</span></span>
- <span data-ttu-id="740fa-879">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-879">'Identity'</span></span>
- <span data-ttu-id="740fa-880">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-880">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-881">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-881">'Razor'</span></span>
- <span data-ttu-id="740fa-882">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-883">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-884">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-884">'Blazor'</span></span>
- <span data-ttu-id="740fa-885">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-885">'Identity'</span></span>
- <span data-ttu-id="740fa-886">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-886">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-887">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-887">'Razor'</span></span>
- <span data-ttu-id="740fa-888">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-889">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-890">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-890">'Blazor'</span></span>
- <span data-ttu-id="740fa-891">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-891">'Identity'</span></span>
- <span data-ttu-id="740fa-892">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-892">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-893">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-893">'Razor'</span></span>
- <span data-ttu-id="740fa-894">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-895">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-896">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-896">'Blazor'</span></span>
- <span data-ttu-id="740fa-897">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-897">'Identity'</span></span>
- <span data-ttu-id="740fa-898">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-898">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-899">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-899">'Razor'</span></span>
- <span data-ttu-id="740fa-900">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-901">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-902">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-902">'Blazor'</span></span>
- <span data-ttu-id="740fa-903">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-903">'Identity'</span></span>
- <span data-ttu-id="740fa-904">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-904">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-905">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-905">'Razor'</span></span>
- <span data-ttu-id="740fa-906">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-906">'SignalR' uid:</span></span> 

<span data-ttu-id="740fa-907">---------------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-908">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-908">'Blazor'</span></span>
- <span data-ttu-id="740fa-909">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-909">'Identity'</span></span>
- <span data-ttu-id="740fa-910">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-910">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-911">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-911">'Razor'</span></span>
- <span data-ttu-id="740fa-912">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-913">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-914">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-914">'Blazor'</span></span>
- <span data-ttu-id="740fa-915">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-915">'Identity'</span></span>
- <span data-ttu-id="740fa-916">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-916">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-917">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-917">'Razor'</span></span>
- <span data-ttu-id="740fa-918">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-919">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-920">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-920">'Blazor'</span></span>
- <span data-ttu-id="740fa-921">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-921">'Identity'</span></span>
- <span data-ttu-id="740fa-922">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-922">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-923">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-923">'Razor'</span></span>
- <span data-ttu-id="740fa-924">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-925">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-926">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-926">'Blazor'</span></span>
- <span data-ttu-id="740fa-927">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-927">'Identity'</span></span>
- <span data-ttu-id="740fa-928">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-928">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-929">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-929">'Razor'</span></span>
- <span data-ttu-id="740fa-930">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-931">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-932">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-932">'Blazor'</span></span>
- <span data-ttu-id="740fa-933">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-933">'Identity'</span></span>
- <span data-ttu-id="740fa-934">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-934">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-935">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-935">'Razor'</span></span>
- <span data-ttu-id="740fa-936">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-937">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-938">'Blazor'</span></span>
- <span data-ttu-id="740fa-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-939">'Identity'</span></span>
- <span data-ttu-id="740fa-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-941">'Razor'</span></span>
- <span data-ttu-id="740fa-942">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-943">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-944">'Blazor'</span></span>
- <span data-ttu-id="740fa-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-945">'Identity'</span></span>
- <span data-ttu-id="740fa-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-947">'Razor'</span></span>
- <span data-ttu-id="740fa-948">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-949">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-950">'Blazor'</span></span>
- <span data-ttu-id="740fa-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-951">'Identity'</span></span>
- <span data-ttu-id="740fa-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-953">'Razor'</span></span>
- <span data-ttu-id="740fa-954">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-955">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-956">'Blazor'</span></span>
- <span data-ttu-id="740fa-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-957">'Identity'</span></span>
- <span data-ttu-id="740fa-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-959">'Razor'</span></span>
- <span data-ttu-id="740fa-960">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-961">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-962">'Blazor'</span></span>
- <span data-ttu-id="740fa-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-963">'Identity'</span></span>
- <span data-ttu-id="740fa-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-965">'Razor'</span></span>
- <span data-ttu-id="740fa-966">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-967">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-968">'Blazor'</span></span>
- <span data-ttu-id="740fa-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-969">'Identity'</span></span>
- <span data-ttu-id="740fa-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-971">'Razor'</span></span>
- <span data-ttu-id="740fa-972">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-973">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-974">'Blazor'</span></span>
- <span data-ttu-id="740fa-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-975">'Identity'</span></span>
- <span data-ttu-id="740fa-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-977">'Razor'</span></span>
- <span data-ttu-id="740fa-978">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-979">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-980">'Blazor'</span></span>
- <span data-ttu-id="740fa-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-981">'Identity'</span></span>
- <span data-ttu-id="740fa-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-983">'Razor'</span></span>
- <span data-ttu-id="740fa-984">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-985">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-986">'Blazor'</span></span>
- <span data-ttu-id="740fa-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-987">'Identity'</span></span>
- <span data-ttu-id="740fa-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-989">'Razor'</span></span>
- <span data-ttu-id="740fa-990">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-991">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-992">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-992">'Blazor'</span></span>
- <span data-ttu-id="740fa-993">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-993">'Identity'</span></span>
- <span data-ttu-id="740fa-994">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-994">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-995">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-995">'Razor'</span></span>
- <span data-ttu-id="740fa-996">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-997">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-998">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-998">'Blazor'</span></span>
- <span data-ttu-id="740fa-999">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-999">'Identity'</span></span>
- <span data-ttu-id="740fa-1000">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1000">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1001">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1001">'Razor'</span></span>
- <span data-ttu-id="740fa-1002">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1003">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1004">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1004">'Blazor'</span></span>
- <span data-ttu-id="740fa-1005">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1005">'Identity'</span></span>
- <span data-ttu-id="740fa-1006">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1006">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1007">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1007">'Razor'</span></span>
- <span data-ttu-id="740fa-1008">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1008">'SignalR' uid:</span></span> 

<span data-ttu-id="740fa-1009">------------------- | | Regravar caminho em QueryString |`^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="740fa-1009">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="740fa-1010">`/path?var1=abc&var2=123`| | Barra à direita da faixa |`(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="740fa-1010">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="740fa-1011">`/path`| | Impor barra à direita |`(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="740fa-1011">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="740fa-1012">`/path/`| | Evite reescrever solicitações específicas | `^(.*)(?<!\.axd)$`ou`^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="740fa-1012">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="740fa-1013">Sim: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="740fa-1013">Yes: `/resource.htm`</span></span><br><span data-ttu-id="740fa-1014">Não:`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="740fa-1014">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="740fa-1015">`/resource.axd`| | Reorganizar segmentos de URL |`path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="740fa-1015">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="740fa-1016">`path/3/2/1`| | Substituir um segmento de URL |`^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="740fa-1016">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="740fa-1017">Este documento apresenta a reconfiguração de URL com instruções sobre como usar o Middleware de Reconfiguração de URL em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="740fa-1017">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="740fa-1018">A reconfiguração de URL é o ato de modificar URLs de solicitação com base em uma ou mais regras predefinidas.</span><span class="sxs-lookup"><span data-stu-id="740fa-1018">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="740fa-1019">A reconfiguração de URL cria uma abstração entre locais de recursos e seus endereços, de forma que os locais e os endereços não estejam totalmente vinculados.</span><span class="sxs-lookup"><span data-stu-id="740fa-1019">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="740fa-1020">A reconfiguração de URL é útil em vários cenários para:</span><span class="sxs-lookup"><span data-stu-id="740fa-1020">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="740fa-1021">Mover ou substituir recursos de servidor temporária ou permanentemente e manter localizadores estáveis para esses recursos.</span><span class="sxs-lookup"><span data-stu-id="740fa-1021">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="740fa-1022">Dividir o processamento de solicitação entre diferentes aplicativos ou áreas de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-1022">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="740fa-1023">Remover, adicionar ou reorganizar segmentos de URL em solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="740fa-1023">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="740fa-1024">Otimizar URLs públicas para SEO (Otimização do Mecanismo de Pesquisa).</span><span class="sxs-lookup"><span data-stu-id="740fa-1024">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="740fa-1025">Permitir o uso de URLs públicas amigáveis para ajudar os visitantes a prever o conteúdo retornado ao solicitar um recurso.</span><span class="sxs-lookup"><span data-stu-id="740fa-1025">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="740fa-1026">Redirecionar solicitações não seguras para pontos de extremidade seguros.</span><span class="sxs-lookup"><span data-stu-id="740fa-1026">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="740fa-1027">Impedir o hotlink, em que um site externo usa um ativo estático hospedado em outro site vinculando o ativo ao seu próprio conteúdo.</span><span class="sxs-lookup"><span data-stu-id="740fa-1027">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="740fa-1028">A reconfiguração de URL pode reduzir o desempenho de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-1028">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="740fa-1029">Sempre que possível, limite o número e a complexidade das regras.</span><span class="sxs-lookup"><span data-stu-id="740fa-1029">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="740fa-1030">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="740fa-1030">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="740fa-1031">Redirecionamento e reconfiguração de URL</span><span class="sxs-lookup"><span data-stu-id="740fa-1031">URL redirect and URL rewrite</span></span>

<span data-ttu-id="740fa-1032">A diferença entre os termos *redirecionamento de URL* e *reconfiguração de URL* é sutil, mas tem implicações importantes no fornecimento de recursos aos clientes.</span><span class="sxs-lookup"><span data-stu-id="740fa-1032">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="740fa-1033">O Middleware de Reconfiguração de URL do ASP.NET Core pode atender às necessidades de ambos.</span><span class="sxs-lookup"><span data-stu-id="740fa-1033">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="740fa-1034">Um *redirecionamento de URL* envolve uma operação do lado do cliente, em que o cliente é instruído a acessar um recurso em um endereço diferente do que ele solicitou originalmente.</span><span class="sxs-lookup"><span data-stu-id="740fa-1034">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="740fa-1035">Isso exige uma viagem de ida e volta para o servidor.</span><span class="sxs-lookup"><span data-stu-id="740fa-1035">This requires a round trip to the server.</span></span> <span data-ttu-id="740fa-1036">A URL de redirecionamento retornada para o cliente é exibida na barra de endereços do navegador quando o cliente faz uma nova solicitação para o recurso.</span><span class="sxs-lookup"><span data-stu-id="740fa-1036">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="740fa-1037">Se `/resource` é *redirecionado* para `/different-resource`, o servidor responde que o cliente deve obter o recurso em `/different-resource` com um código de status indicando que o redirecionamento é temporário ou permanente.</span><span class="sxs-lookup"><span data-stu-id="740fa-1037">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Um ponto de extremidade de serviço WebAPI foi alterado temporariamente da versão 1 (v1) para a versão 2 (v2) no servidor.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="740fa-1043">Ao redirecionar solicitações para uma URL diferente, indique se o redirecionamento é permanente ou temporário especificando o código de status com a resposta:</span><span class="sxs-lookup"><span data-stu-id="740fa-1043">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="740fa-1044">O código de status *301 – Movido Permanentemente* é usado quando o recurso tem uma nova URL permanente e você deseja instruir o cliente que todas as solicitações futuras para o recurso devem usar a nova URL.</span><span class="sxs-lookup"><span data-stu-id="740fa-1044">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="740fa-1045">*O cliente pode armazenar a resposta em cache e reutilizá-la quando um código de status 301 é recebido.*</span><span class="sxs-lookup"><span data-stu-id="740fa-1045">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="740fa-1046">O código de status *302 – Encontrado* é usado quando o redirecionamento é temporário ou está geralmente sujeito a alterações.</span><span class="sxs-lookup"><span data-stu-id="740fa-1046">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="740fa-1047">O código de status 302 indica para o cliente não armazenar a URL e usá-la no futuro.</span><span class="sxs-lookup"><span data-stu-id="740fa-1047">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="740fa-1048">Para obter mais informações sobre códigos de status, confira [RFC 2616: definições de código de status](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="740fa-1048">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="740fa-1049">Uma *reconfiguração de URL* é uma operação do servidor que fornece um recurso de um endereço de recurso diferente do que o solicitado pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="740fa-1049">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="740fa-1050">A reconfiguração de uma URL não exige uma viagem de ida e volta para o servidor.</span><span class="sxs-lookup"><span data-stu-id="740fa-1050">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="740fa-1051">A URL reconfigurada não é retornada para o cliente e não é exibida na barra de endereços do navegador.</span><span class="sxs-lookup"><span data-stu-id="740fa-1051">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="740fa-1052">Se `/resource` é *reconfigurado* como `/different-resource`, o servidor efetua fetch *internamente* e retorna o recurso em `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1052">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="740fa-1053">Embora o cliente possa ter a capacidade de recuperar o recurso na URL reconfigurada, ele não é informado de que o recurso existe na URL reconfigurada ao fazer a solicitação e receber a resposta.</span><span class="sxs-lookup"><span data-stu-id="740fa-1053">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Um ponto de extremidade de serviço WebAPI foi alterado da versão 1 (v1) para a versão 2 (v2) no servidor.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="740fa-1058">Aplicativo de exemplo de reconfiguração de URL</span><span class="sxs-lookup"><span data-stu-id="740fa-1058">URL rewriting sample app</span></span>

<span data-ttu-id="740fa-1059">Explore as funcionalidades do Middleware de Reconfiguração de URL com o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="740fa-1059">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="740fa-1060">O aplicativo aplica as regras de redirecionamento e reconfiguração e mostra a URL redirecionada ou reconfigurada para vários cenários.</span><span class="sxs-lookup"><span data-stu-id="740fa-1060">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="740fa-1061">Quando usar o Middleware de Reconfiguração de URL</span><span class="sxs-lookup"><span data-stu-id="740fa-1061">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="740fa-1062">Use o Middleware de Reconfiguração de URL quando não for possível usar as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="740fa-1062">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="740fa-1063">Módulo de Reconfiguração de URL com o IIS no Windows Server</span><span class="sxs-lookup"><span data-stu-id="740fa-1063">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="740fa-1064">Módulo mod_rewrite do Apache no Apache Server</span><span class="sxs-lookup"><span data-stu-id="740fa-1064">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="740fa-1065">Reconfiguração de URL no Nginx</span><span class="sxs-lookup"><span data-stu-id="740fa-1065">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="740fa-1066">Além disso, use o middleware quando o aplicativo estiver hospedado no [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente chamado WebListener).</span><span class="sxs-lookup"><span data-stu-id="740fa-1066">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="740fa-1067">As principais razões para usar as tecnologias de reconfiguração de URL baseada em servidor no IIS, no Apache e no Nginx são:</span><span class="sxs-lookup"><span data-stu-id="740fa-1067">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="740fa-1068">O middleware não dá suporte às funcionalidades completas desses módulos.</span><span class="sxs-lookup"><span data-stu-id="740fa-1068">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="740fa-1069">Algumas funcionalidades dos módulos de servidor não funcionam com projetos ASP.NET Core, como as restrições `IsFile` e `IsDirectory` do módulo de Reconfiguração do IIS.</span><span class="sxs-lookup"><span data-stu-id="740fa-1069">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="740fa-1070">Nesses cenários, use o middleware.</span><span class="sxs-lookup"><span data-stu-id="740fa-1070">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="740fa-1071">O desempenho do middleware provavelmente não corresponde ao desempenho dos módulos.</span><span class="sxs-lookup"><span data-stu-id="740fa-1071">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="740fa-1072">Os parâmetros de comparação são a única maneira de saber com certeza qual abordagem degrada mais o desempenho ou se a degradação de desempenho é insignificante.</span><span class="sxs-lookup"><span data-stu-id="740fa-1072">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="740fa-1073">Pacote</span><span class="sxs-lookup"><span data-stu-id="740fa-1073">Package</span></span>

<span data-ttu-id="740fa-1074">Para incluir o middleware em seu projeto, adicione uma referência de pacote ao [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) no arquivo de projeto, que contém o pacote [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite).</span><span class="sxs-lookup"><span data-stu-id="740fa-1074">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="740fa-1075">Quando não estiver usando o metapacote `Microsoft.AspNetCore.App`, adicione uma referência de projeto ao pacote `Microsoft.AspNetCore.Rewrite`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1075">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="740fa-1076">Extensão e opções</span><span class="sxs-lookup"><span data-stu-id="740fa-1076">Extension and options</span></span>

<span data-ttu-id="740fa-1077">Estabeleça regras de reconfiguração e redirecionamento de URL criando uma instância da classe [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) com métodos de extensão para cada uma das regras de reconfiguração.</span><span class="sxs-lookup"><span data-stu-id="740fa-1077">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="740fa-1078">Encadeie várias regras na ordem em que deseja que sejam processadas.</span><span class="sxs-lookup"><span data-stu-id="740fa-1078">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="740fa-1079">As `RewriteOptions` são passadas para o Middleware de Reconfiguração de URL, conforme ele é adicionado ao pipeline de solicitação com <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="740fa-1079">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="740fa-1080">Redirecionar não www para www</span><span class="sxs-lookup"><span data-stu-id="740fa-1080">Redirect non-www to www</span></span>

<span data-ttu-id="740fa-1081">Três opções permitem que o aplicativo redirecione solicitações não `www`para `www`:</span><span class="sxs-lookup"><span data-stu-id="740fa-1081">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="740fa-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Redirecionar permanentemente a solicitação para o `www` subdomínio se a solicitação for não `www` .</span><span class="sxs-lookup"><span data-stu-id="740fa-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="740fa-1083">Redireciona com um código de status [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).</span><span class="sxs-lookup"><span data-stu-id="740fa-1083">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="740fa-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirecione a solicitação para o `www` subdomínio se a solicitação de entrada for não `www` .</span><span class="sxs-lookup"><span data-stu-id="740fa-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="740fa-1085">Redireciona com um código de status [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect).</span><span class="sxs-lookup"><span data-stu-id="740fa-1085">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="740fa-1086">Uma sobrecarga permite que você forneça o código de status para a resposta.</span><span class="sxs-lookup"><span data-stu-id="740fa-1086">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="740fa-1087">Use um campo da classe <xref:Microsoft.AspNetCore.Http.StatusCodes> para uma atribuição de código de status.</span><span class="sxs-lookup"><span data-stu-id="740fa-1087">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="740fa-1088">Redirecionamento de URL</span><span class="sxs-lookup"><span data-stu-id="740fa-1088">URL redirect</span></span>

<span data-ttu-id="740fa-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> para redirecionar as solicitações.</span><span class="sxs-lookup"><span data-stu-id="740fa-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="740fa-1090">O primeiro parâmetro contém o regex para correspondência no caminho da URL de entrada.</span><span class="sxs-lookup"><span data-stu-id="740fa-1090">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="740fa-1091">O segundo parâmetro é a cadeia de caracteres de substituição.</span><span class="sxs-lookup"><span data-stu-id="740fa-1091">The second parameter is the replacement string.</span></span> <span data-ttu-id="740fa-1092">O terceiro parâmetro, se presente, especifica o código de status.</span><span class="sxs-lookup"><span data-stu-id="740fa-1092">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="740fa-1093">Se você não especificar o código de status, ele usará como padrão *302 – Encontrado*, que indica que o recurso foi substituído ou movido temporariamente.</span><span class="sxs-lookup"><span data-stu-id="740fa-1093">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="740fa-1094">Em um navegador com as ferramentas para desenvolvedores habilitadas, faça uma solicitação para o aplicativo de exemplo com o caminho `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1094">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="740fa-1095">O regex corresponde ao caminho de solicitação em `redirect-rule/(.*)` e o caminho é substituído por `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1095">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="740fa-1096">A URL de redirecionamento é enviada novamente ao cliente com um código de status *302 – Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1096">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="740fa-1097">O navegador faz uma nova solicitação na URL de redirecionamento, que é exibida na barra de endereços do navegador.</span><span class="sxs-lookup"><span data-stu-id="740fa-1097">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="740fa-1098">Como não há nenhuma correspondência de regras no aplicativo de exemplo na URL de redirecionamento:</span><span class="sxs-lookup"><span data-stu-id="740fa-1098">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="740fa-1099">A segunda solicitação recebe uma resposta *200 – OK* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-1099">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="740fa-1100">O corpo da resposta mostra a URL de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="740fa-1100">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="740fa-1101">Uma viagem de ida e volta é feita para o servidor quando uma URL é *redirecionada*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1101">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="740fa-1102">Tenha cuidado ao estabelecer regras de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="740fa-1102">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="740fa-1103">As regras de redirecionamento são avaliadas em cada solicitação para o aplicativo, incluindo após um redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="740fa-1103">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="740fa-1104">É fácil criar acidentalmente um *loop de redirecionamentos infinitos*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1104">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="740fa-1105">Solicitação original: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="740fa-1105">Original Request: `/redirect-rule/1234/5678`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando as solicitações e respostas](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="740fa-1107">A parte da expressão contida nos parênteses é chamada um *grupo de captura*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1107">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="740fa-1108">O ponto (`.`) da expressão significa *corresponder a qualquer caractere*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1108">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="740fa-1109">O asterisco (`*`) indica *corresponder ao caractere zero precedente ou mais vezes*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1109">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="740fa-1110">Portanto, os dois últimos segmentos de caminho da URL, `1234/5678`, são capturados pelo grupo de captura `(.*)`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1110">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="740fa-1111">Qualquer valor que você fornecer na URL de solicitação após `redirect-rule/` é capturado por esse único grupo de captura.</span><span class="sxs-lookup"><span data-stu-id="740fa-1111">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="740fa-1112">Na cadeia de caracteres de substituição, os grupos capturados são injetados na cadeia de caracteres com o cifrão (`$`) seguido do número de sequência da captura.</span><span class="sxs-lookup"><span data-stu-id="740fa-1112">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="740fa-1113">O primeiro valor de grupo de captura é obtido com `$1`, o segundo com `$2` e eles continuam em sequência para os grupos de captura no regex.</span><span class="sxs-lookup"><span data-stu-id="740fa-1113">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="740fa-1114">Há apenas um grupo capturado no regex da regra de redirecionamento no aplicativo de exemplo, para que haja apenas um grupo injetado na cadeia de caracteres de substituição, que é `$1`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1114">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="740fa-1115">Quando a regra é aplicada, a URL se torna `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1115">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="740fa-1116">Redirecionamento de URL para um ponto de extremidade seguro</span><span class="sxs-lookup"><span data-stu-id="740fa-1116">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="740fa-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> para redirecionar solicitações HTTP para o mesmo host e caminho usando o protocolo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="740fa-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="740fa-1118">Se o código de status não for fornecido, o middleware usará como padrão *302 – Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1118">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="740fa-1119">Se a porta não for fornecida:</span><span class="sxs-lookup"><span data-stu-id="740fa-1119">If the port isn't supplied:</span></span>

* <span data-ttu-id="740fa-1120">O middleware usará como padrão `null`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1120">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="740fa-1121">O esquema será alterado para `https` (protocolo HTTPS), e o cliente acessará o recurso na porta 443.</span><span class="sxs-lookup"><span data-stu-id="740fa-1121">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="740fa-1122">O exemplo a seguir mostra como definir o código de status como *301 –Movido Permanentemente* e alterar a porta para 5001.</span><span class="sxs-lookup"><span data-stu-id="740fa-1122">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="740fa-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> para redirecionar solicitações não seguras para o mesmo host e caminho com o protocolo HTTPS seguro na porta 443.</span><span class="sxs-lookup"><span data-stu-id="740fa-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="740fa-1124">O middleware define o código de status como *301 – Movido Permanentemente*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1124">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="740fa-1125">Ao redirecionar para um ponto de extremidade seguro sem a necessidade de regras de redirecionamento adicionais, recomendamos o uso do Middleware de Redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="740fa-1125">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="740fa-1126">Para obter mais informações, veja o tópico [Impor HTTPS](xref:security/enforcing-ssl#require-https).</span><span class="sxs-lookup"><span data-stu-id="740fa-1126">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="740fa-1127">O aplicativo de exemplo pode demonstrar como usar `AddRedirectToHttps` ou `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1127">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="740fa-1128">Adicione o método de extensão às `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1128">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="740fa-1129">Faça uma solicitação não segura para o aplicativo em qualquer URL.</span><span class="sxs-lookup"><span data-stu-id="740fa-1129">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="740fa-1130">Ignore o aviso de segurança do navegador de que o certificado autoassinado não é confiável ou crie uma exceção para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="740fa-1130">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="740fa-1131">Solicitação original usando `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="740fa-1131">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando as solicitações e respostas](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="740fa-1133">Solicitação original usando `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="740fa-1133">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando as solicitações e respostas](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="740fa-1135">Reconfiguração de URL</span><span class="sxs-lookup"><span data-stu-id="740fa-1135">URL rewrite</span></span>

<span data-ttu-id="740fa-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> para criar uma regra para a reconfiguração de URLs.</span><span class="sxs-lookup"><span data-stu-id="740fa-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="740fa-1137">O primeiro parâmetro contém o regex para correspondência no caminho da URL de entrada.</span><span class="sxs-lookup"><span data-stu-id="740fa-1137">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="740fa-1138">O segundo parâmetro é a cadeia de caracteres de substituição.</span><span class="sxs-lookup"><span data-stu-id="740fa-1138">The second parameter is the replacement string.</span></span> <span data-ttu-id="740fa-1139">O terceiro parâmetro, `skipRemainingRules: {true|false}`, indica para o middleware se ele deve ou não ignorar regras de reconfiguração adicionais se a regra atual é aplicada.</span><span class="sxs-lookup"><span data-stu-id="740fa-1139">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="740fa-1140">Solicitação original: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="740fa-1140">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando a solicitação e resposta](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="740fa-1142">O acento circunflexo (`^`) no início da expressão significa que a correspondência começa no início do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="740fa-1142">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="740fa-1143">No exemplo anterior com a regra de redirecionamento, `redirect-rule/(.*)`, não há nenhum acento circunflexo (`^`) no início do regex.</span><span class="sxs-lookup"><span data-stu-id="740fa-1143">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="740fa-1144">Portanto, qualquer caractere pode preceder `redirect-rule/` no caminho para uma correspondência com êxito.</span><span class="sxs-lookup"><span data-stu-id="740fa-1144">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="740fa-1145">Caminho</span><span class="sxs-lookup"><span data-stu-id="740fa-1145">Path</span></span>                               | <span data-ttu-id="740fa-1146">Correspondência</span><span class="sxs-lookup"><span data-stu-id="740fa-1146">Match</span></span> |
| ---
<span data-ttu-id="740fa-1147">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1148">'Blazor'</span></span>
- <span data-ttu-id="740fa-1149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1149">'Identity'</span></span>
- <span data-ttu-id="740fa-1150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1150">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1151">'Razor'</span></span>
- <span data-ttu-id="740fa-1152">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1153">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1154">'Blazor'</span></span>
- <span data-ttu-id="740fa-1155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1155">'Identity'</span></span>
- <span data-ttu-id="740fa-1156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1156">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1157">'Razor'</span></span>
- <span data-ttu-id="740fa-1158">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1159">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1160">'Blazor'</span></span>
- <span data-ttu-id="740fa-1161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1161">'Identity'</span></span>
- <span data-ttu-id="740fa-1162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1162">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1163">'Razor'</span></span>
- <span data-ttu-id="740fa-1164">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1165">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1166">'Blazor'</span></span>
- <span data-ttu-id="740fa-1167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1167">'Identity'</span></span>
- <span data-ttu-id="740fa-1168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1168">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1169">'Razor'</span></span>
- <span data-ttu-id="740fa-1170">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1171">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1172">'Blazor'</span></span>
- <span data-ttu-id="740fa-1173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1173">'Identity'</span></span>
- <span data-ttu-id="740fa-1174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1174">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1175">'Razor'</span></span>
- <span data-ttu-id="740fa-1176">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1177">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1178">'Blazor'</span></span>
- <span data-ttu-id="740fa-1179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1179">'Identity'</span></span>
- <span data-ttu-id="740fa-1180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1180">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1181">'Razor'</span></span>
- <span data-ttu-id="740fa-1182">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1183">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1184">'Blazor'</span></span>
- <span data-ttu-id="740fa-1185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1185">'Identity'</span></span>
- <span data-ttu-id="740fa-1186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1186">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1187">'Razor'</span></span>
- <span data-ttu-id="740fa-1188">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1189">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1190">'Blazor'</span></span>
- <span data-ttu-id="740fa-1191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1191">'Identity'</span></span>
- <span data-ttu-id="740fa-1192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1192">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1193">'Razor'</span></span>
- <span data-ttu-id="740fa-1194">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1195">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1196">'Blazor'</span></span>
- <span data-ttu-id="740fa-1197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1197">'Identity'</span></span>
- <span data-ttu-id="740fa-1198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1198">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1199">'Razor'</span></span>
- <span data-ttu-id="740fa-1200">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1201">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1202">'Blazor'</span></span>
- <span data-ttu-id="740fa-1203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1203">'Identity'</span></span>
- <span data-ttu-id="740fa-1204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1204">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1205">'Razor'</span></span>
- <span data-ttu-id="740fa-1206">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1207">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1208">'Blazor'</span></span>
- <span data-ttu-id="740fa-1209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1209">'Identity'</span></span>
- <span data-ttu-id="740fa-1210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1210">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1211">'Razor'</span></span>
- <span data-ttu-id="740fa-1212">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1213">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1214">'Blazor'</span></span>
- <span data-ttu-id="740fa-1215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1215">'Identity'</span></span>
- <span data-ttu-id="740fa-1216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1216">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1217">'Razor'</span></span>
- <span data-ttu-id="740fa-1218">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1219">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1220">'Blazor'</span></span>
- <span data-ttu-id="740fa-1221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1221">'Identity'</span></span>
- <span data-ttu-id="740fa-1222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1222">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1223">'Razor'</span></span>
- <span data-ttu-id="740fa-1224">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1225">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1226">'Blazor'</span></span>
- <span data-ttu-id="740fa-1227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1227">'Identity'</span></span>
- <span data-ttu-id="740fa-1228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1228">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1229">'Razor'</span></span>
- <span data-ttu-id="740fa-1230">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1231">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1232">'Blazor'</span></span>
- <span data-ttu-id="740fa-1233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1233">'Identity'</span></span>
- <span data-ttu-id="740fa-1234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1234">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1235">'Razor'</span></span>
- <span data-ttu-id="740fa-1236">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1236">'SignalR' uid:</span></span> 

<span data-ttu-id="740fa-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Sim | | `/my-cool-redirect-rule/1234/5678` | Sim | | `/anotherredirect-rule/1234/5678`  | Sim |</span><span class="sxs-lookup"><span data-stu-id="740fa-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="740fa-1238">A regra de reconfiguração, `^rewrite-rule/(\d+)/(\d+)`, corresponde apenas a caminhos se eles são iniciados com `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1238">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="740fa-1239">Na tabela a seguir, observe a diferença na correspondência.</span><span class="sxs-lookup"><span data-stu-id="740fa-1239">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="740fa-1240">Caminho</span><span class="sxs-lookup"><span data-stu-id="740fa-1240">Path</span></span>                              | <span data-ttu-id="740fa-1241">Correspondência</span><span class="sxs-lookup"><span data-stu-id="740fa-1241">Match</span></span> |
| ---
<span data-ttu-id="740fa-1242">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1243">'Blazor'</span></span>
- <span data-ttu-id="740fa-1244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1244">'Identity'</span></span>
- <span data-ttu-id="740fa-1245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1245">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1246">'Razor'</span></span>
- <span data-ttu-id="740fa-1247">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1248">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1249">'Blazor'</span></span>
- <span data-ttu-id="740fa-1250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1250">'Identity'</span></span>
- <span data-ttu-id="740fa-1251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1251">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1252">'Razor'</span></span>
- <span data-ttu-id="740fa-1253">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1254">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1255">'Blazor'</span></span>
- <span data-ttu-id="740fa-1256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1256">'Identity'</span></span>
- <span data-ttu-id="740fa-1257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1257">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1258">'Razor'</span></span>
- <span data-ttu-id="740fa-1259">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1260">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1261">'Blazor'</span></span>
- <span data-ttu-id="740fa-1262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1262">'Identity'</span></span>
- <span data-ttu-id="740fa-1263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1263">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1264">'Razor'</span></span>
- <span data-ttu-id="740fa-1265">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1266">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1267">'Blazor'</span></span>
- <span data-ttu-id="740fa-1268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1268">'Identity'</span></span>
- <span data-ttu-id="740fa-1269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1269">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1270">'Razor'</span></span>
- <span data-ttu-id="740fa-1271">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1272">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1273">'Blazor'</span></span>
- <span data-ttu-id="740fa-1274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1274">'Identity'</span></span>
- <span data-ttu-id="740fa-1275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1275">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1276">'Razor'</span></span>
- <span data-ttu-id="740fa-1277">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1278">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1279">'Blazor'</span></span>
- <span data-ttu-id="740fa-1280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1280">'Identity'</span></span>
- <span data-ttu-id="740fa-1281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1281">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1282">'Razor'</span></span>
- <span data-ttu-id="740fa-1283">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1284">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1285">'Blazor'</span></span>
- <span data-ttu-id="740fa-1286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1286">'Identity'</span></span>
- <span data-ttu-id="740fa-1287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1287">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1288">'Razor'</span></span>
- <span data-ttu-id="740fa-1289">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1290">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1291">'Blazor'</span></span>
- <span data-ttu-id="740fa-1292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1292">'Identity'</span></span>
- <span data-ttu-id="740fa-1293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1293">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1294">'Razor'</span></span>
- <span data-ttu-id="740fa-1295">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1296">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1297">'Blazor'</span></span>
- <span data-ttu-id="740fa-1298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1298">'Identity'</span></span>
- <span data-ttu-id="740fa-1299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1299">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1300">'Razor'</span></span>
- <span data-ttu-id="740fa-1301">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1302">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1303">'Blazor'</span></span>
- <span data-ttu-id="740fa-1304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1304">'Identity'</span></span>
- <span data-ttu-id="740fa-1305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1305">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1306">'Razor'</span></span>
- <span data-ttu-id="740fa-1307">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1308">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1309">'Blazor'</span></span>
- <span data-ttu-id="740fa-1310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1310">'Identity'</span></span>
- <span data-ttu-id="740fa-1311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1311">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1312">'Razor'</span></span>
- <span data-ttu-id="740fa-1313">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1314">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1315">'Blazor'</span></span>
- <span data-ttu-id="740fa-1316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1316">'Identity'</span></span>
- <span data-ttu-id="740fa-1317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1317">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1318">'Razor'</span></span>
- <span data-ttu-id="740fa-1319">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1320">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1321">'Blazor'</span></span>
- <span data-ttu-id="740fa-1322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1322">'Identity'</span></span>
- <span data-ttu-id="740fa-1323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1323">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1324">'Razor'</span></span>
- <span data-ttu-id="740fa-1325">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1325">'SignalR' uid:</span></span> 

<span data-ttu-id="740fa-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Sim | | `/my-cool-rewrite-rule/1234/5678` | Não | | `/anotherrewrite-rule/1234/5678`  | Não |</span><span class="sxs-lookup"><span data-stu-id="740fa-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="740fa-1327">Após a parte `^rewrite-rule/` da expressão, há dois grupos de captura, `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1327">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="740fa-1328">O `\d` significa *corresponder a um dígito (número)*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1328">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="740fa-1329">O sinal de adição (`+`) significa *corresponder a um ou mais caracteres anteriores*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1329">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="740fa-1330">Portanto, a URL precisa conter um número seguido de uma barra "/" seguida de outro número.</span><span class="sxs-lookup"><span data-stu-id="740fa-1330">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="740fa-1331">Esses grupos de captura são injetados na URL reconfigurada como `$1` e `$2`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1331">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="740fa-1332">A cadeia de caracteres de substituição da regra de reconfiguração coloca os grupos capturados na cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="740fa-1332">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="740fa-1333">O caminho solicitado de `/rewrite-rule/1234/5678` foi reconfigurado para obter o recurso em `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1333">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="740fa-1334">Se uma cadeia de consulta estiver presente na solicitação original, ela será preservada quando a URL for reconfigurada.</span><span class="sxs-lookup"><span data-stu-id="740fa-1334">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="740fa-1335">Não há nenhuma viagem de ida e volta para o servidor para obtenção do recurso.</span><span class="sxs-lookup"><span data-stu-id="740fa-1335">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="740fa-1336">Se o recurso existir, ele será buscado e retornado para o cliente com um código de status *200 – OK*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1336">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="740fa-1337">Como o cliente não é redirecionado, a URL na barra de endereços do navegador não é alterada.</span><span class="sxs-lookup"><span data-stu-id="740fa-1337">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="740fa-1338">Os clientes não conseguem detectar que uma operação de reconfiguração de URL ocorreu no servidor.</span><span class="sxs-lookup"><span data-stu-id="740fa-1338">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="740fa-1339">Use `skipRemainingRules: true` sempre que possível, porque as regras de correspondência são computacionalmente caras e aumentam o tempo de resposta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-1339">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="740fa-1340">Para a resposta mais rápida do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="740fa-1340">For the fastest app response:</span></span>
>
> * <span data-ttu-id="740fa-1341">Ordene as regras de reconfiguração da regra com correspondência mais frequente para a regra com correspondência menos frequente.</span><span class="sxs-lookup"><span data-stu-id="740fa-1341">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="740fa-1342">Ignore o processamento das regras restantes quando ocorrer uma correspondência e nenhum processamento de regra adicional for necessário.</span><span class="sxs-lookup"><span data-stu-id="740fa-1342">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="740fa-1343">mod_rewrite do Apache</span><span class="sxs-lookup"><span data-stu-id="740fa-1343">Apache mod_rewrite</span></span>

<span data-ttu-id="740fa-1344">Aplique as regras do mod_rewrite do Apache com <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="740fa-1344">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="740fa-1345">Verifique se o arquivo de regras foi implantado com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-1345">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="740fa-1346">Para obter mais informações e exemplos de regras de mod_rewrite, consulte [mod_rewrite do Apache](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="740fa-1346">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="740fa-1347">Um <xref:System.IO.StreamReader> é usado para ler as regras do arquivo de regras *ApacheModRewrite.txt*:</span><span class="sxs-lookup"><span data-stu-id="740fa-1347">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="740fa-1348">O aplicativo de exemplo redireciona solicitações de `/apache-mod-rules-redirect/(.\*)` para `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1348">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="740fa-1349">O código de status da resposta é *302 – Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1349">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="740fa-1350">Solicitação original: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="740fa-1350">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando as solicitações e respostas](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="740fa-1352">O middleware dá suporte às seguintes variáveis de servidor do mod_rewrite do Apache:</span><span class="sxs-lookup"><span data-stu-id="740fa-1352">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="740fa-1353">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="740fa-1353">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="740fa-1354">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="740fa-1354">HTTP_ACCEPT</span></span>
* <span data-ttu-id="740fa-1355">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="740fa-1355">HTTP_CONNECTION</span></span>
* <span data-ttu-id="740fa-1356">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="740fa-1356">HTTP_COOKIE</span></span>
* <span data-ttu-id="740fa-1357">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="740fa-1357">HTTP_FORWARDED</span></span>
* <span data-ttu-id="740fa-1358">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="740fa-1358">HTTP_HOST</span></span>
* <span data-ttu-id="740fa-1359">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="740fa-1359">HTTP_REFERER</span></span>
* <span data-ttu-id="740fa-1360">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="740fa-1360">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="740fa-1361">HTTPS</span><span class="sxs-lookup"><span data-stu-id="740fa-1361">HTTPS</span></span>
* <span data-ttu-id="740fa-1362">IPV6</span><span class="sxs-lookup"><span data-stu-id="740fa-1362">IPV6</span></span>
* <span data-ttu-id="740fa-1363">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="740fa-1363">QUERY_STRING</span></span>
* <span data-ttu-id="740fa-1364">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="740fa-1364">REMOTE_ADDR</span></span>
* <span data-ttu-id="740fa-1365">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="740fa-1365">REMOTE_PORT</span></span>
* <span data-ttu-id="740fa-1366">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="740fa-1366">REQUEST_FILENAME</span></span>
* <span data-ttu-id="740fa-1367">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="740fa-1367">REQUEST_METHOD</span></span>
* <span data-ttu-id="740fa-1368">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="740fa-1368">REQUEST_SCHEME</span></span>
* <span data-ttu-id="740fa-1369">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="740fa-1369">REQUEST_URI</span></span>
* <span data-ttu-id="740fa-1370">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="740fa-1370">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="740fa-1371">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="740fa-1371">SERVER_ADDR</span></span>
* <span data-ttu-id="740fa-1372">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="740fa-1372">SERVER_PORT</span></span>
* <span data-ttu-id="740fa-1373">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="740fa-1373">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="740fa-1374">TIME</span><span class="sxs-lookup"><span data-stu-id="740fa-1374">TIME</span></span>
* <span data-ttu-id="740fa-1375">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="740fa-1375">TIME_DAY</span></span>
* <span data-ttu-id="740fa-1376">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="740fa-1376">TIME_HOUR</span></span>
* <span data-ttu-id="740fa-1377">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="740fa-1377">TIME_MIN</span></span>
* <span data-ttu-id="740fa-1378">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="740fa-1378">TIME_MON</span></span>
* <span data-ttu-id="740fa-1379">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="740fa-1379">TIME_SEC</span></span>
* <span data-ttu-id="740fa-1380">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="740fa-1380">TIME_WDAY</span></span>
* <span data-ttu-id="740fa-1381">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="740fa-1381">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="740fa-1382">Regras do Módulo de Reconfiguração de URL do IIS</span><span class="sxs-lookup"><span data-stu-id="740fa-1382">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="740fa-1383">Para usar o mesmo conjunto de regras que se aplica ao Módulo de Reconfiguração de URL do IIS, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="740fa-1383">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="740fa-1384">Verifique se o arquivo de regras foi implantado com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-1384">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="740fa-1385">Não instrua o middleware a usar o arquivo *web.config* do aplicativo quando ele estiver em execução no IIS do Windows Server.</span><span class="sxs-lookup"><span data-stu-id="740fa-1385">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="740fa-1386">Com o IIS, essas regras devem ser armazenadas fora do arquivo *web.config* do aplicativo para evitar conflitos com o módulo de Reconfiguração do IIS.</span><span class="sxs-lookup"><span data-stu-id="740fa-1386">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="740fa-1387">Para obter mais informações e exemplos de regras do Módulo de Reconfiguração de URL do IIS, consulte [Usando o Módulo de Reconfiguração de URL 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) e [Referência de configuração do Módulo de Reconfiguração de URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="740fa-1387">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="740fa-1388">Um <xref:System.IO.StreamReader> é usado para ler as regras do arquivo de regras *IISUrlRewrite.xml*:</span><span class="sxs-lookup"><span data-stu-id="740fa-1388">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="740fa-1389">O aplicativo de exemplo reconfigura as solicitações de `/iis-rules-rewrite/(.*)` para `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1389">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="740fa-1390">A resposta é enviada ao cliente com um código de status *200 – OK*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1390">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="740fa-1391">Solicitação original: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="740fa-1391">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando a solicitação e resposta](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="740fa-1393">Caso você tenha um Módulo de Reconfiguração do IIS ativo com regras no nível do servidor configuradas que poderiam afetar o aplicativo de maneiras indesejadas, desabilite o Módulo de Reconfiguração do IIS em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="740fa-1393">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="740fa-1394">Para obter mais informações, consulte [Desabilitando módulos do IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="740fa-1394">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="740fa-1395">Recursos sem suporte</span><span class="sxs-lookup"><span data-stu-id="740fa-1395">Unsupported features</span></span>

<span data-ttu-id="740fa-1396">O middleware liberado com o ASP.NET Core 2.x não dá suporte aos seguintes recursos do Módulo de Reconfiguração de URL do IIS:</span><span class="sxs-lookup"><span data-stu-id="740fa-1396">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="740fa-1397">Regras de saída</span><span class="sxs-lookup"><span data-stu-id="740fa-1397">Outbound Rules</span></span>
* <span data-ttu-id="740fa-1398">Variáveis de servidor personalizadas</span><span class="sxs-lookup"><span data-stu-id="740fa-1398">Custom Server Variables</span></span>
* <span data-ttu-id="740fa-1399">Curingas</span><span class="sxs-lookup"><span data-stu-id="740fa-1399">Wildcards</span></span>
* <span data-ttu-id="740fa-1400">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="740fa-1400">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="740fa-1401">Variáveis de servidor compatíveis</span><span class="sxs-lookup"><span data-stu-id="740fa-1401">Supported server variables</span></span>

<span data-ttu-id="740fa-1402">O middleware dá suporte às seguintes variáveis de servidor do Módulo de Reconfiguração de URL do IIS:</span><span class="sxs-lookup"><span data-stu-id="740fa-1402">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="740fa-1403">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="740fa-1403">CONTENT_LENGTH</span></span>
* <span data-ttu-id="740fa-1404">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="740fa-1404">CONTENT_TYPE</span></span>
* <span data-ttu-id="740fa-1405">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="740fa-1405">HTTP_ACCEPT</span></span>
* <span data-ttu-id="740fa-1406">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="740fa-1406">HTTP_CONNECTION</span></span>
* <span data-ttu-id="740fa-1407">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="740fa-1407">HTTP_COOKIE</span></span>
* <span data-ttu-id="740fa-1408">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="740fa-1408">HTTP_HOST</span></span>
* <span data-ttu-id="740fa-1409">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="740fa-1409">HTTP_REFERER</span></span>
* <span data-ttu-id="740fa-1410">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="740fa-1410">HTTP_URL</span></span>
* <span data-ttu-id="740fa-1411">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="740fa-1411">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="740fa-1412">HTTPS</span><span class="sxs-lookup"><span data-stu-id="740fa-1412">HTTPS</span></span>
* <span data-ttu-id="740fa-1413">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="740fa-1413">LOCAL_ADDR</span></span>
* <span data-ttu-id="740fa-1414">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="740fa-1414">QUERY_STRING</span></span>
* <span data-ttu-id="740fa-1415">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="740fa-1415">REMOTE_ADDR</span></span>
* <span data-ttu-id="740fa-1416">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="740fa-1416">REMOTE_PORT</span></span>
* <span data-ttu-id="740fa-1417">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="740fa-1417">REQUEST_FILENAME</span></span>
* <span data-ttu-id="740fa-1418">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="740fa-1418">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="740fa-1419">Também obtenha um <xref:Microsoft.Extensions.FileProviders.IFileProvider> por meio de um <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="740fa-1419">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="740fa-1420">Essa abordagem pode fornecer maior flexibilidade para o local dos arquivos de regras de reconfiguração.</span><span class="sxs-lookup"><span data-stu-id="740fa-1420">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="740fa-1421">Verifique se os arquivos de regras de reconfiguração são implantados no servidor no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="740fa-1421">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="740fa-1422">Regra baseada em método</span><span class="sxs-lookup"><span data-stu-id="740fa-1422">Method-based rule</span></span>

<span data-ttu-id="740fa-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para implementar sua própria lógica de regra em um método.</span><span class="sxs-lookup"><span data-stu-id="740fa-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="740fa-1424">`Add` expõe o <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, que disponibiliza o <xref:Microsoft.AspNetCore.Http.HttpContext> para uso no método.</span><span class="sxs-lookup"><span data-stu-id="740fa-1424">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="740fa-1425">O [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determina como o processamento adicional de pipeline é feito.</span><span class="sxs-lookup"><span data-stu-id="740fa-1425">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="740fa-1426">Defina o valor como um dos campos <xref:Microsoft.AspNetCore.Rewrite.RuleResult> descritos na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="740fa-1426">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="740fa-1427">Ação</span><span class="sxs-lookup"><span data-stu-id="740fa-1427">Action</span></span>                                                           |
| ---
<span data-ttu-id="740fa-1428">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1429">'Blazor'</span></span>
- <span data-ttu-id="740fa-1430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1430">'Identity'</span></span>
- <span data-ttu-id="740fa-1431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1431">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1432">'Razor'</span></span>
- <span data-ttu-id="740fa-1433">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1434">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1435">'Blazor'</span></span>
- <span data-ttu-id="740fa-1436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1436">'Identity'</span></span>
- <span data-ttu-id="740fa-1437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1437">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1438">'Razor'</span></span>
- <span data-ttu-id="740fa-1439">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1439">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1440">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1441">'Blazor'</span></span>
- <span data-ttu-id="740fa-1442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1442">'Identity'</span></span>
- <span data-ttu-id="740fa-1443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1443">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1444">'Razor'</span></span>
- <span data-ttu-id="740fa-1445">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1446">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1447">'Blazor'</span></span>
- <span data-ttu-id="740fa-1448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1448">'Identity'</span></span>
- <span data-ttu-id="740fa-1449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1449">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1450">'Razor'</span></span>
- <span data-ttu-id="740fa-1451">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1451">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1452">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1453">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1453">'Blazor'</span></span>
- <span data-ttu-id="740fa-1454">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1454">'Identity'</span></span>
- <span data-ttu-id="740fa-1455">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1455">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1456">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1456">'Razor'</span></span>
- <span data-ttu-id="740fa-1457">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1457">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1458">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1459">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1459">'Blazor'</span></span>
- <span data-ttu-id="740fa-1460">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1460">'Identity'</span></span>
- <span data-ttu-id="740fa-1461">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1461">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1462">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1462">'Razor'</span></span>
- <span data-ttu-id="740fa-1463">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1463">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1464">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1465">'Blazor'</span></span>
- <span data-ttu-id="740fa-1466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1466">'Identity'</span></span>
- <span data-ttu-id="740fa-1467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1467">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1468">'Razor'</span></span>
- <span data-ttu-id="740fa-1469">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1470">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1471">'Blazor'</span></span>
- <span data-ttu-id="740fa-1472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1472">'Identity'</span></span>
- <span data-ttu-id="740fa-1473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1473">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1474">'Razor'</span></span>
- <span data-ttu-id="740fa-1475">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1476">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1477">'Blazor'</span></span>
- <span data-ttu-id="740fa-1478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1478">'Identity'</span></span>
- <span data-ttu-id="740fa-1479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1479">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1480">'Razor'</span></span>
- <span data-ttu-id="740fa-1481">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1481">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1482">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1483">'Blazor'</span></span>
- <span data-ttu-id="740fa-1484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1484">'Identity'</span></span>
- <span data-ttu-id="740fa-1485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1485">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1486">'Razor'</span></span>
- <span data-ttu-id="740fa-1487">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1488">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1489">'Blazor'</span></span>
- <span data-ttu-id="740fa-1490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1490">'Identity'</span></span>
- <span data-ttu-id="740fa-1491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1491">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1492">'Razor'</span></span>
- <span data-ttu-id="740fa-1493">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1494">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1495">'Blazor'</span></span>
- <span data-ttu-id="740fa-1496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1496">'Identity'</span></span>
- <span data-ttu-id="740fa-1497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1497">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1498">'Razor'</span></span>
- <span data-ttu-id="740fa-1499">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1499">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1500">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1501">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1501">'Blazor'</span></span>
- <span data-ttu-id="740fa-1502">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1502">'Identity'</span></span>
- <span data-ttu-id="740fa-1503">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1503">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1504">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1504">'Razor'</span></span>
- <span data-ttu-id="740fa-1505">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1505">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1506">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1507">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1507">'Blazor'</span></span>
- <span data-ttu-id="740fa-1508">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1508">'Identity'</span></span>
- <span data-ttu-id="740fa-1509">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1509">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1510">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1510">'Razor'</span></span>
- <span data-ttu-id="740fa-1511">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1511">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1512">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1513">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1513">'Blazor'</span></span>
- <span data-ttu-id="740fa-1514">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1514">'Identity'</span></span>
- <span data-ttu-id="740fa-1515">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1515">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1516">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1516">'Razor'</span></span>
- <span data-ttu-id="740fa-1517">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1517">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1518">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1519">'Blazor'</span></span>
- <span data-ttu-id="740fa-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1520">'Identity'</span></span>
- <span data-ttu-id="740fa-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1522">'Razor'</span></span>
- <span data-ttu-id="740fa-1523">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1523">'SignalR' uid:</span></span> 

<span data-ttu-id="740fa-1524">------------------ | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1525">'Blazor'</span></span>
- <span data-ttu-id="740fa-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1526">'Identity'</span></span>
- <span data-ttu-id="740fa-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1528">'Razor'</span></span>
- <span data-ttu-id="740fa-1529">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1530">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1531">'Blazor'</span></span>
- <span data-ttu-id="740fa-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1532">'Identity'</span></span>
- <span data-ttu-id="740fa-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1534">'Razor'</span></span>
- <span data-ttu-id="740fa-1535">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1536">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1537">'Blazor'</span></span>
- <span data-ttu-id="740fa-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1538">'Identity'</span></span>
- <span data-ttu-id="740fa-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1540">'Razor'</span></span>
- <span data-ttu-id="740fa-1541">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1542">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1543">'Blazor'</span></span>
- <span data-ttu-id="740fa-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1544">'Identity'</span></span>
- <span data-ttu-id="740fa-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1546">'Razor'</span></span>
- <span data-ttu-id="740fa-1547">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1548">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1549">'Blazor'</span></span>
- <span data-ttu-id="740fa-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1550">'Identity'</span></span>
- <span data-ttu-id="740fa-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1552">'Razor'</span></span>
- <span data-ttu-id="740fa-1553">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1553">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1554">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1555">'Blazor'</span></span>
- <span data-ttu-id="740fa-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1556">'Identity'</span></span>
- <span data-ttu-id="740fa-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1558">'Razor'</span></span>
- <span data-ttu-id="740fa-1559">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1560">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1561">'Blazor'</span></span>
- <span data-ttu-id="740fa-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1562">'Identity'</span></span>
- <span data-ttu-id="740fa-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1564">'Razor'</span></span>
- <span data-ttu-id="740fa-1565">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1565">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1566">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1567">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1567">'Blazor'</span></span>
- <span data-ttu-id="740fa-1568">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1568">'Identity'</span></span>
- <span data-ttu-id="740fa-1569">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1569">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1570">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1570">'Razor'</span></span>
- <span data-ttu-id="740fa-1571">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1572">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1573">'Blazor'</span></span>
- <span data-ttu-id="740fa-1574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1574">'Identity'</span></span>
- <span data-ttu-id="740fa-1575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1575">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1576">'Razor'</span></span>
- <span data-ttu-id="740fa-1577">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1578">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1579">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1579">'Blazor'</span></span>
- <span data-ttu-id="740fa-1580">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1580">'Identity'</span></span>
- <span data-ttu-id="740fa-1581">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1581">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1582">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1582">'Razor'</span></span>
- <span data-ttu-id="740fa-1583">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1584">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1585">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1585">'Blazor'</span></span>
- <span data-ttu-id="740fa-1586">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1586">'Identity'</span></span>
- <span data-ttu-id="740fa-1587">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1587">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1588">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1588">'Razor'</span></span>
- <span data-ttu-id="740fa-1589">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1590">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1591">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1591">'Blazor'</span></span>
- <span data-ttu-id="740fa-1592">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1592">'Identity'</span></span>
- <span data-ttu-id="740fa-1593">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1593">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1594">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1594">'Razor'</span></span>
- <span data-ttu-id="740fa-1595">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1595">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1596">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1597">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1597">'Blazor'</span></span>
- <span data-ttu-id="740fa-1598">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1598">'Identity'</span></span>
- <span data-ttu-id="740fa-1599">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1599">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1600">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1600">'Razor'</span></span>
- <span data-ttu-id="740fa-1601">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1601">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1602">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1603">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1603">'Blazor'</span></span>
- <span data-ttu-id="740fa-1604">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1604">'Identity'</span></span>
- <span data-ttu-id="740fa-1605">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1605">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1606">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1606">'Razor'</span></span>
- <span data-ttu-id="740fa-1607">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1607">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1608">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1609">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1609">'Blazor'</span></span>
- <span data-ttu-id="740fa-1610">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1610">'Identity'</span></span>
- <span data-ttu-id="740fa-1611">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1611">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1612">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1612">'Razor'</span></span>
- <span data-ttu-id="740fa-1613">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1613">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1614">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1615">'Blazor'</span></span>
- <span data-ttu-id="740fa-1616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1616">'Identity'</span></span>
- <span data-ttu-id="740fa-1617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1617">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1618">'Razor'</span></span>
- <span data-ttu-id="740fa-1619">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1620">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1621">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1621">'Blazor'</span></span>
- <span data-ttu-id="740fa-1622">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1622">'Identity'</span></span>
- <span data-ttu-id="740fa-1623">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1623">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1624">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1624">'Razor'</span></span>
- <span data-ttu-id="740fa-1625">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1625">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1626">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1627">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1627">'Blazor'</span></span>
- <span data-ttu-id="740fa-1628">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1628">'Identity'</span></span>
- <span data-ttu-id="740fa-1629">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1629">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1630">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1630">'Razor'</span></span>
- <span data-ttu-id="740fa-1631">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1631">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1632">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1633">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1633">'Blazor'</span></span>
- <span data-ttu-id="740fa-1634">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1634">'Identity'</span></span>
- <span data-ttu-id="740fa-1635">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1635">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1636">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1636">'Razor'</span></span>
- <span data-ttu-id="740fa-1637">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1637">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1638">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1639">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1639">'Blazor'</span></span>
- <span data-ttu-id="740fa-1640">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1640">'Identity'</span></span>
- <span data-ttu-id="740fa-1641">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1641">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1642">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1642">'Razor'</span></span>
- <span data-ttu-id="740fa-1643">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1643">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1644">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1645">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1645">'Blazor'</span></span>
- <span data-ttu-id="740fa-1646">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1646">'Identity'</span></span>
- <span data-ttu-id="740fa-1647">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1647">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1648">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1648">'Razor'</span></span>
- <span data-ttu-id="740fa-1649">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1649">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1650">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1651">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1651">'Blazor'</span></span>
- <span data-ttu-id="740fa-1652">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1652">'Identity'</span></span>
- <span data-ttu-id="740fa-1653">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1653">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1654">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1654">'Razor'</span></span>
- <span data-ttu-id="740fa-1655">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1655">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1656">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1657">'Blazor'</span></span>
- <span data-ttu-id="740fa-1658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1658">'Identity'</span></span>
- <span data-ttu-id="740fa-1659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1659">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1660">'Razor'</span></span>
- <span data-ttu-id="740fa-1661">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1662">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1663">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1663">'Blazor'</span></span>
- <span data-ttu-id="740fa-1664">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1664">'Identity'</span></span>
- <span data-ttu-id="740fa-1665">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1665">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1666">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1666">'Razor'</span></span>
- <span data-ttu-id="740fa-1667">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1667">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1668">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1669">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1669">'Blazor'</span></span>
- <span data-ttu-id="740fa-1670">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1670">'Identity'</span></span>
- <span data-ttu-id="740fa-1671">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1671">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1672">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1672">'Razor'</span></span>
- <span data-ttu-id="740fa-1673">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1673">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1674">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1675">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1675">'Blazor'</span></span>
- <span data-ttu-id="740fa-1676">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1676">'Identity'</span></span>
- <span data-ttu-id="740fa-1677">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1677">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1678">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1678">'Razor'</span></span>
- <span data-ttu-id="740fa-1679">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1679">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1680">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1681">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1681">'Blazor'</span></span>
- <span data-ttu-id="740fa-1682">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1682">'Identity'</span></span>
- <span data-ttu-id="740fa-1683">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1683">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1684">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1684">'Razor'</span></span>
- <span data-ttu-id="740fa-1685">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1685">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1686">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1687">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1687">'Blazor'</span></span>
- <span data-ttu-id="740fa-1688">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1688">'Identity'</span></span>
- <span data-ttu-id="740fa-1689">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1689">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1690">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1690">'Razor'</span></span>
- <span data-ttu-id="740fa-1691">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1691">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1692">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1693">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1693">'Blazor'</span></span>
- <span data-ttu-id="740fa-1694">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1694">'Identity'</span></span>
- <span data-ttu-id="740fa-1695">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1695">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1696">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1696">'Razor'</span></span>
- <span data-ttu-id="740fa-1697">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1697">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1698">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1699">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1699">'Blazor'</span></span>
- <span data-ttu-id="740fa-1700">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1700">'Identity'</span></span>
- <span data-ttu-id="740fa-1701">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1701">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1702">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1702">'Razor'</span></span>
- <span data-ttu-id="740fa-1703">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1703">'SignalR' uid:</span></span> 

<span data-ttu-id="740fa-1704">-------------------------------- | | `RuleResult.ContinueRules`(padrão) | Continue aplicando regras.</span><span class="sxs-lookup"><span data-stu-id="740fa-1704">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="740fa-1705">| | `RuleResult.EndResponse`             | Interrompa a aplicação de regras e envie a resposta.</span><span class="sxs-lookup"><span data-stu-id="740fa-1705">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="740fa-1706">| | `RuleResult.SkipRemainingRules`      | Interrompa a aplicação de regras e envie o contexto para o próximo middleware.</span><span class="sxs-lookup"><span data-stu-id="740fa-1706">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="740fa-1707">O aplicativo de exemplo demonstra um método que redireciona as solicitações para caminhos que terminam com *.xml*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1707">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="740fa-1708">Se uma solicitação for feita para `/file.xml`, ela será redirecionada para `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1708">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="740fa-1709">O código de status é definido como *301 – Movido Permanentemente*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1709">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="740fa-1710">Quando o navegador faz uma nova solicitação para */xmlfiles/file.xml*, o Middleware de Arquivo Estático fornece o arquivo para o cliente por meio da pasta *wwwroot/xmlfiles*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1710">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="740fa-1711">Para um redirecionamento, defina explicitamente o código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="740fa-1711">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="740fa-1712">Caso contrário, um código de status *200 – OK* será retornado e o redirecionamento não ocorrerá no cliente.</span><span class="sxs-lookup"><span data-stu-id="740fa-1712">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="740fa-1713">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="740fa-1713">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="740fa-1714">Essa abordagem também pode reconfigurar as solicitações.</span><span class="sxs-lookup"><span data-stu-id="740fa-1714">This approach can also rewrite requests.</span></span> <span data-ttu-id="740fa-1715">O aplicativo de exemplo demonstra a reconfiguração do caminho de qualquer solicitação de arquivo de texto para fornecer o arquivo de texto *file.txt* por meio da pasta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1715">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="740fa-1716">O Middleware de Arquivo Estático fornece o arquivo com base no caminho de solicitação atualizado:</span><span class="sxs-lookup"><span data-stu-id="740fa-1716">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="740fa-1717">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="740fa-1717">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="740fa-1718">Regra baseada em IRule</span><span class="sxs-lookup"><span data-stu-id="740fa-1718">IRule-based rule</span></span>

<span data-ttu-id="740fa-1719">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para usar a lógica de regra em uma classe que implementa a interface <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="740fa-1719">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="740fa-1720">`IRule` fornece maior flexibilidade em comparação ao uso da abordagem de regra baseada em método.</span><span class="sxs-lookup"><span data-stu-id="740fa-1720">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="740fa-1721">A classe de implementação pode incluir um construtor, que permite passar parâmetros para o método <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="740fa-1721">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="740fa-1722">Os valores dos parâmetros no aplicativo de exemplo para a `extension` e o `newPath` são verificados para atender a várias condições.</span><span class="sxs-lookup"><span data-stu-id="740fa-1722">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="740fa-1723">A `extension` precisa conter um valor que precisa ser *.png*, *.jpg* ou *.gif*.</span><span class="sxs-lookup"><span data-stu-id="740fa-1723">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="740fa-1724">Se o `newPath` não é válido, uma <xref:System.ArgumentException> é gerada.</span><span class="sxs-lookup"><span data-stu-id="740fa-1724">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="740fa-1725">Se uma solicitação é feita para *image.png*, a solicitação é redirecionada para `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1725">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="740fa-1726">Se uma solicitação é feita para *image.jpg*, a solicitação é redirecionada para `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="740fa-1726">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="740fa-1727">O código de status é definido como *301 – Movido Permanentemente* e o `context.Result` é definida para parar o processamento de regras e enviar a resposta.</span><span class="sxs-lookup"><span data-stu-id="740fa-1727">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="740fa-1728">Solicitação original: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="740fa-1728">Original Request: `/image.png`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando as solicitações e respostas para image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="740fa-1730">Solicitação original: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="740fa-1730">Original Request: `/image.jpg`</span></span>

![Janela do navegador com as Ferramentas para Desenvolvedores acompanhando as solicitações e respostas para image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="740fa-1732">Exemplos do regex</span><span class="sxs-lookup"><span data-stu-id="740fa-1732">Regex examples</span></span>

| <span data-ttu-id="740fa-1733">Goal</span><span class="sxs-lookup"><span data-stu-id="740fa-1733">Goal</span></span> | <span data-ttu-id="740fa-1734">Cadeia de caracteres do regex &</span><span class="sxs-lookup"><span data-stu-id="740fa-1734">Regex String &</span></span><br><span data-ttu-id="740fa-1735">Exemplo de correspondência</span><span class="sxs-lookup"><span data-stu-id="740fa-1735">Match Example</span></span> | <span data-ttu-id="740fa-1736">Cadeia de caracteres de substituição &</span><span class="sxs-lookup"><span data-stu-id="740fa-1736">Replacement String &</span></span><br><span data-ttu-id="740fa-1737">Exemplo de saída</span><span class="sxs-lookup"><span data-stu-id="740fa-1737">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="740fa-1738">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1739">'Blazor'</span></span>
- <span data-ttu-id="740fa-1740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1740">'Identity'</span></span>
- <span data-ttu-id="740fa-1741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1741">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1742">'Razor'</span></span>
- <span data-ttu-id="740fa-1743">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1744">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1745">'Blazor'</span></span>
- <span data-ttu-id="740fa-1746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1746">'Identity'</span></span>
- <span data-ttu-id="740fa-1747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1747">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1748">'Razor'</span></span>
- <span data-ttu-id="740fa-1749">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1750">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1751">'Blazor'</span></span>
- <span data-ttu-id="740fa-1752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1752">'Identity'</span></span>
- <span data-ttu-id="740fa-1753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1753">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1754">'Razor'</span></span>
- <span data-ttu-id="740fa-1755">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1755">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1756">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1757">'Blazor'</span></span>
- <span data-ttu-id="740fa-1758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1758">'Identity'</span></span>
- <span data-ttu-id="740fa-1759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1759">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1760">'Razor'</span></span>
- <span data-ttu-id="740fa-1761">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1762">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1763">'Blazor'</span></span>
- <span data-ttu-id="740fa-1764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1764">'Identity'</span></span>
- <span data-ttu-id="740fa-1765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1765">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1766">'Razor'</span></span>
- <span data-ttu-id="740fa-1767">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1768">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1769">'Blazor'</span></span>
- <span data-ttu-id="740fa-1770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1770">'Identity'</span></span>
- <span data-ttu-id="740fa-1771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1771">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1772">'Razor'</span></span>
- <span data-ttu-id="740fa-1773">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1774">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1775">'Blazor'</span></span>
- <span data-ttu-id="740fa-1776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1776">'Identity'</span></span>
- <span data-ttu-id="740fa-1777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1777">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1778">'Razor'</span></span>
- <span data-ttu-id="740fa-1779">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1780">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1781">'Blazor'</span></span>
- <span data-ttu-id="740fa-1782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1782">'Identity'</span></span>
- <span data-ttu-id="740fa-1783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1783">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1784">'Razor'</span></span>
- <span data-ttu-id="740fa-1785">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1786">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1787">'Blazor'</span></span>
- <span data-ttu-id="740fa-1788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1788">'Identity'</span></span>
- <span data-ttu-id="740fa-1789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1789">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1790">'Razor'</span></span>
- <span data-ttu-id="740fa-1791">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1792">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1793">'Blazor'</span></span>
- <span data-ttu-id="740fa-1794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1794">'Identity'</span></span>
- <span data-ttu-id="740fa-1795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1795">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1796">'Razor'</span></span>
- <span data-ttu-id="740fa-1797">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1798">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1799">'Blazor'</span></span>
- <span data-ttu-id="740fa-1800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1800">'Identity'</span></span>
- <span data-ttu-id="740fa-1801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1801">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1802">'Razor'</span></span>
- <span data-ttu-id="740fa-1803">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1804">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1805">'Blazor'</span></span>
- <span data-ttu-id="740fa-1806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1806">'Identity'</span></span>
- <span data-ttu-id="740fa-1807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1807">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1808">'Razor'</span></span>
- <span data-ttu-id="740fa-1809">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1810">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1811">'Blazor'</span></span>
- <span data-ttu-id="740fa-1812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1812">'Identity'</span></span>
- <span data-ttu-id="740fa-1813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1813">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1814">'Razor'</span></span>
- <span data-ttu-id="740fa-1815">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1815">'SignalR' uid:</span></span> 

<span data-ttu-id="740fa-1816">---------------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1817">'Blazor'</span></span>
- <span data-ttu-id="740fa-1818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1818">'Identity'</span></span>
- <span data-ttu-id="740fa-1819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1819">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1820">'Razor'</span></span>
- <span data-ttu-id="740fa-1821">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1821">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1822">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1823">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1823">'Blazor'</span></span>
- <span data-ttu-id="740fa-1824">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1824">'Identity'</span></span>
- <span data-ttu-id="740fa-1825">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1825">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1826">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1826">'Razor'</span></span>
- <span data-ttu-id="740fa-1827">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1827">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1828">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1829">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1829">'Blazor'</span></span>
- <span data-ttu-id="740fa-1830">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1830">'Identity'</span></span>
- <span data-ttu-id="740fa-1831">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1831">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1832">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1832">'Razor'</span></span>
- <span data-ttu-id="740fa-1833">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1833">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1834">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1835">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1835">'Blazor'</span></span>
- <span data-ttu-id="740fa-1836">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1836">'Identity'</span></span>
- <span data-ttu-id="740fa-1837">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1837">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1838">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1838">'Razor'</span></span>
- <span data-ttu-id="740fa-1839">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1840">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1841">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1841">'Blazor'</span></span>
- <span data-ttu-id="740fa-1842">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1842">'Identity'</span></span>
- <span data-ttu-id="740fa-1843">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1843">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1844">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1844">'Razor'</span></span>
- <span data-ttu-id="740fa-1845">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1846">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1847">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1847">'Blazor'</span></span>
- <span data-ttu-id="740fa-1848">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1848">'Identity'</span></span>
- <span data-ttu-id="740fa-1849">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1849">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1850">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1850">'Razor'</span></span>
- <span data-ttu-id="740fa-1851">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1852">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1853">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1853">'Blazor'</span></span>
- <span data-ttu-id="740fa-1854">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1854">'Identity'</span></span>
- <span data-ttu-id="740fa-1855">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1855">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1856">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1856">'Razor'</span></span>
- <span data-ttu-id="740fa-1857">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1858">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1859">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1859">'Blazor'</span></span>
- <span data-ttu-id="740fa-1860">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1860">'Identity'</span></span>
- <span data-ttu-id="740fa-1861">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1861">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1862">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1862">'Razor'</span></span>
- <span data-ttu-id="740fa-1863">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1864">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1865">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1865">'Blazor'</span></span>
- <span data-ttu-id="740fa-1866">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1866">'Identity'</span></span>
- <span data-ttu-id="740fa-1867">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1867">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1868">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1868">'Razor'</span></span>
- <span data-ttu-id="740fa-1869">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1870">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1871">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1871">'Blazor'</span></span>
- <span data-ttu-id="740fa-1872">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1872">'Identity'</span></span>
- <span data-ttu-id="740fa-1873">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1873">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1874">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1874">'Razor'</span></span>
- <span data-ttu-id="740fa-1875">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1876">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1877">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1877">'Blazor'</span></span>
- <span data-ttu-id="740fa-1878">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1878">'Identity'</span></span>
- <span data-ttu-id="740fa-1879">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1879">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1880">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1880">'Razor'</span></span>
- <span data-ttu-id="740fa-1881">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1882">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1883">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1883">'Blazor'</span></span>
- <span data-ttu-id="740fa-1884">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1884">'Identity'</span></span>
- <span data-ttu-id="740fa-1885">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1885">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1886">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1886">'Razor'</span></span>
- <span data-ttu-id="740fa-1887">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1887">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1888">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1889">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1889">'Blazor'</span></span>
- <span data-ttu-id="740fa-1890">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1890">'Identity'</span></span>
- <span data-ttu-id="740fa-1891">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1891">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1892">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1892">'Razor'</span></span>
- <span data-ttu-id="740fa-1893">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1894">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1895">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1895">'Blazor'</span></span>
- <span data-ttu-id="740fa-1896">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1896">'Identity'</span></span>
- <span data-ttu-id="740fa-1897">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1897">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1898">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1898">'Razor'</span></span>
- <span data-ttu-id="740fa-1899">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1900">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1901">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1901">'Blazor'</span></span>
- <span data-ttu-id="740fa-1902">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1902">'Identity'</span></span>
- <span data-ttu-id="740fa-1903">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1903">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1904">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1904">'Razor'</span></span>
- <span data-ttu-id="740fa-1905">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1906">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1907">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1907">'Blazor'</span></span>
- <span data-ttu-id="740fa-1908">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1908">'Identity'</span></span>
- <span data-ttu-id="740fa-1909">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1909">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1910">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1910">'Razor'</span></span>
- <span data-ttu-id="740fa-1911">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="740fa-1912">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="740fa-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="740fa-1913">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1913">'Blazor'</span></span>
- <span data-ttu-id="740fa-1914">'Identity'</span><span class="sxs-lookup"><span data-stu-id="740fa-1914">'Identity'</span></span>
- <span data-ttu-id="740fa-1915">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="740fa-1915">'Let's Encrypt'</span></span>
- <span data-ttu-id="740fa-1916">'Razor'</span><span class="sxs-lookup"><span data-stu-id="740fa-1916">'Razor'</span></span>
- <span data-ttu-id="740fa-1917">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="740fa-1917">'SignalR' uid:</span></span> 

<span data-ttu-id="740fa-1918">------------------- | | Regravar caminho em QueryString |`^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="740fa-1918">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="740fa-1919">`/path?var1=abc&var2=123`| | Barra à direita da faixa |`(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="740fa-1919">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="740fa-1920">`/path`| | Impor barra à direita |`(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="740fa-1920">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="740fa-1921">`/path/`| | Evite reescrever solicitações específicas | `^(.*)(?<!\.axd)$`ou`^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="740fa-1921">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="740fa-1922">Sim: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="740fa-1922">Yes: `/resource.htm`</span></span><br><span data-ttu-id="740fa-1923">Não:`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="740fa-1923">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="740fa-1924">`/resource.axd`| | Reorganizar segmentos de URL |`path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="740fa-1924">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="740fa-1925">`path/3/2/1`| | Substituir um segmento de URL |`^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="740fa-1925">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="740fa-1926">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="740fa-1926">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="740fa-1927">Expressões regulares no .NET</span><span class="sxs-lookup"><span data-stu-id="740fa-1927">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="740fa-1928">Linguagem de expressão regular-referência rápida</span><span class="sxs-lookup"><span data-stu-id="740fa-1928">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="740fa-1929">mod_rewrite do Apache</span><span class="sxs-lookup"><span data-stu-id="740fa-1929">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="740fa-1930">Usando o Módulo de Reconfiguração de URL 2.0 (para IIS)</span><span class="sxs-lookup"><span data-stu-id="740fa-1930">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="740fa-1931">Referência de configuração do Módulo de Reconfiguração de URL</span><span class="sxs-lookup"><span data-stu-id="740fa-1931">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="740fa-1932">Fórum do Módulo de Reconfiguração de URL do IIS</span><span class="sxs-lookup"><span data-stu-id="740fa-1932">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="740fa-1933">Manter uma estrutura de URL simples</span><span class="sxs-lookup"><span data-stu-id="740fa-1933">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="740fa-1934">10 dicas e truques de reconfiguração de URL</span><span class="sxs-lookup"><span data-stu-id="740fa-1934">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="740fa-1935">Inserir ou não inserir uma barra "/"</span><span class="sxs-lookup"><span data-stu-id="740fa-1935">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)

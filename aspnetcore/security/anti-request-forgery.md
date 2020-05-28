---
<span data-ttu-id="426bc-101">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="426bc-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="426bc-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="426bc-102">'Blazor'</span></span>
- <span data-ttu-id="426bc-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="426bc-103">'Identity'</span></span>
- <span data-ttu-id="426bc-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="426bc-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="426bc-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="426bc-105">'Razor'</span></span>
- <span data-ttu-id="426bc-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="426bc-106">'SignalR' uid:</span></span> 

---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="426bc-107">Impedir ataques de solicitação intersite forjada (XSRF/CSRF) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="426bc-107">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="426bc-108">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="426bc-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="426bc-109">A solicitação entre sites forjada (também conhecida como XSRF ou CSRF) é um ataque contra aplicativos hospedados na Web, nos quais um aplicativo Web mal-intencionado pode influenciar a interação entre um navegador cliente e um aplicativo Web que confia nesse navegador.</span><span class="sxs-lookup"><span data-stu-id="426bc-109">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="426bc-110">Esses ataques são possíveis porque os navegadores da Web enviam automaticamente alguns tipos de tokens de autenticação com cada solicitação a um site.</span><span class="sxs-lookup"><span data-stu-id="426bc-110">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="426bc-111">Essa forma de exploração também é conhecida como um ataque ou *sessão* de *um clique* , pois o ataque aproveita a sessão autenticada anteriormente do usuário.</span><span class="sxs-lookup"><span data-stu-id="426bc-111">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="426bc-112">Um exemplo de um ataque de CSRF:</span><span class="sxs-lookup"><span data-stu-id="426bc-112">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="426bc-113">Um usuário entra no `www.good-banking-site.com` usando a autenticação de formulários.</span><span class="sxs-lookup"><span data-stu-id="426bc-113">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="426bc-114">O servidor autentica o usuário e emite uma resposta que inclui um cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="426bc-114">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="426bc-115">O site está vulnerável a ataques porque confia em qualquer solicitação recebida com um cookie de autenticação válido.</span><span class="sxs-lookup"><span data-stu-id="426bc-115">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="426bc-116">O usuário visita um site mal-intencionado, `www.bad-crook-site.com` .</span><span class="sxs-lookup"><span data-stu-id="426bc-116">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="426bc-117">O site mal-intencionado, `www.bad-crook-site.com` , contém um formulário HTML semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="426bc-117">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="426bc-118">Observe que as `action` postagens do formulário para o site vulnerável, não para o site mal-intencionado.</span><span class="sxs-lookup"><span data-stu-id="426bc-118">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="426bc-119">Esta é a parte "entre sites" do CSRF.</span><span class="sxs-lookup"><span data-stu-id="426bc-119">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="426bc-120">O usuário seleciona o botão enviar.</span><span class="sxs-lookup"><span data-stu-id="426bc-120">The user selects the submit button.</span></span> <span data-ttu-id="426bc-121">O navegador faz a solicitação e inclui automaticamente o cookie de autenticação para o domínio solicitado, `www.good-banking-site.com` .</span><span class="sxs-lookup"><span data-stu-id="426bc-121">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="426bc-122">A solicitação é executada no `www.good-banking-site.com` servidor com o contexto de autenticação do usuário e pode executar qualquer ação que um usuário autenticado tem permissão para executar.</span><span class="sxs-lookup"><span data-stu-id="426bc-122">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="426bc-123">Além do cenário em que o usuário seleciona o botão para enviar o formulário, o site mal-intencionado poderia:</span><span class="sxs-lookup"><span data-stu-id="426bc-123">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="426bc-124">Execute um script que envia automaticamente o formulário.</span><span class="sxs-lookup"><span data-stu-id="426bc-124">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="426bc-125">Envie o envio do formulário como uma solicitação AJAX.</span><span class="sxs-lookup"><span data-stu-id="426bc-125">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="426bc-126">Oculte o formulário usando o CSS.</span><span class="sxs-lookup"><span data-stu-id="426bc-126">Hide the form using CSS.</span></span>

<span data-ttu-id="426bc-127">Esses cenários alternativos não exigem nenhuma ação ou entrada do usuário que não seja a visita inicial do site mal-intencionado.</span><span class="sxs-lookup"><span data-stu-id="426bc-127">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="426bc-128">O uso de HTTPS não impede um ataque de CSRF.</span><span class="sxs-lookup"><span data-stu-id="426bc-128">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="426bc-129">O site mal-intencionado pode enviar uma `https://www.good-banking-site.com/` solicitação tão facilmente quanto pode enviar uma solicitação insegura.</span><span class="sxs-lookup"><span data-stu-id="426bc-129">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="426bc-130">Alguns ataques têm como alvo pontos de extremidade que respondem a solicitações GET. nesse caso, uma marca de imagem pode ser usada para executar a ação.</span><span class="sxs-lookup"><span data-stu-id="426bc-130">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="426bc-131">Essa forma de ataque é comum em sites de fórum que permitem imagens, mas bloqueiam JavaScript.</span><span class="sxs-lookup"><span data-stu-id="426bc-131">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="426bc-132">Aplicativos que alteram o estado em solicitações GET, em que variáveis ou recursos são alterados, são vulneráveis a ataques mal-intencionados.</span><span class="sxs-lookup"><span data-stu-id="426bc-132">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="426bc-133">**As solicitações GET que alteram o estado são inseguras. Uma prática recomendada é nunca alterar o estado em uma solicitação GET.**</span><span class="sxs-lookup"><span data-stu-id="426bc-133">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="426bc-134">Ataques de CSRF são possíveis em aplicativos Web que usam cookies para autenticação porque:</span><span class="sxs-lookup"><span data-stu-id="426bc-134">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="426bc-135">Os navegadores armazenam cookies emitidos por um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="426bc-135">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="426bc-136">Os cookies armazenados incluem cookies de sessão para usuários autenticados.</span><span class="sxs-lookup"><span data-stu-id="426bc-136">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="426bc-137">Os navegadores enviam todos os cookies associados a um domínio para o aplicativo Web a cada solicitação, independentemente de como a solicitação para o aplicativo foi gerada no navegador.</span><span class="sxs-lookup"><span data-stu-id="426bc-137">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="426bc-138">No entanto, ataques CSRF não são limitados à exploração de cookies.</span><span class="sxs-lookup"><span data-stu-id="426bc-138">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="426bc-139">Por exemplo, a autenticação básica e resumida também são vulneráveis.</span><span class="sxs-lookup"><span data-stu-id="426bc-139">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="426bc-140">Depois que um usuário entra com a autenticação básica ou resumida, o navegador envia automaticamente as credenciais até que a sessão &dagger; termine.</span><span class="sxs-lookup"><span data-stu-id="426bc-140">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="426bc-141">&dagger;Nesse contexto, *Session* refere-se à sessão do lado do cliente durante a qual o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="426bc-141">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="426bc-142">Não está relacionado a sessões do lado do servidor ou ao [middleware de sessão ASP.NET Core](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="426bc-142">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="426bc-143">Os usuários podem se proteger contra vulnerabilidades do CSRF tomando precauções:</span><span class="sxs-lookup"><span data-stu-id="426bc-143">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="426bc-144">Desconexão de aplicativos Web quando terminar de usá-los.</span><span class="sxs-lookup"><span data-stu-id="426bc-144">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="426bc-145">Limpe os cookies do navegador periodicamente.</span><span class="sxs-lookup"><span data-stu-id="426bc-145">Clear browser cookies periodically.</span></span>

<span data-ttu-id="426bc-146">No entanto, as vulnerabilidades do CSRF são fundamentalmente um problema com o aplicativo Web, não com o usuário final.</span><span class="sxs-lookup"><span data-stu-id="426bc-146">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="426bc-147">Conceitos básicos de autenticação</span><span class="sxs-lookup"><span data-stu-id="426bc-147">Authentication fundamentals</span></span>

<span data-ttu-id="426bc-148">A autenticação baseada em cookie é uma forma popular de autenticação.</span><span class="sxs-lookup"><span data-stu-id="426bc-148">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="426bc-149">Os sistemas de autenticação baseados em token estão crescendo em popularidade, especialmente para aplicativos de página única (SPAs).</span><span class="sxs-lookup"><span data-stu-id="426bc-149">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="426bc-150">Autenticação baseada em cookie</span><span class="sxs-lookup"><span data-stu-id="426bc-150">Cookie-based authentication</span></span>

<span data-ttu-id="426bc-151">Quando um usuário é autenticado usando seu nome de usuário e senha, ele recebe um token que contém um tíquete de autenticação que pode ser usado para autenticação e autorização.</span><span class="sxs-lookup"><span data-stu-id="426bc-151">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="426bc-152">O token é armazenado como um cookie que acompanha cada solicitação que o cliente faz.</span><span class="sxs-lookup"><span data-stu-id="426bc-152">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="426bc-153">Gerar e validar esse cookie é executado pelo middleware de autenticação de cookie.</span><span class="sxs-lookup"><span data-stu-id="426bc-153">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="426bc-154">O [middleware](xref:fundamentals/middleware/index) serializa uma entidade de usuário em um cookie criptografado.</span><span class="sxs-lookup"><span data-stu-id="426bc-154">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="426bc-155">Em solicitações subsequentes, o middleware valida o cookie, recria a entidade de segurança e atribui a entidade de segurança à propriedade de [usuário](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) de [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span><span class="sxs-lookup"><span data-stu-id="426bc-155">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="426bc-156">Autenticação baseada em token</span><span class="sxs-lookup"><span data-stu-id="426bc-156">Token-based authentication</span></span>

<span data-ttu-id="426bc-157">Quando um usuário é autenticado, ele recebe um token (não um token de falsificação).</span><span class="sxs-lookup"><span data-stu-id="426bc-157">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="426bc-158">O token contém informações de usuário na forma de [declarações](/dotnet/framework/security/claims-based-identity-model) ou um token de referência que aponta o aplicativo para o estado do usuário mantido no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="426bc-158">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="426bc-159">Quando um usuário tenta acessar um recurso que requer autenticação, o token é enviado ao aplicativo com um cabeçalho de autorização adicional na forma de token de portador.</span><span class="sxs-lookup"><span data-stu-id="426bc-159">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="426bc-160">Isso torna o aplicativo sem estado.</span><span class="sxs-lookup"><span data-stu-id="426bc-160">This makes the app stateless.</span></span> <span data-ttu-id="426bc-161">Em cada solicitação subsequente, o token é passado na solicitação de validação do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="426bc-161">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="426bc-162">Esse token não está *criptografado*; Ele está *codificado*.</span><span class="sxs-lookup"><span data-stu-id="426bc-162">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="426bc-163">No servidor, o token é decodificado para acessar suas informações.</span><span class="sxs-lookup"><span data-stu-id="426bc-163">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="426bc-164">Para enviar o token em solicitações subsequentes, armazene o token no armazenamento local do navegador.</span><span class="sxs-lookup"><span data-stu-id="426bc-164">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="426bc-165">Não se preocupe com a vulnerabilidade de CSRF se o token for armazenado no armazenamento local do navegador.</span><span class="sxs-lookup"><span data-stu-id="426bc-165">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="426bc-166">CSRF é uma preocupação quando o token é armazenado em um cookie.</span><span class="sxs-lookup"><span data-stu-id="426bc-166">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="426bc-167">Para obter mais informações, consulte o exemplo de código de spa problema do GitHub [adiciona dois cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="426bc-167">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="426bc-168">Vários aplicativos hospedados em um domínio</span><span class="sxs-lookup"><span data-stu-id="426bc-168">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="426bc-169">Os ambientes de hospedagem compartilhados são vulneráveis ao seqüestro de sessão, CSRF de logon e outros ataques.</span><span class="sxs-lookup"><span data-stu-id="426bc-169">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="426bc-170">Embora `example1.contoso.net` e `example2.contoso.net` sejam hosts diferentes, há uma relação de confiança implícita entre os hosts no `*.contoso.net` domínio.</span><span class="sxs-lookup"><span data-stu-id="426bc-170">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="426bc-171">Essa relação de confiança implícita permite que hosts potencialmente não confiáveis afetem os cookies uns dos outros (as políticas de mesma origem que regem as solicitações AJAX não necessariamente se aplicam a cookies HTTP).</span><span class="sxs-lookup"><span data-stu-id="426bc-171">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="426bc-172">Os ataques que exploram cookies confiáveis entre aplicativos hospedados no mesmo domínio podem ser impedidos por não compartilhar domínios.</span><span class="sxs-lookup"><span data-stu-id="426bc-172">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="426bc-173">Quando cada aplicativo é hospedado em seu próprio domínio, não há nenhuma relação de confiança de cookie implícita para explorar.</span><span class="sxs-lookup"><span data-stu-id="426bc-173">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="426bc-174">Configuração de antifalsificação de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="426bc-174">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="426bc-175">ASP.NET Core implementa antifalsificação usando a [proteção de dados do ASP.NET Core](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="426bc-175">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="426bc-176">A pilha de proteção de dados deve ser configurada para funcionar em um farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="426bc-176">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="426bc-177">Consulte [Configurando a proteção de dados](xref:security/data-protection/configuration/overview) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="426bc-177">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="426bc-178">O middleware antifalsificação é adicionado ao contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) quando uma das seguintes APIs é chamada em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="426bc-178">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="426bc-179">O middleware antifalsificação é adicionado ao contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) quando <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> é chamado em`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="426bc-179">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="426bc-180">No ASP.NET Core 2,0 ou posterior, o [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injeta tokens de antifalsificação em elementos de formulário HTML.</span><span class="sxs-lookup"><span data-stu-id="426bc-180">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="426bc-181">A seguinte marcação em um Razor arquivo gera automaticamente tokens de antifalsificação:</span><span class="sxs-lookup"><span data-stu-id="426bc-181">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="426bc-182">Da mesma forma, [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) gera tokens antifalsificação por padrão se o método do formulário não for Get.</span><span class="sxs-lookup"><span data-stu-id="426bc-182">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="426bc-183">A geração automática de tokens antifalsificação para elementos de formulário HTML acontece quando a `<form>` marca contém o `method="post"` atributo e qualquer uma das seguintes opções é verdadeira:</span><span class="sxs-lookup"><span data-stu-id="426bc-183">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="426bc-184">O atributo action está vazio ( `action=""` ).</span><span class="sxs-lookup"><span data-stu-id="426bc-184">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="426bc-185">O atributo action não é fornecido ( `<form method="post">` ).</span><span class="sxs-lookup"><span data-stu-id="426bc-185">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="426bc-186">A geração automática de tokens antifalsificação para elementos de formulário HTML pode ser desabilitada:</span><span class="sxs-lookup"><span data-stu-id="426bc-186">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="426bc-187">Desabilite explicitamente os tokens antifalsificação com o `asp-antiforgery` atributo:</span><span class="sxs-lookup"><span data-stu-id="426bc-187">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="426bc-188">O elemento form é aceito por auxiliares de marca usando o símbolo de desativação auxiliar de marca [!](xref:mvc/views/tag-helpers/intro#opt-out):</span><span class="sxs-lookup"><span data-stu-id="426bc-188">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="426bc-189">Remova o `FormTagHelper` da exibição.</span><span class="sxs-lookup"><span data-stu-id="426bc-189">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="426bc-190">O `FormTagHelper` pode ser removido de um modo de exibição adicionando a seguinte diretiva à Razor exibição:</span><span class="sxs-lookup"><span data-stu-id="426bc-190">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="426bc-191">As [ Razor páginas](xref:razor-pages/index) são protegidas automaticamente de XSRF/CSRF.</span><span class="sxs-lookup"><span data-stu-id="426bc-191">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="426bc-192">Para obter mais informações, consulte [XSRF/CSRF e Razor páginas](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="426bc-192">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="426bc-193">A abordagem mais comum para a defesa contra ataques CSRF é usar o *padrão de token do sincronizador* (STP).</span><span class="sxs-lookup"><span data-stu-id="426bc-193">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="426bc-194">O STP é usado quando o usuário solicita uma página com dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="426bc-194">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="426bc-195">O servidor envia um token associado à identidade do usuário atual para o cliente.</span><span class="sxs-lookup"><span data-stu-id="426bc-195">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="426bc-196">O cliente envia de volta o token para o servidor para verificação.</span><span class="sxs-lookup"><span data-stu-id="426bc-196">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="426bc-197">Se o servidor receber um token que não corresponda à identidade do usuário autenticado, a solicitação será rejeitada.</span><span class="sxs-lookup"><span data-stu-id="426bc-197">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="426bc-198">O token é exclusivo e imprevisível.</span><span class="sxs-lookup"><span data-stu-id="426bc-198">The token is unique and unpredictable.</span></span> <span data-ttu-id="426bc-199">O token também pode ser usado para garantir o sequenciamento adequado de uma série de solicitações (por exemplo, garantindo a sequência de solicitação de: Página 1 > página 2 > página 3).</span><span class="sxs-lookup"><span data-stu-id="426bc-199">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="426bc-200">Todos os formulários em modelos ASP.NET Core MVC e de Razor páginas geram tokens antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="426bc-200">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="426bc-201">O seguinte par de exemplos de exibição gera tokens antifalsificação:</span><span class="sxs-lookup"><span data-stu-id="426bc-201">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="426bc-202">Adicione explicitamente um token de antifalsificação a um `<form>` elemento sem usar auxiliares de marcas com o auxiliar HTML [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :</span><span class="sxs-lookup"><span data-stu-id="426bc-202">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="426bc-203">Em cada um dos casos anteriores, ASP.NET Core adiciona um campo de formulário oculto semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="426bc-203">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="426bc-204">ASP.NET Core inclui três [filtros](xref:mvc/controllers/filters) para trabalhar com tokens antifalsificação:</span><span class="sxs-lookup"><span data-stu-id="426bc-204">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="426bc-205">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="426bc-205">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="426bc-206">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="426bc-206">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="426bc-207">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="426bc-207">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="426bc-208">Opções de antifalsificação</span><span class="sxs-lookup"><span data-stu-id="426bc-208">Antiforgery options</span></span>

<span data-ttu-id="426bc-209">Personalize [as opções de antifalsificação](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="426bc-209">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="426bc-210">&dagger;Defina as propriedades do antifalsificação `Cookie` usando as propriedades da classe [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .</span><span class="sxs-lookup"><span data-stu-id="426bc-210">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="426bc-211">Opção</span><span class="sxs-lookup"><span data-stu-id="426bc-211">Option</span></span> | <span data-ttu-id="426bc-212">Descrição</span><span class="sxs-lookup"><span data-stu-id="426bc-212">Description</span></span> |
| ---
<span data-ttu-id="426bc-213">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="426bc-213">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="426bc-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="426bc-214">'Blazor'</span></span>
- <span data-ttu-id="426bc-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="426bc-215">'Identity'</span></span>
- <span data-ttu-id="426bc-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="426bc-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="426bc-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="426bc-217">'Razor'</span></span>
- <span data-ttu-id="426bc-218">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="426bc-218">'SignalR' uid:</span></span> 

<span data-ttu-id="426bc-219">--- | título do---: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="426bc-219">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="426bc-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="426bc-220">'Blazor'</span></span>
- <span data-ttu-id="426bc-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="426bc-221">'Identity'</span></span>
- <span data-ttu-id="426bc-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="426bc-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="426bc-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="426bc-223">'Razor'</span></span>
- <span data-ttu-id="426bc-224">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="426bc-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="426bc-225">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="426bc-225">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="426bc-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="426bc-226">'Blazor'</span></span>
- <span data-ttu-id="426bc-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="426bc-227">'Identity'</span></span>
- <span data-ttu-id="426bc-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="426bc-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="426bc-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="426bc-229">'Razor'</span></span>
- <span data-ttu-id="426bc-230">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="426bc-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="426bc-231">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="426bc-231">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="426bc-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="426bc-232">'Blazor'</span></span>
- <span data-ttu-id="426bc-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="426bc-233">'Identity'</span></span>
- <span data-ttu-id="426bc-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="426bc-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="426bc-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="426bc-235">'Razor'</span></span>
- <span data-ttu-id="426bc-236">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="426bc-236">'SignalR' uid:</span></span> 

<span data-ttu-id="426bc-237">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determina as configurações usadas para criar os cookies antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="426bc-237">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="426bc-238">| | [FormName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | O nome do campo de formulário oculto usado pelo sistema antifalsificação para renderizar tokens de antifalsificação em exibições.</span><span class="sxs-lookup"><span data-stu-id="426bc-238">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="426bc-239">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | O nome do cabeçalho usado pelo sistema antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="426bc-239">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="426bc-240">Se `null` , o sistema considerará apenas os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="426bc-240">If `null`, the system considers only form data.</span></span> <span data-ttu-id="426bc-241">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Especifica se a geração do cabeçalho deve ser suprimida `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="426bc-241">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="426bc-242">Por padrão, o cabeçalho é gerado com um valor de "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="426bc-242">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="426bc-243">O padrão é `false`.</span><span class="sxs-lookup"><span data-stu-id="426bc-243">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="426bc-244">Opção</span><span class="sxs-lookup"><span data-stu-id="426bc-244">Option</span></span> | <span data-ttu-id="426bc-245">Descrição</span><span class="sxs-lookup"><span data-stu-id="426bc-245">Description</span></span> |
| ---
<span data-ttu-id="426bc-246">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="426bc-246">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="426bc-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="426bc-247">'Blazor'</span></span>
- <span data-ttu-id="426bc-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="426bc-248">'Identity'</span></span>
- <span data-ttu-id="426bc-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="426bc-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="426bc-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="426bc-250">'Razor'</span></span>
- <span data-ttu-id="426bc-251">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="426bc-251">'SignalR' uid:</span></span> 

<span data-ttu-id="426bc-252">--- | título do---: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="426bc-252">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="426bc-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="426bc-253">'Blazor'</span></span>
- <span data-ttu-id="426bc-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="426bc-254">'Identity'</span></span>
- <span data-ttu-id="426bc-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="426bc-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="426bc-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="426bc-256">'Razor'</span></span>
- <span data-ttu-id="426bc-257">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="426bc-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="426bc-258">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="426bc-258">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="426bc-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="426bc-259">'Blazor'</span></span>
- <span data-ttu-id="426bc-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="426bc-260">'Identity'</span></span>
- <span data-ttu-id="426bc-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="426bc-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="426bc-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="426bc-262">'Razor'</span></span>
- <span data-ttu-id="426bc-263">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="426bc-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="426bc-264">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="426bc-264">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="426bc-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="426bc-265">'Blazor'</span></span>
- <span data-ttu-id="426bc-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="426bc-266">'Identity'</span></span>
- <span data-ttu-id="426bc-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="426bc-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="426bc-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="426bc-268">'Razor'</span></span>
- <span data-ttu-id="426bc-269">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="426bc-269">'SignalR' uid:</span></span> 

<span data-ttu-id="426bc-270">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determina as configurações usadas para criar os cookies antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="426bc-270">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="426bc-271">| | [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | O domínio do cookie.</span><span class="sxs-lookup"><span data-stu-id="426bc-271">| | [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | The domain of the cookie.</span></span> <span data-ttu-id="426bc-272">O padrão é `null`.</span><span class="sxs-lookup"><span data-stu-id="426bc-272">Defaults to `null`.</span></span> <span data-ttu-id="426bc-273">Esta propriedade é obsoleta e será removida em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="426bc-273">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="426bc-274">A alternativa recomendada é cookie. domain.</span><span class="sxs-lookup"><span data-stu-id="426bc-274">The recommended alternative is Cookie.Domain.</span></span> <span data-ttu-id="426bc-275">| | [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | O nome do cookie.</span><span class="sxs-lookup"><span data-stu-id="426bc-275">| | [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | The name of the cookie.</span></span> <span data-ttu-id="426bc-276">Se não estiver definido, o sistema gera um nome exclusivo que começa com o [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore. antifalsificação. ").</span><span class="sxs-lookup"><span data-stu-id="426bc-276">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="426bc-277">Esta propriedade é obsoleta e será removida em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="426bc-277">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="426bc-278">A alternativa recomendada é Cookie.Name.</span><span class="sxs-lookup"><span data-stu-id="426bc-278">The recommended alternative is Cookie.Name.</span></span> <span data-ttu-id="426bc-279">| | [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | O caminho definido no cookie.</span><span class="sxs-lookup"><span data-stu-id="426bc-279">| | [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | The path set on the cookie.</span></span> <span data-ttu-id="426bc-280">Esta propriedade é obsoleta e será removida em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="426bc-280">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="426bc-281">A alternativa recomendada é cookie. Path.</span><span class="sxs-lookup"><span data-stu-id="426bc-281">The recommended alternative is Cookie.Path.</span></span> <span data-ttu-id="426bc-282">| | [FormName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | O nome do campo de formulário oculto usado pelo sistema antifalsificação para renderizar tokens de antifalsificação em exibições.</span><span class="sxs-lookup"><span data-stu-id="426bc-282">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="426bc-283">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | O nome do cabeçalho usado pelo sistema antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="426bc-283">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="426bc-284">Se `null` , o sistema considerará apenas os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="426bc-284">If `null`, the system considers only form data.</span></span> <span data-ttu-id="426bc-285">| | [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Especifica se o HTTPS é exigido pelo sistema antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="426bc-285">| | [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="426bc-286">Se `true` , as solicitações não-HTTPS falharão.</span><span class="sxs-lookup"><span data-stu-id="426bc-286">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="426bc-287">O padrão é `false`.</span><span class="sxs-lookup"><span data-stu-id="426bc-287">Defaults to `false`.</span></span> <span data-ttu-id="426bc-288">Esta propriedade é obsoleta e será removida em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="426bc-288">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="426bc-289">A alternativa recomendada é definir cookie. SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="426bc-289">The recommended alternative is to set Cookie.SecurePolicy.</span></span> <span data-ttu-id="426bc-290">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Especifica se a geração do cabeçalho deve ser suprimida `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="426bc-290">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="426bc-291">Por padrão, o cabeçalho é gerado com um valor de "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="426bc-291">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="426bc-292">O padrão é `false`.</span><span class="sxs-lookup"><span data-stu-id="426bc-292">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="426bc-293">Para obter mais informações, consulte [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="426bc-293">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="426bc-294">Configurar recursos antifalsificação com IAntiforgery</span><span class="sxs-lookup"><span data-stu-id="426bc-294">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="426bc-295">O [IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) fornece a API para configurar recursos antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="426bc-295">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="426bc-296">`IAntiforgery`pode ser solicitado no `Configure` método da `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="426bc-296">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="426bc-297">O exemplo a seguir usa o middleware do home page do aplicativo para gerar um token de antifalsificação e enviá-lo na resposta como um cookie (usando a Convenção de nomenclatura angular padrão descrita mais adiante neste tópico):</span><span class="sxs-lookup"><span data-stu-id="426bc-297">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="426bc-298">Exigir validação de antifalsificação</span><span class="sxs-lookup"><span data-stu-id="426bc-298">Require antiforgery validation</span></span>

<span data-ttu-id="426bc-299">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) é um filtro de ação que pode ser aplicado a uma ação individual, a um controlador ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="426bc-299">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="426bc-300">As solicitações feitas às ações que têm esse filtro aplicado são bloqueadas, a menos que a solicitação inclua um token antifalsificação válido.</span><span class="sxs-lookup"><span data-stu-id="426bc-300">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="426bc-301">O `ValidateAntiForgeryToken` atributo requer um token para solicitações para os métodos de ação que ele marca, incluindo solicitações HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="426bc-301">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="426bc-302">Se o `ValidateAntiForgeryToken` atributo for aplicado nos controladores do aplicativo, ele poderá ser substituído pelo `IgnoreAntiforgeryToken` atributo.</span><span class="sxs-lookup"><span data-stu-id="426bc-302">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="426bc-303">ASP.NET Core não dá suporte à adição de tokens antifalsificação para obter solicitações automaticamente.</span><span class="sxs-lookup"><span data-stu-id="426bc-303">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="426bc-304">Validar automaticamente tokens antifalsificação somente para métodos HTTP inseguros</span><span class="sxs-lookup"><span data-stu-id="426bc-304">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="426bc-305">ASP.NET Core aplicativos não geram tokens antifalsificação para métodos HTTP seguros (GET, cabeçalho, opções e rastreamento).</span><span class="sxs-lookup"><span data-stu-id="426bc-305">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="426bc-306">Em vez de aplicar amplamente o `ValidateAntiForgeryToken` atributo e, em seguida, substituí-lo por `IgnoreAntiforgeryToken` atributos, o atributo [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="426bc-306">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="426bc-307">Esse atributo funciona de forma idêntica ao `ValidateAntiForgeryToken` atributo, exceto pelo fato de que ele não requer tokens para solicitações feitas usando os seguintes métodos http:</span><span class="sxs-lookup"><span data-stu-id="426bc-307">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="426bc-308">GET</span><span class="sxs-lookup"><span data-stu-id="426bc-308">GET</span></span>
* <span data-ttu-id="426bc-309">HEAD</span><span class="sxs-lookup"><span data-stu-id="426bc-309">HEAD</span></span>
* <span data-ttu-id="426bc-310">OPÇÕES</span><span class="sxs-lookup"><span data-stu-id="426bc-310">OPTIONS</span></span>
* <span data-ttu-id="426bc-311">RASTREAMENTO</span><span class="sxs-lookup"><span data-stu-id="426bc-311">TRACE</span></span>

<span data-ttu-id="426bc-312">Recomendamos o uso de `AutoValidateAntiforgeryToken` amplos para cenários que não são da API.</span><span class="sxs-lookup"><span data-stu-id="426bc-312">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="426bc-313">Isso garante que as ações de POSTAgem sejam protegidas por padrão.</span><span class="sxs-lookup"><span data-stu-id="426bc-313">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="426bc-314">A alternativa é ignorar tokens antifalsificação por padrão, a menos que `ValidateAntiForgeryToken` seja aplicado a métodos de ação individuais.</span><span class="sxs-lookup"><span data-stu-id="426bc-314">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="426bc-315">É mais provável que esse cenário para um método POST Action seja deixado desprotegido por engano, deixando o aplicativo vulnerável a ataques CSRF.</span><span class="sxs-lookup"><span data-stu-id="426bc-315">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="426bc-316">Todas as postagens devem enviar o token de antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="426bc-316">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="426bc-317">As APIs não têm um mecanismo automático para enviar a parte não cookie do token.</span><span class="sxs-lookup"><span data-stu-id="426bc-317">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="426bc-318">A implementação provavelmente depende da implementação do código do cliente.</span><span class="sxs-lookup"><span data-stu-id="426bc-318">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="426bc-319">Alguns exemplos são mostrados abaixo:</span><span class="sxs-lookup"><span data-stu-id="426bc-319">Some examples are shown below:</span></span>

<span data-ttu-id="426bc-320">Exemplo de nível de classe:</span><span class="sxs-lookup"><span data-stu-id="426bc-320">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="426bc-321">Exemplo global:</span><span class="sxs-lookup"><span data-stu-id="426bc-321">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="426bc-322">serviços. AddMvc (opções => opções. Filters. Add (novo AutoValidateAntiforgeryTokenAttribute ());</span><span class="sxs-lookup"><span data-stu-id="426bc-322">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="426bc-323">Substituir atributos globais ou de antifalsificação do controlador</span><span class="sxs-lookup"><span data-stu-id="426bc-323">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="426bc-324">O filtro [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) é usado para eliminar a necessidade de um token de antifalsificação para uma determinada ação (ou controlador).</span><span class="sxs-lookup"><span data-stu-id="426bc-324">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="426bc-325">Quando aplicado, esse filtro substitui `ValidateAntiForgeryToken` e `AutoValidateAntiforgeryToken` filtra os filtros especificados em um nível mais alto (globalmente ou em um controlador).</span><span class="sxs-lookup"><span data-stu-id="426bc-325">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="426bc-326">Atualizar tokens após a autenticação</span><span class="sxs-lookup"><span data-stu-id="426bc-326">Refresh tokens after authentication</span></span>

<span data-ttu-id="426bc-327">Os tokens devem ser atualizados depois que o usuário é autenticado redirecionando o usuário para uma página de exibição ou Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="426bc-327">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="426bc-328">JavaScript, AJAX e SPAs</span><span class="sxs-lookup"><span data-stu-id="426bc-328">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="426bc-329">Em aplicativos baseados em HTML tradicionais, os tokens antifalsificação são passados para o servidor usando campos de formulário ocultos.</span><span class="sxs-lookup"><span data-stu-id="426bc-329">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="426bc-330">Em aplicativos modernos baseados em JavaScript e SPAs, muitas solicitações são feitas programaticamente.</span><span class="sxs-lookup"><span data-stu-id="426bc-330">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="426bc-331">Essas solicitações AJAX podem usar outras técnicas (como cabeçalhos de solicitação ou cookies) para enviar o token.</span><span class="sxs-lookup"><span data-stu-id="426bc-331">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="426bc-332">Se os cookies forem usados para armazenar tokens de autenticação e autenticar solicitações de API no servidor, o CSRF será um problema potencial.</span><span class="sxs-lookup"><span data-stu-id="426bc-332">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="426bc-333">Se o armazenamento local for usado para armazenar o token, a vulnerabilidade CSRF poderá ser reduzida porque os valores do armazenamento local não serão enviados automaticamente para o servidor com cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="426bc-333">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="426bc-334">Portanto, o uso do armazenamento local para armazenar o token de antifalsificação no cliente e o envio do token como um cabeçalho de solicitação é uma abordagem recomendada.</span><span class="sxs-lookup"><span data-stu-id="426bc-334">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="426bc-335">JavaScript</span><span class="sxs-lookup"><span data-stu-id="426bc-335">JavaScript</span></span>

<span data-ttu-id="426bc-336">Usando JavaScript com exibições, o token pode ser criado usando um serviço de dentro da exibição.</span><span class="sxs-lookup"><span data-stu-id="426bc-336">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="426bc-337">Insira o serviço [Microsoft. AspNetCore. antifalsificating. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) na exibição e chame [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span><span class="sxs-lookup"><span data-stu-id="426bc-337">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="426bc-338">Essa abordagem elimina a necessidade de lidar diretamente com a configuração de cookies do servidor ou leitura deles a partir do cliente.</span><span class="sxs-lookup"><span data-stu-id="426bc-338">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="426bc-339">O exemplo anterior usa JavaScript para ler o valor do campo oculto para o cabeçalho de POSTAgem AJAX.</span><span class="sxs-lookup"><span data-stu-id="426bc-339">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="426bc-340">O JavaScript também pode acessar tokens em cookies e usar o conteúdo do cookie para criar um cabeçalho com o valor do token.</span><span class="sxs-lookup"><span data-stu-id="426bc-340">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="426bc-341">Supondo que as solicitações de script enviem o token em um cabeçalho chamado `X-CSRF-TOKEN` , configure o serviço antifalsificação para procurar o `X-CSRF-TOKEN` cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="426bc-341">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="426bc-342">O exemplo a seguir usa JavaScript para fazer uma solicitação AJAX com o cabeçalho apropriado:</span><span class="sxs-lookup"><span data-stu-id="426bc-342">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="426bc-343">AngularJS</span><span class="sxs-lookup"><span data-stu-id="426bc-343">AngularJS</span></span>

<span data-ttu-id="426bc-344">O AngularJS usa uma Convenção para tratar CSRF.</span><span class="sxs-lookup"><span data-stu-id="426bc-344">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="426bc-345">Se o servidor enviar um cookie com o nome `XSRF-TOKEN` , o `$http` serviço AngularJS adicionará o valor de cookie a um cabeçalho ao enviar uma solicitação ao servidor.</span><span class="sxs-lookup"><span data-stu-id="426bc-345">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="426bc-346">Esse processo é automático.</span><span class="sxs-lookup"><span data-stu-id="426bc-346">This process is automatic.</span></span> <span data-ttu-id="426bc-347">O cabeçalho não precisa ser definido explicitamente no cliente.</span><span class="sxs-lookup"><span data-stu-id="426bc-347">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="426bc-348">O nome do cabeçalho é `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="426bc-348">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="426bc-349">O servidor deve detectar esse cabeçalho e validar seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="426bc-349">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="426bc-350">Para ASP.NET Core API funcione com essa convenção na inicialização do seu aplicativo:</span><span class="sxs-lookup"><span data-stu-id="426bc-350">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="426bc-351">Configure seu aplicativo para fornecer um token em um cookie chamado `XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="426bc-351">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="426bc-352">Configure o serviço antifalsificação para procurar um cabeçalho chamado `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="426bc-352">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="426bc-353">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="426bc-353">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="426bc-354">Estender antifalsificação</span><span class="sxs-lookup"><span data-stu-id="426bc-354">Extend antiforgery</span></span>

<span data-ttu-id="426bc-355">O tipo [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) permite que os desenvolvedores estendam o comportamento do sistema CSRF por meio da ida e volta de dados adicionais em cada token.</span><span class="sxs-lookup"><span data-stu-id="426bc-355">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="426bc-356">O método [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) é chamado cada vez que um token de campo é gerado e o valor de retorno é inserido no token gerado.</span><span class="sxs-lookup"><span data-stu-id="426bc-356">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="426bc-357">Um implementador poderia retornar um carimbo de data/hora, um nonce ou qualquer outro valor e, em seguida, chamar [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) para validar esses dados quando o token for validado.</span><span class="sxs-lookup"><span data-stu-id="426bc-357">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="426bc-358">O nome de usuário do cliente já está inserido nos tokens gerados, portanto, não é necessário incluir essas informações.</span><span class="sxs-lookup"><span data-stu-id="426bc-358">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="426bc-359">Se um token incluir dados complementares, mas não `IAntiForgeryAdditionalDataProvider` estiver configurado, os dados complementares não serão validados.</span><span class="sxs-lookup"><span data-stu-id="426bc-359">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="426bc-360">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="426bc-360">Additional resources</span></span>

* <span data-ttu-id="426bc-361">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) em [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="426bc-361">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>

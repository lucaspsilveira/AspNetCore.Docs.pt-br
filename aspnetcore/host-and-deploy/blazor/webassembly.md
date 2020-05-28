---
<span data-ttu-id="3cdaa-101">Título: ' hospedar e implantar ASP.NET Core Blazor Webassembly ' autor: Descrição: ' saiba como hospedar e implantar um Blazor aplicativo usando ASP.NET Core, CDN (redes de distribuição de conteúdo), servidores de arquivos e páginas do github. '</span><span class="sxs-lookup"><span data-stu-id="3cdaa-101">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="3cdaa-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3cdaa-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-103">'Blazor'</span></span>
- <span data-ttu-id="3cdaa-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-104">'Identity'</span></span>
- <span data-ttu-id="3cdaa-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="3cdaa-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-106">'Razor'</span></span>
- <span data-ttu-id="3cdaa-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="3cdaa-107">'SignalR' uid:</span></span> 

---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="3cdaa-108">Hospedar e implantar ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="3cdaa-108">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="3cdaa-109">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)e [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="3cdaa-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="3cdaa-110">Com o [ Blazor modelo de hospedagem Webassembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="3cdaa-110">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="3cdaa-111">O Blazor aplicativo, suas dependências e o tempo de execução do .NET são baixados para o navegador em paralelo.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-111">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="3cdaa-112">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-112">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="3cdaa-113">Há suporte para as seguintes estratégias de implantação:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-113">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="3cdaa-114">O Blazor aplicativo é servido por um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-114">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="3cdaa-115">Esta estratégia é abordada na seção [Implantação hospedada com o ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-115">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="3cdaa-116">O Blazor aplicativo é colocado em um servidor Web ou serviço de hospedagem estática, em que o .net não é usado para servir o Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-116">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="3cdaa-117">Essa estratégia é abordada na seção [implantação autônoma](#standalone-deployment) , que inclui informações sobre como hospedar um Blazor aplicativo Webassembly como um subaplicativo do IIS.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-117">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="3cdaa-118">Precompactação Brotli</span><span class="sxs-lookup"><span data-stu-id="3cdaa-118">Brotli precompression</span></span>

<span data-ttu-id="3cdaa-119">Quando um Blazor aplicativo Webassembly é publicado, a saída é precompactada usando o [algoritmo de compactação Brotli](https://tools.ietf.org/html/rfc7932) no nível mais alto para reduzir o tamanho do aplicativo e remover a necessidade de compactação de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-119">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="3cdaa-120">Para a configuração de compactação *Web. config* do IIS, consulte a seção [IIS: Brotli e a compactação Gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="3cdaa-120">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="3cdaa-121">Reescrever as URLs para obter o roteamento correto</span><span class="sxs-lookup"><span data-stu-id="3cdaa-121">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="3cdaa-122">O roteamento de solicitações para componentes de página em um Blazor aplicativo Webassembly não é tão simples quanto o roteamento de solicitações em um Blazor servidor, aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-122">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="3cdaa-123">Considere um Blazor aplicativo Webassembly com dois componentes:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-123">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="3cdaa-124">*Main. Razor*: carrega na raiz do aplicativo e contém um link para o `About` componente ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-124">*Main.razor*: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="3cdaa-125">*Sobre o. Razor*: `About` componente.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-125">*About.razor*: `About` component.</span></span>

<span data-ttu-id="3cdaa-126">Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="3cdaa-126">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="3cdaa-127">O navegador faz uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-127">The browser makes a request.</span></span>
1. <span data-ttu-id="3cdaa-128">A página padrão é retornada, que é geralmente é *index.html*.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-128">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="3cdaa-129">A *index.html* inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-129">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="3cdaa-130">o roteador do é carregado e o Razor `Main` componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-130">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="3cdaa-131">Na página principal, selecionar o link para o `About` componente funciona no cliente, pois o Blazor roteador interrompe o navegador de fazer uma solicitação na Internet para `www.contoso.com` `About` e serve o componente renderizado em `About` si.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-131">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="3cdaa-132">Todas as solicitações de pontos de extremidade internos *no Blazor aplicativo Webassembly* funcionam da mesma maneira: as solicitações não disparam solicitações baseadas em navegador para recursos hospedados no servidor na Internet.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-132">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="3cdaa-133">O roteador trata das solicitações internamente.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-133">The router handles the requests internally.</span></span>

<span data-ttu-id="3cdaa-134">Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-134">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="3cdaa-135">Este recurso não existe no host do aplicativo na Internet; portanto, uma resposta *404 – Não Encontrado* é retornada.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-135">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="3cdaa-136">Como os navegadores fazem solicitações aos hosts baseados na Internet de páginas do lado do cliente, os servidores Web e os serviços de hospedagem precisam reescrever todas as solicitações de recursos que não estão fisicamente no servidor para a página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-136">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="3cdaa-137">Quando *index. html* for retornado, o roteador do aplicativo Blazor assumirá e responderá com o recurso correto.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-137">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="3cdaa-138">Ao implantar em um servidor IIS, você pode usar o módulo de reescrita de URL com o arquivo *Web. config* publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-138">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="3cdaa-139">Para obter mais informações, consulte a seção [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="3cdaa-139">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="3cdaa-140">Implantação hospedada com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3cdaa-140">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="3cdaa-141">Uma *implantação hospedada* serve o Blazor aplicativo Webassembly para os navegadores de um [aplicativo ASP.NET Core](xref:index) que é executado em um servidor Web.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-141">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="3cdaa-142">O Blazor aplicativo Webassembly do cliente é publicado na pasta */bin/Release/{Target Framework}/Publish/wwwroot* do aplicativo de servidor, juntamente com quaisquer outros ativos estáticos da Web do aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-142">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="3cdaa-143">Os dois aplicativos são implantados juntos.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-143">The two apps are deployed together.</span></span> <span data-ttu-id="3cdaa-144">É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-144">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="3cdaa-145">Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto de \*\* Blazor aplicativo Webassembly\*\* ( `blazorwasm` modelo ao usar o comando [dotnet New](/dotnet/core/tools/dotnet-new) ) com a opção **Hosted** selecionada ( `-ho|--hosted` ao usar o `dotnet new` comando).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-145">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="3cdaa-146">Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-146">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="3cdaa-147">Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-147">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="3cdaa-148">Implantação autônoma</span><span class="sxs-lookup"><span data-stu-id="3cdaa-148">Standalone deployment</span></span>

<span data-ttu-id="3cdaa-149">Uma *implantação autônoma* serve o Blazor aplicativo Webassembly como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-149">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="3cdaa-150">Qualquer servidor de arquivos estático é capaz de atender ao Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-150">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="3cdaa-151">Os ativos de implantação autônomo são publicados na pasta */bin/Release/{Target Framework}/Publish/wwwroot*</span><span class="sxs-lookup"><span data-stu-id="3cdaa-151">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="3cdaa-152">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="3cdaa-152">Azure App Service</span></span>

Blazor<span data-ttu-id="3cdaa-153">Os aplicativos Webassembly podem ser implantados em serviços Azure App no Windows, que hospedam o aplicativo no [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-153"> WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="3cdaa-154">BlazorAtualmente, não há suporte para a implantação de um aplicativo Webassembly autônomo no serviço Azure app para Linux.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-154">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="3cdaa-155">Uma imagem do servidor Linux para hospedar o aplicativo não está disponível no momento.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-155">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="3cdaa-156">O trabalho está em andamento para habilitar esse cenário.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-156">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="3cdaa-157">IIS</span><span class="sxs-lookup"><span data-stu-id="3cdaa-157">IIS</span></span>

<span data-ttu-id="3cdaa-158">O IIS é um servidor de arquivos estático com capacidade para Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-158">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="3cdaa-159">Para configurar o IIS para hospedar Blazor , consulte [criar um site estático no IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-159">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="3cdaa-160">Os ativos publicados são criados na pasta */bin/Release/{TARGET FRAMEWORK}/publish*.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-160">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="3cdaa-161">Hospede o conteúdo da pasta *publish* no servidor Web ou no serviço de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-161">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="3cdaa-162">web.config</span><span class="sxs-lookup"><span data-stu-id="3cdaa-162">web.config</span></span>

<span data-ttu-id="3cdaa-163">Quando um Blazor projeto é publicado, um arquivo *Web. config* é criado com a seguinte configuração do IIS:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-163">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="3cdaa-164">Os tipos MIME são definidos para as seguintes extensões de arquivo:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-164">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="3cdaa-165">*. dll*:`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="3cdaa-165">*.dll*: `application/octet-stream`</span></span>
  * <span data-ttu-id="3cdaa-166">*. JSON*:`application/json`</span><span class="sxs-lookup"><span data-stu-id="3cdaa-166">*.json*: `application/json`</span></span>
  * <span data-ttu-id="3cdaa-167">*. WASM*:`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="3cdaa-167">*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="3cdaa-168">*. WOFF*:`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="3cdaa-168">*.woff*: `application/font-woff`</span></span>
  * <span data-ttu-id="3cdaa-169">*. woff2*:`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="3cdaa-169">*.woff2*: `application/font-woff`</span></span>
* <span data-ttu-id="3cdaa-170">A compactação HTTP está habilitada para os seguintes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-170">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="3cdaa-171">As regras do Módulo de Reescrita de URL são estabelecidas:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-171">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="3cdaa-172">Serve o subdiretório onde residem os ativos estáticos do aplicativo (*wwwroot/{caminho solicitado}*).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-172">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="3cdaa-173">Crie o roteamento de fallback de SPA para que as solicitações de ativos que não sejam de arquivo sejam redirecionadas para o documento padrão do aplicativo em sua pasta de ativos estáticos (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-173">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="3cdaa-174">Usar um Web. config personalizado</span><span class="sxs-lookup"><span data-stu-id="3cdaa-174">Use a custom web.config</span></span>

<span data-ttu-id="3cdaa-175">Para usar um arquivo *Web. config* personalizado, coloque o arquivo *Web. config* personalizado na raiz da pasta do projeto e publique o projeto.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-175">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="3cdaa-176">Instalação do Módulo de Regeneração de URL</span><span class="sxs-lookup"><span data-stu-id="3cdaa-176">Install the URL Rewrite Module</span></span>

<span data-ttu-id="3cdaa-177">O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-177">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="3cdaa-178">Por padrão, o módulo não está instalado e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-178">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="3cdaa-179">O módulo precisa ser baixado do site do IIS.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-179">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="3cdaa-180">Use o Web Platform Installer para instalar o módulo:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-180">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="3cdaa-181">Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-181">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="3cdaa-182">Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-182">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="3cdaa-183">Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-183">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="3cdaa-184">Copie o instalador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-184">Copy the installer to the server.</span></span> <span data-ttu-id="3cdaa-185">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-185">Run the installer.</span></span> <span data-ttu-id="3cdaa-186">Selecione o botão **Instalar** e aceite os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-186">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="3cdaa-187">Uma reinicialização do servidor não será necessária após a conclusão da instalação.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-187">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="3cdaa-188">Configuração do site</span><span class="sxs-lookup"><span data-stu-id="3cdaa-188">Configure the website</span></span>

<span data-ttu-id="3cdaa-189">Defina o **Caminho físico** do site como a pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-189">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="3cdaa-190">A pasta contém:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-190">The folder contains:</span></span>

* <span data-ttu-id="3cdaa-191">O arquivo *web.config* que o IIS usa para configurar o site, incluindo as regras de redirecionamento e os tipos de conteúdo do arquivo necessários.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-191">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="3cdaa-192">A pasta de ativos estática do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-192">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="3cdaa-193">Hospedar como um subaplicativo do IIS</span><span class="sxs-lookup"><span data-stu-id="3cdaa-193">Host as an IIS sub-app</span></span>

<span data-ttu-id="3cdaa-194">Se um aplicativo autônomo for hospedado como um subaplicativo do IIS, execute um dos seguintes procedimentos:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-194">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="3cdaa-195">Desabilite o manipulador do módulo ASP.NET Core herdado.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-195">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="3cdaa-196">Remova o manipulador no Blazor arquivo *Web. config* publicado do aplicativo adicionando uma `<handlers>` seção ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-196">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="3cdaa-197">Desabilite a herança da seção do aplicativo raiz (pai) `<system.webServer>` usando um `<location>` elemento com `inheritInChildApplications` definido como `false` :</span><span class="sxs-lookup"><span data-stu-id="3cdaa-197">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="3cdaa-198">A remoção do manipulador ou a desabilitação da herança é executada além da [configuração do caminho base do aplicativo](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-198">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="3cdaa-199">Defina o caminho base do aplicativo no arquivo *index.html* do aplicativo do alias do IIS usado ao configurar o subaplicativo no IIS.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-199">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="3cdaa-200">Compactação Brotli e gzip</span><span class="sxs-lookup"><span data-stu-id="3cdaa-200">Brotli and Gzip compression</span></span>

<span data-ttu-id="3cdaa-201">O IIS pode ser configurado via *Web. config* para atender a ativos compactados Brotli ou gzip Blazor .</span><span class="sxs-lookup"><span data-stu-id="3cdaa-201">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="3cdaa-202">Para obter um exemplo de configuração, consulte [Web. config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-202">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="3cdaa-203">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="3cdaa-203">Troubleshooting</span></span>

<span data-ttu-id="3cdaa-204">Se um *500 – Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Regeneração de URL está instalado.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-204">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="3cdaa-205">Quando o módulo não estiver instalado, o arquivo *web.config* não poderá ser analisado pelo IIS.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-205">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="3cdaa-206">Isso impede que o Gerenciador do IIS carregue a configuração do site e o site de serviços de Blazor arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-206">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="3cdaa-207">Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-207">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="3cdaa-208">Armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="3cdaa-208">Azure Storage</span></span>

<span data-ttu-id="3cdaa-209">A hospedagem de arquivos estáticos de [armazenamento do Azure](/azure/storage/) permite Hospedagem de aplicativos sem servidor Blazor .</span><span class="sxs-lookup"><span data-stu-id="3cdaa-209">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="3cdaa-210">Nomes de domínio personalizados, CDN (Rede de Distribuição de Conteúdo) do Azure e HTTPS são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-210">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="3cdaa-211">Quando o serviço de blob está habilitado para hospedagem de site estático em uma conta de armazenamento:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-211">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="3cdaa-212">Defina o **Nome do documento de índice** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-212">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="3cdaa-213">Defina o **Caminho do documento de erro** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-213">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="3cdaa-214">os componentes e outros pontos de extremidade que não são de arquivo não residem em caminhos físicos no conteúdo estático armazenado pelo serviço BLOB.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-214"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="3cdaa-215">Quando uma solicitação para um desses recursos é recebida e o Blazor roteador deve lidar, o erro *404-não encontrado* gerado pelo serviço blob roteia a solicitação para o caminho do **documento de erro**.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-215">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="3cdaa-216">O blob *index. html* é retornado e o Blazor roteador carrega e processa o caminho.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-216">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="3cdaa-217">Para mais informações, confira [Hospedagem de site estático no Armazenamento do Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-217">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="3cdaa-218">Nginx</span><span class="sxs-lookup"><span data-stu-id="3cdaa-218">Nginx</span></span>

<span data-ttu-id="3cdaa-219">O arquivo *nginx. conf* a seguir é simplificado para mostrar como configurar o Nginx para enviar o arquivo *index. html* sempre que ele não encontrar um arquivo correspondente no disco.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-219">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="3cdaa-220">Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-220">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="3cdaa-221">Nginx no Docker</span><span class="sxs-lookup"><span data-stu-id="3cdaa-221">Nginx in Docker</span></span>

<span data-ttu-id="3cdaa-222">Para hospedar Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem Nginx baseada em Alpine.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-222">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="3cdaa-223">Atualize o Dockerfile para copiar o arquivo *nginx.config* no contêiner.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-223">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="3cdaa-224">Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-224">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="3cdaa-225">Apache</span><span class="sxs-lookup"><span data-stu-id="3cdaa-225">Apache</span></span>

<span data-ttu-id="3cdaa-226">Para implantar um Blazor aplicativo Webassembly no CentOS 7 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-226">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="3cdaa-227">Crie o arquivo de configuração do Apache.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-227">Create the Apache configuration file.</span></span> <span data-ttu-id="3cdaa-228">O exemplo a seguir é um arquivo de configuração simplificado (*blazorapp. config*):</span><span class="sxs-lookup"><span data-stu-id="3cdaa-228">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="3cdaa-229">Coloque o arquivo de configuração do Apache no `/etc/httpd/conf.d/` diretório, que é o diretório de configuração padrão do Apache no CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-229">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="3cdaa-230">Coloque os arquivos do aplicativo no `/var/www/blazorapp` diretório (o local especificado para `DocumentRoot` o no arquivo de configuração).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-230">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="3cdaa-231">Reinicie o serviço apache.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-231">Restart the Apache service.</span></span>

<span data-ttu-id="3cdaa-232">Para obter mais informações, consulte [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-232">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="3cdaa-233">Páginas do GitHub</span><span class="sxs-lookup"><span data-stu-id="3cdaa-233">GitHub Pages</span></span>

<span data-ttu-id="3cdaa-234">Para lidar com as reescritas de URL, adicione um arquivo *404.html* com um script que manipule o redirecionamento de solicitação para a página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-234">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="3cdaa-235">Para obter uma implementação de exemplo fornecida pela comunidade, confira [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Aplicativos de página única das Páginas do GitHub) ([rafrex/spa-github-pages no GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-235">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="3cdaa-236">Um exemplo usando a abordagem da comunidade pode ser visto em [blazor-demo/blazor-demo.github.io no GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site dinâmico](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-236">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="3cdaa-237">Ao usar um site de projeto em vez de um site de empresa, adicione ou atualize a tag `<base>` no *index.html*.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-237">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="3cdaa-238">Defina o valor de atributo `href` com o nome do repositório GitHub com uma barra à direita (por exemplo, `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-238">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="3cdaa-239">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="3cdaa-239">Host configuration values</span></span>

<span data-ttu-id="3cdaa-240">Os [ Blazor aplicativos Webassembly](xref:blazor/hosting-models#blazor-webassembly) podem aceitar os seguintes valores de configuração de host como argumentos de linha de comando em tempo de execução no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-240">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="3cdaa-241">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="3cdaa-241">Content root</span></span>

<span data-ttu-id="3cdaa-242">O `--contentroot` argumento define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo ([raiz de conteúdo](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-242">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="3cdaa-243">Nos exemplos a seguir, `/content-root-path` é o caminho raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-243">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="3cdaa-244">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-244">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="3cdaa-245">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-245">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="3cdaa-246">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-246">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="3cdaa-247">Esta configuração é usada quando o aplicativo é executado com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-247">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="3cdaa-248">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-248">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="3cdaa-249">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-249">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="3cdaa-250">Caminho base</span><span class="sxs-lookup"><span data-stu-id="3cdaa-250">Path base</span></span>

<span data-ttu-id="3cdaa-251">O `--pathbase` argumento define o caminho base do aplicativo para um aplicativo executado localmente com um caminho de URL relativo não raiz (a `<base>` marca `href` é definida para um caminho diferente `/` de para preparo e produção).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-251">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="3cdaa-252">Nos exemplos a seguir, `/relative-URL-path` é o caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-252">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="3cdaa-253">Para obter mais informações, consulte [caminho base do aplicativo](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-253">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3cdaa-254">Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-254">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="3cdaa-255">Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/">` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-255">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="3cdaa-256">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-256">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="3cdaa-257">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-257">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="3cdaa-258">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-258">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="3cdaa-259">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-259">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="3cdaa-260">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-260">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="3cdaa-261">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-261">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="3cdaa-262">URLs</span><span class="sxs-lookup"><span data-stu-id="3cdaa-262">URLs</span></span>

<span data-ttu-id="3cdaa-263">O argumento `--urls` define os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-263">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="3cdaa-264">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-264">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="3cdaa-265">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-265">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="3cdaa-266">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-266">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="3cdaa-267">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-267">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="3cdaa-268">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-268">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="3cdaa-269">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-269">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="3cdaa-270">Configurar o vinculador</span><span class="sxs-lookup"><span data-stu-id="3cdaa-270">Configure the Linker</span></span>

Blazor<span data-ttu-id="3cdaa-271">executa a vinculação de IL (linguagem intermediária) em cada Build de versão para remover o IL desnecessário dos assemblies de saída.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-271"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="3cdaa-272">Para obter mais informações, consulte <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-272">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="3cdaa-273">Carregamento de recurso de inicialização personalizada</span><span class="sxs-lookup"><span data-stu-id="3cdaa-273">Custom boot resource loading</span></span>

<span data-ttu-id="3cdaa-274">Um Blazor aplicativo Webassembly pode ser inicializado com a `loadBootResource` função para substituir o mecanismo de carregamento do recurso de inicialização interno.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-274">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="3cdaa-275">Use `loadBootResource` para os seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-275">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="3cdaa-276">Permitir que os usuários carreguem recursos estáticos, como dados de fuso horário ou *dotnet. WASM* de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-276">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="3cdaa-277">Carregue assemblies compactados usando uma solicitação HTTP e descompacte-os no cliente para hosts que não dão suporte à busca de conteúdo compactado do servidor.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-277">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="3cdaa-278">Recursos de alias para um nome diferente redirecionando cada `fetch` solicitação para um novo nome.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-278">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="3cdaa-279">`loadBootResource`os parâmetros aparecem na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-279">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="3cdaa-280">Parâmetro</span><span class="sxs-lookup"><span data-stu-id="3cdaa-280">Parameter</span></span>    | <span data-ttu-id="3cdaa-281">Descrição</span><span class="sxs-lookup"><span data-stu-id="3cdaa-281">Description</span></span> |
| ---
<span data-ttu-id="3cdaa-282">Título: ' hospedar e implantar ASP.NET Core Blazor Webassembly ' autor: Descrição: ' saiba como hospedar e implantar um Blazor aplicativo usando ASP.NET Core, CDN (redes de distribuição de conteúdo), servidores de arquivos e páginas do github. '</span><span class="sxs-lookup"><span data-stu-id="3cdaa-282">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="3cdaa-283">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-283">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3cdaa-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-284">'Blazor'</span></span>
- <span data-ttu-id="3cdaa-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-285">'Identity'</span></span>
- <span data-ttu-id="3cdaa-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="3cdaa-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-287">'Razor'</span></span>
- <span data-ttu-id="3cdaa-288">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="3cdaa-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3cdaa-289">Título: ' hospedar e implantar ASP.NET Core Blazor Webassembly ' autor: Descrição: ' saiba como hospedar e implantar um Blazor aplicativo usando ASP.NET Core, CDN (redes de distribuição de conteúdo), servidores de arquivos e páginas do github. '</span><span class="sxs-lookup"><span data-stu-id="3cdaa-289">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="3cdaa-290">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-290">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3cdaa-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-291">'Blazor'</span></span>
- <span data-ttu-id="3cdaa-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-292">'Identity'</span></span>
- <span data-ttu-id="3cdaa-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="3cdaa-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-294">'Razor'</span></span>
- <span data-ttu-id="3cdaa-295">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="3cdaa-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3cdaa-296">Título: ' hospedar e implantar ASP.NET Core Blazor Webassembly ' autor: Descrição: ' saiba como hospedar e implantar um Blazor aplicativo usando ASP.NET Core, CDN (redes de distribuição de conteúdo), servidores de arquivos e páginas do github. '</span><span class="sxs-lookup"><span data-stu-id="3cdaa-296">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="3cdaa-297">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-297">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3cdaa-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-298">'Blazor'</span></span>
- <span data-ttu-id="3cdaa-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-299">'Identity'</span></span>
- <span data-ttu-id="3cdaa-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="3cdaa-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-301">'Razor'</span></span>
- <span data-ttu-id="3cdaa-302">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="3cdaa-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3cdaa-303">Título: ' hospedar e implantar ASP.NET Core Blazor Webassembly ' autor: Descrição: ' saiba como hospedar e implantar um Blazor aplicativo usando ASP.NET Core, CDN (redes de distribuição de conteúdo), servidores de arquivos e páginas do github. '</span><span class="sxs-lookup"><span data-stu-id="3cdaa-303">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="3cdaa-304">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-304">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3cdaa-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-305">'Blazor'</span></span>
- <span data-ttu-id="3cdaa-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-306">'Identity'</span></span>
- <span data-ttu-id="3cdaa-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="3cdaa-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-308">'Razor'</span></span>
- <span data-ttu-id="3cdaa-309">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="3cdaa-309">'SignalR' uid:</span></span> 

<span data-ttu-id="3cdaa-310">------ | título do---: ' hospedar e implantar ASP.NET Core Blazor Webassembly ' autor: Descrição: ' saiba como hospedar e implantar um Blazor aplicativo usando ASP.NET Core, CDN (redes de distribuição de conteúdo), servidores de arquivos e páginas do github. '</span><span class="sxs-lookup"><span data-stu-id="3cdaa-310">------ | --- title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="3cdaa-311">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-311">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3cdaa-312">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-312">'Blazor'</span></span>
- <span data-ttu-id="3cdaa-313">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-313">'Identity'</span></span>
- <span data-ttu-id="3cdaa-314">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-314">'Let's Encrypt'</span></span>
- <span data-ttu-id="3cdaa-315">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-315">'Razor'</span></span>
- <span data-ttu-id="3cdaa-316">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="3cdaa-316">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3cdaa-317">Título: ' hospedar e implantar ASP.NET Core Blazor Webassembly ' autor: Descrição: ' saiba como hospedar e implantar um Blazor aplicativo usando ASP.NET Core, CDN (redes de distribuição de conteúdo), servidores de arquivos e páginas do github. '</span><span class="sxs-lookup"><span data-stu-id="3cdaa-317">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="3cdaa-318">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-318">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3cdaa-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-319">'Blazor'</span></span>
- <span data-ttu-id="3cdaa-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-320">'Identity'</span></span>
- <span data-ttu-id="3cdaa-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="3cdaa-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-322">'Razor'</span></span>
- <span data-ttu-id="3cdaa-323">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="3cdaa-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3cdaa-324">Título: ' hospedar e implantar ASP.NET Core Blazor Webassembly ' autor: Descrição: ' saiba como hospedar e implantar um Blazor aplicativo usando ASP.NET Core, CDN (redes de distribuição de conteúdo), servidores de arquivos e páginas do github. '</span><span class="sxs-lookup"><span data-stu-id="3cdaa-324">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="3cdaa-325">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-325">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3cdaa-326">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-326">'Blazor'</span></span>
- <span data-ttu-id="3cdaa-327">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-327">'Identity'</span></span>
- <span data-ttu-id="3cdaa-328">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-328">'Let's Encrypt'</span></span>
- <span data-ttu-id="3cdaa-329">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3cdaa-329">'Razor'</span></span>
- <span data-ttu-id="3cdaa-330">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="3cdaa-330">'SignalR' uid:</span></span> 

<span data-ttu-id="3cdaa-331">------ | | `type`       | O tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-331">------ | | `type`       | The type of the resource.</span></span> <span data-ttu-id="3cdaa-332">Tipos permissiváveis: `assembly` , `pdb` ,, `dotnetjs` `dotnetwasm` , `timezonedata` | | `name`       | O nome do recurso.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-332">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` | | `name`       | The name of the resource.</span></span> <span data-ttu-id="3cdaa-333">| | `defaultUri` | O URI relativo ou absoluto do recurso.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-333">| | `defaultUri` | The relative or absolute URI of the resource.</span></span> <span data-ttu-id="3cdaa-334">| | `integrity`  | A cadeia de caracteres de integridade que representa o conteúdo esperado na resposta.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-334">| | `integrity`  | The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="3cdaa-335">`loadBootResource`retorna qualquer um dos seguintes para substituir o processo de carregamento:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-335">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="3cdaa-336">Cadeia de caracteres de URI.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-336">URI string.</span></span> <span data-ttu-id="3cdaa-337">No exemplo a seguir (*wwwroot/index.html*), os seguintes arquivos são atendidos de uma CDN em `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="3cdaa-337">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="3cdaa-338">*dotnet. \* . JS*</span><span class="sxs-lookup"><span data-stu-id="3cdaa-338">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="3cdaa-339">*dotnet. WASM*</span><span class="sxs-lookup"><span data-stu-id="3cdaa-339">*dotnet.wasm*</span></span>
  * <span data-ttu-id="3cdaa-340">Dados de fuso horário</span><span class="sxs-lookup"><span data-stu-id="3cdaa-340">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="3cdaa-341">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-341">`Promise<Response>`.</span></span> <span data-ttu-id="3cdaa-342">Passe o `integrity` parâmetro em um cabeçalho para manter o comportamento de verificação de integridade padrão.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-342">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="3cdaa-343">O exemplo a seguir (*wwwroot/index.html*) adiciona um cabeçalho HTTP personalizado às solicitações de saída e passa o `integrity` parâmetro para a `fetch` chamada:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-343">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="3cdaa-344">`null`/`undefined`, o que resulta no comportamento de carregamento padrão.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-344">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="3cdaa-345">As fontes externas devem retornar os cabeçalhos CORS necessários para que os navegadores permitam o carregamento de recursos entre origens.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-345">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="3cdaa-346">O CDNs geralmente fornece os cabeçalhos necessários por padrão.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-346">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="3cdaa-347">Você só precisa especificar tipos para comportamentos personalizados.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-347">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="3cdaa-348">Tipos não especificados para `loadBootResource` são carregados pela estrutura de acordo com seus comportamentos de carregamento padrão.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-348">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="3cdaa-349">Alterar a extensão do nome de arquivo de arquivos DLL</span><span class="sxs-lookup"><span data-stu-id="3cdaa-349">Change the filename extension of DLL files</span></span>

<span data-ttu-id="3cdaa-350">Caso você tenha a necessidade de alterar as extensões de nome de arquivo dos arquivos *. dll* publicados do aplicativo, siga as orientações nesta seção.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-350">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="3cdaa-351">Depois de publicar o aplicativo, use um script de shell ou o pipeline de compilação DevOps para renomear os arquivos *. dll* para usar uma extensão de arquivo diferente.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-351">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="3cdaa-352">Direcione os arquivos *. dll* no diretório *wwwroot* da saída publicada do aplicativo (por exemplo, *{Content root}/bin/Release/netstandard2.1/Publish/wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-352">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="3cdaa-353">Nos exemplos a seguir, os arquivos *. dll* são renomeados para usar a extensão de arquivo *. bin* .</span><span class="sxs-lookup"><span data-stu-id="3cdaa-353">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="3cdaa-354">No Windows:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-354">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="3cdaa-355">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-355">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="3cdaa-356">No Linux ou macOS:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-356">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="3cdaa-357">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-357">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="3cdaa-358">Para usar uma extensão de arquivo diferente de *. bin*, substitua *. bin* nos comandos anteriores.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-358">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="3cdaa-359">Para abordar os arquivos compactados *. boot. JSON. gz* e *blazor.boot.JSON.br* , adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-359">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="3cdaa-360">Remova os arquivos compactados *. boot. JSON. gz* e *blazor.boot.JSON.br* .</span><span class="sxs-lookup"><span data-stu-id="3cdaa-360">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="3cdaa-361">A compactação está desabilitada com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-361">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="3cdaa-362">Recompacte o arquivo *. boot. JSON* atualizado.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-362">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="3cdaa-363">As diretrizes anteriores também se aplicam quando os ativos de trabalho do serviço estão em uso.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-363">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="3cdaa-364">Remova ou recompacte *wwwroot/Service-Worker-assets. js. br* e *wwwroot/Service-Worker-assets. js. gz*.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-364">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span> <span data-ttu-id="3cdaa-365">Caso contrário, as verificações de integridade de arquivo falharão no navegador.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-365">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="3cdaa-366">O exemplo de Windows a seguir usa um script do PowerShell colocado na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3cdaa-366">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="3cdaa-367">*ChangeDLLExtensions. ps1:*:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-367">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="3cdaa-368">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-368">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="3cdaa-369">No arquivo de projeto, o script é executado após a publicação do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="3cdaa-369">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="3cdaa-370">Para fornecer comentários, visite [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="3cdaa-370">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
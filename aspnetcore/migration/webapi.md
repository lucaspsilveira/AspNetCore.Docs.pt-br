---
<span data-ttu-id="61653-101">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="61653-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61653-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61653-102">'Blazor'</span></span>
- <span data-ttu-id="61653-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61653-103">'Identity'</span></span>
- <span data-ttu-id="61653-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61653-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="61653-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61653-105">'Razor'</span></span>
- <span data-ttu-id="61653-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="61653-106">'SignalR' uid:</span></span> 

---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="61653-107">Migrar de ASP.NET Web API para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="61653-107">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="61653-108">De [Scott Addie](https://twitter.com/scott_addie) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="61653-108">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="61653-109">Uma API Web do ASP.NET 4. x é um serviço HTTP que atinge uma ampla variedade de clientes, incluindo navegadores e dispositivos móveis.</span><span class="sxs-lookup"><span data-stu-id="61653-109">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="61653-110">ASP.NET Core combina os modelos de aplicativo de API Web e MVC do ASP.NET 4. x em um único modelo de programação conhecido como ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="61653-110">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="61653-111">Este artigo demonstra as etapas necessárias para migrar da API Web do ASP.NET 4. x para ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="61653-111">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="61653-112">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="61653-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="61653-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="61653-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="61653-114">Examine o projeto de API Web do ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="61653-114">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="61653-115">Este artigo usa o projeto *ProductsApp* criado em [introdução com ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="61653-115">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="61653-116">Nesse projeto, um projeto de API da Web básico do ASP.NET 4. x é configurado da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="61653-116">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="61653-117">No *global.asax.cs*, é feita uma chamada para `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="61653-117">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="61653-118">A `WebApiConfig` classe é encontrada na pasta *App_Start* e tem um método estático `Register` :</span><span class="sxs-lookup"><span data-stu-id="61653-118">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="61653-119">A classe anterior:</span><span class="sxs-lookup"><span data-stu-id="61653-119">The preceding class:</span></span>

* <span data-ttu-id="61653-120">Configura o [Roteamento de atributos](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), embora não esteja realmente sendo usado.</span><span class="sxs-lookup"><span data-stu-id="61653-120">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="61653-121">Configura a tabela de roteamento.</span><span class="sxs-lookup"><span data-stu-id="61653-121">Configures the routing table.</span></span>
<span data-ttu-id="61653-122">O código de exemplo espera que as URLs correspondam ao formato `/api/{controller}/{id}` , `{id}` sendo opcional.</span><span class="sxs-lookup"><span data-stu-id="61653-122">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="61653-123">As seções a seguir demonstram a migração do projeto de API Web para ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="61653-123">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="61653-124">Criar o projeto de destino</span><span class="sxs-lookup"><span data-stu-id="61653-124">Create the destination project</span></span>

<span data-ttu-id="61653-125">Crie uma nova solução em branco no Visual Studio e adicione o projeto de API Web ASP.NET 4. x para migrar:</span><span class="sxs-lookup"><span data-stu-id="61653-125">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="61653-126">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="61653-126">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="61653-127">Selecione o modelo de **solução em branco** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="61653-127">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="61653-128">Nomeie a solução *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="61653-128">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="61653-129">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="61653-129">Select **Create**.</span></span>
1. <span data-ttu-id="61653-130">Adicione o projeto *ProductsApp* existente à solução.</span><span class="sxs-lookup"><span data-stu-id="61653-130">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="61653-131">Adicione um novo projeto de API para migrar para o:</span><span class="sxs-lookup"><span data-stu-id="61653-131">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="61653-132">Adicione um novo projeto de **aplicativo Web ASP.NET Core** à solução.</span><span class="sxs-lookup"><span data-stu-id="61653-132">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="61653-133">Na caixa de diálogo **configurar seu novo projeto** , nomeie o projeto *ProductsCore*e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="61653-133">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="61653-134">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 3,1** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="61653-134">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="61653-135">Selecione o modelo de projeto **API** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="61653-135">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="61653-136">Remova os arquivos de exemplo *WeatherForecast.cs* e *Controllers/WeatherForecastController. cs* do novo projeto *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="61653-136">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="61653-137">A solução agora contém dois projetos.</span><span class="sxs-lookup"><span data-stu-id="61653-137">The solution now contains two projects.</span></span> <span data-ttu-id="61653-138">As seções a seguir explicam como migrar o conteúdo do projeto *ProductsApp* para o projeto *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="61653-138">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="61653-139">Migrar configuração</span><span class="sxs-lookup"><span data-stu-id="61653-139">Migrate configuration</span></span>

<span data-ttu-id="61653-140">ASP.NET Core não usa a pasta *App_Start* ou o arquivo *global. asax* .</span><span class="sxs-lookup"><span data-stu-id="61653-140">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="61653-141">Além disso, o arquivo *Web. config* é adicionado no momento da publicação.</span><span class="sxs-lookup"><span data-stu-id="61653-141">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="61653-142">A classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="61653-142">The `Startup` class:</span></span>

* <span data-ttu-id="61653-143">Substitui *global. asax*.</span><span class="sxs-lookup"><span data-stu-id="61653-143">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="61653-144">Manipula todas as tarefas de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="61653-144">Handles all app startup tasks.</span></span>

<span data-ttu-id="61653-145">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="61653-145">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="61653-146">Migrar modelos e controladores</span><span class="sxs-lookup"><span data-stu-id="61653-146">Migrate models and controllers</span></span>

<span data-ttu-id="61653-147">O código a seguir mostra o `ProductsController` a ser atualizado para ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="61653-147">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="61653-148">Atualize o `ProductsController` para ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="61653-148">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="61653-149">Copie os *controladores/ProductsController. cs* e a pasta *modelos* do projeto original para o novo.</span><span class="sxs-lookup"><span data-stu-id="61653-149">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="61653-150">Altere o namespace raiz dos arquivos copiados para `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="61653-150">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="61653-151">Atualize a `using ProductsApp.Models;` instrução para `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="61653-151">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="61653-152">Os seguintes componentes não existem no ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="61653-152">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="61653-153">Classe `ApiController`</span><span class="sxs-lookup"><span data-stu-id="61653-153">`ApiController` class</span></span>
* <span data-ttu-id="61653-154">Namespace `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="61653-154">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="61653-155">Interface `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="61653-155">`IHttpActionResult` interface</span></span>

<span data-ttu-id="61653-156">Faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="61653-156">Make the following changes:</span></span>

1. <span data-ttu-id="61653-157">Alterar `ApiController` para <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="61653-157">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="61653-158">Adicione `using Microsoft.AspNetCore.Mvc;` para resolver a `ControllerBase` referência.</span><span class="sxs-lookup"><span data-stu-id="61653-158">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="61653-159">Excluir `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="61653-159">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="61653-160">Altere o `GetProduct` tipo de retorno da ação de `IHttpActionResult` para `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="61653-160">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="61653-161">Simplifique a `GetProduct` instrução da ação `return` para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="61653-161">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="61653-162">Configurar o roteamento</span><span class="sxs-lookup"><span data-stu-id="61653-162">Configure routing</span></span>

<span data-ttu-id="61653-163">O modelo de projeto de *API* ASP.NET Core inclui a configuração de roteamento de ponto de extremidade no código gerado.</span><span class="sxs-lookup"><span data-stu-id="61653-163">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="61653-164">O seguinte <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> e as <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> chamadas:</span><span class="sxs-lookup"><span data-stu-id="61653-164">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="61653-165">Registre a correspondência de rota e a execução do ponto de extremidade no pipeline de [middleware](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="61653-165">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="61653-166">Substitua o arquivo de *App_Start/webapiconfig.cs* do projeto *ProductsApp* .</span><span class="sxs-lookup"><span data-stu-id="61653-166">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="61653-167">Configure o roteamento da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="61653-167">Configure routing as follows:</span></span>

1. <span data-ttu-id="61653-168">Marque a `ProductsController` classe com os seguintes atributos:</span><span class="sxs-lookup"><span data-stu-id="61653-168">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="61653-169">O [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atributo anterior configura o padrão de roteamento de atributo do controlador.</span><span class="sxs-lookup"><span data-stu-id="61653-169">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="61653-170">O [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo torna o atributo roteando um requisito para todas as ações neste controlador.</span><span class="sxs-lookup"><span data-stu-id="61653-170">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="61653-171">O roteamento de atributos dá suporte a tokens, como `[controller]` e `[action]` .</span><span class="sxs-lookup"><span data-stu-id="61653-171">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="61653-172">No tempo de execução, cada token é substituído pelo nome do controlador ou da ação, respectivamente, ao qual o atributo foi aplicado.</span><span class="sxs-lookup"><span data-stu-id="61653-172">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="61653-173">Os tokens:</span><span class="sxs-lookup"><span data-stu-id="61653-173">The tokens:</span></span>
    * <span data-ttu-id="61653-174">Reduza o número de cadeias de caracteres mágicos no projeto.</span><span class="sxs-lookup"><span data-stu-id="61653-174">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="61653-175">Certifique-se de que as rotas permaneçam sincronizadas com os controladores e ações correspondentes quando refatoração de renomeação automática forem aplicadas.</span><span class="sxs-lookup"><span data-stu-id="61653-175">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="61653-176">Habilite solicitações HTTP Get para as `ProductController` ações:</span><span class="sxs-lookup"><span data-stu-id="61653-176">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="61653-177">Aplique o [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo à `GetAllProducts` ação.</span><span class="sxs-lookup"><span data-stu-id="61653-177">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="61653-178">Aplique o `[HttpGet("{id}")]` atributo à `GetProduct` ação.</span><span class="sxs-lookup"><span data-stu-id="61653-178">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="61653-179">Execute o projeto migrado e navegue até `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="61653-179">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="61653-180">Uma lista completa de três produtos é exibida.</span><span class="sxs-lookup"><span data-stu-id="61653-180">A full list of three products appears.</span></span> <span data-ttu-id="61653-181">Navegue até `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="61653-181">Browse to `/api/products/1`.</span></span> <span data-ttu-id="61653-182">O primeiro produto é exibido.</span><span class="sxs-lookup"><span data-stu-id="61653-182">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="61653-183">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="61653-183">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="61653-184">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="61653-184">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="61653-185">Examine o projeto de API Web do ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="61653-185">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="61653-186">Este artigo usa o projeto *ProductsApp* criado em [introdução com ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="61653-186">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="61653-187">Nesse projeto, um projeto de API da Web básico do ASP.NET 4. x é configurado da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="61653-187">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="61653-188">No *global.asax.cs*, é feita uma chamada para `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="61653-188">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="61653-189">A `WebApiConfig` classe é encontrada na pasta *App_Start* e tem um método estático `Register` :</span><span class="sxs-lookup"><span data-stu-id="61653-189">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="61653-190">Essa classe configura o [Roteamento de atributos](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), embora ele não esteja realmente sendo usado no projeto.</span><span class="sxs-lookup"><span data-stu-id="61653-190">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="61653-191">Ele também configura a tabela de roteamento, que é usada pelo ASP.NET Web API.</span><span class="sxs-lookup"><span data-stu-id="61653-191">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="61653-192">Nesse caso, a API Web ASP.NET 4. x espera que as URLs correspondam ao formato `/api/{controller}/{id}` , `{id}` sendo opcional.</span><span class="sxs-lookup"><span data-stu-id="61653-192">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="61653-193">As seções a seguir demonstram a migração do projeto de API Web para ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="61653-193">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="61653-194">Criar o projeto de destino</span><span class="sxs-lookup"><span data-stu-id="61653-194">Create the destination project</span></span>

<span data-ttu-id="61653-195">Conclua as seguintes etapas no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="61653-195">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="61653-196">Vá para **arquivo**  >  **novo**  >  **projeto**  >  **outros tipos de projeto**  >  **soluções do Visual Studio**.</span><span class="sxs-lookup"><span data-stu-id="61653-196">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="61653-197">Selecione **solução em branco**e nomeie a solução *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="61653-197">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="61653-198">Clique no botão **OK**.</span><span class="sxs-lookup"><span data-stu-id="61653-198">Click the **OK** button.</span></span>
* <span data-ttu-id="61653-199">Adicione o projeto *ProductsApp* existente à solução.</span><span class="sxs-lookup"><span data-stu-id="61653-199">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="61653-200">Adicione um novo projeto de **aplicativo Web ASP.NET Core** à solução.</span><span class="sxs-lookup"><span data-stu-id="61653-200">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="61653-201">Selecione a estrutura de destino **.NET Core** na lista suspensa e selecione o modelo projeto de **API** .</span><span class="sxs-lookup"><span data-stu-id="61653-201">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="61653-202">Nomeie o projeto *ProductsCore*e clique no botão **OK** .</span><span class="sxs-lookup"><span data-stu-id="61653-202">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="61653-203">A solução agora contém dois projetos.</span><span class="sxs-lookup"><span data-stu-id="61653-203">The solution now contains two projects.</span></span> <span data-ttu-id="61653-204">As seções a seguir explicam como migrar o conteúdo do projeto *ProductsApp* para o projeto *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="61653-204">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="61653-205">Migrar configuração</span><span class="sxs-lookup"><span data-stu-id="61653-205">Migrate configuration</span></span>

<span data-ttu-id="61653-206">ASP.NET Core não usa:</span><span class="sxs-lookup"><span data-stu-id="61653-206">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="61653-207">*App_Start* pasta ou o arquivo *global. asax*</span><span class="sxs-lookup"><span data-stu-id="61653-207">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="61653-208">o arquivo *Web. config* é adicionado no momento da publicação.</span><span class="sxs-lookup"><span data-stu-id="61653-208">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="61653-209">A classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="61653-209">The `Startup` class:</span></span>

* <span data-ttu-id="61653-210">Substitui *global. asax*.</span><span class="sxs-lookup"><span data-stu-id="61653-210">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="61653-211">Manipula todas as tarefas de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="61653-211">Handles all app startup tasks.</span></span>

<span data-ttu-id="61653-212">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="61653-212">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="61653-213">No ASP.NET Core MVC, o roteamento de atributo é incluído por padrão quando <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> é chamado em `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="61653-213">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="61653-214">A chamada a seguir `UseMvc` substitui o arquivo de *App_Start/webapiconfig.cs* do projeto *ProductsApp* :</span><span class="sxs-lookup"><span data-stu-id="61653-214">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="61653-215">Migrar modelos e controladores</span><span class="sxs-lookup"><span data-stu-id="61653-215">Migrate models and controllers</span></span>

<span data-ttu-id="61653-216">O código a seguir mostra a `ProductsController` atualização para ASP.NET Core:[!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="61653-216">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="61653-217">Atualize o `ProductsController` para ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="61653-217">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="61653-218">Copie os *controladores/ProductsController. cs* do projeto original para o novo.</span><span class="sxs-lookup"><span data-stu-id="61653-218">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="61653-219">Copie a pasta *modelos* do projeto original para o novo.</span><span class="sxs-lookup"><span data-stu-id="61653-219">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="61653-220">Altere o namespace raiz dos arquivos copiados para `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="61653-220">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="61653-221">Atualize a `using ProductsApp.Models;` instrução para `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="61653-221">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="61653-222">Os seguintes componentes não existem no ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="61653-222">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="61653-223">Classe `ApiController`</span><span class="sxs-lookup"><span data-stu-id="61653-223">`ApiController` class</span></span>
* <span data-ttu-id="61653-224">Namespace `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="61653-224">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="61653-225">Interface `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="61653-225">`IHttpActionResult` interface</span></span>

<span data-ttu-id="61653-226">Faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="61653-226">Make the following changes:</span></span>

1. <span data-ttu-id="61653-227">Alterar `ApiController` para <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="61653-227">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="61653-228">Adicione `using Microsoft.AspNetCore.Mvc;` para resolver a `ControllerBase` referência.</span><span class="sxs-lookup"><span data-stu-id="61653-228">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="61653-229">Excluir `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="61653-229">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="61653-230">Altere o `GetProduct` tipo de retorno da ação de `IHttpActionResult` para `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="61653-230">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="61653-231">Simplifique a `GetProduct` instrução da ação `return` para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="61653-231">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="61653-232">Configurar o roteamento</span><span class="sxs-lookup"><span data-stu-id="61653-232">Configure routing</span></span>

<span data-ttu-id="61653-233">Configure o roteamento da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="61653-233">Configure routing as follows:</span></span>

1. <span data-ttu-id="61653-234">Marque a `ProductsController` classe com os seguintes atributos:</span><span class="sxs-lookup"><span data-stu-id="61653-234">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="61653-235">O [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atributo anterior configura o padrão de roteamento de atributo do controlador.</span><span class="sxs-lookup"><span data-stu-id="61653-235">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="61653-236">O [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo torna o atributo roteando um requisito para todas as ações neste controlador.</span><span class="sxs-lookup"><span data-stu-id="61653-236">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="61653-237">O roteamento de atributos dá suporte a tokens, como `[controller]` e `[action]` .</span><span class="sxs-lookup"><span data-stu-id="61653-237">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="61653-238">No tempo de execução, cada token é substituído pelo nome do controlador ou da ação, respectivamente, ao qual o atributo foi aplicado.</span><span class="sxs-lookup"><span data-stu-id="61653-238">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="61653-239">Os tokens reduzem o número de cadeias de caracteres mágicos no projeto.</span><span class="sxs-lookup"><span data-stu-id="61653-239">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="61653-240">Os tokens também garantem que as rotas permaneçam sincronizadas com os controladores e ações correspondentes quando refatoração de renomeação automática são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="61653-240">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="61653-241">Defina o modo de compatibilidade do projeto para ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="61653-241">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="61653-242">A alteração anterior:</span><span class="sxs-lookup"><span data-stu-id="61653-242">The preceding change:</span></span>

    * <span data-ttu-id="61653-243">É necessário para usar o `[ApiController]` atributo no nível do controlador.</span><span class="sxs-lookup"><span data-stu-id="61653-243">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="61653-244">Optamos pelos comportamentos potencialmente em potencial introduzidos no ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="61653-244">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="61653-245">Habilite solicitações HTTP Get para as `ProductController` ações:</span><span class="sxs-lookup"><span data-stu-id="61653-245">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="61653-246">Aplique o [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo à `GetAllProducts` ação.</span><span class="sxs-lookup"><span data-stu-id="61653-246">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="61653-247">Aplique o `[HttpGet("{id}")]` atributo à `GetProduct` ação.</span><span class="sxs-lookup"><span data-stu-id="61653-247">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="61653-248">Execute o projeto migrado e navegue até `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="61653-248">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="61653-249">Uma lista completa de três produtos é exibida.</span><span class="sxs-lookup"><span data-stu-id="61653-249">A full list of three products appears.</span></span> <span data-ttu-id="61653-250">Navegue até `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="61653-250">Browse to `/api/products/1`.</span></span> <span data-ttu-id="61653-251">O primeiro produto é exibido.</span><span class="sxs-lookup"><span data-stu-id="61653-251">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="61653-252">Correção de compatibilidade</span><span class="sxs-lookup"><span data-stu-id="61653-252">Compatibility shim</span></span>

<span data-ttu-id="61653-253">A biblioteca [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) fornece um Shim de compatibilidade para mover projetos de API Web ASP.NET 4. x para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="61653-253">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="61653-254">O Shim de compatibilidade estende ASP.NET Core para dar suporte a um número de convenções da API Web 2 do ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="61653-254">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="61653-255">O exemplo de porta anteriormente neste documento é básico o bastante para que o Shim de compatibilidade seja desnecessário.</span><span class="sxs-lookup"><span data-stu-id="61653-255">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="61653-256">Para projetos maiores, o uso do Shim de compatibilidade pode ser útil para a ponte temporária da lacuna da API entre ASP.NET Core e a API Web 2 do ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="61653-256">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="61653-257">O Shim da compatibilidade da API Web deve ser usado como uma medida temporária para dar suporte à migração de projetos de API Web grandes ASP.NET 4. x para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="61653-257">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="61653-258">Ao longo do tempo, os projetos devem ser atualizados para usar padrões de ASP.NET Core em vez de depender do Shim de compatibilidade.</span><span class="sxs-lookup"><span data-stu-id="61653-258">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="61653-259">Os recursos de compatibilidade incluídos no `Microsoft.AspNetCore.Mvc.WebApiCompatShim` incluem:</span><span class="sxs-lookup"><span data-stu-id="61653-259">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="61653-260">Adiciona um `ApiController` tipo para que os tipos base dos controladores não precisem ser atualizados.</span><span class="sxs-lookup"><span data-stu-id="61653-260">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="61653-261">Habilita a associação de modelo de estilo de API Web.</span><span class="sxs-lookup"><span data-stu-id="61653-261">Enables Web API-style model binding.</span></span> <span data-ttu-id="61653-262">ASP.NET Core funções de associação de modelo MVC da mesma forma que o do ASP.NET 4. x MVC 5, por padrão.</span><span class="sxs-lookup"><span data-stu-id="61653-262">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="61653-263">O Shim de compatibilidade altera a associação de modelo para ser mais semelhante às convenções de associação de modelo da API Web 2 do ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="61653-263">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="61653-264">Por exemplo, tipos complexos são associados automaticamente do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="61653-264">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="61653-265">Estende a associação de modelo para que as ações do controlador possam ter parâmetros do tipo `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="61653-265">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="61653-266">Adiciona os formatadores de mensagem, permitindo que as ações retornem os resultados do tipo `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="61653-266">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="61653-267">Adiciona métodos de resposta adicionais que as ações da API Web 2 podem ter usado para atender às respostas:</span><span class="sxs-lookup"><span data-stu-id="61653-267">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="61653-268">`HttpResponseMessage`geradores</span><span class="sxs-lookup"><span data-stu-id="61653-268">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="61653-269">Métodos de resultado da ação:</span><span class="sxs-lookup"><span data-stu-id="61653-269">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="61653-270">Adiciona uma instância do `IContentNegotiator` ao contêiner de injeção de dependência (di) do aplicativo e disponibiliza os tipos relacionados à negociação de conteúdo de [Microsoft. AspNet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="61653-270">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="61653-271">Exemplos desses tipos incluem `DefaultContentNegotiator` e `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="61653-271">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="61653-272">Para usar o Shim de compatibilidade:</span><span class="sxs-lookup"><span data-stu-id="61653-272">To use the compatibility shim:</span></span>

1. <span data-ttu-id="61653-273">Instale o pacote NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="61653-273">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="61653-274">Registre os serviços da correção de compatibilidade com o contêiner de DI do aplicativo chamando `services.AddMvc().AddWebApiConventions()` em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="61653-274">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="61653-275">Defina rotas específicas da API Web usando `MapWebApiRoute` no `IRouteBuilder` na chamada do aplicativo `IApplicationBuilder.UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="61653-275">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="61653-276">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="61653-276">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end

---
title: Migrar de ASP.NET Web API para ASP.NET Core
author: ardalis
description: Saiba como migrar uma implementação de API da Web da API Web do ASP.NET 4. x para ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/webapi
ms.openlocfilehash: 3c8bf27a97de92a42817d4af625976a4920001aa
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84145545"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="cdfa9-103">Migrar de ASP.NET Web API para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cdfa9-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="cdfa9-104">De [Scott Addie](https://twitter.com/scott_addie) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="cdfa9-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="cdfa9-105">Uma API Web do ASP.NET 4. x é um serviço HTTP que atinge uma ampla variedade de clientes, incluindo navegadores e dispositivos móveis.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="cdfa9-106">ASP.NET Core combina os modelos de aplicativo de API Web e MVC do ASP.NET 4. x em um único modelo de programação conhecido como ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-106">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="cdfa9-107">Este artigo demonstra as etapas necessárias para migrar da API Web do ASP.NET 4. x para ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="cdfa9-108">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cdfa9-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="cdfa9-109">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="cdfa9-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="cdfa9-110">Examine o projeto de API Web do ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="cdfa9-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="cdfa9-111">Este artigo usa o projeto *ProductsApp* criado em [introdução com ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="cdfa9-111">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="cdfa9-112">Nesse projeto, um projeto de API da Web básico do ASP.NET 4. x é configurado da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-112">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="cdfa9-113">No *global.asax.cs*, é feita uma chamada para `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="cdfa9-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="cdfa9-114">A `WebApiConfig` classe é encontrada na pasta *App_Start* e tem um método estático `Register` :</span><span class="sxs-lookup"><span data-stu-id="cdfa9-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="cdfa9-115">A classe anterior:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-115">The preceding class:</span></span>

* <span data-ttu-id="cdfa9-116">Configura o [Roteamento de atributos](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), embora não esteja realmente sendo usado.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-116">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="cdfa9-117">Configura a tabela de roteamento.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-117">Configures the routing table.</span></span>
<span data-ttu-id="cdfa9-118">O código de exemplo espera que as URLs correspondam ao formato `/api/{controller}/{id}` , `{id}` sendo opcional.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-118">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="cdfa9-119">As seções a seguir demonstram a migração do projeto de API Web para ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-119">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="cdfa9-120">Criar o projeto de destino</span><span class="sxs-lookup"><span data-stu-id="cdfa9-120">Create the destination project</span></span>

<span data-ttu-id="cdfa9-121">Crie uma nova solução em branco no Visual Studio e adicione o projeto de API Web ASP.NET 4. x para migrar:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-121">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="cdfa9-122">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-122">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="cdfa9-123">Selecione o modelo de **solução em branco** e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-123">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="cdfa9-124">Nomeie a solução *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-124">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="cdfa9-125">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-125">Select **Create**.</span></span>
1. <span data-ttu-id="cdfa9-126">Adicione o projeto *ProductsApp* existente à solução.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-126">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="cdfa9-127">Adicione um novo projeto de API para migrar para o:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-127">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="cdfa9-128">Adicione um novo projeto de **aplicativo Web ASP.NET Core** à solução.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="cdfa9-129">Na caixa de diálogo **configurar seu novo projeto** , nomeie o projeto *ProductsCore*e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-129">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="cdfa9-130">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 3,1** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="cdfa9-131">Selecione o modelo de projeto **API** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-131">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="cdfa9-132">Remova os arquivos de exemplo *WeatherForecast.cs* e *Controllers/WeatherForecastController. cs* do novo projeto *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-132">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="cdfa9-133">A solução agora contém dois projetos.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-133">The solution now contains two projects.</span></span> <span data-ttu-id="cdfa9-134">As seções a seguir explicam como migrar o conteúdo do projeto *ProductsApp* para o projeto *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-134">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="cdfa9-135">Migrar configuração</span><span class="sxs-lookup"><span data-stu-id="cdfa9-135">Migrate configuration</span></span>

<span data-ttu-id="cdfa9-136">ASP.NET Core não usa a pasta *App_Start* ou o arquivo *global. asax* .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-136">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="cdfa9-137">Além disso, o arquivo *Web. config* é adicionado no momento da publicação.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-137">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="cdfa9-138">A classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-138">The `Startup` class:</span></span>

* <span data-ttu-id="cdfa9-139">Substitui *global. asax*.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="cdfa9-140">Manipula todas as tarefas de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="cdfa9-141">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-141">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="cdfa9-142">Migrar modelos e controladores</span><span class="sxs-lookup"><span data-stu-id="cdfa9-142">Migrate models and controllers</span></span>

<span data-ttu-id="cdfa9-143">O código a seguir mostra o `ProductsController` a ser atualizado para ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-143">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="cdfa9-144">Atualize o `ProductsController` para ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-144">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="cdfa9-145">Copie os *controladores/ProductsController. cs* e a pasta *modelos* do projeto original para o novo.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-145">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="cdfa9-146">Altere o namespace raiz dos arquivos copiados para `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-146">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="cdfa9-147">Atualize a `using ProductsApp.Models;` instrução para `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-147">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="cdfa9-148">Os seguintes componentes não existem no ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-148">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="cdfa9-149">Classe `ApiController`</span><span class="sxs-lookup"><span data-stu-id="cdfa9-149">`ApiController` class</span></span>
* <span data-ttu-id="cdfa9-150">Namespace `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="cdfa9-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="cdfa9-151">Interface `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="cdfa9-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="cdfa9-152">Faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-152">Make the following changes:</span></span>

1. <span data-ttu-id="cdfa9-153">Alterar `ApiController` para <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="cdfa9-154">Adicione `using Microsoft.AspNetCore.Mvc;` para resolver a `ControllerBase` referência.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="cdfa9-155">Excluir `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="cdfa9-156">Altere o `GetProduct` tipo de retorno da ação de `IHttpActionResult` para `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="cdfa9-157">Simplifique a `GetProduct` instrução da ação `return` para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="cdfa9-158">Configurar o roteamento</span><span class="sxs-lookup"><span data-stu-id="cdfa9-158">Configure routing</span></span>

<span data-ttu-id="cdfa9-159">O modelo de projeto de *API* ASP.NET Core inclui a configuração de roteamento de ponto de extremidade no código gerado.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-159">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="cdfa9-160">O seguinte <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> e as <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> chamadas:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-160">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="cdfa9-161">Registre a correspondência de rota e a execução do ponto de extremidade no pipeline de [middleware](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-161">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="cdfa9-162">Substitua o arquivo de *App_Start/webapiconfig.cs* do projeto *ProductsApp* .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-162">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="cdfa9-163">Configure o roteamento da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-163">Configure routing as follows:</span></span>

1. <span data-ttu-id="cdfa9-164">Marque a `ProductsController` classe com os seguintes atributos:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-164">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="cdfa9-165">O [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atributo anterior configura o padrão de roteamento de atributo do controlador.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-165">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="cdfa9-166">O [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo torna o atributo roteando um requisito para todas as ações neste controlador.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-166">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="cdfa9-167">O roteamento de atributos dá suporte a tokens, como `[controller]` e `[action]` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-167">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="cdfa9-168">No tempo de execução, cada token é substituído pelo nome do controlador ou da ação, respectivamente, ao qual o atributo foi aplicado.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-168">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="cdfa9-169">Os tokens:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-169">The tokens:</span></span>
    * <span data-ttu-id="cdfa9-170">Reduza o número de cadeias de caracteres mágicos no projeto.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-170">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="cdfa9-171">Certifique-se de que as rotas permaneçam sincronizadas com os controladores e ações correspondentes quando refatoração de renomeação automática forem aplicadas.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-171">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="cdfa9-172">Habilite solicitações HTTP Get para as `ProductController` ações:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-172">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="cdfa9-173">Aplique o [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo à `GetAllProducts` ação.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-173">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="cdfa9-174">Aplique o `[HttpGet("{id}")]` atributo à `GetProduct` ação.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-174">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="cdfa9-175">Execute o projeto migrado e navegue até `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="cdfa9-176">Uma lista completa de três produtos é exibida.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-176">A full list of three products appears.</span></span> <span data-ttu-id="cdfa9-177">Navegue até `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="cdfa9-178">O primeiro produto é exibido.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-178">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cdfa9-179">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cdfa9-179">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="cdfa9-180">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="cdfa9-180">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="cdfa9-181">Examine o projeto de API Web do ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="cdfa9-181">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="cdfa9-182">Este artigo usa o projeto *ProductsApp* criado em [introdução com ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="cdfa9-182">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="cdfa9-183">Nesse projeto, um projeto de API da Web básico do ASP.NET 4. x é configurado da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-183">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="cdfa9-184">No *global.asax.cs*, é feita uma chamada para `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="cdfa9-184">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="cdfa9-185">A `WebApiConfig` classe é encontrada na pasta *App_Start* e tem um método estático `Register` :</span><span class="sxs-lookup"><span data-stu-id="cdfa9-185">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="cdfa9-186">Essa classe configura o [Roteamento de atributos](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), embora ele não esteja realmente sendo usado no projeto.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-186">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="cdfa9-187">Ele também configura a tabela de roteamento, que é usada pelo ASP.NET Web API.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-187">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="cdfa9-188">Nesse caso, a API Web ASP.NET 4. x espera que as URLs correspondam ao formato `/api/{controller}/{id}` , `{id}` sendo opcional.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-188">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="cdfa9-189">As seções a seguir demonstram a migração do projeto de API Web para ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-189">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="cdfa9-190">Criar o projeto de destino</span><span class="sxs-lookup"><span data-stu-id="cdfa9-190">Create the destination project</span></span>

<span data-ttu-id="cdfa9-191">Conclua as seguintes etapas no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-191">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="cdfa9-192">Vá para **arquivo**  >  **novo**  >  **projeto**  >  **outros tipos de projeto**  >  **soluções do Visual Studio**.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-192">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="cdfa9-193">Selecione **solução em branco**e nomeie a solução *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-193">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="cdfa9-194">Clique no botão **OK**.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-194">Click the **OK** button.</span></span>
* <span data-ttu-id="cdfa9-195">Adicione o projeto *ProductsApp* existente à solução.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-195">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="cdfa9-196">Adicione um novo projeto de **aplicativo Web ASP.NET Core** à solução.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-196">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="cdfa9-197">Selecione a estrutura de destino **.NET Core** na lista suspensa e selecione o modelo projeto de **API** .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-197">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="cdfa9-198">Nomeie o projeto *ProductsCore*e clique no botão **OK** .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-198">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="cdfa9-199">A solução agora contém dois projetos.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-199">The solution now contains two projects.</span></span> <span data-ttu-id="cdfa9-200">As seções a seguir explicam como migrar o conteúdo do projeto *ProductsApp* para o projeto *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-200">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="cdfa9-201">Migrar configuração</span><span class="sxs-lookup"><span data-stu-id="cdfa9-201">Migrate configuration</span></span>

<span data-ttu-id="cdfa9-202">ASP.NET Core não usa:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-202">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="cdfa9-203">*App_Start* pasta ou o arquivo *global. asax*</span><span class="sxs-lookup"><span data-stu-id="cdfa9-203">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="cdfa9-204">o arquivo *Web. config* é adicionado no momento da publicação.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-204">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="cdfa9-205">A classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-205">The `Startup` class:</span></span>

* <span data-ttu-id="cdfa9-206">Substitui *global. asax*.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-206">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="cdfa9-207">Manipula todas as tarefas de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-207">Handles all app startup tasks.</span></span>

<span data-ttu-id="cdfa9-208">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-208">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="cdfa9-209">No ASP.NET Core MVC, o roteamento de atributo é incluído por padrão quando <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> é chamado em `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-209">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="cdfa9-210">A chamada a seguir `UseMvc` substitui o arquivo de *App_Start/webapiconfig.cs* do projeto *ProductsApp* :</span><span class="sxs-lookup"><span data-stu-id="cdfa9-210">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="cdfa9-211">Migrar modelos e controladores</span><span class="sxs-lookup"><span data-stu-id="cdfa9-211">Migrate models and controllers</span></span>

<span data-ttu-id="cdfa9-212">O código a seguir mostra a `ProductsController` atualização para ASP.NET Core:[!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="cdfa9-212">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="cdfa9-213">Atualize o `ProductsController` para ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-213">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="cdfa9-214">Copie os *controladores/ProductsController. cs* do projeto original para o novo.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-214">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="cdfa9-215">Copie a pasta *modelos* do projeto original para o novo.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-215">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="cdfa9-216">Altere o namespace raiz dos arquivos copiados para `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-216">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="cdfa9-217">Atualize a `using ProductsApp.Models;` instrução para `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-217">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="cdfa9-218">Os seguintes componentes não existem no ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-218">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="cdfa9-219">Classe `ApiController`</span><span class="sxs-lookup"><span data-stu-id="cdfa9-219">`ApiController` class</span></span>
* <span data-ttu-id="cdfa9-220">Namespace `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="cdfa9-220">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="cdfa9-221">Interface `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="cdfa9-221">`IHttpActionResult` interface</span></span>

<span data-ttu-id="cdfa9-222">Faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-222">Make the following changes:</span></span>

1. <span data-ttu-id="cdfa9-223">Alterar `ApiController` para <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-223">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="cdfa9-224">Adicione `using Microsoft.AspNetCore.Mvc;` para resolver a `ControllerBase` referência.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-224">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="cdfa9-225">Excluir `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-225">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="cdfa9-226">Altere o `GetProduct` tipo de retorno da ação de `IHttpActionResult` para `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-226">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="cdfa9-227">Simplifique a `GetProduct` instrução da ação `return` para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-227">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="cdfa9-228">Configurar o roteamento</span><span class="sxs-lookup"><span data-stu-id="cdfa9-228">Configure routing</span></span>

<span data-ttu-id="cdfa9-229">Configure o roteamento da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-229">Configure routing as follows:</span></span>

1. <span data-ttu-id="cdfa9-230">Marque a `ProductsController` classe com os seguintes atributos:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-230">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="cdfa9-231">O [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atributo anterior configura o padrão de roteamento de atributo do controlador.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-231">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="cdfa9-232">O [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo torna o atributo roteando um requisito para todas as ações neste controlador.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-232">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="cdfa9-233">O roteamento de atributos dá suporte a tokens, como `[controller]` e `[action]` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-233">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="cdfa9-234">No tempo de execução, cada token é substituído pelo nome do controlador ou da ação, respectivamente, ao qual o atributo foi aplicado.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-234">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="cdfa9-235">Os tokens reduzem o número de cadeias de caracteres mágicos no projeto.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-235">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="cdfa9-236">Os tokens também garantem que as rotas permaneçam sincronizadas com os controladores e ações correspondentes quando refatoração de renomeação automática são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-236">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="cdfa9-237">Defina o modo de compatibilidade do projeto para ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-237">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="cdfa9-238">A alteração anterior:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-238">The preceding change:</span></span>

    * <span data-ttu-id="cdfa9-239">É necessário para usar o `[ApiController]` atributo no nível do controlador.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-239">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="cdfa9-240">Optamos pelos comportamentos potencialmente em potencial introduzidos no ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-240">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="cdfa9-241">Habilite solicitações HTTP Get para as `ProductController` ações:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-241">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="cdfa9-242">Aplique o [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo à `GetAllProducts` ação.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-242">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="cdfa9-243">Aplique o `[HttpGet("{id}")]` atributo à `GetProduct` ação.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-243">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="cdfa9-244">Execute o projeto migrado e navegue até `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-244">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="cdfa9-245">Uma lista completa de três produtos é exibida.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-245">A full list of three products appears.</span></span> <span data-ttu-id="cdfa9-246">Navegue até `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-246">Browse to `/api/products/1`.</span></span> <span data-ttu-id="cdfa9-247">O primeiro produto é exibido.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-247">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="cdfa9-248">Correção de compatibilidade</span><span class="sxs-lookup"><span data-stu-id="cdfa9-248">Compatibility shim</span></span>

<span data-ttu-id="cdfa9-249">A biblioteca [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) fornece um Shim de compatibilidade para mover projetos de API Web ASP.NET 4. x para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-249">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="cdfa9-250">O Shim de compatibilidade estende ASP.NET Core para dar suporte a um número de convenções da API Web 2 do ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-250">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="cdfa9-251">O exemplo de porta anteriormente neste documento é básico o bastante para que o Shim de compatibilidade seja desnecessário.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-251">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="cdfa9-252">Para projetos maiores, o uso do Shim de compatibilidade pode ser útil para a ponte temporária da lacuna da API entre ASP.NET Core e a API Web 2 do ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-252">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="cdfa9-253">O Shim da compatibilidade da API Web deve ser usado como uma medida temporária para dar suporte à migração de projetos de API Web grandes ASP.NET 4. x para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-253">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="cdfa9-254">Ao longo do tempo, os projetos devem ser atualizados para usar padrões de ASP.NET Core em vez de depender do Shim de compatibilidade.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-254">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="cdfa9-255">Os recursos de compatibilidade incluídos no `Microsoft.AspNetCore.Mvc.WebApiCompatShim` incluem:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-255">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="cdfa9-256">Adiciona um `ApiController` tipo para que os tipos base dos controladores não precisem ser atualizados.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-256">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="cdfa9-257">Habilita a associação de modelo de estilo de API Web.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-257">Enables Web API-style model binding.</span></span> <span data-ttu-id="cdfa9-258">ASP.NET Core funções de associação de modelo MVC da mesma forma que o do ASP.NET 4. x MVC 5, por padrão.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-258">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="cdfa9-259">O Shim de compatibilidade altera a associação de modelo para ser mais semelhante às convenções de associação de modelo da API Web 2 do ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-259">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="cdfa9-260">Por exemplo, tipos complexos são associados automaticamente do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="cdfa9-260">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="cdfa9-261">Estende a associação de modelo para que as ações do controlador possam ter parâmetros do tipo `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-261">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="cdfa9-262">Adiciona os formatadores de mensagem, permitindo que as ações retornem os resultados do tipo `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-262">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="cdfa9-263">Adiciona métodos de resposta adicionais que as ações da API Web 2 podem ter usado para atender às respostas:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-263">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="cdfa9-264">`HttpResponseMessage`geradores</span><span class="sxs-lookup"><span data-stu-id="cdfa9-264">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="cdfa9-265">Métodos de resultado da ação:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-265">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="cdfa9-266">Adiciona uma instância do `IContentNegotiator` ao contêiner de injeção de dependência (di) do aplicativo e disponibiliza os tipos relacionados à negociação de conteúdo de [Microsoft. AspNet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="cdfa9-266">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="cdfa9-267">Exemplos desses tipos incluem `DefaultContentNegotiator` e `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-267">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="cdfa9-268">Para usar o Shim de compatibilidade:</span><span class="sxs-lookup"><span data-stu-id="cdfa9-268">To use the compatibility shim:</span></span>

1. <span data-ttu-id="cdfa9-269">Instale o pacote NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-269">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="cdfa9-270">Registre os serviços da correção de compatibilidade com o contêiner de DI do aplicativo chamando `services.AddMvc().AddWebApiConventions()` em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-270">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="cdfa9-271">Defina rotas específicas da API Web usando `MapWebApiRoute` no `IRouteBuilder` na chamada do aplicativo `IApplicationBuilder.UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="cdfa9-271">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cdfa9-272">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cdfa9-272">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end

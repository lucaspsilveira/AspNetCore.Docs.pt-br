---
title: Chame uma API web Blazor do ASP.NET Core WebAssembly
author: guardrex
description: Saiba como chamar uma API Blazor web a partir de um aplicativo WebAssembly usando ajudantes JSON, incluindo fazer solicitações de compartilhamento de recursos de origem cruzada (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 2f2d4150f4fa1e7f47310f2a88b816f445cd1d3a
ms.sourcegitcommit: 49c91ad4b69f4f8032394cbf2d5ae1b19a7f863b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81544850"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="8a4e6-103">Ligue para uma API web da ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="8a4e6-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="8a4e6-104">Por [Luke Latham](https://github.com/guardrex), Daniel [Roth](https://github.com/danroth27), e Juan De [la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="8a4e6-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="8a4e6-105">Os aplicativos WebAssembly chamam APIs `HttpClient` da Web usando um serviço pré-configurado. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="8a4e6-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="8a4e6-106">Componha as solicitações, que podem incluir opções [de API](https://developer.mozilla.org/docs/Web/API/Fetch_API) javaScript Fetch, usando Blazor ajudantes JSON ou com <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="8a4e6-107">O `HttpClient` serviço Blazor em aplicativos WebAssembly é focado em fazer solicitações de volta ao servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="8a4e6-108">A orientação neste tópico Blazor diz respeito apenas aos aplicativos WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="8a4e6-109">Os aplicativos do servidor chamam APIs da <xref:System.Net.Http.IHttpClientFactory>Web usando <xref:System.Net.Http.HttpClient> instâncias, normalmente criadas usando . [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="8a4e6-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="8a4e6-110">A orientação neste tópico não Blazor diz respeito aos aplicativos do Server.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="8a4e6-111">Ao Blazor desenvolver aplicativos server, siga <xref:fundamentals/http-requests>as orientações em .</span><span class="sxs-lookup"><span data-stu-id="8a4e6-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="8a4e6-112">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) [(como baixar)](xref:index#how-to-download-a-sample) &ndash; Selecione o aplicativo *BlazorWebAssemblySample.*</span><span class="sxs-lookup"><span data-stu-id="8a4e6-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="8a4e6-113">Veja os seguintes componentes no aplicativo de amostra *BlazorWebAssemblySample:*</span><span class="sxs-lookup"><span data-stu-id="8a4e6-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="8a4e6-114">Chamada Web API *(Páginas/CallWebAPI.razor)*</span><span class="sxs-lookup"><span data-stu-id="8a4e6-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="8a4e6-115">TESTADOR de solicitação HTTP (*Componentes/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="8a4e6-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="8a4e6-116">Pacotes</span><span class="sxs-lookup"><span data-stu-id="8a4e6-116">Packages</span></span>

<span data-ttu-id="8a4e6-117">Faça referência ao pacote [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="8a4e6-118">Adicione o serviço HttpClient</span><span class="sxs-lookup"><span data-stu-id="8a4e6-118">Add the HttpClient service</span></span>

<span data-ttu-id="8a4e6-119">Em `Program.Main`, `HttpClient` adicionar um serviço se ele ainda não existir:</span><span class="sxs-lookup"><span data-stu-id="8a4e6-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="8a4e6-120">Ajudantes httpclient e JSON</span><span class="sxs-lookup"><span data-stu-id="8a4e6-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="8a4e6-121">Em Blazor um aplicativo WebAssembly, [httpClient](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta para o servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="8a4e6-122">Um Blazor aplicativo server não `HttpClient` inclui um serviço por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="8a4e6-123">Forneça `HttpClient` um aplicativo usando a [infra-estrutura de fábrica HttpClient.](xref:fundamentals/http-requests)</span><span class="sxs-lookup"><span data-stu-id="8a4e6-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="8a4e6-124">`HttpClient`e os ajudantes JSON também são usados para chamar de pontos finais de API da Web de terceiros.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="8a4e6-125">`HttpClient`é implementado usando a [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) do navegador e está sujeito às suas limitações, incluindo a aplicação da mesma política de origem.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="8a4e6-126">O endereço base do cliente está definido como endereço do servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="8a4e6-127">Injete `HttpClient` uma `@inject` instância usando a diretiva:</span><span class="sxs-lookup"><span data-stu-id="8a4e6-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="8a4e6-128">Nos exemplos a seguir, uma API toda web processa as operações de criação, leitura, atualização e exclusão (CRUD).</span><span class="sxs-lookup"><span data-stu-id="8a4e6-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="8a4e6-129">Os exemplos são baseados em uma `TodoItem` classe que armazena o:</span><span class="sxs-lookup"><span data-stu-id="8a4e6-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="8a4e6-130">ID`Id`( `long` &ndash; , ) ID único do item.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="8a4e6-131">Nome`Name`( `string` &ndash; ) Nome do item.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="8a4e6-132">Status`IsComplete`( `bool` &ndash; ) Indicação se o item Todo estiver concluído.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="8a4e6-133">Os métodos de ajuda json enviam solicitações para um URI (uma API web nos seguintes exemplos) e processam a resposta:</span><span class="sxs-lookup"><span data-stu-id="8a4e6-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="8a4e6-134">`GetFromJsonAsync`&ndash; Envia uma solicitação HTTP GET e analisa o corpo de resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="8a4e6-135">No código a `_todoItems` seguir, os são exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="8a4e6-136">O `GetTodoItems` método é acionado quando o componente é concluído renderização[(OnInitializedAsync).](xref:blazor/lifecycle#component-initialization-methods)</span><span class="sxs-lookup"><span data-stu-id="8a4e6-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="8a4e6-137">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="8a4e6-138">`PostAsJsonAsync`&ndash; Envia uma solicitação HTTP POST, incluindo conteúdo codificado pelo JSON, e analisa o corpo de resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="8a4e6-139">No código a `_newItemName` seguir, é fornecido por um elemento vinculado do componente.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="8a4e6-140">O `AddItem` método é acionado selecionando um `<button>` elemento.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="8a4e6-141">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-141">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="8a4e6-142">Chamadas `PostAsJsonAsync` para <xref:System.Net.Http.HttpResponseMessage>retornar um .</span><span class="sxs-lookup"><span data-stu-id="8a4e6-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span>

* <span data-ttu-id="8a4e6-143">`PutAsJsonAsync`&ndash; Envia uma solicitação HTTP PUT, incluindo conteúdo codificado pelo JSON.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-143">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="8a4e6-144">No código a `_editItem` seguir, `IsCompleted` os valores são `Name` fornecidos por elementos vinculados do componente.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-144">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="8a4e6-145">O item `Id` é definido quando o item é selecionado em `EditItem` outra parte da ui e é chamado.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-145">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="8a4e6-146">O `SaveItem` método é acionado selecionando o elemento Salvar. `<button>`</span><span class="sxs-lookup"><span data-stu-id="8a4e6-146">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="8a4e6-147">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-147">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```
  
  <span data-ttu-id="8a4e6-148">Chamadas `PutAsJsonAsync` para <xref:System.Net.Http.HttpResponseMessage>retornar um .</span><span class="sxs-lookup"><span data-stu-id="8a4e6-148">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span>

<span data-ttu-id="8a4e6-149"><xref:System.Net.Http>inclui métodos de extensão adicionais para enviar solicitações HTTP e receber respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-149"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="8a4e6-150">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) é usado para enviar uma solicitação HTTP DELETE para uma API web.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-150">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="8a4e6-151">No código a seguir, o elemento Excluir `<button>` chama o `DeleteItem` método.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-151">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="8a4e6-152">O `<input>` elemento vinculado `id` fornece o item a ser excluído.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-152">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="8a4e6-153">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-153">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="8a4e6-154">Compartilhamento de recursos de origem cruzada (CORS)</span><span class="sxs-lookup"><span data-stu-id="8a4e6-154">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="8a4e6-155">A segurança do navegador impede que uma página da Web evideta solicitações para um domínio diferente daquele que serviu à página da Web.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-155">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="8a4e6-156">Essa restrição é chamada *de política de mesma origem.*</span><span class="sxs-lookup"><span data-stu-id="8a4e6-156">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="8a4e6-157">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-157">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="8a4e6-158">Para fazer solicitações do navegador para um ponto final com uma origem diferente, o *ponto final* deve habilitar o compartilhamento de recursos de origem [cruzada (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="8a4e6-158">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="8a4e6-159">O [ Blazor aplicativo de amostra WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstra o uso do CORS no componente Call Web API *(Pages/CallWebAPI.razor*).</span><span class="sxs-lookup"><span data-stu-id="8a4e6-159">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="8a4e6-160">Para permitir que outros sites façam solicitações de compartilhamento de <xref:security/cors>recursos de origem cruzada (CORS) ao seu aplicativo, consulte .</span><span class="sxs-lookup"><span data-stu-id="8a4e6-160">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="8a4e6-161">HttpClient e HttpRequestMessage com opções de solicitação de API do Fetch</span><span class="sxs-lookup"><span data-stu-id="8a4e6-161">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="8a4e6-162">Ao executar no WebAssembly em um Blazor aplicativo WebAssembly, use [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> para personalizar solicitações.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-162">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="8a4e6-163">Por exemplo, você pode especificar o uri de solicitação, o método HTTP e quaisquer cabeçalhos de solicitação desejados.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-163">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="8a4e6-164">Para obter mais informações sobre as opções de API do Fetch, consulte [os docs da Web DoMN: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="8a4e6-164">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="8a4e6-165">Ao enviar credenciais (cookies/cabeçalhos de `Authorization` autorização) nas solicitações do CORS, o cabeçalho deve ser permitido pela política cors.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-165">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="8a4e6-166">A seguinte política inclui configuração para:</span><span class="sxs-lookup"><span data-stu-id="8a4e6-166">The following policy includes configuration for:</span></span>

* <span data-ttu-id="8a4e6-167">Solicitar origens`http://localhost:5000` `https://localhost:5001`(, ).</span><span class="sxs-lookup"><span data-stu-id="8a4e6-167">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="8a4e6-168">Qualquer método (verbo).</span><span class="sxs-lookup"><span data-stu-id="8a4e6-168">Any method (verb).</span></span>
* <span data-ttu-id="8a4e6-169">`Content-Type`e `Authorization` cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="8a4e6-169">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="8a4e6-170">Para permitir um cabeçalho `x-custom-header`personalizado (por <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>exemplo), liste o cabeçalho ao ligar .</span><span class="sxs-lookup"><span data-stu-id="8a4e6-170">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="8a4e6-171">Credenciais definidas pelo código`credentials` JavaScript `include`do lado do cliente (propriedade definida para ).</span><span class="sxs-lookup"><span data-stu-id="8a4e6-171">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="8a4e6-172">Para obter mais <xref:security/cors> informações, consulte o componente HTTP Request Tester do aplicativo de amostra *(Components/HTTPRequestTester.razor).*</span><span class="sxs-lookup"><span data-stu-id="8a4e6-172">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a4e6-173">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8a4e6-173">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="8a4e6-174">Configuração do ponto final do Kestrel HTTPS</span><span class="sxs-lookup"><span data-stu-id="8a4e6-174">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="8a4e6-175">Cross Origin Resource Sharing (CORS) no W3C</span><span class="sxs-lookup"><span data-stu-id="8a4e6-175">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)

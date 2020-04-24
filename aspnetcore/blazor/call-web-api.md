---
title: Chamar uma API Web de ASP.NET Core Blazor Webassembly
author: guardrex
description: Saiba como chamar uma API da Web de um Blazor aplicativo Webassembly usando auxiliares JSON, incluindo a criação de solicitações de CORS (compartilhamento de recursos entre origens).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: abc546cc0079a01e3999b2c7c083235d3fff9b06
ms.sourcegitcommit: e94ecfae6a3ef568fa197da791c8bc595917d17a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82122225"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="defef-103">Chamar uma API Web do ASP.NET Core mais</span><span class="sxs-lookup"><span data-stu-id="defef-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="defef-104">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="defef-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="defef-105">Aplicativos [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) chamam APIs da Web usando um `HttpClient` serviço pré-configurado.</span><span class="sxs-lookup"><span data-stu-id="defef-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="defef-106">Redação de solicitações, que podem incluir opções de API de busca de JavaScript, usando auxiliares de <xref:System.Net.Http.HttpRequestMessage>JSON mais [populadores](https://developer.mozilla.org/docs/Web/API/Fetch_API) ou com o.</span><span class="sxs-lookup"><span data-stu-id="defef-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="defef-107">O `HttpClient` serviço em aplicativos Webassembly mais podestas se concentra em fazer solicitações de volta ao servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="defef-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="defef-108">As diretrizes neste tópico referem-se apenas a aplicativos Webassembly mais podestas.</span><span class="sxs-lookup"><span data-stu-id="defef-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="defef-109">Aplicativos de [servidor mais incrivelmente](xref:blazor/hosting-models#blazor-server) chamam APIs da <xref:System.Net.Http.HttpClient> Web usando instâncias, normalmente <xref:System.Net.Http.IHttpClientFactory>criadas usando.</span><span class="sxs-lookup"><span data-stu-id="defef-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="defef-110">As diretrizes neste tópico não pertencem a aplicativos de servidor mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="defef-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="defef-111">Ao desenvolver aplicativos de servidor mais incrivelmente, siga as orientações <xref:fundamentals/http-requests>em.</span><span class="sxs-lookup"><span data-stu-id="defef-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="defef-112">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample)) &ndash; selecione o aplicativo *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="defef-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="defef-113">Consulte os seguintes componentes no aplicativo de exemplo *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="defef-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="defef-114">Chamar a API Web (*pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="defef-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="defef-115">Testador de solicitação HTTP (*componentes/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="defef-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="defef-116">Pacotes</span><span class="sxs-lookup"><span data-stu-id="defef-116">Packages</span></span>

<span data-ttu-id="defef-117">Referencie o pacote NuGet [System .net. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="defef-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="defef-118">Adicionar o serviço HttpClient</span><span class="sxs-lookup"><span data-stu-id="defef-118">Add the HttpClient service</span></span>

<span data-ttu-id="defef-119">No `Program.Main`, adicione um `HttpClient` serviço se ele ainda não existir:</span><span class="sxs-lookup"><span data-stu-id="defef-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="defef-120">Auxiliares HttpClient e JSON</span><span class="sxs-lookup"><span data-stu-id="defef-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="defef-121">Em um aplicativo Webassembly mais novo, o [HttpClient](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta ao servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="defef-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="defef-122">Um aplicativo de servidor mais incrivelmente não inclui `HttpClient` um serviço por padrão.</span><span class="sxs-lookup"><span data-stu-id="defef-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="defef-123">Forneça um `HttpClient` para o aplicativo usando a [infraestrutura de fábrica do HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="defef-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="defef-124">`HttpClient`e auxiliares JSON também são usados para chamar pontos de extremidade de API Web de terceiros.</span><span class="sxs-lookup"><span data-stu-id="defef-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="defef-125">`HttpClient`é implementado usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de navegador e está sujeito a suas limitações, incluindo a imposição da mesma política de origem.</span><span class="sxs-lookup"><span data-stu-id="defef-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="defef-126">O endereço base do cliente é definido como o endereço do servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="defef-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="defef-127">Injetar `HttpClient` uma instância usando `@inject` a diretiva:</span><span class="sxs-lookup"><span data-stu-id="defef-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="defef-128">Nos exemplos a seguir, uma API Web todo processa as operações CRUD (criar, ler, atualizar e excluir).</span><span class="sxs-lookup"><span data-stu-id="defef-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="defef-129">Os exemplos são baseados em uma `TodoItem` classe que armazena:</span><span class="sxs-lookup"><span data-stu-id="defef-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="defef-130">ID (`Id`, `long`) &ndash; ID exclusiva do item.</span><span class="sxs-lookup"><span data-stu-id="defef-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="defef-131">Nome (`Name`, `string`) &ndash; nome do item.</span><span class="sxs-lookup"><span data-stu-id="defef-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="defef-132">Indicação de`IsComplete`status `bool`( &ndash; ,) se o item de tarefas pendentes for concluído.</span><span class="sxs-lookup"><span data-stu-id="defef-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="defef-133">Os métodos auxiliares JSON enviam solicitações para um URI (uma API Web nos exemplos a seguir) e processam a resposta:</span><span class="sxs-lookup"><span data-stu-id="defef-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="defef-134">`GetFromJsonAsync`&ndash; Envia uma solicitação HTTP Get e analisa o corpo da resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="defef-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="defef-135">No código a seguir, os `_todoItems` são exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="defef-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="defef-136">O `GetTodoItems` método é disparado quando o processamento do componente é concluído ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="defef-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="defef-137">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="defef-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="defef-138">`PostAsJsonAsync`&ndash; Envia uma solicitação HTTP post, incluindo conteúdo codificado em JSON, e analisa o corpo da resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="defef-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="defef-139">No código a seguir, `_newItemName` é fornecido por um elemento associado do componente.</span><span class="sxs-lookup"><span data-stu-id="defef-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="defef-140">O `AddItem` método é disparado selecionando um `<button>` elemento.</span><span class="sxs-lookup"><span data-stu-id="defef-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="defef-141">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="defef-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="defef-142">Chamadas para `PostAsJsonAsync` retornar um <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="defef-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="defef-143">Para desserializar o conteúdo JSON da mensagem de resposta, use o `ReadFromJsonAsync<T>` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="defef-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="defef-144">`PutAsJsonAsync`&ndash; Envia uma solicitação HTTP PUT, incluindo conteúdo codificado em JSON.</span><span class="sxs-lookup"><span data-stu-id="defef-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="defef-145">No código a seguir, `_editItem` os valores `Name` para `IsCompleted` e são fornecidos pelos elementos associados do componente.</span><span class="sxs-lookup"><span data-stu-id="defef-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="defef-146">O item `Id` é definido quando o item é selecionado em outra parte da interface do usuário e `EditItem` é chamado.</span><span class="sxs-lookup"><span data-stu-id="defef-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="defef-147">O `SaveItem` método é disparado selecionando `<button>` o elemento Save.</span><span class="sxs-lookup"><span data-stu-id="defef-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="defef-148">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="defef-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="defef-149">Chamadas para `PutAsJsonAsync` retornar um <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="defef-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="defef-150">Para desserializar o conteúdo JSON da mensagem de resposta, use o `ReadFromJsonAsync<T>` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="defef-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="defef-151"><xref:System.Net.Http>inclui métodos de extensão adicionais para enviar solicitações HTTP e receber respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="defef-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="defef-152">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) é usado para enviar uma solicitação HTTP Delete para uma API da Web.</span><span class="sxs-lookup"><span data-stu-id="defef-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="defef-153">No código a seguir, o elemento `<button>` Delete chama o `DeleteItem` método.</span><span class="sxs-lookup"><span data-stu-id="defef-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="defef-154">O elemento `<input>` associado fornece o `id` do item a ser excluído.</span><span class="sxs-lookup"><span data-stu-id="defef-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="defef-155">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="defef-155">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="defef-156">CORS (compartilhamento de recursos entre origens)</span><span class="sxs-lookup"><span data-stu-id="defef-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="defef-157">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que atuou na página da Web.</span><span class="sxs-lookup"><span data-stu-id="defef-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="defef-158">Essa restrição é chamada de *política de mesma origem*.</span><span class="sxs-lookup"><span data-stu-id="defef-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="defef-159">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="defef-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="defef-160">Para fazer solicitações do navegador para um ponto de extremidade com uma origem diferente, o *ponto de extremidade* deve habilitar o [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="defef-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="defef-161">O [aplicativo de exemplo Webassembly mais claro (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstra o uso do CORS no componente chamar API da Web (*pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="defef-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="defef-162">Para permitir que outros sites façam solicitações de compartilhamento de recursos entre origens (CORS) para seu aplicativo, <xref:security/cors>consulte.</span><span class="sxs-lookup"><span data-stu-id="defef-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="defef-163">HttpClient e HttpRequestMessage com opções de solicitação de API de busca</span><span class="sxs-lookup"><span data-stu-id="defef-163">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="defef-164">Ao executar em Webassembly em um aplicativo Webassembly mais incrivelmente, use [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> para personalizar solicitações.</span><span class="sxs-lookup"><span data-stu-id="defef-164">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="defef-165">Por exemplo, você pode especificar o URI de solicitação, o método HTTP e todos os cabeçalhos de solicitação desejados.</span><span class="sxs-lookup"><span data-stu-id="defef-165">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                JsonContent.Create(new TodoItem
                { 
                    Name: "A New Todo Item",
                    IsComplete: false
                })
        };
        
        requestMessage.Headers.Authorization = 
           new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="defef-166">A implementação do .NET Webassembly `HttpClient` de usa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="defef-166">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="defef-167">FETCH permite configurar várias [opções específicas de solicitação](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="defef-167">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="defef-168">As opções de solicitação de busca HTTP podem `HttpRequestMessage` ser configuradas com métodos de extensão mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="defef-168">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="defef-169">`HttpRequestMessage`método de extensão</span><span class="sxs-lookup"><span data-stu-id="defef-169">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="defef-170">Buscar propriedade de solicitação</span><span class="sxs-lookup"><span data-stu-id="defef-170">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="defef-171">fornecidas</span><span class="sxs-lookup"><span data-stu-id="defef-171">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="defef-172">armazenar</span><span class="sxs-lookup"><span data-stu-id="defef-172">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="defef-173">mode</span><span class="sxs-lookup"><span data-stu-id="defef-173">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="defef-174">verifica</span><span class="sxs-lookup"><span data-stu-id="defef-174">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="defef-175">Você pode definir opções adicionais usando o método de `SetBrowserRequestOption` extensão mais genérico.</span><span class="sxs-lookup"><span data-stu-id="defef-175">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="defef-176">A resposta HTTP normalmente é armazenada em buffer Blazor em um aplicativo Webassembly para habilitar o suporte para leituras de sincronização no conteúdo da resposta.</span><span class="sxs-lookup"><span data-stu-id="defef-176">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="defef-177">Para habilitar o suporte para streaming de resposta, `SetBrowserResponseStreamingEnabled` use o método de extensão na solicitação.</span><span class="sxs-lookup"><span data-stu-id="defef-177">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="defef-178">Para incluir credenciais em uma solicitação entre origens, use o método `SetBrowserRequestCredentials` de extensão:</span><span class="sxs-lookup"><span data-stu-id="defef-178">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="defef-179">Para obter mais informações sobre as opções de API de busca, consulte [MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="defef-179">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="defef-180">Ao enviar credenciais (cookies/cabeçalhos de autorização) em solicitações CORS, `Authorization` o cabeçalho deve ser permitido pela política CORS.</span><span class="sxs-lookup"><span data-stu-id="defef-180">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="defef-181">A política a seguir inclui a configuração para o:</span><span class="sxs-lookup"><span data-stu-id="defef-181">The following policy includes configuration for:</span></span>

* <span data-ttu-id="defef-182">Origens da solicitação`http://localhost:5000`( `https://localhost:5001`,).</span><span class="sxs-lookup"><span data-stu-id="defef-182">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="defef-183">Qualquer método (verbo).</span><span class="sxs-lookup"><span data-stu-id="defef-183">Any method (verb).</span></span>
* <span data-ttu-id="defef-184">`Content-Type`cabeçalhos `Authorization` e.</span><span class="sxs-lookup"><span data-stu-id="defef-184">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="defef-185">Para permitir um cabeçalho personalizado (por exemplo, `x-custom-header`), liste o cabeçalho ao <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>chamar.</span><span class="sxs-lookup"><span data-stu-id="defef-185">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="defef-186">Credenciais definidas pelo código JavaScript do lado do cliente`credentials` (propriedade definida `include`como).</span><span class="sxs-lookup"><span data-stu-id="defef-186">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="defef-187">Para obter mais informações, <xref:security/cors> consulte e o componente testador de solicitação HTTP do aplicativo de exemplo (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="defef-187">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="defef-188">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="defef-188">Additional resources</span></span>

* [<span data-ttu-id="defef-189">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="defef-189">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="defef-190">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="defef-190">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="defef-191">Configuração de ponto de extremidade HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="defef-191">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="defef-192">CORS (compartilhamento de recursos entre origens) no W3C</span><span class="sxs-lookup"><span data-stu-id="defef-192">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)

---
title: Chamar uma API da Web de ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Saiba como chamar uma API da Web de um Blazor WebAssembly aplicativo usando auxiliares JSON, incluindo a criação de solicitações de CORS (compartilhamento de recursos entre origens).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 1417056beac99a8dfee47131c2cb6ab7ec52ad1e
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445262"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="373f9-103">Chamar uma API da Web de ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="373f9-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="373f9-104">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="373f9-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

> [!NOTE]
> <span data-ttu-id="373f9-105">Este tópico aplica-se a Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="373f9-105">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="373f9-106">[Blazor Server](xref:blazor/hosting-models#blazor-server)os aplicativos chamam APIs da Web usando <xref:System.Net.Http.HttpClient> instâncias, normalmente criadas usando <xref:System.Net.Http.IHttpClientFactory> .</span><span class="sxs-lookup"><span data-stu-id="373f9-106">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="373f9-107">Para obter diretrizes que se aplicam ao Blazor Server , consulte <xref:fundamentals/http-requests> .</span><span class="sxs-lookup"><span data-stu-id="373f9-107">For guidance that applies to Blazor Server, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="373f9-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)os aplicativos chamam APIs Web usando um serviço pré-configurado <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="373f9-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="373f9-109">Redação de solicitações, que podem incluir opções de [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript, usando Blazor auxiliares JSON ou com <xref:System.Net.Http.HttpRequestMessage> .</span><span class="sxs-lookup"><span data-stu-id="373f9-109">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="373f9-110">O <xref:System.Net.Http.HttpClient> serviço em Blazor WebAssembly aplicativos se concentra em fazer solicitações de volta para o servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="373f9-110">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="373f9-111">As diretrizes neste tópico referem-se apenas a Blazor WebAssembly aplicativos.</span><span class="sxs-lookup"><span data-stu-id="373f9-111">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="373f9-112">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample)): selecione o `BlazorWebAssemblySample` aplicativo.</span><span class="sxs-lookup"><span data-stu-id="373f9-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the `BlazorWebAssemblySample` app.</span></span>

<span data-ttu-id="373f9-113">Consulte os seguintes componentes no `BlazorWebAssemblySample` aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="373f9-113">See the following components in the `BlazorWebAssemblySample` sample app:</span></span>

* <span data-ttu-id="373f9-114">Chamar API Web ( `Pages/CallWebAPI.razor` )</span><span class="sxs-lookup"><span data-stu-id="373f9-114">Call Web API (`Pages/CallWebAPI.razor`)</span></span>
* <span data-ttu-id="373f9-115">Testador de solicitação HTTP ( `Components/HTTPRequestTester.razor` )</span><span class="sxs-lookup"><span data-stu-id="373f9-115">HTTP Request Tester (`Components/HTTPRequestTester.razor`)</span></span>

## <a name="packages"></a><span data-ttu-id="373f9-116">Pacotes</span><span class="sxs-lookup"><span data-stu-id="373f9-116">Packages</span></span>

<span data-ttu-id="373f9-117">Referencie o [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json/) pacote NuGet no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="373f9-117">Reference the [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="373f9-118">Adicionar o serviço HttpClient</span><span class="sxs-lookup"><span data-stu-id="373f9-118">Add the HttpClient service</span></span>

<span data-ttu-id="373f9-119">No `Program.Main` , adicione um <xref:System.Net.Http.HttpClient> serviço se ele ainda não existir:</span><span class="sxs-lookup"><span data-stu-id="373f9-119">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="373f9-120">Auxiliares HttpClient e JSON</span><span class="sxs-lookup"><span data-stu-id="373f9-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="373f9-121">Em um Blazor WebAssembly aplicativo, [`HttpClient`](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta ao servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="373f9-121">In a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="373f9-122">Um Blazor Server aplicativo não inclui um <xref:System.Net.Http.HttpClient> serviço por padrão.</span><span class="sxs-lookup"><span data-stu-id="373f9-122">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="373f9-123">Forneça um <xref:System.Net.Http.HttpClient> para o aplicativo usando a [ `HttpClient` infraestrutura de fábrica](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="373f9-123">Provide an <xref:System.Net.Http.HttpClient> to the app using the [`HttpClient` factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="373f9-124"><xref:System.Net.Http.HttpClient>e auxiliares JSON também são usados para chamar pontos de extremidade de API Web de terceiros.</span><span class="sxs-lookup"><span data-stu-id="373f9-124"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="373f9-125"><xref:System.Net.Http.HttpClient>é implementado usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de navegador e está sujeito a suas limitações, incluindo a imposição da mesma política de origem.</span><span class="sxs-lookup"><span data-stu-id="373f9-125"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="373f9-126">O endereço base do cliente é definido como o endereço do servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="373f9-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="373f9-127">Injetar uma <xref:System.Net.Http.HttpClient> instância usando a [`@inject`](xref:mvc/views/razor#inject) diretiva:</span><span class="sxs-lookup"><span data-stu-id="373f9-127">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="373f9-128">Nos exemplos a seguir, uma API Web todo processa as operações CRUD (criar, ler, atualizar e excluir).</span><span class="sxs-lookup"><span data-stu-id="373f9-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="373f9-129">Os exemplos são baseados em uma `TodoItem` classe que armazena:</span><span class="sxs-lookup"><span data-stu-id="373f9-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="373f9-130">ID ( `Id` , `long` ): ID exclusiva do item.</span><span class="sxs-lookup"><span data-stu-id="373f9-130">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="373f9-131">Nome ( `Name` , `string` ): o nome do item.</span><span class="sxs-lookup"><span data-stu-id="373f9-131">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="373f9-132">Status ( `IsComplete` , `bool` ): indica se o item de tarefas pendentes foi concluído.</span><span class="sxs-lookup"><span data-stu-id="373f9-132">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="373f9-133">Os métodos auxiliares JSON enviam solicitações para um URI (uma API Web nos exemplos a seguir) e processam a resposta:</span><span class="sxs-lookup"><span data-stu-id="373f9-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="373f9-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Envia uma solicitação HTTP GET e analisa o corpo da resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="373f9-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="373f9-135">No código a seguir, os `todoItems` são exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="373f9-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="373f9-136">O `GetTodoItems` método é disparado quando a renderização do componente é concluída ( [`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) ).</span><span class="sxs-lookup"><span data-stu-id="373f9-136">The `GetTodoItems` method is triggered when the component is finished rendering ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="373f9-137">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="373f9-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="373f9-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Envia uma solicitação HTTP POST, incluindo conteúdo codificado em JSON, e analisa o corpo da resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="373f9-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="373f9-139">No código a seguir, `newItemName` é fornecido por um elemento associado do componente.</span><span class="sxs-lookup"><span data-stu-id="373f9-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="373f9-140">O `AddItem` método é disparado selecionando um `<button>` elemento.</span><span class="sxs-lookup"><span data-stu-id="373f9-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="373f9-141">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="373f9-141">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="373f9-142">Chamadas para <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> retornar um <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="373f9-142">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="373f9-143">Para desserializar o conteúdo JSON da mensagem de resposta, use o `ReadFromJsonAsync<T>` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="373f9-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="373f9-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Envia uma solicitação HTTP PUT, incluindo conteúdo codificado em JSON.</span><span class="sxs-lookup"><span data-stu-id="373f9-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="373f9-145">No código a seguir, `editItem` os valores para `Name` e `IsCompleted` são fornecidos pelos elementos associados do componente.</span><span class="sxs-lookup"><span data-stu-id="373f9-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="373f9-146">O item `Id` é definido quando o item é selecionado em outra parte da interface do usuário e `EditItem` é chamado.</span><span class="sxs-lookup"><span data-stu-id="373f9-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="373f9-147">O `SaveItem` método é disparado selecionando o `<button>` elemento Save.</span><span class="sxs-lookup"><span data-stu-id="373f9-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="373f9-148">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="373f9-148">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="373f9-149">Chamadas para <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> retornar um <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="373f9-149">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="373f9-150">Para desserializar o conteúdo JSON da mensagem de resposta, use o <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> método de extensão:</span><span class="sxs-lookup"><span data-stu-id="373f9-150">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="373f9-151"><xref:System.Net.Http>inclui métodos de extensão adicionais para enviar solicitações HTTP e receber respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="373f9-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="373f9-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType>é usado para enviar uma solicitação HTTP DELETE para uma API da Web.</span><span class="sxs-lookup"><span data-stu-id="373f9-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="373f9-153">No código a seguir, o `<button>` elemento delete chama o `DeleteItem` método.</span><span class="sxs-lookup"><span data-stu-id="373f9-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="373f9-154">O `<input>` elemento associado fornece o `id` do item a ser excluído.</span><span class="sxs-lookup"><span data-stu-id="373f9-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="373f9-155">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="373f9-155">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="373f9-156">Chamado HttpClient com IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="373f9-156">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="373f9-157"><xref:System.Net.Http.IHttpClientFactory>Há suporte para serviços e para a configuração de um nome <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="373f9-157"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="373f9-158">Referencie o [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/) pacote NuGet no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="373f9-158">Reference the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package in the project file.</span></span>

<span data-ttu-id="373f9-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="373f9-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="373f9-160">`FetchData`componente ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="373f9-160">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="373f9-161">HttpClient digitado</span><span class="sxs-lookup"><span data-stu-id="373f9-161">Typed HttpClient</span></span>

<span data-ttu-id="373f9-162"><xref:System.Net.Http.HttpClient>O tipo usa uma ou mais das instâncias do aplicativo <xref:System.Net.Http.HttpClient> , padrão ou nomeadas, para retornar dados de um ou mais pontos de extremidade da API Web.</span><span class="sxs-lookup"><span data-stu-id="373f9-162">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="373f9-163">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="373f9-163">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastClient
{
    private readonly HttpClient client;

    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];
    
        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }
    
        return forecasts;
    }
}
```

<span data-ttu-id="373f9-164">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="373f9-164">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="373f9-165">Os componentes injetam o tipo <xref:System.Net.Http.HttpClient> para chamar a API da Web.</span><span class="sxs-lookup"><span data-stu-id="373f9-165">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="373f9-166">`FetchData`componente ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="373f9-166">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Client.GetForecastAsync();
    }
}
```

## <a name="handle-errors"></a><span data-ttu-id="373f9-167">Tratar erros</span><span class="sxs-lookup"><span data-stu-id="373f9-167">Handle errors</span></span>

<span data-ttu-id="373f9-168">Quando ocorrem erros durante a interação com uma API da Web, eles podem ser manipulados pelo código do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="373f9-168">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="373f9-169">Por exemplo, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> espera uma resposta JSON da API do servidor com um `Content-Type` de `application/json` .</span><span class="sxs-lookup"><span data-stu-id="373f9-169">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="373f9-170">Se a resposta não estiver no formato JSON, a validação de conteúdo lançará um <xref:System.NotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="373f9-170">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="373f9-171">No exemplo a seguir, o ponto de extremidade do URI para a solicitação de dados previsão do tempo está incorreto.</span><span class="sxs-lookup"><span data-stu-id="373f9-171">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="373f9-172">O URI deve ser para `WeatherForecast` , mas aparece na chamada como `WeatherForcast` ("e" ausente).</span><span class="sxs-lookup"><span data-stu-id="373f9-172">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="373f9-173">A <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> chamada espera que o JSON seja retornado, mas o servidor retorna HTML para uma exceção sem tratamento no servidor com um `Content-Type` de `text/html` .</span><span class="sxs-lookup"><span data-stu-id="373f9-173">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="373f9-174">A exceção sem tratamento ocorre no servidor porque o caminho não é encontrado e o middleware não pode servir a uma página ou exibição para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="373f9-174">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="373f9-175">No <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> no cliente, <xref:System.NotSupportedException> é gerado quando o conteúdo da resposta é validado como não JSON.</span><span class="sxs-lookup"><span data-stu-id="373f9-175">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="373f9-176">A exceção é capturada no `catch` bloco, em que a lógica personalizada pode registrar o erro ou apresentar uma mensagem de erro amigável ao usuário:</span><span class="sxs-lookup"><span data-stu-id="373f9-176">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="373f9-177">O exemplo anterior é para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="373f9-177">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="373f9-178">Um aplicativo de servidor de API Web pode ser configurado para retornar JSON mesmo quando um ponto de extremidade não existe ou uma exceção sem tratamento no servidor ocorre.</span><span class="sxs-lookup"><span data-stu-id="373f9-178">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled exception on the server occurs.</span></span>

<span data-ttu-id="373f9-179">Para obter mais informações, consulte <xref:blazor/fundamentals/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="373f9-179">For more information, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="373f9-180">CORS (compartilhamento de recursos entre origens)</span><span class="sxs-lookup"><span data-stu-id="373f9-180">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="373f9-181">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que atuou na página da Web.</span><span class="sxs-lookup"><span data-stu-id="373f9-181">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="373f9-182">Essa restrição é chamada de *política de mesma origem*.</span><span class="sxs-lookup"><span data-stu-id="373f9-182">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="373f9-183">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="373f9-183">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="373f9-184">Para fazer solicitações do navegador para um ponto de extremidade com uma origem diferente, o *ponto de extremidade* deve habilitar o [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="373f9-184">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="373f9-185">O [ Blazor WebAssembly aplicativo de exemplo ( Blazor WebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstra o uso do CORS no componente Call Web API ( `Pages/CallWebAPI.razor` ).</span><span class="sxs-lookup"><span data-stu-id="373f9-185">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (`Pages/CallWebAPI.razor`).</span></span>

<span data-ttu-id="373f9-186">Para obter mais informações sobre o CORS com solicitações seguras em Blazor aplicativos, consulte <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors> .</span><span class="sxs-lookup"><span data-stu-id="373f9-186">For more information on CORS with secure requests in Blazor apps, see <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>.</span></span>

<span data-ttu-id="373f9-187">Para obter informações gerais sobre o CORS com aplicativos ASP.NET Core, consulte <xref:security/cors> .</span><span class="sxs-lookup"><span data-stu-id="373f9-187">For general information on CORS with ASP.NET Core apps, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="373f9-188">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="373f9-188">Additional resources</span></span>

* <span data-ttu-id="373f9-189"><xref:blazor/security/webassembly/additional-scenarios>: Inclui cobertura no uso do <xref:System.Net.Http.HttpClient> para fazer solicitações de API da Web seguras.</span><span class="sxs-lookup"><span data-stu-id="373f9-189"><xref:blazor/security/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="373f9-190">Configuração de ponto de extremidade HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="373f9-190">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="373f9-191">CORS (compartilhamento de recursos entre origens) no W3C</span><span class="sxs-lookup"><span data-stu-id="373f9-191">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)

---
title: Chamar uma API Web de ASP.NET Core Blazor Webassembly
author: guardrex
description: Saiba como chamar uma API da Web de um Blazor aplicativo Webassembly usando auxiliares JSON, incluindo a criação de solicitações de CORS (compartilhamento de recursos entre origens).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 7ed2d51c0d41a50a2e139d739a0a06cd9f392a83
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153497"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="869d2-103">Chamar uma API da Web de ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="869d2-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="869d2-104">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="869d2-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="869d2-105">Aplicativos [ Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) chamam APIs da Web usando um serviço pré-configurado `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="869d2-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="869d2-106">Redação de solicitações, que podem incluir opções de [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript, usando Blazor auxiliares JSON ou com <xref:System.Net.Http.HttpRequestMessage> .</span><span class="sxs-lookup"><span data-stu-id="869d2-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="869d2-107">O `HttpClient` serviço em Blazor aplicativos Webassembly é voltado para fazer solicitações de volta ao servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="869d2-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="869d2-108">As diretrizes neste tópico pertencem apenas aos Blazor aplicativos Webassembly.</span><span class="sxs-lookup"><span data-stu-id="869d2-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="869d2-109">Aplicativos de [ Blazor servidor](xref:blazor/hosting-models#blazor-server) chamam APIs da Web usando <xref:System.Net.Http.HttpClient> instâncias, normalmente criadas usando <xref:System.Net.Http.IHttpClientFactory> .</span><span class="sxs-lookup"><span data-stu-id="869d2-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="869d2-110">As diretrizes neste tópico não pertencem a Blazor aplicativos de servidor.</span><span class="sxs-lookup"><span data-stu-id="869d2-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="869d2-111">Ao desenvolver Blazor aplicativos de servidor, siga as orientações em <xref:fundamentals/http-requests> .</span><span class="sxs-lookup"><span data-stu-id="869d2-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="869d2-112">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample)) &ndash; Selecione o aplicativo *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="869d2-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="869d2-113">Consulte os seguintes componentes no aplicativo de exemplo *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="869d2-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="869d2-114">Chamar a API Web (*pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="869d2-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="869d2-115">Testador de solicitação HTTP (*componentes/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="869d2-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="869d2-116">Pacotes</span><span class="sxs-lookup"><span data-stu-id="869d2-116">Packages</span></span>

<span data-ttu-id="869d2-117">Referencie o pacote NuGet [System .net. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="869d2-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="869d2-118">Adicionar o serviço HttpClient</span><span class="sxs-lookup"><span data-stu-id="869d2-118">Add the HttpClient service</span></span>

<span data-ttu-id="869d2-119">No `Program.Main` , adicione um `HttpClient` serviço se ele ainda não existir:</span><span class="sxs-lookup"><span data-stu-id="869d2-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="869d2-120">Auxiliares HttpClient e JSON</span><span class="sxs-lookup"><span data-stu-id="869d2-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="869d2-121">Em um Blazor aplicativo Webassembly, o [HttpClient](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta ao servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="869d2-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="869d2-122">Um Blazor aplicativo de servidor não inclui um `HttpClient` serviço por padrão.</span><span class="sxs-lookup"><span data-stu-id="869d2-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="869d2-123">Forneça um `HttpClient` para o aplicativo usando a [infraestrutura de fábrica do HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="869d2-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="869d2-124">`HttpClient`e auxiliares JSON também são usados para chamar pontos de extremidade de API Web de terceiros.</span><span class="sxs-lookup"><span data-stu-id="869d2-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="869d2-125">`HttpClient`é implementado usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de navegador e está sujeito a suas limitações, incluindo a imposição da mesma política de origem.</span><span class="sxs-lookup"><span data-stu-id="869d2-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="869d2-126">O endereço base do cliente é definido como o endereço do servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="869d2-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="869d2-127">Injetar uma `HttpClient` instância usando a `@inject` diretiva:</span><span class="sxs-lookup"><span data-stu-id="869d2-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="869d2-128">Nos exemplos a seguir, uma API Web todo processa as operações CRUD (criar, ler, atualizar e excluir).</span><span class="sxs-lookup"><span data-stu-id="869d2-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="869d2-129">Os exemplos são baseados em uma `TodoItem` classe que armazena:</span><span class="sxs-lookup"><span data-stu-id="869d2-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="869d2-130">ID ( `Id` , `long` ) &ndash; ID exclusiva do item.</span><span class="sxs-lookup"><span data-stu-id="869d2-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="869d2-131">Nome ( `Name` , `string` ) &ndash; nome do item.</span><span class="sxs-lookup"><span data-stu-id="869d2-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="869d2-132">`IsComplete`Indicação de status (, `bool` ) &ndash; se o item de tarefas pendentes for concluído.</span><span class="sxs-lookup"><span data-stu-id="869d2-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="869d2-133">Os métodos auxiliares JSON enviam solicitações para um URI (uma API Web nos exemplos a seguir) e processam a resposta:</span><span class="sxs-lookup"><span data-stu-id="869d2-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="869d2-134">`GetFromJsonAsync`&ndash;Envia uma solicitação HTTP Get e analisa o corpo da resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="869d2-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="869d2-135">No código a seguir, os `todoItems` são exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="869d2-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="869d2-136">O `GetTodoItems` método é disparado quando o processamento do componente é concluído ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="869d2-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="869d2-137">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="869d2-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="869d2-138">`PostAsJsonAsync`&ndash;Envia uma solicitação HTTP post, incluindo conteúdo codificado em JSON, e analisa o corpo da resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="869d2-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="869d2-139">No código a seguir, `newItemName` é fornecido por um elemento associado do componente.</span><span class="sxs-lookup"><span data-stu-id="869d2-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="869d2-140">O `AddItem` método é disparado selecionando um `<button>` elemento.</span><span class="sxs-lookup"><span data-stu-id="869d2-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="869d2-141">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="869d2-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="869d2-142">Chamadas para `PostAsJsonAsync` retornar um <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="869d2-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="869d2-143">Para desserializar o conteúdo JSON da mensagem de resposta, use o `ReadFromJsonAsync<T>` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="869d2-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="869d2-144">`PutAsJsonAsync`&ndash;Envia uma solicitação HTTP PUT, incluindo conteúdo codificado em JSON.</span><span class="sxs-lookup"><span data-stu-id="869d2-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="869d2-145">No código a seguir, `editItem` os valores para `Name` e `IsCompleted` são fornecidos pelos elementos associados do componente.</span><span class="sxs-lookup"><span data-stu-id="869d2-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="869d2-146">O item `Id` é definido quando o item é selecionado em outra parte da interface do usuário e `EditItem` é chamado.</span><span class="sxs-lookup"><span data-stu-id="869d2-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="869d2-147">O `SaveItem` método é disparado selecionando o `<button>` elemento Save.</span><span class="sxs-lookup"><span data-stu-id="869d2-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="869d2-148">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="869d2-148">See the sample app for a complete example.</span></span>

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
          var editItem = todoItems.Single(i => i.Id == id);
          editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="869d2-149">Chamadas para `PutAsJsonAsync` retornar um <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="869d2-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="869d2-150">Para desserializar o conteúdo JSON da mensagem de resposta, use o `ReadFromJsonAsync<T>` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="869d2-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="869d2-151"><xref:System.Net.Http>inclui métodos de extensão adicionais para enviar solicitações HTTP e receber respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="869d2-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="869d2-152">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) é usado para enviar uma solicitação HTTP Delete para uma API da Web.</span><span class="sxs-lookup"><span data-stu-id="869d2-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="869d2-153">No código a seguir, o `<button>` elemento delete chama o `DeleteItem` método.</span><span class="sxs-lookup"><span data-stu-id="869d2-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="869d2-154">O `<input>` elemento associado fornece o `id` do item a ser excluído.</span><span class="sxs-lookup"><span data-stu-id="869d2-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="869d2-155">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="869d2-155">See the sample app for a complete example.</span></span>

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

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="869d2-156">Chamado HttpClient com IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="869d2-156">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="869d2-157"><xref:System.Net.Http.IHttpClientFactory>Há suporte para serviços e para a configuração de um nome <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="869d2-157"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="869d2-158">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="869d2-158">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="869d2-159">`FetchData`componente (*pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="869d2-159">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="869d2-160">HttpClient digitado</span><span class="sxs-lookup"><span data-stu-id="869d2-160">Typed HttpClient</span></span>

<span data-ttu-id="869d2-161"><xref:System.Net.Http.HttpClient>O tipo usa uma ou mais das instâncias do aplicativo <xref:System.Net.Http.HttpClient> , padrão ou nomeadas, para retornar dados de um ou mais pontos de extremidade da API Web.</span><span class="sxs-lookup"><span data-stu-id="869d2-161">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="869d2-162">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="869d2-162">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="869d2-163">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="869d2-163">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="869d2-164">Os componentes injetam o tipo `HttpClient` para chamar a API da Web.</span><span class="sxs-lookup"><span data-stu-id="869d2-164">Components inject the typed `HttpClient` to call the web API.</span></span>

<span data-ttu-id="869d2-165">`FetchData`componente (*pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="869d2-165">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="handle-errors"></a><span data-ttu-id="869d2-166">Tratar erros</span><span class="sxs-lookup"><span data-stu-id="869d2-166">Handle errors</span></span>

<span data-ttu-id="869d2-167">Quando ocorrem erros durante a interação com uma API da Web, eles podem ser manipulados pelo código do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="869d2-167">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="869d2-168">Por exemplo, `GetFromJsonAsync` espera uma resposta JSON da API do servidor com um `Content-Type` de `application/json` .</span><span class="sxs-lookup"><span data-stu-id="869d2-168">For example, `GetFromJsonAsync` expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="869d2-169">Se a resposta não estiver no formato JSON, a validação de conteúdo lançará um <xref:System.NotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="869d2-169">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="869d2-170">No exemplo a seguir, o ponto de extremidade do URI para a solicitação de dados previsão do tempo está incorreto.</span><span class="sxs-lookup"><span data-stu-id="869d2-170">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="869d2-171">O URI deve ser para `WeatherForecast` , mas aparece na chamada como `WeatherForcast` ("e" ausente).</span><span class="sxs-lookup"><span data-stu-id="869d2-171">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="869d2-172">A `GetFromJsonAsync` chamada espera que o JSON seja retornado, mas o servidor retorna HTML para uma exceção sem tratamento no servidor com um `Content-Type` de `text/html` .</span><span class="sxs-lookup"><span data-stu-id="869d2-172">The `GetFromJsonAsync` call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="869d2-173">A exceção sem tratamento ocorre no servidor porque o caminho não é encontrado e o middleware não pode servir a uma página ou exibição para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="869d2-173">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="869d2-174">No `OnInitializedAsync` no cliente, <xref:System.NotSupportedException> é gerado quando o conteúdo da resposta é validado como não JSON.</span><span class="sxs-lookup"><span data-stu-id="869d2-174">In `OnInitializedAsync` on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="869d2-175">A exceção é capturada no `catch` bloco, em que a lógica personalizada pode registrar o erro ou apresentar uma mensagem de erro amigável ao usuário:</span><span class="sxs-lookup"><span data-stu-id="869d2-175">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

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
> <span data-ttu-id="869d2-176">O exemplo anterior é para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="869d2-176">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="869d2-177">Um aplicativo de servidor de API Web pode ser configurado para retornar JSON mesmo quando um ponto de extremidade não existe ou um exceção sem tratamento no servidor ocorre.</span><span class="sxs-lookup"><span data-stu-id="869d2-177">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="869d2-178">Para obter mais informações, consulte <xref:blazor/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="869d2-178">For more information, see <xref:blazor/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="869d2-179">CORS (compartilhamento de recursos entre origens)</span><span class="sxs-lookup"><span data-stu-id="869d2-179">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="869d2-180">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que atuou na página da Web.</span><span class="sxs-lookup"><span data-stu-id="869d2-180">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="869d2-181">Essa restrição é chamada de *política de mesma origem*.</span><span class="sxs-lookup"><span data-stu-id="869d2-181">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="869d2-182">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="869d2-182">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="869d2-183">Para fazer solicitações do navegador para um ponto de extremidade com uma origem diferente, o *ponto de extremidade* deve habilitar o [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="869d2-183">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="869d2-184">O [ Blazor aplicativo de exemplo Webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstra o uso do CORS no componente chamar API da Web (*pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="869d2-184">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="869d2-185">Para permitir que outros sites façam solicitações de compartilhamento de recursos entre origens (CORS) para seu aplicativo, consulte <xref:security/cors> .</span><span class="sxs-lookup"><span data-stu-id="869d2-185">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="869d2-186">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="869d2-186">Additional resources</span></span>

* <span data-ttu-id="869d2-187"><xref:security/blazor/webassembly/additional-scenarios>&ndash;Inclui cobertura no uso `HttpClient` para fazer solicitações de API da Web seguras.</span><span class="sxs-lookup"><span data-stu-id="869d2-187"><xref:security/blazor/webassembly/additional-scenarios> &ndash; Includes coverage on using `HttpClient` to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="869d2-188">Configuração de ponto de extremidade HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="869d2-188">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="869d2-189">CORS (compartilhamento de recursos entre origens) no W3C</span><span class="sxs-lookup"><span data-stu-id="869d2-189">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)

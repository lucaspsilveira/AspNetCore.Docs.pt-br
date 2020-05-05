---
title: Chamar uma API Web de ASP.NET Core Blazor Webassembly
author: guardrex
description: Saiba como chamar uma API da Web de um Blazor aplicativo Webassembly usando auxiliares JSON, incluindo a criação de solicitações de CORS (compartilhamento de recursos entre origens).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: d823db3688e05f6befefacc9f390e0dcdbf329a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767142"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="5a60e-103">Chamar uma API da Web de ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="5a60e-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="5a60e-104">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="5a60e-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="5a60e-105">Aplicativos Webassembly chamam APIs da Web usando um `HttpClient` serviço pré-configurado. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="5a60e-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="5a60e-106">Redação de solicitações, que podem incluir opções de [API](https://developer.mozilla.org/docs/Web/API/Fetch_API) de busca Blazor de JavaScript, usando auxiliares JSON ou com <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="5a60e-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="5a60e-107">O `HttpClient` serviço em Blazor aplicativos Webassembly é voltado para fazer solicitações de volta ao servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="5a60e-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="5a60e-108">As diretrizes neste tópico pertencem apenas aos Blazor aplicativos Webassembly.</span><span class="sxs-lookup"><span data-stu-id="5a60e-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="5a60e-109">Aplicativos de servidor chamam APIs da <xref:System.Net.Http.HttpClient> Web usando instâncias, normalmente <xref:System.Net.Http.IHttpClientFactory>criadas usando. [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="5a60e-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="5a60e-110">As diretrizes neste tópico não pertencem a aplicativos Blazor de servidor.</span><span class="sxs-lookup"><span data-stu-id="5a60e-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="5a60e-111">Ao desenvolver Blazor aplicativos de servidor, siga as orientações <xref:fundamentals/http-requests>em.</span><span class="sxs-lookup"><span data-stu-id="5a60e-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="5a60e-112">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample)) &ndash; selecione o aplicativo *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="5a60e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="5a60e-113">Consulte os seguintes componentes no aplicativo de exemplo *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="5a60e-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="5a60e-114">Chamar a API Web (*pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="5a60e-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="5a60e-115">Testador de solicitação HTTP (*componentes/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="5a60e-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="5a60e-116">Pacotes</span><span class="sxs-lookup"><span data-stu-id="5a60e-116">Packages</span></span>

<span data-ttu-id="5a60e-117">Referencie o pacote NuGet [System .net. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="5a60e-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="5a60e-118">Adicionar o serviço HttpClient</span><span class="sxs-lookup"><span data-stu-id="5a60e-118">Add the HttpClient service</span></span>

<span data-ttu-id="5a60e-119">No `Program.Main`, adicione um `HttpClient` serviço se ele ainda não existir:</span><span class="sxs-lookup"><span data-stu-id="5a60e-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="5a60e-120">Auxiliares HttpClient e JSON</span><span class="sxs-lookup"><span data-stu-id="5a60e-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="5a60e-121">Em um Blazor aplicativo Webassembly, o [HttpClient](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta ao servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="5a60e-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="5a60e-122">Um Blazor aplicativo de servidor não inclui `HttpClient` um serviço por padrão.</span><span class="sxs-lookup"><span data-stu-id="5a60e-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="5a60e-123">Forneça um `HttpClient` para o aplicativo usando a [infraestrutura de fábrica do HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="5a60e-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="5a60e-124">`HttpClient`e auxiliares JSON também são usados para chamar pontos de extremidade de API Web de terceiros.</span><span class="sxs-lookup"><span data-stu-id="5a60e-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="5a60e-125">`HttpClient`é implementado usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de navegador e está sujeito a suas limitações, incluindo a imposição da mesma política de origem.</span><span class="sxs-lookup"><span data-stu-id="5a60e-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="5a60e-126">O endereço base do cliente é definido como o endereço do servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="5a60e-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="5a60e-127">Injetar `HttpClient` uma instância usando `@inject` a diretiva:</span><span class="sxs-lookup"><span data-stu-id="5a60e-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="5a60e-128">Nos exemplos a seguir, uma API Web todo processa as operações CRUD (criar, ler, atualizar e excluir).</span><span class="sxs-lookup"><span data-stu-id="5a60e-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="5a60e-129">Os exemplos são baseados em uma `TodoItem` classe que armazena:</span><span class="sxs-lookup"><span data-stu-id="5a60e-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="5a60e-130">ID (`Id`, `long`) &ndash; ID exclusiva do item.</span><span class="sxs-lookup"><span data-stu-id="5a60e-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="5a60e-131">Nome (`Name`, `string`) &ndash; nome do item.</span><span class="sxs-lookup"><span data-stu-id="5a60e-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="5a60e-132">Indicação de`IsComplete`status `bool`( &ndash; ,) se o item de tarefas pendentes for concluído.</span><span class="sxs-lookup"><span data-stu-id="5a60e-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="5a60e-133">Os métodos auxiliares JSON enviam solicitações para um URI (uma API Web nos exemplos a seguir) e processam a resposta:</span><span class="sxs-lookup"><span data-stu-id="5a60e-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="5a60e-134">`GetFromJsonAsync`&ndash; Envia uma solicitação HTTP Get e analisa o corpo da resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="5a60e-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="5a60e-135">No código a seguir, os `_todoItems` são exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="5a60e-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="5a60e-136">O `GetTodoItems` método é disparado quando o processamento do componente é concluído ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="5a60e-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="5a60e-137">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="5a60e-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="5a60e-138">`PostAsJsonAsync`&ndash; Envia uma solicitação HTTP post, incluindo conteúdo codificado em JSON, e analisa o corpo da resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="5a60e-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="5a60e-139">No código a seguir, `_newItemName` é fornecido por um elemento associado do componente.</span><span class="sxs-lookup"><span data-stu-id="5a60e-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="5a60e-140">O `AddItem` método é disparado selecionando um `<button>` elemento.</span><span class="sxs-lookup"><span data-stu-id="5a60e-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="5a60e-141">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="5a60e-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="5a60e-142">Chamadas para `PostAsJsonAsync` retornar um <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="5a60e-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="5a60e-143">Para desserializar o conteúdo JSON da mensagem de resposta, use o `ReadFromJsonAsync<T>` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="5a60e-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="5a60e-144">`PutAsJsonAsync`&ndash; Envia uma solicitação HTTP PUT, incluindo conteúdo codificado em JSON.</span><span class="sxs-lookup"><span data-stu-id="5a60e-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="5a60e-145">No código a seguir, `_editItem` os valores `Name` para `IsCompleted` e são fornecidos pelos elementos associados do componente.</span><span class="sxs-lookup"><span data-stu-id="5a60e-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="5a60e-146">O item `Id` é definido quando o item é selecionado em outra parte da interface do usuário e `EditItem` é chamado.</span><span class="sxs-lookup"><span data-stu-id="5a60e-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="5a60e-147">O `SaveItem` método é disparado selecionando `<button>` o elemento Save.</span><span class="sxs-lookup"><span data-stu-id="5a60e-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="5a60e-148">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="5a60e-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="5a60e-149">Chamadas para `PutAsJsonAsync` retornar um <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="5a60e-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="5a60e-150">Para desserializar o conteúdo JSON da mensagem de resposta, use o `ReadFromJsonAsync<T>` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="5a60e-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="5a60e-151"><xref:System.Net.Http>inclui métodos de extensão adicionais para enviar solicitações HTTP e receber respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="5a60e-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="5a60e-152">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) é usado para enviar uma solicitação HTTP Delete para uma API da Web.</span><span class="sxs-lookup"><span data-stu-id="5a60e-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="5a60e-153">No código a seguir, o elemento `<button>` Delete chama o `DeleteItem` método.</span><span class="sxs-lookup"><span data-stu-id="5a60e-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="5a60e-154">O elemento `<input>` associado fornece o `id` do item a ser excluído.</span><span class="sxs-lookup"><span data-stu-id="5a60e-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="5a60e-155">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="5a60e-155">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="5a60e-156">CORS (compartilhamento de recursos entre origens)</span><span class="sxs-lookup"><span data-stu-id="5a60e-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="5a60e-157">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que atuou na página da Web.</span><span class="sxs-lookup"><span data-stu-id="5a60e-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="5a60e-158">Essa restrição é chamada de *política de mesma origem*.</span><span class="sxs-lookup"><span data-stu-id="5a60e-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="5a60e-159">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="5a60e-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="5a60e-160">Para fazer solicitações do navegador para um ponto de extremidade com uma origem diferente, o *ponto de extremidade* deve habilitar o [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="5a60e-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="5a60e-161">O [ Blazor aplicativo de exemplo Webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstra o uso do CORS no componente chamar API da Web (*pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="5a60e-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="5a60e-162">Para permitir que outros sites façam solicitações de compartilhamento de recursos entre origens (CORS) para seu aplicativo, <xref:security/cors>consulte.</span><span class="sxs-lookup"><span data-stu-id="5a60e-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5a60e-163">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5a60e-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="5a60e-164">Configuração de ponto de extremidade HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="5a60e-164">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="5a60e-165">CORS (compartilhamento de recursos entre origens) no W3C</span><span class="sxs-lookup"><span data-stu-id="5a60e-165">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)

---
title: Chame uma API web Blazor do ASP.NET Core WebAssembly
author: guardrex
description: Saiba como chamar uma API Blazor web a partir de um aplicativo WebAssembly usando ajudantes JSON, incluindo fazer solicitações de compartilhamento de recursos de origem cruzada (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 943f9d440adbe11ac1977f28aebee53a5510a86b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661576"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="16061-103">Ligue para uma API web da ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="16061-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="16061-104">Por [Luke Latham](https://github.com/guardrex), Daniel [Roth](https://github.com/danroth27), e Juan De [la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="16061-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="16061-105">Os aplicativos WebAssembly chamam APIs `HttpClient` da Web usando um serviço pré-configurado. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="16061-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="16061-106">Componha as solicitações, que podem incluir opções [de API](https://developer.mozilla.org/docs/Web/API/Fetch_API) javaScript Fetch, usando Blazor ajudantes JSON ou com <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="16061-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="16061-107">O `HttpClient` serviço Blazor em aplicativos WebAssembly é focado em fazer solicitações de volta ao servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="16061-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="16061-108">A orientação neste tópico Blazor diz respeito apenas aos aplicativos WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="16061-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="16061-109">Os aplicativos do servidor chamam APIs da <xref:System.Net.Http.IHttpClientFactory>Web usando <xref:System.Net.Http.HttpClient> instâncias, normalmente criadas usando . [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="16061-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="16061-110">A orientação neste tópico não Blazor diz respeito aos aplicativos do Server.</span><span class="sxs-lookup"><span data-stu-id="16061-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="16061-111">Ao Blazor desenvolver aplicativos server, siga <xref:fundamentals/http-requests>as orientações em .</span><span class="sxs-lookup"><span data-stu-id="16061-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="16061-112">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) [(como baixar)](xref:index#how-to-download-a-sample) &ndash; Selecione o aplicativo *BlazorWebAssemblySample.*</span><span class="sxs-lookup"><span data-stu-id="16061-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="16061-113">Veja os seguintes componentes no aplicativo de amostra *BlazorWebAssemblySample:*</span><span class="sxs-lookup"><span data-stu-id="16061-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="16061-114">Chamada Web API *(Páginas/CallWebAPI.razor)*</span><span class="sxs-lookup"><span data-stu-id="16061-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="16061-115">TESTADOR de solicitação HTTP (*Componentes/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="16061-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="16061-116">Pacotes</span><span class="sxs-lookup"><span data-stu-id="16061-116">Packages</span></span>

<span data-ttu-id="16061-117">Faça referência ao pacote [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="16061-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="16061-118">Adicione o serviço HttpClient</span><span class="sxs-lookup"><span data-stu-id="16061-118">Add the HttpClient service</span></span>

<span data-ttu-id="16061-119">Em `Program.Main`, `HttpClient` adicionar um serviço se ele ainda não existir:</span><span class="sxs-lookup"><span data-stu-id="16061-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="16061-120">Ajudantes httpclient e JSON</span><span class="sxs-lookup"><span data-stu-id="16061-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="16061-121">Em Blazor um aplicativo WebAssembly, [httpClient](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta para o servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="16061-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="16061-122">Um Blazor aplicativo server não `HttpClient` inclui um serviço por padrão.</span><span class="sxs-lookup"><span data-stu-id="16061-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="16061-123">Forneça `HttpClient` um aplicativo usando a [infra-estrutura de fábrica HttpClient.](xref:fundamentals/http-requests)</span><span class="sxs-lookup"><span data-stu-id="16061-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="16061-124">`HttpClient`e os ajudantes JSON também são usados para chamar de pontos finais de API da Web de terceiros.</span><span class="sxs-lookup"><span data-stu-id="16061-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="16061-125">`HttpClient`é implementado usando a [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) do navegador e está sujeito às suas limitações, incluindo a aplicação da mesma política de origem.</span><span class="sxs-lookup"><span data-stu-id="16061-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="16061-126">O endereço base do cliente está definido como endereço do servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="16061-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="16061-127">Injete `HttpClient` uma `@inject` instância usando a diretiva:</span><span class="sxs-lookup"><span data-stu-id="16061-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="16061-128">Nos exemplos a seguir, uma API toda web processa as operações de criação, leitura, atualização e exclusão (CRUD).</span><span class="sxs-lookup"><span data-stu-id="16061-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="16061-129">Os exemplos são baseados em uma `TodoItem` classe que armazena o:</span><span class="sxs-lookup"><span data-stu-id="16061-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="16061-130">ID`Id`( `long` &ndash; , ) ID único do item.</span><span class="sxs-lookup"><span data-stu-id="16061-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="16061-131">Nome`Name`( `string` &ndash; ) Nome do item.</span><span class="sxs-lookup"><span data-stu-id="16061-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="16061-132">Status`IsComplete`( `bool` &ndash; ) Indicação se o item Todo estiver concluído.</span><span class="sxs-lookup"><span data-stu-id="16061-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="16061-133">Os métodos de ajuda json enviam solicitações para um URI (uma API web nos seguintes exemplos) e processam a resposta:</span><span class="sxs-lookup"><span data-stu-id="16061-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="16061-134">`GetFromJsonAsync`&ndash; Envia uma solicitação HTTP GET e analisa o corpo de resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="16061-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="16061-135">No código a `_todoItems` seguir, os são exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="16061-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="16061-136">O `GetTodoItems` método é acionado quando o componente é concluído renderização[(OnInitializedAsync).](xref:blazor/lifecycle#component-initialization-methods)</span><span class="sxs-lookup"><span data-stu-id="16061-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="16061-137">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="16061-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="16061-138">`PostAsJsonAsync`&ndash; Envia uma solicitação HTTP POST, incluindo conteúdo codificado pelo JSON, e analisa o corpo de resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="16061-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="16061-139">No código a `_newItemName` seguir, é fornecido por um elemento vinculado do componente.</span><span class="sxs-lookup"><span data-stu-id="16061-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="16061-140">O `AddItem` método é acionado selecionando um `<button>` elemento.</span><span class="sxs-lookup"><span data-stu-id="16061-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="16061-141">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="16061-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="16061-142">Chamadas `PostAsJsonAsync` para <xref:System.Net.Http.HttpResponseMessage>retornar um .</span><span class="sxs-lookup"><span data-stu-id="16061-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="16061-143">Para desserializar o conteúdo JSON da `ReadFromJsonAsync<T>` mensagem de resposta, use o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="16061-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="16061-144">`PutAsJsonAsync`&ndash; Envia uma solicitação HTTP PUT, incluindo conteúdo codificado pelo JSON.</span><span class="sxs-lookup"><span data-stu-id="16061-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="16061-145">No código a `_editItem` seguir, `IsCompleted` os valores são `Name` fornecidos por elementos vinculados do componente.</span><span class="sxs-lookup"><span data-stu-id="16061-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="16061-146">O item `Id` é definido quando o item é selecionado em `EditItem` outra parte da ui e é chamado.</span><span class="sxs-lookup"><span data-stu-id="16061-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="16061-147">O `SaveItem` método é acionado selecionando o elemento Salvar. `<button>`</span><span class="sxs-lookup"><span data-stu-id="16061-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="16061-148">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="16061-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="16061-149">Chamadas `PutAsJsonAsync` para <xref:System.Net.Http.HttpResponseMessage>retornar um .</span><span class="sxs-lookup"><span data-stu-id="16061-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="16061-150">Para desserializar o conteúdo JSON da `ReadFromJsonAsync<T>` mensagem de resposta, use o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="16061-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="16061-151"><xref:System.Net.Http>inclui métodos de extensão adicionais para enviar solicitações HTTP e receber respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="16061-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="16061-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) é usado para enviar uma solicitação HTTP DELETE para uma API web.</span><span class="sxs-lookup"><span data-stu-id="16061-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="16061-153">No código a seguir, o elemento Excluir `<button>` chama o `DeleteItem` método.</span><span class="sxs-lookup"><span data-stu-id="16061-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="16061-154">O `<input>` elemento vinculado `id` fornece o item a ser excluído.</span><span class="sxs-lookup"><span data-stu-id="16061-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="16061-155">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="16061-155">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="16061-156">Compartilhamento de recursos de origem cruzada (CORS)</span><span class="sxs-lookup"><span data-stu-id="16061-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="16061-157">A segurança do navegador impede que uma página da Web evideta solicitações para um domínio diferente daquele que serviu à página da Web.</span><span class="sxs-lookup"><span data-stu-id="16061-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="16061-158">Essa restrição é chamada *de política de mesma origem.*</span><span class="sxs-lookup"><span data-stu-id="16061-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="16061-159">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="16061-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="16061-160">Para fazer solicitações do navegador para um ponto final com uma origem diferente, o *ponto final* deve habilitar o compartilhamento de recursos de origem [cruzada (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="16061-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="16061-161">O [ Blazor aplicativo de amostra WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstra o uso do CORS no componente Call Web API *(Pages/CallWebAPI.razor*).</span><span class="sxs-lookup"><span data-stu-id="16061-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="16061-162">Para permitir que outros sites façam solicitações de compartilhamento de <xref:security/cors>recursos de origem cruzada (CORS) ao seu aplicativo, consulte .</span><span class="sxs-lookup"><span data-stu-id="16061-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="16061-163">HttpClient e HttpRequestMessage com opções de solicitação de API do Fetch</span><span class="sxs-lookup"><span data-stu-id="16061-163">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="16061-164">Ao executar no WebAssembly em um Blazor aplicativo WebAssembly, use [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> para personalizar solicitações.</span><span class="sxs-lookup"><span data-stu-id="16061-164">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="16061-165">Por exemplo, você pode especificar o uri de solicitação, o método HTTP e quaisquer cabeçalhos de solicitação desejados.</span><span class="sxs-lookup"><span data-stu-id="16061-165">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

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

<span data-ttu-id="16061-166">Para obter mais informações sobre as opções de API do Fetch, consulte [os docs da Web DoMN: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="16061-166">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="16061-167">Ao enviar credenciais (cookies/cabeçalhos de `Authorization` autorização) nas solicitações do CORS, o cabeçalho deve ser permitido pela política cors.</span><span class="sxs-lookup"><span data-stu-id="16061-167">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="16061-168">A seguinte política inclui configuração para:</span><span class="sxs-lookup"><span data-stu-id="16061-168">The following policy includes configuration for:</span></span>

* <span data-ttu-id="16061-169">Solicitar origens`http://localhost:5000` `https://localhost:5001`(, ).</span><span class="sxs-lookup"><span data-stu-id="16061-169">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="16061-170">Qualquer método (verbo).</span><span class="sxs-lookup"><span data-stu-id="16061-170">Any method (verb).</span></span>
* <span data-ttu-id="16061-171">`Content-Type`e `Authorization` cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="16061-171">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="16061-172">Para permitir um cabeçalho `x-custom-header`personalizado (por <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>exemplo), liste o cabeçalho ao ligar .</span><span class="sxs-lookup"><span data-stu-id="16061-172">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="16061-173">Credenciais definidas pelo código`credentials` JavaScript `include`do lado do cliente (propriedade definida para ).</span><span class="sxs-lookup"><span data-stu-id="16061-173">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="16061-174">Para obter mais <xref:security/cors> informações, consulte o componente HTTP Request Tester do aplicativo de amostra *(Components/HTTPRequestTester.razor).*</span><span class="sxs-lookup"><span data-stu-id="16061-174">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16061-175">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="16061-175">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="16061-176">Configuração do ponto final do Kestrel HTTPS</span><span class="sxs-lookup"><span data-stu-id="16061-176">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="16061-177">Cross Origin Resource Sharing (CORS) no W3C</span><span class="sxs-lookup"><span data-stu-id="16061-177">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)

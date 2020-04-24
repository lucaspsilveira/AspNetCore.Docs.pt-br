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
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Chamar uma API Web do ASP.NET Core mais

Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Aplicativos [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) chamam APIs da Web usando um `HttpClient` serviço pré-configurado. Redação de solicitações, que podem incluir opções de API de busca de JavaScript, usando auxiliares de <xref:System.Net.Http.HttpRequestMessage>JSON mais [populadores](https://developer.mozilla.org/docs/Web/API/Fetch_API) ou com o. O `HttpClient` serviço em aplicativos Webassembly mais podestas se concentra em fazer solicitações de volta ao servidor de origem. As diretrizes neste tópico referem-se apenas a aplicativos Webassembly mais podestas.

Aplicativos de [servidor mais incrivelmente](xref:blazor/hosting-models#blazor-server) chamam APIs da <xref:System.Net.Http.HttpClient> Web usando instâncias, normalmente <xref:System.Net.Http.IHttpClientFactory>criadas usando. As diretrizes neste tópico não pertencem a aplicativos de servidor mais incrivelmente. Ao desenvolver aplicativos de servidor mais incrivelmente, siga as orientações <xref:fundamentals/http-requests>em.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample)) &ndash; selecione o aplicativo *BlazorWebAssemblySample* .

Consulte os seguintes componentes no aplicativo de exemplo *BlazorWebAssemblySample* :

* Chamar a API Web (*pages/CallWebAPI. Razor*)
* Testador de solicitação HTTP (*componentes/HTTPRequestTester. Razor*)

## <a name="packages"></a>Pacotes

Referencie o pacote NuGet [System .net. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) no arquivo de projeto.

## <a name="add-the-httpclient-service"></a>Adicionar o serviço HttpClient

No `Program.Main`, adicione um `HttpClient` serviço se ele ainda não existir:

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>Auxiliares HttpClient e JSON

Em um aplicativo Webassembly mais novo, o [HttpClient](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta ao servidor de origem.

Um aplicativo de servidor mais incrivelmente não inclui `HttpClient` um serviço por padrão. Forneça um `HttpClient` para o aplicativo usando a [infraestrutura de fábrica do HttpClient](xref:fundamentals/http-requests).

`HttpClient`e auxiliares JSON também são usados para chamar pontos de extremidade de API Web de terceiros. `HttpClient`é implementado usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de navegador e está sujeito a suas limitações, incluindo a imposição da mesma política de origem.

O endereço base do cliente é definido como o endereço do servidor de origem. Injetar `HttpClient` uma instância usando `@inject` a diretiva:

```razor
@using System.Net.Http
@inject HttpClient Http
```

Nos exemplos a seguir, uma API Web todo processa as operações CRUD (criar, ler, atualizar e excluir). Os exemplos são baseados em uma `TodoItem` classe que armazena:

* ID (`Id`, `long`) &ndash; ID exclusiva do item.
* Nome (`Name`, `string`) &ndash; nome do item.
* Indicação de`IsComplete`status `bool`( &ndash; ,) se o item de tarefas pendentes for concluído.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Os métodos auxiliares JSON enviam solicitações para um URI (uma API Web nos exemplos a seguir) e processam a resposta:

* `GetFromJsonAsync`&ndash; Envia uma solicitação HTTP Get e analisa o corpo da resposta JSON para criar um objeto.

  No código a seguir, os `_todoItems` são exibidos pelo componente. O `GetTodoItems` método é disparado quando o processamento do componente é concluído ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Consulte o aplicativo de exemplo para obter um exemplo completo.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`&ndash; Envia uma solicitação HTTP post, incluindo conteúdo codificado em JSON, e analisa o corpo da resposta JSON para criar um objeto.

  No código a seguir, `_newItemName` é fornecido por um elemento associado do componente. O `AddItem` método é disparado selecionando um `<button>` elemento. Consulte o aplicativo de exemplo para obter um exemplo completo.

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
  
  Chamadas para `PostAsJsonAsync` retornar um <xref:System.Net.Http.HttpResponseMessage>. Para desserializar o conteúdo JSON da mensagem de resposta, use o `ReadFromJsonAsync<T>` método de extensão:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync`&ndash; Envia uma solicitação HTTP PUT, incluindo conteúdo codificado em JSON.

  No código a seguir, `_editItem` os valores `Name` para `IsCompleted` e são fornecidos pelos elementos associados do componente. O item `Id` é definido quando o item é selecionado em outra parte da interface do usuário e `EditItem` é chamado. O `SaveItem` método é disparado selecionando `<button>` o elemento Save. Consulte o aplicativo de exemplo para obter um exemplo completo.

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
  
  Chamadas para `PutAsJsonAsync` retornar um <xref:System.Net.Http.HttpResponseMessage>. Para desserializar o conteúdo JSON da mensagem de resposta, use o `ReadFromJsonAsync<T>` método de extensão:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>inclui métodos de extensão adicionais para enviar solicitações HTTP e receber respostas HTTP. [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) é usado para enviar uma solicitação HTTP Delete para uma API da Web.

No código a seguir, o elemento `<button>` Delete chama o `DeleteItem` método. O elemento `<input>` associado fornece o `id` do item a ser excluído. Consulte o aplicativo de exemplo para obter um exemplo completo.

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

## <a name="cross-origin-resource-sharing-cors"></a>CORS (compartilhamento de recursos entre origens)

A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que atuou na página da Web. Essa restrição é chamada de *política de mesma origem*. A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site. Para fazer solicitações do navegador para um ponto de extremidade com uma origem diferente, o *ponto de extremidade* deve habilitar o [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/).

O [aplicativo de exemplo Webassembly mais claro (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstra o uso do CORS no componente chamar API da Web (*pages/CallWebAPI. Razor*).

Para permitir que outros sites façam solicitações de compartilhamento de recursos entre origens (CORS) para seu aplicativo, <xref:security/cors>consulte.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient e HttpRequestMessage com opções de solicitação de API de busca

Ao executar em Webassembly em um aplicativo Webassembly mais incrivelmente, use [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> para personalizar solicitações. Por exemplo, você pode especificar o URI de solicitação, o método HTTP e todos os cabeçalhos de solicitação desejados.

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

A implementação do .NET Webassembly `HttpClient` de usa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). FETCH permite configurar várias [opções específicas de solicitação](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

As opções de solicitação de busca HTTP podem `HttpRequestMessage` ser configuradas com métodos de extensão mostrados na tabela a seguir.

| `HttpRequestMessage`método de extensão | Buscar propriedade de solicitação |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [fornecidas](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [armazenar](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [verifica](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Você pode definir opções adicionais usando o método de `SetBrowserRequestOption` extensão mais genérico.
 
A resposta HTTP normalmente é armazenada em buffer Blazor em um aplicativo Webassembly para habilitar o suporte para leituras de sincronização no conteúdo da resposta. Para habilitar o suporte para streaming de resposta, `SetBrowserResponseStreamingEnabled` use o método de extensão na solicitação.

Para incluir credenciais em uma solicitação entre origens, use o método `SetBrowserRequestCredentials` de extensão:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Para obter mais informações sobre as opções de API de busca, consulte [MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Ao enviar credenciais (cookies/cabeçalhos de autorização) em solicitações CORS, `Authorization` o cabeçalho deve ser permitido pela política CORS.

A política a seguir inclui a configuração para o:

* Origens da solicitação`http://localhost:5000`( `https://localhost:5001`,).
* Qualquer método (verbo).
* `Content-Type`cabeçalhos `Authorization` e. Para permitir um cabeçalho personalizado (por exemplo, `x-custom-header`), liste o cabeçalho ao <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>chamar.
* Credenciais definidas pelo código JavaScript do lado do cliente`credentials` (propriedade definida `include`como).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Para obter mais informações, <xref:security/cors> consulte e o componente testador de solicitação HTTP do aplicativo de exemplo (*Components/HTTPRequestTester. Razor*).

## <a name="additional-resources"></a>Recursos adicionais

* [Solicitar tokens de acesso adicionais](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Anexar tokens a solicitações de saída](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configuração de ponto de extremidade HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [CORS (compartilhamento de recursos entre origens) no W3C](https://www.w3.org/TR/cors/)

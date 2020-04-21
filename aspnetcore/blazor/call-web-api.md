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
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a>Ligue para uma API web da ASP.NET CoreBlazor

Por [Luke Latham](https://github.com/guardrex), Daniel [Roth](https://github.com/danroth27), e Juan De [la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Os aplicativos WebAssembly chamam APIs `HttpClient` da Web usando um serviço pré-configurado. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly) Componha as solicitações, que podem incluir opções [de API](https://developer.mozilla.org/docs/Web/API/Fetch_API) javaScript Fetch, usando Blazor ajudantes JSON ou com <xref:System.Net.Http.HttpRequestMessage>. O `HttpClient` serviço Blazor em aplicativos WebAssembly é focado em fazer solicitações de volta ao servidor de origem. A orientação neste tópico Blazor diz respeito apenas aos aplicativos WebAssembly.

Os aplicativos do servidor chamam APIs da <xref:System.Net.Http.IHttpClientFactory>Web usando <xref:System.Net.Http.HttpClient> instâncias, normalmente criadas usando . [ Blazor ](xref:blazor/hosting-models#blazor-server) A orientação neste tópico não Blazor diz respeito aos aplicativos do Server. Ao Blazor desenvolver aplicativos server, siga <xref:fundamentals/http-requests>as orientações em .

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) [(como baixar)](xref:index#how-to-download-a-sample) &ndash; Selecione o aplicativo *BlazorWebAssemblySample.*

Veja os seguintes componentes no aplicativo de amostra *BlazorWebAssemblySample:*

* Chamada Web API *(Páginas/CallWebAPI.razor)*
* TESTADOR de solicitação HTTP (*Componentes/HTTPRequestTester.razor*)

## <a name="packages"></a>Pacotes

Faça referência ao pacote [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet no arquivo do projeto.

## <a name="add-the-httpclient-service"></a>Adicione o serviço HttpClient

Em `Program.Main`, `HttpClient` adicionar um serviço se ele ainda não existir:

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>Ajudantes httpclient e JSON

Em Blazor um aplicativo WebAssembly, [httpClient](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta para o servidor de origem.

Um Blazor aplicativo server não `HttpClient` inclui um serviço por padrão. Forneça `HttpClient` um aplicativo usando a [infra-estrutura de fábrica HttpClient.](xref:fundamentals/http-requests)

`HttpClient`e os ajudantes JSON também são usados para chamar de pontos finais de API da Web de terceiros. `HttpClient`é implementado usando a [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) do navegador e está sujeito às suas limitações, incluindo a aplicação da mesma política de origem.

O endereço base do cliente está definido como endereço do servidor de origem. Injete `HttpClient` uma `@inject` instância usando a diretiva:

```razor
@using System.Net.Http
@inject HttpClient Http
```

Nos exemplos a seguir, uma API toda web processa as operações de criação, leitura, atualização e exclusão (CRUD). Os exemplos são baseados em uma `TodoItem` classe que armazena o:

* ID`Id`( `long` &ndash; , ) ID único do item.
* Nome`Name`( `string` &ndash; ) Nome do item.
* Status`IsComplete`( `bool` &ndash; ) Indicação se o item Todo estiver concluído.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Os métodos de ajuda json enviam solicitações para um URI (uma API web nos seguintes exemplos) e processam a resposta:

* `GetFromJsonAsync`&ndash; Envia uma solicitação HTTP GET e analisa o corpo de resposta JSON para criar um objeto.

  No código a `_todoItems` seguir, os são exibidos pelo componente. O `GetTodoItems` método é acionado quando o componente é concluído renderização[(OnInitializedAsync).](xref:blazor/lifecycle#component-initialization-methods) Consulte o aplicativo de exemplo para obter um exemplo completo.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`&ndash; Envia uma solicitação HTTP POST, incluindo conteúdo codificado pelo JSON, e analisa o corpo de resposta JSON para criar um objeto.

  No código a `_newItemName` seguir, é fornecido por um elemento vinculado do componente. O `AddItem` método é acionado selecionando um `<button>` elemento. Consulte o aplicativo de exemplo para obter um exemplo completo.

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
  
  Chamadas `PostAsJsonAsync` para <xref:System.Net.Http.HttpResponseMessage>retornar um . Para desserializar o conteúdo JSON da `ReadFromJsonAsync<T>` mensagem de resposta, use o método de extensão:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync`&ndash; Envia uma solicitação HTTP PUT, incluindo conteúdo codificado pelo JSON.

  No código a `_editItem` seguir, `IsCompleted` os valores são `Name` fornecidos por elementos vinculados do componente. O item `Id` é definido quando o item é selecionado em `EditItem` outra parte da ui e é chamado. O `SaveItem` método é acionado selecionando o elemento Salvar. `<button>` Consulte o aplicativo de exemplo para obter um exemplo completo.

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
  
  Chamadas `PutAsJsonAsync` para <xref:System.Net.Http.HttpResponseMessage>retornar um . Para desserializar o conteúdo JSON da `ReadFromJsonAsync<T>` mensagem de resposta, use o método de extensão:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>inclui métodos de extensão adicionais para enviar solicitações HTTP e receber respostas HTTP. [HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) é usado para enviar uma solicitação HTTP DELETE para uma API web.

No código a seguir, o elemento Excluir `<button>` chama o `DeleteItem` método. O `<input>` elemento vinculado `id` fornece o item a ser excluído. Consulte o aplicativo de exemplo para obter um exemplo completo.

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

## <a name="cross-origin-resource-sharing-cors"></a>Compartilhamento de recursos de origem cruzada (CORS)

A segurança do navegador impede que uma página da Web evideta solicitações para um domínio diferente daquele que serviu à página da Web. Essa restrição é chamada *de política de mesma origem.* A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site. Para fazer solicitações do navegador para um ponto final com uma origem diferente, o *ponto final* deve habilitar o compartilhamento de recursos de origem [cruzada (CORS)](https://www.w3.org/TR/cors/).

O [ Blazor aplicativo de amostra WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstra o uso do CORS no componente Call Web API *(Pages/CallWebAPI.razor*).

Para permitir que outros sites façam solicitações de compartilhamento de <xref:security/cors>recursos de origem cruzada (CORS) ao seu aplicativo, consulte .

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient e HttpRequestMessage com opções de solicitação de API do Fetch

Ao executar no WebAssembly em um Blazor aplicativo WebAssembly, use [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> para personalizar solicitações. Por exemplo, você pode especificar o uri de solicitação, o método HTTP e quaisquer cabeçalhos de solicitação desejados.

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

Para obter mais informações sobre as opções de API do Fetch, consulte [os docs da Web DoMN: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Ao enviar credenciais (cookies/cabeçalhos de `Authorization` autorização) nas solicitações do CORS, o cabeçalho deve ser permitido pela política cors.

A seguinte política inclui configuração para:

* Solicitar origens`http://localhost:5000` `https://localhost:5001`(, ).
* Qualquer método (verbo).
* `Content-Type`e `Authorization` cabeçalhos. Para permitir um cabeçalho `x-custom-header`personalizado (por <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>exemplo), liste o cabeçalho ao ligar .
* Credenciais definidas pelo código`credentials` JavaScript `include`do lado do cliente (propriedade definida para ).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Para obter mais <xref:security/cors> informações, consulte o componente HTTP Request Tester do aplicativo de amostra *(Components/HTTPRequestTester.razor).*

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configuração do ponto final do Kestrel HTTPS](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Cross Origin Resource Sharing (CORS) no W3C](https://www.w3.org/TR/cors/)

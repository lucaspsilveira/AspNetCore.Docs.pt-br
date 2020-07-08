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
ms.openlocfilehash: a2e320eb24d47de9e704c2a5355d28cf90bad0cd
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059936"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Chamar uma API da Web de ASP.NET CoreBlazor

Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)

> [!NOTE]
> Este tópico aplica-se a Blazor WebAssembly . [Blazor Server](xref:blazor/hosting-models#blazor-server)os aplicativos chamam APIs da Web usando <xref:System.Net.Http.HttpClient> instâncias, normalmente criadas usando <xref:System.Net.Http.IHttpClientFactory> . Para obter diretrizes que se aplicam ao Blazor Server , consulte <xref:fundamentals/http-requests> .

[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)os aplicativos chamam APIs Web usando um serviço pré-configurado <xref:System.Net.Http.HttpClient> . Redação de solicitações, que podem incluir opções de [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript, usando Blazor auxiliares JSON ou com <xref:System.Net.Http.HttpRequestMessage> . O <xref:System.Net.Http.HttpClient> serviço em Blazor WebAssembly aplicativos se concentra em fazer solicitações de volta para o servidor de origem. As diretrizes neste tópico referem-se apenas a Blazor WebAssembly aplicativos.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample)): selecione o `BlazorWebAssemblySample` aplicativo.

Consulte os seguintes componentes no `BlazorWebAssemblySample` aplicativo de exemplo:

* Chamar API Web ( `Pages/CallWebAPI.razor` )
* Testador de solicitação HTTP ( `Components/HTTPRequestTester.razor` )

## <a name="packages"></a>Pacotes

Referencie o [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json/) pacote NuGet no arquivo de projeto.

## <a name="add-the-httpclient-service"></a>Adicionar o serviço HttpClient

No `Program.Main` , adicione um <xref:System.Net.Http.HttpClient> serviço se ele ainda não existir:

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>Auxiliares HttpClient e JSON

Em um Blazor WebAssembly aplicativo, [`HttpClient`](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta ao servidor de origem.

Um Blazor Server aplicativo não inclui um <xref:System.Net.Http.HttpClient> serviço por padrão. Forneça um <xref:System.Net.Http.HttpClient> para o aplicativo usando a [ `HttpClient` infraestrutura de fábrica](xref:fundamentals/http-requests).

<xref:System.Net.Http.HttpClient>e auxiliares JSON também são usados para chamar pontos de extremidade de API Web de terceiros. <xref:System.Net.Http.HttpClient>é implementado usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de navegador e está sujeito a suas limitações, incluindo a imposição da mesma política de origem.

O endereço base do cliente é definido como o endereço do servidor de origem. Injetar uma <xref:System.Net.Http.HttpClient> instância usando a [`@inject`](xref:mvc/views/razor#inject) diretiva:

```razor
@using System.Net.Http
@inject HttpClient Http
```

Nos exemplos a seguir, uma API Web todo processa as operações CRUD (criar, ler, atualizar e excluir). Os exemplos são baseados em uma `TodoItem` classe que armazena:

* ID ( `Id` , `long` ): ID exclusiva do item.
* Nome ( `Name` , `string` ): o nome do item.
* Status ( `IsComplete` , `bool` ): indica se o item de tarefas pendentes foi concluído.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Os métodos auxiliares JSON enviam solicitações para um URI (uma API Web nos exemplos a seguir) e processam a resposta:

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Envia uma solicitação HTTP GET e analisa o corpo da resposta JSON para criar um objeto.

  No código a seguir, os `todoItems` são exibidos pelo componente. O `GetTodoItems` método é disparado quando a renderização do componente é concluída ( [`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) ). Consulte o aplicativo de exemplo para obter um exemplo completo.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Envia uma solicitação HTTP POST, incluindo conteúdo codificado em JSON, e analisa o corpo da resposta JSON para criar um objeto.

  No código a seguir, `newItemName` é fornecido por um elemento associado do componente. O `AddItem` método é disparado selecionando um `<button>` elemento. Consulte o aplicativo de exemplo para obter um exemplo completo.

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
  
  Chamadas para <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> retornar um <xref:System.Net.Http.HttpResponseMessage> . Para desserializar o conteúdo JSON da mensagem de resposta, use o `ReadFromJsonAsync<T>` método de extensão:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Envia uma solicitação HTTP PUT, incluindo conteúdo codificado em JSON.

  No código a seguir, `editItem` os valores para `Name` e `IsCompleted` são fornecidos pelos elementos associados do componente. O item `Id` é definido quando o item é selecionado em outra parte da interface do usuário e `EditItem` é chamado. O `SaveItem` método é disparado selecionando o `<button>` elemento Save. Consulte o aplicativo de exemplo para obter um exemplo completo.

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
  
  Chamadas para <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> retornar um <xref:System.Net.Http.HttpResponseMessage> . Para desserializar o conteúdo JSON da mensagem de resposta, use o <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> método de extensão:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>inclui métodos de extensão adicionais para enviar solicitações HTTP e receber respostas HTTP. <xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType>é usado para enviar uma solicitação HTTP DELETE para uma API da Web.

No código a seguir, o `<button>` elemento delete chama o `DeleteItem` método. O `<input>` elemento associado fornece o `id` do item a ser excluído. Consulte o aplicativo de exemplo para obter um exemplo completo.

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

## <a name="named-httpclient-with-ihttpclientfactory"></a>Chamado HttpClient com IHttpClientFactory

<xref:System.Net.Http.IHttpClientFactory>Há suporte para serviços e para a configuração de um nome <xref:System.Net.Http.HttpClient> .

Referencie o [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/) pacote NuGet no arquivo de projeto.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

`FetchData`componente ( `Pages/FetchData.razor` ):

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

## <a name="typed-httpclient"></a>HttpClient digitado

<xref:System.Net.Http.HttpClient>O tipo usa uma ou mais das instâncias do aplicativo <xref:System.Net.Http.HttpClient> , padrão ou nomeadas, para retornar dados de um ou mais pontos de extremidade da API Web.

`WeatherForecastClient.cs`:

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

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Os componentes injetam o tipo <xref:System.Net.Http.HttpClient> para chamar a API da Web.

`FetchData`componente ( `Pages/FetchData.razor` ):

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

## <a name="handle-errors"></a>Tratar erros

Quando ocorrem erros durante a interação com uma API da Web, eles podem ser manipulados pelo código do desenvolvedor. Por exemplo, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> espera uma resposta JSON da API do servidor com um `Content-Type` de `application/json` . Se a resposta não estiver no formato JSON, a validação de conteúdo lançará um <xref:System.NotSupportedException> .

No exemplo a seguir, o ponto de extremidade do URI para a solicitação de dados previsão do tempo está incorreto. O URI deve ser para `WeatherForecast` , mas aparece na chamada como `WeatherForcast` ("e" ausente).

A <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> chamada espera que o JSON seja retornado, mas o servidor retorna HTML para uma exceção sem tratamento no servidor com um `Content-Type` de `text/html` . A exceção sem tratamento ocorre no servidor porque o caminho não é encontrado e o middleware não pode servir a uma página ou exibição para a solicitação.

No <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> no cliente, <xref:System.NotSupportedException> é gerado quando o conteúdo da resposta é validado como não JSON. A exceção é capturada no `catch` bloco, em que a lógica personalizada pode registrar o erro ou apresentar uma mensagem de erro amigável ao usuário:

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
> O exemplo anterior é para fins de demonstração. Um aplicativo de servidor de API Web pode ser configurado para retornar JSON mesmo quando um ponto de extremidade não existe ou uma exceção sem tratamento no servidor ocorre.

Para obter mais informações, consulte <xref:blazor/fundamentals/handle-errors>.

## <a name="cross-origin-resource-sharing-cors"></a>CORS (compartilhamento de recursos entre origens)

A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que atuou na página da Web. Essa restrição é chamada de *política de mesma origem*. A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site. Para fazer solicitações do navegador para um ponto de extremidade com uma origem diferente, o *ponto de extremidade* deve habilitar o [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/).

O [ Blazor WebAssembly aplicativo de exemplo (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstra o uso do CORS no componente Call Web API ( `Pages/CallWebAPI.razor` ).

Para obter mais informações e exemplos de código de solicitações seguras, consulte <xref:blazor/security/webassembly/additional-scenarios> . Para obter mais informações sobre o CORS com solicitações seguras, consulte a [seção CORS](xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors) do artigo anterior.

Para obter mais informações, consulte <xref:security/cors>.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/security/webassembly/additional-scenarios>: Inclui cobertura no uso do <xref:System.Net.Http.HttpClient> para fazer solicitações de API da Web seguras.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configuração de ponto de extremidade HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [CORS (compartilhamento de recursos entre origens) no W3C](https://www.w3.org/TR/cors/)

---
title: Usar filtros de Hub no ASP.NET CoreSignalR
author: brecon
description: Saiba como usar filtros de Hub no ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hub-filters
ms.openlocfilehash: c7ba0fff8bca53e2d6d12add693ee391ffa789ca
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408559"
---
# <a name="use-hub-filters-in-aspnet-core-signalr"></a>Usar filtros de Hub no ASP.NET CoreSignalR

Filtros de Hub:

* Estão disponíveis no ASP.NET Core 5,0 ou posterior.
* Permitir que a lógica seja executada antes e depois dos métodos de Hub serem invocados pelos clientes.

Este artigo fornece diretrizes para escrever e usar filtros de Hub.

## <a name="configure-hub-filters"></a>Configurar filtros de Hub

Os filtros de Hub podem ser aplicados globalmente ou por tipo de Hub. A ordem na qual os filtros são adicionados é a ordem na qual os filtros são executados. Filtros de Hub globais são executados antes dos filtros de Hub locais.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

Um filtro de Hub pode ser adicionado de uma das seguintes maneiras:

* Adicionar um filtro por tipo concreto:

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    Isso será resolvido da injeção de dependência (DI) ou do tipo ativado.

* Adicionar um filtro por tipo de tempo de execução:

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    Isso será resolvido a partir de DI ou do tipo ativado.

* Adicionar um filtro por instância:

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    Essa instância será usada como um singleton. Todas as invocações de método de Hub usarão a mesma instância.

Os filtros de Hub são criados e descartados por invocação de Hub. Se você quiser armazenar o estado global no filtro ou nenhum estado, adicione o tipo de filtro hub a DI como um singleton para melhorar o desempenho. Como alternativa, adicione o filtro como uma instância, se possível.

## <a name="create-hub-filters"></a>Criar filtros de Hub

Crie um filtro declarando uma classe que herda de `IHubFilter` e adicione o `InvokeMethodAsync` método. Também há `OnConnectedAsync` e `OnDisconnectedAsync` isso pode, opcionalmente, ser implementado para encapsular os `OnConnectedAsync` métodos de Hub e, `OnDisconnectedAsync` respectivamente.

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

Os filtros são muito semelhantes ao middleware. O `next` método invoca o próximo filtro. O filtro final invocará o método de Hub. Os filtros também podem armazenar o resultado de esperar `next` e executar a lógica após o método de Hub ser chamado antes de retornar o resultado de `next` .

Para ignorar uma invocação de método de Hub em um filtro, lance uma exceção do tipo `HubException` em vez de chamar `next` . O cliente receberá um erro se ele estiver esperando um resultado.

## <a name="use-hub-filters"></a>Usar filtros de Hub

Ao escrever a lógica de filtro, tente torná-la genérica usando atributos em métodos de Hub em vez de verificar nomes de métodos de Hub.

Considere um filtro que verificará um argumento de método de Hub para frases banidas e substituirá as frases que encontrar `***` .
Para este exemplo, suponha que uma `LanguageFilterAttribute` classe seja definida. A classe tem uma propriedade chamada `FilterArgument` que pode ser definida ao usar o atributo.

1. Coloque o atributo no método de Hub que tem um argumento de cadeia de caracteres a ser limpo:

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. Defina um filtro de Hub para verificar o atributo e substituir as frases banidas em um argumento de método de Hub por `***` :

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "***");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. Registre o filtro de Hub no `Startup.ConfigureServices` método. Para evitar a reinicialização da lista de frases proibidas para cada invocação, o filtro de Hub é registrado como um singleton:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a>O objeto HubInvocationContext

O `HubInvocationContext` contém informações para a invocação do método de Hub atual.

| Propriedade | Descrição | Type |
| ------ | ------ | ----------- |
| `Context ` | O `HubCallerContext` contém informações sobre a conexão. | `HubCallerContext` |
| `Hub` | A instância do Hub que está sendo usada para esta invocação de método de Hub. | `Hub` |
| `HubMethodName` | O nome do método de Hub que está sendo invocado. | `string` |
| `HubMethodArguments` | A lista de argumentos que estão sendo passados para o método de Hub. | `IReadOnlyList<string>` |
| `ServiceProvider` | O provedor de serviço com escopo para esta invocação de método de Hub. | `IServiceProvider` |
| `HubMethod` | As informações do método de Hub. | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a>O objeto HubLifetimeContext

O `HubLifetimeContext` contém informações para os `OnConnectedAsync` `OnDisconnectedAsync` métodos de Hub e.

| Propriedade | Descrição | Type |
| ------ | ------ | ----------- |
| `Context ` | O `HubCallerContext` contém informações sobre a conexão. | `HubCallerContext` |
| `Hub` | A instância do Hub que está sendo usada para esta invocação de método de Hub. | `Hub` |
| `ServiceProvider` | O provedor de serviço com escopo para esta invocação de método de Hub. | `IServiceProvider` |

## <a name="authorization-and-filters"></a>Autorização e filtros

[Autorize os atributos nos métodos de Hub](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) executados antes dos filtros de Hub.

---
title: Aplicativos do Blazor Secure ASP.NET Core Server
author: guardrex
description: Saiba como mitigar ameaças Blazor de segurança aos aplicativos do Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626020"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Aplicativos Secure ASP.NET Core Blazor Server

Por [Javier Calvarro Nelson](https://github.com/javiercn)

Os aplicativos Blazor Server adotam um modelo de processamento de dados *imponente,* onde o servidor e o cliente mantêm um relacionamento duradouro. O estado persistente é mantido por um [circuito,](xref:blazor/state-management)que pode abranger conexões que também são potencialmente de longa duração.

Quando um usuário visita um site do Blazor Server, o servidor cria um circuito na memória do servidor. O circuito indica ao navegador qual conteúdo renderizar e responder a eventos, como quando o usuário seleciona um botão na interface do usuário. Para executar essas ações, um circuito invoca funções JavaScript no navegador do usuário e nos métodos .NET no servidor. Esta interação baseada em JavaScript bidirecional é referida como [JavaScript interop (Interop JS)](xref:blazor/call-javascript-from-dotnet).

Como o JS interop ocorre pela Internet e o cliente usa um navegador remoto, os aplicativos do Blazor Server compartilham a maioria das preocupações de segurança do aplicativo web. Este tópico descreve ameaças comuns aos aplicativos do Blazor Server e fornece orientação de mitigação de ameaças focada em aplicativos voltados para a Internet.

Em ambientes restritos, como dentro de redes corporativas ou intranets, algumas das orientações de mitigação também:

* Não se aplica no ambiente restrito.
* Não vale o custo para implementar porque o risco de segurança é baixo em um ambiente restrito.

## <a name="blazor-server-project-template"></a>Modelo de projeto do Blazor Server

O modelo de projeto Blazor Server pode ser configurado para autenticação quando o projeto for criado.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Siga a orientação <xref:blazor/get-started> do Visual Studio no artigo para criar um novo projeto Blazor Server com um mecanismo de autenticação.

Depois de escolher o modelo **Blazor Server App** na caixa de diálogo **Criar um novo aplicativo Web ASP.NET Core**, selecione **Alterar** em **Autenticação**.

Uma caixa de diálogo é aberta para oferecer o mesmo conjunto de mecanismos de autenticação para outros projetos ASP.NET Core:

* **Sem autenticação**
* **Contas de usuário individuais** &ndash; as contas de usuário podem ser armazenadas:
  * Dentro do aplicativo usando o sistema de [Identidade](xref:security/authentication/identity) do ASP.NET Core.
  * Com o [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Contas de trabalho ou escola**
* **Autenticação do Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Siga a orientação do <xref:blazor/get-started> Visual Studio Code no artigo para criar um novo projeto blazor server com um mecanismo de autenticação:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.

| Mecanismo de autenticação                                                                 | `{AUTHENTICATION}` valor |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Sem Autenticação                                                                        | `None`                   |
| Individual<br>Usuários armazenados no aplicativo com o ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Usuários armazenados no [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Contas corporativas ou de estudante<br>Autenticação organizacional para um único locatário.            | `SingleOrg`              |
| Contas corporativas ou de estudante<br>Autenticação organizacional para vários locatários.           | `MultiOrg`               |
| Autenticação do Windows                                                                   | `Windows`                |

O comando cria uma pasta com nome do valor fornecido para o espaço reservado `{APP NAME}` e usa o nome da pasta como o nome do aplicativo. Para obter mais informações, confira o [novo comando do dotnet](/dotnet/core/tools/dotnet-new) no Guia do .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Siga o Visual Studio para <xref:blazor/get-started> obter orientação do Mac no artigo.

1. Na **configuração da nova etapa do Aplicativo do Servidor Blazor,** selecione **Autenticação Individual (no aplicativo)** a partir da **queda de autenticação.**

1. O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Identidade Central.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Siga a orientação da <xref:blazor/get-started> .NET Core CLI no artigo para criar um novo projeto do Blazor Server com um mecanismo de autenticação:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.

| Mecanismo de autenticação                                                                 | `{AUTHENTICATION}` valor |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Sem Autenticação                                                                        | `None`                   |
| Individual<br>Usuários armazenados no aplicativo com o ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Usuários armazenados no [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Contas corporativas ou de estudante<br>Autenticação organizacional para um único locatário.            | `SingleOrg`              |
| Contas corporativas ou de estudante<br>Autenticação organizacional para vários locatários.           | `MultiOrg`               |
| Autenticação do Windows                                                                   | `Windows`                |

O comando cria uma pasta com nome do valor fornecido para o espaço reservado `{APP NAME}` e usa o nome da pasta como o nome do aplicativo. Para obter mais informações, confira o [novo comando do dotnet](/dotnet/core/tools/dotnet-new) no Guia do .NET Core.

---

## <a name="pass-tokens-to-a-blazor-server-app"></a>Passe tokens para um aplicativo Blazor Server

Auteire o aplicativo Blazor Server como você faria com um aplicativo regular razor pages ou MVC. Proprovisionar e salvar os tokens no cookie de autenticação. Por exemplo:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

Para obter o código `Startup.ConfigureServices` de exemplo, incluindo um exemplo completo, consulte os [tokens Passing para um aplicativo Blazor do lado](https://github.com/javiercn/blazor-server-aad-sample)do servidor .

Defina uma classe para passar no estado inicial do aplicativo com os tokens de acesso e atualização:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Defina um serviço de provedor de token **escopo** que pode ser usado dentro do aplicativo Blazor para resolver os tokens de DI:

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

In, `Startup.ConfigureServices`adicionar serviços para:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

No arquivo *_Host.cshtml,* crie `InitialApplicationState` e aprove e passe-o como parâmetro para o aplicativo:

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

No `App` componente *(App.razor),* resolva o serviço e inicialize-o com os dados do parâmetro:

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

No serviço que faz uma solicitação de API segura, injete o provedor de tokens e recupere o token para chamar a API:

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a>Esgotamento de recursos

O esgotamento dos recursos pode ocorrer quando um cliente interage com o servidor e faz com que o servidor consuma recursos excessivos. O consumo excessivo de recursos afeta principalmente:

* [Cpu](#cpu)
* [Memória](#memory)
* [Conexões de cliente](#client-connections)

Ataques de negação de serviço (DoS) geralmente buscam esgotar os recursos de um aplicativo ou servidor. No entanto, o esgotamento dos recursos não é necessariamente o resultado de um ataque ao sistema. Por exemplo, recursos finitos podem ser esgotados devido à alta demanda do usuário. O DoS é coberto ainda mais na seção [de ataques Negação de serviço (DoS).](#denial-of-service-dos-attacks)

Recursos externos à estrutura de Blazor, como bancos de dados e alças de arquivos (usados para ler e gravar arquivos), também podem sofrer esgotamento de recursos. Para obter mais informações, consulte <xref:performance/performance-best-practices>.

### <a name="cpu"></a>CPU

A exaustão da CPU pode ocorrer quando um ou mais clientes forçam o servidor a realizar um trabalho intensivo da CPU.

Por exemplo, considere um aplicativo Blazor Server que calcula um *número Fibonnacci*. Um número de Fibonnacci é produzido a partir de uma seqüência de Fibonnacci, onde cada número na seqüência é a soma dos dois números anteriores. A quantidade de trabalho necessária para alcançar a resposta depende do comprimento da seqüência e do tamanho do valor inicial. Se o aplicativo não colocar limites à solicitação de um cliente, os cálculos intensivos em CPU podem dominar o tempo da CPU e diminuir o desempenho de outras tarefas. O consumo excessivo de recursos é uma preocupação de segurança que afeta a disponibilidade.

A exaustão da CPU é uma preocupação para todos os aplicativos voltados para o público. Em aplicativos web regulares, solicitações e conexões são uma salvaguarda, mas os aplicativos do Blazor Server não fornecem as mesmas salvaguardas. Os aplicativos do Blazor Server devem incluir verificações e limites apropriados antes de realizar um trabalho potencialmente intensivo em CPU.

### <a name="memory"></a>Memória

A exaustão da memória pode ocorrer quando um ou mais clientes forçam o servidor a consumir uma grande quantidade de memória.

Por exemplo, considere um aplicativo lateral do servidor Blazor com um componente que aceita e exibe uma lista de itens. Se o aplicativo Blazor não colocar limites ao número de itens permitidos ou ao número de itens entregues ao cliente, o processamento e renderização com uso intensivo de memória podem dominar a memória do servidor até o ponto em que o desempenho do servidor sofre. O servidor pode travar ou diminuir até o ponto em que parece ter caído.

Considere o seguinte cenário para manter e exibir uma lista de itens que dizem respeito a um potencial cenário de exaustão de memória no servidor:

* Os itens em `List<MyItem>` uma propriedade ou campo usam a memória do servidor. Se o aplicativo permitir que a lista de itens cresça sem limites, há o risco de o servidor ficar sem memória. Ficar sem memória faz com que a sessão atual termine (falha) e todas as sessões simultâneas nessa instância do servidor recebam uma exceção fora da memória. Para evitar que esse cenário ocorra, o aplicativo deve usar uma estrutura de dados que imponha um limite de itens para usuários simultâneos.
* Se um esquema de paginação não for usado para renderização, o servidor usará memória adicional para objetos que não são visíveis na ia. Sem um limite no número de itens, as demandas de memória podem esgotar a memória disponível do servidor. Para evitar esse cenário, use uma das seguintes abordagens:
  * Use listas paginadas ao renderizar.
  * Apenas exiba os primeiros 100 a 1.000 itens e exija que o usuário insira critérios de pesquisa para encontrar itens além dos itens exibidos.
  * Para um cenário de renderização mais avançado, implemente listas ou grades que suportem a *virtualização*. Usando a virtualização, as listas só renderizam um subconjunto de itens atualmente visíveis para o usuário. Quando o usuário interage com a barra de rolagem na ui, o componente renderiza apenas os itens necessários para exibição. Os itens que atualmente não são necessários para exibição podem ser mantidos no armazenamento secundário, que é a abordagem ideal. Itens não exibidos também podem ser mantidos na memória, o que é menos ideal.

Os aplicativos Blazor Server oferecem um modelo de programação semelhante a outras estruturas de interface do usuário para aplicativos estatais, como WPF, Windows Forms ou Blazor WebAssembly. A principal diferença é que em vários dos frameworks de IA a memória consumida pelo aplicativo pertence ao cliente e afeta apenas esse cliente individual. Por exemplo, um aplicativo Blazor WebAssembly é executado inteiramente no cliente e só usa recursos de memória do cliente. No cenário do Blazor Server, a memória consumida pelo aplicativo pertence ao servidor e é compartilhada entre os clientes na instância do servidor.

As demandas de memória do lado do servidor são uma consideração para todos os aplicativos do Blazor Server. No entanto, a maioria dos aplicativos da Web são apátridas, e a memória usada durante o processamento de uma solicitação é liberada quando a resposta é retornada. Como uma recomendação geral, não permita que os clientes aloquem uma quantidade de memória não vinculada como em qualquer outro aplicativo do lado do servidor que persista nas conexões com o cliente. A memória consumida por um aplicativo Blazor Server persiste por mais tempo do que uma única solicitação.

> [!NOTE]
> Durante o desenvolvimento, um profiler pode ser usado ou um rastreamento capturado para avaliar as demandas de memória dos clientes. Um profiler ou rastreamento não capturará a memória alocada a um cliente específico. Para capturar o uso de memória de um cliente específico durante o desenvolvimento, capture um dump e examine a demanda de memória de todos os objetos enraizados no circuito de um usuário.

### <a name="client-connections"></a>Conexões de cliente

O esgotamento da conexão pode ocorrer quando um ou mais clientes abrem muitas conexões simultâneas ao servidor, impedindo que outros clientes estabeleçam novas conexões.

Os clientes Blazor estabelecem uma única conexão por sessão e mantêm a conexão aberta enquanto a janela do navegador estiver aberta. As exigências do servidor de manter todas as conexões não são específicas dos aplicativos Blazor. Dada a natureza persistente das conexões e a natureza imponente dos aplicativos Blazor Server, a exaustão da conexão é um risco maior para a disponibilidade do aplicativo.

Por padrão, não há limite no número de conexões por usuário para um aplicativo Blazor Server. Se o aplicativo precisar de um limite de conexão, faça uma ou mais das seguintes abordagens:

* Requerem autenticação, o que naturalmente limita a capacidade dos usuários não autorizados de se conectarem ao aplicativo. Para que esse cenário seja eficaz, os usuários devem ser impedidos de provisionar novos usuários à vontade.
* Limitar o número de conexões por usuário. A limitação de conexões pode ser realizada através das seguintes abordagens. Tenha cuidado para permitir que usuários legítimos acessem o aplicativo (por exemplo, quando um limite de conexão é estabelecido com base no endereço IP do cliente).
  * Ao nível do aplicativo:
    * Extensibilidade de roteamento de ponto final.
    * Exija autenticação para se conectar ao aplicativo e acompanhar as sessões ativas por usuário.
    * Rejeitar novas sessões ao atingir um limite.
    * As conexões do Proxy WebSocket a um aplicativo através do uso de um proxy, como o [Azure SignalR Service,](/azure/azure-signalr/signalr-overview) que multiplexa conexões de clientes para um aplicativo. Isso fornece um aplicativo com maior capacidade de conexão do que um único cliente pode estabelecer, impedindo que um cliente esgote as conexões com o servidor.
  * No nível do servidor: Use um proxy/gateway na frente do aplicativo. Por exemplo, [o Azure Front Door](/azure/frontdoor/front-door-overview) permite que você defina, gerencie e monitore o roteamento global do tráfego da Web para um aplicativo.

## <a name="denial-of-service-dos-attacks"></a>Ataques de negação de serviço (DoS)

Os ataques de Negação de Serviço (DoS) envolvem um cliente fazendo com que o servidor esgote um ou mais de seus recursos tornando o aplicativo indisponível. Os aplicativos do Blazor Server incluem alguns limites padrão e dependem de outros limites ASP.NET Core e SignalR para proteger contra ataques DoS:

| Limite do aplicativo Blazor Server                            | Descrição | Padrão |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | Número máximo de circuitos desconectados que um determinado servidor mantém na memória de cada vez. | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | A quantidade máxima de tempo que um circuito desconectado é mantido na memória antes de ser derrubado. | 3 minutos |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | A quantidade máxima de tempo que o servidor espera antes de cronometrar uma invocação de função JavaScript assíncrona. | 1 minuto |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | Número máximo de lotes de renderização não reconhecidos que o servidor mantém na memória por circuito em um dado momento para suportar uma reconexão robusta. Depois de atingir o limite, o servidor deixa de produzir novos lotes de renderização até que um ou mais lotes tenham sido reconhecidos pelo cliente. | 10 |


| Limite signalR e ASP.NET Core             | Descrição | Padrão |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | Tamanho da mensagem para uma mensagem individual. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Interações com o navegador (cliente)

Um cliente interage com o servidor através do js interop event dispatching and render completing. A comunicação interop JS vai para os dois lados entre JavaScript e .NET:

* Os eventos do navegador são enviados do cliente para o servidor de forma assíncrona.
* O servidor responde de forma assíncrona rerenderizando a ui conforme necessário.

### <a name="javascript-functions-invoked-from-net"></a>Funções JavaScript invocadas a partir de .NET

Para chamadas dos métodos .NET para JavaScript:

* Todas as invocações têm um tempo de intervalo configurável após o qual falham, retornando a <xref:System.OperationCanceledException> ao chamador.
  * Há um tempo padrão para as`CircuitOptions.JSInteropDefaultCallTimeout`chamadas de um minuto. Para configurar esse limite, consulte <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.
  * Um token de cancelamento pode ser fornecido para controlar o cancelamento por chamada. Confie no tempo limite de chamada padrão, sempre que possível e limite de tempo, qualquer chamada ao cliente se um token de cancelamento for fornecido.
* O resultado de uma chamada JavaScript não é confiável. O Blazor cliente do aplicativo em execução no navegador procura a função JavaScript para invocar. A função é invocada, e ou o resultado ou um erro é produzido. Um cliente mal-intencionado pode tentar:
  * Causar um problema no aplicativo retornando um erro da função JavaScript.
  * Induzir um comportamento não intencional no servidor retornando um resultado inesperado da função JavaScript.

Tome as seguintes precauções para se proteger dos cenários anteriores:

* Enrole as chamadas de interop js dentro de demonstrações [de try-catch](/dotnet/csharp/language-reference/keywords/try-catch) para explicar erros que podem ocorrer durante as invocações. Para obter mais informações, consulte <xref:blazor/handle-errors#javascript-interop>.
* Valide os dados retornados das invocações de interop JS, incluindo mensagens de erro, antes de tomar qualquer ação.

### <a name="net-methods-invoked-from-the-browser"></a>Métodos .NET invocados a partir do navegador

Não confie em chamadas de JavaScript para métodos .NET. Quando um método .NET for exposto ao JavaScript, considere como o método .NET é invocado:

* Trate qualquer método .NET exposto ao JavaScript como você seria um ponto final público para o aplicativo.
  * Validar a entrada.
    * Certifique-se de que os valores estão dentro dos intervalos esperados.
    * Certifique-se de que o usuário tem permissão para executar a ação solicitada.
  * Não aloque uma quantidade excessiva de recursos como parte da invocação do método .NET. Por exemplo, realize verificações e coloque limites no uso da CPU e da memória.
  * Leve em conta que métodos estáticos e de instância podem ser expostos a clientes JavaScript. Evite compartilhar o estado entre as sessões, a menos que o projeto exija o compartilhamento de estado com restrições apropriadas.
    * Por exemplo, métodos `DotNetReference` expostos através de objetos que são originalmente criados através de injeção de dependência (DI), os objetos devem ser registrados como objetos escopo. Isso se aplica a qualquer Blazor serviço DI que o aplicativo Server usa.
    * Para métodos estáticos, evite estabelecer um estado que não possa ser escopo para o cliente, a menos que o aplicativo esteja compartilhando explicitamente estado por design em todos os usuários em uma instância de servidor.
  * Evite passar dados fornecidos pelo usuário em parâmetros para chamadas JavaScript. Se a passagem de dados em parâmetros for absolutamente necessária, certifique-se de que o código JavaScript lida com a passagem dos dados sem introduzir vulnerabilidades [de scripting entre sites (XSS).](#cross-site-scripting-xss) Por exemplo, não escreva dados fornecidos pelo usuário no DoM `innerHTML` (Document Object Model, modelo de objeto de documento) definindo a propriedade de um elemento. Considere usar [a Política de Segurança de Conteúdo (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) para desativar `eval` e outros primitivos JavaScript inseguros.
* Evite implementar o envio personalizado de invocações .NET em cima da implementação de expedição do framework. Expor os métodos .NET ao navegador é um cenário Blazor avançado, não recomendado para o desenvolvimento geral.

### <a name="events"></a>Eventos

Os eventos fornecem um Blazor ponto de entrada para um aplicativo do Servidor. As mesmas regras para salvaguardar pontos finais em Blazor aplicativos web se aplicam ao tratamento de eventos em aplicativos do Servidor. Um cliente mal-intencionado pode enviar qualquer dado que desejar enviar como carga útil para um evento.

Por exemplo:

* Um evento de `<select>` alteração para um poderia enviar um valor que não está dentro das opções que o aplicativo apresentou ao cliente.
* Um `<input>` poderia enviar qualquer dado de texto para o servidor, ignorando a validação do lado do cliente.

O aplicativo deve validar os dados para qualquer evento que o aplicativo manuseie. Os Blazor [componentes de formulários-quadro](xref:blazor/forms-validation) realizam validações básicas. Se o aplicativo usar componentes de formulários personalizados, o código personalizado deve ser escrito para validar os dados do evento conforme apropriado.

BlazorOs eventos do servidor são assíncronos, então vários eventos podem ser enviados para o servidor antes que o aplicativo tenha tempo de reagir produzindo uma nova renderização. Isso tem algumas implicações de segurança a considerar. Limitar as ações do cliente no aplicativo deve ser realizado dentro dos manipuladores de eventos e não depender do estado de exibição renderizado atual.

Considere um componente de contador que deve permitir que um usuário incremente um contador no máximo três vezes. O botão para incrementar o contador é `count`condicionalmente baseado no valor de :

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Um cliente pode despachar um ou mais eventos de incremento antes que a estrutura produza uma nova renderização deste componente. O resultado é `count` que o pode ser incrementado *mais de três vezes* pelo usuário porque o botão não é removido pela ui rapidamente o suficiente. A maneira correta de atingir `count` o limite de três incrementos é mostrada no seguinte exemplo:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

Adicionando `if (count < 3) { ... }` a verificação dentro do `count` manipulador, a decisão de incrementar é baseada no estado atual do aplicativo. A decisão não se baseia no estado da UI como era no exemplo anterior, que pode estar temporariamente obsoleto.

### <a name="guard-against-multiple-dispatches"></a>Proteja-se contra vários despachos

Se um retorno de chamada de evento invocar uma operação de longa duração de forma assíncrona, como buscar dados de um serviço externo ou banco de dados, considere usar um guarda. O protetor pode impedir que o usuário enfileiravárias operações enquanto a operação está em andamento com feedback visual. O código do `isLoading` `true` componente `GetForecastAsync` a seguir define-se enquanto obtém dados do servidor. `isLoading` Enquanto `true`estiver, o botão está desativado na ui:

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

O padrão de proteção demonstrado no exemplo anterior funciona se a `async` - `await` operação de fundo for executada de forma assíncrona com o padrão.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Cancele cedo e evite usar após o descarte

Além de usar um guarda como descrito na seção [Desmembramento contra vários despachos,](#guard-against-multiple-dispatches) considere usar um <xref:System.Threading.CancellationToken> para cancelar operações de longa duração quando o componente for descartado. Esta abordagem tem o benefício adicional de evitar *o uso após o descarte* em componentes:

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Evite eventos que produzam grandes quantidades de dados

Alguns eventos do `oninput` DOM, como ou `onscroll`, podem produzir uma grande quantidade de dados. Evite usar esses Blazor eventos em aplicativos de servidor.

## <a name="additional-security-guidance"></a>Orientação adicional de segurança

As orientações para a Blazor garantia de ASP.NET os aplicativos Core se aplicam aos aplicativos do Server e são abordados nas seguintes seções:

* [Registro e dados confidenciais](#logging-and-sensitive-data)
* [Proteger informações em trânsito com HTTPS](#protect-information-in-transit-with-https)
* [Scripting entre sites (XSS)](#cross-site-scripting-xss))
* [Proteção de origem cruzada](#cross-origin-protection)
* [Click-jacking](#click-jacking)
* [Redirecionamentos abertos](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Registro e dados confidenciais

As interações de interop js entre o cliente e o <xref:Microsoft.Extensions.Logging.ILogger> servidor são registradas nos registros do servidor com instâncias. Blazorevita registrar informações confidenciais, como eventos reais ou entradas e saídas interop JS.

Quando ocorre um erro no servidor, o framework notifica o cliente e destrói a sessão. Por padrão, o cliente recebe uma mensagem de erro genérica que pode ser vista nas ferramentas de desenvolvedor do navegador.

O erro do lado do cliente não inclui o callstack e não fornece detalhes sobre a causa do erro, mas os registros do servidor contêm essas informações. Para fins de desenvolvimento, informações confidenciais de erro podem ser disponibilizadas ao cliente, permitindo erros detalhados.

Habilitar erros detalhados com:

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors`chave de configuração. Por exemplo, `ASPNETCORE_DETAILEDERRORS` defina a variável `true`de ambiente para um valor de .

> [!WARNING]
> Expor informações de erro aos clientes na Internet é um risco de segurança que deve ser sempre evitado.

### <a name="protect-information-in-transit-with-https"></a>Proteger informações em trânsito com HTTPS

BlazorO SignalR servidor usa para comunicação entre o cliente e o servidor. BlazorO servidor normalmente SignalR usa o transporte que negocia, que normalmente é websockets.

BlazorO servidor não garante a integridade e a confidencialidade dos dados enviados entre o servidor e o cliente. Use sempre HTTPS.

### <a name="cross-site-scripting-xss"></a>Scripting entre sites (XSS)

O scripting entre sites (XSS) permite que uma parte não autorizada execute lógica arbitrária no contexto do navegador. Um aplicativo comprometido pode potencialmente executar código arbitrário no cliente. A vulnerabilidade pode ser usada para potencialmente executar uma série de ações maliciosas contra o servidor:

* Despachar eventos falsos/inválidos para o servidor.
* Falha de despacho/conclusão de renderização inválida.
* Evite despachar as conclusões da renderização.
* Despachar chamadas interop de JavaScript para .NET.
* Modifique a resposta de chamadas interop de .NET para JavaScript.
* Evite enviar resultados de interop .NET para JS.

A Blazor estrutura do servidor toma medidas para proteger contra algumas das ameaças anteriores:

* Pare de produzir novas atualizações de iu se o cliente não estiver reconhecendo lotes de renderização. Configurado `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`com .
* Apagar qualquer chamada .NET para JavaScript após um minuto sem receber uma resposta do cliente. Configurado `CircuitOptions.JSInteropDefaultCallTimeout`com .
* Executa a validação básica em todas as entradas provenientes do navegador durante a interop JS:
  * As referências .NET são válidas e do tipo esperado pelo método .NET.
  * Os dados não são mal formados.
  * O número correto de argumentos para o método está presente na carga útil.
  * Os argumentos ou resultado podem ser desserializados corretamente antes de invocar o método.
* Executa a validação básica em todas as entradas provenientes do navegador a partir de eventos despachados:
  * O evento tem um tipo válido.
  * Os dados do evento podem ser desserializados.
  * Há um manipulador de eventos associado ao evento.

Além das garantias que a estrutura implementa, o aplicativo deve ser codificado pelo desenvolvedor para proteger contra ameaças e tomar as medidas apropriadas:

* Sempre valide dados ao lidar com eventos.
* Tome as medidas apropriadas ao receber dados inválidos:
  * Ignore os dados e retorne. Isso permite que o aplicativo continue processando solicitações.
  * Se o aplicativo determinar que a entrada é ilegítima e não pode ser produzida por cliente legítimo, lance uma exceção. Uma exceção derruba o circuito e termina a sessão.
* Não confie na mensagem de erro fornecida pelas conclusões do lote de renderização incluídas nos registros. O erro é fornecido pelo cliente e geralmente não pode ser confiável, pois o cliente pode estar comprometido.
* Não confie na entrada em chamadas interop JS em qualquer direção entre os métodos JavaScript e .NET.
* O app é responsável por validar que o conteúdo dos argumentos e resultados são válidos, mesmo que os argumentos ou resultados sejam desserializados corretamente.

Para que exista uma vulnerabilidade XSS, o aplicativo deve incorporar a entrada do usuário na página renderizada. BlazorOs componentes do servidor executam uma etapa de tempo de compilação onde a marcação em um arquivo *.razor* é transformada em lógica C# processual. Em tempo de execução, a lógica C# constrói uma árvore de *renderização* descrevendo os elementos, texto e componentes da criança. Isso é aplicado ao DOM do navegador através de uma seqüência de instruções JavaScript (ou é serializado em HTML no caso de pré-renderização):

* A entrada do usuário renderizada via sintaxe de navalha normal (por exemplo, `@someStringValue`) não expõe uma vulnerabilidade XSS porque a sintaxe Razor é adicionada ao DOM através de comandos que só podem escrever texto. Mesmo que o valor inclua marcação HTML, o valor é exibido como texto estático. Ao pré-renderizar, a saída é codificada por HTML, que também exibe o conteúdo como texto estático.
* As tags de script não são permitidas e não devem ser incluídas na árvore de renderização de componentes do aplicativo. Se uma tag de script estiver incluída na marcação de um componente, um erro de tempo de compilação será gerado.
* Os autores dos componentes podem escrever componentes em C# sem usar razor. O autor do componente é responsável por usar as APIs corretas ao emitir saída. Por exemplo, `builder.AddContent(0, someUserSuppliedString)` use e *não,* `builder.AddMarkupContent(0, someUserSuppliedString)`pois este último poderia criar uma vulnerabilidade XSS.

Como parte da proteção contra ataques XSS, considere implementar mitigações XSS, como [a Política de Segurança de Conteúdo (CSP).](https://developer.mozilla.org/docs/Web/HTTP/CSP)

Para obter mais informações, consulte <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Proteção de origem cruzada

Ataques de origem cruzada envolvem um cliente de origem diferente realizando uma ação contra o servidor. A ação maliciosa é tipicamente uma solicitação GET ou um formulário POST (Cross-Site Request Forgery, CSRF), mas abrir um WebSocket malicioso também é possível. BlazorOs aplicativos do servidor oferecem [as mesmas garantias que qualquer outro SignalR aplicativo que use o protocolo hub oferece:](xref:signalr/security)

* BlazorOs aplicativos do servidor podem ser acessados de origem cruzada, a menos que medidas adicionais sejam tomadas para impedi-lo. Para desativar o `DisableCorsAttribute` Blazor acesso de origem cruzada, desabilite o CORS no ponto final adicionando o middleware CORS ao pipeline e adicionando os metadados ao ponto final ou limite o conjunto de origens permitidas [configurando SignalR para compartilhamento de recursos de origem cruzada](xref:signalr/security#cross-origin-resource-sharing).
* Se o CORS estiver ativado, podem ser necessárias etapas extras para proteger o aplicativo, dependendo da configuração do CORS. Se o CORS estiver globalmente habilitado, Blazor o CORS `DisableCorsAttribute` poderá ser desativado para o `hub.MapBlazorHub()`hub do Servidor adicionando os metadados aos metadados do ponto final após a chamada .

Para obter mais informações, consulte <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Click-jacking

O click-jacking envolve renderizar um site como um `<iframe>` site de origem diferente, a fim de enganar o usuário para realizar ações no site sob ataque.

Para proteger um aplicativo de `<iframe>`renderização dentro de um [CSP (Content Security Policy, política](https://developer.mozilla.org/docs/Web/HTTP/CSP) de segurança de conteúdo) e o `X-Frame-Options` cabeçalho. Para obter mais informações, consulte [os docs da Web MDN: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Redirecionamentos abertos

Quando Blazor uma sessão de aplicativo do Servidor é iniciada, o servidor executa a validação básica das URLs enviadas como parte do início da sessão. A estrutura verifica se a URL base é um pai da URL atual antes de estabelecer o circuito. Não são realizadas verificações adicionais pelo quadro.

Quando um usuário seleciona um link no cliente, a URL do link é enviada para o servidor, que determina que ação tomar. Por exemplo, o aplicativo pode realizar uma navegação do lado do cliente ou indicar ao navegador para ir ao novo local.

Os componentes também podem desencadear solicitações `NavigationManager`de navegação de forma programática através do uso de . Em tais cenários, o aplicativo pode realizar uma navegação do lado do cliente ou indicar ao navegador para ir ao novo local.

Os componentes devem:

* Evite usar a entrada do usuário como parte dos argumentos de chamada de navegação.
* Valide argumentos para garantir que o destino seja permitido pelo aplicativo.

Caso contrário, um usuário mal-intencionado pode forçar o navegador a ir para um site controlado por invasores. Neste cenário, o invasor engana o aplicativo para usar alguma `NavigationManager.Navigate` entrada do usuário como parte da invocação do método.

Este conselho também se aplica ao renderizar links como parte do aplicativo:

* Se possível, use links relativos.
* Valide que destinos de link absolutos são válidos antes de incluí-los em uma página.

Para obter mais informações, consulte <xref:security/preventing-open-redirects>.

## <a name="authentication-and-authorization"></a>Autenticação e autorização

Para obter orientação sobre <xref:security/blazor/index>autenticação e autorização, consulte .

## <a name="security-checklist"></a>Lista de verificação de segurança

A seguinte lista de considerações de segurança não é abrangente:

* Validar argumentos de eventos.
* Validar entradas e resultados de chamadas interop JS.
* Evite usar (ou validar antecipadamente) a entrada do usuário para chamadas .NET para JS interop.
* Evite que o cliente aloque uma quantidade de memória não vinculada.
  * Dados dentro do componente.
  * `DotNetObject`referências devolvidas ao cliente.
* Proteja-se contra vários despachos.
* Cancele as operações de longa duração quando o componente estiver descartado.
* Evite eventos que produzam grandes quantidades de dados.
* Evite usar a entrada do `NavigationManager.Navigate` usuário como parte das chamadas e valide a entrada do usuário para URLs contra um conjunto de origens permitidas primeiro, se inevitável.
* Não tome decisões de autorização com base no estado da UI, mas apenas do estado componente.
* Considere usar [a Política de Segurança de Conteúdo (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) para proteger contra ataques XSS.
* Considere usar CSP e [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) para proteger contra o click-jacking.
* Certifique-se de que as configurações do CORS são Blazor apropriadas ao ativar o CORS ou desativar explicitamente o CORS para aplicativos.
* Teste para garantir que os limites Blazor do lado do servidor para o aplicativo forneçam uma experiência de usuário aceitável sem níveis inaceitáveis de risco.

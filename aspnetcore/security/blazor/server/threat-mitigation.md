---
Título: ' diretrizes de mitigação de ameaças para Blazor o ASP.NET Core Server ' autor: Descrição: ' saiba como mitigar ameaças de segurança para Blazor aplicativos de servidor. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---
# <a name="threat-mitigation-guidance-for-aspnet-core-blazor-server"></a>Diretrizes de mitigação de ameaças para o ASP.NET Core Blazor Server

Por [Javier Calvarro Nelson](https://github.com/javiercn)

BlazorOs aplicativos de servidor adotam um modelo de processamento de dados com *estado* , onde o servidor e o cliente mantêm uma relação de vida longa. O estado persistente é mantido por um [circuito](xref:blazor/state-management), que pode abranger conexões que também são potencialmente de longa duração.

Quando um usuário visita um Blazor site do servidor, o servidor cria um circuito na memória do servidor. O circuito indica ao navegador qual conteúdo renderizar e responde a eventos, como quando o usuário seleciona um botão na interface do usuário. Para executar essas ações, um circuito invoca funções JavaScript no navegador do usuário e nos métodos do .NET no servidor. Essa interação baseada em JavaScript bidirecional é conhecida como interoperabilidade [JavaScript (JS Interop)](xref:blazor/call-javascript-from-dotnet).

Como a interoperabilidade do JS ocorre pela Internet e o cliente usa um navegador remoto, os Blazor aplicativos de servidor compartilham a maioria das questões de segurança do aplicativo Web. Este tópico descreve ameaças comuns a Blazor aplicativos de servidor e fornece diretrizes de mitigação de ameaças voltadas para aplicativos voltados para a Internet.

Em ambientes restritos, como dentro de redes corporativas ou intranets, algumas das diretrizes de mitigação:

* Não se aplica ao ambiente restrito.
* Não vale o custo para implementar porque o risco de segurança está baixo em um ambiente restrito.

## <a name="blazor-and-shared-state"></a>Blazore estado compartilhado

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a>Esgotamento de recursos

O esgotamento de recursos pode ocorrer quando um cliente interage com o servidor e faz com que o servidor consuma recursos excessivos. O consumo excessivo de recursos afeta principalmente:

* [CPU](#cpu)
* [Memória](#memory)
* [Conexões de cliente](#client-connections)

Ataques de DoS (negação de serviço) geralmente buscam esgotar os recursos de um aplicativo ou servidor. No entanto, o esgotamento de recursos não é necessariamente o resultado de um ataque no sistema. Por exemplo, recursos finitos podem ser esgotados devido à alta demanda do usuário. O DoS é abordado mais detalhadamente na seção [ataques dos (negação de serviço)](#denial-of-service-dos-attacks) .

Recursos externos à Blazor estrutura, como bancos de dados e identificadores de arquivo (usados para ler e gravar arquivos), também podem ter esgotamento de recursos. Para obter mais informações, consulte <xref:performance/performance-best-practices>.

### <a name="cpu"></a>CPU

O esgotamento de CPU pode ocorrer quando um ou mais clientes forçam o servidor a executar um trabalho intensivo de CPU.

Por exemplo, considere um Blazor aplicativo de servidor que calcula um *número Fibonnacci*. Um número Fibonnacci é produzido de uma sequência Fibonnacci, em que cada número na sequência é a soma dos dois números anteriores. A quantidade de trabalho necessária para alcançar a resposta depende do comprimento da sequência e do tamanho do valor inicial. Se o aplicativo não coloca limites na solicitação de um cliente, os cálculos com uso intensivo de CPU podem dominar o tempo da CPU e diminuir o desempenho de outras tarefas. O consumo excessivo de recursos é uma preocupação de segurança que afeta a disponibilidade.

O esgotamento da CPU é uma preocupação para todos os aplicativos voltados ao público. Em aplicativos Web regulares, solicitações e conexões atingiram o tempo limite como uma proteção, mas Blazor os aplicativos de servidor não fornecem as mesmas proteções. BlazorAplicativos de servidor devem incluir verificações e limites apropriados antes de executar um trabalho potencialmente intensivo de CPU.

### <a name="memory"></a>Memória

O esgotamento de memória pode ocorrer quando um ou mais clientes forçam o servidor a consumir uma grande quantidade de memória.

Por exemplo, considere um Blazor aplicativo do lado do servidor com um componente que aceita e exibe uma lista de itens. Se o Blazor aplicativo não coloca limites no número de itens permitidos ou no número de itens processados de volta para o cliente, o processamento e a renderização com uso intensivo de memória podem dominar a memória do servidor para o ponto em que o desempenho do servidor sofre. O servidor pode falhar ou ficar lento até o ponto em que parece ter falhado.

Considere o cenário a seguir para manter e exibir uma lista de itens que pertencem a um possível cenário de esgotamento de memória no servidor:

* Os itens em uma `List<MyItem>` propriedade ou campo usam a memória do servidor. Se o aplicativo permitir que a lista de itens cresça sem limites, haverá um risco de que o servidor fique com memória insuficiente. A execução de memória insuficiente faz com que a sessão atual seja encerrada (falha) e todas as sessões simultâneas nessa instância do servidor recebam uma exceção de memória insuficiente. Para evitar que esse cenário ocorra, o aplicativo deve usar uma estrutura de dados que impõe um limite de item em usuários simultâneos.
* Se um esquema de paginação não for usado para renderização, o servidor usará memória adicional para objetos que não estão visíveis na interface do usuário. Sem um limite para o número de itens, as demandas de memória podem esgotar a memória disponível do servidor. Para evitar esse cenário, use uma das seguintes abordagens:
  * Use listas paginadas ao renderizar.
  * Exiba apenas os primeiros 100 a 1.000 itens e exija que o usuário insira critérios de pesquisa para localizar itens além dos itens exibidos.
  * Para um cenário de renderização mais avançado, implemente listas ou grades que dão suporte à *virtualização*. Usando a virtualização, as listas só renderizam um subconjunto de itens visíveis no momento para o usuário. Quando o usuário interage com a barra de rolagem na interface do usuário, o componente renderiza somente os itens necessários para exibição. Os itens que atualmente não são necessários para exibição podem ser mantidos no armazenamento secundário, que é a abordagem ideal. Itens não exibidos também podem ser mantidos na memória, o que é menos ideal.

BlazorOs aplicativos de servidor oferecem um modelo de programação semelhante a outras estruturas de interface do usuário para aplicativos com estado, como WPF, Windows Forms ou Blazor Webassembly. A principal diferença é que, em várias das estruturas da interface do usuário, a memória consumida pelo aplicativo pertence ao cliente e afeta apenas esse cliente individual. Por exemplo, um Blazor aplicativo Webassembly é executado inteiramente no cliente e usa apenas recursos de memória do cliente. No Blazor cenário do servidor, a memória consumida pelo aplicativo pertence ao servidor e é compartilhada entre clientes na instância do servidor.

As demandas de memória do lado do servidor são uma consideração para todos os Blazor aplicativos de servidor. No entanto, a maioria dos aplicativos Web não tem estado e a memória usada durante o processamento de uma solicitação é liberada quando a resposta é retornada. Como recomendação geral, não permita que os clientes aloquem uma quantidade não associada de memória como em qualquer outro aplicativo do lado do servidor que persista as conexões do cliente. A memória consumida por um Blazor aplicativo de servidor persiste por mais tempo do que uma única solicitação.

> [!NOTE]
> Durante o desenvolvimento, um criador de perfil pode ser usado ou um rastreamento capturado para avaliar as demandas de memória dos clientes. Um criador de perfil ou rastreamento não capturará a memória alocada para um cliente específico. Para capturar o uso de memória de um cliente específico durante o desenvolvimento, Capture um despejo e examine a demanda de memória de todos os objetos com raiz no circuito do usuário.

### <a name="client-connections"></a>Conexões de cliente

O esgotamento de conexão pode ocorrer quando um ou mais clientes abrem muitas conexões simultâneas com o servidor, impedindo que outros clientes estabeleçam novas conexões.

BlazorOs clientes estabelecem uma única conexão por sessão e mantêm a conexão aberta enquanto a janela do navegador está aberta. As demandas no servidor de manutenção de todas as conexões não são específicas para os Blazor aplicativos. Considerando a natureza persistente das conexões e a natureza com estado dos Blazor aplicativos de servidor, o esgotamento de conexão é um risco maior à disponibilidade do aplicativo.

Por padrão, não há limite para o número de conexões por usuário para um Blazor aplicativo de servidor. Se o aplicativo exigir um limite de conexão, execute uma ou mais das seguintes abordagens:

* Exigir autenticação, o que limita naturalmente a capacidade de usuários não autorizados se conectarem ao aplicativo. Para que esse cenário seja eficaz, os usuários devem ser impedidos de provisionar novos usuários no.
* Limite o número de conexões por usuário. A limitação de conexões pode ser realizada por meio das abordagens a seguir. Tome cuidado para permitir que usuários legítimos acessem o aplicativo (por exemplo, quando um limite de conexão é estabelecido com base no endereço IP do cliente).
  * No nível do aplicativo:
    * Extensibilidade de roteamento de ponto de extremidade.
    * Exigir autenticação para se conectar ao aplicativo e controlar as sessões ativas por usuário.
    * Rejeite novas sessões após atingir um limite.
    * Conexões de WebSocket de proxy para um aplicativo por meio do uso de um proxy, como o [ SignalR serviço do Azure](/azure/azure-signalr/signalr-overview) que multiplexa conexões de clientes para um aplicativo. Isso fornece um aplicativo com maior capacidade de conexão do que um único cliente pode estabelecer, impedindo que um cliente esgotasse as conexões com o servidor.
  * No nível do servidor: Use um proxy/gateway na frente do aplicativo. Por exemplo, a [porta frontal do Azure](/azure/frontdoor/front-door-overview) permite que você defina, gerencie e monitore o roteamento global do tráfego da Web para um aplicativo.

## <a name="denial-of-service-dos-attacks"></a>Ataques de negação de serviço (DoS)

Ataques de DoS (negação de serviço) envolvem um cliente que está fazendo com que o servidor esgotasse um ou mais de seus recursos, tornando o aplicativo indisponível. BlazorOs aplicativos de servidor incluem alguns limites padrão e contam com outros ASP.NET Core e SignalR limites para proteger contra ataques de dos que estão definidos no <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> .

| BlazorLimite de aplicativo do servidor | Descrição | Padrão |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | Número máximo de circuitos desconectados que um determinado servidor mantém na memória por vez. | 100 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | Quantidade máxima de tempo que um circuito desconectado é mantido na memória antes de ser interrompido. | 3 minutos |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | Quantidade máxima de tempo que o servidor espera antes de atingir o tempo limite de uma invocação de função JavaScript assíncrona. | 1 minuto |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | Número máximo de lotes de renderização não confirmados que o servidor mantém na memória por circuito em um determinado momento para dar suporte à reconexão robusta. Depois de atingir o limite, o servidor para de produzir novos lotes de renderização até que um ou mais lotes tenham sido confirmados pelo cliente. | 10 |

Defina o tamanho máximo da mensagem de uma única mensagem de Hub de entrada com <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> .

| SignalRe ASP.NET Core limite | Descrição | Padrão |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> | Tamanho da mensagem para uma mensagem individual. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Interações com o navegador (cliente)

Um cliente interage com o servidor por meio de expedição de evento de interoperabilidade do JS e conclusão do processamento. A comunicação de interoperabilidade do JS é de ambas as maneiras entre JavaScript e .NET:

* Os eventos de navegador são expedidos do cliente para o servidor de maneira assíncrona.
* O servidor responde de forma assíncrona reprocessando a interface do usuário conforme necessário.

### <a name="javascript-functions-invoked-from-net"></a>Funções JavaScript invocadas do .NET

Para chamadas de métodos .NET para JavaScript:

* Todas as invocações têm um tempo limite configurável após o qual falham, retornando um <xref:System.OperationCanceledException> para o chamador.
  * Há um tempo limite padrão para as chamadas ( <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> ) de um minuto. Para configurar esse limite, consulte <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls> .
  * Um token de cancelamento pode ser fornecido para controlar o cancelamento em uma base por chamada. Conte com o tempo limite de chamada padrão quando possível e Associação de tempo qualquer chamada ao cliente se um token de cancelamento for fornecido.
* O resultado de uma chamada JavaScript não pode ser confiável. O Blazor cliente de aplicativo em execução no navegador pesquisa a função JavaScript a ser invocada. A função é invocada e o resultado ou um erro é produzido. Um cliente mal-intencionado pode tentar:
  * Causar um problema no aplicativo retornando um erro da função JavaScript.
  * Induzir um comportamento indesejado no servidor retornando um resultado inesperado da função JavaScript.

Tome as seguintes precauções para proteger contra os cenários anteriores:

* Encapsular chamadas de interoperabilidade JS em instruções [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) para considerar erros que podem ocorrer durante as invocações. Para obter mais informações, consulte <xref:blazor/handle-errors#javascript-interop>.
* Valide os dados retornados das invocações de interoperabilidade do JS, incluindo mensagens de erro, antes de realizar qualquer ação.

### <a name="net-methods-invoked-from-the-browser"></a>Métodos .NET invocados no navegador

Não confie em chamadas de JavaScript para métodos .NET. Quando um método .NET é exposto ao JavaScript, considere como o método .NET é invocado:

* Trate qualquer método .NET exposto ao JavaScript como você faria com um ponto de extremidade público para o aplicativo.
  * Validar entrada.
    * Verifique se os valores estão dentro dos intervalos esperados.
    * Verifique se o usuário tem permissão para executar a ação solicitada.
  * Não aloque uma quantidade excessiva de recursos como parte da invocação do método .NET. Por exemplo, execute verificações e coloque limites no uso de CPU e memória.
  * Leve em consideração que os métodos estáticos e de instância podem ser expostos a clientes JavaScript. Evite compartilhar o estado entre as sessões, a menos que o design chame o estado de compartilhamento com as restrições apropriadas.
    * Para métodos de instância expostos por meio `DotNetReference` de objetos criados originalmente por injeção de dependência (di), os objetos devem ser registrados como objetos com escopo. Isso se aplica a qualquer serviço de DI que o Blazor aplicativo do servidor usa.
    * Para métodos estáticos, evite estabelecer o estado que não pode ser definido como escopo para o cliente, a menos que o aplicativo esteja explicitamente compartilhando o estado por design em todos os usuários em uma instância de servidor.
  * Evite passar dados fornecidos pelo usuário em parâmetros para chamadas JavaScript. Se a passagem de dados em parâmetros for absolutamente necessária, verifique se o código JavaScript lida com a passagem dos dados sem introduzir vulnerabilidades de [XSS (script entre sites)](#cross-site-scripting-xss) . Por exemplo, não grave dados fornecidos pelo usuário no Modelo de Objeto do Documento (DOM) definindo a `innerHTML` propriedade de um elemento. Considere o uso da [política de segurança de conteúdo (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) para desabilitar `eval` e outros primitivos JavaScript não seguros.
* Evite implementar a expedição personalizada de invocações do .NET sobre a implementação de expedição da estrutura. Expor métodos .NET ao navegador é um cenário avançado, não recomendado para Blazor desenvolvimento geral.

### <a name="events"></a>Eventos

Os eventos fornecem um ponto de entrada para um Blazor aplicativo de servidor. As mesmas regras para proteger pontos de extremidade em aplicativos Web se aplicam à manipulação de eventos em Blazor aplicativos de servidor. Um cliente mal-intencionado pode enviar todos os dados que deseja enviar como a carga de um evento.

Por exemplo:

* Um evento de alteração para um `<select>` pode enviar um valor que não está dentro das opções que o aplicativo apresentou ao cliente.
* Um `<input>` pode enviar qualquer dado de texto para o servidor, ignorando a validação do lado do cliente.

O aplicativo deve validar os dados para qualquer evento que o aplicativo manipular. Os Blazor [componentes de formulários](xref:blazor/forms-validation) de estrutura executam validações básicas. Se o aplicativo usar componentes de formulários personalizados, o código personalizado deverá ser escrito para validar os dados de evento conforme apropriado.

BlazorOs eventos de servidor são assíncronos, de modo que vários eventos podem ser expedidos para o servidor antes que o aplicativo tenha tempo de reagir, produzindo um novo processamento. Isso tem algumas implicações de segurança a serem consideradas. A limitação de ações do cliente no aplicativo deve ser executada dentro de manipuladores de eventos e não depende do estado atual da exibição renderizada.

Considere um componente de contador que deve permitir que um usuário aumente um contador no máximo três vezes. O botão para incrementar o contador é condicionalmente baseado no valor de `count` :

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

Um cliente pode enviar um ou mais eventos de incremento antes que a estrutura produza um novo processamento desse componente. O resultado é que o `count` pode ser incrementado *três vezes* pelo usuário, pois o botão não é removido pela interface de usuário com rapidez suficiente. A maneira correta de atingir o limite de três `count` incrementos é mostrada no exemplo a seguir:

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

Ao adicionar a `if (count < 3) { ... }` verificação dentro do manipulador, a decisão de incrementar `count` é baseada no estado atual do aplicativo. A decisão não se baseia no estado da interface do usuário como estava no exemplo anterior, que pode estar temporariamente obsoleta.

### <a name="guard-against-multiple-dispatches"></a>Proteção contra vários despachos

Se um retorno de chamada de evento invocar uma operação de execução longa de forma assíncrona, como a busca de dados de um serviço externo ou banco de dado, considere o uso de uma proteção. A proteção pode impedir que o usuário enfileirar várias operações enquanto a operação está em andamento com comentários visuais. O código de componente a seguir define `isLoading` `true` enquanto `GetForecastAsync` obtém dados do servidor. Enquanto `isLoading` estiver `true` , o botão será desabilitado na interface do usuário:

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

O padrão de proteção demonstrado no exemplo anterior funcionará se a operação em segundo plano for executada de forma assíncrona com o `async` - `await` padrão.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Cancelar antes e evitar usar-After-Dispose

Além de usar uma proteção conforme descrito na seção [proteção contra vários despachos](#guard-against-multiple-dispatches) , considere usar um <xref:System.Threading.CancellationToken> para cancelar operações de longa execução quando o componente for descartado. Essa abordagem tem o benefício adicional de evitar o *uso-After-Dispose* nos componentes:

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

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Evitar eventos que produzem grandes quantidades de dados

Alguns eventos DOM, como `oninput` ou `onscroll` , podem produzir uma grande quantidade de dados. Evite usar esses eventos em Blazor aplicativos de servidor.

## <a name="additional-security-guidance"></a>Diretrizes de segurança adicionais

As diretrizes para proteger aplicativos ASP.NET Core se aplicam a Blazor aplicativos de servidor e são abordadas nas seguintes seções:

* [Registro em log e dados confidenciais](#logging-and-sensitive-data)
* [Proteger informações em trânsito com HTTPS](#protect-information-in-transit-with-https)
* [Scripts entre sites (XSS)](#cross-site-scripting-xss)
* [Proteção entre origens](#cross-origin-protection)
* [Sensor de clique](#click-jacking)
* [Redirecionamentos abertos](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Registro em log e dados confidenciais

As interações de interoperabilidade do JS entre o cliente e o servidor são registradas nos logs do servidor com <xref:Microsoft.Extensions.Logging.ILogger> instâncias. Blazorevita informações confidenciais de log, como eventos reais ou entradas e saídas de interoperabilidade do JS.

Quando ocorre um erro no servidor, a estrutura notifica o cliente e destrói a sessão. Por padrão, o cliente recebe uma mensagem de erro genérica que pode ser vista nas ferramentas de desenvolvedor do navegador.

O erro do lado do cliente não inclui a pilha de chamadas e não fornece detalhes sobre a causa do erro, mas os logs do servidor contêm essas informações. Para fins de desenvolvimento, informações de erro confidenciais podem ser disponibilizadas para o cliente habilitando erros detalhados.

Habilitar erros detalhados em JavaScript com:

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.
* A `DetailedErrors` chave de configuração definida como `true` , que pode ser definida no arquivo de configurações do aplicativo (*appSettings. JSON*). A chave também pode ser definida usando a `ASPNETCORE_DETAILEDERRORS` variável de ambiente com um valor de `true` .

> [!WARNING]
> Expor informações de erro aos clientes na Internet é um risco de segurança que sempre deve ser evitado.

### <a name="protect-information-in-transit-with-https"></a>Proteger informações em trânsito com HTTPS

BlazorO servidor usa SignalR para a comunicação entre o cliente e o servidor. BlazorO servidor normalmente usa o transporte que SignalR negocia, que normalmente é WebSockets.

BlazorO servidor não garante a integridade e a confidencialidade dos dados enviados entre o servidor e o cliente. Sempre use HTTPS.

### <a name="cross-site-scripting-xss"></a>Scripts entre sites (XSS)

O XSS (script entre sites) permite que uma parte não autorizada execute lógica arbitrária no contexto do navegador. Um aplicativo comprometido poderia potencialmente executar código arbitrário no cliente. A vulnerabilidade pode ser usada para potencialmente executar várias ações mal-intencionadas no servidor:

* Despache eventos falsos/inválidos para o servidor.
* Falha de expedição/conclusões de renderização inválidas.
* Evite a expedição de conclusões de renderização.
* Despache chamadas de interoperabilidade do JavaScript para o .NET.
* Modifique a resposta das chamadas de interoperabilidade do .NET para o JavaScript.
* Evite distribuir resultados de interoperabilidade do .NET para JS.

A Blazor estrutura do servidor executa as etapas para proteger contra algumas das ameaças anteriores:

* Interromperá a produção de novas atualizações de interface do usuário se o cliente não estiver confirmando lotes de renderização. Configurado com <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType> .
* O tempo limite de qualquer chamada .NET para JavaScript após um minuto sem receber uma resposta do cliente. Configurado com <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> .
* Executa a validação básica em todas as entradas provenientes do navegador durante a interoperabilidade do JS:
  * As referências do .NET são válidas e do tipo esperado pelo método .NET.
  * Os dados não estão malformados.
  * O número correto de argumentos para o método está presente na carga.
  * Os argumentos ou o resultado podem ser desserializados corretamente antes de invocar o método.
* Executa a validação básica em todas as entradas provenientes do navegador de eventos expedidos:
  * O evento tem um tipo válido.
  * Os dados do evento podem ser desserializados.
  * Há um manipulador de eventos associado ao evento.

Além das proteções que a estrutura implementa, o aplicativo deve ser codificado pelo desenvolvedor para proteger contra ameaças e tomar as medidas apropriadas:

* Sempre validar dados ao manipular eventos.
* Execute a ação apropriada ao receber dados inválidos:
  * Ignorar os dados e retornar. Isso permite que o aplicativo continue processando solicitações.
  * Se o aplicativo determinar que a entrada é ilegítimo e não pôde ser produzida pelo cliente legítimo, acione uma exceção. Lançar uma exceção destrói o circuito e encerra a sessão.
* Não confie na mensagem de erro fornecida por conclusões de lote de renderização incluídas nos logs. O erro é fornecido pelo cliente e, em geral, não pode ser confiável, pois o cliente pode estar comprometido.
* Não confie na entrada em chamadas de interoperabilidade JS em qualquer direção entre os métodos JavaScript e .NET.
* O aplicativo é responsável por validar que o conteúdo dos argumentos e dos resultados é válido, mesmo que os argumentos ou os resultados sejam desserializados corretamente.

Para que uma vulnerabilidade de XSS exista, o aplicativo deve incorporar a entrada do usuário na página renderizada. BlazorOs componentes do servidor executam uma etapa de tempo de compilação em que a marcação em um arquivo *. Razor* é transformada em lógica C# de procedimento. Em tempo de execução, a lógica do C# cria uma *árvore de renderização* que descreve os elementos, o texto e os componentes filho. Isso é aplicado ao DOM do navegador por meio de uma sequência de instruções de JavaScript (ou é serializado para HTML no caso de pré-processamento):

* A entrada do usuário renderizada por meio Razor da sintaxe normal (por exemplo, `@someStringValue` ) não expõe uma vulnerabilidade de XSS porque a Razor sintaxe é adicionada ao dom por meio de comandos que só podem gravar texto. Mesmo se o valor incluir marcação HTML, o valor será exibido como texto estático. Ao renderizar, a saída é codificada em HTML, que também exibe o conteúdo como texto estático.
* Marcas de script não são permitidas e não devem ser incluídas na árvore de renderização de componente do aplicativo. Se uma marca de script for incluída na marcação de um componente, um erro de tempo de compilação será gerado.
* Os autores de componentes podem criar componentes em C# sem usar o Razor . O autor do componente é responsável por usar as APIs corretas ao emitir a saída. Por exemplo, use `builder.AddContent(0, someUserSuppliedString)` e *not* `builder.AddMarkupContent(0, someUserSuppliedString)` , pois o último pode criar uma vulnerabilidade de XSS.

Como parte da proteção contra ataques XSS, considere a implementação de mitigações XSS, como a [política de segurança de conteúdo (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).

Para obter mais informações, consulte <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Proteção entre origens

Os ataques entre origens envolvem um cliente de uma origem diferente executando uma ação no servidor. A ação mal-intencionada é normalmente uma solicitação GET ou uma POSTAgem de formulário (solicitação entre sites forjada, CSRF), mas também é possível abrir um WebSocket mal-intencionado. BlazorOs aplicativos de servidor oferecem [as mesmas garantias de que qualquer outro SignalR aplicativo usando a oferta de protocolo de Hub](xref:signalr/security):

* BlazorOs aplicativos de servidor podem ser acessados entre origens, a menos que sejam tomadas medidas adicionais para impedi-lo. Para desabilitar o acesso entre origens, desabilite o CORS no ponto de extremidade adicionando o middleware do CORS ao pipeline e adicionando o <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> aos Blazor metadados do ponto de extremidade ou limite o conjunto de origens permitidas [Configurando SignalR para compartilhamento de recursos entre origens](xref:signalr/security#cross-origin-resource-sharing).
* Se o CORS estiver habilitado, etapas adicionais poderão ser necessárias para proteger o aplicativo dependendo da configuração do CORS. Se o CORS for habilitado globalmente, o CORS poderá ser desabilitado para o Blazor Hub de servidor adicionando os <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> metadados aos metadados do ponto de extremidade após <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> a chamada no construtor de rotas do ponto de extremidade.

Para obter mais informações, consulte <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Sensor de clique

A tomada de clique envolve a renderização de um site como um `<iframe>` dentro de um site de uma origem diferente para induzir o usuário a executar ações no site sob ataque.

Para proteger um aplicativo contra a renderização dentro de um `<iframe>` , use o [CSP (política de segurança de conteúdo)](https://developer.mozilla.org/docs/Web/HTTP/CSP) e o `X-Frame-Options` cabeçalho. Para obter mais informações, consulte [MDN Web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Redirecionamentos abertos

Quando uma Blazor sessão de aplicativo de servidor é iniciada, o servidor executa a validação básica das URLs enviadas como parte do início da sessão. A estrutura verifica se a URL base é um pai da URL atual antes de estabelecer o circuito. Nenhuma verificação adicional é executada pela estrutura.

Quando um usuário seleciona um link no cliente, a URL do link é enviada para o servidor, que determina a ação a ser tomada. Por exemplo, o aplicativo pode executar uma navegação no lado do cliente ou indicar ao navegador para ir para o novo local.

Os componentes também podem disparar solicitações de navegação programaticamente por meio do uso do <xref:Microsoft.AspNetCore.Components.NavigationManager> . Nesses cenários, o aplicativo pode executar uma navegação no lado do cliente ou indicar ao navegador para ir para o novo local.

Os componentes devem:

* Evite usar a entrada do usuário como parte dos argumentos de chamada de navegação.
* Valide os argumentos para garantir que o destino seja permitido pelo aplicativo.

Caso contrário, um usuário mal-intencionado pode forçar o navegador a ir para um site controlado pelo invasor. Nesse cenário, o invasor vaza o aplicativo para usar alguma entrada do usuário como parte da invocação do <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> método.

Esse aviso também se aplica ao renderizar links como parte do aplicativo:

* Se possível, use links relativos.
* Valide se os destinos de link absoluto são válidos antes de incluí-los em uma página.

Para obter mais informações, consulte <xref:security/preventing-open-redirects>.

## <a name="security-checklist"></a>Lista de verificação de segurança

A seguinte lista de considerações de segurança não é abrangente:

* Validar argumentos de eventos.
* Validar entradas e resultados de chamadas de interoperabilidade JS.
* Evite usar (ou validar com antecedência) a entrada do usuário para chamadas de interoperabilidade .NET para JS.
* Impedir que o cliente aloque uma quantidade desassociada de memória.
  * Dados dentro do componente.
  * `DotNetObject`referências retornadas ao cliente.
* Proteção contra vários despachos.
* Cancelar operações de execução longa quando o componente for descartado.
* Evite eventos que produzem grandes quantidades de dados.
* Evite usar a entrada do usuário como parte das chamadas para <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> e validar a entrada do usuário para URLs em um conjunto de origens permitidas primeiro se for inevitável.
* Não tome decisões de autorização com base no estado da interface do usuário, mas somente no estado do componente.
* Considere o uso da [CSP (política de segurança de conteúdo)](https://developer.mozilla.org/docs/Web/HTTP/CSP) para proteger contra ataques de XSS.
* Considere usar o CSP e [as opções X-frame](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) para proteger contra o clique.
* Verifique se as configurações de CORS são apropriadas ao habilitar o CORS ou desabilitar explicitamente o CORS para Blazor aplicativos.
* Teste para garantir que os limites do lado do servidor para o Blazor aplicativo forneçam uma experiência de usuário aceitável sem níveis inaceitáveis de risco.

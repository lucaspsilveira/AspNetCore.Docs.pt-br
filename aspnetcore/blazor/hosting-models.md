---
Título: ' ASP.NET Core Blazor modelos de hospedagem ' autor: Descrição: ' entender Blazor Webassembly e Blazor modelos de Hospedagem de servidor '.
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---
# <a name="aspnet-core-blazor-hosting-models"></a>BlazorModelos de hospedagem ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27)

Blazoro é uma estrutura da Web criada para executar o lado do cliente no navegador em um tempo de execução .NET baseado em [Webassembler](https://webassembly.org/)(* Blazor Webassembly*) ou no lado do servidor no ASP.NET Core (* Blazor servidor*). Independentemente do modelo de hospedagem, os modelos de aplicativo e componente *são os mesmos*.

Para criar um projeto para os modelos de hospedagem descritos neste artigo, consulte <xref:blazor/get-started> .

Para configuração avançada, consulte <xref:blazor/hosting-model-configuration> .

## <a name="blazor-webassembly"></a>BlazorWebassembly

O modelo de hospedagem principal para o Blazor está executando o lado do cliente no navegador no Webassembly. O Blazor aplicativo, suas dependências e o tempo de execução do .NET são baixados para o navegador. O aplicativo é executado diretamente no thread da interface do usuário do navegador. As atualizações da interface do usuário e o tratamento de eventos ocorrem no mesmo processo. Os ativos do aplicativo são implantados como arquivos estáticos em um servidor Web ou serviço capaz de fornecer conteúdo estático aos clientes.

![BlazorWebassembly: o Blazor aplicativo é executado em um thread de interface do usuário dentro do navegador.](hosting-models/_static/blazor-webassembly.png)

Para criar um Blazor aplicativo usando o modelo de hospedagem do lado do cliente, use o modelo de ** Blazor aplicativo Webassembly** ([dotnet novo blazorwasm](/dotnet/core/tools/dotnet-new)).

Depois de selecionar o modelo de ** Blazor aplicativo Webassembly** , você tem a opção de configurar o aplicativo para usar um ASP.NET Core backend marcando a caixa de seleção **ASP.NET Core hospedado** ([dotnet New blazorwasm – hospedado](/dotnet/core/tools/dotnet-new)). O aplicativo ASP.NET Core serve o Blazor aplicativo aos clientes. O Blazor aplicativo Webassembly pode interagir com o servidor pela rede usando chamadas de API Web ou [SignalR](xref:signalr/introduction) ( <xref:tutorials/signalr-blazor-webassembly> ).

Os modelos incluem o `blazor.webassembly.js` script que manipula:

* Baixar o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.
* Inicialização do tempo de execução para executar o aplicativo.

O Blazor modelo de hospedagem Webassembly oferece vários benefícios:

* Não há nenhuma dependência do lado do servidor .NET. O aplicativo está totalmente funcionando depois de ser baixado para o cliente.
* Recursos e funcionalidades do cliente são totalmente aproveitados.
* O trabalho é descarregado do servidor para o cliente.
* Um servidor Web ASP.NET Core não é necessário para hospedar o aplicativo. Cenários de implantação sem servidor são possíveis (por exemplo, servindo o aplicativo de uma CDN).

Há desvantagens na hospedagem do Blazor Webassembly:

* O aplicativo é restrito aos recursos do navegador.
* Hardware e software de cliente com capacidade (por exemplo, suporte a Webassembly) é necessário.
* O tamanho do download é maior e os aplicativos demoram mais para serem carregados.
* O suporte ao tempo de execução e às ferramentas do .NET é menos maduro. Por exemplo, existem limitações em [.net Standard](/dotnet/standard/net-standard) suporte e depuração.

O Blazor modelo de aplicativo hospedado oferece suporte a [contêineres do Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Clique com o botão direito do mouse no projeto do servidor no Visual Studio e selecione **Adicionar**  >  **suporte ao Docker**.

## <a name="blazor-server"></a>BlazorServidor

Com o Blazor modelo de hospedagem do servidor, o aplicativo é executado no servidor de dentro de um aplicativo ASP.NET Core. As atualizações de interface do usuário, manipulação de eventos e chamadas JavaScript são manipuladas por uma [SignalR](xref:signalr/introduction) conexão.

![O navegador interage com o aplicativo (hospedado dentro de um aplicativo ASP.NET Core) no servidor por uma SignalR conexão.](hosting-models/_static/blazor-server.png)

Para criar um Blazor aplicativo usando o Blazor modelo de Hospedagem de servidor, use o modelo de ** Blazor aplicativo ASP.NET Core Server** ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)). O aplicativo ASP.NET Core hospeda o Blazor aplicativo de servidor e cria o SignalR ponto de extremidade onde os clientes se conectam.

O aplicativo ASP.NET Core referencia a classe do aplicativo `Startup` a ser adicionada:

* Serviços do lado do servidor.
* O aplicativo para o pipeline de tratamento de solicitação.

O `blazor.server.js` script estabelece a conexão do cliente. É responsabilidade do aplicativo persistir e restaurar o estado do aplicativo, conforme necessário (por exemplo, no caso de uma conexão de rede perdida). O `blazor.server.js` script é servido por um recurso inserido na estrutura compartilhada ASP.NET Core.

O Blazor modelo de Hospedagem de servidor oferece vários benefícios:

* O tamanho do download é significativamente menor do que um Blazor aplicativo Webassembly e o aplicativo é carregado muito mais rapidamente.
* O aplicativo aproveita totalmente os recursos do servidor, incluindo o uso de qualquer API compatível com o .NET Core.
* O .NET Core no servidor é usado para executar o aplicativo, portanto, as ferramentas .NET existentes, como depuração, funcionam conforme o esperado.
* Há suporte para clientes finos. Por exemplo, Blazor aplicativos de servidor funcionam com navegadores que não dão suporte a Webassembly e em dispositivos com restrição de recursos.
* A base de código .NET/C# do aplicativo, incluindo o código de componente do aplicativo, não é servida aos clientes.

Há desvantagens na Blazor hospedagem do servidor:

* A latência mais alta geralmente existe. Cada interação do usuário envolve um salto de rede.
* Não há suporte offline. Se a conexão do cliente falhar, o aplicativo para de funcionar.
* A escalabilidade é desafiadora para aplicativos com muitos usuários. O servidor deve gerenciar várias conexões de cliente e manipular o estado do cliente.
* Um servidor de ASP.NET Core é necessário para atender ao aplicativo. Cenários de implantação sem servidor não são possíveis (por exemplo, servir o aplicativo de uma CDN).

O Blazor modelo de aplicativo do servidor dá suporte a [contêineres do Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Clique com o botão direito do mouse no projeto no Visual Studio e selecione **Adicionar**  >  **suporte ao Docker**.

### <a name="comparison-to-server-rendered-ui"></a>Comparação com a interface do usuário renderizada pelo servidor

Uma maneira de entender os Blazor aplicativos de servidor é entender como ele difere dos modelos tradicionais para renderizar a interface do usuário em aplicativos ASP.NET Core usando Razor exibições ou Razor páginas. Ambos os modelos usam o Razor idioma para descrever o conteúdo HTML, mas eles são significativamente diferentes na forma como a marcação é renderizada.

Quando uma Razor página ou exibição é renderizada, cada linha de Razor código emite HTML na forma de texto. Após a renderização, o servidor descarta a página ou a instância de exibição, incluindo qualquer estado produzido. Quando outra solicitação para a página ocorre, por exemplo, quando a validação do servidor falha e o resumo de validação é exibido:

* A página inteira é reprocessada para o texto HTML novamente.
* A página é enviada ao cliente.

Um Blazor aplicativo é composto por elementos reutilizáveis da interface do usuário chamada de *componentes*. Um componente contém código C#, marcação e outros componentes. Quando um componente é renderizado, Blazor o produz um grafo dos componentes incluídos de forma semelhante a um XML ou modelo de objeto do documento de HTML (dom). Esse grafo inclui o estado do componente mantido em Propriedades e campos. Blazoravalia o grafo do componente para produzir uma representação binária da marcação. O formato binário pode ser:

* Transformado em texto HTML (durante o pré-processamento &dagger; ).
* Usado para atualizar a marcação com eficiência durante a renderização regular.

&dagger;*Pré-processamento*: o Razor componente solicitado é compilado no servidor em HTML estático e enviado ao cliente, onde ele é renderizado para o usuário. Depois que a conexão é feita entre o cliente e o servidor, os elementos preprocessados estáticos do componente são substituídos por elementos interativos. O pré-processamento faz com que o aplicativo sinta-se mais responsivo para o usuário.

Uma atualização de interface do usuário no Blazor é disparada por:

* Interação do usuário, como selecionar um botão.
* Gatilhos de aplicativo, como um temporizador.

O grafo é rerenderizado e uma *comparação* de interface do usuário (diferença) é calculada. Essa diferença é o menor conjunto de edições DOM necessárias para atualizar a interface do usuário no cliente. A comparação é enviada ao cliente em um formato binário e aplicada pelo navegador.

Um componente é descartado depois que o usuário navega para fora dele no cliente. Embora um usuário esteja interagindo com um componente, o estado do componente (serviços, recursos) deve ser mantido na memória do servidor. Como o estado de muitos componentes pode ser mantido pelo servidor simultaneamente, o esgotamento de memória é uma preocupação que deve ser resolvida. Para obter orientação sobre como criar um Blazor aplicativo de servidor para garantir o melhor uso da memória do servidor, consulte <xref:security/blazor/server/threat-mitigation> .

### <a name="circuits"></a>Circuitos

Um Blazor aplicativo de servidor é criado com base [em SignalR ASP.NET Core ](xref:signalr/introduction). Cada cliente se comunica com o servidor em uma ou mais SignalR conexões chamadas de *circuito*. Um circuito é Blazor abstração de SignalR conexões que podem tolerar interrupções de rede temporárias. Quando um Blazor cliente vê que a SignalR conexão é desconectada, ele tenta se reconectar ao servidor usando uma nova SignalR conexão.

Cada tela do navegador (guia do navegador ou iframe) que está conectada a um Blazor aplicativo de servidor usa uma SignalR conexão. Essa é ainda outra distinção importante em comparação com os aplicativos típicos renderizados pelo servidor. Em um aplicativo renderizado pelo servidor, a abertura do mesmo aplicativo em várias telas do navegador geralmente não se traduz em demandas de recursos adicionais no servidor. Em um Blazor aplicativo de servidor, cada tela do navegador requer um circuito separado e instâncias separadas do estado do componente a serem gerenciadas pelo servidor.

Blazorconsidera o fechamento de uma guia do navegador ou a navegação para uma URL externa um encerramento *normal* . No caso de um encerramento normal, o circuito e os recursos associados são lançados imediatamente. Um cliente também pode se desconectar não normalmente, por exemplo, devido a uma interrupção de rede. BlazorO servidor armazena circuitos desconectados para um intervalo configurável para permitir que o cliente se reconecte.

BlazorO servidor permite que o código defina um *manipulador de circuito*, que permite a execução de código em alterações no estado do circuito de um usuário. Para obter mais informações, consulte <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Latência da interface do usuário

A latência da interface do usuário é o tempo que leva de uma ação iniciada até a hora em que a interface do usuário é atualizada. Valores menores para latência de interface do usuário são imperativos para um aplicativo se sentir responsivo a um usuário. Em um Blazor aplicativo de servidor, cada ação é enviada ao servidor, processada e uma comparação da interface do usuário é enviada de volta. Consequentemente, a latência da interface do usuário é a soma da latência da rede e a latência do servidor no processamento da ação.

Para um aplicativo de linha de negócios limitado a uma rede corporativa privada, o efeito sobre as percepções do usuário de latência devido à latência da rede geralmente é imperceptível. Para um aplicativo implantado pela Internet, a latência pode ser perceptível para os usuários, especialmente se os usuários forem amplamente distribuídos geograficamente.

O uso de memória também pode contribuir para a latência do aplicativo. O aumento do uso da memória resulta em uma coleta de lixo frequente ou na paginação da memória no disco, e conseqüentemente aumenta a latência da interface do usuário.

BlazorAplicativos de servidor devem ser otimizados para minimizar a latência da interface do usuário, reduzindo a latência de rede e o uso de memória Para obter uma abordagem para medir a latência de rede, consulte <xref:host-and-deploy/blazor/server#measure-network-latency> . Para obter mais informações sobre o SignalR e o Blazor , consulte:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server/threat-mitigation>

### <a name="connection-to-the-server"></a>Conexão com o servidor

BlazorOs aplicativos de servidor exigem uma SignalR conexão ativa com o servidor. Se a conexão for perdida, o aplicativo tentará se reconectar ao servidor. Desde que o estado do cliente ainda esteja na memória, a sessão do cliente será retomada sem perder o estado.

Um Blazor aplicativo de servidor é renderizado em resposta à primeira solicitação do cliente, que configura o estado da interface do usuário no servidor. Quando o cliente tenta criar uma SignalR conexão, o cliente deve se reconectar ao mesmo servidor. BlazorOs aplicativos de servidor que usam mais de um servidor back-end devem implementar *sessões adesivas* para SignalR conexões.

É recomendável usar [o SignalR serviço do Azure](/azure/azure-signalr) para Blazor aplicativos de servidor. O serviço permite escalar verticalmente um Blazor aplicativo de servidor para um grande número de conexões simultâneas SignalR . As sessões adesivas são habilitadas para o serviço do Azure SignalR definindo a opção do serviço `ServerStickyMode` ou o valor de configuração como `Required` . Para obter mais informações, consulte <xref:host-and-deploy/blazor/server#signalr-configuration>.

Ao usar o IIS, as sessões adesivas são habilitadas com Application Request Routing. Para obter mais informações, consulte [balanceamento de carga http usando Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/hosting-model-configuration>
* <xref:tutorials/signalr-blazor-webassembly>

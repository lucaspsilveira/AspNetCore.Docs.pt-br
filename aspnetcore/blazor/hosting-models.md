---
title: modelos Blazor de hospedagem ASP.NET Core
author: guardrex
description: Entenda Blazor os Blazor modelos de hospedagem do WebAssembly e do Servidor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 0dfc991f76acb227ce9ea27a07fbae50571f0117
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80471834"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>modelos Blazor de hospedagem ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazoré uma estrutura web projetada para executar o lado do cliente no navegador em um tempo de execução .NET baseado no [WebAssembly](https://webassembly.org/)* Blazor (WebAssembly)* ou no lado do servidor no ASP.NET Core* Blazor (Server).* Independentemente do modelo de hospedagem, os modelos de aplicativo e *componentes são os mesmos*.

Para criar um projeto para os modelos <xref:blazor/get-started>de hospedagem descritos neste artigo, consulte .

Para configuração <xref:blazor/hosting-model-configuration>avançada, consulte .

## <a name="opno-locblazor-webassembly"></a>BlazorWebAssembly

O principal modelo Blazor de hospedagem para está executando o lado do cliente no navegador no WebAssembly. O Blazor aplicativo, suas dependências e o tempo de execução .NET são baixados para o navegador. O aplicativo é executado diretamente no thread da interface do usuário do navegador. Atualizações de iu e tratamento de eventos ocorrem dentro do mesmo processo. Os ativos do aplicativo são implantados como arquivos estáticos para um servidor web ou serviço capaz de servir conteúdo estático aos clientes.

![BlazorWebAssembly: Blazor O aplicativo é executado em um segmento de interface do usuário dentro do navegador.](hosting-models/_static/blazor-webassembly.png)

Para criar Blazor um aplicativo usando o modelo de hospedagem do lado do cliente, use o ** Blazor ** modelo do WebAssembly App[(dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).

Depois de selecionar o modelo do ** Blazor Aplicativo WebAssembly,** você tem a opção de configurar o aplicativo para usar um backend ASP.NET Core selecionando a caixa de seleção **hosted ASP.NET Core** [(dotnet new blazorwasm --hosted).](/dotnet/core/tools/dotnet-new) O aplicativo ASP.NET Blazor Core atende o aplicativo aos clientes. O Blazor aplicativo WebAssembly pode interagir com o servidor através [SignalR](xref:signalr/introduction) <xref:tutorials/signalr-blazor-webassembly>da rede usando chamadas de API web ou ( ).

Os modelos `blazor.webassembly.js` incluem o script que lida com:

* Baixando o tempo de execução .NET, o aplicativo e as dependências do aplicativo.
* Inicialização do tempo de execução para executar o aplicativo.

O Blazor modelo de hospedagem do WebAssembly oferece vários benefícios:

* Não há dependência do lado do servidor .NET. O aplicativo está funcionando plenamente depois de ser baixado para o cliente.
* Os recursos e recursos do cliente são totalmente aproveitados.
* O trabalho é descarregado do servidor para o cliente.
* Um servidor web ASP.NET Core não é necessário para hospedar o aplicativo. Cenários de implantação sem servidor são possíveis (por exemplo, servindo o aplicativo a partir de um CDN).

Existem desvantagens para a Blazor hospedagem do WebAssembly:

* O aplicativo é restrito aos recursos do navegador.
* É necessário hardware e software de cliente capazes (por exemplo, suporte ao WebAssembly).
* O tamanho do download é maior e os aplicativos demoram mais para carregar.
* O tempo de execução .NET e o suporte a ferramentas são menos maduros. Por exemplo, existem limitações no suporte e depuração [do .NET Standard.](/dotnet/standard/net-standard)

O Blazor modelo de aplicativo hospedado suporta [contêineres Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Clique com o botão direito do mouse no projeto Servidor no Visual Studio e **selecione Adicionar** > **suporte ao Docker**.

## <a name="opno-locblazor-server"></a>BlazorServidor

Com Blazor o modelo de hospedagem do Servidor, o aplicativo é executado no servidor a partir de um aplicativo ASP.NET Core. Atualizações de iu, tratamento de eventos e chamadas [SignalR](xref:signalr/introduction) JavaScript são tratadas por uma conexão.

![O navegador interage com o aplicativo (hospedado dentro de um aplicativo SignalR ASP.NET Core) no servidor por uma conexão.](hosting-models/_static/blazor-server.png)

Para criar Blazor um Blazor aplicativo usando o modelo de hospedagem do Servidor, use o modelo ASP.NET do Core ** Blazor Server App** [(dotnet new blazorserver](/dotnet/core/tools/dotnet-new)). O aplicativo ASP.NET Blazor Core hospeda SignalR o aplicativo Server e cria o ponto final onde os clientes se conectam.

O aplicativo ASP.NET Core faz `Startup` referência à classe do aplicativo para adicionar:

* Serviços do lado do servidor.
* O aplicativo para o pipeline de tratamento de solicitação.

O `blazor.server.js` script estabelece a conexão com o cliente. É responsabilidade do aplicativo persistir e restaurar o estado do aplicativo conforme necessário (por exemplo, no caso de uma conexão de rede perdida). O `blazor.server.js` script é servido a partir de um recurso incorporado na estrutura compartilhada ASP.NET Core.

O Blazor modelo de hospedagem do Servidor oferece vários benefícios:

* O tamanho do download é Blazor significativamente menor do que um aplicativo WebAssembly, e o aplicativo carrega muito mais rápido.
* O aplicativo aproveita ao máximo os recursos do servidor, incluindo o uso de quaisquer APIs compatíveis com o .NET Core.
* O .NET Core no servidor é usado para executar o aplicativo, então a ferramenta .NET existente, como a depuração, funciona como esperado.
* Clientes magros são suportados. Por exemplo, Blazor os aplicativos do Server trabalham com navegadores que não suportam o WebAssembly e em dispositivos com restrição de recursos.
* A base de código .NET/C#do aplicativo, incluindo o código de componentes do aplicativo, não é servida aos clientes.

Há desvantagens na Blazor hospedagem do Servidor:

* A latência mais alta geralmente existe. Toda interação do usuário envolve um salto de rede.
* Não há suporte offline. Se a conexão com o cliente falhar, o aplicativo pára de funcionar.
* A escalabilidade é um desafio para aplicativos com muitos usuários. O servidor deve gerenciar várias conexões com clientes e lidar com o estado do cliente.
* Um servidor ASP.NET Core é necessário para servir o aplicativo. Cenários de implantação sem servidor não são possíveis (por exemplo, servindo o aplicativo a partir de um CDN).

O Blazor modelo do aplicativo Server suporta [contêineres Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Clique com o botão direito do mouse sobre o projeto no Visual Studio e **selecione Adicionar** > **suporte ao Docker**.

### <a name="comparison-to-server-rendered-ui"></a>Comparação com a ui renderizada pelo servidor

Uma maneira Blazor de entender os aplicativos do Server é entender como ele difere dos modelos tradicionais para renderizar interface do usuário em ASP.NET aplicativos Core usando visualizações de Razor ou Páginas navalha. Ambos os modelos usam a linguagem Razor para descrever o conteúdo HTML, mas eles diferem significativamente na forma como a marcação é renderizada.

Quando uma página de navalha ou exibição é renderizada, cada linha de código Razor emite HTML em forma de texto. Após a renderização, o servidor elimina a página ou a instância de exibição, incluindo qualquer estado que tenha sido produzido. Quando ocorre outra solicitação para a página, por exemplo, quando a validação do servidor falha e o resumo de validação é exibido:

* A página inteira é rerenderizada para texto HTML novamente.
* A página é enviada ao cliente.

Um Blazor aplicativo é composto de elementos reutilizáveis de ui chamados *componentes*. Um componente contém código C#, marcação e outros componentes. Quando um componente é Blazor renderizado, produz um gráfico dos componentes incluídos semelhante a um HTML ou XML Document Object Model (DOM). Este gráfico inclui o estado componente mantido em propriedades e campos. Blazoravalia o gráfico componente para produzir uma representação binária da marcação. O formato binário pode ser:

* Transformado em texto HTML&dagger;(durante a pré-renderização ).
* Usado para atualizar eficientemente a marcação durante a renderização regular.

&dagger;*Pré-renderização* &ndash; O componente Razor solicitado é compilado no servidor em HTML estático e enviado ao cliente, onde é renderizado ao usuário. Depois que a conexão é feita entre o cliente e o servidor, os elementos pré-renderizados estáticos do componente são substituídos por elementos interativos. A pré-renderização faz com que o aplicativo se sinta mais responsivo ao usuário.

Uma atualização de Blazor iu é acionada por:

* Interação do usuário, como selecionar um botão.
* Gatilhos de aplicativos, como um temporizador.

O gráfico é rerenderizado, e um *diferencial* de UI (diferença) é calculado. Este diff é o menor conjunto de edições DOM necessárias para atualizar a iu no cliente. O diff é enviado ao cliente em um formato binário e aplicado pelo navegador.

Um componente é descartado depois que o usuário navega longe dele no cliente. Enquanto um usuário está interagindo com um componente, o estado do componente (serviços, recursos) deve ser mantido na memória do servidor. Como o estado de muitos componentes pode ser mantido pelo servidor simultaneamente, a exaustão da memória é uma preocupação que deve ser tratada. Para obter orientações Blazor sobre como criar um aplicativo Server <xref:security/blazor/server>para garantir o melhor uso da memória do servidor, consulte .

### <a name="circuits"></a>Circuitos

Um Blazor aplicativo Server é construído em cima do [ASP.NET Core SignalR ](xref:signalr/introduction). Cada cliente se comunica com o SignalR servidor por uma ou mais conexões chamadas de *circuito*. Um circuito Blazoré a abstração sobre SignalR conexões que podem tolerar interrupções temporárias da rede. Quando Blazor um cliente vê SignalR que a conexão está desconectada, ele SignalR tenta se reconectar ao servidor usando uma nova conexão.

Cada tela do navegador (guia do navegador Blazor ou iframe) que está conectada a um aplicativo do Servidor usa uma SignalR conexão. Esta é mais uma distinção importante em comparação com aplicativos típicos renderizados por servidor. Em um aplicativo renderizado por servidor, abrir o mesmo aplicativo em várias telas do navegador normalmente não se traduz em demandas adicionais de recursos no servidor. Em Blazor um aplicativo do Server, cada tela do navegador requer um circuito separado e instâncias separadas do estado componente para serem gerenciadas pelo servidor.

Blazorconsidera fechar uma guia do navegador ou navegar para uma URL externa uma rescisão *graciosa.* Em caso de rescisão graciosa, o circuito e os recursos associados são imediatamente liberados. Um cliente também pode desconectar-se não graciosamente, por exemplo, devido a uma interrupção de rede. BlazorO servidor armazena circuitos desconectados para um intervalo configurável para permitir que o cliente se reconecte.

BlazorO servidor permite que o código defina um *manipulador de circuitos,* que permite executar o código em alterações no estado do circuito de um usuário. Para obter mais informações, consulte <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Latência de UI

Latência de UI é o tempo que leva de uma ação iniciada até o momento em que a ui é atualizada. Valores menores para latência de IA são imprescindíveis para que um aplicativo se sinta responsivo a um usuário. Em Blazor um aplicativo do Servidor, cada ação é enviada para o servidor, processada e um diferencial de iu é enviado de volta. Consequentemente, a latência da UI é a soma da latência da rede e da latência do servidor no processamento da ação.

Para uma linha de aplicativo de negócios que é limitada a uma rede corporativa privada, o efeito sobre a percepção do usuário de latência devido à latência da rede são geralmente imperceptíveis. Para um aplicativo implantado pela Internet, a latência pode se tornar perceptível para os usuários, especialmente se os usuários forem amplamente distribuídos geograficamente.

O uso da memória também pode contribuir para a latência do aplicativo. O aumento do uso da memória resulta em coleta de lixo frequente ou paginação de memória para disco, ambos os quais degradam o desempenho do aplicativo e, consequentemente, aumentam a latência da IU. Para obter mais informações, consulte <xref:security/blazor/server>.

BlazorOs aplicativos do servidor devem ser otimizados para minimizar a latência da interface do usuário, reduzindo a latência da rede e o uso da memória. Para uma abordagem para medir <xref:host-and-deploy/blazor/server#measure-network-latency>a latência da rede, consulte . Para obter SignalR mais Blazorinformações sobre e , consulte:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>Conexão com o servidor

BlazorOs aplicativos do SignalR servidor exigem uma conexão ativa com o servidor. Se a conexão for perdida, o aplicativo tentará se reconectar ao servidor. Enquanto o estado do cliente ainda estiver na memória, a sessão do cliente é retomada sem perder o estado.

Um Blazor aplicativo do Server prerenderiza em resposta à primeira solicitação do cliente, que configura o estado de ida e qualquer vez no servidor. Quando o cliente tenta SignalR criar uma conexão, o cliente deve se reconectar ao mesmo servidor. BlazorOs aplicativos de servidor que usam mais de SignalR um servidor backend devem implementar *sessões pegajosas* para conexões.

Recomendamos o uso dos Blazor aplicativos [Azure SignalR Service](/azure/azure-signalr) for Server. O serviço permite dimensionar um Blazor aplicativo server para SignalR um grande número de conexões simultâneas. As sessões pegajosas SignalR são habilitadas para `ServerStickyMode` o Serviço Azure definindo a opção ou o valor de configuração do serviço para `Required`. Para obter mais informações, consulte <xref:host-and-deploy/blazor/server#signalr-configuration>.

Ao usar o IIS, as sessões pegajosas são habilitadas com o roteamento de solicitação de aplicativo. Para obter mais informações, consulte [HTTP Balanceamento de carga usando o roteamento de solicitação de aplicativo](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/hosting-model-configuration>
* <xref:tutorials/signalr-blazor-webassembly>

---
title: Lidar com erros Blazor em aplicativos ASP.NET Core
author: guardrex
description: Descubra como Blazor ASP.NET Blazor Core como gerencia exceções não tratadas e como desenvolver aplicativos que detectam e lidam com erros.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 4fdaf7fb90d126b8f7f029aac3af49eec3b69e74
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80382269"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a>Lidar com erros Blazor em aplicativos ASP.NET Core

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

Este artigo descreve Blazor como gerencia exceções não tratadas e como desenvolver aplicativos que detectam e lidam com erros.

## <a name="detailed-errors-during-development"></a>Erros detalhados durante o desenvolvimento

Quando Blazor um aplicativo não está funcionando corretamente durante o desenvolvimento, receber informações detalhadas de erro do aplicativo ajuda a solucionar problemas e corrigir o problema. Quando ocorre um Blazor erro, os aplicativos exibem uma barra de ouro na parte inferior da tela:

* Durante o desenvolvimento, a barra de ouro direciona você para o console do navegador, onde você pode ver a exceção.
* Na produção, a barra de ouro notifica o usuário de que ocorreu um erro e recomenda a atualização do navegador.

A ui para esta experiência de Blazor manipulação de erros faz parte dos modelos do projeto.

Em Blazor um aplicativo WebAssembly, personalize a experiência no arquivo *wwwroot/index.html:*

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

Em Blazor um aplicativo do Server, personalize a experiência no arquivo *Páginas/_Host.cshtml:*

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

O `blazor-error-ui` elemento é oculto pelos Blazor estilos incluídos nos modelos *(wwwroot/css/site.css)* e, em seguida, mostrado quando ocorre um erro:

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a>Como Blazor um aplicativo do Servidor reage a exceções não tratadas

BlazorServer é uma estrutura imponente. Enquanto os usuários interagem com um aplicativo, eles mantêm uma conexão com o servidor conhecido como *circuito*. O circuito mantém instâncias de componentes ativos, além de muitos outros aspectos do estado, tais como:

* A mais recente saída renderizada de componentes.
* O conjunto atual de delegados de manipulação de eventos que poderia ser acionado por eventos do lado do cliente.

Se um usuário abrir o aplicativo em várias guias do navegador, ele terá vários circuitos independentes.

Blazortrata a maioria das exceções não tratadas como fatais para o circuito onde ocorrem. Se um circuito for encerrado devido a uma exceção não tratada, o usuário só poderá continuar a interagir com o aplicativo recarregando a página para criar um novo circuito. Circuitos fora do que está encerrado, que são circuitos para outros usuários ou outras guias do navegador, não são afetados. Este cenário é semelhante a um&mdash;aplicativo de desktop que trava o aplicativo crash deve ser reiniciado, mas outros aplicativos não são afetados.

Um circuito é encerrado quando ocorre uma exceção não manuseada pelas seguintes razões:

* Uma exceção não manuseada muitas vezes deixa o circuito em um estado indefinido.
* O funcionamento normal do aplicativo não pode ser garantido após uma exceção não tratada.
* As vulnerabilidades de segurança podem aparecer no aplicativo se o circuito continuar.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Gerenciar exceções não tratadas no código do desenvolvedor

Para que um aplicativo continue após um erro, o aplicativo deve ter lógica de manipulação de erros. Seções posteriores deste artigo descrevem fontes potenciais de exceções não tratadas.

Na produção, não torne mensagens de exceção de quadro ou empilhe traços na ui. Renderizar mensagens de exceção ou empilhar vestígios pode:

* Divulgar informações confidenciais aos usuários finais.
* Ajude um usuário mal-intencionado a descobrir fraquezas em um aplicativo que podem comprometer a segurança do aplicativo, servidor ou rede.

## <a name="log-errors-with-a-persistent-provider"></a>Erros de log com um provedor persistente

Se ocorrer uma exceção não manuseada, a exceção será registrada em <xref:Microsoft.Extensions.Logging.ILogger> instâncias configuradas no contêiner de serviço. Por padrão, Blazor os aplicativos registram a saída do console com o Provedor de Registro de Console. Considere fazer login em um local mais permanente com um provedor que gerencia o tamanho do registro e a rotação de log. Para obter mais informações, consulte <xref:fundamentals/logging/index>.

Durante o Blazor desenvolvimento, geralmente envia todos os detalhes de exceções ao console do navegador para ajudar na depuração. Na produção, erros detalhados no console do navegador são desativados por padrão, o que significa que os erros não são enviados aos clientes, mas os detalhes completos da exceção ainda estão registrados do lado do servidor. Para obter mais informações, consulte <xref:fundamentals/error-handling>.

Você deve decidir quais incidentes registrar e o nível de gravidade dos incidentes registrados. Usuários hostis podem ser capazes de desencadear erros deliberadamente. Por exemplo, não registre um incidente de `ProductId` um erro onde um desconhecido é fornecido na URL de um componente que exibe detalhes do produto. Nem todos os erros devem ser tratados como incidentes de alta gravidade para o registro.

## <a name="places-where-errors-may-occur"></a>Locais onde erros podem ocorrer

O framework e o código do aplicativo podem disparar exceções não tratadas em qualquer um dos seguintes locais:

* [Instantiva de componentes](#component-instantiation)
* [Métodos de ciclo de vida](#lifecycle-methods)
* [Lógica de renderização](#rendering-logic)
* [Manipuladores de eventos](#event-handlers)
* [Descarte de componentes](#component-disposal)
* [Interoperabilidade do JavaScript](#javascript-interop)
* [BlazorRerenderização do servidor](#blazor-server-prerendering)

As exceções não tratadas anteriores são descritas nas seguintes seções deste artigo.

### <a name="component-instantiation"></a>Instantiva de componentes

Quando Blazor cria uma instância de um componente:

* O construtor do componente é invocado.
* Os construtores de quaisquer serviços DI não singleton fornecidos [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) ao construtor [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) do componente através da diretiva ou do atributo são invocados.

Um Blazor circuito do servidor falha quando qualquer construtor `[Inject]` executado ou um setter para qualquer propriedade lança uma exceção não manuseada. A exceção é fatal porque a estrutura não pode instanciar o componente. Se a lógica do construtor pode lançar exceções, o aplicativo deve prender as exceções usando uma declaração [de tentativa de captura](/dotnet/csharp/language-reference/keywords/try-catch) com manipulação de erros e registro.

### <a name="lifecycle-methods"></a>Métodos de ciclo de vida

Durante a vida útil Blazor de um componente, invoca os seguintes [métodos de ciclo de vida:](xref:blazor/lifecycle)

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

Se qualquer método de ciclo de vida lançar uma exceção, sincronizada ou Blazor assíncronamente, a exceção é fatal para um circuito do Servidor. Para que os componentes lidem com erros nos métodos do ciclo de vida, adicione a lógica de manipulação de erros.

No exemplo a `OnParametersSetAsync` seguir, quando se chama um método para obter um produto:

* Uma exceção `ProductRepository.GetProductByIdAsync` lançada no método `try-catch` é tratada por uma declaração.
* Quando `catch` o bloco é executado:
  * `loadFailed`é definido `true`como , que é usado para exibir uma mensagem de erro para o usuário.
  * O erro está registrado.

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Lógica de renderização

A marcação declarativa `.razor` em um arquivo de componente `BuildRenderTree`é compilada em um método C# chamado . Quando um componente `BuildRenderTree` renderiza, executa e constrói uma estrutura de dados descrevendo os elementos, texto e componentes filho do componente renderizado.

A lógica de renderização pode abrir uma exceção. Um exemplo desse cenário `@someObject.PropertyName` ocorre quando `@someObject` `null`é avaliado, mas é . Uma exceção não manuseada lançada Blazor pela lógica de renderização é fatal para um circuito do Servidor.

Para evitar uma exceção de referência `null` nula na lógica de renderização, verifique se há um objeto antes de acessar seus membros. No exemplo a `person.Address` seguir, as propriedades `person.Address` `null`não são acessadas se for:

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

O código anterior `person` pressupõe `null`que não é. Muitas vezes, a estrutura do código garante que um objeto existe no momento em que o componente é renderizado. Nesses casos, não é necessário verificar `null` a lógica de renderização. No exemplo anterior, `person` pode ser `person` garantido que exista porque é criado quando o componente é instanciado.

### <a name="event-handlers"></a>Manipuladores de eventos

O código do lado do cliente aciona invocações do código C# quando os manipuladores de eventos são criados usando:

* `@onclick`
* `@onchange`
* Outros `@on...` atributos
* `@bind`

O código do manipulador de eventos pode lançar uma exceção não manuseada nesses cenários.

Se um manipulador de eventos lançar uma exceção não manuseada (por Blazor exemplo, uma consulta de banco de dados falhará), a exceção será fatal para um circuito do Servidor. Se o aplicativo chamar código que pode falhar por razões externas, tente exceções usando uma declaração [de tentativa de captura](/dotnet/csharp/language-reference/keywords/try-catch) com manipulação e registro de erros.

Se o código do usuário não prender e lidar com a exceção, a estrutura registra a exceção e encerra o circuito.

### <a name="component-disposal"></a>Descarte de componentes

Um componente pode ser removido da ui, por exemplo, porque o usuário navegou para outra página. Quando um componente <xref:System.IDisposable?displayProperty=fullName> que implementa é removido da ui, <xref:System.IDisposable.Dispose*> a estrutura chama o método do componente.

Se o método `Dispose` do componente lançar uma exceção não Blazor manuseada, a exceção será fatal para um circuito do Servidor. Se a lógica de descarte pode lançar exceções, o aplicativo deve prender as exceções usando uma declaração [de tentativa de captura](/dotnet/csharp/language-reference/keywords/try-catch) com manipulação de erros e registro.

Para obter mais informações <xref:blazor/lifecycle#component-disposal-with-idisposable>sobre o descarte de componentes, consulte .

### <a name="javascript-interop"></a>Interoperabilidade do JavaScript

`IJSRuntime.InvokeAsync<T>`permite que o código .NET faça chamadas assíncronas para o tempo de execução javaScript no navegador do usuário.

As seguintes condições se `InvokeAsync<T>`aplicam ao manuseio de erros com:

* Se uma `InvokeAsync<T>` chamada falhar sincronizadamente, uma exceção .NET ocorrerá. Uma chamada `InvokeAsync<T>` pode falhar, por exemplo, porque os argumentos fornecidos não podem ser serializados. O código do desenvolvedor deve pegar a exceção. Se o código do aplicativo em um manipulador de eventos ou método de ciclo Blazor de vida de componentes não lidar com uma exceção, a exceção resultante é fatal para um circuito do Servidor.
* Se uma `InvokeAsync<T>` chamada falhar assíncronamente, o .NET <xref:System.Threading.Tasks.Task> falhará. Uma chamada `InvokeAsync<T>` pode falhar, por exemplo, porque o código lado `Promise` JavaScript `rejected`lança uma exceção ou retorna uma que concluída como . O código do desenvolvedor deve pegar a exceção. Se usar o operador [de espera,](/dotnet/csharp/language-reference/keywords/await) considere envolver a chamada do método em uma declaração [de tentativa de captura](/dotnet/csharp/language-reference/keywords/try-catch) com manipulação de erro e registro. Caso contrário, o código de falha resulta em uma Blazor exceção não tratada que é fatal para um circuito do servidor.
* Por padrão, `InvokeAsync<T>` as chamadas devem ser concluídas dentro de um determinado período ou então os tempos de chamada são esgotados. O período de tempo padrão é de um minuto. O tempo há10 protege o código contra uma perda na conectividade de rede ou código JavaScript que nunca envia uma mensagem de conclusão. Se a chamada for chamada, o resultado `Task` falhará com um <xref:System.OperationCanceledException>. Capturar e processar a exceção com o registro.

Da mesma forma, o código JavaScript pode iniciar [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) chamadas para métodos .NET indicados pelo atributo. Se esses métodos .NET lançarem uma exceção não manuseada:

* A exceção não é tratada Blazor como fatal para um circuito de servidor.
* O lado `Promise` JavaScript é rejeitado.

Você tem a opção de usar o código de manipulação de erros no lado .NET ou no lado JavaScript da chamada do método.

Para obter mais informações, consulte os seguintes artigos:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>BlazorPrerendering do servidor

Blazoros componentes podem ser pré-renderizados usando o [Component Tag Helper para](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) que sua marcação HTML renderizada seja devolvida como parte da solicitação HTTP inicial do usuário. Isso funciona por:

* Criação de um novo circuito para todos os componentes pré-renderizados que fazem parte da mesma página.
* Gerando o HTML inicial.
* Tratando o `disconnected` circuito como até que SignalR o navegador do usuário estabeleça uma conexão de volta para o mesmo servidor. Quando a conexão é estabelecida, a interatividade no circuito é retomada e a marcação HTML dos componentes é atualizada.

Se algum componente lançar uma exceção não manuseada durante a pré-renderização, por exemplo, durante um método de ciclo de vida ou na lógica de renderização:

* A exceção é fatal para o circuito.
* A exceção é lançada a `Component` pilha de chamadas do Tag Helper. Portanto, toda a solicitação HTTP falha a menos que a exceção seja explicitamente capturada pelo código do desenvolvedor.

Em circunstâncias normais, quando a pré-renderização falha, continuar a construir e renderizar o componente não faz sentido porque um componente de trabalho não pode ser renderizado.

Para tolerar erros que possam ocorrer durante a pré-renderização, a lógica de manipulação de erros deve ser colocada dentro de um componente que pode lançar exceções. Use instruções [de captura de](/dotnet/csharp/language-reference/keywords/try-catch) tentativa com manipulação de erros e registro. Em vez `Component` de envolver o `try-catch` Tag Helper em uma declaração, `Component` coloque a lógica de manipulação de erros no componente renderizado pelo Tag Helper.

## <a name="advanced-scenarios"></a>Cenários avançados

### <a name="recursive-rendering"></a>Renderização recursiva

Os componentes podem ser aninhados recursivamente. Isso é útil para representar estruturas de dados recursivas. Por exemplo, `TreeNode` um componente `TreeNode` pode renderizar mais componentes para cada um dos filhos do nó.

Ao renderizar recursivamente, evite padrões de codificação que resultem em recursão infinita:

* Não torne recursivamente uma estrutura de dados que contenha um ciclo. Por exemplo, não faça um nó de árvore cujos filhos se incluam.
* Não crie uma cadeia de layouts que contenham um ciclo. Por exemplo, não crie um layout cujo layout seja em si.
* Não permita que um usuário final viole invariantes (regras) de recursão através de entrada de dados maliciosos ou chamadas interop JavaScript.

Loops infinitos durante a renderização:

* Faz com que o processo de renderização continue para sempre.
* Equivale a criar um loop não terminado.

Nesses cenários, Blazor um circuito servidor afetado falha, e o segmento geralmente tenta:

* Consumir o máximo de tempo de CPU permitido pelo sistema operacional, indefinidamente.
* Consumir uma quantidade ilimitada de memória do servidor. Consumir memória ilimitada é equivalente ao cenário em que um loop não terminado adiciona entradas a uma coleção em cada iteração.

Para evitar padrões infinitos de recursão, certifique-se de que o código de renderização recursivo contenha condições de parada adequadas.

### <a name="custom-render-tree-logic"></a>Lógica de árvore de renderização personalizada

A Blazor maioria dos componentes são implementados como arquivos *.razor* `RenderTreeBuilder` e são compilados para produzir lógica que opera em um para renderizar sua saída. Um desenvolvedor pode `RenderTreeBuilder` implementar manualmente a lógica usando o código C# procedural. Para obter mais informações, consulte <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> O uso da lógica manual de construção de árvores de renderização é considerado um cenário avançado e inseguro, não recomendado para o desenvolvimento geral de componentes.

Se `RenderTreeBuilder` o código for escrito, o desenvolvedor deve garantir a correção do código. Por exemplo, o desenvolvedor deve garantir que:

* Chamadas `OpenElement` para `CloseElement` e são corretamente equilibradas.
* Atributos só são adicionados nos lugares corretos.

A lógica incorreta do construtor de árvores de renderização manual pode causar comportamentos arbitrários indefinidos, incluindo falhas, travamentos de servidor e vulnerabilidades de segurança.

Considere a lógica manual do construtor de árvores de renderização no mesmo nível de complexidade e com o mesmo nível de *perigo* que escrever código de montagem ou instruções MSIL manualmente.

---
Título: "criar seu primeiro Blazor aplicativo" autor: guardrex Descrição: "compilar um Blazor aplicativo passo a passo".
monikerRange: ' >= aspnetcore-3,0 ' MS. Author: Riande MS. Custom: MVC MS. Date: 05/19/2020 no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' UID: Tutorials/First-mais incrivelmente-aplicativo

---
# <a name="build-your-first-blazor-app"></a>Crie seu primeiro Blazor aplicativo

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Este tutorial mostra como criar e modificar um Blazor aplicativo. Você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto de aplicativo de lista de tarefas Blazor
> * Modificar Razor componentes
> * Usar manipulação de eventos e vinculação de dados em componentes
> * Usar injeção de dependência (DI) e roteamento em um Blazor aplicativo

No final deste tutorial, você terá um aplicativo de chat em funcionamento.

## <a name="build-components"></a>Componentes do build

1. Siga as orientações no <xref:blazor/get-started> artigo para criar um Blazor projeto para este tutorial. Nomeie o projeto como *ToDoList*.

1. Navegue até cada uma das três páginas do aplicativo na pasta *páginas* : Home, Counter e FETCH Data. Essas páginas são implementadas pelos Razor arquivos de componente *index. Razor*, *Counter. Razor*e *FetchData. Razor*.

1. Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página. Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript. Com o Blazor , você pode escrever C# em vez disso.

1. Examine a implementação do componente `Counter` no arquivo *Counter.razor*.

   *Pages/Counter.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   A interface do usuário do componente `Counter` é definida usando HTML. A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe C# inserida chamada [Razor](xref:mvc/views/razor) . A marcação HTML e a lógica de renderização de C# são convertidas em uma classe de componente no momento da compilação. O nome da classe .NET gerada corresponde ao nome do arquivo.

   Os membros da classe de componente são definidos em um bloco `@code`. No bloco `@code`, o estado do componente (propriedades, campos) e os métodos são especificados para manipulação de eventos ou para definição de outra lógica de componente. Assim, esses membros são usados como parte da lógica de renderização do componente e para manipulação de eventos.

   Quando o botão **Clique aqui** botão é selecionado:

   * O manipulador `onclick` registrado do componente `Counter` é chamado (o método `IncrementCount`).
   * O componente `Counter` regenera sua árvore de renderização.
   * A nova árvore de renderização é comparada à anterior.
   * Apenas as modificações ao DOM (Modelo de Objeto do Documento) são aplicadas. A contagem exibida é atualizada.

1. Modifique a lógica de C# do componente `Counter` para que o incremento da contagem seja por dois em vez de um.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Recompile e execute o aplicativo para ver as alterações. Selecione o botão **Clique aqui**. O contador incrementa em dois.

## <a name="use-components"></a>Usar componentes

Inclua um componente em outro componente usando uma sintaxe HTML.

1. Adicione o componente `Counter` no componente `Index` do aplicativo adicionando um elemento `<Counter />` ao componente `Index` (*Index.razor*).

   Se você estiver usando Blazor o Webassembly para essa experiência, um `SurveyPrompt` componente será usado pelo `Index` componente. Substitua o elemento `<SurveyPrompt>` pelo elemento `<Counter />`. Se você estiver usando um Blazor aplicativo de servidor para essa experiência, adicione o `<Counter />` elemento ao `Index` componente:

   *Pages/Index.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Recompile e execute o aplicativo. O componente `Index` tem seu próprio contador.

## <a name="component-parameters"></a>Parâmetros do componente

Componentes também podem ter parâmetros. Os parâmetros de componente são definidos usando propriedades públicas na classe de componente com o [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributo. Use atributos para especificar argumentos para um componente na marcação.

1. Atualize o código C# do componente da `@code` seguinte maneira:

   * Adicione uma `IncrementAmount` propriedade pública com o [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributo.
   * Altere o `IncrementCount` método para usar a `IncrementAmount` propriedade ao aumentar o valor de `currentCount` .

   *Pages/Counter.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Especifique um parâmetro `IncrementAmount` no elemento `<Counter>` do componente `Index` usando um atributo. Defina o valor para incrementar o contador em 10.

   *Pages/Index.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Recarregue o componente `Index`. O contador é incrementado em dez sempre que o botão **Clique aqui** é selecionado. O contador no componente `Counter` continua a incrementar em um.

## <a name="route-to-components"></a>Rotear para componentes

A diretiva `@page` no início do arquivo *Counter.razor* especifica que esse componente `Counter` é um ponto de extremidade de roteamento. O componente `Counter` manipula solicitações enviadas para `/counter`. Sem a diretiva `@page`, o componente não lidará com as solicitações roteadas, mas ele ainda poderá ser usado por outros componentes.

## <a name="dependency-injection"></a>Injeção de dependência

### <a name="blazor-server-experience"></a>BlazorExperiência do servidor

Se estiver trabalhando com um Blazor aplicativo de servidor, o `WeatherForecastService` serviço será registrado como um [singleton](xref:fundamentals/dependency-injection#service-lifetimes) no `Startup.ConfigureServices` . Uma instância do serviço está disponível em todo o aplicativo por meio de [injeção de dependência (di)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

A [`@inject`](xref:mvc/views/razor#inject) diretiva é usada para injetar a instância do `WeatherForecastService` serviço no `FetchData` componente.

*Pages/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

O componente `FetchData` usa o serviço injetado, como `ForecastService`, para recuperar uma matriz de objetos `WeatherForecast`:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>BlazorExperiência de Webassembly

Se estiver trabalhando com um Blazor aplicativo Webassembly, <xref:System.Net.Http.HttpClient> será injetado para obter dados de previsão do tempo do arquivo *Weather. JSON* na pasta *wwwroot/Sample-data* .

*Pages/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

Um [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop é usado para renderizar cada instância de previsão como uma linha na tabela de dados meteorológicos:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Criar uma lista de tarefas pendentes

Adicione um novo componente ao aplicativo que implemente uma lista de tarefas pendentes simples.

1. Adicione um novo `Todo` Razor componente ao aplicativo na pasta *páginas* . Se você estiver usando o Visual Studio, clique com o botão direito do mouse na pasta **páginas** e selecione **Adicionar**  >  **novo item**  >  ** Razor componente**. Nomeie o arquivo do componente *todo. Razor*. Em outros ambientes de desenvolvimento, adicione um arquivo em branco à pasta **páginas** chamada *todo. Razor*.

1. Forneça a marcação inicial do componente:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Adicione o componente `Todo` à barra de navegação.

   O componente `NavMenu` (*Shared/NavMenu.razor*) é usado no layout do aplicativo. Layouts são componentes que permitem que você evite a duplicação de conteúdo no aplicativo.

   Adicione um elemento `<NavLink>` para o componente `Todo` adicionando a seguinte marcação de item da lista abaixo dos itens existentes no arquivo *Shared/NavMenu.razor*:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Recompile e execute o aplicativo. Visite a nova página de Tarefas Pendentes para confirmar se o link para o componente `Todo` está funcionando.

1. Adicione um arquivo *TodoItem.cs* à raiz do projeto para manter uma classe que representará um item de tarefa pendente. Use o seguinte código C# para a classe `TodoItem`:

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Retorne para o componente `Todo` (*Pages/Todo.razor*):

   * Adicione um campo para os itens de tarefas pendentes em um bloco `@code`. O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.
   * Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista (`<li>`).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. O aplicativo requer elementos de interface do usuário para adicionar itens de tarefas à lista. Adicione uma entrada de texto (`<input>`) e um botão (`<button>`) abaixo da lista não ordenada (`<ul>...</ul>`):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Recompile e execute o aplicativo. Nada acontece quando o botão **Adicionar tarefas pendentes** é selecionado porque nenhum manipulador de eventos está conectado ao botão.

1. Adicione um método `AddTodo` ao componente `Todo` e registre-o para seleções de botão usando o atributo `@onclick`. O método C# `AddTodo` é chamado quando o botão é selecionado:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` na parte superior de `@code`, bloqueie e associe-o ao valor da próxima entrada de texto usando o atributo `bind` no elemento `<input>`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista. Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Recompile e execute o aplicativo. Adicione alguns itens de tarefas à lista de tarefas para testar o novo código.

1. O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos. Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`. Altere `@todo.Title` para um elemento `<input>` associado a `@todo.Title`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Para verificar se esses valores estão associados, atualize o cabeçalho `<h3>` para mostrar uma contagem do número de itens de tarefa pendente que não estão concluídos (`IsDone` é `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. O componente `Todo` concluído (*Pages/Todo.razor*):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Recompile e execute o aplicativo. Adicione itens de tarefa pendente para testar o novo código.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um projeto de aplicativo de lista de tarefas Blazor
> * Modificar Razor componentes
> * Usar manipulação de eventos e vinculação de dados em componentes
> * Usar injeção de dependência (DI) e roteamento em um Blazor aplicativo

Saiba como criar e usar componentes:

> [!div class="nextstepaction"]
> <xref:blazor/components>

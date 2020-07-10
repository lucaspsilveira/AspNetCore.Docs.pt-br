---
title: Compilar um Blazor aplicativo de lista de tarefas pendentes
author: guardrex
description: Crie um Blazor aplicativo passo a passo.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 174a8e561701bb3ebd68ed05e42dfc3d70a9b450
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176233"
---
# <a name="build-a-blazor-todo-list-app"></a>Compilar um Blazor aplicativo de lista de tarefas pendentes

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Este tutorial mostra como criar e modificar um Blazor aplicativo. Você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto de aplicativo de lista de tarefas Blazor
> * Modificar Razor componentes
> * Usar manipulação de eventos e vinculação de dados em componentes
> * Usar injeção de dependência (DI) e roteamento em um Blazor aplicativo

No final deste tutorial, você terá um aplicativo de lista de tarefas em funcionamento.

1. Crie um novo Blazor aplicativo chamado `TodoList` em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   O comando anterior cria uma pasta chamada `TodoList` para manter o aplicativo. Altere os diretórios para a `TodoList` pasta com o seguinte comando:

   ```dotnetcli
   cd TodoList
   ```

1. Adicione um novo `Todo` Razor componente ao aplicativo na `Pages` pasta usando o seguinte comando:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razoros nomes de arquivo de componente exigem uma primeira letra em maiúscula, portanto, confirme se o `Todo` nome do arquivo de componente começa com uma letra maiúscula `T` .

1. No, `Pages/Todo.razor` forneça a marcação inicial para o componente:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Adicione o componente `Todo` à barra de navegação.

   O `NavMenu` componente ( `Shared/NavMenu.razor` ) é usado no layout do aplicativo. Layouts são componentes que permitem que você evite a duplicação de conteúdo no aplicativo.

   Adicione um `<NavLink>` elemento para o `Todo` componente adicionando a seguinte marcação de item de lista abaixo dos itens de lista existentes no `Shared/NavMenu.razor` arquivo:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Recompile e execute o aplicativo. Visite a nova página de Tarefas Pendentes para confirmar se o link para o componente `Todo` está funcionando.

1. Adicione um `TodoItem.cs` arquivo à raiz do projeto para conter uma classe que representa um item de tarefas pendentes. Use o seguinte código C# para a classe `TodoItem`:

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Retornar ao `Todo` componente ( `Pages/Todo.razor` ):

   * Adicione um campo para os itens de tarefas pendentes em um bloco `@code`. O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.
   * Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista (`<li>`).

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. O aplicativo requer elementos de interface do usuário para adicionar itens de tarefas à lista. Adicione uma entrada de texto (`<input>`) e um botão (`<button>`) abaixo da lista não ordenada (`<ul>...</ul>`):

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Recompile e execute o aplicativo. Quando o **`Add todo`** botão é selecionado, nada acontece porque um manipulador de eventos não está conectado ao botão.

1. Adicione um método `AddTodo` ao componente `Todo` e registre-o para seleções de botão usando o atributo `@onclick`. O método C# `AddTodo` é chamado quando o botão é selecionado:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` na parte superior de `@code`, bloqueie e associe-o ao valor da próxima entrada de texto usando o atributo `bind` no elemento `<input>`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista. Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Recompile e execute o aplicativo. Adicione alguns itens de tarefas à lista de tarefas para testar o novo código.

1. O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos. Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`. Altere `@todo.Title` para um elemento `<input>` associado a `@todo.Title`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Para verificar se esses valores estão associados, atualize o cabeçalho `<h3>` para mostrar uma contagem do número de itens de tarefa pendente que não estão concluídos (`IsDone` é `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. O `Todo` componente concluído ( `Pages/Todo.razor` ):

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Recompile e execute o aplicativo. Adicione itens de tarefa pendente para testar o novo código.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um projeto de aplicativo de lista de tarefas Blazor
> * Modificar Razor componentes
> * Usar manipulação de eventos e vinculação de dados em componentes
> * Usar injeção de dependência (DI) e roteamento em um Blazor aplicativo

Saiba mais sobre as ferramentas para ASP.NET Core Blazor :

> [!div class="nextstepaction"]
> <xref:blazor/tooling>

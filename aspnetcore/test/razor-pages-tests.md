---
title: Razor Pages testes de unidade no núcleo ASP.NET
author: rick-anderson
description: Aprenda a criar testes unitários para aplicativos Razor Pages.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: 0e217b6b7f15519a3da44f5d074cf80fa96a3b3a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664405"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Razor Pages testes de unidade no núcleo ASP.NET

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core suporta testes unitários de aplicativos Razor Pages. Testes da camada de acesso a dados (DAL) e modelos de página ajudam a garantir:

* Partes de um aplicativo Razor Pages funcionam de forma independente e em conjunto como uma unidade durante a construção do aplicativo.
* Classes e métodos têm escopos limitados de responsabilidade.
* Existe documentação adicional sobre como o aplicativo deve se comportar.
* Regressões, que são erros causados por atualizações do código, são encontradas durante a construção e implantação automatizadas.

Este tópico pressupõe que você tenha uma compreensão básica dos aplicativos de Páginas de Barbear e testes unitários. Se você não estiver familiarizado com aplicativos de Páginas de Barbear ou conceitos de teste, veja os seguintes tópicos:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [C# de teste de unidade no .NET Core usando dotnet test e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([como baixar](xref:index#how-to-download-a-sample))

O projeto amostral é composto por dois aplicativos:

| Aplicativo         | Pasta do projeto                     | Descrição |
| ----------- | ---------------------------------- | ----------- |
| Aplicativo de mensagens | *src/RazorPagesTestSample*         | Permite que um usuário adicione uma mensagem, exclua uma mensagem, exclua todas as mensagens e analise mensagens (encontre o número médio de palavras por mensagem). |
| Aplicativo de teste    | *testes/RazorPagesTestSample.Testes* | Usado para testar o modelo de página DAL e Index do aplicativo de mensagem. |

Os testes podem ser executados usando os recursos de teste incorporados de um IDE, como [visual studio](/visualstudio/test/unit-test-your-code) ou visual studio [para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Se estiver usando [o Visual Studio Code](https://code.visualstudio.com/) ou a linha de comando, execute o seguinte comando em um prompt de comando na pasta *testes/RazorPagesTestSample.Tests:*

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organização do aplicativo de mensagens

O aplicativo de mensagem é um sistema de mensagens Razor Pages com as seguintes características:

* A página Índice do aplicativo (*Páginas/Index.cshtml* e *Páginas/Index.cshtml.cs*) fornece métodos de modelo de ui e página para controlar a adição, exclusão e análise de mensagens (encontre o número médio de palavras por mensagem).
* Uma mensagem é `Message` descrita pela classe *(Data/Message.cs*) com duas propriedades: `Id` (chave) e `Text` (mensagem). A `Text` propriedade é necessária e limitada a 200 caracteres.
* As mensagens são armazenadas usando [o banco de dados de memória](/ef/core/providers/in-memory/) do Entity Framework&#8224;.
* O aplicativo contém um DAL em `AppDbContext` sua classe de contexto de banco de dados (*Data/AppDbContext.cs*). Os métodos DAL `virtual`são marcados, o que permite zombar dos métodos de uso nos testes.
* Se o banco de dados estiver vazio na inicialização do aplicativo, a armazenamento de mensagens será inicializada com três mensagens. Essas *mensagens semeadas* também são usadas em testes.

&#8224;O tópico EF, [Teste com a Memória,](/ef/core/miscellaneous/testing/in-memory)explica como usar um banco de dados na memória para testes com o MSTest. Este tópico usa a estrutura de teste [xUnit.](https://xunit.github.io/) Os conceitos de teste e implementações de teste em diferentes estruturas de teste são semelhantes, mas não idênticos.

Embora o aplicativo de amostra não use o padrão de repositório e não seja um exemplo eficaz do [padrão Unit of Work (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)razor pages suporta esses padrões de desenvolvimento. Para obter mais informações, consulte [Projetar a camada de persistência da infra-estrutura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e <xref:mvc/controllers/testing> (a amostra implementa o padrão do repositório).

## <a name="test-app-organization"></a>Organização do aplicativo de teste

O aplicativo de teste é um aplicativo de console dentro da pasta *testes/RazorPagesTestSample.Tests.*

| Pasta do aplicativo de teste | Descrição |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* contém os testes unitários para o DAL.</li><li>*IndexPageTests.cs* contém os testes unitários para o modelo de página Índice.</li></ul> |
| *Utilidades*     | Contém `TestDbContextOptions` o método usado para criar novas opções de contexto de banco de dados para cada teste de unidade DAL para que o banco de dados seja redefinido para sua condição de base para cada teste. |

A estrutura de teste é [xUnit](https://xunit.github.io/). A estrutura de zombaria do objeto é [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testes unitários da camada de acesso a dados (DAL)

O aplicativo de mensagem possui um `AppDbContext` DAL com quatro métodos contidos na classe (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Cada método tem um ou dois testes unitários no aplicativo de teste.

| Método DAL               | Função                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Obtém `List<Message>` um do banco de `Text` dados classificado pela propriedade. |
| `AddMessageAsync`        | Adiciona `Message` um ao banco de dados.                                          |
| `DeleteAllMessagesAsync` | Exclui todas `Message` as entradas do banco de dados.                           |
| `DeleteMessageAsync`     | Exclui um `Message` único do `Id`banco de dados por .                      |

Os testes unitários do DAL requerem <xref:Microsoft.EntityFrameworkCore.DbContextOptions> ao criar um novo `AppDbContext` para cada teste. Uma abordagem `DbContextOptions` para criar o para <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>cada teste é usar um:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

O problema com essa abordagem é que cada teste recebe o banco de dados em qualquer estado que o teste anterior o deixou. Isso pode ser problemático ao tentar escrever testes de unidades atômicas que não interferem uns com os outros. Para forçar `AppDbContext` o uso de um novo contexto `DbContextOptions` de banco de dados para cada teste, forneça uma instância baseada em um novo provedor de serviços. O aplicativo de teste mostra `Utilities` como `TestDbContextOptions` fazer isso usando seu método de classe (*testes/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

O `DbContextOptions` uso dos testes da unidade DAL permite que cada teste execute atomicamente com uma nova instância de banco de dados:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Cada método de `DataAccessLayerTest` teste da classe *(UnitTests/DataAccessLayerTest.cs)* segue um padrão semelhante de Arrange-Act-Assert:

1. Organizar: O banco de dados é configurado para o teste e/ou o resultado esperado é definido.
1. O teste é executado.
1. Afirmar: As afirmações são feitas para determinar se o resultado do teste é um sucesso.

Por exemplo, `DeleteMessageAsync` o método é responsável pela remoção de uma única mensagem identificada por sua `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Há dois testes para este método. Um teste verifica se o método exclui uma mensagem quando a mensagem está presente no banco de dados. O outro método testa que o banco `Id` de dados não muda se a mensagem para exclusão não existir. O `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` método é mostrado abaixo:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Primeiro, o método realiza a etapa Organizar, onde ocorre a preparação para a etapa do Ato. As mensagens de semeação `seedMessages`são obtidas e mantidas em . As mensagens de semeação são salvas no banco de dados. A mensagem `Id` `1` com um de um é definido para exclusão. Quando `DeleteMessageAsync` o método é executado, as mensagens esperadas devem ter todas `Id` `1`as mensagens, exceto aquela com um de . A `expectedMessages` variável representa esse resultado esperado.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

O método age: O `DeleteMessageAsync` método é `recId` `1`executado passando no de:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Finalmente, o método `Messages` obtém o do contexto `expectedMessages` e o compara com a afirmação de que os dois são iguais:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Para comparar que os `List<Message>` dois são os mesmos:

* As mensagens são `Id`ordenadas por .
* Os pares de mensagens são comparados na `Text` propriedade.

Um método de `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` teste semelhante, verifica o resultado de tentar excluir uma mensagem que não existe. Neste caso, as mensagens esperadas no banco de dados `DeleteMessageAsync` devem ser iguais às mensagens reais após a execução do método. Não deve haver alteração no conteúdo do banco de dados:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testes unitários dos métodos do modelo de página

Outro conjunto de testes unitários é responsável por testes de métodos de modelo de página. No aplicativo de mensagem, os modelos de página Índice são encontrados na `IndexModel` classe em *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Método do modelo de página | Função |
| ----------------- | -------- |
| `OnGetAsync` | Obtém as mensagens do DAL para `GetMessagesAsync` a ui usando o método. |
| `OnPostAddMessageAsync` | Se o [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) for `AddMessageAsync` válido, ligue para adicionar uma mensagem ao banco de dados. |
| `OnPostDeleteAllMessagesAsync` | Chamadas `DeleteAllMessagesAsync` para excluir todas as mensagens no banco de dados. |
| `OnPostDeleteMessageAsync` | Executa `DeleteMessageAsync` para excluir uma `Id` mensagem com o especificado. |
| `OnPostAnalyzeMessagesAsync` | Se uma ou mais mensagens estiverem no banco de dados, calcule o número médio de palavras por mensagem. |

Os métodos do modelo de página `IndexPageTests` são testados usando sete testes na classe (*testes/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). Os testes usam o padrão familiar de "Arrange-Assert-Act". Estes testes se concentram em:

* Determinar se os métodos seguem o comportamento correto quando o [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) é inválido.
* Confirmar os métodos <xref:Microsoft.AspNetCore.Mvc.IActionResult>produz o correto .
* Verificando se as atribuições de valor da propriedade são feitas corretamente.

Este grupo de testes muitas vezes zomba dos métodos do DAL para produzir dados esperados para a etapa act onde um método de modelo de página é executado. Por exemplo, `GetMessagesAsync` o `AppDbContext` método do é ridicularizado para produzir saída. Quando um método de modelo de página executa esse método, o simulado retorna o resultado. Os dados não vêm do banco de dados. Isso cria condições de teste previsíveis e confiáveis para o uso do DAL nos testes do modelo de página.

O `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` teste mostra `GetMessagesAsync` como o método é ridicularizado para o modelo de página:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Quando `OnGetAsync` o método é executado na etapa Act, ele `GetMessagesAsync` chama o método do modelo de página.

Passo da lei do teste unitário *(testes/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`método do `OnGetAsync` modelo de página *(src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

O `GetMessagesAsync` método no DAL não retorna o resultado para esta chamada de método. A versão simulada do método retorna o resultado.

Na `Assert` etapa, as mensagens`actualMessages`reais () `Messages` são atribuídas da propriedade do modelo de página. Uma verificação de tipo também é realizada quando as mensagens são atribuídas. As mensagens esperadas e `Text` reais são comparadas por suas propriedades. O teste afirma que `List<Message>` as duas instâncias contêm as mesmas mensagens.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Outros testes neste grupo criam objetos <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>modelo <xref:Microsoft.AspNetCore.Mvc.ActionContext> de `PageContext`página `ViewDataDictionary`que incluem `PageContext`o , o , um para estabelecer o , a , e a . Estes são úteis na realização de testes. Por exemplo, o aplicativo `ModelState` de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> mensagem estabelece <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> um erro `OnPostAddMessageAsync` com a verificação de que um válido é devolvido quando é executado:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Recursos adicionais

* [C# de teste de unidade no .NET Core usando dotnet test e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Teste unitário seu código](/visualstudio/test/unit-test-your-code) (estúdio visual)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Compilar uma solução completa do .NET Core no macOS usando o Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Começando com xUnit.net: Usando o .NET Core com a linha de comando .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core suporta testes unitários de aplicativos Razor Pages. Testes da camada de acesso a dados (DAL) e modelos de página ajudam a garantir:

* Partes de um aplicativo Razor Pages funcionam de forma independente e em conjunto como uma unidade durante a construção do aplicativo.
* Classes e métodos têm escopos limitados de responsabilidade.
* Existe documentação adicional sobre como o aplicativo deve se comportar.
* Regressões, que são erros causados por atualizações do código, são encontradas durante a construção e implantação automatizadas.

Este tópico pressupõe que você tenha uma compreensão básica dos aplicativos de Páginas de Barbear e testes unitários. Se você não estiver familiarizado com aplicativos de Páginas de Barbear ou conceitos de teste, veja os seguintes tópicos:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [C# de teste de unidade no .NET Core usando dotnet test e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([como baixar](xref:index#how-to-download-a-sample))

O projeto amostral é composto por dois aplicativos:

| Aplicativo         | Pasta do projeto                     | Descrição |
| ----------- | ---------------------------------- | ----------- |
| Aplicativo de mensagens | *src/RazorPagesTestSample*         | Permite que um usuário adicione uma mensagem, exclua uma mensagem, exclua todas as mensagens e analise mensagens (encontre o número médio de palavras por mensagem). |
| Aplicativo de teste    | *testes/RazorPagesTestSample.Testes* | Usado para testar o modelo de página DAL e Index do aplicativo de mensagem. |

Os testes podem ser executados usando os recursos de teste incorporados de um IDE, como [visual studio](/visualstudio/test/unit-test-your-code) ou visual studio [para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Se estiver usando [o Visual Studio Code](https://code.visualstudio.com/) ou a linha de comando, execute o seguinte comando em um prompt de comando na pasta *testes/RazorPagesTestSample.Tests:*

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organização do aplicativo de mensagens

O aplicativo de mensagem é um sistema de mensagens Razor Pages com as seguintes características:

* A página Índice do aplicativo (*Páginas/Index.cshtml* e *Páginas/Index.cshtml.cs*) fornece métodos de modelo de ui e página para controlar a adição, exclusão e análise de mensagens (encontre o número médio de palavras por mensagem).
* Uma mensagem é `Message` descrita pela classe *(Data/Message.cs*) com duas propriedades: `Id` (chave) e `Text` (mensagem). A `Text` propriedade é necessária e limitada a 200 caracteres.
* As mensagens são armazenadas usando [o banco de dados de memória](/ef/core/providers/in-memory/) do Entity Framework&#8224;.
* O aplicativo contém um DAL em `AppDbContext` sua classe de contexto de banco de dados (*Data/AppDbContext.cs*). Os métodos DAL `virtual`são marcados, o que permite zombar dos métodos de uso nos testes.
* Se o banco de dados estiver vazio na inicialização do aplicativo, a armazenamento de mensagens será inicializada com três mensagens. Essas *mensagens semeadas* também são usadas em testes.

&#8224;O tópico EF, [Teste com a Memória,](/ef/core/miscellaneous/testing/in-memory)explica como usar um banco de dados na memória para testes com o MSTest. Este tópico usa a estrutura de teste [xUnit.](https://xunit.github.io/) Os conceitos de teste e implementações de teste em diferentes estruturas de teste são semelhantes, mas não idênticos.

Embora o aplicativo de amostra não use o padrão de repositório e não seja um exemplo eficaz do [padrão Unit of Work (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)razor pages suporta esses padrões de desenvolvimento. Para obter mais informações, consulte [Projetar a camada de persistência da infra-estrutura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e <xref:mvc/controllers/testing> (a amostra implementa o padrão do repositório).

## <a name="test-app-organization"></a>Organização do aplicativo de teste

O aplicativo de teste é um aplicativo de console dentro da pasta *testes/RazorPagesTestSample.Tests.*

| Pasta do aplicativo de teste | Descrição |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* contém os testes unitários para o DAL.</li><li>*IndexPageTests.cs* contém os testes unitários para o modelo de página Índice.</li></ul> |
| *Utilidades*     | Contém `TestDbContextOptions` o método usado para criar novas opções de contexto de banco de dados para cada teste de unidade DAL para que o banco de dados seja redefinido para sua condição de base para cada teste. |

A estrutura de teste é [xUnit](https://xunit.github.io/). A estrutura de zombaria do objeto é [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testes unitários da camada de acesso a dados (DAL)

O aplicativo de mensagem possui um `AppDbContext` DAL com quatro métodos contidos na classe (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Cada método tem um ou dois testes unitários no aplicativo de teste.

| Método DAL               | Função                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Obtém `List<Message>` um do banco de `Text` dados classificado pela propriedade. |
| `AddMessageAsync`        | Adiciona `Message` um ao banco de dados.                                          |
| `DeleteAllMessagesAsync` | Exclui todas `Message` as entradas do banco de dados.                           |
| `DeleteMessageAsync`     | Exclui um `Message` único do `Id`banco de dados por .                      |

Os testes unitários do DAL requerem <xref:Microsoft.EntityFrameworkCore.DbContextOptions> ao criar um novo `AppDbContext` para cada teste. Uma abordagem `DbContextOptions` para criar o para <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>cada teste é usar um:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

O problema com essa abordagem é que cada teste recebe o banco de dados em qualquer estado que o teste anterior o deixou. Isso pode ser problemático ao tentar escrever testes de unidades atômicas que não interferem uns com os outros. Para forçar `AppDbContext` o uso de um novo contexto `DbContextOptions` de banco de dados para cada teste, forneça uma instância baseada em um novo provedor de serviços. O aplicativo de teste mostra `Utilities` como `TestDbContextOptions` fazer isso usando seu método de classe (*testes/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

O `DbContextOptions` uso dos testes da unidade DAL permite que cada teste execute atomicamente com uma nova instância de banco de dados:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Cada método de `DataAccessLayerTest` teste da classe *(UnitTests/DataAccessLayerTest.cs)* segue um padrão semelhante de Arrange-Act-Assert:

1. Organizar: O banco de dados é configurado para o teste e/ou o resultado esperado é definido.
1. O teste é executado.
1. Afirmar: As afirmações são feitas para determinar se o resultado do teste é um sucesso.

Por exemplo, `DeleteMessageAsync` o método é responsável pela remoção de uma única mensagem identificada por sua `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Há dois testes para este método. Um teste verifica se o método exclui uma mensagem quando a mensagem está presente no banco de dados. O outro método testa que o banco `Id` de dados não muda se a mensagem para exclusão não existir. O `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` método é mostrado abaixo:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Primeiro, o método realiza a etapa Organizar, onde ocorre a preparação para a etapa do Ato. As mensagens de semeação `seedMessages`são obtidas e mantidas em . As mensagens de semeação são salvas no banco de dados. A mensagem `Id` `1` com um de um é definido para exclusão. Quando `DeleteMessageAsync` o método é executado, as mensagens esperadas devem ter todas `Id` `1`as mensagens, exceto aquela com um de . A `expectedMessages` variável representa esse resultado esperado.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

O método age: O `DeleteMessageAsync` método é `recId` `1`executado passando no de:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Finalmente, o método `Messages` obtém o do contexto `expectedMessages` e o compara com a afirmação de que os dois são iguais:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Para comparar que os `List<Message>` dois são os mesmos:

* As mensagens são `Id`ordenadas por .
* Os pares de mensagens são comparados na `Text` propriedade.

Um método de `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` teste semelhante, verifica o resultado de tentar excluir uma mensagem que não existe. Neste caso, as mensagens esperadas no banco de dados `DeleteMessageAsync` devem ser iguais às mensagens reais após a execução do método. Não deve haver alteração no conteúdo do banco de dados:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testes unitários dos métodos do modelo de página

Outro conjunto de testes unitários é responsável por testes de métodos de modelo de página. No aplicativo de mensagem, os modelos de página Índice são encontrados na `IndexModel` classe em *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Método do modelo de página | Função |
| ----------------- | -------- |
| `OnGetAsync` | Obtém as mensagens do DAL para `GetMessagesAsync` a ui usando o método. |
| `OnPostAddMessageAsync` | Se o [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) for `AddMessageAsync` válido, ligue para adicionar uma mensagem ao banco de dados. |
| `OnPostDeleteAllMessagesAsync` | Chamadas `DeleteAllMessagesAsync` para excluir todas as mensagens no banco de dados. |
| `OnPostDeleteMessageAsync` | Executa `DeleteMessageAsync` para excluir uma `Id` mensagem com o especificado. |
| `OnPostAnalyzeMessagesAsync` | Se uma ou mais mensagens estiverem no banco de dados, calcule o número médio de palavras por mensagem. |

Os métodos do modelo de página `IndexPageTests` são testados usando sete testes na classe (*testes/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). Os testes usam o padrão familiar de "Arrange-Assert-Act". Estes testes se concentram em:

* Determinar se os métodos seguem o comportamento correto quando o [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) é inválido.
* Confirmar os métodos <xref:Microsoft.AspNetCore.Mvc.IActionResult>produz o correto .
* Verificando se as atribuições de valor da propriedade são feitas corretamente.

Este grupo de testes muitas vezes zomba dos métodos do DAL para produzir dados esperados para a etapa act onde um método de modelo de página é executado. Por exemplo, `GetMessagesAsync` o `AppDbContext` método do é ridicularizado para produzir saída. Quando um método de modelo de página executa esse método, o simulado retorna o resultado. Os dados não vêm do banco de dados. Isso cria condições de teste previsíveis e confiáveis para o uso do DAL nos testes do modelo de página.

O `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` teste mostra `GetMessagesAsync` como o método é ridicularizado para o modelo de página:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Quando `OnGetAsync` o método é executado na etapa Act, ele `GetMessagesAsync` chama o método do modelo de página.

Passo da lei do teste unitário *(testes/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`método do `OnGetAsync` modelo de página *(src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

O `GetMessagesAsync` método no DAL não retorna o resultado para esta chamada de método. A versão simulada do método retorna o resultado.

Na `Assert` etapa, as mensagens`actualMessages`reais () `Messages` são atribuídas da propriedade do modelo de página. Uma verificação de tipo também é realizada quando as mensagens são atribuídas. As mensagens esperadas e `Text` reais são comparadas por suas propriedades. O teste afirma que `List<Message>` as duas instâncias contêm as mesmas mensagens.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Outros testes neste grupo criam objetos <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>modelo <xref:Microsoft.AspNetCore.Mvc.ActionContext> de `PageContext`página `ViewDataDictionary`que incluem `PageContext`o , o , um para estabelecer o , a , e a . Estes são úteis na realização de testes. Por exemplo, o aplicativo `ModelState` de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> mensagem estabelece <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> um erro `OnPostAddMessageAsync` com a verificação de que um válido é devolvido quando é executado:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Recursos adicionais

* [C# de teste de unidade no .NET Core usando dotnet test e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Teste unitário seu código](/visualstudio/test/unit-test-your-code) (estúdio visual)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Compilar uma solução completa do .NET Core no macOS usando o Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Começando com xUnit.net: Usando o .NET Core com a linha de comando .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

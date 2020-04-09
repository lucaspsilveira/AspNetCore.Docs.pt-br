---
title: Use LibMan com ASP.NET Core no Visual Studio
author: scottaddie
description: Aprenda a usar o LibMan em um projeto ASP.NET Core com o Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658308"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Use LibMan com ASP.NET Core no Visual Studio

Por [Scott Addie](https://twitter.com/Scott_Addie)

O Visual Studio tem suporte integrado para [o LibMan](xref:client-side/libman/index) em projetos ASP.NET Core, incluindo:

* Suporte para configurar e executar operações de restauração libman na compilação.
* Itens do menu para ativar as operações de restauração e limpeza do LibMan.
* Pesquise a caixa de diálogo para encontrar bibliotecas e adicionar os arquivos a um projeto.
* Suporte de edição para *libman.json*&mdash;o arquivo manifesto LibMan.

[Exibir ou baixar o código da amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **carga de trabalho de ASP.NET e desenvolvimento web**

## <a name="add-library-files"></a>Adicionar arquivos de biblioteca

Os arquivos da biblioteca podem ser adicionados a um projeto ASP.NET Core de duas maneiras diferentes:

1. [Use a caixa de diálogo Adicionar biblioteca do lado do cliente](#use-the-add-client-side-library-dialog)
1. [Configurar manualmente entradas de arquivo manifesto LibMan](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Use a caixa de diálogo Adicionar biblioteca do lado do cliente

Siga estas etapas para instalar uma biblioteca do lado do cliente:

* No **Solution Explorer,** clique com o botão direito do mouse na pasta de projeto na qual os arquivos devem ser adicionados. Escolha **adicionar** > **biblioteca ao lado do cliente**. A caixa de diálogo **Adicionar biblioteca do lado** do cliente é exibida:

  ![Adicionar diálogo da biblioteca do lado do cliente](_static/add-library-dialog.png)

* Selecione o provedor de biblioteca saqueado do **Provedor.** O CDNJS é o provedor padrão.
* Digite o nome da biblioteca para buscar na caixa de texto da **Biblioteca.** O IntelliSense fornece uma lista de bibliotecas que começam com o texto fornecido.
* Selecione a biblioteca na lista IntelliSense. Observe que o nome da `@` biblioteca está sufixo com o símbolo e a versão estável mais recente conhecida pelo provedor selecionado.
* Decida quais arquivos incluir:
  * Selecione o botão **Incluir todos os arquivos** de biblioteca para incluir todos os arquivos da biblioteca.
  * Selecione o botão **Desescolher arquivos específicos** para incluir um subconjunto dos arquivos da biblioteca. Quando o botão de rádio é selecionado, a árvore seletora de arquivos é ativada. Verifique as caixas à esquerda dos nomes dos arquivos para baixar.
* Especifique a pasta de projeto para armazenar os arquivos na caixa de texto **Local de** destino. Como recomendação, armazene cada biblioteca em uma pasta separada.

  A pasta **de localização de destino** sugerida é baseada no local a partir do qual a caixa de diálogo foi lançada:

  * Se lançado a partir da raiz do projeto:
    * *wwwroot/lib* é usado se *wwwroot* existir.
    * *lib* é usado se *wwwroot* não existe.
  * Se for lançado a partir de uma pasta de projeto, o nome da pasta correspondente será usado.

  A sugestão da pasta é sufixada com o nome da biblioteca. A tabela a seguir ilustra sugestões de pastas ao instalar jQuery em um projeto Razor Pages.
  
  |Local de lançamento                           |Pasta sugerida      |
  |------------------------------------------|----------------------|
  |raiz do projeto (se *wwwroot* existe)        |*wwwroot/lib/jquery/* |
  |raiz do projeto (se *wwwroot* não existe) |*lib/jquery/*         |
  |*Pasta páginas* no projeto                 |*Páginas/jquery/*       |

* Clique no botão **Instalar** para baixar os arquivos, de acordo com a configuração em *libman.json*.
* Revise o feed do **Gerenciador** de bibliotecas da janela **Saída** para obter detalhes da instalação. Por exemplo:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>Configurar manualmente entradas de arquivo manifesto LibMan

Todas as operações da LibMan no Visual Studio são baseadas no conteúdo do manifesto LibMan da raiz do projeto *(libman.json).* Você pode editar manualmente *libman.json* para configurar arquivos de biblioteca para o projeto. O Visual Studio restaura todos os arquivos da biblioteca assim *que libman.json* for salvo.

Para abrir *libman.json* para edição, existem as seguintes opções:

* Clique duas vezes no arquivo *libman.json* no **Solution Explorer**.
* Clique com o botão direito do mouse no projeto no **Solution Explorer** e **selecione Gerenciar bibliotecas lado do cliente**. **&#8224;**
* Selecione **Gerenciar bibliotecas lado do cliente** no menu Visual Studio **Project.** **&#8224;**

**&#8224;&#8224;.** Se o arquivo *libman.json* ainda não existir na raiz do projeto, ele será criado com o conteúdo do modelo de item padrão.

O Visual Studio oferece suporte de edição JSON rico, como coloração, formatação, IntelliSense e validação de esquemas. O esquema JSON do manifesto LibMan [https://json.schemastore.org/libman](https://json.schemastore.org/libman)é encontrado em .

Com o seguinte arquivo manifesto, libman recupera arquivos `libraries` de acordo com a configuração definida na propriedade. Segue-se uma explicação dos `libraries` literais do objeto definidos a seguir:

* Um subconjunto da versão 3.3.1 [do JQuery](https://jquery.com/) é recuperado do provedor CDNJS. O subconjunto é `files` definido&mdash;na propriedade*jquery.min.js*, *jquery.js*e *jquery.min.map*. Os arquivos são colocados na pasta *wwwroot/lib/jquery* do projeto.
* A totalidade da versão 4.1.3 do [Bootstrap](https://getbootstrap.com/) é recuperada e colocada em uma pasta *wwwroot/lib/bootstrap.* A propriedade do `provider` objeto literal `defaultProvider` substitui o valor da propriedade. LibMan recupera os arquivos Bootstrap do provedor unpkg.
* Um subconjunto de [Lodash](https://lodash.com/) foi aprovado por um órgão governamental dentro da organização. Os arquivos *lodash.js* e *lodash.min.js* são recuperados do sistema de arquivos local em *C:\\temp\\lodash\\*. Os arquivos são copiados para a pasta *wwwroot/lib/lodash* do projeto.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan suporta apenas uma versão de cada biblioteca de cada provedor. O arquivo *libman.json* falha na validação do esquema se contiver duas bibliotecas com o mesmo nome de biblioteca para um determinado provedor.

## <a name="restore-library-files"></a>Restaurar arquivos da biblioteca

Para restaurar arquivos de biblioteca de dentro do Visual Studio, deve haver um arquivo *libman.json* válido na raiz do projeto. Os arquivos restaurados são colocados no projeto no local especificado para cada biblioteca.

Os arquivos da biblioteca podem ser restaurados em um projeto ASP.NET Core de duas maneiras:

1. [Restaurar arquivos durante a compilação](#restore-files-during-build)
1. [Restaurar arquivos manualmente](#restore-files-manually)

### <a name="restore-files-during-build"></a>Restaurar arquivos durante a compilação

LibMan pode restaurar os arquivos de biblioteca definidos como parte do processo de compilação. Por padrão, o comportamento *de restauração na compilação* é desativado.

Para habilitar e testar o comportamento de restauração na compilação:

* Clique com o botão direito do mouse *libman.json* no **Solution Explorer** e **selecione Ativar restaurar bibliotecas do lado do cliente na compilação** a partir do menu de contexto.
* Clique no botão **Sim** quando solicitado a instalar um pacote NuGet. O pacote [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet é adicionado ao projeto:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Construa o projeto para confirmar a restauração do arquivo LibMan. O `Microsoft.Web.LibraryManager.Build` pacote injeta um alvo MSBuild que executa o LibMan durante a operação de compilação do projeto.
* Revise o feed **Build** da **janela Saída** para um registro de atividade libman:

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

Quando o comportamento de restauração na compilação é ativado, o menu de contexto *libman.json* exibe uma opção **Desativar Restaurar bibliotecas do lado do cliente na** opção Build. A seleção desta `Microsoft.Web.LibraryManager.Build` opção remove a referência do pacote do arquivo do projeto. Consequentemente, as bibliotecas do lado do cliente não são mais restauradas em cada compilação.

Independentemente da configuração de restauração na compilação, você pode restaurar manualmente a qualquer momento a partir do menu de contexto *libman.json.* Para obter mais informações, consulte [Restaurar arquivos manualmente](#restore-files-manually).

### <a name="restore-files-manually"></a>Restaurar arquivos manualmente

Para restaurar manualmente arquivos da biblioteca:

* Para todos os projetos na solução:
  * Clique com o botão direito do mouse no nome da solução no **Solution Explorer**.
  * Selecione a opção **Restaurar bibliotecas do lado do cliente.**
* Para um projeto específico:
  * Clique com o botão direito do mouse no arquivo *libman.json* no **Solution Explorer**.
  * Selecione a opção **Restaurar bibliotecas do lado do cliente.**

Enquanto a operação de restauração estiver em execução:

* O ícone tsc (Task Status Center) na barra de status do Visual Studio será animado e lerá *a operação Restaurar iniciada*. Clicar no ícone abre uma dica de ferramenta listando as tarefas em segundo plano conhecidas.
* As mensagens serão enviadas para a barra de status e para o feed do Gerenciador de **bibliotecas** da janela **Saída.** Por exemplo:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>Excluir arquivos da biblioteca

Para executar a operação *limpa,* que exclui arquivos de biblioteca previamente restaurados no Visual Studio:

* Clique com o botão direito do mouse no arquivo *libman.json* no **Solution Explorer**.
* Selecione a opção **Bibliotecas do Lado do Cliente Limpo.**

Para evitar a remoção não intencional de arquivos não-biblioteca, a operação limpa não exclui diretórios inteiros. Ele só remove arquivos que foram incluídos na restauração anterior.

Enquanto a operação limpa estiver em execução:

* O ícone TSC na barra de status do Visual Studio será animado e lerá *a operação bibliotecas clientes iniciada*. Clicar no ícone abre uma dica de ferramenta listando as tarefas em segundo plano conhecidas.
* As mensagens são enviadas para a barra de status e para o feed do **Gerenciador** de bibliotecas da janela **Saída.** Por exemplo:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

A operação limpa só exclui arquivos do projeto. Os arquivos da biblioteca permanecem no cache para uma recuperação mais rápida em futuras operações de restauração. Para gerenciar arquivos de biblioteca armazenados no cache da máquina local, use o [Cli LibMan](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Desinstalar arquivos de biblioteca

Para desinstalar arquivos de biblioteca:

* Abra *libman.json.*
* Posicione o cuidado `libraries` dentro do objeto correspondente literal.
* Clique no ícone da lâmpada que aparece na margem esquerda e selecione **Desinstalar \<library_name>@\<library_version>**:

  ![Desinstale a opção menu de contexto da biblioteca](_static/uninstall-menu-option.png)

Alternativamente, você pode editar e salvar manualmente o manifesto LibMan *(libman.json*). A [operação de restauração](#restore-library-files) é executada quando o arquivo é salvo. Os arquivos da biblioteca que não estão mais definidos em *libman.json* são removidos do projeto.

## <a name="update-library-version"></a>Atualizar versão da biblioteca

Para verificar uma versão atualizada da biblioteca:

* Abra *libman.json.*
* Posicione o cuidado `libraries` dentro do objeto correspondente literal.
* Clique no ícone da lâmpada que aparece na margem esquerda. Passar o tempo **verifique se há atualizações**.

LibMan verifica uma versão de biblioteca mais recente do que a versão instalada. Os seguintes resultados podem ocorrer:

* Uma mensagem **Sem atualizações encontradas** será exibida se a versão mais recente já estiver instalada.
* A versão estável mais recente é exibida se não estiver instalada.

  ![Verifique se há atualizações opção de menu de contexto](_static/update-menu-option.png)

* Se uma versão mais recente do que a versão instalada estiver disponível, a pré-versão será exibida.

Para fazer o downgrade para uma versão de biblioteca mais antiga, edite manualmente o arquivo *libman.json.* Quando o arquivo é salvo, a [operação de restauração](#restore-library-files)LibMan:

* Remove arquivos redundantes da versão anterior.
* Adiciona arquivos novos e atualizados da nova versão.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:client-side/libman/libman-cli>
* [Repositório do GitHub do LibMan](https://github.com/aspnet/LibraryManager)

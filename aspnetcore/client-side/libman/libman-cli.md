---
title: Use o Cli LibMan com ASP.NET Núcleo
author: scottaddie
description: Aprenda a usar o LibMan CLI em um projeto ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664629"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>Use o Cli LibMan com ASP.NET Núcleo

Por [Scott Addie](https://twitter.com/Scott_Addie)

O [LibMan](xref:client-side/libman/index) CLI é uma ferramenta multiplataforma que é suportada em todos os lugares .NET Core é suportado.

## <a name="prerequisites"></a>Pré-requisitos

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Instalação

Para instalar o Cli LibMan:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

Uma [ferramenta global .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) está instalada no pacote [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet.

Para instalar o Cli LibMan a partir de uma fonte específica do pacote NuGet:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

No exemplo anterior, uma ferramenta global do .NET Core é instalada a partir do arquivo *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg.*

## <a name="usage"></a>Uso

Após a instalação bem-sucedida da CLI, o seguinte comando pode ser usado:

```console
libman
```

Para visualizar a versão CLI instalada:

```console
libman --version
```

Para visualizar os comandos CLI disponíveis:

```console
libman --help
```

O comando anterior exibe saída semelhante à seguinte:

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

As seções a seguir descrevem os comandos da CLI disponíveis.

## <a name="initialize-libman-in-the-project"></a>Inicialize libman no projeto

O `libman init` comando cria um arquivo *libman.json* se não existir. O arquivo é criado com o conteúdo do modelo de item padrão.

### <a name="synopsis"></a>Sinopse

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `libman init`:

* `-d|--default-destination <PATH>`

  Um caminho relativo à pasta atual. Os arquivos da biblioteca são `destination` instalados neste local se nenhuma propriedade for definida para uma biblioteca em *libman.json*. O `<PATH>` valor é `defaultDestination` escrito na propriedade de *libman.json*.

* `-p|--default-provider <PROVIDER>`

  O provedor a ser usado se nenhum provedor for definido para uma determinada biblioteca. O `<PROVIDER>` valor é `defaultProvider` escrito na propriedade de *libman.json*. Substitua por `<PROVIDER>` um dos seguintes valores:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Para criar um arquivo *libman.json* em um projeto ASP.NET Core:

* Navegue até a raiz do projeto.
* Execute o comando a seguir:

  ```console
  libman init
  ```

* Digite o nome do provedor `Enter` padrão ou pressione para usar o provedor CDNJS padrão. Os valores válidos incluem:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init comando - provedor padrão](_static/libman-init-provider.png)

Um arquivo *libman.json* é adicionado à raiz do projeto com o seguinte conteúdo:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Adicionar arquivos de biblioteca

O `libman install` comando baixa e instala arquivos de biblioteca no projeto. Um arquivo *libman.json* é adicionado se um não existe. O arquivo *libman.json* é modificado para armazenar detalhes de configuração para os arquivos da biblioteca.

### <a name="synopsis"></a>Sinopse

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Argumentos

`LIBRARY`

O nome da biblioteca para instalar. Este nome pode incluir notação de `@1.2.0`número de versão (por exemplo, ).

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `libman install`:

* `-d|--destination <PATH>`

  O local para instalar a biblioteca. Se não for especificado, o local padrão é usado. Se `defaultDestination` nenhuma propriedade for especificada em *libman.json,* esta opção é necessária.

* `--files <FILE>`

  Especifique o nome do arquivo a ser instalado na biblioteca. Se não for especificado, todos os arquivos da biblioteca serão instalados. Forneça `--files` uma opção por arquivo a ser instalado. Caminhos relativos também são suportados. Por exemplo: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  O nome do provedor para usar na aquisição da biblioteca. Substitua por `<PROVIDER>` um dos seguintes valores:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Se não for `defaultProvider` especificado, a propriedade em *libman.json* é usada. Se `defaultProvider` nenhuma propriedade for especificada em *libman.json,* esta opção é necessária.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Considere o seguinte arquivo *libman.json:*

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Para instalar o arquivo jQuery versão 3.2.1 *jquery.min.js* na pasta *wwwroot/scripts/jquery* usando o provedor CDNJS:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

O arquivo *libman.json* se assemelha ao seguinte:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

Para instalar os arquivos *calendar.js* e *calendar.css* de *C:\\temp\\contosoCalendar\\ * usando o provedor do sistema de arquivos:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

O seguinte prompt aparece por duas razões:

* O arquivo *libman.json* não `defaultDestination` contém uma propriedade.
* O `libman install` comando não contém `-d|--destination` a opção.

![comando de instalação libman - destino](_static/libman-install-destination.png)

Depois de aceitar o destino padrão, o arquivo *libman.json* se assemelha ao seguinte:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a>Restaurar arquivos da biblioteca

O `libman restore` comando instala arquivos de biblioteca definidos em *libman.json*. As seguintes regras se aplicam:

* Se não houver nenhum arquivo *libman.json* na raiz do projeto, um erro será retornado.
* Se uma biblioteca especificar `defaultProvider` um provedor, a propriedade em *libman.json* será ignorada.
* Se uma biblioteca especificar `defaultDestination` um destino, a propriedade em *libman.json* será ignorada.

### <a name="synopsis"></a>Sinopse

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `libman restore`:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Para restaurar os arquivos da biblioteca definidos em *libman.json*:

```console
libman restore
```

## <a name="delete-library-files"></a>Excluir arquivos da biblioteca

O `libman clean` comando exclui arquivos de biblioteca previamente restaurados via LibMan. As pastas que ficam vazias após essa operação são excluídas. As configurações associadas dos `libraries` arquivos da biblioteca na propriedade de *libman.json* não são removidas.

### <a name="synopsis"></a>Sinopse

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `libman clean`:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Para excluir arquivos de biblioteca instalados via LibMan:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Desinstalar arquivos de biblioteca

O comando `libman uninstall`:

* Exclui todos os arquivos associados à biblioteca especificada do destino em *libman.json*.
* Remove a configuração da biblioteca associada do *libman.json*.

Ocorre um erro quando:

* Não existe nenhum arquivo *libman.json* na raiz do projeto.
* A biblioteca especificada não existe.

Se mais de uma biblioteca com o mesmo nome for instalada, você será solicitado a escolher uma.

### <a name="synopsis"></a>Sinopse

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Argumentos

`LIBRARY`

O nome da biblioteca para desinstalar. Este nome pode incluir notação de `@1.2.0`número de versão (por exemplo, ).

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `libman uninstall`:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Considere o seguinte arquivo *libman.json:*

[!code-json[](samples/LibManSample/libman.json)]

* Para desinstalar o jQuery, qualquer um dos seguintes comandos será bem sucedido:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Para desinstalar os arquivos Lodash instalados através do `filesystem` provedor:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Atualizar versão da biblioteca

O `libman update` comando atualiza uma biblioteca instalada via LibMan para a versão especificada.

Ocorre um erro quando:

* Não existe nenhum arquivo *libman.json* na raiz do projeto.
* A biblioteca especificada não existe.

Se mais de uma biblioteca com o mesmo nome for instalada, você será solicitado a escolher uma.

### <a name="synopsis"></a>Sinopse

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Argumentos

`LIBRARY`

O nome da biblioteca para atualizar.

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `libman update`:

* `-pre`

  Obtenha a versão de pré-lançamento mais recente da biblioteca.

* `--to <VERSION>`

  Obtenha uma versão específica da biblioteca.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

* Para atualizar o jQuery para a versão mais recente:

  ```console
  libman update jquery
  ```

* Para atualizar jQuery para a versão 3.3.1:

  ```console
  libman update jquery --to 3.3.1
  ```

* Para atualizar o jQuery para a versão mais recente de pré-lançamento:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Gerenciar cache de biblioteca

O `libman cache` comando gerencia o cache da biblioteca LibMan. O `filesystem` provedor não usa o cache da biblioteca.

### <a name="synopsis"></a>Sinopse

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Argumentos

`PROVIDER`

Só usado `clean` com o comando. Especifica o cache do provedor para limpar. Os valores válidos incluem:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `libman cache`:

* `--files`

  Liste os nomes dos arquivos armazenados em cache.

* `--libraries`

  Liste os nomes das bibliotecas armazenadas em cache.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

* Para visualizar os nomes das bibliotecas armazenadas em cache por provedor, use um dos seguintes comandos:

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Uma saída semelhante à apresentada a seguir será exibida:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* Para exibir os nomes dos arquivos de biblioteca armazenados em cache por provedor:

  ```console
  libman cache list --files
  ```

  Uma saída semelhante à apresentada a seguir será exibida:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  Observe que a saída anterior mostra que as versões jQuery 3.2.1 e 3.3.1 estão armazenadas em cache sob o provedor CDNJS.

* Para esvaziar o cache da biblioteca para o provedor CDNJS:

  ```console
  libman cache clean cdnjs
  ```

  Depois de esvaziar o cache do `libman cache list` provedor CDNJS, o comando exibe o seguinte:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* Para esvaziar o cache de todos os provedores suportados:

  ```console
  libman cache clean
  ```

  Depois de esvaziar todos `libman cache list` os caches do provedor, o comando exibe o seguinte:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Recursos adicionais

* [Instalar uma Ferramenta Global](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [Repositório do GitHub do LibMan](https://github.com/aspnet/LibraryManager)

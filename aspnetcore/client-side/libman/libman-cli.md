---
title: Usar a CLI do LibMan com ASP.NET Core
author: scottaddie
description: Saiba como usar a CLI do LibMan em um projeto ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: ed5dffb83a2f1a40f3d6596d23135c0fa5b6791f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403177"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>Usar a CLI do LibMan com ASP.NET Core

Por [Scott Addie](https://twitter.com/Scott_Addie)

A CLI do [LibMan](xref:client-side/libman/index) é uma ferramenta de plataforma cruzada com suporte em todo o .NET Core.

## <a name="prerequisites"></a>Pré-requisitos

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Instalação

Para instalar a CLI do LibMan:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

Uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) é instalada do pacote NuGet [Microsoft. Web. LibraryManager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) .

Para instalar a CLI do LibMan de uma origem específica do pacote NuGet:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

No exemplo anterior, uma ferramenta global do .NET Core é instalada a partir do arquivo *C:\Temp\Microsoft.Web.LibraryManager.CLI.1.0.94-g606058a278.nupkg* do computador Windows local.

## <a name="usage"></a>Uso

Após a instalação bem-sucedida da CLI, o comando a seguir pode ser usado:

```console
libman
```

Para exibir a versão instalada da CLI:

```console
libman --version
```

Para exibir os comandos da CLI disponíveis:

```console
libman --help
```

O comando anterior exibe uma saída semelhante à seguinte:

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

## <a name="initialize-libman-in-the-project"></a>Inicializar LibMan no projeto

O `libman init` comando criará um *libman.jsno* arquivo se ele não existir. O arquivo é criado com o conteúdo do modelo de item padrão.

### <a name="synopsis"></a>Sinopse

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `libman init`:

* `-d|--default-destination <PATH>`

  Um caminho relativo à pasta atual. Os arquivos de biblioteca serão instalados nesse local se nenhuma `destination` propriedade for definida para uma biblioteca no *libman.jsem*. O `<PATH>` valor é gravado na `defaultDestination` propriedade de *libman.jsem*.

* `-p|--default-provider <PROVIDER>`

  O provedor a ser usado se nenhum provedor for definido para uma determinada biblioteca. O `<PROVIDER>` valor é gravado na `defaultProvider` propriedade de *libman.jsem*. Substitua `<PROVIDER>` por um dos seguintes valores:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Para criar um *libman.jsno* arquivo em um projeto ASP.NET Core:

* Navegue até a raiz do projeto.
* Execute o comando a seguir:

  ```console
  libman init
  ```

* Digite o nome do provedor padrão ou pressione `Enter` para usar o provedor CDNJS padrão. Os valores válidos incluem:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![comando init Libman – provedor padrão](_static/libman-init-provider.png)

Um *libman.jsno* arquivo é adicionado à raiz do projeto com o seguinte conteúdo:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Adicionar arquivos de biblioteca

O `libman install` comando baixa e instala arquivos de biblioteca no projeto. Um *libman.jsno* arquivo será adicionado se um não existir. O *libman.jsno* arquivo é modificado para armazenar detalhes de configuração dos arquivos de biblioteca.

### <a name="synopsis"></a>Sinopse

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Argumentos

`LIBRARY`

O nome da biblioteca a ser instalada. Esse nome pode incluir a notação de número de versão (por exemplo, `@1.2.0` ).

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `libman install`:

* `-d|--destination <PATH>`

  O local para instalar a biblioteca. Se não for especificado, o local padrão será usado. Se nenhuma `defaultDestination` propriedade for especificada no *libman.jsem*, essa opção será necessária.

* `--files <FILE>`

  Especifique o nome do arquivo a ser instalado da biblioteca. Se não for especificado, todos os arquivos da biblioteca serão instalados. Forneça uma `--files` opção por arquivo a ser instalada. Caminhos relativos também têm suporte. Por exemplo: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  O nome do provedor a ser usado para a aquisição da biblioteca. Substitua `<PROVIDER>` por um dos seguintes valores:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Se não for especificado, a `defaultProvider` propriedade no *libman.jsem* será usada. Se nenhuma `defaultProvider` propriedade for especificada no *libman.jsem*, essa opção será necessária.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Considere o seguinte *libman.jsno* arquivo:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Para instalar o arquivo jQuery versão 3.2.1 *jquery.min.js* na pasta *wwwroot/scripts/jQuery* usando o provedor CDNJS:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

O *libman.jsno* arquivo é semelhante ao seguinte:

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

Para instalar os arquivos *calendar.js* e *Calendar. css* de *C: \\ temp \\ contosoCalendar \\ * usando o provedor do sistema de arquivos:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

O prompt a seguir aparece por dois motivos:

* O *libman.jsno* arquivo não contém uma `defaultDestination` propriedade.
* O `libman install` comando não contém a `-d|--destination` opção.

![comando de instalação do Libman-destino](_static/libman-install-destination.png)

Depois de aceitar o destino padrão, o *libman.jsno* arquivo é semelhante ao seguinte:

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

## <a name="restore-library-files"></a>Restaurar arquivos de biblioteca

O `libman restore` comando instala os arquivos de biblioteca definidos no *libman.jsem*. As seguintes regras se aplicam:

* Se nenhum *libman.jsno* arquivo existir na raiz do projeto, um erro será retornado.
* Se uma biblioteca especificar um provedor, a `defaultProvider` propriedade no *libman.jsem* será ignorada.
* Se uma biblioteca especificar um destino, a `defaultDestination` propriedade no *libman.jsem* será ignorada.

### <a name="synopsis"></a>Sinopse

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `libman restore`:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Para restaurar os arquivos de biblioteca definidos no *libman.jsem*:

```console
libman restore
```

## <a name="delete-library-files"></a>Excluir arquivos de biblioteca

O `libman clean` comando exclui os arquivos de biblioteca restaurados anteriormente por meio de LibMan. Pastas que se tornam vazias depois que essa operação é excluída. As configurações associadas dos arquivos de biblioteca na `libraries` propriedade de *libman.json* não são removidas.

### <a name="synopsis"></a>Sinopse

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `libman clean`:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Para excluir arquivos de biblioteca instalados por meio do LibMan:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Desinstalar arquivos de biblioteca

O comando `libman uninstall`:

* Exclui todos os arquivos associados à biblioteca especificada do destino no *libman.jsem*.
* Remove a configuração da biblioteca associada do *libman.jsem*.

Ocorrerá um erro quando:

* Não existe *libman.jsno* arquivo na raiz do projeto.
* A biblioteca especificada não existe.

Se mais de uma biblioteca com o mesmo nome estiver instalada, você será solicitado a escolher uma.

### <a name="synopsis"></a>Sinopse

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Argumentos

`LIBRARY`

O nome da biblioteca a ser desinstalada. Esse nome pode incluir a notação de número de versão (por exemplo, `@1.2.0` ).

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `libman uninstall`:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Considere o seguinte *libman.jsno* arquivo:

[!code-json[](samples/LibManSample/libman.json)]

* Para desinstalar o jQuery, qualquer um dos seguintes comandos é executado com sucesso:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Para desinstalar os arquivos do Lodash instalados por meio do `filesystem` provedor:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Atualizar versão da biblioteca

O `libman update` comando atualiza uma biblioteca instalada por meio de LibMan para a versão especificada.

Ocorrerá um erro quando:

* Não existe *libman.jsno* arquivo na raiz do projeto.
* A biblioteca especificada não existe.

Se mais de uma biblioteca com o mesmo nome estiver instalada, você será solicitado a escolher uma.

### <a name="synopsis"></a>Sinopse

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Argumentos

`LIBRARY`

O nome da biblioteca a ser atualizada.

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

* Para atualizar o jQuery para a versão 3.3.1:

  ```console
  libman update jquery --to 3.3.1
  ```

* Para atualizar o jQuery para a versão de pré-lançamento mais recente:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Gerenciar cache de biblioteca

O `libman cache` comando gerencia o cache da biblioteca LibMan. O `filesystem` provedor não usa o cache de biblioteca.

### <a name="synopsis"></a>Sinopse

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Argumentos

`PROVIDER`

Usado somente com o `clean` comando. Especifica o cache do provedor a ser limpo. Os valores válidos incluem:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `libman cache`:

* `--files`

  Lista os nomes de arquivos que são armazenados em cache.

* `--libraries`

  Liste os nomes das bibliotecas armazenadas em cache.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

* Para exibir os nomes das bibliotecas armazenadas em cache por provedor, use um dos seguintes comandos:

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

  Observe que a saída anterior mostra que as versões 3.2.1 e 3.3.1 do jQuery são armazenadas em cache no provedor CDNJS.

* Para esvaziar o cache de biblioteca para o provedor CDNJS:

  ```console
  libman cache clean cdnjs
  ```

  Depois de esvaziar o cache do provedor de CDNJS, o `libman cache list` comando exibe o seguinte:

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

* Para esvaziar o cache para todos os provedores com suporte:

  ```console
  libman cache clean
  ```

  Depois de esvaziar todos os caches do provedor, o `libman cache list` comando exibe o seguinte:

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

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
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="e9020-103">Use o Cli LibMan com ASP.NET Núcleo</span><span class="sxs-lookup"><span data-stu-id="e9020-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="e9020-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="e9020-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="e9020-105">O [LibMan](xref:client-side/libman/index) CLI é uma ferramenta multiplataforma que é suportada em todos os lugares .NET Core é suportado.</span><span class="sxs-lookup"><span data-stu-id="e9020-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e9020-106">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e9020-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="e9020-107">Instalação</span><span class="sxs-lookup"><span data-stu-id="e9020-107">Installation</span></span>

<span data-ttu-id="e9020-108">Para instalar o Cli LibMan:</span><span class="sxs-lookup"><span data-stu-id="e9020-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="e9020-109">Uma [ferramenta global .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) está instalada no pacote [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="e9020-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="e9020-110">Para instalar o Cli LibMan a partir de uma fonte específica do pacote NuGet:</span><span class="sxs-lookup"><span data-stu-id="e9020-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="e9020-111">No exemplo anterior, uma ferramenta global do .NET Core é instalada a partir do arquivo *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg.*</span><span class="sxs-lookup"><span data-stu-id="e9020-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="e9020-112">Uso</span><span class="sxs-lookup"><span data-stu-id="e9020-112">Usage</span></span>

<span data-ttu-id="e9020-113">Após a instalação bem-sucedida da CLI, o seguinte comando pode ser usado:</span><span class="sxs-lookup"><span data-stu-id="e9020-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="e9020-114">Para visualizar a versão CLI instalada:</span><span class="sxs-lookup"><span data-stu-id="e9020-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="e9020-115">Para visualizar os comandos CLI disponíveis:</span><span class="sxs-lookup"><span data-stu-id="e9020-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="e9020-116">O comando anterior exibe saída semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="e9020-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="e9020-117">As seções a seguir descrevem os comandos da CLI disponíveis.</span><span class="sxs-lookup"><span data-stu-id="e9020-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="e9020-118">Inicialize libman no projeto</span><span class="sxs-lookup"><span data-stu-id="e9020-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="e9020-119">O `libman init` comando cria um arquivo *libman.json* se não existir.</span><span class="sxs-lookup"><span data-stu-id="e9020-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="e9020-120">O arquivo é criado com o conteúdo do modelo de item padrão.</span><span class="sxs-lookup"><span data-stu-id="e9020-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e9020-121">Sinopse</span><span class="sxs-lookup"><span data-stu-id="e9020-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="e9020-122">Opções</span><span class="sxs-lookup"><span data-stu-id="e9020-122">Options</span></span>

<span data-ttu-id="e9020-123">As opções a seguir estão disponíveis para o comando `libman init`:</span><span class="sxs-lookup"><span data-stu-id="e9020-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="e9020-124">Um caminho relativo à pasta atual.</span><span class="sxs-lookup"><span data-stu-id="e9020-124">A path relative to the current folder.</span></span> <span data-ttu-id="e9020-125">Os arquivos da biblioteca são `destination` instalados neste local se nenhuma propriedade for definida para uma biblioteca em *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="e9020-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="e9020-126">O `<PATH>` valor é `defaultDestination` escrito na propriedade de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="e9020-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="e9020-127">O provedor a ser usado se nenhum provedor for definido para uma determinada biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e9020-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="e9020-128">O `<PROVIDER>` valor é `defaultProvider` escrito na propriedade de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="e9020-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="e9020-129">Substitua por `<PROVIDER>` um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="e9020-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e9020-130">Exemplos</span><span class="sxs-lookup"><span data-stu-id="e9020-130">Examples</span></span>

<span data-ttu-id="e9020-131">Para criar um arquivo *libman.json* em um projeto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e9020-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="e9020-132">Navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="e9020-132">Navigate to the project root.</span></span>
* <span data-ttu-id="e9020-133">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="e9020-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="e9020-134">Digite o nome do provedor `Enter` padrão ou pressione para usar o provedor CDNJS padrão.</span><span class="sxs-lookup"><span data-stu-id="e9020-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="e9020-135">Os valores válidos incluem:</span><span class="sxs-lookup"><span data-stu-id="e9020-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init comando - provedor padrão](_static/libman-init-provider.png)

<span data-ttu-id="e9020-137">Um arquivo *libman.json* é adicionado à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="e9020-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="e9020-138">Adicionar arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="e9020-138">Add library files</span></span>

<span data-ttu-id="e9020-139">O `libman install` comando baixa e instala arquivos de biblioteca no projeto.</span><span class="sxs-lookup"><span data-stu-id="e9020-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="e9020-140">Um arquivo *libman.json* é adicionado se um não existe.</span><span class="sxs-lookup"><span data-stu-id="e9020-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="e9020-141">O arquivo *libman.json* é modificado para armazenar detalhes de configuração para os arquivos da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e9020-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e9020-142">Sinopse</span><span class="sxs-lookup"><span data-stu-id="e9020-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="e9020-143">Argumentos</span><span class="sxs-lookup"><span data-stu-id="e9020-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="e9020-144">O nome da biblioteca para instalar.</span><span class="sxs-lookup"><span data-stu-id="e9020-144">The name of the library to install.</span></span> <span data-ttu-id="e9020-145">Este nome pode incluir notação de `@1.2.0`número de versão (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="e9020-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="e9020-146">Opções</span><span class="sxs-lookup"><span data-stu-id="e9020-146">Options</span></span>

<span data-ttu-id="e9020-147">As opções a seguir estão disponíveis para o comando `libman install`:</span><span class="sxs-lookup"><span data-stu-id="e9020-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="e9020-148">O local para instalar a biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e9020-148">The location to install the library.</span></span> <span data-ttu-id="e9020-149">Se não for especificado, o local padrão é usado.</span><span class="sxs-lookup"><span data-stu-id="e9020-149">If not specified, the default location is used.</span></span> <span data-ttu-id="e9020-150">Se `defaultDestination` nenhuma propriedade for especificada em *libman.json,* esta opção é necessária.</span><span class="sxs-lookup"><span data-stu-id="e9020-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="e9020-151">Especifique o nome do arquivo a ser instalado na biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e9020-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="e9020-152">Se não for especificado, todos os arquivos da biblioteca serão instalados.</span><span class="sxs-lookup"><span data-stu-id="e9020-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="e9020-153">Forneça `--files` uma opção por arquivo a ser instalado.</span><span class="sxs-lookup"><span data-stu-id="e9020-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="e9020-154">Caminhos relativos também são suportados.</span><span class="sxs-lookup"><span data-stu-id="e9020-154">Relative paths are supported too.</span></span> <span data-ttu-id="e9020-155">Por exemplo: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="e9020-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="e9020-156">O nome do provedor para usar na aquisição da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e9020-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="e9020-157">Substitua por `<PROVIDER>` um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="e9020-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="e9020-158">Se não for `defaultProvider` especificado, a propriedade em *libman.json* é usada.</span><span class="sxs-lookup"><span data-stu-id="e9020-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="e9020-159">Se `defaultProvider` nenhuma propriedade for especificada em *libman.json,* esta opção é necessária.</span><span class="sxs-lookup"><span data-stu-id="e9020-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e9020-160">Exemplos</span><span class="sxs-lookup"><span data-stu-id="e9020-160">Examples</span></span>

<span data-ttu-id="e9020-161">Considere o seguinte arquivo *libman.json:*</span><span class="sxs-lookup"><span data-stu-id="e9020-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="e9020-162">Para instalar o arquivo jQuery versão 3.2.1 *jquery.min.js* na pasta *wwwroot/scripts/jquery* usando o provedor CDNJS:</span><span class="sxs-lookup"><span data-stu-id="e9020-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="e9020-163">O arquivo *libman.json* se assemelha ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="e9020-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="e9020-164">Para instalar os arquivos *calendar.js* e *calendar.css* de \*C:\\temp\\contosoCalendar\\ \* usando o provedor do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="e9020-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="e9020-165">O seguinte prompt aparece por duas razões:</span><span class="sxs-lookup"><span data-stu-id="e9020-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="e9020-166">O arquivo *libman.json* não `defaultDestination` contém uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="e9020-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="e9020-167">O `libman install` comando não contém `-d|--destination` a opção.</span><span class="sxs-lookup"><span data-stu-id="e9020-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![comando de instalação libman - destino](_static/libman-install-destination.png)

<span data-ttu-id="e9020-169">Depois de aceitar o destino padrão, o arquivo *libman.json* se assemelha ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="e9020-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="e9020-170">Restaurar arquivos da biblioteca</span><span class="sxs-lookup"><span data-stu-id="e9020-170">Restore library files</span></span>

<span data-ttu-id="e9020-171">O `libman restore` comando instala arquivos de biblioteca definidos em *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="e9020-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="e9020-172">As seguintes regras se aplicam:</span><span class="sxs-lookup"><span data-stu-id="e9020-172">The following rules apply:</span></span>

* <span data-ttu-id="e9020-173">Se não houver nenhum arquivo *libman.json* na raiz do projeto, um erro será retornado.</span><span class="sxs-lookup"><span data-stu-id="e9020-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="e9020-174">Se uma biblioteca especificar `defaultProvider` um provedor, a propriedade em *libman.json* será ignorada.</span><span class="sxs-lookup"><span data-stu-id="e9020-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="e9020-175">Se uma biblioteca especificar `defaultDestination` um destino, a propriedade em *libman.json* será ignorada.</span><span class="sxs-lookup"><span data-stu-id="e9020-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e9020-176">Sinopse</span><span class="sxs-lookup"><span data-stu-id="e9020-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="e9020-177">Opções</span><span class="sxs-lookup"><span data-stu-id="e9020-177">Options</span></span>

<span data-ttu-id="e9020-178">As opções a seguir estão disponíveis para o comando `libman restore`:</span><span class="sxs-lookup"><span data-stu-id="e9020-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e9020-179">Exemplos</span><span class="sxs-lookup"><span data-stu-id="e9020-179">Examples</span></span>

<span data-ttu-id="e9020-180">Para restaurar os arquivos da biblioteca definidos em *libman.json*:</span><span class="sxs-lookup"><span data-stu-id="e9020-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="e9020-181">Excluir arquivos da biblioteca</span><span class="sxs-lookup"><span data-stu-id="e9020-181">Delete library files</span></span>

<span data-ttu-id="e9020-182">O `libman clean` comando exclui arquivos de biblioteca previamente restaurados via LibMan.</span><span class="sxs-lookup"><span data-stu-id="e9020-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="e9020-183">As pastas que ficam vazias após essa operação são excluídas.</span><span class="sxs-lookup"><span data-stu-id="e9020-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="e9020-184">As configurações associadas dos `libraries` arquivos da biblioteca na propriedade de *libman.json* não são removidas.</span><span class="sxs-lookup"><span data-stu-id="e9020-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e9020-185">Sinopse</span><span class="sxs-lookup"><span data-stu-id="e9020-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="e9020-186">Opções</span><span class="sxs-lookup"><span data-stu-id="e9020-186">Options</span></span>

<span data-ttu-id="e9020-187">As opções a seguir estão disponíveis para o comando `libman clean`:</span><span class="sxs-lookup"><span data-stu-id="e9020-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e9020-188">Exemplos</span><span class="sxs-lookup"><span data-stu-id="e9020-188">Examples</span></span>

<span data-ttu-id="e9020-189">Para excluir arquivos de biblioteca instalados via LibMan:</span><span class="sxs-lookup"><span data-stu-id="e9020-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="e9020-190">Desinstalar arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="e9020-190">Uninstall library files</span></span>

<span data-ttu-id="e9020-191">O comando `libman uninstall`:</span><span class="sxs-lookup"><span data-stu-id="e9020-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="e9020-192">Exclui todos os arquivos associados à biblioteca especificada do destino em *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="e9020-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="e9020-193">Remove a configuração da biblioteca associada do *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="e9020-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="e9020-194">Ocorre um erro quando:</span><span class="sxs-lookup"><span data-stu-id="e9020-194">An error occurs when:</span></span>

* <span data-ttu-id="e9020-195">Não existe nenhum arquivo *libman.json* na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="e9020-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="e9020-196">A biblioteca especificada não existe.</span><span class="sxs-lookup"><span data-stu-id="e9020-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="e9020-197">Se mais de uma biblioteca com o mesmo nome for instalada, você será solicitado a escolher uma.</span><span class="sxs-lookup"><span data-stu-id="e9020-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e9020-198">Sinopse</span><span class="sxs-lookup"><span data-stu-id="e9020-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="e9020-199">Argumentos</span><span class="sxs-lookup"><span data-stu-id="e9020-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="e9020-200">O nome da biblioteca para desinstalar.</span><span class="sxs-lookup"><span data-stu-id="e9020-200">The name of the library to uninstall.</span></span> <span data-ttu-id="e9020-201">Este nome pode incluir notação de `@1.2.0`número de versão (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="e9020-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="e9020-202">Opções</span><span class="sxs-lookup"><span data-stu-id="e9020-202">Options</span></span>

<span data-ttu-id="e9020-203">As opções a seguir estão disponíveis para o comando `libman uninstall`:</span><span class="sxs-lookup"><span data-stu-id="e9020-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e9020-204">Exemplos</span><span class="sxs-lookup"><span data-stu-id="e9020-204">Examples</span></span>

<span data-ttu-id="e9020-205">Considere o seguinte arquivo *libman.json:*</span><span class="sxs-lookup"><span data-stu-id="e9020-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="e9020-206">Para desinstalar o jQuery, qualquer um dos seguintes comandos será bem sucedido:</span><span class="sxs-lookup"><span data-stu-id="e9020-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="e9020-207">Para desinstalar os arquivos Lodash instalados através do `filesystem` provedor:</span><span class="sxs-lookup"><span data-stu-id="e9020-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="e9020-208">Atualizar versão da biblioteca</span><span class="sxs-lookup"><span data-stu-id="e9020-208">Update library version</span></span>

<span data-ttu-id="e9020-209">O `libman update` comando atualiza uma biblioteca instalada via LibMan para a versão especificada.</span><span class="sxs-lookup"><span data-stu-id="e9020-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="e9020-210">Ocorre um erro quando:</span><span class="sxs-lookup"><span data-stu-id="e9020-210">An error occurs when:</span></span>

* <span data-ttu-id="e9020-211">Não existe nenhum arquivo *libman.json* na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="e9020-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="e9020-212">A biblioteca especificada não existe.</span><span class="sxs-lookup"><span data-stu-id="e9020-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="e9020-213">Se mais de uma biblioteca com o mesmo nome for instalada, você será solicitado a escolher uma.</span><span class="sxs-lookup"><span data-stu-id="e9020-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e9020-214">Sinopse</span><span class="sxs-lookup"><span data-stu-id="e9020-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="e9020-215">Argumentos</span><span class="sxs-lookup"><span data-stu-id="e9020-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="e9020-216">O nome da biblioteca para atualizar.</span><span class="sxs-lookup"><span data-stu-id="e9020-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="e9020-217">Opções</span><span class="sxs-lookup"><span data-stu-id="e9020-217">Options</span></span>

<span data-ttu-id="e9020-218">As opções a seguir estão disponíveis para o comando `libman update`:</span><span class="sxs-lookup"><span data-stu-id="e9020-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="e9020-219">Obtenha a versão de pré-lançamento mais recente da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e9020-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="e9020-220">Obtenha uma versão específica da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e9020-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e9020-221">Exemplos</span><span class="sxs-lookup"><span data-stu-id="e9020-221">Examples</span></span>

* <span data-ttu-id="e9020-222">Para atualizar o jQuery para a versão mais recente:</span><span class="sxs-lookup"><span data-stu-id="e9020-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="e9020-223">Para atualizar jQuery para a versão 3.3.1:</span><span class="sxs-lookup"><span data-stu-id="e9020-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="e9020-224">Para atualizar o jQuery para a versão mais recente de pré-lançamento:</span><span class="sxs-lookup"><span data-stu-id="e9020-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="e9020-225">Gerenciar cache de biblioteca</span><span class="sxs-lookup"><span data-stu-id="e9020-225">Manage library cache</span></span>

<span data-ttu-id="e9020-226">O `libman cache` comando gerencia o cache da biblioteca LibMan.</span><span class="sxs-lookup"><span data-stu-id="e9020-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="e9020-227">O `filesystem` provedor não usa o cache da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e9020-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e9020-228">Sinopse</span><span class="sxs-lookup"><span data-stu-id="e9020-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="e9020-229">Argumentos</span><span class="sxs-lookup"><span data-stu-id="e9020-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="e9020-230">Só usado `clean` com o comando.</span><span class="sxs-lookup"><span data-stu-id="e9020-230">Only used with the `clean` command.</span></span> <span data-ttu-id="e9020-231">Especifica o cache do provedor para limpar.</span><span class="sxs-lookup"><span data-stu-id="e9020-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="e9020-232">Os valores válidos incluem:</span><span class="sxs-lookup"><span data-stu-id="e9020-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="e9020-233">Opções</span><span class="sxs-lookup"><span data-stu-id="e9020-233">Options</span></span>

<span data-ttu-id="e9020-234">As opções a seguir estão disponíveis para o comando `libman cache`:</span><span class="sxs-lookup"><span data-stu-id="e9020-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="e9020-235">Liste os nomes dos arquivos armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="e9020-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="e9020-236">Liste os nomes das bibliotecas armazenadas em cache.</span><span class="sxs-lookup"><span data-stu-id="e9020-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e9020-237">Exemplos</span><span class="sxs-lookup"><span data-stu-id="e9020-237">Examples</span></span>

* <span data-ttu-id="e9020-238">Para visualizar os nomes das bibliotecas armazenadas em cache por provedor, use um dos seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="e9020-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="e9020-239">Uma saída semelhante à apresentada a seguir será exibida:</span><span class="sxs-lookup"><span data-stu-id="e9020-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="e9020-240">Para exibir os nomes dos arquivos de biblioteca armazenados em cache por provedor:</span><span class="sxs-lookup"><span data-stu-id="e9020-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="e9020-241">Uma saída semelhante à apresentada a seguir será exibida:</span><span class="sxs-lookup"><span data-stu-id="e9020-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="e9020-242">Observe que a saída anterior mostra que as versões jQuery 3.2.1 e 3.3.1 estão armazenadas em cache sob o provedor CDNJS.</span><span class="sxs-lookup"><span data-stu-id="e9020-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="e9020-243">Para esvaziar o cache da biblioteca para o provedor CDNJS:</span><span class="sxs-lookup"><span data-stu-id="e9020-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="e9020-244">Depois de esvaziar o cache do `libman cache list` provedor CDNJS, o comando exibe o seguinte:</span><span class="sxs-lookup"><span data-stu-id="e9020-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="e9020-245">Para esvaziar o cache de todos os provedores suportados:</span><span class="sxs-lookup"><span data-stu-id="e9020-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="e9020-246">Depois de esvaziar todos `libman cache list` os caches do provedor, o comando exibe o seguinte:</span><span class="sxs-lookup"><span data-stu-id="e9020-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="e9020-247">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e9020-247">Additional resources</span></span>

* [<span data-ttu-id="e9020-248">Instalar uma Ferramenta Global</span><span class="sxs-lookup"><span data-stu-id="e9020-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="e9020-249">Repositório do GitHub do LibMan</span><span class="sxs-lookup"><span data-stu-id="e9020-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)

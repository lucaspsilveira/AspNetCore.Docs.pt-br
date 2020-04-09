---
title: Gerenciar referências de Protobuf com dotnet-grpc
author: juntaoluo
description: Aprenda a adicionar, atualizar, remover e listar referências do Protobuf com a ferramenta global dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667331"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="48a37-103">Gerenciar referências de Protobuf com dotnet-grpc</span><span class="sxs-lookup"><span data-stu-id="48a37-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="48a37-104">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="48a37-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="48a37-105">`dotnet-grpc`é uma ferramenta global do .NET Core para gerenciar referências [protobuf (*.proto)*](xref:grpc/basics#proto-file) dentro de um projeto .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="48a37-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="48a37-106">A ferramenta pode ser usada para adicionar, atualizar, remover e listar referências protobuf.</span><span class="sxs-lookup"><span data-stu-id="48a37-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="48a37-107">Instalação</span><span class="sxs-lookup"><span data-stu-id="48a37-107">Installation</span></span>

<span data-ttu-id="48a37-108">Para instalar `dotnet-grpc` a [Ferramenta Global do Núcleo .NET,](/dotnet/core/tools/global-tools)execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="48a37-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="48a37-109">Adicionar referências</span><span class="sxs-lookup"><span data-stu-id="48a37-109">Add references</span></span>

<span data-ttu-id="48a37-110">`dotnet-grpc`pode ser usado para adicionar referências `<Protobuf />` protobuf como itens ao arquivo *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="48a37-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="48a37-111">As referências protobuf são usadas para gerar os ativos do cliente c# e/ou do servidor.</span><span class="sxs-lookup"><span data-stu-id="48a37-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="48a37-112">A `dotnet-grpc` ferramenta pode:</span><span class="sxs-lookup"><span data-stu-id="48a37-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="48a37-113">Crie uma referência Protobuf a partir de arquivos locais em disco.</span><span class="sxs-lookup"><span data-stu-id="48a37-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="48a37-114">Crie uma referência Protobuf a partir de um arquivo remoto especificado por uma URL.</span><span class="sxs-lookup"><span data-stu-id="48a37-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="48a37-115">Certifique-se de que as dependências corretas do pacote gRPC sejam adicionadas ao projeto.</span><span class="sxs-lookup"><span data-stu-id="48a37-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="48a37-116">Por exemplo, `Grpc.AspNetCore` o pacote é adicionado a um aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="48a37-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="48a37-117">`Grpc.AspNetCore`contém bibliotecas de servidores e clientes gRPC e suporte a ferramentas.</span><span class="sxs-lookup"><span data-stu-id="48a37-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="48a37-118">Alternativamente, `Grpc.Net.Client`os `Grpc.Tools` `Google.Protobuf` pacotes e os pacotes, que contêm apenas as bibliotecas de clientes gRPC e suporte a ferramentas, são adicionados a um aplicativo console.</span><span class="sxs-lookup"><span data-stu-id="48a37-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="48a37-119">Adicionar arquivo</span><span class="sxs-lookup"><span data-stu-id="48a37-119">Add file</span></span>

<span data-ttu-id="48a37-120">O `add-file` comando é usado para adicionar arquivos locais no disco como referências protobuf.</span><span class="sxs-lookup"><span data-stu-id="48a37-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="48a37-121">Os caminhos de arquivo fornecidos:</span><span class="sxs-lookup"><span data-stu-id="48a37-121">The file paths provided:</span></span>

* <span data-ttu-id="48a37-122">Pode ser relativo ao diretório atual ou caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="48a37-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="48a37-123">Pode conter curingas para [globbing](https://wikipedia.org/wiki/Glob_(programming))de arquivo baseado em padrão .</span><span class="sxs-lookup"><span data-stu-id="48a37-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="48a37-124">Se algum arquivo estiver fora do `Link` diretório do projeto, um `Protos` elemento será adicionado para exibir o arquivo sob a pasta no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="48a37-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="48a37-125">Uso</span><span class="sxs-lookup"><span data-stu-id="48a37-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="48a37-126">Argumentos</span><span class="sxs-lookup"><span data-stu-id="48a37-126">Arguments</span></span>

| <span data-ttu-id="48a37-127">Argumento</span><span class="sxs-lookup"><span data-stu-id="48a37-127">Argument</span></span> | <span data-ttu-id="48a37-128">Descrição</span><span class="sxs-lookup"><span data-stu-id="48a37-128">Description</span></span> |
|-|-|
| <span data-ttu-id="48a37-129">files</span><span class="sxs-lookup"><span data-stu-id="48a37-129">files</span></span> | <span data-ttu-id="48a37-130">As referências do arquivo protobuf.</span><span class="sxs-lookup"><span data-stu-id="48a37-130">The protobuf file references.</span></span> <span data-ttu-id="48a37-131">Estes podem ser um caminho para glob para arquivos protobuf locais.</span><span class="sxs-lookup"><span data-stu-id="48a37-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="48a37-132">Opções</span><span class="sxs-lookup"><span data-stu-id="48a37-132">Options</span></span>

| <span data-ttu-id="48a37-133">Opção curta</span><span class="sxs-lookup"><span data-stu-id="48a37-133">Short option</span></span> | <span data-ttu-id="48a37-134">Opção longa</span><span class="sxs-lookup"><span data-stu-id="48a37-134">Long option</span></span> | <span data-ttu-id="48a37-135">Descrição</span><span class="sxs-lookup"><span data-stu-id="48a37-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="48a37-136">-p</span><span class="sxs-lookup"><span data-stu-id="48a37-136">-p</span></span> | <span data-ttu-id="48a37-137">--projeto</span><span class="sxs-lookup"><span data-stu-id="48a37-137">--project</span></span> | <span data-ttu-id="48a37-138">O caminho para o arquivo do projeto para operar.</span><span class="sxs-lookup"><span data-stu-id="48a37-138">The path to the project file to operate on.</span></span> <span data-ttu-id="48a37-139">Se um arquivo não for especificado, o comando procurará o diretório atual por um.</span><span class="sxs-lookup"><span data-stu-id="48a37-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="48a37-140">-S</span><span class="sxs-lookup"><span data-stu-id="48a37-140">-s</span></span> | <span data-ttu-id="48a37-141">--serviços</span><span class="sxs-lookup"><span data-stu-id="48a37-141">--services</span></span> | <span data-ttu-id="48a37-142">O tipo de serviços gRPC que devem ser gerados.</span><span class="sxs-lookup"><span data-stu-id="48a37-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="48a37-143">Se `Default` for especificado, `Both` é usado `Client` para projetos web e é usado para projetos não-Web.</span><span class="sxs-lookup"><span data-stu-id="48a37-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="48a37-144">Os valores `Client` `Default`aceitos `Server`são, `Both`, , `None`, .</span><span class="sxs-lookup"><span data-stu-id="48a37-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="48a37-145">-i</span><span class="sxs-lookup"><span data-stu-id="48a37-145">-i</span></span> | <span data-ttu-id="48a37-146">--adicional-importação-dirs</span><span class="sxs-lookup"><span data-stu-id="48a37-146">--additional-import-dirs</span></span> | <span data-ttu-id="48a37-147">Diretórios adicionais a serem usados na resolução de importações para os arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="48a37-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="48a37-148">Esta é uma lista de caminhos separados por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="48a37-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="48a37-149">--acesso</span><span class="sxs-lookup"><span data-stu-id="48a37-149">--access</span></span> | <span data-ttu-id="48a37-150">O modificador de acesso a ser usado para as classes C# geradas.</span><span class="sxs-lookup"><span data-stu-id="48a37-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="48a37-151">O valor padrão é `Public`.</span><span class="sxs-lookup"><span data-stu-id="48a37-151">The default value is `Public`.</span></span> <span data-ttu-id="48a37-152">Os valores aceitos são `Internal` e `Public`.</span><span class="sxs-lookup"><span data-stu-id="48a37-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="48a37-153">Adicionar URL</span><span class="sxs-lookup"><span data-stu-id="48a37-153">Add URL</span></span>

<span data-ttu-id="48a37-154">O `add-url` comando é usado para adicionar um arquivo remoto especificado por uma URL de origem como referência Protobuf.</span><span class="sxs-lookup"><span data-stu-id="48a37-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="48a37-155">Um caminho de arquivo deve ser fornecido para especificar onde baixar o arquivo remoto.</span><span class="sxs-lookup"><span data-stu-id="48a37-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="48a37-156">O caminho do arquivo pode ser relativo ao diretório atual ou a um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="48a37-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="48a37-157">Se o caminho do arquivo estiver `Link` fora do diretório do projeto, `Protos` um elemento será adicionado para exibir o arquivo sob a pasta virtual no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="48a37-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="48a37-158">Uso</span><span class="sxs-lookup"><span data-stu-id="48a37-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="48a37-159">Argumentos</span><span class="sxs-lookup"><span data-stu-id="48a37-159">Arguments</span></span>

| <span data-ttu-id="48a37-160">Argumento</span><span class="sxs-lookup"><span data-stu-id="48a37-160">Argument</span></span> | <span data-ttu-id="48a37-161">Descrição</span><span class="sxs-lookup"><span data-stu-id="48a37-161">Description</span></span> |
|-|-|
| <span data-ttu-id="48a37-162">url</span><span class="sxs-lookup"><span data-stu-id="48a37-162">url</span></span> | <span data-ttu-id="48a37-163">A URL para um arquivo protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="48a37-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="48a37-164">Opções</span><span class="sxs-lookup"><span data-stu-id="48a37-164">Options</span></span>

| <span data-ttu-id="48a37-165">Opção curta</span><span class="sxs-lookup"><span data-stu-id="48a37-165">Short option</span></span> | <span data-ttu-id="48a37-166">Opção longa</span><span class="sxs-lookup"><span data-stu-id="48a37-166">Long option</span></span> | <span data-ttu-id="48a37-167">Descrição</span><span class="sxs-lookup"><span data-stu-id="48a37-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="48a37-168">-o</span><span class="sxs-lookup"><span data-stu-id="48a37-168">-o</span></span> | <span data-ttu-id="48a37-169">--output</span><span class="sxs-lookup"><span data-stu-id="48a37-169">--output</span></span> | <span data-ttu-id="48a37-170">Especifica o caminho de download para o arquivo protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="48a37-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="48a37-171">Trata-se de uma opção obrigatória.</span><span class="sxs-lookup"><span data-stu-id="48a37-171">This is a required option.</span></span>
| <span data-ttu-id="48a37-172">-p</span><span class="sxs-lookup"><span data-stu-id="48a37-172">-p</span></span> | <span data-ttu-id="48a37-173">--projeto</span><span class="sxs-lookup"><span data-stu-id="48a37-173">--project</span></span> | <span data-ttu-id="48a37-174">O caminho para o arquivo do projeto para operar.</span><span class="sxs-lookup"><span data-stu-id="48a37-174">The path to the project file to operate on.</span></span> <span data-ttu-id="48a37-175">Se um arquivo não for especificado, o comando procurará o diretório atual por um.</span><span class="sxs-lookup"><span data-stu-id="48a37-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="48a37-176">-S</span><span class="sxs-lookup"><span data-stu-id="48a37-176">-s</span></span> | <span data-ttu-id="48a37-177">--serviços</span><span class="sxs-lookup"><span data-stu-id="48a37-177">--services</span></span> | <span data-ttu-id="48a37-178">O tipo de serviços gRPC que devem ser gerados.</span><span class="sxs-lookup"><span data-stu-id="48a37-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="48a37-179">Se `Default` for especificado, `Both` é usado `Client` para projetos web e é usado para projetos não-Web.</span><span class="sxs-lookup"><span data-stu-id="48a37-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="48a37-180">Os valores `Client` `Default`aceitos `Server`são, `Both`, , `None`, .</span><span class="sxs-lookup"><span data-stu-id="48a37-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="48a37-181">-i</span><span class="sxs-lookup"><span data-stu-id="48a37-181">-i</span></span> | <span data-ttu-id="48a37-182">--adicional-importação-dirs</span><span class="sxs-lookup"><span data-stu-id="48a37-182">--additional-import-dirs</span></span> | <span data-ttu-id="48a37-183">Diretórios adicionais a serem usados na resolução de importações para os arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="48a37-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="48a37-184">Esta é uma lista de caminhos separados por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="48a37-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="48a37-185">--acesso</span><span class="sxs-lookup"><span data-stu-id="48a37-185">--access</span></span> | <span data-ttu-id="48a37-186">O modificador de acesso a ser usado para as classes C# geradas.</span><span class="sxs-lookup"><span data-stu-id="48a37-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="48a37-187">O valor padrão é `Public`.</span><span class="sxs-lookup"><span data-stu-id="48a37-187">Default value is `Public`.</span></span> <span data-ttu-id="48a37-188">Os valores aceitos são `Internal` e `Public`.</span><span class="sxs-lookup"><span data-stu-id="48a37-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="48a37-189">Remover</span><span class="sxs-lookup"><span data-stu-id="48a37-189">Remove</span></span>

<span data-ttu-id="48a37-190">O `remove` comando é usado para remover referências protobuf do arquivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="48a37-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="48a37-191">O comando aceita argumentos de caminho e URLs de origem como argumentos.</span><span class="sxs-lookup"><span data-stu-id="48a37-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="48a37-192">A ferramenta:</span><span class="sxs-lookup"><span data-stu-id="48a37-192">The tool:</span></span>

* <span data-ttu-id="48a37-193">Apenas remove a referência Protobuf.</span><span class="sxs-lookup"><span data-stu-id="48a37-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="48a37-194">Não exclui o arquivo *.proto,* mesmo que tenha sido originalmente baixado de uma URL remota.</span><span class="sxs-lookup"><span data-stu-id="48a37-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="48a37-195">Uso</span><span class="sxs-lookup"><span data-stu-id="48a37-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="48a37-196">Argumentos</span><span class="sxs-lookup"><span data-stu-id="48a37-196">Arguments</span></span>

| <span data-ttu-id="48a37-197">Argumento</span><span class="sxs-lookup"><span data-stu-id="48a37-197">Argument</span></span> | <span data-ttu-id="48a37-198">Descrição</span><span class="sxs-lookup"><span data-stu-id="48a37-198">Description</span></span> |
|-|-|
| <span data-ttu-id="48a37-199">referências</span><span class="sxs-lookup"><span data-stu-id="48a37-199">references</span></span> | <span data-ttu-id="48a37-200">Os URLs ou caminhos de arquivo das referências protobuf para remover.</span><span class="sxs-lookup"><span data-stu-id="48a37-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="48a37-201">Opções</span><span class="sxs-lookup"><span data-stu-id="48a37-201">Options</span></span>

| <span data-ttu-id="48a37-202">Opção curta</span><span class="sxs-lookup"><span data-stu-id="48a37-202">Short option</span></span> | <span data-ttu-id="48a37-203">Opção longa</span><span class="sxs-lookup"><span data-stu-id="48a37-203">Long option</span></span> | <span data-ttu-id="48a37-204">Descrição</span><span class="sxs-lookup"><span data-stu-id="48a37-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="48a37-205">-p</span><span class="sxs-lookup"><span data-stu-id="48a37-205">-p</span></span> | <span data-ttu-id="48a37-206">--projeto</span><span class="sxs-lookup"><span data-stu-id="48a37-206">--project</span></span> | <span data-ttu-id="48a37-207">O caminho para o arquivo do projeto para operar.</span><span class="sxs-lookup"><span data-stu-id="48a37-207">The path to the project file to operate on.</span></span> <span data-ttu-id="48a37-208">Se um arquivo não for especificado, o comando procurará o diretório atual por um.</span><span class="sxs-lookup"><span data-stu-id="48a37-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="48a37-209">Atualizar</span><span class="sxs-lookup"><span data-stu-id="48a37-209">Refresh</span></span>

<span data-ttu-id="48a37-210">O `refresh` comando é usado para atualizar uma referência remota com o conteúdo mais recente da URL de origem.</span><span class="sxs-lookup"><span data-stu-id="48a37-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="48a37-211">Tanto o caminho do arquivo de download quanto a URL de origem podem ser usados para especificar a referência a ser atualizada.</span><span class="sxs-lookup"><span data-stu-id="48a37-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="48a37-212">Observação:</span><span class="sxs-lookup"><span data-stu-id="48a37-212">Note:</span></span>

* <span data-ttu-id="48a37-213">Os hashes do conteúdo do arquivo são comparados para determinar se o arquivo local deve ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="48a37-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="48a37-214">Nenhuma informação de carimbo de data e hora é comparada.</span><span class="sxs-lookup"><span data-stu-id="48a37-214">No timestamp information is compared.</span></span>

<span data-ttu-id="48a37-215">A ferramenta sempre substitui o arquivo local pelo arquivo remoto se uma atualização for necessária.</span><span class="sxs-lookup"><span data-stu-id="48a37-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="48a37-216">Uso</span><span class="sxs-lookup"><span data-stu-id="48a37-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="48a37-217">Argumentos</span><span class="sxs-lookup"><span data-stu-id="48a37-217">Arguments</span></span>

| <span data-ttu-id="48a37-218">Argumento</span><span class="sxs-lookup"><span data-stu-id="48a37-218">Argument</span></span> | <span data-ttu-id="48a37-219">Descrição</span><span class="sxs-lookup"><span data-stu-id="48a37-219">Description</span></span> |
|-|-|
| <span data-ttu-id="48a37-220">referências</span><span class="sxs-lookup"><span data-stu-id="48a37-220">references</span></span> | <span data-ttu-id="48a37-221">Os URLs ou caminhos de arquivo para referências de protobuf remotas que devem ser atualizadas.</span><span class="sxs-lookup"><span data-stu-id="48a37-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="48a37-222">Deixe este argumento vazio para atualizar todas as referências remotas.</span><span class="sxs-lookup"><span data-stu-id="48a37-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="48a37-223">Opções</span><span class="sxs-lookup"><span data-stu-id="48a37-223">Options</span></span>

| <span data-ttu-id="48a37-224">Opção curta</span><span class="sxs-lookup"><span data-stu-id="48a37-224">Short option</span></span> | <span data-ttu-id="48a37-225">Opção longa</span><span class="sxs-lookup"><span data-stu-id="48a37-225">Long option</span></span> | <span data-ttu-id="48a37-226">Descrição</span><span class="sxs-lookup"><span data-stu-id="48a37-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="48a37-227">-p</span><span class="sxs-lookup"><span data-stu-id="48a37-227">-p</span></span> | <span data-ttu-id="48a37-228">--projeto</span><span class="sxs-lookup"><span data-stu-id="48a37-228">--project</span></span> | <span data-ttu-id="48a37-229">O caminho para o arquivo do projeto para operar.</span><span class="sxs-lookup"><span data-stu-id="48a37-229">The path to the project file to operate on.</span></span> <span data-ttu-id="48a37-230">Se um arquivo não for especificado, o comando procurará o diretório atual por um.</span><span class="sxs-lookup"><span data-stu-id="48a37-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="48a37-231">--dry-run</span><span class="sxs-lookup"><span data-stu-id="48a37-231">--dry-run</span></span> | <span data-ttu-id="48a37-232">Produz uma lista de arquivos que seriam atualizados sem baixar nenhum novo conteúdo.</span><span class="sxs-lookup"><span data-stu-id="48a37-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="48a37-233">Lista</span><span class="sxs-lookup"><span data-stu-id="48a37-233">List</span></span>

<span data-ttu-id="48a37-234">O `list` comando é usado para exibir todas as referências do Protobuf no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="48a37-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="48a37-235">Se todos os valores de uma coluna forem valores padrão, a coluna poderá ser omitida.</span><span class="sxs-lookup"><span data-stu-id="48a37-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="48a37-236">Uso</span><span class="sxs-lookup"><span data-stu-id="48a37-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="48a37-237">Opções</span><span class="sxs-lookup"><span data-stu-id="48a37-237">Options</span></span>

| <span data-ttu-id="48a37-238">Opção curta</span><span class="sxs-lookup"><span data-stu-id="48a37-238">Short option</span></span> | <span data-ttu-id="48a37-239">Opção longa</span><span class="sxs-lookup"><span data-stu-id="48a37-239">Long option</span></span> | <span data-ttu-id="48a37-240">Descrição</span><span class="sxs-lookup"><span data-stu-id="48a37-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="48a37-241">-p</span><span class="sxs-lookup"><span data-stu-id="48a37-241">-p</span></span> | <span data-ttu-id="48a37-242">--projeto</span><span class="sxs-lookup"><span data-stu-id="48a37-242">--project</span></span> | <span data-ttu-id="48a37-243">O caminho para o arquivo do projeto para operar.</span><span class="sxs-lookup"><span data-stu-id="48a37-243">The path to the project file to operate on.</span></span> <span data-ttu-id="48a37-244">Se um arquivo não for especificado, o comando procurará o diretório atual por um.</span><span class="sxs-lookup"><span data-stu-id="48a37-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48a37-245">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="48a37-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>

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
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Gerenciar referências de Protobuf com dotnet-grpc

Por [John Luo](https://github.com/juntaoluo)

`dotnet-grpc`é uma ferramenta global do .NET Core para gerenciar referências [protobuf (*.proto)*](xref:grpc/basics#proto-file) dentro de um projeto .NET gRPC. A ferramenta pode ser usada para adicionar, atualizar, remover e listar referências protobuf.

## <a name="installation"></a>Instalação

Para instalar `dotnet-grpc` a [Ferramenta Global do Núcleo .NET,](/dotnet/core/tools/global-tools)execute o seguinte comando:

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Adicionar referências

`dotnet-grpc`pode ser usado para adicionar referências `<Protobuf />` protobuf como itens ao arquivo *.csproj:*

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

As referências protobuf são usadas para gerar os ativos do cliente c# e/ou do servidor. A `dotnet-grpc` ferramenta pode:

* Crie uma referência Protobuf a partir de arquivos locais em disco.
* Crie uma referência Protobuf a partir de um arquivo remoto especificado por uma URL.
* Certifique-se de que as dependências corretas do pacote gRPC sejam adicionadas ao projeto.

Por exemplo, `Grpc.AspNetCore` o pacote é adicionado a um aplicativo web. `Grpc.AspNetCore`contém bibliotecas de servidores e clientes gRPC e suporte a ferramentas. Alternativamente, `Grpc.Net.Client`os `Grpc.Tools` `Google.Protobuf` pacotes e os pacotes, que contêm apenas as bibliotecas de clientes gRPC e suporte a ferramentas, são adicionados a um aplicativo console.

### <a name="add-file"></a>Adicionar arquivo

O `add-file` comando é usado para adicionar arquivos locais no disco como referências protobuf. Os caminhos de arquivo fornecidos:

* Pode ser relativo ao diretório atual ou caminhos absolutos.
* Pode conter curingas para [globbing](https://wikipedia.org/wiki/Glob_(programming))de arquivo baseado em padrão .

Se algum arquivo estiver fora do `Link` diretório do projeto, um `Protos` elemento será adicionado para exibir o arquivo sob a pasta no Visual Studio.

### <a name="usage"></a>Uso

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Argumentos

| Argumento | Descrição |
|-|-|
| files | As referências do arquivo protobuf. Estes podem ser um caminho para glob para arquivos protobuf locais. |

#### <a name="options"></a>Opções

| Opção curta | Opção longa | Descrição |
|-|-|-|
| -p | --projeto | O caminho para o arquivo do projeto para operar. Se um arquivo não for especificado, o comando procurará o diretório atual por um.
| -S | --serviços | O tipo de serviços gRPC que devem ser gerados. Se `Default` for especificado, `Both` é usado `Client` para projetos web e é usado para projetos não-Web. Os valores `Client` `Default`aceitos `Server`são, `Both`, , `None`, .
| -i | --adicional-importação-dirs | Diretórios adicionais a serem usados na resolução de importações para os arquivos protobuf. Esta é uma lista de caminhos separados por ponto e vírgula.
| | --acesso | O modificador de acesso a ser usado para as classes C# geradas. O valor padrão é `Public`. Os valores aceitos são `Internal` e `Public`.

### <a name="add-url"></a>Adicionar URL

O `add-url` comando é usado para adicionar um arquivo remoto especificado por uma URL de origem como referência Protobuf. Um caminho de arquivo deve ser fornecido para especificar onde baixar o arquivo remoto. O caminho do arquivo pode ser relativo ao diretório atual ou a um caminho absoluto. Se o caminho do arquivo estiver `Link` fora do diretório do projeto, `Protos` um elemento será adicionado para exibir o arquivo sob a pasta virtual no Visual Studio.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Argumentos

| Argumento | Descrição |
|-|-|
| url | A URL para um arquivo protobuf remoto. |

#### <a name="options"></a>Opções

| Opção curta | Opção longa | Descrição |
|-|-|-|
| -o | --output | Especifica o caminho de download para o arquivo protobuf remoto. Trata-se de uma opção obrigatória.
| -p | --projeto | O caminho para o arquivo do projeto para operar. Se um arquivo não for especificado, o comando procurará o diretório atual por um.
| -S | --serviços | O tipo de serviços gRPC que devem ser gerados. Se `Default` for especificado, `Both` é usado `Client` para projetos web e é usado para projetos não-Web. Os valores `Client` `Default`aceitos `Server`são, `Both`, , `None`, .
| -i | --adicional-importação-dirs | Diretórios adicionais a serem usados na resolução de importações para os arquivos protobuf. Esta é uma lista de caminhos separados por ponto e vírgula.
| | --acesso | O modificador de acesso a ser usado para as classes C# geradas. O valor padrão é `Public`. Os valores aceitos são `Internal` e `Public`.

## <a name="remove"></a>Remover

O `remove` comando é usado para remover referências protobuf do arquivo *.csproj.* O comando aceita argumentos de caminho e URLs de origem como argumentos. A ferramenta:

* Apenas remove a referência Protobuf.
* Não exclui o arquivo *.proto,* mesmo que tenha sido originalmente baixado de uma URL remota.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Argumentos

| Argumento | Descrição |
|-|-|
| referências | Os URLs ou caminhos de arquivo das referências protobuf para remover. |

### <a name="options"></a>Opções

| Opção curta | Opção longa | Descrição |
|-|-|-|
| -p | --projeto | O caminho para o arquivo do projeto para operar. Se um arquivo não for especificado, o comando procurará o diretório atual por um.

## <a name="refresh"></a>Atualizar

O `refresh` comando é usado para atualizar uma referência remota com o conteúdo mais recente da URL de origem. Tanto o caminho do arquivo de download quanto a URL de origem podem ser usados para especificar a referência a ser atualizada. Observação:

* Os hashes do conteúdo do arquivo são comparados para determinar se o arquivo local deve ser atualizado.
* Nenhuma informação de carimbo de data e hora é comparada.

A ferramenta sempre substitui o arquivo local pelo arquivo remoto se uma atualização for necessária.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Argumentos

| Argumento | Descrição |
|-|-|
| referências | Os URLs ou caminhos de arquivo para referências de protobuf remotas que devem ser atualizadas. Deixe este argumento vazio para atualizar todas as referências remotas. |

### <a name="options"></a>Opções

| Opção curta | Opção longa | Descrição |
|-|-|-|
| -p | --projeto | O caminho para o arquivo do projeto para operar. Se um arquivo não for especificado, o comando procurará o diretório atual por um.
| | --dry-run | Produz uma lista de arquivos que seriam atualizados sem baixar nenhum novo conteúdo.

## <a name="list"></a>Lista

O `list` comando é usado para exibir todas as referências do Protobuf no arquivo do projeto. Se todos os valores de uma coluna forem valores padrão, a coluna poderá ser omitida.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Opções

| Opção curta | Opção longa | Descrição |
|-|-|-|
| -p | --projeto | O caminho para o arquivo do projeto para operar. Se um arquivo não for especificado, o comando procurará o diretório atual por um.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>

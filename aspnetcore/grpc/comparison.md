---
title: Comparar serviços gRPC com APIs HTTP
author: jamesnk
description: Saiba como o gRPC se compara com as APIs HTTP e quais são os cenários recomendados.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- SignalR
uid: grpc/comparison
ms.openlocfilehash: 2dff64f1f2d67b8a1e676acf6cf131b684099750
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405880"
---
# <a name="compare-grpc-services-with-http-apis"></a>Comparar serviços gRPC com APIs HTTP

Por [James Newton-King](https://twitter.com/jamesnk)

Este artigo explica como [os serviços gRPC](https://grpc.io/docs/guides/) se comparam às APIs HTTP (incluindo ASP.NET [APIs web](xref:web-api/index)Core ). A tecnologia usada para fornecer uma API para o seu aplicativo é uma escolha importante, e o gRPC oferece benefícios exclusivos em comparação com as APIs HTTP. Este artigo discute os pontos fortes e fracos do gRPC e recomenda cenários para o uso do gRPC em relação a outras tecnologias.

## <a name="high-level-comparison"></a>Comparação de alto nível

A tabela a seguir oferece uma comparação de alto nível de recursos entre as APIs gRPC e HTTP com JSON.

| Recurso          | gRPC                                               | APIs HTTP com JSON           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Contrato         | Obrigatório (*.proto)*                                | Opcional (OpenAPI)            |
| Protocolo         | HTTP/2                                             | HTTP                          |
| Carga útil          | [Protobuf (pequeno, binário)](#performance)           | JSON (grande, legível humano)  |
| Prescritiva | [Especificação estrita](#strict-specification)      | Solta. Qualquer HTTP é válido.     |
| Streaming        | [Cliente, servidor, bidirecional](#streaming)       | Cliente, servidor                |
| Suporte ao navegador  | [Não (requer grpc-web)](#limited-browser-support) | Sim                           |
| Segurança         | Transporte (TLS)                                    | Transporte (TLS)               |
| Geração de código do cliente | [Sim](#code-generation)                      | Ferramentas OpenAPI + de terceiros |

## <a name="grpc-strengths"></a>pontos fortes do gRPC

### <a name="performance"></a>Desempenho

As mensagens gRPC são serializadas usando [o Protobuf](https://developers.google.com/protocol-buffers/docs/overview), um formato de mensagem binária eficiente. Protobuf serializa muito rapidamente no servidor e cliente. A serialização do Protobuf resulta em pequenas cargas de mensagens, importantes em cenários de largura de banda limitada, como aplicativos móveis.

O gRPC foi projetado para HTTP/2, uma revisão importante do HTTP que fornece benefícios significativos de desempenho em HTTP 1.x:

* Enquadramento binário e compressão. O protocolo HTTP/2 é compacto e eficiente tanto no envio quanto no recebimento.
* Multiplelação de várias chamadas HTTP/2 em uma única conexão TCP. O multiplexação elimina o [bloqueio de cabeça de linha](https://en.wikipedia.org/wiki/Head-of-line_blocking).

### <a name="code-generation"></a>Geração de código

Todas as estruturas gRPC fornecem suporte de primeira classe para geração de código. Um arquivo central para o desenvolvimento do gRPC é o [arquivo .proto](https://developers.google.com/protocol-buffers/docs/proto3), que define o contrato de serviços e mensagens gRPC. A partir deste arquivo, os frameworks gRPC irão gerar uma classe base de serviço, mensagens e um cliente completo.

Ao compartilhar o arquivo *.proto* entre o servidor e o cliente, as mensagens e o código do cliente podem ser gerados de ponta a ponta. A geração de código do cliente elimina a duplicação de mensagens no cliente e no servidor e cria um cliente fortemente digitado para você. Não ter que escrever um cliente economiza tempo de desenvolvimento significativo em aplicativos com muitos serviços.

### <a name="strict-specification"></a>Especificação estrita

Uma especificação formal para API HTTP com JSON não existe. Os desenvolvedores debatem o melhor formato de URLs, verbos HTTP e códigos de resposta.

A [especificação gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) é prescritiva sobre o formato que um serviço gRPC deve seguir. O gRPC elimina o debate e economiza tempo do desenvolvedor porque o gRPC é consistente entre plataformas e implementações.

### <a name="streaming"></a>Streaming

O HTTP/2 fornece uma base para fluxos de comunicação de longa duração e em tempo real. O gRPC fornece suporte de primeira classe para streaming através de HTTP/2.

Um serviço gRPC suporta todas as combinações de streaming:

* Unary (sem streaming)
* Servidor para streaming de clientes
* Fluxo de cliente para servidor
* Streaming bidirecional

### <a name="deadlinetimeouts-and-cancellation"></a>Prazos/intervalos e cancelamento

O gRPC permite que os clientes especifiquem quanto tempo estão dispostos a esperar que um RPC seja concluído. O [prazo](https://grpc.io/blog/deadlines) é enviado ao servidor, e o servidor pode decidir que ação tomar se ultrapassar o prazo. Por exemplo, o servidor pode cancelar solicitações gRPC/HTTP/banco de dados em andamento no tempo há10.

A propagação do prazo e do cancelamento através de chamadas gRPC para crianças ajuda a impor limites de uso de recursos.

## <a name="grpc-recommended-scenarios"></a>cenários recomendados pela gRPC

o gRPC é adequado aos seguintes cenários:

* **Microsserviços** &ndash; gRPC é projetado para baixa latência e comunicação de alto throughput. gRPC é ótimo para microsserviços leves onde a eficiência é crítica.
* **O gRPC de comunicação** &ndash; ponto a ponto em tempo real tem excelente suporte para streaming bidirecional. Os serviços gRPC podem enviar mensagens em tempo real sem votação.
* A ferramenta gRPC de &ndash; **ambientes poliglotas** suporta todas as linguagens de desenvolvimento popular, tornando o gRPC uma boa escolha para ambientes multi-idiomas.
* **Ambientes restritos** &ndash; à rede as mensagens gRPC são serializadas com protobuf, um formato de mensagem leve. Uma mensagem gRPC é sempre menor do que uma mensagem JSON equivalente.

## <a name="grpc-weaknesses"></a>fraquezas gRPC

### <a name="limited-browser-support"></a>Suporte limitado ao navegador

É impossível chamar diretamente um serviço gRPC de um navegador hoje. O gRPC usa fortemente recursos HTTP/2 e nenhum navegador fornece o nível de controle necessário sobre as solicitações da Web para suportar um cliente gRPC. Por exemplo, os navegadores não permitem que um chamador exija que http/2 seja usado ou forneça acesso a quadros HTTP/2 subjacentes.

[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) é uma tecnologia adicional da equipe gRPC que fornece suporte gRPC limitado no navegador. o gRPC-Web consiste em duas partes: um cliente JavaScript que suporta todos os navegadores modernos e um proxy gRPC-Web no servidor. O cliente gRPC-Web chama o proxy e o proxy será encaminhado nas solicitações gRPC para o servidor gRPC.

Nem todos os recursos do gRPC são suportados pelo gRPC-Web. O streaming de clientes e bidirecionais não é suportado e há suporte limitado para streaming de servidor.

> [!TIP]
> .NET Core tem suporte experimental para gRPC-Web. Visite <xref:grpc/browser> para mais informações.

### <a name="not-human-readable"></a>Não é legível humano

As solicitações de API HTTP são enviadas como texto e podem ser lidas e criadas por humanos.

as mensagens gRPC são codificadas com protobuf por padrão. Embora o Protobuf seja eficiente para enviar e receber, seu formato binário não é legível humano. Protobuf requer a descrição da interface da mensagem especificada no arquivo *.proto* para desserializar adequadamente. Ferramentas adicionais são necessárias para analisar as cargas protobuf no fio e para compor as solicitações manualmente.

Existem recursos como [reflexão](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) do servidor e a [ferramenta de linha de comando gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) para ajudar com mensagens binárias do Protobuf. Além disso, as mensagens Protobuf suportam [a conversão de e para JSON](https://developers.google.com/protocol-buffers/docs/proto3#json). A conversão JSON incorporada fornece uma maneira eficiente de converter mensagens Protobuf para e de forma legível humana ao depurar.

## <a name="alternative-framework-scenarios"></a>Cenários de quadros alternativos

Outros quadros são recomendados sobre o gRPC nos seguintes cenários:

* **As APIs acessíveis** &ndash; ao navegador gRPC não são totalmente suportadas no navegador. o gRPC-Web pode oferecer suporte ao navegador, mas tem limitações e introduz um proxy de servidor.
* **A transmissão em tempo real** &ndash; da comunicação gRPC suporta comunicação em tempo real via streaming, mas o conceito de transmitir uma mensagem para conexões registradas não existe. Por exemplo, em um cenário de sala de bate-papo onde novas mensagens de bate-papo devem ser enviadas para todos os clientes na sala de bate-papo, cada chamada gRPC é necessária para transmitir individualmente novas mensagens de bate-papo para o cliente. [SignalR](xref:signalr/introduction)é uma estrutura útil para este cenário. SignalRtem o conceito de conexões persistentes e suporte incorporado para transmitir mensagens.
* **Comunicação entre processos** &ndash; Um processo deve hospedar um servidor HTTP/2 para aceitar chamadas gRPC recebidas. Para o Windows, [os tubos](/dotnet/standard/io/pipe-operations) de comunicação entre processos são um método de comunicação rápido e leve.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

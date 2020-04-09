---
title: Controle de versão de serviços gRPC
author: jamesnk
description: Saiba como versácia os serviços gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 9bd76009ba28a1abef25a98686afea6753d4a8f4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664111"
---
# <a name="versioning-grpc-services"></a>Controle de versão de serviços gRPC

Por [James Newton-King](https://twitter.com/jamesnk)

Novos recursos adicionados a um aplicativo podem exigir que os serviços de gRPC fornecidos aos clientes mudem, às vezes de maneiras inesperadas e quebrando. Quando os serviços gRPC mudam:

* Deve-se considerar como as mudanças impactam os clientes.
* Uma estratégia de versionamento para apoiar mudanças deve ser implementada.

## <a name="backwards-compatibility"></a>Compatibilidade com versões anteriores

O protocolo gRPC foi projetado para suportar serviços que mudam ao longo do tempo. Geralmente, as adições aos serviços e métodos gRPC não são quebras. Mudanças não quebras permitem que os clientes existentes continuem trabalhando sem alterações. Alterar ou excluir serviços gRPC está quebrando alterações. Quando os serviços gRPC têm alterações quebrando, os clientes que usam esse serviço devem ser atualizados e reimplantados.

Fazer alterações não-quebras em um serviço tem uma série de benefícios:

* Os clientes existentes continuam a ser executados.
* Evita o trabalho envolvido em notificar os clientes de quebrar mudanças e atualizá-las.
* Apenas uma versão do serviço precisa ser documentada e mantida.

### <a name="non-breaking-changes"></a>Alterações não relacionadas à falha

Essas alterações não são desconexas em um nível de protocolo gRPC e nível binário .NET.

* **Adicionando um novo serviço**
* **Adicionando um novo método a um serviço**
* **Adicionando um campo a uma mensagem de solicitação** - Campos adicionados a uma mensagem de solicitação são desserializados com o [valor padrão](https://developers.google.com/protocol-buffers/docs/proto3#default) no servidor quando não definidos. Para ser uma mudança sem quebra, o serviço deve ter sucesso quando o novo campo não é definido por clientes mais velhos.
* **Adicionando um campo a uma mensagem de resposta** - Campos adicionados a uma mensagem de resposta são desserializados na coleção de [campos desconhecidos](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) da mensagem no cliente.
* **Agregando um valor a um enum** - Enums são serializados como um valor numérico. Novos valores de enum são desserializados no cliente para o valor enum sem um nome enum. Para ser uma mudança sem quebra, os clientes mais velhos devem ser executados corretamente ao receber o novo valor enum.

### <a name="binary-breaking-changes"></a>Mudanças de quebra binárias

As seguintes alterações não são de quebra em um nível de protocolo gRPC, mas o cliente precisa ser atualizado se ele atualizar para o contrato *.proto* mais recente ou conjunto de cliente .NET. A compatibilidade binária é importante se você planeja publicar uma biblioteca gRPC no NuGet.

* **Remoção de um campo** - Valores de um campo removido são desserializados para [campos desconhecidos](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)de uma mensagem . Este não é um protocolo gRPC quebrando a mudança, mas o cliente precisa ser atualizado se ele atualizar para o contrato mais recente. É importante que um número de campo removido não seja acidentalmente reutilizado no futuro. Para garantir que isso não aconteça, especifique números de campo e nomes excluídos na mensagem usando a palavra-chave [reservada](https://developers.google.com/protocol-buffers/docs/proto3#reserved) do Protobuf.
* **Renomeando uma mensagem** - Os nomes das mensagens não são normalmente enviados na rede, então isso não é uma alteração de quebra de protocolo gRPC. O cliente precisará ser atualizado se atualizar para o contrato mais recente. Uma situação em que os nomes das mensagens **são enviados** na rede é com [Quaisquer](https://developers.google.com/protocol-buffers/docs/proto3#any) campos, quando o nome da mensagem é usado para identificar o tipo de mensagem.
* **Alterando** csharp_namespace `csharp_namespace` - A alteração mudará o namespace dos tipos .NET gerados. Este não é um protocolo gRPC quebrando a mudança, mas o cliente precisa ser atualizado se ele atualizar para o contrato mais recente.

### <a name="protocol-breaking-changes"></a>Mudanças de quebra de protocolo

Os seguintes itens são alterações de protocolo e quebra binária:

* **Renomeação de um campo** - Com o conteúdo protobuf, os nomes de campo são usados apenas em código gerado. O número de campo é usado para identificar campos na rede. Renomear um campo não é uma mudança de protocolo para Protobuf. No entanto, se um servidor estiver usando o conteúdo JSON, então renomear um campo é uma mudança de ruptura.
* **Alterar um tipo de dados de campo** - Alterar o tipo de dados de um campo para um tipo [incompatível](https://developers.google.com/protocol-buffers/docs/proto3#updating) causará erros ao desserializar a mensagem. Mesmo que o novo tipo de dados seja compatível, é provável que o cliente precise ser atualizado para suportar o novo tipo se ele atualizar para o contrato mais recente.
* **Alterando um número de campo** - Com as cargas protobuf, o número de campo é usado para identificar campos na rede.
* **Renomeando um pacote, serviço ou método** - o gRPC usa o nome do pacote, o nome do serviço e o nome do método para criar a URL. O cliente obtém um *status NÃO IMPLEMENTADO* do servidor.
* **Remoção de um serviço ou método** - O cliente obtém um status *UNIMPLEMENTED* do servidor ao chamar o método removido.

### <a name="behavior-breaking-changes"></a>Mudanças de quebra de comportamento

Ao fazer alterações não-quebras, você também deve considerar se os clientes mais velhos podem continuar trabalhando com o novo comportamento de serviço. Por exemplo, adicionar um novo campo a uma mensagem de solicitação:

* Não é uma mudança de protocolo.
* Devolver um status de erro no servidor se o novo campo não estiver definido torna-o uma mudança de ruptura para clientes antigos.

A compatibilidade de comportamento é determinada pelo código específico do aplicativo.

## <a name="version-number-services"></a>Serviços de número de versão

Os serviços devem se esforçar para permanecer retrocompatíveis com clientes antigos. Eventualmente, alterações no seu aplicativo podem exigir alterações. Quebrar clientes antigos e forçá-los a serem atualizados junto com seu serviço não é uma boa experiência de usuário. Uma maneira de manter a compatibilidade inversa ao fazer alterações de quebra é publicar várias versões de um serviço.

O gRPC suporta um especificador de [pacote](https://developers.google.com/protocol-buffers/docs/proto3#packages) opcional, que funciona muito como um namespace .NET. Na verdade, `package` o nome .NET será usado como o `option csharp_namespace` namespace .NET para os tipos .NET gerados se não estiver definido no arquivo *.proto.* O pacote pode ser usado para especificar um número de versão para o seu serviço e suas mensagens:

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

O nome do pacote é combinado com o nome do serviço para identificar um endereço de serviço. Um endereço de serviço permite que várias versões de um serviço sejam hospedadas lado a lado:

* `greet.v1.Greeter`
* `greet.v2.Greeter`

As implementações do serviço versionado são registradas em *Startup.cs:*

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

A inclusão de um número de versão no nome do pacote lhe dá a oportunidade de publicar uma versão *v2* do seu serviço com alterações de quebra, enquanto continua a suportar clientes mais antigos que chamam a versão *v1.* Uma vez que os clientes tenham atualizado para usar o serviço *v2,* você pode optar por remover a versão antiga. Ao planejar publicar várias versões de um serviço:

* Evite quebrar mudanças se for razoável.
* Não atualize o número da versão a menos que faça alterações.
* Atualize o número da versão quando fizer alterações de quebra.

A publicação de várias versões de um serviço o duplica. Para reduzir a duplicação, considere mover a lógica de negócios das implementações de serviços para um local centralizado que pode ser reutilizado pelas implementações antigas e novas:

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Serviços e mensagens gerados com diferentes nomes de pacotes são **diferentes tipos .NET**. Mudar a lógica de negócios para um local centralizado requer o mapeamento de mensagens para tipos comuns.

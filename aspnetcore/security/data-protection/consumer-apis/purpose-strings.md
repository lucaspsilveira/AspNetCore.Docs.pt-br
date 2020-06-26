---
title: Cadeias de caracteres de finalidade no ASP.NET Core
author: rick-anderson
description: Saiba como as cadeias de caracteres de finalidade são usadas no ASP.NET Core APIs de proteção de dados.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: b52961fd33ce2d3708754f73ea38456d8d5f8f3c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404282"
---
# <a name="purpose-strings-in-aspnet-core"></a>Cadeias de caracteres de finalidade no ASP.NET Core

<a name="data-protection-consumer-apis-purposes"></a>

Os componentes que consomem `IDataProtectionProvider` devem passar um parâmetro de *finalidades* exclusivas para o `CreateProtector` método. O *parâmetro* de finalidades é inerente à segurança do sistema de proteção de dados, pois ele fornece isolamento entre os consumidores criptográficos, mesmo que as chaves de criptografia raiz sejam as mesmas.

Quando um consumidor especifica uma finalidade, a cadeia de caracteres de finalidade é usada junto com as chaves de criptografia raiz para derivar subchaves de criptografia exclusivas para esse consumidor. Isso isola o consumidor de todos os outros consumidores criptográficos no aplicativo: nenhum outro componente pode ler suas cargas e não pode ler as cargas de outro componente. Esse isolamento também renderiza as categorias inteiras de ataques inviáveis no componente.

![Exemplo de diagrama de finalidade](purpose-strings/_static/purposes.png)

No diagrama acima, `IDataProtector` as instâncias A e B **não podem** ler as cargas de cada outra, apenas suas próprias.

A cadeia de caracteres de finalidade não precisa ser secreta. Ele deve simplesmente ser exclusivo no sentido de que nenhum outro componente bem comparecedo fornecerá a mesma cadeia de caracteres de finalidade.

>[!TIP]
> Usar o namespace e o nome do tipo do componente que consome as APIs de proteção de dados é uma boa regra geral, como na prática, essas informações nunca entrarão em conflito.
>
>Um componente criado pela contoso, que é responsável por criar tokens de portador, pode usar contoso. Security. BearerToken como sua cadeia de caracteres de finalidade. Ou-ainda melhor-ele pode usar contoso. Security. BearerToken. v1 como sua cadeia de caracteres de finalidade. Acrescentar o número de versão permite que uma versão futura use contoso. Security. BearerToken. v2 como sua finalidade, e as diferentes versões seriam completamente isoladas umas das outras no que se referem a cargas.

Uma vez que o parâmetro de finalidades `CreateProtector` é uma matriz de cadeia de caracteres, a acima poderia ter sido especificada como `[ "Contoso.Security.BearerToken", "v1" ]` . Isso permite estabelecer uma hierarquia de finalidades e abre a possibilidade de cenários de multilocação com o sistema de proteção de dados.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Os componentes não devem permitir que a entrada do usuário não confiável seja a única fonte de entrada para a cadeia de finalidades.
>
>Por exemplo, considere um componente contoso. Messaging. SecureMessage, que é responsável pelo armazenamento de mensagens seguras. Se o componente de mensagens seguras fosse chamado `CreateProtector([ username ])` , um usuário mal-intencionado pode criar uma conta com o nome de usuário "contoso. Security. BearerToken" em uma tentativa de obter o componente a ser chamado `CreateProtector([ "Contoso.Security.BearerToken" ])` , fazendo com que o sistema de mensagens seguro possa fazer com que os tokens da autenticação possam ser percebidos como símbolos de autenticações.
>
>Uma cadeia de finalidades para o componente de mensagens seria melhor `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , o que fornece isolamento adequado.

O isolamento fornecido pelo e os comportamentos de `IDataProtectionProvider` , `IDataProtector` e são os seguintes:

* Para um determinado `IDataProtectionProvider` objeto, o `CreateProtector` método criará um `IDataProtector` objeto vinculado exclusivamente ao `IDataProtectionProvider` objeto que o criou e o parâmetro final que foi passado para o método.

* O parâmetro de finalidade não deve ser nulo. (Se a finalidade for especificada como uma matriz, isso significa que a matriz não deve ter comprimento zero e todos os elementos da matriz devem ser não nulos.) Uma finalidade de cadeia de caracteres vazia é tecnicamente permitida, mas não é recomendada.

* Dois argumentos de finalidades são equivalentes se e somente se contiverem as mesmas cadeias de caracteres (usando um comparador ordinal) na mesma ordem. Um argumento de finalidade única é equivalente à matriz de finalidades de elemento único correspondente.

* Dois `IDataProtector` objetos são equivalentes se e somente se eles forem criados a partir de objetos equivalentes `IDataProtectionProvider` com parâmetros de finalidades equivalentes.

* Para um determinado `IDataProtector` objeto, uma chamada para retornará `Unprotect(protectedData)` o If original `unprotectedData` se e somente se `protectedData := Protect(unprotectedData)` for um `IDataProtector` objeto equivalente.

> [!NOTE]
> Não estamos considerando o caso em que algum componente escolhe intencionalmente uma cadeia de caracteres de finalidade que é conhecida como conflito com outro componente. Esse componente, essencialmente, seria considerado mal-intencionado, e esse sistema não se destina a fornecer garantias de segurança caso o código mal-intencionado já esteja em execução dentro do processo de trabalho.

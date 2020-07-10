---
title: Auxiliar de marca de link no ASP.NET Core
author: rick-anderson
ms.author: riande
description: Descubra os atributos auxiliares de marca de link ASP.NET Core e a função que cada atributo desempenha na extensão do comportamento da marca de link HTML.
ms.custom: mvc
ms.date: 09/24/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: ac9f6449e2b7b135318ecf116e1dba7b33ddff83
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212391"
---
# <a name="link-tag-helper-in-aspnet-core"></a>Auxiliar de marca de link no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

O [auxiliar de marca de link](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) gera um link para um arquivo CSS primário ou de retorno. Normalmente, o arquivo CSS primário está em uma CDN ( [rede de distribuição de conteúdo](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) ).

[!INCLUDE[](~/includes/cdn.md)]

O auxiliar de marca de link permite que você especifique uma CDN para o arquivo CSS e um fallback quando a CDN não estiver disponível. O auxiliar de marca de link fornece a vantagem de desempenho de uma CDN com a robustez da hospedagem local.

A marcação a seguir Razor mostra o `head` elemento de um arquivo de layout criado com o modelo de aplicativo Web ASP.NET Core:

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

Este é um HTML renderizado do código anterior (em um ambiente que não é de desenvolvimento):

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

No código anterior, o auxiliar de marca de link gerou o `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` elemento e o JavaScript a seguir, que é usado para verificar o arquivo *bootstrap. min. css* solicitado está disponível na CDN. Nesse caso, o arquivo CSS estava disponível para que o auxiliar de marca gerasse o `<link />` elemento com o arquivo CSS da CDN.

## <a name="commonly-used-link-tag-helper-attributes"></a>Atributos auxiliares de marca de link usados com frequência

Consulte [auxiliar de marca de link](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) para todos os atributos, propriedades e métodos auxiliares de marca de link.

### <a name="href"></a>href

Endereço preferencial do recurso vinculado. O endereço é passado para o HTML gerado em todos os casos.

### <a name="asp-fallback-href"></a>ASP-fallback-href

A URL de uma folha de estilos CSS para fazer fallback no caso em que a URL primária falhar.

### <a name="asp-fallback-test-class"></a>ASP-fallback-Test-Class

O nome da classe definida na folha de estilos a ser usada para o teste de fallback. Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.

### <a name="asp-fallback-test-property"></a>ASP-fallback-Test-Property

O nome da propriedade CSS a ser usado para o teste de fallback. Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.

### <a name="asp-fallback-test-value"></a>ASP-fallback-Test-Value

O valor da propriedade CSS a ser usado para o teste de fallback. Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>

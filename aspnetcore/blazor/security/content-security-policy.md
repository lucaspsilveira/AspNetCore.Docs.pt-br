---
title: Impor uma política de segurança de conteúdo para ASP.NET CoreBlazor
author: guardrex
description: Saiba como usar uma política de segurança de conteúdo (CSP) com Blazor aplicativos ASP.NET Core para ajudar a proteger contra ataques XSS (scripts entre sites).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/content-security-policy
ms.openlocfilehash: 63359260766846ccf14ececfae82bf02cc598cf9
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103530"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>Impor uma política de segurança de conteúdo para ASP.NET CoreBlazor

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

O [XSS (script entre sites)](xref:security/cross-site-scripting) é uma vulnerabilidade de segurança em que um invasor coloca um ou mais scripts mal-intencionados do lado do cliente no conteúdo renderizado de um aplicativo. Uma política de segurança de conteúdo (CSP) ajuda a proteger contra ataques de XSS, informando o navegador de válido:

* Fontes de conteúdo carregado, incluindo scripts, folhas de estilos e imagens.
* Ações tomadas por uma página, especificando destinos de URL permitidos de formulários.
* Plug-ins que podem ser carregados.

Para aplicar um CSP a um aplicativo, o desenvolvedor especifica várias *diretivas* de segurança de conteúdo do CSP em um ou mais `Content-Security-Policy` cabeçalhos ou `<meta>` marcas.

As políticas são avaliadas pelo navegador enquanto uma página está sendo carregada. O navegador inspeciona as fontes da página e determina se elas atendem aos requisitos das diretivas de segurança do conteúdo. Quando as diretivas de política não são atendidas para um recurso, o navegador não carrega o recurso. Por exemplo, considere uma política que não permita scripts de terceiros. Quando uma página contém uma `<script>` marca com uma origem de terceiros no `src` atributo, o navegador impede que o script seja carregado.

O CSP tem suporte na maioria dos navegadores de desktop e móvel modernos, incluindo Chrome, Edge, Firefox, Opera e Safari. O CSP é recomendado para Blazor aplicativos.

## <a name="policy-directives"></a>Diretivas de política

No mínimo, especifique as seguintes diretivas e fontes para Blazor aplicativos. Adicione outras diretivas e fontes conforme necessário. As seguintes diretivas são usadas na seção [aplicar a política](#apply-the-policy) deste artigo, em que são fornecidas as políticas de segurança de exemplo para Blazor Webassembly e Blazor servidor:

* [base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): restringe as URLs para a marca de uma página `<base>` . Especifique `self` para indicar que a origem do aplicativo, incluindo o esquema e o número da porta, é uma origem válida.
* [bloquear-tudo-conteúdo misto](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): impede o carregamento de conteúdo http e HTTPS mistos.
* [Default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): indica um fallback para diretivas de origem que não são explicitamente especificadas pela política. Especifique `self` para indicar que a origem do aplicativo, incluindo o esquema e o número da porta, é uma origem válida.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): indica fontes válidas para imagens.
  * Especifique `data:` para permitir o carregamento de imagens de `data:` URLs.
  * Especifique `https:` para permitir o carregamento de imagens de pontos de extremidade HTTPS.
* [objeto-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): indica fontes válidas para `<object>` as `<embed>` marcas, e `<applet>` . Especifique `none` para impedir todas as fontes de URL.
* [script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): indica fontes válidas para scripts.
  * Especifique a `https://stackpath.bootstrapcdn.com/` origem do host para scripts de bootstrap.
  * Especifique `self` para indicar que a origem do aplicativo, incluindo o esquema e o número da porta, é uma origem válida.
  * Em um Blazor aplicativo Webassembly:
    * Especifique os seguintes hashes para permitir que os Blazor scripts embutidos Webassembly necessários sejam carregados:
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * Especifique `unsafe-eval` para usar os `eval()` métodos e para a criação de código a partir de cadeias de caracteres.
  * Em um Blazor aplicativo de servidor, especifique o `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash para o script embutido que executa a detecção de fallback para folhas de estilo.
* [Style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): indica fontes válidas para folhas de estilo.
  * Especifique a `https://stackpath.bootstrapcdn.com/` origem do host para as folhas de estilo de inicialização.
  * Especifique `self` para indicar que a origem do aplicativo, incluindo o esquema e o número da porta, é uma origem válida.
  * Especifique `unsafe-inline` para permitir o uso de estilos embutidos. A declaração embutida é necessária para a interface do usuário em Blazor aplicativos de servidor para reconectar o cliente e o servidor após a solicitação inicial. Em uma versão futura, o estilo embutido pode ser removido para que `unsafe-inline` não seja mais necessário.
* [atualização-inseguro – solicitações](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): indica que as URLs de conteúdo de fontes inseguras (http) devem ser adquiridas com segurança via HTTPS.

As diretivas anteriores têm suporte de todos os navegadores, exceto o Microsoft Internet Explorer.

Para obter hashes de SHA para scripts embutidos adicionais:

* Aplique o CSP mostrado na seção [aplicar a política](#apply-the-policy) .
* Acesse o console de ferramentas de desenvolvedor do navegador ao executar o aplicativo localmente. O navegador calcula e exibe hashes de scripts bloqueados quando um cabeçalho ou `meta` marca CSP está presente.
* Copie os hashes fornecidos pelo navegador para as `script-src` fontes. Use aspas simples em volta de cada hash.

Para uma matriz de suporte de navegador de nível 2 de política de segurança de conteúdo, consulte posso [usar: nível de política de segurança de conteúdo 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).

## <a name="apply-the-policy"></a>Aplicar a política

Use uma `<meta>` marca para aplicar a política:

* Defina o valor do `http-equiv` atributo como `Content-Security-Policy` .
* Coloque as diretivas no `content` valor do atributo. Diretivas separadas com um ponto e vírgula ( `;` ).
* Sempre coloque a `meta` marca no `<head>` conteúdo.

As seções a seguir mostram exemplos de políticas para Blazor Webassembly e Blazor servidor. Esses exemplos têm a versão deste artigo para cada versão do Blazor . Para usar uma versão apropriada para sua versão, selecione a versão do documento com o seletor de **versão** na página da Web.

### <a name="blazor-webassembly"></a>BlazorWebassembly

No `<head>` conteúdo da página host *wwwroot/index.html* , aplique as diretivas descritas na seção [diretivas de política](#policy-directives) :

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### <a name="blazor-server"></a>BlazorServidor

No `<head>` conteúdo da página do host *Pages/_Host. cshtml* , aplique as diretivas descritas na seção [diretivas de política](#policy-directives) :

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a>Limitações da marca meta

Uma `<meta>` política de marca não dá suporte às seguintes diretivas:

* [quadro-ancestrais](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [relatório-para](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [URI do relatório](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [AppDomain](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Para dar suporte às diretivas anteriores, use um cabeçalho chamado `Content-Security-Policy` . A cadeia de caracteres de diretiva é o valor do cabeçalho.

## <a name="test-a-policy-and-receive-violation-reports"></a>Testar uma política e receber relatórios de violação

O teste ajuda a confirmar se os scripts de terceiros não são bloqueados inadvertidamente durante a criação de uma política inicial.

Para testar uma política durante um período de tempo sem impor as diretivas de política, defina o `<meta>` atributo da marca `http-equiv` ou o nome do cabeçalho de uma política baseada em cabeçalho como `Content-Security-Policy-Report-Only` . Os relatórios de falha são enviados como documentos JSON para uma URL especificada. Para obter mais informações, consulte [MDN Web docs: content-Security-Policy-Report-only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Para relatórios sobre violações enquanto uma política estiver ativa, consulte os seguintes artigos:

* [relatório-para](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [URI do relatório](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

Embora `report-uri` o não seja mais recomendado para uso, ambas as diretivas devem ser usadas até que `report-to` o tenha suporte de todos os principais navegadores. Não use exclusivamente `report-uri` porque o suporte para `report-uri` está sujeito a ser descartado a *qualquer momento* dos navegadores. Remova o suporte para `report-uri` em suas políticas quando o `report-to` tiver suporte total. Para acompanhar a adoção do `report-to` , consulte posso [usar: relatar](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).

Testar e atualizar a política de um aplicativo a cada versão.

## <a name="troubleshoot"></a>Solucionar problemas

* Os erros aparecem no console de ferramentas para desenvolvedores do navegador. Os navegadores fornecem informações sobre:
  * Elementos que não estão em conformidade com a política.
  * Como modificar a política para permitir um item bloqueado.
* Uma política só será completamente efetiva quando o navegador do cliente oferecer suporte a todas as diretivas incluídas. Para obter uma matriz de suporte do navegador atual, consulte [posso usar: content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).

## <a name="additional-resources"></a>Recursos adicionais

* [MDN Web docs: content-Security-Policy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [Nível de política de segurança de conteúdo 2](https://www.w3.org/TR/CSP2/)
* [Avaliador do Google CSP](https://csp-evaluator.withgoogle.com/)

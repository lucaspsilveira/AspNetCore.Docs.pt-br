---
title: Sessão no ASP.NET Core
author: rick-anderson
description: Descubra abordagens para preservar a sessão entre solicitações.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/app-state
ms.openlocfilehash: 30123e043a7c152b5719af8092b2ab42a70d2787
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2020
ms.locfileid: "86407613"
---
# <a name="session-and-state-management-in-aspnet-core"></a>Gerenciamento de sessão e estado no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Diana LaRose](https://github.com/DianaLaRose)

O HTTP é um protocolo sem estado. Por padrão, as solicitações HTTP são mensagens independentes que não retêm valores de usuário. Este artigo descreve várias abordagens para preservar dados de usuário entre solicitações.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Gerenciamento de estado

O estado pode ser armazenado usando várias abordagens. Cada abordagem é descrita posteriormente neste tópico.

| Abordagem de armazenamento | Mecanismo de armazenamento |
| ---------------- | ----------------- |
| [Arar](#cookies) | Cookies HTTP. Pode incluir dados armazenados usando o código do aplicativo do lado do servidor. |
| [Estado da sessão](#session-state) | Cookies HTTP e o código de aplicativo do lado do servidor |
| [TempData](#tempdata) | Estado de sessão ou cookies HTTP |
| [Cadeias de consulta](#query-strings) | Cadeias de caracteres de consulta HTTP |
| [Campos ocultos](#hidden-fields) | Campos de formulário HTTP |
| [HttpContext.Items](#httpcontextitems) | Código do aplicativo do lado do servidor |
| [Cache](#cache) | Código do aplicativo do lado do servidor |

## <a name="cookies"></a>Cookies

Cookies armazenam dados entre solicitações. Como os cookies são enviados com cada solicitação, seu tamanho deve ser reduzido ao mínimo. Idealmente, somente um identificador deve ser armazenado em um cookie com os dados armazenados pelo aplicativo. A maioria dos navegadores restringe o tamanho do cookie a 4.096 bytes. Somente um número limitado de cookies está disponível para cada domínio.

Uma vez que os cookies estão sujeitos à adulteração, eles devem ser validados pelo aplicativo. Os cookies podem ser excluídos por usuários e expirarem em clientes. No entanto, os cookies geralmente são a forma mais durável de persistência de dados no cliente.

Frequentemente, cookies são usados para personalização quando o conteúdo é personalizado para um usuário conhecido. O usuário é apenas identificado, e não autenticado, na maioria dos casos. O cookie pode armazenar o nome do usuário, o nome da conta ou a ID de usuário exclusiva, como um GUID. O cookie pode ser usado para acessar as configurações personalizadas do usuário, como sua cor de plano de fundo de site preferencial.

Consulte as [normas gerais de proteção de dados da União Europeia (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) ao emitir cookies e lidar com questões de privacidade. Para obter mais informações, veja [Suporte ao RGPD (Regulamento Geral sobre a Proteção de Dados) no ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Estado de sessão

Estado de sessão é um cenário do ASP.NET Core para o armazenamento de dados de usuário enquanto o usuário procura um aplicativo Web. Estado de sessão usa um armazenamento mantido pelo aplicativo para que os dados persistam entre solicitações de um cliente. Os dados da sessão são apoiados por um cache e considerados dados efêmeras. O site deve continuar a funcionar sem os dados da sessão. Os dados críticos do aplicativo devem ser armazenados no banco de dados do usuário e armazenados em cache na sessão apenas como uma otimização de desempenho.

A sessão não tem suporte em [SignalR](xref:signalr/index) aplicativos porque um [ SignalR Hub](xref:signalr/hubs) pode ser executado independentemente de um contexto http. Por exemplo, isso pode ocorrer quando uma solicitação de sondagem longa é mantida aberta por um hub além do tempo de vida do contexto HTTP da solicitação.

ASP.NET Core mantém o estado da sessão fornecendo um cookie ao cliente que contém uma ID de sessão. A ID da sessão do cookie:

* É enviado para o aplicativo com cada solicitação.
* É usado pelo aplicativo para buscar os dados da sessão.

Estado de sessão exibe os seguintes comportamentos:

* O cookie de sessão é específico para o navegador. As sessões não são compartilhadas entre navegadores.
* Cookies da sessão são excluídos quando a sessão do navegador termina.
* Se um cookie for recebido de uma sessão expirada, será criada uma nova sessão que usa o mesmo cookie de sessão.
* Sessões vazias não são mantidas. A sessão deve ter pelo menos um valor definido para persistir a sessão entre solicitações. Quando uma sessão não é mantida, uma nova ID de sessão é gerada para cada nova solicitação.
* O aplicativo mantém uma sessão por um tempo limitado após a última solicitação. O aplicativo define o tempo limite da sessão ou usa o valor padrão de 20 minutos. O estado da sessão é ideal para armazenar dados do usuário:
  * Isso é específico para uma sessão específica.
  * Onde os dados não exigem armazenamento permanente entre as sessões.
* Os dados da sessão são excluídos quando a <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> implementação é chamada ou quando a sessão expira.
* Não há nenhum mecanismo padrão para informar o código do aplicativo de que um navegador cliente foi fechado ou quando o cookie de sessão foi excluído ou expirou no cliente.
* Os cookies de estado de sessão não são marcados como essenciais por padrão. O estado da sessão não funciona, a menos que o rastreamento seja permitido pelo visitante do site. Para obter mais informações, consulte <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Não armazene dados confidenciais no estado de sessão. O usuário não pode fechar o navegador e limpar o cookie de sessão. Alguns navegadores mantêm cookies de sessão válidos entre as janelas do navegador. Uma sessão pode não estar restrita a um único usuário. O próximo usuário pode continuar a procurar o aplicativo com o mesmo cookie de sessão.

O provedor de cache na memória armazena dados de sessão na memória do servidor em que o aplicativo reside. Em um cenário de farm de servidores:

* Use *sessões persistentes* para vincular cada sessão a uma instância de aplicativo específico em um servidor individual. O [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) usa o [ARR (Application Request Routing)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) para impor as sessões persistentes por padrão. Entretanto, sessões autoadesivas podem afetar a escalabilidade e complicar atualizações de aplicativos Web. Uma abordagem melhor é usar um Redis ou cache distribuído do SQL Server, que não requer sessões persistentes. Para obter mais informações, consulte <xref:performance/caching/distributed>.
* O cookie de sessão é criptografado via <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> . A Proteção de Dados deve ser configurada corretamente para ler os cookies de sessão em cada computador. Para obter mais informações, veja <xref:security/data-protection/introduction> e [Provedores de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Configurar o estado de sessão

O pacote [Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) :

* Está incluído implicitamente pela estrutura.
* Fornece middleware para gerenciar o estado da sessão.

Para habilitar o middleware da sessão, `Startup` deve conter:

* Qualquer um dos <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> caches de memória. A implementação `IDistributedCache` é usada como um repositório de backup para a sessão. Para obter mais informações, consulte <xref:performance/caching/distributed>.
* Uma chamada para <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> no `ConfigureServices` .
* Uma chamada para <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> no `Configure` .

O código a seguir mostra como configurar o provedor de sessão na memória com uma implementação padrão na memória de `IDistributedCache`:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,45)]

O código anterior define um tempo limite curto para simplificar o teste.

A ordem do middleware é importante.  Chame `UseSession` After `UseRouting` e before `UseEndpoints` . Consulte [ordenação de middleware](xref:fundamentals/middleware/index#order).

[HttpContext.Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session) estará disponível depois que o estado de sessão for configurado.

`HttpContext.Session` não pode ser acessado antes que `UseSession` tenha sido chamado.

Não é possível criar uma nova sessão com um novo cookie de sessão depois que o aplicativo começou a gravar no fluxo de resposta. A exceção é registrada no log do servidor Web e não é exibida no navegador.

### <a name="load-session-state-asynchronously"></a>Carregue o estado de sessão de maneira assíncrona

O provedor de sessão padrão no ASP.NET Core carrega registros de sessão do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> armazenamento de backup subjacente de forma assíncrona somente se o <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> método for explicitamente chamado antes dos <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A> <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> métodos, ou <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> . Se `LoadAsync` não for chamado primeiro, o registro de sessão subjacente será carregado de maneira síncrona, o que pode incorrer em uma penalidade de desempenho em escala.

Para que os aplicativos imponham esse padrão, empacote as <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> <xref:Microsoft.AspNetCore.Session.DistributedSession> implementações e com versões que lançam uma exceção se o `LoadAsync` método não for chamado antes `TryGetValue` , `Set` , ou `Remove` . Registre as versões encapsuladas no contêiner de serviços.

### <a name="session-options"></a>Opções da sessão

Para substituir os padrões de sessão, use <xref:Microsoft.AspNetCore.Builder.SessionOptions> .

| Opção | Descrição |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Determina as configurações usadas para criar o cookie. <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name>o padrão é <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> ( `.AspNetCore.Session` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path>o padrão é <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> ( `/` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite>o padrão é <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> ( `1` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly>o padrão é `true` . <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>o padrão é `false` . |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | O `IdleTimeout` indica por quanto tempo a sessão pode ficar ociosa antes de seu conteúdo ser abandonado. Cada acesso à sessão redefine o tempo limite. Essa configuração aplica-se somente ao conteúdo da sessão, não ao cookie. O padrão é de 20 minutos. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | O tempo máximo permitido para carregar uma sessão do repositório ou para confirmá-la de volta para o repositório. Essa configuração pode se aplicar somente a operações assíncronas. Esse tempo limite pode ser desabilitado usando <xref:System.Threading.Timeout.InfiniteTimeSpan> . O padrão é 1 minuto. |

A sessão usa um cookie para rastrear e identificar solicitações de um único navegador. Por padrão, esse cookie se chama `.AspNetCore.Session`, e usa um caminho de `/`. Como o padrão do cookie não especifica um domínio, ele não é disponibilizado para o script do lado do cliente na página (porque o <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> padrão é `true` ).

Para substituir os padrões de sessão do cookie, use <xref:Microsoft.AspNetCore.Builder.SessionOptions>:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

O aplicativo usa a <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> propriedade para determinar por quanto tempo uma sessão pode ficar ociosa antes que seu conteúdo no cache do servidor seja abandonado. Essa propriedade é independente da expiração do cookie. Cada solicitação que passa pelo [Middleware da Sessão](xref:Microsoft.AspNetCore.Session.SessionMiddleware) redefine o tempo limite.

Estado de sessão é *sem bloqueio*. Se duas solicitações tentarem simultaneamente modificar o conteúdo de uma sessão, a última solicitação substituirá a primeira. `Session` é implementado como uma *sessão coerente*, o que significa que todo o conteúdo é armazenado junto. Quando duas solicitações buscam modificar valores de sessão diferentes, a última solicitação pode substituir as alterações de sessão feitas pelo primeira.

### <a name="set-and-get-session-values"></a>Definir e obter valores de Session

O estado da sessão é acessado de uma classe Razor pages ou de uma <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> <xref:Microsoft.AspNetCore.Mvc.Controller> classe MVC com <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> . Essa propriedade é uma <xref:Microsoft.AspNetCore.Http.ISession> implementação.

A implementação de `ISession` fornece vários métodos de extensão para definir e recuperar valores de inteiro e cadeia de caracteres. Os métodos de extensão estão no <xref:Microsoft.AspNetCore.Http> namespace.

Métodos de extensão `ISession`:

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

O exemplo a seguir recupera o valor da sessão para a `IndexModel.SessionKeyName` chave ( `_Name` no aplicativo de exemplo) em uma Razor página de páginas:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

O exemplo a seguir mostra como definir e obter um número inteiro e uma cadeia de caracteres:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Todos os dados de sessão devem ser serializados para habilitar um cenário de cache distribuído, mesmo ao usar o cache na memória. Serializadores de cadeia de caracteres e inteiros são fornecidos pelos métodos de extensão do <xref:Microsoft.AspNetCore.Http.ISession> . Tipos complexos devem ser serializados pelo usuário por meio de outro mecanismo, como JSON.

Use o seguinte código de exemplo para serializar objetos:

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

O exemplo a seguir mostra como definir e obter um objeto serializável com a `SessionExtensions` classe:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core expõe as Razor páginas [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) ou Controller <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Essa propriedade armazena dados até que eles sejam lidos em outra solicitação. Os métodos [Keep (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) e [Peek (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) podem ser usados para examinar os dados sem exclusão no final da solicitação. [Manter](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) marca todos os itens no dicionário para retenção. `TempData` é:

* Útil para redirecionamento quando os dados são necessários para mais de uma única solicitação.
* Implementado por `TempData` provedores usando cookies ou o estado da sessão.

## <a name="tempdata-samples"></a>Amostras de TempData

Considere a seguinte página que cria um cliente:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

A página a seguir exibe `TempData["Message"]` :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Na marcação anterior, no final da solicitação, `TempData["Message"]` **não** é excluído porque `Peek` é usado. A atualização da página exibe o conteúdo de `TempData["Message"]` .

A marcação a seguir é semelhante ao código anterior, mas usa `Keep` para preservar os dados no final da solicitação:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

A navegação entre as páginas *IndexPeek* e *IndexKeep* não será excluída `TempData["Message"]` .

O código a seguir exibe `TempData["Message"]` , mas no final da solicitação, `TempData["Message"]` é excluído:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Provedores de TempData

O provedor TempData baseado em cookies é usado por padrão para armazenar TempData em cookies.

Os dados do cookie são criptografados usando <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> , codificados com <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> e, em seguida, em bloco. O tamanho máximo do cookie é menor que [4096 bytes](http://www.faqs.org/rfcs/rfc2965.html) devido à criptografia e ao agrupamento. Os dados do cookie não são compactados porque a compactação de dados criptografados pode levar a problemas de segurança, como os ataques [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) e [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)). Para obter mais informações sobre o provedor TempData baseado em cookie, consulte <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider> .

### <a name="choose-a-tempdata-provider"></a>Escolha um provedor de TempData

Escolher um provedor de TempData envolve várias considerações, como:

* O aplicativo já usa estado de sessão? Nesse caso, usar o provedor de estado de sessão TempData não tem nenhum custo adicional para o aplicativo além do tamanho dos dados.
* O aplicativo usa TempData somente com moderação para quantidades relativamente pequenas de dados, até 500 bytes? Em caso afirmativo, o provedor de TempData do cookie adiciona um pequeno custo a cada solicitação que transportar TempData. Caso contrário, o provedor de TempData do estado de sessão pode ser útil para evitar fazer viagens de ida e volta para uma grande quantidade de dados a cada solicitação até que TempData seja consumido.
* O aplicativo é executado em um farm de servidores em vários servidores? Se for o caso, não será necessária nenhuma configuração adicional para usar o provedor TempData do cookie fora da Proteção de Dados (confira <xref:security/data-protection/introduction> e [Provedores de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-providers)).

A maioria dos clientes da Web, como navegadores da Web, impõe limites no tamanho máximo de cada cookie e no número total de cookies. Ao usar o provedor de TempData do cookie, verifique se o aplicativo não excederá [esses limites](http://www.faqs.org/rfcs/rfc2965.html). Considere o tamanho total dos dados. Conta para aumento no tamanho de cookie devido à criptografia e ao agrupamento.

### <a name="configure-the-tempdata-provider"></a>Configurar o provedor de TempData

O provedor de TempData baseado em cookie é habilitado por padrão.

Para habilitar o provedor TempData baseado em sessão, use o <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> método de extensão. Apenas uma chamada para `AddSessionStateTempDataProvider` é necessária:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,8,30)]

## <a name="query-strings"></a>Cadeias de consulta

É possível passar uma quantidade limitada de dados de uma solicitação para outra adicionando-a à cadeia de caracteres de consulta da nova solicitação. Isso é útil para capturar o estado de uma maneira persistente que permita que links com estado inserido sejam compartilhados por email ou por redes sociais. Uma vez que cadeias de consulta de URL são públicas, nunca use cadeias de consulta para dados confidenciais.

Além do compartilhamento indesejado, a inclusão de dados em cadeias de caracteres de consulta pode expor o aplicativo a ataques [CSRF (solicitação intersite forjada)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) . Qualquer estado de sessão preservado deve proteger contra ataques CSRF. Para obter mais informações, confira [Impedir ataques de XSRF/CSRF (solicitação intersite forjada)](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Campos ocultos

Dados podem ser salvos em campos de formulário ocultos e postados novamente na solicitação seguinte. Isso é comum em formulários com várias páginas. Uma vez que o cliente potencialmente pode adulterar os dados, o aplicativo deve sempre revalidar os dados armazenados nos campos ocultos.

## <a name="httpcontextitems"></a>HttpContext.Items

A <xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> coleção é usada para armazenar dados durante o processamento de uma única solicitação. O conteúdo da coleção é descartado após uma solicitação ser processada. A coleção `Items` costuma ser usada para permitir que componentes ou middleware se comuniquem quando operam em diferentes momentos durante uma solicitação e não têm nenhuma maneira direta de passar parâmetros.

No exemplo a seguir, o [middleware](xref:fundamentals/middleware/index) adiciona `isVerified` à `Items` coleção:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

Para middleware que é usado somente em um único aplicativo, chaves fixas `string` são aceitáveis. O middleware compartilhado entre aplicativos deve usar chaves de objeto exclusivas para evitar colisões de chave. O exemplo a seguir mostra como usar uma chave de objeto exclusiva definida em uma classe de middleware:

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Outros códigos podem acessar o valor armazenado em `HttpContext.Items` usando a chave exposta pela classe do middleware:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Essa abordagem também tem a vantagem de eliminar o uso de cadeias de caracteres de chave no código.

## <a name="cache"></a>Cache

O cache é uma maneira eficiente de armazenar e recuperar dados. O aplicativo pode controlar o tempo de vida de itens em cache. Para obter mais informações, consulte <xref:performance/caching/response>.

Dados armazenados em cache não são associados uma solicitação, usuário ou sessão específico. **Não armazene em cache dados específicos do usuário que possam ser recuperados por outras solicitações do usuário.**

Para armazenar em cache os dados de todo o aplicativo, consulte <xref:performance/caching/memory> .

## <a name="common-errors"></a>Erros comuns

* "Não é possível resolver o serviço para o tipo 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' ao tentar ativar 'Microsoft.AspNetCore.Session.DistributedSessionStore'."

  Isso geralmente é causado pela falha ao configurar pelo menos uma `IDistributedCache` implementação. Para obter mais informações, consulte <xref:performance/caching/distributed> e <xref:performance/caching/memory>.

Se o middleware de sessão não persistir em uma sessão:

* O middleware registra a exceção e a solicitação continua normalmente.
* Isso leva a um comportamento imprevisível.

O middleware de sessão pode falhar ao persistir uma sessão se o armazenamento de backup não estiver disponível. Por exemplo, um usuário armazena um carrinho de compras na sessão. O usuário adiciona um item ao carrinho, mas a confirmação falha. O aplicativo não sabe sobre a falha, assim, relata ao usuário que o item foi adicionado ao seu carrinho, o que não é verdade.

A abordagem recomendada para verificar se há erros é chamar `await feature.Session.CommitAsync` quando o aplicativo é concluído gravando na sessão. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*> gerará uma exceção se o repositório de backup não estiver disponível. Se `CommitAsync` falhar, o aplicativo poderá processar a exceção. <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*>gera sob as mesmas condições quando o armazenamento de dados está indisponível.
  
## <a name="signalr-and-session-state"></a>SignalRe estado da sessão

SignalRos aplicativos não devem usar o estado de sessão para armazenar informações. SignalRos aplicativos podem armazenar por estado de conexão no `Context.Items` no Hub. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Recursos adicionais

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose) e [Luke Latham](https://github.com/guardrex)

O HTTP é um protocolo sem estado. Sem realizar etapas adicionais, as solicitações HTTP são mensagens independentes que não mantêm os valores de usuário nem o estado do aplicativo. Este artigo descreve várias abordagens para preservar dados do usuário e estado de aplicativo entre solicitações.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Gerenciamento de estado

O estado pode ser armazenado usando várias abordagens. Cada abordagem é descrita posteriormente neste tópico.

| Abordagem de armazenamento | Mecanismo de armazenamento |
| ---------------- | ----------------- |
| [Arar](#cookies) | Cookies HTTP (podem incluir dados armazenados usando o código de aplicativo do lado do servidor) |
| [Estado da sessão](#session-state) | Cookies HTTP e o código de aplicativo do lado do servidor |
| [TempData](#tempdata) | Estado de sessão ou cookies HTTP |
| [Cadeias de consulta](#query-strings) | Cadeias de caracteres de consulta HTTP |
| [Campos ocultos](#hidden-fields) | Campos de formulário HTTP |
| [HttpContext.Items](#httpcontextitems) | Código do aplicativo do lado do servidor |
| [Cache](#cache) | Código do aplicativo do lado do servidor |
| [Injeção de dependência](#dependency-injection) | Código do aplicativo do lado do servidor |

## <a name="cookies"></a>Cookies

Cookies armazenam dados entre solicitações. Como os cookies são enviados com cada solicitação, seu tamanho deve ser reduzido ao mínimo. Idealmente, somente um identificador deve ser armazenado em um cookie com os dados armazenados pelo aplicativo. A maioria dos navegadores restringe o tamanho do cookie a 4.096 bytes. Somente um número limitado de cookies está disponível para cada domínio.

Uma vez que os cookies estão sujeitos à adulteração, eles devem ser validados pelo aplicativo. Os cookies podem ser excluídos por usuários e expirarem em clientes. No entanto, os cookies geralmente são a forma mais durável de persistência de dados no cliente.

Frequentemente, cookies são usados para personalização quando o conteúdo é personalizado para um usuário conhecido. O usuário é apenas identificado, e não autenticado, na maioria dos casos. O cookie pode armazenar o nome do usuário, o nome da conta ou a ID de usuário único (como um GUID). Você pode usar o cookie para acessar as configurações personalizadas do usuário, como cor preferida da tela de fundo do site.

Esteja ciente do [RGPD (Regulamento Geral sobre a Proteção de Dados) da União Europeia](https://ec.europa.eu/info/law/law-topic/data-protection) ao emitir cookies e lidar com questões de privacidade. Para obter mais informações, veja [Suporte ao RGPD (Regulamento Geral sobre a Proteção de Dados) no ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Estado de sessão

Estado de sessão é um cenário do ASP.NET Core para o armazenamento de dados de usuário enquanto o usuário procura um aplicativo Web. Estado de sessão usa um armazenamento mantido pelo aplicativo para que os dados persistam entre solicitações de um cliente. Os dados da sessão são apoiados por um cache e considerados dados efêmeros&mdash;o site deve continuar funcionando sem os dados da sessão. Os dados críticos do aplicativo devem ser armazenados no banco de dados do usuário e armazenados em cache na sessão apenas como uma otimização de desempenho.

> [!NOTE]
> A sessão não tem suporte em [SignalR](xref:signalr/index) aplicativos porque um [ SignalR Hub](xref:signalr/hubs) pode ser executado independentemente de um contexto http. Por exemplo, isso pode ocorrer quando uma solicitação de sondagem longa é mantida aberta por um hub além do tempo de vida do contexto HTTP da solicitação.

O ASP.NET Core mantém o estado de sessão fornecendo um cookie para o cliente que contém uma ID de sessão, que é enviada para o aplicativo com cada solicitação. O aplicativo usa a ID da sessão para buscar os dados da sessão.

Estado de sessão exibe os seguintes comportamentos:

* Uma vez que o cookie da sessão é específico ao navegador, não é possível compartilhar sessões entre navegadores.
* Cookies da sessão são excluídos quando a sessão do navegador termina.
* Se um cookie for recebido de uma sessão expirada, será criada uma nova sessão que usa o mesmo cookie de sessão.
* Sessões vazias não são mantidas&mdash;a sessão deve ter pelo menos um valor definido nela para que mantenha a sessão entre solicitações. Quando uma sessão não é mantida, uma nova ID de sessão é gerada para cada nova solicitação.
* O aplicativo mantém uma sessão por um tempo limitado após a última solicitação. O aplicativo define o tempo limite da sessão ou usa o valor padrão de 20 minutos. Estado de sessão é ideal para armazenar dados de usuário específicos para uma sessão em particular, mas em que os dados não requerem armazenamento permanente entre sessões.
* Os dados da sessão são excluídos quando a <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> implementação é chamada ou quando a sessão expira.
* Não há nenhum mecanismo padrão para informar o código do aplicativo de que um navegador cliente foi fechado ou quando o cookie de sessão foi excluído ou expirou no cliente.
* Os modelos ASP.NET Core MVC e de Razor páginas incluem suporte para regulamento geral sobre a proteção de dados (GDPR). Os cookies do estado de sessão não são marcados como essenciais por padrão, portanto, o estado de sessão não é funcional, a menos que o rastreamento seja permitido pelo visitante do site. Para obter mais informações, consulte <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Não armazene dados confidenciais no estado de sessão. O usuário não pode fechar o navegador e limpar o cookie de sessão. Alguns navegadores mantêm cookies de sessão válidos entre as janelas do navegador. Uma sessão não pode ser restringida a um único usuário&mdash;o próximo usuário pode continuar a procurar o aplicativo com o mesmo cookie de sessão.

O provedor de cache na memória armazena dados de sessão na memória do servidor em que o aplicativo reside. Em um cenário de farm de servidores:

* Use *sessões persistentes* para vincular cada sessão a uma instância de aplicativo específico em um servidor individual. O [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) usa o [ARR (Application Request Routing)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) para impor as sessões persistentes por padrão. Entretanto, sessões autoadesivas podem afetar a escalabilidade e complicar atualizações de aplicativos Web. Uma abordagem melhor é usar um Redis ou cache distribuído do SQL Server, que não requer sessões persistentes. Para obter mais informações, consulte <xref:performance/caching/distributed>.
* O cookie de sessão é criptografado via <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> . A Proteção de Dados deve ser configurada corretamente para ler os cookies de sessão em cada computador. Para obter mais informações, veja <xref:security/data-protection/introduction> e [Provedores de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Configurar o estado de sessão

O pacote [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/), incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), fornece middleware para gerenciar o estado de sessão. Para habilitar o middleware da sessão, `Startup` deve conter:

* Qualquer um dos <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> caches de memória. A implementação `IDistributedCache` é usada como um repositório de backup para a sessão. Para obter mais informações, consulte <xref:performance/caching/distributed>.
* Uma chamada para <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> no `ConfigureServices` .
* Uma chamada para <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> no `Configure` .

O código a seguir mostra como configurar o provedor de sessão na memória com uma implementação padrão na memória de `IDistributedCache`:

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

A ordem do middleware é importante. No exemplo anterior, uma exceção `InvalidOperationException` ocorre quando `UseSession` é invocado após `UseMvc`. Para obter mais informações, veja [Ordenação de Middleware](xref:fundamentals/middleware/index#order).

<xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType>está disponível após a configuração do estado de sessão.

`HttpContext.Session` não pode ser acessado antes que `UseSession` tenha sido chamado.

Não é possível criar uma nova sessão com um novo cookie de sessão depois que o aplicativo começou a gravar no fluxo de resposta. A exceção é registrada no log do servidor Web e não é exibida no navegador.

### <a name="load-session-state-asynchronously"></a>Carregue o estado de sessão de maneira assíncrona

O provedor de sessão padrão no ASP.NET Core carrega registros de sessão do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> armazenamento de backup subjacente de forma assíncrona somente se o <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> método for explicitamente chamado antes dos <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A> <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> métodos, ou <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> . Se `LoadAsync` não for chamado primeiro, o registro de sessão subjacente será carregado de maneira síncrona, o que pode incorrer em uma penalidade de desempenho em escala.

Para que os aplicativos imponham esse padrão, empacote as <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> <xref:Microsoft.AspNetCore.Session.DistributedSession> implementações e com versões que lançam uma exceção se o `LoadAsync` método não for chamado antes `TryGetValue` , `Set` , ou `Remove` . Registre as versões encapsuladas no contêiner de serviços.

### <a name="session-options"></a>Opções da sessão

Para substituir os padrões de sessão, use <xref:Microsoft.AspNetCore.Builder.SessionOptions> .

| Opção | Descrição |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Determina as configurações usadas para criar o cookie. <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name>o padrão é <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> ( `.AspNetCore.Session` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path>o padrão é <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> ( `/` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite>o padrão é <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> ( `1` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly>o padrão é `true` . <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>o padrão é `false` . |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | O `IdleTimeout` indica por quanto tempo a sessão pode ficar ociosa antes de seu conteúdo ser abandonado. Cada acesso à sessão redefine o tempo limite. Essa configuração aplica-se somente ao conteúdo da sessão, não ao cookie. O padrão é de 20 minutos. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | O tempo máximo permitido para carregar uma sessão do repositório ou para confirmá-la de volta para o repositório. Essa configuração pode se aplicar somente a operações assíncronas. Esse tempo limite pode ser desabilitado usando <xref:System.Threading.Timeout.InfiniteTimeSpan> . O padrão é 1 minuto. |

A sessão usa um cookie para rastrear e identificar solicitações de um único navegador. Por padrão, esse cookie se chama `.AspNetCore.Session`, e usa um caminho de `/`. Como o padrão do cookie não especifica um domínio, ele não é disponibilizado para o script do lado do cliente na página (porque o <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> padrão é `true` ).

Para substituir os padrões de sessão do cookie, use `SessionOptions`:

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

O aplicativo usa a <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> propriedade para determinar por quanto tempo uma sessão pode ficar ociosa antes que seu conteúdo no cache do servidor seja abandonado. Essa propriedade é independente da expiração do cookie. Cada solicitação que passa pelo [Middleware da Sessão](xref:Microsoft.AspNetCore.Session.SessionMiddleware) redefine o tempo limite.

Estado de sessão é *sem bloqueio*. Se duas solicitações tentarem simultaneamente modificar o conteúdo de uma sessão, a última solicitação substituirá a primeira. `Session` é implementado como uma *sessão coerente*, o que significa que todo o conteúdo é armazenado junto. Quando duas solicitações buscam modificar valores de sessão diferentes, a última solicitação pode substituir as alterações de sessão feitas pelo primeira.

### <a name="set-and-get-session-values"></a>Definir e obter valores de Session

O estado da sessão é acessado de uma classe Razor pages ou de uma <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> <xref:Microsoft.AspNetCore.Mvc.Controller> classe MVC com <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> . Essa propriedade é uma <xref:Microsoft.AspNetCore.Http.ISession> implementação.

A implementação de `ISession` fornece vários métodos de extensão para definir e recuperar valores de inteiro e cadeia de caracteres. Os métodos de extensão estão no <xref:Microsoft.AspNetCore.Http> namespace (adicione uma `using Microsoft.AspNetCore.Http;` instrução para obter acesso aos métodos de extensão) quando o pacote [Microsoft. AspNetCore. http. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) é referenciado pelo projeto. Ambos os pacotes são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Métodos de extensão `ISession`:

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

O exemplo a seguir recupera o valor da sessão para a `IndexModel.SessionKeyName` chave ( `_Name` no aplicativo de exemplo) em uma Razor página de páginas:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

O exemplo a seguir mostra como definir e obter um número inteiro e uma cadeia de caracteres:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Todos os dados de sessão devem ser serializados para habilitar um cenário de cache distribuído, mesmo ao usar o cache na memória. Serializadores de cadeia de caracteres e inteiros são fornecidos pelos métodos de extensão do <xref:Microsoft.AspNetCore.Http.ISession> ). Tipos complexos devem ser serializados pelo usuário por meio de outro mecanismo, como JSON.

Adicione os seguintes métodos de extensão para definir e obter objetos serializáveis:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

O exemplo a seguir mostra como definir e obter um objeto serializável com os métodos de extensão:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core expõe as Razor páginas [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) ou Controller <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Essa propriedade armazena dados até que eles sejam lidos em outra solicitação. Os métodos [Keep (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) e [Peek (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) podem ser usados para examinar os dados sem exclusão no final da solicitação. [Keep ()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) marca todos os itens no dicionário para retenção. `TempData`é particularmente útil para o redirecionamento quando os dados são necessários para mais do que uma única solicitação. `TempData`é implementado por `TempData` provedores usando cookies ou o estado de sessão.

## <a name="tempdata-samples"></a>Amostras de TempData

Considere a seguinte página que cria um cliente:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

A página a seguir exibe `TempData["Message"]` :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Na marcação anterior, no final da solicitação, `TempData["Message"]` **não** é excluído porque `Peek` é usado. A atualização da página é exibida `TempData["Message"]` .

A marcação a seguir é semelhante ao código anterior, mas usa `Keep` para preservar os dados no final da solicitação:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

A navegação entre as páginas *IndexPeek* e *IndexKeep* não será excluída `TempData["Message"]` .

O código a seguir exibe `TempData["Message"]` , mas no final da solicitação, `TempData["Message"]` é excluído:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Provedores de TempData

O provedor TempData baseado em cookies é usado por padrão para armazenar TempData em cookies.

Os dados do cookie são criptografados usando <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> , codificados com <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> e, em seguida, em bloco. Como o cookie é dividido em partes, o limite de tamanho de cookie único encontrado no ASP.NET Core 1.x não se aplica. Os dados do cookie não são compactados porque a compactação de dados criptografados pode levar a problemas de segurança, como os ataques [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) e [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)). Para obter mais informações sobre o provedor TempData baseado em cookie, consulte <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider> .

### <a name="choose-a-tempdata-provider"></a>Escolha um provedor de TempData

Escolher um provedor de TempData envolve várias considerações, como:

1. O aplicativo já usa estado de sessão? Se for o caso, usar o provedor de TempData do estado de sessão não terá custos adicionais para o aplicativo (além do tamanho dos dados).
2. O aplicativo usa TempData apenas raramente para quantidades relativamente pequenas de dados (até 500 bytes)? Em caso afirmativo, o provedor de TempData do cookie adiciona um pequeno custo a cada solicitação que transportar TempData. Caso contrário, o provedor de TempData do estado de sessão pode ser útil para evitar fazer viagens de ida e volta para uma grande quantidade de dados a cada solicitação até que TempData seja consumido.
3. O aplicativo é executado em um farm de servidores em vários servidores? Se for o caso, não será necessária nenhuma configuração adicional para usar o provedor TempData do cookie fora da Proteção de Dados (confira <xref:security/data-protection/introduction> e [Provedores de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-providers)).

> [!NOTE]
> A maioria dos clientes da Web (como navegadores da Web) impõem limites quanto ao tamanho máximo de cada cookie, o número total de cookies ou ambos. Ao usar o provedor TempData do cookie, verifique se o aplicativo não ultrapassará esses limites. Considere o tamanho total dos dados. Conta para aumento no tamanho de cookie devido à criptografia e ao agrupamento.

### <a name="configure-the-tempdata-provider"></a>Configurar o provedor de TempData

O provedor de TempData baseado em cookie é habilitado por padrão.

Para habilitar o provedor TempData baseado em sessão, use o <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> método de extensão:

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

A ordem do middleware é importante. No exemplo anterior, uma exceção `InvalidOperationException` ocorre quando `UseSession` é invocado após `UseMvc`. Para obter mais informações, veja [Ordenação de Middleware](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> Se o alvo for o .NET Framework e o provedor TempData baseado em sessão for usado, adicione o pacote [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) ao projeto.

## <a name="query-strings"></a>Cadeias de consulta

É possível passar uma quantidade limitada de dados de uma solicitação para outra adicionando-a à cadeia de caracteres de consulta da nova solicitação. Isso é útil para capturar o estado de uma maneira persistente que permita que links com estado inserido sejam compartilhados por email ou por redes sociais. Uma vez que cadeias de consulta de URL são públicas, nunca use cadeias de consulta para dados confidenciais.

Além da possibilidade de ocorrência de compartilhamento não intencional, incluir dados em cadeias de consulta pode criar oportunidades para ataques de [CSRF (solicitação intersite forjada)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)), que podem enganar os usuários para que eles visitem sites mal-intencionados enquanto estão autenticados. Invasores então podem roubar dados do usuário do aplicativo ou executar ações mal-intencionadas em nome do usuário. Qualquer estado de sessão ou aplicativo preservado deve se proteger contra ataques CSRF. Para obter mais informações, confira [Impedir ataques de XSRF/CSRF (solicitação intersite forjada)](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Campos ocultos

Dados podem ser salvos em campos de formulário ocultos e postados novamente na solicitação seguinte. Isso é comum em formulários com várias páginas. Uma vez que o cliente potencialmente pode adulterar os dados, o aplicativo deve sempre revalidar os dados armazenados nos campos ocultos.

## <a name="httpcontextitems"></a>HttpContext.Items

A <xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> coleção é usada para armazenar dados durante o processamento de uma única solicitação. O conteúdo da coleção é descartado após uma solicitação ser processada. A coleção `Items` costuma ser usada para permitir que componentes ou middleware se comuniquem quando operam em diferentes momentos durante uma solicitação e não têm nenhuma maneira direta de passar parâmetros.

No exemplo a seguir, [middleware](xref:fundamentals/middleware/index) adiciona `isVerified` à coleção `Items`.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

Mais tarde no pipeline, outro middleware poderá acessar o valor de `isVerified`:

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Para middleware usado apenas por um único aplicativo, chaves `string` são aceitáveis. Middleware compartilhado entre instâncias do aplicativo deve usar chaves de objeto únicas para evitar colisões de chaves. O exemplo a seguir mostra como usar uma chave de objeto exclusiva definida em uma classe de middleware:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Outros códigos podem acessar o valor armazenado em `HttpContext.Items` usando a chave exposta pela classe do middleware:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Essa abordagem também tem a vantagem de eliminar o uso de cadeias de caracteres de chave no código.

## <a name="cache"></a>Cache

O cache é uma maneira eficiente de armazenar e recuperar dados. O aplicativo pode controlar o tempo de vida de itens em cache.

Dados armazenados em cache não são associados uma solicitação, usuário ou sessão específico. **Tenha cuidado para não armazenar em cache dados específicos do usuário que podem ser recuperados pelas solicitações de outros usuários.**

Para obter mais informações, consulte <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Injeção de dependência

Use a [Injeção de dependência](xref:fundamentals/dependency-injection) para disponibilizar dados para todos os usuários:

1. Defina um serviço que contenha os dados. Por exemplo, uma classe denominada `MyAppData` está definida:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Adicione a classe de serviço a `Startup.ConfigureServices`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Consuma a classe de serviço de dados:

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a>Erros comuns

* "Não é possível resolver o serviço para o tipo 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' ao tentar ativar 'Microsoft.AspNetCore.Session.DistributedSessionStore'."

  Geralmente, isso é causado quando não é configurada pelo menos uma implementação de `IDistributedCache`. Para obter mais informações, consulte <xref:performance/caching/distributed> e <xref:performance/caching/memory>.

* Caso a sessão de middleware não consiga manter uma sessão (por exemplo, se o repositório de backup não estiver disponível), o middleware registrará a exceção e a solicitação continuará normalmente. Isso leva a um comportamento imprevisível.

  Por exemplo, um usuário armazena um carrinho de compras na sessão. O usuário adiciona um item ao carrinho, mas a confirmação falha. O aplicativo não sabe sobre a falha, assim, relata ao usuário que o item foi adicionado ao seu carrinho, o que não é verdade.

  A abordagem recomendada para verificar se há erros é chamar `await feature.Session.CommitAsync();` do código de aplicativo quando o aplicativo tiver terminado de gravar na sessão. `CommitAsync` gerará uma exceção se o repositório de backup não estiver disponível. Se `CommitAsync` falhar, o aplicativo poderá processar a exceção. `LoadAsync` gera sob as mesmas condições em que o armazenamento de dados não está disponível.
  
## <a name="signalr-and-session-state"></a>SignalRe estado da sessão

SignalRos aplicativos não devem usar o estado de sessão para armazenar informações. SignalRos aplicativos podem armazenar por estado de conexão no `Context.Items` no Hub. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Recursos adicionais

<xref:host-and-deploy/web-farm>
::: moniker-end

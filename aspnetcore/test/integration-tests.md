---
title: Testes de integração no Núcleo ASP.NET
author: rick-anderson
description: Saiba como testes de integração garantem que os componentes do aplicativo funcionem corretamente no nível de infraestrutura, incluindo o banco de dados, o sistema de arquivos e a rede.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
uid: test/integration-tests
ms.openlocfilehash: 414e47b9c5a1c843755bd79d313f503b554945db
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664692"
---
# <a name="integration-tests-in-aspnet-core"></a>Testes de integração no Núcleo ASP.NET

Por [Javier Calvarro Nelson](https://github.com/javiercn), Steve [Smith](https://ardalis.com/), e [Jos van der Til](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

Os testes de integração garantem que os componentes de um aplicativo funcionem corretamente em um nível que inclui a infra-estrutura de suporte do aplicativo, como banco de dados, sistema de arquivos e rede. ASP.NET Core suporta testes de integração usando uma estrutura de teste unitária com um host web de teste e um servidor de teste na memória.

Este tópico pressupõe uma compreensão básica dos testes unitários. Se não estiver familiarizado com os conceitos de teste, consulte o [tópico 0 NET Core e .NET Standard](/dotnet/core/testing/) e seu conteúdo vinculado.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([como baixar](xref:index#how-to-download-a-sample))

O aplicativo de exemplo é um aplicativo Razor Pages e assume uma compreensão básica das Páginas de Navalha. Se não estiver familiarizado com páginas de barbear, veja os seguintes tópicos:

* [Introdução a Páginas do Razor](xref:razor-pages/index)
* [Introdução a Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* [Testes de unidades de páginas Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Para testar SPAs, recomendamos uma ferramenta como [o Selenium,](https://www.seleniumhq.org/)que pode automatizar um navegador.

## <a name="introduction-to-integration-tests"></a>Introdução aos testes de integração

Testes de integração avaliam os componentes de um aplicativo em um nível mais amplo do que [os testes unitários](/dotnet/core/testing/). Os testes unitários são usados para testar componentes isolados de software, como métodos de classe individuais. Testes de integração confirmam que dois ou mais componentes do aplicativo trabalham juntos para produzir um resultado esperado, possivelmente incluindo todos os componentes necessários para processar totalmente uma solicitação.

Esses testes mais amplos são usados para testar a infra-estrutura e toda a estrutura do aplicativo, muitas vezes incluindo os seguintes componentes:

* Banco de dados
* Sistema de arquivos
* Dispositivos de rede
* Pipeline de solicitação-resposta

Os testes unitários utilizam componentes fabricados, conhecidos como *falsos* ou *objetos simulados,* no lugar de componentes de infra-estrutura.

Em contraste com os testes unitários, testes de integração:

* Use os componentes reais que o aplicativo usa na produção.
* Requerem mais código e processamento de dados.
* Demora mais para correr.

Portanto, limitar o uso de testes de integração aos cenários de infraestrutura mais importantes. Se um comportamento pode ser testado usando um teste de unidade ou um teste de integração, escolha o teste da unidade.

> [!TIP]
> Não escreva testes de integração para todas as possíveis permutações de dados e acesso a arquivos com bancos de dados e sistemas de arquivos. Independentemente de quantos lugares em um aplicativo interagem com bancos de dados e sistemas de arquivos, um conjunto focado de testes de integração de leitura, gravação, atualização e exclusão geralmente são capazes de testar adequadamente os componentes do banco de dados e do sistema de arquivos. Use testes unitários para testes de rotina de lógica de método que interagem com esses componentes. Nos testes unitários, o uso de falsificações/simulações de infra-estrutura resulta em uma execução de teste mais rápida.

> [!NOTE]
> Nas discussões de testes de integração, o projeto testado é frequentemente chamado *de Sistema Em Teste*, ou "SUT" para abreviar.
>
> *"SUT" é usado ao longo deste tópico para se referir ao aplicativo ASP.NET Core testado.*

## <a name="aspnet-core-integration-tests"></a>testes de integração do Núcleo ASP.NET

Os testes de integração no ASP.NET Core requerem o seguinte:

* Um projeto de teste é usado para conter e executar os testes. O projeto de teste tem uma referência ao SUT.
* O projeto de teste cria um host web de teste para o SUT e usa um cliente de servidor de teste para lidar com solicitações e respostas com o SUT.
* Um corredor de teste é usado para executar os testes e relatar os resultados do teste.

Os testes de integração seguem uma seqüência de eventos que incluem as etapas habituais *de Organizar,* *Agir*e *Afirmar:*

1. O host web do SUT está configurado.
1. Um cliente de servidor de teste é criado para enviar solicitações ao aplicativo.
1. A etapa de teste *Organizar* é executada: O aplicativo de teste prepara uma solicitação.
1. A etapa de teste *do Act* é executada: O cliente envia a solicitação e recebe a resposta.
1. A etapa de teste *do Assert* é executada: A resposta *real* é validada como um *passe* ou *falha* com base em uma resposta *esperada.*
1. O processo continua até que todos os testes sejam executados.
1. Os resultados dos testes foram informados.

Normalmente, o host web de teste é configurado de forma diferente do host web normal do aplicativo para os testes executados. Por exemplo, um banco de dados diferente ou configurações diferentes do aplicativo podem ser usados para os testes.

Os componentes de infra-estrutura, como o host web de teste e o servidor de teste na memória[(TestServer),](/dotnet/api/microsoft.aspnetcore.testhost.testserver)são fornecidos ou gerenciados pelo pacote [de testes Microsoft.AspNetCore.Mvc.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) O uso deste pacote simplifica a criação e a execução do teste.

O `Microsoft.AspNetCore.Mvc.Testing` pacote lida com as seguintes tarefas:

* Copia o arquivo dependências *(.deps)* do SUT para o diretório *bin* do projeto de teste.
* Define a raiz de [conteúdo](xref:fundamentals/index#content-root) na raiz do projeto do SUT para que arquivos estáticos e páginas/visualizações sejam encontrados quando os testes forem executados.
* Fornece a classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) para simplificar `TestServer`as inicializações do SUT com .

A documentação [de testes](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) da unidade descreve como configurar um projeto de teste e um corredor de teste, juntamente com instruções detalhadas sobre como executar testes e recomendações de como nomear testes e aulas de teste.

> [!NOTE]
> Ao criar um projeto de teste para um aplicativo, separe os testes unitários dos testes de integração em diferentes projetos. Isso ajuda a garantir que os componentes de teste de infra-estrutura não sejam incluídos acidentalmente nos testes da unidade. A separação dos testes de unidade e integração também permite o controle sobre qual conjunto de testes são executados.

Não há praticamente nenhuma diferença entre a configuração para testes de aplicativos Razor Pages e aplicativos MVC. A única diferença está na forma como os testes são nomeados. Em um aplicativo Razor Pages, os testes de pontos finais de `IndexPageTests` página geralmente são nomeados após a classe modelo de página (por exemplo, para testar a integração de componentes para a página Índice). Em um aplicativo MVC, os testes geralmente são organizados por classes `HomeControllerTests` de controladores e nomeados após os controladores que testam (por exemplo, para testar a integração de componentes para o controlador Home).

## <a name="test-app-prerequisites"></a>Pré-requisitos do aplicativo de teste

O projeto de teste deve:

* Consulte o pacote [Microsoft.AspNetCore.Mvc.Testing.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)
* Especifique o SDK`<Project Sdk="Microsoft.NET.Sdk.Web">`da Web no arquivo do projeto ( ).

Esses pré-requisitos podem ser vistos no [aplicativo de amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Inspecione os *testes/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file. O aplicativo de exemplo usa a estrutura de teste [xUnit](https://xunit.github.io/) e a biblioteca de analisador [AngleSharp,](https://anglesharp.github.io/) de modo que o aplicativo de exemplo também faz referência:

* [Xunit](https://www.nuget.org/packages/xunit)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp)

O Entity Framework Core também é usado nos testes. As referências do aplicativo:

* [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>Ambiente SUT

Se o [ambiente](xref:fundamentals/environments) do SUT não estiver definido, o ambiente será padrão para o Desenvolvimento.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Testes básicos com o WebApplicationFactory padrão

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) é usado para criar um [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) para os testes de integração. `TEntryPoint`é a classe de ponto de `Startup` entrada do SUT, geralmente a classe.

As classes de teste implementam uma interface *de fixação de classe* [(IClassFixture)](https://xunit.github.io/docs/shared-context#class-fixture)para indicar que a classe contém testes e fornecem instâncias de objetos compartilhados nos testes da classe.

A classe de `BasicTests`teste `WebApplicationFactory` a seguir, usa o bootstrap do SUT `Get_EndpointsReturnSuccessAndCorrectContentType`e fornece um [HttpClient](/dotnet/api/system.net.http.httpclient) para um método de teste, . O método verifica se o código de status de resposta é bem-sucedido (códigos de status na faixa 200-299) e o `Content-Type` cabeçalho é `text/html; charset=utf-8` para várias páginas de aplicativos.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) cria uma `HttpClient` instância que segue automaticamente redireciona e lida com cookies.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Por padrão, cookies não essenciais não são preservados entre as solicitações quando a [política de consentimento do GDPR](xref:security/gdpr) é ativada. Para preservar cookies não essenciais, como os usados pelo provedor TempData, marque-os como essenciais em seus testes. Para obter instruções sobre como essencial marcar um cookie, consulte [Cookies Essenciais](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Personalizar webapplicationFactory

A configuração do host web pode ser criada `WebApplicationFactory` independentemente das classes de teste herdando para criar uma ou mais fábricas personalizadas:

1. Herdar `WebApplicationFactory` e substituir [configureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). O [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permite a configuração da coleção de serviços com [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   A semeação de banco `InitializeDbForTests` de dados no aplicativo de [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) é realizada pelo método. O método é descrito na [amostra de testes de integração: seção organização do aplicativo de teste.](#test-app-organization)

   O contexto de banco de dados `Startup.ConfigureServices` do SUT está registrado em seu método. O retorno de `builder.ConfigureServices` chamada do aplicativo de teste `Startup.ConfigureServices` é executado *após* a execução do código do aplicativo. A ordem de execução é uma mudança de ruptura para o [Host Genérico](xref:fundamentals/host/generic-host) com a liberação de ASP.NET Core 3.0. Para usar um banco de dados diferente dos testes do que o banco `builder.ConfigureServices`de dados do aplicativo, o contexto de banco de dados do aplicativo deve ser substituído em .

   O aplicativo de amostra encontra o descritor do serviço para o contexto do banco de dados e usa o descritor para remover o registro do serviço. Em seguida, a `ApplicationDbContext` fábrica adiciona um novo que usa um banco de dados na memória para os testes.

   Para se conectar a um banco de dados `UseInMemoryDatabase` diferente do banco de dados na memória, altere a chamada para conectar o contexto a um banco de dados diferente. Para usar um banco de dados de teste do SQL Server:

   * Faça referência ao pacote [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet no arquivo do projeto.
   * Ligue `UseSqlServer` com uma seqüência de conexão para o banco de dados.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Use o `CustomWebApplicationFactory` costume nas aulas de teste. O exemplo a seguir `IndexPageTests` usa a fábrica na classe:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   O cliente do aplicativo de exemplo `HttpClient` está configurado para evitar que os seguintes redirecionamentos. Como explicado mais tarde na seção [de autenticação Mock,](#mock-authentication) isso permite que os testes verifiquem o resultado da primeira resposta do aplicativo. A primeira resposta é um redirecionamento `Location` em muitos desses testes com um cabeçalho.

3. Um teste típico `HttpClient` usa os métodos e auxiliar para processar a solicitação e a resposta:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Qualquer solicitação post ao SUT deve satisfazer a verificação antiforgery que é feita automaticamente pelo [sistema antiforgery](xref:security/data-protection/introduction)de proteção de dados do aplicativo . Para agendar a solicitação post de um teste, o aplicativo de teste deve:

1. Faça uma solicitação para a página.
1. Analise o cookie antiforgery e solicite o token de validação da resposta.
1. Faça a solicitação DO POST com o cookie antiforgery e solicite o token de validação no local.

Os `SendAsync` métodos de extensão auxiliar (*Helpers/HttpClientExtensions.cs)* `GetDocumentAsync` e o método helper *(Helpers/HtmlHelpers.cs)* no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) usam o analisador [AngleSharp](https://anglesharp.github.io/) para lidar com a verificação antiforgery com os seguintes métodos:

* `GetDocumentAsync`&ndash; Recebe a [Mensagea de Resposta http](/dotnet/api/system.net.http.httpresponsemessage) e retorna um `IHtmlDocument`. `GetDocumentAsync`usa uma fábrica que prepara uma `HttpResponseMessage`resposta *virtual* com base no original . Para obter mais informações, consulte a [documentação AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`métodos de `HttpClient` extensão para compor um [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) e chamar [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) para enviar solicitações ao SUT. Sobrecargas `SendAsync` para aceitar o`IHtmlFormElement`formulário HTML ( ) e o seguinte:
  * Enviar botão do`IHtmlElement`formulário ( )
  * Coleta de`IEnumerable<KeyValuePair<string, string>>`valores de forma ( )
  * Enviar botão`IHtmlElement`( )`IEnumerable<KeyValuePair<string, string>>`e valores de formulário ( )

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) é uma biblioteca de análise de terceiros usada para fins de demonstração neste tópico e no aplicativo de amostra. AngleSharp não é suportado ou necessário para testes de integração de aplicativos ASP.NET Core. Outros analisadores podem ser usados, como o [Html Agility Pack (HAP)](https://html-agility-pack.net/). Outra abordagem é escrever código para lidar diretamente com o token de verificação de solicitação do sistema antiforgery e o cookie antiforgery.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personalize o cliente com o WithWebHostBuilder

Quando a configuração adicional é necessária dentro de `WebApplicationFactory` um método de teste, [o ComWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) cria um novo com um [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) que é ainda mais personalizado pela configuração.

O `Post_DeleteMessageHandler_ReturnsRedirectToRoot` método de teste do aplicativo `WithWebHostBuilder`de [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstra o uso de . Este teste realiza uma exclusão de registro no banco de dados, acionando um envio de formulário no SUT.

Como outro teste `IndexPageTests` na classe realiza uma operação que exclui todos os `Post_DeleteMessageHandler_ReturnsRedirectToRoot` registros no banco de dados e pode ser executado antes do método, o banco de dados é ressedío neste método de teste para garantir que um registro esteja presente para o SUT ser excluído. A seleção do `messages` primeiro botão de exclusão do formulário no SUT é simulada na solicitação ao SUT:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Opções de clientes

A tabela a seguir mostra as opções `HttpClient` padrão [de WebApplicationFactoryClientDisponíveis](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) disponíveis ao criar instâncias.

| Opção | Descrição | Padrão |
| ------ | ----------- | ------- |
| [Allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtém ou define `HttpClient` se as instâncias devem ou não seguir automaticamente respostas de redirecionamento. | `true` |
| [Baseaddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtém ou define `HttpClient` o endereço base das instâncias. | `http://localhost` |
| [Manipulação de cookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtém ou `HttpClient` define se as instâncias devem lidar com cookies. | `true` |
| [MaxAutomaticRedirecionações](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtém ou define o número máximo `HttpClient` de respostas de redirecionamento que as instâncias devem seguir. | 7 |

Crie `WebApplicationFactoryClientOptions` a classe e passe-a para o método [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (valores padrão são mostrados no exemplo de código):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Injete serviços simulados

Os serviços podem ser substituídos em um teste com uma chamada para [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) no construtor de host. **Para injetar serviços simulados, `Startup` o SUT deve ter uma classe com um `Startup.ConfigureServices` método.**

O SUT de amostra inclui um serviço escopo que retorna uma cotação. A cotação é incorporada em um campo oculto na página Index quando a página Índice é solicitada.

*Serviços/IQuoteService.cs:*

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Serviços/QuoteService.cs:*

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs:*

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Páginas/Índice.cs:*

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

A marcação a seguir é gerada quando o aplicativo SUT é executado:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Para testar o serviço e citar a injeção em um teste de integração, um serviço simulado é injetado no SUT pelo teste. O serviço simulado substitui `QuoteService` o aplicativo por um serviço fornecido `TestQuoteService`pelo aplicativo de teste, chamado :

*IntegrationTests.IndexPageTests.cs:*

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`é chamado, e o serviço escopo é registrado:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

A marcação produzida durante a execução do `TestQuoteService`teste reflete o texto de citação fornecido por , assim, a afirmação passa:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Autenticação simulada

Os testes `AuthTests` na classe verificam se um ponto final seguro:

* Redireciona um usuário não autenticado para a página de login do aplicativo.
* Retorna o conteúdo para um usuário autenticado.

No SUT, `/SecurePage` a página usa uma convenção [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) para aplicar um [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à página. Para obter mais informações, consulte [as convenções de autorização de Razor Pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

No `Get_SecurePageRedirectsAnUnauthenticatedUser` teste, um [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) é definido para proibir redirecionamentos `false`definindo [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) para :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Ao não permitir que o cliente siga o redirecionamento, as seguintes verificações podem ser feitas:

* O código de status retornado pelo SUT pode ser verificado em relação ao resultado esperado [httpstatusCode.redirecionar,](/dotnet/api/system.net.httpstatuscode) não o código de status final após o redirecionamento para a página de login, que seria [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* O `Location` valor do cabeçalho nos cabeçalhos `http://localhost/Identity/Account/Login`de resposta é verificado para `Location` confirmar que ele começa com , não a resposta final da página de login, onde o cabeçalho não estaria presente.

O aplicativo de <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> teste pode simular um in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) para testar aspectos de autenticação e autorização. Um cenário mínimo retorna um [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

O `TestAuthHandler` é chamado para autenticar um usuário quando `Test` o `AddAuthentication` esquema de `ConfigureTestServices`autenticação é definido para onde está registrado para :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Para obter `WebApplicationFactoryClientOptions`mais informações, consulte a seção [Opções cliente.](#client-options)

## <a name="set-the-environment"></a>Definir o ambiente

Por padrão, o ambiente host e aplicativo do SUT é configurado para usar o ambiente Desenvolvimento. Para substituir o ambiente do SUT:

* Defina `ASPNETCORE_ENVIRONMENT` a variável de `Staging` `Production`ambiente (por exemplo, `Testing`ou outro valor personalizado, como ).
* Substituir `CreateHostBuilder` no aplicativo de teste para ler `ASPNETCORE`variáveis de ambiente prefixadas com .

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Como a infra-estrutura de teste infere o caminho raiz do conteúdo do aplicativo

O `WebApplicationFactory` construtor infere o caminho raiz do [conteúdo](xref:fundamentals/index#content-root) do aplicativo procurando por um [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) `TEntryPoint` no `System.Reflection.Assembly.FullName`conjunto contendo os testes de integração com uma chave igual ao conjunto . Caso um atributo com a chave `WebApplicationFactory` correta não seja encontrado, recue à busca de `TEntryPoint` um arquivo de solução (*.sln)* e anexa o nome do conjunto ao diretório de soluções. O diretório raiz do aplicativo (o caminho raiz do conteúdo) é usado para descobrir visualizações e arquivos de conteúdo.

## <a name="disable-shadow-copying"></a>Desativar a cópia da sombra

A cópia de sombra faz com que os testes sejam executados em um diretório diferente do diretório de saída. Para que os testes funcionem corretamente, a cópia da sombra deve ser desativada. O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) usa xUnit e desativa a cópia de sombra para xUnit, incluindo um arquivo *xunit.runner.json* com a configuração correta. Para obter mais informações, consulte [Configuração xUnit com JSON](https://xunit.github.io/docs/configuring-with-json.html).

Adicione o arquivo *xunit.runner.json* à raiz do projeto de teste com o seguinte conteúdo:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Descarte de objetos

Depois que os `IClassFixture` testes da implementação são executados, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) e [HttpClient](/dotnet/api/system.net.http.httpclient) são eliminados quando xUnit elimina o [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Se os objetos instanciados pelo desenvolvedor exigirem eliminação, elimine-os na `IClassFixture` implementação. Para obter mais informações, consulte [Implementando um método Desfazer](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Amostra de testes de integração

O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) é composto por dois aplicativos:

| Aplicativo | Diretório do projeto | Descrição |
| --- | ----------------- | ----------- |
| Aplicativo de mensagem (o SUT) | *src/RazorPagesProject* | Permite que um usuário adicione, exclua um, exclua todas e analise mensagens. |
| Aplicativo de teste | *testes/RazorPagesProject.Tests* | Usado para testar a integração do SUT. |

Os testes podem ser executados usando os recursos de teste incorporados de um IDE, como [o Visual Studio](https://visualstudio.microsoft.com). Se estiver usando [o Visual Studio Code](https://code.visualstudio.com/) ou a linha de comando, execute o seguinte comando em um prompt de comando no diretório *testes/RazorPagesProject.Tests:*

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Organização do aplicativo de mensagens (SUT)

O SUT é um sistema de mensagens Razor Pages com as seguintes características:

* A página Índice do aplicativo (*Páginas/Index.cshtml* e *Páginas/Index.cshtml.cs*) fornece métodos de modelo de ui e página para controlar a adição, exclusão e análise de mensagens (palavras médias por mensagem).
* Uma mensagem é `Message` descrita pela classe *(Data/Message.cs*) com duas propriedades: `Id` (chave) e `Text` (mensagem). A `Text` propriedade é necessária e limitada a 200 caracteres.
* As mensagens são armazenadas usando [o banco de dados de memória](/ef/core/providers/in-memory/) do Entity Framework&#8224;.
* O aplicativo contém uma camada de acesso a `AppDbContext` dados (DAL) em sua classe de contexto de banco de dados , (*Data/AppDbContext.cs*).
* Se o banco de dados estiver vazio na inicialização do aplicativo, a armazenamento de mensagens será inicializada com três mensagens.
* O aplicativo inclui `/SecurePage` um que só pode ser acessado por um usuário autenticado.

&#8224;O tópico EF, [Teste com a Memória,](/ef/core/miscellaneous/testing/in-memory)explica como usar um banco de dados na memória para testes com o MSTest. Este tópico usa a estrutura de teste [xUnit.](https://xunit.github.io/) Os conceitos de teste e implementações de teste em diferentes estruturas de teste são semelhantes, mas não idênticos.

Embora o aplicativo não use o padrão do repositório e não seja um exemplo eficaz do [padrão Unit of Work (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)razor pages suporta esses padrões de desenvolvimento. Para obter mais informações, consulte [Projetar a camada de persistência da infra-estrutura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e a lógica do controlador de [teste](/aspnet/core/mvc/controllers/testing) (a amostra implementa o padrão de repositório).

### <a name="test-app-organization"></a>Organização do aplicativo de teste

O aplicativo de teste é um aplicativo de console dentro do diretório *testes/RazorPagesProject.Tests.*

| Diretório do aplicativo de teste | Descrição |
| ------------------ | ----------- |
| *Testes auth* | Contém métodos de teste para:<ul><li>Acessando uma página segura por um usuário não autenticado.</li><li>Acessando uma página segura por um <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>usuário autenticado com uma simulação .</li><li>Obtendo um perfil de usuário do GitHub e verificando o login do usuário do perfil.</li></ul> |
| *Testes Básicos* | Contém um método de teste para roteamento e tipo de conteúdo. |
| *IntegrationTests* | Contém os testes de integração `WebApplicationFactory` para a página Índice usando classe personalizada. |
| *Ajudantes/Utilitários* | <ul><li>*Utilities.cs* contém `InitializeDbForTests` o método usado para semear o banco de dados com dados de teste.</li><li>*HtmlHelpers.cs* fornece um método para `IHtmlDocument` retornar um AngleSharp para uso pelos métodos de teste.</li><li>*HttpClientExtensions.cs* fornecer sobrecargas para `SendAsync` enviar solicitações ao SUT.</li></ul> |

A estrutura de teste é [xUnit](https://xunit.github.io/). Os testes de integração são realizados usando o [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), que inclui o [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Como o pacote [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) é usado para configurar o `TestHost` `TestServer` host de teste e o servidor de teste, os pacotes e os pacotes não exigem referências diretas de pacotes na configuração de arquivo de projeto ou desenvolvedor do aplicativo de teste no aplicativo de teste.

**Semeando o banco de dados para testes**

Os testes de integração geralmente requerem um pequeno conjunto de dados no banco de dados antes da execução do teste. Por exemplo, um teste de exclusão exige uma exclusão de registro de banco de dados, de modo que o banco de dados deve ter pelo menos um registro para que a solicitação de exclusão seja bem sucedida.

O aplicativo de amostra semea o banco de dados com três mensagens em *Utilities.cs* que os testes podem usar quando executam:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

O contexto de banco de dados `Startup.ConfigureServices` do SUT está registrado em seu método. O retorno de `builder.ConfigureServices` chamada do aplicativo de teste `Startup.ConfigureServices` é executado *após* a execução do código do aplicativo. Para usar um banco de dados diferente para os testes, `builder.ConfigureServices`o contexto de banco de dados do aplicativo deve ser substituído em . Para obter mais informações, consulte a [seção Personalizar WebApplicationFactory.](#customize-webapplicationfactory)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Os testes de integração garantem que os componentes de um aplicativo funcionem corretamente em um nível que inclui a infra-estrutura de suporte do aplicativo, como banco de dados, sistema de arquivos e rede. ASP.NET Core suporta testes de integração usando uma estrutura de teste unitária com um host web de teste e um servidor de teste na memória.

Este tópico pressupõe uma compreensão básica dos testes unitários. Se não estiver familiarizado com os conceitos de teste, consulte o [tópico 0 NET Core e .NET Standard](/dotnet/core/testing/) e seu conteúdo vinculado.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([como baixar](xref:index#how-to-download-a-sample))

O aplicativo de exemplo é um aplicativo Razor Pages e assume uma compreensão básica das Páginas de Navalha. Se não estiver familiarizado com páginas de barbear, veja os seguintes tópicos:

* [Introdução a Páginas do Razor](xref:razor-pages/index)
* [Introdução a Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* [Testes de unidades de páginas Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Para testar SPAs, recomendamos uma ferramenta como [o Selenium,](https://www.seleniumhq.org/)que pode automatizar um navegador.

## <a name="introduction-to-integration-tests"></a>Introdução aos testes de integração

Testes de integração avaliam os componentes de um aplicativo em um nível mais amplo do que [os testes unitários](/dotnet/core/testing/). Os testes unitários são usados para testar componentes isolados de software, como métodos de classe individuais. Testes de integração confirmam que dois ou mais componentes do aplicativo trabalham juntos para produzir um resultado esperado, possivelmente incluindo todos os componentes necessários para processar totalmente uma solicitação.

Esses testes mais amplos são usados para testar a infra-estrutura e toda a estrutura do aplicativo, muitas vezes incluindo os seguintes componentes:

* Banco de dados
* Sistema de arquivos
* Dispositivos de rede
* Pipeline de solicitação-resposta

Os testes unitários utilizam componentes fabricados, conhecidos como *falsos* ou *objetos simulados,* no lugar de componentes de infra-estrutura.

Em contraste com os testes unitários, testes de integração:

* Use os componentes reais que o aplicativo usa na produção.
* Requerem mais código e processamento de dados.
* Demora mais para correr.

Portanto, limitar o uso de testes de integração aos cenários de infraestrutura mais importantes. Se um comportamento pode ser testado usando um teste de unidade ou um teste de integração, escolha o teste da unidade.

> [!TIP]
> Não escreva testes de integração para todas as possíveis permutações de dados e acesso a arquivos com bancos de dados e sistemas de arquivos. Independentemente de quantos lugares em um aplicativo interagem com bancos de dados e sistemas de arquivos, um conjunto focado de testes de integração de leitura, gravação, atualização e exclusão geralmente são capazes de testar adequadamente os componentes do banco de dados e do sistema de arquivos. Use testes unitários para testes de rotina de lógica de método que interagem com esses componentes. Nos testes unitários, o uso de falsificações/simulações de infra-estrutura resulta em uma execução de teste mais rápida.

> [!NOTE]
> Nas discussões de testes de integração, o projeto testado é frequentemente chamado *de Sistema Em Teste*, ou "SUT" para abreviar.
>
> *"SUT" é usado ao longo deste tópico para se referir ao aplicativo ASP.NET Core testado.*

## <a name="aspnet-core-integration-tests"></a>testes de integração do Núcleo ASP.NET

Os testes de integração no ASP.NET Core requerem o seguinte:

* Um projeto de teste é usado para conter e executar os testes. O projeto de teste tem uma referência ao SUT.
* O projeto de teste cria um host web de teste para o SUT e usa um cliente de servidor de teste para lidar com solicitações e respostas com o SUT.
* Um corredor de teste é usado para executar os testes e relatar os resultados do teste.

Os testes de integração seguem uma seqüência de eventos que incluem as etapas habituais *de Organizar,* *Agir*e *Afirmar:*

1. O host web do SUT está configurado.
1. Um cliente de servidor de teste é criado para enviar solicitações ao aplicativo.
1. A etapa de teste *Organizar* é executada: O aplicativo de teste prepara uma solicitação.
1. A etapa de teste *do Act* é executada: O cliente envia a solicitação e recebe a resposta.
1. A etapa de teste *do Assert* é executada: A resposta *real* é validada como um *passe* ou *falha* com base em uma resposta *esperada.*
1. O processo continua até que todos os testes sejam executados.
1. Os resultados dos testes foram informados.

Normalmente, o host web de teste é configurado de forma diferente do host web normal do aplicativo para os testes executados. Por exemplo, um banco de dados diferente ou configurações diferentes do aplicativo podem ser usados para os testes.

Os componentes de infra-estrutura, como o host web de teste e o servidor de teste na memória[(TestServer),](/dotnet/api/microsoft.aspnetcore.testhost.testserver)são fornecidos ou gerenciados pelo pacote [de testes Microsoft.AspNetCore.Mvc.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) O uso deste pacote simplifica a criação e a execução do teste.

O `Microsoft.AspNetCore.Mvc.Testing` pacote lida com as seguintes tarefas:

* Copia o arquivo dependências *(.deps)* do SUT para o diretório *bin* do projeto de teste.
* Define a raiz de [conteúdo](xref:fundamentals/index#content-root) na raiz do projeto do SUT para que arquivos estáticos e páginas/visualizações sejam encontrados quando os testes forem executados.
* Fornece a classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) para simplificar `TestServer`as inicializações do SUT com .

A documentação [de testes](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) da unidade descreve como configurar um projeto de teste e um corredor de teste, juntamente com instruções detalhadas sobre como executar testes e recomendações de como nomear testes e aulas de teste.

> [!NOTE]
> Ao criar um projeto de teste para um aplicativo, separe os testes unitários dos testes de integração em diferentes projetos. Isso ajuda a garantir que os componentes de teste de infra-estrutura não sejam incluídos acidentalmente nos testes da unidade. A separação dos testes de unidade e integração também permite o controle sobre qual conjunto de testes são executados.

Não há praticamente nenhuma diferença entre a configuração para testes de aplicativos Razor Pages e aplicativos MVC. A única diferença está na forma como os testes são nomeados. Em um aplicativo Razor Pages, os testes de pontos finais de `IndexPageTests` página geralmente são nomeados após a classe modelo de página (por exemplo, para testar a integração de componentes para a página Índice). Em um aplicativo MVC, os testes geralmente são organizados por classes `HomeControllerTests` de controladores e nomeados após os controladores que testam (por exemplo, para testar a integração de componentes para o controlador Home).

## <a name="test-app-prerequisites"></a>Pré-requisitos do aplicativo de teste

O projeto de teste deve:

* Consulte os seguintes pacotes:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Especifique o SDK`<Project Sdk="Microsoft.NET.Sdk.Web">`da Web no arquivo do projeto ( ). O Web SDK é necessário ao fazer referência ao [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Esses pré-requisitos podem ser vistos no [aplicativo de amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Inspecione os *testes/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file. O aplicativo de exemplo usa a estrutura de teste [xUnit](https://xunit.github.io/) e a biblioteca de analisador [AngleSharp,](https://anglesharp.github.io/) de modo que o aplicativo de exemplo também faz referência:

* [Xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>Ambiente SUT

Se o [ambiente](xref:fundamentals/environments) do SUT não estiver definido, o ambiente será padrão para o Desenvolvimento.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Testes básicos com o WebApplicationFactory padrão

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) é usado para criar um [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) para os testes de integração. `TEntryPoint`é a classe de ponto de `Startup` entrada do SUT, geralmente a classe.

As classes de teste implementam uma interface *de fixação de classe* [(IClassFixture)](https://xunit.github.io/docs/shared-context#class-fixture)para indicar que a classe contém testes e fornecem instâncias de objetos compartilhados nos testes da classe.

A classe de `BasicTests`teste `WebApplicationFactory` a seguir, usa o bootstrap do SUT `Get_EndpointsReturnSuccessAndCorrectContentType`e fornece um [HttpClient](/dotnet/api/system.net.http.httpclient) para um método de teste, . O método verifica se o código de status de resposta é bem-sucedido (códigos de status na faixa 200-299) e o `Content-Type` cabeçalho é `text/html; charset=utf-8` para várias páginas de aplicativos.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) cria uma `HttpClient` instância que segue automaticamente redireciona e lida com cookies.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Por padrão, cookies não essenciais não são preservados entre as solicitações quando a [política de consentimento do GDPR](xref:security/gdpr) é ativada. Para preservar cookies não essenciais, como os usados pelo provedor TempData, marque-os como essenciais em seus testes. Para obter instruções sobre como essencial marcar um cookie, consulte [Cookies Essenciais](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Personalizar webapplicationFactory

A configuração do host web pode ser criada `WebApplicationFactory` independentemente das classes de teste herdando para criar uma ou mais fábricas personalizadas:

1. Herdar `WebApplicationFactory` e substituir [configureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). O [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permite a configuração da coleção de serviços com [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   A semeação de banco `InitializeDbForTests` de dados no aplicativo de [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) é realizada pelo método. O método é descrito na [amostra de testes de integração: seção organização do aplicativo de teste.](#test-app-organization)

2. Use o `CustomWebApplicationFactory` costume nas aulas de teste. O exemplo a seguir `IndexPageTests` usa a fábrica na classe:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   O cliente do aplicativo de exemplo `HttpClient` está configurado para evitar que os seguintes redirecionamentos. Como explicado mais tarde na seção [de autenticação Mock,](#mock-authentication) isso permite que os testes verifiquem o resultado da primeira resposta do aplicativo. A primeira resposta é um redirecionamento `Location` em muitos desses testes com um cabeçalho.

3. Um teste típico `HttpClient` usa os métodos e auxiliar para processar a solicitação e a resposta:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Qualquer solicitação post ao SUT deve satisfazer a verificação antiforgery que é feita automaticamente pelo [sistema antiforgery](xref:security/data-protection/introduction)de proteção de dados do aplicativo . Para agendar a solicitação post de um teste, o aplicativo de teste deve:

1. Faça uma solicitação para a página.
1. Analise o cookie antiforgery e solicite o token de validação da resposta.
1. Faça a solicitação DO POST com o cookie antiforgery e solicite o token de validação no local.

Os `SendAsync` métodos de extensão auxiliar (*Helpers/HttpClientExtensions.cs)* `GetDocumentAsync` e o método helper *(Helpers/HtmlHelpers.cs)* no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) usam o analisador [AngleSharp](https://anglesharp.github.io/) para lidar com a verificação antiforgery com os seguintes métodos:

* `GetDocumentAsync`&ndash; Recebe a [Mensagea de Resposta http](/dotnet/api/system.net.http.httpresponsemessage) e retorna um `IHtmlDocument`. `GetDocumentAsync`usa uma fábrica que prepara uma `HttpResponseMessage`resposta *virtual* com base no original . Para obter mais informações, consulte a [documentação AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`métodos de `HttpClient` extensão para compor um [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) e chamar [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) para enviar solicitações ao SUT. Sobrecargas `SendAsync` para aceitar o`IHtmlFormElement`formulário HTML ( ) e o seguinte:
  * Enviar botão do`IHtmlElement`formulário ( )
  * Coleta de`IEnumerable<KeyValuePair<string, string>>`valores de forma ( )
  * Enviar botão`IHtmlElement`( )`IEnumerable<KeyValuePair<string, string>>`e valores de formulário ( )

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) é uma biblioteca de análise de terceiros usada para fins de demonstração neste tópico e no aplicativo de amostra. AngleSharp não é suportado ou necessário para testes de integração de aplicativos ASP.NET Core. Outros analisadores podem ser usados, como o [Html Agility Pack (HAP)](https://html-agility-pack.net/). Outra abordagem é escrever código para lidar diretamente com o token de verificação de solicitação do sistema antiforgery e o cookie antiforgery.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personalize o cliente com o WithWebHostBuilder

Quando a configuração adicional é necessária dentro de `WebApplicationFactory` um método de teste, [o ComWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) cria um novo com um [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) que é ainda mais personalizado pela configuração.

O `Post_DeleteMessageHandler_ReturnsRedirectToRoot` método de teste do aplicativo `WithWebHostBuilder`de [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstra o uso de . Este teste realiza uma exclusão de registro no banco de dados, acionando um envio de formulário no SUT.

Como outro teste `IndexPageTests` na classe realiza uma operação que exclui todos os `Post_DeleteMessageHandler_ReturnsRedirectToRoot` registros no banco de dados e pode ser executado antes do método, o banco de dados é ressedío neste método de teste para garantir que um registro esteja presente para o SUT ser excluído. A seleção do `messages` primeiro botão de exclusão do formulário no SUT é simulada na solicitação ao SUT:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Opções de clientes

A tabela a seguir mostra as opções `HttpClient` padrão [de WebApplicationFactoryClientDisponíveis](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) disponíveis ao criar instâncias.

| Opção | Descrição | Padrão |
| ------ | ----------- | ------- |
| [Allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtém ou define `HttpClient` se as instâncias devem ou não seguir automaticamente respostas de redirecionamento. | `true` |
| [Baseaddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtém ou define `HttpClient` o endereço base das instâncias. | `http://localhost` |
| [Manipulação de cookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtém ou `HttpClient` define se as instâncias devem lidar com cookies. | `true` |
| [MaxAutomaticRedirecionações](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtém ou define o número máximo `HttpClient` de respostas de redirecionamento que as instâncias devem seguir. | 7 |

Crie `WebApplicationFactoryClientOptions` a classe e passe-a para o método [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (valores padrão são mostrados no exemplo de código):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Injete serviços simulados

Os serviços podem ser substituídos em um teste com uma chamada para [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) no construtor de host. **Para injetar serviços simulados, `Startup` o SUT deve ter uma classe com um `Startup.ConfigureServices` método.**

O SUT de amostra inclui um serviço escopo que retorna uma cotação. A cotação é incorporada em um campo oculto na página Index quando a página Índice é solicitada.

*Serviços/IQuoteService.cs:*

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Serviços/QuoteService.cs:*

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs:*

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Páginas/Índice.cs:*

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

A marcação a seguir é gerada quando o aplicativo SUT é executado:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Para testar o serviço e citar a injeção em um teste de integração, um serviço simulado é injetado no SUT pelo teste. O serviço simulado substitui `QuoteService` o aplicativo por um serviço fornecido `TestQuoteService`pelo aplicativo de teste, chamado :

*IntegrationTests.IndexPageTests.cs:*

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`é chamado, e o serviço escopo é registrado:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

A marcação produzida durante a execução do `TestQuoteService`teste reflete o texto de citação fornecido por , assim, a afirmação passa:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Autenticação simulada

Os testes `AuthTests` na classe verificam se um ponto final seguro:

* Redireciona um usuário não autenticado para a página de login do aplicativo.
* Retorna o conteúdo para um usuário autenticado.

No SUT, `/SecurePage` a página usa uma convenção [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) para aplicar um [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à página. Para obter mais informações, consulte [as convenções de autorização de Razor Pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

No `Get_SecurePageRedirectsAnUnauthenticatedUser` teste, um [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) é definido para proibir redirecionamentos `false`definindo [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) para :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Ao não permitir que o cliente siga o redirecionamento, as seguintes verificações podem ser feitas:

* O código de status retornado pelo SUT pode ser verificado em relação ao resultado esperado [httpstatusCode.redirecionar,](/dotnet/api/system.net.httpstatuscode) não o código de status final após o redirecionamento para a página de login, que seria [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* O `Location` valor do cabeçalho nos cabeçalhos `http://localhost/Identity/Account/Login`de resposta é verificado para `Location` confirmar que ele começa com , não a resposta final da página de login, onde o cabeçalho não estaria presente.

O aplicativo de <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> teste pode simular um in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) para testar aspectos de autenticação e autorização. Um cenário mínimo retorna um [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

O `TestAuthHandler` é chamado para autenticar um usuário quando `Test` o `AddAuthentication` esquema de `ConfigureTestServices`autenticação é definido para onde está registrado para :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Para obter `WebApplicationFactoryClientOptions`mais informações, consulte a seção [Opções cliente.](#client-options)

## <a name="set-the-environment"></a>Definir o ambiente

Por padrão, o ambiente host e aplicativo do SUT é configurado para usar o ambiente Desenvolvimento. Para substituir o ambiente do SUT:

* Defina `ASPNETCORE_ENVIRONMENT` a variável de `Staging` `Production`ambiente (por exemplo, `Testing`ou outro valor personalizado, como ).
* Substituir `CreateHostBuilder` no aplicativo de teste para ler `ASPNETCORE`variáveis de ambiente prefixadas com .

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Como a infra-estrutura de teste infere o caminho raiz do conteúdo do aplicativo

O `WebApplicationFactory` construtor infere o caminho raiz do [conteúdo](xref:fundamentals/index#content-root) do aplicativo procurando por um [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) `TEntryPoint` no `System.Reflection.Assembly.FullName`conjunto contendo os testes de integração com uma chave igual ao conjunto . Caso um atributo com a chave `WebApplicationFactory` correta não seja encontrado, recue à busca de `TEntryPoint` um arquivo de solução (*.sln)* e anexa o nome do conjunto ao diretório de soluções. O diretório raiz do aplicativo (o caminho raiz do conteúdo) é usado para descobrir visualizações e arquivos de conteúdo.

## <a name="disable-shadow-copying"></a>Desativar a cópia da sombra

A cópia de sombra faz com que os testes sejam executados em um diretório diferente do diretório de saída. Para que os testes funcionem corretamente, a cópia da sombra deve ser desativada. O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) usa xUnit e desativa a cópia de sombra para xUnit, incluindo um arquivo *xunit.runner.json* com a configuração correta. Para obter mais informações, consulte [Configuração xUnit com JSON](https://xunit.github.io/docs/configuring-with-json.html).

Adicione o arquivo *xunit.runner.json* à raiz do projeto de teste com o seguinte conteúdo:

```json
{
  "shadowCopy": false
}
```

Se estiver usando o Visual Studio, defina a propriedade Copiar de **saída para** copiar **sempre**. Se não estiver usando `Content` o Visual Studio, adicione um destino ao arquivo de projeto do aplicativo de teste:

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Descarte de objetos

Depois que os `IClassFixture` testes da implementação são executados, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) e [HttpClient](/dotnet/api/system.net.http.httpclient) são eliminados quando xUnit elimina o [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Se os objetos instanciados pelo desenvolvedor exigirem eliminação, elimine-os na `IClassFixture` implementação. Para obter mais informações, consulte [Implementando um método Desfazer](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Amostra de testes de integração

O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) é composto por dois aplicativos:

| Aplicativo | Diretório do projeto | Descrição |
| --- | ----------------- | ----------- |
| Aplicativo de mensagem (o SUT) | *src/RazorPagesProject* | Permite que um usuário adicione, exclua um, exclua todas e analise mensagens. |
| Aplicativo de teste | *testes/RazorPagesProject.Tests* | Usado para testar a integração do SUT. |

Os testes podem ser executados usando os recursos de teste incorporados de um IDE, como [o Visual Studio](https://visualstudio.microsoft.com). Se estiver usando [o Visual Studio Code](https://code.visualstudio.com/) ou a linha de comando, execute o seguinte comando em um prompt de comando no diretório *testes/RazorPagesProject.Tests:*

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>Organização do aplicativo de mensagens (SUT)

O SUT é um sistema de mensagens Razor Pages com as seguintes características:

* A página Índice do aplicativo (*Páginas/Index.cshtml* e *Páginas/Index.cshtml.cs*) fornece métodos de modelo de ui e página para controlar a adição, exclusão e análise de mensagens (palavras médias por mensagem).
* Uma mensagem é `Message` descrita pela classe *(Data/Message.cs*) com duas propriedades: `Id` (chave) e `Text` (mensagem). A `Text` propriedade é necessária e limitada a 200 caracteres.
* As mensagens são armazenadas usando [o banco de dados de memória](/ef/core/providers/in-memory/) do Entity Framework&#8224;.
* O aplicativo contém uma camada de acesso a `AppDbContext` dados (DAL) em sua classe de contexto de banco de dados , (*Data/AppDbContext.cs*).
* Se o banco de dados estiver vazio na inicialização do aplicativo, a armazenamento de mensagens será inicializada com três mensagens.
* O aplicativo inclui `/SecurePage` um que só pode ser acessado por um usuário autenticado.

&#8224;O tópico EF, [Teste com a Memória,](/ef/core/miscellaneous/testing/in-memory)explica como usar um banco de dados na memória para testes com o MSTest. Este tópico usa a estrutura de teste [xUnit.](https://xunit.github.io/) Os conceitos de teste e implementações de teste em diferentes estruturas de teste são semelhantes, mas não idênticos.

Embora o aplicativo não use o padrão do repositório e não seja um exemplo eficaz do [padrão Unit of Work (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)razor pages suporta esses padrões de desenvolvimento. Para obter mais informações, consulte [Projetar a camada de persistência da infra-estrutura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e a lógica do controlador de [teste](/aspnet/core/mvc/controllers/testing) (a amostra implementa o padrão de repositório).

### <a name="test-app-organization"></a>Organização do aplicativo de teste

O aplicativo de teste é um aplicativo de console dentro do diretório *testes/RazorPagesProject.Tests.*

| Diretório do aplicativo de teste | Descrição |
| ------------------ | ----------- |
| *Testes auth* | Contém métodos de teste para:<ul><li>Acessando uma página segura por um usuário não autenticado.</li><li>Acessando uma página segura por um <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>usuário autenticado com uma simulação .</li><li>Obtendo um perfil de usuário do GitHub e verificando o login do usuário do perfil.</li></ul> |
| *Testes Básicos* | Contém um método de teste para roteamento e tipo de conteúdo. |
| *IntegrationTests* | Contém os testes de integração `WebApplicationFactory` para a página Índice usando classe personalizada. |
| *Ajudantes/Utilitários* | <ul><li>*Utilities.cs* contém `InitializeDbForTests` o método usado para semear o banco de dados com dados de teste.</li><li>*HtmlHelpers.cs* fornece um método para `IHtmlDocument` retornar um AngleSharp para uso pelos métodos de teste.</li><li>*HttpClientExtensions.cs* fornecer sobrecargas para `SendAsync` enviar solicitações ao SUT.</li></ul> |

A estrutura de teste é [xUnit](https://xunit.github.io/). Os testes de integração são realizados usando o [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), que inclui o [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Como o pacote [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) é usado para configurar o `TestHost` `TestServer` host de teste e o servidor de teste, os pacotes e os pacotes não exigem referências diretas de pacotes na configuração de arquivo de projeto ou desenvolvedor do aplicativo de teste no aplicativo de teste.

**Semeando o banco de dados para testes**

Os testes de integração geralmente requerem um pequeno conjunto de dados no banco de dados antes da execução do teste. Por exemplo, um teste de exclusão exige uma exclusão de registro de banco de dados, de modo que o banco de dados deve ter pelo menos um registro para que a solicitação de exclusão seja bem sucedida.

O aplicativo de amostra semea o banco de dados com três mensagens em *Utilities.cs* que os testes podem usar quando executam:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Testes de unidade](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>

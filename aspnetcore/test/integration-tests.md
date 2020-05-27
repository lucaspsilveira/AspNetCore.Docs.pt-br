---
<span data-ttu-id="d2f03-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-102">'Blazor'</span></span>
- <span data-ttu-id="d2f03-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-103">'Identity'</span></span>
- <span data-ttu-id="d2f03-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-105">'Razor'</span></span>
- <span data-ttu-id="d2f03-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-106">'SignalR' uid:</span></span> 

---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="d2f03-107">Testes de integração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2f03-107">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="d2f03-108">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/)e [Jos van der aguarde](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="d2f03-108">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d2f03-109">Os testes de integração garantem que os componentes de um aplicativo funcionem corretamente em um nível que inclui a infraestrutura de suporte do aplicativo, como o banco de dados, o sistema de arquivos e a rede.</span><span class="sxs-lookup"><span data-stu-id="d2f03-109">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="d2f03-110">O ASP.NET Core dá suporte a testes de integração usando uma estrutura de teste de unidade com um host da Web de teste e um servidor de teste na memória.</span><span class="sxs-lookup"><span data-stu-id="d2f03-110">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="d2f03-111">Este tópico pressupõe uma compreensão básica dos testes de unidade.</span><span class="sxs-lookup"><span data-stu-id="d2f03-111">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="d2f03-112">Se não estiver familiarizado com os conceitos de teste, consulte os [testes de unidade no .NET Core e .net Standard](/dotnet/core/testing/) tópico e seu conteúdo vinculado.</span><span class="sxs-lookup"><span data-stu-id="d2f03-112">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="d2f03-113">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d2f03-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d2f03-114">O aplicativo de exemplo é um Razor aplicativo de páginas e assume uma compreensão básica das Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="d2f03-114">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="d2f03-115">Se não estiver familiarizado com Razor páginas, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="d2f03-115">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="d2f03-116">[Introdução às Razor páginas](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="d2f03-116">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="d2f03-117">[Introdução às Razor páginas](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="d2f03-117">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="d2f03-118">[RazorTestes de unidade de páginas](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="d2f03-118">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="d2f03-119">Para testar o SPAs, recomendamos uma ferramenta como o [Selenium](https://www.seleniumhq.org/), que pode automatizar um navegador.</span><span class="sxs-lookup"><span data-stu-id="d2f03-119">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="d2f03-120">Introdução aos testes de integração</span><span class="sxs-lookup"><span data-stu-id="d2f03-120">Introduction to integration tests</span></span>

<span data-ttu-id="d2f03-121">Os testes de integração avaliam os componentes de um aplicativo em um nível mais amplo do que os [testes de unidade](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="d2f03-121">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="d2f03-122">Os testes de unidade são usados para testar componentes de software isolados, como métodos de classe individuais.</span><span class="sxs-lookup"><span data-stu-id="d2f03-122">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="d2f03-123">Testes de integração confirmam que dois ou mais componentes de aplicativo trabalham juntos para produzir um resultado esperado, possivelmente incluindo todos os componentes necessários para processar totalmente uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="d2f03-123">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="d2f03-124">Esses testes mais amplos são usados para testar a infraestrutura e a estrutura inteira do aplicativo, geralmente incluindo os seguintes componentes:</span><span class="sxs-lookup"><span data-stu-id="d2f03-124">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="d2f03-125">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="d2f03-125">Database</span></span>
* <span data-ttu-id="d2f03-126">Sistema de arquivos</span><span class="sxs-lookup"><span data-stu-id="d2f03-126">File system</span></span>
* <span data-ttu-id="d2f03-127">Dispositivos de rede</span><span class="sxs-lookup"><span data-stu-id="d2f03-127">Network appliances</span></span>
* <span data-ttu-id="d2f03-128">Pipeline de solicitação-resposta</span><span class="sxs-lookup"><span data-stu-id="d2f03-128">Request-response pipeline</span></span>

<span data-ttu-id="d2f03-129">Os testes de unidade usam componentes criei, conhecidos como *falsificações* ou *objetos fictícios*, em vez de componentes de infraestrutura.</span><span class="sxs-lookup"><span data-stu-id="d2f03-129">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="d2f03-130">Ao contrário dos testes de unidade, testes de integração:</span><span class="sxs-lookup"><span data-stu-id="d2f03-130">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="d2f03-131">Use os componentes reais que o aplicativo usa na produção.</span><span class="sxs-lookup"><span data-stu-id="d2f03-131">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="d2f03-132">Exigir mais processamento de código e de dados.</span><span class="sxs-lookup"><span data-stu-id="d2f03-132">Require more code and data processing.</span></span>
* <span data-ttu-id="d2f03-133">Demore mais para executar.</span><span class="sxs-lookup"><span data-stu-id="d2f03-133">Take longer to run.</span></span>

<span data-ttu-id="d2f03-134">Portanto, limite o uso de testes de integração para os cenários de infraestrutura mais importantes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-134">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="d2f03-135">Se um comportamento puder ser testado usando um teste de unidade ou um teste de integração, escolha o teste de unidade.</span><span class="sxs-lookup"><span data-stu-id="d2f03-135">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="d2f03-136">Não escreva testes de integração para todas as permutas possíveis de dados e acesso a arquivos com bancos e sistemas de arquivos.</span><span class="sxs-lookup"><span data-stu-id="d2f03-136">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="d2f03-137">Independentemente de quantos lugares em um aplicativo interagem com bancos de dados e sistemas de arquivos, um conjunto focado de testes de integração de leitura, gravação, atualização e exclusão geralmente é capaz de testar adequadamente os componentes do banco de dados e do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="d2f03-137">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="d2f03-138">Use testes de unidade para testes de rotina da lógica de método que interagem com esses componentes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-138">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="d2f03-139">Em testes de unidade, o uso de falsificações/simulações de infraestrutura resulta em uma execução de teste mais rápida.</span><span class="sxs-lookup"><span data-stu-id="d2f03-139">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="d2f03-140">Em discussões sobre testes de integração, o projeto testado é frequentemente chamado de *sistema em teste*, ou "SUT" para curto.</span><span class="sxs-lookup"><span data-stu-id="d2f03-140">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="d2f03-141">*"SUT" é usado em todo este tópico para se referir ao aplicativo ASP.NET Core testado.*</span><span class="sxs-lookup"><span data-stu-id="d2f03-141">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="d2f03-142">Testes de integração do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2f03-142">ASP.NET Core integration tests</span></span>

<span data-ttu-id="d2f03-143">Os testes de integração no ASP.NET Core exigem o seguinte:</span><span class="sxs-lookup"><span data-stu-id="d2f03-143">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="d2f03-144">Um projeto de teste é usado para conter e executar os testes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-144">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="d2f03-145">O projeto de teste tem uma referência para o SUT.</span><span class="sxs-lookup"><span data-stu-id="d2f03-145">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="d2f03-146">O projeto de teste cria um host Web de teste para o SUT e usa um cliente de servidor de teste para lidar com as solicitações e respostas com o SUT.</span><span class="sxs-lookup"><span data-stu-id="d2f03-146">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="d2f03-147">Um executor de teste é usado para executar os testes e relatar os resultados do teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-147">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="d2f03-148">Os testes de integração seguem uma sequência de eventos que incluem as etapas de teste de *organizar*, *Act*e *Assert* usuais:</span><span class="sxs-lookup"><span data-stu-id="d2f03-148">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="d2f03-149">O host da Web do SUT está configurado.</span><span class="sxs-lookup"><span data-stu-id="d2f03-149">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="d2f03-150">Um cliente de servidor de teste é criado para enviar solicitações ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-150">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="d2f03-151">A etapa *organizar* teste é executada: o aplicativo de teste prepara uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="d2f03-151">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="d2f03-152">A etapa de teste *Act* é executada: o cliente envia a solicitação e recebe a resposta.</span><span class="sxs-lookup"><span data-stu-id="d2f03-152">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="d2f03-153">A etapa de teste *Assert* é executada: a resposta *real* é validada como uma *passagem* ou *falha* com base em uma resposta *esperada* .</span><span class="sxs-lookup"><span data-stu-id="d2f03-153">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="d2f03-154">O processo continua até que todos os testes sejam executados.</span><span class="sxs-lookup"><span data-stu-id="d2f03-154">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="d2f03-155">Os resultados de teste são relatados.</span><span class="sxs-lookup"><span data-stu-id="d2f03-155">The test results are reported.</span></span>

<span data-ttu-id="d2f03-156">Normalmente, o host da Web de teste é configurado de modo diferente do host da Web normal do aplicativo para as execuções de teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-156">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="d2f03-157">Por exemplo, um banco de dados diferente ou configurações de aplicativo diferentes podem ser usadas para os testes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-157">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="d2f03-158">Os componentes de infraestrutura, como o host da Web de teste e o[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)(servidor de teste na memória), são fornecidos ou gerenciados pelo pacote [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="d2f03-158">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="d2f03-159">O uso desse pacote simplifica a criação e a execução de testes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-159">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="d2f03-160">O `Microsoft.AspNetCore.Mvc.Testing` pacote lida com as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="d2f03-160">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="d2f03-161">Copia o arquivo de dependências (*. deps*) do SUT para o diretório *bin* do projeto de teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-161">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="d2f03-162">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) como a raiz do projeto do SUT para que arquivos estáticos e páginas/exibições sejam encontrados quando os testes forem executados.</span><span class="sxs-lookup"><span data-stu-id="d2f03-162">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="d2f03-163">Fornece a classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) para simplificar a inicialização do SUT com `TestServer` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-163">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="d2f03-164">A documentação de [testes de unidade](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) descreve como configurar um projeto de teste e o Test Runner, juntamente com instruções detalhadas sobre como executar testes e recomendações sobre como nomear testes e classes de teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-164">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="d2f03-165">Ao criar um projeto de teste para um aplicativo, separe os testes de unidade dos testes de integração em projetos diferentes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-165">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="d2f03-166">Isso ajuda a garantir que os componentes de teste de infraestrutura não sejam incluídos acidentalmente nos testes de unidade.</span><span class="sxs-lookup"><span data-stu-id="d2f03-166">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="d2f03-167">A separação de testes de unidade e de integração também permite o controle sobre qual conjunto de testes são executados.</span><span class="sxs-lookup"><span data-stu-id="d2f03-167">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="d2f03-168">Praticamente não há nenhuma diferença entre a configuração para testes de Razor aplicativos de páginas e aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="d2f03-168">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="d2f03-169">A única diferença é em como os testes são nomeados.</span><span class="sxs-lookup"><span data-stu-id="d2f03-169">The only difference is in how the tests are named.</span></span> <span data-ttu-id="d2f03-170">Em um Razor aplicativo de páginas, os testes de pontos de extremidade de página geralmente são nomeados após a classe de modelo de página (por exemplo, `IndexPageTests` para testar a integração de componentes para a página de índice).</span><span class="sxs-lookup"><span data-stu-id="d2f03-170">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="d2f03-171">Em um aplicativo MVC, os testes geralmente são organizados por classes de controlador e nomeados após os controladores que testam (por exemplo, `HomeControllerTests` para testar a integração de componentes para o controlador doméstico).</span><span class="sxs-lookup"><span data-stu-id="d2f03-171">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="d2f03-172">Pré-requisitos do aplicativo de teste</span><span class="sxs-lookup"><span data-stu-id="d2f03-172">Test app prerequisites</span></span>

<span data-ttu-id="d2f03-173">O projeto de teste deve:</span><span class="sxs-lookup"><span data-stu-id="d2f03-173">The test project must:</span></span>

* <span data-ttu-id="d2f03-174">Referencie o pacote [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="d2f03-174">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="d2f03-175">Especifique o SDK da Web no arquivo de projeto ( `<Project Sdk="Microsoft.NET.Sdk.Web">` ).</span><span class="sxs-lookup"><span data-stu-id="d2f03-175">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="d2f03-176">Esses pré-requisitos podem ser vistos no aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="d2f03-176">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="d2f03-177">Inspecione o arquivo *tests/RazorPagesProject. Tests/RazorPagesProject. Tests. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d2f03-177">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="d2f03-178">O aplicativo de exemplo usa a estrutura de teste do [xUnit](https://xunit.github.io/) e a biblioteca do analisador [AngleSharp](https://anglesharp.github.io/) , portanto, o aplicativo de exemplo também faz referência a:</span><span class="sxs-lookup"><span data-stu-id="d2f03-178">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="d2f03-179">xUnit</span><span class="sxs-lookup"><span data-stu-id="d2f03-179">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="d2f03-180">xUnit. Runner. VisualStudio</span><span class="sxs-lookup"><span data-stu-id="d2f03-180">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="d2f03-181">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="d2f03-181">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="d2f03-182">Entity Framework Core também é usado nos testes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-182">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="d2f03-183">O aplicativo faz referência a:</span><span class="sxs-lookup"><span data-stu-id="d2f03-183">The app references:</span></span>

* [<span data-ttu-id="d2f03-184">Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="d2f03-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="d2f03-185">[Microsoft. AspNetCore. Identity . EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="d2f03-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="d2f03-186">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="d2f03-186">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="d2f03-187">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="d2f03-187">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="d2f03-188">Microsoft. EntityFrameworkCore. Tools</span><span class="sxs-lookup"><span data-stu-id="d2f03-188">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="d2f03-189">Ambiente SUT</span><span class="sxs-lookup"><span data-stu-id="d2f03-189">SUT environment</span></span>

<span data-ttu-id="d2f03-190">Se o [ambiente](xref:fundamentals/environments) do SUT não estiver definido, o ambiente assume o padrão de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d2f03-190">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="d2f03-191">Testes básicos com o padrão WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="d2f03-191">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="d2f03-192">[WebApplicationFactory \< TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) é usado para criar um [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) para os testes de integração.</span><span class="sxs-lookup"><span data-stu-id="d2f03-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="d2f03-193">`TEntryPoint`é a classe de ponto de entrada do SUT, geralmente a `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="d2f03-193">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="d2f03-194">Classes de teste implementam uma interface de *acessório de classe* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) para indicar que a classe contém testes e fornece instâncias de objeto compartilhado entre os testes na classe.</span><span class="sxs-lookup"><span data-stu-id="d2f03-194">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="d2f03-195">A classe de teste a seguir, `BasicTests` , usa o `WebApplicationFactory` para inicializar o SUT e fornecer um [HttpClient](/dotnet/api/system.net.http.httpclient) para um método de teste, `Get_EndpointsReturnSuccessAndCorrectContentType` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-195">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="d2f03-196">O método verifica se o código de status de resposta é bem-sucedido (códigos de status no intervalo de 200-299) e o `Content-Type` cabeçalho é `text/html; charset=utf-8` para várias páginas de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-196">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="d2f03-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) cria uma instância do `HttpClient` que segue automaticamente os redirecionamentos e manipula cookies.</span><span class="sxs-lookup"><span data-stu-id="d2f03-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="d2f03-198">Por padrão, os cookies não essenciais não são preservados entre solicitações quando a [política de consentimento de GDPR](xref:security/gdpr) está habilitada.</span><span class="sxs-lookup"><span data-stu-id="d2f03-198">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="d2f03-199">Para preservar cookies não essenciais, como os usados pelo provedor TempData, marque-os como essenciais em seus testes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-199">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="d2f03-200">Para obter instruções sobre como marcar um cookie como essencial, consulte [cookies essenciais](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="d2f03-200">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="d2f03-201">Personalizar o WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="d2f03-201">Customize WebApplicationFactory</span></span>

<span data-ttu-id="d2f03-202">A configuração do host Web pode ser criada independentemente das classes de teste herdando de `WebApplicationFactory` para criar uma ou mais fábricas personalizadas:</span><span class="sxs-lookup"><span data-stu-id="d2f03-202">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="d2f03-203">Herdar de `WebApplicationFactory` e substituir [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="d2f03-203">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="d2f03-204">O [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permite a configuração da coleção de serviços com [configuraservices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span><span class="sxs-lookup"><span data-stu-id="d2f03-204">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="d2f03-205">A propagação de banco de dados no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) é executada pelo `InitializeDbForTests` método.</span><span class="sxs-lookup"><span data-stu-id="d2f03-205">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="d2f03-206">O método é descrito na seção [testes de integração exemplo: testar organização de aplicativos](#test-app-organization) .</span><span class="sxs-lookup"><span data-stu-id="d2f03-206">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="d2f03-207">O contexto do banco de dados do SUT é registrado em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="d2f03-207">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d2f03-208">O retorno de chamada do aplicativo de teste `builder.ConfigureServices` é executado *depois* que o código do aplicativo `Startup.ConfigureServices` é executado.</span><span class="sxs-lookup"><span data-stu-id="d2f03-208">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="d2f03-209">A ordem de execução é uma alteração significativa para o [host genérico](xref:fundamentals/host/generic-host) com o lançamento do ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="d2f03-209">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="d2f03-210">Para usar um banco de dados diferente para os testes do que o banco de dados do aplicativo, o contexto do banco de dados do aplicativo deve ser substituído em `builder.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-210">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="d2f03-211">Para SUTs que ainda usam o [host da Web](xref:fundamentals/host/web-host), o retorno de chamada do aplicativo de teste `builder.ConfigureServices` é executado *antes* do código do SUT `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-211">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="d2f03-212">O retorno de chamada do aplicativo de teste `builder.ConfigureTestServices` é executado *após*.</span><span class="sxs-lookup"><span data-stu-id="d2f03-212">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="d2f03-213">O aplicativo de exemplo localiza o descritor de serviço para o contexto do banco de dados e usa o descritor para remover o registro do serviço.</span><span class="sxs-lookup"><span data-stu-id="d2f03-213">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="d2f03-214">Em seguida, a fábrica adiciona um novo `ApplicationDbContext` que usa um banco de dados na memória para os testes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-214">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="d2f03-215">Para se conectar a um banco de dados diferente do banco de dados na memória, altere a `UseInMemoryDatabase` chamada para conectar o contexto a um banco de dados diferente.</span><span class="sxs-lookup"><span data-stu-id="d2f03-215">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="d2f03-216">Para usar um banco de dados de teste SQL Server:</span><span class="sxs-lookup"><span data-stu-id="d2f03-216">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="d2f03-217">Referencie o pacote NuGet [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="d2f03-217">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="d2f03-218">Chame `UseSqlServer` com uma cadeia de conexão para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d2f03-218">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="d2f03-219">Use as `CustomWebApplicationFactory` classes personalizadas em teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-219">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="d2f03-220">O exemplo a seguir usa a fábrica na `IndexPageTests` classe:</span><span class="sxs-lookup"><span data-stu-id="d2f03-220">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="d2f03-221">O cliente do aplicativo de exemplo está configurado para impedir o `HttpClient` dos redirecionamentos a seguir.</span><span class="sxs-lookup"><span data-stu-id="d2f03-221">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="d2f03-222">Conforme explicado mais adiante na seção de [autenticação fictícia](#mock-authentication) , isso permite que os testes verifiquem o resultado da primeira resposta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-222">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="d2f03-223">A primeira resposta é um redirecionamento em muitos desses testes com um `Location` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="d2f03-223">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="d2f03-224">Um teste típico usa os `HttpClient` métodos auxiliares e para processar a solicitação e a resposta:</span><span class="sxs-lookup"><span data-stu-id="d2f03-224">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="d2f03-225">Qualquer solicitação POST para o SUT deve satisfazer a verificação antifalsificação feita automaticamente pelo [sistema antifalsificação de proteção de dados](xref:security/data-protection/introduction)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-225">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="d2f03-226">Para organizar a solicitação POST de um teste, o aplicativo de teste deve:</span><span class="sxs-lookup"><span data-stu-id="d2f03-226">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="d2f03-227">Faça uma solicitação para a página.</span><span class="sxs-lookup"><span data-stu-id="d2f03-227">Make a request for the page.</span></span>
1. <span data-ttu-id="d2f03-228">Analise o cookie antifalsificação e solicite o token de validação da resposta.</span><span class="sxs-lookup"><span data-stu-id="d2f03-228">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="d2f03-229">Faça a solicitação POST com o cookie antifalsificação e solicite o token de validação em vigor.</span><span class="sxs-lookup"><span data-stu-id="d2f03-229">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="d2f03-230">Os `SendAsync` métodos de extensão auxiliares (*Helpers/HttpClientExtensions. cs*) e o `GetDocumentAsync` método auxiliar (*Helpers/HtmlHelpers. cs*) no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) usam o analisador [AngleSharp](https://anglesharp.github.io/) para lidar com a verificação antifalsificação com os seguintes métodos:</span><span class="sxs-lookup"><span data-stu-id="d2f03-230">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="d2f03-231">`GetDocumentAsync`&ndash;Recebe o [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) e retorna um `IHtmlDocument` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-231">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="d2f03-232">`GetDocumentAsync`usa uma fábrica que prepara uma *resposta virtual* com base no original `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-232">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="d2f03-233">Para obter mais informações, consulte a [documentação do AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="d2f03-233">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="d2f03-234">`SendAsync`métodos de extensão para `HttpClient` compor um [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) e chamar [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) para enviar solicitações para o SUT.</span><span class="sxs-lookup"><span data-stu-id="d2f03-234">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="d2f03-235">Sobrecargas para `SendAsync` aceitar o formulário HTML ( `IHtmlFormElement` ) e o seguinte:</span><span class="sxs-lookup"><span data-stu-id="d2f03-235">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="d2f03-236">Botão enviar do formulário ( `IHtmlElement` )</span><span class="sxs-lookup"><span data-stu-id="d2f03-236">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="d2f03-237">Coleção de valores de formulário ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="d2f03-237">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="d2f03-238">Enviar botão ( `IHtmlElement` ) e valores de formulário ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="d2f03-238">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="d2f03-239">[AngleSharp](https://anglesharp.github.io/) é uma biblioteca de análise de terceiros usada para fins de demonstração neste tópico e no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-239">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="d2f03-240">AngleSharp não tem suporte ou é necessário para o teste de integração de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2f03-240">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="d2f03-241">Outros analisadores podem ser usados, como o [Hap (HTML agilidade Pack)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="d2f03-241">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="d2f03-242">Outra abordagem é escrever código para lidar diretamente com o token de verificação de solicitação do sistema antifalsificação e o cookie antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="d2f03-242">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="d2f03-243">Personalizar o cliente com o WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="d2f03-243">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="d2f03-244">Quando uma configuração adicional é necessária dentro de um método de teste, o [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) cria um novo `WebApplicationFactory` com um [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) que é ainda mais personalizado pela configuração.</span><span class="sxs-lookup"><span data-stu-id="d2f03-244">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="d2f03-245">O `Post_DeleteMessageHandler_ReturnsRedirectToRoot` método de teste do [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstra o uso de `WithWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-245">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="d2f03-246">Esse teste executa uma exclusão de registro no banco de dados disparando um envio de formulário no SUT.</span><span class="sxs-lookup"><span data-stu-id="d2f03-246">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="d2f03-247">Como outro teste na `IndexPageTests` classe executa uma operação que exclui todos os registros no banco de dados e pode ser executado antes do `Post_DeleteMessageHandler_ReturnsRedirectToRoot` método, o banco de dados é propagado nesse método de teste para garantir que um registro esteja presente para que o SUT seja excluído.</span><span class="sxs-lookup"><span data-stu-id="d2f03-247">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="d2f03-248">Selecionar o primeiro botão excluir do `messages` formulário no SUT é simulado na solicitação para o SUT:</span><span class="sxs-lookup"><span data-stu-id="d2f03-248">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="d2f03-249">Opções de cliente</span><span class="sxs-lookup"><span data-stu-id="d2f03-249">Client options</span></span>

<span data-ttu-id="d2f03-250">A tabela a seguir mostra o [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) padrão disponível ao criar `HttpClient` instâncias.</span><span class="sxs-lookup"><span data-stu-id="d2f03-250">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="d2f03-251">Opção</span><span class="sxs-lookup"><span data-stu-id="d2f03-251">Option</span></span> | <span data-ttu-id="d2f03-252">Descrição</span><span class="sxs-lookup"><span data-stu-id="d2f03-252">Description</span></span> | <span data-ttu-id="d2f03-253">Padrão</span><span class="sxs-lookup"><span data-stu-id="d2f03-253">Default</span></span> |
| ---
<span data-ttu-id="d2f03-254">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-255">'Blazor'</span></span>
- <span data-ttu-id="d2f03-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-256">'Identity'</span></span>
- <span data-ttu-id="d2f03-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-258">'Razor'</span></span>
- <span data-ttu-id="d2f03-259">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-259">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-260">--- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-261">'Blazor'</span></span>
- <span data-ttu-id="d2f03-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-262">'Identity'</span></span>
- <span data-ttu-id="d2f03-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-264">'Razor'</span></span>
- <span data-ttu-id="d2f03-265">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-266">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-267">'Blazor'</span></span>
- <span data-ttu-id="d2f03-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-268">'Identity'</span></span>
- <span data-ttu-id="d2f03-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-270">'Razor'</span></span>
- <span data-ttu-id="d2f03-271">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-272">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-273">'Blazor'</span></span>
- <span data-ttu-id="d2f03-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-274">'Identity'</span></span>
- <span data-ttu-id="d2f03-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-276">'Razor'</span></span>
- <span data-ttu-id="d2f03-277">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-277">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-278">------ | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-279">'Blazor'</span></span>
- <span data-ttu-id="d2f03-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-280">'Identity'</span></span>
- <span data-ttu-id="d2f03-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-282">'Razor'</span></span>
- <span data-ttu-id="d2f03-283">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-283">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtém ou define se as `HttpClient` instâncias devem seguir ou não automaticamente as respostas de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="d2f03-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="d2f03-285"> | `true`| | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtém ou define o endereço base das `HttpClient` instâncias.</span><span class="sxs-lookup"><span data-stu-id="d2f03-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="d2f03-286"> | `http://localhost`| | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtém ou define se as `HttpClient` instâncias devem lidar com cookies.</span><span class="sxs-lookup"><span data-stu-id="d2f03-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="d2f03-287"> | `true`| | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtém ou define o número máximo de respostas de redirecionamento que as `HttpClient` instâncias devem seguir.</span><span class="sxs-lookup"><span data-stu-id="d2f03-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="d2f03-288">| 7 |</span><span class="sxs-lookup"><span data-stu-id="d2f03-288">| 7 |</span></span>

<span data-ttu-id="d2f03-289">Crie a `WebApplicationFactoryClientOptions` classe e passe-a para o método [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (os valores padrão são mostrados no exemplo de código):</span><span class="sxs-lookup"><span data-stu-id="d2f03-289">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="d2f03-290">Injetar serviços fictícios</span><span class="sxs-lookup"><span data-stu-id="d2f03-290">Inject mock services</span></span>

<span data-ttu-id="d2f03-291">Os serviços podem ser substituídos em um teste com uma chamada para [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) no host Builder.</span><span class="sxs-lookup"><span data-stu-id="d2f03-291">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="d2f03-292">**Para injetar serviços fictícios, o SUT deve ter uma `Startup` classe com um `Startup.ConfigureServices` método.**</span><span class="sxs-lookup"><span data-stu-id="d2f03-292">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="d2f03-293">O SUT de exemplo inclui um serviço com escopo que retorna uma aspa.</span><span class="sxs-lookup"><span data-stu-id="d2f03-293">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="d2f03-294">A cotação é inserida em um campo oculto na página de índice quando a página de índice é solicitada.</span><span class="sxs-lookup"><span data-stu-id="d2f03-294">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="d2f03-295">*Serviços/IQuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="d2f03-295">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="d2f03-296">*Serviços/QuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="d2f03-296">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="d2f03-297">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2f03-297">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="d2f03-298">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2f03-298">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="d2f03-299">*Páginas/index. cs*:</span><span class="sxs-lookup"><span data-stu-id="d2f03-299">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="d2f03-300">A marcação a seguir é gerada quando o aplicativo SUT é executado:</span><span class="sxs-lookup"><span data-stu-id="d2f03-300">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="d2f03-301">Para testar o serviço e a injeção de aspas em um teste de integração, um serviço fictício é injetado no SUT pelo teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-301">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="d2f03-302">O serviço fictício substitui o aplicativo `QuoteService` por um serviço fornecido pelo aplicativo de teste, chamado `TestQuoteService` :</span><span class="sxs-lookup"><span data-stu-id="d2f03-302">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="d2f03-303">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2f03-303">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="d2f03-304">`ConfigureTestServices`é chamado e o serviço com escopo é registrado:</span><span class="sxs-lookup"><span data-stu-id="d2f03-304">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="d2f03-305">A marcação produzida durante a execução do teste reflete o texto da citação fornecido pelo `TestQuoteService` , assim, a asserção passa:</span><span class="sxs-lookup"><span data-stu-id="d2f03-305">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="d2f03-306">Autenticação fictícia</span><span class="sxs-lookup"><span data-stu-id="d2f03-306">Mock authentication</span></span>

<span data-ttu-id="d2f03-307">Testes na `AuthTests` classe verificam se um ponto de extremidade seguro:</span><span class="sxs-lookup"><span data-stu-id="d2f03-307">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="d2f03-308">Redireciona um usuário não autenticado para a página de logon do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-308">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="d2f03-309">Retorna o conteúdo de um usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="d2f03-309">Returns content for an authenticated user.</span></span>

<span data-ttu-id="d2f03-310">No SUT, a `/SecurePage` página usa uma convenção [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) para aplicar um [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à página.</span><span class="sxs-lookup"><span data-stu-id="d2f03-310">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="d2f03-311">Para obter mais informações, consulte [ Razor páginas convenções de autorização](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="d2f03-311">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="d2f03-312">No `Get_SecurePageRedirectsAnUnauthenticatedUser` teste, um [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) é definido para não permitir redirecionamentos definindo [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) como `false` :</span><span class="sxs-lookup"><span data-stu-id="d2f03-312">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="d2f03-313">Ao não permitir que o cliente siga o redirecionamento, as seguintes verificações podem ser feitas:</span><span class="sxs-lookup"><span data-stu-id="d2f03-313">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="d2f03-314">O código de status retornado pelo SUT pode ser verificado em relação ao resultado de [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) esperado, não ao código de status final após o redirecionamento para a página de logon, que seria [HttpStatusCode. ok](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="d2f03-314">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="d2f03-315">O `Location` valor do cabeçalho nos cabeçalhos de resposta é verificado para confirmar que ele começa com `http://localhost/Identity/Account/Login` , não a resposta da página final de logon, em que o cabeçalho não estaria `Location` presente.</span><span class="sxs-lookup"><span data-stu-id="d2f03-315">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="d2f03-316">O aplicativo de teste pode simular um <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> no [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) para testar aspectos de autenticação e autorização.</span><span class="sxs-lookup"><span data-stu-id="d2f03-316">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="d2f03-317">Um cenário mínimo retorna um [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="d2f03-317">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="d2f03-318">O `TestAuthHandler` é chamado para autenticar um usuário quando o esquema de autenticação é definido como `Test` onde o `AddAuthentication` está registrado para `ConfigureTestServices` :</span><span class="sxs-lookup"><span data-stu-id="d2f03-318">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="d2f03-319">Para obter mais informações sobre `WebApplicationFactoryClientOptions` o, consulte a seção [Opções de cliente](#client-options) .</span><span class="sxs-lookup"><span data-stu-id="d2f03-319">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="d2f03-320">Definir o ambiente</span><span class="sxs-lookup"><span data-stu-id="d2f03-320">Set the environment</span></span>

<span data-ttu-id="d2f03-321">Por padrão, o host e o ambiente de aplicativo do SUT são configurados para usar o ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d2f03-321">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="d2f03-322">Para substituir o ambiente do SUT ao usar `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d2f03-322">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="d2f03-323">Defina a `ASPNETCORE_ENVIRONMENT` variável de ambiente (por exemplo,, `Staging` `Production` ou outro valor personalizado, como `Testing` ).</span><span class="sxs-lookup"><span data-stu-id="d2f03-323">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="d2f03-324">Substitua `CreateHostBuilder` no aplicativo de teste para ler as variáveis de ambiente prefixadas com `ASPNETCORE` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-324">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="d2f03-325">Se o SUT usar o host da Web ( `IWebHostBuilder` ), substitua `CreateWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d2f03-325">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="d2f03-326">Como a infraestrutura de teste infere o caminho raiz do conteúdo do aplicativo</span><span class="sxs-lookup"><span data-stu-id="d2f03-326">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="d2f03-327">O `WebApplicationFactory` Construtor infere o caminho [raiz do conteúdo](xref:fundamentals/index#content-root) do aplicativo pesquisando um [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) no assembly que contém os testes de integração com uma chave igual ao `TEntryPoint` assembly `System.Reflection.Assembly.FullName` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-327">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="d2f03-328">Caso um atributo com a chave correta não seja encontrado, `WebApplicationFactory` volte à pesquisa de um arquivo de solução (*. sln*) e acrescente o `TEntryPoint` nome do assembly ao diretório da solução.</span><span class="sxs-lookup"><span data-stu-id="d2f03-328">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="d2f03-329">O diretório raiz do aplicativo (o caminho raiz do conteúdo) é usado para descobrir exibições e arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-329">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="d2f03-330">Desabilitar cópia de sombra</span><span class="sxs-lookup"><span data-stu-id="d2f03-330">Disable shadow copying</span></span>

<span data-ttu-id="d2f03-331">A cópia de sombra faz com que os testes sejam executados em um diretório diferente do diretório de saída.</span><span class="sxs-lookup"><span data-stu-id="d2f03-331">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="d2f03-332">Para que os testes funcionem corretamente, a cópia de sombra deve ser desabilitada.</span><span class="sxs-lookup"><span data-stu-id="d2f03-332">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="d2f03-333">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) usa xUnit e desabilita a cópia de sombra para xUnit, incluindo um arquivo *xUnit. Runner. JSON* com a definição de configuração correta.</span><span class="sxs-lookup"><span data-stu-id="d2f03-333">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="d2f03-334">Para obter mais informações, consulte [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="d2f03-334">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="d2f03-335">Adicione o arquivo *xUnit. Runner. JSON* à raiz do projeto de teste com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="d2f03-335">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="d2f03-336">Alienação de objetos</span><span class="sxs-lookup"><span data-stu-id="d2f03-336">Disposal of objects</span></span>

<span data-ttu-id="d2f03-337">Depois que os testes da `IClassFixture` implementação são executados, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) e [HttpClient](/dotnet/api/system.net.http.httpclient) são descartados quando xUnit descarta o [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="d2f03-337">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="d2f03-338">Se os objetos instanciados pelo desenvolvedor exigirem descarte, descarte-os na `IClassFixture` implementação.</span><span class="sxs-lookup"><span data-stu-id="d2f03-338">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="d2f03-339">Para obter mais informações, consulte [implementando um método Dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="d2f03-339">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="d2f03-340">Exemplo de testes de integração</span><span class="sxs-lookup"><span data-stu-id="d2f03-340">Integration tests sample</span></span>

<span data-ttu-id="d2f03-341">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) é composto por dois aplicativos:</span><span class="sxs-lookup"><span data-stu-id="d2f03-341">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="d2f03-342">Aplicativo</span><span class="sxs-lookup"><span data-stu-id="d2f03-342">App</span></span> | <span data-ttu-id="d2f03-343">Diretório do projeto</span><span class="sxs-lookup"><span data-stu-id="d2f03-343">Project directory</span></span> | <span data-ttu-id="d2f03-344">Descrição</span><span class="sxs-lookup"><span data-stu-id="d2f03-344">Description</span></span> |
| --- | ---
<span data-ttu-id="d2f03-345">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-346">'Blazor'</span></span>
- <span data-ttu-id="d2f03-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-347">'Identity'</span></span>
- <span data-ttu-id="d2f03-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-349">'Razor'</span></span>
- <span data-ttu-id="d2f03-350">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-351">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-352">'Blazor'</span></span>
- <span data-ttu-id="d2f03-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-353">'Identity'</span></span>
- <span data-ttu-id="d2f03-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-355">'Razor'</span></span>
- <span data-ttu-id="d2f03-356">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-357">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-358">'Blazor'</span></span>
- <span data-ttu-id="d2f03-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-359">'Identity'</span></span>
- <span data-ttu-id="d2f03-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-361">'Razor'</span></span>
- <span data-ttu-id="d2f03-362">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-363">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-364">'Blazor'</span></span>
- <span data-ttu-id="d2f03-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-365">'Identity'</span></span>
- <span data-ttu-id="d2f03-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-367">'Razor'</span></span>
- <span data-ttu-id="d2f03-368">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-369">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-370">'Blazor'</span></span>
- <span data-ttu-id="d2f03-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-371">'Identity'</span></span>
- <span data-ttu-id="d2f03-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-373">'Razor'</span></span>
- <span data-ttu-id="d2f03-374">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-375">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-376">'Blazor'</span></span>
- <span data-ttu-id="d2f03-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-377">'Identity'</span></span>
- <span data-ttu-id="d2f03-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-379">'Razor'</span></span>
- <span data-ttu-id="d2f03-380">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-380">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-381">--------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-382">'Blazor'</span></span>
- <span data-ttu-id="d2f03-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-383">'Identity'</span></span>
- <span data-ttu-id="d2f03-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-385">'Razor'</span></span>
- <span data-ttu-id="d2f03-386">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-387">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-388">'Blazor'</span></span>
- <span data-ttu-id="d2f03-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-389">'Identity'</span></span>
- <span data-ttu-id="d2f03-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-391">'Razor'</span></span>
- <span data-ttu-id="d2f03-392">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-393">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-394">'Blazor'</span></span>
- <span data-ttu-id="d2f03-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-395">'Identity'</span></span>
- <span data-ttu-id="d2f03-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-397">'Razor'</span></span>
- <span data-ttu-id="d2f03-398">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-398">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-399">------ | | Aplicativo de mensagens (o SUT) | *src/RazorPagesProject* | Permite que um usuário adicione, exclua um, exclua todos e analise as mensagens.</span><span class="sxs-lookup"><span data-stu-id="d2f03-399">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="d2f03-400">| | Aplicativo de teste | *tests/RazorPagesProject. Tests* | Usado para testar a integração do SUT.</span><span class="sxs-lookup"><span data-stu-id="d2f03-400">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="d2f03-401">Os testes podem ser executados usando os recursos de teste internos de um IDE, como o [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="d2f03-401">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="d2f03-402">Se estiver usando [Visual Studio Code](https://code.visualstudio.com/) ou a linha de comando, execute o seguinte comando em um prompt de comando no diretório *tests/RazorPagesProject. Tests* :</span><span class="sxs-lookup"><span data-stu-id="d2f03-402">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="d2f03-403">Organização do aplicativo de mensagem (SUT)</span><span class="sxs-lookup"><span data-stu-id="d2f03-403">Message app (SUT) organization</span></span>

<span data-ttu-id="d2f03-404">O SUT é um Razor sistema de mensagens de páginas com as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="d2f03-404">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="d2f03-405">A página de índice do aplicativo (*pages/index. cshtml* e *pages/index. cshtml. cs*) fornece uma interface do usuário e métodos de modelo de página para controlar a adição, a exclusão e a análise de mensagens (média de palavras por mensagem).</span><span class="sxs-lookup"><span data-stu-id="d2f03-405">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="d2f03-406">Uma mensagem é descrita pela `Message` classe (*Data/mensagem. cs*) com duas propriedades: `Id` (chave) e `Text` (mensagem).</span><span class="sxs-lookup"><span data-stu-id="d2f03-406">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="d2f03-407">A `Text` propriedade é necessária e limitada a 200 caracteres.</span><span class="sxs-lookup"><span data-stu-id="d2f03-407">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="d2f03-408">As mensagens são armazenadas usando&#8224; [de banco de dados na memória Entity Framework](/ef/core/providers/in-memory/) .</span><span class="sxs-lookup"><span data-stu-id="d2f03-408">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="d2f03-409">O aplicativo contém uma camada de acesso a dados (DAL) em sua classe de contexto de banco de dados, `AppDbContext` (*Data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="d2f03-409">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="d2f03-410">Se o banco de dados estiver vazio na inicialização do aplicativo, o repositório de mensagens será inicializado com três mensagens.</span><span class="sxs-lookup"><span data-stu-id="d2f03-410">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="d2f03-411">O aplicativo inclui um `/SecurePage` que só pode ser acessado por um usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="d2f03-411">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="d2f03-412">&#8224;tópico do EF, [teste com inmemory](/ef/core/miscellaneous/testing/in-memory), explica como usar um banco de dados na memória para testes com MSTest.</span><span class="sxs-lookup"><span data-stu-id="d2f03-412">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="d2f03-413">Este tópico usa a estrutura de teste do [xUnit](https://xunit.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="d2f03-413">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="d2f03-414">Conceitos de teste e implementações de teste em diferentes estruturas de teste são semelhantes, mas não idênticos.</span><span class="sxs-lookup"><span data-stu-id="d2f03-414">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="d2f03-415">Embora o aplicativo não use o padrão de repositório e não seja um exemplo eficaz do [padrão de UoW (unidade de trabalho)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor as páginas dão suporte a esses padrões de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d2f03-415">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="d2f03-416">Para obter mais informações, consulte [projetando a camada de persistência de infraestrutura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e a [lógica do controlador de teste](/aspnet/core/mvc/controllers/testing) (o exemplo implementa o padrão de repositório).</span><span class="sxs-lookup"><span data-stu-id="d2f03-416">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="d2f03-417">Organização de aplicativos de teste</span><span class="sxs-lookup"><span data-stu-id="d2f03-417">Test app organization</span></span>

<span data-ttu-id="d2f03-418">O aplicativo de teste é um aplicativo de console dentro do diretório *tests/RazorPagesProject. Tests* .</span><span class="sxs-lookup"><span data-stu-id="d2f03-418">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="d2f03-419">Testar diretório do aplicativo</span><span class="sxs-lookup"><span data-stu-id="d2f03-419">Test app directory</span></span> | <span data-ttu-id="d2f03-420">Descrição</span><span class="sxs-lookup"><span data-stu-id="d2f03-420">Description</span></span> |
| ---
<span data-ttu-id="d2f03-421">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-422">'Blazor'</span></span>
- <span data-ttu-id="d2f03-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-423">'Identity'</span></span>
- <span data-ttu-id="d2f03-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-425">'Razor'</span></span>
- <span data-ttu-id="d2f03-426">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-427">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-428">'Blazor'</span></span>
- <span data-ttu-id="d2f03-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-429">'Identity'</span></span>
- <span data-ttu-id="d2f03-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-431">'Razor'</span></span>
- <span data-ttu-id="d2f03-432">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-433">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-434">'Blazor'</span></span>
- <span data-ttu-id="d2f03-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-435">'Identity'</span></span>
- <span data-ttu-id="d2f03-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-437">'Razor'</span></span>
- <span data-ttu-id="d2f03-438">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-439">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-440">'Blazor'</span></span>
- <span data-ttu-id="d2f03-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-441">'Identity'</span></span>
- <span data-ttu-id="d2f03-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-443">'Razor'</span></span>
- <span data-ttu-id="d2f03-444">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-445">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-446">'Blazor'</span></span>
- <span data-ttu-id="d2f03-447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-447">'Identity'</span></span>
- <span data-ttu-id="d2f03-448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-448">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-449">'Razor'</span></span>
- <span data-ttu-id="d2f03-450">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-451">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-452">'Blazor'</span></span>
- <span data-ttu-id="d2f03-453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-453">'Identity'</span></span>
- <span data-ttu-id="d2f03-454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-454">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-455">'Razor'</span></span>
- <span data-ttu-id="d2f03-456">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-457">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-458">'Blazor'</span></span>
- <span data-ttu-id="d2f03-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-459">'Identity'</span></span>
- <span data-ttu-id="d2f03-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-461">'Razor'</span></span>
- <span data-ttu-id="d2f03-462">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-462">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-463">--------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-464">'Blazor'</span></span>
- <span data-ttu-id="d2f03-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-465">'Identity'</span></span>
- <span data-ttu-id="d2f03-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-467">'Razor'</span></span>
- <span data-ttu-id="d2f03-468">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-469">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-470">'Blazor'</span></span>
- <span data-ttu-id="d2f03-471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-471">'Identity'</span></span>
- <span data-ttu-id="d2f03-472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-472">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-473">'Razor'</span></span>
- <span data-ttu-id="d2f03-474">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-475">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-476">'Blazor'</span></span>
- <span data-ttu-id="d2f03-477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-477">'Identity'</span></span>
- <span data-ttu-id="d2f03-478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-478">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-479">'Razor'</span></span>
- <span data-ttu-id="d2f03-480">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-480">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-481">------ | | *AuthTests* | Contém métodos de teste para:</span><span class="sxs-lookup"><span data-stu-id="d2f03-481">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="d2f03-482">Acessando uma página segura por um usuário não autenticado.</span><span class="sxs-lookup"><span data-stu-id="d2f03-482">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="d2f03-483">Acessar uma página segura por um usuário autenticado com uma simulação <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="d2f03-483">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="d2f03-484">Obter um perfil de usuário do GitHub e verificar o logon do usuário do perfil.</span><span class="sxs-lookup"><span data-stu-id="d2f03-484">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="d2f03-485">| | *BasicTests* | Contém um método de teste para roteamento e tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-485">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="d2f03-486">| | *IntegrationTests* | Contém os testes de integração para a página de índice usando a `WebApplicationFactory` classe personalizada.</span><span class="sxs-lookup"><span data-stu-id="d2f03-486">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="d2f03-487">| | *Auxiliares/utilitários* | </span><span class="sxs-lookup"><span data-stu-id="d2f03-487">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="d2f03-488">*Utilities.cs* contém o `InitializeDbForTests` método usado para propagar o banco de dados com dado de teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-488">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="d2f03-489">*HtmlHelpers.cs* fornece um método para retornar um AngleSharp `IHtmlDocument` para uso pelos métodos de teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-489">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="d2f03-490">*HttpClientExtensions.cs* fornecem sobrecargas para `SendAsync` o enviar solicitações para o SUT.</span><span class="sxs-lookup"><span data-stu-id="d2f03-490">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="d2f03-491">A estrutura de teste é [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="d2f03-491">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="d2f03-492">Os testes de integração são realizados usando o [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), que inclui o [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="d2f03-492">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="d2f03-493">Como o pacote [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) é usado para configurar o host de teste e o servidor de teste, os `TestHost` `TestServer` pacotes e não exigem referências diretas de pacote no arquivo de projeto do aplicativo de teste ou na configuração do desenvolvedor no aplicativo de teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-493">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="d2f03-494">**Propagando o banco de dados para teste**</span><span class="sxs-lookup"><span data-stu-id="d2f03-494">**Seeding the database for testing**</span></span>

<span data-ttu-id="d2f03-495">Os testes de integração geralmente exigem um pequeno conjunto de dados no banco antes da execução do teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-495">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="d2f03-496">Por exemplo, um teste de exclusão chama uma exclusão de registro de banco de dados, de modo que o banco de dados deve ter pelo menos um registro para que a solicitação de exclusão tenha sucesso.</span><span class="sxs-lookup"><span data-stu-id="d2f03-496">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="d2f03-497">O aplicativo de exemplo propaga o banco de dados com três mensagens no *Utilities.cs* que os testes podem usar quando executam:</span><span class="sxs-lookup"><span data-stu-id="d2f03-497">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="d2f03-498">O contexto do banco de dados do SUT é registrado em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="d2f03-498">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d2f03-499">O retorno de chamada do aplicativo de teste `builder.ConfigureServices` é executado *depois* que o código do aplicativo `Startup.ConfigureServices` é executado.</span><span class="sxs-lookup"><span data-stu-id="d2f03-499">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="d2f03-500">Para usar um banco de dados diferente para os testes, o contexto do banco de dados do aplicativo deve ser substituído em `builder.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-500">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="d2f03-501">Para obter mais informações, consulte a seção [Personalizar WebApplicationFactory](#customize-webapplicationfactory) .</span><span class="sxs-lookup"><span data-stu-id="d2f03-501">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="d2f03-502">Para SUTs que ainda usam o [host da Web](xref:fundamentals/host/web-host), o retorno de chamada do aplicativo de teste `builder.ConfigureServices` é executado *antes* do código do SUT `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-502">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="d2f03-503">O retorno de chamada do aplicativo de teste `builder.ConfigureTestServices` é executado *após*.</span><span class="sxs-lookup"><span data-stu-id="d2f03-503">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d2f03-504">Os testes de integração garantem que os componentes de um aplicativo funcionem corretamente em um nível que inclui a infraestrutura de suporte do aplicativo, como o banco de dados, o sistema de arquivos e a rede.</span><span class="sxs-lookup"><span data-stu-id="d2f03-504">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="d2f03-505">O ASP.NET Core dá suporte a testes de integração usando uma estrutura de teste de unidade com um host da Web de teste e um servidor de teste na memória.</span><span class="sxs-lookup"><span data-stu-id="d2f03-505">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="d2f03-506">Este tópico pressupõe uma compreensão básica dos testes de unidade.</span><span class="sxs-lookup"><span data-stu-id="d2f03-506">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="d2f03-507">Se não estiver familiarizado com os conceitos de teste, consulte os [testes de unidade no .NET Core e .net Standard](/dotnet/core/testing/) tópico e seu conteúdo vinculado.</span><span class="sxs-lookup"><span data-stu-id="d2f03-507">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="d2f03-508">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d2f03-508">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d2f03-509">O aplicativo de exemplo é um Razor aplicativo de páginas e assume uma compreensão básica das Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="d2f03-509">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="d2f03-510">Se não estiver familiarizado com Razor páginas, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="d2f03-510">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="d2f03-511">[Introdução às Razor páginas](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="d2f03-511">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="d2f03-512">[Introdução às Razor páginas](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="d2f03-512">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="d2f03-513">[RazorTestes de unidade de páginas](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="d2f03-513">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="d2f03-514">Para testar o SPAs, recomendamos uma ferramenta como o [Selenium](https://www.seleniumhq.org/), que pode automatizar um navegador.</span><span class="sxs-lookup"><span data-stu-id="d2f03-514">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="d2f03-515">Introdução aos testes de integração</span><span class="sxs-lookup"><span data-stu-id="d2f03-515">Introduction to integration tests</span></span>

<span data-ttu-id="d2f03-516">Os testes de integração avaliam os componentes de um aplicativo em um nível mais amplo do que os [testes de unidade](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="d2f03-516">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="d2f03-517">Os testes de unidade são usados para testar componentes de software isolados, como métodos de classe individuais.</span><span class="sxs-lookup"><span data-stu-id="d2f03-517">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="d2f03-518">Testes de integração confirmam que dois ou mais componentes de aplicativo trabalham juntos para produzir um resultado esperado, possivelmente incluindo todos os componentes necessários para processar totalmente uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="d2f03-518">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="d2f03-519">Esses testes mais amplos são usados para testar a infraestrutura e a estrutura inteira do aplicativo, geralmente incluindo os seguintes componentes:</span><span class="sxs-lookup"><span data-stu-id="d2f03-519">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="d2f03-520">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="d2f03-520">Database</span></span>
* <span data-ttu-id="d2f03-521">Sistema de arquivos</span><span class="sxs-lookup"><span data-stu-id="d2f03-521">File system</span></span>
* <span data-ttu-id="d2f03-522">Dispositivos de rede</span><span class="sxs-lookup"><span data-stu-id="d2f03-522">Network appliances</span></span>
* <span data-ttu-id="d2f03-523">Pipeline de solicitação-resposta</span><span class="sxs-lookup"><span data-stu-id="d2f03-523">Request-response pipeline</span></span>

<span data-ttu-id="d2f03-524">Os testes de unidade usam componentes criei, conhecidos como *falsificações* ou *objetos fictícios*, em vez de componentes de infraestrutura.</span><span class="sxs-lookup"><span data-stu-id="d2f03-524">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="d2f03-525">Ao contrário dos testes de unidade, testes de integração:</span><span class="sxs-lookup"><span data-stu-id="d2f03-525">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="d2f03-526">Use os componentes reais que o aplicativo usa na produção.</span><span class="sxs-lookup"><span data-stu-id="d2f03-526">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="d2f03-527">Exigir mais processamento de código e de dados.</span><span class="sxs-lookup"><span data-stu-id="d2f03-527">Require more code and data processing.</span></span>
* <span data-ttu-id="d2f03-528">Demore mais para executar.</span><span class="sxs-lookup"><span data-stu-id="d2f03-528">Take longer to run.</span></span>

<span data-ttu-id="d2f03-529">Portanto, limite o uso de testes de integração para os cenários de infraestrutura mais importantes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-529">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="d2f03-530">Se um comportamento puder ser testado usando um teste de unidade ou um teste de integração, escolha o teste de unidade.</span><span class="sxs-lookup"><span data-stu-id="d2f03-530">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="d2f03-531">Não escreva testes de integração para todas as permutas possíveis de dados e acesso a arquivos com bancos e sistemas de arquivos.</span><span class="sxs-lookup"><span data-stu-id="d2f03-531">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="d2f03-532">Independentemente de quantos lugares em um aplicativo interagem com bancos de dados e sistemas de arquivos, um conjunto focado de testes de integração de leitura, gravação, atualização e exclusão geralmente é capaz de testar adequadamente os componentes do banco de dados e do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="d2f03-532">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="d2f03-533">Use testes de unidade para testes de rotina da lógica de método que interagem com esses componentes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-533">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="d2f03-534">Em testes de unidade, o uso de falsificações/simulações de infraestrutura resulta em uma execução de teste mais rápida.</span><span class="sxs-lookup"><span data-stu-id="d2f03-534">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="d2f03-535">Em discussões sobre testes de integração, o projeto testado é frequentemente chamado de *sistema em teste*, ou "SUT" para curto.</span><span class="sxs-lookup"><span data-stu-id="d2f03-535">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="d2f03-536">*"SUT" é usado em todo este tópico para se referir ao aplicativo ASP.NET Core testado.*</span><span class="sxs-lookup"><span data-stu-id="d2f03-536">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="d2f03-537">Testes de integração do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2f03-537">ASP.NET Core integration tests</span></span>

<span data-ttu-id="d2f03-538">Os testes de integração no ASP.NET Core exigem o seguinte:</span><span class="sxs-lookup"><span data-stu-id="d2f03-538">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="d2f03-539">Um projeto de teste é usado para conter e executar os testes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-539">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="d2f03-540">O projeto de teste tem uma referência para o SUT.</span><span class="sxs-lookup"><span data-stu-id="d2f03-540">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="d2f03-541">O projeto de teste cria um host Web de teste para o SUT e usa um cliente de servidor de teste para lidar com as solicitações e respostas com o SUT.</span><span class="sxs-lookup"><span data-stu-id="d2f03-541">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="d2f03-542">Um executor de teste é usado para executar os testes e relatar os resultados do teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-542">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="d2f03-543">Os testes de integração seguem uma sequência de eventos que incluem as etapas de teste de *organizar*, *Act*e *Assert* usuais:</span><span class="sxs-lookup"><span data-stu-id="d2f03-543">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="d2f03-544">O host da Web do SUT está configurado.</span><span class="sxs-lookup"><span data-stu-id="d2f03-544">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="d2f03-545">Um cliente de servidor de teste é criado para enviar solicitações ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-545">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="d2f03-546">A etapa *organizar* teste é executada: o aplicativo de teste prepara uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="d2f03-546">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="d2f03-547">A etapa de teste *Act* é executada: o cliente envia a solicitação e recebe a resposta.</span><span class="sxs-lookup"><span data-stu-id="d2f03-547">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="d2f03-548">A etapa de teste *Assert* é executada: a resposta *real* é validada como uma *passagem* ou *falha* com base em uma resposta *esperada* .</span><span class="sxs-lookup"><span data-stu-id="d2f03-548">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="d2f03-549">O processo continua até que todos os testes sejam executados.</span><span class="sxs-lookup"><span data-stu-id="d2f03-549">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="d2f03-550">Os resultados de teste são relatados.</span><span class="sxs-lookup"><span data-stu-id="d2f03-550">The test results are reported.</span></span>

<span data-ttu-id="d2f03-551">Normalmente, o host da Web de teste é configurado de modo diferente do host da Web normal do aplicativo para as execuções de teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-551">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="d2f03-552">Por exemplo, um banco de dados diferente ou configurações de aplicativo diferentes podem ser usadas para os testes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-552">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="d2f03-553">Os componentes de infraestrutura, como o host da Web de teste e o[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)(servidor de teste na memória), são fornecidos ou gerenciados pelo pacote [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="d2f03-553">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="d2f03-554">O uso desse pacote simplifica a criação e a execução de testes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-554">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="d2f03-555">O `Microsoft.AspNetCore.Mvc.Testing` pacote lida com as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="d2f03-555">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="d2f03-556">Copia o arquivo de dependências (*. deps*) do SUT para o diretório *bin* do projeto de teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-556">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="d2f03-557">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) como a raiz do projeto do SUT para que arquivos estáticos e páginas/exibições sejam encontrados quando os testes forem executados.</span><span class="sxs-lookup"><span data-stu-id="d2f03-557">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="d2f03-558">Fornece a classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) para simplificar a inicialização do SUT com `TestServer` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-558">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="d2f03-559">A documentação de [testes de unidade](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) descreve como configurar um projeto de teste e o Test Runner, juntamente com instruções detalhadas sobre como executar testes e recomendações sobre como nomear testes e classes de teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-559">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="d2f03-560">Ao criar um projeto de teste para um aplicativo, separe os testes de unidade dos testes de integração em projetos diferentes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-560">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="d2f03-561">Isso ajuda a garantir que os componentes de teste de infraestrutura não sejam incluídos acidentalmente nos testes de unidade.</span><span class="sxs-lookup"><span data-stu-id="d2f03-561">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="d2f03-562">A separação de testes de unidade e de integração também permite o controle sobre qual conjunto de testes são executados.</span><span class="sxs-lookup"><span data-stu-id="d2f03-562">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="d2f03-563">Praticamente não há nenhuma diferença entre a configuração para testes de Razor aplicativos de páginas e aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="d2f03-563">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="d2f03-564">A única diferença é em como os testes são nomeados.</span><span class="sxs-lookup"><span data-stu-id="d2f03-564">The only difference is in how the tests are named.</span></span> <span data-ttu-id="d2f03-565">Em um Razor aplicativo de páginas, os testes de pontos de extremidade de página geralmente são nomeados após a classe de modelo de página (por exemplo, `IndexPageTests` para testar a integração de componentes para a página de índice).</span><span class="sxs-lookup"><span data-stu-id="d2f03-565">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="d2f03-566">Em um aplicativo MVC, os testes geralmente são organizados por classes de controlador e nomeados após os controladores que testam (por exemplo, `HomeControllerTests` para testar a integração de componentes para o controlador doméstico).</span><span class="sxs-lookup"><span data-stu-id="d2f03-566">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="d2f03-567">Pré-requisitos do aplicativo de teste</span><span class="sxs-lookup"><span data-stu-id="d2f03-567">Test app prerequisites</span></span>

<span data-ttu-id="d2f03-568">O projeto de teste deve:</span><span class="sxs-lookup"><span data-stu-id="d2f03-568">The test project must:</span></span>

* <span data-ttu-id="d2f03-569">Faça referência aos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="d2f03-569">Reference the following packages:</span></span>
  * [<span data-ttu-id="d2f03-570">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="d2f03-570">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="d2f03-571">Microsoft. AspNetCore. Mvc. Testing</span><span class="sxs-lookup"><span data-stu-id="d2f03-571">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="d2f03-572">Especifique o SDK da Web no arquivo de projeto ( `<Project Sdk="Microsoft.NET.Sdk.Web">` ).</span><span class="sxs-lookup"><span data-stu-id="d2f03-572">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="d2f03-573">O SDK da Web é necessário ao fazer referência ao [metapacote do Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d2f03-573">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d2f03-574">Esses pré-requisitos podem ser vistos no aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="d2f03-574">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="d2f03-575">Inspecione o arquivo *tests/RazorPagesProject. Tests/RazorPagesProject. Tests. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d2f03-575">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="d2f03-576">O aplicativo de exemplo usa a estrutura de teste do [xUnit](https://xunit.github.io/) e a biblioteca do analisador [AngleSharp](https://anglesharp.github.io/) , portanto, o aplicativo de exemplo também faz referência a:</span><span class="sxs-lookup"><span data-stu-id="d2f03-576">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="d2f03-577">xUnit</span><span class="sxs-lookup"><span data-stu-id="d2f03-577">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="d2f03-578">xUnit. Runner. VisualStudio</span><span class="sxs-lookup"><span data-stu-id="d2f03-578">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="d2f03-579">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="d2f03-579">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="d2f03-580">Ambiente SUT</span><span class="sxs-lookup"><span data-stu-id="d2f03-580">SUT environment</span></span>

<span data-ttu-id="d2f03-581">Se o [ambiente](xref:fundamentals/environments) do SUT não estiver definido, o ambiente assume o padrão de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d2f03-581">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="d2f03-582">Testes básicos com o padrão WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="d2f03-582">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="d2f03-583">[WebApplicationFactory \< TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) é usado para criar um [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) para os testes de integração.</span><span class="sxs-lookup"><span data-stu-id="d2f03-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="d2f03-584">`TEntryPoint`é a classe de ponto de entrada do SUT, geralmente a `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="d2f03-584">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="d2f03-585">Classes de teste implementam uma interface de *acessório de classe* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) para indicar que a classe contém testes e fornece instâncias de objeto compartilhado entre os testes na classe.</span><span class="sxs-lookup"><span data-stu-id="d2f03-585">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="d2f03-586">A classe de teste a seguir, `BasicTests` , usa o `WebApplicationFactory` para inicializar o SUT e fornecer um [HttpClient](/dotnet/api/system.net.http.httpclient) para um método de teste, `Get_EndpointsReturnSuccessAndCorrectContentType` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-586">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="d2f03-587">O método verifica se o código de status de resposta é bem-sucedido (códigos de status no intervalo de 200-299) e o `Content-Type` cabeçalho é `text/html; charset=utf-8` para várias páginas de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-587">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="d2f03-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) cria uma instância do `HttpClient` que segue automaticamente os redirecionamentos e manipula cookies.</span><span class="sxs-lookup"><span data-stu-id="d2f03-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="d2f03-589">Por padrão, os cookies não essenciais não são preservados entre solicitações quando a [política de consentimento de GDPR](xref:security/gdpr) está habilitada.</span><span class="sxs-lookup"><span data-stu-id="d2f03-589">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="d2f03-590">Para preservar cookies não essenciais, como os usados pelo provedor TempData, marque-os como essenciais em seus testes.</span><span class="sxs-lookup"><span data-stu-id="d2f03-590">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="d2f03-591">Para obter instruções sobre como marcar um cookie como essencial, consulte [cookies essenciais](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="d2f03-591">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="d2f03-592">Personalizar o WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="d2f03-592">Customize WebApplicationFactory</span></span>

<span data-ttu-id="d2f03-593">A configuração do host Web pode ser criada independentemente das classes de teste herdando de `WebApplicationFactory` para criar uma ou mais fábricas personalizadas:</span><span class="sxs-lookup"><span data-stu-id="d2f03-593">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="d2f03-594">Herdar de `WebApplicationFactory` e substituir [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="d2f03-594">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="d2f03-595">O [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permite a configuração da coleção de serviços com [configuraservices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), que é executado antes do aplicativo `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-595">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d2f03-596">O [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permite substituir e modificar os serviços registrados na coleção de serviços com [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span><span class="sxs-lookup"><span data-stu-id="d2f03-596">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="d2f03-597">A propagação de banco de dados no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) é executada pelo `InitializeDbForTests` método.</span><span class="sxs-lookup"><span data-stu-id="d2f03-597">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="d2f03-598">O método é descrito na seção [testes de integração exemplo: testar organização de aplicativos](#test-app-organization) .</span><span class="sxs-lookup"><span data-stu-id="d2f03-598">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="d2f03-599">Use as `CustomWebApplicationFactory` classes personalizadas em teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-599">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="d2f03-600">O exemplo a seguir usa a fábrica na `IndexPageTests` classe:</span><span class="sxs-lookup"><span data-stu-id="d2f03-600">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="d2f03-601">O cliente do aplicativo de exemplo está configurado para impedir o `HttpClient` dos redirecionamentos a seguir.</span><span class="sxs-lookup"><span data-stu-id="d2f03-601">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="d2f03-602">Conforme explicado mais adiante na seção de [autenticação fictícia](#mock-authentication) , isso permite que os testes verifiquem o resultado da primeira resposta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-602">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="d2f03-603">A primeira resposta é um redirecionamento em muitos desses testes com um `Location` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="d2f03-603">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="d2f03-604">Um teste típico usa os `HttpClient` métodos auxiliares e para processar a solicitação e a resposta:</span><span class="sxs-lookup"><span data-stu-id="d2f03-604">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="d2f03-605">Qualquer solicitação POST para o SUT deve satisfazer a verificação antifalsificação feita automaticamente pelo [sistema antifalsificação de proteção de dados](xref:security/data-protection/introduction)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-605">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="d2f03-606">Para organizar a solicitação POST de um teste, o aplicativo de teste deve:</span><span class="sxs-lookup"><span data-stu-id="d2f03-606">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="d2f03-607">Faça uma solicitação para a página.</span><span class="sxs-lookup"><span data-stu-id="d2f03-607">Make a request for the page.</span></span>
1. <span data-ttu-id="d2f03-608">Analise o cookie antifalsificação e solicite o token de validação da resposta.</span><span class="sxs-lookup"><span data-stu-id="d2f03-608">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="d2f03-609">Faça a solicitação POST com o cookie antifalsificação e solicite o token de validação em vigor.</span><span class="sxs-lookup"><span data-stu-id="d2f03-609">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="d2f03-610">Os `SendAsync` métodos de extensão auxiliares (*Helpers/HttpClientExtensions. cs*) e o `GetDocumentAsync` método auxiliar (*Helpers/HtmlHelpers. cs*) no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) usam o analisador [AngleSharp](https://anglesharp.github.io/) para lidar com a verificação antifalsificação com os seguintes métodos:</span><span class="sxs-lookup"><span data-stu-id="d2f03-610">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="d2f03-611">`GetDocumentAsync`&ndash;Recebe o [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) e retorna um `IHtmlDocument` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-611">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="d2f03-612">`GetDocumentAsync`usa uma fábrica que prepara uma *resposta virtual* com base no original `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-612">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="d2f03-613">Para obter mais informações, consulte a [documentação do AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="d2f03-613">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="d2f03-614">`SendAsync`métodos de extensão para `HttpClient` compor um [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) e chamar [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) para enviar solicitações para o SUT.</span><span class="sxs-lookup"><span data-stu-id="d2f03-614">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="d2f03-615">Sobrecargas para `SendAsync` aceitar o formulário HTML ( `IHtmlFormElement` ) e o seguinte:</span><span class="sxs-lookup"><span data-stu-id="d2f03-615">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="d2f03-616">Botão enviar do formulário ( `IHtmlElement` )</span><span class="sxs-lookup"><span data-stu-id="d2f03-616">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="d2f03-617">Coleção de valores de formulário ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="d2f03-617">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="d2f03-618">Enviar botão ( `IHtmlElement` ) e valores de formulário ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="d2f03-618">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="d2f03-619">[AngleSharp](https://anglesharp.github.io/) é uma biblioteca de análise de terceiros usada para fins de demonstração neste tópico e no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-619">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="d2f03-620">AngleSharp não tem suporte ou é necessário para o teste de integração de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2f03-620">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="d2f03-621">Outros analisadores podem ser usados, como o [Hap (HTML agilidade Pack)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="d2f03-621">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="d2f03-622">Outra abordagem é escrever código para lidar diretamente com o token de verificação de solicitação do sistema antifalsificação e o cookie antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="d2f03-622">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="d2f03-623">Personalizar o cliente com o WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="d2f03-623">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="d2f03-624">Quando uma configuração adicional é necessária dentro de um método de teste, o [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) cria um novo `WebApplicationFactory` com um [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) que é ainda mais personalizado pela configuração.</span><span class="sxs-lookup"><span data-stu-id="d2f03-624">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="d2f03-625">O `Post_DeleteMessageHandler_ReturnsRedirectToRoot` método de teste do [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstra o uso de `WithWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-625">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="d2f03-626">Esse teste executa uma exclusão de registro no banco de dados disparando um envio de formulário no SUT.</span><span class="sxs-lookup"><span data-stu-id="d2f03-626">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="d2f03-627">Como outro teste na `IndexPageTests` classe executa uma operação que exclui todos os registros no banco de dados e pode ser executado antes do `Post_DeleteMessageHandler_ReturnsRedirectToRoot` método, o banco de dados é propagado nesse método de teste para garantir que um registro esteja presente para que o SUT seja excluído.</span><span class="sxs-lookup"><span data-stu-id="d2f03-627">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="d2f03-628">Selecionar o primeiro botão excluir do `messages` formulário no SUT é simulado na solicitação para o SUT:</span><span class="sxs-lookup"><span data-stu-id="d2f03-628">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="d2f03-629">Opções de cliente</span><span class="sxs-lookup"><span data-stu-id="d2f03-629">Client options</span></span>

<span data-ttu-id="d2f03-630">A tabela a seguir mostra o [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) padrão disponível ao criar `HttpClient` instâncias.</span><span class="sxs-lookup"><span data-stu-id="d2f03-630">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="d2f03-631">Opção</span><span class="sxs-lookup"><span data-stu-id="d2f03-631">Option</span></span> | <span data-ttu-id="d2f03-632">Descrição</span><span class="sxs-lookup"><span data-stu-id="d2f03-632">Description</span></span> | <span data-ttu-id="d2f03-633">Padrão</span><span class="sxs-lookup"><span data-stu-id="d2f03-633">Default</span></span> |
| ---
<span data-ttu-id="d2f03-634">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-635">'Blazor'</span></span>
- <span data-ttu-id="d2f03-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-636">'Identity'</span></span>
- <span data-ttu-id="d2f03-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-638">'Razor'</span></span>
- <span data-ttu-id="d2f03-639">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-639">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-640">--- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-641">'Blazor'</span></span>
- <span data-ttu-id="d2f03-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-642">'Identity'</span></span>
- <span data-ttu-id="d2f03-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-644">'Razor'</span></span>
- <span data-ttu-id="d2f03-645">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-646">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-647">'Blazor'</span></span>
- <span data-ttu-id="d2f03-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-648">'Identity'</span></span>
- <span data-ttu-id="d2f03-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-650">'Razor'</span></span>
- <span data-ttu-id="d2f03-651">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-652">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-653">'Blazor'</span></span>
- <span data-ttu-id="d2f03-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-654">'Identity'</span></span>
- <span data-ttu-id="d2f03-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-656">'Razor'</span></span>
- <span data-ttu-id="d2f03-657">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-657">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-658">------ | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-659">'Blazor'</span></span>
- <span data-ttu-id="d2f03-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-660">'Identity'</span></span>
- <span data-ttu-id="d2f03-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-662">'Razor'</span></span>
- <span data-ttu-id="d2f03-663">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-663">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtém ou define se as `HttpClient` instâncias devem seguir ou não automaticamente as respostas de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="d2f03-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="d2f03-665"> | `true`| | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtém ou define o endereço base das `HttpClient` instâncias.</span><span class="sxs-lookup"><span data-stu-id="d2f03-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="d2f03-666"> | `http://localhost`| | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtém ou define se as `HttpClient` instâncias devem lidar com cookies.</span><span class="sxs-lookup"><span data-stu-id="d2f03-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="d2f03-667"> | `true`| | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtém ou define o número máximo de respostas de redirecionamento que as `HttpClient` instâncias devem seguir.</span><span class="sxs-lookup"><span data-stu-id="d2f03-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="d2f03-668">| 7 |</span><span class="sxs-lookup"><span data-stu-id="d2f03-668">| 7 |</span></span>

<span data-ttu-id="d2f03-669">Crie a `WebApplicationFactoryClientOptions` classe e passe-a para o método [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (os valores padrão são mostrados no exemplo de código):</span><span class="sxs-lookup"><span data-stu-id="d2f03-669">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="d2f03-670">Injetar serviços fictícios</span><span class="sxs-lookup"><span data-stu-id="d2f03-670">Inject mock services</span></span>

<span data-ttu-id="d2f03-671">Os serviços podem ser substituídos em um teste com uma chamada para [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) no host Builder.</span><span class="sxs-lookup"><span data-stu-id="d2f03-671">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="d2f03-672">**Para injetar serviços fictícios, o SUT deve ter uma `Startup` classe com um `Startup.ConfigureServices` método.**</span><span class="sxs-lookup"><span data-stu-id="d2f03-672">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="d2f03-673">O SUT de exemplo inclui um serviço com escopo que retorna uma aspa.</span><span class="sxs-lookup"><span data-stu-id="d2f03-673">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="d2f03-674">A cotação é inserida em um campo oculto na página de índice quando a página de índice é solicitada.</span><span class="sxs-lookup"><span data-stu-id="d2f03-674">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="d2f03-675">*Serviços/IQuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="d2f03-675">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="d2f03-676">*Serviços/QuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="d2f03-676">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="d2f03-677">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2f03-677">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="d2f03-678">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2f03-678">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="d2f03-679">*Páginas/index. cs*:</span><span class="sxs-lookup"><span data-stu-id="d2f03-679">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="d2f03-680">A marcação a seguir é gerada quando o aplicativo SUT é executado:</span><span class="sxs-lookup"><span data-stu-id="d2f03-680">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="d2f03-681">Para testar o serviço e a injeção de aspas em um teste de integração, um serviço fictício é injetado no SUT pelo teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-681">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="d2f03-682">O serviço fictício substitui o aplicativo `QuoteService` por um serviço fornecido pelo aplicativo de teste, chamado `TestQuoteService` :</span><span class="sxs-lookup"><span data-stu-id="d2f03-682">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="d2f03-683">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2f03-683">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="d2f03-684">`ConfigureTestServices`é chamado e o serviço com escopo é registrado:</span><span class="sxs-lookup"><span data-stu-id="d2f03-684">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="d2f03-685">A marcação produzida durante a execução do teste reflete o texto da citação fornecido pelo `TestQuoteService` , assim, a asserção passa:</span><span class="sxs-lookup"><span data-stu-id="d2f03-685">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="d2f03-686">Autenticação fictícia</span><span class="sxs-lookup"><span data-stu-id="d2f03-686">Mock authentication</span></span>

<span data-ttu-id="d2f03-687">Testes na `AuthTests` classe verificam se um ponto de extremidade seguro:</span><span class="sxs-lookup"><span data-stu-id="d2f03-687">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="d2f03-688">Redireciona um usuário não autenticado para a página de logon do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-688">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="d2f03-689">Retorna o conteúdo de um usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="d2f03-689">Returns content for an authenticated user.</span></span>

<span data-ttu-id="d2f03-690">No SUT, a `/SecurePage` página usa uma convenção [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) para aplicar um [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à página.</span><span class="sxs-lookup"><span data-stu-id="d2f03-690">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="d2f03-691">Para obter mais informações, consulte [ Razor páginas convenções de autorização](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="d2f03-691">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="d2f03-692">No `Get_SecurePageRedirectsAnUnauthenticatedUser` teste, um [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) é definido para não permitir redirecionamentos definindo [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) como `false` :</span><span class="sxs-lookup"><span data-stu-id="d2f03-692">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="d2f03-693">Ao não permitir que o cliente siga o redirecionamento, as seguintes verificações podem ser feitas:</span><span class="sxs-lookup"><span data-stu-id="d2f03-693">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="d2f03-694">O código de status retornado pelo SUT pode ser verificado em relação ao resultado de [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) esperado, não ao código de status final após o redirecionamento para a página de logon, que seria [HttpStatusCode. ok](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="d2f03-694">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="d2f03-695">O `Location` valor do cabeçalho nos cabeçalhos de resposta é verificado para confirmar que ele começa com `http://localhost/Identity/Account/Login` , não a resposta da página final de logon, em que o cabeçalho não estaria `Location` presente.</span><span class="sxs-lookup"><span data-stu-id="d2f03-695">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="d2f03-696">O aplicativo de teste pode simular um <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> no [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) para testar aspectos de autenticação e autorização.</span><span class="sxs-lookup"><span data-stu-id="d2f03-696">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="d2f03-697">Um cenário mínimo retorna um [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="d2f03-697">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="d2f03-698">O `TestAuthHandler` é chamado para autenticar um usuário quando o esquema de autenticação é definido como `Test` onde o `AddAuthentication` está registrado para `ConfigureTestServices` :</span><span class="sxs-lookup"><span data-stu-id="d2f03-698">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="d2f03-699">Para obter mais informações sobre `WebApplicationFactoryClientOptions` o, consulte a seção [Opções de cliente](#client-options) .</span><span class="sxs-lookup"><span data-stu-id="d2f03-699">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="d2f03-700">Definir o ambiente</span><span class="sxs-lookup"><span data-stu-id="d2f03-700">Set the environment</span></span>

<span data-ttu-id="d2f03-701">Por padrão, o host e o ambiente de aplicativo do SUT são configurados para usar o ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d2f03-701">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="d2f03-702">Para substituir o ambiente do SUT:</span><span class="sxs-lookup"><span data-stu-id="d2f03-702">To override the SUT's environment:</span></span>

* <span data-ttu-id="d2f03-703">Defina a `ASPNETCORE_ENVIRONMENT` variável de ambiente (por exemplo,, `Staging` `Production` ou outro valor personalizado, como `Testing` ).</span><span class="sxs-lookup"><span data-stu-id="d2f03-703">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="d2f03-704">Substitua `CreateWebHostBuilder` no aplicativo de teste para ler a `ASPNETCORE_ENVIRONMENT` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="d2f03-704">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

<span data-ttu-id="d2f03-705">O ambiente também pode ser definido diretamente no construtor de hosts em um personalizado <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="d2f03-705">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="d2f03-706">Como a infraestrutura de teste infere o caminho raiz do conteúdo do aplicativo</span><span class="sxs-lookup"><span data-stu-id="d2f03-706">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="d2f03-707">O `WebApplicationFactory` Construtor infere o caminho [raiz do conteúdo](xref:fundamentals/index#content-root) do aplicativo pesquisando um [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) no assembly que contém os testes de integração com uma chave igual ao `TEntryPoint` assembly `System.Reflection.Assembly.FullName` .</span><span class="sxs-lookup"><span data-stu-id="d2f03-707">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="d2f03-708">Caso um atributo com a chave correta não seja encontrado, `WebApplicationFactory` volte à pesquisa de um arquivo de solução (*. sln*) e acrescente o `TEntryPoint` nome do assembly ao diretório da solução.</span><span class="sxs-lookup"><span data-stu-id="d2f03-708">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="d2f03-709">O diretório raiz do aplicativo (o caminho raiz do conteúdo) é usado para descobrir exibições e arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-709">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="d2f03-710">Desabilitar cópia de sombra</span><span class="sxs-lookup"><span data-stu-id="d2f03-710">Disable shadow copying</span></span>

<span data-ttu-id="d2f03-711">A cópia de sombra faz com que os testes sejam executados em um diretório diferente do diretório de saída.</span><span class="sxs-lookup"><span data-stu-id="d2f03-711">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="d2f03-712">Para que os testes funcionem corretamente, a cópia de sombra deve ser desabilitada.</span><span class="sxs-lookup"><span data-stu-id="d2f03-712">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="d2f03-713">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) usa xUnit e desabilita a cópia de sombra para xUnit, incluindo um arquivo *xUnit. Runner. JSON* com a definição de configuração correta.</span><span class="sxs-lookup"><span data-stu-id="d2f03-713">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="d2f03-714">Para obter mais informações, consulte [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="d2f03-714">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="d2f03-715">Adicione o arquivo *xUnit. Runner. JSON* à raiz do projeto de teste com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="d2f03-715">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="d2f03-716">Se estiver usando o Visual Studio, defina a propriedade **copiar para diretório de saída** do arquivo como **copiar sempre**.</span><span class="sxs-lookup"><span data-stu-id="d2f03-716">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="d2f03-717">Se não estiver usando o Visual Studio, adicione um `Content` destino ao arquivo de projeto do aplicativo de teste:</span><span class="sxs-lookup"><span data-stu-id="d2f03-717">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="d2f03-718">Alienação de objetos</span><span class="sxs-lookup"><span data-stu-id="d2f03-718">Disposal of objects</span></span>

<span data-ttu-id="d2f03-719">Depois que os testes da `IClassFixture` implementação são executados, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) e [HttpClient](/dotnet/api/system.net.http.httpclient) são descartados quando xUnit descarta o [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="d2f03-719">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="d2f03-720">Se os objetos instanciados pelo desenvolvedor exigirem descarte, descarte-os na `IClassFixture` implementação.</span><span class="sxs-lookup"><span data-stu-id="d2f03-720">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="d2f03-721">Para obter mais informações, consulte [implementando um método Dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="d2f03-721">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="d2f03-722">Exemplo de testes de integração</span><span class="sxs-lookup"><span data-stu-id="d2f03-722">Integration tests sample</span></span>

<span data-ttu-id="d2f03-723">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) é composto por dois aplicativos:</span><span class="sxs-lookup"><span data-stu-id="d2f03-723">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="d2f03-724">Aplicativo</span><span class="sxs-lookup"><span data-stu-id="d2f03-724">App</span></span> | <span data-ttu-id="d2f03-725">Diretório do projeto</span><span class="sxs-lookup"><span data-stu-id="d2f03-725">Project directory</span></span> | <span data-ttu-id="d2f03-726">Descrição</span><span class="sxs-lookup"><span data-stu-id="d2f03-726">Description</span></span> |
| --- | ---
<span data-ttu-id="d2f03-727">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-728">'Blazor'</span></span>
- <span data-ttu-id="d2f03-729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-729">'Identity'</span></span>
- <span data-ttu-id="d2f03-730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-730">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-731">'Razor'</span></span>
- <span data-ttu-id="d2f03-732">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-733">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-734">'Blazor'</span></span>
- <span data-ttu-id="d2f03-735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-735">'Identity'</span></span>
- <span data-ttu-id="d2f03-736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-736">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-737">'Razor'</span></span>
- <span data-ttu-id="d2f03-738">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-739">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-740">'Blazor'</span></span>
- <span data-ttu-id="d2f03-741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-741">'Identity'</span></span>
- <span data-ttu-id="d2f03-742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-742">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-743">'Razor'</span></span>
- <span data-ttu-id="d2f03-744">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-745">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-746">'Blazor'</span></span>
- <span data-ttu-id="d2f03-747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-747">'Identity'</span></span>
- <span data-ttu-id="d2f03-748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-748">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-749">'Razor'</span></span>
- <span data-ttu-id="d2f03-750">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-751">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-752">'Blazor'</span></span>
- <span data-ttu-id="d2f03-753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-753">'Identity'</span></span>
- <span data-ttu-id="d2f03-754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-754">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-755">'Razor'</span></span>
- <span data-ttu-id="d2f03-756">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-757">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-758">'Blazor'</span></span>
- <span data-ttu-id="d2f03-759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-759">'Identity'</span></span>
- <span data-ttu-id="d2f03-760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-760">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-761">'Razor'</span></span>
- <span data-ttu-id="d2f03-762">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-762">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-763">--------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-764">'Blazor'</span></span>
- <span data-ttu-id="d2f03-765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-765">'Identity'</span></span>
- <span data-ttu-id="d2f03-766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-766">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-767">'Razor'</span></span>
- <span data-ttu-id="d2f03-768">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-769">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-770">'Blazor'</span></span>
- <span data-ttu-id="d2f03-771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-771">'Identity'</span></span>
- <span data-ttu-id="d2f03-772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-772">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-773">'Razor'</span></span>
- <span data-ttu-id="d2f03-774">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-775">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-776">'Blazor'</span></span>
- <span data-ttu-id="d2f03-777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-777">'Identity'</span></span>
- <span data-ttu-id="d2f03-778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-778">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-779">'Razor'</span></span>
- <span data-ttu-id="d2f03-780">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-780">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-781">------ | | Aplicativo de mensagens (o SUT) | *src/RazorPagesProject* | Permite que um usuário adicione, exclua um, exclua todos e analise as mensagens.</span><span class="sxs-lookup"><span data-stu-id="d2f03-781">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="d2f03-782">| | Aplicativo de teste | *tests/RazorPagesProject. Tests* | Usado para testar a integração do SUT.</span><span class="sxs-lookup"><span data-stu-id="d2f03-782">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="d2f03-783">Os testes podem ser executados usando os recursos de teste internos de um IDE, como o [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="d2f03-783">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="d2f03-784">Se estiver usando [Visual Studio Code](https://code.visualstudio.com/) ou a linha de comando, execute o seguinte comando em um prompt de comando no diretório *tests/RazorPagesProject. Tests* :</span><span class="sxs-lookup"><span data-stu-id="d2f03-784">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="d2f03-785">Organização do aplicativo de mensagem (SUT)</span><span class="sxs-lookup"><span data-stu-id="d2f03-785">Message app (SUT) organization</span></span>

<span data-ttu-id="d2f03-786">O SUT é um Razor sistema de mensagens de páginas com as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="d2f03-786">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="d2f03-787">A página de índice do aplicativo (*pages/index. cshtml* e *pages/index. cshtml. cs*) fornece uma interface do usuário e métodos de modelo de página para controlar a adição, a exclusão e a análise de mensagens (média de palavras por mensagem).</span><span class="sxs-lookup"><span data-stu-id="d2f03-787">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="d2f03-788">Uma mensagem é descrita pela `Message` classe (*Data/mensagem. cs*) com duas propriedades: `Id` (chave) e `Text` (mensagem).</span><span class="sxs-lookup"><span data-stu-id="d2f03-788">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="d2f03-789">A `Text` propriedade é necessária e limitada a 200 caracteres.</span><span class="sxs-lookup"><span data-stu-id="d2f03-789">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="d2f03-790">As mensagens são armazenadas usando&#8224; [de banco de dados na memória Entity Framework](/ef/core/providers/in-memory/) .</span><span class="sxs-lookup"><span data-stu-id="d2f03-790">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="d2f03-791">O aplicativo contém uma camada de acesso a dados (DAL) em sua classe de contexto de banco de dados, `AppDbContext` (*Data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="d2f03-791">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="d2f03-792">Se o banco de dados estiver vazio na inicialização do aplicativo, o repositório de mensagens será inicializado com três mensagens.</span><span class="sxs-lookup"><span data-stu-id="d2f03-792">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="d2f03-793">O aplicativo inclui um `/SecurePage` que só pode ser acessado por um usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="d2f03-793">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="d2f03-794">&#8224;tópico do EF, [teste com inmemory](/ef/core/miscellaneous/testing/in-memory), explica como usar um banco de dados na memória para testes com MSTest.</span><span class="sxs-lookup"><span data-stu-id="d2f03-794">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="d2f03-795">Este tópico usa a estrutura de teste do [xUnit](https://xunit.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="d2f03-795">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="d2f03-796">Conceitos de teste e implementações de teste em diferentes estruturas de teste são semelhantes, mas não idênticos.</span><span class="sxs-lookup"><span data-stu-id="d2f03-796">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="d2f03-797">Embora o aplicativo não use o padrão de repositório e não seja um exemplo eficaz do [padrão de UoW (unidade de trabalho)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor as páginas dão suporte a esses padrões de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d2f03-797">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="d2f03-798">Para obter mais informações, consulte [projetando a camada de persistência de infraestrutura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e a [lógica do controlador de teste](/aspnet/core/mvc/controllers/testing) (o exemplo implementa o padrão de repositório).</span><span class="sxs-lookup"><span data-stu-id="d2f03-798">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="d2f03-799">Organização de aplicativos de teste</span><span class="sxs-lookup"><span data-stu-id="d2f03-799">Test app organization</span></span>

<span data-ttu-id="d2f03-800">O aplicativo de teste é um aplicativo de console dentro do diretório *tests/RazorPagesProject. Tests* .</span><span class="sxs-lookup"><span data-stu-id="d2f03-800">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="d2f03-801">Testar diretório do aplicativo</span><span class="sxs-lookup"><span data-stu-id="d2f03-801">Test app directory</span></span> | <span data-ttu-id="d2f03-802">Descrição</span><span class="sxs-lookup"><span data-stu-id="d2f03-802">Description</span></span> |
| ---
<span data-ttu-id="d2f03-803">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-804">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-804">'Blazor'</span></span>
- <span data-ttu-id="d2f03-805">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-805">'Identity'</span></span>
- <span data-ttu-id="d2f03-806">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-806">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-807">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-807">'Razor'</span></span>
- <span data-ttu-id="d2f03-808">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-808">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-809">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-810">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-810">'Blazor'</span></span>
- <span data-ttu-id="d2f03-811">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-811">'Identity'</span></span>
- <span data-ttu-id="d2f03-812">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-812">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-813">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-813">'Razor'</span></span>
- <span data-ttu-id="d2f03-814">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-814">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-815">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-816">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-816">'Blazor'</span></span>
- <span data-ttu-id="d2f03-817">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-817">'Identity'</span></span>
- <span data-ttu-id="d2f03-818">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-818">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-819">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-819">'Razor'</span></span>
- <span data-ttu-id="d2f03-820">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-820">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-821">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-822">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-822">'Blazor'</span></span>
- <span data-ttu-id="d2f03-823">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-823">'Identity'</span></span>
- <span data-ttu-id="d2f03-824">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-824">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-825">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-825">'Razor'</span></span>
- <span data-ttu-id="d2f03-826">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-826">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-827">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-828">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-828">'Blazor'</span></span>
- <span data-ttu-id="d2f03-829">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-829">'Identity'</span></span>
- <span data-ttu-id="d2f03-830">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-830">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-831">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-831">'Razor'</span></span>
- <span data-ttu-id="d2f03-832">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-832">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-833">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-834">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-834">'Blazor'</span></span>
- <span data-ttu-id="d2f03-835">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-835">'Identity'</span></span>
- <span data-ttu-id="d2f03-836">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-836">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-837">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-837">'Razor'</span></span>
- <span data-ttu-id="d2f03-838">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-838">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-839">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-840">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-840">'Blazor'</span></span>
- <span data-ttu-id="d2f03-841">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-841">'Identity'</span></span>
- <span data-ttu-id="d2f03-842">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-842">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-843">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-843">'Razor'</span></span>
- <span data-ttu-id="d2f03-844">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-844">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-845">--------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-846">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-846">'Blazor'</span></span>
- <span data-ttu-id="d2f03-847">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-847">'Identity'</span></span>
- <span data-ttu-id="d2f03-848">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-848">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-849">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-849">'Razor'</span></span>
- <span data-ttu-id="d2f03-850">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-850">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-851">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-852">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-852">'Blazor'</span></span>
- <span data-ttu-id="d2f03-853">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-853">'Identity'</span></span>
- <span data-ttu-id="d2f03-854">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-854">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-855">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-855">'Razor'</span></span>
- <span data-ttu-id="d2f03-856">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-856">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2f03-857">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d2f03-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2f03-858">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-858">'Blazor'</span></span>
- <span data-ttu-id="d2f03-859">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2f03-859">'Identity'</span></span>
- <span data-ttu-id="d2f03-860">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2f03-860">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2f03-861">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2f03-861">'Razor'</span></span>
- <span data-ttu-id="d2f03-862">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d2f03-862">'SignalR' uid:</span></span> 

<span data-ttu-id="d2f03-863">------ | | *AuthTests* | Contém métodos de teste para:</span><span class="sxs-lookup"><span data-stu-id="d2f03-863">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="d2f03-864">Acessando uma página segura por um usuário não autenticado.</span><span class="sxs-lookup"><span data-stu-id="d2f03-864">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="d2f03-865">Acessar uma página segura por um usuário autenticado com uma simulação <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="d2f03-865">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="d2f03-866">Obter um perfil de usuário do GitHub e verificar o logon do usuário do perfil.</span><span class="sxs-lookup"><span data-stu-id="d2f03-866">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="d2f03-867">| | *BasicTests* | Contém um método de teste para roteamento e tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="d2f03-867">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="d2f03-868">| | *IntegrationTests* | Contém os testes de integração para a página de índice usando a `WebApplicationFactory` classe personalizada.</span><span class="sxs-lookup"><span data-stu-id="d2f03-868">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="d2f03-869">| | *Auxiliares/utilitários* | </span><span class="sxs-lookup"><span data-stu-id="d2f03-869">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="d2f03-870">*Utilities.cs* contém o `InitializeDbForTests` método usado para propagar o banco de dados com dado de teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-870">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="d2f03-871">*HtmlHelpers.cs* fornece um método para retornar um AngleSharp `IHtmlDocument` para uso pelos métodos de teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-871">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="d2f03-872">*HttpClientExtensions.cs* fornecem sobrecargas para `SendAsync` o enviar solicitações para o SUT.</span><span class="sxs-lookup"><span data-stu-id="d2f03-872">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="d2f03-873">A estrutura de teste é [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="d2f03-873">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="d2f03-874">Os testes de integração são realizados usando o [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), que inclui o [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="d2f03-874">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="d2f03-875">Como o pacote [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) é usado para configurar o host de teste e o servidor de teste, os `TestHost` `TestServer` pacotes e não exigem referências diretas de pacote no arquivo de projeto do aplicativo de teste ou na configuração do desenvolvedor no aplicativo de teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-875">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="d2f03-876">**Propagando o banco de dados para teste**</span><span class="sxs-lookup"><span data-stu-id="d2f03-876">**Seeding the database for testing**</span></span>

<span data-ttu-id="d2f03-877">Os testes de integração geralmente exigem um pequeno conjunto de dados no banco antes da execução do teste.</span><span class="sxs-lookup"><span data-stu-id="d2f03-877">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="d2f03-878">Por exemplo, um teste de exclusão chama uma exclusão de registro de banco de dados, de modo que o banco de dados deve ter pelo menos um registro para que a solicitação de exclusão tenha sucesso.</span><span class="sxs-lookup"><span data-stu-id="d2f03-878">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="d2f03-879">O aplicativo de exemplo propaga o banco de dados com três mensagens no *Utilities.cs* que os testes podem usar quando executam:</span><span class="sxs-lookup"><span data-stu-id="d2f03-879">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d2f03-880">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d2f03-880">Additional resources</span></span>

* [<span data-ttu-id="d2f03-881">Testes de unidade</span><span class="sxs-lookup"><span data-stu-id="d2f03-881">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>

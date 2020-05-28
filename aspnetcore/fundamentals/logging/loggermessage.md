---
<span data-ttu-id="5dabc-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5dabc-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5dabc-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5dabc-102">'Blazor'</span></span>
- <span data-ttu-id="5dabc-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5dabc-103">'Identity'</span></span>
- <span data-ttu-id="5dabc-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5dabc-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5dabc-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5dabc-105">'Razor'</span></span>
- <span data-ttu-id="5dabc-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5dabc-106">'SignalR' uid:</span></span> 

---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="5dabc-107">Registro em log de alto desempenho com o LoggerMessage no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5dabc-107">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5dabc-108">Os recursos do <xref:Microsoft.Extensions.Logging.LoggerMessage> criam delegados armazenáveis em cache que exigem menos alocações de objeto e sobrecarga de computação reduzida em comparação aos [métodos de extensão do agente](xref:Microsoft.Extensions.Logging.LoggerExtensions), como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> e <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span><span class="sxs-lookup"><span data-stu-id="5dabc-108"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="5dabc-109">Para cenários de registro em log de alto desempenho, use o padrão <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="5dabc-109">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="5dabc-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> fornece as seguintes vantagens de desempenho em relação aos métodos de extensão do Agente:</span><span class="sxs-lookup"><span data-stu-id="5dabc-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="5dabc-111">Métodos de extensão do agente exigem tipos de valor de conversão boxing, como `int`, em `object`.</span><span class="sxs-lookup"><span data-stu-id="5dabc-111">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="5dabc-112">O padrão <xref:Microsoft.Extensions.Logging.LoggerMessage> evita a conversão boxing usando campos <xref:System.Action> estáticos e métodos de extensão com parâmetros fortemente tipados.</span><span class="sxs-lookup"><span data-stu-id="5dabc-112">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="5dabc-113">Os métodos de extensão do agente precisam analisar o modelo de mensagem (cadeia de caracteres de formato nomeada) sempre que uma mensagem de log é gravada.</span><span class="sxs-lookup"><span data-stu-id="5dabc-113">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="5dabc-114"><xref:Microsoft.Extensions.Logging.LoggerMessage> exige apenas a análise de um modelo uma vez quando a mensagem é definida.</span><span class="sxs-lookup"><span data-stu-id="5dabc-114"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="5dabc-115">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5dabc-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5dabc-116">O aplicativo de exemplo demonstra recursos do <xref:Microsoft.Extensions.Logging.LoggerMessage> com um sistema básico de acompanhamento de aspas.</span><span class="sxs-lookup"><span data-stu-id="5dabc-116">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="5dabc-117">O aplicativo adiciona e exclui aspas usando um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="5dabc-117">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="5dabc-118">Conforme ocorrem essas operações, são geradas mensagens de log usando o padrão <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="5dabc-118">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="5dabc-119">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="5dabc-119">LoggerMessage.Define</span></span>

<span data-ttu-id="5dabc-120">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) cria um delegado <xref:System.Action> para registrar uma mensagem em log.</span><span class="sxs-lookup"><span data-stu-id="5dabc-120">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="5dabc-121">Sobrecargas de <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> permitem passar até seis parâmetros de tipo para uma cadeia de caracteres de formato nomeada (modelo).</span><span class="sxs-lookup"><span data-stu-id="5dabc-121"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="5dabc-122">A cadeia de caracteres fornecida para o método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> é um modelo e não uma cadeia de caracteres interpolada.</span><span class="sxs-lookup"><span data-stu-id="5dabc-122">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="5dabc-123">Os espaços reservados são preenchidos na ordem em que os tipos são especificados.</span><span class="sxs-lookup"><span data-stu-id="5dabc-123">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="5dabc-124">Os nomes do espaço reservado no modelo devem ser descritivos e consistentes em todos os modelos.</span><span class="sxs-lookup"><span data-stu-id="5dabc-124">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="5dabc-125">Eles servem como nomes de propriedade em dados de log estruturado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-125">They serve as property names within structured log data.</span></span> <span data-ttu-id="5dabc-126">Recomendamos o uso da [formatação Pascal Case](/dotnet/standard/design-guidelines/capitalization-conventions) para nomes de espaço reservado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-126">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="5dabc-127">Por exemplo, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="5dabc-127">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="5dabc-128">Cada mensagem de log é uma <xref:System.Action> mantida em um campo estático criado por [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="5dabc-128">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="5dabc-129">Por exemplo, o aplicativo de exemplo cria um campo para descrever uma mensagem de log para uma solicitação GET para a página de Índice (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="5dabc-129">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="5dabc-130">Para a <xref:System.Action>, especifique:</span><span class="sxs-lookup"><span data-stu-id="5dabc-130">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="5dabc-131">O nível de log.</span><span class="sxs-lookup"><span data-stu-id="5dabc-131">The log level.</span></span>
* <span data-ttu-id="5dabc-132">Um identificador de evento exclusivo (<xref:Microsoft.Extensions.Logging.EventId>) com o nome do método de extensão estático.</span><span class="sxs-lookup"><span data-stu-id="5dabc-132">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="5dabc-133">O modelo de mensagem (cadeia de caracteres de formato nomeada).</span><span class="sxs-lookup"><span data-stu-id="5dabc-133">The message template (named format string).</span></span> 

<span data-ttu-id="5dabc-134">Uma solicitação para a página de Índice do aplicativo de exemplo define:</span><span class="sxs-lookup"><span data-stu-id="5dabc-134">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="5dabc-135">O nível de log como `Information`.</span><span class="sxs-lookup"><span data-stu-id="5dabc-135">Log level to `Information`.</span></span>
* <span data-ttu-id="5dabc-136">A ID do evento como `1` com o nome do método `IndexPageRequested`.</span><span class="sxs-lookup"><span data-stu-id="5dabc-136">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="5dabc-137">Modelo de mensagem (cadeia de caracteres de formato nomeada) como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="5dabc-137">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="5dabc-138">Repositórios de log estruturado podem usar o nome do evento quando recebem a ID do evento para enriquecer o log.</span><span class="sxs-lookup"><span data-stu-id="5dabc-138">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="5dabc-139">Por exemplo, [Serilog](https://github.com/serilog/serilog-extensions-logging) usa o nome do evento.</span><span class="sxs-lookup"><span data-stu-id="5dabc-139">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="5dabc-140">A <xref:System.Action> é invocada por meio de um método de extensão fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-140">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="5dabc-141">O método `IndexPageRequested` registra uma mensagem para uma solicitação GET da página de Índice no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="5dabc-141">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="5dabc-142">`IndexPageRequested` é chamado no agente no método `OnGetAsync` em *Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5dabc-142">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="5dabc-143">Inspecione a saída do console do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5dabc-143">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="5dabc-144">Para passar parâmetros para uma mensagem de log, defina até seis tipos ao criar o campo estático.</span><span class="sxs-lookup"><span data-stu-id="5dabc-144">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="5dabc-145">O aplicativo de exemplo registra uma cadeia de caracteres em log ao adicionar aspas definindo um tipo `string` para o campo <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="5dabc-145">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="5dabc-146">O modelo de mensagem de log do delegado recebe seus valores de espaço reservado dos tipos fornecidos.</span><span class="sxs-lookup"><span data-stu-id="5dabc-146">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="5dabc-147">O aplicativo de exemplo define um delegado para adicionar aspas quando o parâmetro de aspas é uma `string`:</span><span class="sxs-lookup"><span data-stu-id="5dabc-147">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="5dabc-148">O método de extensão estático para adicionar aspas, `QuoteAdded`, recebe o valor do argumento de aspas e passa-o para o delegado <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="5dabc-148">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="5dabc-149">No modelo de página da página de índice (*pages/index. cshtml. cs*), `QuoteAdded` é chamado para registrar a mensagem:</span><span class="sxs-lookup"><span data-stu-id="5dabc-149">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="5dabc-150">Inspecione a saída do console do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5dabc-150">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="5dabc-151">O aplicativo de exemplo implementa um padrão [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) para exclusão de cotação.</span><span class="sxs-lookup"><span data-stu-id="5dabc-151">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="5dabc-152">Uma mensagem informativa é registrada em log para uma operação de exclusão bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="5dabc-152">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="5dabc-153">Uma mensagem de erro é registrada em log para uma operação de exclusão quando uma exceção é gerada.</span><span class="sxs-lookup"><span data-stu-id="5dabc-153">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="5dabc-154">A mensagem de log para a operação de exclusão sem êxito inclui o rastreamento de pilha da exceção (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="5dabc-154">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="5dabc-155">Observe como a exceção é passada para o delegado em `QuoteDeleteFailed`:</span><span class="sxs-lookup"><span data-stu-id="5dabc-155">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="5dabc-156">No modelo da página de Índice, uma exclusão de aspas bem-sucedida chama o método `QuoteDeleted` no agente.</span><span class="sxs-lookup"><span data-stu-id="5dabc-156">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="5dabc-157">Quando as aspas não são encontradas para exclusão, uma <xref:System.ArgumentNullException> é gerada.</span><span class="sxs-lookup"><span data-stu-id="5dabc-157">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="5dabc-158">A exceção é interceptada pela instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) e registrada chamando o `QuoteDeleteFailed` método no agente de log no bloco [Catch](/dotnet/csharp/language-reference/keywords/try-catch) (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="5dabc-158">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="5dabc-159">Quando as aspas forem excluídas com êxito, inspecione a saída do console do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5dabc-159">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="5dabc-160">Quando a exclusão de aspas falha, inspecione a saída do console do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5dabc-160">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="5dabc-161">Observe que a exceção é incluída na mensagem de log:</span><span class="sxs-lookup"><span data-stu-id="5dabc-161">Note that the exception is included in the log message:</span></span>

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="5dabc-162">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="5dabc-162">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="5dabc-163">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) cria um delegado <xref:System.Func%601> para definir um [escopo de log](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="5dabc-163">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="5dabc-164">Sobrecargas de <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> permitem passar até três parâmetros de tipo para uma cadeia de caracteres de formato nomeada (modelo).</span><span class="sxs-lookup"><span data-stu-id="5dabc-164"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="5dabc-165">Como é o caso com o método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, a cadeia de caracteres fornecida ao método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> é um modelo e não uma cadeia de caracteres interpolada.</span><span class="sxs-lookup"><span data-stu-id="5dabc-165">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="5dabc-166">Os espaços reservados são preenchidos na ordem em que os tipos são especificados.</span><span class="sxs-lookup"><span data-stu-id="5dabc-166">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="5dabc-167">Os nomes do espaço reservado no modelo devem ser descritivos e consistentes em todos os modelos.</span><span class="sxs-lookup"><span data-stu-id="5dabc-167">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="5dabc-168">Eles servem como nomes de propriedade em dados de log estruturado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-168">They serve as property names within structured log data.</span></span> <span data-ttu-id="5dabc-169">Recomendamos o uso da [formatação Pascal Case](/dotnet/standard/design-guidelines/capitalization-conventions) para nomes de espaço reservado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-169">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="5dabc-170">Por exemplo, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="5dabc-170">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="5dabc-171">Defina um [escopo de log](xref:fundamentals/logging/index#log-scopes) a ser aplicado a uma série de mensagens de log usando o método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.</span><span class="sxs-lookup"><span data-stu-id="5dabc-171">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="5dabc-172">O aplicativo de exemplo tem um botão **Limpar Tudo** para excluir todas as aspas no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5dabc-172">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="5dabc-173">As aspas são excluídas com a remoção das aspas individualmente, uma por vez.</span><span class="sxs-lookup"><span data-stu-id="5dabc-173">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="5dabc-174">Sempre que aspas são excluídas, o método `QuoteDeleted` é chamado no agente.</span><span class="sxs-lookup"><span data-stu-id="5dabc-174">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="5dabc-175">Um escopo de log é adicionado a essas mensagens de log.</span><span class="sxs-lookup"><span data-stu-id="5dabc-175">A log scope is added to these log messages.</span></span>

<span data-ttu-id="5dabc-176">Habilite `IncludeScopes` na seção de agente do console de *appSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5dabc-176">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="5dabc-177">Para criar um escopo de log, adicione um campo para conter um delegado <xref:System.Func%601> para o escopo.</span><span class="sxs-lookup"><span data-stu-id="5dabc-177">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="5dabc-178">O aplicativo de exemplo cria um campo chamado `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="5dabc-178">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="5dabc-179">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> para criar o delegado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-179">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="5dabc-180">Até três tipos podem ser especificados para uso como argumentos de modelo quando o delegado é invocado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-180">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="5dabc-181">O aplicativo de exemplo usa um modelo de mensagem que inclui o número de aspas excluídas (um tipo `int`):</span><span class="sxs-lookup"><span data-stu-id="5dabc-181">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="5dabc-182">Forneça um método de extensão estático para a mensagem de log.</span><span class="sxs-lookup"><span data-stu-id="5dabc-182">Provide a static extension method for the log message.</span></span> <span data-ttu-id="5dabc-183">Inclua os parâmetros de tipo para propriedades nomeadas exibidos no modelo de mensagem.</span><span class="sxs-lookup"><span data-stu-id="5dabc-183">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="5dabc-184">O aplicativo de exemplo usa uma `count` de aspas a ser excluída e retorna `_allQuotesDeletedScope`:</span><span class="sxs-lookup"><span data-stu-id="5dabc-184">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="5dabc-185">O escopo encapsula as chamadas de extensão de log em um bloco [using](/dotnet/csharp/language-reference/keywords/using-statement):</span><span class="sxs-lookup"><span data-stu-id="5dabc-185">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="5dabc-186">Inspecione as mensagens de log na saída do console do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5dabc-186">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="5dabc-187">O seguinte resultado mostra três aspas excluídas com a mensagem de escopo de log incluída:</span><span class="sxs-lookup"><span data-stu-id="5dabc-187">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5dabc-188">Os recursos do <xref:Microsoft.Extensions.Logging.LoggerMessage> criam delegados armazenáveis em cache que exigem menos alocações de objeto e sobrecarga de computação reduzida em comparação aos [métodos de extensão do agente](xref:Microsoft.Extensions.Logging.LoggerExtensions), como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> e <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span><span class="sxs-lookup"><span data-stu-id="5dabc-188"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="5dabc-189">Para cenários de registro em log de alto desempenho, use o padrão <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="5dabc-189">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="5dabc-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> fornece as seguintes vantagens de desempenho em relação aos métodos de extensão do Agente:</span><span class="sxs-lookup"><span data-stu-id="5dabc-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="5dabc-191">Métodos de extensão do agente exigem tipos de valor de conversão boxing, como `int`, em `object`.</span><span class="sxs-lookup"><span data-stu-id="5dabc-191">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="5dabc-192">O padrão <xref:Microsoft.Extensions.Logging.LoggerMessage> evita a conversão boxing usando campos <xref:System.Action> estáticos e métodos de extensão com parâmetros fortemente tipados.</span><span class="sxs-lookup"><span data-stu-id="5dabc-192">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="5dabc-193">Os métodos de extensão do agente precisam analisar o modelo de mensagem (cadeia de caracteres de formato nomeada) sempre que uma mensagem de log é gravada.</span><span class="sxs-lookup"><span data-stu-id="5dabc-193">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="5dabc-194"><xref:Microsoft.Extensions.Logging.LoggerMessage> exige apenas a análise de um modelo uma vez quando a mensagem é definida.</span><span class="sxs-lookup"><span data-stu-id="5dabc-194"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="5dabc-195">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5dabc-195">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5dabc-196">O aplicativo de exemplo demonstra recursos do <xref:Microsoft.Extensions.Logging.LoggerMessage> com um sistema básico de acompanhamento de aspas.</span><span class="sxs-lookup"><span data-stu-id="5dabc-196">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="5dabc-197">O aplicativo adiciona e exclui aspas usando um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="5dabc-197">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="5dabc-198">Conforme ocorrem essas operações, são geradas mensagens de log usando o padrão <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="5dabc-198">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="5dabc-199">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="5dabc-199">LoggerMessage.Define</span></span>

<span data-ttu-id="5dabc-200">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) cria um delegado <xref:System.Action> para registrar uma mensagem em log.</span><span class="sxs-lookup"><span data-stu-id="5dabc-200">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="5dabc-201">Sobrecargas de <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> permitem passar até seis parâmetros de tipo para uma cadeia de caracteres de formato nomeada (modelo).</span><span class="sxs-lookup"><span data-stu-id="5dabc-201"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="5dabc-202">A cadeia de caracteres fornecida para o método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> é um modelo e não uma cadeia de caracteres interpolada.</span><span class="sxs-lookup"><span data-stu-id="5dabc-202">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="5dabc-203">Os espaços reservados são preenchidos na ordem em que os tipos são especificados.</span><span class="sxs-lookup"><span data-stu-id="5dabc-203">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="5dabc-204">Os nomes do espaço reservado no modelo devem ser descritivos e consistentes em todos os modelos.</span><span class="sxs-lookup"><span data-stu-id="5dabc-204">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="5dabc-205">Eles servem como nomes de propriedade em dados de log estruturado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-205">They serve as property names within structured log data.</span></span> <span data-ttu-id="5dabc-206">Recomendamos o uso da [formatação Pascal Case](/dotnet/standard/design-guidelines/capitalization-conventions) para nomes de espaço reservado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-206">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="5dabc-207">Por exemplo, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="5dabc-207">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="5dabc-208">Cada mensagem de log é uma <xref:System.Action> mantida em um campo estático criado por [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="5dabc-208">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="5dabc-209">Por exemplo, o aplicativo de exemplo cria um campo para descrever uma mensagem de log para uma solicitação GET para a página de Índice (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="5dabc-209">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="5dabc-210">Para a <xref:System.Action>, especifique:</span><span class="sxs-lookup"><span data-stu-id="5dabc-210">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="5dabc-211">O nível de log.</span><span class="sxs-lookup"><span data-stu-id="5dabc-211">The log level.</span></span>
* <span data-ttu-id="5dabc-212">Um identificador de evento exclusivo (<xref:Microsoft.Extensions.Logging.EventId>) com o nome do método de extensão estático.</span><span class="sxs-lookup"><span data-stu-id="5dabc-212">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="5dabc-213">O modelo de mensagem (cadeia de caracteres de formato nomeada).</span><span class="sxs-lookup"><span data-stu-id="5dabc-213">The message template (named format string).</span></span> 

<span data-ttu-id="5dabc-214">Uma solicitação para a página de Índice do aplicativo de exemplo define:</span><span class="sxs-lookup"><span data-stu-id="5dabc-214">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="5dabc-215">O nível de log como `Information`.</span><span class="sxs-lookup"><span data-stu-id="5dabc-215">Log level to `Information`.</span></span>
* <span data-ttu-id="5dabc-216">A ID do evento como `1` com o nome do método `IndexPageRequested`.</span><span class="sxs-lookup"><span data-stu-id="5dabc-216">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="5dabc-217">Modelo de mensagem (cadeia de caracteres de formato nomeada) como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="5dabc-217">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="5dabc-218">Repositórios de log estruturado podem usar o nome do evento quando recebem a ID do evento para enriquecer o log.</span><span class="sxs-lookup"><span data-stu-id="5dabc-218">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="5dabc-219">Por exemplo, [Serilog](https://github.com/serilog/serilog-extensions-logging) usa o nome do evento.</span><span class="sxs-lookup"><span data-stu-id="5dabc-219">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="5dabc-220">A <xref:System.Action> é invocada por meio de um método de extensão fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-220">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="5dabc-221">O método `IndexPageRequested` registra uma mensagem para uma solicitação GET da página de Índice no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="5dabc-221">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="5dabc-222">`IndexPageRequested` é chamado no agente no método `OnGetAsync` em *Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5dabc-222">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="5dabc-223">Inspecione a saída do console do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5dabc-223">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="5dabc-224">Para passar parâmetros para uma mensagem de log, defina até seis tipos ao criar o campo estático.</span><span class="sxs-lookup"><span data-stu-id="5dabc-224">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="5dabc-225">O aplicativo de exemplo registra uma cadeia de caracteres em log ao adicionar aspas definindo um tipo `string` para o campo <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="5dabc-225">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="5dabc-226">O modelo de mensagem de log do delegado recebe seus valores de espaço reservado dos tipos fornecidos.</span><span class="sxs-lookup"><span data-stu-id="5dabc-226">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="5dabc-227">O aplicativo de exemplo define um delegado para adicionar aspas quando o parâmetro de aspas é uma `string`:</span><span class="sxs-lookup"><span data-stu-id="5dabc-227">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="5dabc-228">O método de extensão estático para adicionar aspas, `QuoteAdded`, recebe o valor do argumento de aspas e passa-o para o delegado <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="5dabc-228">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="5dabc-229">No modelo de página da página de índice (*pages/index. cshtml. cs*), `QuoteAdded` é chamado para registrar a mensagem:</span><span class="sxs-lookup"><span data-stu-id="5dabc-229">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="5dabc-230">Inspecione a saída do console do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5dabc-230">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="5dabc-231">O aplicativo de exemplo implementa um padrão [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) para exclusão de cotação.</span><span class="sxs-lookup"><span data-stu-id="5dabc-231">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="5dabc-232">Uma mensagem informativa é registrada em log para uma operação de exclusão bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="5dabc-232">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="5dabc-233">Uma mensagem de erro é registrada em log para uma operação de exclusão quando uma exceção é gerada.</span><span class="sxs-lookup"><span data-stu-id="5dabc-233">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="5dabc-234">A mensagem de log para a operação de exclusão sem êxito inclui o rastreamento de pilha da exceção (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="5dabc-234">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="5dabc-235">Observe como a exceção é passada para o delegado em `QuoteDeleteFailed`:</span><span class="sxs-lookup"><span data-stu-id="5dabc-235">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="5dabc-236">No modelo da página de Índice, uma exclusão de aspas bem-sucedida chama o método `QuoteDeleted` no agente.</span><span class="sxs-lookup"><span data-stu-id="5dabc-236">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="5dabc-237">Quando as aspas não são encontradas para exclusão, uma <xref:System.ArgumentNullException> é gerada.</span><span class="sxs-lookup"><span data-stu-id="5dabc-237">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="5dabc-238">A exceção é interceptada pela instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) e registrada chamando o `QuoteDeleteFailed` método no agente de log no bloco [Catch](/dotnet/csharp/language-reference/keywords/try-catch) (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="5dabc-238">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="5dabc-239">Quando as aspas forem excluídas com êxito, inspecione a saída do console do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5dabc-239">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="5dabc-240">Quando a exclusão de aspas falha, inspecione a saída do console do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5dabc-240">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="5dabc-241">Observe que a exceção é incluída na mensagem de log:</span><span class="sxs-lookup"><span data-stu-id="5dabc-241">Note that the exception is included in the log message:</span></span>

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="5dabc-242">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="5dabc-242">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="5dabc-243">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) cria um delegado <xref:System.Func%601> para definir um [escopo de log](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="5dabc-243">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="5dabc-244">Sobrecargas de <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> permitem passar até três parâmetros de tipo para uma cadeia de caracteres de formato nomeada (modelo).</span><span class="sxs-lookup"><span data-stu-id="5dabc-244"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="5dabc-245">Como é o caso com o método <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, a cadeia de caracteres fornecida ao método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> é um modelo e não uma cadeia de caracteres interpolada.</span><span class="sxs-lookup"><span data-stu-id="5dabc-245">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="5dabc-246">Os espaços reservados são preenchidos na ordem em que os tipos são especificados.</span><span class="sxs-lookup"><span data-stu-id="5dabc-246">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="5dabc-247">Os nomes do espaço reservado no modelo devem ser descritivos e consistentes em todos os modelos.</span><span class="sxs-lookup"><span data-stu-id="5dabc-247">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="5dabc-248">Eles servem como nomes de propriedade em dados de log estruturado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-248">They serve as property names within structured log data.</span></span> <span data-ttu-id="5dabc-249">Recomendamos o uso da [formatação Pascal Case](/dotnet/standard/design-guidelines/capitalization-conventions) para nomes de espaço reservado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-249">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="5dabc-250">Por exemplo, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="5dabc-250">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="5dabc-251">Defina um [escopo de log](xref:fundamentals/logging/index#log-scopes) a ser aplicado a uma série de mensagens de log usando o método <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.</span><span class="sxs-lookup"><span data-stu-id="5dabc-251">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="5dabc-252">O aplicativo de exemplo tem um botão **Limpar Tudo** para excluir todas as aspas no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5dabc-252">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="5dabc-253">As aspas são excluídas com a remoção das aspas individualmente, uma por vez.</span><span class="sxs-lookup"><span data-stu-id="5dabc-253">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="5dabc-254">Sempre que aspas são excluídas, o método `QuoteDeleted` é chamado no agente.</span><span class="sxs-lookup"><span data-stu-id="5dabc-254">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="5dabc-255">Um escopo de log é adicionado a essas mensagens de log.</span><span class="sxs-lookup"><span data-stu-id="5dabc-255">A log scope is added to these log messages.</span></span>

<span data-ttu-id="5dabc-256">Habilite `IncludeScopes` na seção de agente do console de *appSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5dabc-256">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="5dabc-257">Para criar um escopo de log, adicione um campo para conter um delegado <xref:System.Func%601> para o escopo.</span><span class="sxs-lookup"><span data-stu-id="5dabc-257">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="5dabc-258">O aplicativo de exemplo cria um campo chamado `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="5dabc-258">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="5dabc-259">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> para criar o delegado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-259">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="5dabc-260">Até três tipos podem ser especificados para uso como argumentos de modelo quando o delegado é invocado.</span><span class="sxs-lookup"><span data-stu-id="5dabc-260">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="5dabc-261">O aplicativo de exemplo usa um modelo de mensagem que inclui o número de aspas excluídas (um tipo `int`):</span><span class="sxs-lookup"><span data-stu-id="5dabc-261">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="5dabc-262">Forneça um método de extensão estático para a mensagem de log.</span><span class="sxs-lookup"><span data-stu-id="5dabc-262">Provide a static extension method for the log message.</span></span> <span data-ttu-id="5dabc-263">Inclua os parâmetros de tipo para propriedades nomeadas exibidos no modelo de mensagem.</span><span class="sxs-lookup"><span data-stu-id="5dabc-263">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="5dabc-264">O aplicativo de exemplo usa uma `count` de aspas a ser excluída e retorna `_allQuotesDeletedScope`:</span><span class="sxs-lookup"><span data-stu-id="5dabc-264">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="5dabc-265">O escopo encapsula as chamadas de extensão de log em um bloco [using](/dotnet/csharp/language-reference/keywords/using-statement):</span><span class="sxs-lookup"><span data-stu-id="5dabc-265">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="5dabc-266">Inspecione as mensagens de log na saída do console do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5dabc-266">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="5dabc-267">O seguinte resultado mostra três aspas excluídas com a mensagem de escopo de log incluída:</span><span class="sxs-lookup"><span data-stu-id="5dabc-267">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5dabc-268">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5dabc-268">Additional resources</span></span>

* [<span data-ttu-id="5dabc-269">Registro em log</span><span class="sxs-lookup"><span data-stu-id="5dabc-269">Logging</span></span>](xref:fundamentals/logging/index)

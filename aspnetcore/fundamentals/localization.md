---
<span data-ttu-id="57891-101">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-102">'Blazor'</span></span>
- <span data-ttu-id="57891-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-103">'Identity'</span></span>
- <span data-ttu-id="57891-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-105">'Razor'</span></span>
- <span data-ttu-id="57891-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-106">'SignalR' uid:</span></span> 

---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="57891-107">Globalização e localização no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57891-107">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="57891-108">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) e [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="57891-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="57891-109">Um site multilíngue permite que o site alcance um público mais amplo.</span><span class="sxs-lookup"><span data-stu-id="57891-109">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="57891-110">O ASP.NET Core fornece serviços e middleware para localização em diferentes idiomas e culturas.</span><span class="sxs-lookup"><span data-stu-id="57891-110">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="57891-111">A internacionalização envolve [Globalização](/dotnet/api/system.globalization) e [Localização](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="57891-111">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="57891-112">Globalização é o processo de criação de aplicativos que dão suporte a diferentes culturas.</span><span class="sxs-lookup"><span data-stu-id="57891-112">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="57891-113">A globalização adiciona suporte para entrada, exibição e saída de um conjunto definido de scripts de idiomas relacionados a áreas geográficas específicas.</span><span class="sxs-lookup"><span data-stu-id="57891-113">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="57891-114">Localização é o processo de adaptar um aplicativo globalizado, que você já processou para possibilidade de localização, a determinada cultura/localidade.</span><span class="sxs-lookup"><span data-stu-id="57891-114">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="57891-115">Para obter mais informações, consulte **Termos de globalização e localização** próximo ao final deste documento.</span><span class="sxs-lookup"><span data-stu-id="57891-115">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="57891-116">A localização de aplicativos envolve o seguinte:</span><span class="sxs-lookup"><span data-stu-id="57891-116">App localization involves the following:</span></span>

1. <span data-ttu-id="57891-117">Tornar o conteúdo do aplicativo localizável</span><span class="sxs-lookup"><span data-stu-id="57891-117">Make the app's content localizable</span></span>
1. <span data-ttu-id="57891-118">Fornecer recursos localizados para as culturas e os idiomas aos quais você dá suporte</span><span class="sxs-lookup"><span data-stu-id="57891-118">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="57891-119">Implementar uma estratégia para selecionar o idioma e a cultura para cada solicitação</span><span class="sxs-lookup"><span data-stu-id="57891-119">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="57891-120">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57891-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="57891-121">Tornar o conteúdo do aplicativo localizável</span><span class="sxs-lookup"><span data-stu-id="57891-121">Make the app's content localizable</span></span>

<span data-ttu-id="57891-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer ' não exige o armazenamento das cadeias de caracteres de idioma padrão em um arquivo de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="57891-123">Você pode desenvolver um aplicativo direcionado à localização e não precisa criar arquivos de recurso no início do desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="57891-123">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="57891-124">O código abaixo mostra como encapsular a cadeia de caracteres "About Title" para localização.</span><span class="sxs-lookup"><span data-stu-id="57891-124">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="57891-125">No código anterior, a `IStringLocalizer<T>` implementação vem da [injeção de dependência](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="57891-125">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="57891-126">Se o valor localizado de "About Title" não é encontrado, a chave do indexador é retornada, ou seja, a cadeia de caracteres "About Title".</span><span class="sxs-lookup"><span data-stu-id="57891-126">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="57891-127">Deixe as cadeias de caracteres literais de idioma padrão no aplicativo e encapsule-as no localizador, de modo que você possa se concentrar no desenvolvimento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="57891-127">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="57891-128">Você pode desenvolve seu aplicativo com o idioma padrão e prepará-lo para a etapa de localização sem primeiro criar um arquivo de recurso padrão.</span><span class="sxs-lookup"><span data-stu-id="57891-128">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="57891-129">Como alternativa, você pode usar a abordagem tradicional e fornecer uma chave para recuperar a cadeia de caracteres de idioma padrão.</span><span class="sxs-lookup"><span data-stu-id="57891-129">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="57891-130">Para muitos desenvolvedores, o novo fluxo de trabalho de não ter um arquivo *.resx* de idioma padrão e simplesmente encapsular os literais de cadeia de caracteres pode reduzir a sobrecarga de localizar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="57891-130">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="57891-131">Outros desenvolvedores preferirão o fluxo de trabalho tradicional, pois ele pode facilitar o trabalho com literais de cadeia de caracteres mais longas e a atualização de cadeias de caracteres localizadas.</span><span class="sxs-lookup"><span data-stu-id="57891-131">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="57891-132">Use a implementação `IHtmlLocalizer<T>` para recursos que contêm HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-132">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="57891-133">O `IHtmlLocalizer` codifica em HTML os argumentos que são formatados na cadeia de caracteres de recurso, mas não codifica em HTML a cadeia de caracteres de recurso em si.</span><span class="sxs-lookup"><span data-stu-id="57891-133">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="57891-134">Na amostra realçada abaixo, apenas o valor do parâmetro `name` é codificado em HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-134">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="57891-135">**Observação:** em geral, recomendamos localizar somente o texto e não o HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-135">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="57891-136">No nível mais baixo, você pode obter `IStringLocalizerFactory` com a [Injeção de Dependência](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="57891-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="57891-137">O código acima demonstra cada um dos dois métodos Create de alocador.</span><span class="sxs-lookup"><span data-stu-id="57891-137">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="57891-138">Você pode particionar as cadeias de caracteres localizadas por controlador, área ou ter apenas um contêiner.</span><span class="sxs-lookup"><span data-stu-id="57891-138">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="57891-139">No aplicativo de exemplo, uma classe fictícia chamada `SharedResource` é usada para recursos compartilhados.</span><span class="sxs-lookup"><span data-stu-id="57891-139">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="57891-140">Alguns desenvolvedores usam a classe `Startup` para conter cadeias de caracteres globais ou compartilhadas.</span><span class="sxs-lookup"><span data-stu-id="57891-140">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="57891-141">Na amostra abaixo, os localizadores `InfoController` e `SharedResource` são usados:</span><span class="sxs-lookup"><span data-stu-id="57891-141">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="57891-142">Localização de exibição</span><span class="sxs-lookup"><span data-stu-id="57891-142">View localization</span></span>

<span data-ttu-id="57891-143">O serviço `IViewLocalizer` fornece cadeias de caracteres localizadas para uma [exibição](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="57891-143">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="57891-144">A classe `ViewLocalizer` implementa essa interface e encontra o local do recurso no caminho do arquivo de exibição.</span><span class="sxs-lookup"><span data-stu-id="57891-144">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="57891-145">O seguinte código mostra como usar a implementação padrão de `IViewLocalizer`:</span><span class="sxs-lookup"><span data-stu-id="57891-145">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="57891-146">A implementação padrão de `IViewLocalizer` encontra o arquivo de recurso com base no nome de arquivo da exibição.</span><span class="sxs-lookup"><span data-stu-id="57891-146">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="57891-147">Não há nenhuma opção para usar um arquivo de recurso compartilhado global.</span><span class="sxs-lookup"><span data-stu-id="57891-147">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="57891-148">`ViewLocalizer`implementa o localizador usando `IHtmlLocalizer` , portanto, Razor não codifica a cadeia de caracteres localizada em HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-148">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="57891-149">Parametrize cadeias de recurso e o `IViewLocalizer` codificará em HTML os parâmetros, mas não a cadeia de caracteres de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-149">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="57891-150">Considere a seguinte Razor marcação:</span><span class="sxs-lookup"><span data-stu-id="57891-150">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="57891-151">Um arquivo de recurso em francês pode conter o seguinte:</span><span class="sxs-lookup"><span data-stu-id="57891-151">A French resource file could contain the following:</span></span>

| <span data-ttu-id="57891-152">Chave</span><span class="sxs-lookup"><span data-stu-id="57891-152">Key</span></span> | <span data-ttu-id="57891-153">Valor</span><span class="sxs-lookup"><span data-stu-id="57891-153">Value</span></span> |
| ----- | ---
<span data-ttu-id="57891-154">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-154">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-155">'Blazor'</span></span>
- <span data-ttu-id="57891-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-156">'Identity'</span></span>
- <span data-ttu-id="57891-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-158">'Razor'</span></span>
- <span data-ttu-id="57891-159">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-159">'SignalR' uid:</span></span> 

<span data-ttu-id="57891-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="57891-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="57891-161">A exibição renderizada contém a marcação HTML do arquivo de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-161">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="57891-162">**Observação:** em geral, recomendamos localizar somente o texto e não o HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-162">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="57891-163">Para usar um arquivo de recurso compartilhado em uma exibição, injete `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="57891-163">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="57891-164">Localização de DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="57891-164">DataAnnotations localization</span></span>

<span data-ttu-id="57891-165">As mensagens de erro de DataAnnotations são localizadas com `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="57891-165">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="57891-166">Usando a opção `ResourcesPath = "Resources"`, as mensagens de erro em `RegisterViewModel` podem ser armazenadas em um dos seguintes caminhos:</span><span class="sxs-lookup"><span data-stu-id="57891-166">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="57891-167">*Recursos/ViewModels. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="57891-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="57891-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="57891-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="57891-169">No ASP.NET Core MVC 1.1.0 e superior, atributos que não sejam de validação são localizados.</span><span class="sxs-lookup"><span data-stu-id="57891-169">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="57891-170">O ASP.NET Core MVC 1.0 **não** pesquisa cadeias de caracteres localizadas para atributos que não sejam de validação.</span><span class="sxs-lookup"><span data-stu-id="57891-170">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="57891-171">Usando uma cadeia de caracteres de recurso para várias classes</span><span class="sxs-lookup"><span data-stu-id="57891-171">Using one resource string for multiple classes</span></span>

<span data-ttu-id="57891-172">O seguinte código mostra como usar uma cadeia de caracteres de recurso para atributos de validação com várias classes:</span><span class="sxs-lookup"><span data-stu-id="57891-172">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="57891-173">No código anterior, `SharedResource` é a classe correspondente ao resx em que as mensagens de validação são armazenadas.</span><span class="sxs-lookup"><span data-stu-id="57891-173">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="57891-174">Com essa abordagem, DataAnnotations usará apenas `SharedResource`, em vez de o recurso para cada classe.</span><span class="sxs-lookup"><span data-stu-id="57891-174">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="57891-175">Fornecer recursos localizados para as culturas e os idiomas aos quais você dá suporte</span><span class="sxs-lookup"><span data-stu-id="57891-175">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="57891-176">SupportedCultures e SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="57891-176">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="57891-177">O ASP.NET Core permite que você especifique dois valores de cultura, `SupportedCultures` e `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="57891-177">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="57891-178">O objeto [CultureInfo](/dotnet/api/system.globalization.cultureinfo) para `SupportedCultures` determina os resultados das funções dependentes de cultura, como data, hora, número e formatação de moeda.</span><span class="sxs-lookup"><span data-stu-id="57891-178">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="57891-179">`SupportedCultures` também determina a ordem de classificação de texto, convenções de uso de maiúsculas e comparações de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="57891-179">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="57891-180">Consulte [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) para obter mais informações sobre como o servidor obtém a Cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-180">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="57891-181">O `SupportedUICultures` determina quais cadeias de caracteres traduzidas (de arquivos *. resx* ) são pesquisadas pelo [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="57891-181">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="57891-182">O `ResourceManager` apenas pesquisa cadeias de caracteres específicas a uma cultura determinadas por `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="57891-182">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="57891-183">Cada thread no .NET tem objetos `CurrentCulture` e `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="57891-183">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="57891-184">O ASP.NET Core inspeciona esses valores durante a renderização de funções dependentes de cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-184">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="57891-185">Por exemplo, se a cultura do thread atual estiver definida como "en-US" (inglês, Estados Unidos), `DateTime.Now.ToLongDateString()` exibirá "Thursday, February 18, 2016", mas se `CurrentCulture` estiver definida como "es-ES" (espanhol, Espanha), o resultado será "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="57891-185">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="57891-186">Arquivos de recurso</span><span class="sxs-lookup"><span data-stu-id="57891-186">Resource files</span></span>

<span data-ttu-id="57891-187">Um arquivo de recurso é um mecanismo útil para separar cadeias de caracteres localizáveis do código.</span><span class="sxs-lookup"><span data-stu-id="57891-187">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="57891-188">As cadeias de caracteres traduzidas para o idioma não padrão são isoladas em arquivos de recurso *. resx* .</span><span class="sxs-lookup"><span data-stu-id="57891-188">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="57891-189">Por exemplo, talvez você queira criar um arquivo de recurso em espanhol chamado *Welcome.es.resx* contendo cadeias de caracteres traduzidas.</span><span class="sxs-lookup"><span data-stu-id="57891-189">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="57891-190">"es" são o código de idioma para o espanhol.</span><span class="sxs-lookup"><span data-stu-id="57891-190">"es" is the language code for Spanish.</span></span> <span data-ttu-id="57891-191">Para criar esse arquivo de recurso no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="57891-191">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="57891-192">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta que conterá o arquivo de recurso > **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="57891-192">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Menu contextual aninhado: no Gerenciador de Soluções, um menu contextual é aberto para Recursos.](localization/_static/newi.png)

2. <span data-ttu-id="57891-195">Na caixa **Pesquisar modelos instalados**, insira "recurso" e nomeie o arquivo.</span><span class="sxs-lookup"><span data-stu-id="57891-195">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Caixa de diálogo Adicionar Novo Item](localization/_static/res.png)

3. <span data-ttu-id="57891-197">Insira o valor da chave (cadeia de caracteres nativa) na coluna **Nome** e a cadeia de caracteres traduzida na coluna **Valor**.</span><span class="sxs-lookup"><span data-stu-id="57891-197">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Arquivo Welcome.es.resx (o arquivo de recurso Welcome em espanhol) com a palavra Hello na coluna Nome e a palavra Hola (Olá, em espanhol) na coluna valor](localization/_static/hola.png)

    <span data-ttu-id="57891-199">O Visual Studio mostra o arquivo *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-199">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Gerenciador de Soluções mostrando o arquivo de recurso Welcome em espanhol (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="57891-201">Nomenclatura do arquivo de recurso</span><span class="sxs-lookup"><span data-stu-id="57891-201">Resource file naming</span></span>

<span data-ttu-id="57891-202">Os recursos são nomeados com o nome completo do tipo de sua classe menos o nome do assembly.</span><span class="sxs-lookup"><span data-stu-id="57891-202">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="57891-203">Por exemplo, um recurso em francês em um projeto cujo assembly principal é `LocalizationWebsite.Web.dll` para a classe `LocalizationWebsite.Web.Startup` será nomeado *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-203">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="57891-204">Um recurso para a classe `LocalizationWebsite.Web.Controllers.HomeController` será nomeado *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-204">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="57891-205">Se o namespace da classe de destino não for o mesmo que o nome do assembly, você precisará do nome completo do tipo.</span><span class="sxs-lookup"><span data-stu-id="57891-205">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="57891-206">Por exemplo, no projeto de exemplo, um recurso para o tipo `ExtraNamespace.Tools` será nomeado *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-206">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="57891-207">No projeto de exemplo, o método `ConfigureServices` define o `ResourcesPath` como "Resources", de modo que o caminho relativo do projeto para o arquivo de recurso em francês do controlador principal seja *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-207">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="57891-208">Como alternativa, você pode usar pastas para organizar arquivos de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-208">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="57891-209">Para o controlador principal, o caminho será *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-209">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="57891-210">Se você não usar a opção `ResourcesPath`, o arquivo *.resx* entrará no diretório base do projeto.</span><span class="sxs-lookup"><span data-stu-id="57891-210">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="57891-211">O arquivo de recurso para `HomeController` será nomeado *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-211">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="57891-212">A opção de usar a convenção de nomenclatura de ponto ou caminho depende de como você deseja organizar os arquivos de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-212">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="57891-213">Nome do recurso</span><span class="sxs-lookup"><span data-stu-id="57891-213">Resource name</span></span> | <span data-ttu-id="57891-214">Nomenclatura de ponto ou caminho</span><span class="sxs-lookup"><span data-stu-id="57891-214">Dot or path naming</span></span> |
| ---
<span data-ttu-id="57891-215">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-215">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-216">'Blazor'</span></span>
- <span data-ttu-id="57891-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-217">'Identity'</span></span>
- <span data-ttu-id="57891-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-219">'Razor'</span></span>
- <span data-ttu-id="57891-220">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-221">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-221">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-222">'Blazor'</span></span>
- <span data-ttu-id="57891-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-223">'Identity'</span></span>
- <span data-ttu-id="57891-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-225">'Razor'</span></span>
- <span data-ttu-id="57891-226">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-227">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-227">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-228">'Blazor'</span></span>
- <span data-ttu-id="57891-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-229">'Identity'</span></span>
- <span data-ttu-id="57891-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-231">'Razor'</span></span>
- <span data-ttu-id="57891-232">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-233">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-233">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-234">'Blazor'</span></span>
- <span data-ttu-id="57891-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-235">'Identity'</span></span>
- <span data-ttu-id="57891-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-237">'Razor'</span></span>
- <span data-ttu-id="57891-238">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-238">'SignalR' uid:</span></span> 

<span data-ttu-id="57891-239">------   | título do---: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-240">'Blazor'</span></span>
- <span data-ttu-id="57891-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-241">'Identity'</span></span>
- <span data-ttu-id="57891-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-243">'Razor'</span></span>
- <span data-ttu-id="57891-244">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-245">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-245">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-246">'Blazor'</span></span>
- <span data-ttu-id="57891-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-247">'Identity'</span></span>
- <span data-ttu-id="57891-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-249">'Razor'</span></span>
- <span data-ttu-id="57891-250">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-251">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-251">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-252">'Blazor'</span></span>
- <span data-ttu-id="57891-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-253">'Identity'</span></span>
- <span data-ttu-id="57891-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-255">'Razor'</span></span>
- <span data-ttu-id="57891-256">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-257">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-257">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-258">'Blazor'</span></span>
- <span data-ttu-id="57891-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-259">'Identity'</span></span>
- <span data-ttu-id="57891-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-261">'Razor'</span></span>
- <span data-ttu-id="57891-262">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-262">'SignalR' uid:</span></span> 

<span data-ttu-id="57891-263">------- | | Recursos/controladores. HomeController. fr. resx | Ponto | | Recursos/controladores/HomeController. fr. resx | Caminho | |    |     |</span><span class="sxs-lookup"><span data-stu-id="57891-263">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="57891-264">Os arquivos de recurso usando `@inject IViewLocalizer` em Razor exibições seguem um padrão semelhante.</span><span class="sxs-lookup"><span data-stu-id="57891-264">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="57891-265">O arquivo de recurso de uma exibição pode ser nomeado usando a nomenclatura de ponto ou de caminho.</span><span class="sxs-lookup"><span data-stu-id="57891-265">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="57891-266">exiba os arquivos de recurso que imitam o caminho do arquivo de exibição associado.</span><span class="sxs-lookup"><span data-stu-id="57891-266"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="57891-267">Supondo que definimos o `ResourcesPath` como "Resources", o arquivo de recurso em francês associado à exibição *Views/Home/About.cshtml* pode ser um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="57891-267">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="57891-268">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="57891-268">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="57891-269">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="57891-269">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="57891-270">Se você não usar a opção `ResourcesPath`, o arquivo *.resx* de uma exibição estará localizado na mesma pasta da exibição.</span><span class="sxs-lookup"><span data-stu-id="57891-270">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="57891-271">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="57891-271">RootNamespaceAttribute</span></span> 

<span data-ttu-id="57891-272">O atributo [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) fornece o namespace raiz de um assembly quando o namespace raiz de um assembly é diferente do nome do assembly.</span><span class="sxs-lookup"><span data-stu-id="57891-272">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="57891-273">Isso pode ocorrer quando o nome de um projeto não é um identificador .NET válido.</span><span class="sxs-lookup"><span data-stu-id="57891-273">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="57891-274">Por exemplo `my-project-name.csproj` , usará o namespace raiz `my_project_name` e o nome do assembly `my-project-name` que levam a esse erro.</span><span class="sxs-lookup"><span data-stu-id="57891-274">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="57891-275">Se o namespace raiz de um assembly é diferente do nome do assembly:</span><span class="sxs-lookup"><span data-stu-id="57891-275">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="57891-276">A localização não funciona por padrão.</span><span class="sxs-lookup"><span data-stu-id="57891-276">Localization does not work by default.</span></span>
* <span data-ttu-id="57891-277">A localização falha devido à maneira como os recursos são pesquisados dentro do assembly.</span><span class="sxs-lookup"><span data-stu-id="57891-277">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="57891-278">`RootNamespace` é um valor de tempo de build que não está disponível para o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="57891-278">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="57891-279">Se o `RootNamespace` é diferente de `AssemblyName`, inclua o seguinte no *AssemblyInfo.cs* (com valores de parâmetro substituídos pelos valores reais):</span><span class="sxs-lookup"><span data-stu-id="57891-279">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="57891-280">O código anterior permite a resolução bem-sucedida de arquivos resx.</span><span class="sxs-lookup"><span data-stu-id="57891-280">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="57891-281">Comportamento de fallback da cultura</span><span class="sxs-lookup"><span data-stu-id="57891-281">Culture fallback behavior</span></span>

<span data-ttu-id="57891-282">Ao procurar por um recurso, a localização participa do "fallback de cultura".</span><span class="sxs-lookup"><span data-stu-id="57891-282">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="57891-283">Começando com a cultura solicitada, se ela não for encontrada, ela será revertida para a cultura pai dessa cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-283">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="57891-284">Além disso, a propriedade [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) representa a cultura pai.</span><span class="sxs-lookup"><span data-stu-id="57891-284">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="57891-285">Isso normalmente (mas nem sempre) significa a remoção do significante do ISO.</span><span class="sxs-lookup"><span data-stu-id="57891-285">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="57891-286">Por exemplo, o dialeto do espanhol falado no México é "es-MX".</span><span class="sxs-lookup"><span data-stu-id="57891-286">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="57891-287">Ele tem o pai "es", ou seja, espanhol, não específico de nenhum país.</span><span class="sxs-lookup"><span data-stu-id="57891-287">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="57891-288">Imagine que seu site receba uma solicitação de um recurso “Boas-vindas" usando a cultura"fr-CA".</span><span class="sxs-lookup"><span data-stu-id="57891-288">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="57891-289">O sistema de localização procura os seguintes recursos, em ordem, e seleciona a primeira correspondência:</span><span class="sxs-lookup"><span data-stu-id="57891-289">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="57891-290">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="57891-290">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="57891-291">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="57891-291">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="57891-292">*Welcome.resx* (se o `NeutralResourcesLanguage` for "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="57891-292">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="57891-293">Por exemplo, se você remover o designador de cultura ".fr" e tiver a cultura definida como francês, o arquivo de recurso padrão será lido e as cadeias de caracteres serão localizadas.</span><span class="sxs-lookup"><span data-stu-id="57891-293">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="57891-294">O Gerenciador de Recursos designa um padrão ou um recurso de fallback para quando nenhuma opção atende à cultura solicitada.</span><span class="sxs-lookup"><span data-stu-id="57891-294">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="57891-295">Se você deseja apenas retornar a chave quando um recurso está ausente para a cultura solicitada, não deve ter um arquivo de recurso padrão.</span><span class="sxs-lookup"><span data-stu-id="57891-295">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="57891-296">Gerar arquivos de recurso com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57891-296">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="57891-297">Se você criar um arquivo de recurso no Visual Studio sem uma cultura no nome do arquivo (por exemplo, *Welcome.resx*), o Visual Studio criará uma classe do C# com uma propriedade para cada cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="57891-297">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="57891-298">Geralmente isso não é o desejado com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57891-298">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="57891-299">Normalmente você não tem um arquivo de recurso *.resx* padrão (um arquivo *.resx* sem o nome de cultura).</span><span class="sxs-lookup"><span data-stu-id="57891-299">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="57891-300">Sugerimos que você crie o arquivo *.resx* com um nome de cultura (por exemplo *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="57891-300">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="57891-301">Quando você criar um arquivo *.resx* com um nome de cultura, o Visual Studio não gerará o arquivo de classe.</span><span class="sxs-lookup"><span data-stu-id="57891-301">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="57891-302">Adicionar outras culturas</span><span class="sxs-lookup"><span data-stu-id="57891-302">Add other cultures</span></span>

<span data-ttu-id="57891-303">Cada combinação de idioma e cultura (que não seja o idioma padrão) exige um arquivo de recurso exclusivo.</span><span class="sxs-lookup"><span data-stu-id="57891-303">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="57891-304">Crie arquivos de recurso para diferentes culturas e localidades criando novos arquivos de recurso, nos quais os códigos ISO e de idioma fazem parte do nome do arquivo (por exemplo, **en-us**, **fr-ca** e **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="57891-304">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="57891-305">Esses códigos ISO são colocados entre o nome do arquivo e a extensão de arquivo *.resx*, como em *Welcome.es-MX.resx* (espanhol/México).</span><span class="sxs-lookup"><span data-stu-id="57891-305">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="57891-306">Implementar uma estratégia para selecionar o idioma e a cultura para cada solicitação</span><span class="sxs-lookup"><span data-stu-id="57891-306">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="57891-307">Configurar a localização</span><span class="sxs-lookup"><span data-stu-id="57891-307">Configure localization</span></span>

<span data-ttu-id="57891-308">A localização é configurada no método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="57891-308">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="57891-309">`AddLocalization` Adiciona os serviços de localização ao contêiner de serviços.</span><span class="sxs-lookup"><span data-stu-id="57891-309">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="57891-310">O código acima também define o caminho de recursos como "Resources".</span><span class="sxs-lookup"><span data-stu-id="57891-310">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="57891-311">`AddViewLocalization` Adiciona suporte para os arquivos de exibição localizados.</span><span class="sxs-lookup"><span data-stu-id="57891-311">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="57891-312">Nesta amostra, a localização de exibição se baseia no sufixo do arquivo de exibição.</span><span class="sxs-lookup"><span data-stu-id="57891-312">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="57891-313">Por exemplo, "fr" no arquivo *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="57891-313">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="57891-314">`AddDataAnnotationsLocalization` Adiciona suporte para as mensagens de validação `DataAnnotations` localizadas por meio de abstrações `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="57891-314">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="57891-315">Middleware de localização</span><span class="sxs-lookup"><span data-stu-id="57891-315">Localization middleware</span></span>

<span data-ttu-id="57891-316">A cultura atual em uma solicitação é definida no [Middleware](xref:fundamentals/middleware/index) de localização.</span><span class="sxs-lookup"><span data-stu-id="57891-316">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="57891-317">O middleware de localização é habilitado no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="57891-317">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="57891-318">O middleware de localização precisa ser configurado antes de qualquer middleware que possa verificar a cultura de solicitação (por exemplo, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="57891-318">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="57891-319">`UseRequestLocalization` inicializa um objeto `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="57891-319">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="57891-320">Em cada solicitação, a lista de `RequestCultureProvider` em `RequestLocalizationOptions` é enumerada e o primeiro provedor que pode determinar com êxito a cultura de solicitação é usado.</span><span class="sxs-lookup"><span data-stu-id="57891-320">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="57891-321">Os provedores padrão são obtidos da classe `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="57891-321">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="57891-322">A lista padrão é apresentada do mais específico ao menos específico.</span><span class="sxs-lookup"><span data-stu-id="57891-322">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="57891-323">Mais adiante neste artigo, veremos como você pode alterar a ordem e até mesmo adicionar um provedor de cultura personalizado.</span><span class="sxs-lookup"><span data-stu-id="57891-323">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="57891-324">Se nenhum dos provedores pode determinar a cultura de solicitação, o `DefaultRequestCulture` é usado.</span><span class="sxs-lookup"><span data-stu-id="57891-324">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="57891-325">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="57891-325">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="57891-326">Alguns aplicativos usarão uma cadeia de caracteres de consulta para definir a [cultura e a cultura da interface do usuário](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="57891-326">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="57891-327">Para aplicativos que usam a abordagem do cabeçalho Accept-Language ou do cookie, a adição de uma cadeia de caracteres de consulta à URL é útil para depurar e testar o código.</span><span class="sxs-lookup"><span data-stu-id="57891-327">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="57891-328">Por padrão, o `QueryStringRequestCultureProvider` é registrado como o primeiro provedor de localização na lista `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="57891-328">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="57891-329">Passe os parâmetros `culture` e `ui-culture` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="57891-329">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="57891-330">O seguinte exemplo define a cultura específica (idioma e região) como espanhol/México:</span><span class="sxs-lookup"><span data-stu-id="57891-330">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="57891-331">Se você passar somente uma das duas (`culture` ou `ui-culture`), o provedor da cadeia de caracteres de consulta definirá os dois valores usando aquela que foi passada.</span><span class="sxs-lookup"><span data-stu-id="57891-331">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="57891-332">Por exemplo, a definição apenas da cultura definirá a `Culture` e a `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="57891-332">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="57891-333">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="57891-333">CookieRequestCultureProvider</span></span>

<span data-ttu-id="57891-334">Em geral, aplicativos de produção fornecerão um mecanismo para definir a cultura com o cookie de cultura do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57891-334">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="57891-335">Use o método `MakeCookieValue` para criar um cookie.</span><span class="sxs-lookup"><span data-stu-id="57891-335">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="57891-336">O `CookieRequestCultureProvider` `DefaultCookieName` retorna o nome de cookie padrão usado para rastrear as informações de cultura preferencial do usuário.</span><span class="sxs-lookup"><span data-stu-id="57891-336">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="57891-337">O nome padrão do cookie é `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="57891-337">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="57891-338">O formato do cookie é `c=%LANGCODE%|uic=%LANGCODE%`, em que `c` é `Culture` e `uic` é `UICulture`, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="57891-338">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="57891-339">Se você especificar apenas as informações de cultura e a cultura da interface do usuário, a cultura especificada será usada para as informações de cultura e para a cultura da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="57891-339">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="57891-340">O cabeçalho HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="57891-340">The Accept-Language HTTP header</span></span>

<span data-ttu-id="57891-341">O [cabeçalho Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) é configurável na maioria dos navegadores e originalmente foi criado para especificar o idioma do usuário.</span><span class="sxs-lookup"><span data-stu-id="57891-341">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="57891-342">Essa configuração indica que o navegador foi definido para enviar ou herdou do sistema operacional subjacente.</span><span class="sxs-lookup"><span data-stu-id="57891-342">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="57891-343">O cabeçalho HTTP Accept-Language de uma solicitação do navegador não é uma maneira infalível de detectar o idioma preferencial do usuário (consulte [Definindo preferências de idioma em um navegador](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="57891-343">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="57891-344">Um aplicativo de produção deve incluir uma maneira para que um usuário personalize sua opção de cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-344">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="57891-345">Definir o cabeçalho HTTP Accept-Language no IE</span><span class="sxs-lookup"><span data-stu-id="57891-345">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="57891-346">No ícone de engrenagem, toque em **Opções da Internet**.</span><span class="sxs-lookup"><span data-stu-id="57891-346">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="57891-347">Toque em **Idiomas**.</span><span class="sxs-lookup"><span data-stu-id="57891-347">Tap **Languages**.</span></span>

    ![Opções da Internet](localization/_static/lang.png)

3. <span data-ttu-id="57891-349">Toque em **Definir Preferências de Idioma**.</span><span class="sxs-lookup"><span data-stu-id="57891-349">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="57891-350">Toque em **Adicionar um idioma**.</span><span class="sxs-lookup"><span data-stu-id="57891-350">Tap **Add a language**.</span></span>

5. <span data-ttu-id="57891-351">Adicione o idioma.</span><span class="sxs-lookup"><span data-stu-id="57891-351">Add the language.</span></span>

6. <span data-ttu-id="57891-352">Toque no idioma e, em seguida, em **Mover Para Cima**.</span><span class="sxs-lookup"><span data-stu-id="57891-352">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="57891-353">Usar um provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="57891-353">Use a custom provider</span></span>

<span data-ttu-id="57891-354">Suponha que você deseje permitir que os clientes armazenem seus idiomas e culturas nos bancos de dados.</span><span class="sxs-lookup"><span data-stu-id="57891-354">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="57891-355">Você pode escrever um provedor para pesquisar esses valores para o usuário.</span><span class="sxs-lookup"><span data-stu-id="57891-355">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="57891-356">O seguinte código mostra como adicionar um provedor personalizado:</span><span class="sxs-lookup"><span data-stu-id="57891-356">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="57891-357">Use `RequestLocalizationOptions` para adicionar ou remover provedores de localização.</span><span class="sxs-lookup"><span data-stu-id="57891-357">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="57891-358">Definir a cultura de forma programática</span><span class="sxs-lookup"><span data-stu-id="57891-358">Set the culture programmatically</span></span>

<span data-ttu-id="57891-359">Este projeto de exemplo **Localization.StarterWeb** no [GitHub](https://github.com/aspnet/entropy) contém a interface do usuário para definir a `Culture`.</span><span class="sxs-lookup"><span data-stu-id="57891-359">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="57891-360">O arquivo *Views/Shared/_SelectLanguagePartial.cshtml* permite que você selecione a cultura na lista de culturas compatíveis:</span><span class="sxs-lookup"><span data-stu-id="57891-360">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="57891-361">O arquivo *Views/Shared/_SelectLanguagePartial.cshtml* é adicionado à seção `footer` do arquivo de layout para que ele fique disponível para todos os exibições:</span><span class="sxs-lookup"><span data-stu-id="57891-361">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="57891-362">O método `SetLanguage` define o cookie de cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-362">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="57891-363">Não é possível conectar o *_SelectLanguagePartial.cshtml* ao código de exemplo para este projeto.</span><span class="sxs-lookup"><span data-stu-id="57891-363">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="57891-364">O projeto **Localization. StarterWeb** no [GitHub](https://github.com/aspnet/entropy) tem código para fluir `RequestLocalizationOptions` para um Razor parcial por meio do contêiner de [injeção de dependência](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="57891-364">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="57891-365">Dados de rota de associação de modelo e cadeias de caracteres de consulta</span><span class="sxs-lookup"><span data-stu-id="57891-365">Model binding route data and query strings</span></span>

<span data-ttu-id="57891-366">Consulte [comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="57891-366">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="57891-367">Termos de globalização e localização</span><span class="sxs-lookup"><span data-stu-id="57891-367">Globalization and localization terms</span></span>

<span data-ttu-id="57891-368">O processo de localização do aplicativo também exige uma compreensão básica dos conjuntos de caracteres relevantes geralmente usados no desenvolvimento moderno de software e uma compreensão dos problemas associados a eles.</span><span class="sxs-lookup"><span data-stu-id="57891-368">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="57891-369">Embora todos os computadores armazenem texto como números (códigos), diferentes sistemas armazenam o mesmo texto usando números diferentes.</span><span class="sxs-lookup"><span data-stu-id="57891-369">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="57891-370">O processo de localização se refere à tradução da interface do usuário do aplicativo para uma cultura/localidade específica.</span><span class="sxs-lookup"><span data-stu-id="57891-370">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="57891-371">[Possibilidade de localização](/dotnet/standard/globalization-localization/localizability-review) é um processo intermediário usado para verificar se um aplicativo globalizado está pronto para localização.</span><span class="sxs-lookup"><span data-stu-id="57891-371">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="57891-372">O formato [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) para o nome de cultura é `<languagecode2>-<country/regioncode2>`, em que `<languagecode2>` é o código de idioma e `<country/regioncode2>` é o código de subcultura.</span><span class="sxs-lookup"><span data-stu-id="57891-372">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="57891-373">Por exemplo, `es-CL` para Espanhol (Chile), `en-US` para inglês (Estados Unidos) e `en-AU` para inglês (Austrália).</span><span class="sxs-lookup"><span data-stu-id="57891-373">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="57891-374">O [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) é uma combinação de um código de cultura de duas letras minúsculas ISO 639 associado a um idioma e um código de subcultura de duas letras maiúsculas ISO 3166 associado a um país ou uma região.</span><span class="sxs-lookup"><span data-stu-id="57891-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="57891-375">Consulte [Nome da cultura de idioma](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="57891-375">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="57891-376">Muitas vezes, internacionalização é abreviada como "I18N".</span><span class="sxs-lookup"><span data-stu-id="57891-376">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="57891-377">A abreviação usa a primeira e última letras e o número de letras entre elas e, portanto, 18 significa o número de letras entre o primeiro "I" e o último "N".</span><span class="sxs-lookup"><span data-stu-id="57891-377">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="57891-378">O mesmo se aplica a Globalização (G11N) e Localização (L10N).</span><span class="sxs-lookup"><span data-stu-id="57891-378">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="57891-379">Termos:</span><span class="sxs-lookup"><span data-stu-id="57891-379">Terms:</span></span>

* <span data-ttu-id="57891-380">Globalização (G11N): o processo de fazer com que um aplicativo dê suporte a diferentes idiomas e regiões.</span><span class="sxs-lookup"><span data-stu-id="57891-380">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="57891-381">Localização (L10N): o processo de personalizar um aplicativo para determinado idioma e região.</span><span class="sxs-lookup"><span data-stu-id="57891-381">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="57891-382">Internacionalização (I18N): descreve a globalização e a localização.</span><span class="sxs-lookup"><span data-stu-id="57891-382">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="57891-383">Cultura: um idioma e, opcionalmente, uma região.</span><span class="sxs-lookup"><span data-stu-id="57891-383">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="57891-384">Cultura neutra: uma cultura que tem um idioma especificado, mas não uma região.</span><span class="sxs-lookup"><span data-stu-id="57891-384">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="57891-385">(por exemplo, "en", "es")</span><span class="sxs-lookup"><span data-stu-id="57891-385">(for example "en", "es")</span></span>
* <span data-ttu-id="57891-386">Cultura específica: uma cultura que tem um idioma e uma região especificados.</span><span class="sxs-lookup"><span data-stu-id="57891-386">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="57891-387">(por exemplo, "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="57891-387">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="57891-388">Cultura pai: a cultura neutra que contém uma cultura específica.</span><span class="sxs-lookup"><span data-stu-id="57891-388">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="57891-389">(por exemplo, "en" é a cultura pai de "en-US" e "en-GB")</span><span class="sxs-lookup"><span data-stu-id="57891-389">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="57891-390">Localidade: uma localidade é o mesmo que uma cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-390">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="57891-391">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="57891-391">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="57891-392">O [projeto Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) usado no artigo.</span><span class="sxs-lookup"><span data-stu-id="57891-392">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="57891-393">Globalizando e localizando aplicativos do .NET</span><span class="sxs-lookup"><span data-stu-id="57891-393">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="57891-394">Recursos em arquivos. resx</span><span class="sxs-lookup"><span data-stu-id="57891-394">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="57891-395">Kit de Ferramentas de Aplicativo Multilíngue da Microsoft</span><span class="sxs-lookup"><span data-stu-id="57891-395">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="57891-396">Localização e genéricos</span><span class="sxs-lookup"><span data-stu-id="57891-396">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="57891-397">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) e [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="57891-397">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="57891-398">Um site multilíngue permite que o site alcance um público mais amplo.</span><span class="sxs-lookup"><span data-stu-id="57891-398">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="57891-399">O ASP.NET Core fornece serviços e middleware para localização em diferentes idiomas e culturas.</span><span class="sxs-lookup"><span data-stu-id="57891-399">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="57891-400">A internacionalização envolve [Globalização](/dotnet/api/system.globalization) e [Localização](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="57891-400">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="57891-401">Globalização é o processo de criação de aplicativos que dão suporte a diferentes culturas.</span><span class="sxs-lookup"><span data-stu-id="57891-401">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="57891-402">A globalização adiciona suporte para entrada, exibição e saída de um conjunto definido de scripts de idiomas relacionados a áreas geográficas específicas.</span><span class="sxs-lookup"><span data-stu-id="57891-402">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="57891-403">Localização é o processo de adaptar um aplicativo globalizado, que você já processou para possibilidade de localização, a determinada cultura/localidade.</span><span class="sxs-lookup"><span data-stu-id="57891-403">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="57891-404">Para obter mais informações, consulte **Termos de globalização e localização** próximo ao final deste documento.</span><span class="sxs-lookup"><span data-stu-id="57891-404">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="57891-405">A localização de aplicativos envolve o seguinte:</span><span class="sxs-lookup"><span data-stu-id="57891-405">App localization involves the following:</span></span>

1. <span data-ttu-id="57891-406">Tornar o conteúdo do aplicativo localizável</span><span class="sxs-lookup"><span data-stu-id="57891-406">Make the app's content localizable</span></span>
1. <span data-ttu-id="57891-407">Fornecer recursos localizados para as culturas e os idiomas aos quais você dá suporte</span><span class="sxs-lookup"><span data-stu-id="57891-407">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="57891-408">Implementar uma estratégia para selecionar o idioma e a cultura para cada solicitação</span><span class="sxs-lookup"><span data-stu-id="57891-408">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="57891-409">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57891-409">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="57891-410">Tornar o conteúdo do aplicativo localizável</span><span class="sxs-lookup"><span data-stu-id="57891-410">Make the app's content localizable</span></span>

<span data-ttu-id="57891-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer ' não exige o armazenamento das cadeias de caracteres de idioma padrão em um arquivo de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="57891-412">Você pode desenvolver um aplicativo direcionado à localização e não precisa criar arquivos de recurso no início do desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="57891-412">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="57891-413">O código abaixo mostra como encapsular a cadeia de caracteres "About Title" para localização.</span><span class="sxs-lookup"><span data-stu-id="57891-413">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="57891-414">No código anterior, a `IStringLocalizer<T>` implementação vem da [injeção de dependência](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="57891-414">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="57891-415">Se o valor localizado de "About Title" não é encontrado, a chave do indexador é retornada, ou seja, a cadeia de caracteres "About Title".</span><span class="sxs-lookup"><span data-stu-id="57891-415">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="57891-416">Deixe as cadeias de caracteres literais de idioma padrão no aplicativo e encapsule-as no localizador, de modo que você possa se concentrar no desenvolvimento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="57891-416">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="57891-417">Você pode desenvolve seu aplicativo com o idioma padrão e prepará-lo para a etapa de localização sem primeiro criar um arquivo de recurso padrão.</span><span class="sxs-lookup"><span data-stu-id="57891-417">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="57891-418">Como alternativa, você pode usar a abordagem tradicional e fornecer uma chave para recuperar a cadeia de caracteres de idioma padrão.</span><span class="sxs-lookup"><span data-stu-id="57891-418">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="57891-419">Para muitos desenvolvedores, o novo fluxo de trabalho de não ter um arquivo *.resx* de idioma padrão e simplesmente encapsular os literais de cadeia de caracteres pode reduzir a sobrecarga de localizar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="57891-419">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="57891-420">Outros desenvolvedores preferirão o fluxo de trabalho tradicional, pois ele pode facilitar o trabalho com literais de cadeia de caracteres mais longas e a atualização de cadeias de caracteres localizadas.</span><span class="sxs-lookup"><span data-stu-id="57891-420">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="57891-421">Use a implementação `IHtmlLocalizer<T>` para recursos que contêm HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-421">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="57891-422">O `IHtmlLocalizer` codifica em HTML os argumentos que são formatados na cadeia de caracteres de recurso, mas não codifica em HTML a cadeia de caracteres de recurso em si.</span><span class="sxs-lookup"><span data-stu-id="57891-422">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="57891-423">Na amostra realçada abaixo, apenas o valor do parâmetro `name` é codificado em HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-423">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="57891-424">**Observação:** em geral, recomendamos localizar somente o texto e não o HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-424">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="57891-425">No nível mais baixo, você pode obter `IStringLocalizerFactory` com a [Injeção de Dependência](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="57891-425">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="57891-426">O código acima demonstra cada um dos dois métodos Create de alocador.</span><span class="sxs-lookup"><span data-stu-id="57891-426">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="57891-427">Você pode particionar as cadeias de caracteres localizadas por controlador, área ou ter apenas um contêiner.</span><span class="sxs-lookup"><span data-stu-id="57891-427">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="57891-428">No aplicativo de exemplo, uma classe fictícia chamada `SharedResource` é usada para recursos compartilhados.</span><span class="sxs-lookup"><span data-stu-id="57891-428">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="57891-429">Alguns desenvolvedores usam a classe `Startup` para conter cadeias de caracteres globais ou compartilhadas.</span><span class="sxs-lookup"><span data-stu-id="57891-429">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="57891-430">Na amostra abaixo, os localizadores `InfoController` e `SharedResource` são usados:</span><span class="sxs-lookup"><span data-stu-id="57891-430">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="57891-431">Localização de exibição</span><span class="sxs-lookup"><span data-stu-id="57891-431">View localization</span></span>

<span data-ttu-id="57891-432">O serviço `IViewLocalizer` fornece cadeias de caracteres localizadas para uma [exibição](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="57891-432">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="57891-433">A classe `ViewLocalizer` implementa essa interface e encontra o local do recurso no caminho do arquivo de exibição.</span><span class="sxs-lookup"><span data-stu-id="57891-433">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="57891-434">O seguinte código mostra como usar a implementação padrão de `IViewLocalizer`:</span><span class="sxs-lookup"><span data-stu-id="57891-434">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="57891-435">A implementação padrão de `IViewLocalizer` encontra o arquivo de recurso com base no nome de arquivo da exibição.</span><span class="sxs-lookup"><span data-stu-id="57891-435">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="57891-436">Não há nenhuma opção para usar um arquivo de recurso compartilhado global.</span><span class="sxs-lookup"><span data-stu-id="57891-436">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="57891-437">`ViewLocalizer`implementa o localizador usando `IHtmlLocalizer` , portanto, Razor não codifica a cadeia de caracteres localizada em HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-437">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="57891-438">Parametrize cadeias de recurso e o `IViewLocalizer` codificará em HTML os parâmetros, mas não a cadeia de caracteres de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-438">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="57891-439">Considere a seguinte Razor marcação:</span><span class="sxs-lookup"><span data-stu-id="57891-439">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="57891-440">Um arquivo de recurso em francês pode conter o seguinte:</span><span class="sxs-lookup"><span data-stu-id="57891-440">A French resource file could contain the following:</span></span>

| <span data-ttu-id="57891-441">Chave</span><span class="sxs-lookup"><span data-stu-id="57891-441">Key</span></span> | <span data-ttu-id="57891-442">Valor</span><span class="sxs-lookup"><span data-stu-id="57891-442">Value</span></span> |
| ----- | ---
<span data-ttu-id="57891-443">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-443">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-444">'Blazor'</span></span>
- <span data-ttu-id="57891-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-445">'Identity'</span></span>
- <span data-ttu-id="57891-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-447">'Razor'</span></span>
- <span data-ttu-id="57891-448">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-448">'SignalR' uid:</span></span> 

<span data-ttu-id="57891-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="57891-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="57891-450">A exibição renderizada contém a marcação HTML do arquivo de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-450">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="57891-451">**Observação:** em geral, recomendamos localizar somente o texto e não o HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-451">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="57891-452">Para usar um arquivo de recurso compartilhado em uma exibição, injete `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="57891-452">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="57891-453">Localização de DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="57891-453">DataAnnotations localization</span></span>

<span data-ttu-id="57891-454">As mensagens de erro de DataAnnotations são localizadas com `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="57891-454">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="57891-455">Usando a opção `ResourcesPath = "Resources"`, as mensagens de erro em `RegisterViewModel` podem ser armazenadas em um dos seguintes caminhos:</span><span class="sxs-lookup"><span data-stu-id="57891-455">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="57891-456">*Recursos/ViewModels. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="57891-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="57891-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="57891-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="57891-458">No ASP.NET Core MVC 1.1.0 e superior, atributos que não sejam de validação são localizados.</span><span class="sxs-lookup"><span data-stu-id="57891-458">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="57891-459">O ASP.NET Core MVC 1.0 **não** pesquisa cadeias de caracteres localizadas para atributos que não sejam de validação.</span><span class="sxs-lookup"><span data-stu-id="57891-459">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="57891-460">Usando uma cadeia de caracteres de recurso para várias classes</span><span class="sxs-lookup"><span data-stu-id="57891-460">Using one resource string for multiple classes</span></span>

<span data-ttu-id="57891-461">O seguinte código mostra como usar uma cadeia de caracteres de recurso para atributos de validação com várias classes:</span><span class="sxs-lookup"><span data-stu-id="57891-461">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="57891-462">No código anterior, `SharedResource` é a classe correspondente ao resx em que as mensagens de validação são armazenadas.</span><span class="sxs-lookup"><span data-stu-id="57891-462">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="57891-463">Com essa abordagem, DataAnnotations usará apenas `SharedResource`, em vez de o recurso para cada classe.</span><span class="sxs-lookup"><span data-stu-id="57891-463">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="57891-464">Fornecer recursos localizados para as culturas e os idiomas aos quais você dá suporte</span><span class="sxs-lookup"><span data-stu-id="57891-464">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="57891-465">SupportedCultures e SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="57891-465">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="57891-466">O ASP.NET Core permite que você especifique dois valores de cultura, `SupportedCultures` e `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="57891-466">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="57891-467">O objeto [CultureInfo](/dotnet/api/system.globalization.cultureinfo) para `SupportedCultures` determina os resultados das funções dependentes de cultura, como data, hora, número e formatação de moeda.</span><span class="sxs-lookup"><span data-stu-id="57891-467">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="57891-468">`SupportedCultures` também determina a ordem de classificação de texto, convenções de uso de maiúsculas e comparações de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="57891-468">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="57891-469">Consulte [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) para obter mais informações sobre como o servidor obtém a Cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-469">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="57891-470">O `SupportedUICultures` determina quais cadeias de caracteres traduzidas (de arquivos *. resx* ) são pesquisadas pelo [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="57891-470">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="57891-471">O `ResourceManager` apenas pesquisa cadeias de caracteres específicas a uma cultura determinadas por `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="57891-471">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="57891-472">Cada thread no .NET tem objetos `CurrentCulture` e `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="57891-472">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="57891-473">O ASP.NET Core inspeciona esses valores durante a renderização de funções dependentes de cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-473">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="57891-474">Por exemplo, se a cultura do thread atual estiver definida como "en-US" (inglês, Estados Unidos), `DateTime.Now.ToLongDateString()` exibirá "Thursday, February 18, 2016", mas se `CurrentCulture` estiver definida como "es-ES" (espanhol, Espanha), o resultado será "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="57891-474">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="57891-475">Arquivos de recurso</span><span class="sxs-lookup"><span data-stu-id="57891-475">Resource files</span></span>

<span data-ttu-id="57891-476">Um arquivo de recurso é um mecanismo útil para separar cadeias de caracteres localizáveis do código.</span><span class="sxs-lookup"><span data-stu-id="57891-476">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="57891-477">As cadeias de caracteres traduzidas para o idioma não padrão são isoladas em arquivos de recurso *. resx* .</span><span class="sxs-lookup"><span data-stu-id="57891-477">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="57891-478">Por exemplo, talvez você queira criar um arquivo de recurso em espanhol chamado *Welcome.es.resx* contendo cadeias de caracteres traduzidas.</span><span class="sxs-lookup"><span data-stu-id="57891-478">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="57891-479">"es" são o código de idioma para o espanhol.</span><span class="sxs-lookup"><span data-stu-id="57891-479">"es" is the language code for Spanish.</span></span> <span data-ttu-id="57891-480">Para criar esse arquivo de recurso no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="57891-480">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="57891-481">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta que conterá o arquivo de recurso > **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="57891-481">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Menu contextual aninhado: no Gerenciador de Soluções, um menu contextual é aberto para Recursos.](localization/_static/newi.png)

2. <span data-ttu-id="57891-484">Na caixa **Pesquisar modelos instalados**, insira "recurso" e nomeie o arquivo.</span><span class="sxs-lookup"><span data-stu-id="57891-484">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Caixa de diálogo Adicionar Novo Item](localization/_static/res.png)

3. <span data-ttu-id="57891-486">Insira o valor da chave (cadeia de caracteres nativa) na coluna **Nome** e a cadeia de caracteres traduzida na coluna **Valor**.</span><span class="sxs-lookup"><span data-stu-id="57891-486">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Arquivo Welcome.es.resx (o arquivo de recurso Welcome em espanhol) com a palavra Hello na coluna Nome e a palavra Hola (Olá, em espanhol) na coluna valor](localization/_static/hola.png)

    <span data-ttu-id="57891-488">O Visual Studio mostra o arquivo *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-488">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Gerenciador de Soluções mostrando o arquivo de recurso Welcome em espanhol (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="57891-490">Nomenclatura do arquivo de recurso</span><span class="sxs-lookup"><span data-stu-id="57891-490">Resource file naming</span></span>

<span data-ttu-id="57891-491">Os recursos são nomeados com o nome completo do tipo de sua classe menos o nome do assembly.</span><span class="sxs-lookup"><span data-stu-id="57891-491">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="57891-492">Por exemplo, um recurso em francês em um projeto cujo assembly principal é `LocalizationWebsite.Web.dll` para a classe `LocalizationWebsite.Web.Startup` será nomeado *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-492">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="57891-493">Um recurso para a classe `LocalizationWebsite.Web.Controllers.HomeController` será nomeado *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-493">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="57891-494">Se o namespace da classe de destino não for o mesmo que o nome do assembly, você precisará do nome completo do tipo.</span><span class="sxs-lookup"><span data-stu-id="57891-494">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="57891-495">Por exemplo, no projeto de exemplo, um recurso para o tipo `ExtraNamespace.Tools` será nomeado *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-495">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="57891-496">No projeto de exemplo, o método `ConfigureServices` define o `ResourcesPath` como "Resources", de modo que o caminho relativo do projeto para o arquivo de recurso em francês do controlador principal seja *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-496">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="57891-497">Como alternativa, você pode usar pastas para organizar arquivos de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-497">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="57891-498">Para o controlador principal, o caminho será *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-498">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="57891-499">Se você não usar a opção `ResourcesPath`, o arquivo *.resx* entrará no diretório base do projeto.</span><span class="sxs-lookup"><span data-stu-id="57891-499">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="57891-500">O arquivo de recurso para `HomeController` será nomeado *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-500">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="57891-501">A opção de usar a convenção de nomenclatura de ponto ou caminho depende de como você deseja organizar os arquivos de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-501">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="57891-502">Nome do recurso</span><span class="sxs-lookup"><span data-stu-id="57891-502">Resource name</span></span> | <span data-ttu-id="57891-503">Nomenclatura de ponto ou caminho</span><span class="sxs-lookup"><span data-stu-id="57891-503">Dot or path naming</span></span> |
| ---
<span data-ttu-id="57891-504">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-504">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-505">'Blazor'</span></span>
- <span data-ttu-id="57891-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-506">'Identity'</span></span>
- <span data-ttu-id="57891-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-508">'Razor'</span></span>
- <span data-ttu-id="57891-509">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-510">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-510">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-511">'Blazor'</span></span>
- <span data-ttu-id="57891-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-512">'Identity'</span></span>
- <span data-ttu-id="57891-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-514">'Razor'</span></span>
- <span data-ttu-id="57891-515">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-516">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-516">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-517">'Blazor'</span></span>
- <span data-ttu-id="57891-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-518">'Identity'</span></span>
- <span data-ttu-id="57891-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-520">'Razor'</span></span>
- <span data-ttu-id="57891-521">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-522">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-522">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-523">'Blazor'</span></span>
- <span data-ttu-id="57891-524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-524">'Identity'</span></span>
- <span data-ttu-id="57891-525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-525">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-526">'Razor'</span></span>
- <span data-ttu-id="57891-527">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-527">'SignalR' uid:</span></span> 

<span data-ttu-id="57891-528">------   | título do---: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-529">'Blazor'</span></span>
- <span data-ttu-id="57891-530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-530">'Identity'</span></span>
- <span data-ttu-id="57891-531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-531">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-532">'Razor'</span></span>
- <span data-ttu-id="57891-533">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-533">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-534">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-534">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-535">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-535">'Blazor'</span></span>
- <span data-ttu-id="57891-536">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-536">'Identity'</span></span>
- <span data-ttu-id="57891-537">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-537">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-538">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-538">'Razor'</span></span>
- <span data-ttu-id="57891-539">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-539">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-540">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-540">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-541">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-541">'Blazor'</span></span>
- <span data-ttu-id="57891-542">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-542">'Identity'</span></span>
- <span data-ttu-id="57891-543">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-543">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-544">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-544">'Razor'</span></span>
- <span data-ttu-id="57891-545">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-545">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-546">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-546">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-547">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-547">'Blazor'</span></span>
- <span data-ttu-id="57891-548">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-548">'Identity'</span></span>
- <span data-ttu-id="57891-549">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-549">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-550">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-550">'Razor'</span></span>
- <span data-ttu-id="57891-551">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-551">'SignalR' uid:</span></span> 

<span data-ttu-id="57891-552">------- | | Recursos/controladores. HomeController. fr. resx | Ponto | | Recursos/controladores/HomeController. fr. resx | Caminho | |    |     |</span><span class="sxs-lookup"><span data-stu-id="57891-552">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="57891-553">Os arquivos de recurso usando `@inject IViewLocalizer` em Razor exibições seguem um padrão semelhante.</span><span class="sxs-lookup"><span data-stu-id="57891-553">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="57891-554">O arquivo de recurso de uma exibição pode ser nomeado usando a nomenclatura de ponto ou de caminho.</span><span class="sxs-lookup"><span data-stu-id="57891-554">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="57891-555">exiba os arquivos de recurso que imitam o caminho do arquivo de exibição associado.</span><span class="sxs-lookup"><span data-stu-id="57891-555"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="57891-556">Supondo que definimos o `ResourcesPath` como "Resources", o arquivo de recurso em francês associado à exibição *Views/Home/About.cshtml* pode ser um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="57891-556">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="57891-557">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="57891-557">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="57891-558">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="57891-558">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="57891-559">Se você não usar a opção `ResourcesPath`, o arquivo *.resx* de uma exibição estará localizado na mesma pasta da exibição.</span><span class="sxs-lookup"><span data-stu-id="57891-559">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="57891-560">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="57891-560">RootNamespaceAttribute</span></span> 

<span data-ttu-id="57891-561">O atributo [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) fornece o namespace raiz de um assembly quando o namespace raiz de um assembly é diferente do nome do assembly.</span><span class="sxs-lookup"><span data-stu-id="57891-561">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="57891-562">Isso pode ocorrer quando o nome de um projeto não é um identificador .NET válido.</span><span class="sxs-lookup"><span data-stu-id="57891-562">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="57891-563">Por exemplo `my-project-name.csproj` , usará o namespace raiz `my_project_name` e o nome do assembly `my-project-name` que levam a esse erro.</span><span class="sxs-lookup"><span data-stu-id="57891-563">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="57891-564">Se o namespace raiz de um assembly é diferente do nome do assembly:</span><span class="sxs-lookup"><span data-stu-id="57891-564">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="57891-565">A localização não funciona por padrão.</span><span class="sxs-lookup"><span data-stu-id="57891-565">Localization does not work by default.</span></span>
* <span data-ttu-id="57891-566">A localização falha devido à maneira como os recursos são pesquisados dentro do assembly.</span><span class="sxs-lookup"><span data-stu-id="57891-566">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="57891-567">`RootNamespace` é um valor de tempo de build que não está disponível para o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="57891-567">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="57891-568">Se o `RootNamespace` é diferente de `AssemblyName`, inclua o seguinte no *AssemblyInfo.cs* (com valores de parâmetro substituídos pelos valores reais):</span><span class="sxs-lookup"><span data-stu-id="57891-568">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="57891-569">O código anterior permite a resolução bem-sucedida de arquivos resx.</span><span class="sxs-lookup"><span data-stu-id="57891-569">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="57891-570">Comportamento de fallback da cultura</span><span class="sxs-lookup"><span data-stu-id="57891-570">Culture fallback behavior</span></span>

<span data-ttu-id="57891-571">Ao procurar por um recurso, a localização participa do "fallback de cultura".</span><span class="sxs-lookup"><span data-stu-id="57891-571">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="57891-572">Começando com a cultura solicitada, se ela não for encontrada, ela será revertida para a cultura pai dessa cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-572">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="57891-573">Além disso, a propriedade [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) representa a cultura pai.</span><span class="sxs-lookup"><span data-stu-id="57891-573">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="57891-574">Isso normalmente (mas nem sempre) significa a remoção do significante do ISO.</span><span class="sxs-lookup"><span data-stu-id="57891-574">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="57891-575">Por exemplo, o dialeto do espanhol falado no México é "es-MX".</span><span class="sxs-lookup"><span data-stu-id="57891-575">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="57891-576">Ele tem o pai "es", ou seja, espanhol, não específico de nenhum país.</span><span class="sxs-lookup"><span data-stu-id="57891-576">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="57891-577">Imagine que seu site receba uma solicitação de um recurso “Boas-vindas" usando a cultura"fr-CA".</span><span class="sxs-lookup"><span data-stu-id="57891-577">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="57891-578">O sistema de localização procura os seguintes recursos, em ordem, e seleciona a primeira correspondência:</span><span class="sxs-lookup"><span data-stu-id="57891-578">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="57891-579">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="57891-579">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="57891-580">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="57891-580">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="57891-581">*Welcome.resx* (se o `NeutralResourcesLanguage` for "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="57891-581">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="57891-582">Por exemplo, se você remover o designador de cultura ".fr" e tiver a cultura definida como francês, o arquivo de recurso padrão será lido e as cadeias de caracteres serão localizadas.</span><span class="sxs-lookup"><span data-stu-id="57891-582">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="57891-583">O Gerenciador de Recursos designa um padrão ou um recurso de fallback para quando nenhuma opção atende à cultura solicitada.</span><span class="sxs-lookup"><span data-stu-id="57891-583">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="57891-584">Se você deseja apenas retornar a chave quando um recurso está ausente para a cultura solicitada, não deve ter um arquivo de recurso padrão.</span><span class="sxs-lookup"><span data-stu-id="57891-584">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="57891-585">Gerar arquivos de recurso com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57891-585">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="57891-586">Se você criar um arquivo de recurso no Visual Studio sem uma cultura no nome do arquivo (por exemplo, *Welcome.resx*), o Visual Studio criará uma classe do C# com uma propriedade para cada cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="57891-586">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="57891-587">Geralmente isso não é o desejado com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57891-587">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="57891-588">Normalmente você não tem um arquivo de recurso *.resx* padrão (um arquivo *.resx* sem o nome de cultura).</span><span class="sxs-lookup"><span data-stu-id="57891-588">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="57891-589">Sugerimos que você crie o arquivo *.resx* com um nome de cultura (por exemplo *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="57891-589">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="57891-590">Quando você criar um arquivo *.resx* com um nome de cultura, o Visual Studio não gerará o arquivo de classe.</span><span class="sxs-lookup"><span data-stu-id="57891-590">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="57891-591">Adicionar outras culturas</span><span class="sxs-lookup"><span data-stu-id="57891-591">Add other cultures</span></span>

<span data-ttu-id="57891-592">Cada combinação de idioma e cultura (que não seja o idioma padrão) exige um arquivo de recurso exclusivo.</span><span class="sxs-lookup"><span data-stu-id="57891-592">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="57891-593">Crie arquivos de recurso para diferentes culturas e localidades criando novos arquivos de recurso, nos quais os códigos ISO e de idioma fazem parte do nome do arquivo (por exemplo, **en-us**, **fr-ca** e **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="57891-593">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="57891-594">Esses códigos ISO são colocados entre o nome do arquivo e a extensão de arquivo *.resx*, como em *Welcome.es-MX.resx* (espanhol/México).</span><span class="sxs-lookup"><span data-stu-id="57891-594">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="57891-595">Implementar uma estratégia para selecionar o idioma e a cultura para cada solicitação</span><span class="sxs-lookup"><span data-stu-id="57891-595">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="57891-596">Configurar a localização</span><span class="sxs-lookup"><span data-stu-id="57891-596">Configure localization</span></span>

<span data-ttu-id="57891-597">A localização é configurada no método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="57891-597">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="57891-598">`AddLocalization` Adiciona os serviços de localização ao contêiner de serviços.</span><span class="sxs-lookup"><span data-stu-id="57891-598">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="57891-599">O código acima também define o caminho de recursos como "Resources".</span><span class="sxs-lookup"><span data-stu-id="57891-599">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="57891-600">`AddViewLocalization` Adiciona suporte para os arquivos de exibição localizados.</span><span class="sxs-lookup"><span data-stu-id="57891-600">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="57891-601">Nesta amostra, a localização de exibição se baseia no sufixo do arquivo de exibição.</span><span class="sxs-lookup"><span data-stu-id="57891-601">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="57891-602">Por exemplo, "fr" no arquivo *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="57891-602">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="57891-603">`AddDataAnnotationsLocalization` Adiciona suporte para as mensagens de validação `DataAnnotations` localizadas por meio de abstrações `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="57891-603">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="57891-604">Middleware de localização</span><span class="sxs-lookup"><span data-stu-id="57891-604">Localization middleware</span></span>

<span data-ttu-id="57891-605">A cultura atual em uma solicitação é definida no [Middleware](xref:fundamentals/middleware/index) de localização.</span><span class="sxs-lookup"><span data-stu-id="57891-605">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="57891-606">O middleware de localização é habilitado no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="57891-606">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="57891-607">O middleware de localização precisa ser configurado antes de qualquer middleware que possa verificar a cultura de solicitação (por exemplo, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="57891-607">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="57891-608">`UseRequestLocalization` inicializa um objeto `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="57891-608">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="57891-609">Em cada solicitação, a lista de `RequestCultureProvider` em `RequestLocalizationOptions` é enumerada e o primeiro provedor que pode determinar com êxito a cultura de solicitação é usado.</span><span class="sxs-lookup"><span data-stu-id="57891-609">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="57891-610">Os provedores padrão são obtidos da classe `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="57891-610">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="57891-611">A lista padrão é apresentada do mais específico ao menos específico.</span><span class="sxs-lookup"><span data-stu-id="57891-611">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="57891-612">Mais adiante neste artigo, veremos como você pode alterar a ordem e até mesmo adicionar um provedor de cultura personalizado.</span><span class="sxs-lookup"><span data-stu-id="57891-612">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="57891-613">Se nenhum dos provedores pode determinar a cultura de solicitação, o `DefaultRequestCulture` é usado.</span><span class="sxs-lookup"><span data-stu-id="57891-613">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="57891-614">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="57891-614">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="57891-615">Alguns aplicativos usarão uma cadeia de caracteres de consulta para definir a [cultura e a cultura da interface do usuário](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="57891-615">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="57891-616">Para aplicativos que usam a abordagem do cabeçalho Accept-Language ou do cookie, a adição de uma cadeia de caracteres de consulta à URL é útil para depurar e testar o código.</span><span class="sxs-lookup"><span data-stu-id="57891-616">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="57891-617">Por padrão, o `QueryStringRequestCultureProvider` é registrado como o primeiro provedor de localização na lista `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="57891-617">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="57891-618">Passe os parâmetros `culture` e `ui-culture` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="57891-618">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="57891-619">O seguinte exemplo define a cultura específica (idioma e região) como espanhol/México:</span><span class="sxs-lookup"><span data-stu-id="57891-619">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="57891-620">Se você passar somente uma das duas (`culture` ou `ui-culture`), o provedor da cadeia de caracteres de consulta definirá os dois valores usando aquela que foi passada.</span><span class="sxs-lookup"><span data-stu-id="57891-620">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="57891-621">Por exemplo, a definição apenas da cultura definirá a `Culture` e a `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="57891-621">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="57891-622">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="57891-622">CookieRequestCultureProvider</span></span>

<span data-ttu-id="57891-623">Em geral, aplicativos de produção fornecerão um mecanismo para definir a cultura com o cookie de cultura do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57891-623">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="57891-624">Use o método `MakeCookieValue` para criar um cookie.</span><span class="sxs-lookup"><span data-stu-id="57891-624">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="57891-625">O `CookieRequestCultureProvider` `DefaultCookieName` retorna o nome de cookie padrão usado para rastrear as informações de cultura preferencial do usuário.</span><span class="sxs-lookup"><span data-stu-id="57891-625">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="57891-626">O nome padrão do cookie é `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="57891-626">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="57891-627">O formato do cookie é `c=%LANGCODE%|uic=%LANGCODE%`, em que `c` é `Culture` e `uic` é `UICulture`, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="57891-627">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="57891-628">Se você especificar apenas as informações de cultura e a cultura da interface do usuário, a cultura especificada será usada para as informações de cultura e para a cultura da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="57891-628">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="57891-629">O cabeçalho HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="57891-629">The Accept-Language HTTP header</span></span>

<span data-ttu-id="57891-630">O [cabeçalho Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) é configurável na maioria dos navegadores e originalmente foi criado para especificar o idioma do usuário.</span><span class="sxs-lookup"><span data-stu-id="57891-630">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="57891-631">Essa configuração indica que o navegador foi definido para enviar ou herdou do sistema operacional subjacente.</span><span class="sxs-lookup"><span data-stu-id="57891-631">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="57891-632">O cabeçalho HTTP Accept-Language de uma solicitação do navegador não é uma maneira infalível de detectar o idioma preferencial do usuário (consulte [Definindo preferências de idioma em um navegador](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="57891-632">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="57891-633">Um aplicativo de produção deve incluir uma maneira para que um usuário personalize sua opção de cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-633">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="57891-634">Definir o cabeçalho HTTP Accept-Language no IE</span><span class="sxs-lookup"><span data-stu-id="57891-634">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="57891-635">No ícone de engrenagem, toque em **Opções da Internet**.</span><span class="sxs-lookup"><span data-stu-id="57891-635">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="57891-636">Toque em **Idiomas**.</span><span class="sxs-lookup"><span data-stu-id="57891-636">Tap **Languages**.</span></span>

    ![Opções da Internet](localization/_static/lang.png)

3. <span data-ttu-id="57891-638">Toque em **Definir Preferências de Idioma**.</span><span class="sxs-lookup"><span data-stu-id="57891-638">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="57891-639">Toque em **Adicionar um idioma**.</span><span class="sxs-lookup"><span data-stu-id="57891-639">Tap **Add a language**.</span></span>

5. <span data-ttu-id="57891-640">Adicione o idioma.</span><span class="sxs-lookup"><span data-stu-id="57891-640">Add the language.</span></span>

6. <span data-ttu-id="57891-641">Toque no idioma e, em seguida, em **Mover Para Cima**.</span><span class="sxs-lookup"><span data-stu-id="57891-641">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="57891-642">Usar um provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="57891-642">Use a custom provider</span></span>

<span data-ttu-id="57891-643">Suponha que você deseje permitir que os clientes armazenem seus idiomas e culturas nos bancos de dados.</span><span class="sxs-lookup"><span data-stu-id="57891-643">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="57891-644">Você pode escrever um provedor para pesquisar esses valores para o usuário.</span><span class="sxs-lookup"><span data-stu-id="57891-644">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="57891-645">O seguinte código mostra como adicionar um provedor personalizado:</span><span class="sxs-lookup"><span data-stu-id="57891-645">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="57891-646">Use `RequestLocalizationOptions` para adicionar ou remover provedores de localização.</span><span class="sxs-lookup"><span data-stu-id="57891-646">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="57891-647">Definir a cultura de forma programática</span><span class="sxs-lookup"><span data-stu-id="57891-647">Set the culture programmatically</span></span>

<span data-ttu-id="57891-648">Este projeto de exemplo **Localization.StarterWeb** no [GitHub](https://github.com/aspnet/entropy) contém a interface do usuário para definir a `Culture`.</span><span class="sxs-lookup"><span data-stu-id="57891-648">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="57891-649">O arquivo *Views/Shared/_SelectLanguagePartial.cshtml* permite que você selecione a cultura na lista de culturas compatíveis:</span><span class="sxs-lookup"><span data-stu-id="57891-649">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="57891-650">O arquivo *Views/Shared/_SelectLanguagePartial.cshtml* é adicionado à seção `footer` do arquivo de layout para que ele fique disponível para todos os exibições:</span><span class="sxs-lookup"><span data-stu-id="57891-650">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="57891-651">O método `SetLanguage` define o cookie de cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-651">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="57891-652">Não é possível conectar o *_SelectLanguagePartial.cshtml* ao código de exemplo para este projeto.</span><span class="sxs-lookup"><span data-stu-id="57891-652">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="57891-653">O projeto **Localization. StarterWeb** no [GitHub](https://github.com/aspnet/entropy) tem código para fluir `RequestLocalizationOptions` para um Razor parcial por meio do contêiner de [injeção de dependência](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="57891-653">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="57891-654">Dados de rota de associação de modelo e cadeias de caracteres de consulta</span><span class="sxs-lookup"><span data-stu-id="57891-654">Model binding route data and query strings</span></span>

<span data-ttu-id="57891-655">Consulte [comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="57891-655">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="57891-656">Termos de globalização e localização</span><span class="sxs-lookup"><span data-stu-id="57891-656">Globalization and localization terms</span></span>

<span data-ttu-id="57891-657">O processo de localização do aplicativo também exige uma compreensão básica dos conjuntos de caracteres relevantes geralmente usados no desenvolvimento moderno de software e uma compreensão dos problemas associados a eles.</span><span class="sxs-lookup"><span data-stu-id="57891-657">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="57891-658">Embora todos os computadores armazenem texto como números (códigos), diferentes sistemas armazenam o mesmo texto usando números diferentes.</span><span class="sxs-lookup"><span data-stu-id="57891-658">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="57891-659">O processo de localização se refere à tradução da interface do usuário do aplicativo para uma cultura/localidade específica.</span><span class="sxs-lookup"><span data-stu-id="57891-659">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="57891-660">[Possibilidade de localização](/dotnet/standard/globalization-localization/localizability-review) é um processo intermediário usado para verificar se um aplicativo globalizado está pronto para localização.</span><span class="sxs-lookup"><span data-stu-id="57891-660">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="57891-661">O formato [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) para o nome de cultura é `<languagecode2>-<country/regioncode2>`, em que `<languagecode2>` é o código de idioma e `<country/regioncode2>` é o código de subcultura.</span><span class="sxs-lookup"><span data-stu-id="57891-661">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="57891-662">Por exemplo, `es-CL` para Espanhol (Chile), `en-US` para inglês (Estados Unidos) e `en-AU` para inglês (Austrália).</span><span class="sxs-lookup"><span data-stu-id="57891-662">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="57891-663">O [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) é uma combinação de um código de cultura de duas letras minúsculas ISO 639 associado a um idioma e um código de subcultura de duas letras maiúsculas ISO 3166 associado a um país ou uma região.</span><span class="sxs-lookup"><span data-stu-id="57891-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="57891-664">Consulte [Nome da cultura de idioma](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="57891-664">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="57891-665">Muitas vezes, internacionalização é abreviada como "I18N".</span><span class="sxs-lookup"><span data-stu-id="57891-665">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="57891-666">A abreviação usa a primeira e última letras e o número de letras entre elas e, portanto, 18 significa o número de letras entre o primeiro "I" e o último "N".</span><span class="sxs-lookup"><span data-stu-id="57891-666">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="57891-667">O mesmo se aplica a Globalização (G11N) e Localização (L10N).</span><span class="sxs-lookup"><span data-stu-id="57891-667">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="57891-668">Termos:</span><span class="sxs-lookup"><span data-stu-id="57891-668">Terms:</span></span>

* <span data-ttu-id="57891-669">Globalização (G11N): o processo de fazer com que um aplicativo dê suporte a diferentes idiomas e regiões.</span><span class="sxs-lookup"><span data-stu-id="57891-669">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="57891-670">Localização (L10N): o processo de personalizar um aplicativo para determinado idioma e região.</span><span class="sxs-lookup"><span data-stu-id="57891-670">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="57891-671">Internacionalização (I18N): descreve a globalização e a localização.</span><span class="sxs-lookup"><span data-stu-id="57891-671">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="57891-672">Cultura: um idioma e, opcionalmente, uma região.</span><span class="sxs-lookup"><span data-stu-id="57891-672">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="57891-673">Cultura neutra: uma cultura que tem um idioma especificado, mas não uma região.</span><span class="sxs-lookup"><span data-stu-id="57891-673">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="57891-674">(por exemplo, "en", "es")</span><span class="sxs-lookup"><span data-stu-id="57891-674">(for example "en", "es")</span></span>
* <span data-ttu-id="57891-675">Cultura específica: uma cultura que tem um idioma e uma região especificados.</span><span class="sxs-lookup"><span data-stu-id="57891-675">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="57891-676">(por exemplo, "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="57891-676">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="57891-677">Cultura pai: a cultura neutra que contém uma cultura específica.</span><span class="sxs-lookup"><span data-stu-id="57891-677">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="57891-678">(por exemplo, "en" é a cultura pai de "en-US" e "en-GB")</span><span class="sxs-lookup"><span data-stu-id="57891-678">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="57891-679">Localidade: uma localidade é o mesmo que uma cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-679">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="57891-680">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="57891-680">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="57891-681">O [projeto Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) usado no artigo.</span><span class="sxs-lookup"><span data-stu-id="57891-681">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="57891-682">Globalizando e localizando aplicativos do .NET</span><span class="sxs-lookup"><span data-stu-id="57891-682">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="57891-683">Recursos em arquivos. resx</span><span class="sxs-lookup"><span data-stu-id="57891-683">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="57891-684">Kit de Ferramentas de Aplicativo Multilíngue da Microsoft</span><span class="sxs-lookup"><span data-stu-id="57891-684">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="57891-685">Localização e genéricos</span><span class="sxs-lookup"><span data-stu-id="57891-685">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="57891-686">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) e [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="57891-686">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="57891-687">Um site multilíngue permite que o site alcance um público mais amplo.</span><span class="sxs-lookup"><span data-stu-id="57891-687">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="57891-688">O ASP.NET Core fornece serviços e middleware para localização em diferentes idiomas e culturas.</span><span class="sxs-lookup"><span data-stu-id="57891-688">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="57891-689">A internacionalização envolve [Globalização](/dotnet/api/system.globalization) e [Localização](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="57891-689">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="57891-690">Globalização é o processo de criação de aplicativos que dão suporte a diferentes culturas.</span><span class="sxs-lookup"><span data-stu-id="57891-690">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="57891-691">A globalização adiciona suporte para entrada, exibição e saída de um conjunto definido de scripts de idiomas relacionados a áreas geográficas específicas.</span><span class="sxs-lookup"><span data-stu-id="57891-691">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="57891-692">Localização é o processo de adaptar um aplicativo globalizado, que você já processou para possibilidade de localização, a determinada cultura/localidade.</span><span class="sxs-lookup"><span data-stu-id="57891-692">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="57891-693">Para obter mais informações, consulte **Termos de globalização e localização** próximo ao final deste documento.</span><span class="sxs-lookup"><span data-stu-id="57891-693">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="57891-694">A localização de aplicativos envolve o seguinte:</span><span class="sxs-lookup"><span data-stu-id="57891-694">App localization involves the following:</span></span>

1. <span data-ttu-id="57891-695">Tornar o conteúdo do aplicativo localizável</span><span class="sxs-lookup"><span data-stu-id="57891-695">Make the app's content localizable</span></span>
1. <span data-ttu-id="57891-696">Fornecer recursos localizados para as culturas e os idiomas aos quais você dá suporte</span><span class="sxs-lookup"><span data-stu-id="57891-696">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="57891-697">Implementar uma estratégia para selecionar o idioma e a cultura para cada solicitação</span><span class="sxs-lookup"><span data-stu-id="57891-697">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="57891-698">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57891-698">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="57891-699">Tornar o conteúdo do aplicativo localizável</span><span class="sxs-lookup"><span data-stu-id="57891-699">Make the app's content localizable</span></span>

<span data-ttu-id="57891-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer ' não exige o armazenamento das cadeias de caracteres de idioma padrão em um arquivo de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="57891-701">Você pode desenvolver um aplicativo direcionado à localização e não precisa criar arquivos de recurso no início do desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="57891-701">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="57891-702">O código abaixo mostra como encapsular a cadeia de caracteres "About Title" para localização.</span><span class="sxs-lookup"><span data-stu-id="57891-702">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="57891-703">No código anterior, a `IStringLocalizer<T>` implementação vem da [injeção de dependência](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="57891-703">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="57891-704">Se o valor localizado de "About Title" não é encontrado, a chave do indexador é retornada, ou seja, a cadeia de caracteres "About Title".</span><span class="sxs-lookup"><span data-stu-id="57891-704">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="57891-705">Deixe as cadeias de caracteres literais de idioma padrão no aplicativo e encapsule-as no localizador, de modo que você possa se concentrar no desenvolvimento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="57891-705">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="57891-706">Você pode desenvolve seu aplicativo com o idioma padrão e prepará-lo para a etapa de localização sem primeiro criar um arquivo de recurso padrão.</span><span class="sxs-lookup"><span data-stu-id="57891-706">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="57891-707">Como alternativa, você pode usar a abordagem tradicional e fornecer uma chave para recuperar a cadeia de caracteres de idioma padrão.</span><span class="sxs-lookup"><span data-stu-id="57891-707">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="57891-708">Para muitos desenvolvedores, o novo fluxo de trabalho de não ter um arquivo *.resx* de idioma padrão e simplesmente encapsular os literais de cadeia de caracteres pode reduzir a sobrecarga de localizar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="57891-708">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="57891-709">Outros desenvolvedores preferirão o fluxo de trabalho tradicional, pois ele pode facilitar o trabalho com literais de cadeia de caracteres mais longas e a atualização de cadeias de caracteres localizadas.</span><span class="sxs-lookup"><span data-stu-id="57891-709">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="57891-710">Use a implementação `IHtmlLocalizer<T>` para recursos que contêm HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-710">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="57891-711">O `IHtmlLocalizer` codifica em HTML os argumentos que são formatados na cadeia de caracteres de recurso, mas não codifica em HTML a cadeia de caracteres de recurso em si.</span><span class="sxs-lookup"><span data-stu-id="57891-711">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="57891-712">Na amostra realçada abaixo, apenas o valor do parâmetro `name` é codificado em HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-712">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="57891-713">**Observação:** em geral, recomendamos localizar somente o texto e não o HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-713">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="57891-714">No nível mais baixo, você pode obter `IStringLocalizerFactory` com a [Injeção de Dependência](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="57891-714">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="57891-715">O código acima demonstra cada um dos dois métodos Create de alocador.</span><span class="sxs-lookup"><span data-stu-id="57891-715">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="57891-716">Você pode particionar as cadeias de caracteres localizadas por controlador, área ou ter apenas um contêiner.</span><span class="sxs-lookup"><span data-stu-id="57891-716">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="57891-717">No aplicativo de exemplo, uma classe fictícia chamada `SharedResource` é usada para recursos compartilhados.</span><span class="sxs-lookup"><span data-stu-id="57891-717">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="57891-718">Alguns desenvolvedores usam a classe `Startup` para conter cadeias de caracteres globais ou compartilhadas.</span><span class="sxs-lookup"><span data-stu-id="57891-718">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="57891-719">Na amostra abaixo, os localizadores `InfoController` e `SharedResource` são usados:</span><span class="sxs-lookup"><span data-stu-id="57891-719">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="57891-720">Localização de exibição</span><span class="sxs-lookup"><span data-stu-id="57891-720">View localization</span></span>

<span data-ttu-id="57891-721">O serviço `IViewLocalizer` fornece cadeias de caracteres localizadas para uma [exibição](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="57891-721">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="57891-722">A classe `ViewLocalizer` implementa essa interface e encontra o local do recurso no caminho do arquivo de exibição.</span><span class="sxs-lookup"><span data-stu-id="57891-722">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="57891-723">O seguinte código mostra como usar a implementação padrão de `IViewLocalizer`:</span><span class="sxs-lookup"><span data-stu-id="57891-723">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="57891-724">A implementação padrão de `IViewLocalizer` encontra o arquivo de recurso com base no nome de arquivo da exibição.</span><span class="sxs-lookup"><span data-stu-id="57891-724">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="57891-725">Não há nenhuma opção para usar um arquivo de recurso compartilhado global.</span><span class="sxs-lookup"><span data-stu-id="57891-725">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="57891-726">`ViewLocalizer`implementa o localizador usando `IHtmlLocalizer` , portanto, Razor não codifica a cadeia de caracteres localizada em HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-726">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="57891-727">Parametrize cadeias de recurso e o `IViewLocalizer` codificará em HTML os parâmetros, mas não a cadeia de caracteres de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-727">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="57891-728">Considere a seguinte Razor marcação:</span><span class="sxs-lookup"><span data-stu-id="57891-728">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="57891-729">Um arquivo de recurso em francês pode conter o seguinte:</span><span class="sxs-lookup"><span data-stu-id="57891-729">A French resource file could contain the following:</span></span>

| <span data-ttu-id="57891-730">Chave</span><span class="sxs-lookup"><span data-stu-id="57891-730">Key</span></span> | <span data-ttu-id="57891-731">Valor</span><span class="sxs-lookup"><span data-stu-id="57891-731">Value</span></span> |
| ----- | ---
<span data-ttu-id="57891-732">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-732">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-733">'Blazor'</span></span>
- <span data-ttu-id="57891-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-734">'Identity'</span></span>
- <span data-ttu-id="57891-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-736">'Razor'</span></span>
- <span data-ttu-id="57891-737">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-737">'SignalR' uid:</span></span> 

<span data-ttu-id="57891-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="57891-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="57891-739">A exibição renderizada contém a marcação HTML do arquivo de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-739">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="57891-740">**Observação:** em geral, recomendamos localizar somente o texto e não o HTML.</span><span class="sxs-lookup"><span data-stu-id="57891-740">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="57891-741">Para usar um arquivo de recurso compartilhado em uma exibição, injete `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="57891-741">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="57891-742">Localização de DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="57891-742">DataAnnotations localization</span></span>

<span data-ttu-id="57891-743">As mensagens de erro de DataAnnotations são localizadas com `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="57891-743">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="57891-744">Usando a opção `ResourcesPath = "Resources"`, as mensagens de erro em `RegisterViewModel` podem ser armazenadas em um dos seguintes caminhos:</span><span class="sxs-lookup"><span data-stu-id="57891-744">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="57891-745">*Recursos/ViewModels. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="57891-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="57891-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="57891-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="57891-747">No ASP.NET Core MVC 1.1.0 e superior, atributos que não sejam de validação são localizados.</span><span class="sxs-lookup"><span data-stu-id="57891-747">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="57891-748">O ASP.NET Core MVC 1.0 **não** pesquisa cadeias de caracteres localizadas para atributos que não sejam de validação.</span><span class="sxs-lookup"><span data-stu-id="57891-748">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="57891-749">Usando uma cadeia de caracteres de recurso para várias classes</span><span class="sxs-lookup"><span data-stu-id="57891-749">Using one resource string for multiple classes</span></span>

<span data-ttu-id="57891-750">O seguinte código mostra como usar uma cadeia de caracteres de recurso para atributos de validação com várias classes:</span><span class="sxs-lookup"><span data-stu-id="57891-750">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="57891-751">No código anterior, `SharedResource` é a classe correspondente ao resx em que as mensagens de validação são armazenadas.</span><span class="sxs-lookup"><span data-stu-id="57891-751">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="57891-752">Com essa abordagem, DataAnnotations usará apenas `SharedResource`, em vez de o recurso para cada classe.</span><span class="sxs-lookup"><span data-stu-id="57891-752">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="57891-753">Fornecer recursos localizados para as culturas e os idiomas aos quais você dá suporte</span><span class="sxs-lookup"><span data-stu-id="57891-753">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="57891-754">SupportedCultures e SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="57891-754">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="57891-755">O ASP.NET Core permite que você especifique dois valores de cultura, `SupportedCultures` e `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="57891-755">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="57891-756">O objeto [CultureInfo](/dotnet/api/system.globalization.cultureinfo) para `SupportedCultures` determina os resultados das funções dependentes de cultura, como data, hora, número e formatação de moeda.</span><span class="sxs-lookup"><span data-stu-id="57891-756">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="57891-757">`SupportedCultures` também determina a ordem de classificação de texto, convenções de uso de maiúsculas e comparações de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="57891-757">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="57891-758">Consulte [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) para obter mais informações sobre como o servidor obtém a Cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-758">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="57891-759">O `SupportedUICultures` determina quais cadeias de caracteres traduzidas (de arquivos *. resx* ) são pesquisadas pelo [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="57891-759">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="57891-760">O `ResourceManager` apenas pesquisa cadeias de caracteres específicas a uma cultura determinadas por `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="57891-760">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="57891-761">Cada thread no .NET tem objetos `CurrentCulture` e `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="57891-761">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="57891-762">O ASP.NET Core inspeciona esses valores durante a renderização de funções dependentes de cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-762">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="57891-763">Por exemplo, se a cultura do thread atual estiver definida como "en-US" (inglês, Estados Unidos), `DateTime.Now.ToLongDateString()` exibirá "Thursday, February 18, 2016", mas se `CurrentCulture` estiver definida como "es-ES" (espanhol, Espanha), o resultado será "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="57891-763">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="57891-764">Arquivos de recurso</span><span class="sxs-lookup"><span data-stu-id="57891-764">Resource files</span></span>

<span data-ttu-id="57891-765">Um arquivo de recurso é um mecanismo útil para separar cadeias de caracteres localizáveis do código.</span><span class="sxs-lookup"><span data-stu-id="57891-765">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="57891-766">As cadeias de caracteres traduzidas para o idioma não padrão são isoladas em arquivos de recurso *. resx* .</span><span class="sxs-lookup"><span data-stu-id="57891-766">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="57891-767">Por exemplo, talvez você queira criar um arquivo de recurso em espanhol chamado *Welcome.es.resx* contendo cadeias de caracteres traduzidas.</span><span class="sxs-lookup"><span data-stu-id="57891-767">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="57891-768">"es" são o código de idioma para o espanhol.</span><span class="sxs-lookup"><span data-stu-id="57891-768">"es" is the language code for Spanish.</span></span> <span data-ttu-id="57891-769">Para criar esse arquivo de recurso no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="57891-769">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="57891-770">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta que conterá o arquivo de recurso > **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="57891-770">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Menu contextual aninhado: no Gerenciador de Soluções, um menu contextual é aberto para Recursos.](localization/_static/newi.png)

2. <span data-ttu-id="57891-773">Na caixa **Pesquisar modelos instalados**, insira "recurso" e nomeie o arquivo.</span><span class="sxs-lookup"><span data-stu-id="57891-773">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Caixa de diálogo Adicionar Novo Item](localization/_static/res.png)

3. <span data-ttu-id="57891-775">Insira o valor da chave (cadeia de caracteres nativa) na coluna **Nome** e a cadeia de caracteres traduzida na coluna **Valor**.</span><span class="sxs-lookup"><span data-stu-id="57891-775">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Arquivo Welcome.es.resx (o arquivo de recurso Welcome em espanhol) com a palavra Hello na coluna Nome e a palavra Hola (Olá, em espanhol) na coluna valor](localization/_static/hola.png)

    <span data-ttu-id="57891-777">O Visual Studio mostra o arquivo *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-777">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Gerenciador de Soluções mostrando o arquivo de recurso Welcome em espanhol (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="57891-779">Nomenclatura do arquivo de recurso</span><span class="sxs-lookup"><span data-stu-id="57891-779">Resource file naming</span></span>

<span data-ttu-id="57891-780">Os recursos são nomeados com o nome completo do tipo de sua classe menos o nome do assembly.</span><span class="sxs-lookup"><span data-stu-id="57891-780">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="57891-781">Por exemplo, um recurso em francês em um projeto cujo assembly principal é `LocalizationWebsite.Web.dll` para a classe `LocalizationWebsite.Web.Startup` será nomeado *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-781">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="57891-782">Um recurso para a classe `LocalizationWebsite.Web.Controllers.HomeController` será nomeado *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-782">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="57891-783">Se o namespace da classe de destino não for o mesmo que o nome do assembly, você precisará do nome completo do tipo.</span><span class="sxs-lookup"><span data-stu-id="57891-783">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="57891-784">Por exemplo, no projeto de exemplo, um recurso para o tipo `ExtraNamespace.Tools` será nomeado *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-784">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="57891-785">No projeto de exemplo, o método `ConfigureServices` define o `ResourcesPath` como "Resources", de modo que o caminho relativo do projeto para o arquivo de recurso em francês do controlador principal seja *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-785">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="57891-786">Como alternativa, você pode usar pastas para organizar arquivos de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-786">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="57891-787">Para o controlador principal, o caminho será *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-787">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="57891-788">Se você não usar a opção `ResourcesPath`, o arquivo *.resx* entrará no diretório base do projeto.</span><span class="sxs-lookup"><span data-stu-id="57891-788">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="57891-789">O arquivo de recurso para `HomeController` será nomeado *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="57891-789">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="57891-790">A opção de usar a convenção de nomenclatura de ponto ou caminho depende de como você deseja organizar os arquivos de recurso.</span><span class="sxs-lookup"><span data-stu-id="57891-790">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="57891-791">Nome do recurso</span><span class="sxs-lookup"><span data-stu-id="57891-791">Resource name</span></span> | <span data-ttu-id="57891-792">Nomenclatura de ponto ou caminho</span><span class="sxs-lookup"><span data-stu-id="57891-792">Dot or path naming</span></span> |
| ---
<span data-ttu-id="57891-793">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-793">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-794">'Blazor'</span></span>
- <span data-ttu-id="57891-795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-795">'Identity'</span></span>
- <span data-ttu-id="57891-796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-796">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-797">'Razor'</span></span>
- <span data-ttu-id="57891-798">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-799">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-799">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-800">'Blazor'</span></span>
- <span data-ttu-id="57891-801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-801">'Identity'</span></span>
- <span data-ttu-id="57891-802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-802">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-803">'Razor'</span></span>
- <span data-ttu-id="57891-804">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-805">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-805">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-806">'Blazor'</span></span>
- <span data-ttu-id="57891-807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-807">'Identity'</span></span>
- <span data-ttu-id="57891-808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-808">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-809">'Razor'</span></span>
- <span data-ttu-id="57891-810">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-811">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-811">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-812">'Blazor'</span></span>
- <span data-ttu-id="57891-813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-813">'Identity'</span></span>
- <span data-ttu-id="57891-814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-814">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-815">'Razor'</span></span>
- <span data-ttu-id="57891-816">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-816">'SignalR' uid:</span></span> 

<span data-ttu-id="57891-817">------   | título do---: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-818">'Blazor'</span></span>
- <span data-ttu-id="57891-819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-819">'Identity'</span></span>
- <span data-ttu-id="57891-820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-820">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-821">'Razor'</span></span>
- <span data-ttu-id="57891-822">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-823">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-823">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-824">'Blazor'</span></span>
- <span data-ttu-id="57891-825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-825">'Identity'</span></span>
- <span data-ttu-id="57891-826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-826">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-827">'Razor'</span></span>
- <span data-ttu-id="57891-828">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-829">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-829">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-830">'Blazor'</span></span>
- <span data-ttu-id="57891-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-831">'Identity'</span></span>
- <span data-ttu-id="57891-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-833">'Razor'</span></span>
- <span data-ttu-id="57891-834">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57891-835">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57891-835">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="57891-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57891-836">'Blazor'</span></span>
- <span data-ttu-id="57891-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57891-837">'Identity'</span></span>
- <span data-ttu-id="57891-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57891-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="57891-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57891-839">'Razor'</span></span>
- <span data-ttu-id="57891-840">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="57891-840">'SignalR' uid:</span></span> 

<span data-ttu-id="57891-841">------- | | Recursos/controladores. HomeController. fr. resx | Ponto | | Recursos/controladores/HomeController. fr. resx | Caminho | |    |     |</span><span class="sxs-lookup"><span data-stu-id="57891-841">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="57891-842">Os arquivos de recurso usando `@inject IViewLocalizer` em Razor exibições seguem um padrão semelhante.</span><span class="sxs-lookup"><span data-stu-id="57891-842">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="57891-843">O arquivo de recurso de uma exibição pode ser nomeado usando a nomenclatura de ponto ou de caminho.</span><span class="sxs-lookup"><span data-stu-id="57891-843">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="57891-844">exiba os arquivos de recurso que imitam o caminho do arquivo de exibição associado.</span><span class="sxs-lookup"><span data-stu-id="57891-844"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="57891-845">Supondo que definimos o `ResourcesPath` como "Resources", o arquivo de recurso em francês associado à exibição *Views/Home/About.cshtml* pode ser um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="57891-845">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="57891-846">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="57891-846">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="57891-847">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="57891-847">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="57891-848">Se você não usar a opção `ResourcesPath`, o arquivo *.resx* de uma exibição estará localizado na mesma pasta da exibição.</span><span class="sxs-lookup"><span data-stu-id="57891-848">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="57891-849">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="57891-849">RootNamespaceAttribute</span></span> 

<span data-ttu-id="57891-850">O atributo [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) fornece o namespace raiz de um assembly quando o namespace raiz de um assembly é diferente do nome do assembly.</span><span class="sxs-lookup"><span data-stu-id="57891-850">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="57891-851">Isso pode ocorrer quando o nome de um projeto não é um identificador .NET válido.</span><span class="sxs-lookup"><span data-stu-id="57891-851">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="57891-852">Por exemplo `my-project-name.csproj` , usará o namespace raiz `my_project_name` e o nome do assembly `my-project-name` que levam a esse erro.</span><span class="sxs-lookup"><span data-stu-id="57891-852">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="57891-853">Se o namespace raiz de um assembly é diferente do nome do assembly:</span><span class="sxs-lookup"><span data-stu-id="57891-853">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="57891-854">A localização não funciona por padrão.</span><span class="sxs-lookup"><span data-stu-id="57891-854">Localization does not work by default.</span></span>
* <span data-ttu-id="57891-855">A localização falha devido à maneira como os recursos são pesquisados dentro do assembly.</span><span class="sxs-lookup"><span data-stu-id="57891-855">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="57891-856">`RootNamespace` é um valor de tempo de build que não está disponível para o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="57891-856">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="57891-857">Se o `RootNamespace` é diferente de `AssemblyName`, inclua o seguinte no *AssemblyInfo.cs* (com valores de parâmetro substituídos pelos valores reais):</span><span class="sxs-lookup"><span data-stu-id="57891-857">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="57891-858">O código anterior permite a resolução bem-sucedida de arquivos resx.</span><span class="sxs-lookup"><span data-stu-id="57891-858">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="57891-859">Comportamento de fallback da cultura</span><span class="sxs-lookup"><span data-stu-id="57891-859">Culture fallback behavior</span></span>

<span data-ttu-id="57891-860">Ao procurar por um recurso, a localização participa do "fallback de cultura".</span><span class="sxs-lookup"><span data-stu-id="57891-860">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="57891-861">Começando com a cultura solicitada, se ela não for encontrada, ela será revertida para a cultura pai dessa cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-861">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="57891-862">Além disso, a propriedade [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) representa a cultura pai.</span><span class="sxs-lookup"><span data-stu-id="57891-862">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="57891-863">Isso normalmente (mas nem sempre) significa a remoção do significante do ISO.</span><span class="sxs-lookup"><span data-stu-id="57891-863">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="57891-864">Por exemplo, o dialeto do espanhol falado no México é "es-MX".</span><span class="sxs-lookup"><span data-stu-id="57891-864">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="57891-865">Ele tem o pai "es", ou seja, espanhol, não específico de nenhum país.</span><span class="sxs-lookup"><span data-stu-id="57891-865">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="57891-866">Imagine que seu site receba uma solicitação de um recurso “Boas-vindas" usando a cultura"fr-CA".</span><span class="sxs-lookup"><span data-stu-id="57891-866">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="57891-867">O sistema de localização procura os seguintes recursos, em ordem, e seleciona a primeira correspondência:</span><span class="sxs-lookup"><span data-stu-id="57891-867">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="57891-868">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="57891-868">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="57891-869">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="57891-869">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="57891-870">*Welcome.resx* (se o `NeutralResourcesLanguage` for "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="57891-870">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="57891-871">Por exemplo, se você remover o designador de cultura ".fr" e tiver a cultura definida como francês, o arquivo de recurso padrão será lido e as cadeias de caracteres serão localizadas.</span><span class="sxs-lookup"><span data-stu-id="57891-871">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="57891-872">O Gerenciador de Recursos designa um padrão ou um recurso de fallback para quando nenhuma opção atende à cultura solicitada.</span><span class="sxs-lookup"><span data-stu-id="57891-872">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="57891-873">Se você deseja apenas retornar a chave quando um recurso está ausente para a cultura solicitada, não deve ter um arquivo de recurso padrão.</span><span class="sxs-lookup"><span data-stu-id="57891-873">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="57891-874">Gerar arquivos de recurso com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57891-874">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="57891-875">Se você criar um arquivo de recurso no Visual Studio sem uma cultura no nome do arquivo (por exemplo, *Welcome.resx*), o Visual Studio criará uma classe do C# com uma propriedade para cada cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="57891-875">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="57891-876">Geralmente isso não é o desejado com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57891-876">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="57891-877">Normalmente você não tem um arquivo de recurso *.resx* padrão (um arquivo *.resx* sem o nome de cultura).</span><span class="sxs-lookup"><span data-stu-id="57891-877">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="57891-878">Sugerimos que você crie o arquivo *.resx* com um nome de cultura (por exemplo *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="57891-878">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="57891-879">Quando você criar um arquivo *.resx* com um nome de cultura, o Visual Studio não gerará o arquivo de classe.</span><span class="sxs-lookup"><span data-stu-id="57891-879">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="57891-880">Adicionar outras culturas</span><span class="sxs-lookup"><span data-stu-id="57891-880">Add other cultures</span></span>

<span data-ttu-id="57891-881">Cada combinação de idioma e cultura (que não seja o idioma padrão) exige um arquivo de recurso exclusivo.</span><span class="sxs-lookup"><span data-stu-id="57891-881">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="57891-882">Crie arquivos de recurso para diferentes culturas e localidades criando novos arquivos de recurso, nos quais os códigos ISO e de idioma fazem parte do nome do arquivo (por exemplo, **en-us**, **fr-ca** e **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="57891-882">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="57891-883">Esses códigos ISO são colocados entre o nome do arquivo e a extensão de arquivo *.resx*, como em *Welcome.es-MX.resx* (espanhol/México).</span><span class="sxs-lookup"><span data-stu-id="57891-883">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="57891-884">Implementar uma estratégia para selecionar o idioma e a cultura para cada solicitação</span><span class="sxs-lookup"><span data-stu-id="57891-884">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="57891-885">Configurar a localização</span><span class="sxs-lookup"><span data-stu-id="57891-885">Configure localization</span></span>

<span data-ttu-id="57891-886">A localização é configurada no método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="57891-886">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="57891-887">`AddLocalization` Adiciona os serviços de localização ao contêiner de serviços.</span><span class="sxs-lookup"><span data-stu-id="57891-887">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="57891-888">O código acima também define o caminho de recursos como "Resources".</span><span class="sxs-lookup"><span data-stu-id="57891-888">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="57891-889">`AddViewLocalization` Adiciona suporte para os arquivos de exibição localizados.</span><span class="sxs-lookup"><span data-stu-id="57891-889">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="57891-890">Nesta amostra, a localização de exibição se baseia no sufixo do arquivo de exibição.</span><span class="sxs-lookup"><span data-stu-id="57891-890">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="57891-891">Por exemplo, "fr" no arquivo *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="57891-891">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="57891-892">`AddDataAnnotationsLocalization` Adiciona suporte para as mensagens de validação `DataAnnotations` localizadas por meio de abstrações `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="57891-892">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="57891-893">Middleware de localização</span><span class="sxs-lookup"><span data-stu-id="57891-893">Localization middleware</span></span>

<span data-ttu-id="57891-894">A cultura atual em uma solicitação é definida no [Middleware](xref:fundamentals/middleware/index) de localização.</span><span class="sxs-lookup"><span data-stu-id="57891-894">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="57891-895">O middleware de localização é habilitado no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="57891-895">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="57891-896">O middleware de localização precisa ser configurado antes de qualquer middleware que possa verificar a cultura de solicitação (por exemplo, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="57891-896">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="57891-897">`UseRequestLocalization` inicializa um objeto `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="57891-897">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="57891-898">Em cada solicitação, a lista de `RequestCultureProvider` em `RequestLocalizationOptions` é enumerada e o primeiro provedor que pode determinar com êxito a cultura de solicitação é usado.</span><span class="sxs-lookup"><span data-stu-id="57891-898">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="57891-899">Os provedores padrão são obtidos da classe `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="57891-899">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="57891-900">A lista padrão é apresentada do mais específico ao menos específico.</span><span class="sxs-lookup"><span data-stu-id="57891-900">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="57891-901">Mais adiante neste artigo, veremos como você pode alterar a ordem e até mesmo adicionar um provedor de cultura personalizado.</span><span class="sxs-lookup"><span data-stu-id="57891-901">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="57891-902">Se nenhum dos provedores pode determinar a cultura de solicitação, o `DefaultRequestCulture` é usado.</span><span class="sxs-lookup"><span data-stu-id="57891-902">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="57891-903">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="57891-903">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="57891-904">Alguns aplicativos usarão uma cadeia de caracteres de consulta para definir a [cultura e a cultura da interface do usuário](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="57891-904">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="57891-905">Para aplicativos que usam a abordagem do cabeçalho Accept-Language ou do cookie, a adição de uma cadeia de caracteres de consulta à URL é útil para depurar e testar o código.</span><span class="sxs-lookup"><span data-stu-id="57891-905">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="57891-906">Por padrão, o `QueryStringRequestCultureProvider` é registrado como o primeiro provedor de localização na lista `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="57891-906">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="57891-907">Passe os parâmetros `culture` e `ui-culture` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="57891-907">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="57891-908">O seguinte exemplo define a cultura específica (idioma e região) como espanhol/México:</span><span class="sxs-lookup"><span data-stu-id="57891-908">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="57891-909">Se você passar somente uma das duas (`culture` ou `ui-culture`), o provedor da cadeia de caracteres de consulta definirá os dois valores usando aquela que foi passada.</span><span class="sxs-lookup"><span data-stu-id="57891-909">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="57891-910">Por exemplo, a definição apenas da cultura definirá a `Culture` e a `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="57891-910">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="57891-911">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="57891-911">CookieRequestCultureProvider</span></span>

<span data-ttu-id="57891-912">Em geral, aplicativos de produção fornecerão um mecanismo para definir a cultura com o cookie de cultura do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57891-912">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="57891-913">Use o método `MakeCookieValue` para criar um cookie.</span><span class="sxs-lookup"><span data-stu-id="57891-913">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="57891-914">O `CookieRequestCultureProvider` `DefaultCookieName` retorna o nome de cookie padrão usado para rastrear as informações de cultura preferencial do usuário.</span><span class="sxs-lookup"><span data-stu-id="57891-914">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="57891-915">O nome padrão do cookie é `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="57891-915">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="57891-916">O formato do cookie é `c=%LANGCODE%|uic=%LANGCODE%`, em que `c` é `Culture` e `uic` é `UICulture`, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="57891-916">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="57891-917">Se você especificar apenas as informações de cultura e a cultura da interface do usuário, a cultura especificada será usada para as informações de cultura e para a cultura da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="57891-917">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="57891-918">O cabeçalho HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="57891-918">The Accept-Language HTTP header</span></span>

<span data-ttu-id="57891-919">O [cabeçalho Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) é configurável na maioria dos navegadores e originalmente foi criado para especificar o idioma do usuário.</span><span class="sxs-lookup"><span data-stu-id="57891-919">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="57891-920">Essa configuração indica que o navegador foi definido para enviar ou herdou do sistema operacional subjacente.</span><span class="sxs-lookup"><span data-stu-id="57891-920">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="57891-921">O cabeçalho HTTP Accept-Language de uma solicitação do navegador não é uma maneira infalível de detectar o idioma preferencial do usuário (consulte [Definindo preferências de idioma em um navegador](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="57891-921">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="57891-922">Um aplicativo de produção deve incluir uma maneira para que um usuário personalize sua opção de cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-922">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="57891-923">Definir o cabeçalho HTTP Accept-Language no IE</span><span class="sxs-lookup"><span data-stu-id="57891-923">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="57891-924">No ícone de engrenagem, toque em **Opções da Internet**.</span><span class="sxs-lookup"><span data-stu-id="57891-924">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="57891-925">Toque em **Idiomas**.</span><span class="sxs-lookup"><span data-stu-id="57891-925">Tap **Languages**.</span></span>

    ![Opções da Internet](localization/_static/lang.png)

3. <span data-ttu-id="57891-927">Toque em **Definir Preferências de Idioma**.</span><span class="sxs-lookup"><span data-stu-id="57891-927">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="57891-928">Toque em **Adicionar um idioma**.</span><span class="sxs-lookup"><span data-stu-id="57891-928">Tap **Add a language**.</span></span>

5. <span data-ttu-id="57891-929">Adicione o idioma.</span><span class="sxs-lookup"><span data-stu-id="57891-929">Add the language.</span></span>

6. <span data-ttu-id="57891-930">Toque no idioma e, em seguida, em **Mover Para Cima**.</span><span class="sxs-lookup"><span data-stu-id="57891-930">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="57891-931">O cabeçalho HTTP do idioma do conteúdo</span><span class="sxs-lookup"><span data-stu-id="57891-931">The Content-Language HTTP header</span></span>

<span data-ttu-id="57891-932">O cabeçalho da entidade de [linguagem de conteúdo](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) :</span><span class="sxs-lookup"><span data-stu-id="57891-932">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="57891-933">É usado para descrever os idiomas destinados ao público.</span><span class="sxs-lookup"><span data-stu-id="57891-933">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="57891-934">Permite que um usuário diferencie de acordo com a linguagem preferencial dos usuários.</span><span class="sxs-lookup"><span data-stu-id="57891-934">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="57891-935">Os cabeçalhos de entidade são usados tanto em solicitações HTTP quanto em respostas.</span><span class="sxs-lookup"><span data-stu-id="57891-935">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="57891-936">O `Content-Language` cabeçalho pode ser adicionado definindo a propriedade `ApplyCurrentCultureToResponseHeaders` .</span><span class="sxs-lookup"><span data-stu-id="57891-936">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="57891-937">Adicionando o `Content-Language` cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="57891-937">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="57891-938">Permite que o RequestLocalizationMiddleware defina o `Content-Language` cabeçalho com o `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="57891-938">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="57891-939">Elimina a necessidade de definir o cabeçalho de resposta `Content-Language` explicitamente.</span><span class="sxs-lookup"><span data-stu-id="57891-939">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="57891-940">Usar um provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="57891-940">Use a custom provider</span></span>

<span data-ttu-id="57891-941">Suponha que você deseje permitir que os clientes armazenem seus idiomas e culturas nos bancos de dados.</span><span class="sxs-lookup"><span data-stu-id="57891-941">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="57891-942">Você pode escrever um provedor para pesquisar esses valores para o usuário.</span><span class="sxs-lookup"><span data-stu-id="57891-942">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="57891-943">O seguinte código mostra como adicionar um provedor personalizado:</span><span class="sxs-lookup"><span data-stu-id="57891-943">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="57891-944">Use `RequestLocalizationOptions` para adicionar ou remover provedores de localização.</span><span class="sxs-lookup"><span data-stu-id="57891-944">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="57891-945">Definir a cultura de forma programática</span><span class="sxs-lookup"><span data-stu-id="57891-945">Set the culture programmatically</span></span>

<span data-ttu-id="57891-946">Este projeto de exemplo **Localization.StarterWeb** no [GitHub](https://github.com/aspnet/entropy) contém a interface do usuário para definir a `Culture`.</span><span class="sxs-lookup"><span data-stu-id="57891-946">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="57891-947">O arquivo *Views/Shared/_SelectLanguagePartial.cshtml* permite que você selecione a cultura na lista de culturas compatíveis:</span><span class="sxs-lookup"><span data-stu-id="57891-947">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="57891-948">O arquivo *Views/Shared/_SelectLanguagePartial.cshtml* é adicionado à seção `footer` do arquivo de layout para que ele fique disponível para todos os exibições:</span><span class="sxs-lookup"><span data-stu-id="57891-948">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="57891-949">O método `SetLanguage` define o cookie de cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-949">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="57891-950">Não é possível conectar o *_SelectLanguagePartial.cshtml* ao código de exemplo para este projeto.</span><span class="sxs-lookup"><span data-stu-id="57891-950">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="57891-951">O projeto **Localization. StarterWeb** no [GitHub](https://github.com/aspnet/entropy) tem código para fluir `RequestLocalizationOptions` para um Razor parcial por meio do contêiner de [injeção de dependência](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="57891-951">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="57891-952">Dados de rota de associação de modelo e cadeias de caracteres de consulta</span><span class="sxs-lookup"><span data-stu-id="57891-952">Model binding route data and query strings</span></span>

<span data-ttu-id="57891-953">Consulte [comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="57891-953">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="57891-954">Termos de globalização e localização</span><span class="sxs-lookup"><span data-stu-id="57891-954">Globalization and localization terms</span></span>

<span data-ttu-id="57891-955">O processo de localização do aplicativo também exige uma compreensão básica dos conjuntos de caracteres relevantes geralmente usados no desenvolvimento moderno de software e uma compreensão dos problemas associados a eles.</span><span class="sxs-lookup"><span data-stu-id="57891-955">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="57891-956">Embora todos os computadores armazenem texto como números (códigos), diferentes sistemas armazenam o mesmo texto usando números diferentes.</span><span class="sxs-lookup"><span data-stu-id="57891-956">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="57891-957">O processo de localização se refere à tradução da interface do usuário do aplicativo para uma cultura/localidade específica.</span><span class="sxs-lookup"><span data-stu-id="57891-957">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="57891-958">[Possibilidade de localização](/dotnet/standard/globalization-localization/localizability-review) é um processo intermediário usado para verificar se um aplicativo globalizado está pronto para localização.</span><span class="sxs-lookup"><span data-stu-id="57891-958">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="57891-959">O formato [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) para o nome de cultura é `<languagecode2>-<country/regioncode2>`, em que `<languagecode2>` é o código de idioma e `<country/regioncode2>` é o código de subcultura.</span><span class="sxs-lookup"><span data-stu-id="57891-959">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="57891-960">Por exemplo, `es-CL` para Espanhol (Chile), `en-US` para inglês (Estados Unidos) e `en-AU` para inglês (Austrália).</span><span class="sxs-lookup"><span data-stu-id="57891-960">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="57891-961">O [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) é uma combinação de um código de cultura de duas letras minúsculas ISO 639 associado a um idioma e um código de subcultura de duas letras maiúsculas ISO 3166 associado a um país ou uma região.</span><span class="sxs-lookup"><span data-stu-id="57891-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="57891-962">Consulte [Nome da cultura de idioma](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="57891-962">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="57891-963">Muitas vezes, internacionalização é abreviada como "I18N".</span><span class="sxs-lookup"><span data-stu-id="57891-963">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="57891-964">A abreviação usa a primeira e última letras e o número de letras entre elas e, portanto, 18 significa o número de letras entre o primeiro "I" e o último "N".</span><span class="sxs-lookup"><span data-stu-id="57891-964">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="57891-965">O mesmo se aplica a Globalização (G11N) e Localização (L10N).</span><span class="sxs-lookup"><span data-stu-id="57891-965">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="57891-966">Termos:</span><span class="sxs-lookup"><span data-stu-id="57891-966">Terms:</span></span>

* <span data-ttu-id="57891-967">Globalização (G11N): o processo de fazer com que um aplicativo dê suporte a diferentes idiomas e regiões.</span><span class="sxs-lookup"><span data-stu-id="57891-967">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="57891-968">Localização (L10N): o processo de personalizar um aplicativo para determinado idioma e região.</span><span class="sxs-lookup"><span data-stu-id="57891-968">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="57891-969">Internacionalização (I18N): descreve a globalização e a localização.</span><span class="sxs-lookup"><span data-stu-id="57891-969">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="57891-970">Cultura: um idioma e, opcionalmente, uma região.</span><span class="sxs-lookup"><span data-stu-id="57891-970">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="57891-971">Cultura neutra: uma cultura que tem um idioma especificado, mas não uma região.</span><span class="sxs-lookup"><span data-stu-id="57891-971">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="57891-972">(por exemplo, "en", "es")</span><span class="sxs-lookup"><span data-stu-id="57891-972">(for example "en", "es")</span></span>
* <span data-ttu-id="57891-973">Cultura específica: uma cultura que tem um idioma e uma região especificados.</span><span class="sxs-lookup"><span data-stu-id="57891-973">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="57891-974">(por exemplo, "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="57891-974">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="57891-975">Cultura pai: a cultura neutra que contém uma cultura específica.</span><span class="sxs-lookup"><span data-stu-id="57891-975">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="57891-976">(por exemplo, "en" é a cultura pai de "en-US" e "en-GB")</span><span class="sxs-lookup"><span data-stu-id="57891-976">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="57891-977">Localidade: uma localidade é o mesmo que uma cultura.</span><span class="sxs-lookup"><span data-stu-id="57891-977">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="57891-978">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="57891-978">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="57891-979">O [projeto Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) usado no artigo.</span><span class="sxs-lookup"><span data-stu-id="57891-979">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="57891-980">Globalizando e localizando aplicativos do .NET</span><span class="sxs-lookup"><span data-stu-id="57891-980">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="57891-981">Recursos em arquivos. resx</span><span class="sxs-lookup"><span data-stu-id="57891-981">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="57891-982">Kit de Ferramentas de Aplicativo Multilíngue da Microsoft</span><span class="sxs-lookup"><span data-stu-id="57891-982">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="57891-983">Localização e genéricos</span><span class="sxs-lookup"><span data-stu-id="57891-983">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

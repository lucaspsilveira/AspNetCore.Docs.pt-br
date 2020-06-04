---
<span data-ttu-id="7fd18-101">Título: configurar a localização de objeto portátil no ASP.NET Core autor: sebastienros Descrição: Este artigo apresenta arquivos de objeto portátil e descreve as etapas para usá-los em um aplicativo ASP.NET Core com a estrutura principal do Orchard.</span><span class="sxs-lookup"><span data-stu-id="7fd18-101">title: Configure portable object localization in ASP.NET Core author: sebastienros description: This article introduces Portable Object files and outlines steps for using them in an ASP.NET Core application with the Orchard Core framework.</span></span>
<span data-ttu-id="7fd18-102">MS. Author: scaddie MS. Date: 09/26/2017 no-loc:</span><span class="sxs-lookup"><span data-stu-id="7fd18-102">ms.author: scaddie ms.date: 09/26/2017 no-loc:</span></span>
- <span data-ttu-id="7fd18-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7fd18-103">'Blazor'</span></span>
- <span data-ttu-id="7fd18-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7fd18-104">'Identity'</span></span>
- <span data-ttu-id="7fd18-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7fd18-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="7fd18-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7fd18-106">'Razor'</span></span>
- <span data-ttu-id="7fd18-107">' SignalR ' UID: fundamentos/Portable-Object-Localization</span><span class="sxs-lookup"><span data-stu-id="7fd18-107">'SignalR' uid: fundamentals/portable-object-localization</span></span>

---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="7fd18-108">Configurar a localização de objeto portátil no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7fd18-108">Configure portable object localization in ASP.NET Core</span></span>

<span data-ttu-id="7fd18-109">Por [Sébastien Ros](https://github.com/sebastienros) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="7fd18-109">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="7fd18-110">Este artigo explica as etapas para usar arquivos PO (Objeto Portátil) em um aplicativo ASP.NET Core com a estrutura [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="7fd18-110">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="7fd18-111">**Observação:** o Orchard Core não é um produto da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="7fd18-111">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="7fd18-112">Consequentemente, a Microsoft não fornece suporte para esse recurso.</span><span class="sxs-lookup"><span data-stu-id="7fd18-112">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="7fd18-113">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7fd18-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="7fd18-114">O que é um arquivo PO?</span><span class="sxs-lookup"><span data-stu-id="7fd18-114">What is a PO file?</span></span>

<span data-ttu-id="7fd18-115">Os arquivos PO são distribuídos como arquivos de texto que contém cadeias de caracteres traduzidas em determinado idioma.</span><span class="sxs-lookup"><span data-stu-id="7fd18-115">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="7fd18-116">Algumas vantagens do uso de arquivos PO em vez de arquivos *.resx* incluem:</span><span class="sxs-lookup"><span data-stu-id="7fd18-116">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="7fd18-117">Os arquivos PO dão suporte à pluralização, ao contrário dos arquivos *.resx*.</span><span class="sxs-lookup"><span data-stu-id="7fd18-117">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="7fd18-118">Os arquivos PO não são compilados como os arquivos *.resx*.</span><span class="sxs-lookup"><span data-stu-id="7fd18-118">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="7fd18-119">Dessa forma, não são necessárias ferramentas especializadas nem etapas de build.</span><span class="sxs-lookup"><span data-stu-id="7fd18-119">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="7fd18-120">Os arquivos PO funcionam bem com ferramentas de colaboração de edição online.</span><span class="sxs-lookup"><span data-stu-id="7fd18-120">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="7fd18-121">Exemplo</span><span class="sxs-lookup"><span data-stu-id="7fd18-121">Example</span></span>

<span data-ttu-id="7fd18-122">Este é um arquivo PO de exemplo que contém a tradução de duas cadeias de caracteres em francês, incluindo uma com sua forma plural:</span><span class="sxs-lookup"><span data-stu-id="7fd18-122">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="7fd18-123">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="7fd18-123">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="7fd18-124">Este exemplo usa a seguinte sintaxe:</span><span class="sxs-lookup"><span data-stu-id="7fd18-124">This example uses the following syntax:</span></span>

- <span data-ttu-id="7fd18-125">`#:`: um comentário que indica o contexto da cadeia de caracteres a ser traduzida.</span><span class="sxs-lookup"><span data-stu-id="7fd18-125">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="7fd18-126">A mesma cadeia de caracteres pode ser traduzida de modo diferente, dependendo do local em que ela está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="7fd18-126">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="7fd18-127">`msgid`: a cadeia de caracteres não traduzida.</span><span class="sxs-lookup"><span data-stu-id="7fd18-127">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="7fd18-128">`msgstr`: a cadeia de caracteres traduzida.</span><span class="sxs-lookup"><span data-stu-id="7fd18-128">`msgstr`: The translated string.</span></span>

<span data-ttu-id="7fd18-129">No caso de suporte à pluralização, entradas adicionais podem ser definidas.</span><span class="sxs-lookup"><span data-stu-id="7fd18-129">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="7fd18-130">`msgid_plural`: a cadeia de caracteres no plural não traduzida.</span><span class="sxs-lookup"><span data-stu-id="7fd18-130">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="7fd18-131">`msgstr[0]`: a cadeia de caracteres traduzida para o caso 0.</span><span class="sxs-lookup"><span data-stu-id="7fd18-131">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="7fd18-132">`msgstr[N]`: a cadeia de caracteres traduzida para o caso N.</span><span class="sxs-lookup"><span data-stu-id="7fd18-132">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="7fd18-133">A especificação de arquivo PO pode ser encontrada [aqui](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="7fd18-133">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="7fd18-134">Configurando o suporte a arquivos PO no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7fd18-134">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="7fd18-135">Este exemplo se baseia em um aplicativo ASP.NET Core MVC gerado com base em um modelo de projeto do Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="7fd18-135">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="7fd18-136">Referenciando o pacote</span><span class="sxs-lookup"><span data-stu-id="7fd18-136">Referencing the package</span></span>

<span data-ttu-id="7fd18-137">Adicione uma referência ao pacote NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="7fd18-137">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="7fd18-138">Ele está disponível em [MyGet](https://www.myget.org/) na fonte do pacote a seguir: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="7fd18-138">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="7fd18-139">O arquivo *.csproj* agora contém uma linha semelhante à seguinte (o número de versão pode variar):</span><span class="sxs-lookup"><span data-stu-id="7fd18-139">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="7fd18-140">Registrando o serviço</span><span class="sxs-lookup"><span data-stu-id="7fd18-140">Registering the service</span></span>

<span data-ttu-id="7fd18-141">Adicione os serviços necessários ao método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7fd18-141">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="7fd18-142">Adicione o middleware necessário ao método `Configure` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7fd18-142">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="7fd18-143">Adicione o código a seguir à sua Razor exibição de escolha.</span><span class="sxs-lookup"><span data-stu-id="7fd18-143">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="7fd18-144">*About.cshtml* é usado neste exemplo.</span><span class="sxs-lookup"><span data-stu-id="7fd18-144">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="7fd18-145">Uma instância `IViewLocalizer` é injetada e usada para traduzir o texto “Olá, Mundo!”.</span><span class="sxs-lookup"><span data-stu-id="7fd18-145">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="7fd18-146">Criando um arquivo PO</span><span class="sxs-lookup"><span data-stu-id="7fd18-146">Creating a PO file</span></span>

<span data-ttu-id="7fd18-147">Crie um arquivo chamado \* \<culture code> . po\* na pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7fd18-147">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="7fd18-148">Neste exemplo, o nome do arquivo é *fr.po* porque o idioma francês é usado:</span><span class="sxs-lookup"><span data-stu-id="7fd18-148">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="7fd18-149">Esse arquivo armazena a cadeia de caracteres a ser traduzida e a cadeia de caracteres traduzida do francês.</span><span class="sxs-lookup"><span data-stu-id="7fd18-149">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="7fd18-150">As traduções são revertidas para a cultura pai, se necessário.</span><span class="sxs-lookup"><span data-stu-id="7fd18-150">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="7fd18-151">Neste exemplo, o arquivo *fr.po* é usado se a cultura solicitada é `fr-FR` ou `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="7fd18-151">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="7fd18-152">Testando o aplicativo</span><span class="sxs-lookup"><span data-stu-id="7fd18-152">Testing the application</span></span>

<span data-ttu-id="7fd18-153">Execute o aplicativo e navegue para a URL `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="7fd18-153">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="7fd18-154">O texto **Olá, Mundo!**</span><span class="sxs-lookup"><span data-stu-id="7fd18-154">The text **Hello world!**</span></span> <span data-ttu-id="7fd18-155">seja exibido.</span><span class="sxs-lookup"><span data-stu-id="7fd18-155">is displayed.</span></span>

<span data-ttu-id="7fd18-156">Navegue para a URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="7fd18-156">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="7fd18-157">O texto **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="7fd18-157">The text **Bonjour le monde!**</span></span> <span data-ttu-id="7fd18-158">seja exibido.</span><span class="sxs-lookup"><span data-stu-id="7fd18-158">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="7fd18-159">Pluralização</span><span class="sxs-lookup"><span data-stu-id="7fd18-159">Pluralization</span></span>

<span data-ttu-id="7fd18-160">Os arquivos PO dão suporte a formas de pluralização, que são úteis quando a mesma cadeia de caracteres precisa ser traduzida de modo diferente de acordo com uma cardinalidade.</span><span class="sxs-lookup"><span data-stu-id="7fd18-160">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="7fd18-161">Essa tarefa torna-se complicada pelo fato de que cada idioma define regras personalizadas para selecionar qual cadeia de caracteres será usada de acordo com a cardinalidade.</span><span class="sxs-lookup"><span data-stu-id="7fd18-161">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="7fd18-162">O pacote de Localização do Orchard fornece uma API para invocar essas diferentes formas plurais automaticamente.</span><span class="sxs-lookup"><span data-stu-id="7fd18-162">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="7fd18-163">Criando arquivos PO de pluralização</span><span class="sxs-lookup"><span data-stu-id="7fd18-163">Creating pluralization PO files</span></span>

<span data-ttu-id="7fd18-164">Adicione o seguinte conteúdo ao arquivo *fr.po* mencionado anteriormente:</span><span class="sxs-lookup"><span data-stu-id="7fd18-164">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="7fd18-165">Consulte [O que é um arquivo PO?](#what-is-a-po-file) para obter uma explicação do que representa cada entrada neste exemplo.</span><span class="sxs-lookup"><span data-stu-id="7fd18-165">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="7fd18-166">Adicionando um idioma usando diferentes formas de pluralização</span><span class="sxs-lookup"><span data-stu-id="7fd18-166">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="7fd18-167">Cadeias de caracteres em inglês e francês foram usadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="7fd18-167">English and French strings were used in the previous example.</span></span> <span data-ttu-id="7fd18-168">O inglês e o francês têm apenas duas formas de pluralização e compartilham as mesmas regras de forma, o que significa que uma cardinalidade de um é mapeada para a primeira forma plural.</span><span class="sxs-lookup"><span data-stu-id="7fd18-168">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="7fd18-169">Qualquer outra cardinalidade é mapeada para a segunda forma plural.</span><span class="sxs-lookup"><span data-stu-id="7fd18-169">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="7fd18-170">Nem todos os idiomas compartilham as mesmas regras.</span><span class="sxs-lookup"><span data-stu-id="7fd18-170">Not all languages share the same rules.</span></span> <span data-ttu-id="7fd18-171">Isso é ilustrado com o idioma tcheco, que tem três formas plurais.</span><span class="sxs-lookup"><span data-stu-id="7fd18-171">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="7fd18-172">Crie o arquivo `cs.po` da seguinte maneira e observe como a pluralização precisa de três traduções diferentes:</span><span class="sxs-lookup"><span data-stu-id="7fd18-172">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="7fd18-173">Para aceitar localizações para o tcheco, adicione `"cs"` à lista de culturas com suporte no método `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7fd18-173">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="7fd18-174">Edite o arquivo *Views/Home/About.cshtml* para renderizar cadeias de caracteres localizadas no plural para várias cardinalidades:</span><span class="sxs-lookup"><span data-stu-id="7fd18-174">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="7fd18-175">**Observação:** em um cenário do mundo real, uma variável é usada para representar a contagem.</span><span class="sxs-lookup"><span data-stu-id="7fd18-175">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="7fd18-176">Aqui, repetimos o mesmo código com três valores diferentes para expor um caso muito específico.</span><span class="sxs-lookup"><span data-stu-id="7fd18-176">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="7fd18-177">Ao mudar as culturas, o seguinte é observado:</span><span class="sxs-lookup"><span data-stu-id="7fd18-177">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="7fd18-178">Para `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="7fd18-178">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="7fd18-179">Para `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="7fd18-179">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="7fd18-180">Para `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="7fd18-180">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="7fd18-181">Observe que, para a cultura do tcheco, as três traduções são diferentes.</span><span class="sxs-lookup"><span data-stu-id="7fd18-181">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="7fd18-182">As culturas do francês e do inglês compartilham a mesma construção para as duas últimas cadeias de caracteres traduzidas.</span><span class="sxs-lookup"><span data-stu-id="7fd18-182">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="7fd18-183">Tarefas avançadas</span><span class="sxs-lookup"><span data-stu-id="7fd18-183">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="7fd18-184">Contextualizando cadeias de caracteres</span><span class="sxs-lookup"><span data-stu-id="7fd18-184">Contextualizing strings</span></span>

<span data-ttu-id="7fd18-185">Os aplicativos costumam conter as cadeias de caracteres a serem traduzidas em vários locais.</span><span class="sxs-lookup"><span data-stu-id="7fd18-185">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="7fd18-186">A mesma cadeia de caracteres pode ter uma tradução diferente em determinados locais dentro de um aplicativo ( Razor exibições ou arquivos de classe).</span><span class="sxs-lookup"><span data-stu-id="7fd18-186">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="7fd18-187">Um arquivo PO dá suporte à noção de um contexto de arquivo, que pode ser usado para categorizar a cadeia de caracteres que está sendo representada.</span><span class="sxs-lookup"><span data-stu-id="7fd18-187">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="7fd18-188">Usando um contexto de arquivo, uma cadeia de caracteres pode ser traduzida de forma diferente, dependendo do contexto de arquivo (ou de sua ausência).</span><span class="sxs-lookup"><span data-stu-id="7fd18-188">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="7fd18-189">Os serviços de localização de PO usam o nome da classe completa ou da exibição que é usado ao traduzir uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="7fd18-189">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="7fd18-190">Isso é feito definindo o valor na entrada `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="7fd18-190">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="7fd18-191">Considere uma adição mínima ao exemplo anterior de *fr.po*.</span><span class="sxs-lookup"><span data-stu-id="7fd18-191">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="7fd18-192">Uma Razor exibição localizada em *views/Home/about. cshtml* pode ser definida como o contexto do arquivo, definindo o `msgctxt` valor da entrada reservada:</span><span class="sxs-lookup"><span data-stu-id="7fd18-192">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="7fd18-193">Com o `msgctxt` definido assim, a tradução de texto ocorre durante a navegação para `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="7fd18-193">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="7fd18-194">A tradução não ocorre durante a navegação para `/Home/Contact?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="7fd18-194">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="7fd18-195">Quando não é encontrada a correspondência de nenhuma entrada específica com um contexto de arquivo fornecido, o mecanismo de fallback do Orchard Core procura um arquivo PO apropriado sem contexto.</span><span class="sxs-lookup"><span data-stu-id="7fd18-195">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="7fd18-196">Supondo que não haja nenhum contexto de arquivo específico definido para *Views/Home/Contact.cshtml*, a navegação para `/Home/Contact?culture=fr-FR` carrega um arquivo PO, como:</span><span class="sxs-lookup"><span data-stu-id="7fd18-196">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="7fd18-197">Alterando o local dos arquivos PO</span><span class="sxs-lookup"><span data-stu-id="7fd18-197">Changing the location of PO files</span></span>

<span data-ttu-id="7fd18-198">A localização padrão dos arquivos PO pode ser alterada em `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7fd18-198">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="7fd18-199">Neste exemplo, os arquivos PO são carregados da pasta *Localization*.</span><span class="sxs-lookup"><span data-stu-id="7fd18-199">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="7fd18-200">Implementando uma lógica personalizada para encontrar arquivos de localização</span><span class="sxs-lookup"><span data-stu-id="7fd18-200">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="7fd18-201">Quando uma lógica mais complexa é necessária para localizar arquivos PO, a interface `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` pode ser implementada e registrada como um serviço.</span><span class="sxs-lookup"><span data-stu-id="7fd18-201">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="7fd18-202">Isso é útil quando arquivos PO podem ser armazenados em locais variáveis ou quando os arquivos precisam ser encontrados em uma hierarquia de pastas.</span><span class="sxs-lookup"><span data-stu-id="7fd18-202">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="7fd18-203">Usando um idioma pluralizado padrão diferente</span><span class="sxs-lookup"><span data-stu-id="7fd18-203">Using a different default pluralized language</span></span>

<span data-ttu-id="7fd18-204">O pacote inclui um método de extensão `Plural` específico a duas formas plurais.</span><span class="sxs-lookup"><span data-stu-id="7fd18-204">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="7fd18-205">Para idiomas que exigem mais formas plurais, crie um método de extensão.</span><span class="sxs-lookup"><span data-stu-id="7fd18-205">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="7fd18-206">Com um método de extensão, você não precisará fornecer nenhum arquivo de localização para o idioma padrão – as cadeias de caracteres originais já estão disponíveis diretamente no código.</span><span class="sxs-lookup"><span data-stu-id="7fd18-206">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="7fd18-207">Use a sobrecarga `Plural(int count, string[] pluralForms, params object[] arguments)` mais genérica que aceita uma matriz de cadeia de caracteres de traduções.</span><span class="sxs-lookup"><span data-stu-id="7fd18-207">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

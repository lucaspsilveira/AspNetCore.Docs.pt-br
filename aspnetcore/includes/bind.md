<span data-ttu-id="a25e9-101">A maneira preferida de ler valores de configuração relacionados é usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="a25e9-101">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="a25e9-102">Por exemplo, para ler os seguintes valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="a25e9-102">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="a25e9-103">Crie a seguinte `PositionOptions` classe:</span><span class="sxs-lookup"><span data-stu-id="a25e9-103">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="a25e9-104">Uma classe de opções:</span><span class="sxs-lookup"><span data-stu-id="a25e9-104">An options class:</span></span>

* <span data-ttu-id="a25e9-105">Deve ser não-abstrato com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="a25e9-105">Must be non-abstract with a public parameterless constructor.</span></span>
* <span data-ttu-id="a25e9-106">Todas as propriedades de leitura/gravação públicas do tipo estão associadas.</span><span class="sxs-lookup"><span data-stu-id="a25e9-106">All public read-write properties of the type are bound.</span></span>
* <span data-ttu-id="a25e9-107">Os campos ***não*** estão associados.</span><span class="sxs-lookup"><span data-stu-id="a25e9-107">Fields are ***not*** bound.</span></span> <span data-ttu-id="a25e9-108">No código anterior, `Position` não está associado.</span><span class="sxs-lookup"><span data-stu-id="a25e9-108">In the preceding code, `Position` is not bound.</span></span> <span data-ttu-id="a25e9-109">A `Position` propriedade é usada para que a cadeia de caracteres `"Position"` não precise ser embutida em código no aplicativo ao associar a classe a um provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="a25e9-109">The `Position` property is used so the string `"Position"` doesn't need to be hard coded in the app when binding the class to a configuration provider.</span></span>

<span data-ttu-id="a25e9-110">O seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a25e9-110">The following code:</span></span>

* <span data-ttu-id="a25e9-111">Chama [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) para associar a `PositionOptions` classe à `Position` seção.</span><span class="sxs-lookup"><span data-stu-id="a25e9-111">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="a25e9-112">Exibe os `Position` dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="a25e9-112">Displays the `Position` configuration data.</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="a25e9-113">No código anterior, por padrão, as alterações no arquivo de configuração JSON depois que o aplicativo é iniciado são lidas.</span><span class="sxs-lookup"><span data-stu-id="a25e9-113">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="a25e9-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="a25e9-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="a25e9-115">`ConfigurationBinder.Get<T>`pode ser mais conveniente do que usar o `ConfigurationBinder.Bind` .</span><span class="sxs-lookup"><span data-stu-id="a25e9-115">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="a25e9-116">O código a seguir mostra como usar `ConfigurationBinder.Get<T>` com a `PositionOptions` classe:</span><span class="sxs-lookup"><span data-stu-id="a25e9-116">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="a25e9-117">No código anterior, por padrão, as alterações no arquivo de configuração JSON depois que o aplicativo é iniciado são lidas.</span><span class="sxs-lookup"><span data-stu-id="a25e9-117">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="a25e9-118">Uma abordagem alternativa ao usar o ***padrão de opções*** é associar a `Position` seção e adicioná-la ao [contêiner de serviço de injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a25e9-118">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a25e9-119">No código a seguir, `PositionOptions` é adicionado ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associado à configuração:</span><span class="sxs-lookup"><span data-stu-id="a25e9-119">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="a25e9-120">Usando o código anterior, o código a seguir lê as opções de posição:</span><span class="sxs-lookup"><span data-stu-id="a25e9-120">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="a25e9-121">No código anterior, as alterações no arquivo de configuração JSON depois que o aplicativo foi iniciado ***não*** são lidas.</span><span class="sxs-lookup"><span data-stu-id="a25e9-121">In the preceding code, changes to the JSON configuration file after the app has started are ***not*** read.</span></span> <span data-ttu-id="a25e9-122">Para ler as alterações depois que o aplicativo for iniciado, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span><span class="sxs-lookup"><span data-stu-id="a25e9-122">To read changes after the app has started, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span></span>

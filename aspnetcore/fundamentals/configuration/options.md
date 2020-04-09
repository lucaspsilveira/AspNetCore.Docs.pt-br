---
title: Padrão de opções no ASP.NET Core
author: rick-anderson
description: Descubra como usar o padrão de opções para representar grupos de configurações relacionadas em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
uid: fundamentals/configuration/options
ms.openlocfilehash: 756d3d57122642ab10ab671c9accb75975c3799d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665455"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="12462-103">Padrão de opções no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="12462-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="12462-104">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="12462-104">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="12462-105">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="12462-105">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="12462-106">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="12462-106">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="12462-107">[Separação de preocupações](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes ou acopladas umas às outras.</span><span class="sxs-lookup"><span data-stu-id="12462-107">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="12462-108">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="12462-108">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="12462-109">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="12462-109">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="12462-110">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="12462-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="12462-111">Pacote</span><span class="sxs-lookup"><span data-stu-id="12462-111">Package</span></span>

<span data-ttu-id="12462-112">O pacote [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) é implicitamente referenciado em ASP.NET aplicativos Core.</span><span class="sxs-lookup"><span data-stu-id="12462-112">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="12462-113">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="12462-113">Options interfaces</span></span>

<span data-ttu-id="12462-114">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="12462-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="12462-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> oferece suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="12462-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="12462-116">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="12462-116">Change notifications</span></span>
* [<span data-ttu-id="12462-117">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="12462-117">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="12462-118">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="12462-118">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="12462-119">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="12462-119">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="12462-120">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="12462-120">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="12462-121">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="12462-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="12462-122">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="12462-122">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="12462-123">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="12462-123">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="12462-124">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="12462-124">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="12462-125">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="12462-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="12462-126">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="12462-126">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="12462-127">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="12462-127">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="12462-128">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="12462-128">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="12462-129">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="12462-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="12462-130">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="12462-130">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="12462-131">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="12462-131"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="12462-132">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="12462-132">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="12462-133">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="12462-133">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="12462-134">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="12462-134">General options configuration</span></span>

<span data-ttu-id="12462-135">A configuração de opções gerais é demonstrada no Exemplo 1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="12462-135">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="12462-136">Uma classe de opções deve ser não abstrata com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="12462-136">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="12462-137">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="12462-137">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="12462-138">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="12462-138">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="12462-139">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-139">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="12462-140">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="12462-140">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="12462-141">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-141">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="12462-142">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="12462-142">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="12462-143">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="12462-143">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="12462-144">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="12462-144">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="12462-145">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="12462-145">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="12462-146">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="12462-146">Configure simple options with a delegate</span></span>

<span data-ttu-id="12462-147">A configuração de opções simples com um delegado é demonstrada como o Exemplo 2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="12462-147">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="12462-148">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="12462-148">Use a delegate to set options values.</span></span> <span data-ttu-id="12462-149">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-149">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="12462-150">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-150">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="12462-151">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="12462-151">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="12462-152">*Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="12462-152">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="12462-153">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="12462-153">You can add multiple configuration providers.</span></span> <span data-ttu-id="12462-154">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="12462-154">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="12462-155">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="12462-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="12462-156">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-156">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="12462-157">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="12462-157">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="12462-158">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="12462-158">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="12462-159">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="12462-159">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="12462-160">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="12462-160">Suboptions configuration</span></span>

<span data-ttu-id="12462-161">A configuração de subopções básicas é demonstrada como o Exemplo 3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="12462-161">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="12462-162">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12462-162">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="12462-163">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="12462-163">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="12462-164">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração do formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="12462-164">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="12462-165">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="12462-165">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="12462-166">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-166">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="12462-167">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="12462-167">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="12462-168">O `GetSection` método <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> requer o namespace.</span><span class="sxs-lookup"><span data-stu-id="12462-168">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="12462-169">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="12462-169">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="12462-170">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-170">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="12462-171">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-171">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="12462-172">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="12462-172">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="12462-173">Injeção de opções</span><span class="sxs-lookup"><span data-stu-id="12462-173">Options injection</span></span>

<span data-ttu-id="12462-174">A injeção de opções é demonstrada como Exemplo 4 no aplicativo de amostra.</span><span class="sxs-lookup"><span data-stu-id="12462-174">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="12462-175">Injete <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em:</span><span class="sxs-lookup"><span data-stu-id="12462-175">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="12462-176">Uma página de navalha [`@inject`](xref:mvc/views/razor#inject) ou exibição de MVC com a diretiva Razor.</span><span class="sxs-lookup"><span data-stu-id="12462-176">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="12462-177">Uma página ou modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="12462-177">A page or view model.</span></span>

<span data-ttu-id="12462-178">O exemplo a seguir do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aplicativo de amostra injeta em um modelo de página (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-178">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="12462-179">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="12462-179">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="12462-180">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="12462-180">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="12462-182">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="12462-182">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="12462-183">A recarga <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> de dados de configuração com é demonstrada no Exemplo 5 no aplicativo de amostra.</span><span class="sxs-lookup"><span data-stu-id="12462-183">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="12462-184">Usando <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, as opções são calculadas uma vez por solicitação quando acessadas e armazenadas em cache durante a vida útil da solicitação.</span><span class="sxs-lookup"><span data-stu-id="12462-184">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="12462-185">A diferença `IOptionsMonitor` `IOptionsSnapshot` entre e é que:</span><span class="sxs-lookup"><span data-stu-id="12462-185">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="12462-186">`IOptionsMonitor`é um [serviço singleton](xref:fundamentals/dependency-injection#singleton) que recupera os valores atuais da opção a qualquer momento, o que é especialmente útil em dependências de singleton.</span><span class="sxs-lookup"><span data-stu-id="12462-186">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="12462-187">`IOptionsSnapshot`é um [serviço escopo](xref:fundamentals/dependency-injection#scoped) e fornece um instantâneo `IOptionsSnapshot<T>` das opções no momento em que o objeto é construído.</span><span class="sxs-lookup"><span data-stu-id="12462-187">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="12462-188">Os instantâneos de opções são projetados para uso com dependências transitórias e escopo.</span><span class="sxs-lookup"><span data-stu-id="12462-188">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="12462-189">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="12462-189">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="12462-190">Várias solicitações ao servidor retornam valores de constante fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="12462-190">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="12462-191">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="12462-191">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="12462-192">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="12462-192">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="12462-193">Salvar o arquivo *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="12462-193">Save the *appsettings.json* file.</span></span> <span data-ttu-id="12462-194">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="12462-194">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="12462-195">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="12462-195">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="12462-196">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo 6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="12462-196">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="12462-197">O suporte de opções nomeadas permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="12462-197">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="12462-198">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), que chama o [ConfigureNamedOptions\<TOptions>. Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) o método de extensão.</span><span class="sxs-lookup"><span data-stu-id="12462-198">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="12462-199">As opções nomeadas são sensíveis a maiúsculas e minúsculas.</span><span class="sxs-lookup"><span data-stu-id="12462-199">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="12462-200">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-200">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="12462-201">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="12462-201">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="12462-202">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="12462-202">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="12462-203">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="12462-203">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="12462-204">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="12462-204">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="12462-205">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="12462-205">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="12462-206">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="12462-206">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="12462-207">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="12462-207">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="12462-208">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="12462-208">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="12462-209">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="12462-209">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="12462-210">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="12462-210">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="12462-211">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="12462-211">All options are named instances.</span></span> <span data-ttu-id="12462-212">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="12462-212">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="12462-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="12462-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="12462-214">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="12462-214">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="12462-215">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="12462-215">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="12462-216">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="12462-216">OptionsBuilder API</span></span>

<span data-ttu-id="12462-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="12462-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="12462-218">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="12462-218">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="12462-219">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12462-219">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="12462-220">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="12462-220">Use DI services to configure options</span></span>

<span data-ttu-id="12462-221">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="12462-221">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="12462-222">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="12462-222">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="12462-223">`OptionsBuilder<TOptions>`fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem o uso de até cinco serviços para configurar opções:</span><span class="sxs-lookup"><span data-stu-id="12462-223">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="12462-224">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-224">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="12462-225">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="12462-225">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="12462-226">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="12462-226">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="12462-227">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="12462-227">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="12462-228">Validação de opções</span><span class="sxs-lookup"><span data-stu-id="12462-228">Options validation</span></span>

<span data-ttu-id="12462-229">A validação de opções permite validar as opções depois de configurá-las.</span><span class="sxs-lookup"><span data-stu-id="12462-229">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="12462-230">Chame `Validate` com um método de validação que retorna `true` quando as opções são válidas, e `false` quando não são:</span><span class="sxs-lookup"><span data-stu-id="12462-230">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="12462-231">O exemplo anterior define a instância de opções nomeadas como `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="12462-231">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="12462-232">A instância de opções padrão é `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="12462-232">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="12462-233">A validação é executada após a criação da instância de opções.</span><span class="sxs-lookup"><span data-stu-id="12462-233">Validation runs when the options instance is created.</span></span> <span data-ttu-id="12462-234">Uma instância de opções é garantida para passar validação na primeira vez que é acessada.</span><span class="sxs-lookup"><span data-stu-id="12462-234">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="12462-235">A validação de opções não protege contra modificações de opções após a criação da instância de opções.</span><span class="sxs-lookup"><span data-stu-id="12462-235">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="12462-236">Por exemplo, `IOptionsSnapshot` as opções são criadas e validadas uma vez por solicitação quando as opções são acessadas pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="12462-236">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="12462-237">As `IOptionsSnapshot` opções não são validadas novamente em tentativas de acesso subseqüentes *para a mesma solicitação*.</span><span class="sxs-lookup"><span data-stu-id="12462-237">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="12462-238">O método `Validate` aceita um `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="12462-238">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="12462-239">Para personalizar totalmente a validação, implemente `IValidateOptions<TOptions>`, que permite:</span><span class="sxs-lookup"><span data-stu-id="12462-239">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="12462-240">A validação de vários tipos de opções: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="12462-240">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="12462-241">A validação que depende de outro tipo de opção: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="12462-241">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="12462-242">`IValidateOptions` valida:</span><span class="sxs-lookup"><span data-stu-id="12462-242">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="12462-243">Uma instância específica de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="12462-243">A specific named options instance.</span></span>
* <span data-ttu-id="12462-244">Todas as opções quando `name` for `null`.</span><span class="sxs-lookup"><span data-stu-id="12462-244">All options when `name` is `null`.</span></span>

<span data-ttu-id="12462-245">Retornar um `ValidateOptionsResult` da implementação da interface:</span><span class="sxs-lookup"><span data-stu-id="12462-245">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="12462-246">A validação de anotação de dados está disponível no pacote [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) por meio da chamada do método <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> em `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="12462-246">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="12462-247">`Microsoft.Extensions.Options.DataAnnotations`é implicitamente referenciado em ASP.NET aplicativos Core.</span><span class="sxs-lookup"><span data-stu-id="12462-247">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="12462-248">A validação adiantada (fail fast na inicialização) está sendo considerada para uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="12462-248">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="12462-249">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="12462-249">Options post-configuration</span></span>

<span data-ttu-id="12462-250">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="12462-250">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="12462-251">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="12462-251">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="12462-252">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="12462-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="12462-253">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="12462-253">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="12462-254">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="12462-254">Accessing options during startup</span></span>

<span data-ttu-id="12462-255"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="12462-255"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="12462-256">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="12462-256">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="12462-257">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-257">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="12462-258">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="12462-258">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="12462-259">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="12462-259">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="12462-260">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="12462-260">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="12462-261">[Separação de preocupações](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes ou acopladas umas às outras.</span><span class="sxs-lookup"><span data-stu-id="12462-261">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="12462-262">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="12462-262">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="12462-263">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="12462-263">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="12462-264">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="12462-264">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="12462-265">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="12462-265">Prerequisites</span></span>

<span data-ttu-id="12462-266">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="12462-266">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="12462-267">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="12462-267">Options interfaces</span></span>

<span data-ttu-id="12462-268">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="12462-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="12462-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> oferece suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="12462-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="12462-270">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="12462-270">Change notifications</span></span>
* [<span data-ttu-id="12462-271">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="12462-271">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="12462-272">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="12462-272">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="12462-273">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="12462-273">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="12462-274">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="12462-274">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="12462-275">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="12462-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="12462-276">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="12462-276">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="12462-277">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="12462-277">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="12462-278">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="12462-278">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="12462-279">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="12462-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="12462-280">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="12462-280">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="12462-281">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="12462-281">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="12462-282">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="12462-282">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="12462-283">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="12462-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="12462-284">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="12462-284">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="12462-285">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="12462-285"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="12462-286">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="12462-286">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="12462-287">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="12462-287">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="12462-288">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="12462-288">General options configuration</span></span>

<span data-ttu-id="12462-289">A configuração de opções gerais é demonstrada no Exemplo 1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="12462-289">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="12462-290">Uma classe de opções deve ser não abstrata com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="12462-290">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="12462-291">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="12462-291">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="12462-292">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="12462-292">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="12462-293">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-293">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="12462-294">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="12462-294">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="12462-295">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-295">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="12462-296">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="12462-296">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="12462-297">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="12462-297">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="12462-298">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="12462-298">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="12462-299">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="12462-299">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="12462-300">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="12462-300">Configure simple options with a delegate</span></span>

<span data-ttu-id="12462-301">A configuração de opções simples com um delegado é demonstrada como o Exemplo 2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="12462-301">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="12462-302">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="12462-302">Use a delegate to set options values.</span></span> <span data-ttu-id="12462-303">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-303">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="12462-304">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-304">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="12462-305">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="12462-305">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="12462-306">*Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="12462-306">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="12462-307">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="12462-307">You can add multiple configuration providers.</span></span> <span data-ttu-id="12462-308">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="12462-308">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="12462-309">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="12462-309">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="12462-310">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-310">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="12462-311">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="12462-311">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="12462-312">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="12462-312">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="12462-313">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="12462-313">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="12462-314">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="12462-314">Suboptions configuration</span></span>

<span data-ttu-id="12462-315">A configuração de subopções básicas é demonstrada como o Exemplo 3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="12462-315">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="12462-316">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12462-316">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="12462-317">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="12462-317">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="12462-318">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração do formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="12462-318">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="12462-319">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="12462-319">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="12462-320">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-320">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="12462-321">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="12462-321">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="12462-322">O `GetSection` método <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> requer o namespace.</span><span class="sxs-lookup"><span data-stu-id="12462-322">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="12462-323">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="12462-323">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="12462-324">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-324">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="12462-325">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-325">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="12462-326">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="12462-326">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="12462-327">Injeção de opções</span><span class="sxs-lookup"><span data-stu-id="12462-327">Options injection</span></span>

<span data-ttu-id="12462-328">A injeção de opções é demonstrada como Exemplo 4 no aplicativo de amostra.</span><span class="sxs-lookup"><span data-stu-id="12462-328">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="12462-329">Injete <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em:</span><span class="sxs-lookup"><span data-stu-id="12462-329">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="12462-330">Uma página de navalha [`@inject`](xref:mvc/views/razor#inject) ou exibição de MVC com a diretiva Razor.</span><span class="sxs-lookup"><span data-stu-id="12462-330">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="12462-331">Uma página ou modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="12462-331">A page or view model.</span></span>

<span data-ttu-id="12462-332">O exemplo a seguir do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aplicativo de amostra injeta em um modelo de página (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-332">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="12462-333">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="12462-333">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="12462-334">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="12462-334">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="12462-336">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="12462-336">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="12462-337">A recarga <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> de dados de configuração com é demonstrada no Exemplo 5 no aplicativo de amostra.</span><span class="sxs-lookup"><span data-stu-id="12462-337">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="12462-338">Usando <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, as opções são calculadas uma vez por solicitação quando acessadas e armazenadas em cache durante a vida útil da solicitação.</span><span class="sxs-lookup"><span data-stu-id="12462-338">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="12462-339">A diferença `IOptionsMonitor` `IOptionsSnapshot` entre e é que:</span><span class="sxs-lookup"><span data-stu-id="12462-339">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="12462-340">`IOptionsMonitor`é um [serviço singleton](xref:fundamentals/dependency-injection#singleton) que recupera os valores atuais da opção a qualquer momento, o que é especialmente útil em dependências de singleton.</span><span class="sxs-lookup"><span data-stu-id="12462-340">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="12462-341">`IOptionsSnapshot`é um [serviço escopo](xref:fundamentals/dependency-injection#scoped) e fornece um instantâneo `IOptionsSnapshot<T>` das opções no momento em que o objeto é construído.</span><span class="sxs-lookup"><span data-stu-id="12462-341">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="12462-342">Os instantâneos de opções são projetados para uso com dependências transitórias e escopo.</span><span class="sxs-lookup"><span data-stu-id="12462-342">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="12462-343">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="12462-343">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="12462-344">Várias solicitações ao servidor retornam valores de constante fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="12462-344">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="12462-345">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="12462-345">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="12462-346">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="12462-346">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="12462-347">Salvar o arquivo *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="12462-347">Save the *appsettings.json* file.</span></span> <span data-ttu-id="12462-348">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="12462-348">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="12462-349">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="12462-349">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="12462-350">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo 6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="12462-350">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="12462-351">O suporte de opções nomeadas permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="12462-351">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="12462-352">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), que chama o [ConfigureNamedOptions\<TOptions>. Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) o método de extensão.</span><span class="sxs-lookup"><span data-stu-id="12462-352">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="12462-353">As opções nomeadas são sensíveis a maiúsculas e minúsculas.</span><span class="sxs-lookup"><span data-stu-id="12462-353">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="12462-354">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-354">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="12462-355">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="12462-355">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="12462-356">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="12462-356">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="12462-357">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="12462-357">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="12462-358">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="12462-358">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="12462-359">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="12462-359">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="12462-360">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="12462-360">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="12462-361">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="12462-361">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="12462-362">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="12462-362">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="12462-363">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="12462-363">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="12462-364">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="12462-364">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="12462-365">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="12462-365">All options are named instances.</span></span> <span data-ttu-id="12462-366">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="12462-366">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="12462-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="12462-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="12462-368">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="12462-368">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="12462-369">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="12462-369">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="12462-370">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="12462-370">OptionsBuilder API</span></span>

<span data-ttu-id="12462-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="12462-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="12462-372">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="12462-372">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="12462-373">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12462-373">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="12462-374">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="12462-374">Use DI services to configure options</span></span>

<span data-ttu-id="12462-375">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="12462-375">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="12462-376">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="12462-376">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="12462-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="12462-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="12462-378">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-378">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="12462-379">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="12462-379">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="12462-380">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="12462-380">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="12462-381">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="12462-381">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="12462-382">Validação de opções</span><span class="sxs-lookup"><span data-stu-id="12462-382">Options validation</span></span>

<span data-ttu-id="12462-383">A validação de opções permite validar as opções depois de configurá-las.</span><span class="sxs-lookup"><span data-stu-id="12462-383">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="12462-384">Chame `Validate` com um método de validação que retorna `true` quando as opções são válidas, e `false` quando não são:</span><span class="sxs-lookup"><span data-stu-id="12462-384">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="12462-385">O exemplo anterior define a instância de opções nomeadas como `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="12462-385">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="12462-386">A instância de opções padrão é `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="12462-386">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="12462-387">A validação é executada após a criação da instância de opções.</span><span class="sxs-lookup"><span data-stu-id="12462-387">Validation runs when the options instance is created.</span></span> <span data-ttu-id="12462-388">Uma instância de opções é garantida para passar validação na primeira vez que é acessada.</span><span class="sxs-lookup"><span data-stu-id="12462-388">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="12462-389">A validação de opções não protege contra modificações de opções após a criação da instância de opções.</span><span class="sxs-lookup"><span data-stu-id="12462-389">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="12462-390">Por exemplo, `IOptionsSnapshot` as opções são criadas e validadas uma vez por solicitação quando as opções são acessadas pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="12462-390">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="12462-391">As `IOptionsSnapshot` opções não são validadas novamente em tentativas de acesso subseqüentes *para a mesma solicitação*.</span><span class="sxs-lookup"><span data-stu-id="12462-391">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="12462-392">O método `Validate` aceita um `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="12462-392">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="12462-393">Para personalizar totalmente a validação, implemente `IValidateOptions<TOptions>`, que permite:</span><span class="sxs-lookup"><span data-stu-id="12462-393">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="12462-394">A validação de vários tipos de opções: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="12462-394">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="12462-395">A validação que depende de outro tipo de opção: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="12462-395">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="12462-396">`IValidateOptions` valida:</span><span class="sxs-lookup"><span data-stu-id="12462-396">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="12462-397">Uma instância específica de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="12462-397">A specific named options instance.</span></span>
* <span data-ttu-id="12462-398">Todas as opções quando `name` for `null`.</span><span class="sxs-lookup"><span data-stu-id="12462-398">All options when `name` is `null`.</span></span>

<span data-ttu-id="12462-399">Retornar um `ValidateOptionsResult` da implementação da interface:</span><span class="sxs-lookup"><span data-stu-id="12462-399">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="12462-400">A validação de anotação de dados está disponível no pacote [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) por meio da chamada do método <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> em `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="12462-400">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="12462-401">`Microsoft.Extensions.Options.DataAnnotations`está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="12462-401">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="12462-402">A validação adiantada (fail fast na inicialização) está sendo considerada para uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="12462-402">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="12462-403">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="12462-403">Options post-configuration</span></span>

<span data-ttu-id="12462-404">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="12462-404">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="12462-405">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="12462-405">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="12462-406">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="12462-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="12462-407">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="12462-407">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="12462-408">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="12462-408">Accessing options during startup</span></span>

<span data-ttu-id="12462-409"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="12462-409"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="12462-410">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="12462-410">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="12462-411">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-411">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="12462-412">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="12462-412">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="12462-413">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="12462-413">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="12462-414">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="12462-414">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="12462-415">[Separação de preocupações](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes ou acopladas umas às outras.</span><span class="sxs-lookup"><span data-stu-id="12462-415">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="12462-416">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="12462-416">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="12462-417">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="12462-417">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="12462-418">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="12462-418">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="12462-419">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="12462-419">Prerequisites</span></span>

<span data-ttu-id="12462-420">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="12462-420">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="12462-421">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="12462-421">Options interfaces</span></span>

<span data-ttu-id="12462-422">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="12462-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="12462-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> oferece suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="12462-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="12462-424">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="12462-424">Change notifications</span></span>
* [<span data-ttu-id="12462-425">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="12462-425">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="12462-426">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="12462-426">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="12462-427">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="12462-427">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="12462-428">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="12462-428">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="12462-429">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="12462-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="12462-430">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="12462-430">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="12462-431">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="12462-431">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="12462-432">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="12462-432">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="12462-433">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="12462-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="12462-434">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="12462-434">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="12462-435">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="12462-435">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="12462-436">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="12462-436">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="12462-437">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="12462-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="12462-438">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="12462-438">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="12462-439">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="12462-439"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="12462-440">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="12462-440">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="12462-441">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="12462-441">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="12462-442">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="12462-442">General options configuration</span></span>

<span data-ttu-id="12462-443">A configuração de opções gerais é demonstrada no Exemplo 1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="12462-443">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="12462-444">Uma classe de opções deve ser não abstrata com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="12462-444">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="12462-445">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="12462-445">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="12462-446">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="12462-446">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="12462-447">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-447">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="12462-448">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="12462-448">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="12462-449">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-449">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="12462-450">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="12462-450">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="12462-451">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="12462-451">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="12462-452">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="12462-452">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="12462-453">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="12462-453">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="12462-454">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="12462-454">Configure simple options with a delegate</span></span>

<span data-ttu-id="12462-455">A configuração de opções simples com um delegado é demonstrada como o Exemplo 2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="12462-455">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="12462-456">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="12462-456">Use a delegate to set options values.</span></span> <span data-ttu-id="12462-457">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-457">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="12462-458">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-458">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="12462-459">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="12462-459">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="12462-460">*Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="12462-460">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="12462-461">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="12462-461">You can add multiple configuration providers.</span></span> <span data-ttu-id="12462-462">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="12462-462">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="12462-463">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="12462-463">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="12462-464">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-464">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="12462-465">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="12462-465">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="12462-466">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="12462-466">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="12462-467">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="12462-467">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="12462-468">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="12462-468">Suboptions configuration</span></span>

<span data-ttu-id="12462-469">A configuração de subopções básicas é demonstrada como o Exemplo 3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="12462-469">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="12462-470">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12462-470">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="12462-471">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="12462-471">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="12462-472">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração do formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="12462-472">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="12462-473">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="12462-473">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="12462-474">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-474">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="12462-475">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="12462-475">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="12462-476">O `GetSection` método <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> requer o namespace.</span><span class="sxs-lookup"><span data-stu-id="12462-476">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="12462-477">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="12462-477">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="12462-478">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-478">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="12462-479">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-479">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="12462-480">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="12462-480">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="12462-481">Opções fornecidas por um modelo de exibição ou com a injeção de exibição direta</span><span class="sxs-lookup"><span data-stu-id="12462-481">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="12462-482">As opções fornecidas por um modelo de exibição ou com a injeção de exibição direta são demonstradas como o Exemplo 4 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="12462-482">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="12462-483">As opções podem ser fornecidas em um modelo de exibição ou pela injeção de <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> diretamente em uma exibição (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-483">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="12462-484">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="12462-484">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="12462-485">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="12462-485">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="12462-487">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="12462-487">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="12462-488">A recarga <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> de dados de configuração com é demonstrada no Exemplo 5 no aplicativo de amostra.</span><span class="sxs-lookup"><span data-stu-id="12462-488">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="12462-489">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dá suporte a opções de recarregamento com sobrecarga mínima de processamento.</span><span class="sxs-lookup"><span data-stu-id="12462-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="12462-490">As opções são calculadas uma vez por solicitação, quando acessadas e armazenadas em cache durante o tempo de vida da solicitação.</span><span class="sxs-lookup"><span data-stu-id="12462-490">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="12462-491">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="12462-491">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="12462-492">Várias solicitações ao servidor retornam valores de constante fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="12462-492">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="12462-493">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="12462-493">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="12462-494">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="12462-494">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="12462-495">Salvar o arquivo *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="12462-495">Save the *appsettings.json* file.</span></span> <span data-ttu-id="12462-496">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="12462-496">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="12462-497">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="12462-497">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="12462-498">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo 6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="12462-498">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="12462-499">O suporte de opções nomeadas permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="12462-499">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="12462-500">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), que chama o [ConfigureNamedOptions\<TOptions>. Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) o método de extensão.</span><span class="sxs-lookup"><span data-stu-id="12462-500">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="12462-501">As opções nomeadas são sensíveis a maiúsculas e minúsculas.</span><span class="sxs-lookup"><span data-stu-id="12462-501">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="12462-502">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="12462-502">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="12462-503">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="12462-503">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="12462-504">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="12462-504">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="12462-505">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="12462-505">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="12462-506">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="12462-506">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="12462-507">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="12462-507">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="12462-508">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="12462-508">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="12462-509">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="12462-509">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="12462-510">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="12462-510">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="12462-511">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="12462-511">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="12462-512">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="12462-512">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="12462-513">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="12462-513">All options are named instances.</span></span> <span data-ttu-id="12462-514">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="12462-514">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="12462-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="12462-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="12462-516">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="12462-516">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="12462-517">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="12462-517">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="12462-518">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="12462-518">OptionsBuilder API</span></span>

<span data-ttu-id="12462-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="12462-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="12462-520">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="12462-520">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="12462-521">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="12462-521">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="12462-522">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="12462-522">Use DI services to configure options</span></span>

<span data-ttu-id="12462-523">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="12462-523">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="12462-524">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="12462-524">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="12462-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="12462-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="12462-526">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-526">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="12462-527">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="12462-527">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="12462-528">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="12462-528">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="12462-529">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="12462-529">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="12462-530">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="12462-530">Options post-configuration</span></span>

<span data-ttu-id="12462-531">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="12462-531">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="12462-532">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="12462-532">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="12462-533">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="12462-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="12462-534">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="12462-534">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="12462-535">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="12462-535">Accessing options during startup</span></span>

<span data-ttu-id="12462-536"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="12462-536"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="12462-537">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="12462-537">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="12462-538">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="12462-538">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="12462-539">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="12462-539">Additional resources</span></span>

* <xref:fundamentals/configuration/index>

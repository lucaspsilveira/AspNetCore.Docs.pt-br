---
title: Detectar alterações com tokens de alteração no ASP.NET Core
author: rick-anderson
description: Saiba como usar tokens de alteração para controlar alterações.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/change-tokens
ms.openlocfilehash: f581e26b0d11923d9cf099700c4338c99e68bb59
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106605"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="0819a-103">Detectar alterações com tokens de alteração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0819a-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0819a-104">Um *token de alteração* é um bloco de construção de uso geral e de baixo nível, usado para controlar as alterações de estado.</span><span class="sxs-lookup"><span data-stu-id="0819a-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="0819a-105">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0819a-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="0819a-106">Interface IChangeToken</span><span class="sxs-lookup"><span data-stu-id="0819a-106">IChangeToken interface</span></span>

<span data-ttu-id="0819a-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propaga notificações de que ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="0819a-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="0819a-108">`IChangeToken` reside no namespace <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="0819a-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="0819a-109">O pacote NuGet [Microsoft. Extensions. primitivas](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) é fornecido implicitamente para os aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0819a-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="0819a-110">`IChangeToken` tem duas propriedades:</span><span class="sxs-lookup"><span data-stu-id="0819a-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="0819a-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica se o token de forma proativa gera retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="0819a-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="0819a-112">Se `ActiveChangedCallbacks` é definido como `false`, um retorno de chamada nunca é chamado e o aplicativo precisa sondar `HasChanged` em busca de alterações.</span><span class="sxs-lookup"><span data-stu-id="0819a-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="0819a-113">Também é possível que um token nunca seja cancelado se não ocorrerem alterações ou o ouvinte de alteração subjacente for removido ou desabilitado.</span><span class="sxs-lookup"><span data-stu-id="0819a-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="0819a-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> recebe um valor que indica se uma alteração ocorreu.</span><span class="sxs-lookup"><span data-stu-id="0819a-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="0819a-115">A `IChangeToken` interface inclui o método [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , que registra um retorno de chamada que é invocado quando o token é alterado.</span><span class="sxs-lookup"><span data-stu-id="0819a-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="0819a-116">`HasChanged` precisa ser definido antes de o retorno de chamada ser invocado.</span><span class="sxs-lookup"><span data-stu-id="0819a-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="0819a-117">Classe ChangeToken</span><span class="sxs-lookup"><span data-stu-id="0819a-117">ChangeToken class</span></span>

<span data-ttu-id="0819a-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> é uma classe estática usada para propagar notificações de que ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="0819a-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="0819a-119">`ChangeToken` reside no namespace <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="0819a-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="0819a-120">O pacote NuGet [Microsoft. Extensions. primitivas](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) é fornecido implicitamente para os aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0819a-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="0819a-121">O método [changeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra um `Action` para chamar sempre que o token for alterado:</span><span class="sxs-lookup"><span data-stu-id="0819a-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="0819a-122">`Func<IChangeToken>` produz o token.</span><span class="sxs-lookup"><span data-stu-id="0819a-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="0819a-123">`Action` é chamado quando o token é alterado.</span><span class="sxs-lookup"><span data-stu-id="0819a-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="0819a-124">A sobrecarga [changeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) usa um `TState` parâmetro adicional que é passado para o consumidor do token `Action` .</span><span class="sxs-lookup"><span data-stu-id="0819a-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="0819a-125">`OnChange` retorna um <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="0819a-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="0819a-126">A chamada <xref:System.IDisposable.Dispose*> interrompe a escuta do token de outras alterações e libera os recursos do token.</span><span class="sxs-lookup"><span data-stu-id="0819a-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="0819a-127">Usos de exemplo de tokens de alteração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0819a-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="0819a-128">Os tokens de alteração são usados nas áreas proeminentes do ASP.NET Core para monitorar alterações em objetos:</span><span class="sxs-lookup"><span data-stu-id="0819a-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="0819a-129">Para monitorar as alterações em arquivos, o método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> cria um `IChangeToken` para os arquivos especificados ou para pasta a ser inspecionada.</span><span class="sxs-lookup"><span data-stu-id="0819a-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="0819a-130">Tokens `IChangeToken` podem ser adicionados a entradas de cache para disparar remoções do cache após as alterações.</span><span class="sxs-lookup"><span data-stu-id="0819a-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="0819a-131">Para as alterações de `TOptions`, a implementação <xref:Microsoft.Extensions.Options.OptionsMonitor`1> padrão de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> tem uma sobrecarga que aceita uma ou mais instâncias <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="0819a-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="0819a-132">Cada instância retorna um `IChangeToken` para registrar um retorno de chamada de notificação de alteração para o controle de alterações de opções.</span><span class="sxs-lookup"><span data-stu-id="0819a-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="0819a-133">Monitorar as alterações de configuração</span><span class="sxs-lookup"><span data-stu-id="0819a-133">Monitor for configuration changes</span></span>

<span data-ttu-id="0819a-134">Por padrão, os modelos do ASP.NET Core usam [arquivos de configuração JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* e *appsettings.Production.json*) para carregar as definições de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0819a-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="0819a-135">Esses arquivos são configurados com o método de extensão [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) no <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> que aceita um parâmetro `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="0819a-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="0819a-136">`reloadOnChange` indica se a configuração deve ser recarregada após alterações de arquivo.</span><span class="sxs-lookup"><span data-stu-id="0819a-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="0819a-137">Essa configuração é exibida no método de conveniência <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> de <xref:Microsoft.Extensions.Hosting.Host>:</span><span class="sxs-lookup"><span data-stu-id="0819a-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="0819a-138">A configuração baseada em arquivo é representada por <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="0819a-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="0819a-139">`FileConfigurationSource` usa <xref:Microsoft.Extensions.FileProviders.IFileProvider> para monitorar arquivos.</span><span class="sxs-lookup"><span data-stu-id="0819a-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="0819a-140">Por padrão, o `IFileMonitor` é fornecido por um <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, que usa <xref:System.IO.FileSystemWatcher> para monitorar as alterações do arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="0819a-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="0819a-141">O aplicativo de exemplo demonstra duas implementações para monitorar as alterações de configuração.</span><span class="sxs-lookup"><span data-stu-id="0819a-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="0819a-142">Se qualquer um dos arquivos *appsettings* forem alterados, ambas as implementações de monitoramento de arquivo executam código personalizado&mdash;o aplicativo de exemplo grava uma mensagem no console.</span><span class="sxs-lookup"><span data-stu-id="0819a-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="0819a-143">O `FileSystemWatcher` de um arquivo de configuração pode disparar vários retornos de chamada de token para uma única alteração de arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="0819a-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="0819a-144">Para garantir que o código personalizado é executado somente depois que os vários retornos de chamada de token são acionados, a implementação da amostra verifica os hashes do arquivo.</span><span class="sxs-lookup"><span data-stu-id="0819a-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="0819a-145">O exemplo usa o hash de arquivo SHA1.</span><span class="sxs-lookup"><span data-stu-id="0819a-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="0819a-146">Uma nova tentativa é implementada com uma retirada exponencial.</span><span class="sxs-lookup"><span data-stu-id="0819a-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="0819a-147">A nova tentativa está presente porque o bloqueio de arquivo pode ocorrer, o que impede temporariamente a computação de um novo hash em um arquivo.</span><span class="sxs-lookup"><span data-stu-id="0819a-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="0819a-148">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="0819a-148">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="0819a-149">Token de alteração de inicialização simples</span><span class="sxs-lookup"><span data-stu-id="0819a-149">Simple startup change token</span></span>

<span data-ttu-id="0819a-150">Registre um retorno de chamada `Action` do consumidor de token para notificações de alteração no token de recarregamento de configuração.</span><span class="sxs-lookup"><span data-stu-id="0819a-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="0819a-151">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0819a-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="0819a-152">`config.GetReloadToken()` fornece o token.</span><span class="sxs-lookup"><span data-stu-id="0819a-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="0819a-153">O retorno de chamada é o método `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="0819a-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="0819a-154">O `state` do retorno de chamada é usado para passar no `IWebHostEnvironment`, que é útil para especificar o arquivo de configuração *appsettings* correto para monitorar (por exemplo, *appsettings.Development.JSON* quando estiver no Ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="0819a-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="0819a-155">Hashes de arquivo são usados para impedir que a instrução `WriteConsole` seja executada várias vezes, devido a vários retornos de chamada de token quando o arquivo de configuração é alterado somente uma vez.</span><span class="sxs-lookup"><span data-stu-id="0819a-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="0819a-156">Esse sistema é executado, desde que o aplicativo esteja em execução e não possa ser desabilitado pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="0819a-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="0819a-157">Monitorar alterações de configuração como um serviço</span><span class="sxs-lookup"><span data-stu-id="0819a-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="0819a-158">A amostra implementa:</span><span class="sxs-lookup"><span data-stu-id="0819a-158">The sample implements:</span></span>

* <span data-ttu-id="0819a-159">Monitoramento de token de inicialização básica.</span><span class="sxs-lookup"><span data-stu-id="0819a-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="0819a-160">Monitoramento como serviço.</span><span class="sxs-lookup"><span data-stu-id="0819a-160">Monitoring as a service.</span></span>
* <span data-ttu-id="0819a-161">Um mecanismo para habilitar e desabilitar o monitoramento.</span><span class="sxs-lookup"><span data-stu-id="0819a-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="0819a-162">A amostra estabelece uma interface `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="0819a-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="0819a-163">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="0819a-163">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="0819a-164">O construtor da classe implementada, `ConfigurationMonitor`, registra um retorno de chamada para notificações de alteração:</span><span class="sxs-lookup"><span data-stu-id="0819a-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="0819a-165">`config.GetReloadToken()` fornece o token.</span><span class="sxs-lookup"><span data-stu-id="0819a-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="0819a-166">`InvokeChanged` é o método de retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="0819a-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="0819a-167">O `state` nesta instância é uma referência à instância `IConfigurationMonitor` que é usada para acessar o estado de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="0819a-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="0819a-168">Duas propriedades são usadas:</span><span class="sxs-lookup"><span data-stu-id="0819a-168">Two properties are used:</span></span>

* <span data-ttu-id="0819a-169">`MonitoringEnabled`: Indica se o retorno de chamada deve executar seu código personalizado.</span><span class="sxs-lookup"><span data-stu-id="0819a-169">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="0819a-170">`CurrentState`: Descreve o estado de monitoramento atual para uso na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="0819a-170">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="0819a-171">O método `InvokeChanged` é semelhante à abordagem anterior, exceto que ele:</span><span class="sxs-lookup"><span data-stu-id="0819a-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="0819a-172">Não executa o código, a menos que `MonitoringEnabled` seja `true`.</span><span class="sxs-lookup"><span data-stu-id="0819a-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="0819a-173">Gera saída do `state` atual em sua saída `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="0819a-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="0819a-174">Uma instância `ConfigurationMonitor` é registrada como um serviço em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0819a-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="0819a-175">A página Índice oferece ao usuário o controle sobre o monitoramento de configuração.</span><span class="sxs-lookup"><span data-stu-id="0819a-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="0819a-176">A instância de `IConfigurationMonitor` é injetada no `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="0819a-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="0819a-177">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0819a-177">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="0819a-178">O monitor de configuração (`_monitor`) é usado para habilitar ou desabilitar o monitoramento e definir o estado atual de comentários de interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="0819a-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="0819a-179">Quando `OnPostStartMonitoring` é disparado, o monitoramento é habilitado e o estado atual é desmarcado.</span><span class="sxs-lookup"><span data-stu-id="0819a-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="0819a-180">Quando `OnPostStopMonitoring` é disparado, o monitoramento é desabilitado e o estado é definido para refletir que o monitoramento não está ocorrendo.</span><span class="sxs-lookup"><span data-stu-id="0819a-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="0819a-181">Os botões na interface do usuário habilitam e desabilitar o monitoramento.</span><span class="sxs-lookup"><span data-stu-id="0819a-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="0819a-182">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0819a-182">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="0819a-183">Monitor alterações de arquivos armazenados em cache</span><span class="sxs-lookup"><span data-stu-id="0819a-183">Monitor cached file changes</span></span>

<span data-ttu-id="0819a-184">O conteúdo do arquivo pode ser armazenado em cache em memória usando <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="0819a-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="0819a-185">O cache na memória é descrito no tópico [Cache na memória](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="0819a-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="0819a-186">Sem realizar etapas adicionais, como a implementação descrita abaixo, dados *obsoletos* (desatualizados) são retornados de um cache se os dados de origem são alterados.</span><span class="sxs-lookup"><span data-stu-id="0819a-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="0819a-187">Por exemplo, não levando em conta o status de um arquivo de origem armazenado em cache durante a renovação de um período de [expiração deslizante](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) resulta em dados de arquivo de cache obsoletos.</span><span class="sxs-lookup"><span data-stu-id="0819a-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="0819a-188">Cada solicitação de dados renova o período de expiração deslizante, mas o arquivo nunca é recarregado no cache.</span><span class="sxs-lookup"><span data-stu-id="0819a-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="0819a-189">Os recursos do aplicativo que usam o conteúdo armazenado em cache do arquivo estão sujeitos ao possível recebimento de conteúdo obsoleto.</span><span class="sxs-lookup"><span data-stu-id="0819a-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="0819a-190">O uso de tokens de alteração em um cenário de cache de arquivo impede a presença do conteúdo de arquivo obsoleto no cache.</span><span class="sxs-lookup"><span data-stu-id="0819a-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="0819a-191">O aplicativo de exemplo demonstra uma implementação da abordagem.</span><span class="sxs-lookup"><span data-stu-id="0819a-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="0819a-192">A amostra usa `GetFileContent` para:</span><span class="sxs-lookup"><span data-stu-id="0819a-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="0819a-193">Retornar o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="0819a-193">Return file content.</span></span>
* <span data-ttu-id="0819a-194">Implementar um algoritmo de repetição com retirada exponencial para abranger casos em que um bloqueio de arquivo impede temporariamente a leitura do arquivo.</span><span class="sxs-lookup"><span data-stu-id="0819a-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="0819a-195">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="0819a-195">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="0819a-196">Um `FileService` é criado para manipular pesquisas de arquivos armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="0819a-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="0819a-197">A chamada de método `GetFileContent` do serviço tenta obter o conteúdo do arquivo do cache em memória e retorná-lo para o chamador (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="0819a-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="0819a-198">Se o conteúdo armazenado em cache não é encontrado com a chave de cache, as seguintes ações são executadas:</span><span class="sxs-lookup"><span data-stu-id="0819a-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="0819a-199">O conteúdo do arquivo é obtido com `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="0819a-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="0819a-200">Um token de alteração é obtido do provedor de arquivo com [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="0819a-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="0819a-201">O retorno de chamada do token é disparado quando o arquivo é modificado.</span><span class="sxs-lookup"><span data-stu-id="0819a-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="0819a-202">O conteúdo do arquivo é armazenado em cache com um período de [expiração deslizante](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="0819a-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="0819a-203">O token de alteração é anexado com [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) para remover a entrada do cache se o arquivo é alterado enquanto ele é armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="0819a-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="0819a-204">No exemplo a seguir, os arquivos são armazenados na raiz do [conteúdo](xref:fundamentals/index#content-root)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0819a-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="0819a-205">`IWebHostEnvironment.ContentRootFileProvider`é usado para obter um <xref:Microsoft.Extensions.FileProviders.IFileProvider> apontador para o aplicativo `IWebHostEnvironment.ContentRootPath` .</span><span class="sxs-lookup"><span data-stu-id="0819a-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="0819a-206">O `filePath` é obtido com [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="0819a-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="0819a-207">O `FileService` é registrado no contêiner de serviço junto com o serviço de cache da memória.</span><span class="sxs-lookup"><span data-stu-id="0819a-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="0819a-208">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0819a-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="0819a-209">O modelo de página carrega o conteúdo do arquivo usando o serviço.</span><span class="sxs-lookup"><span data-stu-id="0819a-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="0819a-210">No método `OnGet` da página de índice (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="0819a-210">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="0819a-211">Classe CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="0819a-211">CompositeChangeToken class</span></span>

<span data-ttu-id="0819a-212">Para representar uma ou mais instâncias de `IChangeToken` em um único objeto, use a classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="0819a-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="0819a-213">`HasChanged` nos relatórios de token compostos `true` se um token representado `HasChanged` é `true`.</span><span class="sxs-lookup"><span data-stu-id="0819a-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="0819a-214">`ActiveChangeCallbacks` nos relatórios de token compostos `true` se um token representado `ActiveChangeCallbacks` é `true`.</span><span class="sxs-lookup"><span data-stu-id="0819a-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="0819a-215">Se ocorrerem vários eventos de alteração simultâneos, o retorno de chamada de alteração composto será invocado uma vez.</span><span class="sxs-lookup"><span data-stu-id="0819a-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0819a-216">Um *token de alteração* é um bloco de construção de uso geral e de baixo nível, usado para controlar as alterações de estado.</span><span class="sxs-lookup"><span data-stu-id="0819a-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="0819a-217">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0819a-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="0819a-218">Interface IChangeToken</span><span class="sxs-lookup"><span data-stu-id="0819a-218">IChangeToken interface</span></span>

<span data-ttu-id="0819a-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propaga notificações de que ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="0819a-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="0819a-220">`IChangeToken` reside no namespace <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="0819a-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="0819a-221">Para aplicativos que não usam o [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage-app), crie uma referência de pacote para o pacote NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="0819a-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="0819a-222">`IChangeToken` tem duas propriedades:</span><span class="sxs-lookup"><span data-stu-id="0819a-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="0819a-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica se o token de forma proativa gera retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="0819a-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="0819a-224">Se `ActiveChangedCallbacks` é definido como `false`, um retorno de chamada nunca é chamado e o aplicativo precisa sondar `HasChanged` em busca de alterações.</span><span class="sxs-lookup"><span data-stu-id="0819a-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="0819a-225">Também é possível que um token nunca seja cancelado se não ocorrerem alterações ou o ouvinte de alteração subjacente for removido ou desabilitado.</span><span class="sxs-lookup"><span data-stu-id="0819a-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="0819a-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> recebe um valor que indica se uma alteração ocorreu.</span><span class="sxs-lookup"><span data-stu-id="0819a-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="0819a-227">A `IChangeToken` interface inclui o método [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , que registra um retorno de chamada que é invocado quando o token é alterado.</span><span class="sxs-lookup"><span data-stu-id="0819a-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="0819a-228">`HasChanged` precisa ser definido antes de o retorno de chamada ser invocado.</span><span class="sxs-lookup"><span data-stu-id="0819a-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="0819a-229">Classe ChangeToken</span><span class="sxs-lookup"><span data-stu-id="0819a-229">ChangeToken class</span></span>

<span data-ttu-id="0819a-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> é uma classe estática usada para propagar notificações de que ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="0819a-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="0819a-231">`ChangeToken` reside no namespace <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="0819a-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="0819a-232">Para aplicativos que não usam o [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage-app), crie uma referência de pacote para o pacote NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="0819a-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="0819a-233">O método [changeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra um `Action` para chamar sempre que o token for alterado:</span><span class="sxs-lookup"><span data-stu-id="0819a-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="0819a-234">`Func<IChangeToken>` produz o token.</span><span class="sxs-lookup"><span data-stu-id="0819a-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="0819a-235">`Action` é chamado quando o token é alterado.</span><span class="sxs-lookup"><span data-stu-id="0819a-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="0819a-236">A sobrecarga [changeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) usa um `TState` parâmetro adicional que é passado para o consumidor do token `Action` .</span><span class="sxs-lookup"><span data-stu-id="0819a-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="0819a-237">`OnChange` retorna um <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="0819a-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="0819a-238">A chamada <xref:System.IDisposable.Dispose*> interrompe a escuta do token de outras alterações e libera os recursos do token.</span><span class="sxs-lookup"><span data-stu-id="0819a-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="0819a-239">Usos de exemplo de tokens de alteração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0819a-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="0819a-240">Os tokens de alteração são usados nas áreas proeminentes do ASP.NET Core para monitorar alterações em objetos:</span><span class="sxs-lookup"><span data-stu-id="0819a-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="0819a-241">Para monitorar as alterações em arquivos, o método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> cria um `IChangeToken` para os arquivos especificados ou para pasta a ser inspecionada.</span><span class="sxs-lookup"><span data-stu-id="0819a-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="0819a-242">Tokens `IChangeToken` podem ser adicionados a entradas de cache para disparar remoções do cache após as alterações.</span><span class="sxs-lookup"><span data-stu-id="0819a-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="0819a-243">Para as alterações de `TOptions`, a implementação <xref:Microsoft.Extensions.Options.OptionsMonitor`1> padrão de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> tem uma sobrecarga que aceita uma ou mais instâncias <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="0819a-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="0819a-244">Cada instância retorna um `IChangeToken` para registrar um retorno de chamada de notificação de alteração para o controle de alterações de opções.</span><span class="sxs-lookup"><span data-stu-id="0819a-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="0819a-245">Monitorar as alterações de configuração</span><span class="sxs-lookup"><span data-stu-id="0819a-245">Monitor for configuration changes</span></span>

<span data-ttu-id="0819a-246">Por padrão, os modelos do ASP.NET Core usam [arquivos de configuração JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* e *appsettings.Production.json*) para carregar as definições de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0819a-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="0819a-247">Esses arquivos são configurados com o método de extensão [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) no <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> que aceita um parâmetro `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="0819a-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="0819a-248">`reloadOnChange` indica se a configuração deve ser recarregada após alterações de arquivo.</span><span class="sxs-lookup"><span data-stu-id="0819a-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="0819a-249">Essa configuração é exibida no método de conveniência <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> de <xref:Microsoft.AspNetCore.WebHost>:</span><span class="sxs-lookup"><span data-stu-id="0819a-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="0819a-250">A configuração baseada em arquivo é representada por <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="0819a-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="0819a-251">`FileConfigurationSource` usa <xref:Microsoft.Extensions.FileProviders.IFileProvider> para monitorar arquivos.</span><span class="sxs-lookup"><span data-stu-id="0819a-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="0819a-252">Por padrão, o `IFileMonitor` é fornecido por um <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, que usa <xref:System.IO.FileSystemWatcher> para monitorar as alterações do arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="0819a-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="0819a-253">O aplicativo de exemplo demonstra duas implementações para monitorar as alterações de configuração.</span><span class="sxs-lookup"><span data-stu-id="0819a-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="0819a-254">Se qualquer um dos arquivos *appsettings* forem alterados, ambas as implementações de monitoramento de arquivo executam código personalizado&mdash;o aplicativo de exemplo grava uma mensagem no console.</span><span class="sxs-lookup"><span data-stu-id="0819a-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="0819a-255">O `FileSystemWatcher` de um arquivo de configuração pode disparar vários retornos de chamada de token para uma única alteração de arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="0819a-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="0819a-256">Para garantir que o código personalizado é executado somente depois que os vários retornos de chamada de token são acionados, a implementação da amostra verifica os hashes do arquivo.</span><span class="sxs-lookup"><span data-stu-id="0819a-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="0819a-257">O exemplo usa o hash de arquivo SHA1.</span><span class="sxs-lookup"><span data-stu-id="0819a-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="0819a-258">Uma nova tentativa é implementada com uma retirada exponencial.</span><span class="sxs-lookup"><span data-stu-id="0819a-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="0819a-259">A nova tentativa está presente porque o bloqueio de arquivo pode ocorrer, o que impede temporariamente a computação de um novo hash em um arquivo.</span><span class="sxs-lookup"><span data-stu-id="0819a-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="0819a-260">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="0819a-260">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="0819a-261">Token de alteração de inicialização simples</span><span class="sxs-lookup"><span data-stu-id="0819a-261">Simple startup change token</span></span>

<span data-ttu-id="0819a-262">Registre um retorno de chamada `Action` do consumidor de token para notificações de alteração no token de recarregamento de configuração.</span><span class="sxs-lookup"><span data-stu-id="0819a-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="0819a-263">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0819a-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="0819a-264">`config.GetReloadToken()` fornece o token.</span><span class="sxs-lookup"><span data-stu-id="0819a-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="0819a-265">O retorno de chamada é o método `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="0819a-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="0819a-266">O `state` do retorno de chamada é usado para passar no `IHostingEnvironment`, que é útil para especificar o arquivo de configuração *appsettings* correto para monitorar (por exemplo, *appsettings.Development.JSON* quando estiver no Ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="0819a-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="0819a-267">Hashes de arquivo são usados para impedir que a instrução `WriteConsole` seja executada várias vezes, devido a vários retornos de chamada de token quando o arquivo de configuração é alterado somente uma vez.</span><span class="sxs-lookup"><span data-stu-id="0819a-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="0819a-268">Esse sistema é executado, desde que o aplicativo esteja em execução e não possa ser desabilitado pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="0819a-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="0819a-269">Monitorar alterações de configuração como um serviço</span><span class="sxs-lookup"><span data-stu-id="0819a-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="0819a-270">A amostra implementa:</span><span class="sxs-lookup"><span data-stu-id="0819a-270">The sample implements:</span></span>

* <span data-ttu-id="0819a-271">Monitoramento de token de inicialização básica.</span><span class="sxs-lookup"><span data-stu-id="0819a-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="0819a-272">Monitoramento como serviço.</span><span class="sxs-lookup"><span data-stu-id="0819a-272">Monitoring as a service.</span></span>
* <span data-ttu-id="0819a-273">Um mecanismo para habilitar e desabilitar o monitoramento.</span><span class="sxs-lookup"><span data-stu-id="0819a-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="0819a-274">A amostra estabelece uma interface `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="0819a-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="0819a-275">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="0819a-275">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="0819a-276">O construtor da classe implementada, `ConfigurationMonitor`, registra um retorno de chamada para notificações de alteração:</span><span class="sxs-lookup"><span data-stu-id="0819a-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="0819a-277">`config.GetReloadToken()` fornece o token.</span><span class="sxs-lookup"><span data-stu-id="0819a-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="0819a-278">`InvokeChanged` é o método de retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="0819a-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="0819a-279">O `state` nesta instância é uma referência à instância `IConfigurationMonitor` que é usada para acessar o estado de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="0819a-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="0819a-280">Duas propriedades são usadas:</span><span class="sxs-lookup"><span data-stu-id="0819a-280">Two properties are used:</span></span>

* <span data-ttu-id="0819a-281">`MonitoringEnabled`: Indica se o retorno de chamada deve executar seu código personalizado.</span><span class="sxs-lookup"><span data-stu-id="0819a-281">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="0819a-282">`CurrentState`: Descreve o estado de monitoramento atual para uso na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="0819a-282">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="0819a-283">O método `InvokeChanged` é semelhante à abordagem anterior, exceto que ele:</span><span class="sxs-lookup"><span data-stu-id="0819a-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="0819a-284">Não executa o código, a menos que `MonitoringEnabled` seja `true`.</span><span class="sxs-lookup"><span data-stu-id="0819a-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="0819a-285">Gera saída do `state` atual em sua saída `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="0819a-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="0819a-286">Uma instância `ConfigurationMonitor` é registrada como um serviço em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0819a-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="0819a-287">A página Índice oferece ao usuário o controle sobre o monitoramento de configuração.</span><span class="sxs-lookup"><span data-stu-id="0819a-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="0819a-288">A instância de `IConfigurationMonitor` é injetada no `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="0819a-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="0819a-289">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0819a-289">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="0819a-290">O monitor de configuração (`_monitor`) é usado para habilitar ou desabilitar o monitoramento e definir o estado atual de comentários de interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="0819a-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="0819a-291">Quando `OnPostStartMonitoring` é disparado, o monitoramento é habilitado e o estado atual é desmarcado.</span><span class="sxs-lookup"><span data-stu-id="0819a-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="0819a-292">Quando `OnPostStopMonitoring` é disparado, o monitoramento é desabilitado e o estado é definido para refletir que o monitoramento não está ocorrendo.</span><span class="sxs-lookup"><span data-stu-id="0819a-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="0819a-293">Os botões na interface do usuário habilitam e desabilitar o monitoramento.</span><span class="sxs-lookup"><span data-stu-id="0819a-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="0819a-294">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0819a-294">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="0819a-295">Monitor alterações de arquivos armazenados em cache</span><span class="sxs-lookup"><span data-stu-id="0819a-295">Monitor cached file changes</span></span>

<span data-ttu-id="0819a-296">O conteúdo do arquivo pode ser armazenado em cache em memória usando <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="0819a-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="0819a-297">O cache na memória é descrito no tópico [Cache na memória](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="0819a-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="0819a-298">Sem realizar etapas adicionais, como a implementação descrita abaixo, dados *obsoletos* (desatualizados) são retornados de um cache se os dados de origem são alterados.</span><span class="sxs-lookup"><span data-stu-id="0819a-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="0819a-299">Por exemplo, não levando em conta o status de um arquivo de origem armazenado em cache durante a renovação de um período de [expiração deslizante](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) resulta em dados de arquivo de cache obsoletos.</span><span class="sxs-lookup"><span data-stu-id="0819a-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="0819a-300">Cada solicitação de dados renova o período de expiração deslizante, mas o arquivo nunca é recarregado no cache.</span><span class="sxs-lookup"><span data-stu-id="0819a-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="0819a-301">Os recursos do aplicativo que usam o conteúdo armazenado em cache do arquivo estão sujeitos ao possível recebimento de conteúdo obsoleto.</span><span class="sxs-lookup"><span data-stu-id="0819a-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="0819a-302">O uso de tokens de alteração em um cenário de cache de arquivo impede a presença do conteúdo de arquivo obsoleto no cache.</span><span class="sxs-lookup"><span data-stu-id="0819a-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="0819a-303">O aplicativo de exemplo demonstra uma implementação da abordagem.</span><span class="sxs-lookup"><span data-stu-id="0819a-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="0819a-304">A amostra usa `GetFileContent` para:</span><span class="sxs-lookup"><span data-stu-id="0819a-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="0819a-305">Retornar o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="0819a-305">Return file content.</span></span>
* <span data-ttu-id="0819a-306">Implementar um algoritmo de repetição com retirada exponencial para abranger casos em que um bloqueio de arquivo impede temporariamente a leitura do arquivo.</span><span class="sxs-lookup"><span data-stu-id="0819a-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="0819a-307">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="0819a-307">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="0819a-308">Um `FileService` é criado para manipular pesquisas de arquivos armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="0819a-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="0819a-309">A chamada de método `GetFileContent` do serviço tenta obter o conteúdo do arquivo do cache em memória e retorná-lo para o chamador (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="0819a-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="0819a-310">Se o conteúdo armazenado em cache não é encontrado com a chave de cache, as seguintes ações são executadas:</span><span class="sxs-lookup"><span data-stu-id="0819a-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="0819a-311">O conteúdo do arquivo é obtido com `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="0819a-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="0819a-312">Um token de alteração é obtido do provedor de arquivo com [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="0819a-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="0819a-313">O retorno de chamada do token é disparado quando o arquivo é modificado.</span><span class="sxs-lookup"><span data-stu-id="0819a-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="0819a-314">O conteúdo do arquivo é armazenado em cache com um período de [expiração deslizante](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="0819a-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="0819a-315">O token de alteração é anexado com [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) para remover a entrada do cache se o arquivo é alterado enquanto ele é armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="0819a-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="0819a-316">No exemplo a seguir, os arquivos são armazenados na raiz do [conteúdo](xref:fundamentals/index#content-root)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0819a-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="0819a-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) é usado para obter <xref:Microsoft.Extensions.FileProviders.IFileProvider> apontando para <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0819a-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="0819a-318">O `filePath` é obtido com [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="0819a-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="0819a-319">O `FileService` é registrado no contêiner de serviço junto com o serviço de cache da memória.</span><span class="sxs-lookup"><span data-stu-id="0819a-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="0819a-320">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0819a-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="0819a-321">O modelo de página carrega o conteúdo do arquivo usando o serviço.</span><span class="sxs-lookup"><span data-stu-id="0819a-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="0819a-322">No método `OnGet` da página de índice (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="0819a-322">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="0819a-323">Classe CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="0819a-323">CompositeChangeToken class</span></span>

<span data-ttu-id="0819a-324">Para representar uma ou mais instâncias de `IChangeToken` em um único objeto, use a classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="0819a-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="0819a-325">`HasChanged` nos relatórios de token compostos `true` se um token representado `HasChanged` é `true`.</span><span class="sxs-lookup"><span data-stu-id="0819a-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="0819a-326">`ActiveChangeCallbacks` nos relatórios de token compostos `true` se um token representado `ActiveChangeCallbacks` é `true`.</span><span class="sxs-lookup"><span data-stu-id="0819a-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="0819a-327">Se ocorrerem vários eventos de alteração simultâneos, o retorno de chamada de alteração composto será invocado uma vez.</span><span class="sxs-lookup"><span data-stu-id="0819a-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0819a-328">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0819a-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

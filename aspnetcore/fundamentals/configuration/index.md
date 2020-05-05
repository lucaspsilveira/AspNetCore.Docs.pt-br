---
title: Configuração no ASP.NET Core
author: rick-anderson
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/index
ms.openlocfilehash: c2a7ef9c1523bc179524f328905f3a4b1460a1a5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774490"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="5db0b-103">Configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5db0b-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="5db0b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="5db0b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5db0b-105">A configuração no ASP.NET Core é executada usando um ou mais [provedores de configuração](#cp).</span><span class="sxs-lookup"><span data-stu-id="5db0b-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="5db0b-106">Os provedores de configuração lêem dados de configuração de pares chave-valor usando uma variedade de fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="5db0b-107">Arquivos de configurações, como *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="5db0b-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="5db0b-108">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="5db0b-108">Environment variables</span></span>
* <span data-ttu-id="5db0b-109">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="5db0b-109">Azure Key Vault</span></span>
* <span data-ttu-id="5db0b-110">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="5db0b-110">Azure App Configuration</span></span>
* <span data-ttu-id="5db0b-111">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="5db0b-111">Command-line arguments</span></span>
* <span data-ttu-id="5db0b-112">Provedores personalizados, instalados ou criados</span><span class="sxs-lookup"><span data-stu-id="5db0b-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="5db0b-113">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="5db0b-113">Directory files</span></span>
* <span data-ttu-id="5db0b-114">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="5db0b-114">In-memory .NET objects</span></span>

<span data-ttu-id="5db0b-115">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5db0b-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="5db0b-116">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="5db0b-116">Default configuration</span></span>

<span data-ttu-id="5db0b-117">ASP.NET Core aplicativos Web criados com [dotnet novo](/dotnet/core/tools/dotnet-new) ou o Visual Studio geram o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="5db0b-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="5db0b-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="5db0b-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="5db0b-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Adiciona um existente `IConfiguration` como uma origem.</span><span class="sxs-lookup"><span data-stu-id="5db0b-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="5db0b-120">No caso de configuração padrão, o adiciona a configuração do [host](#hvac) e a define como a primeira origem para a configuração do _aplicativo_ .</span><span class="sxs-lookup"><span data-stu-id="5db0b-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="5db0b-121">[appSettings. JSON](#appsettingsjson) usando o [provedor de configuração JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5db0b-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="5db0b-122">*appSettings.* `Environment` *. JSON* usando o [provedor de configuração JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5db0b-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="5db0b-123">Por exemplo, *appSettings*. ***Produção***. *JSON* e *appSettings*. ***Desenvolvimento***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="5db0b-124">[Segredos do aplicativo](xref:security/app-secrets) quando o aplicativo é executado `Development` no ambiente.</span><span class="sxs-lookup"><span data-stu-id="5db0b-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="5db0b-125">Variáveis de ambiente usando o [provedor de configuração de variáveis de ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="5db0b-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="5db0b-126">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line).</span><span class="sxs-lookup"><span data-stu-id="5db0b-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="5db0b-127">Os provedores de configuração adicionados posteriormente substituem as configurações de chave anteriores.</span><span class="sxs-lookup"><span data-stu-id="5db0b-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="5db0b-128">Por exemplo, se `MyKey` for definido em *appSettings. JSON* e no ambiente, o valor de ambiente será usado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="5db0b-129">Usando os provedores de configuração padrão, o [provedor de configuração de linha de comando](#command-line-configuration-provider) substitui todos os outros provedores.</span><span class="sxs-lookup"><span data-stu-id="5db0b-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="5db0b-130">Para obter mais informações `CreateDefaultBuilder`sobre o, consulte [configurações padrão do Construtor](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="5db0b-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="5db0b-131">O código a seguir exibe os provedores de configuração habilitados na ordem em que foram adicionados:</span><span class="sxs-lookup"><span data-stu-id="5db0b-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="5db0b-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="5db0b-132">appsettings.json</span></span>

<span data-ttu-id="5db0b-133">Considere o seguinte arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="5db0b-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="5db0b-134">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="5db0b-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-135">O padrão <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="5db0b-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="5db0b-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="5db0b-136">*appsettings.json*</span></span>
1. <span data-ttu-id="5db0b-137">*appSettings.* `Environment` *. JSON* : por exemplo, *appSettings*. ***Produção***. *JSON* e *appSettings*. ***Desenvolvimento***. arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="5db0b-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="5db0b-138">A versão de ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="5db0b-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="5db0b-139">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5db0b-140">*appSettings*. `Environment`. valores *JSON* substituem chaves em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="5db0b-141">Por exemplo, por padrão:</span><span class="sxs-lookup"><span data-stu-id="5db0b-141">For example, by default:</span></span>

* <span data-ttu-id="5db0b-142">Em desenvolvimento, *appSettings*. ***Desenvolvimento***. a configuração *JSON* substitui os valores encontrados em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="5db0b-143">Em produção, *appSettings*. ***Produção***. a configuração *JSON* substitui os valores encontrados em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="5db0b-144">Por exemplo, ao implantar o aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="5db0b-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="5db0b-145">Associar dados de configuração hierárquica usando o padrão de opções</span><span class="sxs-lookup"><span data-stu-id="5db0b-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="5db0b-146">A maneira preferida de ler valores de configuração relacionados é usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="5db0b-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="5db0b-147">Por exemplo, para ler os seguintes valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="5db0b-148">Crie a seguinte `PositionOptions` classe:</span><span class="sxs-lookup"><span data-stu-id="5db0b-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="5db0b-149">Todas as propriedades públicas de leitura/gravação do tipo estão associadas.</span><span class="sxs-lookup"><span data-stu-id="5db0b-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="5db0b-150">Os campos ***não*** estão associados.</span><span class="sxs-lookup"><span data-stu-id="5db0b-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="5db0b-151">O seguinte código:</span><span class="sxs-lookup"><span data-stu-id="5db0b-151">The following code:</span></span>

* <span data-ttu-id="5db0b-152">Chama [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) para associar a `PositionOptions` classe à `Position` seção.</span><span class="sxs-lookup"><span data-stu-id="5db0b-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="5db0b-153">Exibe os `Position` dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="5db0b-155">`ConfigurationBinder.Get<T>`pode ser mais conveniente do que `ConfigurationBinder.Bind`usar o.</span><span class="sxs-lookup"><span data-stu-id="5db0b-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="5db0b-156">O código a seguir mostra como usar `ConfigurationBinder.Get<T>` com a `PositionOptions` classe:</span><span class="sxs-lookup"><span data-stu-id="5db0b-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-157">Uma abordagem alternativa ao usar o ***padrão de opções*** é associar a `Position` seção e adicioná-la ao [contêiner de serviço de injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5db0b-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5db0b-158">No código a seguir, `PositionOptions` é adicionado ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associado à configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="5db0b-159">Usando o código anterior, o código a seguir lê as opções de posição:</span><span class="sxs-lookup"><span data-stu-id="5db0b-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-160">Usando a configuração [padrão](#default) , *appSettings. JSON* e *appSettings.* `Environment`os arquivos *. JSON* são habilitados com [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="5db0b-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="5db0b-161">As alterações feitas em *appSettings. JSON* e *appSettings.* `Environment`o arquivo *. JSON* ***após*** o início do aplicativo é lido pelo [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="5db0b-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="5db0b-162">Consulte [provedor de configuração JSON](#jcp) neste documento para obter informações sobre como adicionar arquivos de configuração JSON adicionais.</span><span class="sxs-lookup"><span data-stu-id="5db0b-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="5db0b-163">Gerenciador de segurança e segredo</span><span class="sxs-lookup"><span data-stu-id="5db0b-163">Security and secret manager</span></span>

<span data-ttu-id="5db0b-164">Diretrizes de dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="5db0b-165">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="5db0b-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="5db0b-166">O [Gerenciador de segredo](xref:security/app-secrets) pode ser usado para armazenar segredos no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5db0b-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="5db0b-167">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5db0b-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="5db0b-168">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="5db0b-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="5db0b-169">Por [padrão](#default), o [Gerenciador de segredo](xref:security/app-secrets) lê as definições de configuração após *appSettings. JSON* e *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="5db0b-170">Para obter mais informações sobre como armazenar senhas ou outros dados confidenciais:</span><span class="sxs-lookup"><span data-stu-id="5db0b-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="5db0b-171"><xref:security/app-secrets>: Inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="5db0b-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="5db0b-172">O Gerenciador de segredo usa o [provedor de configuração de arquivo](#fcp) para armazenar segredos de usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="5db0b-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="5db0b-173">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5db0b-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="5db0b-174">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="5db0b-175">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="5db0b-175">Environment variables</span></span>

<span data-ttu-id="5db0b-176">Usando a configuração [padrão](#default) , o <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração de pares chave-valor da variável de ambiente depois de ler *appSettings. JSON*, *appSettings.* `Environment` *. JSON*e o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="5db0b-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="5db0b-177">Portanto, os valores de chave lidos dos valores de substituição de ambiente lidos de *appSettings. JSON*, *appSettings.* `Environment` *. JSON*e o Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="5db0b-178">Os seguintes `set` comandos:</span><span class="sxs-lookup"><span data-stu-id="5db0b-178">The following `set` commands:</span></span>

* <span data-ttu-id="5db0b-179">Defina as chaves de ambiente e os valores do [exemplo anterior](#appsettingsjson) no Windows.</span><span class="sxs-lookup"><span data-stu-id="5db0b-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="5db0b-180">Teste as configurações ao usar o [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="5db0b-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="5db0b-181">O `dotnet run` comando deve ser executado no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="5db0b-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="5db0b-182">As configurações de ambiente anteriores:</span><span class="sxs-lookup"><span data-stu-id="5db0b-182">The preceding environment settings:</span></span>

* <span data-ttu-id="5db0b-183">São definidos apenas em processos iniciados na janela de comando em que foram definidos.</span><span class="sxs-lookup"><span data-stu-id="5db0b-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="5db0b-184">Não serão lidos por navegadores iniciados com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5db0b-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="5db0b-185">Os comandos [setx](/windows-server/administration/windows-commands/setx) a seguir podem ser usados para definir as chaves de ambiente e os valores no Windows.</span><span class="sxs-lookup"><span data-stu-id="5db0b-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="5db0b-186">Ao `set`contrário `setx` de, as configurações são persistidas.</span><span class="sxs-lookup"><span data-stu-id="5db0b-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="5db0b-187">`/M`define a variável no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="5db0b-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="5db0b-188">Se a `/M` opção não for usada, uma variável de ambiente do usuário será definida.</span><span class="sxs-lookup"><span data-stu-id="5db0b-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="5db0b-189">Para testar se os comandos anteriores substituem *appSettings. JSON* e *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="5db0b-190">Com o Visual Studio: saia e reinicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5db0b-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="5db0b-191">Com a CLI: iniciar uma nova janela de comando e `dotnet run`Enter.</span><span class="sxs-lookup"><span data-stu-id="5db0b-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="5db0b-192">Chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> com uma cadeia de caracteres para especificar um prefixo para variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="5db0b-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="5db0b-193">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="5db0b-193">In the preceding code:</span></span>

* <span data-ttu-id="5db0b-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`é adicionado após os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="5db0b-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="5db0b-195">Para obter um exemplo de como ordenar os provedores de configuração, consulte [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="5db0b-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="5db0b-196">Variáveis de ambiente definidas com `MyCustomPrefix_` o prefixo substituem os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="5db0b-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="5db0b-197">Isso inclui variáveis de ambiente sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="5db0b-198">O prefixo é eliminado quando os pares chave-valor de configuração são lidos.</span><span class="sxs-lookup"><span data-stu-id="5db0b-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="5db0b-199">Os comandos a seguir testam o prefixo personalizado:</span><span class="sxs-lookup"><span data-stu-id="5db0b-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="5db0b-200">A [configuração padrão](#default) carrega variáveis de ambiente e argumentos de linha de comando `DOTNET_` prefixados com and `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="5db0b-201">Os `DOTNET_` prefixos e `ASPNETCORE_` são usados por ASP.NET Core para [configuração de host e aplicativo](xref:fundamentals/host/generic-host#host-configuration), mas não para a configuração do usuário.</span><span class="sxs-lookup"><span data-stu-id="5db0b-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="5db0b-202">Para obter mais informações sobre a configuração de host e aplicativo, consulte [host genérico .net](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="5db0b-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="5db0b-203">Em [Azure app serviço](https://azure.microsoft.com/services/app-service/), selecione **nova configuração de aplicativo** na página **configurações > configuração** .</span><span class="sxs-lookup"><span data-stu-id="5db0b-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="5db0b-204">Azure App configurações do aplicativo de serviço são:</span><span class="sxs-lookup"><span data-stu-id="5db0b-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="5db0b-205">Criptografado em repouso e transmitido por um canal criptografado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="5db0b-206">Exposto como variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5db0b-206">Exposed as environment variables.</span></span>

<span data-ttu-id="5db0b-207">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="5db0b-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="5db0b-208">Consulte [prefixos de cadeia de conexão](#constr) para obter informações sobre cadeias de conexão do banco de dados</span><span class="sxs-lookup"><span data-stu-id="5db0b-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="5db0b-209">Linha de comando</span><span class="sxs-lookup"><span data-stu-id="5db0b-209">Command-line</span></span>

<span data-ttu-id="5db0b-210">Usando a configuração [padrão](#default) , o <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração de pares de chave-valor de argumento de linha de comando após as seguintes fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="5db0b-211">*appSettings. JSON* e *appSettings*. `Environment`. arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="5db0b-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="5db0b-212">[Segredos do aplicativo (Gerenciador de segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5db0b-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5db0b-213">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5db0b-213">Environment variables.</span></span>

<span data-ttu-id="5db0b-214">Por [padrão](#default), os valores de configuração definidos nos valores de configuração de substituição de linha de comando são definidos com todos os outros provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="5db0b-215">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="5db0b-215">Command-line arguments</span></span>

<span data-ttu-id="5db0b-216">O comando a seguir define chaves e valores `=`usando:</span><span class="sxs-lookup"><span data-stu-id="5db0b-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="5db0b-217">O comando a seguir define chaves e valores `/`usando:</span><span class="sxs-lookup"><span data-stu-id="5db0b-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="5db0b-218">O comando a seguir define chaves e valores `--`usando:</span><span class="sxs-lookup"><span data-stu-id="5db0b-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="5db0b-219">O valor da chave:</span><span class="sxs-lookup"><span data-stu-id="5db0b-219">The key value:</span></span>

* <span data-ttu-id="5db0b-220">Deve seguir `=`, ou a chave deve ter um prefixo `--` ou `/` quando o valor segue um espaço.</span><span class="sxs-lookup"><span data-stu-id="5db0b-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="5db0b-221">Não será necessário `=` se for usado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="5db0b-222">Por exemplo, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="5db0b-223">No mesmo comando, não misture pares de chave-valor de argumento de linha de comando `=` que usam com pares de chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="5db0b-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="5db0b-224">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="5db0b-224">Switch mappings</span></span>

<span data-ttu-id="5db0b-225">Os mapeamentos de switch permitem a lógica de substituição de nome de **chave** .</span><span class="sxs-lookup"><span data-stu-id="5db0b-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="5db0b-226">Forneça um dicionário de substituições de switch para o <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> método.</span><span class="sxs-lookup"><span data-stu-id="5db0b-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="5db0b-227">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="5db0b-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="5db0b-228">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="5db0b-229">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="5db0b-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="5db0b-230">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="5db0b-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="5db0b-231">Os comutadores devem `-` começar `--`com ou.</span><span class="sxs-lookup"><span data-stu-id="5db0b-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="5db0b-232">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="5db0b-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="5db0b-233">Para usar um dicionário de mapeamentos de opção, passe-o para `AddCommandLine`a chamada para:</span><span class="sxs-lookup"><span data-stu-id="5db0b-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="5db0b-234">O código a seguir mostra os valores de chave para as chaves substituídas:</span><span class="sxs-lookup"><span data-stu-id="5db0b-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-235">Execute o seguinte comando para testar a chave substituta:</span><span class="sxs-lookup"><span data-stu-id="5db0b-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="5db0b-236">Observação: no momento `=` , não pode ser usado para definir valores de substituição de chave com `-`um único traço.</span><span class="sxs-lookup"><span data-stu-id="5db0b-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="5db0b-237">Consulte [este problema do GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="5db0b-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="5db0b-238">O comando a seguir funciona para testar a substituição da chave:</span><span class="sxs-lookup"><span data-stu-id="5db0b-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="5db0b-239">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="5db0b-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="5db0b-240">A `CreateDefaultBuilder` chamada do `AddCommandLine` método não inclui opções mapeadas e não há como passar o dicionário de mapeamento de opção para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5db0b-241">A solução não é passar os argumentos para `CreateDefaultBuilder` , mas sim permitir que `ConfigurationBuilder` o método `AddCommandLine` do método processe os argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="5db0b-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="5db0b-242">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="5db0b-242">Hierarchical configuration data</span></span>

<span data-ttu-id="5db0b-243">A API de configuração lê dados de configuração hierárquicas mesclando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="5db0b-244">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="5db0b-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="5db0b-245">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações:</span><span class="sxs-lookup"><span data-stu-id="5db0b-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-246">A maneira preferida de ler dados de configuração hierárquicos é usar o padrão de opções.</span><span class="sxs-lookup"><span data-stu-id="5db0b-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="5db0b-247">Para obter mais informações, consulte [associar dados de configuração hierárquica](#optpat) neste documento.</span><span class="sxs-lookup"><span data-stu-id="5db0b-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="5db0b-248">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="5db0b-249">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="5db0b-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="5db0b-250">Chaves de configuração e valores</span><span class="sxs-lookup"><span data-stu-id="5db0b-250">Configuration keys and values</span></span>

<span data-ttu-id="5db0b-251">Chaves de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-251">Configuration keys:</span></span>

* <span data-ttu-id="5db0b-252">Não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="5db0b-252">Are case-insensitive.</span></span> <span data-ttu-id="5db0b-253">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="5db0b-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="5db0b-254">Se uma chave e um valor for definido em mais de um provedor de configuração, o valor do último provedor adicionado será usado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="5db0b-255">Para obter mais informações, consulte [configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="5db0b-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="5db0b-256">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="5db0b-256">Hierarchical keys</span></span>
  * <span data-ttu-id="5db0b-257">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="5db0b-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="5db0b-258">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="5db0b-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="5db0b-259">Um sublinhado duplo `__`,, tem suporte em todas as plataformas e é automaticamente convertido em `:`dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="5db0b-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="5db0b-260">Em Azure Key Vault, as chaves hierárquicas usam `--` como um separador.</span><span class="sxs-lookup"><span data-stu-id="5db0b-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="5db0b-261">O [provedor de configuração Azure Key Vault](xref:security/key-vault-configuration) substitui `--` automaticamente por `:` um quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="5db0b-262">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5db0b-263">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="5db0b-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="5db0b-264">Valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-264">Configuration values:</span></span>

* <span data-ttu-id="5db0b-265">São cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="5db0b-265">Are strings.</span></span>
* <span data-ttu-id="5db0b-266">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="5db0b-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="5db0b-267">Provedores de configuração</span><span class="sxs-lookup"><span data-stu-id="5db0b-267">Configuration providers</span></span>

<span data-ttu-id="5db0b-268">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5db0b-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="5db0b-269">Provedor</span><span class="sxs-lookup"><span data-stu-id="5db0b-269">Provider</span></span> | <span data-ttu-id="5db0b-270">Fornece a configuração de </span><span class="sxs-lookup"><span data-stu-id="5db0b-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="5db0b-271">Provedor de configuração de Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5db0b-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="5db0b-272">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="5db0b-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="5db0b-273">Provedor de configuração de Azure App</span><span class="sxs-lookup"><span data-stu-id="5db0b-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="5db0b-274">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="5db0b-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="5db0b-275">Provedor de configuração de linha de comando</span><span class="sxs-lookup"><span data-stu-id="5db0b-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="5db0b-276">Parâmetros da linha de comando</span><span class="sxs-lookup"><span data-stu-id="5db0b-276">Command-line parameters</span></span> |
| [<span data-ttu-id="5db0b-277">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="5db0b-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="5db0b-278">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="5db0b-278">Custom source</span></span> |
| [<span data-ttu-id="5db0b-279">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="5db0b-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="5db0b-280">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="5db0b-280">Environment variables</span></span> |
| [<span data-ttu-id="5db0b-281">Provedor de configuração de arquivo</span><span class="sxs-lookup"><span data-stu-id="5db0b-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="5db0b-282">Arquivos INI, JSON e XML</span><span class="sxs-lookup"><span data-stu-id="5db0b-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="5db0b-283">Provedor de configuração de chave por arquivo</span><span class="sxs-lookup"><span data-stu-id="5db0b-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="5db0b-284">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="5db0b-284">Directory files</span></span> |
| [<span data-ttu-id="5db0b-285">Provedor de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="5db0b-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="5db0b-286">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="5db0b-286">In-memory collections</span></span> |
| [<span data-ttu-id="5db0b-287">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="5db0b-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="5db0b-288">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="5db0b-288">File in the user profile directory</span></span> |

<span data-ttu-id="5db0b-289">As fontes de configuração são lidas na ordem em que seus provedores de configuração são especificados.</span><span class="sxs-lookup"><span data-stu-id="5db0b-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="5db0b-290">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="5db0b-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="5db0b-291">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="5db0b-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="5db0b-292">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="5db0b-292">*appsettings.json*</span></span>
1. <span data-ttu-id="5db0b-293">*appSettings*. `Environment`. *JSON*</span><span class="sxs-lookup"><span data-stu-id="5db0b-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="5db0b-294">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="5db0b-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="5db0b-295">Variáveis de ambiente usando o [provedor de configuração de variáveis de ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="5db0b-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="5db0b-296">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5db0b-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="5db0b-297">Uma prática comum é adicionar o provedor de configuração de linha de comando pela última vez em uma série de provedores para permitir que argumentos de linha de comando substituam a configuração definida pelos outros provedores.</span><span class="sxs-lookup"><span data-stu-id="5db0b-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="5db0b-298">A sequência anterior de provedores é usada na [configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="5db0b-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="5db0b-299">Prefixos de cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="5db0b-299">Connection string prefixes</span></span>

<span data-ttu-id="5db0b-300">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="5db0b-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="5db0b-301">Essas cadeias de conexão estão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="5db0b-302">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo com a [configuração padrão](#default) ou quando nenhum prefixo `AddEnvironmentVariables`é fornecido para.</span><span class="sxs-lookup"><span data-stu-id="5db0b-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="5db0b-303">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="5db0b-303">Connection string prefix</span></span> | <span data-ttu-id="5db0b-304">Provedor</span><span class="sxs-lookup"><span data-stu-id="5db0b-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="5db0b-305">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="5db0b-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="5db0b-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="5db0b-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="5db0b-307">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="5db0b-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="5db0b-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="5db0b-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="5db0b-309">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="5db0b-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="5db0b-310">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="5db0b-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="5db0b-311">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="5db0b-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="5db0b-312">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="5db0b-312">Environment variable key</span></span> | <span data-ttu-id="5db0b-313">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="5db0b-313">Converted configuration key</span></span> | <span data-ttu-id="5db0b-314">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="5db0b-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5db0b-315">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="5db0b-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5db0b-316">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5db0b-317">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="5db0b-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5db0b-318">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5db0b-319">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5db0b-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5db0b-320">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5db0b-321">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5db0b-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="5db0b-322">Provedor de configuração JSON</span><span class="sxs-lookup"><span data-stu-id="5db0b-322">JSON configuration provider</span></span>

<span data-ttu-id="5db0b-323">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor de arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="5db0b-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="5db0b-324">Sobrecargas podem especificar:</span><span class="sxs-lookup"><span data-stu-id="5db0b-324">Overloads can specify:</span></span>

* <span data-ttu-id="5db0b-325">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="5db0b-325">Whether the file is optional.</span></span>
* <span data-ttu-id="5db0b-326">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="5db0b-327">Considere o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="5db0b-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="5db0b-328">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="5db0b-328">The preceding code:</span></span>

* <span data-ttu-id="5db0b-329">Configura o provedor de configuração JSON para carregar o arquivo *myconfig. JSON* com as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="5db0b-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="5db0b-330">`optional: true`: O arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="5db0b-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="5db0b-331">`reloadOnChange: true`: O arquivo é recarregado quando as alterações são salvas.</span><span class="sxs-lookup"><span data-stu-id="5db0b-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="5db0b-332">Lê os [provedores de configuração padrão](#default) antes do arquivo *myconfig. JSON* .</span><span class="sxs-lookup"><span data-stu-id="5db0b-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="5db0b-333">As configurações na configuração de substituição do arquivo *myconfig. JSON* nos provedores de configuração padrão, incluindo o [provedor de configuração de variáveis de ambiente](#evcp) e o provedor de configuração de linha de [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="5db0b-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5db0b-334">Normalmente, você ***não*** deseja que um arquivo JSON personalizado substitua valores definidos no [provedor de configuração de variáveis de ambiente](#evcp) e no provedor de configuração de linha de [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="5db0b-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5db0b-335">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="5db0b-336">No código anterior, as configurações em *myconfig. JSON* e *myconfig*. `Environment`. arquivos *JSON* :</span><span class="sxs-lookup"><span data-stu-id="5db0b-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="5db0b-337">Substitua as configurações em *appSettings. JSON* e *appSettings*. `Environment`. arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="5db0b-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="5db0b-338">São substituídos por configurações no [provedor de configuração de variáveis de ambiente](#evcp) e no [provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="5db0b-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5db0b-339">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *myconfig. JSON* :</span><span class="sxs-lookup"><span data-stu-id="5db0b-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="5db0b-340">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="5db0b-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="5db0b-341">Provedor de configuração de arquivo</span><span class="sxs-lookup"><span data-stu-id="5db0b-341">File configuration provider</span></span>

<span data-ttu-id="5db0b-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="5db0b-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="5db0b-343">Os seguintes provedores de configuração derivam de `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="5db0b-344">Provedor de configuração INI</span><span class="sxs-lookup"><span data-stu-id="5db0b-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="5db0b-345">Provedor de configuração JSON</span><span class="sxs-lookup"><span data-stu-id="5db0b-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="5db0b-346">Provedor de configuração XML</span><span class="sxs-lookup"><span data-stu-id="5db0b-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="5db0b-347">Provedor de configuração INI</span><span class="sxs-lookup"><span data-stu-id="5db0b-347">INI configuration provider</span></span>

<span data-ttu-id="5db0b-348">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="5db0b-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="5db0b-349">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="5db0b-350">No código anterior, as configurações no *MyIniConfig. ini* e no *MyIniConfig*. `Environment`. os arquivos *ini* são substituídos pelas configurações no:</span><span class="sxs-lookup"><span data-stu-id="5db0b-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="5db0b-351">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="5db0b-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="5db0b-352">[Provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="5db0b-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5db0b-353">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *MyIniConfig. ini* :</span><span class="sxs-lookup"><span data-stu-id="5db0b-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="5db0b-354">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="5db0b-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="5db0b-355">Provedor de configuração XML</span><span class="sxs-lookup"><span data-stu-id="5db0b-355">XML configuration provider</span></span>

<span data-ttu-id="5db0b-356">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="5db0b-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="5db0b-357">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="5db0b-358">No código anterior, as configurações em *MyXMLFile. xml* e *MyXMLFile*. `Environment`. os arquivos *XML* são substituídos pelas configurações no:</span><span class="sxs-lookup"><span data-stu-id="5db0b-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="5db0b-359">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="5db0b-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="5db0b-360">[Provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="5db0b-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5db0b-361">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *MyXMLFile. xml* :</span><span class="sxs-lookup"><span data-stu-id="5db0b-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="5db0b-362">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="5db0b-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-363">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="5db0b-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="5db0b-364">O código a seguir lê o arquivo de configuração anterior e exibe as chaves e valores:</span><span class="sxs-lookup"><span data-stu-id="5db0b-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-365">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="5db0b-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="5db0b-366">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5db0b-367">key:attribute</span><span class="sxs-lookup"><span data-stu-id="5db0b-367">key:attribute</span></span>
* <span data-ttu-id="5db0b-368">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="5db0b-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="5db0b-369">Provedor de configuração de chave por arquivo</span><span class="sxs-lookup"><span data-stu-id="5db0b-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="5db0b-370">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="5db0b-371">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-371">The key is the file name.</span></span> <span data-ttu-id="5db0b-372">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-372">The value contains the file's contents.</span></span> <span data-ttu-id="5db0b-373">O provedor de configuração de chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="5db0b-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="5db0b-374">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="5db0b-375">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="5db0b-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="5db0b-376">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="5db0b-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="5db0b-377">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="5db0b-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="5db0b-378">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="5db0b-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="5db0b-379">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="5db0b-380">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="5db0b-381">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5db0b-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="5db0b-382">Provedor de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="5db0b-382">Memory configuration provider</span></span>

<span data-ttu-id="5db0b-383">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="5db0b-384">O código a seguir adiciona uma coleção de memória ao sistema de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="5db0b-385">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe as configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="5db0b-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-386">No código anterior, `config.AddInMemoryCollection(Dict)` é adicionado após os provedores de [configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="5db0b-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="5db0b-387">Para obter um exemplo de como ordenar os provedores de configuração, consulte [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="5db0b-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="5db0b-388">Consulte [associar uma matriz](#boa) para outro exemplo usando `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-388">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="5db0b-389">GetValue</span><span class="sxs-lookup"><span data-stu-id="5db0b-389">GetValue</span></span>

<span data-ttu-id="5db0b-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrai um único valor da configuração com uma chave especificada e converte-o no tipo especificado:</span><span class="sxs-lookup"><span data-stu-id="5db0b-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-391">No código anterior, se `NumberKey` não for encontrado na configuração, o valor padrão de `99` será usado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-391">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="5db0b-392">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="5db0b-392">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="5db0b-393">Para os exemplos a seguir, considere o seguinte arquivo *MySubsection. JSON* :</span><span class="sxs-lookup"><span data-stu-id="5db0b-393">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="5db0b-394">O código a seguir adiciona *MySubsection. JSON* aos provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-394">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="5db0b-395">GetSection</span><span class="sxs-lookup"><span data-stu-id="5db0b-395">GetSection</span></span>

<span data-ttu-id="5db0b-396">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retorna uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="5db0b-396">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="5db0b-397">O código a seguir retorna valores `section1`para:</span><span class="sxs-lookup"><span data-stu-id="5db0b-397">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-398">O código a seguir retorna valores `section2:subsection0`para:</span><span class="sxs-lookup"><span data-stu-id="5db0b-398">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-399">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-399">`GetSection` never returns `null`.</span></span> <span data-ttu-id="5db0b-400">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-400">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="5db0b-401">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="5db0b-401">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="5db0b-402"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="5db0b-402">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="5db0b-403">GetChildren e existe</span><span class="sxs-lookup"><span data-stu-id="5db0b-403">GetChildren and Exists</span></span>

<span data-ttu-id="5db0b-404">O código a seguir chama [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) e retorna valores `section2:subsection0`para:</span><span class="sxs-lookup"><span data-stu-id="5db0b-404">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-405">O código anterior chama [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para verificar se a seção existe:</span><span class="sxs-lookup"><span data-stu-id="5db0b-405">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="5db0b-406">Associar uma matriz</span><span class="sxs-lookup"><span data-stu-id="5db0b-406">Bind an array</span></span>

<span data-ttu-id="5db0b-407">O [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) dá suporte a matrizes de associação a objetos usando índices de matriz em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-407">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5db0b-408">Qualquer formato de matriz que expõe um segmento de chave numérica é capaz de associar a matriz a uma matriz de classe [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="5db0b-408">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="5db0b-409">Considere o *myArray. JSON* do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="5db0b-409">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="5db0b-410">O código a seguir adiciona *myArray. JSON* aos provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-410">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="5db0b-411">O código a seguir lê a configuração e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="5db0b-411">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-412">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="5db0b-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="5db0b-413">Na saída anterior, o índice 3 tem valor `value40`, correspondente a `"4": "value40",` em *myArray. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-413">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="5db0b-414">Os índices de matriz associados são contínuos e não associados ao índice de chave de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-414">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="5db0b-415">O associador de configuração não é capaz de ligar valores nulos ou criar entradas nulas em objetos associados</span><span class="sxs-lookup"><span data-stu-id="5db0b-415">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="5db0b-416">O código a seguir carrega `array:entries` a configuração com <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="5db0b-416">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="5db0b-417">O código a seguir lê a configuração no `arrayDict` `Dictionary` e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="5db0b-417">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-418">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="5db0b-418">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="5db0b-419">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-419">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="5db0b-420">Quando os dados de configuração que contêm uma matriz são associados, os índices de matriz nas chaves de configuração são usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="5db0b-420">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="5db0b-421">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="5db0b-421">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="5db0b-422">O item de configuração ausente para &num;o `ArrayExample` índice 3 pode ser fornecido antes da associação à instância por qualquer provedor de configuração que &num;Leia o par chave/valor do índice 3.</span><span class="sxs-lookup"><span data-stu-id="5db0b-422">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="5db0b-423">Considere o seguinte arquivo *Value3. JSON* do download de exemplo:</span><span class="sxs-lookup"><span data-stu-id="5db0b-423">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="5db0b-424">O código a seguir inclui a configuração para *Value3. JSON* e o `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-424">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="5db0b-425">O código a seguir lê a configuração anterior e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="5db0b-425">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="5db0b-426">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="5db0b-426">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="5db0b-427">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="5db0b-427">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="5db0b-428">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="5db0b-428">Custom configuration provider</span></span>

<span data-ttu-id="5db0b-429">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="5db0b-429">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="5db0b-430">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="5db0b-430">The provider has the following characteristics:</span></span>

* <span data-ttu-id="5db0b-431">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-431">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="5db0b-432">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-432">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="5db0b-433">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="5db0b-433">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="5db0b-434">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="5db0b-434">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="5db0b-435">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-435">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="5db0b-436">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5db0b-436">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="5db0b-437">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-437">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="5db0b-438">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="5db0b-438">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="5db0b-439">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-439">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="5db0b-440">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-440">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="5db0b-441">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-441">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="5db0b-442">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-442">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="5db0b-443">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="5db0b-443">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="5db0b-444">Como [as chaves de configuração](#keys)não diferenciam maiúsculas de minúsculas, o dicionário usado para inicializar o banco de dados é criado com o comparador que não diferencia maiúsculas de minúsculas ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="5db0b-444">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="5db0b-445">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-445">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="5db0b-446">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-446">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="5db0b-447">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-447">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="5db0b-448">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-448">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="5db0b-449">Configuração de acesso na inicialização</span><span class="sxs-lookup"><span data-stu-id="5db0b-449">Access configuration in Startup</span></span>

<span data-ttu-id="5db0b-450">O código a seguir exibe dados de `Startup` configuração em métodos:</span><span class="sxs-lookup"><span data-stu-id="5db0b-450">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="5db0b-451">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="5db0b-451">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="5db0b-452">Configuração de acesso no Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5db0b-452">Access configuration in Razor Pages</span></span>

<span data-ttu-id="5db0b-453">O código a seguir exibe os dados de configuração em uma página Razor:</span><span class="sxs-lookup"><span data-stu-id="5db0b-453">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="5db0b-454">Acessar a configuração em um arquivo de exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="5db0b-454">Access configuration in a MVC view file</span></span>

<span data-ttu-id="5db0b-455">O código a seguir exibe os dados de configuração em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="5db0b-455">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="5db0b-456">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="5db0b-456">Host versus app configuration</span></span>

<span data-ttu-id="5db0b-457">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-457">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="5db0b-458">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-458">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="5db0b-459">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="5db0b-459">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="5db0b-460">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-460">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="5db0b-461">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-461">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="5db0b-462">Configuração de host padrão</span><span class="sxs-lookup"><span data-stu-id="5db0b-462">Default host configuration</span></span>

<span data-ttu-id="5db0b-463">Para obter detalhes sobre a configuração padrão ao usar o [host da Web](xref:fundamentals/host/web-host), confira a [versão do ASP.NET Core 2.2 deste tópico](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="5db0b-463">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="5db0b-464">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="5db0b-464">Host configuration is provided from:</span></span>
  * <span data-ttu-id="5db0b-465">Variáveis de ambiente prefixadas com `DOTNET_` (por `DOTNET_ENVIRONMENT`exemplo,) usando o [provedor de configuração de variáveis de ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5db0b-465">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="5db0b-466">O prefixo (`DOTNET_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-466">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="5db0b-467">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5db0b-467">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="5db0b-468">A configuração padrão do host da Web foi estabelecida (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="5db0b-468">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="5db0b-469">O Kestrel é usado como o servidor Web e configurado usando provedores de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-469">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="5db0b-470">Adicione o middleware de filtragem de host.</span><span class="sxs-lookup"><span data-stu-id="5db0b-470">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="5db0b-471">Adicione o middleware de cabeçalhos encaminhados se a variável de ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for definida para `true`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-471">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="5db0b-472">Habilite a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="5db0b-472">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="5db0b-473">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="5db0b-473">Other configuration</span></span>

<span data-ttu-id="5db0b-474">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-474">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="5db0b-475">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="5db0b-475">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="5db0b-476">*Launch. JSON*/*launchSettings. JSON* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="5db0b-476">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="5db0b-477">Em <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-477">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="5db0b-478">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5db0b-478">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="5db0b-479">o *Web. config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="5db0b-479">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="5db0b-480">Para obter mais informações sobre como migrar a configuração de aplicativo de versões <xref:migration/proper-to-2x/index#store-configurations>anteriores do ASP.net, consulte.</span><span class="sxs-lookup"><span data-stu-id="5db0b-480">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="5db0b-481">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="5db0b-481">Add configuration from an external assembly</span></span>

<span data-ttu-id="5db0b-482">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-482">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="5db0b-483">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-483">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5db0b-484">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5db0b-484">Additional resources</span></span>

* [<span data-ttu-id="5db0b-485">Código-fonte de configuração</span><span class="sxs-lookup"><span data-stu-id="5db0b-485">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5db0b-486">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-486">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="5db0b-487">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-487">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="5db0b-488">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="5db0b-488">Azure Key Vault</span></span>
* <span data-ttu-id="5db0b-489">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="5db0b-489">Azure App Configuration</span></span>
* <span data-ttu-id="5db0b-490">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="5db0b-490">Command-line arguments</span></span>
* <span data-ttu-id="5db0b-491">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="5db0b-491">Custom providers (installed or created)</span></span>
* <span data-ttu-id="5db0b-492">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="5db0b-492">Directory files</span></span>
* <span data-ttu-id="5db0b-493">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="5db0b-493">Environment variables</span></span>
* <span data-ttu-id="5db0b-494">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="5db0b-494">In-memory .NET objects</span></span>
* <span data-ttu-id="5db0b-495">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="5db0b-495">Settings files</span></span>

<span data-ttu-id="5db0b-496">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5db0b-496">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5db0b-497">Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="5db0b-497">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="5db0b-498">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="5db0b-498">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="5db0b-499">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="5db0b-499">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="5db0b-500">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-500">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="5db0b-501">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5db0b-501">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="5db0b-502">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="5db0b-502">Host versus app configuration</span></span>

<span data-ttu-id="5db0b-503">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-503">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="5db0b-504">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-504">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="5db0b-505">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="5db0b-505">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="5db0b-506">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-506">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="5db0b-507">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-507">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="5db0b-508">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="5db0b-508">Other configuration</span></span>

<span data-ttu-id="5db0b-509">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-509">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="5db0b-510">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="5db0b-510">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="5db0b-511">*Launch. JSON*/*launchSettings. JSON* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="5db0b-511">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="5db0b-512">Em <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-512">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="5db0b-513">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5db0b-513">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="5db0b-514">o *Web. config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="5db0b-514">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="5db0b-515">Para obter mais informações sobre como migrar a configuração de aplicativo de versões <xref:migration/proper-to-2x/index#store-configurations>anteriores do ASP.net, consulte.</span><span class="sxs-lookup"><span data-stu-id="5db0b-515">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="5db0b-516">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="5db0b-516">Default configuration</span></span>

<span data-ttu-id="5db0b-517">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="5db0b-517">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="5db0b-518">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="5db0b-518">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="5db0b-519">O seguinte se aplica a aplicativos que usam o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="5db0b-519">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="5db0b-520">Para obter detalhes sobre a configuração padrão ao usar o [host genérico](xref:fundamentals/host/generic-host), confira [versão mais recente deste tópico](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="5db0b-520">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="5db0b-521">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="5db0b-521">Host configuration is provided from:</span></span>
  * <span data-ttu-id="5db0b-522">Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5db0b-522">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="5db0b-523">O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-523">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="5db0b-524">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5db0b-524">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="5db0b-525">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="5db0b-525">App configuration is provided from:</span></span>
  * <span data-ttu-id="5db0b-526">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5db0b-526">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5db0b-527">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5db0b-527">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5db0b-528">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="5db0b-528">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="5db0b-529">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5db0b-529">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="5db0b-530">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5db0b-530">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="5db0b-531">Segurança</span><span class="sxs-lookup"><span data-stu-id="5db0b-531">Security</span></span>

<span data-ttu-id="5db0b-532">Adote as seguintes práticas para proteger dados de configuração confidenciais:</span><span class="sxs-lookup"><span data-stu-id="5db0b-532">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="5db0b-533">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="5db0b-533">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="5db0b-534">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5db0b-534">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="5db0b-535">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="5db0b-535">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="5db0b-536">Para mais informações, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="5db0b-536">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="5db0b-537"><xref:security/app-secrets>&ndash; Inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="5db0b-537"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="5db0b-538">O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="5db0b-538">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="5db0b-539">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="5db0b-539">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="5db0b-540">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5db0b-540">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="5db0b-541">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-541">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="5db0b-542">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="5db0b-542">Hierarchical configuration data</span></span>

<span data-ttu-id="5db0b-543">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-543">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="5db0b-544">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="5db0b-544">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="5db0b-545">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-545">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="5db0b-546">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="5db0b-546">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="5db0b-547">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5db0b-547">section0:key0</span></span>
* <span data-ttu-id="5db0b-548">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5db0b-548">section0:key1</span></span>
* <span data-ttu-id="5db0b-549">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5db0b-549">section1:key0</span></span>
* <span data-ttu-id="5db0b-550">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5db0b-550">section1:key1</span></span>

<span data-ttu-id="5db0b-551">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-551"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="5db0b-552">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="5db0b-552">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="5db0b-553">Convenções</span><span class="sxs-lookup"><span data-stu-id="5db0b-553">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="5db0b-554">Origens e provedores</span><span class="sxs-lookup"><span data-stu-id="5db0b-554">Sources and providers</span></span>

<span data-ttu-id="5db0b-555">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-555">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="5db0b-556">Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada.</span><span class="sxs-lookup"><span data-stu-id="5db0b-556">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="5db0b-557">Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-557">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="5db0b-558"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-558"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5db0b-559"><xref:Microsoft.Extensions.Configuration.IConfiguration>pode ser injetado em um Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obter a configuração da classe.</span><span class="sxs-lookup"><span data-stu-id="5db0b-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="5db0b-560">Nos exemplos a seguir, o `_config` campo é usado para acessar os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-560">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="5db0b-561">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="5db0b-561">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="5db0b-562">simétricas</span><span class="sxs-lookup"><span data-stu-id="5db0b-562">Keys</span></span>

<span data-ttu-id="5db0b-563">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="5db0b-563">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="5db0b-564">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="5db0b-564">Keys are case-insensitive.</span></span> <span data-ttu-id="5db0b-565">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="5db0b-565">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="5db0b-566">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-566">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="5db0b-567">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="5db0b-567">Hierarchical keys</span></span>
  * <span data-ttu-id="5db0b-568">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="5db0b-568">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="5db0b-569">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="5db0b-569">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="5db0b-570">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="5db0b-570">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="5db0b-571">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="5db0b-571">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="5db0b-572">Escreva o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-572">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="5db0b-573">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-573">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5db0b-574">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="5db0b-574">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="5db0b-575">Valores</span><span class="sxs-lookup"><span data-stu-id="5db0b-575">Values</span></span>

<span data-ttu-id="5db0b-576">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="5db0b-576">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="5db0b-577">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="5db0b-577">Values are strings.</span></span>
* <span data-ttu-id="5db0b-578">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="5db0b-578">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="5db0b-579">Provedores</span><span class="sxs-lookup"><span data-stu-id="5db0b-579">Providers</span></span>

<span data-ttu-id="5db0b-580">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5db0b-580">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="5db0b-581">Provedor</span><span class="sxs-lookup"><span data-stu-id="5db0b-581">Provider</span></span> | <span data-ttu-id="5db0b-582">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="5db0b-582">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="5db0b-583">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="5db0b-583">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="5db0b-584">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="5db0b-584">Azure Key Vault</span></span> |
| <span data-ttu-id="5db0b-585">[Provedor da Configuração de Aplicativos do Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="5db0b-585">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="5db0b-586">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="5db0b-586">Azure App Configuration</span></span> |
| [<span data-ttu-id="5db0b-587">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="5db0b-587">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="5db0b-588">Parâmetros da linha de comando</span><span class="sxs-lookup"><span data-stu-id="5db0b-588">Command-line parameters</span></span> |
| [<span data-ttu-id="5db0b-589">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="5db0b-589">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="5db0b-590">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="5db0b-590">Custom source</span></span> |
| [<span data-ttu-id="5db0b-591">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="5db0b-591">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="5db0b-592">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="5db0b-592">Environment variables</span></span> |
| [<span data-ttu-id="5db0b-593">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="5db0b-593">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="5db0b-594">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="5db0b-594">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="5db0b-595">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="5db0b-595">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="5db0b-596">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="5db0b-596">Directory files</span></span> |
| [<span data-ttu-id="5db0b-597">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="5db0b-597">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="5db0b-598">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="5db0b-598">In-memory collections</span></span> |
| <span data-ttu-id="5db0b-599">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="5db0b-599">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="5db0b-600">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="5db0b-600">File in the user profile directory</span></span> |

<span data-ttu-id="5db0b-601">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-601">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="5db0b-602">Os provedores de configuração descritos neste tópico são descritos em ordem alfabética, não na ordem em que o código os organiza.</span><span class="sxs-lookup"><span data-stu-id="5db0b-602">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="5db0b-603">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="5db0b-603">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="5db0b-604">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="5db0b-604">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="5db0b-605">Arquivos (*appSettings. JSON*, *appSettings. { Ambiente}. JSON*, em `{Environment}` que é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="5db0b-605">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="5db0b-606">Cofre da Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="5db0b-606">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="5db0b-607">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="5db0b-607">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="5db0b-608">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="5db0b-608">Environment variables</span></span>
1. <span data-ttu-id="5db0b-609">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="5db0b-609">Command-line arguments</span></span>

<span data-ttu-id="5db0b-610">Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="5db0b-610">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="5db0b-611">A sequência anterior de provedores é usada quando um novo Construtor de hosts é `CreateDefaultBuilder`inicializado com o.</span><span class="sxs-lookup"><span data-stu-id="5db0b-611">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5db0b-612">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5db0b-612">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="5db0b-613">Configurar o construtor de host com UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="5db0b-613">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="5db0b-614">Para configurar o construtor de host, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> no construtor de host com a configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-614">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="5db0b-615">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="5db0b-615">ConfigureAppConfiguration</span></span>

<span data-ttu-id="5db0b-616">Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-616">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="5db0b-617">Substituir a configuração anterior por argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="5db0b-617">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="5db0b-618">Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:</span><span class="sxs-lookup"><span data-stu-id="5db0b-618">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="5db0b-619">Remover provedores adicionados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="5db0b-619">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="5db0b-620">Para remover os provedores adicionados pelo `CreateDefaultBuilder`, chame [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) no [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) primeiro:</span><span class="sxs-lookup"><span data-stu-id="5db0b-620">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="5db0b-621">Consumir a configuração durante a inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="5db0b-621">Consume configuration during app startup</span></span>

<span data-ttu-id="5db0b-622">a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-622">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5db0b-623">Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).</span><span class="sxs-lookup"><span data-stu-id="5db0b-623">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="5db0b-624">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="5db0b-624">Command-line Configuration Provider</span></span>

<span data-ttu-id="5db0b-625">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em runtime.</span><span class="sxs-lookup"><span data-stu-id="5db0b-625">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="5db0b-626">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-626">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5db0b-627">`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-627">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="5db0b-628">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5db0b-628">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5db0b-629">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="5db0b-629">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5db0b-630">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-630">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="5db0b-631">[Segredos do usuário (Gerenciador de segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5db0b-631">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5db0b-632">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5db0b-632">Environment variables.</span></span>

<span data-ttu-id="5db0b-633">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="5db0b-633">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="5db0b-634">Os argumentos de linha de comando passados em runtime substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="5db0b-634">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="5db0b-635">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="5db0b-635">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="5db0b-636">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-636">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="5db0b-637">Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-637">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5db0b-638">Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.</span><span class="sxs-lookup"><span data-stu-id="5db0b-638">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="5db0b-639">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="5db0b-639">**Example**</span></span>

<span data-ttu-id="5db0b-640">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-640">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="5db0b-641">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="5db0b-641">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="5db0b-642">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-642">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="5db0b-643">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-643">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5db0b-644">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-644">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="5db0b-645">Argumentos</span><span class="sxs-lookup"><span data-stu-id="5db0b-645">Arguments</span></span>

<span data-ttu-id="5db0b-646">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="5db0b-646">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="5db0b-647">O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="5db0b-647">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="5db0b-648">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="5db0b-648">Key prefix</span></span>               | <span data-ttu-id="5db0b-649">Exemplo</span><span class="sxs-lookup"><span data-stu-id="5db0b-649">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="5db0b-650">Nenhum prefixo</span><span class="sxs-lookup"><span data-stu-id="5db0b-650">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="5db0b-651">Dois traços (`--`)</span><span class="sxs-lookup"><span data-stu-id="5db0b-651">Two dashes (`--`)</span></span>        | <span data-ttu-id="5db0b-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="5db0b-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="5db0b-653">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="5db0b-653">Forward slash (`/`)</span></span>      | <span data-ttu-id="5db0b-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="5db0b-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="5db0b-655">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="5db0b-655">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="5db0b-656">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="5db0b-656">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="5db0b-657">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="5db0b-657">Switch mappings</span></span>

<span data-ttu-id="5db0b-658">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="5db0b-658">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="5db0b-659">Ao criar manualmente a configuração com <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>um, forneça um dicionário de substituições de switch para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> o método.</span><span class="sxs-lookup"><span data-stu-id="5db0b-659">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="5db0b-660">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="5db0b-660">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="5db0b-661">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-661">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="5db0b-662">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="5db0b-662">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="5db0b-663">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="5db0b-663">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="5db0b-664">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="5db0b-664">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="5db0b-665">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="5db0b-665">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="5db0b-666">Crie um dicionário de mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="5db0b-666">Create a switch mappings dictionary.</span></span> <span data-ttu-id="5db0b-667">No exemplo a seguir, dois mapeamentos de opção são criados:</span><span class="sxs-lookup"><span data-stu-id="5db0b-667">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="5db0b-668">Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:</span><span class="sxs-lookup"><span data-stu-id="5db0b-668">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="5db0b-669">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="5db0b-669">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="5db0b-670">A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-670">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5db0b-671">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="5db0b-671">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="5db0b-672">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="5db0b-672">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="5db0b-673">Chave</span><span class="sxs-lookup"><span data-stu-id="5db0b-673">Key</span></span>       | <span data-ttu-id="5db0b-674">Valor</span><span class="sxs-lookup"><span data-stu-id="5db0b-674">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="5db0b-675">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="5db0b-675">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="5db0b-676">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="5db0b-676">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="5db0b-677">Chave</span><span class="sxs-lookup"><span data-stu-id="5db0b-677">Key</span></span>               | <span data-ttu-id="5db0b-678">Valor</span><span class="sxs-lookup"><span data-stu-id="5db0b-678">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="5db0b-679">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="5db0b-679">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="5db0b-680">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em runtime.</span><span class="sxs-lookup"><span data-stu-id="5db0b-680">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="5db0b-681">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-681">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="5db0b-682">[Azure app serviço](https://azure.microsoft.com/services/app-service/) permite definir variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o provedor de configuração de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5db0b-682">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="5db0b-683">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="5db0b-683">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="5db0b-684">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `ASPNETCORE_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host da Web](xref:fundamentals/host/web-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-684">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="5db0b-685">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5db0b-685">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5db0b-686">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="5db0b-686">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5db0b-687">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-687">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="5db0b-688">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-688">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="5db0b-689">[Segredos do usuário (Gerenciador de segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5db0b-689">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5db0b-690">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="5db0b-690">Command-line arguments.</span></span>

<span data-ttu-id="5db0b-691">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-691">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="5db0b-692">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em runtime para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-692">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="5db0b-693">Para fornecer a configuração de aplicativo de variáveis de ambiente adicionais, chame os provedores adicionais `ConfigureAppConfiguration` do aplicativo `AddEnvironmentVariables` no e chame com o prefixo:</span><span class="sxs-lookup"><span data-stu-id="5db0b-693">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="5db0b-694">Chame `AddEnvironmentVariables` Last para permitir que as variáveis de ambiente com o prefixo fornecido substituam valores de outros provedores.</span><span class="sxs-lookup"><span data-stu-id="5db0b-694">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="5db0b-695">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="5db0b-695">**Example**</span></span>

<span data-ttu-id="5db0b-696">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-696">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="5db0b-697">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-697">Run the sample app.</span></span> <span data-ttu-id="5db0b-698">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-698">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5db0b-699">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-699">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="5db0b-700">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="5db0b-700">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="5db0b-701">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5db0b-701">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="5db0b-702">Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-702">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="5db0b-703">Para expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *pages/index. cshtml. cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="5db0b-703">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="5db0b-704">Prefixos</span><span class="sxs-lookup"><span data-stu-id="5db0b-704">Prefixes</span></span>

<span data-ttu-id="5db0b-705">As variáveis de ambiente carregadas na configuração do aplicativo são filtradas ao fornecer um prefixo `AddEnvironmentVariables` para o método.</span><span class="sxs-lookup"><span data-stu-id="5db0b-705">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="5db0b-706">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-706">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="5db0b-707">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-707">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="5db0b-708">Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5db0b-708">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="5db0b-709">Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5db0b-709">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5db0b-710">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="5db0b-710">**Connection string prefixes**</span></span>

<span data-ttu-id="5db0b-711">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-711">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="5db0b-712">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-712">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="5db0b-713">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="5db0b-713">Connection string prefix</span></span> | <span data-ttu-id="5db0b-714">Provedor</span><span class="sxs-lookup"><span data-stu-id="5db0b-714">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="5db0b-715">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="5db0b-715">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="5db0b-716">MySQL</span><span class="sxs-lookup"><span data-stu-id="5db0b-716">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="5db0b-717">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="5db0b-717">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="5db0b-718">SQL Server</span><span class="sxs-lookup"><span data-stu-id="5db0b-718">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="5db0b-719">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="5db0b-719">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="5db0b-720">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="5db0b-720">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="5db0b-721">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="5db0b-721">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="5db0b-722">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="5db0b-722">Environment variable key</span></span> | <span data-ttu-id="5db0b-723">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="5db0b-723">Converted configuration key</span></span> | <span data-ttu-id="5db0b-724">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="5db0b-724">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5db0b-725">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="5db0b-725">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5db0b-726">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5db0b-727">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="5db0b-727">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5db0b-728">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5db0b-729">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5db0b-729">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5db0b-730">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-730">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5db0b-731">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5db0b-731">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="5db0b-732">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="5db0b-732">**Example**</span></span>

<span data-ttu-id="5db0b-733">Uma variável de ambiente de cadeia de conexão personalizada é criada no servidor:</span><span class="sxs-lookup"><span data-stu-id="5db0b-733">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="5db0b-734">Nome &ndash; do`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="5db0b-734">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="5db0b-735">Valor &ndash; do`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="5db0b-735">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="5db0b-736">Se `IConfiguration` for injetado e atribuído a um campo chamado `_config`, leia o valor:</span><span class="sxs-lookup"><span data-stu-id="5db0b-736">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="5db0b-737">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="5db0b-737">File Configuration Provider</span></span>

<span data-ttu-id="5db0b-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="5db0b-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="5db0b-739">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="5db0b-739">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="5db0b-740">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="5db0b-740">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="5db0b-741">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="5db0b-741">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="5db0b-742">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="5db0b-742">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="5db0b-743">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="5db0b-743">INI Configuration Provider</span></span>

<span data-ttu-id="5db0b-744">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="5db0b-744">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="5db0b-745">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-745">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5db0b-746">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="5db0b-746">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="5db0b-747">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="5db0b-747">Overloads permit specifying:</span></span>

* <span data-ttu-id="5db0b-748">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="5db0b-748">Whether the file is optional.</span></span>
* <span data-ttu-id="5db0b-749">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-749">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5db0b-750">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-750">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5db0b-751">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5db0b-751">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5db0b-752">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="5db0b-752">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="5db0b-753">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-753">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5db0b-754">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5db0b-754">section0:key0</span></span>
* <span data-ttu-id="5db0b-755">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5db0b-755">section0:key1</span></span>
* <span data-ttu-id="5db0b-756">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="5db0b-756">section1:subsection:key</span></span>
* <span data-ttu-id="5db0b-757">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="5db0b-757">section2:subsection0:key</span></span>
* <span data-ttu-id="5db0b-758">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="5db0b-758">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="5db0b-759">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="5db0b-759">JSON Configuration Provider</span></span>

<span data-ttu-id="5db0b-760">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em runtime.</span><span class="sxs-lookup"><span data-stu-id="5db0b-760">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="5db0b-761">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-761">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5db0b-762">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="5db0b-762">Overloads permit specifying:</span></span>

* <span data-ttu-id="5db0b-763">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="5db0b-763">Whether the file is optional.</span></span>
* <span data-ttu-id="5db0b-764">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-764">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5db0b-765">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-765">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5db0b-766">`AddJsonFile`é chamado automaticamente duas vezes quando um novo Construtor de hosts `CreateDefaultBuilder`é inicializado com o.</span><span class="sxs-lookup"><span data-stu-id="5db0b-766">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5db0b-767">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="5db0b-767">The method is called to load configuration from:</span></span>

* <span data-ttu-id="5db0b-768">*appsettings.json* &ndash; Esse arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="5db0b-768">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="5db0b-769">A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-769">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="5db0b-770">*appSettings. {Environment}. JSON* &ndash; a versão do ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="5db0b-770">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="5db0b-771">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5db0b-771">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5db0b-772">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="5db0b-772">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5db0b-773">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5db0b-773">Environment variables.</span></span>
* <span data-ttu-id="5db0b-774">[Segredos do usuário (Gerenciador de segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5db0b-774">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5db0b-775">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="5db0b-775">Command-line arguments.</span></span>

<span data-ttu-id="5db0b-776">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="5db0b-776">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="5db0b-777">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-777">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="5db0b-778">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-778">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5db0b-779">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="5db0b-779">**Example**</span></span>

<span data-ttu-id="5db0b-780">O aplicativo de exemplo aproveita o método `CreateDefaultBuilder` de conveniência estática para criar o host, que inclui duas chamadas para `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-780">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="5db0b-781">A primeira chamada para `AddJsonFile` carrega a configuração de *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-781">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="5db0b-782">A segunda chamada para `AddJsonFile` carrega a configuração de *appSettings. { Ambiente}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-782">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="5db0b-783">Para *appSettings. Development. JSON* no aplicativo de exemplo, o seguinte arquivo é carregado:</span><span class="sxs-lookup"><span data-stu-id="5db0b-783">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="5db0b-784">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-784">Run the sample app.</span></span> <span data-ttu-id="5db0b-785">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-785">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5db0b-786">A saída contém pares chave-valor para a configuração com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-786">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="5db0b-787">O nível de log para a `Logging:LogLevel:Default` chave `Debug` é ao executar o aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5db0b-787">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="5db0b-788">Execute o aplicativo de exemplo novamente no ambiente de produção:</span><span class="sxs-lookup"><span data-stu-id="5db0b-788">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="5db0b-789">Abra o arquivo *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="5db0b-789">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="5db0b-790">No `ConfigurationSample` perfil, altere o valor da variável de `ASPNETCORE_ENVIRONMENT` ambiente para `Production`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-790">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="5db0b-791">Salve o arquivo e execute o aplicativo com `dotnet run` o em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="5db0b-791">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="5db0b-792">As configurações em *appSettings. O Development. JSON* não substitui mais as configurações em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5db0b-792">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="5db0b-793">O nível de log para a `Logging:LogLevel:Default` chave `Warning`é.</span><span class="sxs-lookup"><span data-stu-id="5db0b-793">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="5db0b-794">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="5db0b-794">XML Configuration Provider</span></span>

<span data-ttu-id="5db0b-795">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="5db0b-795">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="5db0b-796">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-796">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5db0b-797">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="5db0b-797">Overloads permit specifying:</span></span>

* <span data-ttu-id="5db0b-798">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="5db0b-798">Whether the file is optional.</span></span>
* <span data-ttu-id="5db0b-799">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-799">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5db0b-800">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-800">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5db0b-801">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="5db0b-801">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="5db0b-802">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-802">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="5db0b-803">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5db0b-803">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5db0b-804">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="5db0b-804">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="5db0b-805">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-805">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5db0b-806">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5db0b-806">section0:key0</span></span>
* <span data-ttu-id="5db0b-807">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5db0b-807">section0:key1</span></span>
* <span data-ttu-id="5db0b-808">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5db0b-808">section1:key0</span></span>
* <span data-ttu-id="5db0b-809">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5db0b-809">section1:key1</span></span>

<span data-ttu-id="5db0b-810">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="5db0b-810">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="5db0b-811">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-811">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5db0b-812">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="5db0b-812">section:section0:key:key0</span></span>
* <span data-ttu-id="5db0b-813">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="5db0b-813">section:section0:key:key1</span></span>
* <span data-ttu-id="5db0b-814">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="5db0b-814">section:section1:key:key0</span></span>
* <span data-ttu-id="5db0b-815">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="5db0b-815">section:section1:key:key1</span></span>

<span data-ttu-id="5db0b-816">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="5db0b-816">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="5db0b-817">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-817">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5db0b-818">key:attribute</span><span class="sxs-lookup"><span data-stu-id="5db0b-818">key:attribute</span></span>
* <span data-ttu-id="5db0b-819">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="5db0b-819">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="5db0b-820">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="5db0b-820">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="5db0b-821">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-821">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="5db0b-822">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-822">The key is the file name.</span></span> <span data-ttu-id="5db0b-823">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-823">The value contains the file's contents.</span></span> <span data-ttu-id="5db0b-824">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="5db0b-824">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="5db0b-825">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-825">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="5db0b-826">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="5db0b-826">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="5db0b-827">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="5db0b-827">Overloads permit specifying:</span></span>

* <span data-ttu-id="5db0b-828">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="5db0b-828">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="5db0b-829">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="5db0b-829">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="5db0b-830">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-830">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="5db0b-831">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-831">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="5db0b-832">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5db0b-832">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="5db0b-833">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="5db0b-833">Memory Configuration Provider</span></span>

<span data-ttu-id="5db0b-834">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-834">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="5db0b-835">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-835">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5db0b-836">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-836">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="5db0b-837">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-837">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="5db0b-838">No exemplo a seguir, um dicionário de configuração é criado:</span><span class="sxs-lookup"><span data-stu-id="5db0b-838">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="5db0b-839">O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-839">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="5db0b-840">GetValue</span><span class="sxs-lookup"><span data-stu-id="5db0b-840">GetValue</span></span>

<span data-ttu-id="5db0b-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrai um único valor da configuração com uma chave especificada e converte-o para o tipo de não coleção especificado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="5db0b-842">Uma sobrecarga aceita um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="5db0b-842">An overload accepts a default value.</span></span>

<span data-ttu-id="5db0b-843">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5db0b-843">The following example:</span></span>

* <span data-ttu-id="5db0b-844">extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-844">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="5db0b-845">Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-845">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="5db0b-846">Digita o valor como um `int`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-846">Types the value as an `int`.</span></span>
* <span data-ttu-id="5db0b-847">Armazena o valor na propriedade `NumberConfig` para uso pela página.</span><span class="sxs-lookup"><span data-stu-id="5db0b-847">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="5db0b-848">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="5db0b-848">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="5db0b-849">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="5db0b-849">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="5db0b-850">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="5db0b-850">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="5db0b-851">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-851">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="5db0b-852">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5db0b-852">section0:key0</span></span>
* <span data-ttu-id="5db0b-853">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5db0b-853">section0:key1</span></span>
* <span data-ttu-id="5db0b-854">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5db0b-854">section1:key0</span></span>
* <span data-ttu-id="5db0b-855">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5db0b-855">section1:key1</span></span>
* <span data-ttu-id="5db0b-856">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="5db0b-856">section2:subsection0:key0</span></span>
* <span data-ttu-id="5db0b-857">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="5db0b-857">section2:subsection0:key1</span></span>
* <span data-ttu-id="5db0b-858">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="5db0b-858">section2:subsection1:key0</span></span>
* <span data-ttu-id="5db0b-859">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="5db0b-859">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="5db0b-860">GetSection</span><span class="sxs-lookup"><span data-stu-id="5db0b-860">GetSection</span></span>

<span data-ttu-id="5db0b-861">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="5db0b-861">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="5db0b-862">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="5db0b-862">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="5db0b-863">`configSection` não tem um valor, apenas uma chave e um caminho.</span><span class="sxs-lookup"><span data-stu-id="5db0b-863">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="5db0b-864">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="5db0b-864">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="5db0b-865">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-865">`GetSection` never returns `null`.</span></span> <span data-ttu-id="5db0b-866">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-866">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="5db0b-867">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="5db0b-867">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="5db0b-868"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="5db0b-868">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="5db0b-869">GetChildren</span><span class="sxs-lookup"><span data-stu-id="5db0b-869">GetChildren</span></span>

<span data-ttu-id="5db0b-870">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="5db0b-870">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="5db0b-871">Exists</span><span class="sxs-lookup"><span data-stu-id="5db0b-871">Exists</span></span>

<span data-ttu-id="5db0b-872">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-872">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="5db0b-873">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-873">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="5db0b-874">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="5db0b-874">Bind to an object graph</span></span>

<span data-ttu-id="5db0b-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="5db0b-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="5db0b-876">Assim como acontece com a associação de um objeto simples, somente as propriedades públicas de leitura/gravação são associadas.</span><span class="sxs-lookup"><span data-stu-id="5db0b-876">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="5db0b-877">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="5db0b-877">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="5db0b-878">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-878">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="5db0b-879">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-879">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="5db0b-880">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="5db0b-880">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="5db0b-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="5db0b-882">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-882">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="5db0b-883">O código a seguir mostra como usar `Get<T>` o com o exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="5db0b-883">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="5db0b-884">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="5db0b-884">Bind an array to a class</span></span>

<span data-ttu-id="5db0b-885">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="5db0b-885">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="5db0b-886">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-886">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5db0b-887">Qualquer formato de matriz que expõe um segmento de chave`:0:`numérico `:1:`( &hellip; `:{n}:`,,) é capaz de associar a matriz a uma matriz de classe poco.</span><span class="sxs-lookup"><span data-stu-id="5db0b-887">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="5db0b-888">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="5db0b-888">Binding is provided by convention.</span></span> <span data-ttu-id="5db0b-889">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="5db0b-889">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="5db0b-890">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="5db0b-890">**In-memory array processing**</span></span>

<span data-ttu-id="5db0b-891">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="5db0b-891">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="5db0b-892">Chave</span><span class="sxs-lookup"><span data-stu-id="5db0b-892">Key</span></span>             | <span data-ttu-id="5db0b-893">Valor</span><span class="sxs-lookup"><span data-stu-id="5db0b-893">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="5db0b-894">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="5db0b-894">array:entries:0</span></span> | <span data-ttu-id="5db0b-895">value0</span><span class="sxs-lookup"><span data-stu-id="5db0b-895">value0</span></span> |
| <span data-ttu-id="5db0b-896">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="5db0b-896">array:entries:1</span></span> | <span data-ttu-id="5db0b-897">value1</span><span class="sxs-lookup"><span data-stu-id="5db0b-897">value1</span></span> |
| <span data-ttu-id="5db0b-898">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="5db0b-898">array:entries:2</span></span> | <span data-ttu-id="5db0b-899">value2</span><span class="sxs-lookup"><span data-stu-id="5db0b-899">value2</span></span> |
| <span data-ttu-id="5db0b-900">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="5db0b-900">array:entries:4</span></span> | <span data-ttu-id="5db0b-901">value4</span><span class="sxs-lookup"><span data-stu-id="5db0b-901">value4</span></span> |
| <span data-ttu-id="5db0b-902">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="5db0b-902">array:entries:5</span></span> | <span data-ttu-id="5db0b-903">value5</span><span class="sxs-lookup"><span data-stu-id="5db0b-903">value5</span></span> |

<span data-ttu-id="5db0b-904">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="5db0b-904">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="5db0b-905">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="5db0b-905">The array skips a value for index &num;3.</span></span> <span data-ttu-id="5db0b-906">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="5db0b-906">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="5db0b-907">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="5db0b-907">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="5db0b-908">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="5db0b-908">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="5db0b-909">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)a sintaxe também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="5db0b-909">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="5db0b-910">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-910">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="5db0b-911">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="5db0b-911">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="5db0b-912">`ArrayExample.Entries` Valor</span><span class="sxs-lookup"><span data-stu-id="5db0b-912">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="5db0b-913">0</span><span class="sxs-lookup"><span data-stu-id="5db0b-913">0</span></span>                            | <span data-ttu-id="5db0b-914">value0</span><span class="sxs-lookup"><span data-stu-id="5db0b-914">value0</span></span>                       |
| <span data-ttu-id="5db0b-915">1</span><span class="sxs-lookup"><span data-stu-id="5db0b-915">1</span></span>                            | <span data-ttu-id="5db0b-916">value1</span><span class="sxs-lookup"><span data-stu-id="5db0b-916">value1</span></span>                       |
| <span data-ttu-id="5db0b-917">2</span><span class="sxs-lookup"><span data-stu-id="5db0b-917">2</span></span>                            | <span data-ttu-id="5db0b-918">value2</span><span class="sxs-lookup"><span data-stu-id="5db0b-918">value2</span></span>                       |
| <span data-ttu-id="5db0b-919">3</span><span class="sxs-lookup"><span data-stu-id="5db0b-919">3</span></span>                            | <span data-ttu-id="5db0b-920">value4</span><span class="sxs-lookup"><span data-stu-id="5db0b-920">value4</span></span>                       |
| <span data-ttu-id="5db0b-921">4</span><span class="sxs-lookup"><span data-stu-id="5db0b-921">4</span></span>                            | <span data-ttu-id="5db0b-922">value5</span><span class="sxs-lookup"><span data-stu-id="5db0b-922">value5</span></span>                       |

<span data-ttu-id="5db0b-923">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-923">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="5db0b-924">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="5db0b-924">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="5db0b-925">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="5db0b-925">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="5db0b-926">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-926">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="5db0b-927">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="5db0b-927">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="5db0b-928">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-928">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="5db0b-929">Em `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="5db0b-929">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="5db0b-930">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-930">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="5db0b-931">Chave</span><span class="sxs-lookup"><span data-stu-id="5db0b-931">Key</span></span>             | <span data-ttu-id="5db0b-932">Valor</span><span class="sxs-lookup"><span data-stu-id="5db0b-932">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="5db0b-933">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="5db0b-933">array:entries:3</span></span> | <span data-ttu-id="5db0b-934">value3</span><span class="sxs-lookup"><span data-stu-id="5db0b-934">value3</span></span> |

<span data-ttu-id="5db0b-935">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="5db0b-935">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="5db0b-936">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="5db0b-936">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="5db0b-937">`ArrayExample.Entries` Valor</span><span class="sxs-lookup"><span data-stu-id="5db0b-937">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="5db0b-938">0</span><span class="sxs-lookup"><span data-stu-id="5db0b-938">0</span></span>                            | <span data-ttu-id="5db0b-939">value0</span><span class="sxs-lookup"><span data-stu-id="5db0b-939">value0</span></span>                       |
| <span data-ttu-id="5db0b-940">1</span><span class="sxs-lookup"><span data-stu-id="5db0b-940">1</span></span>                            | <span data-ttu-id="5db0b-941">value1</span><span class="sxs-lookup"><span data-stu-id="5db0b-941">value1</span></span>                       |
| <span data-ttu-id="5db0b-942">2</span><span class="sxs-lookup"><span data-stu-id="5db0b-942">2</span></span>                            | <span data-ttu-id="5db0b-943">value2</span><span class="sxs-lookup"><span data-stu-id="5db0b-943">value2</span></span>                       |
| <span data-ttu-id="5db0b-944">3</span><span class="sxs-lookup"><span data-stu-id="5db0b-944">3</span></span>                            | <span data-ttu-id="5db0b-945">value3</span><span class="sxs-lookup"><span data-stu-id="5db0b-945">value3</span></span>                       |
| <span data-ttu-id="5db0b-946">4</span><span class="sxs-lookup"><span data-stu-id="5db0b-946">4</span></span>                            | <span data-ttu-id="5db0b-947">value4</span><span class="sxs-lookup"><span data-stu-id="5db0b-947">value4</span></span>                       |
| <span data-ttu-id="5db0b-948">5</span><span class="sxs-lookup"><span data-stu-id="5db0b-948">5</span></span>                            | <span data-ttu-id="5db0b-949">value5</span><span class="sxs-lookup"><span data-stu-id="5db0b-949">value5</span></span>                       |

<span data-ttu-id="5db0b-950">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="5db0b-950">**JSON array processing**</span></span>

<span data-ttu-id="5db0b-951">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="5db0b-951">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="5db0b-952">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="5db0b-952">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="5db0b-953">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="5db0b-953">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="5db0b-954">Chave</span><span class="sxs-lookup"><span data-stu-id="5db0b-954">Key</span></span>                     | <span data-ttu-id="5db0b-955">Valor</span><span class="sxs-lookup"><span data-stu-id="5db0b-955">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="5db0b-956">json_array:key</span><span class="sxs-lookup"><span data-stu-id="5db0b-956">json_array:key</span></span>          | <span data-ttu-id="5db0b-957">valueA</span><span class="sxs-lookup"><span data-stu-id="5db0b-957">valueA</span></span> |
| <span data-ttu-id="5db0b-958">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="5db0b-958">json_array:subsection:0</span></span> | <span data-ttu-id="5db0b-959">valueB</span><span class="sxs-lookup"><span data-stu-id="5db0b-959">valueB</span></span> |
| <span data-ttu-id="5db0b-960">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="5db0b-960">json_array:subsection:1</span></span> | <span data-ttu-id="5db0b-961">valueC</span><span class="sxs-lookup"><span data-stu-id="5db0b-961">valueC</span></span> |
| <span data-ttu-id="5db0b-962">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="5db0b-962">json_array:subsection:2</span></span> | <span data-ttu-id="5db0b-963">valueD</span><span class="sxs-lookup"><span data-stu-id="5db0b-963">valueD</span></span> |

<span data-ttu-id="5db0b-964">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="5db0b-964">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="5db0b-965">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-965">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="5db0b-966">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-966">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="5db0b-967">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="5db0b-967">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="5db0b-968">`JsonArrayExample.Subsection` Valor</span><span class="sxs-lookup"><span data-stu-id="5db0b-968">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="5db0b-969">0</span><span class="sxs-lookup"><span data-stu-id="5db0b-969">0</span></span>                                   | <span data-ttu-id="5db0b-970">valueB</span><span class="sxs-lookup"><span data-stu-id="5db0b-970">valueB</span></span>                              |
| <span data-ttu-id="5db0b-971">1</span><span class="sxs-lookup"><span data-stu-id="5db0b-971">1</span></span>                                   | <span data-ttu-id="5db0b-972">valueC</span><span class="sxs-lookup"><span data-stu-id="5db0b-972">valueC</span></span>                              |
| <span data-ttu-id="5db0b-973">2</span><span class="sxs-lookup"><span data-stu-id="5db0b-973">2</span></span>                                   | <span data-ttu-id="5db0b-974">valueD</span><span class="sxs-lookup"><span data-stu-id="5db0b-974">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="5db0b-975">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="5db0b-975">Custom configuration provider</span></span>

<span data-ttu-id="5db0b-976">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="5db0b-976">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="5db0b-977">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="5db0b-977">The provider has the following characteristics:</span></span>

* <span data-ttu-id="5db0b-978">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-978">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="5db0b-979">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="5db0b-979">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="5db0b-980">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="5db0b-980">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="5db0b-981">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="5db0b-981">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="5db0b-982">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-982">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="5db0b-983">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5db0b-983">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="5db0b-984">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-984">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="5db0b-985">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="5db0b-985">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="5db0b-986">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-986">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="5db0b-987">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-987">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="5db0b-988">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-988">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="5db0b-989">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-989">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="5db0b-990">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="5db0b-990">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="5db0b-991">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-991">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="5db0b-992">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-992">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="5db0b-993">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-993">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="5db0b-994">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5db0b-994">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="5db0b-995">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="5db0b-995">Access configuration during startup</span></span>

<span data-ttu-id="5db0b-996">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5db0b-996">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5db0b-997">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="5db0b-997">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="5db0b-998">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="5db0b-998">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="5db0b-999">Acessar a configuração em Razor uma página de páginas ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="5db0b-999">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="5db0b-1000">Para acessar as definições de configuração Razor em uma página de páginas ou em uma exibição do MVC, adicione uma [diretiva using](xref:mvc/views/razor#using) ([referência C#: usando a diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespace Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) e insira <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="5db0b-1000">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="5db0b-1001">Em uma Razor página de páginas:</span><span class="sxs-lookup"><span data-stu-id="5db0b-1001">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="5db0b-1002">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="5db0b-1002">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="5db0b-1003">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="5db0b-1003">Add configuration from an external assembly</span></span>

<span data-ttu-id="5db0b-1004">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5db0b-1004">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="5db0b-1005">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5db0b-1005">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5db0b-1006">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5db0b-1006">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

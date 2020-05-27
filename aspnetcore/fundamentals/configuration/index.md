---
<span data-ttu-id="8a405-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-102">'Blazor'</span></span>
- <span data-ttu-id="8a405-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-103">'Identity'</span></span>
- <span data-ttu-id="8a405-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-105">'Razor'</span></span>
- <span data-ttu-id="8a405-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="8a405-107">Configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a405-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="8a405-108">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="8a405-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8a405-109">A configuração no ASP.NET Core é executada usando um ou mais [provedores de configuração](#cp).</span><span class="sxs-lookup"><span data-stu-id="8a405-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="8a405-110">Os provedores de configuração lêem dados de configuração de pares chave-valor usando uma variedade de fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="8a405-111">Arquivos de configurações, como *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="8a405-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="8a405-112">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="8a405-112">Environment variables</span></span>
* <span data-ttu-id="8a405-113">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="8a405-113">Azure Key Vault</span></span>
* <span data-ttu-id="8a405-114">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="8a405-114">Azure App Configuration</span></span>
* <span data-ttu-id="8a405-115">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="8a405-115">Command-line arguments</span></span>
* <span data-ttu-id="8a405-116">Provedores personalizados, instalados ou criados</span><span class="sxs-lookup"><span data-stu-id="8a405-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="8a405-117">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="8a405-117">Directory files</span></span>
* <span data-ttu-id="8a405-118">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="8a405-118">In-memory .NET objects</span></span>

<span data-ttu-id="8a405-119">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a405-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="8a405-120">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="8a405-120">Default configuration</span></span>

<span data-ttu-id="8a405-121">ASP.NET Core aplicativos Web criados com [dotnet novo](/dotnet/core/tools/dotnet-new) ou o Visual Studio geram o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="8a405-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="8a405-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="8a405-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="8a405-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Adiciona um existente `IConfiguration` como uma origem.</span><span class="sxs-lookup"><span data-stu-id="8a405-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="8a405-124">No caso de configuração padrão, o adiciona a configuração do [host](#hvac) e a define como a primeira origem para a configuração do _aplicativo_ .</span><span class="sxs-lookup"><span data-stu-id="8a405-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="8a405-125">[appSettings. JSON](#appsettingsjson) usando o [provedor de configuração JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8a405-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="8a405-126">*appSettings.* `Environment` *. JSON* usando o [provedor de configuração JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8a405-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="8a405-127">Por exemplo, *appSettings*. ***Produção***. *JSON* e *appSettings*. ***Desenvolvimento***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="8a405-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="8a405-128">[Segredos do aplicativo](xref:security/app-secrets) quando o aplicativo é executado no `Development` ambiente.</span><span class="sxs-lookup"><span data-stu-id="8a405-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="8a405-129">Variáveis de ambiente usando o [provedor de configuração de variáveis de ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="8a405-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8a405-130">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line).</span><span class="sxs-lookup"><span data-stu-id="8a405-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="8a405-131">Os provedores de configuração adicionados posteriormente substituem as configurações de chave anteriores.</span><span class="sxs-lookup"><span data-stu-id="8a405-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="8a405-132">Por exemplo, se `MyKey` for definido em *appSettings. JSON* e no ambiente, o valor de ambiente será usado.</span><span class="sxs-lookup"><span data-stu-id="8a405-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="8a405-133">Usando os provedores de configuração padrão, o [provedor de configuração de linha de comando](#command-line-configuration-provider) substitui todos os outros provedores.</span><span class="sxs-lookup"><span data-stu-id="8a405-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="8a405-134">Para obter mais informações sobre o `CreateDefaultBuilder` , consulte [configurações padrão do Construtor](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="8a405-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="8a405-135">O código a seguir exibe os provedores de configuração habilitados na ordem em que foram adicionados:</span><span class="sxs-lookup"><span data-stu-id="8a405-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="8a405-136">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="8a405-136">appsettings.json</span></span>

<span data-ttu-id="8a405-137">Considere o seguinte arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8a405-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8a405-138">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="8a405-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-139">O padrão <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="8a405-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="8a405-140">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8a405-140">*appsettings.json*</span></span>
1. <span data-ttu-id="8a405-141">*appSettings.* `Environment` *. JSON* : por exemplo, *appSettings*. ***Produção***. *JSON* e *appSettings*. ***Desenvolvimento***. arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="8a405-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="8a405-142">A versão de ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="8a405-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="8a405-143">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="8a405-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8a405-144">*appSettings*. `Environment` . valores *JSON* substituem chaves em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8a405-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="8a405-145">Por exemplo, por padrão:</span><span class="sxs-lookup"><span data-stu-id="8a405-145">For example, by default:</span></span>

* <span data-ttu-id="8a405-146">Em desenvolvimento, *appSettings*. ***Desenvolvimento***. a configuração *JSON* substitui os valores encontrados em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8a405-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="8a405-147">Em produção, *appSettings*. ***Produção***. a configuração *JSON* substitui os valores encontrados em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8a405-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="8a405-148">Por exemplo, ao implantar o aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="8a405-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="8a405-149">Associar dados de configuração hierárquica usando o padrão de opções</span><span class="sxs-lookup"><span data-stu-id="8a405-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="8a405-150">Usando a configuração [padrão](#default) , *appSettings. JSON* e *appSettings.* `Environment` os arquivos *. JSON* são habilitados com [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="8a405-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="8a405-151">As alterações feitas em *appSettings. JSON* e *appSettings.* `Environment` o arquivo *. JSON* ***após*** o início do aplicativo é lido pelo [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="8a405-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8a405-152">Consulte [provedor de configuração JSON](#jcp) neste documento para obter informações sobre como adicionar arquivos de configuração JSON adicionais.</span><span class="sxs-lookup"><span data-stu-id="8a405-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="8a405-153">Gerenciador de segurança e segredo</span><span class="sxs-lookup"><span data-stu-id="8a405-153">Security and secret manager</span></span>

<span data-ttu-id="8a405-154">Diretrizes de dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="8a405-155">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="8a405-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="8a405-156">O [Gerenciador de segredo](xref:security/app-secrets) pode ser usado para armazenar segredos no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a405-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="8a405-157">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a405-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8a405-158">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="8a405-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8a405-159">Por [padrão](#default), o [Gerenciador de segredo](xref:security/app-secrets) lê as definições de configuração após *appSettings. JSON* e *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8a405-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="8a405-160">Para obter mais informações sobre como armazenar senhas ou outros dados confidenciais:</span><span class="sxs-lookup"><span data-stu-id="8a405-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8a405-161"><xref:security/app-secrets>: Inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="8a405-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8a405-162">O Gerenciador de segredo usa o [provedor de configuração de arquivo](#fcp) para armazenar segredos de usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="8a405-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="8a405-163">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a405-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8a405-164">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8a405-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="8a405-165">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="8a405-165">Environment variables</span></span>

<span data-ttu-id="8a405-166">Usando a configuração [padrão](#default) , o <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração de pares chave-valor da variável de ambiente depois de ler *appSettings. JSON*, *appSettings.* `Environment` *. JSON*e o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8a405-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="8a405-167">Portanto, os valores de chave lidos dos valores de substituição de ambiente lidos de *appSettings. JSON*, *appSettings.* `Environment` *. JSON*e o Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="8a405-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8a405-168">Os seguintes `set` comandos:</span><span class="sxs-lookup"><span data-stu-id="8a405-168">The following `set` commands:</span></span>

* <span data-ttu-id="8a405-169">Defina as chaves de ambiente e os valores do [exemplo anterior](#appsettingsjson) no Windows.</span><span class="sxs-lookup"><span data-stu-id="8a405-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="8a405-170">Teste as configurações ao usar o [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="8a405-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="8a405-171">O `dotnet run` comando deve ser executado no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="8a405-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="8a405-172">As configurações de ambiente anteriores:</span><span class="sxs-lookup"><span data-stu-id="8a405-172">The preceding environment settings:</span></span>

* <span data-ttu-id="8a405-173">São definidos apenas em processos iniciados na janela de comando em que foram definidos.</span><span class="sxs-lookup"><span data-stu-id="8a405-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="8a405-174">Não serão lidos por navegadores iniciados com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8a405-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="8a405-175">Os comandos [setx](/windows-server/administration/windows-commands/setx) a seguir podem ser usados para definir as chaves de ambiente e os valores no Windows.</span><span class="sxs-lookup"><span data-stu-id="8a405-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="8a405-176">Ao contrário `set` de, `setx` as configurações são persistidas.</span><span class="sxs-lookup"><span data-stu-id="8a405-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="8a405-177">`/M`define a variável no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="8a405-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="8a405-178">Se a `/M` opção não for usada, uma variável de ambiente do usuário será definida.</span><span class="sxs-lookup"><span data-stu-id="8a405-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="8a405-179">Para testar se os comandos anteriores substituem *appSettings. JSON* e *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="8a405-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="8a405-180">Com o Visual Studio: saia e reinicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8a405-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="8a405-181">Com a CLI: iniciar uma nova janela de comando e Enter `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="8a405-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="8a405-182">Chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> com uma cadeia de caracteres para especificar um prefixo para variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="8a405-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="8a405-183">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="8a405-183">In the preceding code:</span></span>

* <span data-ttu-id="8a405-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`é adicionado após os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="8a405-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8a405-185">Para obter um exemplo de como ordenar os provedores de configuração, consulte [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="8a405-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="8a405-186">Variáveis de ambiente definidas com o `MyCustomPrefix_` prefixo substituem os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="8a405-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="8a405-187">Isso inclui variáveis de ambiente sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="8a405-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="8a405-188">O prefixo é eliminado quando os pares chave-valor de configuração são lidos.</span><span class="sxs-lookup"><span data-stu-id="8a405-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="8a405-189">Os comandos a seguir testam o prefixo personalizado:</span><span class="sxs-lookup"><span data-stu-id="8a405-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="8a405-190">A [configuração padrão](#default) carrega variáveis de ambiente e argumentos de linha de comando prefixados com `DOTNET_` and `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="8a405-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="8a405-191">Os `DOTNET_` `ASPNETCORE_` prefixos e são usados por ASP.NET Core para [configuração de host e aplicativo](xref:fundamentals/host/generic-host#host-configuration), mas não para a configuração do usuário.</span><span class="sxs-lookup"><span data-stu-id="8a405-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="8a405-192">Para obter mais informações sobre a configuração de host e aplicativo, consulte [host genérico .net](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="8a405-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="8a405-193">Em [Azure app serviço](https://azure.microsoft.com/services/app-service/), selecione **nova configuração de aplicativo** na página **configurações > configuração** .</span><span class="sxs-lookup"><span data-stu-id="8a405-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="8a405-194">Azure App configurações do aplicativo de serviço são:</span><span class="sxs-lookup"><span data-stu-id="8a405-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="8a405-195">Criptografado em repouso e transmitido por um canal criptografado.</span><span class="sxs-lookup"><span data-stu-id="8a405-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="8a405-196">Exposto como variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="8a405-196">Exposed as environment variables.</span></span>

<span data-ttu-id="8a405-197">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="8a405-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8a405-198">Consulte [prefixos de cadeia de conexão](#constr) para obter informações sobre cadeias de conexão do banco de dados</span><span class="sxs-lookup"><span data-stu-id="8a405-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="8a405-199">Linha de comando</span><span class="sxs-lookup"><span data-stu-id="8a405-199">Command-line</span></span>

<span data-ttu-id="8a405-200">Usando a configuração [padrão](#default) , o <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração de pares de chave-valor de argumento de linha de comando após as seguintes fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="8a405-201">*appSettings. JSON* e *appSettings*. `Environment` . arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="8a405-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8a405-202">[Segredos do aplicativo (Gerenciador de segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a405-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8a405-203">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="8a405-203">Environment variables.</span></span>

<span data-ttu-id="8a405-204">Por [padrão](#default), os valores de configuração definidos nos valores de configuração de substituição de linha de comando são definidos com todos os outros provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="8a405-205">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="8a405-205">Command-line arguments</span></span>

<span data-ttu-id="8a405-206">O comando a seguir define chaves e valores usando `=` :</span><span class="sxs-lookup"><span data-stu-id="8a405-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="8a405-207">O comando a seguir define chaves e valores usando `/` :</span><span class="sxs-lookup"><span data-stu-id="8a405-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="8a405-208">O comando a seguir define chaves e valores usando `--` :</span><span class="sxs-lookup"><span data-stu-id="8a405-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="8a405-209">O valor da chave:</span><span class="sxs-lookup"><span data-stu-id="8a405-209">The key value:</span></span>

* <span data-ttu-id="8a405-210">Deve seguir `=` , ou a chave deve ter um prefixo `--` ou `/` quando o valor segue um espaço.</span><span class="sxs-lookup"><span data-stu-id="8a405-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="8a405-211">Não será necessário se `=` for usado.</span><span class="sxs-lookup"><span data-stu-id="8a405-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="8a405-212">Por exemplo, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="8a405-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="8a405-213">No mesmo comando, não misture pares de chave-valor de argumento de linha de comando que usam `=` com pares de chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="8a405-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="8a405-214">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="8a405-214">Switch mappings</span></span>

<span data-ttu-id="8a405-215">Os mapeamentos de switch permitem a lógica de substituição de nome de **chave** .</span><span class="sxs-lookup"><span data-stu-id="8a405-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="8a405-216">Forneça um dicionário de substituições de switch para o <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> método.</span><span class="sxs-lookup"><span data-stu-id="8a405-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8a405-217">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="8a405-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8a405-218">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8a405-219">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="8a405-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8a405-220">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="8a405-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8a405-221">Os comutadores devem começar com `-` ou `--` .</span><span class="sxs-lookup"><span data-stu-id="8a405-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="8a405-222">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="8a405-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8a405-223">Para usar um dicionário de mapeamentos de opção, passe-o para a chamada para `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="8a405-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="8a405-224">O código a seguir mostra os valores de chave para as chaves substituídas:</span><span class="sxs-lookup"><span data-stu-id="8a405-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-225">Execute o seguinte comando para testar a chave substituta:</span><span class="sxs-lookup"><span data-stu-id="8a405-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="8a405-226">Observação: no momento, `=` não pode ser usado para definir valores de substituição de chave com um único traço `-` .</span><span class="sxs-lookup"><span data-stu-id="8a405-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="8a405-227">Consulte [este problema do GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="8a405-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="8a405-228">O comando a seguir funciona para testar a substituição da chave:</span><span class="sxs-lookup"><span data-stu-id="8a405-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="8a405-229">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="8a405-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8a405-230">A `CreateDefaultBuilder` chamada do método `AddCommandLine` não inclui opções mapeadas e não há como passar o dicionário de mapeamento de opção para `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="8a405-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8a405-231">A solução não é passar os argumentos para `CreateDefaultBuilder` , mas sim permitir que o `ConfigurationBuilder` método do método `AddCommandLine` processe os argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="8a405-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8a405-232">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="8a405-232">Hierarchical configuration data</span></span>

<span data-ttu-id="8a405-233">A API de configuração lê dados de configuração hierárquicas mesclando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8a405-234">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8a405-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8a405-235">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações:</span><span class="sxs-lookup"><span data-stu-id="8a405-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-236">A maneira preferida de ler dados de configuração hierárquicos é usar o padrão de opções.</span><span class="sxs-lookup"><span data-stu-id="8a405-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="8a405-237">Para obter mais informações, consulte [associar dados de configuração hierárquica](#optpat) neste documento.</span><span class="sxs-lookup"><span data-stu-id="8a405-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="8a405-238">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8a405-239">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="8a405-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="8a405-240">Chaves de configuração e valores</span><span class="sxs-lookup"><span data-stu-id="8a405-240">Configuration keys and values</span></span>

<span data-ttu-id="8a405-241">Chaves de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-241">Configuration keys:</span></span>

* <span data-ttu-id="8a405-242">Não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="8a405-242">Are case-insensitive.</span></span> <span data-ttu-id="8a405-243">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="8a405-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8a405-244">Se uma chave e um valor for definido em mais de um provedor de configuração, o valor do último provedor adicionado será usado.</span><span class="sxs-lookup"><span data-stu-id="8a405-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="8a405-245">Para obter mais informações, consulte [configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="8a405-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="8a405-246">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="8a405-246">Hierarchical keys</span></span>
  * <span data-ttu-id="8a405-247">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="8a405-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8a405-248">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="8a405-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8a405-249">Um sublinhado duplo, `__` , tem suporte em todas as plataformas e é automaticamente convertido em dois-pontos `:` .</span><span class="sxs-lookup"><span data-stu-id="8a405-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="8a405-250">Em Azure Key Vault, as chaves hierárquicas usam `--` como um separador.</span><span class="sxs-lookup"><span data-stu-id="8a405-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="8a405-251">O [provedor de configuração Azure Key Vault](xref:security/key-vault-configuration) substitui automaticamente `--` por um `:` quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8a405-252">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8a405-253">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="8a405-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="8a405-254">Valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-254">Configuration values:</span></span>

* <span data-ttu-id="8a405-255">São cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="8a405-255">Are strings.</span></span>
* <span data-ttu-id="8a405-256">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="8a405-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="8a405-257">Provedores de configuração</span><span class="sxs-lookup"><span data-stu-id="8a405-257">Configuration providers</span></span>

<span data-ttu-id="8a405-258">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a405-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8a405-259">Provedor</span><span class="sxs-lookup"><span data-stu-id="8a405-259">Provider</span></span> | <span data-ttu-id="8a405-260">Fornece a configuração de </span><span class="sxs-lookup"><span data-stu-id="8a405-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="8a405-261">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-262">'Blazor'</span></span>
- <span data-ttu-id="8a405-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-263">'Identity'</span></span>
- <span data-ttu-id="8a405-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-265">'Razor'</span></span>
- <span data-ttu-id="8a405-266">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-267">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-268">'Blazor'</span></span>
- <span data-ttu-id="8a405-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-269">'Identity'</span></span>
- <span data-ttu-id="8a405-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-271">'Razor'</span></span>
- <span data-ttu-id="8a405-272">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-272">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-273">---- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-274">'Blazor'</span></span>
- <span data-ttu-id="8a405-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-275">'Identity'</span></span>
- <span data-ttu-id="8a405-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-277">'Razor'</span></span>
- <span data-ttu-id="8a405-278">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-279">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-280">'Blazor'</span></span>
- <span data-ttu-id="8a405-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-281">'Identity'</span></span>
- <span data-ttu-id="8a405-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-283">'Razor'</span></span>
- <span data-ttu-id="8a405-284">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-285">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-286">'Blazor'</span></span>
- <span data-ttu-id="8a405-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-287">'Identity'</span></span>
- <span data-ttu-id="8a405-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-289">'Razor'</span></span>
- <span data-ttu-id="8a405-290">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-291">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-292">'Blazor'</span></span>
- <span data-ttu-id="8a405-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-293">'Identity'</span></span>
- <span data-ttu-id="8a405-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-295">'Razor'</span></span>
- <span data-ttu-id="8a405-296">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-297">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-298">'Blazor'</span></span>
- <span data-ttu-id="8a405-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-299">'Identity'</span></span>
- <span data-ttu-id="8a405-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-301">'Razor'</span></span>
- <span data-ttu-id="8a405-302">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-303">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-304">'Blazor'</span></span>
- <span data-ttu-id="8a405-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-305">'Identity'</span></span>
- <span data-ttu-id="8a405-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-307">'Razor'</span></span>
- <span data-ttu-id="8a405-308">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-309">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-310">'Blazor'</span></span>
- <span data-ttu-id="8a405-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-311">'Identity'</span></span>
- <span data-ttu-id="8a405-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-313">'Razor'</span></span>
- <span data-ttu-id="8a405-314">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-315">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-316">'Blazor'</span></span>
- <span data-ttu-id="8a405-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-317">'Identity'</span></span>
- <span data-ttu-id="8a405-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-319">'Razor'</span></span>
- <span data-ttu-id="8a405-320">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-321">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-322">'Blazor'</span></span>
- <span data-ttu-id="8a405-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-323">'Identity'</span></span>
- <span data-ttu-id="8a405-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-325">'Razor'</span></span>
- <span data-ttu-id="8a405-326">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-327">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-328">'Blazor'</span></span>
- <span data-ttu-id="8a405-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-329">'Identity'</span></span>
- <span data-ttu-id="8a405-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-331">'Razor'</span></span>
- <span data-ttu-id="8a405-332">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-333">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-334">'Blazor'</span></span>
- <span data-ttu-id="8a405-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-335">'Identity'</span></span>
- <span data-ttu-id="8a405-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-337">'Razor'</span></span>
- <span data-ttu-id="8a405-338">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-339">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-340">'Blazor'</span></span>
- <span data-ttu-id="8a405-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-341">'Identity'</span></span>
- <span data-ttu-id="8a405-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-343">'Razor'</span></span>
- <span data-ttu-id="8a405-344">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-345">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-346">'Blazor'</span></span>
- <span data-ttu-id="8a405-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-347">'Identity'</span></span>
- <span data-ttu-id="8a405-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-349">'Razor'</span></span>
- <span data-ttu-id="8a405-350">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-351">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-352">'Blazor'</span></span>
- <span data-ttu-id="8a405-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-353">'Identity'</span></span>
- <span data-ttu-id="8a405-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-355">'Razor'</span></span>
- <span data-ttu-id="8a405-356">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-357">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-358">'Blazor'</span></span>
- <span data-ttu-id="8a405-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-359">'Identity'</span></span>
- <span data-ttu-id="8a405-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-361">'Razor'</span></span>
- <span data-ttu-id="8a405-362">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-362">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-363">------------------ | | [Provedor de configuração de Azure Key Vault](xref:security/key-vault-configuration) | Azure Key Vault | | [Provedor de configuração de Azure app](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Configuração de Azure App | | [Provedor de configuração de linha de comando](#clcp) | Parâmetros de linha de comando | | [Provedor de configuração personalizada](#custom-configuration-provider) | Fonte personalizada | | [Provedor de configuração de variáveis de ambiente](#evcp) | Variáveis de ambiente | | [Provedor de configuração de arquivo](#file-configuration-provider) | Arquivos INI, JSON e XML | | [Provedor de configuração de chave por arquivo](#key-per-file-configuration-provider) | Arquivos de diretório | | [Provedor de configuração de memória](#memory-configuration-provider) | Coleções na memória | | [Gerenciador de segredo](xref:security/app-secrets) | Arquivo no diretório de perfil do usuário |</span><span class="sxs-lookup"><span data-stu-id="8a405-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="8a405-364">As fontes de configuração são lidas na ordem em que seus provedores de configuração são especificados.</span><span class="sxs-lookup"><span data-stu-id="8a405-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="8a405-365">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="8a405-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8a405-366">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="8a405-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8a405-367">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8a405-367">*appsettings.json*</span></span>
1. <span data-ttu-id="8a405-368">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="8a405-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="8a405-369">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="8a405-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="8a405-370">Variáveis de ambiente usando o [provedor de configuração de variáveis de ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="8a405-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8a405-371">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8a405-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="8a405-372">Uma prática comum é adicionar o provedor de configuração de linha de comando pela última vez em uma série de provedores para permitir que argumentos de linha de comando substituam a configuração definida pelos outros provedores.</span><span class="sxs-lookup"><span data-stu-id="8a405-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8a405-373">A sequência anterior de provedores é usada na [configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="8a405-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="8a405-374">Prefixos de cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="8a405-374">Connection string prefixes</span></span>

<span data-ttu-id="8a405-375">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="8a405-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="8a405-376">Essas cadeias de conexão estão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8a405-377">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo com a [configuração padrão](#default) ou quando nenhum prefixo é fornecido para `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="8a405-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8a405-378">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="8a405-378">Connection string prefix</span></span> | <span data-ttu-id="8a405-379">Provedor</span><span class="sxs-lookup"><span data-stu-id="8a405-379">Provider</span></span> |
| ---
<span data-ttu-id="8a405-380">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-381">'Blazor'</span></span>
- <span data-ttu-id="8a405-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-382">'Identity'</span></span>
- <span data-ttu-id="8a405-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-384">'Razor'</span></span>
- <span data-ttu-id="8a405-385">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-386">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-387">'Blazor'</span></span>
- <span data-ttu-id="8a405-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-388">'Identity'</span></span>
- <span data-ttu-id="8a405-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-390">'Razor'</span></span>
- <span data-ttu-id="8a405-391">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-392">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-393">'Blazor'</span></span>
- <span data-ttu-id="8a405-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-394">'Identity'</span></span>
- <span data-ttu-id="8a405-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-396">'Razor'</span></span>
- <span data-ttu-id="8a405-397">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-398">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-399">'Blazor'</span></span>
- <span data-ttu-id="8a405-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-400">'Identity'</span></span>
- <span data-ttu-id="8a405-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-402">'Razor'</span></span>
- <span data-ttu-id="8a405-403">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-404">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-405">'Blazor'</span></span>
- <span data-ttu-id="8a405-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-406">'Identity'</span></span>
- <span data-ttu-id="8a405-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-408">'Razor'</span></span>
- <span data-ttu-id="8a405-409">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-410">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-411">'Blazor'</span></span>
- <span data-ttu-id="8a405-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-412">'Identity'</span></span>
- <span data-ttu-id="8a405-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-414">'Razor'</span></span>
- <span data-ttu-id="8a405-415">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-416">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-417">'Blazor'</span></span>
- <span data-ttu-id="8a405-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-418">'Identity'</span></span>
- <span data-ttu-id="8a405-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-420">'Razor'</span></span>
- <span data-ttu-id="8a405-421">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-422">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-423">'Blazor'</span></span>
- <span data-ttu-id="8a405-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-424">'Identity'</span></span>
- <span data-ttu-id="8a405-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-426">'Razor'</span></span>
- <span data-ttu-id="8a405-427">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-428">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-429">'Blazor'</span></span>
- <span data-ttu-id="8a405-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-430">'Identity'</span></span>
- <span data-ttu-id="8a405-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-432">'Razor'</span></span>
- <span data-ttu-id="8a405-433">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-434">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-435">'Blazor'</span></span>
- <span data-ttu-id="8a405-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-436">'Identity'</span></span>
- <span data-ttu-id="8a405-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-438">'Razor'</span></span>
- <span data-ttu-id="8a405-439">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-439">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-440">------------ | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-441">'Blazor'</span></span>
- <span data-ttu-id="8a405-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-442">'Identity'</span></span>
- <span data-ttu-id="8a405-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-444">'Razor'</span></span>
- <span data-ttu-id="8a405-445">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-446">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-447">'Blazor'</span></span>
- <span data-ttu-id="8a405-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-448">'Identity'</span></span>
- <span data-ttu-id="8a405-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-450">'Razor'</span></span>
- <span data-ttu-id="8a405-451">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-451">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-452">---- | | `CUSTOMCONNSTR_` | Provedor personalizado | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | [SQL Server do banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="8a405-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="8a405-453">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="8a405-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8a405-454">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="8a405-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8a405-455">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="8a405-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8a405-456">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="8a405-456">Environment variable key</span></span> | <span data-ttu-id="8a405-457">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="8a405-457">Converted configuration key</span></span> | <span data-ttu-id="8a405-458">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="8a405-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="8a405-459">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-460">'Blazor'</span></span>
- <span data-ttu-id="8a405-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-461">'Identity'</span></span>
- <span data-ttu-id="8a405-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-463">'Razor'</span></span>
- <span data-ttu-id="8a405-464">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-465">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-466">'Blazor'</span></span>
- <span data-ttu-id="8a405-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-467">'Identity'</span></span>
- <span data-ttu-id="8a405-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-469">'Razor'</span></span>
- <span data-ttu-id="8a405-470">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-471">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-472">'Blazor'</span></span>
- <span data-ttu-id="8a405-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-473">'Identity'</span></span>
- <span data-ttu-id="8a405-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-475">'Razor'</span></span>
- <span data-ttu-id="8a405-476">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-477">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-478">'Blazor'</span></span>
- <span data-ttu-id="8a405-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-479">'Identity'</span></span>
- <span data-ttu-id="8a405-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-481">'Razor'</span></span>
- <span data-ttu-id="8a405-482">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-483">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-484">'Blazor'</span></span>
- <span data-ttu-id="8a405-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-485">'Identity'</span></span>
- <span data-ttu-id="8a405-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-487">'Razor'</span></span>
- <span data-ttu-id="8a405-488">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-489">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-490">'Blazor'</span></span>
- <span data-ttu-id="8a405-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-491">'Identity'</span></span>
- <span data-ttu-id="8a405-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-493">'Razor'</span></span>
- <span data-ttu-id="8a405-494">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-495">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-496">'Blazor'</span></span>
- <span data-ttu-id="8a405-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-497">'Identity'</span></span>
- <span data-ttu-id="8a405-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-499">'Razor'</span></span>
- <span data-ttu-id="8a405-500">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-501">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-502">'Blazor'</span></span>
- <span data-ttu-id="8a405-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-503">'Identity'</span></span>
- <span data-ttu-id="8a405-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-505">'Razor'</span></span>
- <span data-ttu-id="8a405-506">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-507">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-508">'Blazor'</span></span>
- <span data-ttu-id="8a405-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-509">'Identity'</span></span>
- <span data-ttu-id="8a405-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-511">'Razor'</span></span>
- <span data-ttu-id="8a405-512">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-513">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-514">'Blazor'</span></span>
- <span data-ttu-id="8a405-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-515">'Identity'</span></span>
- <span data-ttu-id="8a405-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-517">'Razor'</span></span>
- <span data-ttu-id="8a405-518">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-518">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-519">------------ | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-520">'Blazor'</span></span>
- <span data-ttu-id="8a405-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-521">'Identity'</span></span>
- <span data-ttu-id="8a405-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-523">'Razor'</span></span>
- <span data-ttu-id="8a405-524">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-525">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-526">'Blazor'</span></span>
- <span data-ttu-id="8a405-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-527">'Identity'</span></span>
- <span data-ttu-id="8a405-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-529">'Razor'</span></span>
- <span data-ttu-id="8a405-530">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-531">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-532">'Blazor'</span></span>
- <span data-ttu-id="8a405-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-533">'Identity'</span></span>
- <span data-ttu-id="8a405-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-535">'Razor'</span></span>
- <span data-ttu-id="8a405-536">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-537">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-538">'Blazor'</span></span>
- <span data-ttu-id="8a405-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-539">'Identity'</span></span>
- <span data-ttu-id="8a405-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-541">'Razor'</span></span>
- <span data-ttu-id="8a405-542">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-543">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-544">'Blazor'</span></span>
- <span data-ttu-id="8a405-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-545">'Identity'</span></span>
- <span data-ttu-id="8a405-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-547">'Razor'</span></span>
- <span data-ttu-id="8a405-548">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-549">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-550">'Blazor'</span></span>
- <span data-ttu-id="8a405-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-551">'Identity'</span></span>
- <span data-ttu-id="8a405-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-553">'Razor'</span></span>
- <span data-ttu-id="8a405-554">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-555">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-556">'Blazor'</span></span>
- <span data-ttu-id="8a405-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-557">'Identity'</span></span>
- <span data-ttu-id="8a405-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-559">'Razor'</span></span>
- <span data-ttu-id="8a405-560">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-561">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-562">'Blazor'</span></span>
- <span data-ttu-id="8a405-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-563">'Identity'</span></span>
- <span data-ttu-id="8a405-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-565">'Razor'</span></span>
- <span data-ttu-id="8a405-566">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-567">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-568">'Blazor'</span></span>
- <span data-ttu-id="8a405-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-569">'Identity'</span></span>
- <span data-ttu-id="8a405-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-571">'Razor'</span></span>
- <span data-ttu-id="8a405-572">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-573">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-574">'Blazor'</span></span>
- <span data-ttu-id="8a405-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-575">'Identity'</span></span>
- <span data-ttu-id="8a405-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-577">'Razor'</span></span>
- <span data-ttu-id="8a405-578">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-579">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-580">'Blazor'</span></span>
- <span data-ttu-id="8a405-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-581">'Identity'</span></span>
- <span data-ttu-id="8a405-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-583">'Razor'</span></span>
- <span data-ttu-id="8a405-584">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-584">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-585">-------------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-586">'Blazor'</span></span>
- <span data-ttu-id="8a405-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-587">'Identity'</span></span>
- <span data-ttu-id="8a405-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-589">'Razor'</span></span>
- <span data-ttu-id="8a405-590">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-591">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-592">'Blazor'</span></span>
- <span data-ttu-id="8a405-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-593">'Identity'</span></span>
- <span data-ttu-id="8a405-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-595">'Razor'</span></span>
- <span data-ttu-id="8a405-596">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-597">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-598">'Blazor'</span></span>
- <span data-ttu-id="8a405-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-599">'Identity'</span></span>
- <span data-ttu-id="8a405-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-601">'Razor'</span></span>
- <span data-ttu-id="8a405-602">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-603">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-604">'Blazor'</span></span>
- <span data-ttu-id="8a405-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-605">'Identity'</span></span>
- <span data-ttu-id="8a405-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-607">'Razor'</span></span>
- <span data-ttu-id="8a405-608">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-609">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-610">'Blazor'</span></span>
- <span data-ttu-id="8a405-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-611">'Identity'</span></span>
- <span data-ttu-id="8a405-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-613">'Razor'</span></span>
- <span data-ttu-id="8a405-614">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-615">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-616">'Blazor'</span></span>
- <span data-ttu-id="8a405-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-617">'Identity'</span></span>
- <span data-ttu-id="8a405-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-619">'Razor'</span></span>
- <span data-ttu-id="8a405-620">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-621">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-622">'Blazor'</span></span>
- <span data-ttu-id="8a405-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-623">'Identity'</span></span>
- <span data-ttu-id="8a405-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-625">'Razor'</span></span>
- <span data-ttu-id="8a405-626">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-627">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-628">'Blazor'</span></span>
- <span data-ttu-id="8a405-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-629">'Identity'</span></span>
- <span data-ttu-id="8a405-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-631">'Razor'</span></span>
- <span data-ttu-id="8a405-632">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-633">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-634">'Blazor'</span></span>
- <span data-ttu-id="8a405-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-635">'Identity'</span></span>
- <span data-ttu-id="8a405-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-637">'Razor'</span></span>
- <span data-ttu-id="8a405-638">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-639">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-640">'Blazor'</span></span>
- <span data-ttu-id="8a405-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-641">'Identity'</span></span>
- <span data-ttu-id="8a405-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-643">'Razor'</span></span>
- <span data-ttu-id="8a405-644">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-645">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-646">'Blazor'</span></span>
- <span data-ttu-id="8a405-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-647">'Identity'</span></span>
- <span data-ttu-id="8a405-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-649">'Razor'</span></span>
- <span data-ttu-id="8a405-650">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-651">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-652">'Blazor'</span></span>
- <span data-ttu-id="8a405-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-653">'Identity'</span></span>
- <span data-ttu-id="8a405-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-655">'Razor'</span></span>
- <span data-ttu-id="8a405-656">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-657">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-658">'Blazor'</span></span>
- <span data-ttu-id="8a405-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-659">'Identity'</span></span>
- <span data-ttu-id="8a405-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-661">'Razor'</span></span>
- <span data-ttu-id="8a405-662">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-663">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-664">'Blazor'</span></span>
- <span data-ttu-id="8a405-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-665">'Identity'</span></span>
- <span data-ttu-id="8a405-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-667">'Razor'</span></span>
- <span data-ttu-id="8a405-668">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-669">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-670">'Blazor'</span></span>
- <span data-ttu-id="8a405-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-671">'Identity'</span></span>
- <span data-ttu-id="8a405-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-673">'Razor'</span></span>
- <span data-ttu-id="8a405-674">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-675">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-676">'Blazor'</span></span>
- <span data-ttu-id="8a405-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-677">'Identity'</span></span>
- <span data-ttu-id="8a405-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-679">'Razor'</span></span>
- <span data-ttu-id="8a405-680">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-681">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-682">'Blazor'</span></span>
- <span data-ttu-id="8a405-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-683">'Identity'</span></span>
- <span data-ttu-id="8a405-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-685">'Razor'</span></span>
- <span data-ttu-id="8a405-686">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-687">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-688">'Blazor'</span></span>
- <span data-ttu-id="8a405-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-689">'Identity'</span></span>
- <span data-ttu-id="8a405-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-691">'Razor'</span></span>
- <span data-ttu-id="8a405-692">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-693">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-694">'Blazor'</span></span>
- <span data-ttu-id="8a405-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-695">'Identity'</span></span>
- <span data-ttu-id="8a405-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-697">'Razor'</span></span>
- <span data-ttu-id="8a405-698">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-699">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-700">'Blazor'</span></span>
- <span data-ttu-id="8a405-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-701">'Identity'</span></span>
- <span data-ttu-id="8a405-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-703">'Razor'</span></span>
- <span data-ttu-id="8a405-704">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-705">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-706">'Blazor'</span></span>
- <span data-ttu-id="8a405-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-707">'Identity'</span></span>
- <span data-ttu-id="8a405-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-709">'Razor'</span></span>
- <span data-ttu-id="8a405-710">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-711">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-712">'Blazor'</span></span>
- <span data-ttu-id="8a405-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-713">'Identity'</span></span>
- <span data-ttu-id="8a405-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-715">'Razor'</span></span>
- <span data-ttu-id="8a405-716">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-717">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-718">'Blazor'</span></span>
- <span data-ttu-id="8a405-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-719">'Identity'</span></span>
- <span data-ttu-id="8a405-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-721">'Razor'</span></span>
- <span data-ttu-id="8a405-722">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-723">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-724">'Blazor'</span></span>
- <span data-ttu-id="8a405-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-725">'Identity'</span></span>
- <span data-ttu-id="8a405-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-727">'Razor'</span></span>
- <span data-ttu-id="8a405-728">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-729">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-730">'Blazor'</span></span>
- <span data-ttu-id="8a405-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-731">'Identity'</span></span>
- <span data-ttu-id="8a405-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-733">'Razor'</span></span>
- <span data-ttu-id="8a405-734">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-735">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-736">'Blazor'</span></span>
- <span data-ttu-id="8a405-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-737">'Identity'</span></span>
- <span data-ttu-id="8a405-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-739">'Razor'</span></span>
- <span data-ttu-id="8a405-740">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-741">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-742">'Blazor'</span></span>
- <span data-ttu-id="8a405-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-743">'Identity'</span></span>
- <span data-ttu-id="8a405-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-745">'Razor'</span></span>
- <span data-ttu-id="8a405-746">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-747">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-748">'Blazor'</span></span>
- <span data-ttu-id="8a405-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-749">'Identity'</span></span>
- <span data-ttu-id="8a405-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-751">'Razor'</span></span>
- <span data-ttu-id="8a405-752">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-753">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-754">'Blazor'</span></span>
- <span data-ttu-id="8a405-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-755">'Identity'</span></span>
- <span data-ttu-id="8a405-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-757">'Razor'</span></span>
- <span data-ttu-id="8a405-758">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-759">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-760">'Blazor'</span></span>
- <span data-ttu-id="8a405-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-761">'Identity'</span></span>
- <span data-ttu-id="8a405-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-763">'Razor'</span></span>
- <span data-ttu-id="8a405-764">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-765">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-766">'Blazor'</span></span>
- <span data-ttu-id="8a405-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-767">'Identity'</span></span>
- <span data-ttu-id="8a405-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-769">'Razor'</span></span>
- <span data-ttu-id="8a405-770">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-771">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-772">'Blazor'</span></span>
- <span data-ttu-id="8a405-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-773">'Identity'</span></span>
- <span data-ttu-id="8a405-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-775">'Razor'</span></span>
- <span data-ttu-id="8a405-776">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-777">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-778">'Blazor'</span></span>
- <span data-ttu-id="8a405-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-779">'Identity'</span></span>
- <span data-ttu-id="8a405-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-781">'Razor'</span></span>
- <span data-ttu-id="8a405-782">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-783">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-784">'Blazor'</span></span>
- <span data-ttu-id="8a405-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-785">'Identity'</span></span>
- <span data-ttu-id="8a405-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-787">'Razor'</span></span>
- <span data-ttu-id="8a405-788">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-789">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-790">'Blazor'</span></span>
- <span data-ttu-id="8a405-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-791">'Identity'</span></span>
- <span data-ttu-id="8a405-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-793">'Razor'</span></span>
- <span data-ttu-id="8a405-794">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-795">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-796">'Blazor'</span></span>
- <span data-ttu-id="8a405-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-797">'Identity'</span></span>
- <span data-ttu-id="8a405-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-799">'Razor'</span></span>
- <span data-ttu-id="8a405-800">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-801">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-802">'Blazor'</span></span>
- <span data-ttu-id="8a405-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-803">'Identity'</span></span>
- <span data-ttu-id="8a405-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-805">'Razor'</span></span>
- <span data-ttu-id="8a405-806">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-806">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="8a405-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="8a405-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="8a405-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8a405-809">Valor: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="8a405-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8a405-810">Valor: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="8a405-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8a405-811">Valor`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="8a405-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="8a405-812">Provedor de configuração JSON</span><span class="sxs-lookup"><span data-stu-id="8a405-812">JSON configuration provider</span></span>

<span data-ttu-id="8a405-813">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor de arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="8a405-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="8a405-814">Sobrecargas podem especificar:</span><span class="sxs-lookup"><span data-stu-id="8a405-814">Overloads can specify:</span></span>

* <span data-ttu-id="8a405-815">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="8a405-815">Whether the file is optional.</span></span>
* <span data-ttu-id="8a405-816">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="8a405-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="8a405-817">Considere o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a405-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="8a405-818">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="8a405-818">The preceding code:</span></span>

* <span data-ttu-id="8a405-819">Configura o provedor de configuração JSON para carregar o arquivo *myconfig. JSON* com as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="8a405-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="8a405-820">`optional: true`: O arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="8a405-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="8a405-821">`reloadOnChange: true`: O arquivo é recarregado quando as alterações são salvas.</span><span class="sxs-lookup"><span data-stu-id="8a405-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="8a405-822">Lê os [provedores de configuração padrão](#default) antes do arquivo *myconfig. JSON* .</span><span class="sxs-lookup"><span data-stu-id="8a405-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="8a405-823">As configurações na configuração de substituição do arquivo *myconfig. JSON* nos provedores de configuração padrão, incluindo o [provedor de configuração de variáveis de ambiente](#evcp) e o provedor de configuração de linha de [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="8a405-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8a405-824">Normalmente, você ***não*** deseja que um arquivo JSON personalizado substitua valores definidos no [provedor de configuração de variáveis de ambiente](#evcp) e no provedor de configuração de linha de [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="8a405-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8a405-825">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="8a405-826">No código anterior, as configurações em *myconfig. JSON* e *myconfig*. `Environment` . arquivos *JSON* :</span><span class="sxs-lookup"><span data-stu-id="8a405-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="8a405-827">Substitua as configurações em *appSettings. JSON* e *appSettings*. `Environment` . arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="8a405-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8a405-828">São substituídos por configurações no [provedor de configuração de variáveis de ambiente](#evcp) e no [provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="8a405-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8a405-829">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *myconfig. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8a405-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="8a405-830">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="8a405-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="8a405-831">Provedor de configuração de arquivo</span><span class="sxs-lookup"><span data-stu-id="8a405-831">File configuration provider</span></span>

<span data-ttu-id="8a405-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="8a405-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8a405-833">Os seguintes provedores de configuração derivam de `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="8a405-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="8a405-834">Provedor de configuração INI</span><span class="sxs-lookup"><span data-stu-id="8a405-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8a405-835">Provedor de configuração JSON</span><span class="sxs-lookup"><span data-stu-id="8a405-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="8a405-836">Provedor de configuração XML</span><span class="sxs-lookup"><span data-stu-id="8a405-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8a405-837">Provedor de configuração INI</span><span class="sxs-lookup"><span data-stu-id="8a405-837">INI configuration provider</span></span>

<span data-ttu-id="8a405-838">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="8a405-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8a405-839">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="8a405-840">No código anterior, as configurações no *MyIniConfig. ini* e no *MyIniConfig*. `Environment` . os arquivos *ini* são substituídos pelas configurações no:</span><span class="sxs-lookup"><span data-stu-id="8a405-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8a405-841">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="8a405-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8a405-842">[Provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="8a405-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8a405-843">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *MyIniConfig. ini* :</span><span class="sxs-lookup"><span data-stu-id="8a405-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="8a405-844">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="8a405-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="8a405-845">Provedor de configuração XML</span><span class="sxs-lookup"><span data-stu-id="8a405-845">XML configuration provider</span></span>

<span data-ttu-id="8a405-846">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="8a405-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8a405-847">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="8a405-848">No código anterior, as configurações em *MyXMLFile. xml* e *MyXMLFile*. `Environment` . os arquivos *XML* são substituídos pelas configurações no:</span><span class="sxs-lookup"><span data-stu-id="8a405-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8a405-849">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="8a405-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8a405-850">[Provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="8a405-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8a405-851">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *MyXMLFile. xml* :</span><span class="sxs-lookup"><span data-stu-id="8a405-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="8a405-852">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="8a405-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-853">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="8a405-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="8a405-854">O código a seguir lê o arquivo de configuração anterior e exibe as chaves e valores:</span><span class="sxs-lookup"><span data-stu-id="8a405-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-855">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="8a405-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8a405-856">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="8a405-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8a405-857">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8a405-857">key:attribute</span></span>
* <span data-ttu-id="8a405-858">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8a405-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8a405-859">Provedor de configuração de chave por arquivo</span><span class="sxs-lookup"><span data-stu-id="8a405-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="8a405-860">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8a405-861">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="8a405-861">The key is the file name.</span></span> <span data-ttu-id="8a405-862">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="8a405-862">The value contains the file's contents.</span></span> <span data-ttu-id="8a405-863">O provedor de configuração de chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="8a405-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8a405-864">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8a405-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8a405-865">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="8a405-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8a405-866">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="8a405-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="8a405-867">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="8a405-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8a405-868">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="8a405-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8a405-869">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="8a405-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8a405-870">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="8a405-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8a405-871">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8a405-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="8a405-872">Provedor de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="8a405-872">Memory configuration provider</span></span>

<span data-ttu-id="8a405-873">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8a405-874">O código a seguir adiciona uma coleção de memória ao sistema de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="8a405-875">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe as configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="8a405-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-876">No código anterior, `config.AddInMemoryCollection(Dict)` é adicionado após os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="8a405-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8a405-877">Para obter um exemplo de como ordenar os provedores de configuração, consulte [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="8a405-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8a405-878">Consulte [associar uma matriz](#boa) para outro exemplo usando `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="8a405-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="8a405-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="8a405-879">GetValue</span></span>

<span data-ttu-id="8a405-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrai um único valor da configuração com uma chave especificada e converte-o no tipo especificado:</span><span class="sxs-lookup"><span data-stu-id="8a405-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-881">No código anterior, se `NumberKey` não for encontrado na configuração, o valor padrão de `99` será usado.</span><span class="sxs-lookup"><span data-stu-id="8a405-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8a405-882">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="8a405-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8a405-883">Para os exemplos a seguir, considere o seguinte arquivo *MySubsection. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8a405-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="8a405-884">O código a seguir adiciona *MySubsection. JSON* aos provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="8a405-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="8a405-885">GetSection</span></span>

<span data-ttu-id="8a405-886">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retorna uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="8a405-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8a405-887">O código a seguir retorna valores para `section1` :</span><span class="sxs-lookup"><span data-stu-id="8a405-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-888">O código a seguir retorna valores para `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="8a405-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-889">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="8a405-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8a405-890">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="8a405-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8a405-891">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="8a405-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8a405-892"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="8a405-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="8a405-893">GetChildren e existe</span><span class="sxs-lookup"><span data-stu-id="8a405-893">GetChildren and Exists</span></span>

<span data-ttu-id="8a405-894">O código a seguir chama [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) e retorna valores para `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="8a405-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-895">O código anterior chama [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para verificar se a seção existe:</span><span class="sxs-lookup"><span data-stu-id="8a405-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="8a405-896">Associar uma matriz</span><span class="sxs-lookup"><span data-stu-id="8a405-896">Bind an array</span></span>

<span data-ttu-id="8a405-897">O [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) dá suporte a matrizes de associação a objetos usando índices de matriz em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8a405-898">Qualquer formato de matriz que expõe um segmento de chave numérica é capaz de associar a matriz a uma matriz de classe [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="8a405-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="8a405-899">Considere o *myArray. JSON* do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="8a405-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="8a405-900">O código a seguir adiciona *myArray. JSON* aos provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="8a405-901">O código a seguir lê a configuração e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="8a405-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-902">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="8a405-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="8a405-903">Na saída anterior, o índice 3 tem valor `value40` , correspondente a `"4": "value40",` em *myArray. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8a405-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="8a405-904">Os índices de matriz associados são contínuos e não associados ao índice de chave de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="8a405-905">O associador de configuração não é capaz de ligar valores nulos ou criar entradas nulas em objetos associados</span><span class="sxs-lookup"><span data-stu-id="8a405-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="8a405-906">O código a seguir carrega a `array:entries` configuração com o <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> método de extensão:</span><span class="sxs-lookup"><span data-stu-id="8a405-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="8a405-907">O código a seguir lê a configuração no `arrayDict` `Dictionary` e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="8a405-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-908">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="8a405-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="8a405-909">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="8a405-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8a405-910">Quando os dados de configuração que contêm uma matriz são associados, os índices de matriz nas chaves de configuração são usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="8a405-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8a405-911">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="8a405-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8a405-912">O item de configuração ausente para &num; o índice 3 pode ser fornecido antes da associação à `ArrayExample` instância por qualquer provedor de configuração que leia o &num; par chave/valor do índice 3.</span><span class="sxs-lookup"><span data-stu-id="8a405-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="8a405-913">Considere o seguinte arquivo *Value3. JSON* do download de exemplo:</span><span class="sxs-lookup"><span data-stu-id="8a405-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="8a405-914">O código a seguir inclui a configuração para *Value3. JSON* e o `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="8a405-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="8a405-915">O código a seguir lê a configuração anterior e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="8a405-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-916">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="8a405-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="8a405-917">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="8a405-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="8a405-918">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="8a405-918">Custom configuration provider</span></span>

<span data-ttu-id="8a405-919">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="8a405-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8a405-920">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="8a405-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8a405-921">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="8a405-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8a405-922">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8a405-923">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="8a405-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8a405-924">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="8a405-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8a405-925">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8a405-926">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="8a405-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8a405-927">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a405-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8a405-928">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="8a405-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8a405-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a405-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8a405-930">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="8a405-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8a405-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a405-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8a405-932">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="8a405-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8a405-933">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="8a405-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="8a405-934">Como [as chaves de configuração](#keys)não diferenciam maiúsculas de minúsculas, o dicionário usado para inicializar o banco de dados é criado com o comparador que não diferencia maiúsculas de minúsculas ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="8a405-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="8a405-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a405-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8a405-936">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8a405-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8a405-937">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a405-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8a405-938">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a405-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="8a405-939">Configuração de acesso na inicialização</span><span class="sxs-lookup"><span data-stu-id="8a405-939">Access configuration in Startup</span></span>

<span data-ttu-id="8a405-940">O código a seguir exibe dados de configuração em `Startup` métodos:</span><span class="sxs-lookup"><span data-stu-id="8a405-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="8a405-941">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="8a405-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="8a405-942">Configuração de acesso em Razor páginas</span><span class="sxs-lookup"><span data-stu-id="8a405-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="8a405-943">O código a seguir exibe os dados de configuração em uma Razor página:</span><span class="sxs-lookup"><span data-stu-id="8a405-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="8a405-944">No código a seguir, `MyOptions` é adicionado ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associado à configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="8a405-945">A marcação a seguir usa a [`@inject`](xref:mvc/views/razor#inject) Razor diretiva para resolver e exibir os valores de opções:</span><span class="sxs-lookup"><span data-stu-id="8a405-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="8a405-946">Acessar a configuração em um arquivo de exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="8a405-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="8a405-947">O código a seguir exibe os dados de configuração em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="8a405-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="8a405-948">Configurar opções com um delegado</span><span class="sxs-lookup"><span data-stu-id="8a405-948">Configure options with a delegate</span></span>

<span data-ttu-id="8a405-949">As opções configuradas em um delegado substituem valores definidos nos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="8a405-950">A configuração de opções com um delegado é demonstrada como o exemplo 2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="8a405-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="8a405-951">No código a seguir, um <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serviço é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="8a405-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="8a405-952">Ele usa um delegado para configurar valores para `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="8a405-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="8a405-953">O código a seguir exibe os valores de opções:</span><span class="sxs-lookup"><span data-stu-id="8a405-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="8a405-954">No exemplo anterior, os valores de `Option1` e `Option2` são especificados em *appSettings. JSON* e, em seguida, substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="8a405-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8a405-955">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="8a405-955">Host versus app configuration</span></span>

<span data-ttu-id="8a405-956">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="8a405-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8a405-957">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8a405-958">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="8a405-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8a405-959">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8a405-960">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="8a405-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="8a405-961">Configuração de host padrão</span><span class="sxs-lookup"><span data-stu-id="8a405-961">Default host configuration</span></span>

<span data-ttu-id="8a405-962">Para obter detalhes sobre a configuração padrão ao usar o [host da Web](xref:fundamentals/host/web-host), confira a [versão do ASP.NET Core 2.2 deste tópico](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="8a405-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="8a405-963">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="8a405-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8a405-964">Variáveis de ambiente prefixadas com `DOTNET_` (por exemplo, `DOTNET_ENVIRONMENT` ) usando o [provedor de configuração de variáveis de ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8a405-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8a405-965">O prefixo (`DOTNET_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8a405-966">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8a405-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8a405-967">A configuração padrão do host da Web foi estabelecida (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="8a405-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="8a405-968">O Kestrel é usado como o servidor Web e configurado usando provedores de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="8a405-969">Adicione o middleware de filtragem de host.</span><span class="sxs-lookup"><span data-stu-id="8a405-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="8a405-970">Adicione o middleware de cabeçalhos encaminhados se a variável de ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for definida para `true`.</span><span class="sxs-lookup"><span data-stu-id="8a405-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="8a405-971">Habilite a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="8a405-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8a405-972">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="8a405-972">Other configuration</span></span>

<span data-ttu-id="8a405-973">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="8a405-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8a405-974">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="8a405-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8a405-975">*Iniciar. JSON* / *launchSettings. JSON* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="8a405-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8a405-976">Em <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="8a405-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8a405-977">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a405-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8a405-978">o *Web. config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="8a405-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8a405-979">Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="8a405-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8a405-980">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="8a405-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="8a405-981">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8a405-982">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8a405-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a405-983">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8a405-983">Additional resources</span></span>

* [<span data-ttu-id="8a405-984">Código-fonte de configuração</span><span class="sxs-lookup"><span data-stu-id="8a405-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8a405-985">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="8a405-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="8a405-986">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="8a405-987">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="8a405-987">Azure Key Vault</span></span>
* <span data-ttu-id="8a405-988">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="8a405-988">Azure App Configuration</span></span>
* <span data-ttu-id="8a405-989">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="8a405-989">Command-line arguments</span></span>
* <span data-ttu-id="8a405-990">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="8a405-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="8a405-991">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="8a405-991">Directory files</span></span>
* <span data-ttu-id="8a405-992">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="8a405-992">Environment variables</span></span>
* <span data-ttu-id="8a405-993">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="8a405-993">In-memory .NET objects</span></span>
* <span data-ttu-id="8a405-994">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="8a405-994">Settings files</span></span>

<span data-ttu-id="8a405-995">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8a405-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8a405-996">Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="8a405-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="8a405-997">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="8a405-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="8a405-998">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="8a405-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="8a405-999">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="8a405-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8a405-1000">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a405-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8a405-1001">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="8a405-1001">Host versus app configuration</span></span>

<span data-ttu-id="8a405-1002">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="8a405-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8a405-1003">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8a405-1004">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="8a405-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8a405-1005">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8a405-1006">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="8a405-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8a405-1007">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="8a405-1007">Other configuration</span></span>

<span data-ttu-id="8a405-1008">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="8a405-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8a405-1009">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="8a405-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8a405-1010">*Iniciar. JSON* / *launchSettings. JSON* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="8a405-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8a405-1011">Em <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="8a405-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8a405-1012">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a405-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8a405-1013">o *Web. config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="8a405-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8a405-1014">Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="8a405-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="8a405-1015">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="8a405-1015">Default configuration</span></span>

<span data-ttu-id="8a405-1016">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="8a405-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="8a405-1017">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="8a405-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="8a405-1018">O seguinte se aplica a aplicativos que usam o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="8a405-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="8a405-1019">Para obter detalhes sobre a configuração padrão ao usar o [host genérico](xref:fundamentals/host/generic-host), confira [versão mais recente deste tópico](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8a405-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="8a405-1020">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="8a405-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8a405-1021">Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8a405-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8a405-1022">O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8a405-1023">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8a405-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8a405-1024">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="8a405-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="8a405-1025">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8a405-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8a405-1026">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8a405-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8a405-1027">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="8a405-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8a405-1028">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8a405-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="8a405-1029">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8a405-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="8a405-1030">Segurança</span><span class="sxs-lookup"><span data-stu-id="8a405-1030">Security</span></span>

<span data-ttu-id="8a405-1031">Adote as seguintes práticas para proteger dados de configuração confidenciais:</span><span class="sxs-lookup"><span data-stu-id="8a405-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="8a405-1032">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="8a405-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="8a405-1033">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a405-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8a405-1034">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="8a405-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8a405-1035">Para obter mais informações, consulte estes tópicos:</span><span class="sxs-lookup"><span data-stu-id="8a405-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8a405-1036"><xref:security/app-secrets>&ndash;Inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="8a405-1036"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8a405-1037">O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="8a405-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="8a405-1038">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="8a405-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="8a405-1039">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a405-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8a405-1040">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8a405-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8a405-1041">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="8a405-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="8a405-1042">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8a405-1043">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="8a405-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="8a405-1044">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="8a405-1045">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="8a405-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="8a405-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8a405-1046">section0:key0</span></span>
* <span data-ttu-id="8a405-1047">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8a405-1047">section0:key1</span></span>
* <span data-ttu-id="8a405-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8a405-1048">section1:key0</span></span>
* <span data-ttu-id="8a405-1049">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8a405-1049">section1:key1</span></span>

<span data-ttu-id="8a405-1050">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8a405-1051">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="8a405-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="8a405-1052">Convenções</span><span class="sxs-lookup"><span data-stu-id="8a405-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="8a405-1053">Origens e provedores</span><span class="sxs-lookup"><span data-stu-id="8a405-1053">Sources and providers</span></span>

<span data-ttu-id="8a405-1054">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="8a405-1055">Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada.</span><span class="sxs-lookup"><span data-stu-id="8a405-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="8a405-1056">Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.</span><span class="sxs-lookup"><span data-stu-id="8a405-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="8a405-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8a405-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration>pode ser injetado em uma Razor página <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obter a configuração da classe.</span><span class="sxs-lookup"><span data-stu-id="8a405-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="8a405-1059">Nos exemplos a seguir, o `_config` campo é usado para acessar os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="8a405-1060">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="8a405-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="8a405-1061">simétricas</span><span class="sxs-lookup"><span data-stu-id="8a405-1061">Keys</span></span>

<span data-ttu-id="8a405-1062">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a405-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="8a405-1063">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="8a405-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="8a405-1064">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="8a405-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8a405-1065">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="8a405-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="8a405-1066">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="8a405-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="8a405-1067">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="8a405-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8a405-1068">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="8a405-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8a405-1069">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="8a405-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="8a405-1070">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="8a405-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8a405-1071">Escreva o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8a405-1072">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8a405-1073">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="8a405-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="8a405-1074">Valores</span><span class="sxs-lookup"><span data-stu-id="8a405-1074">Values</span></span>

<span data-ttu-id="8a405-1075">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a405-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="8a405-1076">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="8a405-1076">Values are strings.</span></span>
* <span data-ttu-id="8a405-1077">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="8a405-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="8a405-1078">Provedores</span><span class="sxs-lookup"><span data-stu-id="8a405-1078">Providers</span></span>

<span data-ttu-id="8a405-1079">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a405-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8a405-1080">Provedor</span><span class="sxs-lookup"><span data-stu-id="8a405-1080">Provider</span></span> | <span data-ttu-id="8a405-1081">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="8a405-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="8a405-1082">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1083">'Blazor'</span></span>
- <span data-ttu-id="8a405-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1084">'Identity'</span></span>
- <span data-ttu-id="8a405-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1086">'Razor'</span></span>
- <span data-ttu-id="8a405-1087">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1088">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1089">'Blazor'</span></span>
- <span data-ttu-id="8a405-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1090">'Identity'</span></span>
- <span data-ttu-id="8a405-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1092">'Razor'</span></span>
- <span data-ttu-id="8a405-1093">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-1094">---- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1095">'Blazor'</span></span>
- <span data-ttu-id="8a405-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1096">'Identity'</span></span>
- <span data-ttu-id="8a405-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1098">'Razor'</span></span>
- <span data-ttu-id="8a405-1099">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1100">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1101">'Blazor'</span></span>
- <span data-ttu-id="8a405-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1102">'Identity'</span></span>
- <span data-ttu-id="8a405-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1104">'Razor'</span></span>
- <span data-ttu-id="8a405-1105">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1106">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1107">'Blazor'</span></span>
- <span data-ttu-id="8a405-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1108">'Identity'</span></span>
- <span data-ttu-id="8a405-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1110">'Razor'</span></span>
- <span data-ttu-id="8a405-1111">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1112">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1113">'Blazor'</span></span>
- <span data-ttu-id="8a405-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1114">'Identity'</span></span>
- <span data-ttu-id="8a405-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1116">'Razor'</span></span>
- <span data-ttu-id="8a405-1117">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1118">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1119">'Blazor'</span></span>
- <span data-ttu-id="8a405-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1120">'Identity'</span></span>
- <span data-ttu-id="8a405-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1122">'Razor'</span></span>
- <span data-ttu-id="8a405-1123">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1124">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1125">'Blazor'</span></span>
- <span data-ttu-id="8a405-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1126">'Identity'</span></span>
- <span data-ttu-id="8a405-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1128">'Razor'</span></span>
- <span data-ttu-id="8a405-1129">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1130">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1131">'Blazor'</span></span>
- <span data-ttu-id="8a405-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1132">'Identity'</span></span>
- <span data-ttu-id="8a405-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1134">'Razor'</span></span>
- <span data-ttu-id="8a405-1135">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1136">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1137">'Blazor'</span></span>
- <span data-ttu-id="8a405-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1138">'Identity'</span></span>
- <span data-ttu-id="8a405-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1140">'Razor'</span></span>
- <span data-ttu-id="8a405-1141">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1142">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1143">'Blazor'</span></span>
- <span data-ttu-id="8a405-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1144">'Identity'</span></span>
- <span data-ttu-id="8a405-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1146">'Razor'</span></span>
- <span data-ttu-id="8a405-1147">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1148">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1149">'Blazor'</span></span>
- <span data-ttu-id="8a405-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1150">'Identity'</span></span>
- <span data-ttu-id="8a405-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1152">'Razor'</span></span>
- <span data-ttu-id="8a405-1153">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1154">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1155">'Blazor'</span></span>
- <span data-ttu-id="8a405-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1156">'Identity'</span></span>
- <span data-ttu-id="8a405-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1158">'Razor'</span></span>
- <span data-ttu-id="8a405-1159">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1160">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1161">'Blazor'</span></span>
- <span data-ttu-id="8a405-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1162">'Identity'</span></span>
- <span data-ttu-id="8a405-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1164">'Razor'</span></span>
- <span data-ttu-id="8a405-1165">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1166">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1167">'Blazor'</span></span>
- <span data-ttu-id="8a405-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1168">'Identity'</span></span>
- <span data-ttu-id="8a405-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1170">'Razor'</span></span>
- <span data-ttu-id="8a405-1171">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1172">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1173">'Blazor'</span></span>
- <span data-ttu-id="8a405-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1174">'Identity'</span></span>
- <span data-ttu-id="8a405-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1176">'Razor'</span></span>
- <span data-ttu-id="8a405-1177">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1178">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1179">'Blazor'</span></span>
- <span data-ttu-id="8a405-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1180">'Identity'</span></span>
- <span data-ttu-id="8a405-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1182">'Razor'</span></span>
- <span data-ttu-id="8a405-1183">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-1184">------------------ | | [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de*segurança* ) | Azure Key Vault | | [Provedor de configuração do Azure app](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure) | Configuração de Azure App | | [Provedor de configuração de linha de comando](#command-line-configuration-provider) | Parâmetros de linha de comando | | [Provedor de configuração personalizada](#custom-configuration-provider) | Fonte personalizada | | [Provedor de configuração de variáveis de ambiente](#environment-variables-configuration-provider) | Variáveis de ambiente | | [Provedor de configuração de arquivo](#file-configuration-provider) | Arquivos (INI, JSON, XML) | | [Provedor de configuração de chave por arquivo](#key-per-file-configuration-provider) | Arquivos de diretório | | [Provedor de configuração de memória](#memory-configuration-provider) | Coleções na memória | | [Segredos do usuário (Gerenciador de segredo)](xref:security/app-secrets) (tópicos de*segurança* ) | Arquivo no diretório de perfil do usuário |</span><span class="sxs-lookup"><span data-stu-id="8a405-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="8a405-1185">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="8a405-1186">Os provedores de configuração descritos neste tópico são descritos em ordem alfabética, não na ordem em que o código os organiza.</span><span class="sxs-lookup"><span data-stu-id="8a405-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="8a405-1187">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="8a405-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8a405-1188">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="8a405-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8a405-1189">Arquivos (*appSettings. JSON*, *appSettings. { Ambiente}. JSON*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="8a405-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="8a405-1190">Cofre da Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="8a405-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="8a405-1191">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="8a405-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="8a405-1192">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="8a405-1192">Environment variables</span></span>
1. <span data-ttu-id="8a405-1193">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="8a405-1193">Command-line arguments</span></span>

<span data-ttu-id="8a405-1194">Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="8a405-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8a405-1195">A sequência anterior de provedores é usada quando um novo Construtor de hosts é inicializado com o `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="8a405-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8a405-1196">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8a405-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="8a405-1197">Configurar o construtor de host com UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="8a405-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="8a405-1198">Para configurar o construtor de host, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> no construtor de host com a configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="8a405-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="8a405-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="8a405-1200">Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="8a405-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="8a405-1201">Substituir a configuração anterior por argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="8a405-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="8a405-1202">Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:</span><span class="sxs-lookup"><span data-stu-id="8a405-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="8a405-1203">Remover provedores adicionados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="8a405-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="8a405-1204">Para remover os provedores adicionados pelo `CreateDefaultBuilder` , chame [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) no [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) primeiro:</span><span class="sxs-lookup"><span data-stu-id="8a405-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="8a405-1205">Consumir a configuração durante a inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="8a405-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="8a405-1206">a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8a405-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8a405-1207">Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).</span><span class="sxs-lookup"><span data-stu-id="8a405-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="8a405-1208">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="8a405-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="8a405-1209">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em runtime.</span><span class="sxs-lookup"><span data-stu-id="8a405-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="8a405-1210">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8a405-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8a405-1211">`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado.</span><span class="sxs-lookup"><span data-stu-id="8a405-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="8a405-1212">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8a405-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8a405-1213">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="8a405-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8a405-1214">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="8a405-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8a405-1215">[Segredos do usuário (Gerenciador de segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a405-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8a405-1216">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="8a405-1216">Environment variables.</span></span>

<span data-ttu-id="8a405-1217">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="8a405-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="8a405-1218">Os argumentos de linha de comando passados em runtime substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="8a405-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="8a405-1219">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="8a405-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="8a405-1220">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="8a405-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="8a405-1221">Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8a405-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8a405-1222">Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.</span><span class="sxs-lookup"><span data-stu-id="8a405-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="8a405-1223">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="8a405-1223">**Example**</span></span>

<span data-ttu-id="8a405-1224">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="8a405-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="8a405-1225">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="8a405-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="8a405-1226">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="8a405-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="8a405-1227">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8a405-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8a405-1228">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="8a405-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="8a405-1229">Argumentos</span><span class="sxs-lookup"><span data-stu-id="8a405-1229">Arguments</span></span>

<span data-ttu-id="8a405-1230">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="8a405-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="8a405-1231">O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="8a405-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="8a405-1232">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="8a405-1232">Key prefix</span></span>               | <span data-ttu-id="8a405-1233">Exemplo</span><span class="sxs-lookup"><span data-stu-id="8a405-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="8a405-1234">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1235">'Blazor'</span></span>
- <span data-ttu-id="8a405-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1236">'Identity'</span></span>
- <span data-ttu-id="8a405-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1238">'Razor'</span></span>
- <span data-ttu-id="8a405-1239">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1240">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1241">'Blazor'</span></span>
- <span data-ttu-id="8a405-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1242">'Identity'</span></span>
- <span data-ttu-id="8a405-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1244">'Razor'</span></span>
- <span data-ttu-id="8a405-1245">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1246">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1247">'Blazor'</span></span>
- <span data-ttu-id="8a405-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1248">'Identity'</span></span>
- <span data-ttu-id="8a405-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1250">'Razor'</span></span>
- <span data-ttu-id="8a405-1251">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1252">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1253">'Blazor'</span></span>
- <span data-ttu-id="8a405-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1254">'Identity'</span></span>
- <span data-ttu-id="8a405-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1256">'Razor'</span></span>
- <span data-ttu-id="8a405-1257">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1258">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1259">'Blazor'</span></span>
- <span data-ttu-id="8a405-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1260">'Identity'</span></span>
- <span data-ttu-id="8a405-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1262">'Razor'</span></span>
- <span data-ttu-id="8a405-1263">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1264">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1265">'Blazor'</span></span>
- <span data-ttu-id="8a405-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1266">'Identity'</span></span>
- <span data-ttu-id="8a405-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1268">'Razor'</span></span>
- <span data-ttu-id="8a405-1269">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1270">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1271">'Blazor'</span></span>
- <span data-ttu-id="8a405-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1272">'Identity'</span></span>
- <span data-ttu-id="8a405-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1274">'Razor'</span></span>
- <span data-ttu-id="8a405-1275">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1276">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1277">'Blazor'</span></span>
- <span data-ttu-id="8a405-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1278">'Identity'</span></span>
- <span data-ttu-id="8a405-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1280">'Razor'</span></span>
- <span data-ttu-id="8a405-1281">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1282">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1283">'Blazor'</span></span>
- <span data-ttu-id="8a405-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1284">'Identity'</span></span>
- <span data-ttu-id="8a405-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1286">'Razor'</span></span>
- <span data-ttu-id="8a405-1287">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1288">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1289">'Blazor'</span></span>
- <span data-ttu-id="8a405-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1290">'Identity'</span></span>
- <span data-ttu-id="8a405-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1292">'Razor'</span></span>
- <span data-ttu-id="8a405-1293">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-1294">------------ | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1295">'Blazor'</span></span>
- <span data-ttu-id="8a405-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1296">'Identity'</span></span>
- <span data-ttu-id="8a405-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1298">'Razor'</span></span>
- <span data-ttu-id="8a405-1299">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1300">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1301">'Blazor'</span></span>
- <span data-ttu-id="8a405-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1302">'Identity'</span></span>
- <span data-ttu-id="8a405-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1304">'Razor'</span></span>
- <span data-ttu-id="8a405-1305">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1306">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1307">'Blazor'</span></span>
- <span data-ttu-id="8a405-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1308">'Identity'</span></span>
- <span data-ttu-id="8a405-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1310">'Razor'</span></span>
- <span data-ttu-id="8a405-1311">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1312">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1313">'Blazor'</span></span>
- <span data-ttu-id="8a405-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1314">'Identity'</span></span>
- <span data-ttu-id="8a405-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1316">'Razor'</span></span>
- <span data-ttu-id="8a405-1317">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1318">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1319">'Blazor'</span></span>
- <span data-ttu-id="8a405-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1320">'Identity'</span></span>
- <span data-ttu-id="8a405-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1322">'Razor'</span></span>
- <span data-ttu-id="8a405-1323">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1324">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1325">'Blazor'</span></span>
- <span data-ttu-id="8a405-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1326">'Identity'</span></span>
- <span data-ttu-id="8a405-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1328">'Razor'</span></span>
- <span data-ttu-id="8a405-1329">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1330">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1331">'Blazor'</span></span>
- <span data-ttu-id="8a405-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1332">'Identity'</span></span>
- <span data-ttu-id="8a405-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1334">'Razor'</span></span>
- <span data-ttu-id="8a405-1335">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1336">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1337">'Blazor'</span></span>
- <span data-ttu-id="8a405-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1338">'Identity'</span></span>
- <span data-ttu-id="8a405-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1340">'Razor'</span></span>
- <span data-ttu-id="8a405-1341">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1342">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1343">'Blazor'</span></span>
- <span data-ttu-id="8a405-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1344">'Identity'</span></span>
- <span data-ttu-id="8a405-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1346">'Razor'</span></span>
- <span data-ttu-id="8a405-1347">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1348">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1349">'Blazor'</span></span>
- <span data-ttu-id="8a405-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1350">'Identity'</span></span>
- <span data-ttu-id="8a405-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1352">'Razor'</span></span>
- <span data-ttu-id="8a405-1353">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1354">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1355">'Blazor'</span></span>
- <span data-ttu-id="8a405-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1356">'Identity'</span></span>
- <span data-ttu-id="8a405-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1358">'Razor'</span></span>
- <span data-ttu-id="8a405-1359">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1360">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1361">'Blazor'</span></span>
- <span data-ttu-id="8a405-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1362">'Identity'</span></span>
- <span data-ttu-id="8a405-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1364">'Razor'</span></span>
- <span data-ttu-id="8a405-1365">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1366">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1367">'Blazor'</span></span>
- <span data-ttu-id="8a405-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1368">'Identity'</span></span>
- <span data-ttu-id="8a405-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1370">'Razor'</span></span>
- <span data-ttu-id="8a405-1371">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1372">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1373">'Blazor'</span></span>
- <span data-ttu-id="8a405-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1374">'Identity'</span></span>
- <span data-ttu-id="8a405-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1376">'Razor'</span></span>
- <span data-ttu-id="8a405-1377">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1378">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1379">'Blazor'</span></span>
- <span data-ttu-id="8a405-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1380">'Identity'</span></span>
- <span data-ttu-id="8a405-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1382">'Razor'</span></span>
- <span data-ttu-id="8a405-1383">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1384">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1385">'Blazor'</span></span>
- <span data-ttu-id="8a405-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1386">'Identity'</span></span>
- <span data-ttu-id="8a405-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1388">'Razor'</span></span>
- <span data-ttu-id="8a405-1389">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1390">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1391">'Blazor'</span></span>
- <span data-ttu-id="8a405-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1392">'Identity'</span></span>
- <span data-ttu-id="8a405-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1394">'Razor'</span></span>
- <span data-ttu-id="8a405-1395">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1396">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1397">'Blazor'</span></span>
- <span data-ttu-id="8a405-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1398">'Identity'</span></span>
- <span data-ttu-id="8a405-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1400">'Razor'</span></span>
- <span data-ttu-id="8a405-1401">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1402">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1403">'Blazor'</span></span>
- <span data-ttu-id="8a405-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1404">'Identity'</span></span>
- <span data-ttu-id="8a405-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1406">'Razor'</span></span>
- <span data-ttu-id="8a405-1407">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1408">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1409">'Blazor'</span></span>
- <span data-ttu-id="8a405-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1410">'Identity'</span></span>
- <span data-ttu-id="8a405-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1412">'Razor'</span></span>
- <span data-ttu-id="8a405-1413">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1414">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1415">'Blazor'</span></span>
- <span data-ttu-id="8a405-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1416">'Identity'</span></span>
- <span data-ttu-id="8a405-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1418">'Razor'</span></span>
- <span data-ttu-id="8a405-1419">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1420">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1421">'Blazor'</span></span>
- <span data-ttu-id="8a405-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1422">'Identity'</span></span>
- <span data-ttu-id="8a405-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1424">'Razor'</span></span>
- <span data-ttu-id="8a405-1425">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1426">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1427">'Blazor'</span></span>
- <span data-ttu-id="8a405-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1428">'Identity'</span></span>
- <span data-ttu-id="8a405-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1430">'Razor'</span></span>
- <span data-ttu-id="8a405-1431">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1432">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1433">'Blazor'</span></span>
- <span data-ttu-id="8a405-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1434">'Identity'</span></span>
- <span data-ttu-id="8a405-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1436">'Razor'</span></span>
- <span data-ttu-id="8a405-1437">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1438">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1439">'Blazor'</span></span>
- <span data-ttu-id="8a405-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1440">'Identity'</span></span>
- <span data-ttu-id="8a405-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1442">'Razor'</span></span>
- <span data-ttu-id="8a405-1443">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-1444">--------------------------- | | Nenhum prefixo | `CommandLineKey1=value1`                               |
| Dois traços ( `--` ) | `--CommandLineKey2=value2` , `--CommandLineKey2 value2` |
 | Barra ( `/` ) | `/CommandLineKey3=value3` ,`/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="8a405-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="8a405-1445">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="8a405-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="8a405-1446">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="8a405-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="8a405-1447">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="8a405-1447">Switch mappings</span></span>

<span data-ttu-id="8a405-1448">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="8a405-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="8a405-1449">Ao criar manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , forneça um dicionário de substituições de switch para o <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> método.</span><span class="sxs-lookup"><span data-stu-id="8a405-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8a405-1450">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="8a405-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8a405-1451">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8a405-1452">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="8a405-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8a405-1453">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="8a405-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8a405-1454">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="8a405-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="8a405-1455">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="8a405-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8a405-1456">Crie um dicionário de mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="8a405-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="8a405-1457">No exemplo a seguir, dois mapeamentos de opção são criados:</span><span class="sxs-lookup"><span data-stu-id="8a405-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="8a405-1458">Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:</span><span class="sxs-lookup"><span data-stu-id="8a405-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="8a405-1459">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="8a405-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8a405-1460">A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8a405-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8a405-1461">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="8a405-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="8a405-1462">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="8a405-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="8a405-1463">Chave</span><span class="sxs-lookup"><span data-stu-id="8a405-1463">Key</span></span>       | <span data-ttu-id="8a405-1464">Valor</span><span class="sxs-lookup"><span data-stu-id="8a405-1464">Value</span></span>             |
| ---
<span data-ttu-id="8a405-1465">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1466">'Blazor'</span></span>
- <span data-ttu-id="8a405-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1467">'Identity'</span></span>
- <span data-ttu-id="8a405-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1469">'Razor'</span></span>
- <span data-ttu-id="8a405-1470">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1471">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1472">'Blazor'</span></span>
- <span data-ttu-id="8a405-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1473">'Identity'</span></span>
- <span data-ttu-id="8a405-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1475">'Razor'</span></span>
- <span data-ttu-id="8a405-1476">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-1477">----- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1478">'Blazor'</span></span>
- <span data-ttu-id="8a405-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1479">'Identity'</span></span>
- <span data-ttu-id="8a405-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1481">'Razor'</span></span>
- <span data-ttu-id="8a405-1482">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1483">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1484">'Blazor'</span></span>
- <span data-ttu-id="8a405-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1485">'Identity'</span></span>
- <span data-ttu-id="8a405-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1487">'Razor'</span></span>
- <span data-ttu-id="8a405-1488">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1489">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1490">'Blazor'</span></span>
- <span data-ttu-id="8a405-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1491">'Identity'</span></span>
- <span data-ttu-id="8a405-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1493">'Razor'</span></span>
- <span data-ttu-id="8a405-1494">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1495">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1496">'Blazor'</span></span>
- <span data-ttu-id="8a405-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1497">'Identity'</span></span>
- <span data-ttu-id="8a405-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1499">'Razor'</span></span>
- <span data-ttu-id="8a405-1500">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1501">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1502">'Blazor'</span></span>
- <span data-ttu-id="8a405-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1503">'Identity'</span></span>
- <span data-ttu-id="8a405-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1505">'Razor'</span></span>
- <span data-ttu-id="8a405-1506">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1507">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1508">'Blazor'</span></span>
- <span data-ttu-id="8a405-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1509">'Identity'</span></span>
- <span data-ttu-id="8a405-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1511">'Razor'</span></span>
- <span data-ttu-id="8a405-1512">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="8a405-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="8a405-1514">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="8a405-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="8a405-1515">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="8a405-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="8a405-1516">Chave</span><span class="sxs-lookup"><span data-stu-id="8a405-1516">Key</span></span>               | <span data-ttu-id="8a405-1517">Valor</span><span class="sxs-lookup"><span data-stu-id="8a405-1517">Value</span></span>    |
| ---
<span data-ttu-id="8a405-1518">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1519">'Blazor'</span></span>
- <span data-ttu-id="8a405-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1520">'Identity'</span></span>
- <span data-ttu-id="8a405-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1522">'Razor'</span></span>
- <span data-ttu-id="8a405-1523">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1524">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1525">'Blazor'</span></span>
- <span data-ttu-id="8a405-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1526">'Identity'</span></span>
- <span data-ttu-id="8a405-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1528">'Razor'</span></span>
- <span data-ttu-id="8a405-1529">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1530">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1531">'Blazor'</span></span>
- <span data-ttu-id="8a405-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1532">'Identity'</span></span>
- <span data-ttu-id="8a405-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1534">'Razor'</span></span>
- <span data-ttu-id="8a405-1535">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1536">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1537">'Blazor'</span></span>
- <span data-ttu-id="8a405-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1538">'Identity'</span></span>
- <span data-ttu-id="8a405-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1540">'Razor'</span></span>
- <span data-ttu-id="8a405-1541">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1542">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1543">'Blazor'</span></span>
- <span data-ttu-id="8a405-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1544">'Identity'</span></span>
- <span data-ttu-id="8a405-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1546">'Razor'</span></span>
- <span data-ttu-id="8a405-1547">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1548">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1549">'Blazor'</span></span>
- <span data-ttu-id="8a405-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1550">'Identity'</span></span>
- <span data-ttu-id="8a405-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1552">'Razor'</span></span>
- <span data-ttu-id="8a405-1553">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-1554">--------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1555">'Blazor'</span></span>
- <span data-ttu-id="8a405-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1556">'Identity'</span></span>
- <span data-ttu-id="8a405-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1558">'Razor'</span></span>
- <span data-ttu-id="8a405-1559">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1560">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1561">'Blazor'</span></span>
- <span data-ttu-id="8a405-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1562">'Identity'</span></span>
- <span data-ttu-id="8a405-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1564">'Razor'</span></span>
- <span data-ttu-id="8a405-1565">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="8a405-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="8a405-1567">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="8a405-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="8a405-1568">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em runtime.</span><span class="sxs-lookup"><span data-stu-id="8a405-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="8a405-1569">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8a405-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8a405-1570">[Azure app serviço](https://azure.microsoft.com/services/app-service/) permite definir variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o provedor de configuração de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="8a405-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="8a405-1571">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="8a405-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8a405-1572">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `ASPNETCORE_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host da Web](xref:fundamentals/host/web-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="8a405-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="8a405-1573">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8a405-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8a405-1574">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="8a405-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8a405-1575">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="8a405-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="8a405-1576">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="8a405-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8a405-1577">[Segredos do usuário (Gerenciador de segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a405-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8a405-1578">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="8a405-1578">Command-line arguments.</span></span>

<span data-ttu-id="8a405-1579">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="8a405-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="8a405-1580">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em runtime para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="8a405-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="8a405-1581">Para fornecer a configuração de aplicativo de variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo no `ConfigureAppConfiguration` e chame `AddEnvironmentVariables` com o prefixo:</span><span class="sxs-lookup"><span data-stu-id="8a405-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="8a405-1582">Chame `AddEnvironmentVariables` Last para permitir que as variáveis de ambiente com o prefixo fornecido substituam valores de outros provedores.</span><span class="sxs-lookup"><span data-stu-id="8a405-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="8a405-1583">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="8a405-1583">**Example**</span></span>

<span data-ttu-id="8a405-1584">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="8a405-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="8a405-1585">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="8a405-1585">Run the sample app.</span></span> <span data-ttu-id="8a405-1586">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8a405-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8a405-1587">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="8a405-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="8a405-1588">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="8a405-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="8a405-1589">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="8a405-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="8a405-1590">Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="8a405-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="8a405-1591">Para expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *pages/index. cshtml. cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="8a405-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="8a405-1592">Prefixos</span><span class="sxs-lookup"><span data-stu-id="8a405-1592">Prefixes</span></span>

<span data-ttu-id="8a405-1593">As variáveis de ambiente carregadas na configuração do aplicativo são filtradas ao fornecer um prefixo para o `AddEnvironmentVariables` método.</span><span class="sxs-lookup"><span data-stu-id="8a405-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="8a405-1594">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="8a405-1595">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="8a405-1596">Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="8a405-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="8a405-1597">Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8a405-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8a405-1598">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="8a405-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="8a405-1599">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8a405-1600">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="8a405-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8a405-1601">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="8a405-1601">Connection string prefix</span></span> | <span data-ttu-id="8a405-1602">Provedor</span><span class="sxs-lookup"><span data-stu-id="8a405-1602">Provider</span></span> |
| ---
<span data-ttu-id="8a405-1603">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1604">'Blazor'</span></span>
- <span data-ttu-id="8a405-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1605">'Identity'</span></span>
- <span data-ttu-id="8a405-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1607">'Razor'</span></span>
- <span data-ttu-id="8a405-1608">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1609">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1610">'Blazor'</span></span>
- <span data-ttu-id="8a405-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1611">'Identity'</span></span>
- <span data-ttu-id="8a405-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1613">'Razor'</span></span>
- <span data-ttu-id="8a405-1614">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1615">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1616">'Blazor'</span></span>
- <span data-ttu-id="8a405-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1617">'Identity'</span></span>
- <span data-ttu-id="8a405-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1619">'Razor'</span></span>
- <span data-ttu-id="8a405-1620">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1621">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1622">'Blazor'</span></span>
- <span data-ttu-id="8a405-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1623">'Identity'</span></span>
- <span data-ttu-id="8a405-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1625">'Razor'</span></span>
- <span data-ttu-id="8a405-1626">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1627">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1628">'Blazor'</span></span>
- <span data-ttu-id="8a405-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1629">'Identity'</span></span>
- <span data-ttu-id="8a405-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1631">'Razor'</span></span>
- <span data-ttu-id="8a405-1632">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1633">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1634">'Blazor'</span></span>
- <span data-ttu-id="8a405-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1635">'Identity'</span></span>
- <span data-ttu-id="8a405-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1637">'Razor'</span></span>
- <span data-ttu-id="8a405-1638">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1639">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1640">'Blazor'</span></span>
- <span data-ttu-id="8a405-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1641">'Identity'</span></span>
- <span data-ttu-id="8a405-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1643">'Razor'</span></span>
- <span data-ttu-id="8a405-1644">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1645">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1646">'Blazor'</span></span>
- <span data-ttu-id="8a405-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1647">'Identity'</span></span>
- <span data-ttu-id="8a405-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1649">'Razor'</span></span>
- <span data-ttu-id="8a405-1650">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1651">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1652">'Blazor'</span></span>
- <span data-ttu-id="8a405-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1653">'Identity'</span></span>
- <span data-ttu-id="8a405-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1655">'Razor'</span></span>
- <span data-ttu-id="8a405-1656">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1657">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1658">'Blazor'</span></span>
- <span data-ttu-id="8a405-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1659">'Identity'</span></span>
- <span data-ttu-id="8a405-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1661">'Razor'</span></span>
- <span data-ttu-id="8a405-1662">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-1663">------------ | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1664">'Blazor'</span></span>
- <span data-ttu-id="8a405-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1665">'Identity'</span></span>
- <span data-ttu-id="8a405-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1667">'Razor'</span></span>
- <span data-ttu-id="8a405-1668">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1669">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1670">'Blazor'</span></span>
- <span data-ttu-id="8a405-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1671">'Identity'</span></span>
- <span data-ttu-id="8a405-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1673">'Razor'</span></span>
- <span data-ttu-id="8a405-1674">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-1675">---- | | `CUSTOMCONNSTR_` | Provedor personalizado | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | [SQL Server do banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="8a405-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="8a405-1676">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="8a405-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8a405-1677">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="8a405-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8a405-1678">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="8a405-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8a405-1679">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="8a405-1679">Environment variable key</span></span> | <span data-ttu-id="8a405-1680">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="8a405-1680">Converted configuration key</span></span> | <span data-ttu-id="8a405-1681">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="8a405-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="8a405-1682">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1683">'Blazor'</span></span>
- <span data-ttu-id="8a405-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1684">'Identity'</span></span>
- <span data-ttu-id="8a405-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1686">'Razor'</span></span>
- <span data-ttu-id="8a405-1687">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1688">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1689">'Blazor'</span></span>
- <span data-ttu-id="8a405-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1690">'Identity'</span></span>
- <span data-ttu-id="8a405-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1692">'Razor'</span></span>
- <span data-ttu-id="8a405-1693">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1694">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1695">'Blazor'</span></span>
- <span data-ttu-id="8a405-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1696">'Identity'</span></span>
- <span data-ttu-id="8a405-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1698">'Razor'</span></span>
- <span data-ttu-id="8a405-1699">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1700">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1701">'Blazor'</span></span>
- <span data-ttu-id="8a405-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1702">'Identity'</span></span>
- <span data-ttu-id="8a405-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1704">'Razor'</span></span>
- <span data-ttu-id="8a405-1705">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1706">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1707">'Blazor'</span></span>
- <span data-ttu-id="8a405-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1708">'Identity'</span></span>
- <span data-ttu-id="8a405-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1710">'Razor'</span></span>
- <span data-ttu-id="8a405-1711">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1712">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1713">'Blazor'</span></span>
- <span data-ttu-id="8a405-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1714">'Identity'</span></span>
- <span data-ttu-id="8a405-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1716">'Razor'</span></span>
- <span data-ttu-id="8a405-1717">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1718">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1719">'Blazor'</span></span>
- <span data-ttu-id="8a405-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1720">'Identity'</span></span>
- <span data-ttu-id="8a405-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1722">'Razor'</span></span>
- <span data-ttu-id="8a405-1723">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1724">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1725">'Blazor'</span></span>
- <span data-ttu-id="8a405-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1726">'Identity'</span></span>
- <span data-ttu-id="8a405-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1728">'Razor'</span></span>
- <span data-ttu-id="8a405-1729">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1730">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1731">'Blazor'</span></span>
- <span data-ttu-id="8a405-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1732">'Identity'</span></span>
- <span data-ttu-id="8a405-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1734">'Razor'</span></span>
- <span data-ttu-id="8a405-1735">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1736">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1737">'Blazor'</span></span>
- <span data-ttu-id="8a405-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1738">'Identity'</span></span>
- <span data-ttu-id="8a405-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1740">'Razor'</span></span>
- <span data-ttu-id="8a405-1741">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-1742">------------ | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1743">'Blazor'</span></span>
- <span data-ttu-id="8a405-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1744">'Identity'</span></span>
- <span data-ttu-id="8a405-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1746">'Razor'</span></span>
- <span data-ttu-id="8a405-1747">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1748">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1749">'Blazor'</span></span>
- <span data-ttu-id="8a405-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1750">'Identity'</span></span>
- <span data-ttu-id="8a405-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1752">'Razor'</span></span>
- <span data-ttu-id="8a405-1753">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1754">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1755">'Blazor'</span></span>
- <span data-ttu-id="8a405-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1756">'Identity'</span></span>
- <span data-ttu-id="8a405-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1758">'Razor'</span></span>
- <span data-ttu-id="8a405-1759">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1760">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1761">'Blazor'</span></span>
- <span data-ttu-id="8a405-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1762">'Identity'</span></span>
- <span data-ttu-id="8a405-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1764">'Razor'</span></span>
- <span data-ttu-id="8a405-1765">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1766">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1767">'Blazor'</span></span>
- <span data-ttu-id="8a405-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1768">'Identity'</span></span>
- <span data-ttu-id="8a405-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1770">'Razor'</span></span>
- <span data-ttu-id="8a405-1771">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1772">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1773">'Blazor'</span></span>
- <span data-ttu-id="8a405-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1774">'Identity'</span></span>
- <span data-ttu-id="8a405-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1776">'Razor'</span></span>
- <span data-ttu-id="8a405-1777">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1778">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1779">'Blazor'</span></span>
- <span data-ttu-id="8a405-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1780">'Identity'</span></span>
- <span data-ttu-id="8a405-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1782">'Razor'</span></span>
- <span data-ttu-id="8a405-1783">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1784">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1785">'Blazor'</span></span>
- <span data-ttu-id="8a405-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1786">'Identity'</span></span>
- <span data-ttu-id="8a405-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1788">'Razor'</span></span>
- <span data-ttu-id="8a405-1789">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1790">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1791">'Blazor'</span></span>
- <span data-ttu-id="8a405-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1792">'Identity'</span></span>
- <span data-ttu-id="8a405-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1794">'Razor'</span></span>
- <span data-ttu-id="8a405-1795">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1796">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1797">'Blazor'</span></span>
- <span data-ttu-id="8a405-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1798">'Identity'</span></span>
- <span data-ttu-id="8a405-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1800">'Razor'</span></span>
- <span data-ttu-id="8a405-1801">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1802">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1803">'Blazor'</span></span>
- <span data-ttu-id="8a405-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1804">'Identity'</span></span>
- <span data-ttu-id="8a405-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1806">'Razor'</span></span>
- <span data-ttu-id="8a405-1807">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-1808">-------------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1809">'Blazor'</span></span>
- <span data-ttu-id="8a405-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1810">'Identity'</span></span>
- <span data-ttu-id="8a405-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1812">'Razor'</span></span>
- <span data-ttu-id="8a405-1813">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1814">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1815">'Blazor'</span></span>
- <span data-ttu-id="8a405-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1816">'Identity'</span></span>
- <span data-ttu-id="8a405-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1818">'Razor'</span></span>
- <span data-ttu-id="8a405-1819">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1820">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1821">'Blazor'</span></span>
- <span data-ttu-id="8a405-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1822">'Identity'</span></span>
- <span data-ttu-id="8a405-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1824">'Razor'</span></span>
- <span data-ttu-id="8a405-1825">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1826">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1827">'Blazor'</span></span>
- <span data-ttu-id="8a405-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1828">'Identity'</span></span>
- <span data-ttu-id="8a405-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1830">'Razor'</span></span>
- <span data-ttu-id="8a405-1831">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1832">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1833">'Blazor'</span></span>
- <span data-ttu-id="8a405-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1834">'Identity'</span></span>
- <span data-ttu-id="8a405-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1836">'Razor'</span></span>
- <span data-ttu-id="8a405-1837">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1838">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1839">'Blazor'</span></span>
- <span data-ttu-id="8a405-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1840">'Identity'</span></span>
- <span data-ttu-id="8a405-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1842">'Razor'</span></span>
- <span data-ttu-id="8a405-1843">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1844">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1845">'Blazor'</span></span>
- <span data-ttu-id="8a405-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1846">'Identity'</span></span>
- <span data-ttu-id="8a405-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1848">'Razor'</span></span>
- <span data-ttu-id="8a405-1849">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1850">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1851">'Blazor'</span></span>
- <span data-ttu-id="8a405-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1852">'Identity'</span></span>
- <span data-ttu-id="8a405-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1854">'Razor'</span></span>
- <span data-ttu-id="8a405-1855">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1856">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1857">'Blazor'</span></span>
- <span data-ttu-id="8a405-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1858">'Identity'</span></span>
- <span data-ttu-id="8a405-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1860">'Razor'</span></span>
- <span data-ttu-id="8a405-1861">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1862">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1863">'Blazor'</span></span>
- <span data-ttu-id="8a405-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1864">'Identity'</span></span>
- <span data-ttu-id="8a405-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1866">'Razor'</span></span>
- <span data-ttu-id="8a405-1867">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1868">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1869">'Blazor'</span></span>
- <span data-ttu-id="8a405-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1870">'Identity'</span></span>
- <span data-ttu-id="8a405-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1872">'Razor'</span></span>
- <span data-ttu-id="8a405-1873">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1874">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1875">'Blazor'</span></span>
- <span data-ttu-id="8a405-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1876">'Identity'</span></span>
- <span data-ttu-id="8a405-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1878">'Razor'</span></span>
- <span data-ttu-id="8a405-1879">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1880">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1881">'Blazor'</span></span>
- <span data-ttu-id="8a405-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1882">'Identity'</span></span>
- <span data-ttu-id="8a405-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1884">'Razor'</span></span>
- <span data-ttu-id="8a405-1885">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1886">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1887">'Blazor'</span></span>
- <span data-ttu-id="8a405-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1888">'Identity'</span></span>
- <span data-ttu-id="8a405-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1890">'Razor'</span></span>
- <span data-ttu-id="8a405-1891">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1892">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1893">'Blazor'</span></span>
- <span data-ttu-id="8a405-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1894">'Identity'</span></span>
- <span data-ttu-id="8a405-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1896">'Razor'</span></span>
- <span data-ttu-id="8a405-1897">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1898">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1899">'Blazor'</span></span>
- <span data-ttu-id="8a405-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1900">'Identity'</span></span>
- <span data-ttu-id="8a405-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1902">'Razor'</span></span>
- <span data-ttu-id="8a405-1903">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1904">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1905">'Blazor'</span></span>
- <span data-ttu-id="8a405-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1906">'Identity'</span></span>
- <span data-ttu-id="8a405-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1908">'Razor'</span></span>
- <span data-ttu-id="8a405-1909">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1910">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1911">'Blazor'</span></span>
- <span data-ttu-id="8a405-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1912">'Identity'</span></span>
- <span data-ttu-id="8a405-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1914">'Razor'</span></span>
- <span data-ttu-id="8a405-1915">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1916">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1917">'Blazor'</span></span>
- <span data-ttu-id="8a405-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1918">'Identity'</span></span>
- <span data-ttu-id="8a405-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1920">'Razor'</span></span>
- <span data-ttu-id="8a405-1921">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1922">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1923">'Blazor'</span></span>
- <span data-ttu-id="8a405-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1924">'Identity'</span></span>
- <span data-ttu-id="8a405-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1926">'Razor'</span></span>
- <span data-ttu-id="8a405-1927">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1928">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1929">'Blazor'</span></span>
- <span data-ttu-id="8a405-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1930">'Identity'</span></span>
- <span data-ttu-id="8a405-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1932">'Razor'</span></span>
- <span data-ttu-id="8a405-1933">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1934">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1935">'Blazor'</span></span>
- <span data-ttu-id="8a405-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1936">'Identity'</span></span>
- <span data-ttu-id="8a405-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1938">'Razor'</span></span>
- <span data-ttu-id="8a405-1939">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1940">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1941">'Blazor'</span></span>
- <span data-ttu-id="8a405-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1942">'Identity'</span></span>
- <span data-ttu-id="8a405-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1944">'Razor'</span></span>
- <span data-ttu-id="8a405-1945">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1946">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1947">'Blazor'</span></span>
- <span data-ttu-id="8a405-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1948">'Identity'</span></span>
- <span data-ttu-id="8a405-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1950">'Razor'</span></span>
- <span data-ttu-id="8a405-1951">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1952">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1953">'Blazor'</span></span>
- <span data-ttu-id="8a405-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1954">'Identity'</span></span>
- <span data-ttu-id="8a405-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1956">'Razor'</span></span>
- <span data-ttu-id="8a405-1957">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1958">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1959">'Blazor'</span></span>
- <span data-ttu-id="8a405-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1960">'Identity'</span></span>
- <span data-ttu-id="8a405-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1962">'Razor'</span></span>
- <span data-ttu-id="8a405-1963">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1964">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1965">'Blazor'</span></span>
- <span data-ttu-id="8a405-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1966">'Identity'</span></span>
- <span data-ttu-id="8a405-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1968">'Razor'</span></span>
- <span data-ttu-id="8a405-1969">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1970">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1971">'Blazor'</span></span>
- <span data-ttu-id="8a405-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1972">'Identity'</span></span>
- <span data-ttu-id="8a405-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1974">'Razor'</span></span>
- <span data-ttu-id="8a405-1975">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1976">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1977">'Blazor'</span></span>
- <span data-ttu-id="8a405-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1978">'Identity'</span></span>
- <span data-ttu-id="8a405-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1980">'Razor'</span></span>
- <span data-ttu-id="8a405-1981">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1982">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1983">'Blazor'</span></span>
- <span data-ttu-id="8a405-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1984">'Identity'</span></span>
- <span data-ttu-id="8a405-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1986">'Razor'</span></span>
- <span data-ttu-id="8a405-1987">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1988">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1989">'Blazor'</span></span>
- <span data-ttu-id="8a405-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1990">'Identity'</span></span>
- <span data-ttu-id="8a405-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1992">'Razor'</span></span>
- <span data-ttu-id="8a405-1993">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-1994">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1995">'Blazor'</span></span>
- <span data-ttu-id="8a405-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-1996">'Identity'</span></span>
- <span data-ttu-id="8a405-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-1998">'Razor'</span></span>
- <span data-ttu-id="8a405-1999">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2000">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2001">'Blazor'</span></span>
- <span data-ttu-id="8a405-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2002">'Identity'</span></span>
- <span data-ttu-id="8a405-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2004">'Razor'</span></span>
- <span data-ttu-id="8a405-2005">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2006">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2007">'Blazor'</span></span>
- <span data-ttu-id="8a405-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2008">'Identity'</span></span>
- <span data-ttu-id="8a405-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2010">'Razor'</span></span>
- <span data-ttu-id="8a405-2011">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2012">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2013">'Blazor'</span></span>
- <span data-ttu-id="8a405-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2014">'Identity'</span></span>
- <span data-ttu-id="8a405-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2016">'Razor'</span></span>
- <span data-ttu-id="8a405-2017">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2018">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2019">'Blazor'</span></span>
- <span data-ttu-id="8a405-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2020">'Identity'</span></span>
- <span data-ttu-id="8a405-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2022">'Razor'</span></span>
- <span data-ttu-id="8a405-2023">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2024">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2025">'Blazor'</span></span>
- <span data-ttu-id="8a405-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2026">'Identity'</span></span>
- <span data-ttu-id="8a405-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2028">'Razor'</span></span>
- <span data-ttu-id="8a405-2029">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="8a405-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="8a405-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="8a405-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8a405-2032">Valor: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="8a405-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8a405-2033">Valor: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="8a405-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8a405-2034">Valor`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="8a405-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="8a405-2035">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="8a405-2035">**Example**</span></span>

<span data-ttu-id="8a405-2036">Uma variável de ambiente de cadeia de conexão personalizada é criada no servidor:</span><span class="sxs-lookup"><span data-stu-id="8a405-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="8a405-2037">Nome &ndash; do`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="8a405-2037">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="8a405-2038">Valor &ndash; do`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="8a405-2038">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="8a405-2039">Se `IConfiguration` for injetado e atribuído a um campo chamado `_config` , leia o valor:</span><span class="sxs-lookup"><span data-stu-id="8a405-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="8a405-2040">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="8a405-2040">File Configuration Provider</span></span>

<span data-ttu-id="8a405-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="8a405-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8a405-2042">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="8a405-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="8a405-2043">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="8a405-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8a405-2044">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="8a405-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="8a405-2045">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="8a405-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8a405-2046">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="8a405-2046">INI Configuration Provider</span></span>

<span data-ttu-id="8a405-2047">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="8a405-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8a405-2048">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8a405-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8a405-2049">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="8a405-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="8a405-2050">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="8a405-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="8a405-2051">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="8a405-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="8a405-2052">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="8a405-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8a405-2053">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="8a405-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8a405-2054">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8a405-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8a405-2055">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="8a405-2055">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="8a405-2056">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="8a405-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8a405-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8a405-2057">section0:key0</span></span>
* <span data-ttu-id="8a405-2058">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8a405-2058">section0:key1</span></span>
* <span data-ttu-id="8a405-2059">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="8a405-2059">section1:subsection:key</span></span>
* <span data-ttu-id="8a405-2060">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="8a405-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="8a405-2061">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="8a405-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="8a405-2062">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="8a405-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="8a405-2063">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em runtime.</span><span class="sxs-lookup"><span data-stu-id="8a405-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="8a405-2064">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8a405-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8a405-2065">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="8a405-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="8a405-2066">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="8a405-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="8a405-2067">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="8a405-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8a405-2068">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="8a405-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8a405-2069">`AddJsonFile`é chamado automaticamente duas vezes quando um novo Construtor de hosts é inicializado com o `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="8a405-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8a405-2070">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="8a405-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="8a405-2071">*appsettings.json* &ndash; Esse arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="8a405-2071">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="8a405-2072">A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8a405-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="8a405-2073">*appSettings. {Environment}. JSON* &ndash; a versão do ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="8a405-2073">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="8a405-2074">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8a405-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8a405-2075">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="8a405-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8a405-2076">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="8a405-2076">Environment variables.</span></span>
* <span data-ttu-id="8a405-2077">[Segredos do usuário (Gerenciador de segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a405-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8a405-2078">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="8a405-2078">Command-line arguments.</span></span>

<span data-ttu-id="8a405-2079">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="8a405-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="8a405-2080">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="8a405-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="8a405-2081">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="8a405-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8a405-2082">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="8a405-2082">**Example**</span></span>

<span data-ttu-id="8a405-2083">O aplicativo de exemplo aproveita o método de conveniência estática `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="8a405-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="8a405-2084">A primeira chamada para `AddJsonFile` carrega a configuração de *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="8a405-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="8a405-2085">A segunda chamada para `AddJsonFile` carrega a configuração de *appSettings. { Ambiente}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8a405-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="8a405-2086">Para *appSettings. Development. JSON* no aplicativo de exemplo, o seguinte arquivo é carregado:</span><span class="sxs-lookup"><span data-stu-id="8a405-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="8a405-2087">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="8a405-2087">Run the sample app.</span></span> <span data-ttu-id="8a405-2088">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8a405-2089">A saída contém pares chave-valor para a configuração com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="8a405-2090">O nível de log para a chave `Logging:LogLevel:Default` é `Debug` ao executar o aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a405-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="8a405-2091">Execute o aplicativo de exemplo novamente no ambiente de produção:</span><span class="sxs-lookup"><span data-stu-id="8a405-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="8a405-2092">Abra o arquivo *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="8a405-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="8a405-2093">No `ConfigurationSample` perfil, altere o valor da variável de `ASPNETCORE_ENVIRONMENT` ambiente para `Production` .</span><span class="sxs-lookup"><span data-stu-id="8a405-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="8a405-2094">Salve o arquivo e execute o aplicativo com o `dotnet run` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="8a405-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="8a405-2095">As configurações em *appSettings. O Development. JSON* não substitui mais as configurações em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8a405-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="8a405-2096">O nível de log para a chave `Logging:LogLevel:Default` é `Warning` .</span><span class="sxs-lookup"><span data-stu-id="8a405-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="8a405-2097">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="8a405-2097">XML Configuration Provider</span></span>

<span data-ttu-id="8a405-2098">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="8a405-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8a405-2099">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8a405-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8a405-2100">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="8a405-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="8a405-2101">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="8a405-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="8a405-2102">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="8a405-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8a405-2103">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="8a405-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8a405-2104">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="8a405-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="8a405-2105">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="8a405-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="8a405-2106">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8a405-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8a405-2107">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="8a405-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="8a405-2108">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="8a405-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8a405-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8a405-2109">section0:key0</span></span>
* <span data-ttu-id="8a405-2110">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8a405-2110">section0:key1</span></span>
* <span data-ttu-id="8a405-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8a405-2111">section1:key0</span></span>
* <span data-ttu-id="8a405-2112">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8a405-2112">section1:key1</span></span>

<span data-ttu-id="8a405-2113">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="8a405-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="8a405-2114">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="8a405-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8a405-2115">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="8a405-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="8a405-2116">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="8a405-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="8a405-2117">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="8a405-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="8a405-2118">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="8a405-2118">section:section1:key:key1</span></span>

<span data-ttu-id="8a405-2119">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="8a405-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8a405-2120">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="8a405-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8a405-2121">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8a405-2121">key:attribute</span></span>
* <span data-ttu-id="8a405-2122">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8a405-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8a405-2123">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="8a405-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="8a405-2124">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8a405-2125">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="8a405-2125">The key is the file name.</span></span> <span data-ttu-id="8a405-2126">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="8a405-2126">The value contains the file's contents.</span></span> <span data-ttu-id="8a405-2127">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="8a405-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8a405-2128">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8a405-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8a405-2129">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="8a405-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8a405-2130">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="8a405-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="8a405-2131">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="8a405-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8a405-2132">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="8a405-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8a405-2133">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="8a405-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8a405-2134">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8a405-2135">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8a405-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="8a405-2136">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="8a405-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="8a405-2137">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8a405-2138">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8a405-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8a405-2139">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="8a405-2140">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="8a405-2141">No exemplo a seguir, um dicionário de configuração é criado:</span><span class="sxs-lookup"><span data-stu-id="8a405-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="8a405-2142">O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="8a405-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="8a405-2143">GetValue</span></span>

<span data-ttu-id="8a405-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrai um único valor da configuração com uma chave especificada e converte-o para o tipo de não coleção especificado.</span><span class="sxs-lookup"><span data-stu-id="8a405-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="8a405-2145">Uma sobrecarga aceita um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="8a405-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="8a405-2146">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a405-2146">The following example:</span></span>

* <span data-ttu-id="8a405-2147">extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="8a405-2148">Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="8a405-2149">Digita o valor como um `int`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="8a405-2150">Armazena o valor na propriedade `NumberConfig` para uso pela página.</span><span class="sxs-lookup"><span data-stu-id="8a405-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8a405-2151">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="8a405-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8a405-2152">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="8a405-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="8a405-2153">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="8a405-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="8a405-2154">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="8a405-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8a405-2155">section0:key0</span></span>
* <span data-ttu-id="8a405-2156">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8a405-2156">section0:key1</span></span>
* <span data-ttu-id="8a405-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8a405-2157">section1:key0</span></span>
* <span data-ttu-id="8a405-2158">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8a405-2158">section1:key1</span></span>
* <span data-ttu-id="8a405-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="8a405-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="8a405-2160">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="8a405-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="8a405-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="8a405-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="8a405-2162">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="8a405-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="8a405-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="8a405-2163">GetSection</span></span>

<span data-ttu-id="8a405-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="8a405-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8a405-2165">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="8a405-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="8a405-2166">`configSection` não tem um valor, apenas uma chave e um caminho.</span><span class="sxs-lookup"><span data-stu-id="8a405-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="8a405-2167">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="8a405-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="8a405-2168">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8a405-2169">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="8a405-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8a405-2170">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="8a405-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8a405-2171"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="8a405-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="8a405-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="8a405-2172">GetChildren</span></span>

<span data-ttu-id="8a405-2173">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="8a405-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="8a405-2174">Exists</span><span class="sxs-lookup"><span data-stu-id="8a405-2174">Exists</span></span>

<span data-ttu-id="8a405-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="8a405-2176">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="8a405-2177">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="8a405-2177">Bind to an object graph</span></span>

<span data-ttu-id="8a405-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="8a405-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="8a405-2179">Assim como acontece com a associação de um objeto simples, somente as propriedades públicas de leitura/gravação são associadas.</span><span class="sxs-lookup"><span data-stu-id="8a405-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="8a405-2180">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a405-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="8a405-2181">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="8a405-2182">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="8a405-2183">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="8a405-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="8a405-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="8a405-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="8a405-2185">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="8a405-2186">O código a seguir mostra como usar `Get<T>` o com o exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="8a405-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8a405-2187">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="8a405-2187">Bind an array to a class</span></span>

<span data-ttu-id="8a405-2188">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="8a405-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="8a405-2189">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8a405-2190">Qualquer formato de matriz que expõe um segmento de chave numérico ( `:0:` , `:1:` , &hellip; `:{n}:` ) é capaz de associar a matriz a uma matriz de classe poco.</span><span class="sxs-lookup"><span data-stu-id="8a405-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="8a405-2191">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="8a405-2191">Binding is provided by convention.</span></span> <span data-ttu-id="8a405-2192">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="8a405-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="8a405-2193">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="8a405-2193">**In-memory array processing**</span></span>

<span data-ttu-id="8a405-2194">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="8a405-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="8a405-2195">Chave</span><span class="sxs-lookup"><span data-stu-id="8a405-2195">Key</span></span>             | <span data-ttu-id="8a405-2196">Valor</span><span class="sxs-lookup"><span data-stu-id="8a405-2196">Value</span></span>  |
| :---
<span data-ttu-id="8a405-2197">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2198">'Blazor'</span></span>
- <span data-ttu-id="8a405-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2199">'Identity'</span></span>
- <span data-ttu-id="8a405-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2201">'Razor'</span></span>
- <span data-ttu-id="8a405-2202">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2203">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2204">'Blazor'</span></span>
- <span data-ttu-id="8a405-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2205">'Identity'</span></span>
- <span data-ttu-id="8a405-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2207">'Razor'</span></span>
- <span data-ttu-id="8a405-2208">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2209">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2210">'Blazor'</span></span>
- <span data-ttu-id="8a405-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2211">'Identity'</span></span>
- <span data-ttu-id="8a405-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2213">'Razor'</span></span>
- <span data-ttu-id="8a405-2214">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2215">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2216">'Blazor'</span></span>
- <span data-ttu-id="8a405-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2217">'Identity'</span></span>
- <span data-ttu-id="8a405-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2219">'Razor'</span></span>
- <span data-ttu-id="8a405-2220">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-2221">-------: | :----: | | matriz: entradas: 0 | value0 | | matriz: entradas: 1 | value1 | | matriz: entradas: 2 | value2 | | matriz: entradas: 4 | Value4 | | matriz: entradas: 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="8a405-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="8a405-2222">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="8a405-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="8a405-2223">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="8a405-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="8a405-2224">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="8a405-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="8a405-2225">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="8a405-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="8a405-2226">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="8a405-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="8a405-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)a sintaxe também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="8a405-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="8a405-2228">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="8a405-2229">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="8a405-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8a405-2230">`ArrayExample.Entries` Valor</span><span class="sxs-lookup"><span data-stu-id="8a405-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="8a405-2231">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2232">'Blazor'</span></span>
- <span data-ttu-id="8a405-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2233">'Identity'</span></span>
- <span data-ttu-id="8a405-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2235">'Razor'</span></span>
- <span data-ttu-id="8a405-2236">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2237">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2238">'Blazor'</span></span>
- <span data-ttu-id="8a405-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2239">'Identity'</span></span>
- <span data-ttu-id="8a405-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2241">'Razor'</span></span>
- <span data-ttu-id="8a405-2242">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2243">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2244">'Blazor'</span></span>
- <span data-ttu-id="8a405-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2245">'Identity'</span></span>
- <span data-ttu-id="8a405-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2247">'Razor'</span></span>
- <span data-ttu-id="8a405-2248">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2249">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2250">'Blazor'</span></span>
- <span data-ttu-id="8a405-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2251">'Identity'</span></span>
- <span data-ttu-id="8a405-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2253">'Razor'</span></span>
- <span data-ttu-id="8a405-2254">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2255">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2256">'Blazor'</span></span>
- <span data-ttu-id="8a405-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2257">'Identity'</span></span>
- <span data-ttu-id="8a405-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2259">'Razor'</span></span>
- <span data-ttu-id="8a405-2260">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2261">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2262">'Blazor'</span></span>
- <span data-ttu-id="8a405-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2263">'Identity'</span></span>
- <span data-ttu-id="8a405-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2265">'Razor'</span></span>
- <span data-ttu-id="8a405-2266">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2267">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2268">'Blazor'</span></span>
- <span data-ttu-id="8a405-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2269">'Identity'</span></span>
- <span data-ttu-id="8a405-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2271">'Razor'</span></span>
- <span data-ttu-id="8a405-2272">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2273">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2274">'Blazor'</span></span>
- <span data-ttu-id="8a405-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2275">'Identity'</span></span>
- <span data-ttu-id="8a405-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2277">'Razor'</span></span>
- <span data-ttu-id="8a405-2278">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2279">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2280">'Blazor'</span></span>
- <span data-ttu-id="8a405-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2281">'Identity'</span></span>
- <span data-ttu-id="8a405-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2283">'Razor'</span></span>
- <span data-ttu-id="8a405-2284">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2285">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2286">'Blazor'</span></span>
- <span data-ttu-id="8a405-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2287">'Identity'</span></span>
- <span data-ttu-id="8a405-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2289">'Razor'</span></span>
- <span data-ttu-id="8a405-2290">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2291">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2292">'Blazor'</span></span>
- <span data-ttu-id="8a405-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2293">'Identity'</span></span>
- <span data-ttu-id="8a405-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2295">'Razor'</span></span>
- <span data-ttu-id="8a405-2296">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-2297">-------------: | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2298">'Blazor'</span></span>
- <span data-ttu-id="8a405-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2299">'Identity'</span></span>
- <span data-ttu-id="8a405-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2301">'Razor'</span></span>
- <span data-ttu-id="8a405-2302">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2303">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2304">'Blazor'</span></span>
- <span data-ttu-id="8a405-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2305">'Identity'</span></span>
- <span data-ttu-id="8a405-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2307">'Razor'</span></span>
- <span data-ttu-id="8a405-2308">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2309">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2310">'Blazor'</span></span>
- <span data-ttu-id="8a405-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2311">'Identity'</span></span>
- <span data-ttu-id="8a405-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2313">'Razor'</span></span>
- <span data-ttu-id="8a405-2314">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2315">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2316">'Blazor'</span></span>
- <span data-ttu-id="8a405-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2317">'Identity'</span></span>
- <span data-ttu-id="8a405-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2319">'Razor'</span></span>
- <span data-ttu-id="8a405-2320">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2321">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2322">'Blazor'</span></span>
- <span data-ttu-id="8a405-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2323">'Identity'</span></span>
- <span data-ttu-id="8a405-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2325">'Razor'</span></span>
- <span data-ttu-id="8a405-2326">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2327">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2328">'Blazor'</span></span>
- <span data-ttu-id="8a405-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2329">'Identity'</span></span>
- <span data-ttu-id="8a405-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2331">'Razor'</span></span>
- <span data-ttu-id="8a405-2332">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2333">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2334">'Blazor'</span></span>
- <span data-ttu-id="8a405-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2335">'Identity'</span></span>
- <span data-ttu-id="8a405-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2337">'Razor'</span></span>
- <span data-ttu-id="8a405-2338">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2339">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2340">'Blazor'</span></span>
- <span data-ttu-id="8a405-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2341">'Identity'</span></span>
- <span data-ttu-id="8a405-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2343">'Razor'</span></span>
- <span data-ttu-id="8a405-2344">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2345">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2346">'Blazor'</span></span>
- <span data-ttu-id="8a405-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2347">'Identity'</span></span>
- <span data-ttu-id="8a405-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2349">'Razor'</span></span>
- <span data-ttu-id="8a405-2350">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2351">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2352">'Blazor'</span></span>
- <span data-ttu-id="8a405-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2353">'Identity'</span></span>
- <span data-ttu-id="8a405-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2355">'Razor'</span></span>
- <span data-ttu-id="8a405-2356">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2357">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2358">'Blazor'</span></span>
- <span data-ttu-id="8a405-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2359">'Identity'</span></span>
- <span data-ttu-id="8a405-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2361">'Razor'</span></span>
- <span data-ttu-id="8a405-2362">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-2363">-------------: | | 0 | value0 | | 1 | value1 | | 2 | value2 | | 3 | Value4 | | 4 | value5 |</span><span class="sxs-lookup"><span data-stu-id="8a405-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="8a405-2364">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8a405-2365">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="8a405-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8a405-2366">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="8a405-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8a405-2367">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="8a405-2368">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="8a405-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="8a405-2369">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="8a405-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="8a405-2370">Em `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="8a405-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="8a405-2371">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="8a405-2372">Chave</span><span class="sxs-lookup"><span data-stu-id="8a405-2372">Key</span></span>             | <span data-ttu-id="8a405-2373">Valor</span><span class="sxs-lookup"><span data-stu-id="8a405-2373">Value</span></span>  |
| :---
<span data-ttu-id="8a405-2374">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2375">'Blazor'</span></span>
- <span data-ttu-id="8a405-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2376">'Identity'</span></span>
- <span data-ttu-id="8a405-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2378">'Razor'</span></span>
- <span data-ttu-id="8a405-2379">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2380">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2381">'Blazor'</span></span>
- <span data-ttu-id="8a405-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2382">'Identity'</span></span>
- <span data-ttu-id="8a405-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2384">'Razor'</span></span>
- <span data-ttu-id="8a405-2385">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2386">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2387">'Blazor'</span></span>
- <span data-ttu-id="8a405-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2388">'Identity'</span></span>
- <span data-ttu-id="8a405-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2390">'Razor'</span></span>
- <span data-ttu-id="8a405-2391">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2392">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2393">'Blazor'</span></span>
- <span data-ttu-id="8a405-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2394">'Identity'</span></span>
- <span data-ttu-id="8a405-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2396">'Razor'</span></span>
- <span data-ttu-id="8a405-2397">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-2398">-------: | :----: | | matriz: entradas: 3 | Value3 |</span><span class="sxs-lookup"><span data-stu-id="8a405-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="8a405-2399">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="8a405-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="8a405-2400">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="8a405-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8a405-2401">`ArrayExample.Entries` Valor</span><span class="sxs-lookup"><span data-stu-id="8a405-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="8a405-2402">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2403">'Blazor'</span></span>
- <span data-ttu-id="8a405-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2404">'Identity'</span></span>
- <span data-ttu-id="8a405-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2406">'Razor'</span></span>
- <span data-ttu-id="8a405-2407">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2408">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2409">'Blazor'</span></span>
- <span data-ttu-id="8a405-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2410">'Identity'</span></span>
- <span data-ttu-id="8a405-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2412">'Razor'</span></span>
- <span data-ttu-id="8a405-2413">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2414">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2415">'Blazor'</span></span>
- <span data-ttu-id="8a405-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2416">'Identity'</span></span>
- <span data-ttu-id="8a405-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2418">'Razor'</span></span>
- <span data-ttu-id="8a405-2419">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2420">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2421">'Blazor'</span></span>
- <span data-ttu-id="8a405-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2422">'Identity'</span></span>
- <span data-ttu-id="8a405-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2424">'Razor'</span></span>
- <span data-ttu-id="8a405-2425">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2426">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2427">'Blazor'</span></span>
- <span data-ttu-id="8a405-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2428">'Identity'</span></span>
- <span data-ttu-id="8a405-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2430">'Razor'</span></span>
- <span data-ttu-id="8a405-2431">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2432">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2433">'Blazor'</span></span>
- <span data-ttu-id="8a405-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2434">'Identity'</span></span>
- <span data-ttu-id="8a405-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2436">'Razor'</span></span>
- <span data-ttu-id="8a405-2437">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2438">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2439">'Blazor'</span></span>
- <span data-ttu-id="8a405-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2440">'Identity'</span></span>
- <span data-ttu-id="8a405-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2442">'Razor'</span></span>
- <span data-ttu-id="8a405-2443">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2444">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2445">'Blazor'</span></span>
- <span data-ttu-id="8a405-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2446">'Identity'</span></span>
- <span data-ttu-id="8a405-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2448">'Razor'</span></span>
- <span data-ttu-id="8a405-2449">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2450">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2451">'Blazor'</span></span>
- <span data-ttu-id="8a405-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2452">'Identity'</span></span>
- <span data-ttu-id="8a405-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2454">'Razor'</span></span>
- <span data-ttu-id="8a405-2455">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2456">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2457">'Blazor'</span></span>
- <span data-ttu-id="8a405-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2458">'Identity'</span></span>
- <span data-ttu-id="8a405-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2460">'Razor'</span></span>
- <span data-ttu-id="8a405-2461">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2462">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2463">'Blazor'</span></span>
- <span data-ttu-id="8a405-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2464">'Identity'</span></span>
- <span data-ttu-id="8a405-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2466">'Razor'</span></span>
- <span data-ttu-id="8a405-2467">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-2468">-------------: | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2469">'Blazor'</span></span>
- <span data-ttu-id="8a405-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2470">'Identity'</span></span>
- <span data-ttu-id="8a405-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2472">'Razor'</span></span>
- <span data-ttu-id="8a405-2473">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2474">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2475">'Blazor'</span></span>
- <span data-ttu-id="8a405-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2476">'Identity'</span></span>
- <span data-ttu-id="8a405-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2478">'Razor'</span></span>
- <span data-ttu-id="8a405-2479">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2480">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2481">'Blazor'</span></span>
- <span data-ttu-id="8a405-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2482">'Identity'</span></span>
- <span data-ttu-id="8a405-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2484">'Razor'</span></span>
- <span data-ttu-id="8a405-2485">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2486">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2487">'Blazor'</span></span>
- <span data-ttu-id="8a405-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2488">'Identity'</span></span>
- <span data-ttu-id="8a405-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2490">'Razor'</span></span>
- <span data-ttu-id="8a405-2491">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2492">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2493">'Blazor'</span></span>
- <span data-ttu-id="8a405-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2494">'Identity'</span></span>
- <span data-ttu-id="8a405-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2496">'Razor'</span></span>
- <span data-ttu-id="8a405-2497">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2498">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2499">'Blazor'</span></span>
- <span data-ttu-id="8a405-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2500">'Identity'</span></span>
- <span data-ttu-id="8a405-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2502">'Razor'</span></span>
- <span data-ttu-id="8a405-2503">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2504">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2505">'Blazor'</span></span>
- <span data-ttu-id="8a405-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2506">'Identity'</span></span>
- <span data-ttu-id="8a405-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2508">'Razor'</span></span>
- <span data-ttu-id="8a405-2509">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2510">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2511">'Blazor'</span></span>
- <span data-ttu-id="8a405-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2512">'Identity'</span></span>
- <span data-ttu-id="8a405-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2514">'Razor'</span></span>
- <span data-ttu-id="8a405-2515">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2516">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2517">'Blazor'</span></span>
- <span data-ttu-id="8a405-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2518">'Identity'</span></span>
- <span data-ttu-id="8a405-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2520">'Razor'</span></span>
- <span data-ttu-id="8a405-2521">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2522">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2523">'Blazor'</span></span>
- <span data-ttu-id="8a405-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2524">'Identity'</span></span>
- <span data-ttu-id="8a405-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2526">'Razor'</span></span>
- <span data-ttu-id="8a405-2527">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2528">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2529">'Blazor'</span></span>
- <span data-ttu-id="8a405-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2530">'Identity'</span></span>
- <span data-ttu-id="8a405-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2532">'Razor'</span></span>
- <span data-ttu-id="8a405-2533">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-2534">-------------: | | 0 | value0 | | 1 | value1 | | 2 | value2 | | 3 | Value3 | | 4 | Value4 | | 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="8a405-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="8a405-2535">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="8a405-2535">**JSON array processing**</span></span>

<span data-ttu-id="8a405-2536">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="8a405-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="8a405-2537">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="8a405-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="8a405-2538">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="8a405-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="8a405-2539">Chave</span><span class="sxs-lookup"><span data-stu-id="8a405-2539">Key</span></span>                     | <span data-ttu-id="8a405-2540">Valor</span><span class="sxs-lookup"><span data-stu-id="8a405-2540">Value</span></span>  |
| ---
<span data-ttu-id="8a405-2541">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2542">'Blazor'</span></span>
- <span data-ttu-id="8a405-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2543">'Identity'</span></span>
- <span data-ttu-id="8a405-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2545">'Razor'</span></span>
- <span data-ttu-id="8a405-2546">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2547">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2548">'Blazor'</span></span>
- <span data-ttu-id="8a405-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2549">'Identity'</span></span>
- <span data-ttu-id="8a405-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2551">'Razor'</span></span>
- <span data-ttu-id="8a405-2552">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2553">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2554">'Blazor'</span></span>
- <span data-ttu-id="8a405-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2555">'Identity'</span></span>
- <span data-ttu-id="8a405-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2557">'Razor'</span></span>
- <span data-ttu-id="8a405-2558">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2559">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2560">'Blazor'</span></span>
- <span data-ttu-id="8a405-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2561">'Identity'</span></span>
- <span data-ttu-id="8a405-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2563">'Razor'</span></span>
- <span data-ttu-id="8a405-2564">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2565">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2566">'Blazor'</span></span>
- <span data-ttu-id="8a405-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2567">'Identity'</span></span>
- <span data-ttu-id="8a405-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2569">'Razor'</span></span>
- <span data-ttu-id="8a405-2570">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2571">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2572">'Blazor'</span></span>
- <span data-ttu-id="8a405-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2573">'Identity'</span></span>
- <span data-ttu-id="8a405-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2575">'Razor'</span></span>
- <span data-ttu-id="8a405-2576">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2577">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2578">'Blazor'</span></span>
- <span data-ttu-id="8a405-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2579">'Identity'</span></span>
- <span data-ttu-id="8a405-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2581">'Razor'</span></span>
- <span data-ttu-id="8a405-2582">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2583">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2584">'Blazor'</span></span>
- <span data-ttu-id="8a405-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2585">'Identity'</span></span>
- <span data-ttu-id="8a405-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2587">'Razor'</span></span>
- <span data-ttu-id="8a405-2588">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2589">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2590">'Blazor'</span></span>
- <span data-ttu-id="8a405-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2591">'Identity'</span></span>
- <span data-ttu-id="8a405-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2593">'Razor'</span></span>
- <span data-ttu-id="8a405-2594">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-2595">------------ | :----: | | json_array: chave | valor de | | json_array: subseção: 0 | valorB | | json_array: subseção: 1 | valueC | | json_array: subseção: 2 | Valor |</span><span class="sxs-lookup"><span data-stu-id="8a405-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="8a405-2596">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a405-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="8a405-2597">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="8a405-2598">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="8a405-2599">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="8a405-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="8a405-2600">`JsonArrayExample.Subsection` Valor</span><span class="sxs-lookup"><span data-stu-id="8a405-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="8a405-2601">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2602">'Blazor'</span></span>
- <span data-ttu-id="8a405-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2603">'Identity'</span></span>
- <span data-ttu-id="8a405-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2605">'Razor'</span></span>
- <span data-ttu-id="8a405-2606">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2607">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2608">'Blazor'</span></span>
- <span data-ttu-id="8a405-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2609">'Identity'</span></span>
- <span data-ttu-id="8a405-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2611">'Razor'</span></span>
- <span data-ttu-id="8a405-2612">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2613">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2614">'Blazor'</span></span>
- <span data-ttu-id="8a405-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2615">'Identity'</span></span>
- <span data-ttu-id="8a405-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2617">'Razor'</span></span>
- <span data-ttu-id="8a405-2618">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2619">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2620">'Blazor'</span></span>
- <span data-ttu-id="8a405-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2621">'Identity'</span></span>
- <span data-ttu-id="8a405-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2623">'Razor'</span></span>
- <span data-ttu-id="8a405-2624">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2625">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2626">'Blazor'</span></span>
- <span data-ttu-id="8a405-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2627">'Identity'</span></span>
- <span data-ttu-id="8a405-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2629">'Razor'</span></span>
- <span data-ttu-id="8a405-2630">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2631">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2632">'Blazor'</span></span>
- <span data-ttu-id="8a405-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2633">'Identity'</span></span>
- <span data-ttu-id="8a405-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2635">'Razor'</span></span>
- <span data-ttu-id="8a405-2636">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2637">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2638">'Blazor'</span></span>
- <span data-ttu-id="8a405-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2639">'Identity'</span></span>
- <span data-ttu-id="8a405-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2641">'Razor'</span></span>
- <span data-ttu-id="8a405-2642">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2643">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2644">'Blazor'</span></span>
- <span data-ttu-id="8a405-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2645">'Identity'</span></span>
- <span data-ttu-id="8a405-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2647">'Razor'</span></span>
- <span data-ttu-id="8a405-2648">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2649">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2650">'Blazor'</span></span>
- <span data-ttu-id="8a405-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2651">'Identity'</span></span>
- <span data-ttu-id="8a405-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2653">'Razor'</span></span>
- <span data-ttu-id="8a405-2654">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2655">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2656">'Blazor'</span></span>
- <span data-ttu-id="8a405-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2657">'Identity'</span></span>
- <span data-ttu-id="8a405-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2659">'Razor'</span></span>
- <span data-ttu-id="8a405-2660">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2661">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2662">'Blazor'</span></span>
- <span data-ttu-id="8a405-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2663">'Identity'</span></span>
- <span data-ttu-id="8a405-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2665">'Razor'</span></span>
- <span data-ttu-id="8a405-2666">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2667">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2668">'Blazor'</span></span>
- <span data-ttu-id="8a405-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2669">'Identity'</span></span>
- <span data-ttu-id="8a405-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2671">'Razor'</span></span>
- <span data-ttu-id="8a405-2672">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2673">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2674">'Blazor'</span></span>
- <span data-ttu-id="8a405-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2675">'Identity'</span></span>
- <span data-ttu-id="8a405-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2677">'Razor'</span></span>
- <span data-ttu-id="8a405-2678">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2679">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2680">'Blazor'</span></span>
- <span data-ttu-id="8a405-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2681">'Identity'</span></span>
- <span data-ttu-id="8a405-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2683">'Razor'</span></span>
- <span data-ttu-id="8a405-2684">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-2685">-----------------: | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2686">'Blazor'</span></span>
- <span data-ttu-id="8a405-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2687">'Identity'</span></span>
- <span data-ttu-id="8a405-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2689">'Razor'</span></span>
- <span data-ttu-id="8a405-2690">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2691">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2692">'Blazor'</span></span>
- <span data-ttu-id="8a405-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2693">'Identity'</span></span>
- <span data-ttu-id="8a405-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2695">'Razor'</span></span>
- <span data-ttu-id="8a405-2696">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2697">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2698">'Blazor'</span></span>
- <span data-ttu-id="8a405-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2699">'Identity'</span></span>
- <span data-ttu-id="8a405-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2701">'Razor'</span></span>
- <span data-ttu-id="8a405-2702">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2703">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2704">'Blazor'</span></span>
- <span data-ttu-id="8a405-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2705">'Identity'</span></span>
- <span data-ttu-id="8a405-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2707">'Razor'</span></span>
- <span data-ttu-id="8a405-2708">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2709">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2710">'Blazor'</span></span>
- <span data-ttu-id="8a405-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2711">'Identity'</span></span>
- <span data-ttu-id="8a405-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2713">'Razor'</span></span>
- <span data-ttu-id="8a405-2714">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2715">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2716">'Blazor'</span></span>
- <span data-ttu-id="8a405-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2717">'Identity'</span></span>
- <span data-ttu-id="8a405-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2719">'Razor'</span></span>
- <span data-ttu-id="8a405-2720">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2721">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2722">'Blazor'</span></span>
- <span data-ttu-id="8a405-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2723">'Identity'</span></span>
- <span data-ttu-id="8a405-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2725">'Razor'</span></span>
- <span data-ttu-id="8a405-2726">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2727">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2728">'Blazor'</span></span>
- <span data-ttu-id="8a405-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2729">'Identity'</span></span>
- <span data-ttu-id="8a405-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2731">'Razor'</span></span>
- <span data-ttu-id="8a405-2732">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2733">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2734">'Blazor'</span></span>
- <span data-ttu-id="8a405-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2735">'Identity'</span></span>
- <span data-ttu-id="8a405-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2737">'Razor'</span></span>
- <span data-ttu-id="8a405-2738">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2739">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2740">'Blazor'</span></span>
- <span data-ttu-id="8a405-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2741">'Identity'</span></span>
- <span data-ttu-id="8a405-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2743">'Razor'</span></span>
- <span data-ttu-id="8a405-2744">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2745">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2746">'Blazor'</span></span>
- <span data-ttu-id="8a405-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2747">'Identity'</span></span>
- <span data-ttu-id="8a405-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2749">'Razor'</span></span>
- <span data-ttu-id="8a405-2750">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2751">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2752">'Blazor'</span></span>
- <span data-ttu-id="8a405-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2753">'Identity'</span></span>
- <span data-ttu-id="8a405-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2755">'Razor'</span></span>
- <span data-ttu-id="8a405-2756">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2757">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2758">'Blazor'</span></span>
- <span data-ttu-id="8a405-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2759">'Identity'</span></span>
- <span data-ttu-id="8a405-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2761">'Razor'</span></span>
- <span data-ttu-id="8a405-2762">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a405-2763">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a405-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a405-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2764">'Blazor'</span></span>
- <span data-ttu-id="8a405-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a405-2765">'Identity'</span></span>
- <span data-ttu-id="8a405-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a405-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a405-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a405-2767">'Razor'</span></span>
- <span data-ttu-id="8a405-2768">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a405-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="8a405-2769">-----------------: | | 0 | valorB | | 1 | valueC | | 2 | Valor |</span><span class="sxs-lookup"><span data-stu-id="8a405-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="8a405-2770">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="8a405-2770">Custom configuration provider</span></span>

<span data-ttu-id="8a405-2771">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="8a405-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8a405-2772">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="8a405-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8a405-2773">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="8a405-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8a405-2774">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a405-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8a405-2775">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="8a405-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8a405-2776">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="8a405-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8a405-2777">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8a405-2778">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="8a405-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8a405-2779">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a405-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8a405-2780">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="8a405-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8a405-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a405-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8a405-2782">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="8a405-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8a405-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a405-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8a405-2784">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="8a405-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8a405-2785">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="8a405-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="8a405-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a405-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8a405-2787">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8a405-2788">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a405-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8a405-2789">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a405-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="8a405-2790">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="8a405-2790">Access configuration during startup</span></span>

<span data-ttu-id="8a405-2791">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8a405-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8a405-2792">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="8a405-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="8a405-2793">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="8a405-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="8a405-2794">Acessar a configuração em uma Razor página de páginas ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="8a405-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="8a405-2795">Para acessar as definições de configuração em uma Razor página de páginas ou em uma exibição do MVC, adicione uma [diretiva using](xref:mvc/views/razor#using) ([referência C#: usando a diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespace Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) e insira <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="8a405-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="8a405-2796">Em uma Razor página de páginas:</span><span class="sxs-lookup"><span data-stu-id="8a405-2796">In a Razor Pages page:</span></span>

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

<span data-ttu-id="8a405-2797">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="8a405-2797">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8a405-2798">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="8a405-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="8a405-2799">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a405-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8a405-2800">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8a405-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a405-2801">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8a405-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

---
title: Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core
author: rick-anderson
description: Saiba como armazenar e recuperar informações confidenciais como segredos do aplicativo durante o desenvolvimento de um aplicativo ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: 7508aebcda4e14812140f13ece635428908a4abb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776676"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="330f1-103">Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="330f1-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="330f1-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="330f1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="330f1-105">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="330f1-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="330f1-106">Este documento explica técnicas para armazenar e recuperar dados confidenciais durante o desenvolvimento de um aplicativo de ASP.NET Core em um computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="330f1-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="330f1-107">Nunca armazene senhas ou outros dados confidenciais no código-fonte.</span><span class="sxs-lookup"><span data-stu-id="330f1-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="330f1-108">Os segredos de produção não devem ser usados para desenvolvimento ou teste.</span><span class="sxs-lookup"><span data-stu-id="330f1-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="330f1-109">Os segredos não devem ser implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="330f1-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="330f1-110">Em vez disso, os segredos devem ser disponibilizados no ambiente de produção por meio de um meio controlado, como variáveis de ambiente, Azure Key Vault, etc. Você pode armazenar e proteger os segredos de produção e de teste do Azure com o [provedor de configuração Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="330f1-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="330f1-111">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="330f1-111">Environment variables</span></span>

<span data-ttu-id="330f1-112">As variáveis de ambiente são usadas para evitar o armazenamento de segredos do aplicativo no código ou nos arquivos de configuração locais.</span><span class="sxs-lookup"><span data-stu-id="330f1-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="330f1-113">Variáveis de ambiente substituem valores de configuração para todas as fontes de configuração especificadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="330f1-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="330f1-114">Considere um aplicativo Web ASP.NET Core no qual a segurança **das contas de usuário individuais** está habilitada.</span><span class="sxs-lookup"><span data-stu-id="330f1-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="330f1-115">Uma cadeia de conexão de banco de dados padrão é incluída no arquivo *appSettings. JSON* do projeto `DefaultConnection`com a chave.</span><span class="sxs-lookup"><span data-stu-id="330f1-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="330f1-116">A cadeia de conexão padrão é para o LocalDB, que é executado no modo de usuário e não requer uma senha.</span><span class="sxs-lookup"><span data-stu-id="330f1-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="330f1-117">Durante a implantação do aplicativo `DefaultConnection` , o valor da chave pode ser substituído por um valor de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="330f1-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="330f1-118">A variável de ambiente pode armazenar a cadeia de conexão completa com credenciais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="330f1-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="330f1-119">Variáveis de ambiente geralmente são armazenadas em texto simples e não criptografado.</span><span class="sxs-lookup"><span data-stu-id="330f1-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="330f1-120">Se o computador ou o processo estiver comprometido, as variáveis de ambiente poderão ser acessadas por partes não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="330f1-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="330f1-121">Outras medidas para evitar a divulgação de segredos do usuário podem ser necessárias.</span><span class="sxs-lookup"><span data-stu-id="330f1-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="330f1-122">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="330f1-122">Secret Manager</span></span>

<span data-ttu-id="330f1-123">A ferramenta Gerenciador de segredo armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="330f1-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="330f1-124">Nesse contexto, um elemento de dados confidenciais é um segredo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="330f1-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="330f1-125">Os segredos do aplicativo são armazenados em um local separado da árvore do projeto.</span><span class="sxs-lookup"><span data-stu-id="330f1-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="330f1-126">Os segredos do aplicativo são associados a um projeto específico ou compartilhados entre vários projetos.</span><span class="sxs-lookup"><span data-stu-id="330f1-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="330f1-127">Os segredos do aplicativo não são verificados no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="330f1-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="330f1-128">A ferramenta Gerenciador de segredo não criptografa os segredos armazenados e não deve ser tratada como um repositório confiável.</span><span class="sxs-lookup"><span data-stu-id="330f1-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="330f1-129">É apenas para fins de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="330f1-129">It's for development purposes only.</span></span> <span data-ttu-id="330f1-130">As chaves e os valores são armazenados em um arquivo de configuração JSON no diretório de perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="330f1-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="330f1-131">Como funciona a ferramenta Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="330f1-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="330f1-132">A ferramenta Gerenciador de segredo abstrai os detalhes da implementação, como onde e como os valores são armazenados.</span><span class="sxs-lookup"><span data-stu-id="330f1-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="330f1-133">Você pode usar a ferramenta sem conhecer esses detalhes de implementação.</span><span class="sxs-lookup"><span data-stu-id="330f1-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="330f1-134">Os valores são armazenados em um arquivo de configuração JSON em uma pasta de perfil de usuário protegida pelo sistema no computador local:</span><span class="sxs-lookup"><span data-stu-id="330f1-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="330f1-135">Windows</span><span class="sxs-lookup"><span data-stu-id="330f1-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="330f1-136">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="330f1-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="330f1-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="330f1-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="330f1-138">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="330f1-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="330f1-139">Nos caminhos de arquivo anteriores, substitua `<user_secrets_id>` pelo `UserSecretsId` valor especificado no arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="330f1-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="330f1-140">Não escreva o código que depende do local ou do formato dos dados salvos com a ferramenta Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="330f1-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="330f1-141">Esses detalhes de implementação podem ser alterados.</span><span class="sxs-lookup"><span data-stu-id="330f1-141">These implementation details may change.</span></span> <span data-ttu-id="330f1-142">Por exemplo, os valores secretos não são criptografados, mas podem estar no futuro.</span><span class="sxs-lookup"><span data-stu-id="330f1-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="330f1-143">Habilitar o armazenamento secreto</span><span class="sxs-lookup"><span data-stu-id="330f1-143">Enable secret storage</span></span>

<span data-ttu-id="330f1-144">A ferramenta Gerenciador de segredo opera em definições de configuração específicas do projeto armazenadas no seu perfil de usuário.</span><span class="sxs-lookup"><span data-stu-id="330f1-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="330f1-145">A ferramenta Gerenciador de segredo inclui `init` um comando no SDK do .NET Core 3.0.100 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="330f1-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="330f1-146">Para usar os segredos do usuário, execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="330f1-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="330f1-147">O comando anterior adiciona um `UserSecretsId` elemento dentro de `PropertyGroup` um do arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="330f1-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="330f1-148">Por padrão, o texto interno de `UserSecretsId` é um GUID.</span><span class="sxs-lookup"><span data-stu-id="330f1-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="330f1-149">O texto interno é arbitrário, mas é exclusivo para o projeto.</span><span class="sxs-lookup"><span data-stu-id="330f1-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="330f1-150">No Visual Studio, clique com o botão direito do mouse no projeto em Gerenciador de Soluções e selecione **gerenciar segredos do usuário** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="330f1-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="330f1-151">Esse gesto adiciona um `UserSecretsId` elemento, populado com um GUID, ao arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="330f1-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="330f1-152">Definir um segredo</span><span class="sxs-lookup"><span data-stu-id="330f1-152">Set a secret</span></span>

<span data-ttu-id="330f1-153">Defina um segredo de aplicativo que consiste em uma chave e seu valor.</span><span class="sxs-lookup"><span data-stu-id="330f1-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="330f1-154">O segredo é associado ao valor do `UserSecretsId` projeto.</span><span class="sxs-lookup"><span data-stu-id="330f1-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="330f1-155">Por exemplo, execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="330f1-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="330f1-156">No exemplo anterior, os dois pontos indica que `Movies` é um literal de objeto com uma `ServiceApiKey` propriedade.</span><span class="sxs-lookup"><span data-stu-id="330f1-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="330f1-157">A ferramenta Gerenciador de segredo também pode ser usada em outros diretórios.</span><span class="sxs-lookup"><span data-stu-id="330f1-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="330f1-158">Use a `--project` opção para fornecer o caminho do sistema de arquivos no qual o arquivo *. csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="330f1-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="330f1-159">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="330f1-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="330f1-160">Nivelamento de estrutura JSON no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="330f1-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="330f1-161">O gesto **gerenciar segredos do usuário** do Visual Studio abre um arquivo *segredos. JSON* no editor de texto.</span><span class="sxs-lookup"><span data-stu-id="330f1-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="330f1-162">Substitua o conteúdo de *Secrets. JSON* pelos pares de chave-valor a serem armazenados.</span><span class="sxs-lookup"><span data-stu-id="330f1-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="330f1-163">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="330f1-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="330f1-164">A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="330f1-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="330f1-165">Por exemplo, a `dotnet user-secrets remove "Movies:ConnectionString"` execução recolhe o `Movies` literal do objeto.</span><span class="sxs-lookup"><span data-stu-id="330f1-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="330f1-166">O arquivo modificado é semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="330f1-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="330f1-167">Definir vários segredos</span><span class="sxs-lookup"><span data-stu-id="330f1-167">Set multiple secrets</span></span>

<span data-ttu-id="330f1-168">Um lote de segredos pode ser definido por meio de tubulação JSON `set` para o comando.</span><span class="sxs-lookup"><span data-stu-id="330f1-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="330f1-169">No exemplo a seguir, o conteúdo do arquivo *Input. JSON* é canalizado para o `set` comando.</span><span class="sxs-lookup"><span data-stu-id="330f1-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="330f1-170">Windows</span><span class="sxs-lookup"><span data-stu-id="330f1-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="330f1-171">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="330f1-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="330f1-172">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="330f1-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="330f1-173">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="330f1-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="330f1-174">Acessar um segredo</span><span class="sxs-lookup"><span data-stu-id="330f1-174">Access a secret</span></span>

<span data-ttu-id="330f1-175">A [API de configuração do ASP.NET Core](xref:fundamentals/configuration/index) fornece acesso aos segredos do Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="330f1-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="330f1-176">A fonte de configuração segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> o projeto chama para inicializar uma nova instância do host com padrões pré-configurados.</span><span class="sxs-lookup"><span data-stu-id="330f1-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="330f1-177">`CreateDefaultBuilder`chama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando o <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> é <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span><span class="sxs-lookup"><span data-stu-id="330f1-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="330f1-178">Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de configuração de segredos do <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>usuário explicitamente chamando.</span><span class="sxs-lookup"><span data-stu-id="330f1-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="330f1-179">Chame `AddUserSecrets` somente quando o aplicativo for executado no ambiente de desenvolvimento, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="330f1-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="330f1-180">Os segredos do usuário podem ser recuperados por meio da `Configuration` API:</span><span class="sxs-lookup"><span data-stu-id="330f1-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="330f1-181">Mapear segredos para um POCO</span><span class="sxs-lookup"><span data-stu-id="330f1-181">Map secrets to a POCO</span></span>

<span data-ttu-id="330f1-182">O mapeamento de um literal de objeto inteiro para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="330f1-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="330f1-183">Para mapear os segredos anteriores para um POCO, use `Configuration` o recurso de [Associação de grafo de objeto](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API.</span><span class="sxs-lookup"><span data-stu-id="330f1-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="330f1-184">O código a seguir é associado a um `MovieSettings` poco personalizado e acessa o `ServiceApiKey` valor da propriedade:</span><span class="sxs-lookup"><span data-stu-id="330f1-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="330f1-185">Os `Movies:ConnectionString` segredos `Movies:ServiceApiKey` e são mapeados para as respectivas propriedades `MovieSettings`no:</span><span class="sxs-lookup"><span data-stu-id="330f1-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="330f1-186">Substituição de cadeia de caracteres com segredos</span><span class="sxs-lookup"><span data-stu-id="330f1-186">String replacement with secrets</span></span>

<span data-ttu-id="330f1-187">O armazenamento de senhas em texto sem formatação não é seguro.</span><span class="sxs-lookup"><span data-stu-id="330f1-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="330f1-188">Por exemplo, uma cadeia de conexão de banco de dados armazenada em *appSettings. JSON* pode incluir uma senha para o usuário especificado:</span><span class="sxs-lookup"><span data-stu-id="330f1-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="330f1-189">Uma abordagem mais segura é armazenar a senha como um segredo.</span><span class="sxs-lookup"><span data-stu-id="330f1-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="330f1-190">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="330f1-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="330f1-191">Remova o `Password` par chave-valor da cadeia de conexão em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="330f1-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="330f1-192">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="330f1-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="330f1-193">O valor do segredo pode ser definido na <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> propriedade de um objeto para concluir a cadeia de conexão:</span><span class="sxs-lookup"><span data-stu-id="330f1-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="330f1-194">Listar os segredos</span><span class="sxs-lookup"><span data-stu-id="330f1-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="330f1-195">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="330f1-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="330f1-196">A saída a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="330f1-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="330f1-197">No exemplo anterior, dois-pontos nos nomes de chave denota a hierarquia de objeto dentro de *segredos. JSON*.</span><span class="sxs-lookup"><span data-stu-id="330f1-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="330f1-198">Remover um único segredo</span><span class="sxs-lookup"><span data-stu-id="330f1-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="330f1-199">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="330f1-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="330f1-200">O arquivo *segredos. JSON* do aplicativo foi modificado para remover o par chave-valor associado à `MoviesConnectionString` chave:</span><span class="sxs-lookup"><span data-stu-id="330f1-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="330f1-201">`dotnet user-secrets list`exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="330f1-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="330f1-202">Remover todos os segredos</span><span class="sxs-lookup"><span data-stu-id="330f1-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="330f1-203">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="330f1-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="330f1-204">Todos os segredos do usuário para o aplicativo foram excluídos do arquivo *segredos. JSON* :</span><span class="sxs-lookup"><span data-stu-id="330f1-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="330f1-205">A `dotnet user-secrets list` execução exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="330f1-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="330f1-206">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="330f1-206">Additional resources</span></span>

* <span data-ttu-id="330f1-207">Consulte [esse problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar o Gerenciador de segredo do IIS.</span><span class="sxs-lookup"><span data-stu-id="330f1-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="330f1-208">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="330f1-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="330f1-209">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="330f1-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="330f1-210">Este documento explica técnicas para armazenar e recuperar dados confidenciais durante o desenvolvimento de um aplicativo de ASP.NET Core em um computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="330f1-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="330f1-211">Nunca armazene senhas ou outros dados confidenciais no código-fonte.</span><span class="sxs-lookup"><span data-stu-id="330f1-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="330f1-212">Os segredos de produção não devem ser usados para desenvolvimento ou teste.</span><span class="sxs-lookup"><span data-stu-id="330f1-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="330f1-213">Os segredos não devem ser implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="330f1-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="330f1-214">Em vez disso, os segredos devem ser disponibilizados no ambiente de produção por meio de um meio controlado, como variáveis de ambiente, Azure Key Vault, etc. Você pode armazenar e proteger os segredos de produção e de teste do Azure com o [provedor de configuração Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="330f1-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="330f1-215">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="330f1-215">Environment variables</span></span>

<span data-ttu-id="330f1-216">As variáveis de ambiente são usadas para evitar o armazenamento de segredos do aplicativo no código ou nos arquivos de configuração locais.</span><span class="sxs-lookup"><span data-stu-id="330f1-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="330f1-217">Variáveis de ambiente substituem valores de configuração para todas as fontes de configuração especificadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="330f1-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="330f1-218">Considere um aplicativo Web ASP.NET Core no qual a segurança **das contas de usuário individuais** está habilitada.</span><span class="sxs-lookup"><span data-stu-id="330f1-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="330f1-219">Uma cadeia de conexão de banco de dados padrão é incluída no arquivo *appSettings. JSON* do projeto `DefaultConnection`com a chave.</span><span class="sxs-lookup"><span data-stu-id="330f1-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="330f1-220">A cadeia de conexão padrão é para o LocalDB, que é executado no modo de usuário e não requer uma senha.</span><span class="sxs-lookup"><span data-stu-id="330f1-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="330f1-221">Durante a implantação do aplicativo `DefaultConnection` , o valor da chave pode ser substituído por um valor de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="330f1-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="330f1-222">A variável de ambiente pode armazenar a cadeia de conexão completa com credenciais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="330f1-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="330f1-223">Variáveis de ambiente geralmente são armazenadas em texto simples e não criptografado.</span><span class="sxs-lookup"><span data-stu-id="330f1-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="330f1-224">Se o computador ou o processo estiver comprometido, as variáveis de ambiente poderão ser acessadas por partes não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="330f1-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="330f1-225">Outras medidas para evitar a divulgação de segredos do usuário podem ser necessárias.</span><span class="sxs-lookup"><span data-stu-id="330f1-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="330f1-226">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="330f1-226">Secret Manager</span></span>

<span data-ttu-id="330f1-227">A ferramenta Gerenciador de segredo armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="330f1-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="330f1-228">Nesse contexto, um elemento de dados confidenciais é um segredo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="330f1-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="330f1-229">Os segredos do aplicativo são armazenados em um local separado da árvore do projeto.</span><span class="sxs-lookup"><span data-stu-id="330f1-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="330f1-230">Os segredos do aplicativo são associados a um projeto específico ou compartilhados entre vários projetos.</span><span class="sxs-lookup"><span data-stu-id="330f1-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="330f1-231">Os segredos do aplicativo não são verificados no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="330f1-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="330f1-232">A ferramenta Gerenciador de segredo não criptografa os segredos armazenados e não deve ser tratada como um repositório confiável.</span><span class="sxs-lookup"><span data-stu-id="330f1-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="330f1-233">É apenas para fins de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="330f1-233">It's for development purposes only.</span></span> <span data-ttu-id="330f1-234">As chaves e os valores são armazenados em um arquivo de configuração JSON no diretório de perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="330f1-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="330f1-235">Como funciona a ferramenta Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="330f1-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="330f1-236">A ferramenta Gerenciador de segredo abstrai os detalhes da implementação, como onde e como os valores são armazenados.</span><span class="sxs-lookup"><span data-stu-id="330f1-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="330f1-237">Você pode usar a ferramenta sem conhecer esses detalhes de implementação.</span><span class="sxs-lookup"><span data-stu-id="330f1-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="330f1-238">Os valores são armazenados em um arquivo de configuração JSON em uma pasta de perfil de usuário protegida pelo sistema no computador local:</span><span class="sxs-lookup"><span data-stu-id="330f1-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="330f1-239">Windows</span><span class="sxs-lookup"><span data-stu-id="330f1-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="330f1-240">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="330f1-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="330f1-241">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="330f1-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="330f1-242">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="330f1-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="330f1-243">Nos caminhos de arquivo anteriores, substitua `<user_secrets_id>` pelo `UserSecretsId` valor especificado no arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="330f1-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="330f1-244">Não escreva o código que depende do local ou do formato dos dados salvos com a ferramenta Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="330f1-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="330f1-245">Esses detalhes de implementação podem ser alterados.</span><span class="sxs-lookup"><span data-stu-id="330f1-245">These implementation details may change.</span></span> <span data-ttu-id="330f1-246">Por exemplo, os valores secretos não são criptografados, mas podem estar no futuro.</span><span class="sxs-lookup"><span data-stu-id="330f1-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="330f1-247">Habilitar o armazenamento secreto</span><span class="sxs-lookup"><span data-stu-id="330f1-247">Enable secret storage</span></span>

<span data-ttu-id="330f1-248">A ferramenta Gerenciador de segredo opera em definições de configuração específicas do projeto armazenadas no seu perfil de usuário.</span><span class="sxs-lookup"><span data-stu-id="330f1-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="330f1-249">Para usar os segredos do usuário, `UserSecretsId` defina um elemento `PropertyGroup` dentro de um do arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="330f1-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="330f1-250">O texto interno de `UserSecretsId` é arbitrário, mas é exclusivo para o projeto.</span><span class="sxs-lookup"><span data-stu-id="330f1-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="330f1-251">Os desenvolvedores normalmente geram um GUID `UserSecretsId`para o.</span><span class="sxs-lookup"><span data-stu-id="330f1-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="330f1-252">No Visual Studio, clique com o botão direito do mouse no projeto em Gerenciador de Soluções e selecione **gerenciar segredos do usuário** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="330f1-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="330f1-253">Esse gesto adiciona um `UserSecretsId` elemento, populado com um GUID, ao arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="330f1-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="330f1-254">Definir um segredo</span><span class="sxs-lookup"><span data-stu-id="330f1-254">Set a secret</span></span>

<span data-ttu-id="330f1-255">Defina um segredo de aplicativo que consiste em uma chave e seu valor.</span><span class="sxs-lookup"><span data-stu-id="330f1-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="330f1-256">O segredo é associado ao valor do `UserSecretsId` projeto.</span><span class="sxs-lookup"><span data-stu-id="330f1-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="330f1-257">Por exemplo, execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="330f1-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="330f1-258">No exemplo anterior, os dois pontos indica que `Movies` é um literal de objeto com uma `ServiceApiKey` propriedade.</span><span class="sxs-lookup"><span data-stu-id="330f1-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="330f1-259">A ferramenta Gerenciador de segredo também pode ser usada em outros diretórios.</span><span class="sxs-lookup"><span data-stu-id="330f1-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="330f1-260">Use a `--project` opção para fornecer o caminho do sistema de arquivos no qual o arquivo *. csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="330f1-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="330f1-261">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="330f1-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="330f1-262">Nivelamento de estrutura JSON no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="330f1-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="330f1-263">O gesto **gerenciar segredos do usuário** do Visual Studio abre um arquivo *segredos. JSON* no editor de texto.</span><span class="sxs-lookup"><span data-stu-id="330f1-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="330f1-264">Substitua o conteúdo de *Secrets. JSON* pelos pares de chave-valor a serem armazenados.</span><span class="sxs-lookup"><span data-stu-id="330f1-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="330f1-265">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="330f1-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="330f1-266">A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="330f1-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="330f1-267">Por exemplo, a `dotnet user-secrets remove "Movies:ConnectionString"` execução recolhe o `Movies` literal do objeto.</span><span class="sxs-lookup"><span data-stu-id="330f1-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="330f1-268">O arquivo modificado é semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="330f1-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="330f1-269">Definir vários segredos</span><span class="sxs-lookup"><span data-stu-id="330f1-269">Set multiple secrets</span></span>

<span data-ttu-id="330f1-270">Um lote de segredos pode ser definido por meio de tubulação JSON `set` para o comando.</span><span class="sxs-lookup"><span data-stu-id="330f1-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="330f1-271">No exemplo a seguir, o conteúdo do arquivo *Input. JSON* é canalizado para o `set` comando.</span><span class="sxs-lookup"><span data-stu-id="330f1-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="330f1-272">Windows</span><span class="sxs-lookup"><span data-stu-id="330f1-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="330f1-273">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="330f1-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="330f1-274">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="330f1-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="330f1-275">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="330f1-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="330f1-276">Acessar um segredo</span><span class="sxs-lookup"><span data-stu-id="330f1-276">Access a secret</span></span>

<span data-ttu-id="330f1-277">A [API de configuração do ASP.NET Core](xref:fundamentals/configuration/index) fornece acesso aos segredos do Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="330f1-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="330f1-278">Se seu projeto tiver como destino .NET Framework, instale o pacote NuGet [Microsoft. Extensions. Configuration. Usersecretos](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .</span><span class="sxs-lookup"><span data-stu-id="330f1-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="330f1-279">No ASP.NET Core 2,0 ou posterior, a fonte de configuração de segredos do usuário é adicionada automaticamente no modo de desenvolvimento <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> quando o projeto chama para inicializar uma nova instância do host com padrões pré-configurados.</span><span class="sxs-lookup"><span data-stu-id="330f1-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="330f1-280">`CreateDefaultBuilder`chama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando o <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> é <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span><span class="sxs-lookup"><span data-stu-id="330f1-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="330f1-281">Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de configuração de segredos do <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> usuário explicitamente `Startup` chamando no construtor.</span><span class="sxs-lookup"><span data-stu-id="330f1-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="330f1-282">Chame `AddUserSecrets` somente quando o aplicativo for executado no ambiente de desenvolvimento, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="330f1-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="330f1-283">Os segredos do usuário podem ser recuperados por meio da `Configuration` API:</span><span class="sxs-lookup"><span data-stu-id="330f1-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="330f1-284">Mapear segredos para um POCO</span><span class="sxs-lookup"><span data-stu-id="330f1-284">Map secrets to a POCO</span></span>

<span data-ttu-id="330f1-285">O mapeamento de um literal de objeto inteiro para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="330f1-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="330f1-286">Para mapear os segredos anteriores para um POCO, use `Configuration` o recurso de [Associação de grafo de objeto](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API.</span><span class="sxs-lookup"><span data-stu-id="330f1-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="330f1-287">O código a seguir é associado a um `MovieSettings` poco personalizado e acessa o `ServiceApiKey` valor da propriedade:</span><span class="sxs-lookup"><span data-stu-id="330f1-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="330f1-288">Os `Movies:ConnectionString` segredos `Movies:ServiceApiKey` e são mapeados para as respectivas propriedades `MovieSettings`no:</span><span class="sxs-lookup"><span data-stu-id="330f1-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="330f1-289">Substituição de cadeia de caracteres com segredos</span><span class="sxs-lookup"><span data-stu-id="330f1-289">String replacement with secrets</span></span>

<span data-ttu-id="330f1-290">O armazenamento de senhas em texto sem formatação não é seguro.</span><span class="sxs-lookup"><span data-stu-id="330f1-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="330f1-291">Por exemplo, uma cadeia de conexão de banco de dados armazenada em *appSettings. JSON* pode incluir uma senha para o usuário especificado:</span><span class="sxs-lookup"><span data-stu-id="330f1-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="330f1-292">Uma abordagem mais segura é armazenar a senha como um segredo.</span><span class="sxs-lookup"><span data-stu-id="330f1-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="330f1-293">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="330f1-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="330f1-294">Remova o `Password` par chave-valor da cadeia de conexão em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="330f1-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="330f1-295">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="330f1-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="330f1-296">O valor do segredo pode ser definido na <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> propriedade de um objeto para concluir a cadeia de conexão:</span><span class="sxs-lookup"><span data-stu-id="330f1-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="330f1-297">Listar os segredos</span><span class="sxs-lookup"><span data-stu-id="330f1-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="330f1-298">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="330f1-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="330f1-299">A saída a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="330f1-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="330f1-300">No exemplo anterior, dois-pontos nos nomes de chave denota a hierarquia de objeto dentro de *segredos. JSON*.</span><span class="sxs-lookup"><span data-stu-id="330f1-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="330f1-301">Remover um único segredo</span><span class="sxs-lookup"><span data-stu-id="330f1-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="330f1-302">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="330f1-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="330f1-303">O arquivo *segredos. JSON* do aplicativo foi modificado para remover o par chave-valor associado à `MoviesConnectionString` chave:</span><span class="sxs-lookup"><span data-stu-id="330f1-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="330f1-304">A `dotnet user-secrets list` execução exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="330f1-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="330f1-305">Remover todos os segredos</span><span class="sxs-lookup"><span data-stu-id="330f1-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="330f1-306">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="330f1-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="330f1-307">Todos os segredos do usuário para o aplicativo foram excluídos do arquivo *segredos. JSON* :</span><span class="sxs-lookup"><span data-stu-id="330f1-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="330f1-308">A `dotnet user-secrets list` execução exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="330f1-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="330f1-309">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="330f1-309">Additional resources</span></span>

* <span data-ttu-id="330f1-310">Consulte [esse problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar o Gerenciador de segredo do IIS.</span><span class="sxs-lookup"><span data-stu-id="330f1-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end
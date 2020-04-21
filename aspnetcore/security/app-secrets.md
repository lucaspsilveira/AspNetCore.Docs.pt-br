---
title: Armazenamento seguro de segredos de aplicativos em desenvolvimento em ASP.NET Core
author: rick-anderson
description: Aprenda a armazenar e recuperar informações confidenciais como segredos de aplicativos durante o desenvolvimento de um aplicativo ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: 9d4e59c003afc253971ee64fce523c7188d3582a
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661797"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="5261b-103">Armazenamento seguro de segredos de aplicativos em desenvolvimento em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5261b-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5261b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), Kirk [Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), e Scott [Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="5261b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="5261b-105">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5261b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5261b-106">Este documento explica técnicas para armazenar e recuperar dados confidenciais durante o desenvolvimento de um aplicativo ASP.NET Core em uma máquina de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5261b-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="5261b-107">Nunca armazene senhas ou outros dados confidenciais no código-fonte.</span><span class="sxs-lookup"><span data-stu-id="5261b-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="5261b-108">Segredos de produção não devem ser usados para desenvolvimento ou teste.</span><span class="sxs-lookup"><span data-stu-id="5261b-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="5261b-109">Segredos não devem ser implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5261b-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="5261b-110">Em vez disso, segredos devem ser disponibilizados no ambiente de produção através de meios controlados, como variáveis de ambiente, Azure Key Vault, etc. Você pode armazenar e proteger segredos de teste e produção do Azure com o [provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="5261b-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="5261b-111">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="5261b-111">Environment variables</span></span>

<span data-ttu-id="5261b-112">As variáveis de ambiente são usadas para evitar o armazenamento de segredos de aplicativos em código ou em arquivos de configuração locais.</span><span class="sxs-lookup"><span data-stu-id="5261b-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="5261b-113">As variáveis de ambiente anulam os valores de configuração para todas as fontes de configuração especificadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="5261b-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="5261b-114">Considere um aplicativo web ASP.NET Core no qual a segurança **de Contas de Usuário Individuais** está ativada.</span><span class="sxs-lookup"><span data-stu-id="5261b-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="5261b-115">Uma seqüência de conexão de banco de dados padrão é `DefaultConnection`incluída no arquivo *appsettings.json* do projeto com a chave .</span><span class="sxs-lookup"><span data-stu-id="5261b-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="5261b-116">A seqüência de conexão padrão é para LocalDB, que é executado no modo de usuário e não requer uma senha.</span><span class="sxs-lookup"><span data-stu-id="5261b-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="5261b-117">Durante a implantação `DefaultConnection` do aplicativo, o valor-chave pode ser substituído com o valor de uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5261b-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="5261b-118">A variável ambiente pode armazenar a seqüência de conexão completa com credenciais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="5261b-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="5261b-119">As variáveis de ambiente são geralmente armazenadas em texto simples e não criptografado.</span><span class="sxs-lookup"><span data-stu-id="5261b-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="5261b-120">Se a máquina ou o processo estiver comprometido, as variáveis de ambiente podem ser acessadas por partes não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="5261b-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="5261b-121">Medidas adicionais para evitar a divulgação de segredos do usuário podem ser necessárias.</span><span class="sxs-lookup"><span data-stu-id="5261b-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="5261b-122">Gerente Secreto</span><span class="sxs-lookup"><span data-stu-id="5261b-122">Secret Manager</span></span>

<span data-ttu-id="5261b-123">A ferramenta Secret Manager armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5261b-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="5261b-124">Neste contexto, um pedaço de dados confidenciais é um segredo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5261b-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="5261b-125">Os segredos do aplicativo são armazenados em um local separado da árvore do projeto.</span><span class="sxs-lookup"><span data-stu-id="5261b-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="5261b-126">Os segredos do aplicativo estão associados a um projeto específico ou compartilhados em vários projetos.</span><span class="sxs-lookup"><span data-stu-id="5261b-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="5261b-127">Os segredos do aplicativo não são verificados no controle de origem.</span><span class="sxs-lookup"><span data-stu-id="5261b-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="5261b-128">A ferramenta Secret Manager não criptografa os segredos armazenados e não deve ser tratada como uma loja confiável.</span><span class="sxs-lookup"><span data-stu-id="5261b-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="5261b-129">É só para fins de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5261b-129">It's for development purposes only.</span></span> <span data-ttu-id="5261b-130">As chaves e valores são armazenados em um arquivo de configuração JSON no diretório do perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="5261b-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="5261b-131">Como funciona a ferramenta Secret Manager</span><span class="sxs-lookup"><span data-stu-id="5261b-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="5261b-132">A ferramenta Secret Manager abstrai os detalhes da implementação, como onde e como os valores são armazenados.</span><span class="sxs-lookup"><span data-stu-id="5261b-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="5261b-133">Você pode usar a ferramenta sem saber esses detalhes de implementação.</span><span class="sxs-lookup"><span data-stu-id="5261b-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="5261b-134">Os valores são armazenados em um arquivo de configuração JSON em uma pasta de perfil de usuário protegida pelo sistema na máquina local:</span><span class="sxs-lookup"><span data-stu-id="5261b-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="5261b-135">Windows</span><span class="sxs-lookup"><span data-stu-id="5261b-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="5261b-136">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="5261b-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="5261b-137">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5261b-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5261b-138">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="5261b-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="5261b-139">Nos caminhos de `<user_secrets_id>` arquivo `UserSecretsId` anteriores, substitua-o pelo valor especificado no arquivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5261b-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="5261b-140">Não escreva código que dependa da localização ou formato dos dados salvos com a ferramenta Gerenciador Secreto.</span><span class="sxs-lookup"><span data-stu-id="5261b-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="5261b-141">Esses detalhes de implementação podem mudar.</span><span class="sxs-lookup"><span data-stu-id="5261b-141">These implementation details may change.</span></span> <span data-ttu-id="5261b-142">Por exemplo, os valores secretos não são criptografados, mas podem ser no futuro.</span><span class="sxs-lookup"><span data-stu-id="5261b-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="5261b-143">Habilite o armazenamento secreto</span><span class="sxs-lookup"><span data-stu-id="5261b-143">Enable secret storage</span></span>

<span data-ttu-id="5261b-144">A ferramenta Secret Manager opera em configurações específicas do projeto armazenadas no perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="5261b-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="5261b-145">A ferramenta Secret Manager `init` inclui um comando no .NET Core SDK 3.0.100 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="5261b-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="5261b-146">Para usar os segredos do usuário, execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="5261b-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="5261b-147">O comando anterior `UserSecretsId` adiciona `PropertyGroup` um elemento dentro de um arquivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5261b-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="5261b-148">Por padrão, o `UserSecretsId` texto interno de é um GUID.</span><span class="sxs-lookup"><span data-stu-id="5261b-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="5261b-149">O texto interno é arbitrário, mas é único no projeto.</span><span class="sxs-lookup"><span data-stu-id="5261b-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="5261b-150">No Visual Studio, clique com o botão direito do mouse no projeto no Solution Explorer e selecione **Gerenciar segredos** de usuário no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="5261b-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="5261b-151">Este gesto `UserSecretsId` adiciona um elemento, preenchido com um GUID, ao arquivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5261b-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="5261b-152">Estabeleça um segredo</span><span class="sxs-lookup"><span data-stu-id="5261b-152">Set a secret</span></span>

<span data-ttu-id="5261b-153">Defina um segredo de aplicativo que consiste em uma chave e seu valor.</span><span class="sxs-lookup"><span data-stu-id="5261b-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="5261b-154">O segredo está associado ao `UserSecretsId` valor do projeto.</span><span class="sxs-lookup"><span data-stu-id="5261b-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="5261b-155">Por exemplo, execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="5261b-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="5261b-156">No exemplo anterior, o cólon denota `Movies` `ServiceApiKey` que é um objeto literal com uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="5261b-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="5261b-157">A ferramenta Secret Manager também pode ser usada em outros diretórios.</span><span class="sxs-lookup"><span data-stu-id="5261b-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="5261b-158">Use `--project` a opção para fornecer o caminho do sistema de arquivos no qual o arquivo *.csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="5261b-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="5261b-159">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5261b-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="5261b-160">Achatamento da estrutura JSON no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5261b-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="5261b-161">O gesto **Gerenciar segredos de usuário** do Visual Studio abre um arquivo *secrets.json* no editor de texto.</span><span class="sxs-lookup"><span data-stu-id="5261b-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="5261b-162">Substitua o conteúdo do *secrets.json* pelos pares de valor-chave a serem armazenados.</span><span class="sxs-lookup"><span data-stu-id="5261b-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="5261b-163">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5261b-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5261b-164">A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="5261b-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="5261b-165">Por exemplo, `dotnet user-secrets remove "Movies:ConnectionString"` correr `Movies` colapsa o objeto literal.</span><span class="sxs-lookup"><span data-stu-id="5261b-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="5261b-166">O arquivo modificado se assemelha ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="5261b-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="5261b-167">Defina vários segredos</span><span class="sxs-lookup"><span data-stu-id="5261b-167">Set multiple secrets</span></span>

<span data-ttu-id="5261b-168">Um lote de segredos pode ser definido pela `set` tubulação JSON para o comando.</span><span class="sxs-lookup"><span data-stu-id="5261b-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="5261b-169">No exemplo a seguir, o conteúdo do arquivo *input.json* é canalizado para o `set` comando.</span><span class="sxs-lookup"><span data-stu-id="5261b-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="5261b-170">Windows</span><span class="sxs-lookup"><span data-stu-id="5261b-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="5261b-171">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="5261b-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="5261b-172">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5261b-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5261b-173">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="5261b-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="5261b-174">Acesse um segredo</span><span class="sxs-lookup"><span data-stu-id="5261b-174">Access a secret</span></span>

<span data-ttu-id="5261b-175">A [API de configuração principal ASP.NET](xref:fundamentals/configuration/index) fornece acesso a segredos do Gerenciador Secreto.</span><span class="sxs-lookup"><span data-stu-id="5261b-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="5261b-176">Em ASP.NET Core 2.0 ou posterior, a fonte de configuração de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando o projeto chama para inicializar uma nova instância do host com padrões pré-configurados.</span><span class="sxs-lookup"><span data-stu-id="5261b-176">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="5261b-177">`CreateDefaultBuilder`chamadas <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>o é:</span><span class="sxs-lookup"><span data-stu-id="5261b-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="5261b-178">Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> configuração de segredos do usuário explicitamente chamando o `Startup` construtor.</span><span class="sxs-lookup"><span data-stu-id="5261b-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="5261b-179">Ligue `AddUserSecrets` somente quando o aplicativo for executado no ambiente Desenvolvimento, como mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5261b-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="5261b-180">Os segredos do usuário `Configuration` podem ser recuperados através da API:</span><span class="sxs-lookup"><span data-stu-id="5261b-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]


## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="5261b-181">Mapear segredos para um POCO</span><span class="sxs-lookup"><span data-stu-id="5261b-181">Map secrets to a POCO</span></span>

<span data-ttu-id="5261b-182">Mapear um objeto inteiro literal para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="5261b-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5261b-183">Para mapear os segredos anteriores a `Configuration` um POCO, use o recurso de vinculação de gráficos de [objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API.</span><span class="sxs-lookup"><span data-stu-id="5261b-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="5261b-184">O código a seguir `MovieSettings` se liga a `ServiceApiKey` um POCO personalizado e acessa o valor da propriedade:</span><span class="sxs-lookup"><span data-stu-id="5261b-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="5261b-185">Os `Movies:ConnectionString` `Movies:ServiceApiKey` segredos e segredos são mapeados para as respectivas propriedades em `MovieSettings`:</span><span class="sxs-lookup"><span data-stu-id="5261b-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="5261b-186">Substituição de cordas com segredos</span><span class="sxs-lookup"><span data-stu-id="5261b-186">String replacement with secrets</span></span>

<span data-ttu-id="5261b-187">Armazenar senhas em texto simples é inseguro.</span><span class="sxs-lookup"><span data-stu-id="5261b-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="5261b-188">Por exemplo, uma seqüência de conexão de banco de dados armazenada em *appsettings.json* pode incluir uma senha para o usuário especificado:</span><span class="sxs-lookup"><span data-stu-id="5261b-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="5261b-189">Uma abordagem mais segura é armazenar a senha como um segredo.</span><span class="sxs-lookup"><span data-stu-id="5261b-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="5261b-190">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5261b-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="5261b-191">Remova `Password` o par de valor-chave da seqüência de conexões em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5261b-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="5261b-192">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5261b-192">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="5261b-193">O valor do segredo pode <xref:System.Data.SqlClient.SqlConnectionStringBuilder> ser definido <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> na propriedade de um objeto para completar a seqüência de conexões:</span><span class="sxs-lookup"><span data-stu-id="5261b-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="5261b-194">Liste os segredos</span><span class="sxs-lookup"><span data-stu-id="5261b-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5261b-195">Execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="5261b-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="5261b-196">A saída a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="5261b-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="5261b-197">No exemplo anterior, um cólon nos nomes-chave denota a hierarquia do objeto dentro *de secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="5261b-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="5261b-198">Remova um único segredo</span><span class="sxs-lookup"><span data-stu-id="5261b-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5261b-199">Execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="5261b-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="5261b-200">O arquivo *secrets.json* do aplicativo foi modificado para remover `MoviesConnectionString` o par de valor-chave associado à chave:</span><span class="sxs-lookup"><span data-stu-id="5261b-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5261b-201">`dotnet user-secrets list`exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="5261b-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="5261b-202">Remova todos os segredos</span><span class="sxs-lookup"><span data-stu-id="5261b-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5261b-203">Execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="5261b-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="5261b-204">Todos os segredos do usuário para o aplicativo foram excluídos do arquivo *secrets.json:*</span><span class="sxs-lookup"><span data-stu-id="5261b-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="5261b-205">A `dotnet user-secrets list` execução exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="5261b-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="5261b-206">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5261b-206">Additional resources</span></span>

* <span data-ttu-id="5261b-207">Consulte [esta edição](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar o Gerente Secreto do IIS.</span><span class="sxs-lookup"><span data-stu-id="5261b-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5261b-208">Por [Rick Anderson](https://twitter.com/RickAndMSFT), Daniel [Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="5261b-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="5261b-209">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5261b-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5261b-210">Este documento explica técnicas para armazenar e recuperar dados confidenciais durante o desenvolvimento de um aplicativo ASP.NET Core em uma máquina de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5261b-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="5261b-211">Nunca armazene senhas ou outros dados confidenciais no código-fonte.</span><span class="sxs-lookup"><span data-stu-id="5261b-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="5261b-212">Segredos de produção não devem ser usados para desenvolvimento ou teste.</span><span class="sxs-lookup"><span data-stu-id="5261b-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="5261b-213">Segredos não devem ser implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5261b-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="5261b-214">Em vez disso, segredos devem ser disponibilizados no ambiente de produção através de meios controlados, como variáveis de ambiente, Azure Key Vault, etc. Você pode armazenar e proteger segredos de teste e produção do Azure com o [provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="5261b-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="5261b-215">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="5261b-215">Environment variables</span></span>

<span data-ttu-id="5261b-216">As variáveis de ambiente são usadas para evitar o armazenamento de segredos de aplicativos em código ou em arquivos de configuração locais.</span><span class="sxs-lookup"><span data-stu-id="5261b-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="5261b-217">As variáveis de ambiente anulam os valores de configuração para todas as fontes de configuração especificadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="5261b-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="5261b-218">Considere um aplicativo web ASP.NET Core no qual a segurança **de Contas de Usuário Individuais** está ativada.</span><span class="sxs-lookup"><span data-stu-id="5261b-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="5261b-219">Uma seqüência de conexão de banco de dados padrão é `DefaultConnection`incluída no arquivo *appsettings.json* do projeto com a chave .</span><span class="sxs-lookup"><span data-stu-id="5261b-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="5261b-220">A seqüência de conexão padrão é para LocalDB, que é executado no modo de usuário e não requer uma senha.</span><span class="sxs-lookup"><span data-stu-id="5261b-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="5261b-221">Durante a implantação `DefaultConnection` do aplicativo, o valor-chave pode ser substituído com o valor de uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="5261b-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="5261b-222">A variável ambiente pode armazenar a seqüência de conexão completa com credenciais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="5261b-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="5261b-223">As variáveis de ambiente são geralmente armazenadas em texto simples e não criptografado.</span><span class="sxs-lookup"><span data-stu-id="5261b-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="5261b-224">Se a máquina ou o processo estiver comprometido, as variáveis de ambiente podem ser acessadas por partes não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="5261b-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="5261b-225">Medidas adicionais para evitar a divulgação de segredos do usuário podem ser necessárias.</span><span class="sxs-lookup"><span data-stu-id="5261b-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="5261b-226">Gerente Secreto</span><span class="sxs-lookup"><span data-stu-id="5261b-226">Secret Manager</span></span>

<span data-ttu-id="5261b-227">A ferramenta Secret Manager armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5261b-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="5261b-228">Neste contexto, um pedaço de dados confidenciais é um segredo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5261b-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="5261b-229">Os segredos do aplicativo são armazenados em um local separado da árvore do projeto.</span><span class="sxs-lookup"><span data-stu-id="5261b-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="5261b-230">Os segredos do aplicativo estão associados a um projeto específico ou compartilhados em vários projetos.</span><span class="sxs-lookup"><span data-stu-id="5261b-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="5261b-231">Os segredos do aplicativo não são verificados no controle de origem.</span><span class="sxs-lookup"><span data-stu-id="5261b-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="5261b-232">A ferramenta Secret Manager não criptografa os segredos armazenados e não deve ser tratada como uma loja confiável.</span><span class="sxs-lookup"><span data-stu-id="5261b-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="5261b-233">É só para fins de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5261b-233">It's for development purposes only.</span></span> <span data-ttu-id="5261b-234">As chaves e valores são armazenados em um arquivo de configuração JSON no diretório do perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="5261b-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="5261b-235">Como funciona a ferramenta Secret Manager</span><span class="sxs-lookup"><span data-stu-id="5261b-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="5261b-236">A ferramenta Secret Manager abstrai os detalhes da implementação, como onde e como os valores são armazenados.</span><span class="sxs-lookup"><span data-stu-id="5261b-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="5261b-237">Você pode usar a ferramenta sem saber esses detalhes de implementação.</span><span class="sxs-lookup"><span data-stu-id="5261b-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="5261b-238">Os valores são armazenados em um arquivo de configuração JSON em uma pasta de perfil de usuário protegida pelo sistema na máquina local:</span><span class="sxs-lookup"><span data-stu-id="5261b-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="5261b-239">Windows</span><span class="sxs-lookup"><span data-stu-id="5261b-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="5261b-240">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="5261b-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="5261b-241">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5261b-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5261b-242">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="5261b-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="5261b-243">Nos caminhos de `<user_secrets_id>` arquivo `UserSecretsId` anteriores, substitua-o pelo valor especificado no arquivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5261b-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="5261b-244">Não escreva código que dependa da localização ou formato dos dados salvos com a ferramenta Gerenciador Secreto.</span><span class="sxs-lookup"><span data-stu-id="5261b-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="5261b-245">Esses detalhes de implementação podem mudar.</span><span class="sxs-lookup"><span data-stu-id="5261b-245">These implementation details may change.</span></span> <span data-ttu-id="5261b-246">Por exemplo, os valores secretos não são criptografados, mas podem ser no futuro.</span><span class="sxs-lookup"><span data-stu-id="5261b-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="5261b-247">Habilite o armazenamento secreto</span><span class="sxs-lookup"><span data-stu-id="5261b-247">Enable secret storage</span></span>

<span data-ttu-id="5261b-248">A ferramenta Secret Manager opera em configurações específicas do projeto armazenadas no perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="5261b-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="5261b-249">Para usar segredos de `UserSecretsId` usuário, `PropertyGroup` defina um elemento dentro de um arquivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5261b-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="5261b-250">O texto `UserSecretsId` interno de é arbitrário, mas é único para o projeto.</span><span class="sxs-lookup"><span data-stu-id="5261b-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="5261b-251">Os desenvolvedores normalmente geram `UserSecretsId`um GUID para o .</span><span class="sxs-lookup"><span data-stu-id="5261b-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="5261b-252">No Visual Studio, clique com o botão direito do mouse no projeto no Solution Explorer e selecione **Gerenciar segredos** de usuário no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="5261b-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="5261b-253">Este gesto `UserSecretsId` adiciona um elemento, preenchido com um GUID, ao arquivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5261b-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="5261b-254">Estabeleça um segredo</span><span class="sxs-lookup"><span data-stu-id="5261b-254">Set a secret</span></span>

<span data-ttu-id="5261b-255">Defina um segredo de aplicativo que consiste em uma chave e seu valor.</span><span class="sxs-lookup"><span data-stu-id="5261b-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="5261b-256">O segredo está associado ao `UserSecretsId` valor do projeto.</span><span class="sxs-lookup"><span data-stu-id="5261b-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="5261b-257">Por exemplo, execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="5261b-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="5261b-258">No exemplo anterior, o cólon denota `Movies` `ServiceApiKey` que é um objeto literal com uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="5261b-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="5261b-259">A ferramenta Secret Manager também pode ser usada em outros diretórios.</span><span class="sxs-lookup"><span data-stu-id="5261b-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="5261b-260">Use `--project` a opção para fornecer o caminho do sistema de arquivos no qual o arquivo *.csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="5261b-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="5261b-261">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5261b-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="5261b-262">Achatamento da estrutura JSON no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5261b-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="5261b-263">O gesto **Gerenciar segredos de usuário** do Visual Studio abre um arquivo *secrets.json* no editor de texto.</span><span class="sxs-lookup"><span data-stu-id="5261b-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="5261b-264">Substitua o conteúdo do *secrets.json* pelos pares de valor-chave a serem armazenados.</span><span class="sxs-lookup"><span data-stu-id="5261b-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="5261b-265">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5261b-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5261b-266">A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="5261b-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="5261b-267">Por exemplo, `dotnet user-secrets remove "Movies:ConnectionString"` correr `Movies` colapsa o objeto literal.</span><span class="sxs-lookup"><span data-stu-id="5261b-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="5261b-268">O arquivo modificado se assemelha ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="5261b-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="5261b-269">Defina vários segredos</span><span class="sxs-lookup"><span data-stu-id="5261b-269">Set multiple secrets</span></span>

<span data-ttu-id="5261b-270">Um lote de segredos pode ser definido pela `set` tubulação JSON para o comando.</span><span class="sxs-lookup"><span data-stu-id="5261b-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="5261b-271">No exemplo a seguir, o conteúdo do arquivo *input.json* é canalizado para o `set` comando.</span><span class="sxs-lookup"><span data-stu-id="5261b-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="5261b-272">Windows</span><span class="sxs-lookup"><span data-stu-id="5261b-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="5261b-273">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="5261b-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="5261b-274">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5261b-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5261b-275">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="5261b-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="5261b-276">Acesse um segredo</span><span class="sxs-lookup"><span data-stu-id="5261b-276">Access a secret</span></span>

<span data-ttu-id="5261b-277">A [API de configuração principal ASP.NET](xref:fundamentals/configuration/index) fornece acesso a segredos do Gerenciador Secreto.</span><span class="sxs-lookup"><span data-stu-id="5261b-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="5261b-278">Se o projeto tiver como alvo o .NET Framework, instale o pacote [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.</span><span class="sxs-lookup"><span data-stu-id="5261b-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>


<span data-ttu-id="5261b-279">Em ASP.NET Core 2.0 ou posterior, a fonte de configuração de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando o projeto chama para inicializar uma nova instância do host com padrões pré-configurados.</span><span class="sxs-lookup"><span data-stu-id="5261b-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="5261b-280">`CreateDefaultBuilder`chamadas <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>o é:</span><span class="sxs-lookup"><span data-stu-id="5261b-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]


<span data-ttu-id="5261b-281">Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> configuração de segredos do usuário explicitamente chamando o `Startup` construtor.</span><span class="sxs-lookup"><span data-stu-id="5261b-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="5261b-282">Ligue `AddUserSecrets` somente quando o aplicativo for executado no ambiente Desenvolvimento, como mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5261b-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="5261b-283">Os segredos do usuário `Configuration` podem ser recuperados através da API:</span><span class="sxs-lookup"><span data-stu-id="5261b-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="5261b-284">Mapear segredos para um POCO</span><span class="sxs-lookup"><span data-stu-id="5261b-284">Map secrets to a POCO</span></span>

<span data-ttu-id="5261b-285">Mapear um objeto inteiro literal para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="5261b-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5261b-286">Para mapear os segredos anteriores a `Configuration` um POCO, use o recurso de vinculação de gráficos de [objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API.</span><span class="sxs-lookup"><span data-stu-id="5261b-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="5261b-287">O código a seguir `MovieSettings` se liga a `ServiceApiKey` um POCO personalizado e acessa o valor da propriedade:</span><span class="sxs-lookup"><span data-stu-id="5261b-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="5261b-288">Os `Movies:ConnectionString` `Movies:ServiceApiKey` segredos e segredos são mapeados para as respectivas propriedades em `MovieSettings`:</span><span class="sxs-lookup"><span data-stu-id="5261b-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="5261b-289">Substituição de cordas com segredos</span><span class="sxs-lookup"><span data-stu-id="5261b-289">String replacement with secrets</span></span>

<span data-ttu-id="5261b-290">Armazenar senhas em texto simples é inseguro.</span><span class="sxs-lookup"><span data-stu-id="5261b-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="5261b-291">Por exemplo, uma seqüência de conexão de banco de dados armazenada em *appsettings.json* pode incluir uma senha para o usuário especificado:</span><span class="sxs-lookup"><span data-stu-id="5261b-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="5261b-292">Uma abordagem mais segura é armazenar a senha como um segredo.</span><span class="sxs-lookup"><span data-stu-id="5261b-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="5261b-293">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5261b-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="5261b-294">Remova `Password` o par de valor-chave da seqüência de conexões em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5261b-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="5261b-295">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5261b-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="5261b-296">O valor do segredo pode <xref:System.Data.SqlClient.SqlConnectionStringBuilder> ser definido <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> na propriedade de um objeto para completar a seqüência de conexões:</span><span class="sxs-lookup"><span data-stu-id="5261b-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="5261b-297">Liste os segredos</span><span class="sxs-lookup"><span data-stu-id="5261b-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5261b-298">Execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="5261b-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="5261b-299">A saída a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="5261b-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="5261b-300">No exemplo anterior, um cólon nos nomes-chave denota a hierarquia do objeto dentro *de secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="5261b-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="5261b-301">Remova um único segredo</span><span class="sxs-lookup"><span data-stu-id="5261b-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5261b-302">Execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="5261b-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="5261b-303">O arquivo *secrets.json* do aplicativo foi modificado para remover `MoviesConnectionString` o par de valor-chave associado à chave:</span><span class="sxs-lookup"><span data-stu-id="5261b-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5261b-304">A `dotnet user-secrets list` execução exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="5261b-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="5261b-305">Remova todos os segredos</span><span class="sxs-lookup"><span data-stu-id="5261b-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5261b-306">Execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="5261b-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="5261b-307">Todos os segredos do usuário para o aplicativo foram excluídos do arquivo *secrets.json:*</span><span class="sxs-lookup"><span data-stu-id="5261b-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="5261b-308">A `dotnet user-secrets list` execução exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="5261b-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="5261b-309">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5261b-309">Additional resources</span></span>

* <span data-ttu-id="5261b-310">Consulte [esta edição](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar o Gerente Secreto do IIS.</span><span class="sxs-lookup"><span data-stu-id="5261b-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end
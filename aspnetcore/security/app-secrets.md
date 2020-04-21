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
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Armazenamento seguro de segredos de aplicativos em desenvolvimento em ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT), Kirk [Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), e Scott [Addie](https://github.com/scottaddie)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))

Este documento explica técnicas para armazenar e recuperar dados confidenciais durante o desenvolvimento de um aplicativo ASP.NET Core em uma máquina de desenvolvimento. Nunca armazene senhas ou outros dados confidenciais no código-fonte. Segredos de produção não devem ser usados para desenvolvimento ou teste. Segredos não devem ser implantados com o aplicativo. Em vez disso, segredos devem ser disponibilizados no ambiente de produção através de meios controlados, como variáveis de ambiente, Azure Key Vault, etc. Você pode armazenar e proteger segredos de teste e produção do Azure com o [provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variáveis de ambiente

As variáveis de ambiente são usadas para evitar o armazenamento de segredos de aplicativos em código ou em arquivos de configuração locais. As variáveis de ambiente anulam os valores de configuração para todas as fontes de configuração especificadas anteriormente.

Considere um aplicativo web ASP.NET Core no qual a segurança **de Contas de Usuário Individuais** está ativada. Uma seqüência de conexão de banco de dados padrão é `DefaultConnection`incluída no arquivo *appsettings.json* do projeto com a chave . A seqüência de conexão padrão é para LocalDB, que é executado no modo de usuário e não requer uma senha. Durante a implantação `DefaultConnection` do aplicativo, o valor-chave pode ser substituído com o valor de uma variável de ambiente. A variável ambiente pode armazenar a seqüência de conexão completa com credenciais confidenciais.

> [!WARNING]
> As variáveis de ambiente são geralmente armazenadas em texto simples e não criptografado. Se a máquina ou o processo estiver comprometido, as variáveis de ambiente podem ser acessadas por partes não confiáveis. Medidas adicionais para evitar a divulgação de segredos do usuário podem ser necessárias.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gerente Secreto

A ferramenta Secret Manager armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core. Neste contexto, um pedaço de dados confidenciais é um segredo de aplicativo. Os segredos do aplicativo são armazenados em um local separado da árvore do projeto. Os segredos do aplicativo estão associados a um projeto específico ou compartilhados em vários projetos. Os segredos do aplicativo não são verificados no controle de origem.

> [!WARNING]
> A ferramenta Secret Manager não criptografa os segredos armazenados e não deve ser tratada como uma loja confiável. É só para fins de desenvolvimento. As chaves e valores são armazenados em um arquivo de configuração JSON no diretório do perfil do usuário.

## <a name="how-the-secret-manager-tool-works"></a>Como funciona a ferramenta Secret Manager

A ferramenta Secret Manager abstrai os detalhes da implementação, como onde e como os valores são armazenados. Você pode usar a ferramenta sem saber esses detalhes de implementação. Os valores são armazenados em um arquivo de configuração JSON em uma pasta de perfil de usuário protegida pelo sistema na máquina local:

# <a name="windows"></a>[Windows](#tab/windows)

Caminho do sistema de arquivos:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Caminho do sistema de arquivos:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Nos caminhos de `<user_secrets_id>` arquivo `UserSecretsId` anteriores, substitua-o pelo valor especificado no arquivo *.csproj.*

Não escreva código que dependa da localização ou formato dos dados salvos com a ferramenta Gerenciador Secreto. Esses detalhes de implementação podem mudar. Por exemplo, os valores secretos não são criptografados, mas podem ser no futuro.

## <a name="enable-secret-storage"></a>Habilite o armazenamento secreto

A ferramenta Secret Manager opera em configurações específicas do projeto armazenadas no perfil do usuário.

A ferramenta Secret Manager `init` inclui um comando no .NET Core SDK 3.0.100 ou posterior. Para usar os segredos do usuário, execute o seguinte comando no diretório do projeto:

```dotnetcli
dotnet user-secrets init
```

O comando anterior `UserSecretsId` adiciona `PropertyGroup` um elemento dentro de um arquivo *.csproj.* Por padrão, o `UserSecretsId` texto interno de é um GUID. O texto interno é arbitrário, mas é único no projeto.

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

No Visual Studio, clique com o botão direito do mouse no projeto no Solution Explorer e selecione **Gerenciar segredos** de usuário no menu de contexto. Este gesto `UserSecretsId` adiciona um elemento, preenchido com um GUID, ao arquivo *.csproj.*

## <a name="set-a-secret"></a>Estabeleça um segredo

Defina um segredo de aplicativo que consiste em uma chave e seu valor. O segredo está associado ao `UserSecretsId` valor do projeto. Por exemplo, execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

No exemplo anterior, o cólon denota `Movies` `ServiceApiKey` que é um objeto literal com uma propriedade.

A ferramenta Secret Manager também pode ser usada em outros diretórios. Use `--project` a opção para fornecer o caminho do sistema de arquivos no qual o arquivo *.csproj* existe. Por exemplo:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Achatamento da estrutura JSON no Visual Studio

O gesto **Gerenciar segredos de usuário** do Visual Studio abre um arquivo *secrets.json* no editor de texto. Substitua o conteúdo do *secrets.json* pelos pares de valor-chave a serem armazenados. Por exemplo:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set`. Por exemplo, `dotnet user-secrets remove "Movies:ConnectionString"` correr `Movies` colapsa o objeto literal. O arquivo modificado se assemelha ao seguinte:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Defina vários segredos

Um lote de segredos pode ser definido pela `set` tubulação JSON para o comando. No exemplo a seguir, o conteúdo do arquivo *input.json* é canalizado para o `set` comando.

# <a name="windows"></a>[Windows](#tab/windows)

Abra um shell de comando e execute o seguinte comando:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Abra um shell de comando e execute o seguinte comando:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Acesse um segredo

A [API de configuração principal ASP.NET](xref:fundamentals/configuration/index) fornece acesso a segredos do Gerenciador Secreto.

Em ASP.NET Core 2.0 ou posterior, a fonte de configuração de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando o projeto chama para inicializar uma nova instância do host com padrões pré-configurados. `CreateDefaultBuilder`chamadas <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>o é:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> configuração de segredos do usuário explicitamente chamando o `Startup` construtor. Ligue `AddUserSecrets` somente quando o aplicativo for executado no ambiente Desenvolvimento, como mostrado no exemplo a seguir:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

Os segredos do usuário `Configuration` podem ser recuperados através da API:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]


## <a name="map-secrets-to-a-poco"></a>Mapear segredos para um POCO

Mapear um objeto inteiro literal para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Para mapear os segredos anteriores a `Configuration` um POCO, use o recurso de vinculação de gráficos de [objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API. O código a seguir `MovieSettings` se liga a `ServiceApiKey` um POCO personalizado e acessa o valor da propriedade:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Os `Movies:ConnectionString` `Movies:ServiceApiKey` segredos e segredos são mapeados para as respectivas propriedades em `MovieSettings`:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Substituição de cordas com segredos

Armazenar senhas em texto simples é inseguro. Por exemplo, uma seqüência de conexão de banco de dados armazenada em *appsettings.json* pode incluir uma senha para o usuário especificado:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Uma abordagem mais segura é armazenar a senha como um segredo. Por exemplo:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Remova `Password` o par de valor-chave da seqüência de conexões em *appsettings.json*. Por exemplo:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

O valor do segredo pode <xref:System.Data.SqlClient.SqlConnectionStringBuilder> ser definido <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> na propriedade de um objeto para completar a seqüência de conexões:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Liste os segredos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:

```dotnetcli
dotnet user-secrets list
```

A saída a seguir é exibida:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

No exemplo anterior, um cólon nos nomes-chave denota a hierarquia do objeto dentro *de secrets.json*.

## <a name="remove-a-single-secret"></a>Remova um único segredo

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

O arquivo *secrets.json* do aplicativo foi modificado para remover `MoviesConnectionString` o par de valor-chave associado à chave:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`exibe a seguinte mensagem:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Remova todos os segredos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:

```dotnetcli
dotnet user-secrets clear
```

Todos os segredos do usuário para o aplicativo foram excluídos do arquivo *secrets.json:*

```json
{}
```

A `dotnet user-secrets list` execução exibe a seguinte mensagem:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Recursos adicionais

* Consulte [esta edição](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar o Gerente Secreto do IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT), Daniel [Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))

Este documento explica técnicas para armazenar e recuperar dados confidenciais durante o desenvolvimento de um aplicativo ASP.NET Core em uma máquina de desenvolvimento. Nunca armazene senhas ou outros dados confidenciais no código-fonte. Segredos de produção não devem ser usados para desenvolvimento ou teste. Segredos não devem ser implantados com o aplicativo. Em vez disso, segredos devem ser disponibilizados no ambiente de produção através de meios controlados, como variáveis de ambiente, Azure Key Vault, etc. Você pode armazenar e proteger segredos de teste e produção do Azure com o [provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variáveis de ambiente

As variáveis de ambiente são usadas para evitar o armazenamento de segredos de aplicativos em código ou em arquivos de configuração locais. As variáveis de ambiente anulam os valores de configuração para todas as fontes de configuração especificadas anteriormente.

Considere um aplicativo web ASP.NET Core no qual a segurança **de Contas de Usuário Individuais** está ativada. Uma seqüência de conexão de banco de dados padrão é `DefaultConnection`incluída no arquivo *appsettings.json* do projeto com a chave . A seqüência de conexão padrão é para LocalDB, que é executado no modo de usuário e não requer uma senha. Durante a implantação `DefaultConnection` do aplicativo, o valor-chave pode ser substituído com o valor de uma variável de ambiente. A variável ambiente pode armazenar a seqüência de conexão completa com credenciais confidenciais.

> [!WARNING]
> As variáveis de ambiente são geralmente armazenadas em texto simples e não criptografado. Se a máquina ou o processo estiver comprometido, as variáveis de ambiente podem ser acessadas por partes não confiáveis. Medidas adicionais para evitar a divulgação de segredos do usuário podem ser necessárias.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gerente Secreto

A ferramenta Secret Manager armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core. Neste contexto, um pedaço de dados confidenciais é um segredo de aplicativo. Os segredos do aplicativo são armazenados em um local separado da árvore do projeto. Os segredos do aplicativo estão associados a um projeto específico ou compartilhados em vários projetos. Os segredos do aplicativo não são verificados no controle de origem.

> [!WARNING]
> A ferramenta Secret Manager não criptografa os segredos armazenados e não deve ser tratada como uma loja confiável. É só para fins de desenvolvimento. As chaves e valores são armazenados em um arquivo de configuração JSON no diretório do perfil do usuário.

## <a name="how-the-secret-manager-tool-works"></a>Como funciona a ferramenta Secret Manager

A ferramenta Secret Manager abstrai os detalhes da implementação, como onde e como os valores são armazenados. Você pode usar a ferramenta sem saber esses detalhes de implementação. Os valores são armazenados em um arquivo de configuração JSON em uma pasta de perfil de usuário protegida pelo sistema na máquina local:

# <a name="windows"></a>[Windows](#tab/windows)

Caminho do sistema de arquivos:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Caminho do sistema de arquivos:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Nos caminhos de `<user_secrets_id>` arquivo `UserSecretsId` anteriores, substitua-o pelo valor especificado no arquivo *.csproj.*

Não escreva código que dependa da localização ou formato dos dados salvos com a ferramenta Gerenciador Secreto. Esses detalhes de implementação podem mudar. Por exemplo, os valores secretos não são criptografados, mas podem ser no futuro.

## <a name="enable-secret-storage"></a>Habilite o armazenamento secreto

A ferramenta Secret Manager opera em configurações específicas do projeto armazenadas no perfil do usuário.

Para usar segredos de `UserSecretsId` usuário, `PropertyGroup` defina um elemento dentro de um arquivo *.csproj.* O texto `UserSecretsId` interno de é arbitrário, mas é único para o projeto. Os desenvolvedores normalmente geram `UserSecretsId`um GUID para o .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> No Visual Studio, clique com o botão direito do mouse no projeto no Solution Explorer e selecione **Gerenciar segredos** de usuário no menu de contexto. Este gesto `UserSecretsId` adiciona um elemento, preenchido com um GUID, ao arquivo *.csproj.*

## <a name="set-a-secret"></a>Estabeleça um segredo

Defina um segredo de aplicativo que consiste em uma chave e seu valor. O segredo está associado ao `UserSecretsId` valor do projeto. Por exemplo, execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

No exemplo anterior, o cólon denota `Movies` `ServiceApiKey` que é um objeto literal com uma propriedade.

A ferramenta Secret Manager também pode ser usada em outros diretórios. Use `--project` a opção para fornecer o caminho do sistema de arquivos no qual o arquivo *.csproj* existe. Por exemplo:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Achatamento da estrutura JSON no Visual Studio

O gesto **Gerenciar segredos de usuário** do Visual Studio abre um arquivo *secrets.json* no editor de texto. Substitua o conteúdo do *secrets.json* pelos pares de valor-chave a serem armazenados. Por exemplo:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set`. Por exemplo, `dotnet user-secrets remove "Movies:ConnectionString"` correr `Movies` colapsa o objeto literal. O arquivo modificado se assemelha ao seguinte:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Defina vários segredos

Um lote de segredos pode ser definido pela `set` tubulação JSON para o comando. No exemplo a seguir, o conteúdo do arquivo *input.json* é canalizado para o `set` comando.

# <a name="windows"></a>[Windows](#tab/windows)

Abra um shell de comando e execute o seguinte comando:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Abra um shell de comando e execute o seguinte comando:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Acesse um segredo

A [API de configuração principal ASP.NET](xref:fundamentals/configuration/index) fornece acesso a segredos do Gerenciador Secreto.

Se o projeto tiver como alvo o .NET Framework, instale o pacote [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.


Em ASP.NET Core 2.0 ou posterior, a fonte de configuração de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando o projeto chama para inicializar uma nova instância do host com padrões pré-configurados. `CreateDefaultBuilder`chamadas <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>o é:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]


Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> configuração de segredos do usuário explicitamente chamando o `Startup` construtor. Ligue `AddUserSecrets` somente quando o aplicativo for executado no ambiente Desenvolvimento, como mostrado no exemplo a seguir:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

Os segredos do usuário `Configuration` podem ser recuperados através da API:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapear segredos para um POCO

Mapear um objeto inteiro literal para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Para mapear os segredos anteriores a `Configuration` um POCO, use o recurso de vinculação de gráficos de [objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API. O código a seguir `MovieSettings` se liga a `ServiceApiKey` um POCO personalizado e acessa o valor da propriedade:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Os `Movies:ConnectionString` `Movies:ServiceApiKey` segredos e segredos são mapeados para as respectivas propriedades em `MovieSettings`:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Substituição de cordas com segredos

Armazenar senhas em texto simples é inseguro. Por exemplo, uma seqüência de conexão de banco de dados armazenada em *appsettings.json* pode incluir uma senha para o usuário especificado:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Uma abordagem mais segura é armazenar a senha como um segredo. Por exemplo:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Remova `Password` o par de valor-chave da seqüência de conexões em *appsettings.json*. Por exemplo:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

O valor do segredo pode <xref:System.Data.SqlClient.SqlConnectionStringBuilder> ser definido <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> na propriedade de um objeto para completar a seqüência de conexões:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Liste os segredos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:

```dotnetcli
dotnet user-secrets list
```

A saída a seguir é exibida:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

No exemplo anterior, um cólon nos nomes-chave denota a hierarquia do objeto dentro *de secrets.json*.

## <a name="remove-a-single-secret"></a>Remova um único segredo

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

O arquivo *secrets.json* do aplicativo foi modificado para remover `MoviesConnectionString` o par de valor-chave associado à chave:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

A `dotnet user-secrets list` execução exibe a seguinte mensagem:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Remova todos os segredos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *.csproj* existe:

```dotnetcli
dotnet user-secrets clear
```

Todos os segredos do usuário para o aplicativo foram excluídos do arquivo *secrets.json:*

```json
{}
```

A `dotnet user-secrets list` execução exibe a seguinte mensagem:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Recursos adicionais

* Consulte [esta edição](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar o Gerente Secreto do IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end
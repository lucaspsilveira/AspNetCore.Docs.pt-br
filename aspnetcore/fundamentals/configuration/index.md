---
title: Configuração no ASP.NET Core
author: rick-anderson
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: d76ca78bc988f859b4e99752a0e88735e1df1d82
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501335"
---
# <a name="configuration-in-aspnet-core"></a>Configuração no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e Kirk [Larkin](https://twitter.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

A configuração no ASP.NET Core é realizada usando um ou mais [provedores de configuração](#cp). Os provedores de configuração leem dados de configuração de pares de valor-chave usando uma variedade de fontes de configuração:

* Arquivos de configuração, como *appsettings.json*
* Variáveis de ambiente
* Cofre de Chave do Azure
* Configuração de Aplicativo do Azure
* Argumentos de linha de comando
* Provedores personalizados, instalados ou criados
* Arquivos de diretório
* Objetos do .NET na memória

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))

<a name="default"></a>

## <a name="default-configuration"></a>Configuração padrão

ASP.NET apps web Core criados com [dotnet new](/dotnet/core/tools/dotnet-new) ou Visual Studio geram o seguinte código:

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fornece a configuração padrão para o aplicativo na seguinte ordem:

1. [Provedor de configuração em](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) `IConfiguration` cadeia : adiciona um existente como fonte. No caso de configuração padrão, adiciona a configuração [do host](#hvac) e a configura como a primeira fonte para a configuração do _aplicativo._
1. [appsettings.json](#appsettingsjson) usando o [provedor de configuração JSON](#file-configuration-provider).
1. *ajustes.* `Environment` *.json* usando o [provedor de configuração JSON](#file-configuration-provider). Por exemplo, *ajustes.* ***Produção***. *json* e *appsettings*. ***Desenvolvimento***. *json*.
1. [Segredos do aplicativo](xref:security/app-secrets) quando `Development` o aplicativo é executado no ambiente.
1. Variáveis de ambiente usando o [provedor de configuração Variáveis de Ambiente](#evcp).
1. Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line).

Provedores de configuração que são adicionados mais tarde anulam as configurações anteriores da chave. Por exemplo, `MyKey` se for definido em *appsettings.json* e no ambiente, o valor do ambiente é usado. Usando os provedores de configuração padrão, o [provedor de configuração de linha de comando](#command-line-configuration-provider) substitui todos os outros provedores.

Para obter `CreateDefaultBuilder`mais informações, consulte [Configurações do construtor Padrão](xref:fundamentals/host/generic-host#default-builder-settings).

O código a seguir exibe os provedores de configuração habilitados na ordem em que foram adicionados:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a>appsettings.json

Considere o seguinte *arquivo appsettings.json:*

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

O código a seguir do download da [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

A <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> configuração de carregamento padrão na seguinte ordem:

1. *appsettings.json*
1. *ajustes.* `Environment` *.json* : Por exemplo, as *configurações*. ***Produção***. *json* e *appsettings*. ***Desenvolvimento***. *arquivos json.* A versão do ambiente do arquivo é carregada com base no [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*). Para obter mais informações, consulte <xref:fundamentals/environments>.

*ajustes*. `Environment`. *json* valores sobreposição chaves em *appsettings.json*. Por exemplo, por padrão:

* Em desenvolvimento, *appsettings*. ***Desenvolvimento***. a configuração *json* substitui os valores encontrados em *appsettings.json*.
* Na produção, *appsettings*. ***Produção***. a configuração *json* substitui os valores encontrados em *appsettings.json*. Por exemplo, ao implantar o aplicativo no Azure.

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a>Vincular dados de configuração hierárquica usando o padrão de opções

A maneira preferida de ler valores de configuração relacionados é usando o [padrão de opções](xref:fundamentals/configuration/options). Por exemplo, para ler os seguintes valores de configuração:

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

Criar a `PositionOptions` seguinte classe:

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

Todas as propriedades públicas de leitura e gravação do tipo estão vinculadas. Os campos ***não*** estão ligados.

O seguinte código:

* Chamadas [ConfiguraçãoBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) `PositionOptions` para vincular `Position` a classe à seção.
* Exibe `Position` os dados de configuração.

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)liga e retorna o tipo especificado. `ConfigurationBinder.Get<T>`pode ser mais `ConfigurationBinder.Bind`conveniente do que usar . O código a seguir `ConfigurationBinder.Get<T>` mostra `PositionOptions` como usar com a classe:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

Uma abordagem alternativa ao usar o `Position` padrão de ***opções*** é ligar a seção e adicioná-la ao recipiente de serviço de injeção de [dependência](xref:fundamentals/dependency-injection). No código a `PositionOptions` seguir, é adicionado <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ao contêiner de serviço com e vinculado à configuração:

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

Usando o código anterior, o código a seguir lê as opções de posição:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

Usando a configuração [padrão,](#default) as *configurações.json* e *as configurações.* `Environment`Os arquivos *.json* estão habilitados com [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75). Alterações feitas nas *configurações.json* e *appsettings.* `Environment`O arquivo *.json* ***após*** o início do aplicativo é lido pelo [provedor de configuração JSON](#jcp).

Consulte [o provedor de configuração JSON](#jcp) neste documento para obter informações sobre a adição de arquivos adicionais de configuração JSON.

<a name="security"></a>

## <a name="security-and-secret-manager"></a>Segurança e gerente secreto

Diretrizes de dados de configuração:

* Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação. O [gerente secreto](xref:security/app-secrets) pode ser usado para armazenar segredos em desenvolvimento.
* Não use segredos de produção em ambientes de teste ou de desenvolvimento.
* Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.

Por [padrão,](#default) [o gerenciador secreto](xref:security/app-secrets) lê as configurações após *appsettings.json* e *appsettings.* `Environment` *.json*.

Para obter mais informações sobre armazenamento de senhas ou outros dados confidenciais:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>: Inclui conselhos sobre o uso de variáveis de ambiente para armazenar dados confidenciais. O Gerenciador Secreto usa o [provedor de configuração de arquivos](#fcp) para armazenar segredos de usuário em um arquivo JSON no sistema local.

O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core. Para obter mais informações, consulte <xref:security/key-vault-configuration>.

<a name="evcp"></a>

## <a name="environment-variables"></a>Variáveis de ambiente

Usando a configuração <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> [padrão,](#default) a configuração carrega a partir de pares de valores de chave variáveis do ambiente após a leitura *de appsettings.json*, *configurações.* `Environment` *.json*e [gerente secreto.](xref:security/app-secrets) Portanto, os valores-chave lidos a partir do ambiente sobrepõem valores lidos a partir de *appsettings.json*, *appsettings.* `Environment` *.json,* e gerente secreto.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Os `set` seguintes comandos:

* Defina as teclas e valores do ambiente do [exemplo anterior](#appsettingsjson) no Windows.
* Teste as configurações ao usar o [download da amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample). O `dotnet run` comando deve ser executado no diretório do projeto.

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

As configurações do ambiente anteriores:

* São definidos apenas em processos iniciados a partir da janela de comando em que foram definidos.
* Não será lido pelos navegadores lançados com o Visual Studio.

Os seguintes comandos [setx](/windows-server/administration/windows-commands/setx) podem ser usados para definir as chaves e valores do ambiente no Windows. Ao `set` `setx` contrário, as configurações são persistidas. `/M`define a variável no ambiente do sistema. Se `/M` o switch não for usado, uma variável de ambiente do usuário será definida.

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

Para testar que os comandos anteriores sobrepõem *as configurações.json* e *as configurações.* `Environment` *.json:*

* Com Visual Studio: Saia e reinicie o Visual Studio.
* Com a CLI: Inicie uma `dotnet run`nova janela de comando e entre .

Chamada <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> com uma string para especificar um prefixo para variáveis de ambiente:

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

No código anterior:

* `config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`é adicionado após os [provedores de configuração padrão](#default). Para um exemplo de encomendar os provedores de configuração, consulte o provedor de [configuração JSON](#jcp).
* As variáveis de `MyCustomPrefix_` ambiente definidas com o prefixo sobrepõem os [provedores de configuração padrão](#default). Isso inclui variáveis de ambiente sem o prefixo.

O prefixo é retirado quando os pares de valor de chave de configuração são lidos.

Os seguintes comandos testam o prefixo personalizado:

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

A [configuração padrão](#default) carrega variáveis de ambiente e `DOTNET_` `ASPNETCORE_`argumentos de linha de comando prefixados com e . Os `DOTNET_` `ASPNETCORE_` prefixos e os prefixos são usados por ASP.NET Core para [configuração de host e aplicativo,](xref:fundamentals/host/generic-host#host-configuration)mas não para configuração do usuário. Para obter mais informações sobre a configuração do host e do aplicativo, consulte [.NET Generic Host](xref:fundamentals/host/generic-host).

No [Azure App Service](https://azure.microsoft.com/services/app-service/), selecione Nova **configuração de aplicativo** na página Configuração > Configuração de **Configurações.** As configurações do aplicativo azure App Service são:

* Criptografado em repouso e transmitido através de um canal criptografado.
* Expostos como variáveis de ambiente.

Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Consulte [prefixos de seqüência de conexão](#constr) para obter informações sobre as strings de conexão do banco de dados Do Azure.

<a name="clcp"></a>

## <a name="command-line"></a>Linha de comando

Usando a configuração <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> [padrão,](#default) a configuração carrega a partir de pares de valores-chave de argumento de linha de comando após as seguintes fontes de configuração:

* *appsettings.json* e *appsettings*. `Environment`. *arquivos json.*
* [Segredos de aplicativos (Secret Manager)](xref:security/app-secrets) no ambiente de Desenvolvimento.
* Variáveis de ambiente.

Por [padrão,](#default)os valores de configuração definidos nos valores de configuração de substituição de linha de comando definidos com todos os outros provedores de configuração.

### <a name="command-line-arguments"></a>Argumentos de linha de comando

O comando a seguir `=`define chaves e valores usando:

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

O comando a seguir `/`define chaves e valores usando:

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

O comando a seguir `--`define chaves e valores usando:

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

O valor-chave:

* Deve `=`seguir , ou a chave `--` `/` deve ter um prefixo ou quando o valor segue um espaço.
* Não é necessário `=` se for usado. Por exemplo, `MySetting=`.

Dentro do mesmo comando, não misture pares de valores-chave de argumento de linha de comando que são usados `=` com pares de valor-chave que usam um espaço.

### <a name="switch-mappings"></a>Mapeamentos de comutador

Os mapeamentos de switch permitem a lógica de substituição de nomes **de tecla.** Forneça um dicionário de substituições de switch para o <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> método.

Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando. Se a tecla de linha de comando for encontrada no dicionário, o valor do dicionário será passado para trás para definir o par de valor-chave na configuração do aplicativo. Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).

Regras de chave do dicionário de mapeamentos de comutador:

* Os interruptores `-` devem `--`começar com ou .
* O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.

Para usar um dicionário de mapeamentos de `AddCommandLine`switch, passe-o para a chamada para:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

O código a seguir mostra os valores-chave para as teclas substituídas:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

Execute o seguinte comando para testar a substituição da chave:

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

Nota: Atualmente, `=` não pode ser usado para `-`definir valores de substituição de tecla com um único traço . Consulte [este problema do GitHub](https://github.com/dotnet/extensions/issues/3059).

O comando a seguir funciona para testar a substituição da chave:

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos. A `CreateDefaultBuilder` chamada `AddCommandLine` do método não inclui switches mapeados, e não há como `CreateDefaultBuilder`passar o dicionário de mapeamento de switch para . A solução não é passar os `CreateDefaultBuilder` argumentos, `ConfigurationBuilder` mas sim `AddCommandLine` permitir que o método do método processe tanto os argumentos quanto o dicionário de mapeamento de switch.

## <a name="hierarchical-configuration-data"></a>Dados de configuração hierárquica

A API de configuração lê dados de configuração hierárquica, achatando os dados hierárquicos com o uso de um delimitador nas teclas de configuração.

O [download da amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *appsettings.json:*

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

O código a seguir do download da [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

A maneira preferida de ler dados de configuração hierárquica é usando o padrão de opções. Para obter mais informações, consulte [Vincular dados de configuração hierárquica](#optpat) neste documento.

Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração. Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection).

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a>Chaves e valores de configuração

Teclas de configuração:

* São insensíveis aos casos. Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.
* Se uma chave e valor for definido em mais de um provedor de configuração, o valor do último provedor adicionado será usado. Para obter mais informações, consulte [Configuração Padrão](#default).
* Chaves hierárquicas
  * Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.
  * Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas. Um sublinhado `__`duplo, é suportado por todas as plataformas `:`e é automaticamente convertido em um cólon .
  * No Azure Key Vault, `--` as teclas hierárquicas usam como separador. Escreva código para `--` substituir `:` o por um quando os segredos são carregados na configuração do aplicativo.
* O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração. A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#boa).

Valores de configuração:

* São cordas.
* Não é possível armazenar valores nulos na configuração ou associá-los a objetos.

<a name="cp"></a>

## <a name="configuration-providers"></a>Provedores de configuração

A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.

| Provedor | Fornece a configuração de  |
| -------- | ----------------------------------- |
| [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) | Cofre de Chave do Azure |
| [Provedor de configuração do Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Configuração de Aplicativo do Azure |
| [Provedor de configuração de linha de comando](#clcp) | Parâmetros de linha de comando |
| [Provedor de Configuração personalizado](#custom-configuration-provider) | Fonte personalizada |
| [Provedor de configuração de variáveis de ambiente](#evcp) | Variáveis de ambiente |
| [Provedor de configuração de arquivos](#file-configuration-provider) | Arquivos INI, JSON e XML |
| [Provedor de configuração de chave por arquivo](#key-per-file-configuration-provider) | Arquivos de diretório |
| [Provedor de configuração de memória](#memory-configuration-provider) | Coleções na memória |
| [Gerente Secreto](xref:security/app-secrets)  | Arquivo no diretório de perfil do usuário |

As fontes de configuração são lidas na ordem em que seus provedores de configuração são especificados. Encomendar provedores de configuração em código para se adequar às prioridades para as fontes de configuração subjacentes que o aplicativo requer.

Uma sequência comum de provedores de configuração é:

1. *appsettings.json*
1. *ajustes*. `Environment`. *json*
1. [Gerente Secreto](xref:security/app-secrets)
1. Variáveis de ambiente usando o [provedor de configuração Variáveis de Ambiente](#evcp).
1. Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).

Uma prática comum é adicionar o provedor de configuração de linha de comando por último em uma série de provedores para permitir que os argumentos de linha de comando sobreponham a configuração definida pelos outros provedores.

A seqüência anterior de provedores é usada na [configuração padrão](#default).

<a name="constr"></a>

### <a name="connection-string-prefixes"></a>Prefixos de cadeia de conexão

A API de configuração tem regras especiais de processamento para quatro variáveis de ambiente de cadeia de conexão. Essas strings de conexão estão envolvidas na configuração de strings de conexão Azure para o ambiente do aplicativo. As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no `AddEnvironmentVariables`aplicativo com a [configuração padrão](#default) ou quando nenhum prefixo é fornecido a .

| Prefixo da cadeia de conexão | Provedor |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Provedor personalizado |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:

* A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).
* Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).

| Chave de variável de ambiente | Chave de configuração convertida | Entrada de configuração do provedor                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Entrada de configuração não criada.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName`:<br>Valor: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName`:<br>Valor: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName`:<br>Valor: `System.Data.SqlClient`  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a>Provedor de configuração JSON

A <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> configuração de carregadores a partir de pares de valores-chave de arquivo JSON.

Sobrecargas podem especificar:

* Se o arquivo é opcional.
* Se a configuração será recarregada caso o arquivo seja alterado.

Considere o código a seguir:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

O código anterior:

* Configura o provedor de configuração JSON para carregar o arquivo *MyConfig.json* com as seguintes opções:
  * `optional: true`: O arquivo é opcional.
  * `reloadOnChange: true`: O arquivo é recarregado quando as alterações são salvas.
* Lê os [provedores de configuração padrão](#default) antes do arquivo *MyConfig.json.* Configurações na configuração de substituição de arquivo *MyConfig.json* nos provedores de configuração padrão, incluindo o provedor de [configuração de variáveis de ambiente](#evcp) e o provedor de [configuração command-line](#clcp).

Você normalmente ***não*** deseja que um arquivo JSON personalizado seja definido no provedor de [configuração de variáveis de ambiente](#evcp) e no provedor [de configuração command-line](#clcp).

O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

No código anterior, configurações no *MyConfig.json* e *MyConfig*. `Environment`. arquivos *json:*

* Substituir configurações nas *configurações.json* e *appsettings*. `Environment`. *arquivos json.*
* São substituídos por configurações no provedor de [configuração de variáveis de ambiente](#evcp) e no provedor de [configuração command-line](#clcp).

O [download da amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *MyConfig.json:*

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

O código a seguir do download da [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a>Provedor de configuração de arquivos

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos. Os seguintes provedores de configuração derivam de: `FileConfigurationProvider`

* [Provedor de configuração INI](#ini-configuration-provider)
* [Provedor de configuração JSON](#jcp)
* [Provedor de configuração XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Provedor de configuração INI

O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.

O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

No código anterior, configurações no *MyIniConfig.ini* e *MyIniConfig*. `Environment`. os arquivos *ini* são substituídos por configurações no:

* [Provedor de configuração de variáveis de ambiente](#evcp)
* [Provedor de configuração de linha de comando](#clcp).

O [download da amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *MyIniConfig.ini:*

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

O código a seguir do download da [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a>Provedor de configuração XML

O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.

O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

No código anterior, configurações no *MyXMLFile.xml* e *MyXMLFile*. `Environment`. Os arquivos *xml* são substituídos por configurações no:

* [Provedor de configuração de variáveis de ambiente](#evcp)
* [Provedor de configuração de linha de comando](#clcp).

O [download da amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *MyXMLFile.xml:*

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

O código a seguir do download da [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

O código a seguir lê o arquivo de configuração anterior e exibe as chaves e valores:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

É possível usar atributos para fornecer valores:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

O arquivo de configuração anterior carrega as seguintes chaves com `value`:

* key:attribute
* section:key:attribute

## <a name="key-per-file-configuration-provider"></a>Provedor de configuração de chave por arquivo

O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração. A chave é o nome do arquivo. O valor contém o conteúdo do arquivo. O provedor de configuração key-per-file é usado em cenários de hospedagem do Docker.

Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>. O `directoryPath` para os arquivos deve ser um caminho absoluto.

As sobrecargas permitem especificar:

* Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.
* Se o diretório é opcional, e o caminho para o diretório.

O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo. Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a>Provedor de configuração de memória

O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.

O código a seguir adiciona uma coleção de memória ao sistema de configuração:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

O código a seguir do download da [amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe as configurações anteriores:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

No código anterior, `config.AddInMemoryCollection(Dict)` é adicionado após os [provedores de configuração padrão](#default). Para um exemplo de encomendar os provedores de configuração, consulte o provedor de [configuração JSON](#jcp).

Para um exemplo de encomendar os provedores de configuração, consulte o provedor de [configuração JSON](#jcp).

Consulte [Vincular uma matriz](#boa) `MemoryConfigurationProvider`para outro exemplo usando .

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrai um único valor da configuração com uma chave especificada e converte-o para o tipo especificado:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

No código anterior, `NumberKey` se não for encontrado na configuração, o valor padrão de `99` é usado.

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren e Exists

Para os exemplos a seguir, considere o seguinte arquivo *MySubsection.json:*

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

O código a seguir adiciona *MySubsection.json* aos provedores de configuração:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retorna uma subseção de configuração com a chave de subseção especificada.

O código seguinte `section1`retorna valores para:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

O código seguinte `section2:subsection0`retorna valores para:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

`GetSection` nunca retorna `null`. Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.

Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido. <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.

### <a name="getchildren-and-exists"></a>GetChildren e Existe

O código a seguir chama [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) e retorna valores para: `section2:subsection0`

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

O código anterior chama [ConfigurationExtensions.Existe](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para verificar se a seção existe:

 <a name="boa"></a>

## <a name="bind-an-array"></a>Vincule uma matriz

O [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) suporta matrizes de vinculação a objetos usando índices de array em chaves de configuração. Qualquer formato de matriz que exponha um segmento-chave numérico é capaz de vincular a uma matriz de classe [POCO.](https://wikipedia.org/wiki/Plain_Old_CLR_Object)

Considere *MyArray.json* a partir do download da [amostra:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

O código a seguir adiciona *MyArray.json* aos provedores de configuração:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

O código a seguir lê a configuração e exibe os valores:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

O código anterior retorna a seguinte saída:

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

Na saída anterior, o `value40`Índice 3 `"4": "value40",` tem valor, correspondente a *MyArray.json*. Os índices de matriz vinculados são contínuos e não estão vinculados ao índice de chave de configuração. O aglutinante de configuração não é capaz de vincular valores nulos ou criar entradas nulas em objetos vinculados

O código a `array:entries` seguir carrega <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> a configuração com o método de extensão:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

O código a seguir lê `arrayDict` `Dictionary` a configuração no e exibe os valores:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

O código anterior retorna a seguinte saída:

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`. Quando os dados de configuração que contêm um array são vinculados, os índices de array nas teclas de configuração são usados para iterar os dados de configuração ao criar o objeto. Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.

O item de &num;configuração ausente para o `ArrayExample` índice 3 pode ser fornecido &num;antes de ser vinculado à instância por qualquer provedor de configuração que leia o par de tecla/valor do índice 3. Considere o seguinte arquivo *Value3.json* do download da amostra:

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

O código a seguir inclui a configuração `arrayDict` `Dictionary` *de Value3.json* e:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

O código a seguir lê a configuração anterior e exibe os valores:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

O código anterior retorna a seguinte saída:

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

Provedores de configuração personalizados não são necessários para implementar a associação de matriz.

## <a name="custom-configuration-provider"></a>Provedor de Configuração personalizado

O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).

O provedor tem as seguintes características:

* O banco de dados EF na memória é usado para fins de demonstração. Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.
* O provedor lê uma tabela de banco de dados na configuração na inicialização. O provedor não consulta o banco de dados em uma base por chave.
* O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.

Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.

*Models/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. O provedor de configuração inicializa o banco de dados quando ele está vazio. Como [as chaves de configuração são insensíveis](#keys)a casos, o dicionário usado para inicializar o banco de dados é criado com o comparador insensível a casos[(StringComparer.OrdinalIgnoreCase).](xref:System.StringComparer.OrdinalIgnoreCase)

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a>Configuração de acesso em Inicialização

O código a seguir `Startup` exibe dados de configuração em métodos:

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-razor-pages"></a>Configuração de acesso em Páginas de Barbear

O código a seguir exibe dados de configuração em uma página de navalha:

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a>Configuração de acesso em um arquivo de exibição MVC

O código a seguir exibe dados de configuração em uma exibição de MVC:

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a>Configuração do host versus do aplicativo

Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado. O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo. O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico. Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo. Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.

<a name="dhc"></a>

## <a name="default-host-configuration"></a>Configuração de host padrão

Para obter detalhes sobre a configuração padrão ao usar o [host da Web](xref:fundamentals/host/web-host), confira a [versão do ASP.NET Core 2.2 deste tópico](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).

* A configuração do host é fornecida de:
  * Variáveis de ambiente `DOTNET_` prefixadas `DOTNET_ENVIRONMENT`com (por exemplo) usando o [provedor de configuração Variáveis de Ambiente](#environment-variables-configuration-provider). O prefixo (`DOTNET_`) é removido durante o carregamento dos pares chave-valor de configuração.
  * Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).
* A configuração padrão do host da Web foi estabelecida (`ConfigureWebHostDefaults`):
  * O Kestrel é usado como o servidor Web e configurado usando provedores de configuração do aplicativo.
  * Adicione o middleware de filtragem de host.
  * Adicione o middleware de cabeçalhos encaminhados se a variável de ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for definida para `true`.
  * Habilite a integração de IIS.

## <a name="other-configuration"></a>Outra configuração

Este tópico diz respeito apenas à *configuração do aplicativo*. Outros aspectos da execução e hospedagem ASP.NET os aplicativos Core são configurados usando arquivos de configuração não abordados neste tópico:

* *launch.json*/*launchSettings.json* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:
  * Em <xref:fundamentals/environments#development>.
  * Através do conjunto de documentação onde os arquivos são usados para configurar ASP.NET aplicativos Core para cenários de desenvolvimento.
* *Web.config* é um arquivo de configuração do servidor, descrito nos seguintes tópicos:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Para obter mais informações sobre a configuração <xref:migration/proper-to-2x/index#store-configurations>do aplicativo migratório das versões anteriores do ASP.NET, consulte .

## <a name="add-configuration-from-an-external-assembly"></a>Adicionar configuração de um assembly externo

Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo. Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Recursos adicionais

* [Código-fonte de configuração](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*. Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:

* Cofre de Chave do Azure
* Configuração de Aplicativo do Azure
* Argumentos de linha de comando
* Provedores personalizados (instalados ou criados)
* Arquivos de diretório
* Variáveis de ambiente
* Objetos do .NET na memória
* Arquivos de configurações

Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:

```csharp
using Microsoft.Extensions.Configuration;
```

O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico. As opções usam classes para representar grupos de configurações relacionadas. Para obter mais informações, consulte <xref:fundamentals/configuration/options>.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>Configuração do host versus do aplicativo

Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado. O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo. O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico. Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo. Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.

## <a name="other-configuration"></a>Outra configuração

Este tópico diz respeito apenas à *configuração do aplicativo*. Outros aspectos da execução e hospedagem ASP.NET os aplicativos Core são configurados usando arquivos de configuração não abordados neste tópico:

* *launch.json*/*launchSettings.json* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:
  * Em <xref:fundamentals/environments#development>.
  * Através do conjunto de documentação onde os arquivos são usados para configurar ASP.NET aplicativos Core para cenários de desenvolvimento.
* *Web.config* é um arquivo de configuração do servidor, descrito nos seguintes tópicos:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Para obter mais informações sobre a configuração <xref:migration/proper-to-2x/index#store-configurations>do aplicativo migratório das versões anteriores do ASP.NET, consulte .

## <a name="default-configuration"></a>Configuração padrão

Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host. `CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:

O seguinte se aplica a aplicativos que usam o [host da Web](xref:fundamentals/host/web-host). Para obter detalhes sobre a configuração padrão ao usar o [host genérico](xref:fundamentals/host/generic-host), confira [versão mais recente deste tópico](xref:fundamentals/configuration/index).

* A configuração do host é fornecida de:
  * Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider). O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.
  * Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).
* A configuração do aplicativo é fornecida de:
  * *appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).
  * *appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).
  * [Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.
  * Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).
  * Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).

## <a name="security"></a>Segurança

Adote as seguintes práticas para proteger dados de configuração confidenciais:

* Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.
* Não use segredos de produção em ambientes de teste ou de desenvolvimento.
* Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.

Para obter mais informações, consulte estes tópicos:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>&ndash; Inclui conselhos sobre o uso de variáveis de ambiente para armazenar dados confidenciais. O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local. O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.

O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core. Para obter mais informações, consulte <xref:security/key-vault-configuration>.

## <a name="hierarchical-configuration-data"></a>Dados de configuração hierárquica

A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.

No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:

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

Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração. As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:

* section0:key0
* section0:key1
* section1:key0
* section1:key1

Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração. Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).

## <a name="conventions"></a>Convenções

### <a name="sources-and-providers"></a>Origens e provedores

Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.

Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada. Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.

<xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo. <xref:Microsoft.Extensions.Configuration.IConfiguration>pode ser injetado em <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> uma <xref:Microsoft.AspNetCore.Mvc.Controller> Lâmina de Barbear páginas ou MVC para obter configuração para a classe.

Nos exemplos a seguir, o `_config` campo é usado para acessar valores de configuração:

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

Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.

### <a name="keys"></a>simétricas

As chaves de configuração adotam as convenções a seguir:

* As chaves não diferenciam maiúsculas de minúsculas. Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.
* Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.
* Chaves hierárquicas
  * Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.
  * Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas. Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.
  * No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador. Escreva código para substituir os traços por um cólon quando os segredos são carregados na configuração do aplicativo.
* O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração. A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).

### <a name="values"></a>Valores

Os valores de configuração adotam as convenções a seguir:

* Os valores são cadeias de caracteres.
* Não é possível armazenar valores nulos na configuração ou associá-los a objetos.

## <a name="providers"></a>Provedores

A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.

| Provedor | Fornece a configuração de &hellip; |
| -------- | ----------------------------------- |
| [Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*) | Cofre de Chave do Azure |
| [Provedor da Configuração de Aplicativos do Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure) | Configuração de Aplicativo do Azure |
| [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider) | Parâmetros de linha de comando |
| [Provedor de Configuração personalizado](#custom-configuration-provider) | Fonte personalizada |
| [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider) | Variáveis de ambiente |
| [Provedor de Configuração de Arquivo](#file-configuration-provider) | Arquivos (INI, JSON, XML) |
| [Provedor de Configuração de Chave por Arquivo](#key-per-file-configuration-provider) | Arquivos de diretório |
| [Provedor de Configuração de Memória](#memory-configuration-provider) | Coleções na memória |
| [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*) | Arquivo no diretório de perfil do usuário |

Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração. Os provedores de configuração descritos neste tópico são descritos em ordem alfabética, não na ordem que o código os organiza. Encomendar provedores de configuração em código para se adequar às prioridades para as fontes de configuração subjacentes que o aplicativo requer.

Uma sequência comum de provedores de configuração é:

1. Arquivos *(appsettings.json*, *appsettings.{ Environment}.json*, `{Environment}` onde está o ambiente de hospedagem atual do aplicativo)
1. [Cofre de Chaves Azure](xref:security/key-vault-configuration)
1. [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)
1. Variáveis de ambiente
1. Argumentos de linha de comando

Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.

A seqüência anterior de provedores é usada `CreateDefaultBuilder`quando um novo construtor de host é inicializado com . Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).

## <a name="configure-the-host-builder-with-useconfiguration"></a>Configurar o construtor de host com UseConfiguration

Para configurar o construtor de host, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> no construtor de host com a configuração.

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

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>Substituir a configuração anterior por argumentos de linha de comando

Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>Remover provedores adicionados pelo CreateDefaultBuilder

Para remover os `CreateDefaultBuilder`provedores adicionados por , consulte [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) no [IConfigurationBuilder.Sources:](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>Consumir a configuração durante a inicialização do aplicativo

a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`. Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).

## <a name="command-line-configuration-provider"></a>Provedor de Configuração de Linha de Comando

O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em runtime.

Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado. Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).

`CreateDefaultBuilder` também carrega:

* Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.
* [Segredos de usuário (Gerente Secreto)](xref:security/app-secrets) no ambiente desenvolvimento.
* Variáveis de ambiente.

`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último. Os argumentos de linha de comando passados em runtime substituem a configuração definida por outros provedores.

`CreateDefaultBuilder` age quando o host é construído. Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.

Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`. Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**Exemplo**

O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

1. Abra um prompt de comando no diretório do projeto.
1. Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.
1. Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.
1. Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.

### <a name="arguments"></a>Argumentos

O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço. O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).

| Prefixo da chave               | Exemplo                                                |
| ------------------------ | ------------------------------------------------------ |
| Nenhum prefixo                | `CommandLineKey1=value1`                               |
| Dois traços (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Barra (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.

Exemplo de comandos:

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>Mapeamentos de comutador

Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave. Ao construir manualmente <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>a configuração com um , <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> forneça um dicionário de substituições de switch para o método.

Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando. Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo. Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).

Regras de chave do dicionário de mapeamentos de comutador:

* Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).
* O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.

Crie um dicionário de mapeamentos de opção. No exemplo a seguir, dois mapeamentos de opção são criados:

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos. A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`. A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.

Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.

| Chave       | Valor             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.

| Chave               | Valor    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>Provedor de Configuração de Variáveis de Ambiente

O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em runtime.

Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[O Azure App Service](https://azure.microsoft.com/services/app-service/) permite definir variáveis de ambiente no Portal Azure que podem substituir a configuração do aplicativo usando o Provedor de Configuração de Variáveis de Ambiente. Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `ASPNETCORE_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host da Web](xref:fundamentals/host/web-host) e `CreateDefaultBuilder` é chamado. Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).

`CreateDefaultBuilder` também carrega:

* Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.
* Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.
* [Segredos de usuário (Gerente Secreto)](xref:security/app-secrets) no ambiente desenvolvimento.
* Argumentos de linha de comando.

O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*. Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em runtime para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.

Para fornecer a configuração do aplicativo a partir de `ConfigureAppConfiguration` variáveis `AddEnvironmentVariables` adicionais de ambiente, ligue para os provedores adicionais do aplicativo e ligue com o prefixo:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

Ligue `AddEnvironmentVariables` por último para permitir variáveis de ambiente com o prefixo dado para substituir valores de outros provedores.

**Exemplo**

O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.

1. Execute o aplicativo de exemplo. Abra um navegador para o aplicativo em `http://localhost:5000`.
1. Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`. O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.

Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente. Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.

Para expor todas as variáveis de ambiente disponíveis `FilteredConfiguration` para o aplicativo, altere o em *Páginas/Index.cshtml.cs* para o seguinte:

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Prefixos

As variáveis de ambiente carregadas na configuração do aplicativo são `AddEnvironmentVariables` filtradas ao fornecer um prefixo ao método. Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

O prefixo é removido na criação dos pares chave-valor de configuração.

Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente. Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).

**Prefixos de cadeia de conexão**

A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo. As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.

| Prefixo da cadeia de conexão | Provedor |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Provedor personalizado |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:

* A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).
* Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).

| Chave de variável de ambiente | Chave de configuração convertida | Entrada de configuração do provedor                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Entrada de configuração não criada.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName`:<br>Valor: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName`:<br>Valor: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName`:<br>Valor: `System.Data.SqlClient`  |

**Exemplo**

Uma variável de ambiente de string de conexão personalizada é criada no servidor:

* Nome &ndash;`CUSTOMCONNSTR_ReleaseDB`
* Valor &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

Se `IConfiguration` for injetado e atribuído `_config`a um campo chamado, leia o valor:

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>Provedor de Configuração de Arquivo

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos. Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:

* [Provedor de Configuração INI](#ini-configuration-provider)
* [Provedor de Configuração JSON](#json-configuration-provider)
* [Provedor de Configuração XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Provedor de Configuração INI

O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.

Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.

As sobrecargas permitem especificar:

* Se o arquivo é opcional.
* Se a configuração será recarregada caso o arquivo seja alterado.
* O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

Um exemplo genérico de um arquivo de configuração INI:

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

O arquivo de configuração anterior carrega as seguintes chaves com `value`:

* section0:key0
* section0:key1
* section1:subsection:key
* section2:subsection0:key
* section2:subsection1:key

### <a name="json-configuration-provider"></a>Provedor de Configuração JSON

O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em runtime.

Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

As sobrecargas permitem especificar:

* Se o arquivo é opcional.
* Se a configuração será recarregada caso o arquivo seja alterado.
* O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.

`AddJsonFile`é automaticamente chamado duas vezes quando um `CreateDefaultBuilder`novo construtor de host é inicializado com . O método é chamado para carregar a configuração de:

* *appsettings.json* &ndash; Esse arquivo é lido primeiro. A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.
* *ajustes. {Ambiente}.json* &ndash; A versão do ambiente do arquivo é carregada com base no [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).

Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).

`CreateDefaultBuilder` também carrega:

* Variáveis de ambiente.
* [Segredos de usuário (Gerente Secreto)](xref:security/app-secrets) no ambiente desenvolvimento.
* Argumentos de linha de comando.

O Provedor de Configuração JSON é estabelecido primeiro. Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**Exemplo**

O aplicativo de exemplo aproveita `CreateDefaultBuilder` o método de conveniência estática `AddJsonFile`para construir o host, que inclui duas chamadas para:

* A primeira `AddJsonFile` chamada para carregar configuração a partir de *appsettings.json*:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* A segunda `AddJsonFile` chamada para carregar configuração a partir de *configurações.{ Ambiente}.json*. Para *ajustes. Development.json* No aplicativo de exemplo, o seguinte arquivo é carregado:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. Execute o aplicativo de exemplo. Abra um navegador para o aplicativo em `http://localhost:5000`.
1. A saída contém pares de valor-chave para a configuração com base no ambiente do aplicativo. O nível de `Logging:LogLevel:Default` log `Debug` para a chave é ao executar o aplicativo no ambiente Desenvolvimento.
1. Execute o aplicativo de exemplo novamente no ambiente Produção:
   1. Abra o arquivo *Propriedades/launchSettings.json.*
   1. No `ConfigurationSample` perfil, altere o `ASPNETCORE_ENVIRONMENT` valor `Production`da variável ambiente para .
   1. Salve o arquivo e `dotnet run` execute o aplicativo com um shell de comando.
1. As configurações nas *configurações. Development.json* não mais anula as configurações em *appsettings.json*. O nível de `Logging:LogLevel:Default` log `Warning`para a chave é .

### <a name="xml-configuration-provider"></a>Provedor de Configuração XML

O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.

Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

As sobrecargas permitem especificar:

* Se o arquivo é opcional.
* Se a configuração será recarregada caso o arquivo seja alterado.
* O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.

O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados. Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:

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

O arquivo de configuração anterior carrega as seguintes chaves com `value`:

* section0:key0
* section0:key1
* section1:key0
* section1:key1

Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:

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

O arquivo de configuração anterior carrega as seguintes chaves com `value`:

* section:section0:key:key0
* section:section0:key:key1
* section:section1:key:key0
* section:section1:key:key1

É possível usar atributos para fornecer valores:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

O arquivo de configuração anterior carrega as seguintes chaves com `value`:

* key:attribute
* section:key:attribute

## <a name="key-per-file-configuration-provider"></a>Provedor de Configuração de Chave por Arquivo

O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração. A chave é o nome do arquivo. O valor contém o conteúdo do arquivo. O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.

Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>. O `directoryPath` para os arquivos deve ser um caminho absoluto.

As sobrecargas permitem especificar:

* Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.
* Se o diretório é opcional, e o caminho para o diretório.

O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo. Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>Provedor de Configuração de Memória

O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.

Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.

No exemplo a seguir, um dicionário de configuração é criado:

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrai um único valor da configuração com uma chave especificada e converte-o para o tipo de não coleta especificado. Uma sobrecarga aceita um valor padrão.

O exemplo a seguir:

* extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`. Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.
* Digita o valor como um `int`.
* Armazena o valor na propriedade `NumberConfig` para uso pela página.

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

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren e Exists

Para os exemplos a seguir, considere o seguinte arquivo JSON. Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:

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

Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:

* section0:key0
* section0:key1
* section1:key0
* section1:key1
* section2:subsection0:key0
* section2:subsection0:key1
* section2:subsection1:key0
* section2:subsection1:key1

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.

Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:

```csharp
var configSection = _config.GetSection("section1");
```

`configSection` não tem um valor, apenas uma chave e um caminho.

Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection` nunca retorna `null`. Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.

Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido. <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.

### <a name="getchildren"></a>GetChildren

Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a>Exists

Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.

## <a name="bind-to-an-object-graph"></a>Associar a um gráfico de objeto

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro. Como com a vinculação de um objeto simples, apenas as propriedades públicas de leitura/gravação estão vinculadas.

O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`. A instância associada é atribuída a uma propriedade para renderização:

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)liga e retorna o tipo especificado. O `Get<T>` é mais conveniente do que usar `Bind`. O código a seguir `Get<T>` mostra como usar com o exemplo anterior:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>Associar uma matriz a uma classe

*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*

O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração. Qualquer formato de matriz que exponha`:0:`um `:1:` &hellip; `:{n}:`segmento-chave numérico (, ) é capaz de vincular a uma matriz de classe POCO.

> [!NOTE]
> A associação é fornecida por convenção. Provedores de configuração personalizados não são necessários para implementar a associação de matriz.

**Processamento de matriz na memória**

Considere as chaves de configuração e os valores mostrados na tabela a seguir.

| Chave             | Valor  |
| :-------------: | :----: |
| array:entries:0 | value0 |
| array:entries:1 | value1 |
| array:entries:2 | value2 |
| array:entries:4 | value4 |
| array:entries:5 | value5 |

Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

A matriz ignora um valor para o índice &num;3. O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.

No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

Os dados de configuração estão associados ao objeto:

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)a sintaxe também pode ser usada, o que resulta em código mais compacto:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.

| Índice `ArrayExample.Entries` | `ArrayExample.Entries` Valor |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value4                       |
| 4                            | value5                       |

O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`. Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto. Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.

O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração. Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:

*missing_value.json*:

```json
{
  "array:entries:3": "value3"
}
```

Em `ConfigureAppConfiguration`:

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

O par chave-valor mostrado na tabela é carregado na configuração.

| Chave             | Valor  |
| :-------------: | :----: |
| array:entries:3 | value3 |

Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.

| Índice `ArrayExample.Entries` | `ArrayExample.Entries` Valor |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**Processamento de matriz JSON**

Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero. No arquivo de configuração a seguir, `subsection` é uma matriz:

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:

| Chave                     | Valor  |
| ----------------------- | :----: |
| json_array:key          | valueA |
| json_array:subsection:0 | valueB |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | valueD |

No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

Após a associação, `JsonArrayExample.Key` contém o valor `valueA`. Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.

| Índice `JsonArrayExample.Subsection` | `JsonArrayExample.Subsection` Valor |
| :---------------------------------: | :---------------------------------: |
| 0                                   | valueB                              |
| 1                                   | valueC                              |
| 2                                   | valueD                              |

## <a name="custom-configuration-provider"></a>Provedor de Configuração personalizado

O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).

O provedor tem as seguintes características:

* O banco de dados EF na memória é usado para fins de demonstração. Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.
* O provedor lê uma tabela de banco de dados na configuração na inicialização. O provedor não consulta o banco de dados em uma base por chave.
* O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.

Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.

*Models/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. O provedor de configuração inicializa o banco de dados quando ele está vazio.

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a>Acessar a configuração durante a inicialização

Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`. Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:

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

Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Acessar a configuração em uma página do Razor Pages ou exibição do MVC

Para acessar definições de configuração em uma página do Razor Pages ou uma exibição do MVC, adicione [usando diretiva](xref:mvc/views/razor#using) ([referência de C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespace Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e injete <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou na exibição.

Em uma página do Razor:

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

Em uma exibição do MVC:

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

## <a name="add-configuration-from-an-external-assembly"></a>Adicionar configuração de um assembly externo

Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo. Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/configuration/options>

::: moniker-end

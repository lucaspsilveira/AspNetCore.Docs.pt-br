---
title: Provedor de configuração do cofre chave do Azure em ASP.NET Núcleo
author: rick-anderson
description: Saiba como usar o Provedor de Configuração do Cofre chave do Azure para configurar um aplicativo usando pares de valor de nome carregados em tempo de execução.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228160"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Provedor de configuração do cofre chave do Azure em ASP.NET Núcleo

Por [Andrew Stanton-Nurse](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

Este documento explica como usar o Provedor de Configuração [do Cofre chave do Microsoft Azure](https://azure.microsoft.com/services/key-vault/) para carregar os valores de configuração do aplicativo a partir dos segredos do Azure Key Vault. O Azure Key Vault é um serviço baseado em nuvem que auxilia na salvaguarda de chaves e segredos criptográficos usados por aplicativos e serviços. Cenários comuns para usar o Azure Key Vault com aplicativos ASP.NET Core incluem:

* Controlando o acesso a dados de configuração confidenciais.
* Atendendo ao requisito para FIPS 140-2 Módulos de segurança de hardware validados (HSM's) ao armazenar dados de configuração.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pacotes

Adicione uma referência de pacote ao pacote [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)

## <a name="sample-app"></a>Aplicativo de exemplo

O aplicativo de exemplo é executado em `#define` qualquer um dos dois modos determinados pela declaração na parte superior do arquivo *Program.cs:*

* `Certificate`&ndash; Demonstra o uso de um ID do Cliente do Azure Key Vault e do certificado X.509 para acessar segredos armazenados no Azure Key Vault. Esta versão da amostra pode ser executada a partir de qualquer local, implantada no Azure App Service ou em qualquer host capaz de atender a um aplicativo ASP.NET Core.
* `Managed`&ndash; Demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo no Azure Key Vault com autenticação Azure AD sem credenciais armazenadas no código ou configuração do aplicativo. Ao usar identidades gerenciadas para autenticar, não é necessário um ID e senha do aplicativo Azure AD (Client Secret). A `Managed` versão da amostra deve ser implantada no Azure. Siga as orientações na [seção Usar as identidades gerenciadas para a seção de recursos do Azure.](#use-managed-identities-for-azure-resources)

Para obter mais informações sobre como configurar um aplicativo`#define`de <xref:index#preprocessor-directives-in-sample-code>exemplo usando diretivas de pré-processador (), consulte .

## <a name="secret-storage-in-the-development-environment"></a>Armazenamento secreto no ambiente de desenvolvimento

Defina segredos localmente usando a [ferramenta Secret Manager](xref:security/app-secrets). Quando o aplicativo de exemplo é executado na máquina local no ambiente Dedesenvolvimento, segredos são carregados da loja local Secret Manager.

A ferramenta Gerenciador `<UserSecretsId>` Secreto requer uma propriedade no arquivo de projeto do aplicativo. Defina o`{GUID}`valor da propriedade ( ) para qualquer GUID único:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Segredos são criados como pares de valor de nome. Valores hierárquicos `:` (seções de configuração) usam um (cólon) como separador em [nomes de chave de configuração ASP.NET Core.](xref:fundamentals/configuration/index)

O Manager Secreto é usado a partir de um shell `{SECRET NAME}` de comando `{SECRET VALUE}` aberto à raiz de [conteúdo](xref:fundamentals/index#content-root)do projeto, onde está o nome e é o valor:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Quando esses segredos são armazenados no Azure Key Vault no armazenamento Secreto no `_dev` ambiente Produção com `_prod`a seção [Azure Key Vault,](#secret-storage-in-the-production-environment-with-azure-key-vault) o sufixo é alterado para . O sufixo fornece uma sugestão visual na saída do aplicativo indicando a origem dos valores de configuração.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Armazenamento secreto no ambiente de produção com o Azure Key Vault

As instruções fornecidas pelo [Quickstart: Defina e recupere um segredo do Azure Key Vault usando](/azure/key-vault/quick-create-cli) o tópico Azure CLI são resumidas aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de amostra. Consulte o tema para mais detalhes.

1. Abra o shell do Azure Cloud usando qualquer um dos seguintes métodos no [portal Azure](https://portal.azure.com/):

   * Selecione **Experimente** no canto superior direito de um bloco de código. Use a seqüência de pesquisa "Azure CLI" na caixa de texto.
   * Abra o Cloud Shell no seu navegador com o botão **Launch Cloud Shell.**
   * Selecione o botão **Cloud Shell** no menu no canto superior direito do portal Azure.

   Para obter mais informações, consulte [Azure CLI](/cli/azure/) e [Visão Geral do Azure Cloud Shell](/azure/cloud-shell/overview).

1. Se você ainda não estiver autenticado, `az login` faça login com o comando.

1. Crie um grupo de recursos `{RESOURCE GROUP NAME}` com o seguinte comando, onde `{LOCATION}` está o nome do grupo de recursos para o novo grupo de recursos e é a região do Azure (datacenter):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Crie um cofre chave no grupo de `{KEY VAULT NAME}` recursos com o seguinte comando, onde está o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (datacenter):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Crie segredos no cofre de chaves como pares de valor de nome.

   Os nomes secretos do Azure Key Vault estão limitados a caracteres e traços alfanuméricos. Valores hierárquicos (seções de configuração) usam `--` (dois traços) como separador. Os cólons, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core,](xref:fundamentals/configuration/index)não são permitidos em nomes secretos do cofre de chaves. Portanto, dois traços são usados e trocados por um cólon quando os segredos são carregados na configuração do aplicativo.

   Os seguintes segredos são para uso com o aplicativo de amostra. Os valores `_prod` incluem um sufixo `_dev` para distingui-los dos valores de sufixo carregados no ambiente de desenvolvimento dos Segredos do Usuário. Substitua pelo `{KEY VAULT NAME}` nome do cofre de chaves que você criou na etapa anterior:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Use o ID do aplicativo e o certificado X.509 para aplicativos não hospedados no Azure

Configure o Azure AD, o Azure Key Vault e o aplicativo para usar um ID do Aplicativo de Diretório Ativo do Azure e um certificado X.509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**. Para obter mais informações, consulte [Sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Embora o uso de um ID de aplicativo e certificado X.509 seja suportado para aplicativos hospedados no Azure, recomendamos o uso [de identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure. As identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.

O aplicativo de exemplo usa um ID de `#define` aplicativo e um certificado X.509 quando a declaração na parte superior do arquivo *Program.cs* é definida como `Certificate`.

1. Crie um certificado de arquivo PKCS#12 *(.pfx).* As opções para criar certificados incluem [MakeCert no Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).
1. Instale o certificado na loja de certificados pessoais do usuário atual. Marcar a chave como exportável é opcional. Observe a impressão digital do certificado, que é usada mais tarde neste processo.
1. Exporte o certificado de arquivo PKCS#12 *(.pfx)* como um certificado codificado pelo DER *(.cer).*
1. Registre o aplicativo com o Azure AD (**Registros de aplicativos).**
1. Faça o upload do certificado codificado pelo DER *(.cer)* para o Azure AD:
   1. Selecione o aplicativo no Azure AD.
   1. Navegue até **Certificados & segredos**.
   1. Selecione **o certificado Upload** para carregar o certificado, que contém a chave pública. Um certificado *.cer,* *.pem*ou *.crt* é aceitável.
1. Armazene o nome do cofre chave, iD do aplicativo e impressão digital do certificado no arquivo *appsettings.json* do aplicativo.
1. Navegue até **os cofres key** no portal Azure.
1. Selecione o cofre-chave que você criou no armazenamento Secreto no ambiente Produção com a seção [Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)
1. Selecione **políticas de acesso**.
1. Selecione **Adicionar política de acesso**.
1. Abra **permissões secretas** e forneça ao aplicativo permissões **get** and **list.**
1. Selecione **Selecionar o principal** e selecione o aplicativo registrado pelo nome. Escolha o botão **Selecionar**.
1. Selecione **OK**.
1. Clique em **Salvar**.
1. Implante o aplicativo.

O `Certificate` aplicativo de exemplo obtém seus valores de `IConfigurationRoot` configuração com o mesmo nome do nome secreto:

* Valores não hierárquicos: O valor para `SecretName` é obtido com `config["SecretName"]`.
* Valores hierárquicos (seções): Use `:` (cólon) notação ou o método de `GetSection` extensão. Use qualquer uma dessas abordagens para obter o valor de configuração:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

O certificado X.509 é gerenciado pelo sistema operacional. O aplicativo <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> chama com valores fornecidos pelo arquivo *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Valores de exemplo:

* Nome do cofre da chave:`contosovault`
* ID do aplicativo:`627e911e-43cc-61d4-992e-12db9c81b413`
* Impressão digital do certificado:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json:*

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Quando você executa o aplicativo, uma página da web mostra os valores secretos carregados. No ambiente desenvolvimento, valores `_dev` secretos carregam com o sufixo. No ambiente Produção, os valores carregam com o `_prod` sufixo.

## <a name="use-managed-identities-for-azure-resources"></a>Use identidades gerenciadas para recursos do Azure

**Um aplicativo implantado no Azure** pode tirar proveito das [identidades gerenciadas para os recursos do Azure,](/azure/active-directory/managed-identities-azure-resources/overview)o que permite que o aplicativo se autentique com o Azure Key Vault usando a autenticação Azure AD sem credenciais (ID de aplicativo e Senha/Client Secret) armazenadas no aplicativo.

O aplicativo de exemplo usa identidades gerenciadas `#define` para recursos do Azure `Managed`quando a declaração na parte superior do arquivo *Program.cs* é definida como .

Digite o nome do cofre no arquivo *appsettings.json* do aplicativo. O aplicativo de exemplo não requer um ID de aplicativo `Managed` e senha (Client Secret) quando definido para a versão, para que você possa ignorar essas entradas de configuração. O aplicativo é implantado no Azure, e o Azure autentica o aplicativo para acessar o Azure Key Vault apenas usando o nome do cofre armazenado no arquivo *appsettings.json.*

Implante o aplicativo de exemplo no Azure App Service.

Um aplicativo implantado no Azure App Service é automaticamente registrado no Azure AD quando o serviço é criado. Obtenha o ID do objeto a partir da implantação para uso no comando a seguir. O ID do objeto é mostrado no portal Azure no painel **Identidade** do Serviço de Aplicativo.

Usando o Azure CLI e o Object ID `list` `get` do aplicativo, forneça ao aplicativo e permissões para acessar o cofre principal:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Reinicie o aplicativo** usando a Cli, PowerShell ou o portal Azure.

O aplicativo de exemplo:

* Cria uma instância `AzureServiceTokenProvider` da classe sem uma seqüência de conexão. Quando uma seqüência de conexões não é fornecida, o provedor tenta obter um token de acesso a partir de identidades gerenciadas para recursos do Azure.
* Um <xref:Microsoft.Azure.KeyVault.KeyVaultClient> novo é criado `AzureServiceTokenProvider` com o retorno de chamada de token de instância.
* A <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instância é usada com <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> uma implementação padrão que carrega todos os`--`valores secretos e substitui traços duplos ( ) por colunas (`:`) em nomes-chave.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Valor de exemplo do nome do cofre da chave:`contosovault`
    
*appsettings.json:*

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Quando você executa o aplicativo, uma página da web mostra os valores secretos carregados. No ambiente Desenvolvimento, os `_dev` valores secretos têm o sufixo porque são fornecidos pelo User Secrets. No ambiente Produção, os valores carregam com o `_prod` sufixo porque são fornecidos pelo Azure Key Vault.

Se você `Access denied` receber um erro, confirme se o aplicativo está registrado no Azure AD e forneceu acesso ao cofre principal. Confirme que reiniciou o serviço no Azure.

Para obter informações sobre o uso do provedor com uma identidade gerenciada e um pipeline Azure DevOps, consulte [Criar uma conexão de serviço do Azure Resource Manager a uma VM com uma identidade de serviço gerenciada](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="configuration-options"></a>Opções de configuração

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>pode aceitar <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>um:

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Propriedade         | Descrição |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>para usar para recuperar valores. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>exemplo usado para controlar o carregamento secreto. |
| `ReloadInterval` | `Timespan`esperar entre as tentativas de sondagem do cofre principal para mudanças. O valor `null` padrão é (a configuração não é recarregada). |
| `Vault`          | Uri do cofre-chave. |

## <a name="use-a-key-name-prefix"></a>Use um prefixo de nome de chave

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fornece uma sobrecarga que aceita <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>uma implementação de , o que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração. Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo. Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.

> [!WARNING]
> Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou para colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos *de produção)* no mesmo cofre. Recomendamos que diferentes aplicativos e ambientes de desenvolvimento/produção usem cofres-chave separados para isolar ambientes de aplicativos para o mais alto nível de segurança.

No exemplo a seguir, um segredo é estabelecido no cofre-chave (e usando `5000-AppSecret` a ferramenta Secret Manager para o ambiente de desenvolvimento) para (períodos não são permitidos em nomes secretos do cofre chave). Este segredo representa um segredo de aplicativo para a versão 5.0.0.0 do aplicativo. Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre `5100-AppSecret`de chaves (e usando a ferramenta Secret Manager) para . Cada versão do aplicativo carrega seu valor `AppSecret`secreto versionado em sua configuração como , desmontando a versão como ele carrega o segredo.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>é chamado com <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>um costume:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

A <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementação reage aos prefixos da versão de segredos para carregar o segredo adequado na configuração:

* `Load`carrega um segredo quando seu nome começa com o prefixo. Outros segredos não estão carregados.
* `GetKey`:
  * Remove o prefixo do nome secreto.
  * Substitui dois traços em qualquer `KeyDelimiter`nome pelo , que é o delimitador usado na configuração (geralmente um cólon). Azure Key Vault não permite um cólon em nomes secretos.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

O `Load` método é chamado por um algoritmo de provedor que itera através dos segredos do cofre para encontrar os que têm o prefixo da versão. Quando um prefixo `Load`de versão é `GetKey` encontrado com , o algoritmo usa o método para retornar o nome de configuração do nome secreto. Ele retira o prefixo da versão do nome do segredo e retorna o resto do nome secreto para carregar nos pares de valor de nome de configuração do aplicativo.

Quando essa abordagem for implementada:

1. A versão do aplicativo especificada no arquivo de projeto do aplicativo. No exemplo a seguir, a versão `5.0.0.0`do aplicativo é definida como:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Confirme `<UserSecretsId>` se uma propriedade está presente no `{GUID}` arquivo de projeto do aplicativo, onde está um GUID fornecido pelo usuário:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Salve os seguintes segredos localmente com a [ferramenta Secret Manager](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Segredos são salvos no Azure Key Vault usando os seguintes comandos azure CLI:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Quando o aplicativo é executado, os segredos do cofre chave são carregados. O segredo `5000-AppSecret` da seqüência é compatível com a versão do aplicativo`5.0.0.0`especificada no arquivo de projeto do aplicativo ( ).

1. A versão, `5000` (com o painel), é despojada do nome da chave. Em todo o aplicativo, `AppSecret` a configuração de leitura com a chave carrega o valor secreto.

1. Se a versão do aplicativo for alterada `5.1.0.0` no arquivo do projeto e o `5.1.0.0_secret_value_dev` aplicativo for executado `5.1.0.0_secret_value_prod` novamente, o valor secreto devolvido está no ambiente de Desenvolvimento e na Produção.

> [!NOTE]
> Você também pode <xref:Microsoft.Azure.KeyVault.KeyVaultClient> fornecer <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>sua própria implementação para . Um cliente personalizado permite compartilhar uma única instância do cliente através do aplicativo.

## <a name="bind-an-array-to-a-class"></a>Associar uma matriz a uma classe

O provedor é capaz de ler valores de configuração em uma matriz para vincular a uma matriz POCO.

Ao ler a partir de uma fonte`:`de configuração que permite que as teclas contenham separadores`:0:` `:1:`de &hellip; `:{n}:`cólon ( ), um segmento de chave numérica é usado para distinguir as teclas que compõem uma matriz ( , , ). Para obter mais informações, consulte [Configuração: Vincule uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

As chaves do Cofre azure não podem usar um cólon como separador. A abordagem descrita neste tópico utiliza`--`traços duplos ( ) como um separador para valores hierárquicos (seções). As teclas de array são armazenadas no Azure Key`--0--`Vault `--1--` &hellip; `--{n}--`com traços duplos e segmentos-chave numéricos (, ).

Examine a seguinte configuração do provedor de registro [Serilog](https://serilog.net/) fornecida por um arquivo JSON. Existem dois literais `WriteTo` de objeto definidos na matriz que refletem dois *dissipadores*serilog , que descrevem destinos para saída de registro:

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

A configuração mostrada no arquivo JSON anterior é armazenada`--`no Azure Key Vault usando notação de traço duplo ( ) e segmentos numéricos:

| Chave | Valor |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Recarregar segredos

Segredos são guardados até que `IConfigurationRoot.Reload()` seja chamado. Segredos expirados, desativados e atualizados no cofre de `Reload` chaves não são respeitados pelo aplicativo até que sejam executados.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Segredos desativados e expirados

Segredos desativados e <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>expirados jogam um . Para evitar que o aplicativo seja jogado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desativado ou expirado.

## <a name="troubleshoot"></a>Solucionar problemas

Quando o aplicativo falha em carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infra-estrutura ASP.NET Core Logging](xref:fundamentals/logging/index). As seguintes condições impedirão que a configuração seja carregada:

* O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.
* O cofre chave não existe no Cofre Azure Key.
* O aplicativo não está autorizado a acessar o cofre principal.
* A política de acesso `Get` `List` não inclui e permissões.
* No cofre de chaves, os dados de configuração (par de valor de nome) são incorretamente nomeados, ausentes, desativados ou expirados.
* O aplicativo tem o nome`KeyVaultName`do cofre de chave errado`AzureADApplicationId`( ), Azure AD`AzureADCertThumbprint`Application Id ( ), ou Azure AD certificate thumbprint ().
* A chave de configuração (nome) está incorreta no aplicativo para o valor que você está tentando carregar.
* Ao adicionar a diretiva de acesso para o aplicativo ao cofre de chaves, a diretiva foi criada, mas o botão **Salvar** não foi selecionado na ia **de políticas de acesso.**

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Documentação do Cofre chave](/azure/key-vault/)
* [Como gerar e transferir chaves protegidas pelo HSM para o Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET](/azure/key-vault/quick-create-net)
* [Tutorial: Como usar o Azure Key Vault com a máquina virtual do Azure com Windows no .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este documento explica como usar o Provedor de Configuração [do Cofre chave do Microsoft Azure](https://azure.microsoft.com/services/key-vault/) para carregar os valores de configuração do aplicativo a partir dos segredos do Azure Key Vault. O Azure Key Vault é um serviço baseado em nuvem que auxilia na salvaguarda de chaves e segredos criptográficos usados por aplicativos e serviços. Cenários comuns para usar o Azure Key Vault com aplicativos ASP.NET Core incluem:

* Controlando o acesso a dados de configuração confidenciais.
* Atendendo ao requisito para FIPS 140-2 Módulos de segurança de hardware validados (HSM's) ao armazenar dados de configuração.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pacotes

Adicione uma referência de pacote ao pacote [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)

## <a name="sample-app"></a>Aplicativo de exemplo

O aplicativo de exemplo é executado em `#define` qualquer um dos dois modos determinados pela declaração na parte superior do arquivo *Program.cs:*

* `Certificate`&ndash; Demonstra o uso de um ID do Cliente do Azure Key Vault e do certificado X.509 para acessar segredos armazenados no Azure Key Vault. Esta versão da amostra pode ser executada a partir de qualquer local, implantada no Azure App Service ou em qualquer host capaz de atender a um aplicativo ASP.NET Core.
* `Managed`&ndash; Demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo no Azure Key Vault com autenticação Azure AD sem credenciais armazenadas no código ou configuração do aplicativo. Ao usar identidades gerenciadas para autenticar, não é necessário um ID e senha do aplicativo Azure AD (Client Secret). A `Managed` versão da amostra deve ser implantada no Azure. Siga as orientações na [seção Usar as identidades gerenciadas para a seção de recursos do Azure.](#use-managed-identities-for-azure-resources)

Para obter mais informações sobre como configurar um aplicativo`#define`de <xref:index#preprocessor-directives-in-sample-code>exemplo usando diretivas de pré-processador (), consulte .

## <a name="secret-storage-in-the-development-environment"></a>Armazenamento secreto no ambiente de desenvolvimento

Defina segredos localmente usando a [ferramenta Secret Manager](xref:security/app-secrets). Quando o aplicativo de exemplo é executado na máquina local no ambiente Dedesenvolvimento, segredos são carregados da loja local Secret Manager.

A ferramenta Gerenciador `<UserSecretsId>` Secreto requer uma propriedade no arquivo de projeto do aplicativo. Defina o`{GUID}`valor da propriedade ( ) para qualquer GUID único:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Segredos são criados como pares de valor de nome. Valores hierárquicos `:` (seções de configuração) usam um (cólon) como separador em [nomes de chave de configuração ASP.NET Core.](xref:fundamentals/configuration/index)

O Manager Secreto é usado a partir de um shell `{SECRET NAME}` de comando `{SECRET VALUE}` aberto à raiz de [conteúdo](xref:fundamentals/index#content-root)do projeto, onde está o nome e é o valor:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Quando esses segredos são armazenados no Azure Key Vault no armazenamento Secreto no `_dev` ambiente Produção com `_prod`a seção [Azure Key Vault,](#secret-storage-in-the-production-environment-with-azure-key-vault) o sufixo é alterado para . O sufixo fornece uma sugestão visual na saída do aplicativo indicando a origem dos valores de configuração.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Armazenamento secreto no ambiente de produção com o Azure Key Vault

As instruções fornecidas pelo [Quickstart: Defina e recupere um segredo do Azure Key Vault usando](/azure/key-vault/quick-create-cli) o tópico Azure CLI são resumidas aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de amostra. Consulte o tema para mais detalhes.

1. Abra o shell do Azure Cloud usando qualquer um dos seguintes métodos no [portal Azure](https://portal.azure.com/):

   * Selecione **Experimente** no canto superior direito de um bloco de código. Use a seqüência de pesquisa "Azure CLI" na caixa de texto.
   * Abra o Cloud Shell no seu navegador com o botão **Launch Cloud Shell.**
   * Selecione o botão **Cloud Shell** no menu no canto superior direito do portal Azure.

   Para obter mais informações, consulte [Azure CLI](/cli/azure/) e [Visão Geral do Azure Cloud Shell](/azure/cloud-shell/overview).

1. Se você ainda não estiver autenticado, `az login` faça login com o comando.

1. Crie um grupo de recursos `{RESOURCE GROUP NAME}` com o seguinte comando, onde `{LOCATION}` está o nome do grupo de recursos para o novo grupo de recursos e é a região do Azure (datacenter):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Crie um cofre chave no grupo de `{KEY VAULT NAME}` recursos com o seguinte comando, onde está o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (datacenter):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Crie segredos no cofre de chaves como pares de valor de nome.

   Os nomes secretos do Azure Key Vault estão limitados a caracteres e traços alfanuméricos. Valores hierárquicos (seções de configuração) usam `--` (dois traços) como separador. Os cólons, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core,](xref:fundamentals/configuration/index)não são permitidos em nomes secretos do cofre de chaves. Portanto, dois traços são usados e trocados por um cólon quando os segredos são carregados na configuração do aplicativo.

   Os seguintes segredos são para uso com o aplicativo de amostra. Os valores `_prod` incluem um sufixo `_dev` para distingui-los dos valores de sufixo carregados no ambiente de desenvolvimento dos Segredos do Usuário. Substitua pelo `{KEY VAULT NAME}` nome do cofre de chaves que você criou na etapa anterior:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Use o ID do aplicativo e o certificado X.509 para aplicativos não hospedados no Azure

Configure o Azure AD, o Azure Key Vault e o aplicativo para usar um ID do Aplicativo de Diretório Ativo do Azure e um certificado X.509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**. Para obter mais informações, consulte [Sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Embora o uso de um ID de aplicativo e certificado X.509 seja suportado para aplicativos hospedados no Azure, recomendamos o uso [de identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure. As identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.

O aplicativo de exemplo usa um ID de `#define` aplicativo e um certificado X.509 quando a declaração na parte superior do arquivo *Program.cs* é definida como `Certificate`.

1. Crie um certificado de arquivo PKCS#12 *(.pfx).* As opções para criar certificados incluem [MakeCert no Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).
1. Instale o certificado na loja de certificados pessoais do usuário atual. Marcar a chave como exportável é opcional. Observe a impressão digital do certificado, que é usada mais tarde neste processo.
1. Exporte o certificado de arquivo PKCS#12 *(.pfx)* como um certificado codificado pelo DER *(.cer).*
1. Registre o aplicativo com o Azure AD (**Registros de aplicativos).**
1. Faça o upload do certificado codificado pelo DER *(.cer)* para o Azure AD:
   1. Selecione o aplicativo no Azure AD.
   1. Navegue até **Certificados & segredos**.
   1. Selecione **o certificado Upload** para carregar o certificado, que contém a chave pública. Um certificado *.cer,* *.pem*ou *.crt* é aceitável.
1. Armazene o nome do cofre chave, iD do aplicativo e impressão digital do certificado no arquivo *appsettings.json* do aplicativo.
1. Navegue até **os cofres key** no portal Azure.
1. Selecione o cofre-chave que você criou no armazenamento Secreto no ambiente Produção com a seção [Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)
1. Selecione **políticas de acesso**.
1. Selecione **Adicionar política de acesso**.
1. Abra **permissões secretas** e forneça ao aplicativo permissões **get** and **list.**
1. Selecione **Selecionar o principal** e selecione o aplicativo registrado pelo nome. Escolha o botão **Selecionar**.
1. Selecione **OK**.
1. Clique em **Salvar**.
1. Implante o aplicativo.

O `Certificate` aplicativo de exemplo obtém seus valores de `IConfigurationRoot` configuração com o mesmo nome do nome secreto:

* Valores não hierárquicos: O valor para `SecretName` é obtido com `config["SecretName"]`.
* Valores hierárquicos (seções): Use `:` (cólon) notação ou o método de `GetSection` extensão. Use qualquer uma dessas abordagens para obter o valor de configuração:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

O certificado X.509 é gerenciado pelo sistema operacional. O aplicativo <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> chama com valores fornecidos pelo arquivo *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Valores de exemplo:

* Nome do cofre da chave:`contosovault`
* ID do aplicativo:`627e911e-43cc-61d4-992e-12db9c81b413`
* Impressão digital do certificado:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json:*

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Quando você executa o aplicativo, uma página da web mostra os valores secretos carregados. No ambiente desenvolvimento, valores `_dev` secretos carregam com o sufixo. No ambiente Produção, os valores carregam com o `_prod` sufixo.

## <a name="use-managed-identities-for-azure-resources"></a>Use identidades gerenciadas para recursos do Azure

**Um aplicativo implantado no Azure** pode tirar proveito das [identidades gerenciadas para os recursos do Azure,](/azure/active-directory/managed-identities-azure-resources/overview)o que permite que o aplicativo se autentique com o Azure Key Vault usando a autenticação Azure AD sem credenciais (ID de aplicativo e Senha/Client Secret) armazenadas no aplicativo.

O aplicativo de exemplo usa identidades gerenciadas `#define` para recursos do Azure `Managed`quando a declaração na parte superior do arquivo *Program.cs* é definida como .

Digite o nome do cofre no arquivo *appsettings.json* do aplicativo. O aplicativo de exemplo não requer um ID de aplicativo `Managed` e senha (Client Secret) quando definido para a versão, para que você possa ignorar essas entradas de configuração. O aplicativo é implantado no Azure, e o Azure autentica o aplicativo para acessar o Azure Key Vault apenas usando o nome do cofre armazenado no arquivo *appsettings.json.*

Implante o aplicativo de exemplo no Azure App Service.

Um aplicativo implantado no Azure App Service é automaticamente registrado no Azure AD quando o serviço é criado. Obtenha o ID do objeto a partir da implantação para uso no comando a seguir. O ID do objeto é mostrado no portal Azure no painel **Identidade** do Serviço de Aplicativo.

Usando o Azure CLI e o Object ID `list` `get` do aplicativo, forneça ao aplicativo e permissões para acessar o cofre principal:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Reinicie o aplicativo** usando a Cli, PowerShell ou o portal Azure.

O aplicativo de exemplo:

* Cria uma instância `AzureServiceTokenProvider` da classe sem uma seqüência de conexão. Quando uma seqüência de conexões não é fornecida, o provedor tenta obter um token de acesso a partir de identidades gerenciadas para recursos do Azure.
* Um <xref:Microsoft.Azure.KeyVault.KeyVaultClient> novo é criado `AzureServiceTokenProvider` com o retorno de chamada de token de instância.
* A <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instância é usada com <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> uma implementação padrão que carrega todos os`--`valores secretos e substitui traços duplos ( ) por colunas (`:`) em nomes-chave.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Valor de exemplo do nome do cofre da chave:`contosovault`
    
*appsettings.json:*

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Quando você executa o aplicativo, uma página da web mostra os valores secretos carregados. No ambiente Desenvolvimento, os `_dev` valores secretos têm o sufixo porque são fornecidos pelo User Secrets. No ambiente Produção, os valores carregam com o `_prod` sufixo porque são fornecidos pelo Azure Key Vault.

Se você `Access denied` receber um erro, confirme se o aplicativo está registrado no Azure AD e forneceu acesso ao cofre principal. Confirme que reiniciou o serviço no Azure.

Para obter informações sobre o uso do provedor com uma identidade gerenciada e um pipeline Azure DevOps, consulte [Criar uma conexão de serviço do Azure Resource Manager a uma VM com uma identidade de serviço gerenciada](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="use-a-key-name-prefix"></a>Use um prefixo de nome de chave

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fornece uma sobrecarga que aceita <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>uma implementação de , o que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração. Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo. Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.

> [!WARNING]
> Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou para colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos *de produção)* no mesmo cofre. Recomendamos que diferentes aplicativos e ambientes de desenvolvimento/produção usem cofres-chave separados para isolar ambientes de aplicativos para o mais alto nível de segurança.

No exemplo a seguir, um segredo é estabelecido no cofre-chave (e usando `5000-AppSecret` a ferramenta Secret Manager para o ambiente de desenvolvimento) para (períodos não são permitidos em nomes secretos do cofre chave). Este segredo representa um segredo de aplicativo para a versão 5.0.0.0 do aplicativo. Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre `5100-AppSecret`de chaves (e usando a ferramenta Secret Manager) para . Cada versão do aplicativo carrega seu valor `AppSecret`secreto versionado em sua configuração como , desmontando a versão como ele carrega o segredo.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>é chamado com <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>um costume:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

A <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementação reage aos prefixos da versão de segredos para carregar o segredo adequado na configuração:

* `Load`carrega um segredo quando seu nome começa com o prefixo. Outros segredos não estão carregados.
* `GetKey`:
  * Remove o prefixo do nome secreto.
  * Substitui dois traços em qualquer `KeyDelimiter`nome pelo , que é o delimitador usado na configuração (geralmente um cólon). Azure Key Vault não permite um cólon em nomes secretos.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

O `Load` método é chamado por um algoritmo de provedor que itera através dos segredos do cofre para encontrar os que têm o prefixo da versão. Quando um prefixo `Load`de versão é `GetKey` encontrado com , o algoritmo usa o método para retornar o nome de configuração do nome secreto. Ele retira o prefixo da versão do nome do segredo e retorna o resto do nome secreto para carregar nos pares de valor de nome de configuração do aplicativo.

Quando essa abordagem for implementada:

1. A versão do aplicativo especificada no arquivo de projeto do aplicativo. No exemplo a seguir, a versão `5.0.0.0`do aplicativo é definida como:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Confirme `<UserSecretsId>` se uma propriedade está presente no `{GUID}` arquivo de projeto do aplicativo, onde está um GUID fornecido pelo usuário:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Salve os seguintes segredos localmente com a [ferramenta Secret Manager](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Segredos são salvos no Azure Key Vault usando os seguintes comandos azure CLI:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Quando o aplicativo é executado, os segredos do cofre chave são carregados. O segredo `5000-AppSecret` da seqüência é compatível com a versão do aplicativo`5.0.0.0`especificada no arquivo de projeto do aplicativo ( ).

1. A versão, `5000` (com o painel), é despojada do nome da chave. Em todo o aplicativo, `AppSecret` a configuração de leitura com a chave carrega o valor secreto.

1. Se a versão do aplicativo for alterada `5.1.0.0` no arquivo do projeto e o `5.1.0.0_secret_value_dev` aplicativo for executado `5.1.0.0_secret_value_prod` novamente, o valor secreto devolvido está no ambiente de Desenvolvimento e na Produção.

> [!NOTE]
> Você também pode <xref:Microsoft.Azure.KeyVault.KeyVaultClient> fornecer <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>sua própria implementação para . Um cliente personalizado permite compartilhar uma única instância do cliente através do aplicativo.

## <a name="bind-an-array-to-a-class"></a>Associar uma matriz a uma classe

O provedor é capaz de ler valores de configuração em uma matriz para vincular a uma matriz POCO.

Ao ler a partir de uma fonte`:`de configuração que permite que as teclas contenham separadores`:0:` `:1:`de &hellip; `:{n}:`cólon ( ), um segmento de chave numérica é usado para distinguir as teclas que compõem uma matriz ( , , ). Para obter mais informações, consulte [Configuração: Vincule uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

As chaves do Cofre azure não podem usar um cólon como separador. A abordagem descrita neste tópico utiliza`--`traços duplos ( ) como um separador para valores hierárquicos (seções). As teclas de array são armazenadas no Azure Key`--0--`Vault `--1--` &hellip; `--{n}--`com traços duplos e segmentos-chave numéricos (, ).

Examine a seguinte configuração do provedor de registro [Serilog](https://serilog.net/) fornecida por um arquivo JSON. Existem dois literais `WriteTo` de objeto definidos na matriz que refletem dois *dissipadores*serilog , que descrevem destinos para saída de registro:

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

A configuração mostrada no arquivo JSON anterior é armazenada`--`no Azure Key Vault usando notação de traço duplo ( ) e segmentos numéricos:

| Chave | Valor |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Recarregar segredos

Segredos são guardados até que `IConfigurationRoot.Reload()` seja chamado. Segredos expirados, desativados e atualizados no cofre de `Reload` chaves não são respeitados pelo aplicativo até que sejam executados.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Segredos desativados e expirados

Segredos desativados e <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>expirados jogam um . Para evitar que o aplicativo seja jogado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desativado ou expirado.

## <a name="troubleshoot"></a>Solucionar problemas

Quando o aplicativo falha em carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infra-estrutura ASP.NET Core Logging](xref:fundamentals/logging/index). As seguintes condições impedirão que a configuração seja carregada:

* O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.
* O cofre chave não existe no Cofre Azure Key.
* O aplicativo não está autorizado a acessar o cofre principal.
* A política de acesso `Get` `List` não inclui e permissões.
* No cofre de chaves, os dados de configuração (par de valor de nome) são incorretamente nomeados, ausentes, desativados ou expirados.
* O aplicativo tem o nome`KeyVaultName`do cofre de chave errado`AzureADApplicationId`( ), Azure AD`AzureADCertThumbprint`Application Id ( ), ou Azure AD certificate thumbprint ().
* A chave de configuração (nome) está incorreta no aplicativo para o valor que você está tentando carregar.
* Ao adicionar a diretiva de acesso para o aplicativo ao cofre de chaves, a diretiva foi criada, mas o botão **Salvar** não foi selecionado na ia **de políticas de acesso.**

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Documentação do Cofre chave](/azure/key-vault/)
* [Como gerar e transferir chaves protegidas pelo HSM para o Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET](/azure/key-vault/quick-create-net)
* [Tutorial: Como usar o Azure Key Vault com a máquina virtual do Azure com Windows no .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end


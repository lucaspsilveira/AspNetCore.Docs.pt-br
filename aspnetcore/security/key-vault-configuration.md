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
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="b5a53-103">Provedor de configuração do cofre chave do Azure em ASP.NET Núcleo</span><span class="sxs-lookup"><span data-stu-id="b5a53-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="b5a53-104">Por [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="b5a53-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5a53-105">Este documento explica como usar o Provedor de Configuração [do Cofre chave do Microsoft Azure](https://azure.microsoft.com/services/key-vault/) para carregar os valores de configuração do aplicativo a partir dos segredos do Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b5a53-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="b5a53-106">O Azure Key Vault é um serviço baseado em nuvem que auxilia na salvaguarda de chaves e segredos criptográficos usados por aplicativos e serviços.</span><span class="sxs-lookup"><span data-stu-id="b5a53-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="b5a53-107">Cenários comuns para usar o Azure Key Vault com aplicativos ASP.NET Core incluem:</span><span class="sxs-lookup"><span data-stu-id="b5a53-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="b5a53-108">Controlando o acesso a dados de configuração confidenciais.</span><span class="sxs-lookup"><span data-stu-id="b5a53-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="b5a53-109">Atendendo ao requisito para FIPS 140-2 Módulos de segurança de hardware validados (HSM's) ao armazenar dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="b5a53-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="b5a53-110">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b5a53-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="b5a53-111">Pacotes</span><span class="sxs-lookup"><span data-stu-id="b5a53-111">Packages</span></span>

<span data-ttu-id="b5a53-112">Adicione uma referência de pacote ao pacote [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)</span><span class="sxs-lookup"><span data-stu-id="b5a53-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="b5a53-113">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="b5a53-113">Sample app</span></span>

<span data-ttu-id="b5a53-114">O aplicativo de exemplo é executado em `#define` qualquer um dos dois modos determinados pela declaração na parte superior do arquivo *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="b5a53-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="b5a53-115">`Certificate`&ndash; Demonstra o uso de um ID do Cliente do Azure Key Vault e do certificado X.509 para acessar segredos armazenados no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b5a53-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="b5a53-116">Esta versão da amostra pode ser executada a partir de qualquer local, implantada no Azure App Service ou em qualquer host capaz de atender a um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b5a53-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="b5a53-117">`Managed`&ndash; Demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo no Azure Key Vault com autenticação Azure AD sem credenciais armazenadas no código ou configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="b5a53-118">Ao usar identidades gerenciadas para autenticar, não é necessário um ID e senha do aplicativo Azure AD (Client Secret).</span><span class="sxs-lookup"><span data-stu-id="b5a53-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="b5a53-119">A `Managed` versão da amostra deve ser implantada no Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="b5a53-120">Siga as orientações na [seção Usar as identidades gerenciadas para a seção de recursos do Azure.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="b5a53-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="b5a53-121">Para obter mais informações sobre como configurar um aplicativo`#define`de <xref:index#preprocessor-directives-in-sample-code>exemplo usando diretivas de pré-processador (), consulte .</span><span class="sxs-lookup"><span data-stu-id="b5a53-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="b5a53-122">Armazenamento secreto no ambiente de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="b5a53-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="b5a53-123">Defina segredos localmente usando a [ferramenta Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b5a53-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="b5a53-124">Quando o aplicativo de exemplo é executado na máquina local no ambiente Dedesenvolvimento, segredos são carregados da loja local Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="b5a53-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="b5a53-125">A ferramenta Gerenciador `<UserSecretsId>` Secreto requer uma propriedade no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="b5a53-126">Defina o`{GUID}`valor da propriedade ( ) para qualquer GUID único:</span><span class="sxs-lookup"><span data-stu-id="b5a53-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="b5a53-127">Segredos são criados como pares de valor de nome.</span><span class="sxs-lookup"><span data-stu-id="b5a53-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="b5a53-128">Valores hierárquicos `:` (seções de configuração) usam um (cólon) como separador em [nomes de chave de configuração ASP.NET Core.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="b5a53-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="b5a53-129">O Manager Secreto é usado a partir de um shell `{SECRET NAME}` de comando `{SECRET VALUE}` aberto à raiz de [conteúdo](xref:fundamentals/index#content-root)do projeto, onde está o nome e é o valor:</span><span class="sxs-lookup"><span data-stu-id="b5a53-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="b5a53-130">Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="b5a53-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="b5a53-131">Quando esses segredos são armazenados no Azure Key Vault no armazenamento Secreto no `_dev` ambiente Produção com `_prod`a seção [Azure Key Vault,](#secret-storage-in-the-production-environment-with-azure-key-vault) o sufixo é alterado para .</span><span class="sxs-lookup"><span data-stu-id="b5a53-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="b5a53-132">O sufixo fornece uma sugestão visual na saída do aplicativo indicando a origem dos valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="b5a53-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="b5a53-133">Armazenamento secreto no ambiente de produção com o Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5a53-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="b5a53-134">As instruções fornecidas pelo [Quickstart: Defina e recupere um segredo do Azure Key Vault usando](/azure/key-vault/quick-create-cli) o tópico Azure CLI são resumidas aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de amostra.</span><span class="sxs-lookup"><span data-stu-id="b5a53-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="b5a53-135">Consulte o tema para mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="b5a53-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="b5a53-136">Abra o shell do Azure Cloud usando qualquer um dos seguintes métodos no [portal Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="b5a53-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="b5a53-137">Selecione **Experimente** no canto superior direito de um bloco de código.</span><span class="sxs-lookup"><span data-stu-id="b5a53-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="b5a53-138">Use a seqüência de pesquisa "Azure CLI" na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="b5a53-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="b5a53-139">Abra o Cloud Shell no seu navegador com o botão **Launch Cloud Shell.**</span><span class="sxs-lookup"><span data-stu-id="b5a53-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="b5a53-140">Selecione o botão **Cloud Shell** no menu no canto superior direito do portal Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="b5a53-141">Para obter mais informações, consulte [Azure CLI](/cli/azure/) e [Visão Geral do Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="b5a53-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="b5a53-142">Se você ainda não estiver autenticado, `az login` faça login com o comando.</span><span class="sxs-lookup"><span data-stu-id="b5a53-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="b5a53-143">Crie um grupo de recursos `{RESOURCE GROUP NAME}` com o seguinte comando, onde `{LOCATION}` está o nome do grupo de recursos para o novo grupo de recursos e é a região do Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="b5a53-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="b5a53-144">Crie um cofre chave no grupo de `{KEY VAULT NAME}` recursos com o seguinte comando, onde está o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="b5a53-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="b5a53-145">Crie segredos no cofre de chaves como pares de valor de nome.</span><span class="sxs-lookup"><span data-stu-id="b5a53-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="b5a53-146">Os nomes secretos do Azure Key Vault estão limitados a caracteres e traços alfanuméricos.</span><span class="sxs-lookup"><span data-stu-id="b5a53-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="b5a53-147">Valores hierárquicos (seções de configuração) usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="b5a53-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="b5a53-148">Os cólons, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core,](xref:fundamentals/configuration/index)não são permitidos em nomes secretos do cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="b5a53-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="b5a53-149">Portanto, dois traços são usados e trocados por um cólon quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="b5a53-150">Os seguintes segredos são para uso com o aplicativo de amostra.</span><span class="sxs-lookup"><span data-stu-id="b5a53-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="b5a53-151">Os valores `_prod` incluem um sufixo `_dev` para distingui-los dos valores de sufixo carregados no ambiente de desenvolvimento dos Segredos do Usuário.</span><span class="sxs-lookup"><span data-stu-id="b5a53-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="b5a53-152">Substitua pelo `{KEY VAULT NAME}` nome do cofre de chaves que você criou na etapa anterior:</span><span class="sxs-lookup"><span data-stu-id="b5a53-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="b5a53-153">Use o ID do aplicativo e o certificado X.509 para aplicativos não hospedados no Azure</span><span class="sxs-lookup"><span data-stu-id="b5a53-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="b5a53-154">Configure o Azure AD, o Azure Key Vault e o aplicativo para usar um ID do Aplicativo de Diretório Ativo do Azure e um certificado X.509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="b5a53-155">Para obter mais informações, consulte [Sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="b5a53-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="b5a53-156">Embora o uso de um ID de aplicativo e certificado X.509 seja suportado para aplicativos hospedados no Azure, recomendamos o uso [de identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="b5a53-157">As identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="b5a53-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="b5a53-158">O aplicativo de exemplo usa um ID de `#define` aplicativo e um certificado X.509 quando a declaração na parte superior do arquivo *Program.cs* é definida como `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="b5a53-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="b5a53-159">Crie um certificado de arquivo PKCS#12 *(.pfx).*</span><span class="sxs-lookup"><span data-stu-id="b5a53-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="b5a53-160">As opções para criar certificados incluem [MakeCert no Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="b5a53-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="b5a53-161">Instale o certificado na loja de certificados pessoais do usuário atual.</span><span class="sxs-lookup"><span data-stu-id="b5a53-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="b5a53-162">Marcar a chave como exportável é opcional.</span><span class="sxs-lookup"><span data-stu-id="b5a53-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="b5a53-163">Observe a impressão digital do certificado, que é usada mais tarde neste processo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="b5a53-164">Exporte o certificado de arquivo PKCS#12 *(.pfx)* como um certificado codificado pelo DER *(.cer).*</span><span class="sxs-lookup"><span data-stu-id="b5a53-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="b5a53-165">Registre o aplicativo com o Azure AD (**Registros de aplicativos).**</span><span class="sxs-lookup"><span data-stu-id="b5a53-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="b5a53-166">Faça o upload do certificado codificado pelo DER *(.cer)* para o Azure AD:</span><span class="sxs-lookup"><span data-stu-id="b5a53-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="b5a53-167">Selecione o aplicativo no Azure AD.</span><span class="sxs-lookup"><span data-stu-id="b5a53-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="b5a53-168">Navegue até **Certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="b5a53-169">Selecione **o certificado Upload** para carregar o certificado, que contém a chave pública.</span><span class="sxs-lookup"><span data-stu-id="b5a53-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="b5a53-170">Um certificado *.cer,* *.pem*ou *.crt* é aceitável.</span><span class="sxs-lookup"><span data-stu-id="b5a53-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="b5a53-171">Armazene o nome do cofre chave, iD do aplicativo e impressão digital do certificado no arquivo *appsettings.json* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="b5a53-172">Navegue até **os cofres key** no portal Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="b5a53-173">Selecione o cofre-chave que você criou no armazenamento Secreto no ambiente Produção com a seção [Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)</span><span class="sxs-lookup"><span data-stu-id="b5a53-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="b5a53-174">Selecione **políticas de acesso**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="b5a53-175">Selecione **Adicionar política de acesso**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="b5a53-176">Abra **permissões secretas** e forneça ao aplicativo permissões **get** and **list.**</span><span class="sxs-lookup"><span data-stu-id="b5a53-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="b5a53-177">Selecione **Selecionar o principal** e selecione o aplicativo registrado pelo nome.</span><span class="sxs-lookup"><span data-stu-id="b5a53-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="b5a53-178">Escolha o botão **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="b5a53-179">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-179">Select **OK**.</span></span>
1. <span data-ttu-id="b5a53-180">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-180">Select **Save**.</span></span>
1. <span data-ttu-id="b5a53-181">Implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-181">Deploy the app.</span></span>

<span data-ttu-id="b5a53-182">O `Certificate` aplicativo de exemplo obtém seus valores de `IConfigurationRoot` configuração com o mesmo nome do nome secreto:</span><span class="sxs-lookup"><span data-stu-id="b5a53-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="b5a53-183">Valores não hierárquicos: O valor para `SecretName` é obtido com `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="b5a53-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="b5a53-184">Valores hierárquicos (seções): Use `:` (cólon) notação ou o método de `GetSection` extensão.</span><span class="sxs-lookup"><span data-stu-id="b5a53-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="b5a53-185">Use qualquer uma dessas abordagens para obter o valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="b5a53-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="b5a53-186">O certificado X.509 é gerenciado pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="b5a53-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="b5a53-187">O aplicativo <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> chama com valores fornecidos pelo arquivo *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="b5a53-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="b5a53-188">Valores de exemplo:</span><span class="sxs-lookup"><span data-stu-id="b5a53-188">Example values:</span></span>

* <span data-ttu-id="b5a53-189">Nome do cofre da chave:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="b5a53-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="b5a53-190">ID do aplicativo:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="b5a53-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="b5a53-191">Impressão digital do certificado:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="b5a53-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="b5a53-192">*appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="b5a53-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="b5a53-193">Quando você executa o aplicativo, uma página da web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="b5a53-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="b5a53-194">No ambiente desenvolvimento, valores `_dev` secretos carregam com o sufixo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="b5a53-195">No ambiente Produção, os valores carregam com o `_prod` sufixo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="b5a53-196">Use identidades gerenciadas para recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="b5a53-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="b5a53-197">**Um aplicativo implantado no Azure** pode tirar proveito das [identidades gerenciadas para os recursos do Azure,](/azure/active-directory/managed-identities-azure-resources/overview)o que permite que o aplicativo se autentique com o Azure Key Vault usando a autenticação Azure AD sem credenciais (ID de aplicativo e Senha/Client Secret) armazenadas no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="b5a53-198">O aplicativo de exemplo usa identidades gerenciadas `#define` para recursos do Azure `Managed`quando a declaração na parte superior do arquivo *Program.cs* é definida como .</span><span class="sxs-lookup"><span data-stu-id="b5a53-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="b5a53-199">Digite o nome do cofre no arquivo *appsettings.json* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="b5a53-200">O aplicativo de exemplo não requer um ID de aplicativo `Managed` e senha (Client Secret) quando definido para a versão, para que você possa ignorar essas entradas de configuração.</span><span class="sxs-lookup"><span data-stu-id="b5a53-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="b5a53-201">O aplicativo é implantado no Azure, e o Azure autentica o aplicativo para acessar o Azure Key Vault apenas usando o nome do cofre armazenado no arquivo *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="b5a53-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="b5a53-202">Implante o aplicativo de exemplo no Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b5a53-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="b5a53-203">Um aplicativo implantado no Azure App Service é automaticamente registrado no Azure AD quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="b5a53-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="b5a53-204">Obtenha o ID do objeto a partir da implantação para uso no comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="b5a53-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="b5a53-205">O ID do objeto é mostrado no portal Azure no painel **Identidade** do Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="b5a53-206">Usando o Azure CLI e o Object ID `list` `get` do aplicativo, forneça ao aplicativo e permissões para acessar o cofre principal:</span><span class="sxs-lookup"><span data-stu-id="b5a53-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="b5a53-207">**Reinicie o aplicativo** usando a Cli, PowerShell ou o portal Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="b5a53-208">O aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="b5a53-208">The sample app:</span></span>

* <span data-ttu-id="b5a53-209">Cria uma instância `AzureServiceTokenProvider` da classe sem uma seqüência de conexão.</span><span class="sxs-lookup"><span data-stu-id="b5a53-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="b5a53-210">Quando uma seqüência de conexões não é fornecida, o provedor tenta obter um token de acesso a partir de identidades gerenciadas para recursos do Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="b5a53-211">Um <xref:Microsoft.Azure.KeyVault.KeyVaultClient> novo é criado `AzureServiceTokenProvider` com o retorno de chamada de token de instância.</span><span class="sxs-lookup"><span data-stu-id="b5a53-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="b5a53-212">A <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instância é usada com <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> uma implementação padrão que carrega todos os`--`valores secretos e substitui traços duplos ( ) por colunas (`:`) em nomes-chave.</span><span class="sxs-lookup"><span data-stu-id="b5a53-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="b5a53-213">Valor de exemplo do nome do cofre da chave:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="b5a53-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="b5a53-214">*appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="b5a53-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="b5a53-215">Quando você executa o aplicativo, uma página da web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="b5a53-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="b5a53-216">No ambiente Desenvolvimento, os `_dev` valores secretos têm o sufixo porque são fornecidos pelo User Secrets.</span><span class="sxs-lookup"><span data-stu-id="b5a53-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="b5a53-217">No ambiente Produção, os valores carregam com o `_prod` sufixo porque são fornecidos pelo Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b5a53-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="b5a53-218">Se você `Access denied` receber um erro, confirme se o aplicativo está registrado no Azure AD e forneceu acesso ao cofre principal.</span><span class="sxs-lookup"><span data-stu-id="b5a53-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="b5a53-219">Confirme que reiniciou o serviço no Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="b5a53-220">Para obter informações sobre o uso do provedor com uma identidade gerenciada e um pipeline Azure DevOps, consulte [Criar uma conexão de serviço do Azure Resource Manager a uma VM com uma identidade de serviço gerenciada](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="b5a53-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="b5a53-221">Opções de configuração</span><span class="sxs-lookup"><span data-stu-id="b5a53-221">Configuration options</span></span>

<span data-ttu-id="b5a53-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>pode aceitar <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>um:</span><span class="sxs-lookup"><span data-stu-id="b5a53-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="b5a53-223">Propriedade</span><span class="sxs-lookup"><span data-stu-id="b5a53-223">Property</span></span>         | <span data-ttu-id="b5a53-224">Descrição</span><span class="sxs-lookup"><span data-stu-id="b5a53-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="b5a53-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>para usar para recuperar valores.</span><span class="sxs-lookup"><span data-stu-id="b5a53-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="b5a53-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>exemplo usado para controlar o carregamento secreto.</span><span class="sxs-lookup"><span data-stu-id="b5a53-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="b5a53-227">`Timespan`esperar entre as tentativas de sondagem do cofre principal para mudanças.</span><span class="sxs-lookup"><span data-stu-id="b5a53-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="b5a53-228">O valor `null` padrão é (a configuração não é recarregada).</span><span class="sxs-lookup"><span data-stu-id="b5a53-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="b5a53-229">Uri do cofre-chave.</span><span class="sxs-lookup"><span data-stu-id="b5a53-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="b5a53-230">Use um prefixo de nome de chave</span><span class="sxs-lookup"><span data-stu-id="b5a53-230">Use a key name prefix</span></span>

<span data-ttu-id="b5a53-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fornece uma sobrecarga que aceita <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>uma implementação de , o que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="b5a53-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="b5a53-232">Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="b5a53-233">Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="b5a53-234">Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou para colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos *de produção)* no mesmo cofre.</span><span class="sxs-lookup"><span data-stu-id="b5a53-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="b5a53-235">Recomendamos que diferentes aplicativos e ambientes de desenvolvimento/produção usem cofres-chave separados para isolar ambientes de aplicativos para o mais alto nível de segurança.</span><span class="sxs-lookup"><span data-stu-id="b5a53-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="b5a53-236">No exemplo a seguir, um segredo é estabelecido no cofre-chave (e usando `5000-AppSecret` a ferramenta Secret Manager para o ambiente de desenvolvimento) para (períodos não são permitidos em nomes secretos do cofre chave).</span><span class="sxs-lookup"><span data-stu-id="b5a53-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="b5a53-237">Este segredo representa um segredo de aplicativo para a versão 5.0.0.0 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="b5a53-238">Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre `5100-AppSecret`de chaves (e usando a ferramenta Secret Manager) para .</span><span class="sxs-lookup"><span data-stu-id="b5a53-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="b5a53-239">Cada versão do aplicativo carrega seu valor `AppSecret`secreto versionado em sua configuração como , desmontando a versão como ele carrega o segredo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="b5a53-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>é chamado com <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>um costume:</span><span class="sxs-lookup"><span data-stu-id="b5a53-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="b5a53-241">A <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementação reage aos prefixos da versão de segredos para carregar o segredo adequado na configuração:</span><span class="sxs-lookup"><span data-stu-id="b5a53-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="b5a53-242">`Load`carrega um segredo quando seu nome começa com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="b5a53-243">Outros segredos não estão carregados.</span><span class="sxs-lookup"><span data-stu-id="b5a53-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="b5a53-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="b5a53-244">`GetKey`:</span></span>
  * <span data-ttu-id="b5a53-245">Remove o prefixo do nome secreto.</span><span class="sxs-lookup"><span data-stu-id="b5a53-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="b5a53-246">Substitui dois traços em qualquer `KeyDelimiter`nome pelo , que é o delimitador usado na configuração (geralmente um cólon).</span><span class="sxs-lookup"><span data-stu-id="b5a53-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="b5a53-247">Azure Key Vault não permite um cólon em nomes secretos.</span><span class="sxs-lookup"><span data-stu-id="b5a53-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="b5a53-248">O `Load` método é chamado por um algoritmo de provedor que itera através dos segredos do cofre para encontrar os que têm o prefixo da versão.</span><span class="sxs-lookup"><span data-stu-id="b5a53-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="b5a53-249">Quando um prefixo `Load`de versão é `GetKey` encontrado com , o algoritmo usa o método para retornar o nome de configuração do nome secreto.</span><span class="sxs-lookup"><span data-stu-id="b5a53-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="b5a53-250">Ele retira o prefixo da versão do nome do segredo e retorna o resto do nome secreto para carregar nos pares de valor de nome de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="b5a53-251">Quando essa abordagem for implementada:</span><span class="sxs-lookup"><span data-stu-id="b5a53-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="b5a53-252">A versão do aplicativo especificada no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="b5a53-253">No exemplo a seguir, a versão `5.0.0.0`do aplicativo é definida como:</span><span class="sxs-lookup"><span data-stu-id="b5a53-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b5a53-254">Confirme `<UserSecretsId>` se uma propriedade está presente no `{GUID}` arquivo de projeto do aplicativo, onde está um GUID fornecido pelo usuário:</span><span class="sxs-lookup"><span data-stu-id="b5a53-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="b5a53-255">Salve os seguintes segredos localmente com a [ferramenta Secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="b5a53-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="b5a53-256">Segredos são salvos no Azure Key Vault usando os seguintes comandos azure CLI:</span><span class="sxs-lookup"><span data-stu-id="b5a53-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="b5a53-257">Quando o aplicativo é executado, os segredos do cofre chave são carregados.</span><span class="sxs-lookup"><span data-stu-id="b5a53-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="b5a53-258">O segredo `5000-AppSecret` da seqüência é compatível com a versão do aplicativo`5.0.0.0`especificada no arquivo de projeto do aplicativo ( ).</span><span class="sxs-lookup"><span data-stu-id="b5a53-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="b5a53-259">A versão, `5000` (com o painel), é despojada do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="b5a53-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="b5a53-260">Em todo o aplicativo, `AppSecret` a configuração de leitura com a chave carrega o valor secreto.</span><span class="sxs-lookup"><span data-stu-id="b5a53-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="b5a53-261">Se a versão do aplicativo for alterada `5.1.0.0` no arquivo do projeto e o `5.1.0.0_secret_value_dev` aplicativo for executado `5.1.0.0_secret_value_prod` novamente, o valor secreto devolvido está no ambiente de Desenvolvimento e na Produção.</span><span class="sxs-lookup"><span data-stu-id="b5a53-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="b5a53-262">Você também pode <xref:Microsoft.Azure.KeyVault.KeyVaultClient> fornecer <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>sua própria implementação para .</span><span class="sxs-lookup"><span data-stu-id="b5a53-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="b5a53-263">Um cliente personalizado permite compartilhar uma única instância do cliente através do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="b5a53-264">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="b5a53-264">Bind an array to a class</span></span>

<span data-ttu-id="b5a53-265">O provedor é capaz de ler valores de configuração em uma matriz para vincular a uma matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="b5a53-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="b5a53-266">Ao ler a partir de uma fonte`:`de configuração que permite que as teclas contenham separadores`:0:` `:1:`de &hellip; `:{n}:`cólon ( ), um segmento de chave numérica é usado para distinguir as teclas que compõem uma matriz ( , , ).</span><span class="sxs-lookup"><span data-stu-id="b5a53-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="b5a53-267">Para obter mais informações, consulte [Configuração: Vincule uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="b5a53-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="b5a53-268">As chaves do Cofre azure não podem usar um cólon como separador.</span><span class="sxs-lookup"><span data-stu-id="b5a53-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="b5a53-269">A abordagem descrita neste tópico utiliza`--`traços duplos ( ) como um separador para valores hierárquicos (seções).</span><span class="sxs-lookup"><span data-stu-id="b5a53-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="b5a53-270">As teclas de array são armazenadas no Azure Key`--0--`Vault `--1--` &hellip; `--{n}--`com traços duplos e segmentos-chave numéricos (, ).</span><span class="sxs-lookup"><span data-stu-id="b5a53-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="b5a53-271">Examine a seguinte configuração do provedor de registro [Serilog](https://serilog.net/) fornecida por um arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="b5a53-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="b5a53-272">Existem dois literais `WriteTo` de objeto definidos na matriz que refletem dois *dissipadores*serilog , que descrevem destinos para saída de registro:</span><span class="sxs-lookup"><span data-stu-id="b5a53-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="b5a53-273">A configuração mostrada no arquivo JSON anterior é armazenada`--`no Azure Key Vault usando notação de traço duplo ( ) e segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="b5a53-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="b5a53-274">Chave</span><span class="sxs-lookup"><span data-stu-id="b5a53-274">Key</span></span> | <span data-ttu-id="b5a53-275">Valor</span><span class="sxs-lookup"><span data-stu-id="b5a53-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="b5a53-276">Recarregar segredos</span><span class="sxs-lookup"><span data-stu-id="b5a53-276">Reload secrets</span></span>

<span data-ttu-id="b5a53-277">Segredos são guardados até que `IConfigurationRoot.Reload()` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="b5a53-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="b5a53-278">Segredos expirados, desativados e atualizados no cofre de `Reload` chaves não são respeitados pelo aplicativo até que sejam executados.</span><span class="sxs-lookup"><span data-stu-id="b5a53-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="b5a53-279">Segredos desativados e expirados</span><span class="sxs-lookup"><span data-stu-id="b5a53-279">Disabled and expired secrets</span></span>

<span data-ttu-id="b5a53-280">Segredos desativados e <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>expirados jogam um .</span><span class="sxs-lookup"><span data-stu-id="b5a53-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="b5a53-281">Para evitar que o aplicativo seja jogado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desativado ou expirado.</span><span class="sxs-lookup"><span data-stu-id="b5a53-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b5a53-282">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="b5a53-282">Troubleshoot</span></span>

<span data-ttu-id="b5a53-283">Quando o aplicativo falha em carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infra-estrutura ASP.NET Core Logging](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b5a53-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b5a53-284">As seguintes condições impedirão que a configuração seja carregada:</span><span class="sxs-lookup"><span data-stu-id="b5a53-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="b5a53-285">O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="b5a53-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="b5a53-286">O cofre chave não existe no Cofre Azure Key.</span><span class="sxs-lookup"><span data-stu-id="b5a53-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="b5a53-287">O aplicativo não está autorizado a acessar o cofre principal.</span><span class="sxs-lookup"><span data-stu-id="b5a53-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="b5a53-288">A política de acesso `Get` `List` não inclui e permissões.</span><span class="sxs-lookup"><span data-stu-id="b5a53-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="b5a53-289">No cofre de chaves, os dados de configuração (par de valor de nome) são incorretamente nomeados, ausentes, desativados ou expirados.</span><span class="sxs-lookup"><span data-stu-id="b5a53-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="b5a53-290">O aplicativo tem o nome`KeyVaultName`do cofre de chave errado`AzureADApplicationId`( ), Azure AD`AzureADCertThumbprint`Application Id ( ), ou Azure AD certificate thumbprint ().</span><span class="sxs-lookup"><span data-stu-id="b5a53-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="b5a53-291">A chave de configuração (nome) está incorreta no aplicativo para o valor que você está tentando carregar.</span><span class="sxs-lookup"><span data-stu-id="b5a53-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="b5a53-292">Ao adicionar a diretiva de acesso para o aplicativo ao cofre de chaves, a diretiva foi criada, mas o botão **Salvar** não foi selecionado na ia **de políticas de acesso.**</span><span class="sxs-lookup"><span data-stu-id="b5a53-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5a53-293">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b5a53-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="b5a53-294">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5a53-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="b5a53-295">Microsoft Azure: Documentação do Cofre chave</span><span class="sxs-lookup"><span data-stu-id="b5a53-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="b5a53-296">Como gerar e transferir chaves protegidas pelo HSM para o Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5a53-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="b5a53-297">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="b5a53-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="b5a53-298">Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET</span><span class="sxs-lookup"><span data-stu-id="b5a53-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="b5a53-299">Tutorial: Como usar o Azure Key Vault com a máquina virtual do Azure com Windows no .NET</span><span class="sxs-lookup"><span data-stu-id="b5a53-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b5a53-300">Este documento explica como usar o Provedor de Configuração [do Cofre chave do Microsoft Azure](https://azure.microsoft.com/services/key-vault/) para carregar os valores de configuração do aplicativo a partir dos segredos do Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b5a53-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="b5a53-301">O Azure Key Vault é um serviço baseado em nuvem que auxilia na salvaguarda de chaves e segredos criptográficos usados por aplicativos e serviços.</span><span class="sxs-lookup"><span data-stu-id="b5a53-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="b5a53-302">Cenários comuns para usar o Azure Key Vault com aplicativos ASP.NET Core incluem:</span><span class="sxs-lookup"><span data-stu-id="b5a53-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="b5a53-303">Controlando o acesso a dados de configuração confidenciais.</span><span class="sxs-lookup"><span data-stu-id="b5a53-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="b5a53-304">Atendendo ao requisito para FIPS 140-2 Módulos de segurança de hardware validados (HSM's) ao armazenar dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="b5a53-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="b5a53-305">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b5a53-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="b5a53-306">Pacotes</span><span class="sxs-lookup"><span data-stu-id="b5a53-306">Packages</span></span>

<span data-ttu-id="b5a53-307">Adicione uma referência de pacote ao pacote [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)</span><span class="sxs-lookup"><span data-stu-id="b5a53-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="b5a53-308">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="b5a53-308">Sample app</span></span>

<span data-ttu-id="b5a53-309">O aplicativo de exemplo é executado em `#define` qualquer um dos dois modos determinados pela declaração na parte superior do arquivo *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="b5a53-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="b5a53-310">`Certificate`&ndash; Demonstra o uso de um ID do Cliente do Azure Key Vault e do certificado X.509 para acessar segredos armazenados no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b5a53-310">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="b5a53-311">Esta versão da amostra pode ser executada a partir de qualquer local, implantada no Azure App Service ou em qualquer host capaz de atender a um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b5a53-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="b5a53-312">`Managed`&ndash; Demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo no Azure Key Vault com autenticação Azure AD sem credenciais armazenadas no código ou configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-312">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="b5a53-313">Ao usar identidades gerenciadas para autenticar, não é necessário um ID e senha do aplicativo Azure AD (Client Secret).</span><span class="sxs-lookup"><span data-stu-id="b5a53-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="b5a53-314">A `Managed` versão da amostra deve ser implantada no Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="b5a53-315">Siga as orientações na [seção Usar as identidades gerenciadas para a seção de recursos do Azure.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="b5a53-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="b5a53-316">Para obter mais informações sobre como configurar um aplicativo`#define`de <xref:index#preprocessor-directives-in-sample-code>exemplo usando diretivas de pré-processador (), consulte .</span><span class="sxs-lookup"><span data-stu-id="b5a53-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="b5a53-317">Armazenamento secreto no ambiente de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="b5a53-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="b5a53-318">Defina segredos localmente usando a [ferramenta Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b5a53-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="b5a53-319">Quando o aplicativo de exemplo é executado na máquina local no ambiente Dedesenvolvimento, segredos são carregados da loja local Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="b5a53-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="b5a53-320">A ferramenta Gerenciador `<UserSecretsId>` Secreto requer uma propriedade no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="b5a53-321">Defina o`{GUID}`valor da propriedade ( ) para qualquer GUID único:</span><span class="sxs-lookup"><span data-stu-id="b5a53-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="b5a53-322">Segredos são criados como pares de valor de nome.</span><span class="sxs-lookup"><span data-stu-id="b5a53-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="b5a53-323">Valores hierárquicos `:` (seções de configuração) usam um (cólon) como separador em [nomes de chave de configuração ASP.NET Core.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="b5a53-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="b5a53-324">O Manager Secreto é usado a partir de um shell `{SECRET NAME}` de comando `{SECRET VALUE}` aberto à raiz de [conteúdo](xref:fundamentals/index#content-root)do projeto, onde está o nome e é o valor:</span><span class="sxs-lookup"><span data-stu-id="b5a53-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="b5a53-325">Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="b5a53-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="b5a53-326">Quando esses segredos são armazenados no Azure Key Vault no armazenamento Secreto no `_dev` ambiente Produção com `_prod`a seção [Azure Key Vault,](#secret-storage-in-the-production-environment-with-azure-key-vault) o sufixo é alterado para .</span><span class="sxs-lookup"><span data-stu-id="b5a53-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="b5a53-327">O sufixo fornece uma sugestão visual na saída do aplicativo indicando a origem dos valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="b5a53-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="b5a53-328">Armazenamento secreto no ambiente de produção com o Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5a53-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="b5a53-329">As instruções fornecidas pelo [Quickstart: Defina e recupere um segredo do Azure Key Vault usando](/azure/key-vault/quick-create-cli) o tópico Azure CLI são resumidas aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de amostra.</span><span class="sxs-lookup"><span data-stu-id="b5a53-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="b5a53-330">Consulte o tema para mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="b5a53-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="b5a53-331">Abra o shell do Azure Cloud usando qualquer um dos seguintes métodos no [portal Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="b5a53-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="b5a53-332">Selecione **Experimente** no canto superior direito de um bloco de código.</span><span class="sxs-lookup"><span data-stu-id="b5a53-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="b5a53-333">Use a seqüência de pesquisa "Azure CLI" na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="b5a53-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="b5a53-334">Abra o Cloud Shell no seu navegador com o botão **Launch Cloud Shell.**</span><span class="sxs-lookup"><span data-stu-id="b5a53-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="b5a53-335">Selecione o botão **Cloud Shell** no menu no canto superior direito do portal Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="b5a53-336">Para obter mais informações, consulte [Azure CLI](/cli/azure/) e [Visão Geral do Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="b5a53-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="b5a53-337">Se você ainda não estiver autenticado, `az login` faça login com o comando.</span><span class="sxs-lookup"><span data-stu-id="b5a53-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="b5a53-338">Crie um grupo de recursos `{RESOURCE GROUP NAME}` com o seguinte comando, onde `{LOCATION}` está o nome do grupo de recursos para o novo grupo de recursos e é a região do Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="b5a53-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="b5a53-339">Crie um cofre chave no grupo de `{KEY VAULT NAME}` recursos com o seguinte comando, onde está o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="b5a53-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="b5a53-340">Crie segredos no cofre de chaves como pares de valor de nome.</span><span class="sxs-lookup"><span data-stu-id="b5a53-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="b5a53-341">Os nomes secretos do Azure Key Vault estão limitados a caracteres e traços alfanuméricos.</span><span class="sxs-lookup"><span data-stu-id="b5a53-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="b5a53-342">Valores hierárquicos (seções de configuração) usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="b5a53-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="b5a53-343">Os cólons, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core,](xref:fundamentals/configuration/index)não são permitidos em nomes secretos do cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="b5a53-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="b5a53-344">Portanto, dois traços são usados e trocados por um cólon quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="b5a53-345">Os seguintes segredos são para uso com o aplicativo de amostra.</span><span class="sxs-lookup"><span data-stu-id="b5a53-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="b5a53-346">Os valores `_prod` incluem um sufixo `_dev` para distingui-los dos valores de sufixo carregados no ambiente de desenvolvimento dos Segredos do Usuário.</span><span class="sxs-lookup"><span data-stu-id="b5a53-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="b5a53-347">Substitua pelo `{KEY VAULT NAME}` nome do cofre de chaves que você criou na etapa anterior:</span><span class="sxs-lookup"><span data-stu-id="b5a53-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="b5a53-348">Use o ID do aplicativo e o certificado X.509 para aplicativos não hospedados no Azure</span><span class="sxs-lookup"><span data-stu-id="b5a53-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="b5a53-349">Configure o Azure AD, o Azure Key Vault e o aplicativo para usar um ID do Aplicativo de Diretório Ativo do Azure e um certificado X.509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="b5a53-350">Para obter mais informações, consulte [Sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="b5a53-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="b5a53-351">Embora o uso de um ID de aplicativo e certificado X.509 seja suportado para aplicativos hospedados no Azure, recomendamos o uso [de identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="b5a53-352">As identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="b5a53-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="b5a53-353">O aplicativo de exemplo usa um ID de `#define` aplicativo e um certificado X.509 quando a declaração na parte superior do arquivo *Program.cs* é definida como `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="b5a53-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="b5a53-354">Crie um certificado de arquivo PKCS#12 *(.pfx).*</span><span class="sxs-lookup"><span data-stu-id="b5a53-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="b5a53-355">As opções para criar certificados incluem [MakeCert no Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="b5a53-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="b5a53-356">Instale o certificado na loja de certificados pessoais do usuário atual.</span><span class="sxs-lookup"><span data-stu-id="b5a53-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="b5a53-357">Marcar a chave como exportável é opcional.</span><span class="sxs-lookup"><span data-stu-id="b5a53-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="b5a53-358">Observe a impressão digital do certificado, que é usada mais tarde neste processo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="b5a53-359">Exporte o certificado de arquivo PKCS#12 *(.pfx)* como um certificado codificado pelo DER *(.cer).*</span><span class="sxs-lookup"><span data-stu-id="b5a53-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="b5a53-360">Registre o aplicativo com o Azure AD (**Registros de aplicativos).**</span><span class="sxs-lookup"><span data-stu-id="b5a53-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="b5a53-361">Faça o upload do certificado codificado pelo DER *(.cer)* para o Azure AD:</span><span class="sxs-lookup"><span data-stu-id="b5a53-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="b5a53-362">Selecione o aplicativo no Azure AD.</span><span class="sxs-lookup"><span data-stu-id="b5a53-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="b5a53-363">Navegue até **Certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="b5a53-364">Selecione **o certificado Upload** para carregar o certificado, que contém a chave pública.</span><span class="sxs-lookup"><span data-stu-id="b5a53-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="b5a53-365">Um certificado *.cer,* *.pem*ou *.crt* é aceitável.</span><span class="sxs-lookup"><span data-stu-id="b5a53-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="b5a53-366">Armazene o nome do cofre chave, iD do aplicativo e impressão digital do certificado no arquivo *appsettings.json* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="b5a53-367">Navegue até **os cofres key** no portal Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="b5a53-368">Selecione o cofre-chave que você criou no armazenamento Secreto no ambiente Produção com a seção [Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)</span><span class="sxs-lookup"><span data-stu-id="b5a53-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="b5a53-369">Selecione **políticas de acesso**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="b5a53-370">Selecione **Adicionar política de acesso**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="b5a53-371">Abra **permissões secretas** e forneça ao aplicativo permissões **get** and **list.**</span><span class="sxs-lookup"><span data-stu-id="b5a53-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="b5a53-372">Selecione **Selecionar o principal** e selecione o aplicativo registrado pelo nome.</span><span class="sxs-lookup"><span data-stu-id="b5a53-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="b5a53-373">Escolha o botão **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="b5a53-374">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-374">Select **OK**.</span></span>
1. <span data-ttu-id="b5a53-375">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="b5a53-375">Select **Save**.</span></span>
1. <span data-ttu-id="b5a53-376">Implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-376">Deploy the app.</span></span>

<span data-ttu-id="b5a53-377">O `Certificate` aplicativo de exemplo obtém seus valores de `IConfigurationRoot` configuração com o mesmo nome do nome secreto:</span><span class="sxs-lookup"><span data-stu-id="b5a53-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="b5a53-378">Valores não hierárquicos: O valor para `SecretName` é obtido com `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="b5a53-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="b5a53-379">Valores hierárquicos (seções): Use `:` (cólon) notação ou o método de `GetSection` extensão.</span><span class="sxs-lookup"><span data-stu-id="b5a53-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="b5a53-380">Use qualquer uma dessas abordagens para obter o valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="b5a53-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="b5a53-381">O certificado X.509 é gerenciado pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="b5a53-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="b5a53-382">O aplicativo <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> chama com valores fornecidos pelo arquivo *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="b5a53-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="b5a53-383">Valores de exemplo:</span><span class="sxs-lookup"><span data-stu-id="b5a53-383">Example values:</span></span>

* <span data-ttu-id="b5a53-384">Nome do cofre da chave:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="b5a53-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="b5a53-385">ID do aplicativo:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="b5a53-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="b5a53-386">Impressão digital do certificado:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="b5a53-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="b5a53-387">*appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="b5a53-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="b5a53-388">Quando você executa o aplicativo, uma página da web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="b5a53-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="b5a53-389">No ambiente desenvolvimento, valores `_dev` secretos carregam com o sufixo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="b5a53-390">No ambiente Produção, os valores carregam com o `_prod` sufixo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="b5a53-391">Use identidades gerenciadas para recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="b5a53-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="b5a53-392">**Um aplicativo implantado no Azure** pode tirar proveito das [identidades gerenciadas para os recursos do Azure,](/azure/active-directory/managed-identities-azure-resources/overview)o que permite que o aplicativo se autentique com o Azure Key Vault usando a autenticação Azure AD sem credenciais (ID de aplicativo e Senha/Client Secret) armazenadas no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="b5a53-393">O aplicativo de exemplo usa identidades gerenciadas `#define` para recursos do Azure `Managed`quando a declaração na parte superior do arquivo *Program.cs* é definida como .</span><span class="sxs-lookup"><span data-stu-id="b5a53-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="b5a53-394">Digite o nome do cofre no arquivo *appsettings.json* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="b5a53-395">O aplicativo de exemplo não requer um ID de aplicativo `Managed` e senha (Client Secret) quando definido para a versão, para que você possa ignorar essas entradas de configuração.</span><span class="sxs-lookup"><span data-stu-id="b5a53-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="b5a53-396">O aplicativo é implantado no Azure, e o Azure autentica o aplicativo para acessar o Azure Key Vault apenas usando o nome do cofre armazenado no arquivo *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="b5a53-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="b5a53-397">Implante o aplicativo de exemplo no Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b5a53-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="b5a53-398">Um aplicativo implantado no Azure App Service é automaticamente registrado no Azure AD quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="b5a53-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="b5a53-399">Obtenha o ID do objeto a partir da implantação para uso no comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="b5a53-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="b5a53-400">O ID do objeto é mostrado no portal Azure no painel **Identidade** do Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="b5a53-401">Usando o Azure CLI e o Object ID `list` `get` do aplicativo, forneça ao aplicativo e permissões para acessar o cofre principal:</span><span class="sxs-lookup"><span data-stu-id="b5a53-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="b5a53-402">**Reinicie o aplicativo** usando a Cli, PowerShell ou o portal Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="b5a53-403">O aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="b5a53-403">The sample app:</span></span>

* <span data-ttu-id="b5a53-404">Cria uma instância `AzureServiceTokenProvider` da classe sem uma seqüência de conexão.</span><span class="sxs-lookup"><span data-stu-id="b5a53-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="b5a53-405">Quando uma seqüência de conexões não é fornecida, o provedor tenta obter um token de acesso a partir de identidades gerenciadas para recursos do Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="b5a53-406">Um <xref:Microsoft.Azure.KeyVault.KeyVaultClient> novo é criado `AzureServiceTokenProvider` com o retorno de chamada de token de instância.</span><span class="sxs-lookup"><span data-stu-id="b5a53-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="b5a53-407">A <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instância é usada com <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> uma implementação padrão que carrega todos os`--`valores secretos e substitui traços duplos ( ) por colunas (`:`) em nomes-chave.</span><span class="sxs-lookup"><span data-stu-id="b5a53-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="b5a53-408">Valor de exemplo do nome do cofre da chave:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="b5a53-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="b5a53-409">*appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="b5a53-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="b5a53-410">Quando você executa o aplicativo, uma página da web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="b5a53-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="b5a53-411">No ambiente Desenvolvimento, os `_dev` valores secretos têm o sufixo porque são fornecidos pelo User Secrets.</span><span class="sxs-lookup"><span data-stu-id="b5a53-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="b5a53-412">No ambiente Produção, os valores carregam com o `_prod` sufixo porque são fornecidos pelo Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b5a53-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="b5a53-413">Se você `Access denied` receber um erro, confirme se o aplicativo está registrado no Azure AD e forneceu acesso ao cofre principal.</span><span class="sxs-lookup"><span data-stu-id="b5a53-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="b5a53-414">Confirme que reiniciou o serviço no Azure.</span><span class="sxs-lookup"><span data-stu-id="b5a53-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="b5a53-415">Para obter informações sobre o uso do provedor com uma identidade gerenciada e um pipeline Azure DevOps, consulte [Criar uma conexão de serviço do Azure Resource Manager a uma VM com uma identidade de serviço gerenciada](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="b5a53-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="b5a53-416">Use um prefixo de nome de chave</span><span class="sxs-lookup"><span data-stu-id="b5a53-416">Use a key name prefix</span></span>

<span data-ttu-id="b5a53-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fornece uma sobrecarga que aceita <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>uma implementação de , o que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="b5a53-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="b5a53-418">Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="b5a53-419">Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="b5a53-420">Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou para colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos *de produção)* no mesmo cofre.</span><span class="sxs-lookup"><span data-stu-id="b5a53-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="b5a53-421">Recomendamos que diferentes aplicativos e ambientes de desenvolvimento/produção usem cofres-chave separados para isolar ambientes de aplicativos para o mais alto nível de segurança.</span><span class="sxs-lookup"><span data-stu-id="b5a53-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="b5a53-422">No exemplo a seguir, um segredo é estabelecido no cofre-chave (e usando `5000-AppSecret` a ferramenta Secret Manager para o ambiente de desenvolvimento) para (períodos não são permitidos em nomes secretos do cofre chave).</span><span class="sxs-lookup"><span data-stu-id="b5a53-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="b5a53-423">Este segredo representa um segredo de aplicativo para a versão 5.0.0.0 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="b5a53-424">Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre `5100-AppSecret`de chaves (e usando a ferramenta Secret Manager) para .</span><span class="sxs-lookup"><span data-stu-id="b5a53-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="b5a53-425">Cada versão do aplicativo carrega seu valor `AppSecret`secreto versionado em sua configuração como , desmontando a versão como ele carrega o segredo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="b5a53-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>é chamado com <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>um costume:</span><span class="sxs-lookup"><span data-stu-id="b5a53-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="b5a53-427">A <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementação reage aos prefixos da versão de segredos para carregar o segredo adequado na configuração:</span><span class="sxs-lookup"><span data-stu-id="b5a53-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="b5a53-428">`Load`carrega um segredo quando seu nome começa com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="b5a53-429">Outros segredos não estão carregados.</span><span class="sxs-lookup"><span data-stu-id="b5a53-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="b5a53-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="b5a53-430">`GetKey`:</span></span>
  * <span data-ttu-id="b5a53-431">Remove o prefixo do nome secreto.</span><span class="sxs-lookup"><span data-stu-id="b5a53-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="b5a53-432">Substitui dois traços em qualquer `KeyDelimiter`nome pelo , que é o delimitador usado na configuração (geralmente um cólon).</span><span class="sxs-lookup"><span data-stu-id="b5a53-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="b5a53-433">Azure Key Vault não permite um cólon em nomes secretos.</span><span class="sxs-lookup"><span data-stu-id="b5a53-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="b5a53-434">O `Load` método é chamado por um algoritmo de provedor que itera através dos segredos do cofre para encontrar os que têm o prefixo da versão.</span><span class="sxs-lookup"><span data-stu-id="b5a53-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="b5a53-435">Quando um prefixo `Load`de versão é `GetKey` encontrado com , o algoritmo usa o método para retornar o nome de configuração do nome secreto.</span><span class="sxs-lookup"><span data-stu-id="b5a53-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="b5a53-436">Ele retira o prefixo da versão do nome do segredo e retorna o resto do nome secreto para carregar nos pares de valor de nome de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="b5a53-437">Quando essa abordagem for implementada:</span><span class="sxs-lookup"><span data-stu-id="b5a53-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="b5a53-438">A versão do aplicativo especificada no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="b5a53-439">No exemplo a seguir, a versão `5.0.0.0`do aplicativo é definida como:</span><span class="sxs-lookup"><span data-stu-id="b5a53-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b5a53-440">Confirme `<UserSecretsId>` se uma propriedade está presente no `{GUID}` arquivo de projeto do aplicativo, onde está um GUID fornecido pelo usuário:</span><span class="sxs-lookup"><span data-stu-id="b5a53-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="b5a53-441">Salve os seguintes segredos localmente com a [ferramenta Secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="b5a53-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="b5a53-442">Segredos são salvos no Azure Key Vault usando os seguintes comandos azure CLI:</span><span class="sxs-lookup"><span data-stu-id="b5a53-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="b5a53-443">Quando o aplicativo é executado, os segredos do cofre chave são carregados.</span><span class="sxs-lookup"><span data-stu-id="b5a53-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="b5a53-444">O segredo `5000-AppSecret` da seqüência é compatível com a versão do aplicativo`5.0.0.0`especificada no arquivo de projeto do aplicativo ( ).</span><span class="sxs-lookup"><span data-stu-id="b5a53-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="b5a53-445">A versão, `5000` (com o painel), é despojada do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="b5a53-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="b5a53-446">Em todo o aplicativo, `AppSecret` a configuração de leitura com a chave carrega o valor secreto.</span><span class="sxs-lookup"><span data-stu-id="b5a53-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="b5a53-447">Se a versão do aplicativo for alterada `5.1.0.0` no arquivo do projeto e o `5.1.0.0_secret_value_dev` aplicativo for executado `5.1.0.0_secret_value_prod` novamente, o valor secreto devolvido está no ambiente de Desenvolvimento e na Produção.</span><span class="sxs-lookup"><span data-stu-id="b5a53-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="b5a53-448">Você também pode <xref:Microsoft.Azure.KeyVault.KeyVaultClient> fornecer <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>sua própria implementação para .</span><span class="sxs-lookup"><span data-stu-id="b5a53-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="b5a53-449">Um cliente personalizado permite compartilhar uma única instância do cliente através do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b5a53-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="b5a53-450">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="b5a53-450">Bind an array to a class</span></span>

<span data-ttu-id="b5a53-451">O provedor é capaz de ler valores de configuração em uma matriz para vincular a uma matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="b5a53-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="b5a53-452">Ao ler a partir de uma fonte`:`de configuração que permite que as teclas contenham separadores`:0:` `:1:`de &hellip; `:{n}:`cólon ( ), um segmento de chave numérica é usado para distinguir as teclas que compõem uma matriz ( , , ).</span><span class="sxs-lookup"><span data-stu-id="b5a53-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="b5a53-453">Para obter mais informações, consulte [Configuração: Vincule uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="b5a53-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="b5a53-454">As chaves do Cofre azure não podem usar um cólon como separador.</span><span class="sxs-lookup"><span data-stu-id="b5a53-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="b5a53-455">A abordagem descrita neste tópico utiliza`--`traços duplos ( ) como um separador para valores hierárquicos (seções).</span><span class="sxs-lookup"><span data-stu-id="b5a53-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="b5a53-456">As teclas de array são armazenadas no Azure Key`--0--`Vault `--1--` &hellip; `--{n}--`com traços duplos e segmentos-chave numéricos (, ).</span><span class="sxs-lookup"><span data-stu-id="b5a53-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="b5a53-457">Examine a seguinte configuração do provedor de registro [Serilog](https://serilog.net/) fornecida por um arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="b5a53-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="b5a53-458">Existem dois literais `WriteTo` de objeto definidos na matriz que refletem dois *dissipadores*serilog , que descrevem destinos para saída de registro:</span><span class="sxs-lookup"><span data-stu-id="b5a53-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="b5a53-459">A configuração mostrada no arquivo JSON anterior é armazenada`--`no Azure Key Vault usando notação de traço duplo ( ) e segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="b5a53-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="b5a53-460">Chave</span><span class="sxs-lookup"><span data-stu-id="b5a53-460">Key</span></span> | <span data-ttu-id="b5a53-461">Valor</span><span class="sxs-lookup"><span data-stu-id="b5a53-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="b5a53-462">Recarregar segredos</span><span class="sxs-lookup"><span data-stu-id="b5a53-462">Reload secrets</span></span>

<span data-ttu-id="b5a53-463">Segredos são guardados até que `IConfigurationRoot.Reload()` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="b5a53-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="b5a53-464">Segredos expirados, desativados e atualizados no cofre de `Reload` chaves não são respeitados pelo aplicativo até que sejam executados.</span><span class="sxs-lookup"><span data-stu-id="b5a53-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="b5a53-465">Segredos desativados e expirados</span><span class="sxs-lookup"><span data-stu-id="b5a53-465">Disabled and expired secrets</span></span>

<span data-ttu-id="b5a53-466">Segredos desativados e <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>expirados jogam um .</span><span class="sxs-lookup"><span data-stu-id="b5a53-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="b5a53-467">Para evitar que o aplicativo seja jogado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desativado ou expirado.</span><span class="sxs-lookup"><span data-stu-id="b5a53-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b5a53-468">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="b5a53-468">Troubleshoot</span></span>

<span data-ttu-id="b5a53-469">Quando o aplicativo falha em carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infra-estrutura ASP.NET Core Logging](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b5a53-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b5a53-470">As seguintes condições impedirão que a configuração seja carregada:</span><span class="sxs-lookup"><span data-stu-id="b5a53-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="b5a53-471">O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="b5a53-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="b5a53-472">O cofre chave não existe no Cofre Azure Key.</span><span class="sxs-lookup"><span data-stu-id="b5a53-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="b5a53-473">O aplicativo não está autorizado a acessar o cofre principal.</span><span class="sxs-lookup"><span data-stu-id="b5a53-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="b5a53-474">A política de acesso `Get` `List` não inclui e permissões.</span><span class="sxs-lookup"><span data-stu-id="b5a53-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="b5a53-475">No cofre de chaves, os dados de configuração (par de valor de nome) são incorretamente nomeados, ausentes, desativados ou expirados.</span><span class="sxs-lookup"><span data-stu-id="b5a53-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="b5a53-476">O aplicativo tem o nome`KeyVaultName`do cofre de chave errado`AzureADApplicationId`( ), Azure AD`AzureADCertThumbprint`Application Id ( ), ou Azure AD certificate thumbprint ().</span><span class="sxs-lookup"><span data-stu-id="b5a53-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="b5a53-477">A chave de configuração (nome) está incorreta no aplicativo para o valor que você está tentando carregar.</span><span class="sxs-lookup"><span data-stu-id="b5a53-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="b5a53-478">Ao adicionar a diretiva de acesso para o aplicativo ao cofre de chaves, a diretiva foi criada, mas o botão **Salvar** não foi selecionado na ia **de políticas de acesso.**</span><span class="sxs-lookup"><span data-stu-id="b5a53-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5a53-479">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b5a53-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="b5a53-480">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5a53-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="b5a53-481">Microsoft Azure: Documentação do Cofre chave</span><span class="sxs-lookup"><span data-stu-id="b5a53-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="b5a53-482">Como gerar e transferir chaves protegidas pelo HSM para o Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5a53-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="b5a53-483">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="b5a53-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="b5a53-484">Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET</span><span class="sxs-lookup"><span data-stu-id="b5a53-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="b5a53-485">Tutorial: Como usar o Azure Key Vault com a máquina virtual do Azure com Windows no .NET</span><span class="sxs-lookup"><span data-stu-id="b5a53-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end


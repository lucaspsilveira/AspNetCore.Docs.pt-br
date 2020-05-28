---
<span data-ttu-id="5479b-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5479b-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5479b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5479b-102">'Blazor'</span></span>
- <span data-ttu-id="5479b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5479b-103">'Identity'</span></span>
- <span data-ttu-id="5479b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5479b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5479b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5479b-105">'Razor'</span></span>
- <span data-ttu-id="5479b-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5479b-106">'SignalR' uid:</span></span> 

---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="5479b-107">Azure Key Vault provedor de configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5479b-107">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="5479b-108">Por [Andrew Stanton-enfermaria](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="5479b-108">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5479b-109">Este documento explica como usar o provedor de configuração do [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para carregar valores de configuração de aplicativo de segredos Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="5479b-109">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="5479b-110">Azure Key Vault é um serviço baseado em nuvem que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços.</span><span class="sxs-lookup"><span data-stu-id="5479b-110">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="5479b-111">Os cenários comuns para usar Azure Key Vault com aplicativos ASP.NET Core incluem:</span><span class="sxs-lookup"><span data-stu-id="5479b-111">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="5479b-112">Controlando o acesso a dados de configuração confidenciais.</span><span class="sxs-lookup"><span data-stu-id="5479b-112">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="5479b-113">Atendendo ao requisito de módulos de segurança de hardware (HSM) validados do FIPS 140-2 nível 2 ao armazenar dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="5479b-113">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="5479b-114">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5479b-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="5479b-115">Pacotes</span><span class="sxs-lookup"><span data-stu-id="5479b-115">Packages</span></span>

<span data-ttu-id="5479b-116">Adicione uma referência de pacote ao pacote [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="5479b-116">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="5479b-117">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="5479b-117">Sample app</span></span>

<span data-ttu-id="5479b-118">O aplicativo de exemplo é executado em um dos dois modos determinados pela `#define` instrução na parte superior do arquivo *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="5479b-118">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="5479b-119">`Certificate`: Demonstra o uso de uma Azure Key Vault ID do cliente e do certificado X. 509 para acessar os segredos armazenados no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="5479b-119">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="5479b-120">Esta versão do exemplo pode ser executada em qualquer local, implantada no serviço Azure App ou em qualquer host capaz de servir um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5479b-120">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="5479b-121">`Managed`: Demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo para Azure Key Vault com a autenticação do Azure ad sem credenciais armazenadas no código ou na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-121">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="5479b-122">Ao usar identidades gerenciadas para autenticar, uma ID de aplicativo do Azure AD e senha (segredo do cliente) não são necessárias.</span><span class="sxs-lookup"><span data-stu-id="5479b-122">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="5479b-123">A `Managed` versão do exemplo deve ser implantada no Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-123">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="5479b-124">Siga as orientações na seção [usar as identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="5479b-124">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="5479b-125">Para obter mais informações sobre como configurar um aplicativo de exemplo usando diretivas de pré-processador ( `#define` ), consulte <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="5479b-125">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="5479b-126">Armazenamento de segredo no ambiente de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="5479b-126">Secret storage in the Development environment</span></span>

<span data-ttu-id="5479b-127">Defina os segredos localmente usando a [ferramenta Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="5479b-127">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="5479b-128">Quando o aplicativo de exemplo é executado no computador local no ambiente de desenvolvimento, os segredos são carregados do repositório do Gerenciador de segredo local.</span><span class="sxs-lookup"><span data-stu-id="5479b-128">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="5479b-129">A ferramenta Gerenciador de segredo requer uma `<UserSecretsId>` propriedade no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-129">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="5479b-130">Defina o valor da propriedade ( `{GUID}` ) para qualquer Guid exclusivo:</span><span class="sxs-lookup"><span data-stu-id="5479b-130">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="5479b-131">Os segredos são criados como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="5479b-131">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="5479b-132">Valores hierárquicos (seções de configuração) usam um `:` (dois-pontos) como um separador em ASP.NET Core nomes de chave de [configuração](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="5479b-132">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="5479b-133">O Gerenciador de segredo é usado em um shell de comando aberto na [raiz do conteúdo](xref:fundamentals/index#content-root)do projeto, em que `{SECRET NAME}` é o nome e `{SECRET VALUE}` é o valor:</span><span class="sxs-lookup"><span data-stu-id="5479b-133">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="5479b-134">Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="5479b-134">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="5479b-135">Quando esses segredos são armazenados em Azure Key Vault no [armazenamento secreto no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção, o `_dev` sufixo é alterado para `_prod` .</span><span class="sxs-lookup"><span data-stu-id="5479b-135">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="5479b-136">O sufixo fornece uma indicação visual na saída do aplicativo que indica a origem dos valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="5479b-136">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="5479b-137">Armazenamento de segredo no ambiente de produção com Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5479b-137">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="5479b-138">As instruções fornecidas pelo guia de [início rápido: definem e recuperam um segredo de Azure Key Vault usando CLI do Azure](/azure/key-vault/quick-create-cli) tópico são resumidos aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="5479b-138">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="5479b-139">Consulte o tópico para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="5479b-139">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="5479b-140">Abra o Azure cloud shell usando qualquer um dos seguintes métodos no [portal do Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="5479b-140">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="5479b-141">Selecione **Experimente** no canto superior direito de um bloco de código.</span><span class="sxs-lookup"><span data-stu-id="5479b-141">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="5479b-142">Use a cadeia de caracteres de pesquisa "CLI do Azure" na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="5479b-142">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="5479b-143">Abra Cloud Shell em seu navegador com o botão **iniciar Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="5479b-143">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="5479b-144">Selecione o botão **Cloud Shell** no menu no canto superior direito do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-144">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="5479b-145">Para obter mais informações, consulte [CLI do Azure](/cli/azure/) e [visão geral do Azure cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="5479b-145">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="5479b-146">Se você ainda não estiver autenticado, entre com o `az login` comando.</span><span class="sxs-lookup"><span data-stu-id="5479b-146">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="5479b-147">Crie um grupo de recursos com o seguinte comando, em que `{RESOURCE GROUP NAME}` é o nome do grupo de recursos para o novo grupo de recursos e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="5479b-147">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="5479b-148">Crie um cofre de chaves no grupo de recursos com o seguinte comando, em que `{KEY VAULT NAME}` é o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="5479b-148">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="5479b-149">Crie segredos no cofre de chaves como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="5479b-149">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="5479b-150">Azure Key Vault nomes de segredo são limitados a caracteres alfanuméricos e traços.</span><span class="sxs-lookup"><span data-stu-id="5479b-150">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="5479b-151">Os valores hierárquicos (seções de configuração) usam `--` (dois traços) como um separador.</span><span class="sxs-lookup"><span data-stu-id="5479b-151">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="5479b-152">Os dois-pontos, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core](xref:fundamentals/configuration/index), não são permitidos em nomes de segredo do cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="5479b-152">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="5479b-153">Portanto, dois traços são usados e alternados por dois-pontos quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-153">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="5479b-154">Os segredos a seguir são para uso com o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="5479b-154">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="5479b-155">Os valores incluem um `_prod` sufixo para distingui-los dos `_dev` valores de sufixo carregados no ambiente de desenvolvimento de segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="5479b-155">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="5479b-156">Substitua `{KEY VAULT NAME}` pelo nome do cofre de chaves que você criou na etapa anterior:</span><span class="sxs-lookup"><span data-stu-id="5479b-156">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="5479b-157">Use a ID do aplicativo e o certificado X. 509 para aplicativos não hospedados no Azure</span><span class="sxs-lookup"><span data-stu-id="5479b-157">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="5479b-158">Configure o Azure AD, Azure Key Vault e o aplicativo para usar uma ID de aplicativo Azure Active Directory e um certificado X. 509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**.</span><span class="sxs-lookup"><span data-stu-id="5479b-158">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="5479b-159">Para obter mais informações, consulte [Sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="5479b-159">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="5479b-160">Embora o uso de uma ID de aplicativo e um certificado X. 509 tenha suporte para aplicativos hospedados no Azure, é recomendável usar [identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-160">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="5479b-161">Identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5479b-161">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="5479b-162">O aplicativo de exemplo usa uma ID de aplicativo e um certificado X. 509 quando a `#define` instrução na parte superior do arquivo *Program.cs* é definida como `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="5479b-162">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="5479b-163">Crie um certificado PKCS # 12 arquivo morto (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="5479b-163">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="5479b-164">As opções para criar certificados incluem [MakeCert no Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="5479b-164">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="5479b-165">Instale o certificado no repositório de certificados pessoal do usuário atual.</span><span class="sxs-lookup"><span data-stu-id="5479b-165">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="5479b-166">Marcar a chave como exportável é opcional.</span><span class="sxs-lookup"><span data-stu-id="5479b-166">Marking the key as exportable is optional.</span></span> <span data-ttu-id="5479b-167">Observe a impressão digital do certificado, que é usada posteriormente neste processo.</span><span class="sxs-lookup"><span data-stu-id="5479b-167">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="5479b-168">Exporte o certificado PKCS # 12 Archive (*. pfx*) como um certificado codificado em der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="5479b-168">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="5479b-169">Registre o aplicativo com o Azure AD (**registros de aplicativo**).</span><span class="sxs-lookup"><span data-stu-id="5479b-169">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="5479b-170">Carregue o certificado codificado em DER (*. cer*) no Azure AD:</span><span class="sxs-lookup"><span data-stu-id="5479b-170">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="5479b-171">Selecione o aplicativo no Azure AD.</span><span class="sxs-lookup"><span data-stu-id="5479b-171">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="5479b-172">Navegue até **certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="5479b-172">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="5479b-173">Selecione **carregar certificado** para carregar o certificado, que contém a chave pública.</span><span class="sxs-lookup"><span data-stu-id="5479b-173">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="5479b-174">Um certificado *. cer*, *. pem*ou *. CRT* é aceitável.</span><span class="sxs-lookup"><span data-stu-id="5479b-174">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="5479b-175">Armazene o nome do cofre de chaves, a ID do aplicativo e a impressão digital do certificado no arquivo *appSettings. JSON* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-175">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="5479b-176">Navegue até **cofres de chaves** na portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-176">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="5479b-177">Selecione o cofre de chaves que você criou no [armazenamento de segredo no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção.</span><span class="sxs-lookup"><span data-stu-id="5479b-177">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="5479b-178">Selecione **políticas de acesso**.</span><span class="sxs-lookup"><span data-stu-id="5479b-178">Select **Access policies**.</span></span>
1. <span data-ttu-id="5479b-179">Selecione **Adicionar política de acesso**.</span><span class="sxs-lookup"><span data-stu-id="5479b-179">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="5479b-180">Abra **permissões de segredo** e forneça ao aplicativo as permissões **Get** e **list** .</span><span class="sxs-lookup"><span data-stu-id="5479b-180">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="5479b-181">Selecione **selecionar entidade de segurança** e selecione o aplicativo registrado por nome.</span><span class="sxs-lookup"><span data-stu-id="5479b-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="5479b-182">Escolha o botão **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="5479b-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="5479b-183">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="5479b-183">Select **OK**.</span></span>
1. <span data-ttu-id="5479b-184">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="5479b-184">Select **Save**.</span></span>
1. <span data-ttu-id="5479b-185">Implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-185">Deploy the app.</span></span>

<span data-ttu-id="5479b-186">O `Certificate` aplicativo de exemplo obtém seus valores de configuração de `IConfigurationRoot` com o mesmo nome que o nome do segredo:</span><span class="sxs-lookup"><span data-stu-id="5479b-186">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="5479b-187">Valores não hierárquicos: o valor para `SecretName` é obtido com `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="5479b-187">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="5479b-188">Valores hierárquicos (seções): `:` notação de uso (dois-pontos) ou o `GetSection` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="5479b-188">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="5479b-189">Use qualquer uma dessas abordagens para obter o valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="5479b-189">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="5479b-190">O certificado X. 509 é gerenciado pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="5479b-190">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="5479b-191">O aplicativo chama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> com valores fornecidos pelo arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="5479b-191">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="5479b-192">Valores de exemplo:</span><span class="sxs-lookup"><span data-stu-id="5479b-192">Example values:</span></span>

* <span data-ttu-id="5479b-193">Nome do cofre de chaves:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="5479b-193">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="5479b-194">ID do aplicativo:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="5479b-194">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="5479b-195">Impressão digital do certificado:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="5479b-195">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="5479b-196">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="5479b-196">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="5479b-197">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="5479b-197">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="5479b-198">No ambiente de desenvolvimento, os valores secretos são carregados com o `_dev` sufixo.</span><span class="sxs-lookup"><span data-stu-id="5479b-198">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="5479b-199">No ambiente de produção, os valores são carregados com o `_prod` sufixo.</span><span class="sxs-lookup"><span data-stu-id="5479b-199">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="5479b-200">Usar identidades gerenciadas para recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="5479b-200">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="5479b-201">**Um aplicativo implantado no Azure** pode aproveitar as [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview), o que permite que o aplicativo se autentique com Azure Key Vault usando a autenticação do Azure ad sem credenciais (ID do aplicativo e senha/segredo do cliente) armazenados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-201">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="5479b-202">O aplicativo de exemplo usa identidades gerenciadas para recursos do Azure quando a `#define` instrução na parte superior do arquivo *Program.cs* é definida como `Managed` .</span><span class="sxs-lookup"><span data-stu-id="5479b-202">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="5479b-203">Insira o nome do cofre no arquivo *appSettings. JSON* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-203">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="5479b-204">O aplicativo de exemplo não requer uma ID de aplicativo e uma senha (segredo do cliente) quando definido para a `Managed` versão, para que você possa ignorar essas entradas de configuração.</span><span class="sxs-lookup"><span data-stu-id="5479b-204">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="5479b-205">O aplicativo é implantado no Azure e o Azure autentica o aplicativo para acessar Azure Key Vault apenas usando o nome do cofre armazenado no arquivo *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="5479b-205">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="5479b-206">Implante o aplicativo de exemplo no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="5479b-206">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="5479b-207">Um aplicativo implantado no serviço Azure App é registrado automaticamente com o Azure AD quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="5479b-207">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="5479b-208">Obtenha a ID de objeto da implantação para uso no comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="5479b-208">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="5479b-209">A ID de objeto é mostrada no portal do Azure no **Identity** painel do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-209">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="5479b-210">Usando CLI do Azure e a ID de objeto do aplicativo, forneça ao aplicativo `list` e `get` permissões para acessar o cofre de chaves:</span><span class="sxs-lookup"><span data-stu-id="5479b-210">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="5479b-211">**Reinicie o aplicativo** usando CLI do Azure, PowerShell ou o portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-211">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="5479b-212">O aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="5479b-212">The sample app:</span></span>

* <span data-ttu-id="5479b-213">Cria uma instância da `AzureServiceTokenProvider` classe sem uma cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="5479b-213">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="5479b-214">Quando uma cadeia de conexão não é fornecida, o provedor tenta obter um token de acesso de identidades gerenciadas para recursos do Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-214">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="5479b-215">Um novo <xref:Microsoft.Azure.KeyVault.KeyVaultClient> é criado com o `AzureServiceTokenProvider` retorno de chamada de token de instância.</span><span class="sxs-lookup"><span data-stu-id="5479b-215">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="5479b-216">A <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instância é usada com uma implementação padrão do <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> que carrega todos os valores secretos e substitui traços duplos ( `--` ) por dois-pontos ( `:` ) em nomes de chave.</span><span class="sxs-lookup"><span data-stu-id="5479b-216">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="5479b-217">Valor de exemplo do nome do cofre de chaves:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="5479b-217">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="5479b-218">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="5479b-218">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="5479b-219">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="5479b-219">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="5479b-220">No ambiente de desenvolvimento, os valores secretos têm o `_dev` sufixo porque são fornecidos por segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="5479b-220">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="5479b-221">No ambiente de produção, os valores são carregados com o `_prod` sufixo porque são fornecidos por Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="5479b-221">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="5479b-222">Se você receber um `Access denied` erro, confirme se o aplicativo está registrado no Azure AD e se forneceu acesso ao cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="5479b-222">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="5479b-223">Confirme que você reiniciou o serviço no Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-223">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="5479b-224">Para obter informações sobre como usar o provedor com uma identidade gerenciada e um pipeline DevOps do Azure, consulte [criar uma conexão de serviço Azure Resource Manager para uma VM com uma identidade de serviço gerenciado](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="5479b-224">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="5479b-225">Opções de configuração</span><span class="sxs-lookup"><span data-stu-id="5479b-225">Configuration options</span></span>

<span data-ttu-id="5479b-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>pode aceitar um <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :</span><span class="sxs-lookup"><span data-stu-id="5479b-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="5479b-227">Propriedade</span><span class="sxs-lookup"><span data-stu-id="5479b-227">Property</span></span>         | <span data-ttu-id="5479b-228">Descrição</span><span class="sxs-lookup"><span data-stu-id="5479b-228">Description</span></span> |
| ---
<span data-ttu-id="5479b-229">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5479b-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5479b-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5479b-230">'Blazor'</span></span>
- <span data-ttu-id="5479b-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5479b-231">'Identity'</span></span>
- <span data-ttu-id="5479b-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5479b-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="5479b-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5479b-233">'Razor'</span></span>
- <span data-ttu-id="5479b-234">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5479b-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5479b-235">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5479b-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5479b-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5479b-236">'Blazor'</span></span>
- <span data-ttu-id="5479b-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5479b-237">'Identity'</span></span>
- <span data-ttu-id="5479b-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5479b-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="5479b-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5479b-239">'Razor'</span></span>
- <span data-ttu-id="5479b-240">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5479b-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5479b-241">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5479b-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5479b-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5479b-242">'Blazor'</span></span>
- <span data-ttu-id="5479b-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5479b-243">'Identity'</span></span>
- <span data-ttu-id="5479b-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5479b-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="5479b-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5479b-245">'Razor'</span></span>
- <span data-ttu-id="5479b-246">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5479b-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5479b-247">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5479b-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5479b-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5479b-248">'Blazor'</span></span>
- <span data-ttu-id="5479b-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5479b-249">'Identity'</span></span>
- <span data-ttu-id="5479b-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5479b-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="5479b-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5479b-251">'Razor'</span></span>
- <span data-ttu-id="5479b-252">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5479b-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5479b-253">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5479b-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5479b-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5479b-254">'Blazor'</span></span>
- <span data-ttu-id="5479b-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5479b-255">'Identity'</span></span>
- <span data-ttu-id="5479b-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5479b-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="5479b-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5479b-257">'Razor'</span></span>
- <span data-ttu-id="5479b-258">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5479b-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5479b-259">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5479b-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5479b-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5479b-260">'Blazor'</span></span>
- <span data-ttu-id="5479b-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5479b-261">'Identity'</span></span>
- <span data-ttu-id="5479b-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5479b-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="5479b-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5479b-263">'Razor'</span></span>
- <span data-ttu-id="5479b-264">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5479b-264">'SignalR' uid:</span></span> 

<span data-ttu-id="5479b-265">-------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5479b-265">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5479b-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5479b-266">'Blazor'</span></span>
- <span data-ttu-id="5479b-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5479b-267">'Identity'</span></span>
- <span data-ttu-id="5479b-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5479b-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="5479b-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5479b-269">'Razor'</span></span>
- <span data-ttu-id="5479b-270">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5479b-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5479b-271">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5479b-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5479b-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5479b-272">'Blazor'</span></span>
- <span data-ttu-id="5479b-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5479b-273">'Identity'</span></span>
- <span data-ttu-id="5479b-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5479b-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="5479b-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5479b-275">'Razor'</span></span>
- <span data-ttu-id="5479b-276">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5479b-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5479b-277">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5479b-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5479b-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5479b-278">'Blazor'</span></span>
- <span data-ttu-id="5479b-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5479b-279">'Identity'</span></span>
- <span data-ttu-id="5479b-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5479b-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="5479b-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5479b-281">'Razor'</span></span>
- <span data-ttu-id="5479b-282">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5479b-282">'SignalR' uid:</span></span> 

<span data-ttu-id="5479b-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> para usar para recuperar valores.</span><span class="sxs-lookup"><span data-stu-id="5479b-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> <span data-ttu-id="5479b-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instância usada para controlar o carregamento de segredo.</span><span class="sxs-lookup"><span data-stu-id="5479b-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> <span data-ttu-id="5479b-285">| | `ReloadInterval` | `Timespan` aguardar entre as tentativas de sondagem do cofre de chaves para alterações.</span><span class="sxs-lookup"><span data-stu-id="5479b-285">| | `ReloadInterval` | `Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="5479b-286">O valor padrão é `null` (a configuração não é recarregada).</span><span class="sxs-lookup"><span data-stu-id="5479b-286">The default value is `null` (configuration isn't reloaded).</span></span> <span data-ttu-id="5479b-287">| | `Vault`          | URI do Key Vault.</span><span class="sxs-lookup"><span data-stu-id="5479b-287">| | `Vault`          | Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="5479b-288">Usar um prefixo de nome de chave</span><span class="sxs-lookup"><span data-stu-id="5479b-288">Use a key name prefix</span></span>

<span data-ttu-id="5479b-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fornece uma sobrecarga que aceita uma implementação do <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="5479b-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="5479b-290">Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-290">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="5479b-291">Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-291">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="5479b-292">Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos de *produção* ) no mesmo cofre.</span><span class="sxs-lookup"><span data-stu-id="5479b-292">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="5479b-293">Recomendamos que aplicativos diferentes e ambientes de desenvolvimento/produção usem cofres de chaves separados para isolar os ambientes de aplicativo para o nível mais alto de segurança.</span><span class="sxs-lookup"><span data-stu-id="5479b-293">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="5479b-294">No exemplo a seguir, um segredo é estabelecido no cofre de chaves (e usando a ferramenta Gerenciador de segredo para o ambiente de desenvolvimento) para `5000-AppSecret` (os períodos não são permitidos em nomes de segredo do cofre de chaves).</span><span class="sxs-lookup"><span data-stu-id="5479b-294">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="5479b-295">Esse segredo representa um segredo do aplicativo para a versão 5.0.0.0 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-295">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="5479b-296">Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre de chaves (e usando a ferramenta Gerenciador de segredo) para o `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="5479b-296">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="5479b-297">Cada versão do aplicativo carrega seu valor secreto com versão em sua configuração como `AppSecret` , eliminando a versão à medida que ela carrega o segredo.</span><span class="sxs-lookup"><span data-stu-id="5479b-297">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="5479b-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>é chamado com um personalizado <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="5479b-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="5479b-299">A <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementação reage aos prefixos de versão dos segredos para carregar o segredo apropriado na configuração:</span><span class="sxs-lookup"><span data-stu-id="5479b-299">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="5479b-300">`Load`carrega um segredo quando seu nome começa com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="5479b-300">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="5479b-301">Outros segredos não são carregados.</span><span class="sxs-lookup"><span data-stu-id="5479b-301">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="5479b-302">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="5479b-302">`GetKey`:</span></span>
  * <span data-ttu-id="5479b-303">Remove o prefixo do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="5479b-303">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="5479b-304">Substitui dois traços em qualquer nome pelo `KeyDelimiter` , que é o delimitador usado na configuração (geralmente um dois-pontos).</span><span class="sxs-lookup"><span data-stu-id="5479b-304">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="5479b-305">Azure Key Vault não permite dois pontos em nomes secretos.</span><span class="sxs-lookup"><span data-stu-id="5479b-305">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="5479b-306">O `Load` método é chamado por um algoritmo de provedor que itera por meio dos segredos do cofre para localizar aqueles que têm o prefixo de versão.</span><span class="sxs-lookup"><span data-stu-id="5479b-306">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="5479b-307">Quando um prefixo de versão é encontrado com `Load` , o algoritmo usa o `GetKey` método para retornar o nome de configuração do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="5479b-307">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="5479b-308">Ele retira o prefixo de versão do nome do segredo e retorna o restante do nome do segredo para carregar nos pares nome-valor da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-308">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="5479b-309">Quando essa abordagem é implementada:</span><span class="sxs-lookup"><span data-stu-id="5479b-309">When this approach is implemented:</span></span>

1. <span data-ttu-id="5479b-310">A versão do aplicativo especificada no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-310">The app's version specified in the app's project file.</span></span> <span data-ttu-id="5479b-311">No exemplo a seguir, a versão do aplicativo é definida como `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="5479b-311">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="5479b-312">Confirme se uma `<UserSecretsId>` propriedade está presente no arquivo de projeto do aplicativo, em que `{GUID}` é um GUID fornecido pelo usuário:</span><span class="sxs-lookup"><span data-stu-id="5479b-312">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="5479b-313">Salve os seguintes segredos localmente com a [ferramenta Gerenciador de segredo](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="5479b-313">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="5479b-314">Os segredos são salvos em Azure Key Vault usando os seguintes comandos CLI do Azure:</span><span class="sxs-lookup"><span data-stu-id="5479b-314">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="5479b-315">Quando o aplicativo é executado, os segredos do cofre de chaves são carregados.</span><span class="sxs-lookup"><span data-stu-id="5479b-315">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="5479b-316">O segredo da cadeia de caracteres para `5000-AppSecret` é correspondido à versão do aplicativo especificada no arquivo de projeto do aplicativo ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="5479b-316">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="5479b-317">A versão, `5000` (com o Dash), é removida do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="5479b-317">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="5479b-318">Em todo o aplicativo, a leitura da configuração com a chave `AppSecret` carrega o valor secreto.</span><span class="sxs-lookup"><span data-stu-id="5479b-318">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="5479b-319">Se a versão do aplicativo for alterada no arquivo de projeto para `5.1.0.0` e o aplicativo for executado novamente, o valor secreto retornado estará `5.1.0.0_secret_value_dev` no ambiente de desenvolvimento e `5.1.0.0_secret_value_prod` em produção.</span><span class="sxs-lookup"><span data-stu-id="5479b-319">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="5479b-320">Você também pode fornecer sua própria <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementação para o <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="5479b-320">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="5479b-321">Um cliente personalizado permite compartilhar uma única instância do cliente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-321">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="5479b-322">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="5479b-322">Bind an array to a class</span></span>

<span data-ttu-id="5479b-323">O provedor é capaz de ler valores de configuração em uma matriz para associação a uma matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="5479b-323">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="5479b-324">Ao ler de uma fonte de configuração que permite que as chaves contenham separadores de dois-pontos ( `:` ), um segmento de chave numérico é usado para distinguir as chaves que compõem uma matriz ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="5479b-324">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="5479b-325">Para obter mais informações, consulte [configuração: associar uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="5479b-325">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="5479b-326">Azure Key Vault chaves não podem usar dois-pontos como separador.</span><span class="sxs-lookup"><span data-stu-id="5479b-326">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="5479b-327">A abordagem descrita neste tópico usa traços duplos ( `--` ) como separador de valores hierárquicos (seções).</span><span class="sxs-lookup"><span data-stu-id="5479b-327">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="5479b-328">As chaves de matriz são armazenadas em Azure Key Vault com traços duplos e segmentos de chave numérica ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="5479b-328">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="5479b-329">Examine a seguinte configuração do provedor de log [Serilog](https://serilog.net/) fornecida por um arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="5479b-329">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="5479b-330">Há dois literais de objeto definidos na `WriteTo` matriz que refletem dois *coletores*Serilog, que descrevem os destinos para a saída de log:</span><span class="sxs-lookup"><span data-stu-id="5479b-330">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="5479b-331">A configuração mostrada no arquivo JSON anterior é armazenada em Azure Key Vault usando a `--` notação de traço duplo () e os segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="5479b-331">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="5479b-332">Chave</span><span class="sxs-lookup"><span data-stu-id="5479b-332">Key</span></span> | <span data-ttu-id="5479b-333">Valor</span><span class="sxs-lookup"><span data-stu-id="5479b-333">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="5479b-334">Recarregar segredos</span><span class="sxs-lookup"><span data-stu-id="5479b-334">Reload secrets</span></span>

<span data-ttu-id="5479b-335">Os segredos são armazenados em cache até que `IConfigurationRoot.Reload()` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="5479b-335">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="5479b-336">Os segredos expirados, desabilitados e atualizados no cofre de chaves não são respeitados pelo aplicativo até que o `Reload` seja executado.</span><span class="sxs-lookup"><span data-stu-id="5479b-336">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="5479b-337">Segredos desabilitados e expirados</span><span class="sxs-lookup"><span data-stu-id="5479b-337">Disabled and expired secrets</span></span>

<span data-ttu-id="5479b-338">Os segredos desabilitados e expirados lançam um <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="5479b-338">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="5479b-339">Para impedir que o aplicativo seja acionado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desabilitado ou expirado.</span><span class="sxs-lookup"><span data-stu-id="5479b-339">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5479b-340">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="5479b-340">Troubleshoot</span></span>

<span data-ttu-id="5479b-341">Quando o aplicativo falha ao carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infraestrutura de log de ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="5479b-341">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="5479b-342">As seguintes condições impedirão que a configuração seja carregada:</span><span class="sxs-lookup"><span data-stu-id="5479b-342">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="5479b-343">O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="5479b-343">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="5479b-344">O cofre de chaves não existe no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="5479b-344">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="5479b-345">O aplicativo não está autorizado a acessar o cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="5479b-345">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="5479b-346">A política de acesso não `Get` inclui `List` permissões e.</span><span class="sxs-lookup"><span data-stu-id="5479b-346">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="5479b-347">No cofre de chaves, os dados de configuração (par nome-valor) são nomeados incorretamente, ausentes, desabilitados ou expirados.</span><span class="sxs-lookup"><span data-stu-id="5479b-347">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="5479b-348">O aplicativo tem o nome do cofre de chaves errado ( `KeyVaultName` ), a ID do aplicativo do Azure AD ( `AzureADApplicationId` ) ou a impressão digital do certificado do Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="5479b-348">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="5479b-349">A chave de configuração (Name) está incorreta no aplicativo para o valor que você está tentando carregar.</span><span class="sxs-lookup"><span data-stu-id="5479b-349">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="5479b-350">Ao adicionar a política de acesso para o aplicativo ao cofre de chaves, a política foi criada, mas o botão **salvar** não foi selecionado na interface do usuário de **políticas de acesso** .</span><span class="sxs-lookup"><span data-stu-id="5479b-350">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5479b-351">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5479b-351">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="5479b-352">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="5479b-352">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="5479b-353">Microsoft Azure: Key Vault documentação</span><span class="sxs-lookup"><span data-stu-id="5479b-353">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="5479b-354">Como gerar e transferir chaves protegidas por HSM para Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5479b-354">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="5479b-355">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="5479b-355">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="5479b-356">Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET</span><span class="sxs-lookup"><span data-stu-id="5479b-356">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="5479b-357">Tutorial: Como usar o Azure Key Vault com a máquina virtual do Azure com Windows no .NET</span><span class="sxs-lookup"><span data-stu-id="5479b-357">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5479b-358">Este documento explica como usar o provedor de configuração do [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para carregar valores de configuração de aplicativo de segredos Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="5479b-358">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="5479b-359">Azure Key Vault é um serviço baseado em nuvem que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços.</span><span class="sxs-lookup"><span data-stu-id="5479b-359">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="5479b-360">Os cenários comuns para usar Azure Key Vault com aplicativos ASP.NET Core incluem:</span><span class="sxs-lookup"><span data-stu-id="5479b-360">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="5479b-361">Controlando o acesso a dados de configuração confidenciais.</span><span class="sxs-lookup"><span data-stu-id="5479b-361">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="5479b-362">Atendendo ao requisito de módulos de segurança de hardware (HSM) validados do FIPS 140-2 nível 2 ao armazenar dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="5479b-362">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="5479b-363">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5479b-363">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="5479b-364">Pacotes</span><span class="sxs-lookup"><span data-stu-id="5479b-364">Packages</span></span>

<span data-ttu-id="5479b-365">Adicione uma referência de pacote ao pacote [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="5479b-365">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="5479b-366">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="5479b-366">Sample app</span></span>

<span data-ttu-id="5479b-367">O aplicativo de exemplo é executado em um dos dois modos determinados pela `#define` instrução na parte superior do arquivo *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="5479b-367">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="5479b-368">`Certificate`: Demonstra o uso de uma Azure Key Vault ID do cliente e do certificado X. 509 para acessar os segredos armazenados no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="5479b-368">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="5479b-369">Esta versão do exemplo pode ser executada em qualquer local, implantada no serviço Azure App ou em qualquer host capaz de servir um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5479b-369">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="5479b-370">`Managed`: Demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo para Azure Key Vault com a autenticação do Azure ad sem credenciais armazenadas no código ou na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-370">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="5479b-371">Ao usar identidades gerenciadas para autenticar, uma ID de aplicativo do Azure AD e senha (segredo do cliente) não são necessárias.</span><span class="sxs-lookup"><span data-stu-id="5479b-371">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="5479b-372">A `Managed` versão do exemplo deve ser implantada no Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-372">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="5479b-373">Siga as orientações na seção [usar as identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="5479b-373">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="5479b-374">Para obter mais informações sobre como configurar um aplicativo de exemplo usando diretivas de pré-processador ( `#define` ), consulte <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="5479b-374">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="5479b-375">Armazenamento de segredo no ambiente de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="5479b-375">Secret storage in the Development environment</span></span>

<span data-ttu-id="5479b-376">Defina os segredos localmente usando a [ferramenta Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="5479b-376">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="5479b-377">Quando o aplicativo de exemplo é executado no computador local no ambiente de desenvolvimento, os segredos são carregados do repositório do Gerenciador de segredo local.</span><span class="sxs-lookup"><span data-stu-id="5479b-377">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="5479b-378">A ferramenta Gerenciador de segredo requer uma `<UserSecretsId>` propriedade no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-378">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="5479b-379">Defina o valor da propriedade ( `{GUID}` ) para qualquer Guid exclusivo:</span><span class="sxs-lookup"><span data-stu-id="5479b-379">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="5479b-380">Os segredos são criados como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="5479b-380">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="5479b-381">Valores hierárquicos (seções de configuração) usam um `:` (dois-pontos) como um separador em ASP.NET Core nomes de chave de [configuração](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="5479b-381">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="5479b-382">O Gerenciador de segredo é usado em um shell de comando aberto na [raiz do conteúdo](xref:fundamentals/index#content-root)do projeto, em que `{SECRET NAME}` é o nome e `{SECRET VALUE}` é o valor:</span><span class="sxs-lookup"><span data-stu-id="5479b-382">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="5479b-383">Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="5479b-383">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="5479b-384">Quando esses segredos são armazenados em Azure Key Vault no [armazenamento secreto no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção, o `_dev` sufixo é alterado para `_prod` .</span><span class="sxs-lookup"><span data-stu-id="5479b-384">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="5479b-385">O sufixo fornece uma indicação visual na saída do aplicativo que indica a origem dos valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="5479b-385">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="5479b-386">Armazenamento de segredo no ambiente de produção com Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5479b-386">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="5479b-387">As instruções fornecidas pelo guia de [início rápido: definem e recuperam um segredo de Azure Key Vault usando CLI do Azure](/azure/key-vault/quick-create-cli) tópico são resumidos aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="5479b-387">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="5479b-388">Consulte o tópico para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="5479b-388">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="5479b-389">Abra o Azure cloud shell usando qualquer um dos seguintes métodos no [portal do Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="5479b-389">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="5479b-390">Selecione **Experimente** no canto superior direito de um bloco de código.</span><span class="sxs-lookup"><span data-stu-id="5479b-390">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="5479b-391">Use a cadeia de caracteres de pesquisa "CLI do Azure" na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="5479b-391">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="5479b-392">Abra Cloud Shell em seu navegador com o botão **iniciar Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="5479b-392">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="5479b-393">Selecione o botão **Cloud Shell** no menu no canto superior direito do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-393">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="5479b-394">Para obter mais informações, consulte [CLI do Azure](/cli/azure/) e [visão geral do Azure cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="5479b-394">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="5479b-395">Se você ainda não estiver autenticado, entre com o `az login` comando.</span><span class="sxs-lookup"><span data-stu-id="5479b-395">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="5479b-396">Crie um grupo de recursos com o seguinte comando, em que `{RESOURCE GROUP NAME}` é o nome do grupo de recursos para o novo grupo de recursos e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="5479b-396">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="5479b-397">Crie um cofre de chaves no grupo de recursos com o seguinte comando, em que `{KEY VAULT NAME}` é o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="5479b-397">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="5479b-398">Crie segredos no cofre de chaves como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="5479b-398">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="5479b-399">Azure Key Vault nomes de segredo são limitados a caracteres alfanuméricos e traços.</span><span class="sxs-lookup"><span data-stu-id="5479b-399">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="5479b-400">Os valores hierárquicos (seções de configuração) usam `--` (dois traços) como um separador.</span><span class="sxs-lookup"><span data-stu-id="5479b-400">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="5479b-401">Os dois-pontos, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core](xref:fundamentals/configuration/index), não são permitidos em nomes de segredo do cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="5479b-401">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="5479b-402">Portanto, dois traços são usados e alternados por dois-pontos quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-402">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="5479b-403">Os segredos a seguir são para uso com o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="5479b-403">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="5479b-404">Os valores incluem um `_prod` sufixo para distingui-los dos `_dev` valores de sufixo carregados no ambiente de desenvolvimento de segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="5479b-404">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="5479b-405">Substitua `{KEY VAULT NAME}` pelo nome do cofre de chaves que você criou na etapa anterior:</span><span class="sxs-lookup"><span data-stu-id="5479b-405">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="5479b-406">Use a ID do aplicativo e o certificado X. 509 para aplicativos não hospedados no Azure</span><span class="sxs-lookup"><span data-stu-id="5479b-406">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="5479b-407">Configure o Azure AD, Azure Key Vault e o aplicativo para usar uma ID de aplicativo Azure Active Directory e um certificado X. 509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**.</span><span class="sxs-lookup"><span data-stu-id="5479b-407">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="5479b-408">Para obter mais informações, consulte [Sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="5479b-408">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="5479b-409">Embora o uso de uma ID de aplicativo e um certificado X. 509 tenha suporte para aplicativos hospedados no Azure, é recomendável usar [identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-409">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="5479b-410">Identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5479b-410">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="5479b-411">O aplicativo de exemplo usa uma ID de aplicativo e um certificado X. 509 quando a `#define` instrução na parte superior do arquivo *Program.cs* é definida como `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="5479b-411">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="5479b-412">Crie um certificado PKCS # 12 arquivo morto (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="5479b-412">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="5479b-413">As opções para criar certificados incluem [MakeCert no Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="5479b-413">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="5479b-414">Instale o certificado no repositório de certificados pessoal do usuário atual.</span><span class="sxs-lookup"><span data-stu-id="5479b-414">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="5479b-415">Marcar a chave como exportável é opcional.</span><span class="sxs-lookup"><span data-stu-id="5479b-415">Marking the key as exportable is optional.</span></span> <span data-ttu-id="5479b-416">Observe a impressão digital do certificado, que é usada posteriormente neste processo.</span><span class="sxs-lookup"><span data-stu-id="5479b-416">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="5479b-417">Exporte o certificado PKCS # 12 Archive (*. pfx*) como um certificado codificado em der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="5479b-417">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="5479b-418">Registre o aplicativo com o Azure AD (**registros de aplicativo**).</span><span class="sxs-lookup"><span data-stu-id="5479b-418">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="5479b-419">Carregue o certificado codificado em DER (*. cer*) no Azure AD:</span><span class="sxs-lookup"><span data-stu-id="5479b-419">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="5479b-420">Selecione o aplicativo no Azure AD.</span><span class="sxs-lookup"><span data-stu-id="5479b-420">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="5479b-421">Navegue até **certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="5479b-421">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="5479b-422">Selecione **carregar certificado** para carregar o certificado, que contém a chave pública.</span><span class="sxs-lookup"><span data-stu-id="5479b-422">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="5479b-423">Um certificado *. cer*, *. pem*ou *. CRT* é aceitável.</span><span class="sxs-lookup"><span data-stu-id="5479b-423">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="5479b-424">Armazene o nome do cofre de chaves, a ID do aplicativo e a impressão digital do certificado no arquivo *appSettings. JSON* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-424">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="5479b-425">Navegue até **cofres de chaves** na portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-425">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="5479b-426">Selecione o cofre de chaves que você criou no [armazenamento de segredo no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção.</span><span class="sxs-lookup"><span data-stu-id="5479b-426">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="5479b-427">Selecione **políticas de acesso**.</span><span class="sxs-lookup"><span data-stu-id="5479b-427">Select **Access policies**.</span></span>
1. <span data-ttu-id="5479b-428">Selecione **Adicionar política de acesso**.</span><span class="sxs-lookup"><span data-stu-id="5479b-428">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="5479b-429">Abra **permissões de segredo** e forneça ao aplicativo as permissões **Get** e **list** .</span><span class="sxs-lookup"><span data-stu-id="5479b-429">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="5479b-430">Selecione **selecionar entidade de segurança** e selecione o aplicativo registrado por nome.</span><span class="sxs-lookup"><span data-stu-id="5479b-430">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="5479b-431">Escolha o botão **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="5479b-431">Select the **Select** button.</span></span>
1. <span data-ttu-id="5479b-432">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="5479b-432">Select **OK**.</span></span>
1. <span data-ttu-id="5479b-433">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="5479b-433">Select **Save**.</span></span>
1. <span data-ttu-id="5479b-434">Implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-434">Deploy the app.</span></span>

<span data-ttu-id="5479b-435">O `Certificate` aplicativo de exemplo obtém seus valores de configuração de `IConfigurationRoot` com o mesmo nome que o nome do segredo:</span><span class="sxs-lookup"><span data-stu-id="5479b-435">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="5479b-436">Valores não hierárquicos: o valor para `SecretName` é obtido com `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="5479b-436">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="5479b-437">Valores hierárquicos (seções): `:` notação de uso (dois-pontos) ou o `GetSection` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="5479b-437">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="5479b-438">Use qualquer uma dessas abordagens para obter o valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="5479b-438">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="5479b-439">O certificado X. 509 é gerenciado pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="5479b-439">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="5479b-440">O aplicativo chama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> com valores fornecidos pelo arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="5479b-440">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="5479b-441">Valores de exemplo:</span><span class="sxs-lookup"><span data-stu-id="5479b-441">Example values:</span></span>

* <span data-ttu-id="5479b-442">Nome do cofre de chaves:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="5479b-442">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="5479b-443">ID do aplicativo:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="5479b-443">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="5479b-444">Impressão digital do certificado:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="5479b-444">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="5479b-445">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="5479b-445">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="5479b-446">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="5479b-446">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="5479b-447">No ambiente de desenvolvimento, os valores secretos são carregados com o `_dev` sufixo.</span><span class="sxs-lookup"><span data-stu-id="5479b-447">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="5479b-448">No ambiente de produção, os valores são carregados com o `_prod` sufixo.</span><span class="sxs-lookup"><span data-stu-id="5479b-448">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="5479b-449">Usar identidades gerenciadas para recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="5479b-449">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="5479b-450">**Um aplicativo implantado no Azure** pode aproveitar as [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview), o que permite que o aplicativo se autentique com Azure Key Vault usando a autenticação do Azure ad sem credenciais (ID do aplicativo e senha/segredo do cliente) armazenados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-450">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="5479b-451">O aplicativo de exemplo usa identidades gerenciadas para recursos do Azure quando a `#define` instrução na parte superior do arquivo *Program.cs* é definida como `Managed` .</span><span class="sxs-lookup"><span data-stu-id="5479b-451">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="5479b-452">Insira o nome do cofre no arquivo *appSettings. JSON* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-452">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="5479b-453">O aplicativo de exemplo não requer uma ID de aplicativo e uma senha (segredo do cliente) quando definido para a `Managed` versão, para que você possa ignorar essas entradas de configuração.</span><span class="sxs-lookup"><span data-stu-id="5479b-453">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="5479b-454">O aplicativo é implantado no Azure e o Azure autentica o aplicativo para acessar Azure Key Vault apenas usando o nome do cofre armazenado no arquivo *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="5479b-454">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="5479b-455">Implante o aplicativo de exemplo no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="5479b-455">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="5479b-456">Um aplicativo implantado no serviço Azure App é registrado automaticamente com o Azure AD quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="5479b-456">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="5479b-457">Obtenha a ID de objeto da implantação para uso no comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="5479b-457">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="5479b-458">A ID de objeto é mostrada no portal do Azure no **Identity** painel do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-458">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="5479b-459">Usando CLI do Azure e a ID de objeto do aplicativo, forneça ao aplicativo `list` e `get` permissões para acessar o cofre de chaves:</span><span class="sxs-lookup"><span data-stu-id="5479b-459">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="5479b-460">**Reinicie o aplicativo** usando CLI do Azure, PowerShell ou o portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-460">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="5479b-461">O aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="5479b-461">The sample app:</span></span>

* <span data-ttu-id="5479b-462">Cria uma instância da `AzureServiceTokenProvider` classe sem uma cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="5479b-462">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="5479b-463">Quando uma cadeia de conexão não é fornecida, o provedor tenta obter um token de acesso de identidades gerenciadas para recursos do Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-463">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="5479b-464">Um novo <xref:Microsoft.Azure.KeyVault.KeyVaultClient> é criado com o `AzureServiceTokenProvider` retorno de chamada de token de instância.</span><span class="sxs-lookup"><span data-stu-id="5479b-464">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="5479b-465">A <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instância é usada com uma implementação padrão do <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> que carrega todos os valores secretos e substitui traços duplos ( `--` ) por dois-pontos ( `:` ) em nomes de chave.</span><span class="sxs-lookup"><span data-stu-id="5479b-465">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="5479b-466">Valor de exemplo do nome do cofre de chaves:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="5479b-466">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="5479b-467">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="5479b-467">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="5479b-468">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="5479b-468">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="5479b-469">No ambiente de desenvolvimento, os valores secretos têm o `_dev` sufixo porque são fornecidos por segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="5479b-469">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="5479b-470">No ambiente de produção, os valores são carregados com o `_prod` sufixo porque são fornecidos por Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="5479b-470">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="5479b-471">Se você receber um `Access denied` erro, confirme se o aplicativo está registrado no Azure AD e se forneceu acesso ao cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="5479b-471">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="5479b-472">Confirme que você reiniciou o serviço no Azure.</span><span class="sxs-lookup"><span data-stu-id="5479b-472">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="5479b-473">Para obter informações sobre como usar o provedor com uma identidade gerenciada e um pipeline DevOps do Azure, consulte [criar uma conexão de serviço Azure Resource Manager para uma VM com uma identidade de serviço gerenciado](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="5479b-473">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="5479b-474">Usar um prefixo de nome de chave</span><span class="sxs-lookup"><span data-stu-id="5479b-474">Use a key name prefix</span></span>

<span data-ttu-id="5479b-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>fornece uma sobrecarga que aceita uma implementação do <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="5479b-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="5479b-476">Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-476">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="5479b-477">Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-477">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="5479b-478">Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos de *produção* ) no mesmo cofre.</span><span class="sxs-lookup"><span data-stu-id="5479b-478">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="5479b-479">Recomendamos que aplicativos diferentes e ambientes de desenvolvimento/produção usem cofres de chaves separados para isolar os ambientes de aplicativo para o nível mais alto de segurança.</span><span class="sxs-lookup"><span data-stu-id="5479b-479">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="5479b-480">No exemplo a seguir, um segredo é estabelecido no cofre de chaves (e usando a ferramenta Gerenciador de segredo para o ambiente de desenvolvimento) para `5000-AppSecret` (os períodos não são permitidos em nomes de segredo do cofre de chaves).</span><span class="sxs-lookup"><span data-stu-id="5479b-480">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="5479b-481">Esse segredo representa um segredo do aplicativo para a versão 5.0.0.0 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-481">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="5479b-482">Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre de chaves (e usando a ferramenta Gerenciador de segredo) para o `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="5479b-482">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="5479b-483">Cada versão do aplicativo carrega seu valor secreto com versão em sua configuração como `AppSecret` , eliminando a versão à medida que ela carrega o segredo.</span><span class="sxs-lookup"><span data-stu-id="5479b-483">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="5479b-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>é chamado com um personalizado <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="5479b-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="5479b-485">A <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementação reage aos prefixos de versão dos segredos para carregar o segredo apropriado na configuração:</span><span class="sxs-lookup"><span data-stu-id="5479b-485">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="5479b-486">`Load`carrega um segredo quando seu nome começa com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="5479b-486">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="5479b-487">Outros segredos não são carregados.</span><span class="sxs-lookup"><span data-stu-id="5479b-487">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="5479b-488">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="5479b-488">`GetKey`:</span></span>
  * <span data-ttu-id="5479b-489">Remove o prefixo do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="5479b-489">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="5479b-490">Substitui dois traços em qualquer nome pelo `KeyDelimiter` , que é o delimitador usado na configuração (geralmente um dois-pontos).</span><span class="sxs-lookup"><span data-stu-id="5479b-490">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="5479b-491">Azure Key Vault não permite dois pontos em nomes secretos.</span><span class="sxs-lookup"><span data-stu-id="5479b-491">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="5479b-492">O `Load` método é chamado por um algoritmo de provedor que itera por meio dos segredos do cofre para localizar aqueles que têm o prefixo de versão.</span><span class="sxs-lookup"><span data-stu-id="5479b-492">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="5479b-493">Quando um prefixo de versão é encontrado com `Load` , o algoritmo usa o `GetKey` método para retornar o nome de configuração do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="5479b-493">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="5479b-494">Ele retira o prefixo de versão do nome do segredo e retorna o restante do nome do segredo para carregar nos pares nome-valor da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-494">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="5479b-495">Quando essa abordagem é implementada:</span><span class="sxs-lookup"><span data-stu-id="5479b-495">When this approach is implemented:</span></span>

1. <span data-ttu-id="5479b-496">A versão do aplicativo especificada no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-496">The app's version specified in the app's project file.</span></span> <span data-ttu-id="5479b-497">No exemplo a seguir, a versão do aplicativo é definida como `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="5479b-497">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="5479b-498">Confirme se uma `<UserSecretsId>` propriedade está presente no arquivo de projeto do aplicativo, em que `{GUID}` é um GUID fornecido pelo usuário:</span><span class="sxs-lookup"><span data-stu-id="5479b-498">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="5479b-499">Salve os seguintes segredos localmente com a [ferramenta Gerenciador de segredo](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="5479b-499">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="5479b-500">Os segredos são salvos em Azure Key Vault usando os seguintes comandos CLI do Azure:</span><span class="sxs-lookup"><span data-stu-id="5479b-500">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="5479b-501">Quando o aplicativo é executado, os segredos do cofre de chaves são carregados.</span><span class="sxs-lookup"><span data-stu-id="5479b-501">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="5479b-502">O segredo da cadeia de caracteres para `5000-AppSecret` é correspondido à versão do aplicativo especificada no arquivo de projeto do aplicativo ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="5479b-502">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="5479b-503">A versão, `5000` (com o Dash), é removida do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="5479b-503">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="5479b-504">Em todo o aplicativo, a leitura da configuração com a chave `AppSecret` carrega o valor secreto.</span><span class="sxs-lookup"><span data-stu-id="5479b-504">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="5479b-505">Se a versão do aplicativo for alterada no arquivo de projeto para `5.1.0.0` e o aplicativo for executado novamente, o valor secreto retornado estará `5.1.0.0_secret_value_dev` no ambiente de desenvolvimento e `5.1.0.0_secret_value_prod` em produção.</span><span class="sxs-lookup"><span data-stu-id="5479b-505">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="5479b-506">Você também pode fornecer sua própria <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementação para o <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="5479b-506">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="5479b-507">Um cliente personalizado permite compartilhar uma única instância do cliente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5479b-507">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="5479b-508">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="5479b-508">Bind an array to a class</span></span>

<span data-ttu-id="5479b-509">O provedor é capaz de ler valores de configuração em uma matriz para associação a uma matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="5479b-509">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="5479b-510">Ao ler de uma fonte de configuração que permite que as chaves contenham separadores de dois-pontos ( `:` ), um segmento de chave numérico é usado para distinguir as chaves que compõem uma matriz ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="5479b-510">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="5479b-511">Para obter mais informações, consulte [configuração: associar uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="5479b-511">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="5479b-512">Azure Key Vault chaves não podem usar dois-pontos como separador.</span><span class="sxs-lookup"><span data-stu-id="5479b-512">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="5479b-513">A abordagem descrita neste tópico usa traços duplos ( `--` ) como separador de valores hierárquicos (seções).</span><span class="sxs-lookup"><span data-stu-id="5479b-513">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="5479b-514">As chaves de matriz são armazenadas em Azure Key Vault com traços duplos e segmentos de chave numérica ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="5479b-514">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="5479b-515">Examine a seguinte configuração do provedor de log [Serilog](https://serilog.net/) fornecida por um arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="5479b-515">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="5479b-516">Há dois literais de objeto definidos na `WriteTo` matriz que refletem dois *coletores*Serilog, que descrevem os destinos para a saída de log:</span><span class="sxs-lookup"><span data-stu-id="5479b-516">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="5479b-517">A configuração mostrada no arquivo JSON anterior é armazenada em Azure Key Vault usando a `--` notação de traço duplo () e os segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="5479b-517">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="5479b-518">Chave</span><span class="sxs-lookup"><span data-stu-id="5479b-518">Key</span></span> | <span data-ttu-id="5479b-519">Valor</span><span class="sxs-lookup"><span data-stu-id="5479b-519">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="5479b-520">Recarregar segredos</span><span class="sxs-lookup"><span data-stu-id="5479b-520">Reload secrets</span></span>

<span data-ttu-id="5479b-521">Os segredos são armazenados em cache até que `IConfigurationRoot.Reload()` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="5479b-521">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="5479b-522">Os segredos expirados, desabilitados e atualizados no cofre de chaves não são respeitados pelo aplicativo até que o `Reload` seja executado.</span><span class="sxs-lookup"><span data-stu-id="5479b-522">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="5479b-523">Segredos desabilitados e expirados</span><span class="sxs-lookup"><span data-stu-id="5479b-523">Disabled and expired secrets</span></span>

<span data-ttu-id="5479b-524">Os segredos desabilitados e expirados lançam um <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="5479b-524">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="5479b-525">Para impedir que o aplicativo seja acionado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desabilitado ou expirado.</span><span class="sxs-lookup"><span data-stu-id="5479b-525">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5479b-526">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="5479b-526">Troubleshoot</span></span>

<span data-ttu-id="5479b-527">Quando o aplicativo falha ao carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infraestrutura de log de ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="5479b-527">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="5479b-528">As seguintes condições impedirão que a configuração seja carregada:</span><span class="sxs-lookup"><span data-stu-id="5479b-528">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="5479b-529">O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="5479b-529">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="5479b-530">O cofre de chaves não existe no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="5479b-530">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="5479b-531">O aplicativo não está autorizado a acessar o cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="5479b-531">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="5479b-532">A política de acesso não `Get` inclui `List` permissões e.</span><span class="sxs-lookup"><span data-stu-id="5479b-532">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="5479b-533">No cofre de chaves, os dados de configuração (par nome-valor) são nomeados incorretamente, ausentes, desabilitados ou expirados.</span><span class="sxs-lookup"><span data-stu-id="5479b-533">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="5479b-534">O aplicativo tem o nome do cofre de chaves errado ( `KeyVaultName` ), a ID do aplicativo do Azure AD ( `AzureADApplicationId` ) ou a impressão digital do certificado do Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="5479b-534">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="5479b-535">A chave de configuração (Name) está incorreta no aplicativo para o valor que você está tentando carregar.</span><span class="sxs-lookup"><span data-stu-id="5479b-535">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="5479b-536">Ao adicionar a política de acesso para o aplicativo ao cofre de chaves, a política foi criada, mas o botão **salvar** não foi selecionado na interface do usuário de **políticas de acesso** .</span><span class="sxs-lookup"><span data-stu-id="5479b-536">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5479b-537">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5479b-537">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="5479b-538">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="5479b-538">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="5479b-539">Microsoft Azure: Key Vault documentação</span><span class="sxs-lookup"><span data-stu-id="5479b-539">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="5479b-540">Como gerar e transferir chaves protegidas por HSM para Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5479b-540">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="5479b-541">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="5479b-541">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="5479b-542">Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET</span><span class="sxs-lookup"><span data-stu-id="5479b-542">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="5479b-543">Tutorial: Como usar o Azure Key Vault com a máquina virtual do Azure com Windows no .NET</span><span class="sxs-lookup"><span data-stu-id="5479b-543">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end


---
title: Criptografia de chave em repouso no Windows e no Azure usando ASP.NET Core
author: rick-anderson
description: Aprenda detalhes de implementação de criptografia de chave de proteção de dados ASP.NET Core em repouso.
ms.author: riande
ms.date: 07/16/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: c927c926212aeb1263d15fd3fdc753c377b2e305
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153564"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a><span data-ttu-id="af732-103">Criptografia de chave em repouso no Windows e no Azure usando ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af732-103">Key encryption at rest in Windows and Azure using ASP.NET Core</span></span>

<span data-ttu-id="af732-104">O sistema de proteção de dados [emprega um mecanismo de descoberta por padrão](xref:security/data-protection/configuration/default-settings) para determinar como as chaves criptográficas devem ser criptografadas em repouso.</span><span class="sxs-lookup"><span data-stu-id="af732-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="af732-105">O desenvolvedor pode substituir o mecanismo de descoberta e especificar manualmente como as chaves devem ser criptografadas em repouso.</span><span class="sxs-lookup"><span data-stu-id="af732-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="af732-106">Se você especificar um [local de persistência de chave](xref:security/data-protection/implementation/key-storage-providers)explícita, o sistema de proteção de dados cancelará o registro do mecanismo de criptografia de chave padrão em repouso.</span><span class="sxs-lookup"><span data-stu-id="af732-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="af732-107">Consequentemente, as chaves não são mais criptografadas em repouso.</span><span class="sxs-lookup"><span data-stu-id="af732-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="af732-108">Recomendamos que você [especifique um mecanismo de criptografia de chave explícito](xref:security/data-protection/implementation/key-encryption-at-rest) para implantações de produção.</span><span class="sxs-lookup"><span data-stu-id="af732-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="af732-109">As opções de mecanismo de criptografia em repouso são descritas neste tópico.</span><span class="sxs-lookup"><span data-stu-id="af732-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="af732-110">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="af732-110">Azure Key Vault</span></span>

<span data-ttu-id="af732-111">Para armazenar chaves no [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure o sistema com [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) na `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="af732-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="af732-112">Para obter mais informações, consulte [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span><span class="sxs-lookup"><span data-stu-id="af732-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="af732-113">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="af732-113">Windows DPAPI</span></span>

<span data-ttu-id="af732-114">**Aplica-se somente a implantações do Windows.**</span><span class="sxs-lookup"><span data-stu-id="af732-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="af732-115">Quando o Windows DPAPI é usado, o material da chave é criptografado com [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) antes de ser persistido no armazenamento.</span><span class="sxs-lookup"><span data-stu-id="af732-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="af732-116">O DPAPI é um mecanismo de criptografia apropriado para dados que nunca são lidos fora do computador atual (embora seja possível fazer o back-up dessas chaves até Active Directory; consulte [DPAPI e perfis móveis](https://support.microsoft.com/kb/309408/#6)).</span><span class="sxs-lookup"><span data-stu-id="af732-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="af732-117">Para configurar a criptografia de chave em repouso DPAPI, chame um dos métodos de extensão [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) :</span><span class="sxs-lookup"><span data-stu-id="af732-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="af732-118">Se `ProtectKeysWithDpapi` for chamado sem parâmetros, somente a conta de usuário do Windows atual poderá decifrar o anel de chave persistente.</span><span class="sxs-lookup"><span data-stu-id="af732-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="af732-119">Opcionalmente, você pode especificar que qualquer conta de usuário no computador (não apenas a conta de usuário atual) seja capaz de decifrar o anel de chave:</span><span class="sxs-lookup"><span data-stu-id="af732-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="af732-120">Certificado X.509</span><span class="sxs-lookup"><span data-stu-id="af732-120">X.509 certificate</span></span>

<span data-ttu-id="af732-121">Se o aplicativo for distribuído entre vários computadores, poderá ser conveniente distribuir um certificado X. 509 compartilhado entre os computadores e configurar os aplicativos hospedados para usar o certificado para criptografia de chaves em repouso:</span><span class="sxs-lookup"><span data-stu-id="af732-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="af732-122">Devido a limitações de .NET Framework, há suporte apenas para certificados com chaves privadas CAPI.</span><span class="sxs-lookup"><span data-stu-id="af732-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="af732-123">Consulte o conteúdo abaixo para obter possíveis soluções alternativas para essas limitações.</span><span class="sxs-lookup"><span data-stu-id="af732-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="af732-124">Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="af732-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="af732-125">**Esse mecanismo está disponível somente no Windows 8/Windows Server 2012 ou posterior.**</span><span class="sxs-lookup"><span data-stu-id="af732-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="af732-126">A partir do Windows 8, o sistema operacional Windows dá suporte a DPAPI-NG (também chamado de CNG DPAPI).</span><span class="sxs-lookup"><span data-stu-id="af732-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="af732-127">Para obter mais informações, consulte [about CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span><span class="sxs-lookup"><span data-stu-id="af732-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="af732-128">A entidade de segurança é codificada como uma regra de descritor de proteção.</span><span class="sxs-lookup"><span data-stu-id="af732-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="af732-129">No exemplo a seguir que chama [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), somente o usuário ingressado no domínio com o SID especificado pode descriptografar o anel de chave:</span><span class="sxs-lookup"><span data-stu-id="af732-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="af732-130">Também há uma sobrecarga sem parâmetros de `ProtectKeysWithDpapiNG` .</span><span class="sxs-lookup"><span data-stu-id="af732-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="af732-131">Use este método de conveniência para especificar a regra "SID = {CURRENT_ACCOUNT_SID}", em que *CURRENT_ACCOUNT_SID* é o SID da conta de usuário atual do Windows:</span><span class="sxs-lookup"><span data-stu-id="af732-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="af732-132">Nesse cenário, o controlador de domínio do AD é responsável por distribuir as chaves de criptografia usadas pelas operações de DPAPI-NG.</span><span class="sxs-lookup"><span data-stu-id="af732-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="af732-133">O usuário de destino pode decifrar a carga criptografada de qualquer computador associado ao domínio (desde que o processo esteja sendo executado sob sua identidade).</span><span class="sxs-lookup"><span data-stu-id="af732-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="af732-134">Criptografia baseada em certificado com o Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="af732-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="af732-135">Se o aplicativo estiver em execução no Windows 8.1/Windows Server 2012 R2 ou posterior, você poderá usar o Windows DPAPI-NG para executar a criptografia baseada em certificado.</span><span class="sxs-lookup"><span data-stu-id="af732-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="af732-136">Use a cadeia de caracteres do descritor de regra "CERTIFICATE = HashId: impressão digital", em que a *impressão digital* é a impressão digital SHA1 codificada em hex do certificado:</span><span class="sxs-lookup"><span data-stu-id="af732-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="af732-137">Qualquer aplicativo apontado neste repositório deve estar em execução no Windows 8.1/Windows Server 2012 R2 ou posterior para decifrar as chaves.</span><span class="sxs-lookup"><span data-stu-id="af732-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="af732-138">Criptografia de chave personalizada</span><span class="sxs-lookup"><span data-stu-id="af732-138">Custom key encryption</span></span>

<span data-ttu-id="af732-139">Se os mecanismos na caixa não forem apropriados, o desenvolvedor poderá especificar seu próprio mecanismo de criptografia de chave, fornecendo um [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)personalizado.</span><span class="sxs-lookup"><span data-stu-id="af732-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>

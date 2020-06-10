---
title: Hospedar o ASP.NET Core em um serviço Windows
author: rick-anderson
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/windows-service
ms.openlocfilehash: af0c3836362233e41a79e72bd28b4a331e9763bc
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106475"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="d67b7-103">Hospedar o ASP.NET Core em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="d67b7-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d67b7-104">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="d67b7-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d67b7-105">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="d67b7-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d67b7-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d67b7-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d67b7-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="d67b7-107">Prerequisites</span></span>

* [<span data-ttu-id="d67b7-108">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="d67b7-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d67b7-109">PowerShell 6.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="d67b7-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="d67b7-110">Modelo de serviço de trabalho</span><span class="sxs-lookup"><span data-stu-id="d67b7-110">Worker Service template</span></span>

<span data-ttu-id="d67b7-111">O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada.</span><span class="sxs-lookup"><span data-stu-id="d67b7-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="d67b7-112">Para usar o modelo como base para um aplicativo de Serviço Windows:</span><span class="sxs-lookup"><span data-stu-id="d67b7-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="d67b7-113">Crie um aplicativo de serviço de trabalho usando o modelo do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d67b7-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="d67b7-114">Siga as orientações na seção [Configuração de aplicativos](#app-configuration) para atualizar o aplicativo do Serviço de Trabalho para que ele possa ser executado como um Serviço Windows.</span><span class="sxs-lookup"><span data-stu-id="d67b7-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="d67b7-115">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="d67b7-115">App configuration</span></span>

<span data-ttu-id="d67b7-116">O aplicativo requer uma referência de pacote para [Microsoft. Extensions. host. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="d67b7-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="d67b7-117">`IHostBuilder.UseWindowsService`é chamado ao criar o host.</span><span class="sxs-lookup"><span data-stu-id="d67b7-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="d67b7-118">Se o aplicativo estiver sendo executado como um Serviço Windows, o método:</span><span class="sxs-lookup"><span data-stu-id="d67b7-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="d67b7-119">Define o tempo de vida do host como `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="d67b7-120">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) como [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="d67b7-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="d67b7-121">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="d67b7-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="d67b7-122">Habilita o registro em log no log de eventos:</span><span class="sxs-lookup"><span data-stu-id="d67b7-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="d67b7-123">O nome do aplicativo é usado como o nome de origem padrão.</span><span class="sxs-lookup"><span data-stu-id="d67b7-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="d67b7-124">O nível de log padrão é *aviso* ou superior para um aplicativo com base em um modelo de ASP.NET Core que chama `CreateDefaultBuilder` para criar o host.</span><span class="sxs-lookup"><span data-stu-id="d67b7-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="d67b7-125">Substitua o nível de log padrão pela `Logging:EventLog:LogLevel:Default` chave em *appSettings. JSON* / *appSettings. { Ambiente}. JSON* ou outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="d67b7-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="d67b7-126">Somente administradores podem criar novas fontes de evento.</span><span class="sxs-lookup"><span data-stu-id="d67b7-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="d67b7-127">Quando uma fonte de evento não puder ser criada usando o nome do aplicativo, um aviso será registrado em log como a fonte do *Aplicativo* e os logs de eventos serão desabilitados.</span><span class="sxs-lookup"><span data-stu-id="d67b7-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="d67b7-128">Em `CreateHostBuilder` *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d67b7-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="d67b7-129">Os aplicativos de exemplo a seguir acompanham este tópico:</span><span class="sxs-lookup"><span data-stu-id="d67b7-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="d67b7-130">Exemplo de serviço de trabalho em segundo plano: um aplicativo não Web de exemplo baseado no [modelo de serviço de trabalho](#worker-service-template) que usa [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="d67b7-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="d67b7-131">Exemplo de serviço de aplicativo Web: um Razor exemplo de aplicativo Web de páginas que é executado como um serviço do Windows com [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="d67b7-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="d67b7-132">Para obter diretrizes do MVC, consulte os artigos em <xref:mvc/overview> e <xref:migration/22-to-30> .</span><span class="sxs-lookup"><span data-stu-id="d67b7-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="d67b7-133">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="d67b7-133">Deployment type</span></span>

<span data-ttu-id="d67b7-134">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="d67b7-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d67b7-135">.</span><span class="sxs-lookup"><span data-stu-id="d67b7-135">SDK</span></span>

<span data-ttu-id="d67b7-136">Para um serviço baseado em aplicativo Web que usa as Razor páginas ou as estruturas MVC, especifique o SDK da Web no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="d67b7-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d67b7-137">Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados](xref:fundamentals/host/hosted-services)), ESPECIFIQUE o SDK do trabalhador no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="d67b7-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d67b7-138">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="d67b7-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d67b7-139">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="d67b7-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d67b7-140">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d67b7-141">Se você estiver usando o [SDK da Web](#sdk), um arquivo *Web. config* , que normalmente é produzido ao publicar um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="d67b7-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d67b7-142">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d67b7-143">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="d67b7-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d67b7-144">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="d67b7-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d67b7-145">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d67b7-146">Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="d67b7-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d67b7-147">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="d67b7-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d67b7-148">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="d67b7-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d67b7-149">Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="d67b7-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d67b7-150">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="d67b7-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="d67b7-151">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-151">Service user account</span></span>

<span data-ttu-id="d67b7-152">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="d67b7-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d67b7-153">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="d67b7-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d67b7-154">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="d67b7-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d67b7-155">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="d67b7-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d67b7-156">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="d67b7-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d67b7-157">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="d67b7-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d67b7-158">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="d67b7-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d67b7-159">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="d67b7-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d67b7-160">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-160">Log on as a service rights</span></span>

<span data-ttu-id="d67b7-161">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="d67b7-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d67b7-162">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d67b7-163">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d67b7-164">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d67b7-165">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d67b7-166">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="d67b7-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d67b7-167">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="d67b7-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d67b7-168">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-168">Select **Advanced**.</span></span> <span data-ttu-id="d67b7-169">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-169">Select **Find Now**.</span></span> <span data-ttu-id="d67b7-170">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="d67b7-170">Select the user account from the list.</span></span> <span data-ttu-id="d67b7-171">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-171">Select **OK**.</span></span> <span data-ttu-id="d67b7-172">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="d67b7-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d67b7-173">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="d67b7-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d67b7-174">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="d67b7-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d67b7-175">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-175">Create a service</span></span>

<span data-ttu-id="d67b7-176">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d67b7-177">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d67b7-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d67b7-178">`{EXE PATH}`: Caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d67b7-179">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="d67b7-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d67b7-180">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="d67b7-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d67b7-181">`{DOMAIN OR COMPUTER NAME\USER}`: Conta de usuário de serviço (por exemplo, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d67b7-182">`{SERVICE NAME}`: Nome do serviço (por exemplo, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d67b7-183">`{EXE FILE PATH}`: O caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d67b7-184">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="d67b7-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d67b7-185">`{DESCRIPTION}`: Descrição do serviço (por exemplo, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d67b7-186">`{DISPLAY NAME}`: Nome de exibição do serviço (por exemplo, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d67b7-187">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-187">Start a service</span></span>

<span data-ttu-id="d67b7-188">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d67b7-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d67b7-189">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d67b7-190">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-190">Determine a service's status</span></span>

<span data-ttu-id="d67b7-191">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d67b7-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d67b7-192">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="d67b7-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d67b7-193">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-193">Stop a service</span></span>

<span data-ttu-id="d67b7-194">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="d67b7-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d67b7-195">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-195">Remove a service</span></span>

<span data-ttu-id="d67b7-196">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="d67b7-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d67b7-197">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="d67b7-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d67b7-198">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="d67b7-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d67b7-199">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="d67b7-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d67b7-200">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="d67b7-200">Configure endpoints</span></span>

<span data-ttu-id="d67b7-201">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d67b7-202">Configure a URL e a porta definindo a `ASPNETCORE_URLS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="d67b7-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d67b7-203">Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:</span><span class="sxs-lookup"><span data-stu-id="d67b7-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d67b7-204">As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d67b7-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d67b7-205">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="d67b7-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d67b7-206">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d67b7-207">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="d67b7-207">Current directory and content root</span></span>

<span data-ttu-id="d67b7-208">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d67b7-209">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="d67b7-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d67b7-210">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="d67b7-211">Usar ContentRootPath ou ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="d67b7-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="d67b7-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> para localizar os recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="d67b7-213">Quando o aplicativo é executado como um serviço, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> o define <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> como [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="d67b7-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="d67b7-214">Os arquivos de configurações padrão do aplicativo, *appSettings. JSON* e *appSettings. { Environment}. JSON*, são carregados a partir da raiz do conteúdo do aplicativo chamando [CreateDefaultBuilder durante a construção do host](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="d67b7-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="d67b7-215">Para outros arquivos de configurações carregados pelo código do desenvolvedor no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> , não há necessidade de chamar <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> .</span><span class="sxs-lookup"><span data-stu-id="d67b7-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="d67b7-216">No exemplo a seguir, o arquivo *custom_settings. JSON* existe na raiz do conteúdo do aplicativo e é carregado sem definir explicitamente um caminho base:</span><span class="sxs-lookup"><span data-stu-id="d67b7-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="d67b7-217">Não tente usar <xref:System.IO.Directory.GetCurrentDirectory*> para obter um caminho de recurso porque um aplicativo de serviço do Windows retorna a pasta *C: \\ Windows \\ System32* como seu diretório atual.</span><span class="sxs-lookup"><span data-stu-id="d67b7-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d67b7-218">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="d67b7-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d67b7-219">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="d67b7-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d67b7-220">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="d67b7-220">Troubleshoot</span></span>

<span data-ttu-id="d67b7-221">Para solucionar problemas de um aplicativo de serviço do Windows, consulte <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="d67b7-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d67b7-222">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="d67b7-222">Common errors</span></span>

* <span data-ttu-id="d67b7-223">Uma versão antiga ou de pré-lançamento do PowerShell está em uso.</span><span class="sxs-lookup"><span data-stu-id="d67b7-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d67b7-224">O serviço registrado não usa a saída **publicada** do aplicativo do comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="d67b7-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d67b7-225">A saída do comando [dotnet Build](/dotnet/core/tools/dotnet-build) não tem suporte para implantação de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d67b7-226">Os ativos publicados são encontrados em uma das seguintes pastas, dependendo do tipo de implantação:</span><span class="sxs-lookup"><span data-stu-id="d67b7-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d67b7-227">FDD do *compartimento/versão/{estrutura de destino}* (a)</span><span class="sxs-lookup"><span data-stu-id="d67b7-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d67b7-228">*bin/Release/{Framework de destino} identificador de/{Runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="d67b7-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d67b7-229">O serviço não está em estado de execução.</span><span class="sxs-lookup"><span data-stu-id="d67b7-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d67b7-230">Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos.</span><span class="sxs-lookup"><span data-stu-id="d67b7-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d67b7-231">O caminho base de um serviço do Windows é *c: \\ Windows \\ System32*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d67b7-232">O usuário não tem direitos de *logon como um serviço* .</span><span class="sxs-lookup"><span data-stu-id="d67b7-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d67b7-233">A senha do usuário expirou ou foi passada incorretamente ao executar o `New-Service` comando do PowerShell.</span><span class="sxs-lookup"><span data-stu-id="d67b7-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d67b7-234">O aplicativo requer autenticação ASP.NET Core, mas não está configurado para conexões seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d67b7-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d67b7-235">A porta da URL da solicitação está incorreta ou não está configurada corretamente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d67b7-236">Logs de eventos do sistema e do aplicativo</span><span class="sxs-lookup"><span data-stu-id="d67b7-236">System and Application Event Logs</span></span>

<span data-ttu-id="d67b7-237">Acesse os logs de eventos do sistema e do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d67b7-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d67b7-238">Abra o menu Iniciar, procure *Visualizador de eventos*e selecione o aplicativo **Visualizador de eventos** .</span><span class="sxs-lookup"><span data-stu-id="d67b7-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d67b7-239">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d67b7-240">Selecione **sistema** para abrir o log de eventos do sistema.</span><span class="sxs-lookup"><span data-stu-id="d67b7-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d67b7-241">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d67b7-242">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="d67b7-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d67b7-243">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="d67b7-243">Run the app at a command prompt</span></span>

<span data-ttu-id="d67b7-244">Muitos erros de inicialização não produzem informações úteis nos logs de eventos.</span><span class="sxs-lookup"><span data-stu-id="d67b7-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d67b7-245">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="d67b7-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d67b7-246">Para registrar em log detalhes adicionais do aplicativo, reduza o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="d67b7-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d67b7-247">Limpar caches de pacote</span><span class="sxs-lookup"><span data-stu-id="d67b7-247">Clear package caches</span></span>

<span data-ttu-id="d67b7-248">Um aplicativo em funcionamento pode falhar imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d67b7-249">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="d67b7-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d67b7-250">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="d67b7-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d67b7-251">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d67b7-252">Limpe os caches de pacote executando [dotnet NuGet local All--Clear](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="d67b7-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d67b7-253">A limpeza de caches de pacote também pode ser realizada com a ferramenta [NuGet. exe](https://www.nuget.org/downloads) e executando o comando `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="d67b7-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d67b7-254">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="d67b7-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d67b7-255">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="d67b7-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d67b7-256">Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d67b7-257">Aplicativo lento ou travando</span><span class="sxs-lookup"><span data-stu-id="d67b7-257">Slow or hanging app</span></span>

<span data-ttu-id="d67b7-258">Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="d67b7-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d67b7-259">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="d67b7-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="d67b7-260">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="d67b7-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d67b7-261">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d67b7-262">Execute o [script do PowerShell do EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) com o nome do executável do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d67b7-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d67b7-263">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="d67b7-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d67b7-264">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="d67b7-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d67b7-265">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="d67b7-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d67b7-266">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d67b7-267">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="d67b7-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d67b7-268">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="d67b7-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d67b7-269">Quando um aplicativo *paralisa* (para de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [arquivos de despejo no modo de usuário: escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o despejo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d67b7-270">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="d67b7-270">Analyze the dump</span></span>

<span data-ttu-id="d67b7-271">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="d67b7-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d67b7-272">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="d67b7-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d67b7-273">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d67b7-273">Additional resources</span></span>

* <span data-ttu-id="d67b7-274">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="d67b7-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d67b7-275">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="d67b7-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d67b7-276">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="d67b7-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d67b7-277">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d67b7-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d67b7-278">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="d67b7-278">Prerequisites</span></span>

* [<span data-ttu-id="d67b7-279">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="d67b7-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d67b7-280">PowerShell 6.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="d67b7-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="d67b7-281">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="d67b7-281">App configuration</span></span>

<span data-ttu-id="d67b7-282">O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="d67b7-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="d67b7-283">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="d67b7-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="d67b7-284">Verifique se o depurador está anexado ou se uma opção `--console` está presente.</span><span class="sxs-lookup"><span data-stu-id="d67b7-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="d67b7-285">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="d67b7-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="d67b7-286">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="d67b7-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="d67b7-287">Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="d67b7-288">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="d67b7-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="d67b7-289">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="d67b7-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="d67b7-290">Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="d67b7-291">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="d67b7-292">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="d67b7-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="d67b7-293">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="d67b7-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="d67b7-294">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="d67b7-295">No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="d67b7-296">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="d67b7-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="d67b7-297">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="d67b7-297">Deployment type</span></span>

<span data-ttu-id="d67b7-298">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="d67b7-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d67b7-299">.</span><span class="sxs-lookup"><span data-stu-id="d67b7-299">SDK</span></span>

<span data-ttu-id="d67b7-300">Para um serviço baseado em aplicativo Web que usa as Razor páginas ou as estruturas MVC, especifique o SDK da Web no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="d67b7-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d67b7-301">Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados](xref:fundamentals/host/hosted-services)), ESPECIFIQUE o SDK do trabalhador no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="d67b7-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d67b7-302">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="d67b7-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d67b7-303">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="d67b7-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d67b7-304">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d67b7-305">O [RID (identificador de tempo de execução](/dotnet/core/rid-catalog) do Windows) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="d67b7-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="d67b7-306">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="d67b7-307">A propriedade `<SelfContained>` é definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="d67b7-308">Essas propriedades instruem o SDK a gerar um arquivo executável (*.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d67b7-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="d67b7-309">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="d67b7-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d67b7-310">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d67b7-311">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="d67b7-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d67b7-312">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="d67b7-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d67b7-313">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d67b7-314">Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="d67b7-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d67b7-315">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="d67b7-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d67b7-316">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="d67b7-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d67b7-317">Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="d67b7-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d67b7-318">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="d67b7-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="d67b7-319">Uma propriedade `<SelfContained>` está definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="d67b7-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="d67b7-320">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-320">Service user account</span></span>

<span data-ttu-id="d67b7-321">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="d67b7-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d67b7-322">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="d67b7-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d67b7-323">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="d67b7-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d67b7-324">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="d67b7-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d67b7-325">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="d67b7-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d67b7-326">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="d67b7-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d67b7-327">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="d67b7-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d67b7-328">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="d67b7-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d67b7-329">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-329">Log on as a service rights</span></span>

<span data-ttu-id="d67b7-330">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="d67b7-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d67b7-331">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d67b7-332">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d67b7-333">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d67b7-334">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d67b7-335">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="d67b7-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d67b7-336">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="d67b7-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d67b7-337">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-337">Select **Advanced**.</span></span> <span data-ttu-id="d67b7-338">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-338">Select **Find Now**.</span></span> <span data-ttu-id="d67b7-339">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="d67b7-339">Select the user account from the list.</span></span> <span data-ttu-id="d67b7-340">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-340">Select **OK**.</span></span> <span data-ttu-id="d67b7-341">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="d67b7-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d67b7-342">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="d67b7-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d67b7-343">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="d67b7-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d67b7-344">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-344">Create a service</span></span>

<span data-ttu-id="d67b7-345">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d67b7-346">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d67b7-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d67b7-347">`{EXE PATH}`: Caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-347">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d67b7-348">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="d67b7-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d67b7-349">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="d67b7-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d67b7-350">`{DOMAIN OR COMPUTER NAME\USER}`: Conta de usuário de serviço (por exemplo, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-350">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d67b7-351">`{SERVICE NAME}`: Nome do serviço (por exemplo, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-351">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d67b7-352">`{EXE FILE PATH}`: O caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-352">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d67b7-353">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="d67b7-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d67b7-354">`{DESCRIPTION}`: Descrição do serviço (por exemplo, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-354">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d67b7-355">`{DISPLAY NAME}`: Nome de exibição do serviço (por exemplo, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-355">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d67b7-356">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-356">Start a service</span></span>

<span data-ttu-id="d67b7-357">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d67b7-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d67b7-358">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d67b7-359">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-359">Determine a service's status</span></span>

<span data-ttu-id="d67b7-360">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d67b7-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d67b7-361">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="d67b7-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d67b7-362">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-362">Stop a service</span></span>

<span data-ttu-id="d67b7-363">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="d67b7-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d67b7-364">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-364">Remove a service</span></span>

<span data-ttu-id="d67b7-365">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="d67b7-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="d67b7-366">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="d67b7-366">Handle starting and stopping events</span></span>

<span data-ttu-id="d67b7-367">Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="d67b7-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="d67b7-368">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="d67b7-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="d67b7-369">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="d67b7-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="d67b7-370">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="d67b7-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="d67b7-371">Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="d67b7-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d67b7-372">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="d67b7-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d67b7-373">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="d67b7-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d67b7-374">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="d67b7-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d67b7-375">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="d67b7-375">Configure endpoints</span></span>

<span data-ttu-id="d67b7-376">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d67b7-377">Configure a URL e a porta definindo a `ASPNETCORE_URLS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="d67b7-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d67b7-378">Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:</span><span class="sxs-lookup"><span data-stu-id="d67b7-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d67b7-379">As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d67b7-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d67b7-380">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="d67b7-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d67b7-381">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d67b7-382">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="d67b7-382">Current directory and content root</span></span>

<span data-ttu-id="d67b7-383">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d67b7-384">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="d67b7-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d67b7-385">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="d67b7-386">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="d67b7-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="d67b7-387">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="d67b7-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="d67b7-388">Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configurações, chame <xref:System.IO.Directory.SetCurrentDirectory*> o caminho para a [raiz do conteúdo](xref:fundamentals/index#content-root)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="d67b7-389">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d67b7-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d67b7-390">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="d67b7-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d67b7-391">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="d67b7-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d67b7-392">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="d67b7-392">Troubleshoot</span></span>

<span data-ttu-id="d67b7-393">Para solucionar problemas de um aplicativo de serviço do Windows, consulte <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="d67b7-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d67b7-394">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="d67b7-394">Common errors</span></span>

* <span data-ttu-id="d67b7-395">Uma versão antiga ou de pré-lançamento do PowerShell está em uso.</span><span class="sxs-lookup"><span data-stu-id="d67b7-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d67b7-396">O serviço registrado não usa a saída **publicada** do aplicativo do comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="d67b7-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d67b7-397">A saída do comando [dotnet Build](/dotnet/core/tools/dotnet-build) não tem suporte para implantação de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d67b7-398">Os ativos publicados são encontrados em uma das seguintes pastas, dependendo do tipo de implantação:</span><span class="sxs-lookup"><span data-stu-id="d67b7-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d67b7-399">FDD do *compartimento/versão/{estrutura de destino}* (a)</span><span class="sxs-lookup"><span data-stu-id="d67b7-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d67b7-400">*bin/Release/{Framework de destino} identificador de/{Runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="d67b7-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d67b7-401">O serviço não está em estado de execução.</span><span class="sxs-lookup"><span data-stu-id="d67b7-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d67b7-402">Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos.</span><span class="sxs-lookup"><span data-stu-id="d67b7-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d67b7-403">O caminho base de um serviço do Windows é *c: \\ Windows \\ System32*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d67b7-404">O usuário não tem direitos de *logon como um serviço* .</span><span class="sxs-lookup"><span data-stu-id="d67b7-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d67b7-405">A senha do usuário expirou ou foi passada incorretamente ao executar o `New-Service` comando do PowerShell.</span><span class="sxs-lookup"><span data-stu-id="d67b7-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d67b7-406">O aplicativo requer autenticação ASP.NET Core, mas não está configurado para conexões seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d67b7-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d67b7-407">A porta da URL da solicitação está incorreta ou não está configurada corretamente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d67b7-408">Logs de eventos do sistema e do aplicativo</span><span class="sxs-lookup"><span data-stu-id="d67b7-408">System and Application Event Logs</span></span>

<span data-ttu-id="d67b7-409">Acesse os logs de eventos do sistema e do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d67b7-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d67b7-410">Abra o menu Iniciar, procure *Visualizador de eventos*e selecione o aplicativo **Visualizador de eventos** .</span><span class="sxs-lookup"><span data-stu-id="d67b7-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d67b7-411">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d67b7-412">Selecione **sistema** para abrir o log de eventos do sistema.</span><span class="sxs-lookup"><span data-stu-id="d67b7-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d67b7-413">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d67b7-414">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="d67b7-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d67b7-415">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="d67b7-415">Run the app at a command prompt</span></span>

<span data-ttu-id="d67b7-416">Muitos erros de inicialização não produzem informações úteis nos logs de eventos.</span><span class="sxs-lookup"><span data-stu-id="d67b7-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d67b7-417">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="d67b7-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d67b7-418">Para registrar em log detalhes adicionais do aplicativo, reduza o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="d67b7-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d67b7-419">Limpar caches de pacote</span><span class="sxs-lookup"><span data-stu-id="d67b7-419">Clear package caches</span></span>

<span data-ttu-id="d67b7-420">Um aplicativo em funcionamento pode falhar imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d67b7-421">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="d67b7-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d67b7-422">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="d67b7-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d67b7-423">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d67b7-424">Limpe os caches de pacote executando [dotnet NuGet local All--Clear](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="d67b7-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d67b7-425">A limpeza de caches de pacote também pode ser realizada com a ferramenta [NuGet. exe](https://www.nuget.org/downloads) e executando o comando `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="d67b7-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d67b7-426">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="d67b7-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d67b7-427">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="d67b7-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d67b7-428">Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d67b7-429">Aplicativo lento ou travando</span><span class="sxs-lookup"><span data-stu-id="d67b7-429">Slow or hanging app</span></span>

<span data-ttu-id="d67b7-430">Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="d67b7-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d67b7-431">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="d67b7-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="d67b7-432">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="d67b7-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d67b7-433">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d67b7-434">Execute o [script do PowerShell do EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome do executável do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d67b7-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d67b7-435">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="d67b7-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d67b7-436">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="d67b7-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d67b7-437">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="d67b7-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d67b7-438">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d67b7-439">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="d67b7-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d67b7-440">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="d67b7-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d67b7-441">Quando um aplicativo *paralisa* (para de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [arquivos de despejo no modo de usuário: escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o despejo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d67b7-442">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="d67b7-442">Analyze the dump</span></span>

<span data-ttu-id="d67b7-443">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="d67b7-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d67b7-444">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="d67b7-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d67b7-445">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d67b7-445">Additional resources</span></span>

* <span data-ttu-id="d67b7-446">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="d67b7-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d67b7-447">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="d67b7-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d67b7-448">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="d67b7-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d67b7-449">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d67b7-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d67b7-450">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="d67b7-450">Prerequisites</span></span>

* [<span data-ttu-id="d67b7-451">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="d67b7-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d67b7-452">PowerShell 6.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="d67b7-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="d67b7-453">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="d67b7-453">App configuration</span></span>

<span data-ttu-id="d67b7-454">O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="d67b7-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="d67b7-455">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="d67b7-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="d67b7-456">Verifique se o depurador está anexado ou se uma opção `--console` está presente.</span><span class="sxs-lookup"><span data-stu-id="d67b7-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="d67b7-457">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="d67b7-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="d67b7-458">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="d67b7-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="d67b7-459">Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="d67b7-460">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="d67b7-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="d67b7-461">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="d67b7-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="d67b7-462">Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="d67b7-463">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="d67b7-464">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="d67b7-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="d67b7-465">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="d67b7-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="d67b7-466">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="d67b7-467">No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="d67b7-468">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="d67b7-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="d67b7-469">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="d67b7-469">Deployment type</span></span>

<span data-ttu-id="d67b7-470">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="d67b7-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d67b7-471">.</span><span class="sxs-lookup"><span data-stu-id="d67b7-471">SDK</span></span>

<span data-ttu-id="d67b7-472">Para um serviço baseado em aplicativo Web que usa as Razor páginas ou as estruturas MVC, especifique o SDK da Web no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="d67b7-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d67b7-473">Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados](xref:fundamentals/host/hosted-services)), ESPECIFIQUE o SDK do trabalhador no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="d67b7-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d67b7-474">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="d67b7-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d67b7-475">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="d67b7-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d67b7-476">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d67b7-477">O [RID (identificador de tempo de execução](/dotnet/core/rid-catalog) do Windows) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="d67b7-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="d67b7-478">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="d67b7-479">A propriedade `<SelfContained>` é definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="d67b7-480">Essas propriedades instruem o SDK a gerar um arquivo executável (*.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d67b7-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="d67b7-481">A propriedade `<UseAppHost>` é definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="d67b7-482">Essa propriedade fornece o serviço com um caminho de ativação (um arquivo executável *.exe*) para FDD.</span><span class="sxs-lookup"><span data-stu-id="d67b7-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="d67b7-483">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="d67b7-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d67b7-484">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d67b7-485">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="d67b7-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d67b7-486">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="d67b7-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d67b7-487">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d67b7-488">Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="d67b7-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d67b7-489">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="d67b7-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d67b7-490">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="d67b7-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d67b7-491">Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="d67b7-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d67b7-492">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="d67b7-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="d67b7-493">Uma propriedade `<SelfContained>` está definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="d67b7-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="d67b7-494">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-494">Service user account</span></span>

<span data-ttu-id="d67b7-495">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="d67b7-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d67b7-496">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="d67b7-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d67b7-497">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="d67b7-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d67b7-498">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="d67b7-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d67b7-499">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="d67b7-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d67b7-500">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="d67b7-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d67b7-501">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="d67b7-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d67b7-502">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="d67b7-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d67b7-503">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-503">Log on as a service rights</span></span>

<span data-ttu-id="d67b7-504">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="d67b7-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d67b7-505">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d67b7-506">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d67b7-507">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d67b7-508">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d67b7-509">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="d67b7-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d67b7-510">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="d67b7-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d67b7-511">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-511">Select **Advanced**.</span></span> <span data-ttu-id="d67b7-512">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-512">Select **Find Now**.</span></span> <span data-ttu-id="d67b7-513">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="d67b7-513">Select the user account from the list.</span></span> <span data-ttu-id="d67b7-514">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-514">Select **OK**.</span></span> <span data-ttu-id="d67b7-515">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="d67b7-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d67b7-516">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="d67b7-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d67b7-517">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="d67b7-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d67b7-518">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-518">Create a service</span></span>

<span data-ttu-id="d67b7-519">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d67b7-520">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d67b7-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d67b7-521">`{EXE PATH}`: Caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-521">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d67b7-522">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="d67b7-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d67b7-523">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="d67b7-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d67b7-524">`{DOMAIN OR COMPUTER NAME\USER}`: Conta de usuário de serviço (por exemplo, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-524">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d67b7-525">`{SERVICE NAME}`: Nome do serviço (por exemplo, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-525">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d67b7-526">`{EXE FILE PATH}`: O caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-526">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d67b7-527">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="d67b7-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d67b7-528">`{DESCRIPTION}`: Descrição do serviço (por exemplo, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-528">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d67b7-529">`{DISPLAY NAME}`: Nome de exibição do serviço (por exemplo, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="d67b7-529">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d67b7-530">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-530">Start a service</span></span>

<span data-ttu-id="d67b7-531">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d67b7-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d67b7-532">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d67b7-533">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-533">Determine a service's status</span></span>

<span data-ttu-id="d67b7-534">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d67b7-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d67b7-535">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="d67b7-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d67b7-536">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-536">Stop a service</span></span>

<span data-ttu-id="d67b7-537">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="d67b7-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d67b7-538">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="d67b7-538">Remove a service</span></span>

<span data-ttu-id="d67b7-539">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="d67b7-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="d67b7-540">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="d67b7-540">Handle starting and stopping events</span></span>

<span data-ttu-id="d67b7-541">Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="d67b7-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="d67b7-542">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="d67b7-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="d67b7-543">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="d67b7-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="d67b7-544">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="d67b7-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="d67b7-545">Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="d67b7-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d67b7-546">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="d67b7-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d67b7-547">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="d67b7-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d67b7-548">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="d67b7-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d67b7-549">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="d67b7-549">Configure endpoints</span></span>

<span data-ttu-id="d67b7-550">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d67b7-551">Configure a URL e a porta definindo a `ASPNETCORE_URLS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="d67b7-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d67b7-552">Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:</span><span class="sxs-lookup"><span data-stu-id="d67b7-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d67b7-553">As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d67b7-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d67b7-554">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="d67b7-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d67b7-555">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d67b7-556">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="d67b7-556">Current directory and content root</span></span>

<span data-ttu-id="d67b7-557">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d67b7-558">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="d67b7-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d67b7-559">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="d67b7-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="d67b7-560">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="d67b7-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="d67b7-561">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="d67b7-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="d67b7-562">Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configurações, chame <xref:System.IO.Directory.SetCurrentDirectory*> o caminho para a [raiz do conteúdo](xref:fundamentals/index#content-root)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="d67b7-563">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d67b7-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d67b7-564">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="d67b7-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d67b7-565">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="d67b7-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d67b7-566">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="d67b7-566">Troubleshoot</span></span>

<span data-ttu-id="d67b7-567">Para solucionar problemas de um aplicativo de serviço do Windows, consulte <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="d67b7-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d67b7-568">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="d67b7-568">Common errors</span></span>

* <span data-ttu-id="d67b7-569">Uma versão antiga ou de pré-lançamento do PowerShell está em uso.</span><span class="sxs-lookup"><span data-stu-id="d67b7-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d67b7-570">O serviço registrado não usa a saída **publicada** do aplicativo do comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="d67b7-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d67b7-571">A saída do comando [dotnet Build](/dotnet/core/tools/dotnet-build) não tem suporte para implantação de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d67b7-572">Os ativos publicados são encontrados em uma das seguintes pastas, dependendo do tipo de implantação:</span><span class="sxs-lookup"><span data-stu-id="d67b7-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d67b7-573">FDD do *compartimento/versão/{estrutura de destino}* (a)</span><span class="sxs-lookup"><span data-stu-id="d67b7-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d67b7-574">*bin/Release/{Framework de destino} identificador de/{Runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="d67b7-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d67b7-575">O serviço não está em estado de execução.</span><span class="sxs-lookup"><span data-stu-id="d67b7-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d67b7-576">Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos.</span><span class="sxs-lookup"><span data-stu-id="d67b7-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d67b7-577">O caminho base de um serviço do Windows é *c: \\ Windows \\ System32*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d67b7-578">O usuário não tem direitos de *logon como um serviço* .</span><span class="sxs-lookup"><span data-stu-id="d67b7-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d67b7-579">A senha do usuário expirou ou foi passada incorretamente ao executar o `New-Service` comando do PowerShell.</span><span class="sxs-lookup"><span data-stu-id="d67b7-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d67b7-580">O aplicativo requer autenticação ASP.NET Core, mas não está configurado para conexões seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d67b7-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d67b7-581">A porta da URL da solicitação está incorreta ou não está configurada corretamente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d67b7-582">Logs de eventos do sistema e do aplicativo</span><span class="sxs-lookup"><span data-stu-id="d67b7-582">System and Application Event Logs</span></span>

<span data-ttu-id="d67b7-583">Acesse os logs de eventos do sistema e do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d67b7-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d67b7-584">Abra o menu Iniciar, procure *Visualizador de eventos*e selecione o aplicativo **Visualizador de eventos** .</span><span class="sxs-lookup"><span data-stu-id="d67b7-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d67b7-585">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="d67b7-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d67b7-586">Selecione **sistema** para abrir o log de eventos do sistema.</span><span class="sxs-lookup"><span data-stu-id="d67b7-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d67b7-587">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d67b7-588">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="d67b7-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d67b7-589">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="d67b7-589">Run the app at a command prompt</span></span>

<span data-ttu-id="d67b7-590">Muitos erros de inicialização não produzem informações úteis nos logs de eventos.</span><span class="sxs-lookup"><span data-stu-id="d67b7-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d67b7-591">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="d67b7-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d67b7-592">Para registrar em log detalhes adicionais do aplicativo, reduza o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="d67b7-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d67b7-593">Limpar caches de pacote</span><span class="sxs-lookup"><span data-stu-id="d67b7-593">Clear package caches</span></span>

<span data-ttu-id="d67b7-594">Um aplicativo em funcionamento pode falhar imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d67b7-595">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="d67b7-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d67b7-596">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="d67b7-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d67b7-597">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="d67b7-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d67b7-598">Limpe os caches de pacote executando [dotnet NuGet local All--Clear](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="d67b7-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d67b7-599">A limpeza de caches de pacote também pode ser realizada com a ferramenta [NuGet. exe](https://www.nuget.org/downloads) e executando o comando `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="d67b7-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d67b7-600">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="d67b7-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d67b7-601">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="d67b7-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d67b7-602">Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d67b7-603">Aplicativo lento ou travando</span><span class="sxs-lookup"><span data-stu-id="d67b7-603">Slow or hanging app</span></span>

<span data-ttu-id="d67b7-604">Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="d67b7-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d67b7-605">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="d67b7-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="d67b7-606">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="d67b7-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d67b7-607">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="d67b7-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d67b7-608">Execute o [script do PowerShell do EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome do executável do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d67b7-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d67b7-609">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="d67b7-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d67b7-610">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="d67b7-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d67b7-611">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="d67b7-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d67b7-612">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d67b7-613">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="d67b7-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d67b7-614">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="d67b7-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d67b7-615">Quando um aplicativo *paralisa* (para de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [arquivos de despejo no modo de usuário: escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o despejo.</span><span class="sxs-lookup"><span data-stu-id="d67b7-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d67b7-616">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="d67b7-616">Analyze the dump</span></span>

<span data-ttu-id="d67b7-617">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="d67b7-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d67b7-618">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="d67b7-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d67b7-619">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d67b7-619">Additional resources</span></span>

* <span data-ttu-id="d67b7-620">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="d67b7-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

---
<span data-ttu-id="f9b81-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f9b81-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f9b81-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f9b81-102">'Blazor'</span></span>
- <span data-ttu-id="f9b81-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f9b81-103">'Identity'</span></span>
- <span data-ttu-id="f9b81-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f9b81-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f9b81-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f9b81-105">'Razor'</span></span>
- <span data-ttu-id="f9b81-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="f9b81-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="f9b81-107">Hospedar o ASP.NET Core em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="f9b81-107">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f9b81-108">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="f9b81-108">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="f9b81-109">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="f9b81-109">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="f9b81-110">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9b81-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f9b81-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="f9b81-111">Prerequisites</span></span>

* [<span data-ttu-id="f9b81-112">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="f9b81-112">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="f9b81-113">PowerShell 6.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="f9b81-113">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="f9b81-114">Modelo de serviço de trabalho</span><span class="sxs-lookup"><span data-stu-id="f9b81-114">Worker Service template</span></span>

<span data-ttu-id="f9b81-115">O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada.</span><span class="sxs-lookup"><span data-stu-id="f9b81-115">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="f9b81-116">Para usar o modelo como base para um aplicativo de Serviço Windows:</span><span class="sxs-lookup"><span data-stu-id="f9b81-116">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="f9b81-117">Crie um aplicativo de serviço de trabalho usando o modelo do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9b81-117">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="f9b81-118">Siga as orientações na seção [Configuração de aplicativos](#app-configuration) para atualizar o aplicativo do Serviço de Trabalho para que ele possa ser executado como um Serviço Windows.</span><span class="sxs-lookup"><span data-stu-id="f9b81-118">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="f9b81-119">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="f9b81-119">App configuration</span></span>

<span data-ttu-id="f9b81-120">O aplicativo requer uma referência de pacote para [Microsoft. Extensions. host. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="f9b81-120">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="f9b81-121">`IHostBuilder.UseWindowsService`é chamado ao criar o host.</span><span class="sxs-lookup"><span data-stu-id="f9b81-121">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="f9b81-122">Se o aplicativo estiver sendo executado como um Serviço Windows, o método:</span><span class="sxs-lookup"><span data-stu-id="f9b81-122">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="f9b81-123">Define o tempo de vida do host como `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-123">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="f9b81-124">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) como [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="f9b81-124">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="f9b81-125">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="f9b81-125">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="f9b81-126">Habilita o registro em log no log de eventos:</span><span class="sxs-lookup"><span data-stu-id="f9b81-126">Enables logging to the event log:</span></span>
  * <span data-ttu-id="f9b81-127">O nome do aplicativo é usado como o nome de origem padrão.</span><span class="sxs-lookup"><span data-stu-id="f9b81-127">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="f9b81-128">O nível de log padrão é *aviso* ou superior para um aplicativo com base em um modelo de ASP.NET Core que chama `CreateDefaultBuilder` para criar o host.</span><span class="sxs-lookup"><span data-stu-id="f9b81-128">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="f9b81-129">Substitua o nível de log padrão pela `Logging:EventLog:LogLevel:Default` chave em *appSettings. JSON* / *appSettings. { Ambiente}. JSON* ou outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="f9b81-129">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="f9b81-130">Somente administradores podem criar novas fontes de evento.</span><span class="sxs-lookup"><span data-stu-id="f9b81-130">Only administrators can create new event sources.</span></span> <span data-ttu-id="f9b81-131">Quando uma fonte de evento não puder ser criada usando o nome do aplicativo, um aviso será registrado em log como a fonte do *Aplicativo* e os logs de eventos serão desabilitados.</span><span class="sxs-lookup"><span data-stu-id="f9b81-131">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="f9b81-132">Em `CreateHostBuilder` *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f9b81-132">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="f9b81-133">Os aplicativos de exemplo a seguir acompanham este tópico:</span><span class="sxs-lookup"><span data-stu-id="f9b81-133">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="f9b81-134">Exemplo de serviço de trabalho em segundo plano: um aplicativo não Web de exemplo baseado no [modelo de serviço de trabalho](#worker-service-template) que usa [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="f9b81-134">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="f9b81-135">Exemplo de serviço de aplicativo Web: um Razor exemplo de aplicativo Web de páginas que é executado como um serviço do Windows com [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="f9b81-135">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="f9b81-136">Para obter diretrizes do MVC, consulte os artigos em <xref:mvc/overview> e <xref:migration/22-to-30> .</span><span class="sxs-lookup"><span data-stu-id="f9b81-136">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="f9b81-137">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="f9b81-137">Deployment type</span></span>

<span data-ttu-id="f9b81-138">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="f9b81-138">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="f9b81-139">.</span><span class="sxs-lookup"><span data-stu-id="f9b81-139">SDK</span></span>

<span data-ttu-id="f9b81-140">Para um serviço baseado em aplicativo Web que usa as Razor páginas ou as estruturas MVC, especifique o SDK da Web no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="f9b81-140">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="f9b81-141">Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados](xref:fundamentals/host/hosted-services)), ESPECIFIQUE o SDK do trabalhador no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="f9b81-141">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="f9b81-142">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="f9b81-142">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="f9b81-143">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="f9b81-143">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="f9b81-144">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-144">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="f9b81-145">Se você estiver usando o [SDK da Web](#sdk), um arquivo *Web. config* , que normalmente é produzido ao publicar um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="f9b81-145">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="f9b81-146">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-146">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="f9b81-147">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="f9b81-147">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="f9b81-148">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="f9b81-148">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="f9b81-149">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-149">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="f9b81-150">Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="f9b81-150">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="f9b81-151">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="f9b81-151">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="f9b81-152">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="f9b81-152">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="f9b81-153">Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="f9b81-153">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="f9b81-154">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="f9b81-154">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="f9b81-155">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-155">Service user account</span></span>

<span data-ttu-id="f9b81-156">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="f9b81-156">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="f9b81-157">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="f9b81-157">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="f9b81-158">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="f9b81-158">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="f9b81-159">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="f9b81-159">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="f9b81-160">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="f9b81-160">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="f9b81-161">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="f9b81-161">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="f9b81-162">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="f9b81-162">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="f9b81-163">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="f9b81-163">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="f9b81-164">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-164">Log on as a service rights</span></span>

<span data-ttu-id="f9b81-165">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="f9b81-165">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="f9b81-166">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-166">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="f9b81-167">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-167">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="f9b81-168">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-168">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="f9b81-169">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-169">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="f9b81-170">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="f9b81-170">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="f9b81-171">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="f9b81-171">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="f9b81-172">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-172">Select **Advanced**.</span></span> <span data-ttu-id="f9b81-173">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-173">Select **Find Now**.</span></span> <span data-ttu-id="f9b81-174">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="f9b81-174">Select the user account from the list.</span></span> <span data-ttu-id="f9b81-175">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-175">Select **OK**.</span></span> <span data-ttu-id="f9b81-176">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="f9b81-176">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="f9b81-177">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="f9b81-177">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="f9b81-178">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="f9b81-178">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="f9b81-179">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-179">Create a service</span></span>

<span data-ttu-id="f9b81-180">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-180">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="f9b81-181">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f9b81-181">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="f9b81-182">`{EXE PATH}`: Caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-182">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="f9b81-183">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="f9b81-183">Don't include the app's executable in the path.</span></span> <span data-ttu-id="f9b81-184">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="f9b81-184">A trailing slash isn't required.</span></span>
* <span data-ttu-id="f9b81-185">`{DOMAIN OR COMPUTER NAME\USER}`: Conta de usuário de serviço (por exemplo, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-185">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="f9b81-186">`{SERVICE NAME}`: Nome do serviço (por exemplo, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-186">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="f9b81-187">`{EXE FILE PATH}`: O caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-187">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="f9b81-188">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="f9b81-188">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="f9b81-189">`{DESCRIPTION}`: Descrição do serviço (por exemplo, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-189">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="f9b81-190">`{DISPLAY NAME}`: Nome de exibição do serviço (por exemplo, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-190">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="f9b81-191">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-191">Start a service</span></span>

<span data-ttu-id="f9b81-192">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="f9b81-192">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="f9b81-193">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-193">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="f9b81-194">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-194">Determine a service's status</span></span>

<span data-ttu-id="f9b81-195">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="f9b81-195">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="f9b81-196">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="f9b81-196">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="f9b81-197">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-197">Stop a service</span></span>

<span data-ttu-id="f9b81-198">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="f9b81-198">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="f9b81-199">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-199">Remove a service</span></span>

<span data-ttu-id="f9b81-200">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="f9b81-200">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f9b81-201">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="f9b81-201">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f9b81-202">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="f9b81-202">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="f9b81-203">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="f9b81-203">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="f9b81-204">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="f9b81-204">Configure endpoints</span></span>

<span data-ttu-id="f9b81-205">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-205">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="f9b81-206">Configure a URL e a porta definindo a `ASPNETCORE_URLS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="f9b81-206">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="f9b81-207">Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:</span><span class="sxs-lookup"><span data-stu-id="f9b81-207">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="f9b81-208">As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9b81-208">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="f9b81-209">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="f9b81-209">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="f9b81-210">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-210">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="f9b81-211">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="f9b81-211">Current directory and content root</span></span>

<span data-ttu-id="f9b81-212">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-212">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="f9b81-213">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="f9b81-213">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="f9b81-214">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-214">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="f9b81-215">Usar ContentRootPath ou ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="f9b81-215">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="f9b81-216">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> para localizar os recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-216">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="f9b81-217">Quando o aplicativo é executado como um serviço, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> o define <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> como [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="f9b81-217">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="f9b81-218">Os arquivos de configurações padrão do aplicativo, *appSettings. JSON* e *appSettings. { Environment}. JSON*, são carregados a partir da raiz do conteúdo do aplicativo chamando [CreateDefaultBuilder durante a construção do host](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="f9b81-218">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="f9b81-219">Para outros arquivos de configurações carregados pelo código do desenvolvedor no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> , não há necessidade de chamar <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> .</span><span class="sxs-lookup"><span data-stu-id="f9b81-219">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f9b81-220">No exemplo a seguir, o arquivo *custom_settings. JSON* existe na raiz do conteúdo do aplicativo e é carregado sem definir explicitamente um caminho base:</span><span class="sxs-lookup"><span data-stu-id="f9b81-220">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="f9b81-221">Não tente usar <xref:System.IO.Directory.GetCurrentDirectory*> para obter um caminho de recurso porque um aplicativo de serviço do Windows retorna a pasta *C: \\ Windows \\ System32* como seu diretório atual.</span><span class="sxs-lookup"><span data-stu-id="f9b81-221">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="f9b81-222">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="f9b81-222">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="f9b81-223">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="f9b81-223">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f9b81-224">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="f9b81-224">Troubleshoot</span></span>

<span data-ttu-id="f9b81-225">Para solucionar problemas de um aplicativo de serviço do Windows, consulte <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="f9b81-225">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="f9b81-226">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="f9b81-226">Common errors</span></span>

* <span data-ttu-id="f9b81-227">Uma versão antiga ou de pré-lançamento do PowerShell está em uso.</span><span class="sxs-lookup"><span data-stu-id="f9b81-227">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="f9b81-228">O serviço registrado não usa a saída **publicada** do aplicativo do comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="f9b81-228">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="f9b81-229">A saída do comando [dotnet Build](/dotnet/core/tools/dotnet-build) não tem suporte para implantação de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-229">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="f9b81-230">Os ativos publicados são encontrados em uma das seguintes pastas, dependendo do tipo de implantação:</span><span class="sxs-lookup"><span data-stu-id="f9b81-230">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="f9b81-231">FDD do *compartimento/versão/{estrutura de destino}* (a)</span><span class="sxs-lookup"><span data-stu-id="f9b81-231">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="f9b81-232">*bin/Release/{Framework de destino} identificador de/{Runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="f9b81-232">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="f9b81-233">O serviço não está em estado de execução.</span><span class="sxs-lookup"><span data-stu-id="f9b81-233">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="f9b81-234">Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos.</span><span class="sxs-lookup"><span data-stu-id="f9b81-234">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="f9b81-235">O caminho base de um serviço do Windows é *c: \\ Windows \\ System32*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-235">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="f9b81-236">O usuário não tem direitos de *logon como um serviço* .</span><span class="sxs-lookup"><span data-stu-id="f9b81-236">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="f9b81-237">A senha do usuário expirou ou foi passada incorretamente ao executar o `New-Service` comando do PowerShell.</span><span class="sxs-lookup"><span data-stu-id="f9b81-237">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="f9b81-238">O aplicativo requer autenticação ASP.NET Core, mas não está configurado para conexões seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f9b81-238">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="f9b81-239">A porta da URL da solicitação está incorreta ou não está configurada corretamente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-239">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="f9b81-240">Logs de eventos do sistema e do aplicativo</span><span class="sxs-lookup"><span data-stu-id="f9b81-240">System and Application Event Logs</span></span>

<span data-ttu-id="f9b81-241">Acesse os logs de eventos do sistema e do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f9b81-241">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="f9b81-242">Abra o menu Iniciar, procure *Visualizador de eventos*e selecione o aplicativo **Visualizador de eventos** .</span><span class="sxs-lookup"><span data-stu-id="f9b81-242">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="f9b81-243">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-243">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="f9b81-244">Selecione **sistema** para abrir o log de eventos do sistema.</span><span class="sxs-lookup"><span data-stu-id="f9b81-244">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="f9b81-245">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-245">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="f9b81-246">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="f9b81-246">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="f9b81-247">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="f9b81-247">Run the app at a command prompt</span></span>

<span data-ttu-id="f9b81-248">Muitos erros de inicialização não produzem informações úteis nos logs de eventos.</span><span class="sxs-lookup"><span data-stu-id="f9b81-248">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="f9b81-249">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="f9b81-249">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="f9b81-250">Para registrar em log detalhes adicionais do aplicativo, reduza o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f9b81-250">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="f9b81-251">Limpar caches de pacote</span><span class="sxs-lookup"><span data-stu-id="f9b81-251">Clear package caches</span></span>

<span data-ttu-id="f9b81-252">Um aplicativo em funcionamento pode falhar imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-252">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="f9b81-253">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="f9b81-253">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="f9b81-254">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="f9b81-254">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="f9b81-255">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-255">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="f9b81-256">Limpe os caches de pacote executando [dotnet NuGet local All--Clear](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f9b81-256">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="f9b81-257">A limpeza de caches de pacote também pode ser realizada com a ferramenta [NuGet. exe](https://www.nuget.org/downloads) e executando o comando `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="f9b81-257">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="f9b81-258">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="f9b81-258">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="f9b81-259">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="f9b81-259">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="f9b81-260">Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-260">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="f9b81-261">Aplicativo lento ou travando</span><span class="sxs-lookup"><span data-stu-id="f9b81-261">Slow or hanging app</span></span>

<span data-ttu-id="f9b81-262">Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="f9b81-262">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="f9b81-263">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="f9b81-263">App crashes or encounters an exception</span></span>

<span data-ttu-id="f9b81-264">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="f9b81-264">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="f9b81-265">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-265">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="f9b81-266">Execute o [script do PowerShell do EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) com o nome do executável do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f9b81-266">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="f9b81-267">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="f9b81-267">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="f9b81-268">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="f9b81-268">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="f9b81-269">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="f9b81-269">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="f9b81-270">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-270">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="f9b81-271">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="f9b81-271">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="f9b81-272">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="f9b81-272">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="f9b81-273">Quando um aplicativo *paralisa* (para de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [arquivos de despejo no modo de usuário: escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o despejo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-273">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="f9b81-274">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="f9b81-274">Analyze the dump</span></span>

<span data-ttu-id="f9b81-275">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="f9b81-275">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="f9b81-276">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="f9b81-276">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9b81-277">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f9b81-277">Additional resources</span></span>

* <span data-ttu-id="f9b81-278">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="f9b81-278">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f9b81-279">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="f9b81-279">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="f9b81-280">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="f9b81-280">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="f9b81-281">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9b81-281">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f9b81-282">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="f9b81-282">Prerequisites</span></span>

* [<span data-ttu-id="f9b81-283">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="f9b81-283">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="f9b81-284">PowerShell 6.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="f9b81-284">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="f9b81-285">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="f9b81-285">App configuration</span></span>

<span data-ttu-id="f9b81-286">O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="f9b81-286">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="f9b81-287">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="f9b81-287">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="f9b81-288">Verifique se o depurador está anexado ou se uma opção `--console` está presente.</span><span class="sxs-lookup"><span data-stu-id="f9b81-288">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="f9b81-289">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="f9b81-289">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="f9b81-290">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="f9b81-290">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="f9b81-291">Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-291">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="f9b81-292">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="f9b81-292">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="f9b81-293">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="f9b81-293">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="f9b81-294">Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-294">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="f9b81-295">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-295">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="f9b81-296">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="f9b81-296">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="f9b81-297">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="f9b81-297">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="f9b81-298">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-298">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="f9b81-299">No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-299">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="f9b81-300">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="f9b81-300">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="f9b81-301">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="f9b81-301">Deployment type</span></span>

<span data-ttu-id="f9b81-302">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="f9b81-302">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="f9b81-303">.</span><span class="sxs-lookup"><span data-stu-id="f9b81-303">SDK</span></span>

<span data-ttu-id="f9b81-304">Para um serviço baseado em aplicativo Web que usa as Razor páginas ou as estruturas MVC, especifique o SDK da Web no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="f9b81-304">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="f9b81-305">Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados](xref:fundamentals/host/hosted-services)), ESPECIFIQUE o SDK do trabalhador no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="f9b81-305">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="f9b81-306">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="f9b81-306">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="f9b81-307">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="f9b81-307">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="f9b81-308">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-308">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="f9b81-309">O [RID (identificador de tempo de execução](/dotnet/core/rid-catalog) do Windows) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="f9b81-309">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="f9b81-310">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-310">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="f9b81-311">A propriedade `<SelfContained>` é definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-311">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="f9b81-312">Essas propriedades instruem o SDK a gerar um arquivo executável (*.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9b81-312">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="f9b81-313">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="f9b81-313">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="f9b81-314">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-314">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="f9b81-315">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="f9b81-315">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="f9b81-316">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="f9b81-316">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="f9b81-317">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-317">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="f9b81-318">Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="f9b81-318">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="f9b81-319">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="f9b81-319">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="f9b81-320">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="f9b81-320">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="f9b81-321">Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="f9b81-321">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="f9b81-322">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="f9b81-322">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="f9b81-323">Uma propriedade `<SelfContained>` está definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="f9b81-323">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="f9b81-324">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-324">Service user account</span></span>

<span data-ttu-id="f9b81-325">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="f9b81-325">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="f9b81-326">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="f9b81-326">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="f9b81-327">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="f9b81-327">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="f9b81-328">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="f9b81-328">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="f9b81-329">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="f9b81-329">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="f9b81-330">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="f9b81-330">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="f9b81-331">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="f9b81-331">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="f9b81-332">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="f9b81-332">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="f9b81-333">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-333">Log on as a service rights</span></span>

<span data-ttu-id="f9b81-334">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="f9b81-334">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="f9b81-335">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-335">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="f9b81-336">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-336">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="f9b81-337">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-337">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="f9b81-338">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-338">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="f9b81-339">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="f9b81-339">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="f9b81-340">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="f9b81-340">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="f9b81-341">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-341">Select **Advanced**.</span></span> <span data-ttu-id="f9b81-342">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-342">Select **Find Now**.</span></span> <span data-ttu-id="f9b81-343">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="f9b81-343">Select the user account from the list.</span></span> <span data-ttu-id="f9b81-344">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-344">Select **OK**.</span></span> <span data-ttu-id="f9b81-345">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="f9b81-345">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="f9b81-346">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="f9b81-346">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="f9b81-347">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="f9b81-347">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="f9b81-348">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-348">Create a service</span></span>

<span data-ttu-id="f9b81-349">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-349">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="f9b81-350">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f9b81-350">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="f9b81-351">`{EXE PATH}`: Caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-351">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="f9b81-352">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="f9b81-352">Don't include the app's executable in the path.</span></span> <span data-ttu-id="f9b81-353">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="f9b81-353">A trailing slash isn't required.</span></span>
* <span data-ttu-id="f9b81-354">`{DOMAIN OR COMPUTER NAME\USER}`: Conta de usuário de serviço (por exemplo, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-354">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="f9b81-355">`{SERVICE NAME}`: Nome do serviço (por exemplo, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-355">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="f9b81-356">`{EXE FILE PATH}`: O caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-356">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="f9b81-357">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="f9b81-357">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="f9b81-358">`{DESCRIPTION}`: Descrição do serviço (por exemplo, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-358">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="f9b81-359">`{DISPLAY NAME}`: Nome de exibição do serviço (por exemplo, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-359">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="f9b81-360">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-360">Start a service</span></span>

<span data-ttu-id="f9b81-361">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="f9b81-361">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="f9b81-362">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-362">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="f9b81-363">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-363">Determine a service's status</span></span>

<span data-ttu-id="f9b81-364">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="f9b81-364">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="f9b81-365">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="f9b81-365">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="f9b81-366">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-366">Stop a service</span></span>

<span data-ttu-id="f9b81-367">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="f9b81-367">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="f9b81-368">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-368">Remove a service</span></span>

<span data-ttu-id="f9b81-369">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="f9b81-369">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="f9b81-370">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="f9b81-370">Handle starting and stopping events</span></span>

<span data-ttu-id="f9b81-371">Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="f9b81-371">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="f9b81-372">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="f9b81-372">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="f9b81-373">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="f9b81-373">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="f9b81-374">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="f9b81-374">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="f9b81-375">Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="f9b81-375">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f9b81-376">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="f9b81-376">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f9b81-377">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="f9b81-377">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="f9b81-378">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="f9b81-378">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="f9b81-379">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="f9b81-379">Configure endpoints</span></span>

<span data-ttu-id="f9b81-380">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-380">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="f9b81-381">Configure a URL e a porta definindo a `ASPNETCORE_URLS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="f9b81-381">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="f9b81-382">Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:</span><span class="sxs-lookup"><span data-stu-id="f9b81-382">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="f9b81-383">As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9b81-383">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="f9b81-384">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="f9b81-384">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="f9b81-385">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-385">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="f9b81-386">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="f9b81-386">Current directory and content root</span></span>

<span data-ttu-id="f9b81-387">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-387">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="f9b81-388">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="f9b81-388">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="f9b81-389">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-389">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="f9b81-390">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="f9b81-390">Set the content root path to the app's folder</span></span>

<span data-ttu-id="f9b81-391">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="f9b81-391">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="f9b81-392">Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configurações, chame <xref:System.IO.Directory.SetCurrentDirectory*> o caminho para a [raiz do conteúdo](xref:fundamentals/index#content-root)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-392">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="f9b81-393">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f9b81-393">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="f9b81-394">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="f9b81-394">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="f9b81-395">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="f9b81-395">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f9b81-396">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="f9b81-396">Troubleshoot</span></span>

<span data-ttu-id="f9b81-397">Para solucionar problemas de um aplicativo de serviço do Windows, consulte <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="f9b81-397">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="f9b81-398">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="f9b81-398">Common errors</span></span>

* <span data-ttu-id="f9b81-399">Uma versão antiga ou de pré-lançamento do PowerShell está em uso.</span><span class="sxs-lookup"><span data-stu-id="f9b81-399">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="f9b81-400">O serviço registrado não usa a saída **publicada** do aplicativo do comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="f9b81-400">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="f9b81-401">A saída do comando [dotnet Build](/dotnet/core/tools/dotnet-build) não tem suporte para implantação de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-401">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="f9b81-402">Os ativos publicados são encontrados em uma das seguintes pastas, dependendo do tipo de implantação:</span><span class="sxs-lookup"><span data-stu-id="f9b81-402">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="f9b81-403">FDD do *compartimento/versão/{estrutura de destino}* (a)</span><span class="sxs-lookup"><span data-stu-id="f9b81-403">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="f9b81-404">*bin/Release/{Framework de destino} identificador de/{Runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="f9b81-404">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="f9b81-405">O serviço não está em estado de execução.</span><span class="sxs-lookup"><span data-stu-id="f9b81-405">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="f9b81-406">Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos.</span><span class="sxs-lookup"><span data-stu-id="f9b81-406">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="f9b81-407">O caminho base de um serviço do Windows é *c: \\ Windows \\ System32*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-407">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="f9b81-408">O usuário não tem direitos de *logon como um serviço* .</span><span class="sxs-lookup"><span data-stu-id="f9b81-408">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="f9b81-409">A senha do usuário expirou ou foi passada incorretamente ao executar o `New-Service` comando do PowerShell.</span><span class="sxs-lookup"><span data-stu-id="f9b81-409">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="f9b81-410">O aplicativo requer autenticação ASP.NET Core, mas não está configurado para conexões seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f9b81-410">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="f9b81-411">A porta da URL da solicitação está incorreta ou não está configurada corretamente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-411">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="f9b81-412">Logs de eventos do sistema e do aplicativo</span><span class="sxs-lookup"><span data-stu-id="f9b81-412">System and Application Event Logs</span></span>

<span data-ttu-id="f9b81-413">Acesse os logs de eventos do sistema e do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f9b81-413">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="f9b81-414">Abra o menu Iniciar, procure *Visualizador de eventos*e selecione o aplicativo **Visualizador de eventos** .</span><span class="sxs-lookup"><span data-stu-id="f9b81-414">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="f9b81-415">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-415">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="f9b81-416">Selecione **sistema** para abrir o log de eventos do sistema.</span><span class="sxs-lookup"><span data-stu-id="f9b81-416">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="f9b81-417">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-417">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="f9b81-418">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="f9b81-418">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="f9b81-419">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="f9b81-419">Run the app at a command prompt</span></span>

<span data-ttu-id="f9b81-420">Muitos erros de inicialização não produzem informações úteis nos logs de eventos.</span><span class="sxs-lookup"><span data-stu-id="f9b81-420">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="f9b81-421">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="f9b81-421">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="f9b81-422">Para registrar em log detalhes adicionais do aplicativo, reduza o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f9b81-422">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="f9b81-423">Limpar caches de pacote</span><span class="sxs-lookup"><span data-stu-id="f9b81-423">Clear package caches</span></span>

<span data-ttu-id="f9b81-424">Um aplicativo em funcionamento pode falhar imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-424">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="f9b81-425">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="f9b81-425">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="f9b81-426">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="f9b81-426">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="f9b81-427">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-427">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="f9b81-428">Limpe os caches de pacote executando [dotnet NuGet local All--Clear](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f9b81-428">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="f9b81-429">A limpeza de caches de pacote também pode ser realizada com a ferramenta [NuGet. exe](https://www.nuget.org/downloads) e executando o comando `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="f9b81-429">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="f9b81-430">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="f9b81-430">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="f9b81-431">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="f9b81-431">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="f9b81-432">Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-432">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="f9b81-433">Aplicativo lento ou travando</span><span class="sxs-lookup"><span data-stu-id="f9b81-433">Slow or hanging app</span></span>

<span data-ttu-id="f9b81-434">Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="f9b81-434">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="f9b81-435">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="f9b81-435">App crashes or encounters an exception</span></span>

<span data-ttu-id="f9b81-436">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="f9b81-436">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="f9b81-437">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-437">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="f9b81-438">Execute o [script do PowerShell do EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome do executável do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f9b81-438">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="f9b81-439">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="f9b81-439">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="f9b81-440">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="f9b81-440">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="f9b81-441">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="f9b81-441">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="f9b81-442">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-442">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="f9b81-443">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="f9b81-443">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="f9b81-444">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="f9b81-444">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="f9b81-445">Quando um aplicativo *paralisa* (para de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [arquivos de despejo no modo de usuário: escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o despejo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-445">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="f9b81-446">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="f9b81-446">Analyze the dump</span></span>

<span data-ttu-id="f9b81-447">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="f9b81-447">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="f9b81-448">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="f9b81-448">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9b81-449">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f9b81-449">Additional resources</span></span>

* <span data-ttu-id="f9b81-450">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="f9b81-450">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f9b81-451">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="f9b81-451">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="f9b81-452">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="f9b81-452">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="f9b81-453">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9b81-453">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f9b81-454">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="f9b81-454">Prerequisites</span></span>

* [<span data-ttu-id="f9b81-455">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="f9b81-455">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="f9b81-456">PowerShell 6.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="f9b81-456">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="f9b81-457">Configuração de aplicativo</span><span class="sxs-lookup"><span data-stu-id="f9b81-457">App configuration</span></span>

<span data-ttu-id="f9b81-458">O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="f9b81-458">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="f9b81-459">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="f9b81-459">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="f9b81-460">Verifique se o depurador está anexado ou se uma opção `--console` está presente.</span><span class="sxs-lookup"><span data-stu-id="f9b81-460">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="f9b81-461">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="f9b81-461">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="f9b81-462">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="f9b81-462">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="f9b81-463">Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-463">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="f9b81-464">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="f9b81-464">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="f9b81-465">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="f9b81-465">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="f9b81-466">Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-466">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="f9b81-467">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-467">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="f9b81-468">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="f9b81-468">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="f9b81-469">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="f9b81-469">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="f9b81-470">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-470">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="f9b81-471">No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-471">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="f9b81-472">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="f9b81-472">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="f9b81-473">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="f9b81-473">Deployment type</span></span>

<span data-ttu-id="f9b81-474">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="f9b81-474">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="f9b81-475">.</span><span class="sxs-lookup"><span data-stu-id="f9b81-475">SDK</span></span>

<span data-ttu-id="f9b81-476">Para um serviço baseado em aplicativo Web que usa as Razor páginas ou as estruturas MVC, especifique o SDK da Web no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="f9b81-476">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="f9b81-477">Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados](xref:fundamentals/host/hosted-services)), ESPECIFIQUE o SDK do trabalhador no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="f9b81-477">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="f9b81-478">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="f9b81-478">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="f9b81-479">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="f9b81-479">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="f9b81-480">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-480">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="f9b81-481">O [RID (identificador de tempo de execução](/dotnet/core/rid-catalog) do Windows) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="f9b81-481">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="f9b81-482">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-482">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="f9b81-483">A propriedade `<SelfContained>` é definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-483">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="f9b81-484">Essas propriedades instruem o SDK a gerar um arquivo executável (*.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9b81-484">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="f9b81-485">A propriedade `<UseAppHost>` é definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-485">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="f9b81-486">Essa propriedade fornece o serviço com um caminho de ativação (um arquivo executável *.exe*) para FDD.</span><span class="sxs-lookup"><span data-stu-id="f9b81-486">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="f9b81-487">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="f9b81-487">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="f9b81-488">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-488">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="f9b81-489">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="f9b81-489">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="f9b81-490">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="f9b81-490">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="f9b81-491">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-491">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="f9b81-492">Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="f9b81-492">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="f9b81-493">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="f9b81-493">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="f9b81-494">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="f9b81-494">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="f9b81-495">Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="f9b81-495">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="f9b81-496">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="f9b81-496">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="f9b81-497">Uma propriedade `<SelfContained>` está definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="f9b81-497">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="f9b81-498">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-498">Service user account</span></span>

<span data-ttu-id="f9b81-499">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="f9b81-499">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="f9b81-500">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="f9b81-500">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="f9b81-501">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="f9b81-501">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="f9b81-502">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="f9b81-502">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="f9b81-503">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="f9b81-503">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="f9b81-504">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="f9b81-504">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="f9b81-505">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="f9b81-505">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="f9b81-506">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="f9b81-506">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="f9b81-507">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-507">Log on as a service rights</span></span>

<span data-ttu-id="f9b81-508">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="f9b81-508">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="f9b81-509">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-509">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="f9b81-510">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-510">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="f9b81-511">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-511">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="f9b81-512">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-512">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="f9b81-513">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="f9b81-513">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="f9b81-514">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="f9b81-514">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="f9b81-515">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-515">Select **Advanced**.</span></span> <span data-ttu-id="f9b81-516">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-516">Select **Find Now**.</span></span> <span data-ttu-id="f9b81-517">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="f9b81-517">Select the user account from the list.</span></span> <span data-ttu-id="f9b81-518">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-518">Select **OK**.</span></span> <span data-ttu-id="f9b81-519">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="f9b81-519">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="f9b81-520">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="f9b81-520">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="f9b81-521">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="f9b81-521">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="f9b81-522">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-522">Create a service</span></span>

<span data-ttu-id="f9b81-523">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-523">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="f9b81-524">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f9b81-524">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="f9b81-525">`{EXE PATH}`: Caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-525">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="f9b81-526">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="f9b81-526">Don't include the app's executable in the path.</span></span> <span data-ttu-id="f9b81-527">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="f9b81-527">A trailing slash isn't required.</span></span>
* <span data-ttu-id="f9b81-528">`{DOMAIN OR COMPUTER NAME\USER}`: Conta de usuário de serviço (por exemplo, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-528">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="f9b81-529">`{SERVICE NAME}`: Nome do serviço (por exemplo, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-529">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="f9b81-530">`{EXE FILE PATH}`: O caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-530">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="f9b81-531">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="f9b81-531">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="f9b81-532">`{DESCRIPTION}`: Descrição do serviço (por exemplo, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-532">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="f9b81-533">`{DISPLAY NAME}`: Nome de exibição do serviço (por exemplo, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="f9b81-533">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="f9b81-534">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-534">Start a service</span></span>

<span data-ttu-id="f9b81-535">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="f9b81-535">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="f9b81-536">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-536">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="f9b81-537">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-537">Determine a service's status</span></span>

<span data-ttu-id="f9b81-538">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="f9b81-538">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="f9b81-539">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="f9b81-539">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="f9b81-540">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-540">Stop a service</span></span>

<span data-ttu-id="f9b81-541">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="f9b81-541">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="f9b81-542">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="f9b81-542">Remove a service</span></span>

<span data-ttu-id="f9b81-543">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="f9b81-543">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="f9b81-544">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="f9b81-544">Handle starting and stopping events</span></span>

<span data-ttu-id="f9b81-545">Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="f9b81-545">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="f9b81-546">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="f9b81-546">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="f9b81-547">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="f9b81-547">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="f9b81-548">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="f9b81-548">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="f9b81-549">Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="f9b81-549">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f9b81-550">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="f9b81-550">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f9b81-551">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="f9b81-551">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="f9b81-552">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="f9b81-552">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="f9b81-553">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="f9b81-553">Configure endpoints</span></span>

<span data-ttu-id="f9b81-554">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-554">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="f9b81-555">Configure a URL e a porta definindo a `ASPNETCORE_URLS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="f9b81-555">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="f9b81-556">Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:</span><span class="sxs-lookup"><span data-stu-id="f9b81-556">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="f9b81-557">As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9b81-557">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="f9b81-558">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="f9b81-558">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="f9b81-559">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-559">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="f9b81-560">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="f9b81-560">Current directory and content root</span></span>

<span data-ttu-id="f9b81-561">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-561">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="f9b81-562">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="f9b81-562">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="f9b81-563">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="f9b81-563">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="f9b81-564">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="f9b81-564">Set the content root path to the app's folder</span></span>

<span data-ttu-id="f9b81-565">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="f9b81-565">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="f9b81-566">Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configurações, chame <xref:System.IO.Directory.SetCurrentDirectory*> o caminho para a [raiz do conteúdo](xref:fundamentals/index#content-root)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-566">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="f9b81-567">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f9b81-567">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="f9b81-568">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="f9b81-568">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="f9b81-569">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="f9b81-569">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f9b81-570">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="f9b81-570">Troubleshoot</span></span>

<span data-ttu-id="f9b81-571">Para solucionar problemas de um aplicativo de serviço do Windows, consulte <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="f9b81-571">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="f9b81-572">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="f9b81-572">Common errors</span></span>

* <span data-ttu-id="f9b81-573">Uma versão antiga ou de pré-lançamento do PowerShell está em uso.</span><span class="sxs-lookup"><span data-stu-id="f9b81-573">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="f9b81-574">O serviço registrado não usa a saída **publicada** do aplicativo do comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="f9b81-574">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="f9b81-575">A saída do comando [dotnet Build](/dotnet/core/tools/dotnet-build) não tem suporte para implantação de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-575">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="f9b81-576">Os ativos publicados são encontrados em uma das seguintes pastas, dependendo do tipo de implantação:</span><span class="sxs-lookup"><span data-stu-id="f9b81-576">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="f9b81-577">FDD do *compartimento/versão/{estrutura de destino}* (a)</span><span class="sxs-lookup"><span data-stu-id="f9b81-577">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="f9b81-578">*bin/Release/{Framework de destino} identificador de/{Runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="f9b81-578">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="f9b81-579">O serviço não está em estado de execução.</span><span class="sxs-lookup"><span data-stu-id="f9b81-579">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="f9b81-580">Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos.</span><span class="sxs-lookup"><span data-stu-id="f9b81-580">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="f9b81-581">O caminho base de um serviço do Windows é *c: \\ Windows \\ System32*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-581">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="f9b81-582">O usuário não tem direitos de *logon como um serviço* .</span><span class="sxs-lookup"><span data-stu-id="f9b81-582">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="f9b81-583">A senha do usuário expirou ou foi passada incorretamente ao executar o `New-Service` comando do PowerShell.</span><span class="sxs-lookup"><span data-stu-id="f9b81-583">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="f9b81-584">O aplicativo requer autenticação ASP.NET Core, mas não está configurado para conexões seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f9b81-584">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="f9b81-585">A porta da URL da solicitação está incorreta ou não está configurada corretamente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-585">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="f9b81-586">Logs de eventos do sistema e do aplicativo</span><span class="sxs-lookup"><span data-stu-id="f9b81-586">System and Application Event Logs</span></span>

<span data-ttu-id="f9b81-587">Acesse os logs de eventos do sistema e do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f9b81-587">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="f9b81-588">Abra o menu Iniciar, procure *Visualizador de eventos*e selecione o aplicativo **Visualizador de eventos** .</span><span class="sxs-lookup"><span data-stu-id="f9b81-588">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="f9b81-589">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="f9b81-589">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="f9b81-590">Selecione **sistema** para abrir o log de eventos do sistema.</span><span class="sxs-lookup"><span data-stu-id="f9b81-590">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="f9b81-591">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-591">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="f9b81-592">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="f9b81-592">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="f9b81-593">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="f9b81-593">Run the app at a command prompt</span></span>

<span data-ttu-id="f9b81-594">Muitos erros de inicialização não produzem informações úteis nos logs de eventos.</span><span class="sxs-lookup"><span data-stu-id="f9b81-594">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="f9b81-595">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="f9b81-595">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="f9b81-596">Para registrar em log detalhes adicionais do aplicativo, reduza o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f9b81-596">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="f9b81-597">Limpar caches de pacote</span><span class="sxs-lookup"><span data-stu-id="f9b81-597">Clear package caches</span></span>

<span data-ttu-id="f9b81-598">Um aplicativo em funcionamento pode falhar imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-598">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="f9b81-599">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="f9b81-599">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="f9b81-600">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="f9b81-600">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="f9b81-601">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="f9b81-601">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="f9b81-602">Limpe os caches de pacote executando [dotnet NuGet local All--Clear](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f9b81-602">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="f9b81-603">A limpeza de caches de pacote também pode ser realizada com a ferramenta [NuGet. exe](https://www.nuget.org/downloads) e executando o comando `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="f9b81-603">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="f9b81-604">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="f9b81-604">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="f9b81-605">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="f9b81-605">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="f9b81-606">Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-606">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="f9b81-607">Aplicativo lento ou travando</span><span class="sxs-lookup"><span data-stu-id="f9b81-607">Slow or hanging app</span></span>

<span data-ttu-id="f9b81-608">Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="f9b81-608">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="f9b81-609">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="f9b81-609">App crashes or encounters an exception</span></span>

<span data-ttu-id="f9b81-610">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="f9b81-610">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="f9b81-611">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="f9b81-611">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="f9b81-612">Execute o [script do PowerShell do EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome do executável do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f9b81-612">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="f9b81-613">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="f9b81-613">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="f9b81-614">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="f9b81-614">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="f9b81-615">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="f9b81-615">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="f9b81-616">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-616">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="f9b81-617">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="f9b81-617">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="f9b81-618">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="f9b81-618">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="f9b81-619">Quando um aplicativo *paralisa* (para de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [arquivos de despejo no modo de usuário: escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o despejo.</span><span class="sxs-lookup"><span data-stu-id="f9b81-619">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="f9b81-620">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="f9b81-620">Analyze the dump</span></span>

<span data-ttu-id="f9b81-621">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="f9b81-621">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="f9b81-622">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="f9b81-622">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9b81-623">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f9b81-623">Additional resources</span></span>

* <span data-ttu-id="f9b81-624">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="f9b81-624">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

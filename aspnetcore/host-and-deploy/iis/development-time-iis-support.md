---
<span data-ttu-id="072b0-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="072b0-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="072b0-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="072b0-102">'Blazor'</span></span>
- <span data-ttu-id="072b0-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="072b0-103">'Identity'</span></span>
- <span data-ttu-id="072b0-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="072b0-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="072b0-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="072b0-105">'Razor'</span></span>
- <span data-ttu-id="072b0-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="072b0-106">'SignalR' uid:</span></span> 

---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="072b0-107">Suporte ao IIS no tempo de desenvolvimento no Visual Studio para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="072b0-107">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="072b0-108">Por [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="072b0-108">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="072b0-109">Este artigo descreve o suporte do [Visual Studio](https://visualstudio.microsoft.com) para a depuração de aplicativos do ASP.NET Core em execução com o IIS no Windows Server.</span><span class="sxs-lookup"><span data-stu-id="072b0-109">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="072b0-110">Este tópico orienta como habilitar esse cenário e configurar um projeto.</span><span class="sxs-lookup"><span data-stu-id="072b0-110">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="072b0-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="072b0-111">Prerequisites</span></span>

* [<span data-ttu-id="072b0-112">Visual Studio para Windows</span><span class="sxs-lookup"><span data-stu-id="072b0-112">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="072b0-113">Carga de trabalho **ASP.NET e desenvolvimento para a Web**</span><span class="sxs-lookup"><span data-stu-id="072b0-113">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="072b0-114">Carga de trabalho de **desenvolvimento multiplataforma do .NET Core**</span><span class="sxs-lookup"><span data-stu-id="072b0-114">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="072b0-115">Certificado de segurança X.509 (para o suporte a HTTPS)</span><span class="sxs-lookup"><span data-stu-id="072b0-115">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="072b0-116">Habilitar o IIS</span><span class="sxs-lookup"><span data-stu-id="072b0-116">Enable IIS</span></span>

1. <span data-ttu-id="072b0-117">No Windows, navegue até **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="072b0-117">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="072b0-118">Selecione a caixa de seleção **Serviços de Informações da Internet**.</span><span class="sxs-lookup"><span data-stu-id="072b0-118">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="072b0-119">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="072b0-119">Select **OK**.</span></span>

<span data-ttu-id="072b0-120">A instalação do IIS pode exigir uma reinicialização do sistema.</span><span class="sxs-lookup"><span data-stu-id="072b0-120">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="072b0-121">Configurar o IIS</span><span class="sxs-lookup"><span data-stu-id="072b0-121">Configure IIS</span></span>

<span data-ttu-id="072b0-122">O IIS deve ter um site configurado com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="072b0-122">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="072b0-123">**Nome do host**: normalmente, o **site padrão** é usado com um **nome de host** de `localhost` .</span><span class="sxs-lookup"><span data-stu-id="072b0-123">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="072b0-124">No entanto, qualquer site válido do IIS com um nome de host exclusivo funciona.</span><span class="sxs-lookup"><span data-stu-id="072b0-124">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="072b0-125">**Associação do site**</span><span class="sxs-lookup"><span data-stu-id="072b0-125">**Site Binding**</span></span>
  * <span data-ttu-id="072b0-126">Para aplicativos que exijam HTTPS, crie uma associação à porta 443 com um certificado.</span><span class="sxs-lookup"><span data-stu-id="072b0-126">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="072b0-127">Tipicamente, o **Certificado de Desenvolvimento do IIS Express** é usado, mas qualquer certificado válido funciona.</span><span class="sxs-lookup"><span data-stu-id="072b0-127">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="072b0-128">Para aplicativos que usam HTTP, confirme a existência de uma associação à porta 80 ou crie uma para um novo site.</span><span class="sxs-lookup"><span data-stu-id="072b0-128">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="072b0-129">Uso de uma associação simples para HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="072b0-129">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="072b0-130">**Não há suporte para a associação simultânea às portas HTTP e HTTPS.**</span><span class="sxs-lookup"><span data-stu-id="072b0-130">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="072b0-131">Habilitar o suporte ao IIS no tempo de desenvolvimento no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="072b0-131">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="072b0-132">Inicie o Instalador do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="072b0-132">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="072b0-133">Selecione **Modificar** para a instalação do Visual Studio que você planeja usar para o suporte de tempo de desenvolvimento do IIS.</span><span class="sxs-lookup"><span data-stu-id="072b0-133">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="072b0-134">Para a carga de trabalho de **desenvolvimento da web e ASP.NET**, localize e instale o componente de**suporte IIS ao tempo de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="072b0-134">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="072b0-135">O componente está listado na seção **Opcional**, em **Suporte IIS ao tempo de desenvolvimento** no painel **Detalhes da instalação** à direita das cargas de trabalho.</span><span class="sxs-lookup"><span data-stu-id="072b0-135">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="072b0-136">O componente instala o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), que é um módulo nativo do IIS necessário para executar aplicativos ASP.NET Core com o IIS.</span><span class="sxs-lookup"><span data-stu-id="072b0-136">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="072b0-137">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="072b0-137">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="072b0-138">Redirecionamento para HTTPS</span><span class="sxs-lookup"><span data-stu-id="072b0-138">HTTPS redirection</span></span>

<span data-ttu-id="072b0-139">Para um novo projeto que exija HTTPS, selecione a caixa de seleção para **Configurar para HTTPS** na janela **Criar um novo Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="072b0-139">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="072b0-140">A marcação da caixa de seleção adiciona ao aplicativo um [Redirecionamento de HTTPS e Middleware HSTS](xref:security/enforcing-ssl) quando ele é criado.</span><span class="sxs-lookup"><span data-stu-id="072b0-140">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="072b0-141">Para um projeto existente que exija HTTPS, use o Redirecionamento de HTTPS e Middleware HSTS em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="072b0-141">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="072b0-142">Para obter mais informações, consulte <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="072b0-142">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="072b0-143">Para um projeto que usa HTTP, o [Redirecionamento de HTTPS e o Middleware HSTS](xref:security/enforcing-ssl) não são adicionados ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="072b0-143">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="072b0-144">Nenhuma configuração de aplicativo é necessária.</span><span class="sxs-lookup"><span data-stu-id="072b0-144">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="072b0-145">Perfil de inicialização do IIS</span><span class="sxs-lookup"><span data-stu-id="072b0-145">IIS launch profile</span></span>

<span data-ttu-id="072b0-146">Crie um novo perfil de inicialização para adicionar suporte ao IIS no tempo de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="072b0-146">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="072b0-147">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções**.</span><span class="sxs-lookup"><span data-stu-id="072b0-147">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="072b0-148">Selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="072b0-148">Select **Properties**.</span></span> <span data-ttu-id="072b0-149">Abra a guia **depurar** .</span><span class="sxs-lookup"><span data-stu-id="072b0-149">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="072b0-150">Para **Perfil**, selecione o botão **Novo**.</span><span class="sxs-lookup"><span data-stu-id="072b0-150">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="072b0-151">Nomeie o perfil "IIS" na janela pop-up.</span><span class="sxs-lookup"><span data-stu-id="072b0-151">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="072b0-152">Selecione **OK** para criar o perfil.</span><span class="sxs-lookup"><span data-stu-id="072b0-152">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="072b0-153">Para a configuração **Iniciar**, selecione **IIS** da lista.</span><span class="sxs-lookup"><span data-stu-id="072b0-153">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="072b0-154">Selecione a caixa de seleção **Iniciar navegador** e forneça a URL de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="072b0-154">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="072b0-155">Quando o aplicativo exigir HTTPS, use um ponto de extremidade HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="072b0-155">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="072b0-156">Para HTTP, use um ponto de extremidade HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="072b0-156">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="072b0-157">Forneça o mesmo nome de host e porta usados pela [configuração do IIS especificada anteriormente](#configure-iis), normalmente `localhost`.</span><span class="sxs-lookup"><span data-stu-id="072b0-157">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="072b0-158">Forneça o nome do aplicativo no final da URL.</span><span class="sxs-lookup"><span data-stu-id="072b0-158">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="072b0-159">Por exemplo, `https://localhost/WebApplication1` (HTTPS) ou `http://localhost/WebApplication1` (HTTP) são URLs de ponto de extremidade válidas.</span><span class="sxs-lookup"><span data-stu-id="072b0-159">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="072b0-160">Na seção **Variáveis de ambiente**, selecione o botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="072b0-160">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="072b0-161">Forneça uma variável de ambiente com um **Nome** de `ASPNETCORE_ENVIRONMENT` e um **Valor** de `Development`.</span><span class="sxs-lookup"><span data-stu-id="072b0-161">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="072b0-162">Na área de **Configurações do servidor Web**, defina a **URL do aplicativo** com o mesmo valor usado para a URL de ponto de extremidade **Iniciar navegador**.</span><span class="sxs-lookup"><span data-stu-id="072b0-162">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="072b0-163">Para a configuração de **Modelo de hospedagem** no Visual Studio 2019 ou posterior, selecione **Padrão** para usar o modelo de hospedagem usado pelo projeto.</span><span class="sxs-lookup"><span data-stu-id="072b0-163">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="072b0-164">Se o projeto definir a propriedade `<AspNetCoreHostingModel>` no arquivo de projeto, o valor da propriedade (`InProcess` ou `OutOfProcess`) será usado.</span><span class="sxs-lookup"><span data-stu-id="072b0-164">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="072b0-165">Se a propriedade não estiver presente, o modelo do aplicativo de hospedagem padrão será usado, que está em processo.</span><span class="sxs-lookup"><span data-stu-id="072b0-165">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="072b0-166">Se o aplicativo exigir uma configuração explícita de modelo de hospedagem diferente da do modelo de hospedagem normal do aplicativo, defina o **Modelo de hospedagem** para `In Process` ou `Out Of Process`, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="072b0-166">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="072b0-167">Salve o perfil.</span><span class="sxs-lookup"><span data-stu-id="072b0-167">Save the profile.</span></span>

<span data-ttu-id="072b0-168">Quando não estiver usando o Visual Studio, adicione manualmente um perfil de inicialização ao arquivo [launchSettings.json](https://json.schemastore.org/launchsettings) na pasta *Propriedades*.</span><span class="sxs-lookup"><span data-stu-id="072b0-168">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="072b0-169">O exemplo a seguir configura o perfil para usar o protocolo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="072b0-169">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="072b0-170">Confirme se os pontos de extremidade `applicationUrl` e `launchUrl` correspondem e usam o mesmo protocolo que a configuração de associação do IIS, seja HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="072b0-170">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="072b0-171">Executar o projeto</span><span class="sxs-lookup"><span data-stu-id="072b0-171">Run the project</span></span>

<span data-ttu-id="072b0-172">Execute o Visual Studio como um administrador:</span><span class="sxs-lookup"><span data-stu-id="072b0-172">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="072b0-173">Confirme se a lista de lista suspensa de configuração de compilação está definida para **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="072b0-173">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="072b0-174">Defina o [botão Iniciar Depuração](/visualstudio/debugger/debugger-feature-tour) para o perfil do **IIS** e selecione o botão para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="072b0-174">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="072b0-175">O Visual Studio poderá solicitar uma reinicialização se não estiver executando como administrador.</span><span class="sxs-lookup"><span data-stu-id="072b0-175">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="072b0-176">Se solicitado, reinicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="072b0-176">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="072b0-177">Se for usado um certificado de desenvolvimento não confiável, o navegador poderá exigir a criação de uma exceção para o certificado não confiável.</span><span class="sxs-lookup"><span data-stu-id="072b0-177">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="072b0-178">A depuração de uma configuração de Compilação de versão com [Apenas Meu Código](/visualstudio/debugger/just-my-code) e otimizações de compilador resulta em uma experiência inadequada.</span><span class="sxs-lookup"><span data-stu-id="072b0-178">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="072b0-179">Por exemplo, os pontos de interrupção não são atingidos.</span><span class="sxs-lookup"><span data-stu-id="072b0-179">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="072b0-180">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="072b0-180">Additional resources</span></span>

* [<span data-ttu-id="072b0-181">Introdução ao Gerenciador do IIS no IIS</span><span class="sxs-lookup"><span data-stu-id="072b0-181">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="072b0-182">Este artigo descreve o suporte do [Visual Studio](https://visualstudio.microsoft.com) para a depuração de aplicativos do ASP.NET Core em execução com o IIS no Windows Server.</span><span class="sxs-lookup"><span data-stu-id="072b0-182">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="072b0-183">Este tópico orienta como habilitar esse cenário e configurar um projeto.</span><span class="sxs-lookup"><span data-stu-id="072b0-183">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="072b0-184">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="072b0-184">Prerequisites</span></span>

* [<span data-ttu-id="072b0-185">Visual Studio para Windows</span><span class="sxs-lookup"><span data-stu-id="072b0-185">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="072b0-186">Carga de trabalho **ASP.NET e desenvolvimento para a Web**</span><span class="sxs-lookup"><span data-stu-id="072b0-186">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="072b0-187">Carga de trabalho de **desenvolvimento multiplataforma do .NET Core**</span><span class="sxs-lookup"><span data-stu-id="072b0-187">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="072b0-188">Certificado de segurança X.509 (para o suporte a HTTPS)</span><span class="sxs-lookup"><span data-stu-id="072b0-188">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="072b0-189">Habilitar o IIS</span><span class="sxs-lookup"><span data-stu-id="072b0-189">Enable IIS</span></span>

1. <span data-ttu-id="072b0-190">No Windows, navegue até **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="072b0-190">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="072b0-191">Selecione a caixa de seleção **Serviços de Informações da Internet**.</span><span class="sxs-lookup"><span data-stu-id="072b0-191">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="072b0-192">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="072b0-192">Select **OK**.</span></span>

<span data-ttu-id="072b0-193">A instalação do IIS pode exigir uma reinicialização do sistema.</span><span class="sxs-lookup"><span data-stu-id="072b0-193">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="072b0-194">Configurar o IIS</span><span class="sxs-lookup"><span data-stu-id="072b0-194">Configure IIS</span></span>

<span data-ttu-id="072b0-195">O IIS deve ter um site configurado com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="072b0-195">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="072b0-196">**Nome do host**: normalmente, o **site padrão** é usado com um **nome de host** de `localhost` .</span><span class="sxs-lookup"><span data-stu-id="072b0-196">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="072b0-197">No entanto, qualquer site válido do IIS com um nome de host exclusivo funciona.</span><span class="sxs-lookup"><span data-stu-id="072b0-197">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="072b0-198">**Associação do site**</span><span class="sxs-lookup"><span data-stu-id="072b0-198">**Site Binding**</span></span>
  * <span data-ttu-id="072b0-199">Para aplicativos que exijam HTTPS, crie uma associação à porta 443 com um certificado.</span><span class="sxs-lookup"><span data-stu-id="072b0-199">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="072b0-200">Tipicamente, o **Certificado de Desenvolvimento do IIS Express** é usado, mas qualquer certificado válido funciona.</span><span class="sxs-lookup"><span data-stu-id="072b0-200">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="072b0-201">Para aplicativos que usam HTTP, confirme a existência de uma associação à porta 80 ou crie uma para um novo site.</span><span class="sxs-lookup"><span data-stu-id="072b0-201">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="072b0-202">Uso de uma associação simples para HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="072b0-202">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="072b0-203">**Não há suporte para a associação simultânea às portas HTTP e HTTPS.**</span><span class="sxs-lookup"><span data-stu-id="072b0-203">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="072b0-204">Habilitar o suporte ao IIS no tempo de desenvolvimento no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="072b0-204">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="072b0-205">Inicie o Instalador do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="072b0-205">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="072b0-206">Selecione **Modificar** para a instalação do Visual Studio que você planeja usar para o suporte de tempo de desenvolvimento do IIS.</span><span class="sxs-lookup"><span data-stu-id="072b0-206">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="072b0-207">Para a carga de trabalho de **desenvolvimento da web e ASP.NET**, localize e instale o componente de**suporte IIS ao tempo de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="072b0-207">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="072b0-208">O componente está listado na seção **Opcional**, em **Suporte IIS ao tempo de desenvolvimento** no painel **Detalhes da instalação** à direita das cargas de trabalho.</span><span class="sxs-lookup"><span data-stu-id="072b0-208">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="072b0-209">O componente instala o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), que é um módulo nativo do IIS necessário para executar aplicativos ASP.NET Core com o IIS.</span><span class="sxs-lookup"><span data-stu-id="072b0-209">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="072b0-210">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="072b0-210">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="072b0-211">Redirecionamento para HTTPS</span><span class="sxs-lookup"><span data-stu-id="072b0-211">HTTPS redirection</span></span>

<span data-ttu-id="072b0-212">Para um novo projeto que exija HTTPS, selecione a caixa de seleção para **Configurar para HTTPS** na janela **Criar um novo Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="072b0-212">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="072b0-213">A marcação da caixa de seleção adiciona ao aplicativo um [Redirecionamento de HTTPS e Middleware HSTS](xref:security/enforcing-ssl) quando ele é criado.</span><span class="sxs-lookup"><span data-stu-id="072b0-213">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="072b0-214">Para um projeto existente que exija HTTPS, use o Redirecionamento de HTTPS e Middleware HSTS em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="072b0-214">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="072b0-215">Para obter mais informações, consulte <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="072b0-215">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="072b0-216">Para um projeto que usa HTTP, o [Redirecionamento de HTTPS e o Middleware HSTS](xref:security/enforcing-ssl) não são adicionados ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="072b0-216">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="072b0-217">Nenhuma configuração de aplicativo é necessária.</span><span class="sxs-lookup"><span data-stu-id="072b0-217">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="072b0-218">Perfil de inicialização do IIS</span><span class="sxs-lookup"><span data-stu-id="072b0-218">IIS launch profile</span></span>

<span data-ttu-id="072b0-219">Crie um novo perfil de inicialização para adicionar suporte ao IIS no tempo de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="072b0-219">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="072b0-220">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções**.</span><span class="sxs-lookup"><span data-stu-id="072b0-220">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="072b0-221">Selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="072b0-221">Select **Properties**.</span></span> <span data-ttu-id="072b0-222">Abra a guia **depurar** .</span><span class="sxs-lookup"><span data-stu-id="072b0-222">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="072b0-223">Para **Perfil**, selecione o botão **Novo**.</span><span class="sxs-lookup"><span data-stu-id="072b0-223">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="072b0-224">Nomeie o perfil "IIS" na janela pop-up.</span><span class="sxs-lookup"><span data-stu-id="072b0-224">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="072b0-225">Selecione **OK** para criar o perfil.</span><span class="sxs-lookup"><span data-stu-id="072b0-225">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="072b0-226">Para a configuração **Iniciar**, selecione **IIS** da lista.</span><span class="sxs-lookup"><span data-stu-id="072b0-226">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="072b0-227">Selecione a caixa de seleção **Iniciar navegador** e forneça a URL de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="072b0-227">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="072b0-228">Quando o aplicativo exigir HTTPS, use um ponto de extremidade HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="072b0-228">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="072b0-229">Para HTTP, use um ponto de extremidade HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="072b0-229">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="072b0-230">Forneça o mesmo nome de host e porta usados pela [configuração do IIS especificada anteriormente](#configure-iis), normalmente `localhost`.</span><span class="sxs-lookup"><span data-stu-id="072b0-230">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="072b0-231">Forneça o nome do aplicativo no final da URL.</span><span class="sxs-lookup"><span data-stu-id="072b0-231">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="072b0-232">Por exemplo, `https://localhost/WebApplication1` (HTTPS) ou `http://localhost/WebApplication1` (HTTP) são URLs de ponto de extremidade válidas.</span><span class="sxs-lookup"><span data-stu-id="072b0-232">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="072b0-233">Na seção **Variáveis de ambiente**, selecione o botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="072b0-233">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="072b0-234">Forneça uma variável de ambiente com um **Nome** de `ASPNETCORE_ENVIRONMENT` e um **Valor** de `Development`.</span><span class="sxs-lookup"><span data-stu-id="072b0-234">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="072b0-235">Na área de **Configurações do servidor Web**, defina a **URL do aplicativo** com o mesmo valor usado para a URL de ponto de extremidade **Iniciar navegador**.</span><span class="sxs-lookup"><span data-stu-id="072b0-235">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="072b0-236">Para a configuração de **Modelo de hospedagem** no Visual Studio 2019 ou posterior, selecione **Padrão** para usar o modelo de hospedagem usado pelo projeto.</span><span class="sxs-lookup"><span data-stu-id="072b0-236">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="072b0-237">Se o projeto definir a propriedade `<AspNetCoreHostingModel>` no arquivo de projeto, o valor da propriedade (`InProcess` ou `OutOfProcess`) será usado.</span><span class="sxs-lookup"><span data-stu-id="072b0-237">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="072b0-238">Se a propriedade não estiver presente, o modelo do aplicativo de hospedagem padrão será usado, que está fora de processo.</span><span class="sxs-lookup"><span data-stu-id="072b0-238">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="072b0-239">Se o aplicativo exigir uma configuração explícita de modelo de hospedagem diferente da do modelo de hospedagem normal do aplicativo, defina o **Modelo de hospedagem** para `In Process` ou `Out Of Process`, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="072b0-239">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="072b0-240">Salve o perfil.</span><span class="sxs-lookup"><span data-stu-id="072b0-240">Save the profile.</span></span>

<span data-ttu-id="072b0-241">Quando não estiver usando o Visual Studio, adicione manualmente um perfil de inicialização ao arquivo [launchSettings.json](https://json.schemastore.org/launchsettings) na pasta *Propriedades*.</span><span class="sxs-lookup"><span data-stu-id="072b0-241">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="072b0-242">O exemplo a seguir configura o perfil para usar o protocolo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="072b0-242">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="072b0-243">Confirme se os pontos de extremidade `applicationUrl` e `launchUrl` correspondem e usam o mesmo protocolo que a configuração de associação do IIS, seja HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="072b0-243">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="072b0-244">Executar o projeto</span><span class="sxs-lookup"><span data-stu-id="072b0-244">Run the project</span></span>

<span data-ttu-id="072b0-245">Execute o Visual Studio como um administrador:</span><span class="sxs-lookup"><span data-stu-id="072b0-245">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="072b0-246">Confirme se a lista de lista suspensa de configuração de compilação está definida para **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="072b0-246">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="072b0-247">Defina o [botão Iniciar Depuração](/visualstudio/debugger/debugger-feature-tour) para o perfil do **IIS** e selecione o botão para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="072b0-247">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="072b0-248">O Visual Studio poderá solicitar uma reinicialização se não estiver executando como administrador.</span><span class="sxs-lookup"><span data-stu-id="072b0-248">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="072b0-249">Se solicitado, reinicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="072b0-249">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="072b0-250">Se for usado um certificado de desenvolvimento não confiável, o navegador poderá exigir a criação de uma exceção para o certificado não confiável.</span><span class="sxs-lookup"><span data-stu-id="072b0-250">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="072b0-251">A depuração de uma configuração de Compilação de versão com [Apenas Meu Código](/visualstudio/debugger/just-my-code) e otimizações de compilador resulta em uma experiência inadequada.</span><span class="sxs-lookup"><span data-stu-id="072b0-251">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="072b0-252">Por exemplo, os pontos de interrupção não são atingidos.</span><span class="sxs-lookup"><span data-stu-id="072b0-252">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="072b0-253">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="072b0-253">Additional resources</span></span>

* [<span data-ttu-id="072b0-254">Introdução ao Gerenciador do IIS no IIS</span><span class="sxs-lookup"><span data-stu-id="072b0-254">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

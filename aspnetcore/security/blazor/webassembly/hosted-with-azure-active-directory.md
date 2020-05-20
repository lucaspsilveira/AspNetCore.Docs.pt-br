---
<span data-ttu-id="9eeab-101">Título: ' proteger um Blazor aplicativo hospedado Webassembly ASP.NET Core com Azure Active Directory ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9eeab-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9eeab-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9eeab-102">'Blazor'</span></span>
- <span data-ttu-id="9eeab-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9eeab-103">'Identity'</span></span>
- <span data-ttu-id="9eeab-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9eeab-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="9eeab-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9eeab-105">'Razor'</span></span>
- <span data-ttu-id="9eeab-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="9eeab-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="9eeab-107">Proteger um Blazor aplicativo hospedado Webassembly ASP.NET Core com Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="9eeab-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="9eeab-108">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9eeab-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9eeab-109">Este artigo descreve como criar um [ Blazor aplicativo hospedado Webassembly](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="9eeab-109">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="9eeab-110">Registrar aplicativos no AAD e criar a solução</span><span class="sxs-lookup"><span data-stu-id="9eeab-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="9eeab-111">Criar um locatário</span><span class="sxs-lookup"><span data-stu-id="9eeab-111">Create a tenant</span></span>

<span data-ttu-id="9eeab-112">Siga as orientações em [início rápido: configurar um locatário](/azure/active-directory/develop/quickstart-create-new-tenant) para criar um locatário no AAD.</span><span class="sxs-lookup"><span data-stu-id="9eeab-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="9eeab-113">Registrar um aplicativo de API do servidor</span><span class="sxs-lookup"><span data-stu-id="9eeab-113">Register a server API app</span></span>

<span data-ttu-id="9eeab-114">Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *aplicativo de API do servidor*:</span><span class="sxs-lookup"><span data-stu-id="9eeab-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="9eeab-115">Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="9eeab-116">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor servidor AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="9eeab-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="9eeab-117">Escolha um **tipo de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="9eeab-118">Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="9eeab-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="9eeab-119">O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="9eeab-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="9eeab-120">Desabilite a caixa de seleção **permissões**  >  **conceder administrador concento para OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="9eeab-120">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9eeab-121">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-121">Select **Register**.</span></span>

<span data-ttu-id="9eeab-122">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="9eeab-122">Record the following information:</span></span>

* <span data-ttu-id="9eeab-123">*Aplicativo de API do servidor* ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="9eeab-123">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="9eeab-124">ID do diretório (ID do locatário) (por exemplo, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="9eeab-124">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="9eeab-125">Domínio de locatário do AAD (por exemplo, `contoso.onmicrosoft.com` ) &ndash; o domínio está disponível como o **domínio do Publicador** na folha de **identidade visual** do portal do Azure para o aplicativo registrado.</span><span class="sxs-lookup"><span data-stu-id="9eeab-125">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="9eeab-126">Em **permissões de API**, remova a permissão **Microsoft Graph**  >  **User. Read** , pois o aplicativo não requer acesso de entrada ou perfil de usuário.</span><span class="sxs-lookup"><span data-stu-id="9eeab-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="9eeab-127">No **expor uma API**:</span><span class="sxs-lookup"><span data-stu-id="9eeab-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="9eeab-128">Selecione **Adicionar um escopo**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="9eeab-129">Selecione **Salvar e continuar**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="9eeab-130">Forneça um **nome de escopo** (por exemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="9eeab-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="9eeab-131">Forneça um **nome de exibição de consentimento do administrador** (por exemplo, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="9eeab-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="9eeab-132">Forneça uma **Descrição de consentimento do administrador** (por exemplo, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="9eeab-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="9eeab-133">Confirme se o **estado** está definido como **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="9eeab-134">Selecione **Adicionar escopo**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-134">Select **Add scope**.</span></span>

<span data-ttu-id="9eeab-135">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="9eeab-135">Record the following information:</span></span>

* <span data-ttu-id="9eeab-136">URI da ID do aplicativo (por exemplo,, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` ou o valor personalizado que você forneceu)</span><span class="sxs-lookup"><span data-stu-id="9eeab-136">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="9eeab-137">Escopo padrão (por exemplo, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="9eeab-137">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="9eeab-138">Registrar um aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="9eeab-138">Register a client app</span></span>

<span data-ttu-id="9eeab-139">Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *aplicativo cliente*:</span><span class="sxs-lookup"><span data-stu-id="9eeab-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="9eeab-140">Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="9eeab-141">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor cliente AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="9eeab-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="9eeab-142">Escolha um **tipo de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="9eeab-143">Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="9eeab-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="9eeab-144">Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="9eeab-144">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="9eeab-145">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="9eeab-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="9eeab-146">Por IIS Express, a porta gerada aleatoriamente pode ser encontrada nas propriedades do aplicativo do servidor no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="9eeab-146">For IIS Express, the randomly generated port can be found in the Server app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="9eeab-147">Desabilite a caixa de seleção **permissões**  >  **conceder administrador concento para OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="9eeab-147">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9eeab-148">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-148">Select **Register**.</span></span>

<span data-ttu-id="9eeab-149">Registre a ID do aplicativo de aplicativo *cliente* (ID do cliente) (por exemplo, `33333333-3333-3333-3333-333333333333` ).</span><span class="sxs-lookup"><span data-stu-id="9eeab-149">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="9eeab-150">Em **Authentication**  >  **configurações da plataforma**de autenticação  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="9eeab-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="9eeab-151">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="9eeab-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="9eeab-152">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="9eeab-153">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="9eeab-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="9eeab-154">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-154">Select the **Save** button.</span></span>

<span data-ttu-id="9eeab-155">Em **permissões de API**:</span><span class="sxs-lookup"><span data-stu-id="9eeab-155">In **API permissions**:</span></span>

1. <span data-ttu-id="9eeab-156">Confirme se o aplicativo tem a permissão **Microsoft Graph**  >  **User. Read** .</span><span class="sxs-lookup"><span data-stu-id="9eeab-156">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="9eeab-157">Selecione **Adicionar uma permissão** seguida por **minhas APIs**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-157">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="9eeab-158">Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, \*\* Blazor servidor AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="9eeab-158">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="9eeab-159">Abra a lista de **APIs** .</span><span class="sxs-lookup"><span data-stu-id="9eeab-159">Open the **API** list.</span></span>
1. <span data-ttu-id="9eeab-160">Habilite o acesso à API (por exemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="9eeab-160">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="9eeab-161">Escolha **Adicionar permissões**.</span><span class="sxs-lookup"><span data-stu-id="9eeab-161">Select **Add permissions**.</span></span>
1. <span data-ttu-id="9eeab-162">Selecione o botão **conceder conteúdo do administrador para {nome do locatário}** .</span><span class="sxs-lookup"><span data-stu-id="9eeab-162">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="9eeab-163">Selecione **Sim** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="9eeab-163">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="9eeab-164">Criar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="9eeab-164">Create the app</span></span>

<span data-ttu-id="9eeab-165">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="9eeab-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="9eeab-166">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="9eeab-166">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="9eeab-167">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="9eeab-167">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="9eeab-168">Passe o URI da ID do aplicativo para a `app-id-uri` opção, mas observe que uma alteração de configuração pode ser necessária no aplicativo cliente, que é descrito na seção [escopos de token de acesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="9eeab-168">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="9eeab-169">Configuração de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="9eeab-169">Server app configuration</span></span>

<span data-ttu-id="9eeab-170">*Esta seção pertence ao aplicativo de **servidor** da solução.*</span><span class="sxs-lookup"><span data-stu-id="9eeab-170">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9eeab-171">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="9eeab-171">Authentication package</span></span>

<span data-ttu-id="9eeab-172">O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo `Microsoft.AspNetCore.Authentication.AzureAD.UI` :</span><span class="sxs-lookup"><span data-stu-id="9eeab-172">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.2.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="9eeab-173">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="9eeab-173">Authentication service support</span></span>

<span data-ttu-id="9eeab-174">O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="9eeab-174">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="9eeab-175">O `AddAzureADBearer` método configura os parâmetros específicos no manipulador de portador JWT necessários para validar tokens emitidos pelo Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="9eeab-175">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="9eeab-176">`UseAuthentication`e `UseAuthorization` Verifique se:</span><span class="sxs-lookup"><span data-stu-id="9eeab-176">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="9eeab-177">O aplicativo tenta analisar e validar tokens em solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="9eeab-177">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="9eeab-178">Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.</span><span class="sxs-lookup"><span data-stu-id="9eeab-178">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="9eeab-179">Usuário. Identity . Nomes</span><span class="sxs-lookup"><span data-stu-id="9eeab-179">User.Identity.Name</span></span>

<span data-ttu-id="9eeab-180">Por padrão, a API do aplicativo de servidor popula `User.Identity.Name` com o valor do `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo de declaração (por exemplo, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="9eeab-180">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="9eeab-181">Para configurar o aplicativo para receber o valor do `name` tipo de declaração, configure o [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9eeab-181">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="9eeab-182">Configurações de aplicativo</span><span class="sxs-lookup"><span data-stu-id="9eeab-182">App settings</span></span>

<span data-ttu-id="9eeab-183">O arquivo *appSettings. JSON* contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso:</span><span class="sxs-lookup"><span data-stu-id="9eeab-183">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="9eeab-184">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="9eeab-184">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="9eeab-185">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="9eeab-185">WeatherForecast controller</span></span>

<span data-ttu-id="9eeab-186">O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida com o `[Authorize]` atributo aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="9eeab-186">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="9eeab-187">É **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="9eeab-187">It's **important** to understand that:</span></span>

* <span data-ttu-id="9eeab-188">O `[Authorize]` atributo nesse controlador de API é a única coisa que protege essa API contra o acesso não autorizado.</span><span class="sxs-lookup"><span data-stu-id="9eeab-188">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="9eeab-189">O `[Authorize]` atributo usado no Blazor aplicativo Webassembly serve apenas como uma dica para o aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="9eeab-189">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="9eeab-190">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="9eeab-190">Client app configuration</span></span>

<span data-ttu-id="9eeab-191">*Esta seção pertence ao aplicativo **cliente** da solução.*</span><span class="sxs-lookup"><span data-stu-id="9eeab-191">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9eeab-192">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="9eeab-192">Authentication package</span></span>

<span data-ttu-id="9eeab-193">Quando um aplicativo é criado para usar contas corporativas ou de estudante ( `SingleOrg` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="9eeab-193">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="9eeab-194">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="9eeab-194">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="9eeab-195">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9eeab-195">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="9eeab-196">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9eeab-196">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="9eeab-197">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="9eeab-197">Authentication service support</span></span>

<span data-ttu-id="9eeab-198">O suporte para `HttpClient` instâncias é adicionado que inclui tokens de acesso ao fazer solicitações ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="9eeab-198">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="9eeab-199">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9eeab-199">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="9eeab-200">O suporte para autenticação de usuários é registrado no contêiner de serviço com o `AddMsalAuthentication` método de extensão fornecido pelo `Microsoft.Authentication.WebAssembly.Msal` pacote.</span><span class="sxs-lookup"><span data-stu-id="9eeab-200">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="9eeab-201">Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="9eeab-201">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="9eeab-202">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9eeab-202">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="9eeab-203">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9eeab-203">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="9eeab-204">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9eeab-204">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="9eeab-205">A configuração é fornecida pelo arquivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9eeab-205">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="9eeab-206">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="9eeab-206">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="9eeab-207">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="9eeab-207">Access token scopes</span></span>

<span data-ttu-id="9eeab-208">Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:</span><span class="sxs-lookup"><span data-stu-id="9eeab-208">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="9eeab-209">Incluído por padrão na solicitação de entrada.</span><span class="sxs-lookup"><span data-stu-id="9eeab-209">Included by default in the sign in request.</span></span>
* <span data-ttu-id="9eeab-210">Usado para provisionar um token de acesso imediatamente após a autenticação.</span><span class="sxs-lookup"><span data-stu-id="9eeab-210">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="9eeab-211">Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="9eeab-211">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="9eeab-212">Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="9eeab-212">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="9eeab-213">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="9eeab-213">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="9eeab-214">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="9eeab-214">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="9eeab-215">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="9eeab-215">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="9eeab-216">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="9eeab-216">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="9eeab-217">Página de índice</span><span class="sxs-lookup"><span data-stu-id="9eeab-217">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="9eeab-218">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="9eeab-218">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="9eeab-219">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="9eeab-219">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="9eeab-220">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="9eeab-220">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="9eeab-221">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="9eeab-221">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="9eeab-222">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="9eeab-222">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="9eeab-223">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="9eeab-223">Run the app</span></span>

<span data-ttu-id="9eeab-224">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="9eeab-224">Run the app from the Server project.</span></span> <span data-ttu-id="9eeab-225">Ao usar o Visual Studio, seja:</span><span class="sxs-lookup"><span data-stu-id="9eeab-225">When using Visual Studio, either:</span></span>

* <span data-ttu-id="9eeab-226">Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .</span><span class="sxs-lookup"><span data-stu-id="9eeab-226">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="9eeab-227">Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="9eeab-227">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="9eeab-228">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9eeab-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="9eeab-229">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="9eeab-229">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="9eeab-230">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="9eeab-230">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

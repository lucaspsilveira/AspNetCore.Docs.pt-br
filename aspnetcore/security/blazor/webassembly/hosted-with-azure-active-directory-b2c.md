---
<span data-ttu-id="3df16-101">Título: ' proteger um Blazor aplicativo hospedado Webassembly ASP.NET Core com Azure Active Directory B2C ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3df16-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3df16-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3df16-102">'Blazor'</span></span>
- <span data-ttu-id="3df16-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3df16-103">'Identity'</span></span>
- <span data-ttu-id="3df16-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3df16-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="3df16-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3df16-105">'Razor'</span></span>
- <span data-ttu-id="3df16-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="3df16-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="3df16-107">Proteger um Blazor aplicativo hospedado Webassembly ASP.NET Core com Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="3df16-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="3df16-108">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3df16-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3df16-109">Este artigo descreve como criar um Blazor aplicativo Webassembly autônomo que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="3df16-109">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="3df16-110">Registrar aplicativos em AAD B2C e criar solução</span><span class="sxs-lookup"><span data-stu-id="3df16-110">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="3df16-111">Criar um locatário</span><span class="sxs-lookup"><span data-stu-id="3df16-111">Create a tenant</span></span>

<span data-ttu-id="3df16-112">Siga as orientações em [tutorial: criar um locatário de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para criar um locatário de AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="3df16-112">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="3df16-113">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="3df16-113">Record the following information:</span></span>

* <span data-ttu-id="3df16-114">Instância de AAD B2C (por exemplo, `https://contoso.b2clogin.com/` , que inclui a barra à direita)</span><span class="sxs-lookup"><span data-stu-id="3df16-114">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="3df16-115">AAD B2C domínio de locatário (por exemplo, `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="3df16-115">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="3df16-116">Registrar um aplicativo de API do servidor</span><span class="sxs-lookup"><span data-stu-id="3df16-116">Register a server API app</span></span>

<span data-ttu-id="3df16-117">Siga as orientações em [tutorial: registrar um aplicativo no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar um aplicativo do AAD para o *aplicativo de API do servidor*:</span><span class="sxs-lookup"><span data-stu-id="3df16-117">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="3df16-118">Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.</span><span class="sxs-lookup"><span data-stu-id="3df16-118">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="3df16-119">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor servidor AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="3df16-119">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="3df16-120">Para **tipos de conta com suporte**, selecione a opção multilocatário: **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="3df16-120">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="3df16-121">O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="3df16-121">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="3df16-122">Confirme se **as permissões**  >  **concedem a decento do administrador a OpenID e offline_access permissões** estão habilitadas.</span><span class="sxs-lookup"><span data-stu-id="3df16-122">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="3df16-123">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="3df16-123">Select **Register**.</span></span>

<span data-ttu-id="3df16-124">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="3df16-124">Record the following information:</span></span>

* <span data-ttu-id="3df16-125">*Aplicativo de API do servidor* ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="3df16-125">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="3df16-126">ID do diretório (ID do locatário) (por exemplo, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="3df16-126">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="3df16-127">Domínio de locatário do AAD (por exemplo, `contoso.onmicrosoft.com` ) &ndash; o domínio está disponível como o **domínio do Publicador** na folha de **identidade visual** do portal do Azure para o aplicativo registrado.</span><span class="sxs-lookup"><span data-stu-id="3df16-127">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="3df16-128">No **expor uma API**:</span><span class="sxs-lookup"><span data-stu-id="3df16-128">In **Expose an API**:</span></span>

1. <span data-ttu-id="3df16-129">Selecione **Adicionar um escopo**.</span><span class="sxs-lookup"><span data-stu-id="3df16-129">Select **Add a scope**.</span></span>
1. <span data-ttu-id="3df16-130">Selecione **Salvar e continuar**.</span><span class="sxs-lookup"><span data-stu-id="3df16-130">Select **Save and continue**.</span></span>
1. <span data-ttu-id="3df16-131">Forneça um **nome de escopo** (por exemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="3df16-131">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="3df16-132">Forneça um **nome de exibição de consentimento do administrador** (por exemplo, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="3df16-132">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="3df16-133">Forneça uma **Descrição de consentimento do administrador** (por exemplo, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="3df16-133">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="3df16-134">Confirme se o **estado** está definido como **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="3df16-134">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="3df16-135">Selecione **Adicionar escopo**.</span><span class="sxs-lookup"><span data-stu-id="3df16-135">Select **Add scope**.</span></span>

<span data-ttu-id="3df16-136">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="3df16-136">Record the following information:</span></span>

* <span data-ttu-id="3df16-137">URI da ID do aplicativo (por exemplo,, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` ou o valor personalizado que você forneceu)</span><span class="sxs-lookup"><span data-stu-id="3df16-137">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="3df16-138">Escopo padrão (por exemplo, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="3df16-138">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="3df16-139">Registrar um aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="3df16-139">Register a client app</span></span>

<span data-ttu-id="3df16-140">Siga as orientações em [tutorial: registrar um aplicativo em Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) novamente para registrar um aplicativo do AAD para o *aplicativo cliente*:</span><span class="sxs-lookup"><span data-stu-id="3df16-140">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="3df16-141">Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.</span><span class="sxs-lookup"><span data-stu-id="3df16-141">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="3df16-142">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor AAD B2C cliente\*\*).</span><span class="sxs-lookup"><span data-stu-id="3df16-142">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="3df16-143">Para **tipos de conta com suporte**, selecione a opção multilocatário: **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="3df16-143">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="3df16-144">Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="3df16-144">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="3df16-145">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="3df16-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="3df16-146">Por IIS Express, a porta gerada aleatoriamente pode ser encontrada nas propriedades do aplicativo do servidor no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="3df16-146">For IIS Express, the randomly generated port can be found in the Server app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="3df16-147">Confirme se **as permissões**  >  **concedem a decento do administrador a OpenID e offline_access permissões** estão habilitadas.</span><span class="sxs-lookup"><span data-stu-id="3df16-147">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="3df16-148">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="3df16-148">Select **Register**.</span></span>

<span data-ttu-id="3df16-149">Registre a ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="3df16-149">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="3df16-150">Em **Authentication**  >  **configurações da plataforma**de autenticação  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="3df16-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="3df16-151">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="3df16-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="3df16-152">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="3df16-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="3df16-153">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="3df16-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="3df16-154">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="3df16-154">Select the **Save** button.</span></span>

<span data-ttu-id="3df16-155">Em **permissões de API**:</span><span class="sxs-lookup"><span data-stu-id="3df16-155">In **API permissions**:</span></span>

1. <span data-ttu-id="3df16-156">Selecione **Adicionar uma permissão** seguida por **minhas APIs**.</span><span class="sxs-lookup"><span data-stu-id="3df16-156">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="3df16-157">Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, \*\* Blazor servidor AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="3df16-157">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="3df16-158">Abra a lista de **APIs** .</span><span class="sxs-lookup"><span data-stu-id="3df16-158">Open the **API** list.</span></span>
1. <span data-ttu-id="3df16-159">Habilite o acesso à API (por exemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="3df16-159">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="3df16-160">Escolha **Adicionar permissões**.</span><span class="sxs-lookup"><span data-stu-id="3df16-160">Select **Add permissions**.</span></span>
1. <span data-ttu-id="3df16-161">Selecione o botão **conceder conteúdo do administrador para {nome do locatário}** .</span><span class="sxs-lookup"><span data-stu-id="3df16-161">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="3df16-162">Selecione **Sim** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="3df16-162">Select **Yes** to confirm.</span></span>

<span data-ttu-id="3df16-163">Em **casa**  >  **Azure ad B2C**  >  **fluxos de usuário**:</span><span class="sxs-lookup"><span data-stu-id="3df16-163">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="3df16-164">Criar um fluxo de usuário de inscrição e de entrada</span><span class="sxs-lookup"><span data-stu-id="3df16-164">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="3df16-165">No mínimo, selecione o atributo de usuário nome de exibição de **declarações do aplicativo**  >  **Display Name** para popular o `context.User.Identity.Name` no `LoginDisplay` componente (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="3df16-165">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="3df16-166">Registre o nome do fluxo de usuário de entrada e de entrada criado para o aplicativo (por exemplo, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="3df16-166">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="3df16-167">Criar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="3df16-167">Create the app</span></span>

<span data-ttu-id="3df16-168">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3df16-168">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="3df16-169">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="3df16-169">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="3df16-170">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="3df16-170">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="3df16-171">Passe o URI da ID do aplicativo para a `app-id-uri` opção, mas observe que uma alteração de configuração pode ser necessária no aplicativo cliente, que é descrito na seção [escopos de token de acesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="3df16-171">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="3df16-172">Além disso, o escopo configurado pelo modelo hospedado Blazor pode ter o host de URI de ID de aplicativo repetido.</span><span class="sxs-lookup"><span data-stu-id="3df16-172">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="3df16-173">Confirme se o escopo configurado para a `DefaultAccessTokenScopes` coleção está correto em `Program.Main` (*Program.cs*) do *aplicativo cliente*.</span><span class="sxs-lookup"><span data-stu-id="3df16-173">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="3df16-174">Configuração de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="3df16-174">Server app configuration</span></span>

<span data-ttu-id="3df16-175">*Esta seção pertence ao aplicativo de **servidor** da solução.*</span><span class="sxs-lookup"><span data-stu-id="3df16-175">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="3df16-176">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="3df16-176">Authentication package</span></span>

<span data-ttu-id="3df16-177">O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo `Microsoft.AspNetCore.Authentication.AzureADB2C.UI` :</span><span class="sxs-lookup"><span data-stu-id="3df16-177">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.2.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="3df16-178">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="3df16-178">Authentication service support</span></span>

<span data-ttu-id="3df16-179">O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="3df16-179">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="3df16-180">O `AddAzureADB2CBearer` método configura os parâmetros específicos no manipulador de portador JWT necessários para validar tokens emitidos pelo Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="3df16-180">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="3df16-181">`UseAuthentication`e `UseAuthorization` Verifique se:</span><span class="sxs-lookup"><span data-stu-id="3df16-181">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="3df16-182">O aplicativo tenta analisar e validar tokens em solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="3df16-182">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="3df16-183">Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.</span><span class="sxs-lookup"><span data-stu-id="3df16-183">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="3df16-184">Usuário. Identity . Nomes</span><span class="sxs-lookup"><span data-stu-id="3df16-184">User.Identity.Name</span></span>

<span data-ttu-id="3df16-185">Por padrão, o `User.Identity.Name` não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="3df16-185">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="3df16-186">Para configurar o aplicativo para receber o valor do `name` tipo de declaração, configure o [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3df16-186">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="3df16-187">Configurações de aplicativo</span><span class="sxs-lookup"><span data-stu-id="3df16-187">App settings</span></span>

<span data-ttu-id="3df16-188">O arquivo *appSettings. JSON* contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso.</span><span class="sxs-lookup"><span data-stu-id="3df16-188">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="3df16-189">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="3df16-189">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="3df16-190">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="3df16-190">WeatherForecast controller</span></span>

<span data-ttu-id="3df16-191">O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida com o `[Authorize]` atributo aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="3df16-191">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="3df16-192">É **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="3df16-192">It's **important** to understand that:</span></span>

* <span data-ttu-id="3df16-193">O `[Authorize]` atributo nesse controlador de API é a única coisa que protege essa API contra o acesso não autorizado.</span><span class="sxs-lookup"><span data-stu-id="3df16-193">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="3df16-194">O `[Authorize]` atributo usado no Blazor aplicativo Webassembly serve apenas como uma dica para o aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="3df16-194">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="3df16-195">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="3df16-195">Client app configuration</span></span>

<span data-ttu-id="3df16-196">*Esta seção pertence ao aplicativo **cliente** da solução.*</span><span class="sxs-lookup"><span data-stu-id="3df16-196">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="3df16-197">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="3df16-197">Authentication package</span></span>

<span data-ttu-id="3df16-198">Quando um aplicativo é criado para usar uma conta individual do B2C ( `IndividualB2C` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="3df16-198">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="3df16-199">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="3df16-199">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="3df16-200">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="3df16-200">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="3df16-201">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3df16-201">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="3df16-202">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="3df16-202">Authentication service support</span></span>

<span data-ttu-id="3df16-203">O suporte para `HttpClient` instâncias é adicionado que inclui tokens de acesso ao fazer solicitações ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="3df16-203">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="3df16-204">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3df16-204">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="3df16-205">O suporte para autenticação de usuários é registrado no contêiner de serviço com o `AddMsalAuthentication` método de extensão fornecido pelo `Microsoft.Authentication.WebAssembly.Msal` pacote.</span><span class="sxs-lookup"><span data-stu-id="3df16-205">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="3df16-206">Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="3df16-206">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="3df16-207">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3df16-207">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="3df16-208">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3df16-208">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="3df16-209">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3df16-209">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="3df16-210">A configuração é fornecida pelo arquivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="3df16-210">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="3df16-211">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="3df16-211">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="3df16-212">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="3df16-212">Access token scopes</span></span>

<span data-ttu-id="3df16-213">Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:</span><span class="sxs-lookup"><span data-stu-id="3df16-213">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="3df16-214">Incluído por padrão na solicitação de entrada.</span><span class="sxs-lookup"><span data-stu-id="3df16-214">Included by default in the sign in request.</span></span>
* <span data-ttu-id="3df16-215">Usado para provisionar um token de acesso imediatamente após a autenticação.</span><span class="sxs-lookup"><span data-stu-id="3df16-215">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="3df16-216">Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="3df16-216">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="3df16-217">Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="3df16-217">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="3df16-218">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="3df16-218">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="3df16-219">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="3df16-219">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="3df16-220">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="3df16-220">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="3df16-221">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="3df16-221">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="3df16-222">Página de índice</span><span class="sxs-lookup"><span data-stu-id="3df16-222">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="3df16-223">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="3df16-223">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="3df16-224">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="3df16-224">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="3df16-225">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="3df16-225">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="3df16-226">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="3df16-226">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="3df16-227">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="3df16-227">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="3df16-228">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="3df16-228">Run the app</span></span>

<span data-ttu-id="3df16-229">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="3df16-229">Run the app from the Server project.</span></span> <span data-ttu-id="3df16-230">Ao usar o Visual Studio, seja:</span><span class="sxs-lookup"><span data-stu-id="3df16-230">When using Visual Studio, either:</span></span>

* <span data-ttu-id="3df16-231">Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .</span><span class="sxs-lookup"><span data-stu-id="3df16-231">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="3df16-232">Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="3df16-232">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="3df16-233">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3df16-233">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="3df16-234">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="3df16-234">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="3df16-235">Tutorial - Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="3df16-235">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="3df16-236">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="3df16-236">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

---
<span data-ttu-id="5c4af-101">Título: "proteger um Blazor aplicativo autônomo Webassembly ASP.NET Core com contas da Microsoft" autor: Descrição: monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5c4af-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5c4af-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5c4af-102">'Blazor'</span></span>
- <span data-ttu-id="5c4af-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5c4af-103">'Identity'</span></span>
- <span data-ttu-id="5c4af-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5c4af-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5c4af-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5c4af-105">'Razor'</span></span>
- <span data-ttu-id="5c4af-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="5c4af-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="5c4af-107">Proteger um Blazor aplicativo autônomo Webassembly ASP.NET Core com contas da Microsoft</span><span class="sxs-lookup"><span data-stu-id="5c4af-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="5c4af-108">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5c4af-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5c4af-109">Para criar um Blazor aplicativo Webassembly autônomo que usa [contas da Microsoft com Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="5c4af-109">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="5c4af-110">Criar um locatário do AAD e um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="5c4af-110">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="5c4af-111">Registre um aplicativo do AAD na área **Azure Active Directory**  >  **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="5c4af-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="5c4af-112">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor contas da Microsoft do AAD autônomo\*\*).</span><span class="sxs-lookup"><span data-stu-id="5c4af-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="5c4af-113">Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional**.</span><span class="sxs-lookup"><span data-stu-id="5c4af-113">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="5c4af-114">Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="5c4af-114">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="5c4af-115">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="5c4af-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="5c4af-116">Por IIS Express, a porta gerada aleatoriamente pode ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="5c4af-116">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="5c4af-117">Desabilite a caixa de seleção **permissões**  >  **conceder administrador concento para OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="5c4af-117">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="5c4af-118">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="5c4af-118">Select **Register**.</span></span>

<span data-ttu-id="5c4af-119">Registre a ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="5c4af-119">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="5c4af-120">Em **Authentication**  >  **configurações da plataforma**de autenticação  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="5c4af-120">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="5c4af-121">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="5c4af-121">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="5c4af-122">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="5c4af-122">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="5c4af-123">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="5c4af-123">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="5c4af-124">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="5c4af-124">Select the **Save** button.</span></span>

<span data-ttu-id="5c4af-125">Criar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5c4af-125">Create the app.</span></span> <span data-ttu-id="5c4af-126">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="5c4af-126">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="5c4af-127">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="5c4af-127">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="5c4af-128">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="5c4af-128">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="5c4af-129">Depois de criar o aplicativo, você deve ser capaz de:</span><span class="sxs-lookup"><span data-stu-id="5c4af-129">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="5c4af-130">Faça logon no aplicativo usando um conta Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5c4af-130">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="5c4af-131">Solicitar tokens de acesso para APIs da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5c4af-131">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="5c4af-132">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="5c4af-132">For more information, see:</span></span>
  * [<span data-ttu-id="5c4af-133">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="5c4af-133">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="5c4af-134">[Início rápido: configurar um aplicativo para expor APIs da Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="5c4af-134">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="5c4af-135">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="5c4af-135">Authentication package</span></span>

<span data-ttu-id="5c4af-136">Quando um aplicativo é criado para usar contas corporativas ou de estudante ( `SingleOrg` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="5c4af-136">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="5c4af-137">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="5c4af-137">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="5c4af-138">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5c4af-138">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="5c4af-139">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5c4af-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="5c4af-140">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="5c4af-140">Authentication service support</span></span>

<span data-ttu-id="5c4af-141">O suporte para autenticação de usuários é registrado no contêiner de serviço com o `AddMsalAuthentication` método de extensão fornecido pelo `Microsoft.Authentication.WebAssembly.Msal` pacote.</span><span class="sxs-lookup"><span data-stu-id="5c4af-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="5c4af-142">Esse método configura todos os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="5c4af-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="5c4af-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5c4af-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="5c4af-144">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5c4af-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="5c4af-145">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5c4af-145">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="5c4af-146">A configuração é fornecida pelo arquivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="5c4af-146">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="5c4af-147">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="5c4af-147">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="5c4af-148">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="5c4af-148">Access token scopes</span></span>

<span data-ttu-id="5c4af-149">O Blazor modelo Webassembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="5c4af-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="5c4af-150">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão do `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="5c4af-150">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="5c4af-151">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="5c4af-151">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="5c4af-152">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="5c4af-152">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="5c4af-153">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="5c4af-153">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="5c4af-154">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="5c4af-154">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="5c4af-155">Página de índice</span><span class="sxs-lookup"><span data-stu-id="5c4af-155">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="5c4af-156">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="5c4af-156">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="5c4af-157">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="5c4af-157">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="5c4af-158">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="5c4af-158">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="5c4af-159">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="5c4af-159">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="5c4af-160">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5c4af-160">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="5c4af-161">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="5c4af-161">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="5c4af-162">Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="5c4af-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="5c4af-163">Início Rápido: Configurar um aplicativo para expor APIs Web</span><span class="sxs-lookup"><span data-stu-id="5c4af-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)

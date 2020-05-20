---
<span data-ttu-id="8e283-101">Título: ' proteger um Blazor aplicativo autônomo Webassembly ASP.NET Core com Azure Active Directory ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8e283-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8e283-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8e283-102">'Blazor'</span></span>
- <span data-ttu-id="8e283-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8e283-103">'Identity'</span></span>
- <span data-ttu-id="8e283-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8e283-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="8e283-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8e283-105">'Razor'</span></span>
- <span data-ttu-id="8e283-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8e283-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="8e283-107">Proteger um Blazor aplicativo autônomo Webassembly ASP.NET Core com Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="8e283-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="8e283-108">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8e283-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8e283-109">Para criar um Blazor aplicativo Webassembly autônomo que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="8e283-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="8e283-110">[Criar um locatário do AAD e um aplicativo Web](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="8e283-110">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="8e283-111">Registre um aplicativo do AAD na área **Azure Active Directory**  >  **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="8e283-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="8e283-112">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor AAD autônomo\*\*).</span><span class="sxs-lookup"><span data-stu-id="8e283-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="8e283-113">Escolha um **tipo de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="8e283-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="8e283-114">Você pode selecionar **contas neste diretório organizacional somente** para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="8e283-114">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="8e283-115">Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="8e283-115">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="8e283-116">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="8e283-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="8e283-117">Por IIS Express, a porta gerada aleatoriamente pode ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="8e283-117">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="8e283-118">Desabilite a caixa de seleção **permissões**  >  **conceder administrador concento para OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="8e283-118">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="8e283-119">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="8e283-119">Select **Register**.</span></span>

<span data-ttu-id="8e283-120">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="8e283-120">Record the following information:</span></span>

* <span data-ttu-id="8e283-121">ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="8e283-121">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="8e283-122">ID do diretório (ID do locatário) (por exemplo, `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="8e283-122">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="8e283-123">Em **Authentication**  >  **configurações da plataforma**de autenticação  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="8e283-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="8e283-124">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="8e283-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="8e283-125">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="8e283-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="8e283-126">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="8e283-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="8e283-127">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="8e283-127">Select the **Save** button.</span></span>

<span data-ttu-id="8e283-128">Criar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8e283-128">Create the app.</span></span> <span data-ttu-id="8e283-129">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="8e283-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="8e283-130">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="8e283-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="8e283-131">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="8e283-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="8e283-132">Depois de criar o aplicativo, você deve ser capaz de:</span><span class="sxs-lookup"><span data-stu-id="8e283-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="8e283-133">Faça logon no aplicativo usando uma conta de usuário do AAD.</span><span class="sxs-lookup"><span data-stu-id="8e283-133">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="8e283-134">Solicitar tokens de acesso para APIs da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="8e283-134">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="8e283-135">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="8e283-135">For more information, see:</span></span>
  * [<span data-ttu-id="8e283-136">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="8e283-136">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="8e283-137">[Início rápido: configurar um aplicativo para expor APIs da Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="8e283-137">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="8e283-138">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="8e283-138">Authentication package</span></span>

<span data-ttu-id="8e283-139">Quando um aplicativo é criado para usar contas corporativas ou de estudante ( `SingleOrg` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="8e283-139">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="8e283-140">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="8e283-140">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="8e283-141">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8e283-141">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="8e283-142">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8e283-142">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="8e283-143">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="8e283-143">Authentication service support</span></span>

<span data-ttu-id="8e283-144">O suporte para autenticação de usuários é registrado no contêiner de serviço com o `AddMsalAuthentication` método de extensão fornecido pelo `Microsoft.Authentication.WebAssembly.Msal` pacote.</span><span class="sxs-lookup"><span data-stu-id="8e283-144">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="8e283-145">Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="8e283-145">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="8e283-146">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8e283-146">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="8e283-147">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8e283-147">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="8e283-148">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8e283-148">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="8e283-149">A configuração é fornecida pelo arquivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8e283-149">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="8e283-150">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="8e283-150">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="8e283-151">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="8e283-151">Access token scopes</span></span>

<span data-ttu-id="8e283-152">O Blazor modelo Webassembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="8e283-152">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="8e283-153">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão do `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="8e283-153">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="8e283-154">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="8e283-154">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="8e283-155">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="8e283-155">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="8e283-156">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="8e283-156">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="8e283-157">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="8e283-157">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="8e283-158">Página de índice</span><span class="sxs-lookup"><span data-stu-id="8e283-158">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="8e283-159">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="8e283-159">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="8e283-160">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="8e283-160">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="8e283-161">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="8e283-161">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="8e283-162">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="8e283-162">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="8e283-163">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8e283-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="8e283-164">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="8e283-164">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="8e283-165">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="8e283-165">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

---
<span data-ttu-id="e8f30-101">Título: ' proteger um Blazor aplicativo autônomo Webassembly ASP.NET Core com Azure Active Directory ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e8f30-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e8f30-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e8f30-102">'Blazor'</span></span>
- <span data-ttu-id="e8f30-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e8f30-103">'Identity'</span></span>
- <span data-ttu-id="e8f30-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e8f30-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e8f30-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e8f30-105">'Razor'</span></span>
- <span data-ttu-id="e8f30-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="e8f30-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="e8f30-107">Proteger um Blazor aplicativo autônomo Webassembly ASP.NET Core com Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="e8f30-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="e8f30-108">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e8f30-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e8f30-109">Para criar um Blazor aplicativo Webassembly autônomo que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="e8f30-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="e8f30-110">[Criar um locatário do AAD e um aplicativo Web](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="e8f30-110">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="e8f30-111">Registre um aplicativo do AAD na área **Azure Active Directory**  >  **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="e8f30-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e8f30-112">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor AAD autônomo\*\*).</span><span class="sxs-lookup"><span data-stu-id="e8f30-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="e8f30-113">Escolha um **tipo de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="e8f30-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="e8f30-114">Você pode selecionar **contas neste diretório organizacional somente** para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="e8f30-114">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="e8f30-115">Deixe a lista suspensa **URI de redirecionamento** definida como **Web** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="e8f30-115">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="e8f30-116">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="e8f30-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="e8f30-117">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8f30-117">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="e8f30-118">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="e8f30-118">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="e8f30-119">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="e8f30-119">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="e8f30-120">Um comentário aparece mais adiante neste tópico para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="e8f30-120">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="e8f30-121">Desabilite a caixa de seleção **permissões**  >  **conceder administrador concento para OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="e8f30-121">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="e8f30-122">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="e8f30-122">Select **Register**.</span></span>

<span data-ttu-id="e8f30-123">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="e8f30-123">Record the following information:</span></span>

* <span data-ttu-id="e8f30-124">ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="e8f30-124">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="e8f30-125">ID do diretório (ID do locatário) (por exemplo, `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="e8f30-125">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="e8f30-126">Em **Authentication**  >  **configurações da plataforma**de autenticação  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="e8f30-126">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="e8f30-127">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="e8f30-127">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e8f30-128">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="e8f30-128">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e8f30-129">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="e8f30-129">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e8f30-130">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="e8f30-130">Select the **Save** button.</span></span>

<span data-ttu-id="e8f30-131">Criar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8f30-131">Create the app.</span></span> <span data-ttu-id="e8f30-132">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="e8f30-132">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="e8f30-133">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="e8f30-133">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="e8f30-134">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="e8f30-134">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="e8f30-135">No portal do Azure, o **Authentication**  >  URI de redirecionamento da Web de**configurações da plataforma**de autenticação do aplicativo  >  **Web**  >  **Redirect URI** é configurado para a porta 5001 para aplicativos executados no servidor Kestrel com as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="e8f30-135">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="e8f30-136">Se o aplicativo for executado em uma porta IIS Express aleatória, a porta do aplicativo poderá ser encontrada nas propriedades do aplicativo no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="e8f30-136">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="e8f30-137">Se a porta não foi configurada anteriormente com a porta conhecida do aplicativo, retorne ao registro do aplicativo na portal do Azure e atualize o URI de redirecionamento com a porta correta.</span><span class="sxs-lookup"><span data-stu-id="e8f30-137">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="e8f30-138">Depois de criar o aplicativo, você deve ser capaz de:</span><span class="sxs-lookup"><span data-stu-id="e8f30-138">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="e8f30-139">Faça logon no aplicativo usando uma conta de usuário do AAD.</span><span class="sxs-lookup"><span data-stu-id="e8f30-139">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="e8f30-140">Solicitar tokens de acesso para APIs da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e8f30-140">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="e8f30-141">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="e8f30-141">For more information, see:</span></span>
  * [<span data-ttu-id="e8f30-142">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="e8f30-142">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="e8f30-143">[Início rápido: configurar um aplicativo para expor APIs da Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="e8f30-143">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="e8f30-144">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="e8f30-144">Authentication package</span></span>

<span data-ttu-id="e8f30-145">Quando um aplicativo é criado para usar contas corporativas ou de estudante ( `SingleOrg` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="e8f30-145">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="e8f30-146">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="e8f30-146">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e8f30-147">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e8f30-147">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="e8f30-148">O `Microsoft.Authentication.WebAssembly.Msal` pacote adiciona transitivamente o `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8f30-148">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="e8f30-149">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="e8f30-149">Authentication service support</span></span>

<span data-ttu-id="e8f30-150">O suporte para autenticação de usuários é registrado no contêiner de serviço com o `AddMsalAuthentication` método de extensão fornecido pelo `Microsoft.Authentication.WebAssembly.Msal` pacote.</span><span class="sxs-lookup"><span data-stu-id="e8f30-150">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="e8f30-151">Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="e8f30-151">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e8f30-152">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e8f30-152">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="e8f30-153">O `AddMsalAuthentication` método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8f30-153">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e8f30-154">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8f30-154">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="e8f30-155">A configuração é fornecida pelo arquivo *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="e8f30-155">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="e8f30-156">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="e8f30-156">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="e8f30-157">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="e8f30-157">Access token scopes</span></span>

<span data-ttu-id="e8f30-158">O Blazor modelo Webassembly não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="e8f30-158">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="e8f30-159">Para provisionar um token de acesso como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão do `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="e8f30-159">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="e8f30-160">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="e8f30-160">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="e8f30-161">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="e8f30-161">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="e8f30-162">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="e8f30-162">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="e8f30-163">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="e8f30-163">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="e8f30-164">Página de índice</span><span class="sxs-lookup"><span data-stu-id="e8f30-164">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="e8f30-165">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="e8f30-165">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="e8f30-166">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="e8f30-166">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="e8f30-167">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="e8f30-167">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="e8f30-168">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="e8f30-168">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e8f30-169">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e8f30-169">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="e8f30-170">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="e8f30-170">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="e8f30-171">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="e8f30-171">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

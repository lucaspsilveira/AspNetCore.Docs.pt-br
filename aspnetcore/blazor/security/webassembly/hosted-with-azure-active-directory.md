---
title: Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 82916c06413300bbefa85c619239c23a8e40468a
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147750"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="f34fb-102">Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="f34fb-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="f34fb-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f34fb-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f34fb-104">Este artigo descreve como criar um [ Blazor WebAssembly aplicativo hospedado](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="f34fb-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="f34fb-105">Registrar aplicativos no AAD e criar a solução</span><span class="sxs-lookup"><span data-stu-id="f34fb-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="f34fb-106">Criar um locatário</span><span class="sxs-lookup"><span data-stu-id="f34fb-106">Create a tenant</span></span>

<span data-ttu-id="f34fb-107">Siga as orientações em [início rápido: configurar um locatário](/azure/active-directory/develop/quickstart-create-new-tenant) para criar um locatário no AAD.</span><span class="sxs-lookup"><span data-stu-id="f34fb-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="f34fb-108">Registrar um aplicativo de API do servidor</span><span class="sxs-lookup"><span data-stu-id="f34fb-108">Register a server API app</span></span>

<span data-ttu-id="f34fb-109">Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *aplicativo de API do servidor* e, em seguida, faça o seguinte:</span><span class="sxs-lookup"><span data-stu-id="f34fb-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="f34fb-110">Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-110">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="f34fb-111">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="f34fb-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="f34fb-112">Escolha um **tipo de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="f34fb-113">Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="f34fb-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="f34fb-114">O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="f34fb-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="f34fb-115">Desabilite a caixa de seleção **permissões**  >  **conceder consentimento de administrador para OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="f34fb-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="f34fb-116">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-116">Select **Register**.</span></span>

<span data-ttu-id="f34fb-117">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="f34fb-117">Record the following information:</span></span>

* <span data-ttu-id="f34fb-118">*Aplicativo de API do servidor* ID do aplicativo (cliente) (por exemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="f34fb-118">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="f34fb-119">ID do diretório (locatário) (por exemplo, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="f34fb-119">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="f34fb-120">Domínio principal/Publicador/locatário do AAD (por exemplo, `contoso.onmicrosoft.com` ): o domínio está disponível como o **domínio do Publicador** na folha de **identidade visual** do portal do Azure para o aplicativo registrado.</span><span class="sxs-lookup"><span data-stu-id="f34fb-120">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="f34fb-121">Em **permissões de API**, remova a permissão **Microsoft Graph**  >  **User. Read** , pois o aplicativo não requer acesso de entrada ou perfil de usuário.</span><span class="sxs-lookup"><span data-stu-id="f34fb-121">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="f34fb-122">No **expor uma API**:</span><span class="sxs-lookup"><span data-stu-id="f34fb-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="f34fb-123">Selecione **Adicionar um escopo**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="f34fb-124">Selecione **Salvar e continuar**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="f34fb-125">Forneça um **nome de escopo** (por exemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="f34fb-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="f34fb-126">Forneça um **nome de exibição de consentimento do administrador** (por exemplo, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="f34fb-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="f34fb-127">Forneça uma **Descrição de consentimento do administrador** (por exemplo, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="f34fb-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="f34fb-128">Confirme se o **estado** está definido como **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="f34fb-129">Selecione **Adicionar escopo**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-129">Select **Add scope**.</span></span>

<span data-ttu-id="f34fb-130">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="f34fb-130">Record the following information:</span></span>

* <span data-ttu-id="f34fb-131">URI da ID do aplicativo (por exemplo,, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` ou o valor personalizado que você forneceu)</span><span class="sxs-lookup"><span data-stu-id="f34fb-131">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="f34fb-132">Escopo padrão (por exemplo, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="f34fb-132">Default scope (for example, `API.Access`)</span></span>

<span data-ttu-id="f34fb-133">O URI da ID do aplicativo pode exigir uma configuração especial no aplicativo cliente, que é descrita na seção [escopos de token de acesso](#access-token-scopes) mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="f34fb-133">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="f34fb-134">Registrar um aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="f34fb-134">Register a client app</span></span>

<span data-ttu-id="f34fb-135">Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *aplicativo cliente* e, em seguida, faça o seguinte:</span><span class="sxs-lookup"><span data-stu-id="f34fb-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="f34fb-136">Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="f34fb-137">Forneça um **nome** para o aplicativo (por exemplo, \*\* Blazor cliente AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="f34fb-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="f34fb-138">Escolha um **tipo de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="f34fb-139">Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="f34fb-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="f34fb-140">Deixe a lista suspensa **URI de redirecionamento** definida como **Web** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="f34fb-140">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="f34fb-141">A porta padrão para um aplicativo em execução no Kestrel é 5001.</span><span class="sxs-lookup"><span data-stu-id="f34fb-141">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="f34fb-142">Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f34fb-142">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="f34fb-143">Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas propriedades do aplicativo do servidor no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="f34fb-143">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="f34fb-144">Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="f34fb-144">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="f34fb-145">Um comentário é exibido na seção [criar o aplicativo](#create-the-app) para lembrar IIS Express usuários para atualizar o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="f34fb-145">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="f34fb-146">Desabilite a caixa de seleção **permissões**  >  **conceder consentimento de administrador para OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="f34fb-146">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="f34fb-147">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-147">Select **Register**.</span></span>

<span data-ttu-id="f34fb-148">Registre a ID do aplicativo *cliente* (cliente) (por exemplo, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="f34fb-148">Record the *Client app* Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="f34fb-149">Em **Authentication**  >  **configurações da plataforma**de autenticação  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="f34fb-149">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="f34fb-150">Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="f34fb-150">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="f34fb-151">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-151">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="f34fb-152">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="f34fb-152">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="f34fb-153">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-153">Select the **Save** button.</span></span>

<span data-ttu-id="f34fb-154">Em **permissões de API**:</span><span class="sxs-lookup"><span data-stu-id="f34fb-154">In **API permissions**:</span></span>

1. <span data-ttu-id="f34fb-155">Confirme se o aplicativo tem a permissão **Microsoft Graph**  >  **User. Read** .</span><span class="sxs-lookup"><span data-stu-id="f34fb-155">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="f34fb-156">Selecione **Adicionar uma permissão** seguida por **minhas APIs**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-156">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="f34fb-157">Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="f34fb-157">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="f34fb-158">Abra a lista de **APIs** .</span><span class="sxs-lookup"><span data-stu-id="f34fb-158">Open the **API** list.</span></span>
1. <span data-ttu-id="f34fb-159">Habilite o acesso à API (por exemplo, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="f34fb-159">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="f34fb-160">Escolha **Adicionar permissões**.</span><span class="sxs-lookup"><span data-stu-id="f34fb-160">Select **Add permissions**.</span></span>
1. <span data-ttu-id="f34fb-161">Selecione o botão **conceder consentimento de administrador para {nome do locatário}** .</span><span class="sxs-lookup"><span data-stu-id="f34fb-161">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="f34fb-162">Clique em **Sim** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="f34fb-162">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="f34fb-163">Criar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="f34fb-163">Create the app</span></span>

<span data-ttu-id="f34fb-164">Em uma pasta vazia, substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f34fb-164">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="f34fb-165">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="f34fb-165">Placeholder</span></span>                  | <span data-ttu-id="f34fb-166">Nome do portal do Azure</span><span class="sxs-lookup"><span data-stu-id="f34fb-166">Azure portal name</span></span>                                     | <span data-ttu-id="f34fb-167">Exemplo</span><span class="sxs-lookup"><span data-stu-id="f34fb-167">Example</span></span>                                |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="f34fb-168">ID do aplicativo (cliente) para o *aplicativo cliente*</span><span class="sxs-lookup"><span data-stu-id="f34fb-168">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="f34fb-169">Nome do escopo</span><span class="sxs-lookup"><span data-stu-id="f34fb-169">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="f34fb-170">ID do aplicativo (cliente) para o *aplicativo de API do servidor*</span><span class="sxs-lookup"><span data-stu-id="f34fb-170">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="f34fb-171">URI da ID do aplicativo ([consulte a observação](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="f34fb-171">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="f34fb-172">Domínio primário/Publicador/locatário</span><span class="sxs-lookup"><span data-stu-id="f34fb-172">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |
| `{TENANT ID}`                | <span data-ttu-id="f34fb-173">ID do diretório (locatário)</span><span class="sxs-lookup"><span data-stu-id="f34fb-173">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="f34fb-174">O local de saída especificado com a `-o|--output` opção criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f34fb-174">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="f34fb-175">Passe o URI da ID do aplicativo para a `app-id-uri` opção, mas observe que uma alteração de configuração pode ser necessária no aplicativo cliente, que é descrito na seção [escopos de token de acesso](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="f34fb-175">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="f34fb-176">No portal do Azure, o URI de redirecionamento da Web de configurações da plataforma de autenticação *do aplicativo cliente* **Authentication**  >  **Platform configurations**  >  **Web**  >  **Redirect URI** é configurado para a porta 5001 para aplicativos executados no servidor Kestrel com as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="f34fb-176">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="f34fb-177">Se o *aplicativo cliente* for executado em uma porta IIS Express aleatória, a porta do aplicativo poderá ser encontrada nas propriedades *do aplicativo de API do servidor* no painel de **depuração** .</span><span class="sxs-lookup"><span data-stu-id="f34fb-177">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="f34fb-178">Se a porta não foi configurada anteriormente com a porta conhecida *do aplicativo cliente* , retorne ao registro *do aplicativo cliente* no portal do Azure e atualize o URI de redirecionamento com a porta correta.</span><span class="sxs-lookup"><span data-stu-id="f34fb-178">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="f34fb-179">Configuração de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="f34fb-179">Server app configuration</span></span>

<span data-ttu-id="f34fb-180">*Esta seção pertence ao aplicativo da solução **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="f34fb-180">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="f34fb-181">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="f34fb-181">Authentication package</span></span>

<span data-ttu-id="f34fb-182">O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) pacote:</span><span class="sxs-lookup"><span data-stu-id="f34fb-182">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="f34fb-183">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="f34fb-183">Authentication service support</span></span>

<span data-ttu-id="f34fb-184">O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="f34fb-184">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="f34fb-185">O <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> método configura os parâmetros específicos no manipulador de portador JWT necessários para validar tokens emitidos pelo Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="f34fb-185">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="f34fb-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Verifique se:</span><span class="sxs-lookup"><span data-stu-id="f34fb-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="f34fb-187">O aplicativo tenta analisar e validar tokens em solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="f34fb-187">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="f34fb-188">Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.</span><span class="sxs-lookup"><span data-stu-id="f34fb-188">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="f34fb-189">Usuário. Identity . Nomes</span><span class="sxs-lookup"><span data-stu-id="f34fb-189">User.Identity.Name</span></span>

<span data-ttu-id="f34fb-190">Por padrão, a API do aplicativo de servidor popula `User.Identity.Name` com o valor do `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo de declaração (por exemplo, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="f34fb-190">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="f34fb-191">Para configurar o aplicativo para receber o valor do `name` tipo de declaração, configure o <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f34fb-191">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="f34fb-192">Configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="f34fb-192">App settings</span></span>

<span data-ttu-id="f34fb-193">O `appsettings.json` arquivo contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso:</span><span class="sxs-lookup"><span data-stu-id="f34fb-193">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="f34fb-194">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="f34fb-194">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="f34fb-195">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="f34fb-195">WeatherForecast controller</span></span>

<span data-ttu-id="f34fb-196">O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="f34fb-196">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="f34fb-197">É **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="f34fb-197">It's **important** to understand that:</span></span>

* <span data-ttu-id="f34fb-198">O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo nesse controlador de API é a única coisa que protege essa API contra o acesso não autorizado.</span><span class="sxs-lookup"><span data-stu-id="f34fb-198">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="f34fb-199">O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo usado no Blazor WebAssembly aplicativo serve apenas como uma dica para o aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="f34fb-199">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="f34fb-200">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="f34fb-200">Client app configuration</span></span>

<span data-ttu-id="f34fb-201">*Esta seção pertence ao aplicativo da solução **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="f34fb-201">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="f34fb-202">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="f34fb-202">Authentication package</span></span>

<span data-ttu-id="f34fb-203">Quando um aplicativo é criado para usar contas corporativas ou de estudante ( `SingleOrg` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ).</span><span class="sxs-lookup"><span data-stu-id="f34fb-203">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="f34fb-204">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="f34fb-204">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f34fb-205">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f34fb-205">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="f34fb-206">O [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacote adiciona transitivamente o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f34fb-206">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="f34fb-207">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="f34fb-207">Authentication service support</span></span>

<span data-ttu-id="f34fb-208">O suporte para <xref:System.Net.Http.HttpClient> instâncias é adicionado que inclui tokens de acesso ao fazer solicitações ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="f34fb-208">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="f34fb-209">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="f34fb-209">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="f34fb-210">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="f34fb-210">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="f34fb-211">O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensão fornecido pelo [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pacote.</span><span class="sxs-lookup"><span data-stu-id="f34fb-211">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="f34fb-212">Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).</span><span class="sxs-lookup"><span data-stu-id="f34fb-212">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="f34fb-213">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="f34fb-213">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="f34fb-214">O <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f34fb-214">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="f34fb-215">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f34fb-215">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="f34fb-216">A configuração é fornecida pelo `wwwroot/appsettings.json` arquivo:</span><span class="sxs-lookup"><span data-stu-id="f34fb-216">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="f34fb-217">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="f34fb-217">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="f34fb-218">Escopos de token de acesso</span><span class="sxs-lookup"><span data-stu-id="f34fb-218">Access token scopes</span></span>

<span data-ttu-id="f34fb-219">Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:</span><span class="sxs-lookup"><span data-stu-id="f34fb-219">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="f34fb-220">Incluído por padrão na solicitação de entrada.</span><span class="sxs-lookup"><span data-stu-id="f34fb-220">Included by default in the sign in request.</span></span>
* <span data-ttu-id="f34fb-221">Usado para provisionar um token de acesso imediatamente após a autenticação.</span><span class="sxs-lookup"><span data-stu-id="f34fb-221">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="f34fb-222">Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="f34fb-222">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="f34fb-223">Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="f34fb-223">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="f34fb-224">Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :</span><span class="sxs-lookup"><span data-stu-id="f34fb-224">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="f34fb-225">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="f34fb-225">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="f34fb-226">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="f34fb-226">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="f34fb-227">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="f34fb-227">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="f34fb-228">Página de índice</span><span class="sxs-lookup"><span data-stu-id="f34fb-228">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="f34fb-229">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="f34fb-229">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="f34fb-230">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="f34fb-230">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="f34fb-231">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="f34fb-231">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="f34fb-232">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="f34fb-232">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="f34fb-233">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="f34fb-233">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="f34fb-234">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="f34fb-234">Run the app</span></span>

<span data-ttu-id="f34fb-235">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="f34fb-235">Run the app from the Server project.</span></span> <span data-ttu-id="f34fb-236">Ao usar o Visual Studio, seja:</span><span class="sxs-lookup"><span data-stu-id="f34fb-236">When using Visual Studio, either:</span></span>

* <span data-ttu-id="f34fb-237">Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .</span><span class="sxs-lookup"><span data-stu-id="f34fb-237">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="f34fb-238">Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="f34fb-238">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f34fb-239">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f34fb-239">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="f34fb-240">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="f34fb-240">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="f34fb-241">Documentação da plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="f34fb-241">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

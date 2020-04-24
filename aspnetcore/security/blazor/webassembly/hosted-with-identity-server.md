---
title: Proteger um aplicativo Blazor hospedado webassembly ASP.NET Core com o servidor de identidade
author: guardrex
description: Para criar um novo Blazor aplicativo hospedado com autenticação de dentro do Visual Studio que usa um back-end [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: ffdcd30ae9ce5350113569a500e99cf8db82ad65
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138594"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="7bc70-103">Proteger um aplicativo Blazor hospedado webassembly ASP.NET Core com o servidor de identidade</span><span class="sxs-lookup"><span data-stu-id="7bc70-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="7bc70-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7bc70-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="7bc70-105">Para criar um novo Blazor aplicativo hospedado no Visual Studio que usa [IdentityServer](https://identityserver.io/) para autenticar usuários e chamadas de API:</span><span class="sxs-lookup"><span data-stu-id="7bc70-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="7bc70-106">Use o Visual Studio para criar um novo \*\* Blazor aplicativo Webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="7bc70-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="7bc70-107">Para obter mais informações, consulte <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="7bc70-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="7bc70-108">Na caixa de diálogo **criar Blazor um novo aplicativo** , selecione **alterar** na seção **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="7bc70-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="7bc70-109">Selecione **contas de usuário individuais** seguidas por **OK**.</span><span class="sxs-lookup"><span data-stu-id="7bc70-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="7bc70-110">Marque a caixa de seleção **ASP.NET Core hospedado** na seção **avançado** .</span><span class="sxs-lookup"><span data-stu-id="7bc70-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="7bc70-111">Selecione o botão **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7bc70-111">Select the **Create** button.</span></span>

<span data-ttu-id="7bc70-112">Para criar o aplicativo em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7bc70-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="7bc70-113">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho ( `-o BlazorSample`por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="7bc70-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="7bc70-114">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="7bc70-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="7bc70-115">Configuração de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="7bc70-115">Server app configuration</span></span>

<span data-ttu-id="7bc70-116">As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído.</span><span class="sxs-lookup"><span data-stu-id="7bc70-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="7bc70-117">Classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="7bc70-117">Startup class</span></span>

<span data-ttu-id="7bc70-118">A `Startup` classe tem as seguintes adições:</span><span class="sxs-lookup"><span data-stu-id="7bc70-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="7bc70-119">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7bc70-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="7bc70-120">Identidade:</span><span class="sxs-lookup"><span data-stu-id="7bc70-120">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="7bc70-121">IdentityServer com um método <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> auxiliar adicional que configura algumas convenções de ASP.NET Core padrão na parte superior do IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="7bc70-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="7bc70-122">Autenticação com um método <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> auxiliar adicional que configura o aplicativo para validar tokens JWT produzidos por IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="7bc70-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="7bc70-123">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="7bc70-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="7bc70-124">O middleware de autenticação que é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:</span><span class="sxs-lookup"><span data-stu-id="7bc70-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="7bc70-125">O middleware IdentityServer que expõe os pontos de extremidade do OIDC (Open ID Connect):</span><span class="sxs-lookup"><span data-stu-id="7bc70-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="7bc70-126">Middleware de autenticação e autorização:</span><span class="sxs-lookup"><span data-stu-id="7bc70-126">Authentication and Authorization Middleware:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="7bc70-127">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="7bc70-127">AddApiAuthorization</span></span>

<span data-ttu-id="7bc70-128">O <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar configura [IdentityServer](https://identityserver.io/) para cenários de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7bc70-128">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="7bc70-129">O IdentityServer é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7bc70-129">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="7bc70-130">O IdentityServer expõe complexidade desnecessária para os cenários mais comuns.</span><span class="sxs-lookup"><span data-stu-id="7bc70-130">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="7bc70-131">Consequentemente, um conjunto de convenções e opções de configuração é fornecido para considerarmos um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="7bc70-131">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="7bc70-132">Depois que a autenticação precisar ser alterada, toda a potência do IdentityServer ainda estará disponível para personalizar a autenticação para atender aos requisitos de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7bc70-132">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="7bc70-133">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="7bc70-133">AddIdentityServerJwt</span></span>

<span data-ttu-id="7bc70-134">O <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="7bc70-134">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="7bc70-135">A política é configurada para permitir que a identidade manipule todas as solicitações roteadas para qualquer subcaminho no espaço `/Identity`de URL de identidade.</span><span class="sxs-lookup"><span data-stu-id="7bc70-135">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="7bc70-136">O <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> lida com todas as outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="7bc70-136">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="7bc70-137">Além disso, esse método:</span><span class="sxs-lookup"><span data-stu-id="7bc70-137">Additionally, this method:</span></span>

* <span data-ttu-id="7bc70-138">Registra um `{APPLICATION NAME}API` recurso de API com IdentityServer com um escopo padrão `{APPLICATION NAME}API`de.</span><span class="sxs-lookup"><span data-stu-id="7bc70-138">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="7bc70-139">Configura o middleware do token de portador JWT para validar tokens emitidos por IdentityServer para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7bc70-139">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="7bc70-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="7bc70-140">WeatherForecastController</span></span>

<span data-ttu-id="7bc70-141">No `WeatherForecastController` (*Controllers/WeatherForecastController. cs*), [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) o atributo é aplicado à classe.</span><span class="sxs-lookup"><span data-stu-id="7bc70-141">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="7bc70-142">O atributo indica que o usuário deve ser autorizado com base na política padrão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="7bc70-142">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="7bc70-143">A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> é configurado pelo para o esquema de política mencionado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="7bc70-143">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="7bc70-144">O método auxiliar é configurado <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> como o manipulador padrão para solicitações ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7bc70-144">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="7bc70-145">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="7bc70-145">ApplicationDbContext</span></span>

<span data-ttu-id="7bc70-146">No `ApplicationDbContext` (*Data/ApplicationDbContext. cs*), o mesmo <xref:Microsoft.EntityFrameworkCore.DbContext> é usado em identidade com a exceção que ele estende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> para incluir o esquema para IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="7bc70-146">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="7bc70-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>é derivado de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="7bc70-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="7bc70-148">Para obter controle total do esquema de banco de dados, herde de uma das <xref:Microsoft.EntityFrameworkCore.DbContext> classes de identidade disponíveis e configure o contexto para incluir o esquema `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` de identidade `OnModelCreating` chamando no método.</span><span class="sxs-lookup"><span data-stu-id="7bc70-148">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="7bc70-149">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="7bc70-149">OidcConfigurationController</span></span>

<span data-ttu-id="7bc70-150">No `OidcConfigurationController` (*Controllers/OidcConfigurationController. cs*), o ponto de extremidade do cliente é provisionado para atender aos parâmetros OIDC.</span><span class="sxs-lookup"><span data-stu-id="7bc70-150">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="7bc70-151">Arquivos de configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="7bc70-151">App settings files</span></span>

<span data-ttu-id="7bc70-152">No arquivo de configurações do aplicativo (*appSettings. JSON*) na raiz do projeto, `IdentityServer` a seção descreve a lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="7bc70-152">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="7bc70-153">No exemplo a seguir, há um único cliente.</span><span class="sxs-lookup"><span data-stu-id="7bc70-153">In the following example, there's a single client.</span></span> <span data-ttu-id="7bc70-154">O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o `ClientId` parâmetro OAuth.</span><span class="sxs-lookup"><span data-stu-id="7bc70-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="7bc70-155">O perfil indica o tipo de aplicativo que está sendo configurado.</span><span class="sxs-lookup"><span data-stu-id="7bc70-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="7bc70-156">O perfil é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor.</span><span class="sxs-lookup"><span data-stu-id="7bc70-156">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="7bc70-157">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="7bc70-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7bc70-158">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="7bc70-158">Authentication package</span></span>

<span data-ttu-id="7bc70-159">Quando um aplicativo é criado para usar contas de usuário individuais`Individual`(), o aplicativo recebe automaticamente uma referência de pacote `Microsoft.AspNetCore.Components.WebAssembly.Authentication` para o pacote no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7bc70-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="7bc70-160">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="7bc70-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7bc70-161">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7bc70-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="7bc70-162">Substitua `{VERSION}` na referência do pacote anterior pela versão do `Microsoft.AspNetCore.Blazor.Templates` pacote mostrado no <xref:blazor/get-started> artigo.</span><span class="sxs-lookup"><span data-stu-id="7bc70-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="7bc70-163">Suporte à autorização de API</span><span class="sxs-lookup"><span data-stu-id="7bc70-163">API authorization support</span></span>

<span data-ttu-id="7bc70-164">O suporte para autenticação de usuários é conectado ao contêiner de serviço pelo método de extensão fornecido dentro do `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacote.</span><span class="sxs-lookup"><span data-stu-id="7bc70-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="7bc70-165">Esse método configura todos os serviços necessários para que o aplicativo interaja com o sistema de autorização existente.</span><span class="sxs-lookup"><span data-stu-id="7bc70-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="7bc70-166">Por padrão, ele carrega a configuração do aplicativo por convenção de `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="7bc70-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="7bc70-167">Por convenção, a ID do cliente é definida como o nome do assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7bc70-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="7bc70-168">Essa URL pode ser alterada para apontar para um ponto de extremidade separado chamando a sobrecarga com opções.</span><span class="sxs-lookup"><span data-stu-id="7bc70-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="7bc70-169">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="7bc70-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="7bc70-170">Página de índice</span><span class="sxs-lookup"><span data-stu-id="7bc70-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="7bc70-171">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="7bc70-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="7bc70-172">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="7bc70-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="7bc70-173">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="7bc70-173">LoginDisplay component</span></span>

<span data-ttu-id="7bc70-174">O `LoginDisplay` componente (*Shared/LoginDisplay. Razor*) é renderizado no `MainLayout` componente (*Shared/MainLayout. Razor*) e gerencia os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="7bc70-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="7bc70-175">Para usuários autenticados:</span><span class="sxs-lookup"><span data-stu-id="7bc70-175">For authenticated users:</span></span>
  * <span data-ttu-id="7bc70-176">Exibe o nome de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="7bc70-176">Displays the current user name.</span></span>
  * <span data-ttu-id="7bc70-177">Oferece um link para a página de perfil do usuário em ASP.NET Core identidade.</span><span class="sxs-lookup"><span data-stu-id="7bc70-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="7bc70-178">Oferece um botão para fazer logoff do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7bc70-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="7bc70-179">Para usuários anônimos:</span><span class="sxs-lookup"><span data-stu-id="7bc70-179">For anonymous users:</span></span>
  * <span data-ttu-id="7bc70-180">Oferece a opção de se registrar.</span><span class="sxs-lookup"><span data-stu-id="7bc70-180">Offers the option to register.</span></span>
  * <span data-ttu-id="7bc70-181">Oferece a opção de fazer logon.</span><span class="sxs-lookup"><span data-stu-id="7bc70-181">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="7bc70-182">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="7bc70-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="7bc70-183">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="7bc70-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="7bc70-184">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="7bc70-184">Run the app</span></span>

<span data-ttu-id="7bc70-185">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="7bc70-185">Run the app from the Server project.</span></span> <span data-ttu-id="7bc70-186">Ao usar o Visual Studio, selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="7bc70-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7bc70-187">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7bc70-187">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>

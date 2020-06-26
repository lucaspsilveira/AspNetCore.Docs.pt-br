---
title: Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com o Identity servidor
author: guardrex
description: Para criar um novo Blazor aplicativo hospedado com autenticação de dentro do Visual Studio que usa um back-end [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: cce6b6b1ec144e362415fe34645aef567269c873
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402202"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="fcf7b-103">Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com o Identity servidor</span><span class="sxs-lookup"><span data-stu-id="fcf7b-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="fcf7b-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fcf7b-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fcf7b-105">Este artigo explica como criar um novo Blazor aplicativo hospedado que usa [IdentityServer](https://identityserver.io/) para autenticar usuários e chamadas de API.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fcf7b-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcf7b-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fcf7b-107">No Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-107">In Visual Studio:</span></span>

1. <span data-ttu-id="fcf7b-108">Crie um novo **Blazor WebAssembly** aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-108">Create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="fcf7b-109">Para obter mais informações, consulte <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="fcf7b-110">Na caixa de diálogo **criar um novo Blazor aplicativo** , selecione **alterar** na seção **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="fcf7b-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="fcf7b-111">Selecione **contas de usuário individuais** seguidas por **OK**.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="fcf7b-112">Marque a caixa de seleção **ASP.NET Core hospedado** na seção **avançado** .</span><span class="sxs-lookup"><span data-stu-id="fcf7b-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="fcf7b-113">Selecione o botão **Criar**.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-113">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="fcf7b-114">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="fcf7b-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="fcf7b-115">Para criar o aplicativo em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-115">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="fcf7b-116">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="fcf7b-116">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="fcf7b-117">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-117">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="fcf7b-118">Configuração de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="fcf7b-118">Server app configuration</span></span>

<span data-ttu-id="fcf7b-119">As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-119">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="fcf7b-120">Classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="fcf7b-120">Startup class</span></span>

<span data-ttu-id="fcf7b-121">A `Startup` classe tem as seguintes adições.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-121">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="fcf7b-122">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-122">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="fcf7b-123">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="fcf7b-123">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="fcf7b-124">IdentityServer com um <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar adicional que configura as convenções de ASP.NET Core padrão na parte superior do IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-124">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="fcf7b-125">Autenticação com um <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar adicional que configura o aplicativo para validar tokens JWT produzidos por IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-125">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="fcf7b-126">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-126">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="fcf7b-127">O middleware IdentityServer expõe os pontos de extremidade do Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="fcf7b-127">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="fcf7b-128">O middleware de autenticação é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-128">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="fcf7b-129">O middleware de autorização habilita os recursos de autorização:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-129">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="fcf7b-130">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="fcf7b-130">AddApiAuthorization</span></span>

<span data-ttu-id="fcf7b-131">O <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar configura [IdentityServer](https://identityserver.io/) para cenários de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-131">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="fcf7b-132">O IdentityServer é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-132">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="fcf7b-133">O IdentityServer expõe complexidade desnecessária para os cenários mais comuns.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-133">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="fcf7b-134">Consequentemente, um conjunto de convenções e opções de configuração é fornecido para considerarmos um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-134">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="fcf7b-135">Depois que a autenticação precisar ser alterada, todo o poder do IdentityServer estará disponível para personalizar a autenticação de acordo com os requisitos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-135">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="fcf7b-136">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="fcf7b-136">AddIdentityServerJwt</span></span>

<span data-ttu-id="fcf7b-137">O <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-137">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="fcf7b-138">A política está configurada para permitir que o Identity manipule todas as solicitações roteadas para qualquer subcaminho no Identity espaço de URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="fcf7b-138">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="fcf7b-139">O <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> lida com todas as outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-139">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="fcf7b-140">Além disso, esse método:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-140">Additionally, this method:</span></span>

* <span data-ttu-id="fcf7b-141">Registra um `{APPLICATION NAME}API` recurso de API com IdentityServer com um escopo padrão de `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="fcf7b-141">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="fcf7b-142">Configura o middleware do token de portador JWT para validar tokens emitidos por IdentityServer para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-142">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="fcf7b-143">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="fcf7b-143">WeatherForecastController</span></span>

<span data-ttu-id="fcf7b-144">No `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ), o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo é aplicado à classe.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-144">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="fcf7b-145">O atributo indica que o usuário deve ser autorizado com base na política padrão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-145">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="fcf7b-146">A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que é configurado por <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="fcf7b-146">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="fcf7b-147">O método auxiliar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> é configurado como o manipulador padrão para solicitações ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-147">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="fcf7b-148">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="fcf7b-148">ApplicationDbContext</span></span>

<span data-ttu-id="fcf7b-149">No `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ), <xref:Microsoft.EntityFrameworkCore.DbContext> estende- <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se para incluir o esquema para IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-149">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="fcf7b-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>é derivado de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="fcf7b-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="fcf7b-151">Para obter controle total do esquema de banco de dados, herde de uma das Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes disponíveis e configure o contexto para incluir o Identity esquema chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` no <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> método.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-151">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="fcf7b-152">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="fcf7b-152">OidcConfigurationController</span></span>

<span data-ttu-id="fcf7b-153">No `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ), o ponto de extremidade do cliente é provisionado para atender aos parâmetros OIDC.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-153">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="fcf7b-154">Arquivos de configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="fcf7b-154">App settings files</span></span>

<span data-ttu-id="fcf7b-155">No arquivo de configurações do aplicativo ( `appsettings.json` ) na raiz do projeto, a `IdentityServer` seção descreve a lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-155">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="fcf7b-156">No exemplo a seguir, há um único cliente.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-156">In the following example, there's a single client.</span></span> <span data-ttu-id="fcf7b-157">O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o `ClientId` parâmetro OAuth.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-157">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="fcf7b-158">O perfil indica o tipo de aplicativo que está sendo configurado.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-158">The profile indicates the app type being configured.</span></span> <span data-ttu-id="fcf7b-159">O perfil é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-159">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="fcf7b-160">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="fcf7b-160">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="fcf7b-161">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="fcf7b-161">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="fcf7b-162">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="fcf7b-162">Authentication package</span></span>

<span data-ttu-id="fcf7b-163">Quando um aplicativo é criado para usar contas de usuário individuais ( `Individual` ), o aplicativo recebe automaticamente uma referência de pacote para o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-163">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="fcf7b-164">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-164">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="fcf7b-165">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-165">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="fcf7b-166">Suporte à autorização de API</span><span class="sxs-lookup"><span data-stu-id="fcf7b-166">API authorization support</span></span>

<span data-ttu-id="fcf7b-167">O suporte para autenticação de usuários é conectado ao contêiner de serviço pelo método de extensão fornecido dentro do [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-167">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="fcf7b-168">Esse método configura os serviços exigidos pelo aplicativo para interagir com o sistema de autorização existente.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-168">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="fcf7b-169">Por padrão, a configuração para o aplicativo é carregada por convenção de `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="fcf7b-169">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="fcf7b-170">Por convenção, a ID do cliente é definida como o nome do assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-170">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="fcf7b-171">Essa URL pode ser alterada para apontar para um ponto de extremidade separado chamando a sobrecarga com opções.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-171">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="fcf7b-172">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="fcf7b-172">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="fcf7b-173">Página de índice</span><span class="sxs-lookup"><span data-stu-id="fcf7b-173">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="fcf7b-174">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="fcf7b-174">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="fcf7b-175">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="fcf7b-175">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="fcf7b-176">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="fcf7b-176">LoginDisplay component</span></span>

<span data-ttu-id="fcf7b-177">O `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) é renderizado no `MainLayout` componente ( `Shared/MainLayout.razor` ) e gerencia os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-177">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="fcf7b-178">Para usuários autenticados:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-178">For authenticated users:</span></span>
  * <span data-ttu-id="fcf7b-179">Exibe o nome de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-179">Displays the current user name.</span></span>
  * <span data-ttu-id="fcf7b-180">Oferece um link para a página de perfil do usuário no ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="fcf7b-180">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="fcf7b-181">Oferece um botão para fazer logoff do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-181">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="fcf7b-182">Para usuários anônimos:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-182">For anonymous users:</span></span>
  * <span data-ttu-id="fcf7b-183">Oferece a opção de se registrar.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-183">Offers the option to register.</span></span>
  * <span data-ttu-id="fcf7b-184">Oferece a opção de fazer logon.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-184">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="fcf7b-185">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="fcf7b-185">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="fcf7b-186">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="fcf7b-186">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="fcf7b-187">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="fcf7b-187">Run the app</span></span>

<span data-ttu-id="fcf7b-188">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-188">Run the app from the Server project.</span></span> <span data-ttu-id="fcf7b-189">Ao usar o Visual Studio, seja:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-189">When using Visual Studio, either:</span></span>

* <span data-ttu-id="fcf7b-190">Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .</span><span class="sxs-lookup"><span data-stu-id="fcf7b-190">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="fcf7b-191">Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="fcf7b-191">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="fcf7b-192">Nome e declaração de função com autorização de API</span><span class="sxs-lookup"><span data-stu-id="fcf7b-192">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="fcf7b-193">Fábrica de usuário personalizada</span><span class="sxs-lookup"><span data-stu-id="fcf7b-193">Custom user factory</span></span>

<span data-ttu-id="fcf7b-194">No aplicativo cliente, crie uma fábrica de usuário personalizada.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-194">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="fcf7b-195">O servidor envia várias funções como uma matriz JSON em uma única `role` declaração.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-195"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="fcf7b-196">Uma única função é enviada como um valor de cadeia de caracteres na declaração.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-196">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="fcf7b-197">A fábrica cria uma `role` declaração individual para cada uma das funções do usuário.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-197">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="fcf7b-198">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-198">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="fcf7b-199">No aplicativo cliente, registre a fábrica em `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="fcf7b-199">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="fcf7b-200">No aplicativo de servidor, chame <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> no Identity Construtor, que adiciona serviços relacionados a funções:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-200">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="fcf7b-201">Configurar Identity servidor</span><span class="sxs-lookup"><span data-stu-id="fcf7b-201">Configure Identity Server</span></span>

<span data-ttu-id="fcf7b-202">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-202">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="fcf7b-203">Opções de autorização de API</span><span class="sxs-lookup"><span data-stu-id="fcf7b-203">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="fcf7b-204">Serviço de perfil</span><span class="sxs-lookup"><span data-stu-id="fcf7b-204">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="fcf7b-205">Opções de autorização de API</span><span class="sxs-lookup"><span data-stu-id="fcf7b-205">API authorization options</span></span>

<span data-ttu-id="fcf7b-206">No aplicativo do servidor:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-206">In the Server app:</span></span>

* <span data-ttu-id="fcf7b-207">Configure Identity o servidor para colocar o `name` e as `role` declarações no token de ID e no token de acesso.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-207">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="fcf7b-208">Impedir o mapeamento padrão para funções no manipulador de tokens JWT.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-208">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="fcf7b-209">Serviço de perfil</span><span class="sxs-lookup"><span data-stu-id="fcf7b-209">Profile Service</span></span>

<span data-ttu-id="fcf7b-210">No aplicativo de servidor, crie uma `ProfileService` implementação.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-210">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="fcf7b-211">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-211">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="fcf7b-212">No aplicativo de servidor, registre o serviço de perfil em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fcf7b-212">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="fcf7b-213">Usar mecanismos de autorização</span><span class="sxs-lookup"><span data-stu-id="fcf7b-213">Use authorization mechanisms</span></span>

<span data-ttu-id="fcf7b-214">No aplicativo cliente, as abordagens de autorização de componente são funcionais neste ponto.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-214">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="fcf7b-215">Qualquer um dos mecanismos de autorização nos componentes pode usar uma função para autorizar o usuário:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-215">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="fcf7b-216">[ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) (exemplo: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="fcf7b-216">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="fcf7b-217">[ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemplo: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="fcf7b-217">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="fcf7b-218">[Lógica de procedimento](xref:blazor/security/index#procedural-logic) (exemplo: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="fcf7b-218">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="fcf7b-219">Há suporte para vários testes de função:</span><span class="sxs-lookup"><span data-stu-id="fcf7b-219">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="fcf7b-220">`User.Identity.Name`é preenchido no aplicativo cliente com o nome de usuário do usuário, que geralmente é seu endereço de email de entrada.</span><span class="sxs-lookup"><span data-stu-id="fcf7b-220">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="fcf7b-221">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fcf7b-221">Additional resources</span></span>

* [<span data-ttu-id="fcf7b-222">Implantação no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="fcf7b-222">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="fcf7b-223">Importar um certificado do Key Vault (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="fcf7b-223">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="fcf7b-224">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="fcf7b-224">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)

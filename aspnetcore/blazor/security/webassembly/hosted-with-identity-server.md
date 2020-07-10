---
title: Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com o Identity servidor
author: guardrex
description: Para criar um novo Blazor aplicativo hospedado com autenticação de dentro do Visual Studio que usa um back-end do [ Identity servidor](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 1e5b4e37acd11280ec41c137426ecc4776d231be
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176238"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="4b68a-103">Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com o Identity servidor</span><span class="sxs-lookup"><span data-stu-id="4b68a-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="4b68a-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4b68a-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4b68a-105">Este artigo explica como criar um novo Blazor aplicativo hospedado que usa o [ Identity servidor do](https://identityserver.io/) para autenticar usuários e chamadas de API.</span><span class="sxs-lookup"><span data-stu-id="4b68a-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b68a-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b68a-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b68a-107">Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="4b68a-107">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="4b68a-108">Depois de escolher o modelo de \*\* Blazor WebAssembly aplicativo\*\* na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione **alterar** em **autenticação**.</span><span class="sxs-lookup"><span data-stu-id="4b68a-108">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="4b68a-109">Selecione **contas de usuário individuais** com a opção **armazenar contas de usuário no aplicativo** para armazenar usuários no aplicativo usando o sistema do ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="4b68a-109">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="4b68a-110">Marque a caixa de seleção **ASP.NET Core hospedado** na seção **avançado** .</span><span class="sxs-lookup"><span data-stu-id="4b68a-110">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="4b68a-111">Visual Studio Code/CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="4b68a-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="4b68a-112">Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação em uma pasta vazia, especifique o `Individual` mecanismo de autenticação com a `-au|--auth` opção de armazenar usuários no aplicativo usando o [Identity](xref:security/authentication/identity) sistema do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4b68a-112">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="4b68a-113">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="4b68a-113">Placeholder</span></span>  | <span data-ttu-id="4b68a-114">Exemplo</span><span class="sxs-lookup"><span data-stu-id="4b68a-114">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="4b68a-115">O local de saída especificado com a `-o|--output` opção criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b68a-115">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="4b68a-116">Para obter mais informações, consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b68a-116">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4b68a-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4b68a-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4b68a-118">Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="4b68a-118">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="4b68a-119">Na etapa **configurar seu novo Blazor WebAssembly aplicativo** , selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="4b68a-119">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="4b68a-120">O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="4b68a-120">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="4b68a-121">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="4b68a-121">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="4b68a-122">Configuração de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="4b68a-122">Server app configuration</span></span>

<span data-ttu-id="4b68a-123">As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído.</span><span class="sxs-lookup"><span data-stu-id="4b68a-123">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="4b68a-124">Classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="4b68a-124">Startup class</span></span>

<span data-ttu-id="4b68a-125">A `Startup` classe tem as seguintes adições.</span><span class="sxs-lookup"><span data-stu-id="4b68a-125">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="4b68a-126">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4b68a-126">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="4b68a-127">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="4b68a-127">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identity<span data-ttu-id="4b68a-128">Servidor com um <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar adicional que configura as convenções de ASP.NET Core padrão na parte superior do Identity servidor:</span><span class="sxs-lookup"><span data-stu-id="4b68a-128">Server with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="4b68a-129">Autenticação com um <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar adicional que configura o aplicativo para validar tokens JWT produzidos pelo Identity servidor:</span><span class="sxs-lookup"><span data-stu-id="4b68a-129">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="4b68a-130">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="4b68a-130">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="4b68a-131">O Identity middleware do servidor expõe os pontos de extremidade do Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="4b68a-131">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="4b68a-132">O middleware de autenticação é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:</span><span class="sxs-lookup"><span data-stu-id="4b68a-132">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="4b68a-133">O middleware de autorização habilita os recursos de autorização:</span><span class="sxs-lookup"><span data-stu-id="4b68a-133">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="4b68a-134">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="4b68a-134">AddApiAuthorization</span></span>

<span data-ttu-id="4b68a-135">O <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar configura o [ Identity servidor](https://identityserver.io/) para cenários de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b68a-135">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> Identity<span data-ttu-id="4b68a-136">O servidor é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b68a-136">Server is a powerful and extensible framework for handling app security concerns.</span></span> Identity<span data-ttu-id="4b68a-137">O servidor expõe complexidade desnecessária para os cenários mais comuns.</span><span class="sxs-lookup"><span data-stu-id="4b68a-137">Server exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="4b68a-138">Consequentemente, um conjunto de convenções e opções de configuração é fornecido para considerarmos um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="4b68a-138">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="4b68a-139">Depois que a autenticação precisar ser alterada, todo o poder do Identity servidor estará disponível para personalizar a autenticação de acordo com os requisitos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b68a-139">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="4b68a-140">Adicionar Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="4b68a-140">AddIdentityServerJwt</span></span>

<span data-ttu-id="4b68a-141">O <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="4b68a-141">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="4b68a-142">A política está configurada para permitir que o Identity manipule todas as solicitações roteadas para qualquer subcaminho no Identity espaço de URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="4b68a-142">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="4b68a-143">O <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> lida com todas as outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="4b68a-143">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="4b68a-144">Além disso, esse método:</span><span class="sxs-lookup"><span data-stu-id="4b68a-144">Additionally, this method:</span></span>

* <span data-ttu-id="4b68a-145">Registra um `{APPLICATION NAME}API` recurso de API com Identity o servidor com um escopo padrão de `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="4b68a-145">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="4b68a-146">Configura o middleware do token de portador JWT para validar tokens emitidos pelo Identity servidor para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b68a-146">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="4b68a-147">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="4b68a-147">WeatherForecastController</span></span>

<span data-ttu-id="4b68a-148">No `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ), o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo é aplicado à classe.</span><span class="sxs-lookup"><span data-stu-id="4b68a-148">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="4b68a-149">O atributo indica que o usuário deve ser autorizado com base na política padrão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="4b68a-149">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="4b68a-150">A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que é configurado por <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="4b68a-150">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="4b68a-151">O método auxiliar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> é configurado como o manipulador padrão para solicitações ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b68a-151">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="4b68a-152">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="4b68a-152">ApplicationDbContext</span></span>

<span data-ttu-id="4b68a-153">No `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ), <xref:Microsoft.EntityFrameworkCore.DbContext> estende- <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se para incluir o esquema para o Identity servidor.</span><span class="sxs-lookup"><span data-stu-id="4b68a-153">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="4b68a-154"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>é derivado de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="4b68a-154"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="4b68a-155">Para obter controle total do esquema de banco de dados, herde de uma das Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes disponíveis e configure o contexto para incluir o Identity esquema chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` no <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> método.</span><span class="sxs-lookup"><span data-stu-id="4b68a-155">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="4b68a-156">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="4b68a-156">OidcConfigurationController</span></span>

<span data-ttu-id="4b68a-157">No `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ), o ponto de extremidade do cliente é provisionado para atender aos parâmetros OIDC.</span><span class="sxs-lookup"><span data-stu-id="4b68a-157">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="4b68a-158">Configurações de aplicativo</span><span class="sxs-lookup"><span data-stu-id="4b68a-158">App settings</span></span>

<span data-ttu-id="4b68a-159">No arquivo de configurações do aplicativo ( `appsettings.json` ) na raiz do projeto, a `IdentityServer` seção descreve a lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="4b68a-159">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="4b68a-160">No exemplo a seguir, há um único cliente.</span><span class="sxs-lookup"><span data-stu-id="4b68a-160">In the following example, there's a single client.</span></span> <span data-ttu-id="4b68a-161">O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o `ClientId` parâmetro OAuth.</span><span class="sxs-lookup"><span data-stu-id="4b68a-161">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="4b68a-162">O perfil indica o tipo de aplicativo que está sendo configurado.</span><span class="sxs-lookup"><span data-stu-id="4b68a-162">The profile indicates the app type being configured.</span></span> <span data-ttu-id="4b68a-163">O perfil é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor.</span><span class="sxs-lookup"><span data-stu-id="4b68a-163">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="4b68a-164">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="4b68a-164">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="4b68a-165">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="4b68a-165">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="4b68a-166">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="4b68a-166">Authentication package</span></span>

<span data-ttu-id="4b68a-167">Quando um aplicativo é criado para usar contas de usuário individuais ( `Individual` ), o aplicativo recebe automaticamente uma referência de pacote para o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b68a-167">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="4b68a-168">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="4b68a-168">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="4b68a-169">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4b68a-169">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="httpclient-configuration"></a><span data-ttu-id="4b68a-170">`HttpClient`configuração</span><span class="sxs-lookup"><span data-stu-id="4b68a-170">`HttpClient` configuration</span></span>

<span data-ttu-id="4b68a-171">Em `Program.Main` ( `Program.cs` ), um nome <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ) é configurado para fornecer <xref:System.Net.Http.HttpClient> instâncias que incluem tokens de acesso ao fazer solicitações para a API do servidor:</span><span class="sxs-lookup"><span data-stu-id="4b68a-171">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="4b68a-172">Se você estiver configurando um Blazor WebAssembly aplicativo para usar uma Identity instância de servidor existente que não faz parte de uma Blazor solução hospedada, altere o <xref:System.Net.Http.HttpClient> registro do endereço base de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) para a URL do ponto de extremidade de autorização da API do aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="4b68a-172">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a Blazor Hosted solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="4b68a-173">Suporte à autorização de API</span><span class="sxs-lookup"><span data-stu-id="4b68a-173">API authorization support</span></span>

<span data-ttu-id="4b68a-174">O suporte para autenticação de usuários é conectado ao contêiner de serviço pelo método de extensão fornecido dentro do [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote.</span><span class="sxs-lookup"><span data-stu-id="4b68a-174">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="4b68a-175">Esse método configura os serviços exigidos pelo aplicativo para interagir com o sistema de autorização existente.</span><span class="sxs-lookup"><span data-stu-id="4b68a-175">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="4b68a-176">Por padrão, a configuração para o aplicativo é carregada por convenção de `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="4b68a-176">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="4b68a-177">Por convenção, a ID do cliente é definida como o nome do assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b68a-177">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="4b68a-178">Essa URL pode ser alterada para apontar para um ponto de extremidade separado chamando a sobrecarga com opções.</span><span class="sxs-lookup"><span data-stu-id="4b68a-178">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="4b68a-179">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="4b68a-179">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="4b68a-180">Página de índice</span><span class="sxs-lookup"><span data-stu-id="4b68a-180">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="4b68a-181">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="4b68a-181">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="4b68a-182">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="4b68a-182">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="4b68a-183">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="4b68a-183">LoginDisplay component</span></span>

<span data-ttu-id="4b68a-184">O `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) é renderizado no `MainLayout` componente ( `Shared/MainLayout.razor` ) e gerencia os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="4b68a-184">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="4b68a-185">Para usuários autenticados:</span><span class="sxs-lookup"><span data-stu-id="4b68a-185">For authenticated users:</span></span>
  * <span data-ttu-id="4b68a-186">Exibe o nome de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="4b68a-186">Displays the current user name.</span></span>
  * <span data-ttu-id="4b68a-187">Oferece um link para a página de perfil do usuário no ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="4b68a-187">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="4b68a-188">Oferece um botão para fazer logoff do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4b68a-188">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="4b68a-189">Para usuários anônimos:</span><span class="sxs-lookup"><span data-stu-id="4b68a-189">For anonymous users:</span></span>
  * <span data-ttu-id="4b68a-190">Oferece a opção de se registrar.</span><span class="sxs-lookup"><span data-stu-id="4b68a-190">Offers the option to register.</span></span>
  * <span data-ttu-id="4b68a-191">Oferece a opção de fazer logon.</span><span class="sxs-lookup"><span data-stu-id="4b68a-191">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="4b68a-192">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="4b68a-192">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="4b68a-193">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="4b68a-193">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="4b68a-194">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="4b68a-194">Run the app</span></span>

<span data-ttu-id="4b68a-195">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="4b68a-195">Run the app from the Server project.</span></span> <span data-ttu-id="4b68a-196">Ao usar o Visual Studio, seja:</span><span class="sxs-lookup"><span data-stu-id="4b68a-196">When using Visual Studio, either:</span></span>

* <span data-ttu-id="4b68a-197">Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .</span><span class="sxs-lookup"><span data-stu-id="4b68a-197">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="4b68a-198">Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="4b68a-198">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="4b68a-199">Nome e declaração de função com autorização de API</span><span class="sxs-lookup"><span data-stu-id="4b68a-199">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="4b68a-200">Fábrica de usuário personalizada</span><span class="sxs-lookup"><span data-stu-id="4b68a-200">Custom user factory</span></span>

<span data-ttu-id="4b68a-201">No aplicativo cliente, crie uma fábrica de usuário personalizada.</span><span class="sxs-lookup"><span data-stu-id="4b68a-201">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="4b68a-202">O servidor envia várias funções como uma matriz JSON em uma única `role` declaração.</span><span class="sxs-lookup"><span data-stu-id="4b68a-202"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="4b68a-203">Uma única função é enviada como um valor de cadeia de caracteres na declaração.</span><span class="sxs-lookup"><span data-stu-id="4b68a-203">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="4b68a-204">A fábrica cria uma `role` declaração individual para cada uma das funções do usuário.</span><span class="sxs-lookup"><span data-stu-id="4b68a-204">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="4b68a-205">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="4b68a-205">`CustomUserFactory.cs`:</span></span>

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

<span data-ttu-id="4b68a-206">No aplicativo cliente, registre a fábrica em `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="4b68a-206">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="4b68a-207">No aplicativo de servidor, chame <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> no Identity Construtor, que adiciona serviços relacionados a funções:</span><span class="sxs-lookup"><span data-stu-id="4b68a-207">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="4b68a-208">Configurar Identity servidor</span><span class="sxs-lookup"><span data-stu-id="4b68a-208">Configure Identity Server</span></span>

<span data-ttu-id="4b68a-209">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="4b68a-209">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="4b68a-210">Opções de autorização de API</span><span class="sxs-lookup"><span data-stu-id="4b68a-210">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="4b68a-211">Serviço de perfil</span><span class="sxs-lookup"><span data-stu-id="4b68a-211">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="4b68a-212">Opções de autorização de API</span><span class="sxs-lookup"><span data-stu-id="4b68a-212">API authorization options</span></span>

<span data-ttu-id="4b68a-213">No aplicativo do servidor:</span><span class="sxs-lookup"><span data-stu-id="4b68a-213">In the Server app:</span></span>

* <span data-ttu-id="4b68a-214">Configure Identity o servidor para colocar o `name` e as `role` declarações no token de ID e no token de acesso.</span><span class="sxs-lookup"><span data-stu-id="4b68a-214">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="4b68a-215">Impedir o mapeamento padrão para funções no manipulador de tokens JWT.</span><span class="sxs-lookup"><span data-stu-id="4b68a-215">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="4b68a-216">Serviço de perfil</span><span class="sxs-lookup"><span data-stu-id="4b68a-216">Profile Service</span></span>

<span data-ttu-id="4b68a-217">No aplicativo de servidor, crie uma `ProfileService` implementação.</span><span class="sxs-lookup"><span data-stu-id="4b68a-217">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="4b68a-218">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="4b68a-218">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="4b68a-219">No aplicativo de servidor, registre o serviço de perfil em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4b68a-219">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="4b68a-220">Usar mecanismos de autorização</span><span class="sxs-lookup"><span data-stu-id="4b68a-220">Use authorization mechanisms</span></span>

<span data-ttu-id="4b68a-221">No aplicativo cliente, as abordagens de autorização de componente são funcionais neste ponto.</span><span class="sxs-lookup"><span data-stu-id="4b68a-221">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="4b68a-222">Qualquer um dos mecanismos de autorização nos componentes pode usar uma função para autorizar o usuário:</span><span class="sxs-lookup"><span data-stu-id="4b68a-222">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="4b68a-223">[ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) (exemplo: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="4b68a-223">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="4b68a-224">[ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemplo: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="4b68a-224">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="4b68a-225">[Lógica de procedimento](xref:blazor/security/index#procedural-logic) (exemplo: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="4b68a-225">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="4b68a-226">Há suporte para vários testes de função:</span><span class="sxs-lookup"><span data-stu-id="4b68a-226">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="4b68a-227">`User.Identity.Name`é preenchido no aplicativo cliente com o nome de usuário do usuário, que geralmente é seu endereço de email de entrada.</span><span class="sxs-lookup"><span data-stu-id="4b68a-227">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="4b68a-228">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4b68a-228">Additional resources</span></span>

* [<span data-ttu-id="4b68a-229">Implantação no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="4b68a-229">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="4b68a-230">Importar um certificado do Key Vault (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="4b68a-230">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="4b68a-231">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="4b68a-231">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)

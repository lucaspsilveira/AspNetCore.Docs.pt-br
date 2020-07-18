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
ms.openlocfilehash: a27d31345cfe6a4212e3c61d0d99ae6745eab052
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445171"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="7ba95-103">Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com o Identity servidor</span><span class="sxs-lookup"><span data-stu-id="7ba95-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="7ba95-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7ba95-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7ba95-105">Este artigo explica como criar um novo Blazor aplicativo hospedado que usa o [ Identity servidor do](https://identityserver.io/) para autenticar usuários e chamadas de API.</span><span class="sxs-lookup"><span data-stu-id="7ba95-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="7ba95-106">Para configurar um aplicativo autônomo ou hospedado Blazor WebAssembly para usar uma instância de servidor externa existente Identity , siga as orientações em <xref:blazor/security/webassembly/standalone-with-authentication-library> .</span><span class="sxs-lookup"><span data-stu-id="7ba95-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7ba95-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ba95-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7ba95-108">Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="7ba95-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="7ba95-109">Depois de escolher o modelo de \*\* Blazor WebAssembly aplicativo\*\* na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione **alterar** em **autenticação**.</span><span class="sxs-lookup"><span data-stu-id="7ba95-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="7ba95-110">Selecione **contas de usuário individuais** com a opção **armazenar contas de usuário no aplicativo** para armazenar usuários no aplicativo usando o sistema do ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="7ba95-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="7ba95-111">Marque a caixa de seleção **ASP.NET Core hospedado** na seção **avançado** .</span><span class="sxs-lookup"><span data-stu-id="7ba95-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="7ba95-112">Visual Studio Code/CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="7ba95-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="7ba95-113">Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação em uma pasta vazia, especifique o `Individual` mecanismo de autenticação com a `-au|--auth` opção de armazenar usuários no aplicativo usando o [Identity](xref:security/authentication/identity) sistema do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="7ba95-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="7ba95-114">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="7ba95-114">Placeholder</span></span>  | <span data-ttu-id="7ba95-115">Exemplo</span><span class="sxs-lookup"><span data-stu-id="7ba95-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="7ba95-116">O local de saída especificado com a `-o|--output` opção criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba95-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="7ba95-117">Para obter mais informações, consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ba95-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7ba95-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7ba95-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7ba95-119">Para criar um novo Blazor WebAssembly projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="7ba95-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="7ba95-120">Na etapa **configurar seu novo Blazor WebAssembly aplicativo** , selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="7ba95-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="7ba95-121">O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="7ba95-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="7ba95-122">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="7ba95-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="7ba95-123">Configuração de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="7ba95-123">Server app configuration</span></span>

<span data-ttu-id="7ba95-124">As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído.</span><span class="sxs-lookup"><span data-stu-id="7ba95-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="7ba95-125">Classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="7ba95-125">Startup class</span></span>

<span data-ttu-id="7ba95-126">A `Startup` classe tem as seguintes adições.</span><span class="sxs-lookup"><span data-stu-id="7ba95-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="7ba95-127">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7ba95-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="7ba95-128">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="7ba95-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identity<span data-ttu-id="7ba95-129">Servidor com um <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar adicional que configura as convenções de ASP.NET Core padrão na parte superior do Identity servidor:</span><span class="sxs-lookup"><span data-stu-id="7ba95-129">Server with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="7ba95-130">Autenticação com um <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar adicional que configura o aplicativo para validar tokens JWT produzidos pelo Identity servidor:</span><span class="sxs-lookup"><span data-stu-id="7ba95-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="7ba95-131">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="7ba95-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="7ba95-132">O Identity middleware do servidor expõe os pontos de extremidade do Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="7ba95-132">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="7ba95-133">O middleware de autenticação é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:</span><span class="sxs-lookup"><span data-stu-id="7ba95-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="7ba95-134">O middleware de autorização habilita os recursos de autorização:</span><span class="sxs-lookup"><span data-stu-id="7ba95-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="7ba95-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="7ba95-135">AddApiAuthorization</span></span>

<span data-ttu-id="7ba95-136">O <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar configura o [ Identity servidor](https://identityserver.io/) para cenários de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ba95-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> Identity<span data-ttu-id="7ba95-137">O servidor é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba95-137">Server is a powerful and extensible framework for handling app security concerns.</span></span> Identity<span data-ttu-id="7ba95-138">O servidor expõe complexidade desnecessária para os cenários mais comuns.</span><span class="sxs-lookup"><span data-stu-id="7ba95-138">Server exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="7ba95-139">Consequentemente, um conjunto de convenções e opções de configuração é fornecido para considerarmos um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="7ba95-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="7ba95-140">Depois que a autenticação precisar ser alterada, todo o poder do Identity servidor estará disponível para personalizar a autenticação de acordo com os requisitos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba95-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="7ba95-141">Adicionar Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="7ba95-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="7ba95-142">O <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="7ba95-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="7ba95-143">A política está configurada para permitir que o Identity manipule todas as solicitações roteadas para qualquer subcaminho no Identity espaço de URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="7ba95-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="7ba95-144">O <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> lida com todas as outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="7ba95-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="7ba95-145">Além disso, esse método:</span><span class="sxs-lookup"><span data-stu-id="7ba95-145">Additionally, this method:</span></span>

* <span data-ttu-id="7ba95-146">Registra um `{APPLICATION NAME}API` recurso de API com Identity o servidor com um escopo padrão de `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="7ba95-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="7ba95-147">Configura o middleware do token de portador JWT para validar tokens emitidos pelo Identity servidor para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba95-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="7ba95-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="7ba95-148">WeatherForecastController</span></span>

<span data-ttu-id="7ba95-149">No `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ), o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo é aplicado à classe.</span><span class="sxs-lookup"><span data-stu-id="7ba95-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="7ba95-150">O atributo indica que o usuário deve ser autorizado com base na política padrão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="7ba95-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="7ba95-151">A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que é configurado por <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="7ba95-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="7ba95-152">O método auxiliar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> é configurado como o manipulador padrão para solicitações ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba95-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="7ba95-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="7ba95-153">ApplicationDbContext</span></span>

<span data-ttu-id="7ba95-154">No `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ), <xref:Microsoft.EntityFrameworkCore.DbContext> estende- <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se para incluir o esquema para o Identity servidor.</span><span class="sxs-lookup"><span data-stu-id="7ba95-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="7ba95-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>é derivado de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="7ba95-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="7ba95-156">Para obter controle total do esquema de banco de dados, herde de uma das Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes disponíveis e configure o contexto para incluir o Identity esquema chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` no <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> método.</span><span class="sxs-lookup"><span data-stu-id="7ba95-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="7ba95-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="7ba95-157">OidcConfigurationController</span></span>

<span data-ttu-id="7ba95-158">No `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ), o ponto de extremidade do cliente é provisionado para atender aos parâmetros OIDC.</span><span class="sxs-lookup"><span data-stu-id="7ba95-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="7ba95-159">Configurações de aplicativo</span><span class="sxs-lookup"><span data-stu-id="7ba95-159">App settings</span></span>

<span data-ttu-id="7ba95-160">No arquivo de configurações do aplicativo ( `appsettings.json` ) na raiz do projeto, a `IdentityServer` seção descreve a lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="7ba95-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="7ba95-161">No exemplo a seguir, há um único cliente.</span><span class="sxs-lookup"><span data-stu-id="7ba95-161">In the following example, there's a single client.</span></span> <span data-ttu-id="7ba95-162">O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o `ClientId` parâmetro OAuth.</span><span class="sxs-lookup"><span data-stu-id="7ba95-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="7ba95-163">O perfil indica o tipo de aplicativo que está sendo configurado.</span><span class="sxs-lookup"><span data-stu-id="7ba95-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="7ba95-164">O perfil é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor.</span><span class="sxs-lookup"><span data-stu-id="7ba95-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="7ba95-165">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="7ba95-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="7ba95-166">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="7ba95-166">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7ba95-167">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="7ba95-167">Authentication package</span></span>

<span data-ttu-id="7ba95-168">Quando um aplicativo é criado para usar contas de usuário individuais ( `Individual` ), o aplicativo recebe automaticamente uma referência de pacote para o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba95-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="7ba95-169">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="7ba95-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7ba95-170">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7ba95-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="httpclient-configuration"></a><span data-ttu-id="7ba95-171">`HttpClient`configuração</span><span class="sxs-lookup"><span data-stu-id="7ba95-171">`HttpClient` configuration</span></span>

<span data-ttu-id="7ba95-172">Em `Program.Main` ( `Program.cs` ), um nome <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ) é configurado para fornecer <xref:System.Net.Http.HttpClient> instâncias que incluem tokens de acesso ao fazer solicitações para a API do servidor:</span><span class="sxs-lookup"><span data-stu-id="7ba95-172">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="7ba95-173">Se você estiver configurando um Blazor WebAssembly aplicativo para usar uma Identity instância de servidor existente que não faz parte de uma Blazor solução hospedada, altere o <xref:System.Net.Http.HttpClient> registro do endereço base de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) para a URL do ponto de extremidade de autorização da API do aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="7ba95-173">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a Blazor Hosted solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="7ba95-174">Suporte à autorização de API</span><span class="sxs-lookup"><span data-stu-id="7ba95-174">API authorization support</span></span>

<span data-ttu-id="7ba95-175">O suporte para autenticação de usuários é conectado ao contêiner de serviço pelo método de extensão fornecido dentro do [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pacote.</span><span class="sxs-lookup"><span data-stu-id="7ba95-175">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="7ba95-176">Esse método configura os serviços exigidos pelo aplicativo para interagir com o sistema de autorização existente.</span><span class="sxs-lookup"><span data-stu-id="7ba95-176">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="7ba95-177">Por padrão, a configuração para o aplicativo é carregada por convenção de `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="7ba95-177">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="7ba95-178">Por convenção, a ID do cliente é definida como o nome do assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba95-178">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="7ba95-179">Essa URL pode ser alterada para apontar para um ponto de extremidade separado chamando a sobrecarga com opções.</span><span class="sxs-lookup"><span data-stu-id="7ba95-179">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="7ba95-180">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="7ba95-180">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="7ba95-181">Página de índice</span><span class="sxs-lookup"><span data-stu-id="7ba95-181">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="7ba95-182">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="7ba95-182">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="7ba95-183">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="7ba95-183">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="7ba95-184">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="7ba95-184">LoginDisplay component</span></span>

<span data-ttu-id="7ba95-185">O `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) é renderizado no `MainLayout` componente ( `Shared/MainLayout.razor` ) e gerencia os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="7ba95-185">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="7ba95-186">Para usuários autenticados:</span><span class="sxs-lookup"><span data-stu-id="7ba95-186">For authenticated users:</span></span>
  * <span data-ttu-id="7ba95-187">Exibe o nome de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="7ba95-187">Displays the current user name.</span></span>
  * <span data-ttu-id="7ba95-188">Oferece um link para a página de perfil do usuário no ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="7ba95-188">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="7ba95-189">Oferece um botão para fazer logoff do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba95-189">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="7ba95-190">Para usuários anônimos:</span><span class="sxs-lookup"><span data-stu-id="7ba95-190">For anonymous users:</span></span>
  * <span data-ttu-id="7ba95-191">Oferece a opção de se registrar.</span><span class="sxs-lookup"><span data-stu-id="7ba95-191">Offers the option to register.</span></span>
  * <span data-ttu-id="7ba95-192">Oferece a opção de fazer logon.</span><span class="sxs-lookup"><span data-stu-id="7ba95-192">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="7ba95-193">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="7ba95-193">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="7ba95-194">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="7ba95-194">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="7ba95-195">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="7ba95-195">Run the app</span></span>

<span data-ttu-id="7ba95-196">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="7ba95-196">Run the app from the Server project.</span></span> <span data-ttu-id="7ba95-197">Ao usar o Visual Studio, seja:</span><span class="sxs-lookup"><span data-stu-id="7ba95-197">When using Visual Studio, either:</span></span>

* <span data-ttu-id="7ba95-198">Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .</span><span class="sxs-lookup"><span data-stu-id="7ba95-198">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="7ba95-199">Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="7ba95-199">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="7ba95-200">Nome e declaração de função com autorização de API</span><span class="sxs-lookup"><span data-stu-id="7ba95-200">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="7ba95-201">Fábrica de usuário personalizada</span><span class="sxs-lookup"><span data-stu-id="7ba95-201">Custom user factory</span></span>

<span data-ttu-id="7ba95-202">No aplicativo cliente, crie uma fábrica de usuário personalizada.</span><span class="sxs-lookup"><span data-stu-id="7ba95-202">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="7ba95-203">O servidor envia várias funções como uma matriz JSON em uma única `role` declaração.</span><span class="sxs-lookup"><span data-stu-id="7ba95-203"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="7ba95-204">Uma única função é enviada como um valor de cadeia de caracteres na declaração.</span><span class="sxs-lookup"><span data-stu-id="7ba95-204">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="7ba95-205">A fábrica cria uma `role` declaração individual para cada uma das funções do usuário.</span><span class="sxs-lookup"><span data-stu-id="7ba95-205">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="7ba95-206">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="7ba95-206">`CustomUserFactory.cs`:</span></span>

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

<span data-ttu-id="7ba95-207">No aplicativo cliente, registre a fábrica em `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="7ba95-207">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="7ba95-208">No aplicativo de servidor, chame <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> no Identity Construtor, que adiciona serviços relacionados a funções:</span><span class="sxs-lookup"><span data-stu-id="7ba95-208">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="7ba95-209">Configurar Identity servidor</span><span class="sxs-lookup"><span data-stu-id="7ba95-209">Configure Identity Server</span></span>

<span data-ttu-id="7ba95-210">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7ba95-210">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="7ba95-211">Opções de autorização de API</span><span class="sxs-lookup"><span data-stu-id="7ba95-211">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="7ba95-212">Serviço de perfil</span><span class="sxs-lookup"><span data-stu-id="7ba95-212">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="7ba95-213">Opções de autorização de API</span><span class="sxs-lookup"><span data-stu-id="7ba95-213">API authorization options</span></span>

<span data-ttu-id="7ba95-214">No aplicativo do servidor:</span><span class="sxs-lookup"><span data-stu-id="7ba95-214">In the Server app:</span></span>

* <span data-ttu-id="7ba95-215">Configure Identity o servidor para colocar o `name` e as `role` declarações no token de ID e no token de acesso.</span><span class="sxs-lookup"><span data-stu-id="7ba95-215">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="7ba95-216">Impedir o mapeamento padrão para funções no manipulador de tokens JWT.</span><span class="sxs-lookup"><span data-stu-id="7ba95-216">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="7ba95-217">Serviço de perfil</span><span class="sxs-lookup"><span data-stu-id="7ba95-217">Profile Service</span></span>

<span data-ttu-id="7ba95-218">No aplicativo de servidor, crie uma `ProfileService` implementação.</span><span class="sxs-lookup"><span data-stu-id="7ba95-218">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="7ba95-219">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="7ba95-219">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="7ba95-220">No aplicativo de servidor, registre o serviço de perfil em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7ba95-220">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="7ba95-221">Usar mecanismos de autorização</span><span class="sxs-lookup"><span data-stu-id="7ba95-221">Use authorization mechanisms</span></span>

<span data-ttu-id="7ba95-222">No aplicativo cliente, as abordagens de autorização de componente são funcionais neste ponto.</span><span class="sxs-lookup"><span data-stu-id="7ba95-222">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="7ba95-223">Qualquer um dos mecanismos de autorização nos componentes pode usar uma função para autorizar o usuário:</span><span class="sxs-lookup"><span data-stu-id="7ba95-223">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="7ba95-224">[ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) (exemplo: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="7ba95-224">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="7ba95-225">[ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemplo: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="7ba95-225">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="7ba95-226">[Lógica de procedimento](xref:blazor/security/index#procedural-logic) (exemplo: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="7ba95-226">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="7ba95-227">Há suporte para vários testes de função:</span><span class="sxs-lookup"><span data-stu-id="7ba95-227">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="7ba95-228">`User.Identity.Name`é preenchido no aplicativo cliente com o nome de usuário do usuário, que geralmente é seu endereço de email de entrada.</span><span class="sxs-lookup"><span data-stu-id="7ba95-228">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7ba95-229">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7ba95-229">Additional resources</span></span>

* [<span data-ttu-id="7ba95-230">Implantação no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="7ba95-230">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="7ba95-231">Importar um certificado do Key Vault (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="7ba95-231">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="7ba95-232">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="7ba95-232">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)

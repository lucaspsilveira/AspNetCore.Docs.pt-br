---
title: Proteja um Blazor aplicativo hospedado ASP.NET WebAssembly do Core com o Identity Server
author: guardrex
description: Para criar Blazor um novo aplicativo hospedado com autenticação dentro do Visual Studio que usa um backend [do IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501276"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="9a5a0-103">Proteja um Blazor aplicativo hospedado ASP.NET WebAssembly do Core com o Identity Server</span><span class="sxs-lookup"><span data-stu-id="9a5a0-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="9a5a0-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9a5a0-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="9a5a0-105">Para criar Blazor um novo aplicativo hospedado no Visual Studio que usa [o IdentityServer](https://identityserver.io/) para autenticar usuários e chamadas de API:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="9a5a0-106">Use o Visual Studio \*\* Blazor \*\* para criar um novo aplicativo WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="9a5a0-107">Para obter mais informações, consulte <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="9a5a0-108">Na **criação de Blazor uma nova** caixa de diálogo de aplicativo, **selecione Alterar** na seção **Autenticação.**</span><span class="sxs-lookup"><span data-stu-id="9a5a0-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="9a5a0-109">Selecione **Contas de Usuário Individuais** seguidas por **OK**.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="9a5a0-110">Selecione a caixa de seleção **hospedada ASP.NET Core** na seção **Avançado.**</span><span class="sxs-lookup"><span data-stu-id="9a5a0-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="9a5a0-111">Selecione o botão **Criar**.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-111">Select the **Create** button.</span></span>

<span data-ttu-id="9a5a0-112">Para criar o aplicativo em um shell de comando, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="9a5a0-113">Para especificar o local de saída, que cria uma pasta de projeto se ela não `-o BlazorSample`existir, inclua a opção de saída no comando com um caminho (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="9a5a0-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="9a5a0-114">O nome da pasta também passa a fazer parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="9a5a0-115">Configuração do aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="9a5a0-115">Server app configuration</span></span>

<span data-ttu-id="9a5a0-116">As seções a seguir descrevem adições ao projeto quando o suporte à autenticação é incluído.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="9a5a0-117">Classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="9a5a0-117">Startup class</span></span>

<span data-ttu-id="9a5a0-118">A `Startup` classe tem as seguintes adições:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="9a5a0-119">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="9a5a0-120">Identidade com a ui padrão:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="9a5a0-121">IdentityServer com <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> um método de ajuda adicional que configura algumas convenções padrão ASP.NET Core em cima do IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="9a5a0-122">Autenticação com <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> um método auxiliar adicional que configura o aplicativo para validar tokens JWT produzidos pelo IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="9a5a0-123">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="9a5a0-124">O middleware de autenticação responsável pela validação das credenciais de solicitação e pela definição do usuário no contexto da solicitação:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="9a5a0-125">O middleware IdentityServer que expõe os pontos finais do Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="9a5a0-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="9a5a0-126">Autorização do AddApi</span><span class="sxs-lookup"><span data-stu-id="9a5a0-126">AddApiAuthorization</span></span>

<span data-ttu-id="9a5a0-127">O <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método helper configura [O IdentityServer](https://identityserver.io/) para ASP.NET cenários Core.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="9a5a0-128">IdentityServer é uma estrutura poderosa e extensível para lidar com preocupações de segurança de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="9a5a0-129">IdentityServer expõe complexidade desnecessária para os cenários mais comuns.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="9a5a0-130">Consequentemente, um conjunto de convenções e opções de configuração é fornecido que consideramos um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="9a5a0-131">Uma vez que sua autenticação precise ser mudada, o poder total do IdentityServer ainda está disponível para personalizar a autenticação de acordo com as necessidades de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="9a5a0-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="9a5a0-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="9a5a0-133">O <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método helper configura um esquema de diretiva para o aplicativo como o manipulador de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="9a5a0-134">A diretiva está configurada para permitir que a Identidade manuseie `/Identity`todas as solicitações roteadas para qualquer subcaminho no espaço URL de identidade .</span><span class="sxs-lookup"><span data-stu-id="9a5a0-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="9a5a0-135">O <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> cabo todos os outros pedidos.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="9a5a0-136">Além disso, este método:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-136">Additionally, this method:</span></span>

* <span data-ttu-id="9a5a0-137">Registra um `{APPLICATION NAME}API` recurso de API com o `{APPLICATION NAME}API`IdentityServer com um escopo padrão de .</span><span class="sxs-lookup"><span data-stu-id="9a5a0-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="9a5a0-138">Configura o JWT Bearer Token Middleware para validar tokens emitidos pelo IdentityServer para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="9a5a0-139">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="9a5a0-139">WeatherForecastController</span></span>

<span data-ttu-id="9a5a0-140">No `WeatherForecastController` *(Controllers/WeatherForecastController.cs),* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) o atributo é aplicado à classe.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="9a5a0-141">O atributo indica que o usuário deve ser autorizado com base na política padrão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="9a5a0-142">A política de autorização padrão está configurada para usar o <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> esquema de autenticação padrão, que é configurado pelo esquema de diretiva mencionado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="9a5a0-143">O método helper <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> configura-se como o manipulador padrão para solicitações ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="9a5a0-144">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="9a5a0-144">ApplicationDbContext</span></span>

<span data-ttu-id="9a5a0-145">No `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> o mesmo é usado em <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> Identidade, com a exceção que se estende para incluir o esquema para IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="9a5a0-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>é derivado <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>de .</span><span class="sxs-lookup"><span data-stu-id="9a5a0-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="9a5a0-147">Para obter o controle total do esquema do banco <xref:Microsoft.EntityFrameworkCore.DbContext> de dados, herde de uma das classes `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` de `OnModelCreating` Identidade disponíveis e configure o contexto para incluir o esquema de Identidade, chamando o método.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="9a5a0-148">Controlador de configuração oidc</span><span class="sxs-lookup"><span data-stu-id="9a5a0-148">OidcConfigurationController</span></span>

<span data-ttu-id="9a5a0-149">No `OidcConfigurationController` *(Controllers/OidcConfigurationController.cs*), o ponto final do cliente é provisionado para atender aos parâmetros OIDC.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="9a5a0-150">Arquivos de configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="9a5a0-150">App settings files</span></span>

<span data-ttu-id="9a5a0-151">No arquivo de configurações do aplicativo *(appsettings.json*) na raiz do projeto, a `IdentityServer` seção descreve a lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="9a5a0-152">No exemplo a seguir, há um único cliente.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-152">In the following example, there's a single client.</span></span> <span data-ttu-id="9a5a0-153">O nome do cliente corresponde ao nome do aplicativo e `ClientId` é mapeado por convenção para o parâmetro OAuth.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="9a5a0-154">O perfil indica que o tipo de aplicativo está sendo configurado.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="9a5a0-155">O perfil é usado internamente para conduzir convenções que simplificam o processo de configuração do servidor.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="9a5a0-156">No arquivo de configurações do aplicativo ambiente Desenvolvimento *(appsettings. Development.json*) Na raiz `IdentityServer` do projeto, a seção descreve a chave usada para assinar tokens.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="9a5a0-157">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="9a5a0-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9a5a0-158">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="9a5a0-158">Authentication package</span></span>

<span data-ttu-id="9a5a0-159">Quando um aplicativo é criado para`Individual`usar contas de usuário individuais ( `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ), o aplicativo recebe automaticamente uma referência de pacote para o pacote no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="9a5a0-160">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="9a5a0-161">Se adicionar autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="9a5a0-162">Substitua `{VERSION}` a referência do pacote `Microsoft.AspNetCore.Blazor.Templates` anterior pela <xref:blazor/get-started> versão do pacote mostrado no artigo.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="9a5a0-163">Suporte à autorização da API</span><span class="sxs-lookup"><span data-stu-id="9a5a0-163">API authorization support</span></span>

<span data-ttu-id="9a5a0-164">O suporte para autenticar usuários é conectado ao contêiner de serviço `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pelo método de extensão fornecido dentro do pacote.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="9a5a0-165">Este método configura todos os serviços necessários para que o aplicativo interaja com o sistema de autorização existente.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="9a5a0-166">Por padrão, ele carrega a configuração `_configuration/{client-id}`para o aplicativo por convenção de .</span><span class="sxs-lookup"><span data-stu-id="9a5a0-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="9a5a0-167">Por convenção, o ID do cliente é definido para o nome de montagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="9a5a0-168">Esta URL pode ser alterada para apontar para um ponto final separado, chamando a sobrecarga com opções.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="9a5a0-169">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="9a5a0-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="9a5a0-170">Página de índice</span><span class="sxs-lookup"><span data-stu-id="9a5a0-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="9a5a0-171">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="9a5a0-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="9a5a0-172">Componente RedirecionarToLogin</span><span class="sxs-lookup"><span data-stu-id="9a5a0-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="9a5a0-173">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="9a5a0-173">LoginDisplay component</span></span>

<span data-ttu-id="9a5a0-174">O `LoginDisplay` componente (*Compartilhado/LoginDisplay.razor*) é `MainLayout` renderizado no componente *(Compartilhado/MainLayout.razor)* e gerencia os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="9a5a0-175">Para usuários autenticados:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-175">For authenticated users:</span></span>
  * <span data-ttu-id="9a5a0-176">Exibe o nome de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-176">Displays the current user name.</span></span>
  * <span data-ttu-id="9a5a0-177">Oferece um link para a página de perfil do usuário em ASP.NET Identidade Central.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="9a5a0-178">Oferece um botão para sair do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="9a5a0-179">Para usuários anônimos:</span><span class="sxs-lookup"><span data-stu-id="9a5a0-179">For anonymous users:</span></span>
  * <span data-ttu-id="9a5a0-180">Oferece a opção de se inscrever.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-180">Offers the option to register.</span></span>
  * <span data-ttu-id="9a5a0-181">Oferece a opção de fazer login.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="9a5a0-182">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="9a5a0-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="9a5a0-183">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="9a5a0-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="9a5a0-184">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="9a5a0-184">Run the app</span></span>

<span data-ttu-id="9a5a0-185">Execute o aplicativo do projeto Servidor.</span><span class="sxs-lookup"><span data-stu-id="9a5a0-185">Run the app from the Server project.</span></span> <span data-ttu-id="9a5a0-186">Ao usar o Visual Studio, selecione o projeto Servidor no **Solution Explorer** e selecione o botão **Executar** na barra de ferramentas ou iniciar o aplicativo no menu **Debug.**</span><span class="sxs-lookup"><span data-stu-id="9a5a0-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="9a5a0-187">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9a5a0-187">Additional resources</span></span>

* [<span data-ttu-id="9a5a0-188">Solicite tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="9a5a0-188">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)

---
title: Introdução à autenticação para aplicativos de página única no ASP.NET Core
author: javiercn
description: Use Identity com um aplicativo de página única hospedado dentro de um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 2b587517268208dcf66cd2895b7aa22bfa381f84
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060352"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="b9ce1-103">Autenticação e autorização para SPAs</span><span class="sxs-lookup"><span data-stu-id="b9ce1-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="b9ce1-104">O ASP.NET Core 3,0 ou posterior oferece autenticação em aplicativos de página única (SPAs) usando o suporte para autorização de API.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="b9ce1-105">ASP.NET Core Identity para autenticação e armazenamento de usuários é combinado com o [IdentityServer](https://identityserver.io/) para implementar o Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="b9ce1-106">Um parâmetro de autenticação foi adicionado aos modelos de projeto **angular** e **reajam** que é semelhante ao parâmetro de autenticação nos modelos de projeto de **aplicativo Web (Model-View-Controller)** (MVC) e **aplicativo Web** ( Razor páginas).</span><span class="sxs-lookup"><span data-stu-id="b9ce1-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="b9ce1-107">Os valores de parâmetro permitidos são **None** e **individual**.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="b9ce1-108">O modelo de projeto **React.js e Redux** não dá suporte ao parâmetro de autenticação neste momento.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="b9ce1-109">Criar um aplicativo com suporte à autorização de API</span><span class="sxs-lookup"><span data-stu-id="b9ce1-109">Create an app with API authorization support</span></span>

<span data-ttu-id="b9ce1-110">A autenticação e a autorização do usuário podem ser usadas com SPAs angular e reagir.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="b9ce1-111">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="b9ce1-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="b9ce1-113">**Reagir**:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="b9ce1-114">O comando anterior cria um aplicativo ASP.NET Core com um diretório *ClientApp* que contém o Spa.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="b9ce1-115">Descrição geral dos componentes ASP.NET Core do aplicativo</span><span class="sxs-lookup"><span data-stu-id="b9ce1-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="b9ce1-116">As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="b9ce1-117">Classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="b9ce1-117">Startup class</span></span>

<span data-ttu-id="b9ce1-118">Os exemplos de código a seguir dependem do pacote NuGet [Microsoft. AspNetCore. ApiAuthorization. IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) .</span><span class="sxs-lookup"><span data-stu-id="b9ce1-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="b9ce1-119">Os exemplos configuram a autenticação e a autorização da API usando os <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> métodos de extensão e.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="b9ce1-120">Os projetos que usam os modelos de projeto de SPA de reagir ou angulares com autenticação incluem uma referência a esse pacote.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="b9ce1-121">A `Startup` classe tem as seguintes adições:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="b9ce1-122">Dentro do `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * Identity<span data-ttu-id="b9ce1-123">com a interface do usuário padrão:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-123"> with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="b9ce1-124">IdentityServer com um `AddApiAuthorization` método auxiliar adicional que configura algumas convenções de ASP.NET Core padrão na parte superior do IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="b9ce1-125">Autenticação com um `AddIdentityServerJwt` método auxiliar adicional que configura o aplicativo para validar tokens JWT produzidos por IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="b9ce1-126">Dentro do `Startup.Configure` método:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="b9ce1-127">O middleware de autenticação que é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="b9ce1-128">O middleware IdentityServer que expõe os pontos de extremidade do Open ID Connect:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-128">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="b9ce1-129">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="b9ce1-129">AddApiAuthorization</span></span>

<span data-ttu-id="b9ce1-130">Esse método auxiliar configura o IdentityServer para usar nossa configuração com suporte.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-130">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="b9ce1-131">O IdentityServer é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-131">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="b9ce1-132">Ao mesmo tempo, isso expõe uma complexidade desnecessária para os cenários mais comuns.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-132">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="b9ce1-133">Consequentemente, um conjunto de convenções e opções de configuração é fornecido para você que são considerados um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-133">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="b9ce1-134">Depois que a autenticação precisar ser alterada, toda a potência do IdentityServer ainda estará disponível para personalizar a autenticação de acordo com suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-134">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="b9ce1-135">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="b9ce1-135">AddIdentityServerJwt</span></span>

<span data-ttu-id="b9ce1-136">Esse método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-136">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="b9ce1-137">A política está configurada para permitir que o Identity manipule todas as solicitações roteadas para qualquer subcaminho no Identity espaço de URL "/ Identity ".</span><span class="sxs-lookup"><span data-stu-id="b9ce1-137">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="b9ce1-138">O `JwtBearerHandler` lida com todas as outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-138">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="b9ce1-139">Além disso, esse método registra um `<<ApplicationName>>API` recurso de API com IdentityServer com um escopo padrão `<<ApplicationName>>API` e configura o middleware de token de portador JWT para validar tokens emitidos por IdentityServer para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-139">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="b9ce1-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="b9ce1-140">WeatherForecastController</span></span>

<span data-ttu-id="b9ce1-141">No arquivo *Controllers\WeatherForecastController.cs* , observe o `[Authorize]` atributo aplicado à classe que indica que o usuário precisa ser autorizado com base na política padrão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-141">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="b9ce1-142">A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que é configurado até `AddIdentityServerJwt` o esquema de política mencionado acima, tornando o `JwtBearerHandler` método auxiliar configurado por tal como o manipulador padrão para solicitações para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-142">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="b9ce1-143">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="b9ce1-143">ApplicationDbContext</span></span>

<span data-ttu-id="b9ce1-144">No arquivo *Data\ApplicationDbContext.cs* , observe que o mesmo `DbContext` é usado em Identity com a exceção que ele estende `ApiAuthorizationDbContext` (uma classe mais derivada de `IdentityDbContext` ) para incluir o esquema para IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-144">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="b9ce1-145">Para obter controle total do esquema de banco de dados, herde de uma das Identity `DbContext` classes disponíveis e configure o contexto para incluir o Identity esquema chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` no `OnModelCreating` método.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-145">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="b9ce1-146">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="b9ce1-146">OidcConfigurationController</span></span>

<span data-ttu-id="b9ce1-147">No arquivo *Controllers\OidcConfigurationController.cs* , observe o ponto de extremidade provisionado para atender aos parâmetros OIDC que o cliente precisa usar.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-147">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="b9ce1-148">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="b9ce1-148">appsettings.json</span></span>

<span data-ttu-id="b9ce1-149">No *appsettings.jsno* arquivo da raiz do projeto, há uma nova `IdentityServer` seção que descreve a lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-149">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="b9ce1-150">No exemplo a seguir, há um único cliente.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-150">In the following example, there's a single client.</span></span> <span data-ttu-id="b9ce1-151">O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o `ClientId` parâmetro OAuth.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-151">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="b9ce1-152">O perfil indica o tipo de aplicativo que está sendo configurado.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-152">The profile indicates the app type being configured.</span></span> <span data-ttu-id="b9ce1-153">Ele é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-153">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="b9ce1-154">Há vários perfis disponíveis, conforme explicado na seção [perfis de aplicativo](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="b9ce1-154">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="b9ce1-155">appsettings.Development.jsem</span><span class="sxs-lookup"><span data-stu-id="b9ce1-155">appsettings.Development.json</span></span>

<span data-ttu-id="b9ce1-156">No *appsettings.Development.jsno* arquivo da raiz do projeto, há uma `IdentityServer` seção que descreve a chave usada para assinar tokens.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-156">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="b9ce1-157">Ao implantar na produção, uma chave precisa ser provisionada e implantada juntamente com o aplicativo, conforme explicado na seção [implantar na produção](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="b9ce1-157">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="b9ce1-158">Descrição geral do aplicativo angular</span><span class="sxs-lookup"><span data-stu-id="b9ce1-158">General description of the Angular app</span></span>

<span data-ttu-id="b9ce1-159">O suporte de autorização de API e autenticação no modelo angular reside em seu próprio módulo angular no diretório *ClientApp\src\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="b9ce1-159">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="b9ce1-160">O módulo é composto pelos seguintes elementos:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-160">The module is composed of the following elements:</span></span>

* <span data-ttu-id="b9ce1-161">3 componentes:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-161">3 components:</span></span>
  * <span data-ttu-id="b9ce1-162">*login. Component. TS*: manipula o fluxo de logon do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-162">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="b9ce1-163">*logout. Component. TS*: manipula o fluxo de logout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-163">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="b9ce1-164">*login-menu. Component. TS*: um widget que exibe um dos seguintes conjuntos de links:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-164">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="b9ce1-165">Os links de gerenciamento de perfil de usuário e logoff quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-165">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="b9ce1-166">Os links de registro e logon quando o usuário não está autenticado.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-166">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="b9ce1-167">Uma proteção de rota `AuthorizeGuard` que pode ser adicionada a rotas e requer que um usuário seja autenticado antes de visitar a rota.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-167">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="b9ce1-168">Um interceptador HTTP `AuthorizeInterceptor` que anexa o token de acesso a solicitações HTTP de saída direcionando a API quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-168">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="b9ce1-169">Um serviço `AuthorizeService` que manipula os detalhes de nível inferior do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-169">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="b9ce1-170">Um módulo angular que define as rotas associadas às partes de autenticação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-170">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="b9ce1-171">Ele expõe o componente de menu de logon, o Interceptor, a proteção e o serviço para consumo do restante do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-171">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="b9ce1-172">Descrição geral do aplicativo reajam</span><span class="sxs-lookup"><span data-stu-id="b9ce1-172">General description of the React app</span></span>

<span data-ttu-id="b9ce1-173">O suporte para autenticação e autorização de API no modelo reagir reside no diretório *ClientApp\src\components\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="b9ce1-173">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="b9ce1-174">Ele é composto pelos seguintes elementos:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-174">It's composed of the following elements:</span></span>

* <span data-ttu-id="b9ce1-175">4 componentes:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-175">4 components:</span></span>
  * <span data-ttu-id="b9ce1-176">*Login.js*: manipula o fluxo de logon do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-176">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="b9ce1-177">*Logout.js*: manipula o fluxo de logout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-177">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="b9ce1-178">*LoginMenu.js*: um widget que exibe um dos seguintes conjuntos de links:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-178">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="b9ce1-179">Os links de gerenciamento de perfil de usuário e logoff quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-179">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="b9ce1-180">Os links de registro e logon quando o usuário não está autenticado.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-180">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="b9ce1-181">*AuthorizeRoute.js*: um componente de rota que exige que um usuário seja autenticado antes de renderizar o componente indicado no `Component` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-181">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="b9ce1-182">Uma instância exportada `authService` da classe `AuthorizeService` que manipula os detalhes de nível inferior do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-182">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="b9ce1-183">Agora que você já viu os principais componentes da solução, pode fazer uma análise mais profunda de cenários individuais para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-183">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="b9ce1-184">Exigir autorização em uma nova API</span><span class="sxs-lookup"><span data-stu-id="b9ce1-184">Require authorization on a new API</span></span>

<span data-ttu-id="b9ce1-185">Por padrão, o sistema está configurado para exigir facilmente a autorização para novas APIs.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-185">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="b9ce1-186">Para fazer isso, crie um novo controlador e adicione o `[Authorize]` atributo à classe do controlador ou a qualquer ação dentro do controlador.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-186">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="b9ce1-187">Personalizar o manipulador de autenticação de API</span><span class="sxs-lookup"><span data-stu-id="b9ce1-187">Customize the API authentication handler</span></span>

<span data-ttu-id="b9ce1-188">Para personalizar a configuração do manipulador JWT da API, configure sua <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instância:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-188">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="b9ce1-189">O manipulador JWT da API gera eventos que permitem o controle sobre o processo de autenticação usando `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="b9ce1-189">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="b9ce1-190">Para fornecer suporte para autorização de API, `AddIdentityServerJwt` o registra seus próprios manipuladores de eventos.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-190">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="b9ce1-191">Para personalizar o tratamento de um evento, empacote o manipulador de eventos existente com lógica adicional, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-191">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="b9ce1-192">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-192">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="b9ce1-193">No código anterior, o `OnTokenValidated` manipulador de eventos é substituído por uma implementação personalizada.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-193">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="b9ce1-194">Esta implementação:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-194">This implementation:</span></span>

1. <span data-ttu-id="b9ce1-195">Chama a implementação original fornecida pelo suporte à autorização da API.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-195">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="b9ce1-196">Execute sua própria lógica personalizada.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-196">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="b9ce1-197">Proteger uma rota do lado do cliente (angular)</span><span class="sxs-lookup"><span data-stu-id="b9ce1-197">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="b9ce1-198">A proteção de uma rota do lado do cliente é feita pela adição da proteção de autorização à lista de proteções a serem executadas durante a configuração de uma rota.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-198">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="b9ce1-199">Como exemplo, você pode ver como a `fetch-data` rota é configurada dentro do módulo angular do aplicativo principal:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-199">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="b9ce1-200">É importante mencionar que a proteção de uma rota não protege o ponto de extremidade real (que ainda requer um `[Authorize]` atributo aplicado a ele), mas que apenas impede que o usuário navegue até a rota do lado do cliente quando não é autenticado.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-200">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="b9ce1-201">Autenticar solicitações de API (angular)</span><span class="sxs-lookup"><span data-stu-id="b9ce1-201">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="b9ce1-202">A autenticação de solicitações para APIs hospedadas juntamente com o aplicativo é feita automaticamente pelo uso do interceptador de cliente HTTP definido pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-202">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="b9ce1-203">Proteger uma rota do lado do cliente (reagir)</span><span class="sxs-lookup"><span data-stu-id="b9ce1-203">Protect a client-side route (React)</span></span>

<span data-ttu-id="b9ce1-204">Proteger uma rota do lado do cliente usando o `AuthorizeRoute` componente em vez do `Route` componente simples.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-204">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="b9ce1-205">Por exemplo, observe como a `fetch-data` rota é configurada dentro do `App` componente:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-205">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="b9ce1-206">Protegendo uma rota:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-206">Protecting a route:</span></span>

* <span data-ttu-id="b9ce1-207">Não protege o ponto de extremidade real (que ainda requer um `[Authorize]` atributo aplicado a ele).</span><span class="sxs-lookup"><span data-stu-id="b9ce1-207">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="b9ce1-208">Impede que o usuário navegue para a rota determinada do lado do cliente quando ele não é autenticado.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-208">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="b9ce1-209">Autenticar solicitações de API (reagir)</span><span class="sxs-lookup"><span data-stu-id="b9ce1-209">Authenticate API requests (React)</span></span>

<span data-ttu-id="b9ce1-210">A autenticação de solicitações com reagir é feita pela primeira importação da `authService` instância do `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="b9ce1-210">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="b9ce1-211">O token de acesso é recuperado do `authService` e é anexado à solicitação, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-211">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="b9ce1-212">Em reajam de componentes, esse trabalho normalmente é feito no `componentDidMount` método de ciclo de vida ou como resultado de alguma interação do usuário.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-212">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="b9ce1-213">Importar o authService para o componente</span><span class="sxs-lookup"><span data-stu-id="b9ce1-213">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="b9ce1-214">Recuperar e anexar o token de acesso à resposta</span><span class="sxs-lookup"><span data-stu-id="b9ce1-214">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="b9ce1-215">Implantar para a produção</span><span class="sxs-lookup"><span data-stu-id="b9ce1-215">Deploy to production</span></span>

<span data-ttu-id="b9ce1-216">Para implantar o aplicativo na produção, os seguintes recursos precisam ser provisionados:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-216">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="b9ce1-217">Um banco de dados para armazenar as Identity contas de usuário e as concessões IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-217">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="b9ce1-218">Um certificado de produção a ser usado para tokens de assinatura.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-218">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="b9ce1-219">Não há requisitos específicos para esse certificado; pode ser um certificado autoassinado ou um certificado provisionado por meio de uma autoridade de certificação.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-219">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="b9ce1-220">Ele pode ser gerado por meio de ferramentas padrão, como o PowerShell ou o OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-220">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="b9ce1-221">Ele pode ser instalado no repositório de certificados nos computadores de destino ou implantado como um arquivo *. pfx* com uma senha forte.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-221">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="b9ce1-222">Exemplo: implantar no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="b9ce1-222">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="b9ce1-223">Esta seção descreve a implantação do aplicativo no serviço Azure App usando um certificado armazenado no repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-223">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="b9ce1-224">Para modificar o aplicativo para carregar um certificado do repositório de certificados, um plano de serviço de camada Standard ou melhor é necessário quando você configura o aplicativo no portal do Azure em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-224">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="b9ce1-225">Naappsettings.jsdo aplicativo *no* arquivo, modifique a `IdentityServer` seção para incluir os detalhes da chave:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-225">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="b9ce1-226">O nome do repositório representa o nome do repositório de certificados onde o certificado está armazenado.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-226">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="b9ce1-227">Nesse caso, ele aponta para o repositório de usuários pessoais.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-227">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="b9ce1-228">O local do repositório representa onde carregar o certificado ( `CurrentUser` ou `LocalMachine` ).</span><span class="sxs-lookup"><span data-stu-id="b9ce1-228">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="b9ce1-229">A propriedade Name no certificado corresponde ao assunto distinto do certificado.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-229">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="b9ce1-230">Para implantar o serviço Azure App, siga as etapas em [implantar o aplicativo no Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), que explica como criar os recursos do Azure necessários e implantar o aplicativo para produção.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-230">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="b9ce1-231">Depois de seguir as instruções anteriores, o aplicativo é implantado no Azure, mas ainda não está funcional.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-231">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="b9ce1-232">O certificado usado pelo aplicativo deve ser configurado no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-232">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="b9ce1-233">Localize a impressão digital para o certificado e siga as etapas descritas em [carregar seus certificados](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="b9ce1-233">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="b9ce1-234">Embora essas etapas mencionem o SSL, há uma seção de **certificados privados** no portal do Azure em que você pode carregar o certificado provisionado para usar com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-234">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="b9ce1-235">Depois de configurar o aplicativo e as configurações do aplicativo no portal do Azure, reinicie o aplicativo no Portal.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-235">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="b9ce1-236">Outras opções de configuração</span><span class="sxs-lookup"><span data-stu-id="b9ce1-236">Other configuration options</span></span>

<span data-ttu-id="b9ce1-237">O suporte para a autorização da API se baseia no IdentityServer com um conjunto de convenções, valores padrão e aprimoramentos para simplificar a experiência do SPAs.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-237">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="b9ce1-238">Não é necessário dizer que todo o poder do IdentityServer está disponível nos bastidores se as integrações ASP.NET Core não abrangem seu cenário.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-238">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="b9ce1-239">O suporte a ASP.NET Core concentra-se em aplicativos de "primeira parte", onde todos os aplicativos são criados e implantados pela nossa organização.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-239">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="b9ce1-240">Dessa forma, o suporte não é oferecido para coisas como autorização ou Federação.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-240">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="b9ce1-241">Para esses cenários, use o IdentityServer e siga sua documentação.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-241">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="b9ce1-242">Perfis de aplicativo</span><span class="sxs-lookup"><span data-stu-id="b9ce1-242">Application profiles</span></span>

<span data-ttu-id="b9ce1-243">Perfis de aplicativo são configurações predefinidas para aplicativos que definem ainda mais seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-243">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="b9ce1-244">Neste momento, há suporte para os seguintes perfis:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-244">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="b9ce1-245">`IdentityServerSPA`: Representa um SPA hospedado juntamente com IdentityServer como uma única unidade.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-245">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="b9ce1-246">O `redirect_uri` padrão é `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="b9ce1-246">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="b9ce1-247">O `post_logout_redirect_uri` padrão é `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="b9ce1-247">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="b9ce1-248">O conjunto de escopos inclui o `openid` , o `profile` e todos os escopos definidos para as APIs no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-248">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="b9ce1-249">O conjunto de tipos de resposta permitidos OIDC é `id_token token` ou cada um deles individualmente ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="b9ce1-249">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="b9ce1-250">O modo de resposta permitido é `fragment` .</span><span class="sxs-lookup"><span data-stu-id="b9ce1-250">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="b9ce1-251">`SPA`: Representa um SPA que não é hospedado com IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-251">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="b9ce1-252">O conjunto de escopos inclui o `openid` , o `profile` e todos os escopos definidos para as APIs no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-252">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="b9ce1-253">O conjunto de tipos de resposta permitidos OIDC é `id_token token` ou cada um deles individualmente ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="b9ce1-253">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="b9ce1-254">O modo de resposta permitido é `fragment` .</span><span class="sxs-lookup"><span data-stu-id="b9ce1-254">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="b9ce1-255">`IdentityServerJwt`: Representa uma API que é hospedada juntamente com IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-255">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="b9ce1-256">O aplicativo é configurado para ter um único escopo que usa como padrão o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-256">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="b9ce1-257">`API`: Representa uma API que não é hospedada com IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-257">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="b9ce1-258">O aplicativo é configurado para ter um único escopo que usa como padrão o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-258">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="b9ce1-259">Configuração através de AppSettings</span><span class="sxs-lookup"><span data-stu-id="b9ce1-259">Configuration through AppSettings</span></span>

<span data-ttu-id="b9ce1-260">Configure os aplicativos por meio do sistema de configuração adicionando-os à lista de `Clients` ou `Resources` .</span><span class="sxs-lookup"><span data-stu-id="b9ce1-260">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="b9ce1-261">Configure cada cliente `redirect_uri` e `post_logout_redirect_uri` propriedade, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-261">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="b9ce1-262">Ao configurar recursos, você pode configurar os escopos para o recurso, conforme mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="b9ce1-262">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="b9ce1-263">Configuração por meio de código</span><span class="sxs-lookup"><span data-stu-id="b9ce1-263">Configuration through code</span></span>

<span data-ttu-id="b9ce1-264">Você também pode configurar os clientes e os recursos por meio de código usando uma sobrecarga do `AddApiAuthorization` que executa uma ação para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="b9ce1-264">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="b9ce1-265">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b9ce1-265">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>

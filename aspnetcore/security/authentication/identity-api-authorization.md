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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 178f85df0d35027cddb4314f9dabe26af8483ce6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775668"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="80bcd-103">Autenticação e autorização para SPAs</span><span class="sxs-lookup"><span data-stu-id="80bcd-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="80bcd-104">O ASP.NET Core 3,0 ou posterior oferece autenticação em aplicativos de página única (SPAs) usando o suporte para autorização de API.</span><span class="sxs-lookup"><span data-stu-id="80bcd-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="80bcd-105">ASP.NET Core Identity para autenticação e armazenamento de usuários é combinado com o [IdentityServer](https://identityserver.io/) para implementar o Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="80bcd-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="80bcd-106">Um parâmetro de autenticação foi adicionado aos modelos de projeto **angular** e **reajam** que é semelhante ao parâmetro de autenticação nos modelos de projeto de **aplicativo Web (Model-View-Controller)** (MVCRazor ) e **aplicativo Web** (páginas).</span><span class="sxs-lookup"><span data-stu-id="80bcd-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="80bcd-107">Os valores de parâmetro permitidos são **None** e **individual**.</span><span class="sxs-lookup"><span data-stu-id="80bcd-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="80bcd-108">O modelo de projeto **reajam. js e Redux** não dá suporte ao parâmetro de autenticação no momento.</span><span class="sxs-lookup"><span data-stu-id="80bcd-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="80bcd-109">Criar um aplicativo com suporte à autorização de API</span><span class="sxs-lookup"><span data-stu-id="80bcd-109">Create an app with API authorization support</span></span>

<span data-ttu-id="80bcd-110">A autenticação e a autorização do usuário podem ser usadas com SPAs angular e reagir.</span><span class="sxs-lookup"><span data-stu-id="80bcd-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="80bcd-111">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="80bcd-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="80bcd-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="80bcd-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="80bcd-113">**Reagir**:</span><span class="sxs-lookup"><span data-stu-id="80bcd-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="80bcd-114">O comando anterior cria um aplicativo ASP.NET Core com um diretório *ClientApp* que contém o Spa.</span><span class="sxs-lookup"><span data-stu-id="80bcd-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="80bcd-115">Descrição geral dos componentes ASP.NET Core do aplicativo</span><span class="sxs-lookup"><span data-stu-id="80bcd-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="80bcd-116">As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído:</span><span class="sxs-lookup"><span data-stu-id="80bcd-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="80bcd-117">Classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="80bcd-117">Startup class</span></span>

<span data-ttu-id="80bcd-118">A `Startup` classe tem as seguintes adições:</span><span class="sxs-lookup"><span data-stu-id="80bcd-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="80bcd-119">Dentro do `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="80bcd-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * Identity<span data-ttu-id="80bcd-120">com a interface do usuário padrão:</span><span class="sxs-lookup"><span data-stu-id="80bcd-120"> with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="80bcd-121">IdentityServer com um método `AddApiAuthorization` auxiliar adicional que configura algumas convenções de ASP.NET Core padrão na parte superior do IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="80bcd-121">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="80bcd-122">Autenticação com um método `AddIdentityServerJwt` auxiliar adicional que configura o aplicativo para validar tokens JWT produzidos por IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="80bcd-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="80bcd-123">Dentro do `Startup.Configure` método:</span><span class="sxs-lookup"><span data-stu-id="80bcd-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="80bcd-124">O middleware de autenticação que é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:</span><span class="sxs-lookup"><span data-stu-id="80bcd-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="80bcd-125">O middleware IdentityServer que expõe os pontos de extremidade do Open ID Connect:</span><span class="sxs-lookup"><span data-stu-id="80bcd-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="80bcd-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="80bcd-126">AddApiAuthorization</span></span>

<span data-ttu-id="80bcd-127">Esse método auxiliar configura o IdentityServer para usar nossa configuração com suporte.</span><span class="sxs-lookup"><span data-stu-id="80bcd-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="80bcd-128">O IdentityServer é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="80bcd-129">Ao mesmo tempo, isso expõe uma complexidade desnecessária para os cenários mais comuns.</span><span class="sxs-lookup"><span data-stu-id="80bcd-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="80bcd-130">Consequentemente, um conjunto de convenções e opções de configuração é fornecido para você que são considerados um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="80bcd-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="80bcd-131">Depois que a autenticação precisar ser alterada, toda a potência do IdentityServer ainda estará disponível para personalizar a autenticação de acordo com suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="80bcd-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="80bcd-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="80bcd-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="80bcd-133">Esse método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="80bcd-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="80bcd-134">A política está configurada Identity para permitir que o manipule todas as solicitações roteadas para qualquer subcaminho no espaço de Identity URL "/Identity".</span><span class="sxs-lookup"><span data-stu-id="80bcd-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="80bcd-135">O `JwtBearerHandler` lida com todas as outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="80bcd-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="80bcd-136">Além disso, esse método registra `<<ApplicationName>>API` um recurso de API com IdentityServer com um escopo `<<ApplicationName>>API` padrão e configura o middleware de token de portador JWT para validar tokens emitidos por IdentityServer para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="80bcd-137">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="80bcd-137">WeatherForecastController</span></span>

<span data-ttu-id="80bcd-138">No arquivo *Controllers\WeatherForecastController.cs* , observe o `[Authorize]` atributo aplicado à classe que indica que o usuário precisa ser autorizado com base na política padrão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="80bcd-138">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="80bcd-139">A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que é configurado `AddIdentityServerJwt` até o esquema de política mencionado acima, tornando o `JwtBearerHandler` método auxiliar configurado por tal como o manipulador padrão para solicitações para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="80bcd-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="80bcd-140">ApplicationDbContext</span></span>

<span data-ttu-id="80bcd-141">No arquivo *Data\ApplicationDbContext.cs* , observe que o mesmo `DbContext` é usado em Identity com a exceção que ele estende `ApiAuthorizationDbContext` (uma classe mais derivada de `IdentityDbContext`) para incluir o esquema para IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="80bcd-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="80bcd-142">Para obter controle total do esquema de banco de dados, herde de uma Identity `DbContext` das classes disponíveis e configure o contexto para Identity incluir o esquema `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` chamando no `OnModelCreating` método.</span><span class="sxs-lookup"><span data-stu-id="80bcd-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="80bcd-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="80bcd-143">OidcConfigurationController</span></span>

<span data-ttu-id="80bcd-144">No arquivo *Controllers\OidcConfigurationController.cs* , observe o ponto de extremidade provisionado para atender aos parâmetros OIDC que o cliente precisa usar.</span><span class="sxs-lookup"><span data-stu-id="80bcd-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="80bcd-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="80bcd-145">appsettings.json</span></span>

<span data-ttu-id="80bcd-146">No arquivo *appSettings. JSON* da raiz do projeto, há uma nova `IdentityServer` seção que descreve a lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="80bcd-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="80bcd-147">No exemplo a seguir, há um único cliente.</span><span class="sxs-lookup"><span data-stu-id="80bcd-147">In the following example, there's a single client.</span></span> <span data-ttu-id="80bcd-148">O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o `ClientId` parâmetro OAuth.</span><span class="sxs-lookup"><span data-stu-id="80bcd-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="80bcd-149">O perfil indica o tipo de aplicativo que está sendo configurado.</span><span class="sxs-lookup"><span data-stu-id="80bcd-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="80bcd-150">Ele é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor.</span><span class="sxs-lookup"><span data-stu-id="80bcd-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="80bcd-151">Há vários perfis disponíveis, conforme explicado na seção [perfis de aplicativo](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="80bcd-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="80bcd-152">AppSettings. Development. JSON</span><span class="sxs-lookup"><span data-stu-id="80bcd-152">appsettings.Development.json</span></span>

<span data-ttu-id="80bcd-153">Em *appSettings. Arquivo Development. JSON* da raiz do projeto, há uma `IdentityServer` seção que descreve a chave usada para assinar tokens.</span><span class="sxs-lookup"><span data-stu-id="80bcd-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="80bcd-154">Ao implantar na produção, uma chave precisa ser provisionada e implantada juntamente com o aplicativo, conforme explicado na seção [implantar na produção](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="80bcd-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="80bcd-155">Descrição geral do aplicativo angular</span><span class="sxs-lookup"><span data-stu-id="80bcd-155">General description of the Angular app</span></span>

<span data-ttu-id="80bcd-156">O suporte de autorização de API e autenticação no modelo angular reside em seu próprio módulo angular no diretório *ClientApp\src\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="80bcd-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="80bcd-157">O módulo é composto pelos seguintes elementos:</span><span class="sxs-lookup"><span data-stu-id="80bcd-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="80bcd-158">3 componentes:</span><span class="sxs-lookup"><span data-stu-id="80bcd-158">3 components:</span></span>
  * <span data-ttu-id="80bcd-159">*login. Component. TS*: manipula o fluxo de logon do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="80bcd-160">*logout. Component. TS*: manipula o fluxo de logout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="80bcd-161">*login-menu. Component. TS*: um widget que exibe um dos seguintes conjuntos de links:</span><span class="sxs-lookup"><span data-stu-id="80bcd-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="80bcd-162">Os links de gerenciamento de perfil de usuário e logoff quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="80bcd-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="80bcd-163">Os links de registro e logon quando o usuário não está autenticado.</span><span class="sxs-lookup"><span data-stu-id="80bcd-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="80bcd-164">Uma proteção `AuthorizeGuard` de rota que pode ser adicionada a rotas e requer que um usuário seja autenticado antes de visitar a rota.</span><span class="sxs-lookup"><span data-stu-id="80bcd-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="80bcd-165">Um interceptador HTTP `AuthorizeInterceptor` que anexa o token de acesso a solicitações HTTP de saída direcionando a API quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="80bcd-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="80bcd-166">Um serviço `AuthorizeService` que manipula os detalhes de nível inferior do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="80bcd-167">Um módulo angular que define as rotas associadas às partes de autenticação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="80bcd-168">Ele expõe o componente de menu de logon, o Interceptor, a proteção e o serviço para consumo do restante do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="80bcd-169">Descrição geral do aplicativo reajam</span><span class="sxs-lookup"><span data-stu-id="80bcd-169">General description of the React app</span></span>

<span data-ttu-id="80bcd-170">O suporte para autenticação e autorização de API no modelo reagir reside no diretório *ClientApp\src\components\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="80bcd-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="80bcd-171">Ele é composto pelos seguintes elementos:</span><span class="sxs-lookup"><span data-stu-id="80bcd-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="80bcd-172">4 componentes:</span><span class="sxs-lookup"><span data-stu-id="80bcd-172">4 components:</span></span>
  * <span data-ttu-id="80bcd-173">*Login. js*: manipula o fluxo de logon do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="80bcd-174">*Logout. js*: manipula o fluxo de logout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="80bcd-175">*LoginMenu. js*: um widget que exibe um dos seguintes conjuntos de links:</span><span class="sxs-lookup"><span data-stu-id="80bcd-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="80bcd-176">Os links de gerenciamento de perfil de usuário e logoff quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="80bcd-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="80bcd-177">Os links de registro e logon quando o usuário não está autenticado.</span><span class="sxs-lookup"><span data-stu-id="80bcd-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="80bcd-178">*AuthorizeRoute. js*: um componente de rota que requer que um usuário seja autenticado antes de renderizar o componente `Component` indicado no parâmetro.</span><span class="sxs-lookup"><span data-stu-id="80bcd-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="80bcd-179">Uma instância `authService` exportada `AuthorizeService` da classe que manipula os detalhes de nível inferior do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="80bcd-180">Agora que você já viu os principais componentes da solução, pode fazer uma análise mais profunda de cenários individuais para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="80bcd-181">Exigir autorização em uma nova API</span><span class="sxs-lookup"><span data-stu-id="80bcd-181">Require authorization on a new API</span></span>

<span data-ttu-id="80bcd-182">Por padrão, o sistema está configurado para exigir facilmente a autorização para novas APIs.</span><span class="sxs-lookup"><span data-stu-id="80bcd-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="80bcd-183">Para fazer isso, crie um novo controlador e adicione o `[Authorize]` atributo à classe do controlador ou a qualquer ação dentro do controlador.</span><span class="sxs-lookup"><span data-stu-id="80bcd-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="80bcd-184">Personalizar o manipulador de autenticação de API</span><span class="sxs-lookup"><span data-stu-id="80bcd-184">Customize the API authentication handler</span></span>

<span data-ttu-id="80bcd-185">Para personalizar a configuração do manipulador JWT da API, configure sua <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instância:</span><span class="sxs-lookup"><span data-stu-id="80bcd-185">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

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

<span data-ttu-id="80bcd-186">O manipulador JWT da API gera eventos que permitem o controle sobre o processo de `JwtBearerEvents`autenticação usando.</span><span class="sxs-lookup"><span data-stu-id="80bcd-186">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="80bcd-187">Para fornecer suporte para autorização de API `AddIdentityServerJwt` , o registra seus próprios manipuladores de eventos.</span><span class="sxs-lookup"><span data-stu-id="80bcd-187">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="80bcd-188">Para personalizar o tratamento de um evento, empacote o manipulador de eventos existente com lógica adicional, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="80bcd-188">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="80bcd-189">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="80bcd-189">For example:</span></span>

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

<span data-ttu-id="80bcd-190">No código anterior, o manipulador `OnTokenValidated` de eventos é substituído por uma implementação personalizada.</span><span class="sxs-lookup"><span data-stu-id="80bcd-190">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="80bcd-191">Esta implementação:</span><span class="sxs-lookup"><span data-stu-id="80bcd-191">This implementation:</span></span>

1. <span data-ttu-id="80bcd-192">Chama a implementação original fornecida pelo suporte à autorização da API.</span><span class="sxs-lookup"><span data-stu-id="80bcd-192">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="80bcd-193">Execute sua própria lógica personalizada.</span><span class="sxs-lookup"><span data-stu-id="80bcd-193">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="80bcd-194">Proteger uma rota do lado do cliente (angular)</span><span class="sxs-lookup"><span data-stu-id="80bcd-194">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="80bcd-195">A proteção de uma rota do lado do cliente é feita pela adição da proteção de autorização à lista de proteções a serem executadas durante a configuração de uma rota.</span><span class="sxs-lookup"><span data-stu-id="80bcd-195">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="80bcd-196">Como exemplo, você pode ver como a `fetch-data` rota é configurada dentro do módulo angular do aplicativo principal:</span><span class="sxs-lookup"><span data-stu-id="80bcd-196">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="80bcd-197">É importante mencionar que a proteção de uma rota não protege o ponto de extremidade real (que ainda `[Authorize]` requer um atributo aplicado a ele), mas que apenas impede que o usuário navegue até a rota do lado do cliente quando não é autenticado.</span><span class="sxs-lookup"><span data-stu-id="80bcd-197">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="80bcd-198">Autenticar solicitações de API (angular)</span><span class="sxs-lookup"><span data-stu-id="80bcd-198">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="80bcd-199">A autenticação de solicitações para APIs hospedadas juntamente com o aplicativo é feita automaticamente pelo uso do interceptador de cliente HTTP definido pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-199">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="80bcd-200">Proteger uma rota do lado do cliente (reagir)</span><span class="sxs-lookup"><span data-stu-id="80bcd-200">Protect a client-side route (React)</span></span>

<span data-ttu-id="80bcd-201">Proteger uma rota do lado do cliente usando o `AuthorizeRoute` componente em vez do componente `Route` simples.</span><span class="sxs-lookup"><span data-stu-id="80bcd-201">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="80bcd-202">Por exemplo, observe como a `fetch-data` rota é configurada `App` dentro do componente:</span><span class="sxs-lookup"><span data-stu-id="80bcd-202">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="80bcd-203">Protegendo uma rota:</span><span class="sxs-lookup"><span data-stu-id="80bcd-203">Protecting a route:</span></span>

* <span data-ttu-id="80bcd-204">Não protege o ponto de extremidade real (que ainda `[Authorize]` requer um atributo aplicado a ele).</span><span class="sxs-lookup"><span data-stu-id="80bcd-204">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="80bcd-205">Impede que o usuário navegue para a rota determinada do lado do cliente quando ele não é autenticado.</span><span class="sxs-lookup"><span data-stu-id="80bcd-205">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="80bcd-206">Autenticar solicitações de API (reagir)</span><span class="sxs-lookup"><span data-stu-id="80bcd-206">Authenticate API requests (React)</span></span>

<span data-ttu-id="80bcd-207">A autenticação de solicitações com reagir é feita pela primeira `authService` importação da instância `AuthorizeService`do.</span><span class="sxs-lookup"><span data-stu-id="80bcd-207">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="80bcd-208">O token de acesso é recuperado do `authService` e é anexado à solicitação, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-208">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="80bcd-209">Em reajam de componentes, esse trabalho normalmente é feito `componentDidMount` no método de ciclo de vida ou como resultado de alguma interação do usuário.</span><span class="sxs-lookup"><span data-stu-id="80bcd-209">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="80bcd-210">Importar o authService para o componente</span><span class="sxs-lookup"><span data-stu-id="80bcd-210">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="80bcd-211">Recuperar e anexar o token de acesso à resposta</span><span class="sxs-lookup"><span data-stu-id="80bcd-211">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="80bcd-212">Implantar para a produção</span><span class="sxs-lookup"><span data-stu-id="80bcd-212">Deploy to production</span></span>

<span data-ttu-id="80bcd-213">Para implantar o aplicativo na produção, os seguintes recursos precisam ser provisionados:</span><span class="sxs-lookup"><span data-stu-id="80bcd-213">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="80bcd-214">Um banco de dados para Identity armazenar as contas de usuário e as concessões IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="80bcd-214">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="80bcd-215">Um certificado de produção a ser usado para tokens de assinatura.</span><span class="sxs-lookup"><span data-stu-id="80bcd-215">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="80bcd-216">Não há requisitos específicos para esse certificado; pode ser um certificado autoassinado ou um certificado provisionado por meio de uma autoridade de certificação.</span><span class="sxs-lookup"><span data-stu-id="80bcd-216">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="80bcd-217">Ele pode ser gerado por meio de ferramentas padrão, como o PowerShell ou o OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="80bcd-217">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="80bcd-218">Ele pode ser instalado no repositório de certificados nos computadores de destino ou implantado como um arquivo *. pfx* com uma senha forte.</span><span class="sxs-lookup"><span data-stu-id="80bcd-218">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="80bcd-219">Exemplo: implantar nos sites do Azure</span><span class="sxs-lookup"><span data-stu-id="80bcd-219">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="80bcd-220">Esta seção descreve a implantação do aplicativo nos sites do Azure usando um certificado armazenado no repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="80bcd-220">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="80bcd-221">Para modificar o aplicativo para carregar um certificado do repositório de certificados, o plano do serviço de aplicativo precisa estar em pelo menos a camada Standard quando você configura o em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="80bcd-221">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="80bcd-222">No arquivo *appSettings. JSON* do aplicativo, modifique a `IdentityServer` seção para incluir os detalhes da chave:</span><span class="sxs-lookup"><span data-stu-id="80bcd-222">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="80bcd-223">O nome do repositório representa o nome do repositório de certificados onde o certificado está armazenado.</span><span class="sxs-lookup"><span data-stu-id="80bcd-223">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="80bcd-224">Nesse caso, ele aponta para o repositório de usuários pessoais.</span><span class="sxs-lookup"><span data-stu-id="80bcd-224">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="80bcd-225">O local do repositório representa onde carregar o certificado (`CurrentUser` ou `LocalMachine`).</span><span class="sxs-lookup"><span data-stu-id="80bcd-225">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="80bcd-226">A propriedade Name no certificado corresponde ao assunto distinto do certificado.</span><span class="sxs-lookup"><span data-stu-id="80bcd-226">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="80bcd-227">Para implantar os sites do Azure, implante o aplicativo seguindo as etapas em [implantar o aplicativo no Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) para criar os recursos necessários do Azure e implantar o aplicativo para produção.</span><span class="sxs-lookup"><span data-stu-id="80bcd-227">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="80bcd-228">Depois de seguir as instruções anteriores, o aplicativo é implantado no Azure, mas ainda não está funcional.</span><span class="sxs-lookup"><span data-stu-id="80bcd-228">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="80bcd-229">O certificado usado pelo aplicativo ainda precisa ser configurado.</span><span class="sxs-lookup"><span data-stu-id="80bcd-229">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="80bcd-230">Localize a impressão digital do certificado a ser usado e siga as etapas descritas em [carregar seus certificados](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="80bcd-230">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="80bcd-231">Embora essas etapas mencionem o SSL, há uma seção de **certificados privados** no portal em que você pode carregar o certificado provisionado para usar com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-231">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="80bcd-232">Após essa etapa, reinicie o aplicativo e ele deverá ser funcional.</span><span class="sxs-lookup"><span data-stu-id="80bcd-232">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="80bcd-233">Outras opções de configuração</span><span class="sxs-lookup"><span data-stu-id="80bcd-233">Other configuration options</span></span>

<span data-ttu-id="80bcd-234">O suporte para a autorização da API se baseia no IdentityServer com um conjunto de convenções, valores padrão e aprimoramentos para simplificar a experiência do SPAs.</span><span class="sxs-lookup"><span data-stu-id="80bcd-234">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="80bcd-235">Não é necessário dizer que todo o poder do IdentityServer está disponível nos bastidores se as integrações ASP.NET Core não abrangem seu cenário.</span><span class="sxs-lookup"><span data-stu-id="80bcd-235">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="80bcd-236">O suporte a ASP.NET Core concentra-se em aplicativos de "primeira parte", onde todos os aplicativos são criados e implantados pela nossa organização.</span><span class="sxs-lookup"><span data-stu-id="80bcd-236">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="80bcd-237">Dessa forma, o suporte não é oferecido para coisas como autorização ou Federação.</span><span class="sxs-lookup"><span data-stu-id="80bcd-237">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="80bcd-238">Para esses cenários, use o IdentityServer e siga sua documentação.</span><span class="sxs-lookup"><span data-stu-id="80bcd-238">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="80bcd-239">Perfis de aplicativo</span><span class="sxs-lookup"><span data-stu-id="80bcd-239">Application profiles</span></span>

<span data-ttu-id="80bcd-240">Perfis de aplicativo são configurações predefinidas para aplicativos que definem ainda mais seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="80bcd-240">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="80bcd-241">Neste momento, há suporte para os seguintes perfis:</span><span class="sxs-lookup"><span data-stu-id="80bcd-241">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="80bcd-242">`IdentityServerSPA`: Representa um SPA hospedado juntamente com IdentityServer como uma única unidade.</span><span class="sxs-lookup"><span data-stu-id="80bcd-242">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="80bcd-243">O `redirect_uri` padrão é `/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="80bcd-243">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="80bcd-244">O `post_logout_redirect_uri` padrão é `/authentication/logout-callback`.</span><span class="sxs-lookup"><span data-stu-id="80bcd-244">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="80bcd-245">O conjunto de escopos inclui `openid`o `profile`, o e todos os escopos definidos para as APIs no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-245">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="80bcd-246">O conjunto de tipos de resposta permitidos OIDC `id_token token` é ou cada um deles individualmente`id_token`( `token`,).</span><span class="sxs-lookup"><span data-stu-id="80bcd-246">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="80bcd-247">O modo de resposta permitido `fragment`é.</span><span class="sxs-lookup"><span data-stu-id="80bcd-247">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="80bcd-248">`SPA`: Representa um SPA que não é hospedado com IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="80bcd-248">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="80bcd-249">O conjunto de escopos inclui `openid`o `profile`, o e todos os escopos definidos para as APIs no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-249">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="80bcd-250">O conjunto de tipos de resposta permitidos OIDC `id_token token` é ou cada um deles individualmente`id_token`( `token`,).</span><span class="sxs-lookup"><span data-stu-id="80bcd-250">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="80bcd-251">O modo de resposta permitido `fragment`é.</span><span class="sxs-lookup"><span data-stu-id="80bcd-251">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="80bcd-252">`IdentityServerJwt`: Representa uma API que é hospedada juntamente com IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="80bcd-252">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="80bcd-253">O aplicativo é configurado para ter um único escopo que usa como padrão o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-253">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="80bcd-254">`API`: Representa uma API que não é hospedada com IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="80bcd-254">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="80bcd-255">O aplicativo é configurado para ter um único escopo que usa como padrão o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80bcd-255">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="80bcd-256">Configuração através de AppSettings</span><span class="sxs-lookup"><span data-stu-id="80bcd-256">Configuration through AppSettings</span></span>

<span data-ttu-id="80bcd-257">Configure os aplicativos por meio do sistema de configuração adicionando-os à lista `Clients` de `Resources`ou.</span><span class="sxs-lookup"><span data-stu-id="80bcd-257">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="80bcd-258">Configure cada cliente `redirect_uri` e `post_logout_redirect_uri` Propriedade, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="80bcd-258">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="80bcd-259">Ao configurar recursos, você pode configurar os escopos para o recurso, conforme mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="80bcd-259">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="80bcd-260">Configuração por meio de código</span><span class="sxs-lookup"><span data-stu-id="80bcd-260">Configuration through code</span></span>

<span data-ttu-id="80bcd-261">Você também pode configurar os clientes e os recursos por meio de código usando `AddApiAuthorization` uma sobrecarga do que executa uma ação para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="80bcd-261">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="80bcd-262">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="80bcd-262">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>

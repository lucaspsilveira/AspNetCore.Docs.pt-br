---
title: Adicionar, baixar e excluir dados do usuário para Identity o em um projeto ASP.NET Core
author: rick-anderson
description: Saiba como adicionar dados de usuário personalizados ao Identity em um projeto ASP.NET Core. Excluir dados por GDPR.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/add-user-data
ms.openlocfilehash: e5c23cc49a52b8772a43853e9e953dd416d69f69
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408728"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Adicionar, baixar e excluir dados de usuário personalizados para Identity em um projeto ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Este artigo mostra como:

* Adicione dados de usuário personalizados a um aplicativo Web ASP.NET Core.
* Marque o modelo de dados de usuário personalizado com o <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> atributo para que ele seja automaticamente disponibilizado para download e exclusão. Tornar os dados capazes de ser baixados e excluídos ajuda a atender aos requisitos de [GDPR](xref:security/gdpr) .

O exemplo de projeto é criado a partir de um Razor aplicativo Web de páginas, mas as instruções são semelhantes para um aplicativo web ASP.NET Core MVC.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Criar um Razor aplicativo Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**. Nomeie o projeto **WebApp1** se você quiser que ele corresponda ao namespace do código de [exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Selecione **ASP.NET Core aplicativo Web** > **OK**
* Selecione **ASP.NET Core 3,0** na lista suspensa
* Selecionar **aplicativo Web** > **OK**
* Compile e execute o projeto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**. Nomeie o projeto **WebApp1** se você quiser que ele corresponda ao namespace do código de [exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Selecione **ASP.NET Core aplicativo Web** > **OK**
* Selecione **ASP.NET Core 2,2** na lista suspensa
* Selecionar **aplicativo Web** > **OK**
* Compile e execute o projeto.

::: moniker-end


# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Executar o Identity scaffolder

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar**  >  **novo item com Scaffold**.
* No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **Identity**  >  **Adicionar**.
* Na caixa de diálogo **Adicionar Identity ** , as seguintes opções:
  * Selecione o arquivo de layout existente *~/Pages/Shared/_Layout. cshtml*
  * Selecione os seguintes arquivos a serem substituídos:
    * **Conta/registro**
    * **Conta/gerenciamento/índice**
  * Selecione o **+** botão para criar uma nova **classe de contexto de dados**. Aceite o tipo (**WebApp1. Models. WebApp1Context** se o projeto for denominado **WebApp1**).
  * Selecione o **+** botão para criar uma nova **classe de usuário**. Aceite o tipo (**WebApp1User** se o projeto for nomeado **WebApp1**) > **Adicionar**.
* Selecione **Adicionar**.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você não tiver instalado o ASP.NET Core scaffolder anteriormente, instale-o agora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Adicione uma referência de pacote a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo de projeto (. csproj). Execute o seguinte comando no diretório do projeto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Execute o seguinte comando para listar as Identity Opções de scaffolder:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Na pasta do projeto, execute o Identity scaffolder:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Siga as instruções em [Migrations, UseAuthentication e layout](xref:security/authentication/scaffold-identity#efm) para executar as seguintes etapas:

* Crie uma migração e atualize o banco de dados.
* Adicione `UseAuthentication` a `Startup.Configure`.
* Adicione `<partial name="_LoginPartial" />` ao arquivo de layout.
* Teste o aplicativo:
  * Registrar um usuário
  * Selecione o novo nome de usuário (ao lado do link de **logout** ). Talvez seja necessário expandir a janela ou selecionar o ícone da barra de navegação para mostrar o nome de usuário e outros links.
  * Selecione a guia **dados pessoais** .
  * Selecione o botão **baixar** e examinou a *PersonalData.jsno* arquivo.
  * Teste o botão **excluir** , que exclui o usuário conectado.

## <a name="add-custom-user-data-to-the-identity-db"></a>Adicionar dados de usuário personalizados ao Identity BD

Atualize a `IdentityUser` classe derivada com propriedades personalizadas. Se você tiver nomeado o projeto WebApp1, o arquivo será chamado *áreas/ Identity /Data/WebApp1User.cs*. Atualize o arquivo com o seguinte código:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

As propriedades com o atributo [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) são:

* Excluído quando a página *areas/ Identity /pages/Account/Manage/DeletePersonalData.cshtml* Razor chama `UserManager.Delete` .
* Incluídos nos dados baixados pela página *áreas/ Identity /pages/Account/Manage/DownloadPersonalData.cshtml* Razor .

### <a name="update-the-accountmanageindexcshtml-page"></a>Atualizar a página conta/gerenciar/index. cshtml

Atualize o `InputModel` em *áreas/ Identity /pages/Account/Manage/index.cshtml.cs* com o seguinte código realçado:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Atualize as *áreas/ Identity /pages/Account/Manage/index.cshtml* com a seguinte marcação realçada:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Atualize as *áreas/ Identity /pages/Account/Manage/index.cshtml* com a seguinte marcação realçada:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Atualizar a página Account/Register. cshtml

Atualize o `InputModel` em *áreas/ Identity /pages/Account/Register.cshtml.cs* com o seguinte código realçado:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Atualize as *áreas/ Identity /pages/Account/Register.cshtml* com a seguinte marcação realçada:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Atualize as *áreas/ Identity /pages/Account/Register.cshtml* com a seguinte marcação realçada:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Compile o projeto.

### <a name="add-a-migration-for-the-custom-user-data"></a>Adicionar uma migração para os dados de usuário personalizados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

No **console do Gerenciador de pacotes**do Visual Studio:

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Testar criar, exibir, baixar, excluir dados de usuário personalizados

Teste o aplicativo:

* Registrar um novo usuário.
* Exiba os dados de usuário personalizados na `/Identity/Account/Manage` página.
* Baixe e exiba os dados pessoais dos usuários na `/Identity/Account/Manage/PersonalData` página.

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Adicionar declarações ao Identity usando IUserClaimsPrincipalFactory<ApplicationUser>

> [!NOTE]
> Esta seção não é uma extensão do tutorial anterior. Para aplicar as etapas a seguir ao aplicativo criado usando o tutorial, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/18797).

Declarações adicionais podem ser adicionadas a ASP.NET Core Identity usando a `IUserClaimsPrincipalFactory<T>` interface. Essa classe pode ser adicionada ao aplicativo no `Startup.ConfigureServices` método. Adicione a implementação personalizada da classe da seguinte maneira:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

O código de demonstração usa a `ApplicationUser` classe. Essa classe adiciona uma `IsAdmin` propriedade que é usada para adicionar a declaração adicional.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

O `AdditionalUserClaimsPrincipalFactory` implementa a `UserClaimsPrincipalFactory` interface. Uma nova declaração de função é adicionada ao `ClaimsPrincipal` .

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

A declaração adicional pode ser usada no aplicativo. Em uma Razor página, a `IAuthorizationService` instância pode ser usada para acessar o valor da declaração.

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```

---
title: Adicionar, baixar e excluir dados do usuário para identidade em um projeto ASP.NET Core
author: rick-anderson
description: Aprenda a adicionar dados personalizados do usuário à Identidade em um projeto ASP.NET Core. Excluir dados por GDPR.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501223"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Adicione, baixe e exclua dados personalizados do usuário para identidade em um projeto ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Este artigo mostra como:

* Adicione dados de usuário personalizados a um aplicativo web ASP.NET Core.
* Marque o modelo de <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> dados personalizados do usuário com o atributo para que ele esteja automaticamente disponível para download e exclusão. Tornar os dados capazes de serem baixados e excluídos ajuda a atender aos requisitos [do GDPR.](xref:security/gdpr)

A amostra do projeto é criada a partir de um aplicativo web Razor Pages, mas as instruções são semelhantes para um aplicativo web ASP.NET Core MVC.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Criar um aplicativo Web do Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**. Nomeie o projeto **WebApp1** se quiser corresponder ao namespace do código de amostra de [download.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)
* Selecione **ASP.NET ok do aplicativo** > web **principal**
* Selecione **ASP.NET Núcleo 3.0** no dropdown
* Selecione **o aplicativo da** > **Web OK**
* Compile e execute o projeto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**. Nomeie o projeto **WebApp1** se quiser corresponder ao namespace do código de amostra de [download.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)
* Selecione **ASP.NET ok do aplicativo** > web **principal**
* Selecione **ASP.NET Núcleo 2.2** na estada
* Selecione **o aplicativo da** > **Web OK**
* Compile e execute o projeto.

::: moniker-end


# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Execute a pasta de identidade

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* A partir **do Solution Explorer**, clique com o botão direito do mouse no projeto > **adicionar** > **novo item de andaime**.
* No painel esquerdo da caixa de diálogo **Adicionar andaime,** selecione **'Adicionar''** > **Add**
* Na **caixa de diálogo Adicionar identidade,** as seguintes opções:
  * Selecione o arquivo de layout existente *~/Pages/Shared/_Layout.cshtml*
  * Selecione os seguintes arquivos a serem supervisionados:
    * **Conta/Registro**
    * **Conta/Gerenciar/Índice**
  * Selecione **+** o botão para criar uma nova **classe de contexto de dados**. Aceite o tipo (**WebApp1.Models.WebApp1Context** se o projeto for chamado **WebApp1**).
  * Selecione **+** o botão para criar uma nova **classe de usuário**. Aceite o tipo **(WebApp1User** se o projeto for chamado **WebApp1)**> **Adicionar**.
* Selecione **Adicionar**.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você não tiver instalado anteriormente a pasta de scaffolder ASP.NET Core, instale-a agora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Adicione uma referência de pacote ao [arquivo Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo project (.csproj). Execute o seguinte comando no diretório do projeto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Execute o seguinte comando para listar as opções de pasta de scaffolder identidade:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Na pasta do projeto, execute a pasta identidade:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Siga as instruções em [Migrações, UseAutenticação e layout](xref:security/authentication/scaffold-identity#efm) para executar as seguintes etapas:

* Crie uma migração e atualize o banco de dados.
* Adicione `UseAuthentication` a `Startup.Configure`.
* Adicione `<partial name="_LoginPartial" />` ao arquivo de layout.
* Teste o aplicativo:
  * Registrar um usuário
  * Selecione o novo nome de usuário (ao lado do link **Logout).** Você pode precisar expandir a janela ou selecionar o ícone da barra de navegação para mostrar o nome do usuário e outros links.
  * Selecione a guia **Dados Pessoais.**
  * Selecione o botão **Download** e examineo o arquivo *PersonalData.json.*
  * Teste o botão **Excluir,** que exclui o logado no usuário.

## <a name="add-custom-user-data-to-the-identity-db"></a>Adicionar dados de usuário personalizados ao DB de identidade

Atualize `IdentityUser` a classe derivada com propriedades personalizadas. Se você nomeou o projeto WebApp1, o arquivo será chamado *de Áreas/Identidade/Dados/WebApp1User.cs*. Atualize o arquivo com o seguinte código:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

As propriedades com o atributo [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) são:

* Excluído quando as *áreas/identidade/páginas/conta/gerenciar/excluirpersonalData.cshtml* Razor Page `UserManager.Delete`chamadas .
* Incluído nos dados baixados pelas *Áreas/Identidade/Páginas/Conta/Gerenciar/DownloadPersonalData.cshtml* Razor Page.

### <a name="update-the-accountmanageindexcshtml-page"></a>Atualize a página Conta/Gerenciar/Index.cshtml

Atualize `InputModel` o in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* com o seguinte código destacado:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Atualize as *áreas/identidade/páginas/conta/gerenciar/índice.cshtml* com a seguinte marcação destacada:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Atualize as *áreas/identidade/páginas/conta/gerenciar/índice.cshtml* com a seguinte marcação destacada:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Atualize a página Conta/Register.cshtml

Atualize `InputModel` o in *Areas/Identity/Pages/Account/Register.cshtml.cs* com o seguinte código destacado:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Atualize as *áreas/identidade/páginas/conta/register.cshtml* com a seguinte marcação destacada:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Atualize as *áreas/identidade/páginas/conta/register.cshtml* com a seguinte marcação destacada:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Compile o projeto.

### <a name="add-a-migration-for-the-custom-user-data"></a>Adicionar uma migração para os dados personalizados do usuário

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

No visual studio **package manager console**:

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

## <a name="test-create-view-download-delete-custom-user-data"></a>Teste criar, visualizar, baixar, excluir dados personalizados do usuário

Teste o aplicativo:

* Registre um novo usuário.
* Veja os dados personalizados do usuário na `/Identity/Account/Manage` página.
* Baixe e visualize os `/Identity/Account/Manage/PersonalData` dados pessoais dos usuários a partir da página.

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Adicionar reivindicações à identidade usando IUserClaimsPrincipalFactory<ApplicationUser>

Reivindicações adicionais podem ser adicionadas `IUserClaimsPrincipalFactory<T>` ao ASP.NET Identidade Central usando a interface. Esta classe pode ser adicionada `Startup.ConfigureServices` ao aplicativo no método. Adicione a implementação personalizada da classe da seguinte forma:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

O código de `ApplicationUser` demonstração usa a classe. Esta classe `IsAdmin` adiciona uma propriedade que é usada para adicionar a reivindicação adicional.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

O `AdditionalUserClaimsPrincipalFactory` implemento `UserClaimsPrincipalFactory` da interface. Uma nova reivindicação de `ClaimsPrincipal`função é adicionada ao .

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

A reivindicação adicional pode então ser usada no aplicativo. Em uma Página `IAuthorizationService` de Navalha, a instância pode ser usada para acessar o valor da solicitação.

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

---
title: Migrar autenticação e Identity para ASP.NET Core
author: ardalis
description: Saiba como migrar a autenticação e a identidade de um projeto MVC do ASP.NET para um projeto ASP.NET Core MVC.
ms.author: riande
ms.date: 3/22/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/identity
ms.openlocfilehash: 995de894bc77c4db5e5683b36e691b0c5a3463d3
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403749"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a>Migrar autenticação e Identity para ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

No artigo anterior, [migramos a configuração de um projeto MVC ASP.net para ASP.NET Core MVC](xref:migration/configuration). Neste artigo, migramos os recursos de registro, logon e gerenciamento de usuário.

## <a name="configure-identity-and-membership"></a>Configurar Identity e associar

No ASP.NET MVC, os recursos de autenticação e identidade são configurados usando ASP.NET Identity no *Startup.auth.cs* e no *IdentityConfig.cs*, localizados na pasta *App_Start* . No ASP.NET Core MVC, esses recursos são configurados em *Startup.cs*.

Instale os seguintes pacotes NuGet:

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.Cookies`
* `Microsoft.EntityFrameworkCore.SqlServer`

No *Startup.cs*, atualize o `Startup.ConfigureServices` método para usar Entity Framework e Identity Serviços:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

Neste ponto, há dois tipos referenciados no código acima que ainda não migramos do projeto MVC do ASP.NET: `ApplicationDbContext` e `ApplicationUser` . Crie uma nova pasta *modelos* no projeto ASP.NET Core e adicione duas classes a ela correspondente a esses tipos. Você encontrará as versões do ASP.NET MVC dessas classes no */Models/IdentityModels.cs*, mas usaremos um arquivo por classe no projeto migrado, pois isso é mais claro.

*ApplicationUser.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

O projeto Web do ASP.NET Core do MVC inicial não inclui muita personalização de usuários ou o `ApplicationDbContext` . Ao migrar um aplicativo real, você também precisa migrar todas as propriedades e métodos personalizados do usuário e das classes do seu aplicativo `DbContext` , bem como quaisquer outras classes de modelo que seu aplicativo utiliza. Por exemplo, se o `DbContext` tiver um `DbSet<Album>` , você precisará migrar a `Album` classe.

Com esses arquivos em vigor, o arquivo *Startup.cs* pode ser feito para compilar atualizando suas `using` instruções:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

Agora, nosso aplicativo está pronto para dar suporte à autenticação e aos Identity serviços. Ele só precisa ter esses recursos expostos aos usuários.

## <a name="migrate-registration-and-login-logic"></a>Migrar o registro e a lógica de logon

Com Identity os serviços configurados para o aplicativo e acesso a dados configurados usando Entity Framework e SQL Server, estamos prontos para adicionar suporte para registro e logon no aplicativo. Lembre-se de que, [anteriormente no processo de migração](xref:migration/mvc#migrate-the-layout-file) , comentamos uma referência a *_LoginPartial* em *_Layout. cshtml*. Agora é hora de retornar a esse código, remover os comentários e adicionar os controladores e exibições necessários para dar suporte à funcionalidade de logon.

Remova a marca de comentário da `@Html.Partial` linha em *_Layout. cshtml*:

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

Agora, adicione uma nova Razor exibição chamada *_LoginPartial* à pasta *views/Shared* :

Atualize *_LoginPartial. cshtml* com o seguinte código (substitua todo o seu conteúdo):

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

Neste ponto, você deve ser capaz de atualizar o site no seu navegador.

## <a name="summary"></a>Resumo

ASP.NET Core introduz alterações nos recursos do ASP.NET Identity . Neste artigo, você viu como migrar os recursos de autenticação e gerenciamento de usuários do ASP.NET Identity para o ASP.NET Core.

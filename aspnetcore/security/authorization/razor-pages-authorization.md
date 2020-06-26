---
title: RazorPáginas convenções de autorização no ASP.NET Core
author: rick-anderson
description: Saiba como controlar o acesso a páginas com convenções que autorizam usuários e permitem que usuários anônimos acessem páginas ou pastas de páginas.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 0492dd3d9b2aee7e844e944bea96259c3ddf18d0
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408715"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>RazorPáginas convenções de autorização no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Uma maneira de controlar o acesso em seu Razor aplicativo de páginas é usar as convenções de autorização na inicialização. Essas convenções permitem autorizar usuários e permitir que usuários anônimos acessem páginas ou pastas individuais de páginas. As convenções descritas neste tópico aplicam automaticamente [filtros de autorização](xref:mvc/controllers/filters#authorization-filters) para controlar o acesso.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([como baixar](xref:index#how-to-download-a-sample))

O aplicativo de exemplo usa a [autenticação de Identity cookie sem ASP.NET Core ](xref:security/authentication/cookie). Os conceitos e exemplos mostrados neste tópico se aplicam igualmente a aplicativos que usam ASP.NET Core Identity . Para usar ASP.NET Core Identity , siga as orientações em <xref:security/authentication/identity> .

## <a name="require-authorization-to-access-a-page"></a>Exigir autorização para acessar uma página

Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar uma <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à página no caminho especificado:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz de páginas sem uma extensão e que contém apenas barras.

Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> Um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pode ser aplicado a uma classe de modelo de página com o `[Authorize]` atributo Filter. Para obter mais informações, consulte [autorizar atributo de filtro](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Exigir autorização para acessar uma pasta de páginas

Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a todas as páginas em uma pasta no caminho especificado:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz das páginas.

Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Exigir autorização para acessar uma página de área

Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar uma <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à página de área no caminho especificado:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

O nome da página é o caminho do arquivo sem uma extensão relativa ao diretório raiz de páginas para a área especificada. Por exemplo, o nome da página para o arquivo *areas/ Identity /pages/Manage/accounts.cshtml* é */Manage/accounts*.

Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Exigir autorização para acessar uma pasta de áreas

Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a todas as áreas em uma pasta no caminho especificado:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

O caminho da pasta é o caminho da pasta em relação ao diretório raiz de páginas da área especificada. Por exemplo, o caminho da pasta para os arquivos em *áreas/ Identity /pages/Manage/* é */Manage*.

Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Permitir acesso anônimo a uma página

Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a uma página no caminho especificado:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz de páginas sem uma extensão e que contém apenas barras.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Permitir acesso anônimo a uma pasta de páginas

Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a todas as páginas em uma pasta no caminho especificado:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz das páginas.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Observação sobre a combinação de acesso autorizado e anônimo

É válido especificar que uma pasta de páginas requer autorização e, em seguida, especificar que uma página dentro dessa pasta permite o acesso anônimo:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

O inverso, no entanto, não é válido. Você não pode declarar uma pasta de páginas para acesso anônimo e, em seguida, especificar uma página dentro dessa pasta que requer autorização:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

A solicitação de autorização na página privada falhará. Quando os <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> e <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> são aplicados à página, o <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> tem precedência e controla o acesso.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Uma maneira de controlar o acesso em seu Razor aplicativo de páginas é usar as convenções de autorização na inicialização. Essas convenções permitem autorizar usuários e permitir que usuários anônimos acessem páginas ou pastas individuais de páginas. As convenções descritas neste tópico aplicam automaticamente [filtros de autorização](xref:mvc/controllers/filters#authorization-filters) para controlar o acesso.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([como baixar](xref:index#how-to-download-a-sample))

O aplicativo de exemplo usa a [autenticação de Identity cookie sem ASP.NET Core ](xref:security/authentication/cookie). Os conceitos e exemplos mostrados neste tópico se aplicam igualmente a aplicativos que usam ASP.NET Core Identity . Para usar ASP.NET Core Identity , siga as orientações em <xref:security/authentication/identity> .

## <a name="require-authorization-to-access-a-page"></a>Exigir autorização para acessar uma página

Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar uma <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à página no caminho especificado:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz de páginas sem uma extensão e que contém apenas barras.

Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> Um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pode ser aplicado a uma classe de modelo de página com o `[Authorize]` atributo Filter. Para obter mais informações, consulte [autorizar atributo de filtro](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Exigir autorização para acessar uma pasta de páginas

Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a todas as páginas em uma pasta no caminho especificado:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz das páginas.

Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Exigir autorização para acessar uma página de área

Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar uma <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à página de área no caminho especificado:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

O nome da página é o caminho do arquivo sem uma extensão relativa ao diretório raiz de páginas para a área especificada. Por exemplo, o nome da página para o arquivo *areas/ Identity /pages/Manage/accounts.cshtml* é */Manage/accounts*.

Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Exigir autorização para acessar uma pasta de áreas

Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a todas as áreas em uma pasta no caminho especificado:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

O caminho da pasta é o caminho da pasta em relação ao diretório raiz de páginas da área especificada. Por exemplo, o caminho da pasta para os arquivos em *áreas/ Identity /pages/Manage/* é */Manage*.

Para especificar uma [política de autorização](xref:security/authorization/policies), use uma [sobrecarga AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Permitir acesso anônimo a uma página

Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a uma página no caminho especificado:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz de páginas sem uma extensão e que contém apenas barras.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Permitir acesso anônimo a uma pasta de páginas

Use a <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a todas as páginas em uma pasta no caminho especificado:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

O caminho especificado é o caminho do mecanismo de exibição, que é o Razor caminho relativo raiz das páginas.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Observação sobre a combinação de acesso autorizado e anônimo

É válido especificar que uma pasta de páginas que requer autorização e o que especifica que uma página dentro dessa pasta permite o acesso anônimo:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

O inverso, no entanto, não é válido. Você não pode declarar uma pasta de páginas para acesso anônimo e, em seguida, especificar uma página dentro dessa pasta que requer autorização:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

A solicitação de autorização na página privada falhará. Quando os <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> e <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> são aplicados à página, o <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> tem precedência e controla o acesso.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

---
title: Ambientes de ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre ambientes no Blazor , incluindo como definir o ambiente de um Blazor aplicativo Webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/environments
ms.openlocfilehash: 203f29ce606a313463e416b068177ce02acd6231
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103549"
---
# <a name="aspnet-core-blazor-environments"></a>Ambientes de ASP.NET Core Blazor

> [!NOTE]
> Este tópico aplica-se ao Blazor Webassembly. Para obter diretrizes gerais sobre a configuração do aplicativo ASP.NET Core, consulte <xref:fundamentals/environments> .

Ao executar um aplicativo localmente, o ambiente assume como padrão o desenvolvimento. Quando o aplicativo é publicado, o ambiente assume como padrão a produção.

Um Blazor aplicativo Webassembly hospedado pega o ambiente do servidor por meio de um middleware que comunica o ambiente ao navegador adicionando o `blazor-environment` cabeçalho. O valor do cabeçalho é o ambiente. O Blazor aplicativo hospedado e o aplicativo de servidor compartilham o mesmo ambiente. Para obter mais informações, incluindo como configurar o ambiente, consulte <xref:fundamentals/environments> .

Para um aplicativo autônomo em execução localmente, o servidor de desenvolvimento adiciona o `blazor-environment` cabeçalho para especificar o ambiente de desenvolvimento. Para especificar o ambiente para outros ambientes de hospedagem, adicione o `blazor-environment` cabeçalho.

No exemplo a seguir para o IIS, adicione o cabeçalho personalizado ao arquivo de *web.config* publicado. O arquivo de *web.config* está localizado na pasta */versão/{Target Framework}/Publish* :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> Para usar um arquivo de *web.config* personalizado para o IIS que não é substituído quando o aplicativo é publicado na pasta de *publicação* , consulte <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .

Obtenha o ambiente do aplicativo em um componente injetando <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> e lendo a <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> Propriedade:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Durante a inicialização, o <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expõe o <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> por meio da <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> propriedade, o que permite aos desenvolvedores ter lógica específica do ambiente em seu código:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Os métodos de extensão de conveniência a seguir permitem verificar o ambiente atual quanto a desenvolvimento, produção, preparação e nomes de ambiente personalizados:

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

A <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> propriedade pode ser usada durante a inicialização quando o <xref:Microsoft.AspNetCore.Components.NavigationManager> serviço não está disponível.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/environments>

---
title: ASP.NET Core Blazor configuração do modelo de hospedagem
author: guardrex
description: Saiba mais Blazor sobre a configuração do modelo de hospedagem, Razor incluindo como Razor integrar componentes em páginas e aplicativos MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 17ed43a12643f067da73658bec72400acbe1be43
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772068"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core configuração de modelo de hospedagem mais incrivelmente

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Este artigo aborda a configuração do modelo de hospedagem.

## <a name="blazor-webassembly"></a>WebAssembly Blazor

### <a name="environment"></a>Ambiente

Ao executar um aplicativo localmente, o ambiente assume como padrão o desenvolvimento. Quando o aplicativo é publicado, o ambiente assume como padrão a produção.

Um aplicativo Webassembly de mais de um e-app hospedado escolhe o ambiente do servidor por meio de um middleware que comunica o ambiente ao navegador adicionando o `blazor-environment` cabeçalho. O valor do cabeçalho é o ambiente. O aplicativo e o aplicativo de servidor hospedados compartilham o mesmo ambiente. Para obter mais informações, incluindo como configurar o ambiente, consulte <xref:fundamentals/environments>.

Para um aplicativo autônomo em execução localmente, o servidor de desenvolvimento `blazor-environment` adiciona o cabeçalho para especificar o ambiente de desenvolvimento. Para especificar o ambiente para outros ambientes de hospedagem, adicione `blazor-environment` o cabeçalho.

No exemplo a seguir para o IIS, adicione o cabeçalho personalizado ao arquivo *Web. config* publicado. O arquivo *Web. config* está localizado na pasta */versão/{Target Framework}/Publish* :

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
> Para usar um arquivo *Web. config* personalizado para o IIS que não é substituído quando o aplicativo é publicado na pasta de *publicação* , <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>consulte.

Obtenha o ambiente do aplicativo em um componente injetando `IWebAssemblyHostEnvironment` e lendo a `Environment` Propriedade:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Durante a inicialização, `WebAssemblyHostBuilder` o expõe `IWebAssemblyHostEnvironment` o por `HostEnvironment` meio da propriedade, o que permite aos desenvolvedores ter lógica específica do ambiente em seu código:

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
* ' Isenvironment ("{nome do ambiente}")

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

A `IWebAssemblyHostEnvironment.BaseAddress` propriedade pode ser usada durante a inicialização quando `NavigationManager` o serviço não está disponível.

### <a name="configuration"></a>Configuração

Webassembly mais incrivelmente carrega a configuração de:

* Arquivos de configurações do aplicativo por padrão:
  * *wwwroot/appSettings. JSON*
  * *wwwroot/appSettings. {ENVIRONMENT}. JSON*
* Outros [provedores de configuração](xref:fundamentals/configuration/index) registrados pelo aplicativo. Nem todos os provedores são apropriados para aplicativos Webassembly mais poprestados. O esclarecimento sobre quais provedores têm suporte para Webassembly mais claro é acompanhado pelos [provedores de configuração do Clarify para o mais WASM (dotNet/AspNetCore. Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).

> [!WARNING]
> A configuração em um aplicativo Webassembly mais incrivelmente é visível para os usuários. **Não armazene os segredos ou as credenciais do aplicativo na configuração.**

Para obter mais informações sobre provedores de configuração <xref:fundamentals/configuration/index>, consulte.

#### <a name="app-settings-configuration"></a>Configuração de configurações do aplicativo

*wwwroot/appSettings. JSON*:

```json
{
  "message": "Hello from config!"
}
```

Injetar <xref:Microsoft.Extensions.Configuration.IConfiguration> uma instância em um componente para acessar os dados de configuração:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a>Configuração do provedor

O exemplo a seguir usa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> um para fornecer configuração adicional:

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

Injetar <xref:Microsoft.Extensions.Configuration.IConfiguration> uma instância em um componente para acessar os dados de configuração:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

Para ler outros arquivos de configuração da pasta *wwwroot* para a configuração, use `HttpClient` um para obter o conteúdo do arquivo. Ao usar essa abordagem, o registro `HttpClient` de serviço existente pode usar o cliente local criado para ler o arquivo, como mostra o exemplo a seguir:

*wwwroot/Cars. JSON*:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a>Configuração de autenticação

*wwwroot/appSettings. JSON*:

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a>Configuração de log

*wwwroot/appSettings. JSON*:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

`Program.Main`:

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>Configuração do host Builder

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>Configuração armazenada em cache

Os arquivos de configuração são armazenados em cache para uso offline. Com [PWAs (aplicativos Web progressivos)](xref:blazor/progressive-web-app), você só pode atualizar arquivos de configuração ao criar uma nova implantação. A edição de arquivos de configuração entre implantações não tem efeito porque:

* Os usuários têm versões em cache dos arquivos que eles continuam a usar.
* Os arquivos *Service-Worker. js* e *Service-Worker-assets. js* do PWA devem ser recriados na compilação, que sinaliza ao aplicativo no próximo online do usuário que o aplicativo foi reimplantado.

Para obter mais informações sobre como as atualizações em segundo plano são manipuladas pelo PWAs, consulte <xref:blazor/progressive-web-app#background-updates>.

### <a name="logging"></a>Registrando em log

Para obter informações sobre o suporte ao log de Webassembly <xref:fundamentals/logging/index#create-logs-in-blazor>mais incrivelmente, consulte.

## <a name="blazor-server"></a>Servidor Blazor

### <a name="reflect-the-connection-state-in-the-ui"></a>Refletir o estado da conexão na interface do usuário

Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar. Se a reconexão falhar, o usuário receberá a opção de tentar novamente.

Para personalizar a interface do usuário, defina um elemento `id` com `components-reconnect-modal` um de `<body>` no da página do Razor *_Host. cshtml* :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.

| Classe CSS                       | Indicando&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Uma conexão perdida. O cliente está tentando se reconectar. Mostrar o modal. |
| `components-reconnect-hide`     | Uma conexão ativa é restabelecida com o servidor. Ocultar a janela restrita. |
| `components-reconnect-failed`   | Falha na reconexão, provavelmente devido a uma falha de rede. Para tentar a reconexão, `window.Blazor.reconnect()`chame. |
| `components-reconnect-rejected` | Reconexão rejeitada. O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido. Para recarregar o aplicativo, chame `location.reload()`. Esse estado de conexão pode resultar quando:<ul><li>Ocorre uma falha no circuito do lado do servidor.</li><li>O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário. As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</li><li>O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</li></ul> |

### <a name="render-mode"></a>Modo de renderização

Os aplicativos de servidor mais poseriais são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida. Isso é configurado na página Razor *_Host. cshtml* :

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`configura se o componente:

* É renderizado na página.
* É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.

| `RenderMode`        | Descrição |
| ------------------- | ----------- |
| `ServerPrerendered` | Renderiza o componente em HTML estático e inclui um marcador para um Blazor aplicativo de servidor. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar Blazor um aplicativo. |
| `Server`            | Renderiza um marcador para um Blazor aplicativo de servidor. A saída do componente não está incluída. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar Blazor um aplicativo. |
| `Static`            | Renderiza o componente em HTML estático. |

Não há suporte para a renderização de componentes de servidor de uma página HTML estática.

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Configurar o SignalR cliente para Blazor aplicativos de servidor

Às vezes, você precisa configurar o SignalR cliente usado por Blazor aplicativos de servidor. Por exemplo, talvez você queira configurar o SignalR registro em log no cliente para diagnosticar um problema de conexão.

Para configurar o SignalR cliente no arquivo *pages/_Host. cshtml* :

* Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.
* Chame `Blazor.start` e passe um objeto de configuração que especifica o SignalR Construtor.

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

### <a name="logging"></a>Registrando em log

Para obter informações Blazor sobre o suporte a logs <xref:fundamentals/logging/index#create-logs-in-blazor>de servidor, consulte.

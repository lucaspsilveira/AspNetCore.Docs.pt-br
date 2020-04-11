---
title: configuração Blazor do modelo de hospedagem ASP.NET Core
author: guardrex
description: Saiba Blazor mais sobre a configuração do modelo de hospedagem, incluindo como integrar componentes Razor em páginas de barbear e aplicativos MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: ca1b3ea9092640ca561b3fbe02ddce6f974c525e
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123381"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET configuração do modelo de hospedagem Core Blazor

Por [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Este artigo abrange a configuração do modelo de hospedagem.

## <a name="blazor-webassembly"></a>WebAssembly Blazor

### <a name="environment"></a>Ambiente

Ao executar um aplicativo localmente, o ambiente é padrão para o Desenvolvimento. Quando o aplicativo é publicado, o ambiente é padrão para Produção.

Um aplicativo WebAssembly hospedado pega o ambiente do servidor através de um middleware que `blazor-environment` comunica o ambiente ao navegador adicionando o cabeçalho. O valor do cabeçalho é o ambiente. O aplicativo Blazor hospedado e o aplicativo do servidor compartilham o mesmo ambiente. Para obter mais informações, incluindo como <xref:fundamentals/environments>configurar o ambiente, consulte .

Para um aplicativo autônomo em execução local, o servidor de desenvolvimento adiciona o `blazor-environment` cabeçalho para especificar o ambiente Desenvolvimento. Para especificar o ambiente para outros `blazor-environment` ambientes de hospedagem, adicione o cabeçalho.

No exemplo a seguir para IIS, adicione o cabeçalho personalizado ao arquivo *web.config* publicado. O arquivo *Web.config* está localizado na pasta *bin/Release/{TARGET FRAMEWORK}/publish:*

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
> Para usar um arquivo *web.config* personalizado para IIS que não seja substituído quando <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>o aplicativo for publicado na pasta *de publicação,* consulte .

Obtenha o ambiente do aplicativo em `IWebAssemblyHostEnvironment` um componente `Environment` injetando e lendo a propriedade:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

### <a name="configuration"></a>Configuração

A partir da versão ASP.NET Core 3.2 Preview 3, o Blazor WebAssembly suporta a configuração a partir de:

* *wwwroot/appsettings.json*
* *wwwroot/appsettings. {ENVIRONMENT}.json*

Adicionar um *arquivo appsettings.json* na pasta *wwwroot:*

```json
{
  "message": "Hello from config!"
}
```

Injete uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

> [!WARNING]
> A configuração em um aplicativo Blazor WebAssembly é visível para os usuários. **Não armazene segredos de aplicativos ou credenciais na configuração.**

Os arquivos de configuração são armazenados em cache para uso off-line. Com [pwAs (Progressive Web Applications, aplicativos da Web progressivos),](xref:blazor/progressive-web-app)você só pode atualizar arquivos de configuração ao criar uma nova implantação. Editar arquivos de configuração entre implantações não tem efeito porque:

* Os usuários têm versões armazenadas em cache dos arquivos que eles continuam a usar.
* Os arquivos *service-worker.js* e *service-worker-assets.js* do PWA devem ser reconstruídos na compilação, que sinaliza para o aplicativo na próxima visita on-line do usuário que o aplicativo foi reimplantado.

Para obter mais informações sobre como as atualizações <xref:blazor/progressive-web-app#background-updates>em segundo plano são tratadas pelos PWAs, consulte .

## <a name="blazor-server"></a>Servidor Blazor

### <a name="reflect-the-connection-state-in-the-ui"></a>Reflita o estado de conexão na UI

Quando o cliente detecta que a conexão foi perdida, uma ui padrão é exibida para o usuário enquanto o cliente tenta se reconectar. Se a reconexão falhar, o usuário tem a opção de tentar novamente.

Para personalizar a ia de `id` usuário, defina um elemento com um of `components-reconnect-modal` na `<body>` página *_Host.cshtml* Razor:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

A tabela a seguir descreve as classes `components-reconnect-modal` CSS aplicadas ao elemento.

| Classe CSS                       | Indica&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Uma conexão perdida. O cliente está tentando se reconectar. Mostre o modal. |
| `components-reconnect-hide`     | Uma conexão ativa é restabelecida ao servidor. Esconda o modal. |
| `components-reconnect-failed`   | A reconexão falhou, provavelmente devido a uma falha de rede. Para tentar reconectar, ligue . `window.Blazor.reconnect()` |
| `components-reconnect-rejected` | Reconexão rejeitada. O servidor foi alcançado, mas recusou a conexão, e o estado do usuário no servidor foi perdido. Para recarregar o `location.reload()`aplicativo, ligue . Esse estado de conexão pode resultar quando:<ul><li>Ocorre uma falha no circuito do lado do servidor.</li><li>O cliente está desconectado tempo suficiente para que o servidor abandone o estado do usuário. As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</li><li>O servidor é reiniciado ou o processo de trabalhador do aplicativo é reciclado.</li></ul> |

### <a name="render-mode"></a>Modo de renderização

Os aplicativos do Blazor Server são configurados por padrão para pré-renderizar a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida. Isso está configurado na página *_Host.cshtml* Razor:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`configura se o componente:

* É pré-renderizado na página.
* É renderizado como HTML estático na página ou se inclui as informações necessárias para bootstrap um aplicativo Blazor do agente usuário.

| `RenderMode`        | Descrição |
| ------------------- | ----------- |
| `ServerPrerendered` | Renderiza o componente em HTML estático e Blazor inclui um marcador para um aplicativo Server. Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo. |
| `Server`            | Renderiza um marcador Blazor para um aplicativo do Servidor. A saída do componente não está incluída. Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo. |
| `Static`            | Torna o componente em HTML estático. |

A renderização de componentes do servidor de uma página HTML estática não é suportada.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Renderizar componentes interativos imponentes a partir de páginas e visualizações de Navalha

Componentes interativos imponentes podem ser adicionados a uma página ou exibição de Navalha.

Quando a página ou exibição renderiza:

* O componente é pré-renderizado com a página ou exibição.
* O estado de componente inicial usado para pré-renderização é perdido.
* Novo estado componente é SignalR criado quando a conexão é estabelecida.

A página Navalha `Counter` a seguir renderiza um componente:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Renderizar componentes não interativos de páginas e visualizações de Navalha

Na página Navalha `Counter` a seguir, o componente é renderizado estáticamente com um valor inicial especificado usando um formulário:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Uma `MyComponent` vez que é renderizado estáticamente, o componente não pode ser interativo.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Configure SignalR o Blazor cliente para aplicativos do Servidor

Às vezes, você precisa SignalR configurar Blazor o cliente usado pelos aplicativos do Servidor. Por exemplo, você pode querer configurar SignalR o login no cliente para diagnosticar um problema de conexão.

Para configurar SignalR o cliente no arquivo *Páginas/_Host.cshtml:*

* Adicione `autostart="false"` um atributo `<script>` à `blazor.server.js` tag para o script.
* Ligue `Blazor.start` e passe em um objeto SignalR de configuração que especifica o construtor.

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

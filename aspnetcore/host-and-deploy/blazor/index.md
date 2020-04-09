---
title: Hospedar e implantar ASP.NET CoreBlazor
author: guardrex
description: Descubra como hospedar Blazor e implantar aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434259"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hospedar e implantar o ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Publicar o aplicativo

Os aplicativos são publicados para implantação na configuração de versão.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Selecione **Publicar** > **publicar {APLICATIVO}** na barra de navegação.
1. Selecione o botão *destino de publicação*. Para publicar localmente, selecione **Pasta**.
1. Aceite o local padrão no campo **Escolher uma pasta** ou especifique um local diferente. Clique no botão **Publicar**.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Use o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) para publicar o aplicativo com uma configuração de versão:

```dotnetcli
dotnet publish -c Release
```

---

Publicar o aplicativo dispara uma [restauração](/dotnet/core/tools/dotnet-restore) das dependências do projeto e [compila](/dotnet/core/tools/dotnet-build) o projeto antes de criar os ativos para implantação. Como parte do processo de build, os assemblies e métodos não usados são removidos para reduzir o tamanho de download do aplicativo e os tempos de carregamento.

Publicar locais:

* BlazorWebAssembly
  * Autônomo &ndash; O aplicativo é publicado na pasta */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot.* Para implantar o aplicativo como um site estático, copie o conteúdo da pasta *wwwroot* para o host do site estático.
  * Hospedado &ndash; O Blazor aplicativo WebAssembly do cliente é publicado na pasta */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* do aplicativo do servidor, juntamente com quaisquer outros ativos web estáticos do aplicativo do servidor. Implante o conteúdo da pasta *de publicação* no host.
* BlazorServidor &ndash; O aplicativo é publicado na pasta */bin/Release/{TARGET FRAMEWORK}/publish.* Implante o conteúdo da pasta *de publicação* no host.

Os ativos na pasta são implantados no servidor Web. A implantação pode ser um processo manual ou automatizado, dependendo das ferramentas de desenvolvimento em uso.

## <a name="app-base-path"></a>Caminho base do aplicativo

O *caminho base* do aplicativo é o caminho principal da URL do aplicativo. Considere o seguinte aplicativo Blazor e subaplicativo ASP.NET Core:

* O aplicativo ASP.NET `MyApp`Core é chamado:
  * O aplicativo reside fisicamente em *d:/MyApp*.
  * Solicitações são `https://www.contoso.com/{MYAPP RESOURCE}`recebidas em .
* Um Blazor aplicativo `CoolApp` chamado é um `MyApp`subaplicativo de:
  * O subaplicativo reside fisicamente em *d:/MyApp/CoolApp*.
  * Solicitações são `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`recebidas em .

Sem especificar `CoolApp`configuração adicional para , o subaplicativo neste cenário não tem conhecimento de onde reside no servidor. Por exemplo, o aplicativo não pode construir URLs relativos corretos em seus `/CoolApp/`recursos sem saber que ele reside no caminho relativo da URL .

Para fornecer configuração Blazor para o `https://www.contoso.com/CoolApp/`caminho `<base>` base do `href` aplicativo, o atributo da tag está definido comoBlazor o caminho raiz relativo no arquivo *Pages/_Host.cshtml* (Server) ou *wwwroot/index.html* (WebAssembly):Blazor

```html
<base href="/CoolApp/">
```

BlazorOs aplicativos do servidor também definem <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> o caminho base do `Startup.Configure`lado do servidor, chamando o pipeline de solicitação do aplicativo de:

```csharp
app.UsePathBase("/CoolApp");
```

Ao fornecer o caminho relativo da URL, um componente que não está no diretório raiz pode construir URLs em relação ao caminho raiz do aplicativo. Componentes em diferentes níveis da estrutura do diretório podem construir links para outros recursos em locais ao longo do aplicativo. O caminho base do aplicativo também é `href` usado para interceptar hiperlinks selecionados onde o alvo do link está dentro do espaço URI do caminho base do aplicativo. O Blazor roteador lida com a navegação interna.

Em muitos cenários de hospedagem, o caminho relativo da URL para o aplicativo é a raiz do aplicativo. Nesses casos, o caminho base relativo de URL`<base href="/" />`do aplicativo é uma Blazor barra para a frente ( ), que é a configuração padrão para um aplicativo. Em outros cenários de hospedagem, como páginas do GitHub e subaplicativos IIS, o caminho base do aplicativo deve ser definido para o caminho relativo de URL do aplicativo do servidor.

Para definir o caminho base do `<base>` aplicativo, `<head>` atualize a tag dentro dos elementos de tagBlazor do arquivo *Pages/_Host.cshtml* (Server)Blazor ou *wwwroot/index.html* (WebAssembly). Defina `href` o `/{RELATIVE URL PATH}/` valor do atributo para `{RELATIVE URL PATH}` (a barra de arrasto é necessária), onde está o caminho completo de URL relativo do aplicativo.

Para Blazor um aplicativo WebAssembly com um caminho de `<base href="/CoolApp/">`URL relativo não raiz (por exemplo, ), o aplicativo não consegue encontrar seus recursos *quando executado localmente*. Para superar esse problema durante o desenvolvimento e os testes locais, você pode fornecer um argumento *base de caminho* que corresponde ao valor de `href` da tag `<base>` no runtime. Não inclua uma barra de arrasto. Para passar o argumento base de caminho ao `dotnet run` executar o aplicativo localmente, `--pathbase` execute o comando do diretório do aplicativo com a opção:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Para Blazor um aplicativo WebAssembly com `/CoolApp/` um`<base href="/CoolApp/">`caminho de URL relativo de ( ), o comando é:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

O Blazor aplicativo WebAssembly responde `http://localhost:port/CoolApp`localmente em .

## <a name="deployment"></a>Implantação

Confira orientações de implantação nos tópicos a seguir:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>

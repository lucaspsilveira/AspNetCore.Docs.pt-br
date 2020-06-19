---
title: Hospedar e implantar ASP.NET CoreBlazor
author: guardrex
description: Descubra como hospedar e implantar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 3a3c5ab5365e5b4312dd3fd516f4906155911cc9
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103541"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hospedar e implantar ASP.NET CoreBlazor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publicar o aplicativo

Os aplicativos são publicados para implantação na configuração de versão.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Selecione **Compilar**  >  **publicar {Application}** na barra de navegação.
1. Selecione o botão *destino de publicação*. Para publicar localmente, selecione **Pasta**.
1. Aceite o local padrão no campo **Escolher uma pasta** ou especifique um local diferente. Clique no botão **Publicar**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Selecione **criar**  >  **publicar na pasta**.
1. Confirme a pasta para receber os ativos publicados e selecione **publicar**.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Use o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) para publicar o aplicativo com uma configuração de versão:

```dotnetcli
dotnet publish -c Release
```

---

Publicar o aplicativo dispara uma [restauração](/dotnet/core/tools/dotnet-restore) das dependências do projeto e [compila](/dotnet/core/tools/dotnet-build) o projeto antes de criar os ativos para implantação. Como parte do processo de build, os assemblies e métodos não usados são removidos para reduzir o tamanho de download do aplicativo e os tempos de carregamento.

Locais de publicação:

* BlazorWebassembly
  * Autônomo: o aplicativo é publicado na pasta */bin/Release/{Target Framework}/Publish/wwwroot* . Para implantar o aplicativo como um site estático, copie o conteúdo da pasta *wwwroot* para o host do site estático.
  * Hospedado: o Blazor aplicativo Webassembly do cliente é publicado na pasta */bin/Release/{Target Framework}/Publish/wwwroot* do aplicativo de servidor, juntamente com quaisquer outros ativos da Web estáticos do aplicativo do servidor. Implante o conteúdo da pasta de *publicação* no host.
* BlazorServidor: o aplicativo é publicado na pasta */Publish do/bin/Release/{Target Framework}* . Implante o conteúdo da pasta de *publicação* no host.

Os ativos na pasta são implantados no servidor Web. A implantação pode ser um processo manual ou automatizado, dependendo das ferramentas de desenvolvimento em uso.

## <a name="app-base-path"></a>Caminho base do aplicativo

O *caminho base do aplicativo* é o caminho da URL raiz do aplicativo. Considere o seguinte aplicativo ASP.NET Core e o Blazor subaplicativo:

* O aplicativo ASP.NET Core é denominado `MyApp` :
  * O aplicativo reside fisicamente em *d:/MyApp*.
  * As solicitações são recebidas em `https://www.contoso.com/{MYAPP RESOURCE}` .
* Um Blazor aplicativo chamado `CoolApp` é um subaplicativo de `MyApp` :
  * O subaplicativo reside fisicamente em *d:/MyApp/CoolApp*.
  * As solicitações são recebidas em `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .

Sem especificar a configuração adicional para `CoolApp` o, o subaplicativo nesse cenário não tem conhecimento de onde ele reside no servidor. Por exemplo, o aplicativo não pode construir URLs relativas corretas para seus recursos sem saber que ela reside no caminho de URL relativo `/CoolApp/` .

Para fornecer a configuração para o Blazor caminho base do aplicativo `https://www.contoso.com/CoolApp/` , o `<base>` atributo da marca `href` é definido como o caminho raiz relativo no arquivo *pages/_Host. cshtml* ( Blazor servidor) ou *wwwroot/index.html* ( Blazor Webassembly):

```html
<base href="/CoolApp/">
```

BlazorOs aplicativos de servidor também definem o caminho base do servidor chamando <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> no pipeline de solicitação do aplicativo de `Startup.Configure` :

```csharp
app.UsePathBase("/CoolApp");
```

Ao fornecer o caminho de URL relativo, um componente que não está no diretório raiz pode construir URLs relativas ao caminho raiz do aplicativo. Os componentes em diferentes níveis da estrutura de diretório podem criar links para outros recursos em locais em todo o aplicativo. O caminho base do aplicativo também é usado para interceptar hiperlinks selecionados onde o `href` destino do link está dentro do espaço de URI do caminho base do aplicativo. O Blazor roteador manipula a navegação interna.

Em muitos cenários de hospedagem, o caminho de URL relativo para o aplicativo é a raiz do aplicativo. Nesses casos, o caminho base da URL relativa do aplicativo é uma barra ( `<base href="/" />` ), que é a configuração padrão para um Blazor aplicativo. Em outros cenários de hospedagem, como páginas do GitHub e subaplicativos do IIS, o caminho base do aplicativo deve ser definido como o caminho de URL relativo do servidor do aplicativo.

Para definir o caminho base do aplicativo, atualize a `<base>` marca dentro dos `<head>` elementos de marca do arquivo *pages/_Host. cshtml* ( Blazor servidor) ou *wwwroot/index.html* ( Blazor Webassembly). Defina o `href` valor do atributo como `/{RELATIVE URL PATH}/` (a barra à direita é necessária), em que `{RELATIVE URL PATH}` é o caminho de URL relativo completo do aplicativo.

Para um Blazor aplicativo Webassembly com um caminho de URL não raiz relativo (por exemplo, `<base href="/CoolApp/">` ), o aplicativo não consegue localizar seus recursos *quando executado localmente*. Para superar esse problema durante o desenvolvimento e os testes locais, você pode fornecer um argumento *base de caminho* que corresponde ao valor de `href` da tag `<base>` no runtime. Não inclua uma barra à direita. Para passar o argumento de base Path ao executar o aplicativo localmente, execute o `dotnet run` comando no diretório do aplicativo com a `--pathbase` opção:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Para um Blazor aplicativo Webassembly com um caminho de URL relativo de `/CoolApp/` ( `<base href="/CoolApp/">` ), o comando é:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

O Blazor aplicativo Webassembly responde localmente em `http://localhost:port/CoolApp` .

## <a name="deployment"></a>Implantação

Confira orientações de implantação nos tópicos a seguir:

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>

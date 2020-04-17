---
title: Debug ASP.NET Blazor WebAssembly Central
author: guardrex
description: Aprenda a depurar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 8b63444ba5c8cd45e64e722c8978ba4e6d90af36
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488742"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a>Debug ASP.NET Blazor WebAssembly Central

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorOs aplicativos WebAssembly podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (Edge/Chrome).  Alternativamente, você pode depurar seu aplicativo usando Visual Studio ou Visual Studio Code.

Os cenários disponíveis incluem:

* Defina e remova pontos de interrupção.
* Execute o aplicativo com suporte à depuração no Visual Studio e visual Studio Code ( suporte a<kbd>F5).</kbd>
* Passo único<kbd>(F10)</kbd>através do código.
* Retomar a execução de código com <kbd>F8</kbd> em um navegador ou <kbd>F5</kbd> no Visual Studio ou Visual Studio Code.
* No *visor local,* observe os valores das variáveis locais.
* Consulte a pilha de chamadas, incluindo cadeias de chamadas que vão de JavaScript para .NET e de .NET para JavaScript.

Por enquanto, você *não pode:*

* Inspecione as matrizes.
* Hover para inspecionar membros.
* Depurar para dentro ou para fora do código gerenciado.
* Tenha suporte completo para inspecionar tipos de valor.
* Quebre as exceções não tratadas.
* Aperte pontos de interrupção durante a inicialização do aplicativo.
* Depurar um aplicativo com um trabalhador de serviço.

Continuaremos a melhorar a experiência de depuração nos próximos lançamentos.

## <a name="prerequisites"></a>Pré-requisitos

A depuração requer qualquer um dos seguintes navegadores:

* Microsoft Edge (versão 80 ou posterior)
* Google Chrome (versão 70 ou posterior)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Habilite a depuração para visual studio e visual studio code

A depuração é habilitada automaticamente para novos projetos que são criados Blazor usando o ASP.NET o modelo de projeto Do Core 3.2 Preview 3 ou posterior do WebAssembly[(a versão atual é 3.2 Preview 4](xref:blazor/get-started)).

Para habilitar a depuração de um aplicativo WebAssembly existente, Blazor atualize o arquivo *launchSettings.json* no projeto de inicialização para incluir a seguinte `inspectUri` propriedade em cada perfil de lançamento:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Uma vez atualizado, o arquivo *launchSettings.json* deve ser semelhante ao seguinte exemplo:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

A `inspectUri` propriedade:

* Habilita o IDE para detectar Blazor que o aplicativo é um aplicativo WebAssembly.
* Instrui a infra-estrutura de depuração Blazorde scripts para se conectar ao navegador através do proxy de depuração.

## <a name="visual-studio"></a>Visual Studio

Para depurar um Blazor aplicativo WebAssembly no Visual Studio:

1. Certifique-se de ter [instalado a versão de pré-visualização mais recente do Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 ou posterior).
1. Crie um novo aplicativo Blazor webAssembly hospedado ASP.NET Core.
1. Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.
1. Defina um ponto de ruptura `IncrementCount` no *Counter.razor* no método.
1. Navegue até a guia **Contador** e selecione o botão para apertar o ponto de ruptura:

   ![Contador de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Confira o valor `currentCount` do campo na janela local:

   ![Ver moradores locais](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Pressione <kbd>F5</kbd> para continuar a execução.

Ao depurar Blazor seu aplicativo WebAssembly, você também pode depurar o código do servidor:

1. Defina um ponto de ruptura na `OnInitializedAsync`página *FetchData.razor* em .
1. Defina um ponto `WeatherForecastController` de `Get` ruptura no método de ação.
1. Navegue até a guia **Buscar dados** para `FetchData` atingir o primeiro ponto de ruptura no componente pouco antes de emitir uma solicitação HTTP para o servidor:

   ![Debug Buscar Dados](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Pressione <kbd>F5</kbd> para continuar a execução e, `WeatherForecastController`em seguida, clique no ponto de partida no servidor no :

   ![Servidor de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela de previsão do tempo renderizada.

## <a name="visual-studio-code"></a>Visual Studio Code

Para depurar um Blazor aplicativo WebAssembly no Visual Studio Code:
 
1. Instale a [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` set para `true`.

   ![Extensões](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Depurador de visualização JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Abra um Blazor aplicativo WebAssembly existente com a depuração ativada.

   * Se você receber a seguinte notificação de que a configuração adicional é necessária para ativar a depuração, confirme se você tem as extensões corretas instaladas e a depuração de visualização javaScript ativada e, em seguida, recarregue a janela:

     ![Configuração adicional requried](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Uma notificação oferece adicionar os ativos necessários ao aplicativo para construção e depuração. Selecione **Sim**:

     ![Adicionar ativos necessários](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Iniciar o aplicativo no depurador é um processo de duas etapas:

   1\. **Primeiro,** inicie o aplicativo usando a configuração de lançamento **doBlazor .NET Core Launch (Standalone).**

   2\. **Depois que o aplicativo tiver começado,** inicie o navegador usando o ** Blazor .NET Core Debug Web Assembly na** configuração de lançamento do Chrome (requer o Chrome). Para usar o Edge em `type` vez do Chrome, altere a configuração `pwa-chrome` `pwa-msedge`de lançamento em *.vscode/launch.json* de para .

1. Defina um ponto `IncrementCount` de `Counter` ruptura no método no componente e selecione o botão para apertar o ponto de partida:

   ![Contador de depuração em código VS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Depuração no navegador

1. Execute uma compilação de depuração do aplicativo no ambiente Desenvolvimento.

1. Pressione <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.

1. O navegador deve ser executado com depuração remota ativada. Se a depuração remota estiver desativada, uma página de erro de guia do navegador não será **depurada.** A página de erro contém instruções para executar o navegador Blazor com a porta de depuração aberta para que o proxy de depuração possa se conectar ao aplicativo. *Feche todas as instâncias do navegador* e reinicie o navegador conforme instruído.

Uma vez que o navegador esteja em execução com a depuração remota ativada, o atalho do teclado de depuração abre uma nova guia de depurador. Após um momento, a guia **Fontes** mostra uma lista dos conjuntos .NET no aplicativo. Expanda cada montagem e encontre os arquivos de origem *.cs*/*.razor* disponíveis para depuração. Defina pontos de interrupção, volte para a guia do aplicativo e os pontos de interrupção são atingidos quando o código é executado. Após um ponto de quebra é atingido, passo único<kbd>(F10</kbd>) através do código ou currículo<kbd>(F8)</kbd>execução do código normalmente.

Blazorfornece um proxy de depuração que implementa o [Protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com . Informações específicas da NET. Quando o atalho do teclado Blazor depurar é pressionado, aponte o Chrome DevTools para o proxy. O proxy se conecta à janela do navegador que você está procurando depurar (daí a necessidade de ativar a depuração remota).

## <a name="browser-source-maps"></a>Mapas de origem do navegador

Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta aos seus arquivos de origem originais e são comumente usados para depuração do lado do cliente. No Blazor entanto, atualmente não mapeia C# diretamente para JavaScript/WASM. Em Blazor vez disso, a interpretação il dentro do navegador, de modo que os mapas de origem não são relevantes.

## <a name="troubleshoot"></a>Solucionar problemas

Se você estiver com erros, a seguinte dica pode ajudar:

Na guia **Depurador,** abra as ferramentas do desenvolvedor no seu navegador. No console, `localStorage.clear()` execute para remover quaisquer pontos de interrupção.

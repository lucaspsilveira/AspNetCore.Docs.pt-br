---
title: Depurar ASP.NET Core Blazor Webassembly
author: guardrex
description: Saiba como depurar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 75db5d5e69cb200ebf3bd1dc1e0afed0300214cc
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242765"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Depurar ASP.NET Core Blazor Webassembly

[Daniel Roth](https://github.com/danroth27)

BlazorOs aplicativos Webassembly podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (borda/Chrome). Como alternativa, você pode depurar seu aplicativo usando o Visual Studio ou Visual Studio Code.

Os cenários disponíveis incluem:

* Definir e remover pontos de interrupção.
* Execute o aplicativo com suporte para depuração no Visual Studio e Visual Studio Code (suporte a<kbd>F5</kbd> ).
* Etapa única (<kbd>F10</kbd>) por meio do código.
* Retome a execução de código com <kbd>F8</kbd> em um navegador ou <kbd>F5</kbd> no Visual Studio ou Visual Studio Code.
* Na exibição *locais* , observe os valores de variáveis locais.
* Consulte a pilha de chamadas, incluindo cadeias de chamada que vão do JavaScript para o .NET e do .NET para o JavaScript.

Por enquanto, você *não pode*:

* Interromper em exceções sem tratamento.
* Pontos de interrupção de acesso durante a inicialização do aplicativo.

Continuaremos a melhorar a experiência de depuração em versões futuras.

## <a name="prerequisites"></a>Pré-requisitos

A depuração requer um dos seguintes navegadores:

* Microsoft Edge (versão 80 ou posterior)
* Google Chrome (versão 70 ou posterior)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Habilitar depuração para Visual Studio e Visual Studio Code

Para habilitar a depuração para um Blazor aplicativo Webassembly existente, atualize o `launchSettings.json` arquivo no projeto de inicialização para incluir a seguinte `inspectUri` propriedade em cada perfil de inicialização:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Uma vez atualizado, o `launchSettings.json` arquivo deve ser semelhante ao exemplo a seguir:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

A `inspectUri` Propriedade:

* Permite que o IDE detecte que o aplicativo é um Blazor aplicativo Webassembly.
* Instrui a infraestrutura de depuração de script para se conectar ao navegador por meio Blazor do proxy de depuração.

Os valores de espaço reservado para o protocolo WebSockets ( `wsProtocol` ), host ( `url.hostname` ), porta ( `url.port` ) e URI do Inspetor no navegador iniciado ( `browserInspectUri` ) são fornecidos pela estrutura.

## <a name="visual-studio"></a>Visual Studio

Para depurar um Blazor aplicativo Webassembly no Visual Studio:

1. Crie um novo Blazor aplicativo Webassembly hospedado ASP.NET Core.
1. Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.
1. Defina um ponto de interrupção no `Pages/Counter.razor` no `IncrementCount` método.
1. Navegue até a **`Counter`** guia e selecione o botão para atingir o ponto de interrupção:

   ![Contador de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Confira o valor do `currentCount` campo na janela locais:

   ![Exibir locais](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Pressione <kbd>F5</kbd> para continuar a execução.

Ao depurar seu Blazor aplicativo Webassembly, você também pode depurar o código do servidor:

1. Defina um ponto de interrupção na `Pages/FetchData.razor` página em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Defina um ponto de interrupção no `WeatherForecastController` `Get` Método Action.
1. Navegue até a **`Fetch Data`** guia para atingir o primeiro ponto de interrupção no `FetchData` componente logo antes de emitir uma solicitação HTTP para o servidor:

   ![Depurar dados de busca](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Pressione <kbd>F5</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController` :

   ![Servidor de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Para depurar um Blazor aplicativo Webassembly no Visual Studio Code:
 
Instale a [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true` .

![Extensões](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Depurador de visualização JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a>Depurar Blazor Webassembly autônomo

1. Abra o Blazor aplicativo Webassembly autônomo no vs Code.

   Se você receber a notificação a seguir, será necessária uma configuração adicional para habilitar a depuração:
   
   * Confirme se você tem as extensões corretas instaladas.
   * Confirme se a depuração de visualização do JavaScript está habilitada.
   * Recarregue a janela.

   ![Configuração adicional necessária](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. Inicie a depuração usando o atalho de teclado <kbd>F5</kbd> ou o item de menu.

1. Quando solicitado, selecione a opção ** Blazor Webassembly Debug** para iniciar a depuração.

   ![Lista de opções de depuração disponíveis](index/_static/blazor-vscode-debugtypes.png)

1. O aplicativo autônomo é iniciado e um navegador de depuração é aberto.

1. Defina um ponto de interrupção no `IncrementCount` método no `Counter` componente e, em seguida, selecione o botão para atingir o ponto de interrupção:

   ![Contador de depuração no VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a>Depurar Blazor Webassembly hospedado

1. Abra o Blazor aplicativo Webassembly hospedado no vs Code.

1. Se não houver uma configuração de inicialização definida para o projeto, a notificação a seguir será exibida. Selecione **Sim** na barra superior.

   ![Adicionar ativos necessários](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Na janela de seleção, selecione o projeto de *servidor* dentro da solução hospedada.

Um `launch.json` arquivo é gerado com a configuração de inicialização para iniciar o depurador.

### <a name="attach-to-an-existing-debugging-session"></a>Anexar a uma sessão de depuração existente

Para anexar a um aplicativo em execução Blazor , crie um `launch.json` arquivo com a seguinte configuração:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Só há suporte para a anexação a uma sessão de depuração para aplicativos autônomos. Para usar a depuração de pilha completa, você deve iniciar o aplicativo a partir de VS Code.

### <a name="launch-configuration-options"></a>Opções de configuração de inicialização

As opções de configuração de inicialização a seguir têm suporte para o `blazorwasm` tipo de depuração.

| Opção    | Descrição |
| --------- | ----------- |
| `request` | Use `launch` para iniciar e anexar uma sessão de depuração a um Blazor aplicativo Webassembly ou `attach` para anexar uma sessão de depuração a um aplicativo já em execução. |
| `url`     | A URL a ser aberta no navegador durante a depuração. O padrão é `https://localhost:5001`. |
| `browser` | O navegador a ser iniciado para a sessão de depuração. Definir como `edge` ou `chrome`. O padrão é `chrome`. |
| `trace`   | Usado para gerar logs do depurador JS. Defina como `true` para gerar logs. |
| `hosted`  | Deve ser definido como `true` se estiver iniciando e depurando um Blazor aplicativo Webassembly hospedado. |
| `webRoot` | Especifica o caminho absoluto do servidor Web. Deve ser definido se um aplicativo for servido de uma sub-roteiro. |
| `timeout` | O número de milissegundos a aguardar a sessão de depuração ser anexada. O padrão é 30.000 milissegundos (30 segundos). |
| `program` | Uma referência ao executável para executar o servidor do aplicativo hospedado. Deve ser definido se `hosted` for `true` . |
| `cwd`     | O diretório de trabalho para o qual iniciar o aplicativo. Deve ser definido se `hosted` for `true` . |
| `env`     | As variáveis de ambiente a serem fornecidas ao processo iniciado. Aplicável somente se `hosted` for definido como `true` . |

### <a name="example-launch-configurations"></a>Exemplo de configurações de inicialização

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a>Iniciar e depurar um Blazor aplicativo Webassembly autônomo

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>Anexar a um aplicativo em execução em uma URL especificada

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a>Iniciar e depurar um Blazor aplicativo Webassembly hospedado

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a>Depurar no navegador

1. Execute uma compilação de depuração do aplicativo no ambiente de desenvolvimento.

1. Pressione <kbd>Shift</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.

1. O navegador deve ser executado com a depuração remota habilitada. Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será gerada. A página de erro contém instruções para executar o navegador com a porta de depuração aberta para que o Blazor proxy de depuração possa se conectar ao aplicativo. *Feche todas as instâncias do navegador* e reinicie o navegador conforme instruído.

Depois que o navegador estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração abrirá uma nova guia do depurador. Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net no aplicativo. Expanda cada assembly e localize os `.cs` / `.razor` arquivos de origem disponíveis para depuração. Defina pontos de interrupção, alterne de volta para a guia do aplicativo e os pontos de interrupção serão atingidos quando o código for executado. Depois que um ponto de interrupção é atingido, a etapa única (<kbd>F10</kbd>) por meio da execução do código ou retomar (<kbd>F8</kbd>) normalmente.

Blazorfornece um proxy de depuração que implementa o [protocolo devtools do Chrome](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede. Quando o atalho de teclado de depuração é pressionado, Blazor o aponta para o Chrome devtools no proxy. O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).

## <a name="browser-source-maps"></a>Mapas de origem do navegador

Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente. No entanto, o Blazor atualmente não mapeia C# diretamente para JavaScript/WASM. Em vez disso, a Blazor interpretação de Il no navegador, portanto, os mapas de origem não são relevantes.

## <a name="troubleshoot"></a>Solucionar problemas

Se você estiver executando erros, as dicas a seguir podem ajudar:

* Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador. No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.
* Confirme que você instalou e confia no certificado de desenvolvimento ASP.NET Core HTTPS. Para obter mais informações, consulte <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.

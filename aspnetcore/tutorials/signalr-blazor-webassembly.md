---
title: Usar ASP.NET Core SignalR com Blazor Webassembly
author: guardrex
description: Crie um aplicativo de chat que usa SignalR ASP.NET Core Blazor com Webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 1579b92dbc9db08bfdc5572e5d4245bd18d50590
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773782"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Usar Signalr ASP.NET Core com Webassembly de mais incrivelmente

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando Signalr com Webassembly mais alto. Você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto de aplicativo hospedado Webassembly de mais incrivelmente
> * Adicionar a biblioteca de clientes do SignalR
> * Adicionar um Hub do Signalr
> * Adicionar serviços do Signalr e um ponto de extremidade para o Hub do Signalr
> * Adicionar código de componente Razor para chat

No final deste tutorial, você terá um aplicativo de chat em funcionamento.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Criar um projeto de aplicativo Webassembly de mais incrivelmente hospedado

Quando não estiver usando o Visual Studio versão 16,6 Preview 2 ou posterior, instale o modelo [Webassembly](xref:blazor/hosting-models#blazor-webassembly) mais recente. O pacote [Microsoft. AspNetCore. Components. Webassembly. templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) tem uma versão de visualização enquanto o Webassembly de mais de baixo está em visualização. Em um shell de comando, execute o seguinte comando:

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
```

Siga as orientações para sua escolha de ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Criar um novo projeto.

1. Selecione **aplicativo** mais novo e selecione **Avançar**.

1. Digite "BlazorSignalRApp" no campo **nome do projeto** . Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Escolha o modelo de **aplicativo Webassembly mais incrivelmente** .

1. Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .

1. Selecione **Criar**.

> [!NOTE]
> Se você atualizou ou instalou uma nova versão do Visual Studio e o modelo Webassembly mais novo não aparece na interface do usuário do VS, reinstale `dotnet new` o modelo usando o comando mostrado anteriormente.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Em um shell de comando, execute o seguinte comando:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Em Visual Studio Code, abra a pasta do projeto do aplicativo.

1. Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**. Visual Studio Code adiciona automaticamente a pasta *. vscode* com arquivos *Launch. JSON* e *Tasks. JSON* gerados.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Em um shell de comando, execute o seguinte comando:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. No Visual Studio para Mac, abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto (*. sln*).

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando, execute o seguinte comando:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Adicionar a biblioteca de clientes do SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **BlazorSignalRApp. Client** e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como *NuGet.org*.

1. Com **procurar** selecionado, digite "Microsoft. AspNetCore. signaler. Client" na caixa de pesquisa.

1. Nos resultados da pesquisa, selecione o `Microsoft.AspNetCore.SignalR.Client` pacote e selecione **instalar**.

1. Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

No **terminal integrado** (**Exibir** > **terminal** na barra de ferramentas), execute os seguintes comandos:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral da **solução** , clique com o botão direito do mouse no projeto **BlazorSignalRApp. Client** e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como *NuGet.org*.

1. Com **procurar** selecionado, digite "Microsoft. AspNetCore. signaler. Client" na caixa de pesquisa.

1. Nos resultados da pesquisa, marque a caixa de seleção ao lado `Microsoft.AspNetCore.SignalR.Client` do pacote e selecione **Adicionar pacote**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando, execute os seguintes comandos:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Adicionar um Hub do Signalr

No projeto **BlazorSignalRApp. Server** , crie uma pasta *hubs* (plural) e adicione a seguinte `ChatHub` classe (*hubs/ChatHub. cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Adicionar serviços e um ponto de extremidade para o Hub do Signalr

1. No projeto **BlazorSignalRApp. Server** , abra o arquivo *Startup.cs* .

1. Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Adicione os serviços de middleware de compactação de resposta `Startup.ConfigureServices`e de sinalização para:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. `Startup.Configure` Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Adicionar código de componente Razor para chat

1. No projeto **BlazorSignalRApp. Client** , abra o arquivo *pages/index. Razor* .

1. Substitua a marcação pelo código a seguir:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Executar o aplicativo

1. Siga as orientações para suas ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Em **Gerenciador de soluções**, selecione o projeto **BlazorSignalRApp. Server** . Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd>+<kbd>F5</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![Aplicativo de exemplo Webassembly do signalr mais do que é aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país* &copy;não descoberto 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Quando o VS Code oferece para criar um perfil de inicialização para o aplicativo de servidor (*. vscode/Launch. JSON*), a `program` entrada é semelhante à seguinte para apontar para o assembly do`{APPLICATION NAME}.Server.dll`aplicativo ():

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd>+<kbd>F5</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![Aplicativo de exemplo Webassembly do signalr mais do que é aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país* &copy;não descoberto 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral da **solução** , selecione o projeto **BlazorSignalRApp. Server** . Pressione <kbd>⌘</kbd>+<kbd>↩</kbd>* * para executar o aplicativo com depuração ou <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![Aplicativo de exemplo Webassembly do signalr mais do que é aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país* &copy;não descoberto 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. Em um shell de comando, execute os seguintes comandos:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![Aplicativo de exemplo Webassembly do signalr mais do que é aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país* &copy;não descoberto 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um Blazor projeto de aplicativo hospedado pelo Webassembly
> * Adicionar a SignalR biblioteca de cliente
> * Adicionar um SignalR Hub
> * Adicionar SignalR serviços e um ponto de extremidade SignalR para o Hub
> * Adicionar Razor código de componente para o chat

Para saber mais sobre a Blazor criação de aplicativos, Blazor consulte a documentação:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Recursos adicionais

* <xref:signalr/introduction>

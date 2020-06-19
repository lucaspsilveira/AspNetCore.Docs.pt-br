---
title: Usar ASP.NET Core SignalR com Blazor Webassembly
author: guardrex
description: Crie um aplicativo de chat que usa ASP.NET Core SignalR com Blazor Webassembly.
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 8ef029af10f767ae505fddc636bb15f7e7c5e538
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102700"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Usar ASP.NET Core SignalR com Blazor Webassembly

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR Blazor Webassembly. Você aprenderá como:

> [!div class="checklist"]
> * Criar um Blazor projeto de aplicativo hospedado pelo Webassembly
> * Adicionar a SignalR biblioteca de cliente
> * Adicionar um SignalR Hub
> * Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub
> * Adicionar Razor código de componente para o chat

No final deste tutorial, você terá um aplicativo de chat em funcionamento.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16,6 ou posterior](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **ASP.net e** a carga de trabalho de desenvolvimento Web
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* [Visual Studio para Mac versão 8,6 ou posterior](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Criar um Blazor projeto de aplicativo Webassembly hospedado

Siga as orientações para sua escolha de ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!NOTE]
> O Visual Studio 16,6 ou posterior e o SDK do .NET Core 3.1.300 ou posterior são necessários.

1. Criar um novo projeto.

1. Selecione ** Blazor aplicativo** e selecione **Avançar**.

1. Digite "BlazorSignalRApp" no campo **nome do projeto** . Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Escolha o modelo de ** Blazor aplicativo Webassembly** .

1. Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .

1. Selecione **Criar**.

> [!NOTE]
> Se você atualizou ou instalou uma nova versão do Visual Studio e o Blazor modelo Webassembly não aparece na interface do usuário do vs, reinstale o modelo usando o `dotnet new` comando mostrado anteriormente.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Em um shell de comando, execute o seguinte comando:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Em Visual Studio Code, abra a pasta do projeto do aplicativo.

1. Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**. Visual Studio Code adiciona automaticamente a pasta *. vscode* com o *launch.jsgerado no* e *tasks.jsem* arquivos.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Instale a versão mais recente do [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e execute as seguintes etapas:

1. Selecione **arquivo**  >  **nova solução** ou crie um **novo** projeto na **janela iniciar**.

1. Na barra lateral, selecione **aplicativo Web e de console**  >  **App**.

1. Escolha o modelo de ** Blazor aplicativo Webassembly** . Selecione **Avançar**.

   Confirme as seguintes configurações:

   * **Estrutura de destino** definida como **.NET Core 3,1**.
   * **Autenticação** definida como **sem autenticação**.

   Marque a caixa de seleção **ASP.NET Core hospedado** .

   Selecione **Avançar**.

1. No campo **nome do projeto** , nomeie o aplicativo `BlazorSignalRApp` . Selecione **Criar**.

   Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue. As senhas de usuário e de conjunto de chaves são necessárias para confiar no certificado.

1. Abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto (*. sln*).

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando, execute o seguinte comando:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Adicionar a SignalR biblioteca de cliente

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **BlazorSignalRApp. Client** e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como *NuGet.org*.

1. Com **procurar** selecionado, digite "Microsoft. AspNetCore. SignalR . Cliente "na caixa de pesquisa.

1. Nos resultados da pesquisa, selecione o [Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)Pacote do cliente e selecione **instalar**.

1. Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

No **terminal integrado** (**Exibir**  >  **terminal** na barra de ferramentas), execute os seguintes comandos:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral da **solução** , clique com o botão direito do mouse no projeto **BlazorSignalRApp. Client** e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como *NuGet.org*.

1. Com **procurar** selecionado, digite "Microsoft. AspNetCore. SignalR . Cliente "na caixa de pesquisa.

1. Nos resultados da pesquisa, marque a caixa de seleção ao lado de [Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)Pacote do cliente e selecione **Adicionar pacote**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando, execute os seguintes comandos:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Adicionar um SignalR Hub

No projeto **BlazorSignalRApp. Server** , crie uma pasta *hubs* (plural) e adicione a seguinte `ChatHub` classe (*hubs/ChatHub. cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Adicionar serviços e um ponto de extremidade para o SignalR Hub

1. No projeto **BlazorSignalRApp. Server** , abra o arquivo *Startup.cs* .

1. Adicione o namespace para a `ChatHub` classe na parte superior do arquivo:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Adicionar SignalR e responder aos serviços de middleware de compactação para `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. Em `Startup.Configure`:

   * Use o middleware de compactação de resposta na parte superior da configuração do pipeline de processamento.
   * Entre os pontos de extremidade para os controladores e o fallback do lado do cliente, adicione um EndPoint para o Hub.

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a>Adicionar Razor código de componente para o chat

1. No projeto **BlazorSignalRApp. Client** , abra o arquivo *pages/index. Razor* .

1. Substitua a marcação pelo código a seguir:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Executar o aplicativo

1. Siga as orientações para suas ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Em **Gerenciador de soluções**, selecione o projeto **BlazorSignalRApp. Server** . Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![SignalRBlazorAplicativo de exemplo Webassembly aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Quando o VS Code oferece para criar um perfil de inicialização para o aplicativo de servidor (*. vscode/launch.json*), a `program` entrada é semelhante à seguinte para apontar para o assembly do aplicativo ( `{APPLICATION NAME}.Server.dll` ):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Pressione <kbd>F5</kbd> para executar o aplicativo com depuração ou <kbd>Ctrl</kbd> + <kbd>F5</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![SignalRBlazorAplicativo de exemplo Webassembly aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral da **solução** , selecione o projeto **BlazorSignalRApp. Server** . Pressione <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo com depuração ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![SignalRBlazorAplicativo de exemplo Webassembly aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. Em um shell de comando, execute os seguintes comandos:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![SignalRBlazorAplicativo de exemplo Webassembly aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy; 1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um Blazor projeto de aplicativo hospedado pelo Webassembly
> * Adicionar a SignalR biblioteca de cliente
> * Adicionar um SignalR Hub
> * Adicionar SignalR serviços e um ponto de extremidade para o SignalR Hub
> * Adicionar Razor código de componente para o chat

Para saber mais sobre a criação de Blazor aplicativos, consulte a Blazor documentação:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Recursos adicionais

* <xref:signalr/introduction>
* [SignalRnegociação entre origens para autenticação](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)

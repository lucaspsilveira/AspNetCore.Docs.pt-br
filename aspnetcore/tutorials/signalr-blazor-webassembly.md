---
title: Use ASP.NET SignalR Blazor Core com WebAssembly
author: guardrex
description: Crie um aplicativo de SignalR bate-papo que use ASP.NET Core com Blazor webAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 798068c83e16070d3279c88c44af0cd96d182fe2
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488878"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Use ASP.NET Core SignalR com Blazor WebAssembly

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Este tutorial ensina o básico da construção de um aplicativo em tempo real usando signalR com Blazor WebAssembly. Você aprenderá como:

> [!div class="checklist"]
> * Crie um projeto de aplicativo hospedado no WebAssembly Blazor
> * Adicionar a biblioteca de clientes do SignalR
> * Adicione um hub SignalR
> * Adicionar serviços SignalR e um ponto final para o hub SignalR
> * Adicionar código de componente razor para bate-papo

No final deste tutorial, você terá um aplicativo de bate-papo funcionando.

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

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Crie um projeto de aplicativo Blazor WebAssembly hospedado

Ao não usar a versão 16.6 Preview 2 ou posterior do Visual Studio, instale o modelo [Blazor WebAssembly.](xref:blazor/hosting-models#blazor-webassembly) O pacote [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) tem uma versão de pré-visualização enquanto o Blazor WebAssembly está em pré-visualização. Em um shell de comando, execute o seguinte comando:

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
```

Siga as orientações para a sua escolha de ferramenta:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Criar um novo projeto.

1. Selecione **o aplicativo Blazor** e selecione **Next**.

1. Digite "BlazorSignalRApp" no campo Nome do **Projeto.** Confirme se a entrada **do local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Escolha o modelo **do aplicativo Blazor WebAssembly.**

1. Em **Avançado,** selecione a caixa de seleção **hosted ASP.NET Core.**

1. Selecione **Criar**.

> [!NOTE]
> Se você atualizou ou instalou uma nova versão do Visual Studio e o modelo Blazor WebAssembly `dotnet new` não aparecerá na UI VS, reinstale o modelo usando o comando mostrado anteriormente.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Em um shell de comando, execute o seguinte comando:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. No Visual Studio Code, abra a pasta de projetos do aplicativo.

1. Quando a caixa de diálogo aparecer para adicionar ativos para criar e depurar o aplicativo, selecione **Sim**. O Visual Studio Code adiciona automaticamente a pasta *.vscode* com arquivos *launch.json* e *tasks.json gerados.*

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Em um shell de comando, execute o seguinte comando:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. No Visual Studio for Mac, abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto *( .sln*).

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando, execute o seguinte comando:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Adicionar a biblioteca de clientes do SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. No **Solution Explorer,** clique com o botão direito do mouse no projeto **BlazorSignalRApp.Client** e **selecione Gerenciar pacotes NuGet**.

1. Na caixa de diálogo **Gerenciar pacotes NuGet,** confirme se a fonte do **pacote** está definida para *nuget.org*.

1. Com **procurar** selecionado, digite "Microsoft.AspNetCore.SignalR.Client" na caixa de pesquisa.

1. Nos resultados da pesquisa, selecione o `Microsoft.AspNetCore.SignalR.Client` pacote e selecione **Instalar**.

1. Se a **caixa de diálogo 'Inversaver'' 'Inverter''** aparecer, selecione **OK**.

1. Se a caixa de diálogo **Aceitação** de Licença for exibida, selecione **Aceito Se** você concordar com os termos da licença.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

No **Terminal Integrado** (**Exibir** > **Terminal** a partir da barra de ferramentas), execute os seguintes comandos:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral **solução,** clique com o botão direito do mouse no projeto **BlazorSignalRApp.Client** e **selecione Gerenciar pacotes NuGet**.

1. Na caixa de diálogo **Gerenciar pacotes NuGet,** confirme se a lista de informações está definida para *nuget.org*.

1. Com **procurar** selecionado, digite "Microsoft.AspNetCore.SignalR.Client" na caixa de pesquisa.

1. Nos resultados da pesquisa, selecione `Microsoft.AspNetCore.SignalR.Client` a caixa de seleção ao lado do pacote e **selecione Adicionar pacote**.

1. Se a caixa de diálogo **Aceitação** de Licença for exibida, **selecione Aceitar** se você concordar com os termos da licença.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando, execute os seguintes comandos:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Adicione um hub SignalR

No projeto **BlazorSignalRApp.Server,** crie uma pasta *Hubs* (plural) e adicione a seguinte `ChatHub` classe *(Hubs/ChatHub.cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a>Adicionar serviços SignalR e um ponto final para o hub SignalR

1. No projeto **BlazorSignalRApp.Server,** abra o arquivo *Startup.cs.*

1. Adicione o namespace `ChatHub` para a classe na parte superior do arquivo:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Adicione os serviços `Startup.ConfigureServices`SignalR a:

   ```csharp
   services.AddSignalR();
   ```

1. Entre `Startup.Configure` os pontos finais da rota do controlador padrão e o recuo do lado do cliente, adicione um ponto final para o hub:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Adicionar código de componente razor para bate-papo

1. No projeto **BlazorSignalRApp.Client,** abra o arquivo *Páginas/Index.razor.*

1. Substitua a marcação pelo seguinte código:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Executar o aplicativo

1. Siga as orientações para suas ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. No **Solution Explorer,** selecione o projeto **BlazorSignalRApp.Server.** Pressione **Ctrl+F5** para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![SignalR Blazor WebAssembly aplicativo de amostra aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citações: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Selecione **Depurar** > **Executar Sem Depuração** na barra de ferramentas.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![SignalR Blazor WebAssembly aplicativo de amostra aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citações: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral **solução,** selecione o projeto **BlazorSignalRApp.Server.** No menu, **selecione Executar iniciar** > **sem depuração**.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![SignalR Blazor WebAssembly aplicativo de amostra aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citações: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. Em um shell de comando, execute os seguintes comandos:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![SignalR Blazor WebAssembly aplicativo de amostra aberto em duas janelas do navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citações: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Crie Blazor um projeto de aplicativo hospedado no WebAssembly
> * Adicione SignalR a biblioteca do cliente
> * Adicionar SignalR um hub
> * Adicionar SignalR serviços e um SignalR ponto final para o hub
> * Adicionar código de componente razor para bate-papo

Para saber mais Blazor sobre a Blazor construção de aplicativos, consulte a documentação:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Recursos adicionais

* <xref:signalr/introduction>

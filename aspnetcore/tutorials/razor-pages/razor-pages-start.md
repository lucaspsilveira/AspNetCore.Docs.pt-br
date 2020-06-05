---
title: "\"Tutorial: introdução às Razor páginas no ASP.NET Core\""
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor páginas no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor páginas, usar Entity Framework Core e SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 3b8ccf639bb91234f81c67750fffa170e52d636f
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452326"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Tutorial: introdução às Razor páginas no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um Razor aplicativo web ASP.NET Core Pages.

[!INCLUDE[](~/includes/advancedRP.md)]

No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Neste tutorial, você:

> [!div class="checklist"]
> * Crie um Razor aplicativo Web de páginas.
> * Execute o aplicativo.
> * Examinar os arquivos de projeto.

No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Criar um Razor aplicativo Web de páginas

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.
  ![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Nomeie o projeto **RazorPagesMovie**. É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.
  ![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* Selecione **ASP.NET Core 3,1** no menu suspenso, **aplicativo Web**e, em seguida, selecione **criar**.

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  O seguinte projeto inicial é criado:

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Altere para o diretório (`cd`) que contém o projeto.

* Execute os seguintes comandos:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * O `dotnet new` comando cria um novo Razor projeto de páginas na pasta *RazorPagesMovie* .
  * O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.

* Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?** Selecione **Sim** na barra superior.

  Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Arquivo** > **Nova Solução**.

  ![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**. Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.

  ![seleção de modelo de aplicativo Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* Confirme as seguintes configurações:

  * **Estrutura de destino** definida como **.NET Core 3,1**.
  * **Autenticação** definida como **sem autenticação**.
   
  Selecione **Avançar**.

  ![seleção do macOS .NET Core 3,1](razor-pages-start/_static/targetframework3.png)

* Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.

  ![nome do macOS o projeto](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Executar o aplicativo

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Examinar os arquivos de projeto

Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.

### <a name="pages-folder"></a>Pasta Páginas

Contém Razor páginas e arquivos de suporte. Cada Razor página é um par de arquivos:

* Um arquivo *. cshtml* que contém marcação HTML com código C# usando a Razor sintaxe.
* Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.

Arquivos de suporte têm nomes que começam com um sublinhado. Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas. Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página. Para obter mais informações, consulte <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Pasta wwwroot

Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS. Para obter mais informações, consulte <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contém dados de configuração, como cadeias de conexão. Para obter mais informações, consulte <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Module.vb

Contém o ponto de entrada para o programa. Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

contém código que configura o comportamento do aplicativo. Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo tutorial da série:

> [!div class="step-by-step"]
> [Adicionar um modelo](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Este é o primeiro tutorial de uma série. [A série](xref:tutorials/razor-pages/index) ensina as noções básicas da criação de um Razor aplicativo Web de ASP.NET Core páginas.

[!INCLUDE[](~/includes/advancedRP.md)]

No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

Neste tutorial, você:

> [!div class="checklist"]
> * Crie um Razor aplicativo Web de páginas.
> * Execute o aplicativo.
> * Examinar os arquivos de projeto.

No final deste tutorial, você terá um Razor aplicativo Web de páginas de trabalho no qual será criado em Tutoriais posteriores.

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Criar um Razor aplicativo Web de páginas

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.

* Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Nomeie o projeto **RazorPagesMovie**. É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  O seguinte projeto inicial é criado:

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Altere para o diretório (`cd`) que contém o projeto.

* Execute os seguintes comandos:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * O `dotnet new` comando cria um novo Razor projeto de páginas na pasta *RazorPagesMovie* .
  * O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.

* Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?** Selecione **Sim** na barra superior.

  Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Arquivo** > **Nova Solução**.

![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Em Visual Studio para Mac anteriores à versão 8,6, selecione aplicativo Web do aplicativo **.NET Core**  >  **App**  >  **Web Application**  >  **Avançar**. Na versão 8,6 ou posterior, selecione aplicativo Web do aplicativo Web **e de console**  >  **App**  >  **Web Application**  >  **próximo**.

* Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , defina a **estrutura de destino** como **.NET Core 3,1**.

  ![Seleção do .NET Core 3.0 do macOS](razor-pages-start/_static/targetframework3.png)

* Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Executar o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Pressione Ctrl + F5 para execução sem o depurador.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local. Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.

* Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.

  Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Pressione **Ctrl-F5** para executar sem o depurador.

  O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local.

* Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.

  Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Pressione **Cmd-Opt-F5** para execução sem o depurador.

  O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.

* Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.

  Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Examinar os arquivos de projeto

Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.

### <a name="pages-folder"></a>Pasta Páginas

Contém Razor páginas e arquivos de suporte. Cada Razor página é um par de arquivos:

* Um arquivo *. cshtml* que contém marcação HTML com código C# usando a Razor sintaxe.
* Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.

Arquivos de suporte têm nomes que começam com um sublinhado. Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas. Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página. Para obter mais informações, consulte <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Pasta wwwroot

Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS. Para obter mais informações, consulte <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contém dados de configuração, como cadeias de conexão. Para obter mais informações, consulte <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Module.vb

Contém o ponto de entrada para o programa. Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies. Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo tutorial da série:

> [!div class="step-by-step"]
> [Adicionar um modelo](xref:tutorials/razor-pages/model)

::: moniker-end

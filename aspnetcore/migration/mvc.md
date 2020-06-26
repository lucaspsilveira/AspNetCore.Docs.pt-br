---
title: Aprenda a migrar do ASP.NET MVC para o ASP.NET Core MVC
author: wadepickett
description: Saiba como iniciar a migração de um projeto MVC do ASP.NET para ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 6a645d0e5959b4301ee7d2bcfc692f7499574dc4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407317"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migrar do ASP.NET MVC para o ASP.NET Core MVC

::: moniker range=">= aspnetcore-3.0"

Este artigo mostra como iniciar a migração de um projeto MVC do ASP.NET para [ASP.NET Core MVC](xref:mvc/overview). No processo, ele realça as alterações relacionadas do ASP.NET MVC.

A migração do ASP.NET MVC é um processo de várias etapas. Este artigo cobre:

* Configuração inicial.
* Controladores básicos e exibições.
* Conteúdo estático.
* Dependências do lado do cliente.

Para migrar a configuração e o Identity código, consulte [migrar a configuração para ASP.NET Core](xref:migration/configuration) e [migrar a autenticação e Identity para ASP.NET Core](xref:migration/identity).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a>Criar o projeto do MVC do ASP.NET do início

Crie um projeto MVC de exemplo do ASP.NET no Visual Studio para migrar:

1. No menu **Arquivo**, selecione **Novo** > **Projeto**.
1. Selecione **aplicativo Web ASP.net (.NET Framework)** e, em seguida, selecione **Avançar**.
1. Nomeie o projeto *WebApp1* para que o namespace corresponda ao projeto de ASP.NET Core criado na próxima etapa. Selecione **Criar**.
1. Selecione **MVC**e, em seguida, selecione **criar**.

## <a name="create-the-aspnet-core-project"></a>Criar o projeto de ASP.NET Core

Crie uma nova solução com um novo projeto ASP.NET Core para migrar para o:

1. Inicie uma segunda instância do Visual Studio.
1. No menu **Arquivo**, selecione **Novo** > **Projeto**.
1. Selecione **aplicativo Web do ASP.NET Web Core** e, em seguida, selecione **Avançar**.
1. Na caixa de diálogo **configurar seu novo projeto** , nomeie o projeto *WebApp1*.
1. Defina o local para um diretório diferente do projeto anterior para usar o mesmo nome de projeto. Usar o mesmo namespace torna mais fácil copiar o código entre os dois projetos. Selecione **Criar**.
1. Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 3,1** estão selecionados. Selecione o modelo de projeto **aplicativo Web (modelo-exibição-controlador)** e selecione **criar**.

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a>Configurar o site de ASP.NET Core para usar o MVC

Nos projetos ASP.NET Core 3,0 e posteriores, .NET Framework não é mais uma estrutura de destino com suporte. Seu projeto deve ter como destino o .NET Core. A estrutura compartilhada ASP.NET Core, que inclui o MVC, faz parte da instalação do tempo de execução do .NET Core. A estrutura compartilhada é automaticamente referenciada ao usar o `Microsoft.NET.Sdk.Web` SDK no arquivo de projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Para obter mais informações, consulte [referência de estrutura](xref:migration/22-to-30#framework-reference).

Em ASP.NET Core, a `Startup` classe:

* Substitui *global. asax*.
* Manipula todas as tarefas de inicialização do aplicativo.

Para obter mais informações, consulte <xref:fundamentals/startup>.

No ASP.NET Core projeto, abra o arquivo *Startup.cs* :

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

ASP.NET Core aplicativos devem optar por recursos de estrutura com middleware. O código gerado pelo modelo anterior adiciona os seguintes serviços e middleware:

* O <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> método de extensão registra o suporte ao serviço MVC para controladores, recursos relacionados à API e modos de exibição. Para obter mais informações sobre as opções de registro do serviço MVC, consulte [registro de serviço do MVC](xref:migration/22-to-30#mvc-service-registration)
* O <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> método de extensão adiciona o manipulador de arquivo estático `Microsoft.AspNetCore.StaticFiles` . O `UseStaticFiles` método de extensão deve ser chamado antes de `UseRouting` . Para obter mais informações, consulte <xref:fundamentals/static-files>.
* O <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> método de extensão adiciona o roteamento. Para obter mais informações, consulte <xref:fundamentals/routing>.

Essa configuração existente inclui o que é necessário para migrar o projeto de exemplo do ASP.NET MVC. Para obter mais informações sobre ASP.NET Core opções de middleware, consulte <xref:fundamentals/startup> .

## <a name="migrate-controllers-and-views"></a>Migrar controladores e exibições

No projeto ASP.NET Core, uma nova classe de controlador vazia e uma classe de exibição seriam adicionadas para servir como espaços reservados usando os mesmos nomes que o controlador e as classes de exibição em qualquer projeto MVC ASP.NET para o qual migrar.

O projeto ASP.NET Core *WebApp1* já inclui um controlador de exemplo mínimo e uma exibição com o mesmo nome que o projeto MVC do ASP.net. Portanto, elas servirão como espaços reservados para o controlador MVC ASP.NET e exibições a serem migradas do projeto ASP.NET MVC *WebApp1* .

1. Copie os métodos do ASP.NET MVC `HomeController` para substituir os novos métodos de ASP.NET Core `HomeController` . Não é necessário alterar o tipo de retorno dos métodos de ação. O tipo de retorno do método de ação do controlador do modelo interno do ASP.NET MVC é [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); no ASP.NET Core MVC, os métodos de ação retornam `IActionResult` . `ActionResult` implementa `IActionResult`.
1. No projeto ASP.NET Core, clique com o botão direito do mouse no diretório de *modos de exibição/início* , selecione **Adicionar** > **Item existente**.
1. Na caixa de diálogo **Adicionar item existente** , navegue até o diretório *base e os modos de exibição* do projeto ASP.NET MVC *WebApp1* .
1. Selecione os arquivos de exibição *about. cshtml*, *Contact. cshtml*e *index. cshtml* e, Razor em seguida, selecione **Adicionar**, substituindo os arquivos existentes.

Para obter mais informações, consulte <xref:mvc/controllers/actions> e <xref:mvc/views/overview>.

## <a name="test-each-method"></a>Testar cada método

Cada ponto de extremidade do controlador pode ser testado, no entanto, layout e estilos são abordados posteriormente no documento.

1. Execute o aplicativo ASP.NET Core.
1. Invoque as exibições renderizadas do navegador no aplicativo ASP.NET Core em execução substituindo o número da porta atual pelo número da porta usado no projeto ASP.NET Core. Por exemplo, `https://localhost:44375/home/about`.

## <a name="migrate-static-content"></a>Migrar conteúdo estático

No ASP.NET MVC 5 e versões anteriores, o conteúdo estático era hospedado no diretório raiz do projeto Web e foi misturado com arquivos do lado do servidor. No ASP.NET Core, os arquivos estáticos são armazenados no diretório [raiz da Web](xref:fundamentals/index#web-root) do projeto. O diretório padrão é *{Content root}/wwwroot*, mas pode ser alterado. Saiba mais em [Arquivos estáticos no ASP.NET Core](xref:fundamentals/static-files#serve-static-files).

Copie o conteúdo estático do projeto ASP.NET MVC *WebApp1* para o diretório *wwwroot* no projeto ASP.NET Core *WebApp1* :

1. No projeto ASP.NET Core, clique com o botão direito do mouse no diretório *wwwroot* , selecione **Adicionar** > **Item existente**.
1. Na caixa de diálogo **Adicionar item existente** , navegue até o projeto ASP.NET MVC *WebApp1* .
1. Selecione o arquivo *favicon. ico* e, em seguida, selecione **Adicionar**, substituindo o arquivo existente.

## <a name="migrate-the-layout-files"></a>Migrar os arquivos de layout

Copie os arquivos de layout de projeto do ASP.NET MVC para o projeto ASP.NET Core:

1. No projeto ASP.NET Core, clique com o botão direito do mouse no diretório *exibições* , selecione **Adicionar** > **Item existente**.
1. Na caixa de diálogo **Adicionar item existente** , navegue até o diretório de *exibições* do projeto ASP.NET MVC *WebApp1* .
1. Selecione o arquivo *_ViewStart. cshtml* e, em seguida, selecione **Adicionar**.

Copie os arquivos de layout compartilhado do projeto ASP.NET MVC para o projeto ASP.NET Core:

1. No projeto ASP.NET Core, clique com o botão direito do mouse no diretório *views/Shared* , selecione **Add** > **Existing Item**.
1. Na caixa de diálogo **Adicionar item existente** , navegue até o diretório *compartilhado/exibições* do projeto ASP.NET MVC *WebApp1* .
1. Selecione o arquivo *_Layout. cshtml* e, em seguida, selecione **Adicionar**, substituindo o arquivo existente.

No ASP.NET Core projeto, abra o arquivo *_Layout. cshtml* . Faça as seguintes alterações para corresponder ao código concluído mostrado abaixo:

Atualize a inclusão de CSS de Bootstrap para corresponder ao código concluído abaixo:

1. Substituir `@Styles.Render("~/Content/css")` por um `<link>` elemento para carregar *bootstrap. css* (veja abaixo).
1. Remova `@Scripts.Render("~/bundles/modernizr")`.

A marcação de substituição completa para a inclusão de CSS de Bootstrap:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Atualize o jQuery e a inclusão de JavaScript de inicialização para corresponder ao código completo abaixo:

1. Substituir `@Scripts.Render("~/bundles/jquery")` por um `<script>` elemento (veja abaixo).
1. Substituir `@Scripts.Render("~/bundles/bootstrap")` por um `<script>` elemento (veja abaixo).

A marcação de substituição completa para jQuery e inclusão de JavaScript de Bootstrap:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

O arquivo *_Layout. cshtml* atualizado é mostrado abaixo:

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

Exibir o site no navegador. Ele deve ser renderizado com os estilos esperados no lugar.

## <a name="configure-bundling-and-minification"></a>Configurar agrupamento e minificação

ASP.NET Core é compatível com várias soluções de agrupamento e minificação de software livre, como [weboptimizer](https://github.com/ligershark/WebOptimizer) e outras bibliotecas semelhantes. ASP.NET Core não fornece uma solução de agrupamento nativo e minificação. Para obter informações sobre como configurar o agrupamento e o minificação, consulte [agrupamento e minificação](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Resolver erros HTTP 500

Há muitos problemas que podem causar uma mensagem de erro HTTP 500 que não contém informações sobre a origem do problema. Por exemplo, se o arquivo *views/_ViewImports. cshtml* contiver um namespace que não exista no projeto, um erro http 500 será gerado. Por padrão, em aplicativos ASP.NET Core, a `UseDeveloperExceptionPage` extensão é adicionada ao `IApplicationBuilder` e executada quando o ambiente está em *desenvolvimento*. Isso é detalhado no código a seguir:

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

ASP.NET Core converte exceções sem tratamento em respostas de erro HTTP 500. Normalmente, os detalhes do erro não são incluídos nessas respostas para evitar a divulgação de informações potencialmente confidenciais sobre o servidor. Para obter mais informações, consulte a [página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page).

## <a name="next-steps"></a>Próximas etapas

* <xref:migration/identity>

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Este artigo mostra como iniciar a migração de um projeto ASP.NET MVC para [ASP.NET Core MVC](xref:mvc/overview) 2,2. No processo, ele realça muitas das coisas que foram alteradas do ASP.NET MVC. A migração do ASP.NET MVC é um processo de várias etapas. Este artigo cobre:

* Instalação inicial
* Controladores básicos e exibições
* Conteúdo estático
* Dependências do lado do cliente.

Para migrar a configuração e o Identity código, consulte <xref:migration/configuration> e <xref:migration/identity> .

> [!NOTE]
> Os números de versão nos exemplos podem não ser atuais, atualizar os projetos adequadamente.

## <a name="create-the-starter-aspnet-mvc-project"></a>Criar o projeto do MVC do ASP.NET do início

Para demonstrar a atualização, começaremos criando um aplicativo MVC ASP.NET. Crie-o com o nome *WebApp1* para que o namespace corresponda ao projeto ASP.NET Core criado na próxima etapa.

![Caixa de diálogo Novo Projeto do Visual Studio](mvc/_static/new-project.png)

![Caixa de diálogo novo aplicativo Web: modelo de projeto MVC selecionado no painel modelos de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Opcional:* Altere o nome da solução de *WebApp1* para *Mvc5*. O Visual Studio exibe o nome da nova solução (*Mvc5*), o que torna mais fácil dizer a esse projeto do próximo projeto.

## <a name="create-the-aspnet-core-project"></a>Criar o projeto de ASP.NET Core

Crie um novo aplicativo Web *vazio* ASP.NET Core com o mesmo nome do projeto anterior (*WebApp1*) para que os namespaces nos dois projetos correspondam. Ter o mesmo namespace torna mais fácil copiar o código entre os dois projetos. Crie este projeto em um diretório diferente do projeto anterior para usar o mesmo nome.

![Caixa de diálogo Novo Projeto](mvc/_static/new_core.png)

![Caixa de diálogo novo aplicativo Web ASP.NET: modelo de projeto vazio selecionado no painel modelos de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Opcional:* Crie um novo aplicativo ASP.NET Core usando o modelo de projeto de *aplicativo Web* . Nomeie o projeto *WebApp1*e selecione uma opção de autenticação de **contas de usuário individuais**. Renomeie este aplicativo como *FullAspNetCore*. A criação desse projeto poupa tempo na conversão. O resultado final pode ser exibido no código gerado por modelo, o código pode ser copiado para o projeto de conversão ou comparado com o projeto gerado pelo modelo.

## <a name="configure-the-site-to-use-mvc"></a>Configurar o site para usar o MVC

* Ao direcionar o .NET Core, o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) é referenciado por padrão. Este pacote contém pacotes comumente usados por aplicativos MVC. Se estiver direcionando .NET Framework, as referências de pacote deverão ser listadas individualmente no arquivo de projeto.

`Microsoft.AspNetCore.Mvc`é o ASP.NET Core MVC Framework. `Microsoft.AspNetCore.StaticFiles`é o manipulador de arquivo estático. ASP.NET Core aplicativos explicitamente optam pelo middleware, como para o fornecimento de arquivos estáticos. Para obter mais informações, veja [Arquivos estáticos](xref:fundamentals/static-files).

* Abra o arquivo *Startup.cs* e altere o código para corresponder ao seguinte:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

O <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> método de extensão adiciona o manipulador de arquivo estático. Para obter mais informações, consulte inicialização e [Roteamento](xref:fundamentals/routing)de [aplicativos](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Adicionar um controlador e uma exibição

Nesta seção, um controlador e uma exibição mínimas são adicionados para servir como espaços reservados para o controlador MVC ASP.NET e exibições migradas na próxima seção.

* Adicione um diretório de *controladores* .

* Adicione uma **classe de controlador** denominada *HomeController.cs* ao diretório de *controladores* .

![Caixa de diálogo Adicionar Novo Item](mvc/_static/add_mvc_ctl.png)

* Adicione um diretório de *exibições* .

* Adicione um diretório *base views/* .

* Adicione uma ** Razor exibição** denominada *index. cshtml* ao diretório *base views/* .

![Caixa de diálogo Adicionar Novo Item](mvc/_static/view.png)

A estrutura do projeto é mostrada abaixo:

![Gerenciador de Soluções mostrando arquivos e diretórios de WebApp1](mvc/_static/project-structure-controller-view.png)

Substitua o conteúdo do arquivo *Views/Home/Index.cshtml* pela seguinte marcação:

```html
<h1>Hello world!</h1>
```

Execute o aplicativo.

![Aplicativo Web aberto no Microsoft Edge](mvc/_static/hello-world.png)

Para obter mais informações, consulte [Controllers](xref:mvc/controllers/actions) and [views](xref:mvc/views/overview).

A funcionalidade a seguir requer a migração do projeto ASP.NET MVC de exemplo para o projeto ASP.NET Core:

* conteúdo do lado do cliente (CSS, fontes e scripts)

* controladores

* Modos de exibição

* modelos

* reagrupamento

* filtros

* Faça logon/out, Identity (isso é feito no próximo tutorial.)

## <a name="controllers-and-views"></a>Controladores e exibições

* Copie cada um dos métodos do ASP.NET MVC `HomeController` para o novo `HomeController` . No ASP.NET MVC, o tipo de retorno do método de ação do controlador interno do modelo é [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); no ASP.NET Core MVC, os métodos de ação retornam `IActionResult` . `ActionResult`implementa `IActionResult` , portanto, não é necessário alterar o tipo de retorno dos métodos de ação.

* Copie os arquivos de exibição *about. cshtml*, *Contact. cshtml*e *Index. cshtml* Razor do projeto ASP.NET MVC para o projeto ASP.NET Core.

## <a name="test-each-method"></a>Testar cada método

O arquivo de layout e os estilos ainda não foram migrados, portanto, as exibições renderizadas só contêm o conteúdo nos arquivos de exibição. Os links gerados pelo arquivo de layout para as `About` `Contact` exibições e ainda não estarão disponíveis.

Invoque as exibições renderizadas do navegador no aplicativo ASP.NET Core em execução, substituindo o número da porta atual pelo número da porta usada no projeto do ASP.NET Core. Por exemplo: `https://localhost:44375/home/about`.

![Página de contato](mvc/_static/contact-page.png)

Observe a falta de estilos e itens de menu. O estilo será corrigido na próxima seção.

## <a name="static-content"></a>Conteúdo estático

No ASP.NET MVC 5 e versões anteriores, o conteúdo estático era hospedado da raiz do projeto Web e foi misturado com arquivos do lado do servidor. No ASP.NET Core, o conteúdo estático é hospedado no diretório *wwwroot* . Copie o conteúdo estático do aplicativo ASP.NET MVC para o diretório *wwwroot* no projeto ASP.NET Core. Nesta conversão de exemplo:

* Copie o arquivo *favicon. ico* do projeto ASP.NET MVC para o diretório *wwwroot* no projeto ASP.NET Core.

O projeto MVC do ASP.NET usa a [inicialização](https://getbootstrap.com/) para seu estilo e armazena os arquivos de inicialização nos diretórios *conteúdo* e *scripts* . O modelo, que gerou o projeto MVC do ASP.NET, faz referência à inicialização no arquivo de layout (*views/Shared/_Layout. cshtml*). Os arquivos *bootstrap.js* e *bootstrap. css* podem ser COPIADOS do projeto MVC ASP.net para o diretório *wwwroot* no novo projeto. Em vez disso, este documento adiciona suporte para bootstrap (e outras bibliotecas do lado do cliente) usando CDNs, na próxima seção.

## <a name="migrate-the-layout-file"></a>Migrar o arquivo de layout

* Copie o arquivo *_ViewStart. cshtml* do diretório *views* do projeto ASP.NET MVC para o diretório de *exibições* do projeto ASP.NET Core. O arquivo *_ViewStart. cshtml* não foi alterado no ASP.NET Core MVC.

* Crie um diretório *views/Shared* .

* *Opcional:* Copie *_ViewImports. cshtml* do diretório *views* do projeto *FullAspNetCore* MVC para o diretório de *exibições* do projeto ASP.NET Core. Remova qualquer declaração de namespace no arquivo *_ViewImports. cshtml* . O arquivo *_ViewImports. cshtml* fornece namespaces para todos os arquivos de exibição e apresenta [auxiliares de marca](xref:mvc/views/tag-helpers/intro). Os auxiliares de marca são usados no novo arquivo de layout. O arquivo *_ViewImports. cshtml* é novo para ASP.NET Core.

* Copie o arquivo *_Layout. cshtml* do diretório *views/shared* do projeto ASP.NET MVC para o diretório *compartilhado/exibições* do projeto ASP.NET Core.

Abra *_Layout arquivo. cshtml* e faça as seguintes alterações (o código completo é mostrado abaixo):

* Substituir `@Styles.Render("~/Content/css")` por um `<link>` elemento para carregar *bootstrap. css* (veja abaixo).

* Remova `@Scripts.Render("~/bundles/modernizr")`.

* Comente a `@Html.Partial("_LoginPartial")` linha (circundando a linha com `@*...*@` ). Para obter mais informações, consulte [migrar autenticação e Identity para ASP.NET Core](xref:migration/identity)

* Substituir `@Scripts.Render("~/bundles/jquery")` por um `<script>` elemento (veja abaixo).

* Substituir `@Scripts.Render("~/bundles/bootstrap")` por um `<script>` elemento (veja abaixo).

A marcação de substituição para inclusão de CSS de Bootstrap:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

A marcação de substituição para a inclusão do jQuery e do JavaScript de Bootstrap:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

O arquivo *_Layout. cshtml* atualizado é mostrado abaixo:

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Exibir o site no navegador. Agora ele deve ser carregado corretamente, com os estilos esperados em vigor.

* *Opcional:* Tente usar o novo arquivo de layout. Copie o arquivo de layout do projeto *FullAspNetCore* . O novo arquivo de layout usa [auxiliares de marca](xref:mvc/views/tag-helpers/intro) e tem outras melhorias.

## <a name="configure-bundling-and-minification"></a>Configurar agrupamento e minificação

Para obter informações sobre como configurar o agrupamento e o minificação, consulte [agrupamento e minificação](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Resolver erros HTTP 500

Há muitos problemas que podem causar mensagens de erro HTTP 500 que não contêm informações sobre a origem do problema. Por exemplo, se o arquivo *views/_ViewImports. cshtml* contiver um namespace que não exista no projeto, um erro http 500 será gerado. Por padrão, em aplicativos ASP.NET Core, a `UseDeveloperExceptionPage` extensão é adicionada ao `IApplicationBuilder` e executada quando a configuração está em *desenvolvimento*. Veja um exemplo no código a seguir:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core converte exceções sem tratamento em respostas de erro HTTP 500. Normalmente, os detalhes do erro não são incluídos nessas respostas para evitar a divulgação de informações potencialmente confidenciais sobre o servidor. Para obter mais informações, consulte a [página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

Este artigo mostra como iniciar a migração de um projeto ASP.NET MVC para [ASP.NET Core MVC](xref:mvc/overview) 2,1. No processo, ele realça muitas das coisas que foram alteradas do ASP.NET MVC. A migração do ASP.NET MVC é um processo de várias etapas. Este artigo cobre:

* Instalação inicial
* Controladores básicos e exibições
* Conteúdo estático
* Dependências do lado do cliente.

Para migrar a configuração e o Identity código, consulte [migrar a configuração para ASP.NET Core](xref:migration/configuration) e [migrar a autenticação e Identity para ASP.NET Core](xref:migration/identity).

> [!NOTE]
> Os números de versão nos exemplos podem não ser atuais, atualizar os projetos adequadamente.

## <a name="create-the-starter-aspnet-mvc-project"></a>Criar o projeto do MVC do ASP.NET do início

Para demonstrar a atualização, começaremos criando um aplicativo MVC ASP.NET. Crie-o com o nome *WebApp1* para que o namespace corresponda ao projeto ASP.NET Core criado na próxima etapa.

![Caixa de diálogo Novo Projeto do Visual Studio](mvc/_static/new-project.png)

![Caixa de diálogo novo aplicativo Web: modelo de projeto MVC selecionado no painel modelos de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Opcional:* Altere o nome da solução de *WebApp1* para *Mvc5*. O Visual Studio exibe o nome da nova solução (*Mvc5*), o que torna mais fácil dizer a esse projeto do próximo projeto.

## <a name="create-the-aspnet-core-project"></a>Criar o projeto de ASP.NET Core

Crie um novo aplicativo Web *vazio* ASP.NET Core com o mesmo nome do projeto anterior (*WebApp1*) para que os namespaces nos dois projetos correspondam. Ter o mesmo namespace torna mais fácil copiar o código entre os dois projetos. Crie este projeto em um diretório diferente do projeto anterior para usar o mesmo nome.

![Caixa de diálogo Novo Projeto](mvc/_static/new_core.png)

![Caixa de diálogo novo aplicativo Web ASP.NET: modelo de projeto vazio selecionado no painel modelos de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Opcional:* Crie um novo aplicativo ASP.NET Core usando o modelo de projeto de *aplicativo Web* . Nomeie o projeto *WebApp1*e selecione uma opção de autenticação de **contas de usuário individuais**. Renomeie este aplicativo como *FullAspNetCore*. A criação desse projeto poupa tempo na conversão. O resultado final pode ser exibido no código gerado por modelo, o código pode ser copiado para o projeto de conversão ou comparado com o projeto gerado pelo modelo.

## <a name="configure-the-site-to-use-mvc"></a>Configurar o site para usar o MVC

* Ao direcionar o .NET Core, o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) é referenciado por padrão. Este pacote contém pacotes comumente usados por aplicativos MVC. Se estiver direcionando .NET Framework, as referências de pacote deverão ser listadas individualmente no arquivo de projeto.

`Microsoft.AspNetCore.Mvc`é o ASP.NET Core MVC Framework. `Microsoft.AspNetCore.StaticFiles`é o manipulador de arquivo estático. ASP.NET Core aplicativos explicitamente optam pelo middleware, como para o fornecimento de arquivos estáticos. Para obter mais informações, veja [Arquivos estáticos](xref:fundamentals/static-files).

* Abra o arquivo *Startup.cs* e altere o código para corresponder ao seguinte:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

O <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> método de extensão adiciona o manipulador de arquivo estático. O `UseMvc` método de extensão adiciona o roteamento. Para obter mais informações, consulte inicialização e [Roteamento](xref:fundamentals/routing)de [aplicativos](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Adicionar um controlador e uma exibição

Nesta seção, um controlador e uma exibição mínimas são adicionados para servir como espaços reservados para o controlador MVC ASP.NET e exibições migradas na próxima seção.

* Adicione um diretório de *controladores* .

* Adicione uma **classe de controlador** denominada *HomeController.cs* ao diretório de *controladores* .

![Caixa de diálogo Adicionar Novo Item](mvc/_static/add_mvc_ctl.png)

* Adicione um diretório de *exibições* .

* Adicione um diretório *base views/* .

* Adicione uma ** Razor exibição** denominada *index. cshtml* ao diretório *base views/* .

![Caixa de diálogo Adicionar Novo Item](mvc/_static/view.png)

A estrutura do projeto é mostrada abaixo:

![Gerenciador de Soluções mostrando arquivos e diretórios de WebApp1](mvc/_static/project-structure-controller-view.png)

Substitua o conteúdo do arquivo *Views/Home/Index.cshtml* pela seguinte marcação:

```html
<h1>Hello world!</h1>
```

Execute o aplicativo.

![Aplicativo Web aberto no Microsoft Edge](mvc/_static/hello-world.png)

Para obter mais informações, consulte [Controllers](xref:mvc/controllers/actions) and [views](xref:mvc/views/overview).

A funcionalidade a seguir requer a migração do projeto ASP.NET MVC de exemplo para o projeto ASP.NET Core:

* conteúdo do lado do cliente (CSS, fontes e scripts)

* controladores

* Modos de exibição

* modelos

* reagrupamento

* filtros

* Faça logon/out, Identity (isso é feito no próximo tutorial.)

## <a name="controllers-and-views"></a>Controladores e exibições

* Copie cada um dos métodos do ASP.NET MVC `HomeController` para o novo `HomeController` . No ASP.NET MVC, o tipo de retorno do método de ação do controlador interno do modelo é [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); no ASP.NET Core MVC, os métodos de ação retornam `IActionResult` . `ActionResult`implementa `IActionResult` , portanto, não é necessário alterar o tipo de retorno dos métodos de ação.

* Copie os arquivos de exibição *about. cshtml*, *Contact. cshtml*e *Index. cshtml* Razor do projeto ASP.NET MVC para o projeto ASP.NET Core.

## <a name="test-each-method"></a>Testar cada método

O arquivo de layout e os estilos ainda não foram migrados, portanto, as exibições renderizadas só contêm o conteúdo nos arquivos de exibição. Os links gerados pelo arquivo de layout para as `About` `Contact` exibições e ainda não estarão disponíveis.

* Invoque as exibições renderizadas do navegador no aplicativo ASP.NET Core em execução, substituindo o número da porta atual pelo número da porta usada no projeto do ASP.NET Core. Por exemplo: `https://localhost:44375/home/about`.

![Página de contato](mvc/_static/contact-page.png)

Observe a falta de estilos e itens de menu. O estilo será corrigido na próxima seção.

## <a name="static-content"></a>Conteúdo estático

No ASP.NET MVC 5 e versões anteriores, o conteúdo estático era hospedado da raiz do projeto Web e foi misturado com arquivos do lado do servidor. No ASP.NET Core, o conteúdo estático é hospedado no diretório *wwwroot* . Copie o conteúdo estático do aplicativo ASP.NET MVC para o diretório *wwwroot* no projeto ASP.NET Core. Nesta conversão de exemplo:

* Copie o arquivo *favicon. ico* do projeto ASP.NET MVC para o diretório *wwwroot* no projeto ASP.NET Core.

O projeto MVC do ASP.NET usa a [inicialização](https://getbootstrap.com/) para seu estilo e armazena os arquivos de inicialização nos diretórios *conteúdo* e *scripts* . O modelo, que gerou o projeto MVC do ASP.NET, faz referência à inicialização no arquivo de layout (*views/Shared/_Layout. cshtml*). Os arquivos *bootstrap.js* e *bootstrap. css* podem ser COPIADOS do projeto MVC ASP.net para o diretório *wwwroot* no novo projeto. Em vez disso, este documento adiciona suporte para bootstrap (e outras bibliotecas do lado do cliente) usando CDNs, na próxima seção.

## <a name="migrate-the-layout-file"></a>Migrar o arquivo de layout

* Copie o arquivo *_ViewStart. cshtml* do diretório *views* do projeto ASP.NET MVC para o diretório de *exibições* do projeto ASP.NET Core. O arquivo *_ViewStart. cshtml* não foi alterado no ASP.NET Core MVC.

* Crie um diretório *views/Shared* .

* *Opcional:* Copie *_ViewImports. cshtml* do diretório *views* do projeto *FullAspNetCore* MVC para o diretório de *exibições* do projeto ASP.NET Core. Remova qualquer declaração de namespace no arquivo *_ViewImports. cshtml* . O arquivo *_ViewImports. cshtml* fornece namespaces para todos os arquivos de exibição e apresenta [auxiliares de marca](xref:mvc/views/tag-helpers/intro). Os auxiliares de marca são usados no novo arquivo de layout. O arquivo *_ViewImports. cshtml* é novo para ASP.NET Core.

* Copie o arquivo *_Layout. cshtml* do diretório *views/shared* do projeto ASP.NET MVC para o diretório *compartilhado/exibições* do projeto ASP.NET Core.

Abra *_Layout arquivo. cshtml* e faça as seguintes alterações (o código completo é mostrado abaixo):

* Substituir `@Styles.Render("~/Content/css")` por um `<link>` elemento para carregar *bootstrap. css* (veja abaixo).

* Remova `@Scripts.Render("~/bundles/modernizr")`.

* Comente a `@Html.Partial("_LoginPartial")` linha (circundando a linha com `@*...*@` ). Para obter mais informações, consulte [migrar autenticação e Identity para ASP.NET Core](xref:migration/identity)

* Substituir `@Scripts.Render("~/bundles/jquery")` por um `<script>` elemento (veja abaixo).

* Substituir `@Scripts.Render("~/bundles/bootstrap")` por um `<script>` elemento (veja abaixo).

A marcação de substituição para inclusão de CSS de Bootstrap:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

A marcação de substituição para a inclusão do jQuery e do JavaScript de Bootstrap:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

O arquivo *_Layout. cshtml* atualizado é mostrado abaixo:

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Exibir o site no navegador. Agora ele deve ser carregado corretamente, com os estilos esperados em vigor.

* *Opcional:* Tente usar o novo arquivo de layout. Copie o arquivo de layout do projeto *FullAspNetCore* . O novo arquivo de layout usa [auxiliares de marca](xref:mvc/views/tag-helpers/intro) e tem outras melhorias.

## <a name="configure-bundling-and-minification"></a>Configurar agrupamento e minificação

Para obter informações sobre como configurar o agrupamento e o minificação, consulte [agrupamento e minificação](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Resolver erros HTTP 500

Há muitos problemas que podem causar mensagens de erro HTTP 500 que não contêm informações sobre a origem do problema. Por exemplo, se o arquivo *views/_ViewImports. cshtml* contiver um namespace que não exista no projeto, um erro http 500 será gerado. Por padrão, em aplicativos ASP.NET Core, a `UseDeveloperExceptionPage` extensão é adicionada ao `IApplicationBuilder` e executada quando a configuração está em *desenvolvimento*. Veja um exemplo no código a seguir:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core converte exceções sem tratamento em respostas de erro HTTP 500. Normalmente, os detalhes do erro não são incluídos nessas respostas para evitar a divulgação de informações potencialmente confidenciais sobre o servidor. Para obter mais informações, consulte a [página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

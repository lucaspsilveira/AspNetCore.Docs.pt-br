---
title: IU reutilizável Razor em bibliotecas de classes com ASP.NET Core
author: Rick-Anderson
description: Explica como criar Razor uma interface do usuário reutilizável usando exibições parciais em uma biblioteca de classes no ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/ui-class
ms.openlocfilehash: 32aa1cdab0e552a1255c01b5135e9a82a0e37c77
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451895"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Criar interface do usuário reutilizável usando o Razor projeto de biblioteca de classes no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Razorexibições, páginas, controladores, modelos de página, [ Razor componentes](xref:blazor/class-libraries), [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser criados em uma Razor biblioteca de classes (RCL). A RCL pode ser e empacotada e reutilizada. Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém. Quando uma exibição, exibição parcial ou Razor página é encontrada no aplicativo Web e no RCL, a Razor marcação (arquivo *. cshtml* ) no aplicativo Web tem precedência.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Criar uma biblioteca de classes que contém a Razor interface do usuário

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No Visual Studio, selecione **criar novo um novo projeto**.
* Selecione ** Razor biblioteca de classes** > **Avançar**.
* Nomeie a biblioteca (por exemplo, "RazorClassLib"), > **criar**. Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.
* Selecione **páginas de suporte e exibições** se você precisar dar suporte a exibições. Por padrão, há Razor suporte apenas para páginas. Selecione **Criar**.

O Razor modelo de biblioteca de classes (RCL) usa como padrão o Razor desenvolvimento de componentes por padrão. A opção **páginas e exibições de suporte** oferece suporte a páginas e exibições.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Da linha de comando, execute `dotnet new razorclasslib`. Por exemplo:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

O Razor modelo de biblioteca de classes (RCL) usa como padrão o Razor desenvolvimento de componentes por padrão. Passe a `--support-pages-and-views` opção ( `dotnet new razorclasslib --support-pages-and-views` ) para fornecer suporte para páginas e exibições.

Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new). Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.

---

Adicione Razor arquivos ao RCL.

Os modelos de ASP.NET Core assumem que o conteúdo RCL está na pasta *áreas* . Consulte [layout de páginas RCL](#rcl-pages-layout) para criar um RCL que expõe o conteúdo em `~/Pages` em vez de `~/Areas/Pages` .

## <a name="reference-rcl-content"></a>Conteúdo de RCL de referência

A RCL pode ser referenciada por:

* Pacote do NuGet. Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Substituir exibições, exibições parciais e páginas

Quando uma exibição, exibição parcial ou Razor página é encontrada no aplicativo Web e no RCL, a Razor marcação (arquivo *. cshtml* ) no aplicativo Web tem precedência. Por exemplo, adicione *WebApp1/areas/MyFeature/Pages/página1. cshtml* a WebApp1, e página1 no WebApp1 terá precedência sobre PÁGINA1 no RCL.

No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.

Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Atualize a marcação para indicar o novo local. Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.

### <a name="rcl-pages-layout"></a>Layout de páginas RCL

Para fazer referência ao conteúdo do RCL como se ele fosse parte da pasta *páginas* do aplicativo Web, crie o projeto RCL com a seguinte estrutura de arquivo:

* *RazorUIClassLib/páginas*
* *RazorUIClassLib/páginas/compartilhado*

Suponha que *RazorUIClassLib/Pages/Shared* contenham dois arquivos parciais: *_Header. cshtml* e *_Footer. cshtml*. As `<partial>` marcas podem ser adicionadas ao arquivo *_Layout. cshtml* :

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>Criar um RCL com ativos estáticos

Um RCL pode exigir ativos estáticos complementares que podem ser referenciados pelo RCL ou pelo aplicativo de consumo do RCL. ASP.NET Core permite criar RCLs que incluem ativos estáticos disponíveis para um aplicativo de consumo.

Para incluir ativos complementares como parte de um RCL, crie uma pasta *wwwroot* na biblioteca de classes e inclua todos os arquivos necessários nessa pasta.

Ao empacotar um RCL, todos os ativos complementares na pasta *wwwroot* são incluídos automaticamente no pacote.

Use o `dotnet pack` comando em vez da versão NuGet. exe `nuget pack` .

### <a name="exclude-static-assets"></a>Excluir ativos estáticos

Para excluir ativos estáticos, adicione o caminho de exclusão desejado ao `$(DefaultItemExcludes)` grupo de propriedades no arquivo de projeto. Separe as entradas com um ponto e vírgula ( `;` ).

No exemplo a seguir, a folha de estilo *lib. css* na pasta *wwwroot* não é considerada um ativo estático e não está incluída no RCL publicado:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Integração do typescript

Para incluir arquivos TypeScript em um RCL:

1. Coloque os arquivos TypeScript (*. TS*) fora da pasta *wwwroot* . Por exemplo, coloque os arquivos em uma pasta de *cliente* .

1. Configure a saída de compilação do TypeScript para a pasta *wwwroot* . Defina a `TypescriptOutDir` propriedade dentro de um `PropertyGroup` no arquivo de projeto:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Inclua o destino TypeScript como uma dependência do `ResolveCurrentProjectStaticWebAssets` destino adicionando o seguinte destino dentro de um `PropertyGroup` no arquivo de projeto:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Consumir conteúdo de um RCL referenciado

Os arquivos incluídos na pasta *wwwroot* do RCL são expostos para o aplicativo RCL ou consumidor sob o prefixo `_content/{LIBRARY NAME}/` . Por exemplo, uma biblioteca chamada * Razor . Class. lib* resulta em um caminho para o conteúdo estático em `_content/Razor.Class.Lib/` . Ao produzir um pacote NuGet e o nome do assembly não for igual à ID do pacote, use a ID do pacote para `{LIBRARY NAME}` .

O aplicativo de consumo faz referência a ativos estáticos fornecidos pela biblioteca com `<script>` , `<style>` , `<img>` e outras marcas HTML. O aplicativo de consumo deve ter o [suporte a arquivo estático](xref:fundamentals/static-files) habilitado em `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Ao executar o aplicativo de consumo da saída da compilação ( `dotnet run` ), os ativos da Web estáticos são habilitados por padrão no ambiente de desenvolvimento. Para dar suporte a ativos em outros ambientes ao executar a partir da saída da compilação, chame `UseStaticWebAssets` no construtor de hosts em *Program.cs*:

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

`UseStaticWebAssets`A chamada não é necessária ao executar um aplicativo da saída publicada ( `dotnet publish` ).

### <a name="multi-project-development-flow"></a>Fluxo de desenvolvimento de vários projetos

Quando o aplicativo de consumo for executado:

* Os ativos no RCL permanecem em suas pastas originais. Os ativos não são movidos para o aplicativo de consumo.
* Qualquer alteração na pasta *wwwroot* do RCL é refletida no aplicativo de consumo depois que o RCL é recriado e sem recriar o aplicativo de consumo.

Quando o RCL é criado, é produzido um manifesto que descreve os locais estáticos de ativos da Web. O aplicativo de consumo lê o manifesto em tempo de execução para consumir os ativos de projetos e pacotes referenciados. Quando um novo ativo é adicionado a um RCL, o RCL deve ser recriado para atualizar seu manifesto antes que um aplicativo de consumo possa acessar o novo ativo.

### <a name="publish"></a>Publicar

Quando o aplicativo é publicado, os ativos complementares de todos os projetos e pacotes referenciados são copiados para a pasta *wwwroot* do aplicativo publicado em `_content/{LIBRARY NAME}/` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razorexibições, páginas, controladores, modelos de página, [ Razor componentes](xref:blazor/class-libraries), [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser criados em uma Razor biblioteca de classes (RCL). A RCL pode ser e empacotada e reutilizada. Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém. Quando uma exibição, exibição parcial ou Razor página é encontrada no aplicativo Web e no RCL, a Razor marcação (arquivo *. cshtml* ) no aplicativo Web tem precedência.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Criar uma biblioteca de classes que contém a Razor interface do usuário

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Selecione **Aplicativo Web ASP.NET Core**.
* Dê um nome à biblioteca (por exemplo, "RazorClassLib") > **OK**. Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.
* Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.
* Selecione ** Razor biblioteca de classes** > **OK**.

Um RCL tem o seguinte arquivo de projeto:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Da linha de comando, execute `dotnet new razorclasslib`. Por exemplo:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new). Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.

---

Adicione Razor arquivos ao RCL.

Os modelos de ASP.NET Core assumem que o conteúdo RCL está na pasta *áreas* . Consulte [layout de páginas RCL](#rcl-pages-layout) para criar um RCL que expõe o conteúdo em `~/Pages` em vez de `~/Areas/Pages` .

## <a name="reference-rcl-content"></a>Conteúdo de RCL de referência

A RCL pode ser referenciada por:

* Pacote do NuGet. Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Walkthrough: criar um projeto RCL e usar de um Razor projeto de páginas

Você pode baixar o [projeto completo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) e testá-lo em vez de criá-lo. O download de exemplo contém um código adicional e links que facilitam o teste do projeto. Você pode deixar comentários [nesse problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098) com suas opiniões sobre os exemplos de download em relação às instruções passo a passo.

### <a name="test-the-download-app"></a>Testar o aplicativo de download

Se você não tiver baixado o aplicativo concluído e preferir criar o projeto do passo a passo, vá para a [próxima seção](#create-an-rcl).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Abra o arquivo *.sln* no Visual Studio. Execute o aplicativo.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Em um prompt de comando no diretório *cli*, crie a RCL e o aplicativo Web.

```dotnetcli
dotnet build
```

Acesse o diretório *WebApp1* e execute o aplicativo:

```dotnetcli
dotnet run
```

---

Siga as instruções em [Testar WebApp1](#test-webapp1)

## <a name="create-an-rcl"></a>Criar um RCL

Nesta seção, um RCL é criado. Razoros arquivos são adicionados ao RCL.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Crie o projeto da RCL:

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Selecione **Aplicativo Web ASP.NET Core**.
* Nomeie o aplicativo **RazorUIClassLib** como > **OK**.
* Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.
* Selecione ** Razor biblioteca de classes** > **OK**.
* Adicione um Razor arquivo de exibição parcial chamado *RazorUIClassLib/areas/MyFeature/Pages/Shared/_Message. cshtml*.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Na linha de comando, execute o seguinte:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Os comandos anteriores:

* Cria o `RazorUIClassLib` RCL.
* Cria uma Razor página _Message e a adiciona ao RCL. O parâmetro `-np` cria a página sem um `PageModel`.
* Cria um arquivo [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) e o adiciona ao RCL.

O arquivo *_ViewStart. cshtml* é necessário para usar o layout do Razor projeto de páginas (que é adicionado na próxima seção).

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Adicionar Razor arquivos e pastas ao projeto

* Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* pelo código a seguir:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* pelo código a seguir:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` é necessário para usar a exibição parcial (`<partial name="_Message" />`). Em vez de incluir a diretiva `@addTagHelper`, você pode adicionar um arquivo *_ViewImports.cshtml*. Por exemplo:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  Para obter mais informações sobre o *_ViewImports. cshtml*, consulte [importando diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives)

* Crie a biblioteca de classes para verificar se não há nenhum erro de compilador:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

A saída do build contém *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*. *RazorUIClassLib. views. dll* contém o Razor conteúdo compilado.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Usar a Razor biblioteca de interface do usuário de um Razor projeto de páginas

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Criar o Razor aplicativo Web de páginas:

* No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução > **Adicionar** >  **Novo Projeto**.
* Selecione **Aplicativo Web ASP.NET Core**.
* Nomeie o aplicativo como **WebApp1**.
* Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.
* Selecione **Aplicativo Web** > **OK**.

* No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Definir como projeto de inicialização**.
* No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Dependências de Build** > **Dependências do Projeto**.
* Marque **RazorUIClassLib** como uma dependência de **WebApp1**.
* No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Adicionar** > **Referência**.
* Na caixa de diálogo **Gerenciador de Referências**, marque **RazorUIClassLib** > **OK**.

Execute o aplicativo.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Crie um Razor aplicativo Web de páginas e um arquivo de solução contendo o Razor aplicativo Pages e o RCL:

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Crie e execute o aplicativo Web:

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>Testar o WebApp1

Navegue até `/MyFeature/Page1` para verificar se a Razor biblioteca de classes da interface do usuário está em uso.

## <a name="override-views-partial-views-and-pages"></a>Substituir exibições, exibições parciais e páginas

Quando uma exibição, exibição parcial ou Razor página é encontrada no aplicativo Web e no RCL, a Razor marcação (arquivo *. cshtml* ) no aplicativo Web tem precedência. Por exemplo, adicione *WebApp1/areas/MyFeature/Pages/página1. cshtml* a WebApp1, e página1 no WebApp1 terá precedência sobre PÁGINA1 no RCL.

No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.

Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Atualize a marcação para indicar o novo local. Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.

### <a name="rcl-pages-layout"></a>Layout de páginas RCL

Para fazer referência ao conteúdo do RCL como se ele fosse parte da pasta *páginas* do aplicativo Web, crie o projeto RCL com a seguinte estrutura de arquivo:

* *RazorUIClassLib/páginas*
* *RazorUIClassLib/páginas/compartilhado*

Suponha que *RazorUIClassLib/Pages/Shared* contenham dois arquivos parciais: *_Header. cshtml* e *_Footer. cshtml*. As `<partial>` marcas podem ser adicionadas ao arquivo *_Layout. cshtml* :

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/class-libraries>

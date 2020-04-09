---
title: Interface do usuário do Razor reutilizável em bibliotecas de classes com o ASP.NET Core
author: Rick-Anderson
description: Explica como criar a ID navalha reutilizável usando visualizações parciais em uma biblioteca de classes em ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667562"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Crie iU reutilizável usando o projeto de biblioteca da classe Razor no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Visualizações de navalha, páginas, controladores, modelos de página, [componentes de navalha,](xref:blazor/class-libraries) [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser incorporados em uma biblioteca de classe Razor (RCL). A RCL pode ser e empacotada e reutilizada. Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém. Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Criar uma biblioteca de classes contendo a interface do usuário do Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Do Visual Studio selecione **Criar um novo projeto**.
* Selecione **Razor Class Library** > **Next**.
* Nomeie a biblioteca (por exemplo, "RazorClassLib"), > **Criar**. Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.
* Selecione **Páginas de suporte e visualizações** se você precisar apoiar exibições. Por padrão, apenas páginas de barbear são suportadas. Selecione **Criar**.

O modelo de biblioteca de classe Razor (RCL) é padrão para o desenvolvimento de componentes Razor por padrão. A opção **''''''''Opções de exibição'** suporta páginas e visualizações.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Da linha de comando, execute `dotnet new razorclasslib`. Por exemplo:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

O modelo de biblioteca de classe Razor (RCL) é padrão para o desenvolvimento de componentes Razor por padrão. Passe `--support-pages-and-views` a`dotnet new razorclasslib --support-pages-and-views`opção ( ) para fornecer suporte para páginas e visualizações.

Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new). Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.

---

Adicione arquivos Razor na RCL.

Os modelos ASP.NET Core assumem que o conteúdo RCL está na pasta *Áreas.* Consulte [o layout rcl pages](#rcl-pages-layout) para criar `~/Pages` uma `~/Areas/Pages`RCL que exponha o conteúdo em vez de .

## <a name="reference-rcl-content"></a>Conteúdo RCL de referência

A RCL pode ser referenciada por:

* Pacote do NuGet. Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Substituir exibições, exibições parciais e páginas

Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência. Por exemplo, adicione *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* ao WebApp1 e a Página1 no WebApp1 terá precedência sobre a Page1 na RCL.

No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.

Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Atualize a marcação para indicar o novo local. Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.

### <a name="rcl-pages-layout"></a>Layout de páginas RCL

Para referenciar o conteúdo rcl como se fosse parte da pasta *Páginas* do aplicativo web, crie o projeto RCL com a seguinte estrutura de arquivo:

* *RazoruiClassLib/Páginas*
* *RazorUIClassLib/Pages/Shared*

Suponha que *RazorUIClassLib/Pages/Shared* contém dois arquivos *parciais: _Header.cshtml* e *_Footer.cshtml*. As `<partial>` tags podem ser adicionadas ao arquivo *_Layout.cshtml:*

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>Crie uma RCL com ativos estáticos

Uma RCL pode exigir ativos estáticos companheiros que podem ser referenciados pela RCL ou pelo aplicativo de consumo da RCL. ASP.NET Core permite criar RCLs que incluem ativos estáticos que estão disponíveis para um aplicativo consumidor.

Para incluir ativos complementares como parte de uma RCL, crie uma pasta *wwwroot* na biblioteca de classes e inclua quaisquer arquivos necessários nessa pasta.

Ao embalar uma RCL, todos os ativos complementares na pasta *wwwroot* são automaticamente incluídos no pacote.

### <a name="exclude-static-assets"></a>Excluir ativos estáticos

Para excluir ativos estáticos, adicione `$(DefaultItemExcludes)` o caminho de exclusão desejado ao grupo de propriedades no arquivo do projeto. Entradas separadas com`;`ponto e vírgula ( ).

No exemplo a seguir, a folha de estilos *lib.css* na pasta *wwwroot* não é considerada um ativo estático e não está incluída na RCL publicada:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Integração de scripts de máquinas de escrever

Para incluir arquivos TypeScript em uma RCL:

1. Coloque os arquivos TypeScript *(.ts)* fora da pasta *wwwroot.* Por exemplo, coloque os arquivos em uma pasta *Cliente.*

1. Configure a saída de compilação TypeScript para a pasta *wwwroot.* Defina `TypescriptOutDir` a propriedade `PropertyGroup` dentro de um no arquivo do projeto:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Inclua o destino TypeScript como `ResolveCurrentProjectStaticWebAssets` uma dependência do destino adicionando o seguinte destino dentro de um `PropertyGroup` arquivo do projeto:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Consumir conteúdo de uma RCL referenciada

Os arquivos incluídos na pasta *wwwroot* da RCL são expostos à RCL `_content/{LIBRARY NAME}/`ou ao aplicativo de consumo sob o prefixo . Por exemplo, uma biblioteca chamada *Razor.Class.Lib* resulta `_content/Razor.Class.Lib/`em um caminho para conteúdo estático em . Ao produzir um pacote NuGet e o nome de montagem não é o `{LIBRARY NAME}`mesmo que o ID do pacote, use o ID do pacote para .

O aplicativo de consumo faz referência aos `<script>`ativos `<style>` `<img>`estáticos fornecidos pela biblioteca com , e outras tags HTML. O aplicativo consumidor deve ter suporte `Startup.Configure`a arquivos [estáticos](xref:fundamentals/static-files) ativado em:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Ao executar o aplicativo de`dotnet run`consumo a partir da saída de compilação (), os ativos web estáticos são ativados por padrão no ambiente Desenvolvimento. Para suportar ativos em outros ambientes ao `UseStaticWebAssets` executar a partir da saída de compilação, chame o construtor de host em *Program.cs*:

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

A `UseStaticWebAssets` chamada não é necessária ao executar`dotnet publish`um aplicativo a partir da saída publicada ( ).

### <a name="multi-project-development-flow"></a>Fluxo de desenvolvimento de vários projetos

Quando o aplicativo de consumo é executado:

* Os ativos da RCL permanecem em suas pastas originais. Os ativos não são movidos para o aplicativo de consumo.
* Qualquer alteração dentro da pasta *wwwroot* da RCL é refletida no aplicativo de consumo depois que a RCL é reconstruída e sem reconstruir o aplicativo de consumo.

Quando a RCL é construída, um manifesto é produzido que descreve os locais estáticos do ativo web. O aplicativo de consumo lê o manifesto em tempo de execução para consumir os ativos de projetos e pacotes referenciados. Quando um novo ativo é adicionado a uma RCL, a RCL deve ser reconstruída para atualizar seu manifesto antes que um aplicativo consumidor possa acessar o novo ativo.

### <a name="publish"></a>Publicar

Quando o aplicativo é publicado, os ativos complementares de todos os projetos e pacotes `_content/{LIBRARY NAME}/`referenciados são copiados para a pasta *wwwroot* do aplicativo publicado em .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Visualizações de navalha, páginas, controladores, modelos de página, [componentes de navalha,](xref:blazor/class-libraries) [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser incorporados em uma biblioteca de classe Razor (RCL). A RCL pode ser e empacotada e reutilizada. Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém. Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Criar uma biblioteca de classes contendo a interface do usuário do Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Selecione **Aplicativo Web ASP.NET Core**.
* Dê um nome à biblioteca (por exemplo, "RazorClassLib") > **OK**. Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.
* Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.
* Selecione **Biblioteca de Classes Razor** > **OK**.

Uma RCL tem o seguinte arquivo de projeto:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Da linha de comando, execute `dotnet new razorclasslib`. Por exemplo:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new). Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.

---

Adicione arquivos Razor na RCL.

Os modelos ASP.NET Core assumem que o conteúdo RCL está na pasta *Áreas.* Consulte [o layout rcl pages](#rcl-pages-layout) para criar `~/Pages` uma `~/Areas/Pages`RCL que exponha o conteúdo em vez de .

## <a name="reference-rcl-content"></a>Conteúdo RCL de referência

A RCL pode ser referenciada por:

* Pacote do NuGet. Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*. Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Passo a passo: Crie um projeto de RCL e use a partir de um projeto Razor Pages

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

## <a name="create-an-rcl"></a>Criar uma RCL

Nesta seção, uma RCL é criada. Arquivos Razor são adicionados à RCL.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Crie o projeto da RCL:

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Selecione **Aplicativo Web ASP.NET Core**.
* Nomeie o aplicativo **RazorUIClassLib** > **OK**.
* Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.
* Selecione **Biblioteca de Classes Razor** > **OK**.
* Adicione um arquivo Razor de exibição parcial chamado *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Na linha de comando, execute o seguinte:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Os comandos anteriores:

* Cria `RazorUIClassLib` a RCL.
* Criam uma página Razor _Message e a adicionam à RCL. O parâmetro `-np` cria a página sem um `PageModel`.
* Cria um arquivo [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) e adiciona-o à RCL.

O arquivo *_ViewStart.cshtml* é necessário para usar o layout do projeto Páginas de Barbear (que é adicionado na próxima seção).

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Adicionar arquivos e pastas razor ao projeto

* Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* pelo código a seguir:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* pelo código a seguir:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` é necessário para usar a exibição parcial (`<partial name="_Message" />`). Em vez de incluir a diretiva `@addTagHelper`, você pode adicionar um arquivo *_ViewImports.cshtml*. Por exemplo:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  Para obter mais informações sobre *_ViewImports.cshtml*, consulte [Importar Diretivas Compartilhadas](xref:mvc/views/layout#importing-shared-directives)

* Crie a biblioteca de classes para verificar se não há nenhum erro de compilador:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

A saída do build contém *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* contém o conteúdo Razor compilado.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Use a biblioteca da interface do usuário do Razor de um projeto Páginas Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Crie aplicativo Web Páginas Razor:

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

Crie um aplicativo web Razor Pages e um arquivo de solução contendo o aplicativo Razor Pages e o RCL:

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

Navegue `/MyFeature/Page1` para verificar se a biblioteca de classe Razor UI está em uso.

## <a name="override-views-partial-views-and-pages"></a>Substituir exibições, exibições parciais e páginas

Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência. Por exemplo, adicione *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* ao WebApp1 e a Página1 no WebApp1 terá precedência sobre a Page1 na RCL.

No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.

Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Atualize a marcação para indicar o novo local. Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.

### <a name="rcl-pages-layout"></a>Layout de páginas RCL

Para referenciar o conteúdo rcl como se fosse parte da pasta *Páginas* do aplicativo web, crie o projeto RCL com a seguinte estrutura de arquivo:

* *RazoruiClassLib/Páginas*
* *RazorUIClassLib/Pages/Shared*

Suponha que *RazorUIClassLib/Pages/Shared* contém dois arquivos *parciais: _Header.cshtml* e *_Footer.cshtml*. As `<partial>` tags podem ser adicionadas ao arquivo *_Layout.cshtml:*

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

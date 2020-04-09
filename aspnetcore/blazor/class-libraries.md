---
title: bibliotecas de classe de componentes do ASP.NET Core Razor
author: guardrex
description: Descubra como os componentes Blazor podem ser incluídos em aplicativos de uma biblioteca de componentes externos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218760"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>bibliotecas de classe de componentes do ASP.NET Core Razor

Por [Simon Timms](https://github.com/stimms)

Os componentes podem ser compartilhados em uma [biblioteca de classe Razor (RCL)](xref:razor-pages/ui-class) entre projetos. Uma *biblioteca de classe de componentes razor* pode ser incluída a partir de:

* Outro projeto na solução.
* Um pacote NuGet.
* Uma biblioteca .NET referenciada.

Assim como os componentes são tipos .NET regulares, os componentes fornecidos por uma RCL são conjuntos .NET normais.

## <a name="create-an-rcl"></a>Criar uma RCL

Siga a orientação no <xref:blazor/get-started> artigo para configurar seu ambiente para Blazor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Criar um novo projeto.
1. Selecione **Biblioteca de Classe de Barbear**. Selecione **Avançar**.
1. No Criar uma nova sala de diálogo **de biblioteca de classe Razor,** selecione **Criar**.
1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Os exemplos neste tópico usam `MyComponentLib1`o nome do projeto . Selecione **Criar**.
1. Adicione a RCL a uma solução:
   1. Clique com o botão direito do mouse na solução. Selecione **Adicionar** > **projeto existente**.
   1. Navegue até o arquivo de projeto da RCL.
   1. Selecione o arquivo de projeto da RCL *(.csproj).*
1. Adicione uma referência ao RCL do aplicativo:
   1. Clique com o botão direito do mouse no projeto do aplicativo. Selecione **Adicionar** > **referência**.
   1. Selecione o projeto RCL. Selecione **OK**.

> [!NOTE]
> Se as páginas de suporte e a caixa **de seleção de visualizações** forem selecionadas ao gerar o RCL a partir do modelo, adicione também um arquivo *_Imports.razor* à raiz do projeto gerado com os seguintes conteúdos para habilitar a autoria do componente Razor:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Adicione manualmente o arquivo a raiz do projeto gerado.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

1. Use o modelo razor`razorclasslib`class **library** ( ) com o novo comando [dotnet](/dotnet/core/tools/dotnet-new) em um shell de comando. No exemplo a seguir, uma RCL é criada chamada `MyComponentLib1`. A pasta `MyComponentLib1` que é desmemoda é criada automaticamente quando o comando é executado:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Se `-s|--support-pages-and-views` o switch for usado ao gerar o RCL a partir do modelo, adicione também um arquivo *_Imports.razor* à raiz do projeto gerado com os seguintes conteúdos para habilitar a autoria do componente Razor:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Adicione manualmente o arquivo a raiz do projeto gerado.

1. Para adicionar a biblioteca a um projeto existente, use o comando [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) em um shell de comando. No exemplo a seguir, a RCL é adicionada ao aplicativo. Execute o seguinte comando da pasta de projeto do aplicativo com o caminho para a biblioteca:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Consumir um componente de biblioteca

Para consumir componentes definidos em uma biblioteca em outro projeto, use qualquer uma das seguintes abordagens:

* Use o nome completo do tipo com o namespace.
* Use razor's [ \@usando](xref:mvc/views/razor#using) diretiva. Componentes individuais podem ser adicionados pelo nome.

Nos exemplos a `MyComponentLib1` seguir, está uma `SalesReport` biblioteca de componentes contendo um componente.

O `SalesReport` componente pode ser referenciado usando seu nome de tipo completo com namespace:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

O componente também pode ser referenciado se a `@using` biblioteca for colocada em escopo com uma diretiva:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Inclua `@using MyComponentLib1` a diretiva no arquivo *_Import.razor* de nível superior para disponibilizar os componentes da biblioteca para um projeto inteiro. Adicione a diretiva a um arquivo *_Import.razor* em qualquer nível para aplicar o namespace a uma única página ou conjunto de páginas dentro de uma pasta.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Crie uma biblioteca de classes de componentes razor com ativos estáticos

Uma RCL pode incluir ativos estáticos. Os ativos estáticos estão disponíveis para qualquer aplicativo que consuma a biblioteca. Para obter mais informações, consulte <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>Construa, embale e embale para NuGet

Como as bibliotecas de componentes são bibliotecas padrão .NET, empacotá-las e enviá-las para o NuGet não é diferente de embalagem e envio de qualquer biblioteca para nuGet. A embalagem é realizada usando o comando [dotnet pack](/dotnet/core/tools/dotnet-pack) em um shell de comando:

```dotnetcli
dotnet pack
```

Faça upload do pacote para NuGet usando o comando [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) em um shell de comando.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-pages/ui-class>
* [Adicione um arquivo de configuração do linker XML a uma biblioteca](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

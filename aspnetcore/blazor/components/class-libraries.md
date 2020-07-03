---
title: RazorBibliotecas de classes de componentes ASP.NET Core
author: guardrex
description: Descubra como os componentes podem ser incluídos em Blazor aplicativos de uma biblioteca de componentes externos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: b172059407f9a08dacc0fadd804864c7aee7fb90
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944501"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>RazorBibliotecas de classes de componentes ASP.NET Core

Por [Simon Timms](https://github.com/stimms)

Os componentes podem ser compartilhados em uma [ Razor biblioteca de classes (RCL)](xref:razor-pages/ui-class) em projetos. Uma * Razor biblioteca de classes de componentes* pode ser incluída em:

* Outro projeto na solução.
* Um pacote NuGet.
* Uma biblioteca .NET referenciada.

Assim como os componentes são tipos .NET regulares, os componentes fornecidos por um RCL são assemblies normais do .NET.

## <a name="create-an-rcl"></a>Criar um RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Criar um novo projeto.
1. Selecione ** Razor biblioteca de classes**. Selecione **Avançar**.
1. Na caixa de diálogo **criar uma nova Razor biblioteca de classes** , selecione **criar**.
1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Os exemplos neste tópico usam o nome do projeto `MyComponentLib1` . Selecione **Criar**.
1. Adicione o RCL a uma solução:
   1. Clique com o botão direito do mouse na solução. Selecione **Adicionar**  >  **projeto existente**.
   1. Navegue até o arquivo de projeto do RCL.
   1. Selecione o arquivo de projeto do RCL ( `.csproj` ).
1. Adicione uma referência ao RCL do aplicativo:
   1. Clique com o botão direito do mouse no projeto do aplicativo. Selecione **Adicionar**  >  **referência**.
   1. Selecione o projeto RCL. Selecione **OK**.

> [!NOTE]
> Se a caixa de seleção **páginas de suporte e exibições** estiver marcada ao gerar o RCL do modelo, adicione também um `_Imports.razor` arquivo à raiz do projeto gerado com o seguinte conteúdo para habilitar a Razor criação de componentes:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Adicione manualmente o arquivo a raiz do projeto gerado.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

1. Use o modelo de ** Razor biblioteca de classes** ( `razorclasslib` ) com o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando em um shell de comando. No exemplo a seguir, um RCL é criado com o nome `MyComponentLib1` . A pasta que contém `MyComponentLib1` é criada automaticamente quando o comando é executado:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Se a `-s|--support-pages-and-views` opção for usada ao gerar o RCL do modelo, adicione também um `_Imports.razor` arquivo à raiz do projeto gerado com o seguinte conteúdo para habilitar a criação de Razor componentes:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Adicione manualmente o arquivo a raiz do projeto gerado.

1. Para adicionar a biblioteca a um projeto existente, use o [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) comando em um shell de comando. No exemplo a seguir, o RCL é adicionado ao aplicativo. Execute o seguinte comando na pasta do projeto do aplicativo com o caminho para a biblioteca:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Consumir um componente de biblioteca

Para consumir os componentes definidos em uma biblioteca em outro projeto, use uma das seguintes abordagens:

* Use o nome completo do tipo com o namespace.
* Use Razor a [`@using`](xref:mvc/views/razor#using) diretiva do. Componentes individuais podem ser adicionados por nome.

Nos exemplos a seguir, `MyComponentLib1` é uma biblioteca de componentes que contém um `SalesReport` componente.

O `SalesReport` componente pode ser referenciado usando seu nome de tipo completo com namespace:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

O componente também poderá ser referenciado se a biblioteca for colocada no escopo com uma `@using` diretiva:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Inclua a `@using MyComponentLib1` diretiva no arquivo de nível superior `_Import.razor` para disponibilizar os componentes da biblioteca para um projeto inteiro. Adicione a diretiva a um `_Import.razor` arquivo em qualquer nível para aplicar o namespace a uma única página ou a um conjunto de páginas dentro de uma pasta.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Criar uma Razor biblioteca de classes de componentes com ativos estáticos

Um RCL pode incluir ativos estáticos. Os ativos estáticos estão disponíveis para qualquer aplicativo que consuma a biblioteca. Para obter mais informações, consulte <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>Compilar, empacotar e enviar para o NuGet

Como as bibliotecas de componentes são bibliotecas padrão do .NET, o empacotamento e o envio deles para o NuGet não é diferente do empacotamento e do envio de nenhuma biblioteca para o NuGet. O empacotamento é executado usando o [`dotnet pack`](/dotnet/core/tools/dotnet-pack) comando em um shell de comando:

```dotnetcli
dotnet pack
```

Carregue o pacote no NuGet usando o [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) comando em um shell de comando.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-pages/ui-class>
* [Adicionar um arquivo de configuração do vinculador XML a uma biblioteca](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

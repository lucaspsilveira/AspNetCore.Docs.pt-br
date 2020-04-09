---
title: ASP.NET Blazor Core gestão estadual
author: guardrex
description: Aprenda a persistir Blazor o estado em aplicativos do Servidor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218863"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a>ASP.NET Blazor Core gestão estadual

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorServer é uma estrutura de aplicativo imponente. Na maioria das vezes, o aplicativo mantém uma conexão contínua com o servidor. O estado do usuário é mantido na memória do servidor em um *circuito*. 

Exemplos de estado mantidos para o circuito de um usuário incluem:

* A ui&mdash;renderizada a hierarquia de instâncias componentes e sua saída de renderização mais recente.
* Os valores de quaisquer campos e propriedades em instâncias componentes.
* Dados mantidos em instâncias de serviço [de injeção de dependência (DI)](xref:fundamentals/dependency-injection) que são escopo para o circuito.

> [!NOTE]
> Este artigo aborda a Blazor persistência do estado em aplicativos do Servidor. BlazorOs aplicativos WebAssembly podem tirar proveito da persistência do estado do lado do [cliente no navegador,](#client-side-in-the-browser) mas exigem soluções personalizadas ou pacotes de terceiros além do escopo deste artigo.

## <a name="opno-locblazor-circuits"></a>BlazorCircuitos

Se um usuário tiver uma Blazor perda temporária de conexão de rede, tente reconectar o usuário ao seu circuito original para que ele possa continuar a usar o aplicativo. No entanto, nem sempre é possível reconectar um usuário ao seu circuito original na memória do servidor:

* O servidor não pode reter um circuito desconectado para sempre. O servidor deve liberar um circuito desconectado após um intervalo ou quando o servidor estiver sob pressão de memória.
* Em ambientes de implantação multiservidor e balanceados de carga, quaisquer solicitações de processamento de servidor podem ficar indisponíveis a qualquer momento. Servidores individuais podem falhar ou ser removidos automaticamente quando não forem mais necessários para lidar com o volume total de solicitações. O servidor original pode não estar disponível quando o usuário tentar se reconectar.
* O usuário pode fechar e reabrir seu navegador ou recarregar a página, que remove qualquer estado mantido na memória do navegador. Por exemplo, os valores definidos através de chamadas interop JavaScript são perdidos.

Quando um usuário não pode ser reconectado ao seu circuito original, o usuário recebe um novo circuito com um estado vazio. Isso equivale a fechar e reabrir um aplicativo de desktop.

## <a name="preserve-state-across-circuits"></a>Preservar o estado em circuitos

Em alguns cenários, preservar o estado entre circuitos é desejável. Um aplicativo pode reter dados importantes para um usuário se:

* O servidor web fica indisponível.
* O navegador do usuário é forçado a iniciar um novo circuito com um novo servidor web.

Em geral, manter o estado entre circuitos se aplica a cenários onde os usuários estão criando dados ativamente, não simplesmente lendo dados que já existem.

Para preservar o estado além de um único circuito, *não apenas armazene os dados na memória do servidor*. O aplicativo deve persistir os dados em algum outro local de armazenamento. A persistência do estado&mdash;não é automática, você deve tomar medidas ao desenvolver o aplicativo para implementar a persistência de dados imponentes.

A persistência de dados é normalmente necessária apenas para um estado de alto valor que os usuários gastaram esforço para criar. Nos exemplos a seguir, o estado persistente economiza tempo ou auxilia em atividades comerciais:

* Webform multistep &ndash; É demorado para um usuário reinserir dados para várias etapas concluídas de um processo multipasso se seu estado for perdido. Um usuário perde o estado neste cenário se navegar para longe da forma multipasso e retornar ao formulário mais tarde.
* Carrinho &ndash; de compras Qualquer componente comercialmente importante de um aplicativo que represente receita potencial pode ser mantido. Um usuário que perde seu estado e, portanto, seu carrinho de compras, pode comprar menos produtos ou serviços quando retornar ao site mais tarde.

Normalmente não é necessário preservar o estado facilmente recriado, como o nome de usuário inserido em uma caixa de diálogo de login que não foi enviada.

> [!IMPORTANT]
> Um aplicativo só pode persistir *o estado do aplicativo*. UIs não podem ser persistidos, como instâncias de componentes e suas árvores de renderização. Componentes e árvores de renderização não são geralmente serializáveis. Para persistir algo semelhante ao estado de UI, como os nódulos expandidos de um TreeView, o aplicativo deve ter código personalizado para modelar o comportamento como estado de aplicativo serializável.

## <a name="where-to-persist-state"></a>Onde persistir estado

Existem três locais comuns Blazor para o estado persistente em um aplicativo server. Cada abordagem é mais adequada para diferentes cenários e tem diferentes ressalvas:

* [Lado do servidor em um banco de dados](#server-side-in-a-database)
* [URL](#url)
* [Lado do cliente no navegador](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Lado do servidor em um banco de dados

Para persistência permanente de dados ou para quaisquer dados que devem abranger vários usuários ou dispositivos, um banco de dados independente do lado do servidor é quase certamente a melhor escolha. As opções incluem:

* Banco de dados SQL relacional
* Repositório de valor-chave
* Loja Blob
* Loja de mesa

Depois que os dados são salvos no banco de dados, um novo circuito pode ser iniciado por um usuário a qualquer momento. Os dados do usuário são retidos e disponíveis em qualquer novo circuito.

Para obter mais informações sobre as opções de armazenamento de dados do Azure, consulte a Documentação de armazenamento do [Azure](/azure/storage/) e [os bancos de dados do Azure](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>URL

Para dados transitórios que representam o estado de navegação, modele os dados como parte da URL. Exemplos de estado modelado na URL incluem:

* O ID de uma entidade vista.
* O número da página atual em uma grade bicada.

O conteúdo da barra de endereços do navegador é retido:

* Se o usuário recarregar manualmente a página.
* Se o servidor&mdash;web ficar indisponível, o usuário será forçado a recarregar a página para se conectar a um servidor diferente.

Para obter informações sobre `@page` como definir <xref:blazor/routing>padrões de URL com a diretiva, consulte .

### <a name="client-side-in-the-browser"></a>Lado do cliente no navegador

Para dados transitórios que o usuário está criando ativamente, `localStorage` uma `sessionStorage` loja de backup comum é a do navegador e as coleções. O aplicativo não é necessário para gerenciar ou limpar o estado armazenado se o circuito for abandonado, o que é uma vantagem sobre o armazenamento do lado do servidor.

> [!NOTE]
> "Lado do cliente" nesta seção refere-se a cenários do lado do cliente no navegador, não ao [ Blazor modelo de hospedagem WebAssembly](xref:blazor/hosting-models#blazor-webassembly). `localStorage`e `sessionStorage` pode ser Blazor usado em aplicativos WebAssembly, mas apenas escrevendo código personalizado ou usando um pacote de terceiros.

`localStorage`e `sessionStorage` diferem da seguinte forma:

* `localStorage`é escopo para o navegador do usuário. Se o usuário recarregar a página ou fechar e reabrir o navegador, o estado persistirá. Se o usuário abrir várias guias do navegador, o estado será compartilhado entre as guias. Os dados `localStorage` persistem até serem explicitamente limpos.
* `sessionStorage`é escopo para a guia do navegador do usuário. Se o usuário recarregar a guia, o estado persistirá. Se o usuário fechar a guia ou o navegador, o estado será perdido. Se o usuário abrir várias guias do navegador, cada guia terá sua própria versão independente dos dados.

Geralmente, `sessionStorage` é mais seguro usar. `sessionStorage`evita o risco de que um usuário abra várias guias e encontre o seguinte:

* Bugs no armazenamento do estado em guias.
* Comportamento confuso quando uma guia substitui o estado de outras guias.

`localStorage`é a melhor escolha se o aplicativo deve persistir o estado através do fechamento e reabertura do navegador.

Ressalvas para o uso do armazenamento do navegador:

* Semelhante ao uso de um banco de dados do lado do servidor, o carregamento e a salvação de dados são assíncronos.
* Ao contrário de um banco de dados do lado do servidor, o armazenamento não está disponível durante a pré-renderização porque a página solicitada não existe no navegador durante a fase de pré-renderização.
* O armazenamento de alguns kilobytes de Blazor dados é razoável para persistir para aplicativos do Server. Além de alguns kilobytes, você deve considerar as implicações de desempenho porque os dados são carregados e salvos em toda a rede.
* Os usuários podem visualizar ou adulterar os dados. ASP.NET [Proteção de Dados Do](xref:security/data-protection/introduction) Núcleo pode mitigar o risco.

## <a name="third-party-browser-storage-solutions"></a>Soluções de armazenamento de navegador de terceiros

Pacotes NuGet de terceiros fornecem APIs para trabalhar com `localStorage` e `sessionStorage`.

Vale a pena considerar a escolha de um pacote que use de forma transparente ASP.NET [Proteção](xref:security/data-protection/introduction)de Dados do Core. ASP.NET O Core Data Protection criptografa os dados armazenados e reduz o risco potencial de adulteração de dados armazenados. Se os dados serializados do JSON forem armazenados em texto simples, os usuários poderão ver os dados usando ferramentas de desenvolvedor do navegador e também modificar os dados armazenados. Proteger dados nem sempre é um problema porque os dados podem ser triviais por natureza. Por exemplo, ler ou modificar a cor armazenada de um elemento de ida e preço não é um risco significativo de segurança para o usuário ou a organização. Evite permitir que os usuários inspecionem ou alterem *dados confidenciais.*

## <a name="protected-browser-storage-experimental-package"></a>Pacote experimental de armazenamento de navegador protegido

Um exemplo de um pacote NuGet `localStorage` `sessionStorage` que fornece [proteção](xref:security/data-protection/introduction) de dados para e é [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`é um pacote experimental sem suporte inadequado para uso de produção neste momento.

### <a name="installation"></a>Instalação

Para instalar `Microsoft.AspNetCore.ProtectedBrowserStorage` o pacote:

1. No Blazor projeto do aplicativo Server, adicione uma referência de pacote ao [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).
1. No HTML de nível superior (por exemplo, no arquivo *Páginas/_Host.cshtml* no `<script>` modelo padrão do projeto), adicione a seguinte tag:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. No `Startup.ConfigureServices` método, `AddProtectedBrowserStorage` chamada `localStorage` para `sessionStorage` adicionar e serviços à coleta de serviços:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Salvar e carregar dados dentro de um componente

Em qualquer componente que exija carregar ou [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) salvar dados no armazenamento do navegador, use para injetar uma instância de qualquer um dos seguintes:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

A escolha depende de qual loja de apoio você deseja usar. No exemplo a `sessionStorage` seguir, é usado:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

A `@using` declaração pode ser colocada em um arquivo *_Imports.razor* em vez de no componente. O uso do arquivo *_Imports.razor* disponibiliza o namespace para segmentos maiores do aplicativo ou de todo o aplicativo.

Para persistir `_currentCount` o `Counter` valor no componente do `IncrementCount` modelo do `ProtectedSessionStore.SetAsync`projeto, modifique o método a ser usado:

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

Em aplicativos maiores e mais realistas, o armazenamento de campos individuais é um cenário improvável. Os aplicativos são mais propensos a armazenar objetos de modelo inteiros que incluem estado complexo. `ProtectedSessionStore`serializa e desserializa automaticamente os dados JSON.

No exemplo de código `_currentCount` anterior, `sessionStorage['count']` os dados são armazenados como no navegador do usuário. Os dados não são armazenados em texto simples, mas são protegidos usando ASP.NET [Proteção](xref:security/data-protection/introduction)de Dados do Core . Os dados criptografados `sessionStorage['count']` podem ser vistos se forem avaliados no console de desenvolvedor do navegador.

Para recuperar `_currentCount` os dados se `Counter` o usuário retornar ao componente mais tarde (incluindo `ProtectedSessionStore.GetAsync`se eles estiverem em um circuito totalmente novo), use :

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Se os parâmetros do componente `ProtectedSessionStore.GetAsync` incluem estado de `OnParametersSetAsync`navegação, `OnInitializedAsync`ligue e atribua o resultado em , não . `OnInitializedAsync`é chamado apenas uma vez quando o componente é instanciado pela primeira vez. `OnInitializedAsync`não é chamado novamente mais tarde se o usuário navegar para uma URL diferente enquanto permanece na mesma página. Para obter mais informações, consulte <xref:blazor/lifecycle>.

> [!WARNING]
> Os exemplos nesta seção só funcionam se o servidor não tiver pré-renderização ativada. Com a pré-renderização ativada, um erro é gerado semelhante a:
>
> > As chamadas interop JavaScript não podem ser emitidas no momento. Isso porque o componente está sendo pré-renderizado.
>
> Desabilite a pré-renderização ou adicione código adicional para trabalhar com a pré-renderização. Para saber mais sobre a escrita de código que funciona com a pré-renderização, consulte a seção [De pré-renderização de Lidar.](#handle-prerendering)

### <a name="handle-the-loading-state"></a>Manuseie o estado de carga

Como o armazenamento do navegador é assíncrono (acessado através de uma conexão de rede), há sempre um período de tempo antes que os dados sejam carregados e disponíveis para uso por um componente. Para obter os melhores resultados, renderize uma mensagem de estado de carga enquanto o carregamento estiver em andamento, em vez de exibir dados em branco ou padrão.

Uma abordagem é rastrear `null` se os dados estão (ainda carregando) ou não. No componente `Counter` padrão, a contagem `int`é mantida em um . Tornar `_currentCount` anulado adicionando um`?`ponto de interrogação ( ) ao tipo (`int`):

```csharp
private int? _currentCount;
```

Em vez de exibir incondicionalmente o botão contagem e **incremento,** escolha exibir esses elementos somente se os dados forem carregados:

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Lidar com a pré-renderização

Durante a pré-renderização:

* Uma conexão interativa com o navegador do usuário não existe.
* O navegador ainda não tem uma página na qual pode executar o código JavaScript.

`localStorage`ou `sessionStorage` não estão disponíveis durante a pré-renderização. Se o componente tentar interagir com o armazenamento, um erro será gerado semelhante a:

> As chamadas interop JavaScript não podem ser emitidas no momento. Isso porque o componente está sendo pré-renderizado.

Uma maneira de resolver o erro é desativar a pré-renderização. Esta é geralmente a melhor escolha se o aplicativo faz uso pesado do armazenamento baseado em navegador. A pré-renderização adiciona complexidade e não beneficia o aplicativo porque `localStorage` o `sessionStorage` aplicativo não pode pré-renderizar qualquer conteúdo útil até ou estar disponível.

Para desativar a pré-renderização, abra o arquivo *Páginas/_Host.cshtml* e altere o `render-mode` do [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.

A pré-renderização pode ser útil `localStorage` para `sessionStorage`outras páginas que não usam ou . Para manter a pré-renderização ativada, adie a operação de carregamento até que o navegador esteja conectado ao circuito. A seguir, um exemplo para armazenar um contravalor:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Fatorar a preservação do Estado para um local comum

Se muitos componentes dependem do armazenamento baseado no navegador, a reimplementação do código do provedor de estado muitas vezes cria duplicação de código. Uma opção para evitar a duplicação de código é criar um *componente pai do provedor de estado* que encapsula a lógica do provedor de estado. Os componentes infantis podem trabalhar com dados persistentes sem considerar o mecanismo de persistência do estado.

No exemplo a `CounterStateProvider` seguir de um componente, os contra-dados são persistidos:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

O `CounterStateProvider` componente lida com a fase de carregamento não renderizando seu conteúdo filho até que o carregamento esteja concluído.

Para usar `CounterStateProvider` o componente, enrole uma instância do componente em torno de qualquer outro componente que exija acesso ao estado de contador. Para tornar o estado acessível a todos `CounterStateProvider` os componentes `App` de um aplicativo, enrole o componente em torno do `Router` componente *(App.razor):*

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Os componentes embrulhados recebem e podem modificar o estado de contra-ataque persistido. O `Counter` seguinte componente implementa o padrão:

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

O componente anterior não é `ProtectedBrowserStorage`necessário para interagir, nem lida com uma fase de "carregamento".

Para lidar com a pré-renderização como descrito anteriormente, `CounterStateProvider` pode ser alterado para que todos os componentes que consomem os dados de contador trabalhem automaticamente com a pré-renderização. Consulte a seção [De pré-renderização handle](#handle-prerendering) para obter detalhes.

Em geral, recomenda-se o padrão *de componente-pai do provedor de estado:*

* Para consumir estado em muitos outros componentes.
* Se há apenas um objeto de estado de alto nível para persistir.

Para persistir muitos objetos de estado diferentes e consumir diferentes subconjuntos de objetos em diferentes lugares, é melhor evitar lidar com o carregamento e a economia do estado globalmente.

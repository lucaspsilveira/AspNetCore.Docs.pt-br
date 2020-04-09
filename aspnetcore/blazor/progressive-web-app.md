---
title: Construa aplicativos web progressivos com ASP.NET WebAssembly principal Blazor
author: guardrex
description: Aprenda a criar Blazorum PWA (Progressive Web Application) baseado em base sinuosa que usa recursos modernos do navegador para se comportar como um aplicativo de desktop.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/progressive-web-app
ms.openlocfilehash: fe69e51aefae9c80e5bb4b78151d384ce25d41a7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218941"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a>Construa aplicações web progressivas com ASP.NET Core Blazor WebAssembly

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Um Progressive Web Application (PWA) é um Aplicativo de Página Única (SPA) que usa APIs e recursos modernos do navegador para se comportar como um aplicativo de desktop. O Blazor WebAssembly é uma plataforma de aplicativos web baseada em padrões baseado em padrões, por isso pode usar qualquer API do navegador, incluindo APIs PWA necessárias para os seguintes recursos:

* Trabalhando offline e carregando instantaneamente, independente da velocidade da rede.
* Executando em sua própria janela de aplicativo, não apenas uma janela do navegador.
* Sendo lançado a partir do menu inicial do sistema operacional do host, dock ou tela inicial.
* Receber notificações push de um servidor backend, mesmo enquanto o usuário não estiver usando o aplicativo.
* Atualização automática em segundo plano.

A palavra *progressiva* é usada para descrever tais aplicativos porque:

* Um usuário pode primeiro descobrir e usar o aplicativo dentro de seu navegador como qualquer outro SPA.
* Mais tarde, o usuário avança para instalá-lo em seu Sistema Operacional e habilitar notificações push.

## <a name="create-a-project-from-the-pwa-template"></a>Crie um projeto a partir do modelo PWA

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ao criar um novo **aplicativo Blazor WebAssembly** na caixa de diálogo **Criar um novo projeto,** selecione a caixa de seleção **do Aplicativo Da Web Progresso:**

![A caixa de seleção 'Aplicativo Web Progressivo' é selecionada na caixa de diálogo do novo projeto do Visual Studio.](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/CLI do .NET Core](#tab/visual-studio-code+netcore-cli)

Crie um projeto PWA em `--pwa` um shell de comando com o switch:

```dotnetcli
dotnet new blazorwasm -o MyNewProject --pwa
```

---

Opcionalmente, o PWA pode ser configurado para um aplicativo criado a partir do modelo ASP.NET Core Hosted. O cenário PWA é independente do modelo de hospedagem.

## <a name="installation-and-app-manifest"></a>Instalação e manifesto de aplicativos

Ao visitar um aplicativo criado usando o modelo PWA, os usuários têm a opção de instalar o aplicativo no menu inicial do sistema operacional, dock ou tela inicial. A forma como essa opção é apresentada depende do navegador do usuário. Ao usar navegadores baseados em Chromium de desktop, como Edge ou Chrome, um botão **Adicionar** aparece na barra de URL. Depois que o usuário seleciona o botão **Adicionar,** ele recebe uma caixa de diálogo de confirmação:

![O diaglog de confirmação no Google Chrome apresenta um botão Instalar ao usuário para o aplicativo 'MyBlazorPwa'.](progressive-web-app/_static/image2.png)

No iOS, os visitantes podem instalar o PWA usando o botão **Compartilhar** do Safari e sua opção **Adicionar à tela inicial.** No Chrome para Android, os usuários devem selecionar o botão **Menu** no canto superior direito, seguido por **Adicionar à tela inicial**.

Uma vez instalado, o aplicativo aparece em sua própria janela sem uma barra de endereços:

![O aplicativo 'MyBlazorPwa' é executado no Google Chrome sem uma barra de endereços.](progressive-web-app/_static/image3.png)

Para personalizar o título da janela, o esquema de cores, o ícone ou outros detalhes, consulte o arquivo *manifest.json* no diretório *wwwroot* do projeto. O esquema deste arquivo é definido por padrões da Web. Para obter mais informações, consulte [os web docs da MDN: Web App Manifest](https://developer.mozilla.org/docs/Web/Manifest).

## <a name="offline-support"></a>Suporte offline

Por padrão, os aplicativos criados usando a opção de modelo PWA têm suporte para executar off-line. Um usuário deve primeiro visitar o aplicativo enquanto estiver online. O navegador baixa e armazena automaticamente todos os recursos necessários para operar off-line.

> [!IMPORTANT]
> O apoio ao desenvolvimento interferiria no ciclo usual de desenvolvimento de fazer mudanças e testá-las. Portanto, o suporte off-line só é habilitado para aplicativos *publicados.* 

> [!WARNING]
> Se você pretende distribuir um PWA habilitado para offline, existem [vários avisos e ressalvas importantes](#caveats-for-offline-pwas). Esses cenários são inerentes a BlazorPWAs offline e não específicos para . Certifique-se de ler e entender essas ressalvas antes de fazer suposições sobre como seu aplicativo habilitado para offline funcionará.

Para ver como funciona o suporte offline:

1. Publique o aplicativo. Para obter mais informações, consulte <xref:host-and-deploy/blazor/index#publish-the-app>.
1. Implante o aplicativo em um servidor que suporta HTTPS e acesse o aplicativo em um navegador em seu endereço HTTPS seguro.
1. Abra as ferramentas de desenvolvimento do navegador e verifique se um *Trabalhador de Serviço* está registrado para o host na guia **Aplicativo:**

   ![A guia 'Aplicativo' de ferramentas de desenvolvedor esnobar do Google Chrome mostra um Trabalhador de Serviço ativado e em execução.](progressive-web-app/_static/image4.png)

1. Recarregue a página e examine a guia **Rede.** **O trabalhador de serviço** ou o cache de **memória** estão listados como as fontes de todos os ativos da página:

   ![As ferramentas de desenvolvedor do Google Chrome 'Rede' mostram fontes para todos os ativos da página.](progressive-web-app/_static/image5.png)

1. Para verificar se o navegador não depende do acesso à rede para carregar o aplicativo, também:

   * Desligue o servidor web e veja como o aplicativo continua funcionando normalmente, o que inclui recargas de página. Da mesma forma, o aplicativo continua funcionando normalmente quando há uma conexão de rede lenta.
   * Instruir o navegador a simular o modo offline na guia **Rede:**

   ![As ferramentas de desenvolvedor do Google Chrome 'Rede' com o modo navegador foram alteradas de 'Online' para 'Offline'.](progressive-web-app/_static/image6.png)

O suporte off-line usando um trabalhador Blazorde serviço é um padrão web, não específico para . Para obter mais informações sobre os trabalhadores do serviço, consulte [os docs web da MDN: API do trabalhador de serviço](https://developer.mozilla.org/docs/Web/API/Service_Worker_API). Para saber mais sobre padrões de uso comuns para os trabalhadores do serviço, consulte [o Google Web: O ciclo de vida do trabalhador de serviço](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

BlazorO modelo PWA produz dois arquivos de trabalhadores de serviço:

* *wwwroot/service-worker.js*, que é usado durante o desenvolvimento.
* *wwwroot/service-worker.published.js*, que é usado após a publicação do aplicativo.

Para compartilhar a lógica entre os dois arquivos do trabalhador de serviço, considere a seguinte abordagem:

* Adicione um terceiro arquivo JavaScript para manter a lógica comum.
* Use [self.importScripts](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) para carregar a lógica comum em ambos os arquivos do trabalhador de serviço.

### <a name="cache-first-fetch-strategy"></a>Estratégia de busca em cache-first

O trabalhador *de serviço incorporado.published.js* service worker resolve solicitações usando uma estratégia *de cache-first.* Isso significa que o trabalhador do serviço prefere retornar conteúdo em cache, independentemente de o usuário ter acesso à rede ou conteúdo mais novo estar disponível no servidor.

A estratégia cache-first é valiosa porque:

* **Garante confiabilidade.** &ndash;O acesso à rede não é um estado booleano. Um usuário não está simplesmente on-line ou off-line:

  * O dispositivo do usuário pode assumir que está online, mas a rede pode ser tão lenta que é impraticável esperar.
  * A rede pode retornar resultados inválidos para certos URLs, como quando há um portal WIFI cativo que está bloqueando ou redirecionando determinadas solicitações.
  
  É por isso que `navigator.onLine` a API do navegador não é confiável e não deve ser dependente.

* **Garante a correção.** &ndash;Ao construir um cache de recursos offline, o trabalhador de serviço usa hashing de conteúdo para garantir que ele tenha buscado um instantâneo completo e auto-consistente de recursos em um único instante no tempo. Este cache é então usado como uma unidade atômica. Não adianta pedir recursos mais novos à rede, já que as únicas versões necessárias são as já armazenadas em cache. Qualquer outra coisa corre o risco de inconsistência e incompatibilidade (por exemplo, tentar usar versões de conjuntos .NET que não foram compilados juntos).

### <a name="background-updates"></a>Atualizações em segundo plano

Como um modelo mental, você pode pensar em um PWA offline-first como se comportando como um aplicativo móvel que pode ser instalado. O aplicativo é iniciado imediatamente independentemente da conectividade da rede, mas a lógica do aplicativo instalado vem de um instantâneo point-in-time que pode não ser a versão mais recente.

O Blazor modelo PWA produz aplicativos que tentam se atualizar automaticamente em segundo plano sempre que o usuário visita e tem uma conexão de rede em funcionamento. A maneira como isso funciona é a seguinte:

* Durante a compilação, o projeto gera um *manifesto de ativos dos trabalhadores de serviços.* Por padrão, isso é chamado *de service-worker-assets.js*. O manifesto lista todos os recursos estáticos que o aplicativo requer para funcionar offline, como conjuntos .NET, arquivos JavaScript e CSS, incluindo seus hashes de conteúdo. A lista de recursos é carregada pelo trabalhador do serviço para que ele saiba quais recursos armazenar.
* Cada vez que o usuário visita o aplicativo, o navegador resolicita *o service-worker.js* e *o service-worker-assets.js* em segundo plano. Os arquivos são comparados byte-for-byte com o trabalhador de serviço instalado existente. Se o servidor devolver o conteúdo alterado para qualquer um desses arquivos, o funcionário do serviço tentará instalar uma nova versão de si mesmo.
* Ao instalar uma nova versão de si mesmo, o trabalhador de serviço cria um cache novo e separado para recursos offline e começa a preencher o cache com recursos listados em *service-worker-assets.js*. Essa lógica é `onInstall` implementada na função dentro do *serviço-trabalhador.published.js*.
* O processo é concluído com sucesso quando todos os recursos são carregados sem erro e todos os hashes de conteúdo correspondem. Se for bem-sucedido, o novo trabalhador entra em estado *de espera para ativação.* Assim que o usuário fecha o aplicativo (sem guias ou janelas de aplicativos restantes), o novo funcionário do serviço se torna *ativo* e é usado para visitas subsequentes do aplicativo. O antigo funcionário do serviço e seu cache são excluídos.
* Se o processo não for concluído com sucesso, a nova instância do trabalhador de serviço será descartada. O processo de atualização é tentado novamente na próxima visita do usuário, quando esperamos que o cliente tenha uma melhor conexão de rede que possa concluir as solicitações.

Personalize esse processo editando a lógica do trabalhador de serviços. Nenhum dos comportamentos anteriores Blazor é específico, mas é apenas a experiência padrão fornecida pela opção de modelo PWA. Para obter mais informações, consulte [os docs web do MDN: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).

### <a name="how-requests-are-resolved"></a>Como os pedidos são resolvidos

Como descrito na seção [De estratégia de busca em cache- primeiro,](#cache-first-fetch-strategy) o funcionário de serviço padrão usa uma estratégia de *cache-first,* o que significa que ele tenta servir conteúdo em cache quando disponível. Se não houver conteúdo armazenado em cache para uma determinada URL, por exemplo, ao solicitar dados de uma API backend, o funcionário do serviço recua em uma solicitação de rede regular. A solicitação de rede é bem-sucedida se o servidor estiver acessível. Essa lógica é `onFetch` implementada dentro da função dentro *do service-worker.published.js*.

Se os componentes razor do aplicativo dependerem da solicitação de dados das APIs de backend e você quiser fornecer uma experiência de usuário amigável para solicitações com falha devido à indisponibilidade da rede, implemente a lógica dentro dos componentes do aplicativo. Por exemplo, `try/catch` `HttpClient` use em torno de pedidos.

### <a name="support-server-rendered-pages"></a>Suporte a páginas renderizadas pelo servidor

Considere o que acontece quando o usuário `/counter` navega pela primeira vez para uma URL como ou qualquer outro link profundo no aplicativo. Nesses casos, você não deseja retornar o `/counter`conteúdo armazenado em cache como , mas, em vez disso, precisa que o navegador carregue o conteúdo armazenado em cache para `/index.html` iniciar seu Blazor aplicativo WebAssembly. Essas solicitações iniciais são conhecidas como solicitações *de navegação,* em oposição a:

* *solicitações de subrecursos* para imagens, folhas de estilo ou outros arquivos.
* *buscar/solicitações xhr* para dados de API.

O trabalhador de serviço padrão contém lógica de caso especial para solicitações de navegação. O trabalhador do serviço resolve as solicitações `/index.html`retornando o conteúdo armazenado em cache para, independentemente da URL solicitada. Essa lógica é `onFetch` implementada na função dentro do *serviço-trabalhador.published.js*.

Se o seu aplicativo tiver certos URLs que devem `/index.html` retornar HTML renderizado pelo servidor e não servir a partir do cache, então você precisa editar a lógica em seu funcionário de serviço. Se todas as URLs que contiverem `/Identity/` precisarm ser tratadas como solicitações regulares somente on-line para o servidor, então modifique a lógica *service-worker.published.js.* `onFetch` Localize o código a seguir:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Alterar o código para o seguinte:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

Se você não fizer isso, então, independentemente da conectividade da rede, o funcionário do `/index.html`serviço intercepta solicitações para tais URLs e as resolve usando .

### <a name="control-asset-caching"></a>Cache de ativos de controle

Se o seu `ServiceWorkerAssetsManifest` projeto definir a Blazorpropriedade MSBuild, a ferramenta de construção gerará um manifesto de ativos do trabalhador de serviço com o nome especificado. O modelo PWA padrão produz um arquivo de projeto contendo a seguinte propriedade:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

O arquivo é colocado no diretório de saída *wwwroot,* para que `/service-worker-assets.js`o navegador possa recuperar este arquivo solicitando . Para ver o conteúdo deste arquivo, abra */bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js* em um editor de texto. No entanto, não edite o arquivo, pois ele é regenerado em cada compilação.

Por padrão, este manifesto lista:

* Quaisquer Blazorrecursos gerenciados, como conjuntos .NET e os arquivos de tempo de execução .NET WebAssembly necessários para funcionar offline.
* Todos os recursos para publicação no diretório *wwwroot* do aplicativo, como imagens, folhas de estilo e arquivos JavaScript, incluindo ativos web estáticos fornecidos por projetos externos e pacotes NuGet.

Você pode controlar quais desses recursos são buscados e armazenados em `onInstall` cache pelo trabalhador de serviço editando a lógica em *serviço-trabalhador.published.js*. Por padrão, o trabalhador de serviço busca e armazena arquivos que correspondem a extensões típicas de nome de arquivo da Blazor Web, tais como *.html*, *.css,* *.js*e *.wasm*, mais tipos de arquivos específicos para WebAssembly (*.dll*, *.pdb*).

Para incluir recursos adicionais que não estão presentes no diretório *wwwroot* `ItemGroup` do aplicativo, defina entradas extras do MSBuild, como mostrado no exemplo a seguir:

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

Os `AssetUrl` metadados especificam a URL relativa à base que o navegador deve usar ao buscar o recurso para cache. Isso pode ser independente do nome do arquivo de origem original no disco.

> [!IMPORTANT]
> Adicionar `ServiceWorkerAssetsManifestItem` a não faz com que o arquivo seja publicado no diretório *wwwroot* do aplicativo. A saída de publicação deve ser controlada separadamente. A `ServiceWorkerAssetsManifestItem` única causa uma entrada adicional para aparecer no manifesto de ativos do trabalhador do serviço.

## <a name="push-notifications"></a>Notificações por push

Como qualquer outro PWA, um Blazor WebAssembly PWA pode receber notificações push de um servidor backend. O servidor pode enviar notificações push a qualquer momento, mesmo quando o usuário não está usando ativamente o aplicativo. Por exemplo, notificações push podem ser enviadas quando um usuário diferente executa uma ação relevante.

O mecanismo para enviar uma notificação Blazor push é totalmente independente do WebAssembly, uma vez que é implementado pelo servidor backend que pode usar qualquer tecnologia. Se você quiser enviar notificações push de um servidor ASP.NET Core, considere [usar uma técnica semelhante à abordagem tomada na oficina De Pizza Blazing](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).

O mecanismo para receber e exibir uma notificação push Blazor no cliente também é independente do WebAssembly, uma vez que é implementado no arquivo JavaScript do trabalhador de serviço. Por exemplo, veja [a abordagem utilizada na oficina de Pizza Blazing.](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications)

## <a name="caveats-for-offline-pwas"></a>Ressalvas para PWAs offline

Nem todos os aplicativos devem tentar suportar o uso offline. O suporte offline adiciona uma complexidade significativa, embora nem sempre seja relevante para os casos de uso necessários.

O suporte offline geralmente é apenas relevante:

* Se o armazenamento de dados principal for local para o navegador. Por exemplo, a abordagem é relevante em um aplicativo com uma `localStorage` ui para um dispositivo [IoT](https://en.wikipedia.org/wiki/Internet_of_things) que armazena dados ou [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).
* Se o aplicativo realizar uma quantidade significativa de trabalho para buscar e armazenar os dados da API de backend relevantes para cada usuário, para que eles possam navegar através dos dados off-line. Se o aplicativo tiver suporte à edição, um sistema para rastrear alterações e sincronizar dados com o backend deve ser construído.
* Se o objetivo é garantir que o aplicativo seja carregado imediatamente, independentemente das condições da rede. Implemente uma experiência de usuário adequada em torno de solicitações de API backend para mostrar o progresso das solicitações e se comportar graciosamente quando as solicitações falharem devido à indisponibilidade da rede.

Além disso, os PWAs com capacidade off-line devem lidar com uma série de complicações adicionais. Os desenvolvedores devem se familiarizar cuidadosamente com as ressalvas nas seguintes seções.

### <a name="offline-support-only-when-published"></a>Suporte offline somente quando publicado

Durante o desenvolvimento, você normalmente deseja ver cada alteração refletida imediatamente no navegador sem passar por um processo de atualização em segundo plano. Portanto, Blazoro modelo PWA habilita o suporte off-line somente quando publicado.

Ao criar um aplicativo offline, não basta testar o aplicativo no ambiente Dedesenvolvimento. Você deve testar o aplicativo em seu estado publicado para entender como ele responde a diferentes condições de rede.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Atualize a conclusão após a navegação do usuário longe do aplicativo

As atualizações não são concluídas até que o usuário tenha navegado para longe do aplicativo em todas as guias. Como explicado na seção De verção de atualizações em [segundo plano,](#background-updates) depois de implantar uma atualização no aplicativo, o navegador busca os arquivos atualizados do trabalhador do serviço para iniciar o processo de atualização.

O que surpreende muitos desenvolvedores é que, mesmo quando esta atualização é concluída, ela **não** faz efeito até que o usuário tenha navegado em todas as guias. **Não** basta atualizar a guia exibindo o aplicativo, mesmo que seja a única guia exibindo o aplicativo. Até que seu aplicativo esteja completamente fechado, o novo funcionário permanece na *espera para ativar* o status. **Isso não é Blazorespecífico, mas sim um comportamento padrão da plataforma web.**

Isso geralmente incomoda os desenvolvedores que estão tentando testar atualizações para seu funcionário de serviço ou recursos armazenados em cache off-line. Se você verificar as ferramentas de desenvolvedor do navegador, você pode ver algo como o seguinte:

![A guia 'Aplicativo' do Google Chrome mostra que o Trabalhador de Serviço do aplicativo está 'esperando para ativar'.](progressive-web-app/_static/image7.png)

Enquanto a lista de "clientes", que são guias ou janelas exibindo seu aplicativo, não estiver vazia, o trabalhador continuará esperando. A razão pela qual os trabalhadores do serviço fazem isso é para garantir a consistência. Consistência significa que todos os recursos são obtidos do mesmo cache atômico.

Ao testar alterações, você pode achar conveniente clicar no link "skipWaiting", como mostrado na captura de tela anterior, e depois recarregar a página. Você pode automatizar isso para todos os usuários codificando seu funcionário de serviço para [pular a fase de "espera" e ativar imediatamente na atualização](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). Se você pular a fase de espera, você está abrindo mão da garantia de que os recursos são sempre obtidos consistentemente a partir da mesma instância de cache.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Os usuários podem executar qualquer versão histórica do aplicativo

Os desenvolvedores da Web normalmente esperam que os usuários executem apenas a versão mais recente implantada de seu aplicativo web, já que isso é normal dentro do modelo tradicional de distribuição web. No entanto, um PWA off-line é mais parecido com um aplicativo móvel nativo, onde os usuários não estão necessariamente executando a versão mais recente.

Como explicado na seção [De verção](#background-updates) de atualizações em segundo plano, depois de implantar uma atualização no seu aplicativo, cada usuário existente continua a usar uma versão anterior para pelo menos mais uma **visita,** porque a atualização ocorre em segundo plano e não é ativada até que o usuário navegue posteriormente. Além disso, a versão anterior que está sendo usada não é necessariamente a anterior que você implantou. A versão anterior pode ser *qualquer* versão histórica, dependendo de quando o usuário completou uma atualização pela última vez.

Isso pode ser um problema se as partes frontend e backend do seu aplicativo exigirem acordo sobre o esquema para solicitações de API. Você não deve implantar alterações de esquema de API incompatíveis para trás até ter certeza de que todos os usuários foram atualizados. Alternativamente, bloqueie os usuários de usar versões mais antigas incompatíveis do aplicativo. Esse requisito de cenário é o mesmo que para aplicativos móveis nativos. Se você implantar uma mudança de quebra nas APIs do servidor, o aplicativo cliente será quebrado para usuários que ainda não atualizaram.

Se possível, não implante alterações de quebra em suas APIs backend. Se você deve fazê-lo, considere usar [APIs padrão do Trabalhador de Serviço, como ServiceWorkerRegistration,](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) para determinar se o aplicativo está atualizado e, se não, para evitar o uso.

### <a name="interference-with-server-rendered-pages"></a>Interferência com páginas renderizadas pelo servidor

Conforme descrito na seção ['Enviar páginas renderizadas pelo servidor',](#support-server-rendered-pages) se você `/index.html` quiser ignorar o comportamento do trabalhador de serviço de devolver conteúdo para todas as solicitações de navegação, edite a lógica em seu trabalhador de serviço.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Todo o conteúdo do manifesto de ativos do trabalhador de serviço é armazenado em cache por padrão

Conforme descrito na seção [Decache de ativos de Controle,](#control-asset-caching) o *arquivo serviço-trabalhador-assets.js* é gerado durante a compilação e lista todos os ativos que o trabalhador do serviço deve buscar e cache.

Uma vez que esta lista por padrão inclui tudo o que é emitido para *wwwroot*, incluindo conteúdo fornecido por pacotes e projetos externos, você deve ter cuidado para não colocar muito conteúdo lá. Se o diretório *wwwroot* contiver milhões de imagens, o funcionário do serviço tentará buscar e cacheá-las todas, consumindo largura de banda excessiva e provavelmente não completando com sucesso.

Implementar lógica arbitrária para controlar qual subconjunto do conteúdo do manifesto deve ser `onInstall` buscado e armazenado em cache editando a função no *service-worker.published.js*.

### <a name="interaction-with-authentication"></a>Interação com autenticação

É possível usar a opção de modelo PWA em conjunto com as opções de autenticação. Um PWA com capacidade offline também pode suportar autenticação quando o usuário tem conectividade de rede.

Quando um usuário não tem conectividade de rede, ele não pode autenticar ou obter tokens de acesso. Por padrão, tentar visitar a página de login sem acesso à rede resulta em uma mensagem de "erro de rede".

Você deve projetar um fluxo de iu que permite que o usuário faça coisas úteis enquanto estiver offline sem tentar autenticar ou obter tokens de acesso. Alternativamente, você pode projetar o aplicativo para falhar de forma graciosa quando a rede não está disponível. Se isso não for possível em seu aplicativo, talvez você não queira ativar o suporte off-line.

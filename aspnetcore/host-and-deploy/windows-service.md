---
title: Hospedar o ASP.NET Core em um serviço Windows
author: rick-anderson
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 5cb61d330df7e15fbd54396207792596ae018fd3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417584"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Hospedar o ASP.NET Core em um serviço Windows

::: moniker range=">= aspnetcore-3.0"

Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS. Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

* [SDK do ASP.NET Core 2.1 ou posterior](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 ou posterior](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a>Modelo de serviço de trabalho

O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada. Para usar o modelo como base para um aplicativo de Serviço Windows:

1. Crie um aplicativo de serviço de trabalho usando o modelo do .NET Core.
1. Siga as orientações na seção [Configuração de aplicativos](#app-configuration) para atualizar o aplicativo do Serviço de Trabalho para que ele possa ser executado como um Serviço Windows.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a>Configuração do aplicativo

O aplicativo requer uma referência de pacote para [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).

`IHostBuilder.UseWindowsService`é chamado ao construir o host. Se o aplicativo estiver sendo executado como um Serviço Windows, o método:

* Define o tempo de vida do host como `WindowsServiceLifetime`.
* Define a raiz de [conteúdo](xref:fundamentals/index#content-root) como [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory). Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).
* Habilita o registro no registro de eventos:
  * O nome do aplicativo é usado como o nome de origem padrão.
  * O nível de registro padrão é *Aviso* ou superior para `CreateDefaultBuilder` um aplicativo com base em um modelo ASP.NET Core que chama para construir o host.
  * Anular o nível de `Logging:EventLog:LogLevel:Default` registro padrão com a chave em *appsettings.json*/*appsettings.{ Ambiente}.json* ou outro provedor de configuração.
  * Somente administradores podem criar novas fontes de evento. Quando uma fonte de evento não puder ser criada usando o nome do aplicativo, um aviso será registrado em log como a fonte do *Aplicativo* e os logs de eventos serão desabilitados.

Em `CreateHostBuilder` *Program.cs:*

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

Os seguintes aplicativos de exemplo acompanham este tópico:

* Amostra &ndash; de serviço do trabalhador em segundo plano Uma amostra de aplicativo não web com base no [modelo de serviço](#worker-service-template) do trabalhador que usa [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.
* Amostra de &ndash; serviço do aplicativo web Uma amostra de aplicativo web razor pages que é executada como um Serviço do Windows com [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.

Para obter orientação de <xref:mvc/overview> MVC, consulte os artigos em e <xref:migration/22-to-30>.

## <a name="deployment-type"></a>Tipo de implantação

Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>.

Para um serviço baseado em aplicativos web que usa as páginas de barbear ou frameworks MVC, especifique o SDK da Web no arquivo do projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados),](xref:fundamentals/host/hosted-services)especifique o SDK do trabalhador no arquivo do projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>FDD (Implantação dependente de estrutura)

A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino. Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.

Se usar o [Web SDK](#sdk), um arquivo *Web.config,* que normalmente é produzido ao publicar um aplicativo ASP.NET Core, é desnecessário para um aplicativo do Windows Services. Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>SCD (Implantação Autossuficiente)

A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino. O runtime e as dependências do aplicativo são implantados com o aplicativo.

Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Para publicar para vários RIDs:

* Forneça os RIDs em uma lista delimitada por ponto e vírgula.
* Use o [ \<](/dotnet/core/tools/csproj#runtimeidentifiers) nome de propriedade RuntimeIdentifiers>(plural).

Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).

## <a name="service-user-account"></a>Conta de usuário do serviço

Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.

Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.

A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.

Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).

Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado. Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Fazer logon como direitos de um serviço

Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:

1. Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.
1. Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.
1. Abra a política **Fazer logon como um serviço**.
1. Selecione **Adicionar Usuário ou Grupo**.
1. Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:
   1. Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.
   1. Selecione **Avançado**. Escolha **Localizar Agora**. Escolha a conta de usuário na lista. Selecione **OK**. Escolha **OK** novamente para adicionar o usuário à política.
1. Escolha **OK** ou **Aplicar** para aceitar as alterações.

## <a name="create-and-manage-the-windows-service"></a>Criar e gerenciar o Serviço Windows

### <a name="create-a-service"></a>Criar um serviço

Use comandos do PowerShell para registrar um serviço. Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Caminho para a pasta do aplicativo no `d:\myservice`host (por exemplo, ). Não inclua o executável do aplicativo no caminho. A barra à direita não é necessária.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Conta de usuário do `Contoso\ServiceUser`serviço (por exemplo, ).
* `{SERVICE NAME}`&ndash; Nome do serviço `MyService`(por exemplo, ).
* `{EXE FILE PATH}`&ndash; O caminho executável do aplicativo `d:\myservice\myservice.exe`(por exemplo, ). Inclua nome do arquivo do executável com a extensão.
* `{DESCRIPTION}`&ndash; Descrição do serviço `My sample service`(por exemplo, ).
* `{DISPLAY NAME}`&ndash; Nome de exibição `My Service`do serviço (por exemplo, ).

### <a name="start-a-service"></a>Iniciar um serviço

Inicie o serviço com o seguinte comando do PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

O comando leva alguns segundos para iniciar o serviço.

### <a name="determine-a-services-status"></a>Determinar o status do serviço

Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

O status é relatado como um dos seguintes valores:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Parar um serviço

Pare um serviço com o seguinte comando do Powershell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Remover um serviço

Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional. Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configurar pontos de extremidade

Por padrão, o ASP.NET Core é associado a `http://localhost:5000`. Configure a URL e `ASPNETCORE_URLS` a porta definindo a variável ambiente.

Para obter abordagens adicionais de configuração de URL e porta, consulte o artigo do servidor relevante:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

A orientação anterior abrange o suporte para pontos finais HTTPS. Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um Serviço do Windows.

> [!NOTE]
> Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.

## <a name="current-directory-and-content-root"></a>Diretório atual e a raiz do conteúdo

O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*. A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações). Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Usar ContentRootPath ou ContentRootFileProvider

Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> para localizar os recursos do aplicativo.

Quando o aplicativo é <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> executado <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> como um serviço, define o [appContext.BaseDirectory](xref:System.AppContext.BaseDirectory).

Os arquivos de configurações padrão do aplicativo, *appsettings.json* e *appsettings.{ O ambiente}.json*, é carregado a partir da raiz de conteúdo do aplicativo chamando [CreateDefaultBuilder durante a construção do host](xref:fundamentals/host/generic-host#set-up-a-host).

Para outras configurações de arquivos <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>carregados pelo código <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>do desenvolvedor, não há necessidade de ligar . No exemplo a seguir, o arquivo *custom_settings.json* existe na raiz de conteúdo do aplicativo e é carregado sem definir explicitamente um caminho base:

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

Não tente usar <xref:System.IO.Directory.GetCurrentDirectory*> para obter um caminho de recursos porque um aplicativo do Windows Service retorna a pasta *C:\\WINDOWS\\system32* como seu diretório atual.

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Armazenar os arquivos do serviço em um local adequado no disco

Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.

## <a name="troubleshoot"></a>Solucionar problemas

Para solucionar problemas de um aplicativo do Windows Service, consulte <xref:test/troubleshoot>.

### <a name="common-errors"></a>Erros comuns

* Uma versão antiga ou pré-lançamento do PowerShell está em uso.
* O serviço registrado não usa a saída **publicada** do aplicativo a partir do comando [dotnet publish.](/dotnet/core/tools/dotnet-publish) A saída do comando [dotnet build](/dotnet/core/tools/dotnet-build) não é suportada para implantação de aplicativos. Os ativos publicados são encontrados em qualquer uma das seguintes pastas, dependendo do tipo de implantação:
  * *bin/Release/{TARGET FRAMEWORK}/publish* (FDD)
  * *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)
* O serviço não está no estado de EXECUÇÃO.
* Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos. O caminho base de um Windows Service é *c:\\Windows\\System32*.
* O usuário não tem log on como um direito *de serviço.*
* A senha do usuário é expirada ou passada `New-Service` incorretamente ao executar o comando PowerShell.
* O aplicativo requer ASP.NET autenticação Core, mas não está configurado para conexões seguras (HTTPS).
* A porta URL de solicitação está incorreta ou não está configurada corretamente no aplicativo.

### <a name="system-and-application-event-logs"></a>Logs de eventos de sistema e aplicativos

Acesse os registros de eventos do sistema e do aplicativo:

1. Abra o menu Iniciar, procure o *Visualizador de Eventos*e selecione o aplicativo **Visualizador de Eventos.**
1. No **Visualizador de Eventos**, abra o nó **Logs do Windows**.
1. Selecione **Sistema** para abrir o Registro de Eventos do Sistema. Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.
1. Procure erros associados ao aplicativo com falha.

### <a name="run-the-app-at-a-command-prompt"></a>Execute o aplicativo em um prompt de comando

Muitos erros de inicialização não produzem informações úteis nos registros de eventos. Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem. Para registrar detalhes adicionais do aplicativo, baixe o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no ambiente [Desenvolvimento](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Limpar caches de pacotes

Um aplicativo em funcionamento pode falhar imediatamente após atualizar o .NET Core SDK na máquina de desenvolvimento ou alterar as versões do pacote dentro do aplicativo. Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais. A maioria desses problemas pode ser corrigida seguindo estas instruções:

1. Exclua as pastas *bin* e *obj*.
1. Limpe os caches do pacote executando [os locais dotnet nuget tudo -- limpar](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.

   A limpeza de caches de pacotes também pode ser realizada `nuget locals all -clear`com a ferramenta [nuget.exe](https://www.nuget.org/downloads) e executando o comando . *nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).

1. Restaure e recompile o projeto.
1. Exclua todos os arquivos da pasta de implantação no servidor antes de reimplantar o aplicativo.

### <a name="slow-or-hanging-app"></a>Aplicativo lento ou travando

Um *crash dump* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, falha de inicialização ou aplicativo lento.

#### <a name="app-crashes-or-encounters-an-exception"></a>O aplicativo falha ou encontra uma exceção

Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):

1. Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.
1. Execute o [script EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) com o nome executável do aplicativo:

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Execute o aplicativo sob as condições que causam a falha.
1. Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente. O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.

> [!WARNING]
> Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>O aplicativo trava, falha durante a inicialização ou executa normalmente

Quando um aplicativo *é travado* (pára de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [Arquivos de despejo do modo de usuário: Escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o dump.

#### <a name="analyze-the-dump"></a>Analisar o despejo de memória

Um despejo de memória pode ser analisado usando várias abordagens. Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Recursos adicionais

* [Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS. Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

* [SDK do ASP.NET Core 2.1 ou posterior](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 ou posterior](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Configuração do aplicativo

O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console. Verifique se o depurador está anexado ou se uma opção `--console` está presente. Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Se as condições forem falsas (o aplicativo for executado como um serviço):

* Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo. Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado. Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root). Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.
* Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.

Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.

Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.

No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo. Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Tipo de implantação

Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>.

Para um serviço baseado em aplicativos web que usa as páginas de barbear ou frameworks MVC, especifique o SDK da Web no arquivo do projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados),](xref:fundamentals/host/hosted-services)especifique o SDK do trabalhador no arquivo do projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>FDD (Implantação dependente de estrutura)

A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino. Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.

O [Identificador de tempo de execução do Windows (RID)](/dotnet/core/rid-catalog) [\<(RuntimeIdentifier>) ](/dotnet/core/tools/csproj#runtimeidentifier)contém a estrutura de destino. No exemplo a seguir, o RID é especificado como `win7-x64`. A propriedade `<SelfContained>` é definida como `false`. Essas propriedades instruem o SDK a gerar um arquivo executável (*.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.

O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows. Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>SCD (Implantação Autossuficiente)

A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino. O runtime e as dependências do aplicativo são implantados com o aplicativo.

Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Para publicar para vários RIDs:

* Forneça os RIDs em uma lista delimitada por ponto e vírgula.
* Use o [ \<](/dotnet/core/tools/csproj#runtimeidentifiers) nome de propriedade RuntimeIdentifiers>(plural).

Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).

Uma propriedade `<SelfContained>` está definida como `true`:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Conta de usuário do serviço

Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.

Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.

A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.

Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).

Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado. Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Fazer logon como direitos de um serviço

Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:

1. Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.
1. Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.
1. Abra a política **Fazer logon como um serviço**.
1. Selecione **Adicionar Usuário ou Grupo**.
1. Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:
   1. Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.
   1. Selecione **Avançado**. Escolha **Localizar Agora**. Escolha a conta de usuário na lista. Selecione **OK**. Escolha **OK** novamente para adicionar o usuário à política.
1. Escolha **OK** ou **Aplicar** para aceitar as alterações.

## <a name="create-and-manage-the-windows-service"></a>Criar e gerenciar o Serviço Windows

### <a name="create-a-service"></a>Criar um serviço

Use comandos do PowerShell para registrar um serviço. Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Caminho para a pasta do aplicativo no `d:\myservice`host (por exemplo, ). Não inclua o executável do aplicativo no caminho. A barra à direita não é necessária.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Conta de usuário do `Contoso\ServiceUser`serviço (por exemplo, ).
* `{SERVICE NAME}`&ndash; Nome do serviço `MyService`(por exemplo, ).
* `{EXE FILE PATH}`&ndash; O caminho executável do aplicativo `d:\myservice\myservice.exe`(por exemplo, ). Inclua nome do arquivo do executável com a extensão.
* `{DESCRIPTION}`&ndash; Descrição do serviço `My sample service`(por exemplo, ).
* `{DISPLAY NAME}`&ndash; Nome de exibição `My Service`do serviço (por exemplo, ).

### <a name="start-a-service"></a>Iniciar um serviço

Inicie o serviço com o seguinte comando do PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

O comando leva alguns segundos para iniciar o serviço.

### <a name="determine-a-services-status"></a>Determinar o status do serviço

Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

O status é relatado como um dos seguintes valores:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Parar um serviço

Pare um serviço com o seguinte comando do Powershell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Remover um serviço

Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Manipular eventos de início e de parada

Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:

1. Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional. Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configurar pontos de extremidade

Por padrão, o ASP.NET Core é associado a `http://localhost:5000`. Configure a URL e `ASPNETCORE_URLS` a porta definindo a variável ambiente.

Para obter abordagens adicionais de configuração de URL e porta, consulte o artigo do servidor relevante:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

A orientação anterior abrange o suporte para pontos finais HTTPS. Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um Serviço do Windows.

> [!NOTE]
> Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.

## <a name="current-directory-and-content-root"></a>Diretório atual e a raiz do conteúdo

O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*. A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações). Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Defina o caminho da raiz do conteúdo para a pasta do aplicativo

O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado. Em vez `GetCurrentDirectory` de ligar para criar <xref:System.IO.Directory.SetCurrentDirectory*> caminhos para os arquivos de configuração, chame com o caminho para a raiz de [conteúdo](xref:fundamentals/index#content-root)do aplicativo .

No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Armazenar os arquivos do serviço em um local adequado no disco

Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.

## <a name="troubleshoot"></a>Solucionar problemas

Para solucionar problemas de um aplicativo do Windows Service, consulte <xref:test/troubleshoot>.

### <a name="common-errors"></a>Erros comuns

* Uma versão antiga ou pré-lançamento do PowerShell está em uso.
* O serviço registrado não usa a saída **publicada** do aplicativo a partir do comando [dotnet publish.](/dotnet/core/tools/dotnet-publish) A saída do comando [dotnet build](/dotnet/core/tools/dotnet-build) não é suportada para implantação de aplicativos. Os ativos publicados são encontrados em qualquer uma das seguintes pastas, dependendo do tipo de implantação:
  * *bin/Release/{TARGET FRAMEWORK}/publish* (FDD)
  * *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)
* O serviço não está no estado de EXECUÇÃO.
* Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos. O caminho base de um Windows Service é *c:\\Windows\\System32*.
* O usuário não tem log on como um direito *de serviço.*
* A senha do usuário é expirada ou passada `New-Service` incorretamente ao executar o comando PowerShell.
* O aplicativo requer ASP.NET autenticação Core, mas não está configurado para conexões seguras (HTTPS).
* A porta URL de solicitação está incorreta ou não está configurada corretamente no aplicativo.

### <a name="system-and-application-event-logs"></a>Logs de eventos de sistema e aplicativos

Acesse os registros de eventos do sistema e do aplicativo:

1. Abra o menu Iniciar, procure o *Visualizador de Eventos*e selecione o aplicativo **Visualizador de Eventos.**
1. No **Visualizador de Eventos**, abra o nó **Logs do Windows**.
1. Selecione **Sistema** para abrir o Registro de Eventos do Sistema. Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.
1. Procure erros associados ao aplicativo com falha.

### <a name="run-the-app-at-a-command-prompt"></a>Execute o aplicativo em um prompt de comando

Muitos erros de inicialização não produzem informações úteis nos registros de eventos. Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem. Para registrar detalhes adicionais do aplicativo, baixe o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no ambiente [Desenvolvimento](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Limpar caches de pacotes

Um aplicativo em funcionamento pode falhar imediatamente após atualizar o .NET Core SDK na máquina de desenvolvimento ou alterar as versões do pacote dentro do aplicativo. Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais. A maioria desses problemas pode ser corrigida seguindo estas instruções:

1. Exclua as pastas *bin* e *obj*.
1. Limpe os caches do pacote executando [os locais dotnet nuget tudo -- limpar](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.

   A limpeza de caches de pacotes também pode ser realizada `nuget locals all -clear`com a ferramenta [nuget.exe](https://www.nuget.org/downloads) e executando o comando . *nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).

1. Restaure e recompile o projeto.
1. Exclua todos os arquivos da pasta de implantação no servidor antes de reimplantar o aplicativo.

### <a name="slow-or-hanging-app"></a>Aplicativo lento ou travando

Um *crash dump* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, falha de inicialização ou aplicativo lento.

#### <a name="app-crashes-or-encounters-an-exception"></a>O aplicativo falha ou encontra uma exceção

Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):

1. Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.
1. Execute o [script EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome executável do aplicativo:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Execute o aplicativo sob as condições que causam a falha.
1. Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente. O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.

> [!WARNING]
> Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>O aplicativo trava, falha durante a inicialização ou executa normalmente

Quando um aplicativo *é travado* (pára de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [Arquivos de despejo do modo de usuário: Escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o dump.

#### <a name="analyze-the-dump"></a>Analisar o despejo de memória

Um despejo de memória pode ser analisado usando várias abordagens. Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Recursos adicionais

* [Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS. Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

* [SDK do ASP.NET Core 2.1 ou posterior](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 ou posterior](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Configuração do aplicativo

O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console. Verifique se o depurador está anexado ou se uma opção `--console` está presente. Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Se as condições forem falsas (o aplicativo for executado como um serviço):

* Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo. Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado. Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root). Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.
* Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.

Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.

Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.

No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo. Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Tipo de implantação

Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>.

Para um serviço baseado em aplicativos web que usa as páginas de barbear ou frameworks MVC, especifique o SDK da Web no arquivo do projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados),](xref:fundamentals/host/hosted-services)especifique o SDK do trabalhador no arquivo do projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>FDD (Implantação dependente de estrutura)

A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino. Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável (*.exe*), chamado de *executável dependente de estrutura*.

O [Identificador de tempo de execução do Windows (RID)](/dotnet/core/rid-catalog) [\<(RuntimeIdentifier>) ](/dotnet/core/tools/csproj#runtimeidentifier)contém a estrutura de destino. No exemplo a seguir, o RID é especificado como `win7-x64`. A propriedade `<SelfContained>` é definida como `false`. Essas propriedades instruem o SDK a gerar um arquivo executável (*.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.

A propriedade `<UseAppHost>` é definida como `true`. Essa propriedade fornece o serviço com um caminho de ativação (um arquivo executável *.exe*) para FDD.

O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows. Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>SCD (Implantação Autossuficiente)

A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino. O runtime e as dependências do aplicativo são implantados com o aplicativo.

Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Para publicar para vários RIDs:

* Forneça os RIDs em uma lista delimitada por ponto e vírgula.
* Use o [ \<](/dotnet/core/tools/csproj#runtimeidentifiers) nome de propriedade RuntimeIdentifiers>(plural).

Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).

Uma propriedade `<SelfContained>` está definida como `true`:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Conta de usuário do serviço

Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.

Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.

A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.

Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).

Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado. Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Fazer logon como direitos de um serviço

Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:

1. Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.
1. Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.
1. Abra a política **Fazer logon como um serviço**.
1. Selecione **Adicionar Usuário ou Grupo**.
1. Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:
   1. Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.
   1. Selecione **Avançado**. Escolha **Localizar Agora**. Escolha a conta de usuário na lista. Selecione **OK**. Escolha **OK** novamente para adicionar o usuário à política.
1. Escolha **OK** ou **Aplicar** para aceitar as alterações.

## <a name="create-and-manage-the-windows-service"></a>Criar e gerenciar o Serviço Windows

### <a name="create-a-service"></a>Criar um serviço

Use comandos do PowerShell para registrar um serviço. Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Caminho para a pasta do aplicativo no `d:\myservice`host (por exemplo, ). Não inclua o executável do aplicativo no caminho. A barra à direita não é necessária.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Conta de usuário do `Contoso\ServiceUser`serviço (por exemplo, ).
* `{SERVICE NAME}`&ndash; Nome do serviço `MyService`(por exemplo, ).
* `{EXE FILE PATH}`&ndash; O caminho executável do aplicativo `d:\myservice\myservice.exe`(por exemplo, ). Inclua nome do arquivo do executável com a extensão.
* `{DESCRIPTION}`&ndash; Descrição do serviço `My sample service`(por exemplo, ).
* `{DISPLAY NAME}`&ndash; Nome de exibição `My Service`do serviço (por exemplo, ).

### <a name="start-a-service"></a>Iniciar um serviço

Inicie o serviço com o seguinte comando do PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

O comando leva alguns segundos para iniciar o serviço.

### <a name="determine-a-services-status"></a>Determinar o status do serviço

Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

O status é relatado como um dos seguintes valores:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Parar um serviço

Pare um serviço com o seguinte comando do Powershell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Remover um serviço

Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Manipular eventos de início e de parada

Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:

1. Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional. Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configurar pontos de extremidade

Por padrão, o ASP.NET Core é associado a `http://localhost:5000`. Configure a URL e `ASPNETCORE_URLS` a porta definindo a variável ambiente.

Para obter abordagens adicionais de configuração de URL e porta, consulte o artigo do servidor relevante:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

A orientação anterior abrange o suporte para pontos finais HTTPS. Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um Serviço do Windows.

> [!NOTE]
> Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.

## <a name="current-directory-and-content-root"></a>Diretório atual e a raiz do conteúdo

O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*. A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações). Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Defina o caminho da raiz do conteúdo para a pasta do aplicativo

O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado. Em vez `GetCurrentDirectory` de ligar para criar <xref:System.IO.Directory.SetCurrentDirectory*> caminhos para os arquivos de configuração, chame com o caminho para a raiz de [conteúdo](xref:fundamentals/index#content-root)do aplicativo .

No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Armazenar os arquivos do serviço em um local adequado no disco

Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.

## <a name="troubleshoot"></a>Solucionar problemas

Para solucionar problemas de um aplicativo do Windows Service, consulte <xref:test/troubleshoot>.

### <a name="common-errors"></a>Erros comuns

* Uma versão antiga ou pré-lançamento do PowerShell está em uso.
* O serviço registrado não usa a saída **publicada** do aplicativo a partir do comando [dotnet publish.](/dotnet/core/tools/dotnet-publish) A saída do comando [dotnet build](/dotnet/core/tools/dotnet-build) não é suportada para implantação de aplicativos. Os ativos publicados são encontrados em qualquer uma das seguintes pastas, dependendo do tipo de implantação:
  * *bin/Release/{TARGET FRAMEWORK}/publish* (FDD)
  * *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)
* O serviço não está no estado de EXECUÇÃO.
* Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos. O caminho base de um Windows Service é *c:\\Windows\\System32*.
* O usuário não tem log on como um direito *de serviço.*
* A senha do usuário é expirada ou passada `New-Service` incorretamente ao executar o comando PowerShell.
* O aplicativo requer ASP.NET autenticação Core, mas não está configurado para conexões seguras (HTTPS).
* A porta URL de solicitação está incorreta ou não está configurada corretamente no aplicativo.

### <a name="system-and-application-event-logs"></a>Logs de eventos de sistema e aplicativos

Acesse os registros de eventos do sistema e do aplicativo:

1. Abra o menu Iniciar, procure o *Visualizador de Eventos*e selecione o aplicativo **Visualizador de Eventos.**
1. No **Visualizador de Eventos**, abra o nó **Logs do Windows**.
1. Selecione **Sistema** para abrir o Registro de Eventos do Sistema. Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.
1. Procure erros associados ao aplicativo com falha.

### <a name="run-the-app-at-a-command-prompt"></a>Execute o aplicativo em um prompt de comando

Muitos erros de inicialização não produzem informações úteis nos registros de eventos. Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem. Para registrar detalhes adicionais do aplicativo, baixe o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no ambiente [Desenvolvimento](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Limpar caches de pacotes

Um aplicativo em funcionamento pode falhar imediatamente após atualizar o .NET Core SDK na máquina de desenvolvimento ou alterar as versões do pacote dentro do aplicativo. Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais. A maioria desses problemas pode ser corrigida seguindo estas instruções:

1. Exclua as pastas *bin* e *obj*.
1. Limpe os caches do pacote executando [os locais dotnet nuget tudo -- limpar](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.

   A limpeza de caches de pacotes também pode ser realizada `nuget locals all -clear`com a ferramenta [nuget.exe](https://www.nuget.org/downloads) e executando o comando . *nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).

1. Restaure e recompile o projeto.
1. Exclua todos os arquivos da pasta de implantação no servidor antes de reimplantar o aplicativo.

### <a name="slow-or-hanging-app"></a>Aplicativo lento ou travando

Um *crash dump* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, falha de inicialização ou aplicativo lento.

#### <a name="app-crashes-or-encounters-an-exception"></a>O aplicativo falha ou encontra uma exceção

Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):

1. Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.
1. Execute o [script EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome executável do aplicativo:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Execute o aplicativo sob as condições que causam a falha.
1. Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente. O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.

> [!WARNING]
> Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>O aplicativo trava, falha durante a inicialização ou executa normalmente

Quando um aplicativo *é travado* (pára de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [Arquivos de despejo do modo de usuário: Escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o dump.

#### <a name="analyze-the-dump"></a>Analisar o despejo de memória

Um despejo de memória pode ser analisado usando várias abordagens. Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Recursos adicionais

* [Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

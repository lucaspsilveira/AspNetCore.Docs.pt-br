---
title: Autenticação de dois fatores com SMS no ASP.NET Core
author: rick-anderson
description: Saiba como configurar a autenticação de dois fatores (2FA) com um aplicativo ASP.NET Core.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/2fa
ms.openlocfilehash: e33f22356de983c8c4e0211822d5027a33b48de6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775824"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>Autenticação de dois fatores com SMS no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [suíço-desenvolvedores](https://github.com/Swiss-Devs)

>[!WARNING]
> Os aplicativos autenticadores de autenticação de dois fatores (2FA), usando um algoritmo de senha única (TOTP) baseado em tempo, são a abordagem recomendada do setor para 2FA. 2FA usando TOTP é preferível ao SMS 2FA. Para obter mais informações, consulte [habilitar geração de código QR para aplicativos do autenticador TOTP no ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) para ASP.NET Core 2,0 e posterior.

Este tutorial mostra como configurar a autenticação de dois fatores (2FA) usando o SMS. Instruções são fornecidas para [twilio](https://www.twilio.com/) e [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), mas você pode usar qualquer outro provedor de SMS. Recomendamos que você conclua a [confirmação de conta e a recuperação de senha](xref:security/authentication/accconfirm) antes de iniciar este tutorial.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA). [Como baixar](xref:index#how-to-download-a-sample).

## <a name="create-a-new-aspnet-core-project"></a>Criar um projeto ASP.NET Core

Crie um novo aplicativo Web ASP.NET Core chamado `Web2FA` com contas de usuário individuais. Siga as instruções em <xref:security/enforcing-ssl> para configurar e exigir HTTPS.

### <a name="create-an-sms-account"></a>Criar uma conta SMS

Crie uma conta SMS, por exemplo, de [twilio](https://www.twilio.com/) ou [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/). Registre as credenciais de autenticação (para twilio: AccountId e authToken, para ASPSMS: userKey e password).

#### <a name="figuring-out-sms-provider-credentials"></a>Descobrindo credenciais do provedor de SMS

**Twilio**

Na guia painel da sua conta do twilio, copie o **SID da conta** e o **token de autenticação**.

**ASPSMS:**

Em suas configurações de conta, navegue até **userKey** e copie-o junto com sua **senha**.

Mais tarde, armazenaremos esses valores no com a ferramenta Secret-Manager dentro das `SMSAccountIdentification` chaves `SMSAccountPassword`e.

#### <a name="specifying-senderid--originator"></a>Especificando SenderId/originador

**Twilio:** Na guia números, copie o número de **telefone**twilio.

**ASPSMS:** No menu desbloquear originadores, desbloqueie um ou mais originadores ou escolha um originador alfanumérico (sem suporte em todas as redes).

Posteriormente, esse valor será armazenado com a ferramenta Secret-Manager dentro da chave `SMSAccountFrom`.

### <a name="provide-credentials-for-the-sms-service"></a>Fornecer credenciais para o serviço SMS

Usaremos o padrão de [Opções](xref:fundamentals/configuration/options) para acessar a conta de usuário e as configurações de chave.

* Crie uma classe para buscar a chave de SMS segura. Para este exemplo, a `SMSoptions` classe é criada no arquivo *Services/SMSoptions. cs* .

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

Defina o `SMSAccountIdentification` `SMSAccountPassword` e `SMSAccountFrom` com a [ferramenta Secret-Manager](xref:security/app-secrets). Por exemplo: 

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* Adicione o pacote NuGet para o provedor de SMS. No console do Gerenciador de pacotes (PMC), execute:

**Twilio**

`Install-Package Twilio`

**ASPSMS:**

`Install-Package ASPSMS`

* Adicione código no arquivo *Services/messageservices. cs* para habilitar o SMS. Use a seção twilio ou ASPSMS:

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>Configurar a inicialização para usar`SMSoptions`

Adicione `SMSoptions` ao contêiner de serviço no `ConfigureServices` método no *Startup.cs*:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>Habilitar a autenticação de dois fatores

Abra o arquivo de exibição *views/Manage/index. cshtml* Razor e remova os caracteres de comentário (portanto, nenhuma marcação é comentada).

## <a name="log-in-with-two-factor-authentication"></a>Fazer logon com a autenticação de dois fatores

* Executar o aplicativo e registrar um novo usuário

![Exibição de registro de aplicativo Web aberta no Microsoft Edge](2fa/_static/login2fa1.png)

* Toque em seu nome de usuário, que ativa o `Index` método de ação em gerenciar controlador. Em seguida, toque no link número de telefone **Adicionar** .

![Gerenciar exibição-toque no link "Adicionar"](2fa/_static/login2fa2.png)

* Adicione um número de telefone que receberá o código de verificação e toque em **enviar código de verificação**.

![Página Adicionar número de telefone](2fa/_static/login2fa3.png)

* Você receberá uma mensagem de texto com o código de verificação. Insira-o e toque em **Enviar**

![Página verificar número de telefone](2fa/_static/login2fa4.png)

Se você não receber uma mensagem de texto, consulte a página de log do twilio.

* O modo de exibição gerenciar mostra que seu número de telefone foi adicionado com êxito.

![Gerenciar exibição-número de telefone adicionado com êxito](2fa/_static/login2fa5.png)

* Toque em **habilitar** para habilitar a autenticação de dois fatores.

![Gerenciar exibição-habilitar a autenticação de dois fatores](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>Testar a autenticação de dois fatores

* Dê logoff.

* Fazer logon.

* A conta de usuário habilitou a autenticação de dois fatores, portanto, você precisa fornecer o segundo fator de autenticação. Neste tutorial, você habilitou a verificação de telefone. Os modelos internos também permitem que você configure o email como o segundo fator. Você pode configurar um segundo fator adicional para autenticação, como códigos QR. Toque em **Enviar**.

![Enviar exibição de código de verificação](2fa/_static/login2fa7.png)

* Insira o código obtido na mensagem SMS.

* Clicar na caixa de seleção **lembrar este navegador** lhe isentará de precisar usar o 2FA para fazer logon ao usar o mesmo dispositivo e navegador. Habilitar o 2FA e clicar em **lembrar este navegador** fornecerá uma forte proteção 2FA de usuários mal-intencionados tentando acessar sua conta, desde que eles não tenham acesso ao seu dispositivo. Você pode fazer isso em qualquer dispositivo privado que usa regularmente. Ao configurar **lembrar esse navegador**, você obtém a segurança adicional do 2FA de dispositivos que você não usa regularmente, e você tem a conveniência de não ter de passar pelo 2FA em seus próprios dispositivos.

![Verificar exibição](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>Bloqueio de conta para proteção contra ataques de força bruta

O bloqueio de conta é recomendado com 2FA. Depois que um usuário entrar por meio de uma conta local ou de uma conta social, cada tentativa com falha em 2FA será armazenada. Se o número máximo de tentativas de acesso com falha for atingido, o usuário será bloqueado (padrão: bloqueio de 5 minutos após 5 tentativas de acesso com falha). Uma autenticação bem-sucedida redefine a contagem de tentativas de acesso com falha e redefine o relógio. O tempo máximo de bloqueio e tentativas de acesso com falha podem ser definidos com [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) e [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan). O seguinte configura o bloqueio de conta por 10 minutos após 10 tentativas de acesso com falha:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

Confirme se [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) o PasswordSignInAsync `lockoutOnFailure` define `true`como:

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```

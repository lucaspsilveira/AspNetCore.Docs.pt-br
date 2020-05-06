---
title: Migrar da autenticação de associação do ASP.NET para o ASP.NET Core 2,0Identity
author: isaac2004
description: Saiba como migrar aplicativos ASP.NET existentes usando a autenticação de associação para Identityo ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: b5205ef69943f3744bba8381701008369dd0843c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774503"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a>Migrar da autenticação de associação do ASP.NET para a identidade do ASP.NET Core 2,0

Por [Isaac Levin](https://isaaclevin.com)

Este artigo demonstra como migrar o esquema de banco de dados para aplicativos ASP.NET usando a autenticação de associação para ASP.NET Core identidade 2,0.

> [!NOTE]
> Este documento fornece as etapas necessárias para migrar o esquema de banco de dados para aplicativos baseados em associação do ASP.NET para o esquema de banco de dados usado para ASP.NET Core identidade. Para obter mais informações sobre como migrar da autenticação baseada em associação do ASP.NET para ASP.NET Identity, consulte [migrar um aplicativo existente da associação do SQL para ASP.net Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). Para obter mais informações sobre a identidade de ASP.NET Core, consulte [introdução à identidade em ASP.NET Core](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Revisão do esquema de associação

Antes do ASP.NET 2,0, os desenvolvedores eram transtarefados na criação de todo o processo de autenticação e autorização para seus aplicativos. Com o ASP.NET 2,0, a associação foi introduzida, fornecendo uma solução padronizada para lidar com a segurança em aplicativos ASP.NET. Agora, os desenvolvedores conseguiram inicializar um esquema em um banco de dados SQL Server com o comando [Aspnet_regsql. exe](https://msdn.microsoft.com/library/ms229862.aspx) . Depois de executar esse comando, as tabelas a seguir foram criadas no banco de dados.

  ![Tabelas de associação](identity/_static/membership-tables.png)

Para migrar aplicativos existentes para ASP.NET Core identidade 2,0, os dados nessas tabelas precisam ser migrados para as tabelas usadas pelo novo esquema de identidade.

## <a name="aspnet-core-identity-20-schema"></a>Esquema ASP.NET Core Identity 2,0

ASP.NET Core 2,0 segue o princípio de [identidade](/aspnet/identity/index) introduzido no ASP.NET 4,5. Embora o princípio seja compartilhado, a implementação entre as estruturas é diferente, mesmo entre as versões do ASP.NET Core (consulte [migrar autenticação e identidade para ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).

A maneira mais rápida de exibir o esquema para ASP.NET Core identidade 2,0 é criar um novo aplicativo ASP.NET Core 2,0. Siga estas etapas no Visual Studio 2017:

1. Selecione **arquivo** > **novo** > **projeto**.
1. Crie um novo projeto de **aplicativo Web ASP.NET Core** chamado *CoreIdentitySample*.
1. Selecione **ASP.NET Core 2,0** na lista suspensa e, em seguida, selecione **aplicativo Web**. Este modelo produz um aplicativo [Razor Pages](xref:razor-pages/index) . Antes de clicar em **OK**, clique em **alterar autenticação**.
1. Escolha **contas de usuário individuais** para os modelos de identidade. Por fim, clique em **OK**e em **OK**. O Visual Studio cria um projeto usando o modelo de identidade ASP.NET Core.
1. Selecione **ferramentas** > **Gerenciador** > de pacotes NuGet**console do Gerenciador de pacotes** para abrir a janela do console do Gerenciador de **pacotes** (PMC).
1. Navegue até a raiz do projeto no PMC e execute o comando de `Update-Database` [núcleo Entity Framework (EF)](/ef/core) .

    ASP.NET Core identidade 2,0 usa EF Core para interagir com o banco de dados que está armazenando os dados de autenticação. Para que o aplicativo recém-criado funcione, é necessário haver um banco de dados para armazenar os mesmos. Depois de criar um novo aplicativo, a maneira mais rápida de inspecionar o esquema em um ambiente de banco de dados é criar o banco de dados usando [EF Core migrações](/ef/core/managing-schemas/migrations/). Esse processo cria um banco de dados, seja localmente ou em outro lugar, que imita esse esquema. Examine a documentação anterior para obter mais informações.

    EF Core comandos usam a cadeia de conexão para o banco de dados especificado em *appSettings. JSON*. A cadeia de conexão a seguir destina-se a um banco de dados no *localhost* denominado *ASP-NET-Core-Identity*. Nessa configuração, EF Core é configurado para usar a cadeia `DefaultConnection` de conexão.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. Selecione **Exibir** > **pesquisador de objetos do SQL Server**. Expanda o nó correspondente ao nome do banco de dados `ConnectionStrings:DefaultConnection` especificado na propriedade de *appSettings. JSON*.

    O `Update-Database` comando criou o banco de dados especificado com o esquema e qualquer dado necessário para a inicialização do aplicativo. A imagem a seguir ilustra a estrutura de tabela criada com as etapas anteriores.

    ![Tabelas de identidade](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Migrar o esquema

Há diferenças sutis nas estruturas de tabela e nos campos para a associação e a identidade de ASP.NET Core. O padrão mudou substancialmente para autenticação/autorização com aplicativos ASP.NET e ASP.NET Core. Os objetos de chave que ainda são usados com a identidade são *usuários* e *funções*. Aqui estão mapeando tabelas para *usuários*, *funções*e *UserRoles*.

### <a name="users"></a>Usuários

|*Identity<br>(dbo. AspNetUsers*        ||*Membership<br>(dbo. aspnet_Users/dbo. aspnet_Membership)*||
|----------------------------------------|-----------------------------------------------------------|
|**Nome do campo**                 |**Tipo**|**Nome do campo**                                    |**Tipo**|
|`Id`                           |cadeia de caracteres  |`aspnet_Users.UserId`                             |cadeia de caracteres  |
|`UserName`                     |cadeia de caracteres  |`aspnet_Users.UserName`                           |cadeia de caracteres  |
|`Email`                        |cadeia de caracteres  |`aspnet_Membership.Email`                         |cadeia de caracteres  |
|`NormalizedUserName`           |cadeia de caracteres  |`aspnet_Users.LoweredUserName`                    |cadeia de caracteres  |
|`NormalizedEmail`              |cadeia de caracteres  |`aspnet_Membership.LoweredEmail`                  |cadeia de caracteres  |
|`PhoneNumber`                  |cadeia de caracteres  |`aspnet_Users.MobileAlias`                        |cadeia de caracteres  |
|`LockoutEnabled`               |bit     |`aspnet_Membership.IsLockedOut`                   |bit     |

> [!NOTE]
> Nem todos os mapeamentos de campo se assemelham a relações um-para-um de associação a ASP.NET Core identidade. A tabela anterior usa o esquema de usuário de associação padrão e o mapeia para o esquema de identidade ASP.NET Core. Todos os outros campos personalizados que foram usados para associação precisam ser mapeados manualmente. Nesse mapeamento, não há nenhum mapa para senhas, pois os critérios de senha e os Salts de senha não são migrados entre os dois. **É recomendável deixar a senha como nula e solicitar que os usuários redefinam suas senhas.** Em ASP.NET Core identidade, `LockoutEnd` deve ser definido como alguma data no futuro se o usuário estiver bloqueado. Isso é mostrado no script de migração.

### <a name="roles"></a>Funções

|*Identity<br>(dbo. AspNetRoles)*        ||*Associação<br>(dbo. aspnet_Roles)*||
|----------------------------------------|-----------------------------------|
|**Nome do campo**                 |**Tipo**|**Nome do campo**   |**Tipo**         |
|`Id`                           |cadeia de caracteres  |`RoleId`         | cadeia de caracteres          |
|`Name`                         |cadeia de caracteres  |`RoleName`       | cadeia de caracteres          |
|`NormalizedName`               |cadeia de caracteres  |`LoweredRoleName`| cadeia de caracteres          |

### <a name="user-roles"></a>Funções de usuário

|*Identity<br>(dbo. AspNetUserRoles*||*Associação<br>(dbo. aspnet_UsersInRoles)*||
|------------------------------------|------------------------------------------|
|**Nome do campo**           |**Tipo**  |**Nome do campo**|**Tipo**                   |
|`RoleId`                 |cadeia de caracteres    |`RoleId`      |cadeia de caracteres                     |
|`UserId`                 |cadeia de caracteres    |`UserId`      |cadeia de caracteres                     |

Referencie as tabelas de mapeamento anteriores ao criar um script de migração para *usuários* e *funções*. O exemplo a seguir pressupõe que você tenha dois bancos de dados em um servidor de banco de dados. Um banco de dados contém o esquema de associação do ASP.NET existente. O outro banco de dados *CoreIdentitySample* foi criado usando as etapas descritas anteriormente. Os comentários são incluídos embutidos para obter mais detalhes.

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

Após a conclusão do script anterior, o aplicativo Identity ASP.NET Core criado anteriormente é populado com usuários de associação. Os usuários precisam alterar suas senhas antes de fazer logon.

> [!NOTE]
> Se o sistema de associação tiver usuários com nomes de usuário que não corresponderam a seu endereço de email, as alterações serão necessárias para o aplicativo criado anteriormente para acomodar isso. O modelo padrão espera `UserName` e `Email` deve ser o mesmo. Para situações em que elas são diferentes, o processo de logon precisa ser modificado para uso `UserName` em vez `Email`de.

No `PageModel` da página de logon, localizada em *Pages\Account\Login.cshtml.cs*, remova o `[EmailAddress]` atributo da propriedade *email* . Renomeie-o como *username*. Isso requer uma alteração sempre `EmailAddress` que for mencionado, na *exibição* e no *PageModel*. O resultado se parece com o seguinte:

 ![Logon fixo](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a portar os usuários da associação do SQL para o IdentityASP.NET Core 2,0. Para obter mais informações sobre IdentityASP.NET Core, consulte [Introduction Identityto ](xref:security/authentication/identity).

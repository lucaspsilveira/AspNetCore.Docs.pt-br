---
title: Migrar da autenticação de associação do ASP.NET para o ASP.NET Core 2,0Identity
author: isaac2004
description: Saiba como migrar aplicativos ASP.NET existentes usando a autenticação de associação para o ASP.NET Core 2,0 Identity .
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: f039772f4276d0e8bcec2629350eba2ec0e7418c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399680"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a>Migrar da autenticação de associação do ASP.NET para o ASP.NET Core 2,0Identity

Por [Isaac Levin](https://isaaclevin.com)

Este artigo demonstra a migração do esquema de banco de dados para aplicativos ASP.NET usando a autenticação de associação para o ASP.NET Core 2,0 Identity .

> [!NOTE]
> Este documento fornece as etapas necessárias para migrar o esquema de banco de dados para aplicativos baseados em associação do ASP.NET para o esquema de banco de dados usado para ASP.NET Core Identity . Para obter mais informações sobre como migrar da autenticação baseada em associação do ASP.NET para o ASP.NET Identity , consulte [migrar um aplicativo Identity existente da associação do SQL para o ASP.net ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). Para obter mais informações sobre ASP.NET Core Identity , consulte [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Revisão do esquema de associação

Antes do ASP.NET 2,0, os desenvolvedores eram transtarefados na criação de todo o processo de autenticação e autorização para seus aplicativos. Com o ASP.NET 2,0, a associação foi introduzida, fornecendo uma solução padronizada para lidar com a segurança em aplicativos ASP.NET. Agora, os desenvolvedores conseguiram inicializar um esquema em um banco de dados SQL Server com o comando [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) . Depois de executar esse comando, as tabelas a seguir foram criadas no banco de dados.

  ![Tabelas de associação](identity/_static/membership-tables.png)

Para migrar aplicativos existentes para o ASP.NET Core 2,0 Identity , os dados nessas tabelas precisam ser migrados para as tabelas usadas pelo novo Identity esquema.

## <a name="aspnet-core-identity-20-schema"></a>IdentityEsquema ASP.NET Core 2,0

ASP.NET Core 2,0 segue o [Identity](/aspnet/identity/index) princípio introduzido no ASP.NET 4,5. Embora o princípio seja compartilhado, a implementação entre as estruturas é diferente, mesmo entre as versões do ASP.NET Core (consulte [migrar a autenticação e Identity para ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).

A maneira mais rápida de exibir o esquema para o ASP.NET Core 2,0 Identity é criar um novo aplicativo ASP.NET Core 2,0. Siga estas etapas no Visual Studio 2017:

1. Selecione **Arquivo** > **Novo** > **Projeto**.
1. Crie um novo projeto de **aplicativo Web ASP.NET Core** chamado *CoreIdentitySample*.
1. Selecione **ASP.NET Core 2,0** na lista suspensa e, em seguida, selecione **aplicativo Web**. Este modelo produz um aplicativo de [ Razor páginas](xref:razor-pages/index) . Antes de clicar em **OK**, clique em **alterar autenticação**.
1. Escolha **contas de usuário individuais** para os Identity modelos. Por fim, clique em **OK**e em **OK**. O Visual Studio cria um projeto usando o Identity modelo de ASP.NET Core.
1. Selecione **ferramentas**  >  **Gerenciador de pacotes NuGet**  >  **console do Gerenciador de pacotes** para abrir a janela do **console do Gerenciador de pacotes** (PMC).
1. Navegue até a raiz do projeto no PMC e execute o comando de [núcleo Entity Framework (EF)](/ef/core) `Update-Database` .

    ASP.NET Core 2,0 Identity usa EF Core para interagir com o banco de dados que está armazenando os dados de autenticação. Para que o aplicativo recém-criado funcione, é necessário haver um banco de dados para armazenar os mesmos. Depois de criar um novo aplicativo, a maneira mais rápida de inspecionar o esquema em um ambiente de banco de dados é criar o banco de dados usando [EF Core migrações](/ef/core/managing-schemas/migrations/). Esse processo cria um banco de dados, seja localmente ou em outro lugar, que imita esse esquema. Examine a documentação anterior para obter mais informações.

    EF Core comandos usam a cadeia de conexão para o banco de dados especificado em *appsettings.jsem*. A cadeia de conexão a seguir destina-se a um banco de dados no *localhost* denominado *ASP-NET-Core-Identity*. Nessa configuração, EF Core é configurado para usar a `DefaultConnection` cadeia de conexão.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. Selecione **Exibir**  >  **pesquisador de objetos do SQL Server**. Expanda o nó correspondente ao nome do banco de dados especificado na `ConnectionStrings:DefaultConnection` propriedade de *appsettings.jsem*.

    O `Update-Database` comando criou o banco de dados especificado com o esquema e qualquer dado necessário para a inicialização do aplicativo. A imagem a seguir ilustra a estrutura de tabela criada com as etapas anteriores.

    ![IdentityTabelas](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Migrar o esquema

Há diferenças sutis nas estruturas de tabela e nos campos para associação e ASP.NET Core Identity . O padrão mudou substancialmente para autenticação/autorização com aplicativos ASP.NET e ASP.NET Core. Os objetos de chave que ainda são usados com o Identity são *usuários* e *funções*. Aqui estão mapeando tabelas para *usuários*, *funções*e *UserRoles*.

### <a name="users"></a>Usuários

|*Identity<br>dbo. AspNetUsers*        ||*Membership <br> (dbo. aspnet_Users/dbo. aspnet_Membership)*||
|----------------------------------------|-----------------------------------------------------------|
|**Nome do campo**                 |**Tipo**|**Nome do campo**                                    |**Tipo**|
|`Id`                           |string  |`aspnet_Users.UserId`                             |string  |
|`UserName`                     |string  |`aspnet_Users.UserName`                           |string  |
|`Email`                        |string  |`aspnet_Membership.Email`                         |string  |
|`NormalizedUserName`           |string  |`aspnet_Users.LoweredUserName`                    |string  |
|`NormalizedEmail`              |string  |`aspnet_Membership.LoweredEmail`                  |string  |
|`PhoneNumber`                  |string  |`aspnet_Users.MobileAlias`                        |string  |
|`LockoutEnabled`               |bit     |`aspnet_Membership.IsLockedOut`                   |bit     |

> [!NOTE]
> Nem todos os mapeamentos de campo se assemelham a relações um-para-um de associação a ASP.NET Core Identity . A tabela anterior usa o esquema de usuário de associação padrão e o mapeia para o esquema de ASP.NET Core Identity . Todos os outros campos personalizados que foram usados para associação precisam ser mapeados manualmente. Nesse mapeamento, não há nenhum mapa para senhas, pois os critérios de senha e os Salts de senha não são migrados entre os dois. **É recomendável deixar a senha como nula e solicitar que os usuários redefinam suas senhas.** Em ASP.NET Core Identity , `LockoutEnd` deve ser definido como alguma data no futuro se o usuário estiver bloqueado. Isso é mostrado no script de migração.

### <a name="roles"></a>Funções

|*Identity<br>dbo. AspNetRoles)*        ||*Associação <br> (dbo. aspnet_Roles)*||
|----------------------------------------|-----------------------------------|
|**Nome do campo**                 |**Tipo**|**Nome do campo**   |**Tipo**         |
|`Id`                           |string  |`RoleId`         | string          |
|`Name`                         |string  |`RoleName`       | string          |
|`NormalizedName`               |string  |`LoweredRoleName`| string          |

### <a name="user-roles"></a>Funções de usuário

|*Identity<br>dbo. AspNetUserRoles*||*Associação <br> (dbo. aspnet_UsersInRoles)*||
|------------------------------------|------------------------------------------|
|**Nome do campo**           |**Tipo**  |**Nome do campo**|**Tipo**                   |
|`RoleId`                 |string    |`RoleId`      |string                     |
|`UserId`                 |string    |`UserId`      |string                     |

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

Após a conclusão do script anterior, o Identity aplicativo ASP.NET Core criado anteriormente é populado com usuários de associação. Os usuários precisam alterar suas senhas antes de fazer logon.

> [!NOTE]
> Se o sistema de associação tiver usuários com nomes de usuário que não corresponderam a seu endereço de email, as alterações serão necessárias para o aplicativo criado anteriormente para acomodar isso. O modelo padrão espera `UserName` e `Email` deve ser o mesmo. Para situações em que elas são diferentes, o processo de logon precisa ser modificado para uso `UserName` em vez de `Email` .

No `PageModel` da página de logon, localizada em *Pages\Account\Login.cshtml.cs*, remova o `[EmailAddress]` atributo da propriedade *email* . Renomeie-o como *username*. Isso requer uma alteração sempre `EmailAddress` que for mencionado, na *exibição* e no *PageModel*. O resultado se parece com o seguinte:

 ![Logon fixo](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a portar os usuários da associação do SQL para o ASP.NET Core 2,0 Identity . Para obter mais informações sobre ASP.NET Core Identity , consulte [Introduction Identity to ](xref:security/authentication/identity).

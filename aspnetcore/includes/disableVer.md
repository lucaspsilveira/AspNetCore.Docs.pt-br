<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>Desabilitar verificação de conta padrão

Com os modelos padrão, o usuário é redirecionado para o `Account.RegisterConfirmation` onde ele pode selecionar um link para que a conta seja confirmada. O padrão `Account.RegisterConfirmation` é usado ***somente*** para teste; a verificação automática de conta deve ser desabilitada em um aplicativo de produção.

Para exigir uma conta confirmada e impedir o logon imediato no registro, defina `DisplayConfirmAccountLink = false` em */areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs*:

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]
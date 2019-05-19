# <a name="additional-claims-sample-app"></a>Aplicativo de exemplo de declarações adicionais

O aplicativo de exemplo demonstra como:

* Obter o nome fornecido e o sobrenome do usuário do Google e armazenar as declarações de nome com os valores fornecidos pelo Google.
* Store o token de acesso do Google, o usuário `AuthenticationProperties`.

Para usar o aplicativo de exemplo:

1. Registrar o aplicativo e obter uma ID de cliente válido e o segredo do cliente para autenticação do Google. Para obter mais informações, consulte [configuração de logon externo do Google](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins).
1. Forneça a ID de cliente e o segredo do cliente para o aplicativo na [GoogleOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) de `Startup.ConfigureServices`.
1. Execute o aplicativo e solicite a página Minhas declarações. Quando o usuário não estiver conectado, o aplicativo redireciona para o Google. Entrar com o Google. Google redireciona o usuário retorne ao aplicativo (`/MyClaims`). O usuário é autenticado e a página Minhas declarações é carregada. O nome e sobrenome declarações estão presentes em **declarações de usuário** com os valores fornecidos pelo Google. O token de acesso é exibido sob **as propriedades de autenticação**.

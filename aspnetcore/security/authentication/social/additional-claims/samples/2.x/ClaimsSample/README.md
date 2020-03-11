# <a name="additional-claims-sample-app"></a>Aplicativo de exemplo de declarações adicionais

O aplicativo de exemplo demonstra como:

* Obtenha o nome do usuário e o sobrenome do Google e armazene as declarações de nome com os valores fornecidos pelo Google.
* Armazene o token de acesso do Google no `AuthenticationProperties`do usuário.

Para usar o aplicativo de exemplo:

1. Registre o aplicativo e obtenha uma ID de cliente e um segredo do cliente válidos para a autenticação do Google. Para obter mais informações, consulte [configuração de logon externo do Google](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins).
1. Forneça a ID do cliente e o segredo do cliente para o aplicativo no [googleoptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) do `Startup.ConfigureServices`.
1. Execute o aplicativo e solicite a página minhas declarações. Quando o usuário não está conectado, o aplicativo redireciona para o Google. Entre com o Google. O Google redireciona o usuário de volta para o aplicativo (`/MyClaims`). O usuário é autenticado e a página minhas declarações é carregada. As declarações nome e sobrenome fornecidas estão presentes em **declarações do usuário** com os valores fornecidos pelo Google. O token de acesso é exibido em **Propriedades de autenticação**.

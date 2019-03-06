# <a name="aspnet-core-authorization-sample"></a>Amostra de autorização do ASP.NET Core

Este exemplo ilustra o uso de autorização de páginas do Razor por convenções. Este exemplo demonstra os recursos descritos a [convenções de autorização de páginas do Razor](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization) tópico.

Autorização de usuário neste exemplo usa a autenticação de cookie recursos descritos a [usar a autenticação de cookie sem o ASP.NET Core Identity](https://docs.microsoft.com/aspnet/core/security/authentication/cookie) tópico. Os conceitos e os exemplos mostrados neste tópico se aplicam igualmente a aplicativos que usam o ASP.NET Core Identity. Para obter informações sobre como usar o ASP.NET Core Identity, consulte [Introdução à identidade no ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/authentication/identity).

Use o endereço de email **maria.rodriguez@contoso.com** para autenticar o usuário com qualquer senha. O usuário é autenticado na `AuthenticateUser` método na *Pages/Account/Login.cshtml.cs* arquivo. Em um exemplo do mundo real, o usuário deve ser autenticado em relação a um banco de dados.

## <a name="examples-in-this-sample"></a>Exemplos desta amostra

| Recurso | Descrição |
| --- | --- |
| [AuthorizePage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) | Adiciona uma [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) para a página com o caminho especificado. |
| [AuthorizeFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) | Adiciona uma [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) para todas as páginas em uma pasta com o caminho especificado. |
| [AllowAnonymousToPage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) | Adiciona uma [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) para uma página com o caminho especificado. |
| [AllowAnonymousToFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) | Adiciona uma [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) para todas as páginas em uma pasta com o caminho especificado. |

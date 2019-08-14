# <a name="aspnet-core-authorization-sample"></a>Exemplo de autorização de ASP.NET Core

Este exemplo ilustra o uso de Razor Pages autorização por convenções. Este exemplo demonstra os recursos descritos no tópico [Razor Pages convenções de autorização](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization) .

A autorização do usuário neste exemplo usa os recursos de autenticação de cookie descritos no tópico [usar autenticação de cookie sem ASP.NET Core identidade](https://docs.microsoft.com/aspnet/core/security/authentication/cookie) . Os conceitos e exemplos mostrados neste tópico se aplicam igualmente a aplicativos que usam ASP.NET Core identidade. Para obter informações sobre como usar a identidade ASP.NET Core, consulte [introdução à identidade em ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/authentication/identity).

Use o endereço **maria.rodriguez@contoso.com** de email para autenticar o usuário com qualquer senha. O usuário é autenticado no `AuthenticateUser` método no arquivo *pages/Account/Login. cshtml. cs* . Em um exemplo do mundo real, o usuário seria autenticado em um banco de dados.

## <a name="examples-in-this-sample"></a>Exemplos desta amostra

| Recurso | Descrição |
| --- | --- |
| [AuthorizePage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) | Adiciona um [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à página com o caminho especificado. |
| [AuthorizeFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) | Adiciona um [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) a todas as páginas em uma pasta com o caminho especificado. |
| [AllowAnonymousToPage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) | Adiciona um [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) a uma página com o caminho especificado. |
| [AllowAnonymousToFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) | Adiciona um [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) a todas as páginas em uma pasta com o caminho especificado. |

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Usar o SQLite para desenvolvimento, SQL Server para produção

Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento. O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização. `IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

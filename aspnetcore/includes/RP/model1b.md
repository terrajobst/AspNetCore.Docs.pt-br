<!-- THIS INCLUDE USED BY MVC AND RP -->
Adicione as seguintes propriedades à classe `Movie`:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

A classe `Movie` contém:

* O campo `ID` é necessário para o banco de dados para a chave primária.
* `[DataType(DataType.Date)]`:  O atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica o tipo de dados (Data). Com esse atributo:

  * O usuário não precisa inserir informações de tempo no campo de data.
  * Somente a data é exibida, não as informações de tempo.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) são abordados em um tutorial posterior.
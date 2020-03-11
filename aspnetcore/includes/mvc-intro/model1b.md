Adicione as seguintes propriedades à classe `Movie`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

A classe `Movie` contém:

* O campo `Id` que é exigido pelo banco de dados para a chave primária.
* `[DataType(DataType.Date)]`: o atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica o tipo dos dados (`Date`). Com esse atributo:

  * O usuário não precisa inserir informações de tempo no campo de data.
  * Somente a data é exibida, não as informações de tempo.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) são abordados em um tutorial posterior.
<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a>Adicionar regras de validação ao modelo de filme

O namespace DataAnnotations fornece um conjunto de atributos de validação internos aplicados de forma declarativa a uma classe ou propriedade. DataAnnotations também contém atributos de formatação como `DataType`, que ajudam com a formatação e não fornecem validação.

Atualize a classe `Movie` para aproveitar os atributos de validação `Required`, `StringLength`, `RegularExpression` e `Range` internos.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

Os atributos de validação especificam o comportamento que você deseja impor nas propriedades de modelo às quais eles são aplicados:

* Os atributos `Required` e `MinimumLength` indicam que uma propriedade deve ter um valor; porém, nada impede que um usuário insira um espaço em branco para atender a essa validação.
* O atributo `RegularExpression` é usado para limitar quais caracteres podem ser inseridos. No código anterior, "Gênero":

  * Deve usar apenas letras.
  * A primeira letra deve ser maiúscula. Espaços em branco, números e caracteres especiais não são permitidos.

* A "Classificação" `RegularExpression`:

  * Exige que o primeiro caractere seja uma letra maiúscula.
  * Permite caracteres especiais e números nos espaços subsequentes. "PG-13" é válido para uma classificação, mas é um erro em "Gênero".

* O atributo `Range` restringe um valor a um intervalo especificado.
* O atributo `StringLength` permite definir o tamanho máximo de uma propriedade de cadeia de caracteres e, opcionalmente, seu tamanho mínimo.
* Os tipos de valor (como `decimal`, `int`, `float`, `DateTime`) são inerentemente necessários e não precisam do atributo `[Required]`.

Ter as regras de validação automaticamente impostas pelo ASP.NET Core ajuda a tornar seu aplicativo mais robusto. Também garante que você não se esqueça de validar algo e inadvertidamente permita dados incorretos no banco de dados.

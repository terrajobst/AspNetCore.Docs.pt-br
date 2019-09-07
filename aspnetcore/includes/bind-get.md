> [!WARNING]
> Por motivos de segurança, você deve aceitar associar os dados da solicitação `GET` às propriedades do modelo de página. Verifique a entrada do usuário antes de mapeá-la para as propriedades. Optar pela `GET` associação é útil ao abordar cenários que dependem de cadeias de caracteres de consulta ou valores de rota.
>
> Para associar uma propriedade em `GET` solicitações, defina a propriedade do `SupportsGet` atributo [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) como `true`:
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Para obter mais informações, [consulte ASP.NET Core Community encontros: Ligação em obter discussão (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).

> [!WARNING]
> Por motivos de segurança, você deve aceitar associar os dados da solicitação `GET` às propriedades do modelo de página. Verifique a entrada do usuário antes de mapeá-la para as propriedades. Optar pela Associação de `GET` é útil ao lidar com cenários que dependem de cadeias de caracteres de consulta ou valores de rota.
>
> Para associar uma propriedade em solicitações de `GET`, defina a propriedade `SupportsGet` do atributo [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) como `true`:
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Para obter mais informações, consulte [ASP.NET Core Community encontros: associar em obter discussão (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).

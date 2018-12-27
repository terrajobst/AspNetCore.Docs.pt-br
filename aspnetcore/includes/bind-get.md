> [!WARNING]
> Por motivos de segurança, você deve aceitar associar os dados da solicitação `GET` às propriedades do modelo de página. Verifique a entrada do usuário antes de mapeá-la para as propriedades. Aceitar a associação de `GET` é útil ao lidar com cenários que contam com a cadeia de caracteres de consulta ou com os valores de rota.
>
> Para associar uma propriedade às solicitações `GET`, defina a propriedade `SupportsGet` do atributo [[BindProperty]](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) como `true`: `[BindProperty(SupportsGet = true)]`

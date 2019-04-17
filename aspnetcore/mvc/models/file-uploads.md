---
title: Uploads de arquivos no ASP.NET Core
author: ardalis
description: Como usar o model binding e o streaming para carregar arquivos no ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: 831f0c84f0ff062e9e24ccbf4ca81b7143c66e66
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468547"
---
# <a name="file-uploads-in-aspnet-core"></a>Uploads de arquivos no ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

Ações do ASP.NET MVC dão suporte ao upload de um ou mais arquivos usando model binding simples para arquivos menores ou streaming para arquivos maiores.

[Exibir ou baixar amostra do GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a>Upload de arquivos pequenos com o model binding

Para carregar arquivos pequenos, você pode usar um formulário HTML com várias partes ou construir uma solicitação POST usando JavaScript. Um formulário de exemplo usando Razor, que dá suporte a vários arquivos carregados, é mostrado abaixo:

```html
<form method="post" enctype="multipart/form-data" asp-controller="UploadFiles" asp-action="Index">
    <div class="form-group">
        <div class="col-md-10">
            <p>Upload one or more files using this form:</p>
            <input type="file" name="files" multiple>
        </div>
    </div>
    <div class="form-group">
        <div class="col-md-10">
            <input type="submit" value="Upload">
        </div>
    </div>
</form>
```

Para dar suporte a uploads de arquivos, os formulários HTML devem especificar um `enctype` igual a `multipart/form-data`. O elemento de entrada `files` mostrado acima dá suporte ao upload de vários arquivos. Omita o atributo `multiple` neste elemento de entrada para permitir o upload de apenas um arquivo. A marcação acima é renderizada em um navegador como:

![Formulário de upload de arquivo](file-uploads/_static/upload-form.png)

Os arquivos individuais carregados no servidor podem ser acessados por meio do [Model binding](xref:mvc/models/model-binding) usando a interface [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile). `IFormFile` tem esta estrutura:

```csharp
public interface IFormFile
{
    string ContentType { get; }
    string ContentDisposition { get; }
    IHeaderDictionary Headers { get; }
    long Length { get; }
    string Name { get; }
    string FileName { get; }
    Stream OpenReadStream();
    void CopyTo(Stream target);
    Task CopyToAsync(Stream target, CancellationToken cancellationToken = null);
}
```

> [!WARNING]
> Não dependa ou confie na propriedade `FileName` sem validação. A propriedade `FileName` deve ser usada somente para fins de exibição.

Ao fazer upload de arquivos usando model binding e a interface `IFormFile`, o método de ação pode aceitar um único `IFormFile` ou um `IEnumerable<IFormFile>` (ou `List<IFormFile>`) que representa vários arquivos. O exemplo a seguir executa um loop em um ou mais arquivos carregados, salva-os no sistema de arquivos local e retorna o número total e o tamanho dos arquivos carregados.

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

Arquivos carregados usando a técnica `IFormFile` são armazenados em buffer na memória ou no disco no servidor Web antes de serem processados. Dentro do método de ação, o conteúdo de `IFormFile` podem ser acessado como um fluxo. Além do sistema de arquivos local, os arquivos podem ser transmitidos para o [Armazenamento de Blobs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) ou para o [Entity Framework](/ef/core/index).

Para armazenar dados de arquivo binário em um banco de dados usando o Entity Framework, defina uma propriedade do tipo `byte[]` na entidade:

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

Especifique uma propriedade de viewmodel do tipo `IFormFile`:

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> `IFormFile` pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade de viewmodel, como mostrado acima.

Copie o `IFormFile` para um fluxo e salve-o na matriz de bytes:

```csharp
// POST: /Account/Register
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Register(RegisterViewModel model)
{
    ViewData["ReturnUrl"] = returnUrl;
    if  (ModelState.IsValid)
    {
        var user = new ApplicationUser 
        {
            UserName = model.Email,
            Email = model.Email
        };
        using (var memoryStream = new MemoryStream())
        {
            await model.AvatarImage.CopyToAsync(memoryStream);
            user.AvatarImage = memoryStream.ToArray();
        }
    // additional logic omitted

    // Don't rely on or trust the model.AvatarImage.FileName property 
    // without validation.
}
```

> [!NOTE]
> Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.

## <a name="uploading-large-files-with-streaming"></a>Upload de arquivos grandes usando streaming

Se o tamanho ou a frequência dos uploads de arquivos estiver causando problemas de recursos para o aplicativo, considere transmitir o upload dos arquivos por streaming em vez de armazená-los completamente em buffer, como na abordagem de model binding mostrada acima. Embora o uso de `IFormFile` e do model binding seja uma solução muito mais simples, o streaming requer a execução de algumas etapas para ser implementado corretamente.

> [!NOTE]
> Qualquer arquivo armazenado em buffer que exceder 64KB será movido do RAM para um arquivo temporário em disco no servidor. Os recursos (disco, RAM) usados pelos uploads de arquivos dependem do número e do tamanho dos uploads de arquivos simultâneos. Streaming não é tanto uma questão de desempenho, e sim de escala. Se você tentar armazenar muitos uploads em buffer, seu site falhará quando ficar sem memória ou sem espaço em disco.

O exemplo a seguir demonstra como usar JavaScript/Angular para fazer o streaming para uma ação do controlador. O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passada nos cabeçalhos HTTP em vez do corpo da solicitação. Como um método de ação processa os dados carregados diretamente, o model binding é desabilitado por outro filtro. Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado. Após todas as seções serem lidas, a ação executa seu próprio model binding.

A ação inicial carrega o formulário e salva um token antifalsificação em um cookie (por meio do atributo `GenerateAntiforgeryTokenCookieForAjax`):

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

O atributo usa o suporte interno [Antifalsificação](xref:security/anti-request-forgery) do ASP.NET Core para definir um cookie com um token de solicitação:

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

O Angular passa automaticamente um token antifalsificação em um cabeçalho de solicitação chamado `X-XSRF-TOKEN`. O aplicativo ASP.NET Core MVC é configurado para se referir a esse cabeçalho em sua configuração em *Startup.cs*:

[!code-csharp[](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

O atributo `DisableFormValueModelBinding`, mostrado abaixo, é usado para desabilitar o model binding para o método de ação `Upload`.

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

Como o model binding é desabilitado, o método de ação `Upload` não aceita parâmetros. Ele trabalha diretamente com a propriedade `Request` de `ControllerBase`. Um `MultipartReader` é usado para ler cada seção. O arquivo é salvo com um nome de arquivo GUID e os dados de chave/valor são armazenados em um `KeyValueAccumulator`. Após todas as seções terem sido lidas, o conteúdo do `KeyValueAccumulator` é usado para associar os dados do formulário a um tipo de modelo.

O método `Upload` completo é mostrado abaixo:

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a>Solução de problemas

Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.

### <a name="unexpected-not-found-error-with-iis"></a>Erro não encontrado inesperado com o IIS

O erro a seguir indica que o upload do arquivo excede o `maxAllowedContentLength` configurado do servidor:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

A configuração padrão é `30000000`, que é aproximadamente 28,6 MB. O valor pode ser personalizado editando *web.config*:

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- This will handle requests up to 50MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Essa configuração só se aplica ao IIS. Esse comportamento não ocorre por padrão quando a hospedagem é feita no Kestrel. Para obter mais informações, consulte [Limites de solicitação \<requestLimits\>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

### <a name="null-reference-exception-with-iformfile"></a>Exceção de referência nula com IFormFile

Se o controlador estiver aceitando arquivos carregados usando `IFormFile`, mas você observar que o valor sempre é nulo, confirme que seu formulário HTML está especificando um valor de `enctype` igual a `multipart/form-data`. Se esse atributo não estiver definido no elemento `<form>`, o upload do arquivo não ocorrerá e os argumentos `IFormFile` associados serão nulos.

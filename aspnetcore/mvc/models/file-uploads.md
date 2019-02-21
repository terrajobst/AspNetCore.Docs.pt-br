---
title: Uploads de arquivos no ASP.NET Core
author: ardalis
description: Como usar o model binding e o streaming para carregar arquivos no ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: mvc/models/file-uploads
ms.openlocfilehash: 5e6e2cd5fac25e2abe27915c2f4caa64b13e90bd
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56409977"
---
# <a name="file-uploads-in-aspnet-core"></a><span data-ttu-id="49a9e-103">Uploads de arquivos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49a9e-103">File uploads in ASP.NET Core</span></span>

<span data-ttu-id="49a9e-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="49a9e-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="49a9e-105">Ações do ASP.NET MVC dão suporte ao upload de um ou mais arquivos usando model binding simples para arquivos menores ou streaming para arquivos maiores.</span><span class="sxs-lookup"><span data-stu-id="49a9e-105">ASP.NET MVC actions support uploading of one or more files using simple model binding for smaller files or streaming for larger files.</span></span>

[<span data-ttu-id="49a9e-106">Exibir ou baixar a amostra do GitHub</span><span class="sxs-lookup"><span data-stu-id="49a9e-106">View or download sample from GitHub</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a><span data-ttu-id="49a9e-107">Upload de arquivos pequenos com o model binding</span><span class="sxs-lookup"><span data-stu-id="49a9e-107">Uploading small files with model binding</span></span>

<span data-ttu-id="49a9e-108">Para carregar arquivos pequenos, você pode usar um formulário HTML com várias partes ou construir uma solicitação POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="49a9e-108">To upload small files, you can use a multi-part HTML form or construct a POST request using JavaScript.</span></span> <span data-ttu-id="49a9e-109">Um formulário de exemplo usando Razor, que dá suporte a vários arquivos carregados, é mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="49a9e-109">An example form using Razor, which supports multiple uploaded files, is shown below:</span></span>

```html
<form method="post" enctype="multipart/form-data" asp-controller="UploadFiles" asp-action="Index">
    <div class="form-group">
        <div class="col-md-10">
            <p>Upload one or more files using this form:</p>
            <input type="file" name="files" multiple />
        </div>
    </div>
    <div class="form-group">
        <div class="col-md-10">
            <input type="submit" value="Upload" />
        </div>
    </div>
</form>
```

<span data-ttu-id="49a9e-110">Para dar suporte a uploads de arquivos, os formulários HTML devem especificar um `enctype` igual a `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="49a9e-110">In order to support file uploads, HTML forms must specify an `enctype` of `multipart/form-data`.</span></span> <span data-ttu-id="49a9e-111">O elemento de entrada `files` mostrado acima dá suporte ao upload de vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="49a9e-111">The `files` input element shown above supports uploading multiple files.</span></span> <span data-ttu-id="49a9e-112">Omita o atributo `multiple` neste elemento de entrada para permitir o upload de apenas um arquivo.</span><span class="sxs-lookup"><span data-stu-id="49a9e-112">Omit the `multiple` attribute on this input element to allow just a single file to be uploaded.</span></span> <span data-ttu-id="49a9e-113">A marcação acima é renderizada em um navegador como:</span><span class="sxs-lookup"><span data-stu-id="49a9e-113">The above markup renders in a browser as:</span></span>

![Formulário de upload de arquivo](file-uploads/_static/upload-form.png)

<span data-ttu-id="49a9e-115">Os arquivos individuais carregados no servidor podem ser acessados por meio do [Model binding](xref:mvc/models/model-binding) usando a interface [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile).</span><span class="sxs-lookup"><span data-stu-id="49a9e-115">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using the [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) interface.</span></span> <span data-ttu-id="49a9e-116">`IFormFile` tem esta estrutura:</span><span class="sxs-lookup"><span data-stu-id="49a9e-116">`IFormFile` has the following structure:</span></span>

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
> <span data-ttu-id="49a9e-117">Não dependa ou confie na propriedade `FileName` sem validação.</span><span class="sxs-lookup"><span data-stu-id="49a9e-117">Don't rely on or trust the `FileName` property without validation.</span></span> <span data-ttu-id="49a9e-118">A propriedade `FileName` deve ser usada somente para fins de exibição.</span><span class="sxs-lookup"><span data-stu-id="49a9e-118">The `FileName` property should only be used for display purposes.</span></span>

<span data-ttu-id="49a9e-119">Ao fazer upload de arquivos usando model binding e a interface `IFormFile`, o método de ação pode aceitar um único `IFormFile` ou um `IEnumerable<IFormFile>` (ou `List<IFormFile>`) que representa vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="49a9e-119">When uploading files using model binding and the `IFormFile` interface, the action method can accept either a single `IFormFile` or an `IEnumerable<IFormFile>` (or `List<IFormFile>`) representing several files.</span></span> <span data-ttu-id="49a9e-120">O exemplo a seguir executa um loop em um ou mais arquivos carregados, salva-os no sistema de arquivos local e retorna o número total e o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="49a9e-120">The following example loops through one or more uploaded files, saves them to the local file system, and returns the total number and size of files uploaded.</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

<span data-ttu-id="49a9e-121">Arquivos carregados usando a técnica `IFormFile` são armazenados em buffer na memória ou no disco no servidor Web antes de serem processados.</span><span class="sxs-lookup"><span data-stu-id="49a9e-121">Files uploaded using the `IFormFile` technique are buffered in memory or on disk on the web server before being processed.</span></span> <span data-ttu-id="49a9e-122">Dentro do método de ação, o conteúdo de `IFormFile` podem ser acessado como um fluxo.</span><span class="sxs-lookup"><span data-stu-id="49a9e-122">Inside the action method, the `IFormFile` contents are accessible as a stream.</span></span> <span data-ttu-id="49a9e-123">Além do sistema de arquivos local, os arquivos podem ser transmitidos para o [Armazenamento de Blobs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) ou para o [Entity Framework](/ef/core/index).</span><span class="sxs-lookup"><span data-stu-id="49a9e-123">In addition to the local file system, files can be streamed to [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) or [Entity Framework](/ef/core/index).</span></span>

<span data-ttu-id="49a9e-124">Para armazenar dados de arquivo binário em um banco de dados usando o Entity Framework, defina uma propriedade do tipo `byte[]` na entidade:</span><span class="sxs-lookup"><span data-stu-id="49a9e-124">To store binary file data in a database using Entity Framework, define a property of type `byte[]` on the entity:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

<span data-ttu-id="49a9e-125">Especifique uma propriedade de viewmodel do tipo `IFormFile`:</span><span class="sxs-lookup"><span data-stu-id="49a9e-125">Specify a viewmodel property of type `IFormFile`:</span></span>

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="49a9e-126">`IFormFile` pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade de viewmodel, como mostrado acima.</span><span class="sxs-lookup"><span data-stu-id="49a9e-126">`IFormFile` can be used directly as an action method parameter or as a viewmodel property, as shown above.</span></span>

<span data-ttu-id="49a9e-127">Copie o `IFormFile` para um fluxo e salve-o na matriz de bytes:</span><span class="sxs-lookup"><span data-stu-id="49a9e-127">Copy the `IFormFile` to a stream and save it to the byte array:</span></span>

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
> <span data-ttu-id="49a9e-128">Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="49a9e-128">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>

## <a name="uploading-large-files-with-streaming"></a><span data-ttu-id="49a9e-129">Upload de arquivos grandes usando streaming</span><span class="sxs-lookup"><span data-stu-id="49a9e-129">Uploading large files with streaming</span></span>

<span data-ttu-id="49a9e-130">Se o tamanho ou a frequência dos uploads de arquivos estiver causando problemas de recursos para o aplicativo, considere transmitir o upload dos arquivos por streaming em vez de armazená-los completamente em buffer, como na abordagem de model binding mostrada acima.</span><span class="sxs-lookup"><span data-stu-id="49a9e-130">If the size or frequency of file uploads is causing resource problems for the app, consider streaming the file upload rather than buffering it in its entirety, as the model binding approach shown above does.</span></span> <span data-ttu-id="49a9e-131">Embora o uso de `IFormFile` e do model binding seja uma solução muito mais simples, o streaming requer a execução de algumas etapas para ser implementado corretamente.</span><span class="sxs-lookup"><span data-stu-id="49a9e-131">While using `IFormFile` and model binding is a much simpler solution, streaming requires a number of steps to implement properly.</span></span>

> [!NOTE]
> <span data-ttu-id="49a9e-132">Qualquer arquivo armazenado em buffer que exceder 64KB será movido do RAM para um arquivo temporário em disco no servidor.</span><span class="sxs-lookup"><span data-stu-id="49a9e-132">Any single buffered file exceeding 64KB will be moved from RAM to a temp file on disk on the server.</span></span> <span data-ttu-id="49a9e-133">Os recursos (disco, RAM) usados pelos uploads de arquivos dependem do número e do tamanho dos uploads de arquivos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="49a9e-133">The resources (disk, RAM) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="49a9e-134">Streaming não é tanto uma questão de desempenho, e sim de escala.</span><span class="sxs-lookup"><span data-stu-id="49a9e-134">Streaming isn't so much about perf, it's about scale.</span></span> <span data-ttu-id="49a9e-135">Se você tentar armazenar muitos uploads em buffer, seu site falhará quando ficar sem memória ou sem espaço em disco.</span><span class="sxs-lookup"><span data-stu-id="49a9e-135">If you try to buffer too many uploads, your site will crash when it runs out of memory or disk space.</span></span>

<span data-ttu-id="49a9e-136">O exemplo a seguir demonstra como usar JavaScript/Angular para fazer o streaming para uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="49a9e-136">The following example demonstrates using JavaScript/Angular to stream to a controller action.</span></span> <span data-ttu-id="49a9e-137">O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passada nos cabeçalhos HTTP em vez do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="49a9e-137">The file's antiforgery token is generated using a custom filter attribute and passed in HTTP headers instead of in the request body.</span></span> <span data-ttu-id="49a9e-138">Como um método de ação processa os dados carregados diretamente, o model binding é desabilitado por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="49a9e-138">Because the action method processes the uploaded data directly, model binding is disabled by another filter.</span></span> <span data-ttu-id="49a9e-139">Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="49a9e-139">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="49a9e-140">Após todas as seções serem lidas, a ação executa seu próprio model binding.</span><span class="sxs-lookup"><span data-stu-id="49a9e-140">Once all sections have been read, the action performs its own model binding.</span></span>

<span data-ttu-id="49a9e-141">A ação inicial carrega o formulário e salva um token antifalsificação em um cookie (por meio do atributo `GenerateAntiforgeryTokenCookieForAjax`):</span><span class="sxs-lookup"><span data-stu-id="49a9e-141">The initial action loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieForAjax` attribute):</span></span>

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

<span data-ttu-id="49a9e-142">O atributo usa o suporte interno [Antifalsificação](xref:security/anti-request-forgery) do ASP.NET Core para definir um cookie com um token de solicitação:</span><span class="sxs-lookup"><span data-stu-id="49a9e-142">The attribute uses ASP.NET Core's built-in [Antiforgery](xref:security/anti-request-forgery) support to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

<span data-ttu-id="49a9e-143">O Angular passa automaticamente um token antifalsificação em um cabeçalho de solicitação chamado `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="49a9e-143">Angular automatically passes an antiforgery token in a request header named `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="49a9e-144">O aplicativo ASP.NET Core MVC é configurado para se referir a esse cabeçalho em sua configuração em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="49a9e-144">The ASP.NET Core MVC app is configured to refer to this header in its configuration in *Startup.cs*:</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

<span data-ttu-id="49a9e-145">O atributo `DisableFormValueModelBinding`, mostrado abaixo, é usado para desabilitar o model binding para o método de ação `Upload`.</span><span class="sxs-lookup"><span data-stu-id="49a9e-145">The `DisableFormValueModelBinding` attribute, shown below, is used to disable model binding for the `Upload` action method.</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

<span data-ttu-id="49a9e-146">Como o model binding é desabilitado, o método de ação `Upload` não aceita parâmetros.</span><span class="sxs-lookup"><span data-stu-id="49a9e-146">Since model binding is disabled, the `Upload` action method doesn't accept parameters.</span></span> <span data-ttu-id="49a9e-147">Ele trabalha diretamente com a propriedade `Request` de `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="49a9e-147">It works directly with the `Request` property of `ControllerBase`.</span></span> <span data-ttu-id="49a9e-148">Um `MultipartReader` é usado para ler cada seção.</span><span class="sxs-lookup"><span data-stu-id="49a9e-148">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="49a9e-149">O arquivo é salvo com um nome de arquivo GUID e os dados de chave/valor são armazenados em um `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="49a9e-149">The file is saved with a GUID filename and the key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="49a9e-150">Após todas as seções terem sido lidas, o conteúdo do `KeyValueAccumulator` é usado para associar os dados do formulário a um tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="49a9e-150">Once all sections have been read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="49a9e-151">O método `Upload` completo é mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="49a9e-151">The complete `Upload` method is shown below:</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a><span data-ttu-id="49a9e-152">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="49a9e-152">Troubleshooting</span></span>

<span data-ttu-id="49a9e-153">Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.</span><span class="sxs-lookup"><span data-stu-id="49a9e-153">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="unexpected-not-found-error-with-iis"></a><span data-ttu-id="49a9e-154">Erro não encontrado inesperado com o IIS</span><span class="sxs-lookup"><span data-stu-id="49a9e-154">Unexpected Not Found error with IIS</span></span>

<span data-ttu-id="49a9e-155">O erro a seguir indica que o upload do arquivo excede o `maxAllowedContentLength` configurado do servidor:</span><span class="sxs-lookup"><span data-stu-id="49a9e-155">The following error indicates your file upload exceeds the server's configured `maxAllowedContentLength`:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="49a9e-156">A configuração padrão é `30000000`, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="49a9e-156">The default setting is `30000000`, which is approximately 28.6MB.</span></span> <span data-ttu-id="49a9e-157">O valor pode ser personalizado editando *web.config*:</span><span class="sxs-lookup"><span data-stu-id="49a9e-157">The value can be customized by editing *web.config*:</span></span>

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

<span data-ttu-id="49a9e-158">Essa configuração só se aplica ao IIS.</span><span class="sxs-lookup"><span data-stu-id="49a9e-158">This setting only applies to IIS.</span></span> <span data-ttu-id="49a9e-159">Esse comportamento não ocorre por padrão quando a hospedagem é feita no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="49a9e-159">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="49a9e-160">Para obter mais informações, consulte [Limites de solicitação \<requestLimits\>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="49a9e-160">For more information, see [Request Limits \<requestLimits\>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="49a9e-161">Exceção de referência nula com IFormFile</span><span class="sxs-lookup"><span data-stu-id="49a9e-161">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="49a9e-162">Se o controlador estiver aceitando arquivos carregados usando `IFormFile`, mas você observar que o valor sempre é nulo, confirme que seu formulário HTML está especificando um valor de `enctype` igual a `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="49a9e-162">If your controller is accepting uploaded files using `IFormFile` but you find that the value is always null, confirm that your HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="49a9e-163">Se esse atributo não estiver definido no elemento `<form>`, o upload do arquivo não ocorrerá e os argumentos `IFormFile` associados serão nulos.</span><span class="sxs-lookup"><span data-stu-id="49a9e-163">If this attribute isn't set on the `<form>` element, the file upload won't occur and any bound `IFormFile` arguments will be null.</span></span>

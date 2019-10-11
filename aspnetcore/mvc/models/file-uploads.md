---
title: Carregar arquivos no ASP.NET Core
author: guardrex
description: Como usar o model binding e o streaming para carregar arquivos no ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: de8bfee22e39dfc5a6ed254cf0555887891d4590
ms.sourcegitcommit: d81912782a8b0bd164f30a516ad80f8defb5d020
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72179301"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="62680-103">Carregar arquivos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="62680-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="62680-104">De [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/)e [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="62680-104">By [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/), and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="62680-105">O ASP.NET Core dá suporte ao carregamento de um ou mais arquivos usando a associação de modelo em buffer para arquivos menores e streaming sem buffer para arquivos maiores.</span><span class="sxs-lookup"><span data-stu-id="62680-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="62680-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="62680-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="62680-107">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="62680-107">Security considerations</span></span>

<span data-ttu-id="62680-108">Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor.</span><span class="sxs-lookup"><span data-stu-id="62680-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="62680-109">Os invasores podem tentar:</span><span class="sxs-lookup"><span data-stu-id="62680-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="62680-110">Executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="62680-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="62680-111">Carregar vírus ou malware.</span><span class="sxs-lookup"><span data-stu-id="62680-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="62680-112">Comprometa redes e servidores de outras maneiras.</span><span class="sxs-lookup"><span data-stu-id="62680-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="62680-113">As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:</span><span class="sxs-lookup"><span data-stu-id="62680-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="62680-114">Carregue arquivos em uma área de carregamento de arquivo dedicada no sistema, preferencialmente para uma unidade que não seja do sistema.</span><span class="sxs-lookup"><span data-stu-id="62680-114">Upload files to a dedicated file upload area on the system, preferably to a non-system drive.</span></span> <span data-ttu-id="62680-115">Usar um local dedicado torna mais fácil impor restrições de segurança em arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="62680-115">Using a dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="62680-116">Desabilite as permissões de execução no local de carregamento do arquivo. &dagger;</span><span class="sxs-lookup"><span data-stu-id="62680-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="62680-117">Nunca persista arquivos carregados na mesma árvore de diretório que o aplicativo. &dagger;</span><span class="sxs-lookup"><span data-stu-id="62680-117">Never persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="62680-118">Use um nome de arquivo seguro determinado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62680-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="62680-119">Não use um nome de arquivo fornecido pelo usuário ou o nome de arquivo não confiável do arquivo carregado. &dagger; Para exibir um nome de arquivo não confiável em uma interface do usuário ou em uma mensagem de log, codifique o valor em HTML.</span><span class="sxs-lookup"><span data-stu-id="62680-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; To display an untrusted file name in a UI or in a logging message, HTML-encode the value.</span></span>
* <span data-ttu-id="62680-120">Permitir apenas um conjunto específico de extensões de arquivo aprovadas. &dagger;</span><span class="sxs-lookup"><span data-stu-id="62680-120">Only allow a specific set of approved file extensions.&dagger;</span></span>
* <span data-ttu-id="62680-121">Verifique a assinatura de formato de arquivo para impedir que um usuário carregue um arquivo mascarado. &dagger; Por exemplo, não permita que um usuário carregue um arquivo *. exe* com uma extensão *. txt* .</span><span class="sxs-lookup"><span data-stu-id="62680-121">Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension.</span></span>
* <span data-ttu-id="62680-122">Verifique se as verificações do lado do cliente também são executadas no servidor. &dagger; As verificações do lado do cliente são fáceis de contornar.</span><span class="sxs-lookup"><span data-stu-id="62680-122">Verify that client-side checks are also performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="62680-123">Verifique o tamanho de um arquivo carregado e evite carregamentos maiores que o esperado. &dagger;</span><span class="sxs-lookup"><span data-stu-id="62680-123">Check the size of an uploaded file and prevent uploads that are larger than expected.&dagger;</span></span>
* <span data-ttu-id="62680-124">Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-124">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="62680-125">**Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**</span><span class="sxs-lookup"><span data-stu-id="62680-125">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="62680-126">o aplicativo de exemplo &dagger;The demonstra uma abordagem que atende aos critérios.</span><span class="sxs-lookup"><span data-stu-id="62680-126">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="62680-127">Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:</span><span class="sxs-lookup"><span data-stu-id="62680-127">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="62680-128">Assuma completamente o controle de um sistema.</span><span class="sxs-lookup"><span data-stu-id="62680-128">Completely gain control of a system.</span></span>
> * <span data-ttu-id="62680-129">Sobrecarregar um sistema com o resultado que o sistema falha.</span><span class="sxs-lookup"><span data-stu-id="62680-129">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="62680-130">Comprometer dados do sistema ou de usuários.</span><span class="sxs-lookup"><span data-stu-id="62680-130">Compromise user or system data.</span></span>
> * <span data-ttu-id="62680-131">Aplique graffiti a uma interface do usuário pública.</span><span class="sxs-lookup"><span data-stu-id="62680-131">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="62680-132">Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="62680-132">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="62680-133">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="62680-133">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)</span></span>
> * <span data-ttu-id="62680-134">Segurança de @no__t 0Azure: Verifique se os controles apropriados estão em vigor ao aceitar arquivos de usuários @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="62680-134">[Azure Security: Ensure appropriate controls are in place when accepting files from users](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)</span></span>

<span data-ttu-id="62680-135">Para obter mais informações sobre como implementar medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [validação](#validation) .</span><span class="sxs-lookup"><span data-stu-id="62680-135">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="62680-136">Cenários de armazenamento</span><span class="sxs-lookup"><span data-stu-id="62680-136">Storage scenarios</span></span>

<span data-ttu-id="62680-137">As opções de armazenamento comuns para arquivos incluem:</span><span class="sxs-lookup"><span data-stu-id="62680-137">Common storage options for files include:</span></span>

* <span data-ttu-id="62680-138">Banco de Dados</span><span class="sxs-lookup"><span data-stu-id="62680-138">Database</span></span>

  * <span data-ttu-id="62680-139">Para carregamentos de arquivos pequenos, um banco de dados geralmente é mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).</span><span class="sxs-lookup"><span data-stu-id="62680-139">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="62680-140">Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, pois a recuperação de um registro de banco de dados para o usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de Avatar).</span><span class="sxs-lookup"><span data-stu-id="62680-140">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="62680-141">Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dado.</span><span class="sxs-lookup"><span data-stu-id="62680-141">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="62680-142">Armazenamento físico (sistema de arquivos ou compartilhamento de rede)</span><span class="sxs-lookup"><span data-stu-id="62680-142">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="62680-143">Para carregamentos de arquivos grandes:</span><span class="sxs-lookup"><span data-stu-id="62680-143">For large file uploads:</span></span>
    * <span data-ttu-id="62680-144">Os limites de banco de dados podem restringir o tamanho do carregamento.</span><span class="sxs-lookup"><span data-stu-id="62680-144">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="62680-145">O armazenamento físico é geralmente menos econômico do que o armazenamento em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="62680-145">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="62680-146">O armazenamento físico é potencialmente mais barato do que usar um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="62680-146">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="62680-147">O processo do aplicativo deve ter permissões de leitura e gravação no local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="62680-147">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="62680-148">**Nunca conceda a permissão EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="62680-148">**Never grant execute permission.**</span></span>

* <span data-ttu-id="62680-149">Serviço de armazenamento de dados (por exemplo, [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="62680-149">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="62680-150">Os serviços geralmente oferecem escalabilidade e resiliência aprimoradas sobre soluções locais que geralmente estão sujeitas a pontos únicos de falha.</span><span class="sxs-lookup"><span data-stu-id="62680-150">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="62680-151">Os serviços são um custo potencialmente menor em cenários de infraestrutura de armazenamento grandes.</span><span class="sxs-lookup"><span data-stu-id="62680-151">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="62680-152">Para obter mais informações, confira [Início Rápido: Use o .NET para criar um blob no armazenamento de objetos @ no__t-0.</span><span class="sxs-lookup"><span data-stu-id="62680-152">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="62680-153">O tópico demonstra <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, mas <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> pode ser usado para salvar um <xref:System.IO.FileStream> no armazenamento de BLOBs ao trabalhar com um <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="62680-153">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="62680-154">Cenários de upload de arquivo</span><span class="sxs-lookup"><span data-stu-id="62680-154">File upload scenarios</span></span>

<span data-ttu-id="62680-155">Duas abordagens gerais para carregar arquivos são buffer e streaming.</span><span class="sxs-lookup"><span data-stu-id="62680-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="62680-156">**Buffer**</span><span class="sxs-lookup"><span data-stu-id="62680-156">**Buffering**</span></span>

<span data-ttu-id="62680-157">O arquivo inteiro é lido em um <xref:Microsoft.AspNetCore.Http.IFormFile>, que é uma C# representação do arquivo usado para processar ou salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="62680-158">Os recursos (disco, memória) usados por carregamentos de arquivos dependem do número e do tamanho de carregamentos de arquivos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="62680-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="62680-159">Se um aplicativo tentar armazenar em buffer um número excessivo de carregamentos, o site falhará quando ficar sem memória ou espaço em disco.</span><span class="sxs-lookup"><span data-stu-id="62680-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="62680-160">Se o tamanho ou a frequência dos carregamentos de arquivos estiver esgotando os recursos do aplicativo, use o streaming.</span><span class="sxs-lookup"><span data-stu-id="62680-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="62680-161">Qualquer arquivo em buffer único que exceda 64 KB é movido da memória para um arquivo temporário no disco.</span><span class="sxs-lookup"><span data-stu-id="62680-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="62680-162">O armazenamento em buffer de arquivos pequenos é abordado nas seguintes seções deste tópico:</span><span class="sxs-lookup"><span data-stu-id="62680-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="62680-163">Armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="62680-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="62680-164">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="62680-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="62680-165">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="62680-165">**Streaming**</span></span>

<span data-ttu-id="62680-166">O arquivo é recebido de uma solicitação de várias partes e processada ou salva diretamente pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62680-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="62680-167">O streaming não melhora significativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="62680-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="62680-168">O streaming reduz as demandas de memória ou espaço em disco ao carregar arquivos.</span><span class="sxs-lookup"><span data-stu-id="62680-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="62680-169">O streaming de arquivos grandes é abordado na seção [carregar arquivos grandes com streaming](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="62680-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="62680-170">Carregar arquivos pequenos com associação de modelo em buffer para armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="62680-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="62680-171">Para carregar arquivos pequenos, use um formulário com diversas partes ou Construa uma solicitação POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="62680-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="62680-172">O exemplo a seguir demonstra o uso de um Razor Pages formulário para carregar um único arquivo (*pages/BufferedSingleFileUploadPhysical. cshtml* no aplicativo de exemplo):</span><span class="sxs-lookup"><span data-stu-id="62680-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="62680-173">O exemplo a seguir é análogo ao exemplo anterior, exceto pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="62680-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="62680-174">O JavaScript ([buscar API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) é usado para enviar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="62680-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="62680-175">Não há validação.</span><span class="sxs-lookup"><span data-stu-id="62680-175">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="62680-176">Para executar a POSTAgem de formulário no JavaScript para clientes que [não dão suporte à API de busca](https://caniuse.com/#feat=fetch), use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="62680-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="62680-177">Use um polifill de busca (por exemplo, [janela. FETCH retroativo (github/FETCH)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="62680-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="62680-178">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="62680-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="62680-179">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="62680-179">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="62680-180">Para dar suporte a uploads de arquivo, os formulários HTML devem especificar um tipo de codificação (`enctype`) de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="62680-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="62680-181">Para um elemento de entrada `files` para dar suporte ao carregamento de vários arquivos, forneça o atributo `multiple` no elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="62680-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="62680-182">Os arquivos individuais carregados no servidor podem ser acessados por meio de [Associação de modelo](xref:mvc/models/model-binding) usando <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="62680-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="62680-183">O aplicativo de exemplo demonstra vários uploads de arquivo em buffer para cenários de armazenamento físico e banco de dados.</span><span class="sxs-lookup"><span data-stu-id="62680-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

> [!WARNING]
> <span data-ttu-id="62680-184">Não confie ou confie na propriedade `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação.</span><span class="sxs-lookup"><span data-stu-id="62680-184">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="62680-185">A propriedade `FileName` só deve ser usada para fins de exibição e somente após a codificação HTML do valor.</span><span class="sxs-lookup"><span data-stu-id="62680-185">The `FileName` property should only be used for display purposes and only after HTML encoding the value.</span></span>
>
> <span data-ttu-id="62680-186">Os exemplos fornecidos até o momento não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="62680-186">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="62680-187">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="62680-187">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="62680-188">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="62680-188">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="62680-189">Validação</span><span class="sxs-lookup"><span data-stu-id="62680-189">Validation</span></span>](#validation)

<span data-ttu-id="62680-190">Ao carregar arquivos usando a associação de modelo e <xref:Microsoft.AspNetCore.Http.IFormFile>, o método de ação pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="62680-190">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="62680-191">Um único <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="62680-191">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="62680-192">Qualquer uma das seguintes coleções que representam vários arquivos:</span><span class="sxs-lookup"><span data-stu-id="62680-192">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="62680-193">[Listar](xref:System.Collections.Generic.List`1)\< @ no__t-2 @ no__t-3</span><span class="sxs-lookup"><span data-stu-id="62680-193">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="62680-194">A Associação corresponde aos arquivos de formulário por nome.</span><span class="sxs-lookup"><span data-stu-id="62680-194">Binding matches form files by name.</span></span> <span data-ttu-id="62680-195">Por exemplo, o valor HTML `name` em `<input type="file" name="formFile">` deve corresponder ao C# parâmetro/propriedade associado (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="62680-195">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="62680-196">Para obter mais informações, consulte o [valor do atributo Match Name para o nome do parâmetro da seção do método post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="62680-196">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="62680-197">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="62680-197">The following example:</span></span>

* <span data-ttu-id="62680-198">Executa um loop em um ou mais arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="62680-198">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="62680-199">Usa [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-199">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="62680-200">Salva os arquivos no sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62680-200">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="62680-201">Retorna o número total e o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="62680-201">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size, filePath });
}
```

<span data-ttu-id="62680-202">Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho.</span><span class="sxs-lookup"><span data-stu-id="62680-202">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="62680-203">No exemplo a seguir, o caminho é obtido da configuração:</span><span class="sxs-lookup"><span data-stu-id="62680-203">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="62680-204">O caminho passado para o <xref:System.IO.FileStream> *deve* incluir o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-204">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="62680-205">Se o nome do arquivo não for fornecido, um <xref:System.UnauthorizedAccessException> será lançado em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="62680-205">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="62680-206">Os arquivos carregados usando a técnica <xref:Microsoft.AspNetCore.Http.IFormFile> são armazenados em buffer na memória ou no disco no servidor antes do processamento.</span><span class="sxs-lookup"><span data-stu-id="62680-206">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="62680-207">Dentro do método de ação, o conteúdo <xref:Microsoft.AspNetCore.Http.IFormFile> é acessível como um <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="62680-207">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="62680-208">Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como o [armazenamento de BLOBs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="62680-208">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="62680-209">Para outro exemplo que executa um loop sobre vários arquivos para carregar e usa nomes de arquivo seguros, consulte *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="62680-209">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="62680-210">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) gera um <xref:System.IO.IOException> se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores.</span><span class="sxs-lookup"><span data-stu-id="62680-210">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="62680-211">O limite de 65.535 arquivos é um limite por servidor.</span><span class="sxs-lookup"><span data-stu-id="62680-211">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="62680-212">Para obter mais informações sobre esse limite no sistema operacional Windows, consulte os comentários nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="62680-212">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="62680-213">Função GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="62680-213">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="62680-214">Carregar arquivos pequenos com associação de modelo em buffer para um banco de dados</span><span class="sxs-lookup"><span data-stu-id="62680-214">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="62680-215">Para armazenar dados de arquivo binário em um banco de dado usando [Entity Framework](/ef/core/index), defina uma propriedade de matriz <xref:System.Byte> na entidade:</span><span class="sxs-lookup"><span data-stu-id="62680-215">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="62680-216">Especifique uma propriedade de modelo de página para a classe que inclui um <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="62680-216">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="62680-217"><xref:Microsoft.AspNetCore.Http.IFormFile> pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="62680-217"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="62680-218">O exemplo anterior usa uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="62680-218">The prior example uses a bound model property.</span></span>

<span data-ttu-id="62680-219">O `FileUpload` é usado no formulário de Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="62680-219">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="62680-220">Quando o formulário for Postado no servidor, copie o <xref:Microsoft.AspNetCore.Http.IFormFile> em um fluxo e salve-o como uma matriz de bytes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="62680-220">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="62680-221">No exemplo a seguir, `_dbContext` armazena o contexto do banco de dados do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="62680-221">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="62680-222">O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="62680-222">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="62680-223">*Páginas/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="62680-223">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="62680-224">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="62680-224">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="62680-225">Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="62680-225">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="62680-226">Não confie ou confie na propriedade `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação.</span><span class="sxs-lookup"><span data-stu-id="62680-226">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="62680-227">A propriedade `FileName` só deve ser usada para fins de exibição e somente após a codificação HTML.</span><span class="sxs-lookup"><span data-stu-id="62680-227">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="62680-228">Os exemplos fornecidos não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="62680-228">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="62680-229">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="62680-229">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="62680-230">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="62680-230">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="62680-231">Validação</span><span class="sxs-lookup"><span data-stu-id="62680-231">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="62680-232">Carregar arquivos grandes com streaming</span><span class="sxs-lookup"><span data-stu-id="62680-232">Upload large files with streaming</span></span>

<span data-ttu-id="62680-233">O exemplo a seguir demonstra como usar o JavaScript para transmitir um arquivo a uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="62680-233">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="62680-234">O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="62680-234">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="62680-235">Como o método de ação processa diretamente os dados carregados, a associação de modelo de formulário é desabilitada por outro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="62680-235">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="62680-236">Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="62680-236">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="62680-237">Depois que as seções com várias partes forem lidas, a ação executará sua própria associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="62680-237">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="62680-238">A resposta de página inicial carrega o formulário e salva um token de antifalsificação em um cookie (por meio do atributo `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="62680-238">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="62680-239">O atributo usa o [suporte de antifalsificação](xref:security/anti-request-forgery) interno do ASP.NET Core para definir um cookie com um token de solicitação:</span><span class="sxs-lookup"><span data-stu-id="62680-239">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="62680-240">O `DisableFormValueModelBindingAttribute` é usado para desabilitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="62680-240">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="62680-241">No aplicativo de exemplo, `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` são aplicados como filtros para os modelos de aplicativo de página de `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` no `Startup.ConfigureServices` usando [convenções de Razor Pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="62680-241">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="62680-242">Como a associação de modelo não lê o formulário, os parâmetros que estão associados do formulário não são associados (a consulta, a rota e o cabeçalho continuam a funcionar).</span><span class="sxs-lookup"><span data-stu-id="62680-242">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="62680-243">O método de ação funciona diretamente com a propriedade `Request`.</span><span class="sxs-lookup"><span data-stu-id="62680-243">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="62680-244">Um `MultipartReader` é usado para ler cada seção.</span><span class="sxs-lookup"><span data-stu-id="62680-244">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="62680-245">Os dados de chave/valor são armazenados em um `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="62680-245">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="62680-246">Depois que as seções com várias partes forem lidas, o conteúdo do `KeyValueAccumulator` será usado para associar os dados do formulário a um tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="62680-246">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="62680-247">O método `StreamingController.UploadDatabase` completo para streaming para um banco de dados com EF Core:</span><span class="sxs-lookup"><span data-stu-id="62680-247">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="62680-248">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="62680-248">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="62680-249">O método `StreamingController.UploadPhysical` completo para streaming para um local físico:</span><span class="sxs-lookup"><span data-stu-id="62680-249">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="62680-250">No aplicativo de exemplo, as verificações de validação são manipuladas por `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="62680-250">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="62680-251">Validação</span><span class="sxs-lookup"><span data-stu-id="62680-251">Validation</span></span>

<span data-ttu-id="62680-252">A classe `FileHelpers` do aplicativo de exemplo demonstra várias verificações de carregamentos em buffer <xref:Microsoft.AspNetCore.Http.IFormFile> e de arquivos transmitidos.</span><span class="sxs-lookup"><span data-stu-id="62680-252">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="62680-253">Para processar carregamentos de arquivo em buffer <xref:Microsoft.AspNetCore.Http.IFormFile> no aplicativo de exemplo, consulte o método `ProcessFormFile` no arquivo *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="62680-253">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="62680-254">Para o processamento de arquivos transmitidos, consulte o método `ProcessStreamedFile` no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-254">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="62680-255">Os métodos de processamento de validação demonstrados no aplicativo de exemplo não verificam o conteúdo dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="62680-255">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="62680-256">Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para os usuários ou outros sistemas.</span><span class="sxs-lookup"><span data-stu-id="62680-256">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="62680-257">Embora o tópico exemplo forneça um exemplo de trabalho de técnicas de validação, não implemente a classe `FileHelpers` em um aplicativo de produção, a menos que você:</span><span class="sxs-lookup"><span data-stu-id="62680-257">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="62680-258">Entenda totalmente a implementação.</span><span class="sxs-lookup"><span data-stu-id="62680-258">Fully understand the implementation.</span></span>
> * <span data-ttu-id="62680-259">Modifique a implementação conforme apropriado para o ambiente e as especificações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62680-259">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="62680-260">**Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**</span><span class="sxs-lookup"><span data-stu-id="62680-260">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="62680-261">Validação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="62680-261">Content validation</span></span>

<span data-ttu-id="62680-262">**Use uma API de verificação de vírus/malware de terceiros no conteúdo carregado.**</span><span class="sxs-lookup"><span data-stu-id="62680-262">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="62680-263">A verificação de arquivos exige muitos recursos do servidor em cenários de alto volume.</span><span class="sxs-lookup"><span data-stu-id="62680-263">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="62680-264">Se o desempenho do processamento de solicitações for reduzido devido à verificação de arquivo, considere descarregar o trabalho de verificação para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62680-264">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="62680-265">Normalmente, os arquivos carregados são mantidos em uma área em quarentena até que o verificador de vírus em segundo plano os Verifique.</span><span class="sxs-lookup"><span data-stu-id="62680-265">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="62680-266">Quando um arquivo passa, o arquivo é movido para o local de armazenamento de arquivo normal.</span><span class="sxs-lookup"><span data-stu-id="62680-266">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="62680-267">Essas etapas geralmente são executadas em conjunto com um registro de banco de dados que indica o status de verificação de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-267">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="62680-268">Ao usar essa abordagem, o aplicativo e o servidor de aplicativos permanecem concentrados em responder às solicitações.</span><span class="sxs-lookup"><span data-stu-id="62680-268">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="62680-269">Validação de extensão de arquivo</span><span class="sxs-lookup"><span data-stu-id="62680-269">File extension validation</span></span>

<span data-ttu-id="62680-270">A extensão do arquivo carregado deve ser verificada em uma lista de extensões permitidas.</span><span class="sxs-lookup"><span data-stu-id="62680-270">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="62680-271">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="62680-271">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="62680-272">Validação de assinatura de arquivo</span><span class="sxs-lookup"><span data-stu-id="62680-272">File signature validation</span></span>

<span data-ttu-id="62680-273">A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-273">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="62680-274">Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-274">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="62680-275">O aplicativo de exemplo verifica as assinaturas de arquivo para alguns tipos de arquivo comuns.</span><span class="sxs-lookup"><span data-stu-id="62680-275">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="62680-276">No exemplo a seguir, a assinatura de arquivo para uma imagem JPEG é verificada em relação ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="62680-276">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="62680-277">Para obter assinaturas de arquivo adicionais, consulte o [banco de dados assinaturas de arquivo](https://www.filesignatures.net/) e especificações de arquivo oficial.</span><span class="sxs-lookup"><span data-stu-id="62680-277">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="62680-278">Segurança de nome de arquivo</span><span class="sxs-lookup"><span data-stu-id="62680-278">File name security</span></span>

<span data-ttu-id="62680-279">Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo no armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="62680-279">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="62680-280">Crie um nome de arquivo seguro para o arquivo usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.</span><span class="sxs-lookup"><span data-stu-id="62680-280">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="62680-281">O Razor automaticamente codifica os valores de propriedade para exibição.</span><span class="sxs-lookup"><span data-stu-id="62680-281">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="62680-282">O código a seguir é seguro para usar:</span><span class="sxs-lookup"><span data-stu-id="62680-282">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="62680-283">Fora do Razor, sempre <xref:System.Net.WebUtility.HtmlEncode*> conteúdo de nome de arquivo da solicitação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="62680-283">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="62680-284">Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo será substituído por um arquivo com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="62680-284">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="62680-285">Forneça lógica adicional para atender às especificações do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62680-285">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="62680-286">Validação de tamanho</span><span class="sxs-lookup"><span data-stu-id="62680-286">Size validation</span></span>

<span data-ttu-id="62680-287">Limite o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="62680-287">Limit the size of uploaded files.</span></span>

<span data-ttu-id="62680-288">No aplicativo de exemplo, o tamanho do arquivo é limitado a 2 MB (indicado em bytes).</span><span class="sxs-lookup"><span data-stu-id="62680-288">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="62680-289">O limite é fornecido por meio da [configuração](xref:fundamentals/configuration/index) do arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="62680-289">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="62680-290">O `FileSizeLimit` é injetado em classes `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="62680-290">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="62680-291">Quando um tamanho de arquivo exceder o limite, o arquivo será rejeitado:</span><span class="sxs-lookup"><span data-stu-id="62680-291">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="62680-292">Coincidir valor do atributo de nome com o nome do parâmetro do método POST</span><span class="sxs-lookup"><span data-stu-id="62680-292">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="62680-293">Em formulários não Razor que POSTam dados de formulário ou usam o `FormData` do JavaScript diretamente, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="62680-293">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="62680-294">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="62680-294">In the following example:</span></span>

* <span data-ttu-id="62680-295">Ao usar um elemento `<input>`, o atributo `name` é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="62680-295">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="62680-296">Ao usar `FormData` em JavaScript, o nome é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="62680-296">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="62680-297">Use um nome correspondente para o parâmetro do C# método (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="62680-297">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="62680-298">Para um método de manipulador de página Razor Pages chamado `Upload`:</span><span class="sxs-lookup"><span data-stu-id="62680-298">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="62680-299">Para um método de ação do controlador de POSTAgem MVC:</span><span class="sxs-lookup"><span data-stu-id="62680-299">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="62680-300">Configuração de servidor e aplicativo</span><span class="sxs-lookup"><span data-stu-id="62680-300">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="62680-301">Limite de comprimento de corpo com várias partes</span><span class="sxs-lookup"><span data-stu-id="62680-301">Multipart body length limit</span></span>

<span data-ttu-id="62680-302"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> define o limite para o comprimento de cada corpo com diversas partes.</span><span class="sxs-lookup"><span data-stu-id="62680-302"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="62680-303">As seções de formulário que excedem esse limite lançam um <xref:System.IO.InvalidDataException> quando analisadas.</span><span class="sxs-lookup"><span data-stu-id="62680-303">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="62680-304">O padrão é 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="62680-304">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="62680-305">Personalize o limite usando a configuração <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="62680-305">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="62680-306"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> é usado para definir o <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="62680-306"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="62680-307">Em um aplicativo Razor Pages, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="62680-307">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    });
```

<span data-ttu-id="62680-308">Em um aplicativo Razor Pages ou em um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="62680-308">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="62680-309">Tamanho máximo do corpo de solicitação do Kestrel</span><span class="sxs-lookup"><span data-stu-id="62680-309">Kestrel maximum request body size</span></span>

<span data-ttu-id="62680-310">Para aplicativos hospedados pelo Kestrel, o tamanho máximo padrão do corpo da solicitação é 30 milhões bytes, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="62680-310">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="62680-311">Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="62680-311">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="62680-312"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="62680-312"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="62680-313">Em um aplicativo Razor Pages, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="62680-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    });
```

<span data-ttu-id="62680-314">Em um aplicativo de páginas Razor ou um aplicativo MVC, aplique o filtro à classe de manipulador de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="62680-314">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="62680-315">O `RequestSizeLimitAttribute` também pode ser aplicado usando a diretiva [@attribute](xref:mvc/views/razor#attribute) Razor:</span><span class="sxs-lookup"><span data-stu-id="62680-315">The `RequestSizeLimitAttribute` can also be applied using the [@attribute](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="62680-316">Outros limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="62680-316">Other Kestrel limits</span></span>

<span data-ttu-id="62680-317">Outros limites de Kestrel podem ser aplicados para aplicativos hospedados pelo Kestrel:</span><span class="sxs-lookup"><span data-stu-id="62680-317">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="62680-318">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="62680-318">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="62680-319">Taxas de dados de solicitação e resposta</span><span class="sxs-lookup"><span data-stu-id="62680-319">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="62680-320">Limite de comprimento de conteúdo do IIS</span><span class="sxs-lookup"><span data-stu-id="62680-320">IIS content length limit</span></span>

<span data-ttu-id="62680-321">O limite de solicitação padrão (`maxAllowedContentLength`) é 30 milhões bytes, que é de aproximadamente 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="62680-321">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="62680-322">Personalize o limite no arquivo *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="62680-322">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="62680-323">Essa configuração só se aplica ao IIS.</span><span class="sxs-lookup"><span data-stu-id="62680-323">This setting only applies to IIS.</span></span> <span data-ttu-id="62680-324">Esse comportamento não ocorre por padrão quando a hospedagem é feita no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="62680-324">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="62680-325">Para obter mais informações, consulte [Limits Request \<requestLimits >](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="62680-325">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="62680-326">As limitações no módulo ASP.NET Core ou na presença do módulo filtragem de solicitações do IIS podem limitar os carregamentos a dois ou 4 GB.</span><span class="sxs-lookup"><span data-stu-id="62680-326">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="62680-327">Para obter mais informações, consulte [não é possível carregar o arquivo com mais de 2GB de tamanho (ASPNET/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="62680-327">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="62680-328">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="62680-328">Troubleshoot</span></span>

<span data-ttu-id="62680-329">Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.</span><span class="sxs-lookup"><span data-stu-id="62680-329">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="62680-330">Erro não encontrado quando implantado em um servidor IIS</span><span class="sxs-lookup"><span data-stu-id="62680-330">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="62680-331">O erro a seguir indica que o arquivo carregado excede o tamanho do conteúdo configurado do servidor:</span><span class="sxs-lookup"><span data-stu-id="62680-331">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="62680-332">Para obter mais informações sobre como aumentar o limite, consulte a seção [limite de tamanho de conteúdo do IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="62680-332">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="62680-333">Falha na conexão</span><span class="sxs-lookup"><span data-stu-id="62680-333">Connection failure</span></span>

<span data-ttu-id="62680-334">Um erro de conexão e uma conexão de servidor de redefinição provavelmente indicam que o arquivo carregado excede o tamanho máximo do corpo de solicitação do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="62680-334">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="62680-335">Para obter mais informações, consulte a seção [tamanho máximo de corpo de solicitação do Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="62680-335">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="62680-336">Os limites de conexão de cliente Kestrel também podem exigir ajuste.</span><span class="sxs-lookup"><span data-stu-id="62680-336">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="62680-337">Exceção de referência nula com IFormFile</span><span class="sxs-lookup"><span data-stu-id="62680-337">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="62680-338">Se o controlador estiver aceitando arquivos carregados usando <xref:Microsoft.AspNetCore.Http.IFormFile>, mas o valor for `null`, confirme se o formulário HTML está especificando um valor `enctype` de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="62680-338">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="62680-339">Se esse atributo não estiver definido no elemento `<form>`, o upload do arquivo não ocorrerá e todos os argumentos vinculados a <xref:Microsoft.AspNetCore.Http.IFormFile> serão `null`.</span><span class="sxs-lookup"><span data-stu-id="62680-339">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="62680-340">Além disso, confirme se a [nomenclatura de carregamento em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="62680-340">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="62680-341">O ASP.NET Core dá suporte ao carregamento de um ou mais arquivos usando a associação de modelo em buffer para arquivos menores e streaming sem buffer para arquivos maiores.</span><span class="sxs-lookup"><span data-stu-id="62680-341">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="62680-342">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="62680-342">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="62680-343">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="62680-343">Security considerations</span></span>

<span data-ttu-id="62680-344">Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor.</span><span class="sxs-lookup"><span data-stu-id="62680-344">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="62680-345">Os invasores podem tentar:</span><span class="sxs-lookup"><span data-stu-id="62680-345">Attackers may attempt to:</span></span>

* <span data-ttu-id="62680-346">Executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="62680-346">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="62680-347">Carregar vírus ou malware.</span><span class="sxs-lookup"><span data-stu-id="62680-347">Upload viruses or malware.</span></span>
* <span data-ttu-id="62680-348">Comprometa redes e servidores de outras maneiras.</span><span class="sxs-lookup"><span data-stu-id="62680-348">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="62680-349">As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:</span><span class="sxs-lookup"><span data-stu-id="62680-349">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="62680-350">Carregue arquivos em uma área de carregamento de arquivo dedicada no sistema, preferencialmente para uma unidade que não seja do sistema.</span><span class="sxs-lookup"><span data-stu-id="62680-350">Upload files to a dedicated file upload area on the system, preferably to a non-system drive.</span></span> <span data-ttu-id="62680-351">Usar um local dedicado torna mais fácil impor restrições de segurança em arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="62680-351">Using a dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="62680-352">Desabilite as permissões de execução no local de carregamento do arquivo. &dagger;</span><span class="sxs-lookup"><span data-stu-id="62680-352">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="62680-353">Nunca persista arquivos carregados na mesma árvore de diretório que o aplicativo. &dagger;</span><span class="sxs-lookup"><span data-stu-id="62680-353">Never persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="62680-354">Use um nome de arquivo seguro determinado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62680-354">Use a safe file name determined by the app.</span></span> <span data-ttu-id="62680-355">Não use um nome de arquivo fornecido pelo usuário ou o nome de arquivo não confiável do arquivo carregado. &dagger; Para exibir um nome de arquivo não confiável em uma interface do usuário ou em uma mensagem de log, codifique o valor em HTML.</span><span class="sxs-lookup"><span data-stu-id="62680-355">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; To display an untrusted file name in a UI or in a logging message, HTML-encode the value.</span></span>
* <span data-ttu-id="62680-356">Permitir apenas um conjunto específico de extensões de arquivo aprovadas. &dagger;</span><span class="sxs-lookup"><span data-stu-id="62680-356">Only allow a specific set of approved file extensions.&dagger;</span></span>
* <span data-ttu-id="62680-357">Verifique a assinatura de formato de arquivo para impedir que um usuário carregue um arquivo mascarado. &dagger; Por exemplo, não permita que um usuário carregue um arquivo *. exe* com uma extensão *. txt* .</span><span class="sxs-lookup"><span data-stu-id="62680-357">Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension.</span></span>
* <span data-ttu-id="62680-358">Verifique se as verificações do lado do cliente também são executadas no servidor. &dagger; As verificações do lado do cliente são fáceis de contornar.</span><span class="sxs-lookup"><span data-stu-id="62680-358">Verify that client-side checks are also performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="62680-359">Verifique o tamanho de um arquivo carregado e evite carregamentos maiores que o esperado. &dagger;</span><span class="sxs-lookup"><span data-stu-id="62680-359">Check the size of an uploaded file and prevent uploads that are larger than expected.&dagger;</span></span>
* <span data-ttu-id="62680-360">Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-360">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="62680-361">**Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**</span><span class="sxs-lookup"><span data-stu-id="62680-361">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="62680-362">o aplicativo de exemplo &dagger;The demonstra uma abordagem que atende aos critérios.</span><span class="sxs-lookup"><span data-stu-id="62680-362">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="62680-363">Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:</span><span class="sxs-lookup"><span data-stu-id="62680-363">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="62680-364">Assuma completamente o controle de um sistema.</span><span class="sxs-lookup"><span data-stu-id="62680-364">Completely gain control of a system.</span></span>
> * <span data-ttu-id="62680-365">Sobrecarregar um sistema com o resultado que o sistema falha.</span><span class="sxs-lookup"><span data-stu-id="62680-365">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="62680-366">Comprometer dados do sistema ou de usuários.</span><span class="sxs-lookup"><span data-stu-id="62680-366">Compromise user or system data.</span></span>
> * <span data-ttu-id="62680-367">Aplique graffiti a uma interface do usuário pública.</span><span class="sxs-lookup"><span data-stu-id="62680-367">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="62680-368">Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="62680-368">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="62680-369">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="62680-369">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)</span></span>
> * <span data-ttu-id="62680-370">Segurança de @no__t 0Azure: Verifique se os controles apropriados estão em vigor ao aceitar arquivos de usuários @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="62680-370">[Azure Security: Ensure appropriate controls are in place when accepting files from users](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)</span></span>

<span data-ttu-id="62680-371">Para obter mais informações sobre como implementar medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [validação](#validation) .</span><span class="sxs-lookup"><span data-stu-id="62680-371">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="62680-372">Cenários de armazenamento</span><span class="sxs-lookup"><span data-stu-id="62680-372">Storage scenarios</span></span>

<span data-ttu-id="62680-373">As opções de armazenamento comuns para arquivos incluem:</span><span class="sxs-lookup"><span data-stu-id="62680-373">Common storage options for files include:</span></span>

* <span data-ttu-id="62680-374">Banco de Dados</span><span class="sxs-lookup"><span data-stu-id="62680-374">Database</span></span>

  * <span data-ttu-id="62680-375">Para carregamentos de arquivos pequenos, um banco de dados geralmente é mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).</span><span class="sxs-lookup"><span data-stu-id="62680-375">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="62680-376">Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, pois a recuperação de um registro de banco de dados para o usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de Avatar).</span><span class="sxs-lookup"><span data-stu-id="62680-376">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="62680-377">Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dado.</span><span class="sxs-lookup"><span data-stu-id="62680-377">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="62680-378">Armazenamento físico (sistema de arquivos ou compartilhamento de rede)</span><span class="sxs-lookup"><span data-stu-id="62680-378">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="62680-379">Para carregamentos de arquivos grandes:</span><span class="sxs-lookup"><span data-stu-id="62680-379">For large file uploads:</span></span>
    * <span data-ttu-id="62680-380">Os limites de banco de dados podem restringir o tamanho do carregamento.</span><span class="sxs-lookup"><span data-stu-id="62680-380">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="62680-381">O armazenamento físico é geralmente menos econômico do que o armazenamento em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="62680-381">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="62680-382">O armazenamento físico é potencialmente mais barato do que usar um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="62680-382">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="62680-383">O processo do aplicativo deve ter permissões de leitura e gravação no local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="62680-383">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="62680-384">**Nunca conceda a permissão EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="62680-384">**Never grant execute permission.**</span></span>

* <span data-ttu-id="62680-385">Serviço de armazenamento de dados (por exemplo, [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="62680-385">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="62680-386">Os serviços geralmente oferecem escalabilidade e resiliência aprimoradas sobre soluções locais que geralmente estão sujeitas a pontos únicos de falha.</span><span class="sxs-lookup"><span data-stu-id="62680-386">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="62680-387">Os serviços são um custo potencialmente menor em cenários de infraestrutura de armazenamento grandes.</span><span class="sxs-lookup"><span data-stu-id="62680-387">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="62680-388">Para obter mais informações, confira [Início Rápido: Use o .NET para criar um blob no armazenamento de objetos @ no__t-0.</span><span class="sxs-lookup"><span data-stu-id="62680-388">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="62680-389">O tópico demonstra <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, mas <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> pode ser usado para salvar um <xref:System.IO.FileStream> no armazenamento de BLOBs ao trabalhar com um <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="62680-389">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="62680-390">Cenários de upload de arquivo</span><span class="sxs-lookup"><span data-stu-id="62680-390">File upload scenarios</span></span>

<span data-ttu-id="62680-391">Duas abordagens gerais para carregar arquivos são buffer e streaming.</span><span class="sxs-lookup"><span data-stu-id="62680-391">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="62680-392">**Buffer**</span><span class="sxs-lookup"><span data-stu-id="62680-392">**Buffering**</span></span>

<span data-ttu-id="62680-393">O arquivo inteiro é lido em um <xref:Microsoft.AspNetCore.Http.IFormFile>, que é uma C# representação do arquivo usado para processar ou salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-393">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="62680-394">Os recursos (disco, memória) usados por carregamentos de arquivos dependem do número e do tamanho de carregamentos de arquivos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="62680-394">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="62680-395">Se um aplicativo tentar armazenar em buffer um número excessivo de carregamentos, o site falhará quando ficar sem memória ou espaço em disco.</span><span class="sxs-lookup"><span data-stu-id="62680-395">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="62680-396">Se o tamanho ou a frequência dos carregamentos de arquivos estiver esgotando os recursos do aplicativo, use o streaming.</span><span class="sxs-lookup"><span data-stu-id="62680-396">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="62680-397">Qualquer arquivo em buffer único que exceda 64 KB é movido da memória para um arquivo temporário no disco.</span><span class="sxs-lookup"><span data-stu-id="62680-397">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="62680-398">O armazenamento em buffer de arquivos pequenos é abordado nas seguintes seções deste tópico:</span><span class="sxs-lookup"><span data-stu-id="62680-398">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="62680-399">Armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="62680-399">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="62680-400">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="62680-400">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="62680-401">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="62680-401">**Streaming**</span></span>

<span data-ttu-id="62680-402">O arquivo é recebido de uma solicitação de várias partes e processada ou salva diretamente pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62680-402">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="62680-403">O streaming não melhora significativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="62680-403">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="62680-404">O streaming reduz as demandas de memória ou espaço em disco ao carregar arquivos.</span><span class="sxs-lookup"><span data-stu-id="62680-404">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="62680-405">O streaming de arquivos grandes é abordado na seção [carregar arquivos grandes com streaming](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="62680-405">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="62680-406">Carregar arquivos pequenos com associação de modelo em buffer para armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="62680-406">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="62680-407">Para carregar arquivos pequenos, use um formulário com diversas partes ou Construa uma solicitação POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="62680-407">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="62680-408">O exemplo a seguir demonstra o uso de um Razor Pages formulário para carregar um único arquivo (*pages/BufferedSingleFileUploadPhysical. cshtml* no aplicativo de exemplo):</span><span class="sxs-lookup"><span data-stu-id="62680-408">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="62680-409">O exemplo a seguir é análogo ao exemplo anterior, exceto pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="62680-409">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="62680-410">O JavaScript ([buscar API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) é usado para enviar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="62680-410">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="62680-411">Não há validação.</span><span class="sxs-lookup"><span data-stu-id="62680-411">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="62680-412">Para executar a POSTAgem de formulário no JavaScript para clientes que [não dão suporte à API de busca](https://caniuse.com/#feat=fetch), use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="62680-412">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="62680-413">Use um polifill de busca (por exemplo, [janela. FETCH retroativo (github/FETCH)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="62680-413">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="62680-414">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="62680-414">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="62680-415">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="62680-415">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="62680-416">Para dar suporte a uploads de arquivo, os formulários HTML devem especificar um tipo de codificação (`enctype`) de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="62680-416">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="62680-417">Para um elemento de entrada `files` para dar suporte ao carregamento de vários arquivos, forneça o atributo `multiple` no elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="62680-417">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="62680-418">Os arquivos individuais carregados no servidor podem ser acessados por meio de [Associação de modelo](xref:mvc/models/model-binding) usando <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="62680-418">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="62680-419">O aplicativo de exemplo demonstra vários uploads de arquivo em buffer para cenários de armazenamento físico e banco de dados.</span><span class="sxs-lookup"><span data-stu-id="62680-419">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

> [!WARNING]
> <span data-ttu-id="62680-420">Não confie ou confie na propriedade `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação.</span><span class="sxs-lookup"><span data-stu-id="62680-420">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="62680-421">A propriedade `FileName` só deve ser usada para fins de exibição e somente após a codificação HTML do valor.</span><span class="sxs-lookup"><span data-stu-id="62680-421">The `FileName` property should only be used for display purposes and only after HTML encoding the value.</span></span>
>
> <span data-ttu-id="62680-422">Os exemplos fornecidos até o momento não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="62680-422">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="62680-423">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="62680-423">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="62680-424">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="62680-424">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="62680-425">Validação</span><span class="sxs-lookup"><span data-stu-id="62680-425">Validation</span></span>](#validation)

<span data-ttu-id="62680-426">Ao carregar arquivos usando a associação de modelo e <xref:Microsoft.AspNetCore.Http.IFormFile>, o método de ação pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="62680-426">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="62680-427">Um único <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="62680-427">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="62680-428">Qualquer uma das seguintes coleções que representam vários arquivos:</span><span class="sxs-lookup"><span data-stu-id="62680-428">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="62680-429">[Listar](xref:System.Collections.Generic.List`1)\< @ no__t-2 @ no__t-3</span><span class="sxs-lookup"><span data-stu-id="62680-429">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="62680-430">A Associação corresponde aos arquivos de formulário por nome.</span><span class="sxs-lookup"><span data-stu-id="62680-430">Binding matches form files by name.</span></span> <span data-ttu-id="62680-431">Por exemplo, o valor HTML `name` em `<input type="file" name="formFile">` deve corresponder ao C# parâmetro/propriedade associado (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="62680-431">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="62680-432">Para obter mais informações, consulte o [valor do atributo Match Name para o nome do parâmetro da seção do método post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="62680-432">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="62680-433">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="62680-433">The following example:</span></span>

* <span data-ttu-id="62680-434">Executa um loop em um ou mais arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="62680-434">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="62680-435">Usa [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-435">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="62680-436">Salva os arquivos no sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62680-436">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="62680-437">Retorna o número total e o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="62680-437">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size, filePath });
}
```

<span data-ttu-id="62680-438">Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho.</span><span class="sxs-lookup"><span data-stu-id="62680-438">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="62680-439">No exemplo a seguir, o caminho é obtido da configuração:</span><span class="sxs-lookup"><span data-stu-id="62680-439">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="62680-440">O caminho passado para o <xref:System.IO.FileStream> *deve* incluir o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-440">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="62680-441">Se o nome do arquivo não for fornecido, um <xref:System.UnauthorizedAccessException> será lançado em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="62680-441">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="62680-442">Os arquivos carregados usando a técnica <xref:Microsoft.AspNetCore.Http.IFormFile> são armazenados em buffer na memória ou no disco no servidor antes do processamento.</span><span class="sxs-lookup"><span data-stu-id="62680-442">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="62680-443">Dentro do método de ação, o conteúdo <xref:Microsoft.AspNetCore.Http.IFormFile> é acessível como um <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="62680-443">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="62680-444">Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como o [armazenamento de BLOBs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="62680-444">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="62680-445">Para outro exemplo que executa um loop sobre vários arquivos para carregar e usa nomes de arquivo seguros, consulte *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="62680-445">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="62680-446">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) gera um <xref:System.IO.IOException> se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores.</span><span class="sxs-lookup"><span data-stu-id="62680-446">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="62680-447">O limite de 65.535 arquivos é um limite por servidor.</span><span class="sxs-lookup"><span data-stu-id="62680-447">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="62680-448">Para obter mais informações sobre esse limite no sistema operacional Windows, consulte os comentários nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="62680-448">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="62680-449">Função GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="62680-449">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="62680-450">Carregar arquivos pequenos com associação de modelo em buffer para um banco de dados</span><span class="sxs-lookup"><span data-stu-id="62680-450">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="62680-451">Para armazenar dados de arquivo binário em um banco de dado usando [Entity Framework](/ef/core/index), defina uma propriedade de matriz <xref:System.Byte> na entidade:</span><span class="sxs-lookup"><span data-stu-id="62680-451">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="62680-452">Especifique uma propriedade de modelo de página para a classe que inclui um <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="62680-452">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="62680-453"><xref:Microsoft.AspNetCore.Http.IFormFile> pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="62680-453"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="62680-454">O exemplo anterior usa uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="62680-454">The prior example uses a bound model property.</span></span>

<span data-ttu-id="62680-455">O `FileUpload` é usado no formulário de Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="62680-455">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="62680-456">Quando o formulário for Postado no servidor, copie o <xref:Microsoft.AspNetCore.Http.IFormFile> em um fluxo e salve-o como uma matriz de bytes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="62680-456">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="62680-457">No exemplo a seguir, `_dbContext` armazena o contexto do banco de dados do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="62680-457">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="62680-458">O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="62680-458">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="62680-459">*Páginas/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="62680-459">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="62680-460">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="62680-460">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="62680-461">Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="62680-461">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="62680-462">Não confie ou confie na propriedade `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação.</span><span class="sxs-lookup"><span data-stu-id="62680-462">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="62680-463">A propriedade `FileName` só deve ser usada para fins de exibição e somente após a codificação HTML.</span><span class="sxs-lookup"><span data-stu-id="62680-463">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="62680-464">Os exemplos fornecidos não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="62680-464">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="62680-465">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="62680-465">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="62680-466">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="62680-466">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="62680-467">Validação</span><span class="sxs-lookup"><span data-stu-id="62680-467">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="62680-468">Carregar arquivos grandes com streaming</span><span class="sxs-lookup"><span data-stu-id="62680-468">Upload large files with streaming</span></span>

<span data-ttu-id="62680-469">O exemplo a seguir demonstra como usar o JavaScript para transmitir um arquivo a uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="62680-469">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="62680-470">O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="62680-470">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="62680-471">Como o método de ação processa diretamente os dados carregados, a associação de modelo de formulário é desabilitada por outro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="62680-471">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="62680-472">Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="62680-472">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="62680-473">Depois que as seções com várias partes forem lidas, a ação executará sua própria associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="62680-473">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="62680-474">A resposta de página inicial carrega o formulário e salva um token de antifalsificação em um cookie (por meio do atributo `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="62680-474">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="62680-475">O atributo usa o [suporte de antifalsificação](xref:security/anti-request-forgery) interno do ASP.NET Core para definir um cookie com um token de solicitação:</span><span class="sxs-lookup"><span data-stu-id="62680-475">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="62680-476">O `DisableFormValueModelBindingAttribute` é usado para desabilitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="62680-476">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="62680-477">No aplicativo de exemplo, `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` são aplicados como filtros para os modelos de aplicativo de página de `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` no `Startup.ConfigureServices` usando [convenções de Razor Pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="62680-477">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="62680-478">Como a associação de modelo não lê o formulário, os parâmetros que estão associados do formulário não são associados (a consulta, a rota e o cabeçalho continuam a funcionar).</span><span class="sxs-lookup"><span data-stu-id="62680-478">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="62680-479">O método de ação funciona diretamente com a propriedade `Request`.</span><span class="sxs-lookup"><span data-stu-id="62680-479">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="62680-480">Um `MultipartReader` é usado para ler cada seção.</span><span class="sxs-lookup"><span data-stu-id="62680-480">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="62680-481">Os dados de chave/valor são armazenados em um `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="62680-481">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="62680-482">Depois que as seções com várias partes forem lidas, o conteúdo do `KeyValueAccumulator` será usado para associar os dados do formulário a um tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="62680-482">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="62680-483">O método `StreamingController.UploadDatabase` completo para streaming para um banco de dados com EF Core:</span><span class="sxs-lookup"><span data-stu-id="62680-483">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="62680-484">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="62680-484">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="62680-485">O método `StreamingController.UploadPhysical` completo para streaming para um local físico:</span><span class="sxs-lookup"><span data-stu-id="62680-485">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="62680-486">No aplicativo de exemplo, as verificações de validação são manipuladas por `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="62680-486">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="62680-487">Validação</span><span class="sxs-lookup"><span data-stu-id="62680-487">Validation</span></span>

<span data-ttu-id="62680-488">A classe `FileHelpers` do aplicativo de exemplo demonstra várias verificações de carregamentos em buffer <xref:Microsoft.AspNetCore.Http.IFormFile> e de arquivos transmitidos.</span><span class="sxs-lookup"><span data-stu-id="62680-488">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="62680-489">Para processar carregamentos de arquivo em buffer <xref:Microsoft.AspNetCore.Http.IFormFile> no aplicativo de exemplo, consulte o método `ProcessFormFile` no arquivo *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="62680-489">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="62680-490">Para o processamento de arquivos transmitidos, consulte o método `ProcessStreamedFile` no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-490">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="62680-491">Os métodos de processamento de validação demonstrados no aplicativo de exemplo não verificam o conteúdo dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="62680-491">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="62680-492">Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para os usuários ou outros sistemas.</span><span class="sxs-lookup"><span data-stu-id="62680-492">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="62680-493">Embora o tópico exemplo forneça um exemplo de trabalho de técnicas de validação, não implemente a classe `FileHelpers` em um aplicativo de produção, a menos que você:</span><span class="sxs-lookup"><span data-stu-id="62680-493">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="62680-494">Entenda totalmente a implementação.</span><span class="sxs-lookup"><span data-stu-id="62680-494">Fully understand the implementation.</span></span>
> * <span data-ttu-id="62680-495">Modifique a implementação conforme apropriado para o ambiente e as especificações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62680-495">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="62680-496">**Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**</span><span class="sxs-lookup"><span data-stu-id="62680-496">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="62680-497">Validação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="62680-497">Content validation</span></span>

<span data-ttu-id="62680-498">**Use uma API de verificação de vírus/malware de terceiros no conteúdo carregado.**</span><span class="sxs-lookup"><span data-stu-id="62680-498">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="62680-499">A verificação de arquivos exige muitos recursos do servidor em cenários de alto volume.</span><span class="sxs-lookup"><span data-stu-id="62680-499">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="62680-500">Se o desempenho do processamento de solicitações for reduzido devido à verificação de arquivo, considere descarregar o trabalho de verificação para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62680-500">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="62680-501">Normalmente, os arquivos carregados são mantidos em uma área em quarentena até que o verificador de vírus em segundo plano os Verifique.</span><span class="sxs-lookup"><span data-stu-id="62680-501">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="62680-502">Quando um arquivo passa, o arquivo é movido para o local de armazenamento de arquivo normal.</span><span class="sxs-lookup"><span data-stu-id="62680-502">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="62680-503">Essas etapas geralmente são executadas em conjunto com um registro de banco de dados que indica o status de verificação de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-503">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="62680-504">Ao usar essa abordagem, o aplicativo e o servidor de aplicativos permanecem concentrados em responder às solicitações.</span><span class="sxs-lookup"><span data-stu-id="62680-504">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="62680-505">Validação de extensão de arquivo</span><span class="sxs-lookup"><span data-stu-id="62680-505">File extension validation</span></span>

<span data-ttu-id="62680-506">A extensão do arquivo carregado deve ser verificada em uma lista de extensões permitidas.</span><span class="sxs-lookup"><span data-stu-id="62680-506">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="62680-507">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="62680-507">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="62680-508">Validação de assinatura de arquivo</span><span class="sxs-lookup"><span data-stu-id="62680-508">File signature validation</span></span>

<span data-ttu-id="62680-509">A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-509">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="62680-510">Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="62680-510">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="62680-511">O aplicativo de exemplo verifica as assinaturas de arquivo para alguns tipos de arquivo comuns.</span><span class="sxs-lookup"><span data-stu-id="62680-511">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="62680-512">No exemplo a seguir, a assinatura de arquivo para uma imagem JPEG é verificada em relação ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="62680-512">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="62680-513">Para obter assinaturas de arquivo adicionais, consulte o [banco de dados assinaturas de arquivo](https://www.filesignatures.net/) e especificações de arquivo oficial.</span><span class="sxs-lookup"><span data-stu-id="62680-513">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="62680-514">Segurança de nome de arquivo</span><span class="sxs-lookup"><span data-stu-id="62680-514">File name security</span></span>

<span data-ttu-id="62680-515">Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo no armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="62680-515">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="62680-516">Crie um nome de arquivo seguro para o arquivo usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.</span><span class="sxs-lookup"><span data-stu-id="62680-516">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="62680-517">O Razor automaticamente codifica os valores de propriedade para exibição.</span><span class="sxs-lookup"><span data-stu-id="62680-517">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="62680-518">O código a seguir é seguro para usar:</span><span class="sxs-lookup"><span data-stu-id="62680-518">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="62680-519">Fora do Razor, sempre <xref:System.Net.WebUtility.HtmlEncode*> conteúdo de nome de arquivo da solicitação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="62680-519">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="62680-520">Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo será substituído por um arquivo com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="62680-520">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="62680-521">Forneça lógica adicional para atender às especificações do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62680-521">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="62680-522">Validação de tamanho</span><span class="sxs-lookup"><span data-stu-id="62680-522">Size validation</span></span>

<span data-ttu-id="62680-523">Limite o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="62680-523">Limit the size of uploaded files.</span></span>

<span data-ttu-id="62680-524">No aplicativo de exemplo, o tamanho do arquivo é limitado a 2 MB (indicado em bytes).</span><span class="sxs-lookup"><span data-stu-id="62680-524">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="62680-525">O limite é fornecido por meio da [configuração](xref:fundamentals/configuration/index) do arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="62680-525">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="62680-526">O `FileSizeLimit` é injetado em classes `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="62680-526">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="62680-527">Quando um tamanho de arquivo exceder o limite, o arquivo será rejeitado:</span><span class="sxs-lookup"><span data-stu-id="62680-527">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="62680-528">Coincidir valor do atributo de nome com o nome do parâmetro do método POST</span><span class="sxs-lookup"><span data-stu-id="62680-528">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="62680-529">Em formulários não Razor que POSTam dados de formulário ou usam o `FormData` do JavaScript diretamente, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="62680-529">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="62680-530">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="62680-530">In the following example:</span></span>

* <span data-ttu-id="62680-531">Ao usar um elemento `<input>`, o atributo `name` é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="62680-531">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="62680-532">Ao usar `FormData` em JavaScript, o nome é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="62680-532">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="62680-533">Use um nome correspondente para o parâmetro do C# método (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="62680-533">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="62680-534">Para um método de manipulador de página Razor Pages chamado `Upload`:</span><span class="sxs-lookup"><span data-stu-id="62680-534">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="62680-535">Para um método de ação do controlador de POSTAgem MVC:</span><span class="sxs-lookup"><span data-stu-id="62680-535">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="62680-536">Configuração de servidor e aplicativo</span><span class="sxs-lookup"><span data-stu-id="62680-536">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="62680-537">Limite de comprimento de corpo com várias partes</span><span class="sxs-lookup"><span data-stu-id="62680-537">Multipart body length limit</span></span>

<span data-ttu-id="62680-538"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> define o limite para o comprimento de cada corpo com diversas partes.</span><span class="sxs-lookup"><span data-stu-id="62680-538"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="62680-539">As seções de formulário que excedem esse limite lançam um <xref:System.IO.InvalidDataException> quando analisadas.</span><span class="sxs-lookup"><span data-stu-id="62680-539">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="62680-540">O padrão é 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="62680-540">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="62680-541">Personalize o limite usando a configuração <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="62680-541">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="62680-542"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> é usado para definir o <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="62680-542"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="62680-543">Em um aplicativo Razor Pages, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="62680-543">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="62680-544">Em um aplicativo Razor Pages ou em um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="62680-544">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="62680-545">Tamanho máximo do corpo de solicitação do Kestrel</span><span class="sxs-lookup"><span data-stu-id="62680-545">Kestrel maximum request body size</span></span>

<span data-ttu-id="62680-546">Para aplicativos hospedados pelo Kestrel, o tamanho máximo padrão do corpo da solicitação é 30 milhões bytes, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="62680-546">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="62680-547">Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="62680-547">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="62680-548"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="62680-548"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="62680-549">Em um aplicativo Razor Pages, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="62680-549">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="62680-550">Em um aplicativo de páginas Razor ou um aplicativo MVC, aplique o filtro à classe de manipulador de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="62680-550">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="62680-551">Outros limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="62680-551">Other Kestrel limits</span></span>

<span data-ttu-id="62680-552">Outros limites de Kestrel podem ser aplicados para aplicativos hospedados pelo Kestrel:</span><span class="sxs-lookup"><span data-stu-id="62680-552">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="62680-553">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="62680-553">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="62680-554">Taxas de dados de solicitação e resposta</span><span class="sxs-lookup"><span data-stu-id="62680-554">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="62680-555">Limite de comprimento de conteúdo do IIS</span><span class="sxs-lookup"><span data-stu-id="62680-555">IIS content length limit</span></span>

<span data-ttu-id="62680-556">O limite de solicitação padrão (`maxAllowedContentLength`) é 30 milhões bytes, que é de aproximadamente 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="62680-556">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="62680-557">Personalize o limite no arquivo *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="62680-557">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="62680-558">Essa configuração só se aplica ao IIS.</span><span class="sxs-lookup"><span data-stu-id="62680-558">This setting only applies to IIS.</span></span> <span data-ttu-id="62680-559">Esse comportamento não ocorre por padrão quando a hospedagem é feita no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="62680-559">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="62680-560">Para obter mais informações, consulte [Limits Request \<requestLimits >](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="62680-560">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="62680-561">As limitações no módulo ASP.NET Core ou na presença do módulo filtragem de solicitações do IIS podem limitar os carregamentos a dois ou 4 GB.</span><span class="sxs-lookup"><span data-stu-id="62680-561">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="62680-562">Para obter mais informações, consulte [não é possível carregar o arquivo com mais de 2GB de tamanho (ASPNET/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="62680-562">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="62680-563">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="62680-563">Troubleshoot</span></span>

<span data-ttu-id="62680-564">Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.</span><span class="sxs-lookup"><span data-stu-id="62680-564">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="62680-565">Erro não encontrado quando implantado em um servidor IIS</span><span class="sxs-lookup"><span data-stu-id="62680-565">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="62680-566">O erro a seguir indica que o arquivo carregado excede o tamanho do conteúdo configurado do servidor:</span><span class="sxs-lookup"><span data-stu-id="62680-566">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="62680-567">Para obter mais informações sobre como aumentar o limite, consulte a seção [limite de tamanho de conteúdo do IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="62680-567">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="62680-568">Falha na conexão</span><span class="sxs-lookup"><span data-stu-id="62680-568">Connection failure</span></span>

<span data-ttu-id="62680-569">Um erro de conexão e uma conexão de servidor de redefinição provavelmente indicam que o arquivo carregado excede o tamanho máximo do corpo de solicitação do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="62680-569">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="62680-570">Para obter mais informações, consulte a seção [tamanho máximo de corpo de solicitação do Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="62680-570">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="62680-571">Os limites de conexão de cliente Kestrel também podem exigir ajuste.</span><span class="sxs-lookup"><span data-stu-id="62680-571">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="62680-572">Exceção de referência nula com IFormFile</span><span class="sxs-lookup"><span data-stu-id="62680-572">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="62680-573">Se o controlador estiver aceitando arquivos carregados usando <xref:Microsoft.AspNetCore.Http.IFormFile>, mas o valor for `null`, confirme se o formulário HTML está especificando um valor `enctype` de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="62680-573">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="62680-574">Se esse atributo não estiver definido no elemento `<form>`, o upload do arquivo não ocorrerá e todos os argumentos vinculados a <xref:Microsoft.AspNetCore.Http.IFormFile> serão `null`.</span><span class="sxs-lookup"><span data-stu-id="62680-574">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="62680-575">Além disso, confirme se a [nomenclatura de carregamento em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="62680-575">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="62680-576">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="62680-576">Additional resources</span></span>

* <span data-ttu-id="62680-577">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="62680-577">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)</span></span>
* <span data-ttu-id="62680-578">Segurança de @no__t 0Azure: Quadro de segurança: Validação de entrada | Mitigações @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="62680-578">[Azure Security: Security Frame: Input Validation | Mitigations](/azure/security/azure-security-threat-modeling-tool-input-validation)</span></span>
* <span data-ttu-id="62680-579">Padrões de design de nuvem @no__t 0Azure: Padrão de chave valet @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="62680-579">[Azure Cloud Design Patterns: Valet Key pattern](/azure/architecture/patterns/valet-key)</span></span>

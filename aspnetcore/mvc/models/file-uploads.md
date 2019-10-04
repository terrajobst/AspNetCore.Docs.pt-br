---
title: Carregar arquivos no ASP.NET Core
author: guardrex
description: Como usar o model binding e o streaming para carregar arquivos no ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/30/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: 46cdb517742431a7f2a93155564832d586233e68
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925113"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="b0620-103">Carregar arquivos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0620-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="b0620-104">De [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/)e [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="b0620-104">By [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/), and [Rutger Storm](https://github.com/rutix)</span></span>

<span data-ttu-id="b0620-105">O ASP.NET Core dá suporte ao carregamento de um ou mais arquivos usando a associação de modelo em buffer para arquivos menores e streaming sem buffer para arquivos maiores.</span><span class="sxs-lookup"><span data-stu-id="b0620-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="b0620-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b0620-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="b0620-107">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="b0620-107">Security considerations</span></span>

<span data-ttu-id="b0620-108">Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor.</span><span class="sxs-lookup"><span data-stu-id="b0620-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="b0620-109">Os invasores podem tentar:</span><span class="sxs-lookup"><span data-stu-id="b0620-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="b0620-110">Executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="b0620-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="b0620-111">Carregar vírus ou malware.</span><span class="sxs-lookup"><span data-stu-id="b0620-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="b0620-112">Comprometa redes e servidores de outras maneiras.</span><span class="sxs-lookup"><span data-stu-id="b0620-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="b0620-113">As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:</span><span class="sxs-lookup"><span data-stu-id="b0620-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="b0620-114">Carregue arquivos em uma área de carregamento de arquivo dedicada no sistema, preferencialmente para uma unidade que não seja do sistema.</span><span class="sxs-lookup"><span data-stu-id="b0620-114">Upload files to a dedicated file upload area on the system, preferably to a non-system drive.</span></span> <span data-ttu-id="b0620-115">Usar um local dedicado torna mais fácil impor restrições de segurança em arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="b0620-115">Using a dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="b0620-116">Desabilite as permissões de execução no local de carregamento do arquivo. &dagger;</span><span class="sxs-lookup"><span data-stu-id="b0620-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="b0620-117">Nunca persista arquivos carregados na mesma árvore de diretório que o aplicativo. &dagger;</span><span class="sxs-lookup"><span data-stu-id="b0620-117">Never persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="b0620-118">Use um nome de arquivo seguro determinado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0620-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="b0620-119">Não use um nome de arquivo fornecido pelo usuário ou o nome de arquivo não confiável do arquivo carregado. &dagger; Para exibir um nome de arquivo não confiável em uma interface do usuário ou em uma mensagem de log, codifique o valor em HTML.</span><span class="sxs-lookup"><span data-stu-id="b0620-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; To display an untrusted file name in a UI or in a logging message, HTML-encode the value.</span></span>
* <span data-ttu-id="b0620-120">Permitir apenas um conjunto específico de extensões de arquivo aprovadas. &dagger;</span><span class="sxs-lookup"><span data-stu-id="b0620-120">Only allow a specific set of approved file extensions.&dagger;</span></span>
* <span data-ttu-id="b0620-121">Verifique a assinatura de formato de arquivo para impedir que um usuário carregue um arquivo mascarado. &dagger; Por exemplo, não permita que um usuário carregue um arquivo *. exe* com uma extensão *. txt* .</span><span class="sxs-lookup"><span data-stu-id="b0620-121">Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension.</span></span>
* <span data-ttu-id="b0620-122">Verifique se as verificações do lado do cliente também são executadas no servidor. &dagger; As verificações do lado do cliente são fáceis de contornar.</span><span class="sxs-lookup"><span data-stu-id="b0620-122">Verify that client-side checks are also performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="b0620-123">Verifique o tamanho de um arquivo carregado e evite carregamentos maiores que o esperado. &dagger;</span><span class="sxs-lookup"><span data-stu-id="b0620-123">Check the size of an uploaded file and prevent uploads that are larger than expected.&dagger;</span></span>
* <span data-ttu-id="b0620-124">Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0620-124">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="b0620-125">**Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**</span><span class="sxs-lookup"><span data-stu-id="b0620-125">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="b0620-126">o aplicativo de exemplo &dagger;The demonstra uma abordagem que atende aos critérios.</span><span class="sxs-lookup"><span data-stu-id="b0620-126">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="b0620-127">Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:</span><span class="sxs-lookup"><span data-stu-id="b0620-127">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="b0620-128">Assuma completamente o controle de um sistema.</span><span class="sxs-lookup"><span data-stu-id="b0620-128">Completely gain control of a system.</span></span>
> * <span data-ttu-id="b0620-129">Sobrecarregar um sistema com o resultado que o sistema falha.</span><span class="sxs-lookup"><span data-stu-id="b0620-129">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="b0620-130">Comprometer dados do sistema ou de usuários.</span><span class="sxs-lookup"><span data-stu-id="b0620-130">Compromise user or system data.</span></span>
> * <span data-ttu-id="b0620-131">Aplique graffiti a uma interface do usuário pública.</span><span class="sxs-lookup"><span data-stu-id="b0620-131">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="b0620-132">Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="b0620-132">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="b0620-133">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="b0620-133">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)</span></span>
> * <span data-ttu-id="b0620-134">Segurança de @no__t 0Azure: Verifique se os controles apropriados estão em vigor ao aceitar arquivos de usuários @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="b0620-134">[Azure Security: Ensure appropriate controls are in place when accepting files from users](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)</span></span>

<span data-ttu-id="b0620-135">Para obter mais informações sobre como implementar medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [validação](#validation) .</span><span class="sxs-lookup"><span data-stu-id="b0620-135">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="b0620-136">Cenários de armazenamento</span><span class="sxs-lookup"><span data-stu-id="b0620-136">Storage scenarios</span></span>

<span data-ttu-id="b0620-137">As opções de armazenamento comuns para arquivos incluem:</span><span class="sxs-lookup"><span data-stu-id="b0620-137">Common storage options for files include:</span></span>

* <span data-ttu-id="b0620-138">Banco de Dados</span><span class="sxs-lookup"><span data-stu-id="b0620-138">Database</span></span>

  * <span data-ttu-id="b0620-139">Para carregamentos de arquivos pequenos, um banco de dados geralmente é mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).</span><span class="sxs-lookup"><span data-stu-id="b0620-139">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="b0620-140">Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, pois a recuperação de um registro de banco de dados para o usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de Avatar).</span><span class="sxs-lookup"><span data-stu-id="b0620-140">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="b0620-141">Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dado.</span><span class="sxs-lookup"><span data-stu-id="b0620-141">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="b0620-142">Armazenamento físico (sistema de arquivos ou compartilhamento de rede)</span><span class="sxs-lookup"><span data-stu-id="b0620-142">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="b0620-143">Para carregamentos de arquivos grandes:</span><span class="sxs-lookup"><span data-stu-id="b0620-143">For large file uploads:</span></span>
    * <span data-ttu-id="b0620-144">Os limites de banco de dados podem restringir o tamanho do carregamento.</span><span class="sxs-lookup"><span data-stu-id="b0620-144">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="b0620-145">O armazenamento físico é geralmente menos econômico do que o armazenamento em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b0620-145">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="b0620-146">O armazenamento físico é potencialmente mais barato do que usar um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="b0620-146">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="b0620-147">O processo do aplicativo deve ter permissões de leitura e gravação no local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="b0620-147">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="b0620-148">**Nunca conceda a permissão EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="b0620-148">**Never grant execute permission.**</span></span>

* <span data-ttu-id="b0620-149">Serviço de armazenamento de dados (por exemplo, [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="b0620-149">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="b0620-150">Os serviços geralmente oferecem escalabilidade e resiliência aprimoradas sobre soluções locais que geralmente estão sujeitas a pontos únicos de falha.</span><span class="sxs-lookup"><span data-stu-id="b0620-150">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="b0620-151">Os serviços são um custo potencialmente menor em cenários de infraestrutura de armazenamento grandes.</span><span class="sxs-lookup"><span data-stu-id="b0620-151">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="b0620-152">Para obter mais informações, confira [Início Rápido: Use o .NET para criar um blob no armazenamento de objetos @ no__t-0.</span><span class="sxs-lookup"><span data-stu-id="b0620-152">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="b0620-153">O tópico demonstra <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, mas <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> pode ser usado para salvar um <xref:System.IO.FileStream> no armazenamento de BLOBs ao trabalhar com um <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="b0620-153">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="b0620-154">Cenários de upload de arquivo</span><span class="sxs-lookup"><span data-stu-id="b0620-154">File upload scenarios</span></span>

<span data-ttu-id="b0620-155">Duas abordagens gerais para carregar arquivos são buffer e streaming.</span><span class="sxs-lookup"><span data-stu-id="b0620-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="b0620-156">**Buffer**</span><span class="sxs-lookup"><span data-stu-id="b0620-156">**Buffering**</span></span>

<span data-ttu-id="b0620-157">O arquivo inteiro é lido em um <xref:Microsoft.AspNetCore.Http.IFormFile>, que é uma C# representação do arquivo usado para processar ou salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0620-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="b0620-158">Os recursos (disco, memória) usados por carregamentos de arquivos dependem do número e do tamanho de carregamentos de arquivos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="b0620-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="b0620-159">Se um aplicativo tentar armazenar em buffer um número excessivo de carregamentos, o site falhará quando ficar sem memória ou espaço em disco.</span><span class="sxs-lookup"><span data-stu-id="b0620-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="b0620-160">Se o tamanho ou a frequência dos carregamentos de arquivos estiver esgotando os recursos do aplicativo, use o streaming.</span><span class="sxs-lookup"><span data-stu-id="b0620-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="b0620-161">Qualquer arquivo em buffer único que exceda 64 KB é movido da memória para um arquivo temporário no disco.</span><span class="sxs-lookup"><span data-stu-id="b0620-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="b0620-162">O armazenamento em buffer de arquivos pequenos é abordado nas seguintes seções deste tópico:</span><span class="sxs-lookup"><span data-stu-id="b0620-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="b0620-163">Armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="b0620-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="b0620-164">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="b0620-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="b0620-165">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="b0620-165">**Streaming**</span></span>

<span data-ttu-id="b0620-166">O arquivo é recebido de uma solicitação de várias partes e processada ou salva diretamente pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0620-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="b0620-167">O streaming não melhora significativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="b0620-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="b0620-168">O streaming reduz as demandas de memória ou espaço em disco ao carregar arquivos.</span><span class="sxs-lookup"><span data-stu-id="b0620-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="b0620-169">O streaming de arquivos grandes é abordado na seção [carregar arquivos grandes com streaming](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="b0620-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="b0620-170">Carregar arquivos pequenos com associação de modelo em buffer para armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="b0620-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="b0620-171">Para carregar arquivos pequenos, use um formulário com diversas partes ou Construa uma solicitação POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b0620-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="b0620-172">O exemplo a seguir demonstra o uso de um Razor Pages formulário para carregar um único arquivo (*pages/BufferedSingleFileUploadPhysical. cshtml* no aplicativo de exemplo):</span><span class="sxs-lookup"><span data-stu-id="b0620-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="b0620-173">O exemplo a seguir é análogo ao exemplo anterior, exceto pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="b0620-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="b0620-174">O JavaScript ([buscar API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) é usado para enviar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="b0620-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="b0620-175">Não há validação.</span><span class="sxs-lookup"><span data-stu-id="b0620-175">There's no validation.</span></span>

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

<span data-ttu-id="b0620-176">Para executar a POSTAgem de formulário no JavaScript para clientes que [não dão suporte à API de busca](https://caniuse.com/#feat=fetch), use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="b0620-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="b0620-177">Use um polifill de busca (por exemplo, [janela. FETCH retroativo (github/FETCH)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="b0620-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="b0620-178">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="b0620-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="b0620-179">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b0620-179">For example:</span></span>

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

<span data-ttu-id="b0620-180">Para dar suporte a uploads de arquivo, os formulários HTML devem especificar um tipo de codificação (`enctype`) de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="b0620-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="b0620-181">Para um elemento de entrada `files` para dar suporte ao carregamento de vários arquivos, forneça o atributo `multiple` no elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="b0620-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="b0620-182">Os arquivos individuais carregados no servidor podem ser acessados por meio de [Associação de modelo](xref:mvc/models/model-binding) usando <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="b0620-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="b0620-183">O aplicativo de exemplo demonstra vários uploads de arquivo em buffer para cenários de armazenamento físico e banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b0620-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

> [!WARNING]
> <span data-ttu-id="b0620-184">Não confie ou confie na propriedade `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação.</span><span class="sxs-lookup"><span data-stu-id="b0620-184">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="b0620-185">A propriedade `FileName` só deve ser usada para fins de exibição e somente após a codificação HTML do valor.</span><span class="sxs-lookup"><span data-stu-id="b0620-185">The `FileName` property should only be used for display purposes and only after HTML encoding the value.</span></span>
>
> <span data-ttu-id="b0620-186">Os exemplos fornecidos até o momento não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="b0620-186">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="b0620-187">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="b0620-187">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="b0620-188">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="b0620-188">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="b0620-189">Validação</span><span class="sxs-lookup"><span data-stu-id="b0620-189">Validation</span></span>](#validation)

<span data-ttu-id="b0620-190">Ao carregar arquivos usando a associação de modelo e <xref:Microsoft.AspNetCore.Http.IFormFile>, o método de ação pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="b0620-190">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="b0620-191">Um único <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="b0620-191">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="b0620-192">Qualquer uma das seguintes coleções que representam vários arquivos:</span><span class="sxs-lookup"><span data-stu-id="b0620-192">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="b0620-193">[Listar](xref:System.Collections.Generic.List`1)\< @ no__t-2 @ no__t-3</span><span class="sxs-lookup"><span data-stu-id="b0620-193">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="b0620-194">A Associação corresponde aos arquivos de formulário por nome.</span><span class="sxs-lookup"><span data-stu-id="b0620-194">Binding matches form files by name.</span></span> <span data-ttu-id="b0620-195">Por exemplo, o valor HTML `name` em `<input type="file" name="formFile">` deve corresponder ao C# parâmetro/propriedade associado (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="b0620-195">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="b0620-196">Para obter mais informações, consulte o [valor do atributo Match Name para o nome do parâmetro da seção do método post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="b0620-196">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="b0620-197">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="b0620-197">The following example:</span></span>

* <span data-ttu-id="b0620-198">Executa um loop em um ou mais arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="b0620-198">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="b0620-199">Usa [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0620-199">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="b0620-200">Salva os arquivos no sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0620-200">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="b0620-201">Retorna o número total e o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="b0620-201">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="b0620-202">Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho.</span><span class="sxs-lookup"><span data-stu-id="b0620-202">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="b0620-203">No exemplo a seguir, o caminho é obtido da configuração:</span><span class="sxs-lookup"><span data-stu-id="b0620-203">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="b0620-204">O caminho passado para o <xref:System.IO.FileStream> *deve* incluir o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0620-204">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="b0620-205">Se o nome do arquivo não for fornecido, um <xref:System.UnauthorizedAccessException> será lançado em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b0620-205">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="b0620-206">Os arquivos carregados usando a técnica <xref:Microsoft.AspNetCore.Http.IFormFile> são armazenados em buffer na memória ou no disco no servidor antes do processamento.</span><span class="sxs-lookup"><span data-stu-id="b0620-206">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="b0620-207">Dentro do método de ação, o conteúdo <xref:Microsoft.AspNetCore.Http.IFormFile> é acessível como um <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="b0620-207">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="b0620-208">Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como o [armazenamento de BLOBs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="b0620-208">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="b0620-209">Para outro exemplo que executa um loop sobre vários arquivos para carregar e usa nomes de arquivo seguros, consulte *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0620-209">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="b0620-210">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) gera um <xref:System.IO.IOException> se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores.</span><span class="sxs-lookup"><span data-stu-id="b0620-210">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="b0620-211">O limite de 65.535 arquivos é um limite por servidor.</span><span class="sxs-lookup"><span data-stu-id="b0620-211">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="b0620-212">Para obter mais informações sobre esse limite no sistema operacional Windows, consulte os comentários nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="b0620-212">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="b0620-213">Função GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="b0620-213">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="b0620-214">Carregar arquivos pequenos com associação de modelo em buffer para um banco de dados</span><span class="sxs-lookup"><span data-stu-id="b0620-214">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="b0620-215">Para armazenar dados de arquivo binário em um banco de dado usando [Entity Framework](/ef/core/index), defina uma propriedade de matriz <xref:System.Byte> na entidade:</span><span class="sxs-lookup"><span data-stu-id="b0620-215">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="b0620-216">Especifique uma propriedade de modelo de página para a classe que inclui um <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="b0620-216">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="b0620-217"><xref:Microsoft.AspNetCore.Http.IFormFile> pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="b0620-217"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="b0620-218">O exemplo anterior usa uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="b0620-218">The prior example uses a bound model property.</span></span>

<span data-ttu-id="b0620-219">O `FileUpload` é usado no formulário de Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="b0620-219">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="b0620-220">Quando o formulário for Postado no servidor, copie o <xref:Microsoft.AspNetCore.Http.IFormFile> em um fluxo e salve-o como uma matriz de bytes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b0620-220">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="b0620-221">No exemplo a seguir, `_dbContext` armazena o contexto do banco de dados do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b0620-221">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="b0620-222">O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="b0620-222">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="b0620-223">*Páginas/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="b0620-223">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="b0620-224">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="b0620-224">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="b0620-225">Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="b0620-225">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="b0620-226">Não confie ou confie na propriedade `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação.</span><span class="sxs-lookup"><span data-stu-id="b0620-226">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="b0620-227">A propriedade `FileName` só deve ser usada para fins de exibição e somente após a codificação HTML.</span><span class="sxs-lookup"><span data-stu-id="b0620-227">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="b0620-228">Os exemplos fornecidos não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="b0620-228">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="b0620-229">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="b0620-229">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="b0620-230">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="b0620-230">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="b0620-231">Validação</span><span class="sxs-lookup"><span data-stu-id="b0620-231">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="b0620-232">Carregar arquivos grandes com streaming</span><span class="sxs-lookup"><span data-stu-id="b0620-232">Upload large files with streaming</span></span>

<span data-ttu-id="b0620-233">O exemplo a seguir demonstra como usar o JavaScript para transmitir um arquivo a uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="b0620-233">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="b0620-234">O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="b0620-234">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="b0620-235">Como o método de ação processa diretamente os dados carregados, a associação de modelo de formulário é desabilitada por outro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="b0620-235">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="b0620-236">Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="b0620-236">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="b0620-237">Depois que as seções com várias partes forem lidas, a ação executará sua própria associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="b0620-237">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="b0620-238">A resposta de página inicial carrega o formulário e salva um token de antifalsificação em um cookie (por meio do atributo `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="b0620-238">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="b0620-239">O atributo usa o [suporte de antifalsificação](xref:security/anti-request-forgery) interno do ASP.NET Core para definir um cookie com um token de solicitação:</span><span class="sxs-lookup"><span data-stu-id="b0620-239">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="b0620-240">O `DisableFormValueModelBindingAttribute` é usado para desabilitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="b0620-240">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="b0620-241">No aplicativo de exemplo, `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` são aplicados como filtros para os modelos de aplicativo de página de `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` no `Startup.ConfigureServices` usando [convenções de Razor Pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="b0620-241">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="b0620-242">Como a associação de modelo não lê o formulário, os parâmetros que estão associados do formulário não são associados (a consulta, a rota e o cabeçalho continuam a funcionar).</span><span class="sxs-lookup"><span data-stu-id="b0620-242">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="b0620-243">O método de ação funciona diretamente com a propriedade `Request`.</span><span class="sxs-lookup"><span data-stu-id="b0620-243">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="b0620-244">Um `MultipartReader` é usado para ler cada seção.</span><span class="sxs-lookup"><span data-stu-id="b0620-244">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="b0620-245">Os dados de chave/valor são armazenados em um `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="b0620-245">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="b0620-246">Depois que as seções com várias partes forem lidas, o conteúdo do `KeyValueAccumulator` será usado para associar os dados do formulário a um tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="b0620-246">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="b0620-247">O método `StreamingController.UploadDatabase` completo para streaming para um banco de dados com EF Core:</span><span class="sxs-lookup"><span data-stu-id="b0620-247">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="b0620-248">O método `StreamingController.UploadPhysical` completo para streaming para um local físico:</span><span class="sxs-lookup"><span data-stu-id="b0620-248">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="b0620-249">No aplicativo de exemplo, as verificações de validação são manipuladas por `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="b0620-249">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="b0620-250">Validação</span><span class="sxs-lookup"><span data-stu-id="b0620-250">Validation</span></span>

<span data-ttu-id="b0620-251">A classe `FileHelpers` do aplicativo de exemplo demonstra várias verificações de carregamentos em buffer <xref:Microsoft.AspNetCore.Http.IFormFile> e de arquivos transmitidos.</span><span class="sxs-lookup"><span data-stu-id="b0620-251">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="b0620-252">Para processar carregamentos de arquivo em buffer <xref:Microsoft.AspNetCore.Http.IFormFile> no aplicativo de exemplo, consulte o método `ProcessFormFile` no arquivo *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="b0620-252">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="b0620-253">Para o processamento de arquivos transmitidos, consulte o método `ProcessStreamedFile` no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0620-253">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="b0620-254">Os métodos de processamento de validação demonstrados no aplicativo de exemplo não verificam o conteúdo dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="b0620-254">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="b0620-255">Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para os usuários ou outros sistemas.</span><span class="sxs-lookup"><span data-stu-id="b0620-255">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="b0620-256">Embora o tópico exemplo forneça um exemplo de trabalho de técnicas de validação, não implemente a classe `FileHelpers` em um aplicativo de produção, a menos que você:</span><span class="sxs-lookup"><span data-stu-id="b0620-256">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="b0620-257">Entenda totalmente a implementação.</span><span class="sxs-lookup"><span data-stu-id="b0620-257">Fully understand the implementation.</span></span>
> * <span data-ttu-id="b0620-258">Modifique a implementação conforme apropriado para o ambiente e as especificações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0620-258">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="b0620-259">**Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**</span><span class="sxs-lookup"><span data-stu-id="b0620-259">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="b0620-260">Validação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="b0620-260">Content validation</span></span>

<span data-ttu-id="b0620-261">**Use uma API de verificação de vírus/malware de terceiros no conteúdo carregado.**</span><span class="sxs-lookup"><span data-stu-id="b0620-261">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="b0620-262">A verificação de arquivos exige muitos recursos do servidor em cenários de alto volume.</span><span class="sxs-lookup"><span data-stu-id="b0620-262">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="b0620-263">Se o desempenho do processamento de solicitações for reduzido devido à verificação de arquivo, considere descarregar o trabalho de verificação para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0620-263">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="b0620-264">Normalmente, os arquivos carregados são mantidos em uma área em quarentena até que o verificador de vírus em segundo plano os Verifique.</span><span class="sxs-lookup"><span data-stu-id="b0620-264">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="b0620-265">Quando um arquivo passa, o arquivo é movido para o local de armazenamento de arquivo normal.</span><span class="sxs-lookup"><span data-stu-id="b0620-265">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="b0620-266">Essas etapas geralmente são executadas em conjunto com um registro de banco de dados que indica o status de verificação de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0620-266">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="b0620-267">Ao usar essa abordagem, o aplicativo e o servidor de aplicativos permanecem concentrados em responder às solicitações.</span><span class="sxs-lookup"><span data-stu-id="b0620-267">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="b0620-268">Validação de extensão de arquivo</span><span class="sxs-lookup"><span data-stu-id="b0620-268">File extension validation</span></span>

<span data-ttu-id="b0620-269">A extensão do arquivo carregado deve ser verificada em uma lista de extensões permitidas.</span><span class="sxs-lookup"><span data-stu-id="b0620-269">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="b0620-270">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b0620-270">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="b0620-271">Validação de assinatura de arquivo</span><span class="sxs-lookup"><span data-stu-id="b0620-271">File signature validation</span></span>

<span data-ttu-id="b0620-272">A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0620-272">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="b0620-273">Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0620-273">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="b0620-274">O aplicativo de exemplo verifica as assinaturas de arquivo para alguns tipos de arquivo comuns.</span><span class="sxs-lookup"><span data-stu-id="b0620-274">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="b0620-275">No exemplo a seguir, a assinatura de arquivo para uma imagem JPEG é verificada em relação ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="b0620-275">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="b0620-276">Para obter assinaturas de arquivo adicionais, consulte o [banco de dados assinaturas de arquivo](https://www.filesignatures.net/) e especificações de arquivo oficial.</span><span class="sxs-lookup"><span data-stu-id="b0620-276">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="b0620-277">Segurança de nome de arquivo</span><span class="sxs-lookup"><span data-stu-id="b0620-277">File name security</span></span>

<span data-ttu-id="b0620-278">Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo no armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="b0620-278">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="b0620-279">Crie um nome de arquivo seguro para o arquivo usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.</span><span class="sxs-lookup"><span data-stu-id="b0620-279">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="b0620-280">O Razor automaticamente codifica os valores de propriedade para exibição.</span><span class="sxs-lookup"><span data-stu-id="b0620-280">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="b0620-281">O código a seguir é seguro para usar:</span><span class="sxs-lookup"><span data-stu-id="b0620-281">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="b0620-282">Fora do Razor, sempre <xref:System.Net.WebUtility.HtmlEncode*> conteúdo de nome de arquivo da solicitação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="b0620-282">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="b0620-283">Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo será substituído por um arquivo com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="b0620-283">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="b0620-284">Forneça lógica adicional para atender às especificações do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0620-284">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="b0620-285">Validação de tamanho</span><span class="sxs-lookup"><span data-stu-id="b0620-285">Size validation</span></span>

<span data-ttu-id="b0620-286">Limite o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="b0620-286">Limit the size of uploaded files.</span></span>

<span data-ttu-id="b0620-287">No aplicativo de exemplo, o tamanho do arquivo é limitado a 2 MB (indicado em bytes).</span><span class="sxs-lookup"><span data-stu-id="b0620-287">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="b0620-288">O limite é fornecido por meio da [configuração](xref:fundamentals/configuration/index) do arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b0620-288">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="b0620-289">O `FileSizeLimit` é injetado em classes `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="b0620-289">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="b0620-290">Quando um tamanho de arquivo exceder o limite, o arquivo será rejeitado:</span><span class="sxs-lookup"><span data-stu-id="b0620-290">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="b0620-291">Coincidir valor do atributo de nome com o nome do parâmetro do método POST</span><span class="sxs-lookup"><span data-stu-id="b0620-291">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="b0620-292">Em formulários não Razor que POSTam dados de formulário ou usam o `FormData` do JavaScript diretamente, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="b0620-292">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="b0620-293">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="b0620-293">In the following example:</span></span>

* <span data-ttu-id="b0620-294">Ao usar um elemento `<input>`, o atributo `name` é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="b0620-294">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="b0620-295">Ao usar `FormData` em JavaScript, o nome é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="b0620-295">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="b0620-296">Use um nome correspondente para o parâmetro do C# método (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="b0620-296">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="b0620-297">Para um método de manipulador de página Razor Pages chamado `Upload`:</span><span class="sxs-lookup"><span data-stu-id="b0620-297">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="b0620-298">Para um método de ação do controlador de POSTAgem MVC:</span><span class="sxs-lookup"><span data-stu-id="b0620-298">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="b0620-299">Configuração de servidor e aplicativo</span><span class="sxs-lookup"><span data-stu-id="b0620-299">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="b0620-300">Limite de comprimento de corpo com várias partes</span><span class="sxs-lookup"><span data-stu-id="b0620-300">Multipart body length limit</span></span>

<span data-ttu-id="b0620-301"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> define o limite para o comprimento de cada corpo com diversas partes.</span><span class="sxs-lookup"><span data-stu-id="b0620-301"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="b0620-302">As seções de formulário que excedem esse limite lançam um <xref:System.IO.InvalidDataException> quando analisadas.</span><span class="sxs-lookup"><span data-stu-id="b0620-302">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="b0620-303">O padrão é 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="b0620-303">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="b0620-304">Personalize o limite usando a configuração <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0620-304">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="b0620-305"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> é usado para definir o <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="b0620-305"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="b0620-306">Em um aplicativo Razor Pages, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0620-306">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="b0620-307">Em um aplicativo Razor Pages ou em um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="b0620-307">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="b0620-308">Tamanho máximo do corpo de solicitação do Kestrel</span><span class="sxs-lookup"><span data-stu-id="b0620-308">Kestrel maximum request body size</span></span>

<span data-ttu-id="b0620-309">Para aplicativos hospedados pelo Kestrel, o tamanho máximo padrão do corpo da solicitação é 30 milhões bytes, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="b0620-309">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="b0620-310">Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="b0620-310">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

<span data-ttu-id="b0620-311"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="b0620-311"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="b0620-312">Em um aplicativo Razor Pages, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0620-312">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="b0620-313">Em um aplicativo de páginas Razor ou um aplicativo MVC, aplique o filtro à classe de manipulador de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="b0620-313">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="b0620-314">Outros limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="b0620-314">Other Kestrel limits</span></span>

<span data-ttu-id="b0620-315">Outros limites de Kestrel podem ser aplicados para aplicativos hospedados pelo Kestrel:</span><span class="sxs-lookup"><span data-stu-id="b0620-315">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="b0620-316">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="b0620-316">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="b0620-317">Taxas de dados de solicitação e resposta</span><span class="sxs-lookup"><span data-stu-id="b0620-317">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="b0620-318">Limite de comprimento de conteúdo do IIS</span><span class="sxs-lookup"><span data-stu-id="b0620-318">IIS content length limit</span></span>

<span data-ttu-id="b0620-319">O limite de solicitação padrão (`maxAllowedContentLength`) é 30 milhões bytes, que é de aproximadamente 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="b0620-319">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="b0620-320">Personalize o limite no arquivo *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="b0620-320">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="b0620-321">Essa configuração só se aplica ao IIS.</span><span class="sxs-lookup"><span data-stu-id="b0620-321">This setting only applies to IIS.</span></span> <span data-ttu-id="b0620-322">Esse comportamento não ocorre por padrão quando a hospedagem é feita no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b0620-322">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="b0620-323">Para obter mais informações, consulte [Limits Request \<requestLimits >](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="b0620-323">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="b0620-324">As limitações no módulo ASP.NET Core ou na presença do módulo filtragem de solicitações do IIS podem limitar os carregamentos a dois ou 4 GB.</span><span class="sxs-lookup"><span data-stu-id="b0620-324">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="b0620-325">Para obter mais informações, consulte [não é possível carregar o arquivo com mais de 2GB de tamanho (ASPNET/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="b0620-325">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b0620-326">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="b0620-326">Troubleshoot</span></span>

<span data-ttu-id="b0620-327">Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.</span><span class="sxs-lookup"><span data-stu-id="b0620-327">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="b0620-328">Erro não encontrado quando implantado em um servidor IIS</span><span class="sxs-lookup"><span data-stu-id="b0620-328">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="b0620-329">O erro a seguir indica que o arquivo carregado excede o tamanho do conteúdo configurado do servidor:</span><span class="sxs-lookup"><span data-stu-id="b0620-329">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="b0620-330">Para obter mais informações sobre como aumentar o limite, consulte a seção [limite de tamanho de conteúdo do IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="b0620-330">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="b0620-331">Falha na conexão</span><span class="sxs-lookup"><span data-stu-id="b0620-331">Connection failure</span></span>

<span data-ttu-id="b0620-332">Um erro de conexão e uma conexão de servidor de redefinição provavelmente indicam que o arquivo carregado excede o tamanho máximo do corpo de solicitação do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b0620-332">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="b0620-333">Para obter mais informações, consulte a seção [tamanho máximo de corpo de solicitação do Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="b0620-333">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="b0620-334">Os limites de conexão de cliente Kestrel também podem exigir ajuste.</span><span class="sxs-lookup"><span data-stu-id="b0620-334">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="b0620-335">Exceção de referência nula com IFormFile</span><span class="sxs-lookup"><span data-stu-id="b0620-335">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="b0620-336">Se o controlador estiver aceitando arquivos carregados usando <xref:Microsoft.AspNetCore.Http.IFormFile>, mas o valor for `null`, confirme se o formulário HTML está especificando um valor `enctype` de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="b0620-336">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="b0620-337">Se esse atributo não estiver definido no elemento `<form>`, o upload do arquivo não ocorrerá e todos os argumentos vinculados a <xref:Microsoft.AspNetCore.Http.IFormFile> serão `null`.</span><span class="sxs-lookup"><span data-stu-id="b0620-337">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="b0620-338">Além disso, confirme se a [nomenclatura de carregamento em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="b0620-338">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b0620-339">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b0620-339">Additional resources</span></span>

* <span data-ttu-id="b0620-340">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="b0620-340">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)</span></span>
* <span data-ttu-id="b0620-341">Segurança de @no__t 0Azure: Quadro de segurança: Validação de entrada | Mitigações @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="b0620-341">[Azure Security: Security Frame: Input Validation | Mitigations](/azure/security/azure-security-threat-modeling-tool-input-validation)</span></span>
* <span data-ttu-id="b0620-342">Padrões de design de nuvem @no__t 0Azure: Padrão de chave valet @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="b0620-342">[Azure Cloud Design Patterns: Valet Key pattern](/azure/architecture/patterns/valet-key)</span></span>

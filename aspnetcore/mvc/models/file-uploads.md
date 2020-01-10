---
title: Carregar arquivos no ASP.NET Core
author: guardrex
description: Como usar o model binding e o streaming para carregar arquivos no ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: b5433576ff3e997e6d80201236be2d8463a52d07
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829225"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="9e38f-103">Carregar arquivos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9e38f-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="9e38f-104">De [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/)e [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="9e38f-104">By [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/), and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9e38f-105">O ASP.NET Core dá suporte ao carregamento de um ou mais arquivos usando a associação de modelo em buffer para arquivos menores e streaming sem buffer para arquivos maiores.</span><span class="sxs-lookup"><span data-stu-id="9e38f-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="9e38f-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9e38f-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="9e38f-107">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="9e38f-107">Security considerations</span></span>

<span data-ttu-id="9e38f-108">Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor.</span><span class="sxs-lookup"><span data-stu-id="9e38f-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="9e38f-109">Os invasores podem tentar:</span><span class="sxs-lookup"><span data-stu-id="9e38f-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="9e38f-110">Executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="9e38f-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="9e38f-111">Carregar vírus ou malware.</span><span class="sxs-lookup"><span data-stu-id="9e38f-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="9e38f-112">Comprometa redes e servidores de outras maneiras.</span><span class="sxs-lookup"><span data-stu-id="9e38f-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="9e38f-113">As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:</span><span class="sxs-lookup"><span data-stu-id="9e38f-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="9e38f-114">Carregue arquivos em uma área de carregamento de arquivo dedicada, preferencialmente para uma unidade que não seja do sistema.</span><span class="sxs-lookup"><span data-stu-id="9e38f-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="9e38f-115">Um local dedicado torna mais fácil impor restrições de segurança em arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="9e38f-116">Desabilite as permissões de execução no local de carregamento do arquivo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9e38f-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="9e38f-117">**Não** persiste arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9e38f-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="9e38f-118">Use um nome de arquivo seguro determinado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="9e38f-119">Não use um nome de arquivo fornecido pelo usuário ou o nome de arquivo não confiável do arquivo carregado.&dagger; código HTML codifica o nome de arquivo não confiável ao exibi-lo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="9e38f-120">Por exemplo, registrar em log o nome do arquivo ou exibindo na interface do usuário (o Razor codifica automaticamente a saída).</span><span class="sxs-lookup"><span data-stu-id="9e38f-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="9e38f-121">Permitir somente extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9e38f-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="9e38f-122">Verifique se as verificações do lado do cliente são executadas no servidor.&dagger; verificações do lado do cliente são fáceis de evitar.</span><span class="sxs-lookup"><span data-stu-id="9e38f-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="9e38f-123">Verifique o tamanho de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="9e38f-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="9e38f-124">Defina um limite de tamanho máximo para evitar carregamentos grandes.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9e38f-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="9e38f-125">Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="9e38f-126">**Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**</span><span class="sxs-lookup"><span data-stu-id="9e38f-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="9e38f-127">&dagger;aplicativo de exemplo demonstra uma abordagem que atende aos critérios.</span><span class="sxs-lookup"><span data-stu-id="9e38f-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="9e38f-128">Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:</span><span class="sxs-lookup"><span data-stu-id="9e38f-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="9e38f-129">Assuma completamente o controle de um sistema.</span><span class="sxs-lookup"><span data-stu-id="9e38f-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="9e38f-130">Sobrecarregar um sistema com o resultado que o sistema falha.</span><span class="sxs-lookup"><span data-stu-id="9e38f-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="9e38f-131">Comprometer dados do sistema ou de usuários.</span><span class="sxs-lookup"><span data-stu-id="9e38f-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="9e38f-132">Aplique graffiti a uma interface do usuário pública.</span><span class="sxs-lookup"><span data-stu-id="9e38f-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="9e38f-133">Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="9e38f-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="9e38f-134">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="9e38f-134">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="9e38f-135">Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários</span><span class="sxs-lookup"><span data-stu-id="9e38f-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="9e38f-136">Para obter mais informações sobre como implementar medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [validação](#validation) .</span><span class="sxs-lookup"><span data-stu-id="9e38f-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="9e38f-137">Cenários de armazenamento</span><span class="sxs-lookup"><span data-stu-id="9e38f-137">Storage scenarios</span></span>

<span data-ttu-id="9e38f-138">As opções de armazenamento comuns para arquivos incluem:</span><span class="sxs-lookup"><span data-stu-id="9e38f-138">Common storage options for files include:</span></span>

* <span data-ttu-id="9e38f-139">Banco de Dados</span><span class="sxs-lookup"><span data-stu-id="9e38f-139">Database</span></span>

  * <span data-ttu-id="9e38f-140">Para carregamentos de arquivos pequenos, um banco de dados geralmente é mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).</span><span class="sxs-lookup"><span data-stu-id="9e38f-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="9e38f-141">Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, pois a recuperação de um registro de banco de dados para o usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de Avatar).</span><span class="sxs-lookup"><span data-stu-id="9e38f-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="9e38f-142">Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dado.</span><span class="sxs-lookup"><span data-stu-id="9e38f-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="9e38f-143">Armazenamento físico (sistema de arquivos ou compartilhamento de rede)</span><span class="sxs-lookup"><span data-stu-id="9e38f-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="9e38f-144">Para carregamentos de arquivos grandes:</span><span class="sxs-lookup"><span data-stu-id="9e38f-144">For large file uploads:</span></span>
    * <span data-ttu-id="9e38f-145">Os limites de banco de dados podem restringir o tamanho do carregamento.</span><span class="sxs-lookup"><span data-stu-id="9e38f-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="9e38f-146">O armazenamento físico é geralmente menos econômico do que o armazenamento em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="9e38f-147">O armazenamento físico é potencialmente mais barato do que usar um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="9e38f-148">O processo do aplicativo deve ter permissões de leitura e gravação no local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="9e38f-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="9e38f-149">**Nunca conceda a permissão EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="9e38f-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="9e38f-150">Serviço de armazenamento de dados (por exemplo, [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="9e38f-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="9e38f-151">Os serviços geralmente oferecem escalabilidade e resiliência aprimoradas sobre soluções locais que geralmente estão sujeitas a pontos únicos de falha.</span><span class="sxs-lookup"><span data-stu-id="9e38f-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="9e38f-152">Os serviços são um custo potencialmente menor em cenários de infraestrutura de armazenamento grandes.</span><span class="sxs-lookup"><span data-stu-id="9e38f-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="9e38f-153">Para obter mais informações, consulte [início rápido: usar o .net para criar um blob no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="9e38f-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="9e38f-154">O tópico demonstra <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, mas <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> pode ser usado para salvar um <xref:System.IO.FileStream> no armazenamento de BLOBs ao trabalhar com um <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="9e38f-154">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="9e38f-155">Cenários de upload de arquivo</span><span class="sxs-lookup"><span data-stu-id="9e38f-155">File upload scenarios</span></span>

<span data-ttu-id="9e38f-156">Duas abordagens gerais para carregar arquivos são buffer e streaming.</span><span class="sxs-lookup"><span data-stu-id="9e38f-156">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="9e38f-157">**Buffer**</span><span class="sxs-lookup"><span data-stu-id="9e38f-157">**Buffering**</span></span>

<span data-ttu-id="9e38f-158">O arquivo inteiro é lido em um <xref:Microsoft.AspNetCore.Http.IFormFile>, que é uma C# representação do arquivo usado para processar ou salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-158">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="9e38f-159">Os recursos (disco, memória) usados por carregamentos de arquivos dependem do número e do tamanho de carregamentos de arquivos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="9e38f-159">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="9e38f-160">Se um aplicativo tentar armazenar em buffer um número excessivo de carregamentos, o site falhará quando ficar sem memória ou espaço em disco.</span><span class="sxs-lookup"><span data-stu-id="9e38f-160">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="9e38f-161">Se o tamanho ou a frequência dos carregamentos de arquivos estiver esgotando os recursos do aplicativo, use o streaming.</span><span class="sxs-lookup"><span data-stu-id="9e38f-161">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="9e38f-162">Qualquer arquivo em buffer único que exceda 64 KB é movido da memória para um arquivo temporário no disco.</span><span class="sxs-lookup"><span data-stu-id="9e38f-162">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="9e38f-163">O armazenamento em buffer de arquivos pequenos é abordado nas seguintes seções deste tópico:</span><span class="sxs-lookup"><span data-stu-id="9e38f-163">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="9e38f-164">Armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="9e38f-164">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="9e38f-165">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="9e38f-165">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="9e38f-166">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="9e38f-166">**Streaming**</span></span>

<span data-ttu-id="9e38f-167">O arquivo é recebido de uma solicitação de várias partes e processada ou salva diretamente pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-167">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="9e38f-168">O streaming não melhora significativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="9e38f-168">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="9e38f-169">O streaming reduz as demandas de memória ou espaço em disco ao carregar arquivos.</span><span class="sxs-lookup"><span data-stu-id="9e38f-169">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="9e38f-170">O streaming de arquivos grandes é abordado na seção [carregar arquivos grandes com streaming](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="9e38f-170">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="9e38f-171">Carregar arquivos pequenos com associação de modelo em buffer para armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="9e38f-171">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="9e38f-172">Para carregar arquivos pequenos, use um formulário com diversas partes ou Construa uma solicitação POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9e38f-172">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="9e38f-173">O exemplo a seguir demonstra o uso de um Razor Pages formulário para carregar um único arquivo (*pages/BufferedSingleFileUploadPhysical. cshtml* no aplicativo de exemplo):</span><span class="sxs-lookup"><span data-stu-id="9e38f-173">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="9e38f-174">O exemplo a seguir é análogo ao exemplo anterior, exceto pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="9e38f-174">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="9e38f-175">O JavaScript ([buscar API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) é usado para enviar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="9e38f-175">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="9e38f-176">Não há validação.</span><span class="sxs-lookup"><span data-stu-id="9e38f-176">There's no validation.</span></span>

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

<span data-ttu-id="9e38f-177">Para executar a POSTAgem de formulário no JavaScript para clientes que [não dão suporte à API de busca](https://caniuse.com/#feat=fetch), use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="9e38f-177">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="9e38f-178">Use um polifill de busca (por exemplo, [janela. FETCH retroativo (github/FETCH)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="9e38f-178">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="9e38f-179">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-179">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="9e38f-180">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-180">For example:</span></span>

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

<span data-ttu-id="9e38f-181">Para dar suporte a uploads de arquivo, os formulários HTML devem especificar um tipo de codificação (`enctype`) de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-181">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="9e38f-182">Para um elemento de entrada `files` para dar suporte ao carregamento de vários arquivos, forneça o atributo `multiple` no elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-182">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="9e38f-183">Os arquivos individuais carregados no servidor podem ser acessados por meio de [Associação de modelo](xref:mvc/models/model-binding) usando <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="9e38f-183">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="9e38f-184">O aplicativo de exemplo demonstra vários uploads de arquivo em buffer para cenários de armazenamento físico e banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-184">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="9e38f-185">**Não** use a propriedade `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> diferente de para exibição e registro em log.</span><span class="sxs-lookup"><span data-stu-id="9e38f-185">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="9e38f-186">Ao exibir ou registrar em log, o HTML codifica o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-186">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="9e38f-187">Um invasor pode fornecer um nome de arquivo mal-intencionado, incluindo caminhos completos ou caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="9e38f-187">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="9e38f-188">Os aplicativos devem:</span><span class="sxs-lookup"><span data-stu-id="9e38f-188">Applications should:</span></span>
>
> * <span data-ttu-id="9e38f-189">Remova o caminho do nome de arquivo fornecido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="9e38f-189">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="9e38f-190">Salve o nome de arquivo com codificação em HTML e removido por caminho para a interface do usuário ou log.</span><span class="sxs-lookup"><span data-stu-id="9e38f-190">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="9e38f-191">Gere um novo nome de arquivo aleatório para armazenamento.</span><span class="sxs-lookup"><span data-stu-id="9e38f-191">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="9e38f-192">O código a seguir remove o caminho do nome do arquivo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-192">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="9e38f-193">Os exemplos fornecidos até o momento não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="9e38f-193">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="9e38f-194">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e38f-194">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="9e38f-195">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="9e38f-195">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="9e38f-196">Validação</span><span class="sxs-lookup"><span data-stu-id="9e38f-196">Validation</span></span>](#validation)

<span data-ttu-id="9e38f-197">Ao carregar arquivos usando Associação de modelo e <xref:Microsoft.AspNetCore.Http.IFormFile>, o método de ação pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="9e38f-197">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="9e38f-198">Um único <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="9e38f-198">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="9e38f-199">Qualquer uma das seguintes coleções que representam vários arquivos:</span><span class="sxs-lookup"><span data-stu-id="9e38f-199">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="9e38f-200">[Listar](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="9e38f-200">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="9e38f-201">A Associação corresponde aos arquivos de formulário por nome.</span><span class="sxs-lookup"><span data-stu-id="9e38f-201">Binding matches form files by name.</span></span> <span data-ttu-id="9e38f-202">Por exemplo, o valor de `name` HTML em `<input type="file" name="formFile">` deve corresponder C# ao parâmetro/propriedade associado (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="9e38f-202">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="9e38f-203">Para obter mais informações, consulte o [valor do atributo Match Name para o nome do parâmetro da seção do método post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="9e38f-203">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="9e38f-204">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e38f-204">The following example:</span></span>

* <span data-ttu-id="9e38f-205">Executa um loop em um ou mais arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-205">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="9e38f-206">Usa [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-206">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="9e38f-207">Salva os arquivos no sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-207">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="9e38f-208">Retorna o número total e o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-208">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="9e38f-209">Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho.</span><span class="sxs-lookup"><span data-stu-id="9e38f-209">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="9e38f-210">No exemplo a seguir, o caminho é obtido da configuração:</span><span class="sxs-lookup"><span data-stu-id="9e38f-210">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="9e38f-211">O caminho passado para o <xref:System.IO.FileStream> *deve* incluir o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-211">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="9e38f-212">Se o nome do arquivo não for fornecido, um <xref:System.UnauthorizedAccessException> será lançado em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="9e38f-212">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="9e38f-213">Os arquivos carregados usando a técnica de <xref:Microsoft.AspNetCore.Http.IFormFile> são armazenados em buffer na memória ou no disco no servidor antes do processamento.</span><span class="sxs-lookup"><span data-stu-id="9e38f-213">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="9e38f-214">Dentro do método de ação, o conteúdo do <xref:Microsoft.AspNetCore.Http.IFormFile> pode ser acessado como um <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="9e38f-214">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="9e38f-215">Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como o [armazenamento de BLOBs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="9e38f-215">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="9e38f-216">Para outro exemplo que executa um loop sobre vários arquivos para carregar e usa nomes de arquivo seguros, consulte *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-216">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="9e38f-217">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) gera um <xref:System.IO.IOException> se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores.</span><span class="sxs-lookup"><span data-stu-id="9e38f-217">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="9e38f-218">O limite de 65.535 arquivos é um limite por servidor.</span><span class="sxs-lookup"><span data-stu-id="9e38f-218">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="9e38f-219">Para obter mais informações sobre esse limite no sistema operacional Windows, consulte os comentários nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e38f-219">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="9e38f-220">Função GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="9e38f-220">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="9e38f-221">Carregar arquivos pequenos com associação de modelo em buffer para um banco de dados</span><span class="sxs-lookup"><span data-stu-id="9e38f-221">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="9e38f-222">Para armazenar dados de arquivo binário em um banco de dado usando [Entity Framework](/ef/core/index), defina uma propriedade de matriz de <xref:System.Byte> na entidade:</span><span class="sxs-lookup"><span data-stu-id="9e38f-222">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="9e38f-223">Especifique uma propriedade de modelo de página para a classe que inclui um <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="9e38f-223">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="9e38f-224"><xref:Microsoft.AspNetCore.Http.IFormFile> pode ser usada diretamente como um parâmetro de método de ação ou como uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="9e38f-224"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="9e38f-225">O exemplo anterior usa uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="9e38f-225">The prior example uses a bound model property.</span></span>

<span data-ttu-id="9e38f-226">O `FileUpload` é usado no formulário de Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="9e38f-226">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="9e38f-227">Quando o formulário for Postado no servidor, copie o <xref:Microsoft.AspNetCore.Http.IFormFile> para um fluxo e salve-o como uma matriz de bytes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-227">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="9e38f-228">No exemplo a seguir, `_dbContext` armazena o contexto do banco de dados do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-228">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="9e38f-229">O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-229">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="9e38f-230">*Páginas/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="9e38f-230">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="9e38f-231">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="9e38f-231">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="9e38f-232">Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="9e38f-232">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="9e38f-233">Não confie ou confie na propriedade `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação.</span><span class="sxs-lookup"><span data-stu-id="9e38f-233">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="9e38f-234">A propriedade `FileName` só deve ser usada para fins de exibição e somente após a codificação HTML.</span><span class="sxs-lookup"><span data-stu-id="9e38f-234">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="9e38f-235">Os exemplos fornecidos não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="9e38f-235">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="9e38f-236">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e38f-236">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="9e38f-237">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="9e38f-237">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="9e38f-238">Validação</span><span class="sxs-lookup"><span data-stu-id="9e38f-238">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="9e38f-239">Carregar arquivos grandes com streaming</span><span class="sxs-lookup"><span data-stu-id="9e38f-239">Upload large files with streaming</span></span>

<span data-ttu-id="9e38f-240">O exemplo a seguir demonstra como usar o JavaScript para transmitir um arquivo a uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="9e38f-240">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="9e38f-241">O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9e38f-241">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="9e38f-242">Como o método de ação processa diretamente os dados carregados, a associação de modelo de formulário é desabilitada por outro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="9e38f-242">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="9e38f-243">Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="9e38f-243">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="9e38f-244">Depois que as seções com várias partes forem lidas, a ação executará sua própria associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-244">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="9e38f-245">A resposta de página inicial carrega o formulário e salva um token de antifalsificação em um cookie (por meio do atributo `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="9e38f-245">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="9e38f-246">O atributo usa o [suporte de antifalsificação](xref:security/anti-request-forgery) interno do ASP.NET Core para definir um cookie com um token de solicitação:</span><span class="sxs-lookup"><span data-stu-id="9e38f-246">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="9e38f-247">O `DisableFormValueModelBindingAttribute` é usado para desabilitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-247">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="9e38f-248">No aplicativo de exemplo, `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` são aplicados como filtros aos modelos de aplicativo de página de `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` em `Startup.ConfigureServices` usando [convenções de Razor Pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="9e38f-248">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="9e38f-249">Como a associação de modelo não lê o formulário, os parâmetros que estão associados do formulário não são associados (a consulta, a rota e o cabeçalho continuam a funcionar).</span><span class="sxs-lookup"><span data-stu-id="9e38f-249">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="9e38f-250">O método de ação funciona diretamente com a propriedade `Request`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-250">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="9e38f-251">Um `MultipartReader` é usado para ler cada seção.</span><span class="sxs-lookup"><span data-stu-id="9e38f-251">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="9e38f-252">Os dados de chave/valor são armazenados em um `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-252">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="9e38f-253">Depois que as seções com várias partes forem lidas, o conteúdo do `KeyValueAccumulator` será usado para associar os dados do formulário a um tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-253">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="9e38f-254">O método de `StreamingController.UploadDatabase` completo para streaming para um banco de dados com EF Core:</span><span class="sxs-lookup"><span data-stu-id="9e38f-254">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="9e38f-255">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="9e38f-255">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="9e38f-256">O método de `StreamingController.UploadPhysical` completo para streaming para um local físico:</span><span class="sxs-lookup"><span data-stu-id="9e38f-256">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="9e38f-257">No aplicativo de exemplo, as verificações de validação são manipuladas pelo `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-257">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="9e38f-258">Validação</span><span class="sxs-lookup"><span data-stu-id="9e38f-258">Validation</span></span>

<span data-ttu-id="9e38f-259">A classe de `FileHelpers` do aplicativo de exemplo demonstra várias verificações para uploads em buffer <xref:Microsoft.AspNetCore.Http.IFormFile> e arquivos transmitidos.</span><span class="sxs-lookup"><span data-stu-id="9e38f-259">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="9e38f-260">Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivo em buffer no aplicativo de exemplo, consulte o método `ProcessFormFile` no arquivo *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="9e38f-260">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="9e38f-261">Para o processamento de arquivos transmitidos, consulte o método `ProcessStreamedFile` no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-261">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="9e38f-262">Os métodos de processamento de validação demonstrados no aplicativo de exemplo não verificam o conteúdo dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-262">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="9e38f-263">Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para os usuários ou outros sistemas.</span><span class="sxs-lookup"><span data-stu-id="9e38f-263">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="9e38f-264">Embora o tópico exemplo forneça um exemplo de trabalho de técnicas de validação, não implemente a classe `FileHelpers` em um aplicativo de produção, a menos que você:</span><span class="sxs-lookup"><span data-stu-id="9e38f-264">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="9e38f-265">Entenda totalmente a implementação.</span><span class="sxs-lookup"><span data-stu-id="9e38f-265">Fully understand the implementation.</span></span>
> * <span data-ttu-id="9e38f-266">Modifique a implementação conforme apropriado para o ambiente e as especificações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-266">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="9e38f-267">**Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**</span><span class="sxs-lookup"><span data-stu-id="9e38f-267">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="9e38f-268">Validação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="9e38f-268">Content validation</span></span>

<span data-ttu-id="9e38f-269">**Use uma API de verificação de vírus/malware de terceiros no conteúdo carregado.**</span><span class="sxs-lookup"><span data-stu-id="9e38f-269">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="9e38f-270">A verificação de arquivos exige muitos recursos do servidor em cenários de alto volume.</span><span class="sxs-lookup"><span data-stu-id="9e38f-270">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="9e38f-271">Se o desempenho do processamento de solicitações for reduzido devido à verificação de arquivo, considere descarregar o trabalho de verificação para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-271">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="9e38f-272">Normalmente, os arquivos carregados são mantidos em uma área em quarentena até que o verificador de vírus em segundo plano os Verifique.</span><span class="sxs-lookup"><span data-stu-id="9e38f-272">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="9e38f-273">Quando um arquivo passa, o arquivo é movido para o local de armazenamento de arquivo normal.</span><span class="sxs-lookup"><span data-stu-id="9e38f-273">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="9e38f-274">Essas etapas geralmente são executadas em conjunto com um registro de banco de dados que indica o status de verificação de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-274">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="9e38f-275">Ao usar essa abordagem, o aplicativo e o servidor de aplicativos permanecem concentrados em responder às solicitações.</span><span class="sxs-lookup"><span data-stu-id="9e38f-275">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="9e38f-276">Validação de extensão de arquivo</span><span class="sxs-lookup"><span data-stu-id="9e38f-276">File extension validation</span></span>

<span data-ttu-id="9e38f-277">A extensão do arquivo carregado deve ser verificada em uma lista de extensões permitidas.</span><span class="sxs-lookup"><span data-stu-id="9e38f-277">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="9e38f-278">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-278">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="9e38f-279">Validação de assinatura de arquivo</span><span class="sxs-lookup"><span data-stu-id="9e38f-279">File signature validation</span></span>

<span data-ttu-id="9e38f-280">A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-280">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="9e38f-281">Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-281">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="9e38f-282">O aplicativo de exemplo verifica as assinaturas de arquivo para alguns tipos de arquivo comuns.</span><span class="sxs-lookup"><span data-stu-id="9e38f-282">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="9e38f-283">No exemplo a seguir, a assinatura de arquivo para uma imagem JPEG é verificada em relação ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-283">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="9e38f-284">Para obter assinaturas de arquivo adicionais, consulte o [banco de dados assinaturas de arquivo](https://www.filesignatures.net/) e especificações de arquivo oficial.</span><span class="sxs-lookup"><span data-stu-id="9e38f-284">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="9e38f-285">Segurança de nome de arquivo</span><span class="sxs-lookup"><span data-stu-id="9e38f-285">File name security</span></span>

<span data-ttu-id="9e38f-286">Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo no armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="9e38f-286">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="9e38f-287">Crie um nome de arquivo seguro para o arquivo usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.</span><span class="sxs-lookup"><span data-stu-id="9e38f-287">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="9e38f-288">O Razor automaticamente codifica os valores de propriedade para exibição.</span><span class="sxs-lookup"><span data-stu-id="9e38f-288">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="9e38f-289">O código a seguir é seguro para usar:</span><span class="sxs-lookup"><span data-stu-id="9e38f-289">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="9e38f-290">Fora do Razor, sempre <xref:System.Net.WebUtility.HtmlEncode*> conteúdo de nome de arquivo da solicitação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="9e38f-290">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="9e38f-291">Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo será substituído por um arquivo com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="9e38f-291">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="9e38f-292">Forneça lógica adicional para atender às especificações do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-292">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="9e38f-293">Validação de tamanho</span><span class="sxs-lookup"><span data-stu-id="9e38f-293">Size validation</span></span>

<span data-ttu-id="9e38f-294">Limite o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-294">Limit the size of uploaded files.</span></span>

<span data-ttu-id="9e38f-295">No aplicativo de exemplo, o tamanho do arquivo é limitado a 2 MB (indicado em bytes).</span><span class="sxs-lookup"><span data-stu-id="9e38f-295">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="9e38f-296">O limite é fornecido por meio da [configuração](xref:fundamentals/configuration/index) do arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9e38f-296">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="9e38f-297">O `FileSizeLimit` é injetado em classes `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-297">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="9e38f-298">Quando um tamanho de arquivo exceder o limite, o arquivo será rejeitado:</span><span class="sxs-lookup"><span data-stu-id="9e38f-298">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="9e38f-299">Coincidir valor do atributo de nome com o nome do parâmetro do método POST</span><span class="sxs-lookup"><span data-stu-id="9e38f-299">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="9e38f-300">Em formulários não Razor que POSTam dados de formulário ou usam o `FormData` do JavaScript diretamente, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="9e38f-300">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="9e38f-301">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e38f-301">In the following example:</span></span>

* <span data-ttu-id="9e38f-302">Ao usar um elemento `<input>`, o atributo `name` é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-302">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="9e38f-303">Ao usar `FormData` em JavaScript, o nome é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-303">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="9e38f-304">Use um nome correspondente para o parâmetro do C# método (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="9e38f-304">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="9e38f-305">Para um método de manipulador de página Razor Pages chamado `Upload`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-305">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="9e38f-306">Para um método de ação do controlador de POSTAgem MVC:</span><span class="sxs-lookup"><span data-stu-id="9e38f-306">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="9e38f-307">Configuração de servidor e aplicativo</span><span class="sxs-lookup"><span data-stu-id="9e38f-307">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="9e38f-308">Limite de comprimento de corpo com várias partes</span><span class="sxs-lookup"><span data-stu-id="9e38f-308">Multipart body length limit</span></span>

<span data-ttu-id="9e38f-309"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> define o limite para o comprimento de cada corpo com diversas partes.</span><span class="sxs-lookup"><span data-stu-id="9e38f-309"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="9e38f-310">As seções de formulário que excedem esse limite lançam uma <xref:System.IO.InvalidDataException> quando analisadas.</span><span class="sxs-lookup"><span data-stu-id="9e38f-310">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="9e38f-311">O padrão é 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="9e38f-311">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="9e38f-312">Personalize o limite usando a configuração de <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-312">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="9e38f-313"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> é usado para definir o <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="9e38f-313"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="9e38f-314">Em um aplicativo Razor Pages, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-314">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="9e38f-315">Em um aplicativo Razor Pages ou em um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="9e38f-315">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="9e38f-316">Tamanho máximo do corpo de solicitação do Kestrel</span><span class="sxs-lookup"><span data-stu-id="9e38f-316">Kestrel maximum request body size</span></span>

<span data-ttu-id="9e38f-317">Para aplicativos hospedados pelo Kestrel, o tamanho máximo padrão do corpo da solicitação é 30 milhões bytes, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="9e38f-317">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="9e38f-318">Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9e38f-318">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="9e38f-319"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="9e38f-319"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="9e38f-320">Em um aplicativo Razor Pages, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-320">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="9e38f-321">Em um aplicativo de páginas Razor ou um aplicativo MVC, aplique o filtro à classe de manipulador de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="9e38f-321">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="9e38f-322">O `RequestSizeLimitAttribute` também pode ser aplicado usando a diretiva [`@attribute`](xref:mvc/views/razor#attribute) Razor:</span><span class="sxs-lookup"><span data-stu-id="9e38f-322">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="9e38f-323">Outros limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="9e38f-323">Other Kestrel limits</span></span>

<span data-ttu-id="9e38f-324">Outros limites de Kestrel podem ser aplicados para aplicativos hospedados pelo Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9e38f-324">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="9e38f-325">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="9e38f-325">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="9e38f-326">Taxas de dados de solicitação e resposta</span><span class="sxs-lookup"><span data-stu-id="9e38f-326">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="9e38f-327">Limite de comprimento de conteúdo do IIS</span><span class="sxs-lookup"><span data-stu-id="9e38f-327">IIS content length limit</span></span>

<span data-ttu-id="9e38f-328">O limite de solicitação padrão (`maxAllowedContentLength`) é 30 milhões bytes, que é de aproximadamente 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="9e38f-328">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="9e38f-329">Personalize o limite no arquivo *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="9e38f-329">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="9e38f-330">Essa configuração só se aplica ao IIS.</span><span class="sxs-lookup"><span data-stu-id="9e38f-330">This setting only applies to IIS.</span></span> <span data-ttu-id="9e38f-331">Esse comportamento não ocorre por padrão quando a hospedagem é feita no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9e38f-331">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="9e38f-332">Para obter mais informações, consulte [limites de solicitação \<requestLimits >](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="9e38f-332">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="9e38f-333">As limitações no módulo ASP.NET Core ou na presença do módulo filtragem de solicitações do IIS podem limitar os carregamentos a dois ou 4 GB.</span><span class="sxs-lookup"><span data-stu-id="9e38f-333">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="9e38f-334">Para obter mais informações, consulte [não é possível carregar o arquivo com mais de 2GB de tamanho (dotNet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="9e38f-334">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9e38f-335">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="9e38f-335">Troubleshoot</span></span>

<span data-ttu-id="9e38f-336">Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.</span><span class="sxs-lookup"><span data-stu-id="9e38f-336">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="9e38f-337">Erro não encontrado quando implantado em um servidor IIS</span><span class="sxs-lookup"><span data-stu-id="9e38f-337">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="9e38f-338">O erro a seguir indica que o arquivo carregado excede o tamanho do conteúdo configurado do servidor:</span><span class="sxs-lookup"><span data-stu-id="9e38f-338">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="9e38f-339">Para obter mais informações sobre como aumentar o limite, consulte a seção [limite de tamanho de conteúdo do IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="9e38f-339">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="9e38f-340">Falha de conexão</span><span class="sxs-lookup"><span data-stu-id="9e38f-340">Connection failure</span></span>

<span data-ttu-id="9e38f-341">Um erro de conexão e uma conexão de servidor de redefinição provavelmente indicam que o arquivo carregado excede o tamanho máximo do corpo de solicitação do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9e38f-341">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="9e38f-342">Para obter mais informações, consulte a seção [tamanho máximo de corpo de solicitação do Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="9e38f-342">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="9e38f-343">Os limites de conexão de cliente Kestrel também podem exigir ajuste.</span><span class="sxs-lookup"><span data-stu-id="9e38f-343">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="9e38f-344">Exceção de referência nula com IFormFile</span><span class="sxs-lookup"><span data-stu-id="9e38f-344">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="9e38f-345">Se o controlador estiver aceitando arquivos carregados usando <xref:Microsoft.AspNetCore.Http.IFormFile>, mas o valor for `null`, confirme se o formulário HTML está especificando um valor `enctype` de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-345">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="9e38f-346">Se esse atributo não estiver definido no elemento `<form>`, o upload do arquivo não ocorrerá e os argumentos de <xref:Microsoft.AspNetCore.Http.IFormFile> associados serão `null`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-346">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="9e38f-347">Além disso, confirme se a [nomenclatura de carregamento em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="9e38f-347">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="9e38f-348">O fluxo era muito longo</span><span class="sxs-lookup"><span data-stu-id="9e38f-348">Stream was too long</span></span>

<span data-ttu-id="9e38f-349">Os exemplos neste tópico dependem de <xref:System.IO.MemoryStream> para manter o conteúdo do arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="9e38f-349">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="9e38f-350">O limite de tamanho de um `MemoryStream` é `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-350">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="9e38f-351">Se o cenário de upload de arquivo do aplicativo exigir a retenção de conteúdo de arquivo com mais de 50 MB, use uma abordagem alternativa que não dependa de um único `MemoryStream` para manter o conteúdo de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="9e38f-351">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9e38f-352">O ASP.NET Core dá suporte ao carregamento de um ou mais arquivos usando a associação de modelo em buffer para arquivos menores e streaming sem buffer para arquivos maiores.</span><span class="sxs-lookup"><span data-stu-id="9e38f-352">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="9e38f-353">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9e38f-353">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="9e38f-354">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="9e38f-354">Security considerations</span></span>

<span data-ttu-id="9e38f-355">Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor.</span><span class="sxs-lookup"><span data-stu-id="9e38f-355">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="9e38f-356">Os invasores podem tentar:</span><span class="sxs-lookup"><span data-stu-id="9e38f-356">Attackers may attempt to:</span></span>

* <span data-ttu-id="9e38f-357">Executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="9e38f-357">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="9e38f-358">Carregar vírus ou malware.</span><span class="sxs-lookup"><span data-stu-id="9e38f-358">Upload viruses or malware.</span></span>
* <span data-ttu-id="9e38f-359">Comprometa redes e servidores de outras maneiras.</span><span class="sxs-lookup"><span data-stu-id="9e38f-359">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="9e38f-360">As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:</span><span class="sxs-lookup"><span data-stu-id="9e38f-360">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="9e38f-361">Carregue arquivos em uma área de carregamento de arquivo dedicada, preferencialmente para uma unidade que não seja do sistema.</span><span class="sxs-lookup"><span data-stu-id="9e38f-361">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="9e38f-362">Um local dedicado torna mais fácil impor restrições de segurança em arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-362">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="9e38f-363">Desabilite as permissões de execução no local de carregamento do arquivo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9e38f-363">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="9e38f-364">**Não** persiste arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9e38f-364">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="9e38f-365">Use um nome de arquivo seguro determinado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-365">Use a safe file name determined by the app.</span></span> <span data-ttu-id="9e38f-366">Não use um nome de arquivo fornecido pelo usuário ou o nome de arquivo não confiável do arquivo carregado.&dagger; código HTML codifica o nome de arquivo não confiável ao exibi-lo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-366">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="9e38f-367">Por exemplo, registrar em log o nome do arquivo ou exibindo na interface do usuário (o Razor codifica automaticamente a saída).</span><span class="sxs-lookup"><span data-stu-id="9e38f-367">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="9e38f-368">Permitir somente extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9e38f-368">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="9e38f-369">Verifique se as verificações do lado do cliente são executadas no servidor.&dagger; verificações do lado do cliente são fáceis de evitar.</span><span class="sxs-lookup"><span data-stu-id="9e38f-369">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="9e38f-370">Verifique o tamanho de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="9e38f-370">Check the size of an uploaded file.</span></span> <span data-ttu-id="9e38f-371">Defina um limite de tamanho máximo para evitar carregamentos grandes.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9e38f-371">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="9e38f-372">Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-372">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="9e38f-373">**Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**</span><span class="sxs-lookup"><span data-stu-id="9e38f-373">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="9e38f-374">&dagger;aplicativo de exemplo demonstra uma abordagem que atende aos critérios.</span><span class="sxs-lookup"><span data-stu-id="9e38f-374">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="9e38f-375">Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:</span><span class="sxs-lookup"><span data-stu-id="9e38f-375">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="9e38f-376">Assuma completamente o controle de um sistema.</span><span class="sxs-lookup"><span data-stu-id="9e38f-376">Completely gain control of a system.</span></span>
> * <span data-ttu-id="9e38f-377">Sobrecarregar um sistema com o resultado que o sistema falha.</span><span class="sxs-lookup"><span data-stu-id="9e38f-377">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="9e38f-378">Comprometer dados do sistema ou de usuários.</span><span class="sxs-lookup"><span data-stu-id="9e38f-378">Compromise user or system data.</span></span>
> * <span data-ttu-id="9e38f-379">Aplique graffiti a uma interface do usuário pública.</span><span class="sxs-lookup"><span data-stu-id="9e38f-379">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="9e38f-380">Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="9e38f-380">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="9e38f-381">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="9e38f-381">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="9e38f-382">Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários</span><span class="sxs-lookup"><span data-stu-id="9e38f-382">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="9e38f-383">Para obter mais informações sobre como implementar medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [validação](#validation) .</span><span class="sxs-lookup"><span data-stu-id="9e38f-383">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="9e38f-384">Cenários de armazenamento</span><span class="sxs-lookup"><span data-stu-id="9e38f-384">Storage scenarios</span></span>

<span data-ttu-id="9e38f-385">As opções de armazenamento comuns para arquivos incluem:</span><span class="sxs-lookup"><span data-stu-id="9e38f-385">Common storage options for files include:</span></span>

* <span data-ttu-id="9e38f-386">Banco de Dados</span><span class="sxs-lookup"><span data-stu-id="9e38f-386">Database</span></span>

  * <span data-ttu-id="9e38f-387">Para carregamentos de arquivos pequenos, um banco de dados geralmente é mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).</span><span class="sxs-lookup"><span data-stu-id="9e38f-387">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="9e38f-388">Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, pois a recuperação de um registro de banco de dados para o usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de Avatar).</span><span class="sxs-lookup"><span data-stu-id="9e38f-388">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="9e38f-389">Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dado.</span><span class="sxs-lookup"><span data-stu-id="9e38f-389">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="9e38f-390">Armazenamento físico (sistema de arquivos ou compartilhamento de rede)</span><span class="sxs-lookup"><span data-stu-id="9e38f-390">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="9e38f-391">Para carregamentos de arquivos grandes:</span><span class="sxs-lookup"><span data-stu-id="9e38f-391">For large file uploads:</span></span>
    * <span data-ttu-id="9e38f-392">Os limites de banco de dados podem restringir o tamanho do carregamento.</span><span class="sxs-lookup"><span data-stu-id="9e38f-392">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="9e38f-393">O armazenamento físico é geralmente menos econômico do que o armazenamento em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-393">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="9e38f-394">O armazenamento físico é potencialmente mais barato do que usar um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-394">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="9e38f-395">O processo do aplicativo deve ter permissões de leitura e gravação no local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="9e38f-395">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="9e38f-396">**Nunca conceda a permissão EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="9e38f-396">**Never grant execute permission.**</span></span>

* <span data-ttu-id="9e38f-397">Serviço de armazenamento de dados (por exemplo, [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="9e38f-397">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="9e38f-398">Os serviços geralmente oferecem escalabilidade e resiliência aprimoradas sobre soluções locais que geralmente estão sujeitas a pontos únicos de falha.</span><span class="sxs-lookup"><span data-stu-id="9e38f-398">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="9e38f-399">Os serviços são um custo potencialmente menor em cenários de infraestrutura de armazenamento grandes.</span><span class="sxs-lookup"><span data-stu-id="9e38f-399">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="9e38f-400">Para obter mais informações, consulte [início rápido: usar o .net para criar um blob no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="9e38f-400">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="9e38f-401">O tópico demonstra <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, mas <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> pode ser usado para salvar um <xref:System.IO.FileStream> no armazenamento de BLOBs ao trabalhar com um <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="9e38f-401">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="9e38f-402">Cenários de upload de arquivo</span><span class="sxs-lookup"><span data-stu-id="9e38f-402">File upload scenarios</span></span>

<span data-ttu-id="9e38f-403">Duas abordagens gerais para carregar arquivos são buffer e streaming.</span><span class="sxs-lookup"><span data-stu-id="9e38f-403">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="9e38f-404">**Buffer**</span><span class="sxs-lookup"><span data-stu-id="9e38f-404">**Buffering**</span></span>

<span data-ttu-id="9e38f-405">O arquivo inteiro é lido em um <xref:Microsoft.AspNetCore.Http.IFormFile>, que é uma C# representação do arquivo usado para processar ou salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-405">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="9e38f-406">Os recursos (disco, memória) usados por carregamentos de arquivos dependem do número e do tamanho de carregamentos de arquivos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="9e38f-406">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="9e38f-407">Se um aplicativo tentar armazenar em buffer um número excessivo de carregamentos, o site falhará quando ficar sem memória ou espaço em disco.</span><span class="sxs-lookup"><span data-stu-id="9e38f-407">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="9e38f-408">Se o tamanho ou a frequência dos carregamentos de arquivos estiver esgotando os recursos do aplicativo, use o streaming.</span><span class="sxs-lookup"><span data-stu-id="9e38f-408">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="9e38f-409">Qualquer arquivo em buffer único que exceda 64 KB é movido da memória para um arquivo temporário no disco.</span><span class="sxs-lookup"><span data-stu-id="9e38f-409">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="9e38f-410">O armazenamento em buffer de arquivos pequenos é abordado nas seguintes seções deste tópico:</span><span class="sxs-lookup"><span data-stu-id="9e38f-410">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="9e38f-411">Armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="9e38f-411">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="9e38f-412">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="9e38f-412">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="9e38f-413">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="9e38f-413">**Streaming**</span></span>

<span data-ttu-id="9e38f-414">O arquivo é recebido de uma solicitação de várias partes e processada ou salva diretamente pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-414">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="9e38f-415">O streaming não melhora significativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="9e38f-415">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="9e38f-416">O streaming reduz as demandas de memória ou espaço em disco ao carregar arquivos.</span><span class="sxs-lookup"><span data-stu-id="9e38f-416">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="9e38f-417">O streaming de arquivos grandes é abordado na seção [carregar arquivos grandes com streaming](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="9e38f-417">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="9e38f-418">Carregar arquivos pequenos com associação de modelo em buffer para armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="9e38f-418">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="9e38f-419">Para carregar arquivos pequenos, use um formulário com diversas partes ou Construa uma solicitação POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9e38f-419">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="9e38f-420">O exemplo a seguir demonstra o uso de um Razor Pages formulário para carregar um único arquivo (*pages/BufferedSingleFileUploadPhysical. cshtml* no aplicativo de exemplo):</span><span class="sxs-lookup"><span data-stu-id="9e38f-420">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="9e38f-421">O exemplo a seguir é análogo ao exemplo anterior, exceto pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="9e38f-421">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="9e38f-422">O JavaScript ([buscar API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) é usado para enviar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="9e38f-422">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="9e38f-423">Não há validação.</span><span class="sxs-lookup"><span data-stu-id="9e38f-423">There's no validation.</span></span>

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

<span data-ttu-id="9e38f-424">Para executar a POSTAgem de formulário no JavaScript para clientes que [não dão suporte à API de busca](https://caniuse.com/#feat=fetch), use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="9e38f-424">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="9e38f-425">Use um polifill de busca (por exemplo, [janela. FETCH retroativo (github/FETCH)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="9e38f-425">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="9e38f-426">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-426">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="9e38f-427">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-427">For example:</span></span>

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

<span data-ttu-id="9e38f-428">Para dar suporte a uploads de arquivo, os formulários HTML devem especificar um tipo de codificação (`enctype`) de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-428">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="9e38f-429">Para um elemento de entrada `files` para dar suporte ao carregamento de vários arquivos, forneça o atributo `multiple` no elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-429">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="9e38f-430">Os arquivos individuais carregados no servidor podem ser acessados por meio de [Associação de modelo](xref:mvc/models/model-binding) usando <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="9e38f-430">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="9e38f-431">O aplicativo de exemplo demonstra vários uploads de arquivo em buffer para cenários de armazenamento físico e banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-431">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="9e38f-432">**Não** use a propriedade `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> diferente de para exibição e registro em log.</span><span class="sxs-lookup"><span data-stu-id="9e38f-432">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="9e38f-433">Ao exibir ou registrar em log, o HTML codifica o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-433">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="9e38f-434">Um invasor pode fornecer um nome de arquivo mal-intencionado, incluindo caminhos completos ou caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="9e38f-434">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="9e38f-435">Os aplicativos devem:</span><span class="sxs-lookup"><span data-stu-id="9e38f-435">Applications should:</span></span>
>
> * <span data-ttu-id="9e38f-436">Remova o caminho do nome de arquivo fornecido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="9e38f-436">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="9e38f-437">Salve o nome de arquivo com codificação em HTML e removido por caminho para a interface do usuário ou log.</span><span class="sxs-lookup"><span data-stu-id="9e38f-437">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="9e38f-438">Gere um novo nome de arquivo aleatório para armazenamento.</span><span class="sxs-lookup"><span data-stu-id="9e38f-438">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="9e38f-439">O código a seguir remove o caminho do nome do arquivo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-439">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="9e38f-440">Os exemplos fornecidos até o momento não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="9e38f-440">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="9e38f-441">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e38f-441">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="9e38f-442">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="9e38f-442">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="9e38f-443">Validação</span><span class="sxs-lookup"><span data-stu-id="9e38f-443">Validation</span></span>](#validation)

<span data-ttu-id="9e38f-444">Ao carregar arquivos usando Associação de modelo e <xref:Microsoft.AspNetCore.Http.IFormFile>, o método de ação pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="9e38f-444">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="9e38f-445">Um único <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="9e38f-445">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="9e38f-446">Qualquer uma das seguintes coleções que representam vários arquivos:</span><span class="sxs-lookup"><span data-stu-id="9e38f-446">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="9e38f-447">[Listar](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="9e38f-447">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="9e38f-448">A Associação corresponde aos arquivos de formulário por nome.</span><span class="sxs-lookup"><span data-stu-id="9e38f-448">Binding matches form files by name.</span></span> <span data-ttu-id="9e38f-449">Por exemplo, o valor de `name` HTML em `<input type="file" name="formFile">` deve corresponder C# ao parâmetro/propriedade associado (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="9e38f-449">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="9e38f-450">Para obter mais informações, consulte o [valor do atributo Match Name para o nome do parâmetro da seção do método post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="9e38f-450">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="9e38f-451">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e38f-451">The following example:</span></span>

* <span data-ttu-id="9e38f-452">Executa um loop em um ou mais arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-452">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="9e38f-453">Usa [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-453">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="9e38f-454">Salva os arquivos no sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-454">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="9e38f-455">Retorna o número total e o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-455">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="9e38f-456">Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho.</span><span class="sxs-lookup"><span data-stu-id="9e38f-456">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="9e38f-457">No exemplo a seguir, o caminho é obtido da configuração:</span><span class="sxs-lookup"><span data-stu-id="9e38f-457">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="9e38f-458">O caminho passado para o <xref:System.IO.FileStream> *deve* incluir o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-458">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="9e38f-459">Se o nome do arquivo não for fornecido, um <xref:System.UnauthorizedAccessException> será lançado em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="9e38f-459">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="9e38f-460">Os arquivos carregados usando a técnica de <xref:Microsoft.AspNetCore.Http.IFormFile> são armazenados em buffer na memória ou no disco no servidor antes do processamento.</span><span class="sxs-lookup"><span data-stu-id="9e38f-460">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="9e38f-461">Dentro do método de ação, o conteúdo do <xref:Microsoft.AspNetCore.Http.IFormFile> pode ser acessado como um <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="9e38f-461">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="9e38f-462">Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como o [armazenamento de BLOBs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="9e38f-462">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="9e38f-463">Para outro exemplo que executa um loop sobre vários arquivos para carregar e usa nomes de arquivo seguros, consulte *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-463">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="9e38f-464">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) gera um <xref:System.IO.IOException> se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores.</span><span class="sxs-lookup"><span data-stu-id="9e38f-464">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="9e38f-465">O limite de 65.535 arquivos é um limite por servidor.</span><span class="sxs-lookup"><span data-stu-id="9e38f-465">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="9e38f-466">Para obter mais informações sobre esse limite no sistema operacional Windows, consulte os comentários nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e38f-466">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="9e38f-467">Função GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="9e38f-467">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="9e38f-468">Carregar arquivos pequenos com associação de modelo em buffer para um banco de dados</span><span class="sxs-lookup"><span data-stu-id="9e38f-468">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="9e38f-469">Para armazenar dados de arquivo binário em um banco de dado usando [Entity Framework](/ef/core/index), defina uma propriedade de matriz de <xref:System.Byte> na entidade:</span><span class="sxs-lookup"><span data-stu-id="9e38f-469">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="9e38f-470">Especifique uma propriedade de modelo de página para a classe que inclui um <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="9e38f-470">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="9e38f-471"><xref:Microsoft.AspNetCore.Http.IFormFile> pode ser usada diretamente como um parâmetro de método de ação ou como uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="9e38f-471"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="9e38f-472">O exemplo anterior usa uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="9e38f-472">The prior example uses a bound model property.</span></span>

<span data-ttu-id="9e38f-473">O `FileUpload` é usado no formulário de Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="9e38f-473">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="9e38f-474">Quando o formulário for Postado no servidor, copie o <xref:Microsoft.AspNetCore.Http.IFormFile> para um fluxo e salve-o como uma matriz de bytes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-474">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="9e38f-475">No exemplo a seguir, `_dbContext` armazena o contexto do banco de dados do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-475">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="9e38f-476">O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-476">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="9e38f-477">*Páginas/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="9e38f-477">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="9e38f-478">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="9e38f-478">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="9e38f-479">Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="9e38f-479">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="9e38f-480">Não confie ou confie na propriedade `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação.</span><span class="sxs-lookup"><span data-stu-id="9e38f-480">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="9e38f-481">A propriedade `FileName` só deve ser usada para fins de exibição e somente após a codificação HTML.</span><span class="sxs-lookup"><span data-stu-id="9e38f-481">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="9e38f-482">Os exemplos fornecidos não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="9e38f-482">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="9e38f-483">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e38f-483">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="9e38f-484">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="9e38f-484">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="9e38f-485">Validação</span><span class="sxs-lookup"><span data-stu-id="9e38f-485">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="9e38f-486">Carregar arquivos grandes com streaming</span><span class="sxs-lookup"><span data-stu-id="9e38f-486">Upload large files with streaming</span></span>

<span data-ttu-id="9e38f-487">O exemplo a seguir demonstra como usar o JavaScript para transmitir um arquivo a uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="9e38f-487">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="9e38f-488">O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9e38f-488">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="9e38f-489">Como o método de ação processa diretamente os dados carregados, a associação de modelo de formulário é desabilitada por outro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="9e38f-489">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="9e38f-490">Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="9e38f-490">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="9e38f-491">Depois que as seções com várias partes forem lidas, a ação executará sua própria associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-491">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="9e38f-492">A resposta de página inicial carrega o formulário e salva um token de antifalsificação em um cookie (por meio do atributo `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="9e38f-492">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="9e38f-493">O atributo usa o [suporte de antifalsificação](xref:security/anti-request-forgery) interno do ASP.NET Core para definir um cookie com um token de solicitação:</span><span class="sxs-lookup"><span data-stu-id="9e38f-493">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="9e38f-494">O `DisableFormValueModelBindingAttribute` é usado para desabilitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-494">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="9e38f-495">No aplicativo de exemplo, `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` são aplicados como filtros aos modelos de aplicativo de página de `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` em `Startup.ConfigureServices` usando [convenções de Razor Pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="9e38f-495">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="9e38f-496">Como a associação de modelo não lê o formulário, os parâmetros que estão associados do formulário não são associados (a consulta, a rota e o cabeçalho continuam a funcionar).</span><span class="sxs-lookup"><span data-stu-id="9e38f-496">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="9e38f-497">O método de ação funciona diretamente com a propriedade `Request`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-497">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="9e38f-498">Um `MultipartReader` é usado para ler cada seção.</span><span class="sxs-lookup"><span data-stu-id="9e38f-498">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="9e38f-499">Os dados de chave/valor são armazenados em um `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-499">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="9e38f-500">Depois que as seções com várias partes forem lidas, o conteúdo do `KeyValueAccumulator` será usado para associar os dados do formulário a um tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-500">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="9e38f-501">O método de `StreamingController.UploadDatabase` completo para streaming para um banco de dados com EF Core:</span><span class="sxs-lookup"><span data-stu-id="9e38f-501">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="9e38f-502">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="9e38f-502">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="9e38f-503">O método de `StreamingController.UploadPhysical` completo para streaming para um local físico:</span><span class="sxs-lookup"><span data-stu-id="9e38f-503">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="9e38f-504">No aplicativo de exemplo, as verificações de validação são manipuladas pelo `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-504">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="9e38f-505">Validação</span><span class="sxs-lookup"><span data-stu-id="9e38f-505">Validation</span></span>

<span data-ttu-id="9e38f-506">A classe de `FileHelpers` do aplicativo de exemplo demonstra várias verificações para uploads em buffer <xref:Microsoft.AspNetCore.Http.IFormFile> e arquivos transmitidos.</span><span class="sxs-lookup"><span data-stu-id="9e38f-506">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="9e38f-507">Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivo em buffer no aplicativo de exemplo, consulte o método `ProcessFormFile` no arquivo *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="9e38f-507">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="9e38f-508">Para o processamento de arquivos transmitidos, consulte o método `ProcessStreamedFile` no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-508">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="9e38f-509">Os métodos de processamento de validação demonstrados no aplicativo de exemplo não verificam o conteúdo dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-509">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="9e38f-510">Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para os usuários ou outros sistemas.</span><span class="sxs-lookup"><span data-stu-id="9e38f-510">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="9e38f-511">Embora o tópico exemplo forneça um exemplo de trabalho de técnicas de validação, não implemente a classe `FileHelpers` em um aplicativo de produção, a menos que você:</span><span class="sxs-lookup"><span data-stu-id="9e38f-511">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="9e38f-512">Entenda totalmente a implementação.</span><span class="sxs-lookup"><span data-stu-id="9e38f-512">Fully understand the implementation.</span></span>
> * <span data-ttu-id="9e38f-513">Modifique a implementação conforme apropriado para o ambiente e as especificações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-513">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="9e38f-514">**Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**</span><span class="sxs-lookup"><span data-stu-id="9e38f-514">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="9e38f-515">Validação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="9e38f-515">Content validation</span></span>

<span data-ttu-id="9e38f-516">**Use uma API de verificação de vírus/malware de terceiros no conteúdo carregado.**</span><span class="sxs-lookup"><span data-stu-id="9e38f-516">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="9e38f-517">A verificação de arquivos exige muitos recursos do servidor em cenários de alto volume.</span><span class="sxs-lookup"><span data-stu-id="9e38f-517">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="9e38f-518">Se o desempenho do processamento de solicitações for reduzido devido à verificação de arquivo, considere descarregar o trabalho de verificação para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-518">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="9e38f-519">Normalmente, os arquivos carregados são mantidos em uma área em quarentena até que o verificador de vírus em segundo plano os Verifique.</span><span class="sxs-lookup"><span data-stu-id="9e38f-519">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="9e38f-520">Quando um arquivo passa, o arquivo é movido para o local de armazenamento de arquivo normal.</span><span class="sxs-lookup"><span data-stu-id="9e38f-520">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="9e38f-521">Essas etapas geralmente são executadas em conjunto com um registro de banco de dados que indica o status de verificação de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-521">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="9e38f-522">Ao usar essa abordagem, o aplicativo e o servidor de aplicativos permanecem concentrados em responder às solicitações.</span><span class="sxs-lookup"><span data-stu-id="9e38f-522">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="9e38f-523">Validação de extensão de arquivo</span><span class="sxs-lookup"><span data-stu-id="9e38f-523">File extension validation</span></span>

<span data-ttu-id="9e38f-524">A extensão do arquivo carregado deve ser verificada em uma lista de extensões permitidas.</span><span class="sxs-lookup"><span data-stu-id="9e38f-524">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="9e38f-525">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-525">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="9e38f-526">Validação de assinatura de arquivo</span><span class="sxs-lookup"><span data-stu-id="9e38f-526">File signature validation</span></span>

<span data-ttu-id="9e38f-527">A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-527">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="9e38f-528">Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-528">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="9e38f-529">O aplicativo de exemplo verifica as assinaturas de arquivo para alguns tipos de arquivo comuns.</span><span class="sxs-lookup"><span data-stu-id="9e38f-529">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="9e38f-530">No exemplo a seguir, a assinatura de arquivo para uma imagem JPEG é verificada em relação ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="9e38f-530">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="9e38f-531">Para obter assinaturas de arquivo adicionais, consulte o [banco de dados assinaturas de arquivo](https://www.filesignatures.net/) e especificações de arquivo oficial.</span><span class="sxs-lookup"><span data-stu-id="9e38f-531">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="9e38f-532">Segurança de nome de arquivo</span><span class="sxs-lookup"><span data-stu-id="9e38f-532">File name security</span></span>

<span data-ttu-id="9e38f-533">Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo no armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="9e38f-533">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="9e38f-534">Crie um nome de arquivo seguro para o arquivo usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.</span><span class="sxs-lookup"><span data-stu-id="9e38f-534">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="9e38f-535">O Razor automaticamente codifica os valores de propriedade para exibição.</span><span class="sxs-lookup"><span data-stu-id="9e38f-535">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="9e38f-536">O código a seguir é seguro para usar:</span><span class="sxs-lookup"><span data-stu-id="9e38f-536">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="9e38f-537">Fora do Razor, sempre <xref:System.Net.WebUtility.HtmlEncode*> conteúdo de nome de arquivo da solicitação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="9e38f-537">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="9e38f-538">Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo será substituído por um arquivo com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="9e38f-538">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="9e38f-539">Forneça lógica adicional para atender às especificações do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e38f-539">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="9e38f-540">Validação de tamanho</span><span class="sxs-lookup"><span data-stu-id="9e38f-540">Size validation</span></span>

<span data-ttu-id="9e38f-541">Limite o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="9e38f-541">Limit the size of uploaded files.</span></span>

<span data-ttu-id="9e38f-542">No aplicativo de exemplo, o tamanho do arquivo é limitado a 2 MB (indicado em bytes).</span><span class="sxs-lookup"><span data-stu-id="9e38f-542">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="9e38f-543">O limite é fornecido por meio da [configuração](xref:fundamentals/configuration/index) do arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9e38f-543">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="9e38f-544">O `FileSizeLimit` é injetado em classes `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-544">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="9e38f-545">Quando um tamanho de arquivo exceder o limite, o arquivo será rejeitado:</span><span class="sxs-lookup"><span data-stu-id="9e38f-545">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="9e38f-546">Coincidir valor do atributo de nome com o nome do parâmetro do método POST</span><span class="sxs-lookup"><span data-stu-id="9e38f-546">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="9e38f-547">Em formulários não Razor que POSTam dados de formulário ou usam o `FormData` do JavaScript diretamente, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="9e38f-547">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="9e38f-548">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e38f-548">In the following example:</span></span>

* <span data-ttu-id="9e38f-549">Ao usar um elemento `<input>`, o atributo `name` é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-549">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="9e38f-550">Ao usar `FormData` em JavaScript, o nome é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-550">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="9e38f-551">Use um nome correspondente para o parâmetro do C# método (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="9e38f-551">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="9e38f-552">Para um método de manipulador de página Razor Pages chamado `Upload`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-552">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="9e38f-553">Para um método de ação do controlador de POSTAgem MVC:</span><span class="sxs-lookup"><span data-stu-id="9e38f-553">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="9e38f-554">Configuração de servidor e aplicativo</span><span class="sxs-lookup"><span data-stu-id="9e38f-554">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="9e38f-555">Limite de comprimento de corpo com várias partes</span><span class="sxs-lookup"><span data-stu-id="9e38f-555">Multipart body length limit</span></span>

<span data-ttu-id="9e38f-556"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> define o limite para o comprimento de cada corpo com diversas partes.</span><span class="sxs-lookup"><span data-stu-id="9e38f-556"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="9e38f-557">As seções de formulário que excedem esse limite lançam uma <xref:System.IO.InvalidDataException> quando analisadas.</span><span class="sxs-lookup"><span data-stu-id="9e38f-557">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="9e38f-558">O padrão é 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="9e38f-558">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="9e38f-559">Personalize o limite usando a configuração de <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-559">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="9e38f-560"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> é usado para definir o <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="9e38f-560"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="9e38f-561">Em um aplicativo Razor Pages, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-561">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="9e38f-562">Em um aplicativo Razor Pages ou em um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="9e38f-562">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="9e38f-563">Tamanho máximo do corpo de solicitação do Kestrel</span><span class="sxs-lookup"><span data-stu-id="9e38f-563">Kestrel maximum request body size</span></span>

<span data-ttu-id="9e38f-564">Para aplicativos hospedados pelo Kestrel, o tamanho máximo padrão do corpo da solicitação é 30 milhões bytes, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="9e38f-564">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="9e38f-565">Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9e38f-565">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="9e38f-566"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="9e38f-566"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="9e38f-567">Em um aplicativo Razor Pages, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9e38f-567">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="9e38f-568">Em um aplicativo de páginas Razor ou um aplicativo MVC, aplique o filtro à classe de manipulador de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="9e38f-568">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="9e38f-569">Outros limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="9e38f-569">Other Kestrel limits</span></span>

<span data-ttu-id="9e38f-570">Outros limites de Kestrel podem ser aplicados para aplicativos hospedados pelo Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9e38f-570">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="9e38f-571">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="9e38f-571">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="9e38f-572">Taxas de dados de solicitação e resposta</span><span class="sxs-lookup"><span data-stu-id="9e38f-572">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="9e38f-573">Limite de comprimento de conteúdo do IIS</span><span class="sxs-lookup"><span data-stu-id="9e38f-573">IIS content length limit</span></span>

<span data-ttu-id="9e38f-574">O limite de solicitação padrão (`maxAllowedContentLength`) é 30 milhões bytes, que é de aproximadamente 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="9e38f-574">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="9e38f-575">Personalize o limite no arquivo *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="9e38f-575">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="9e38f-576">Essa configuração só se aplica ao IIS.</span><span class="sxs-lookup"><span data-stu-id="9e38f-576">This setting only applies to IIS.</span></span> <span data-ttu-id="9e38f-577">Esse comportamento não ocorre por padrão quando a hospedagem é feita no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9e38f-577">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="9e38f-578">Para obter mais informações, consulte [limites de solicitação \<requestLimits >](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="9e38f-578">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="9e38f-579">As limitações no módulo ASP.NET Core ou na presença do módulo filtragem de solicitações do IIS podem limitar os carregamentos a dois ou 4 GB.</span><span class="sxs-lookup"><span data-stu-id="9e38f-579">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="9e38f-580">Para obter mais informações, consulte [não é possível carregar o arquivo com mais de 2GB de tamanho (dotNet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="9e38f-580">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9e38f-581">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="9e38f-581">Troubleshoot</span></span>

<span data-ttu-id="9e38f-582">Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.</span><span class="sxs-lookup"><span data-stu-id="9e38f-582">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="9e38f-583">Erro não encontrado quando implantado em um servidor IIS</span><span class="sxs-lookup"><span data-stu-id="9e38f-583">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="9e38f-584">O erro a seguir indica que o arquivo carregado excede o tamanho do conteúdo configurado do servidor:</span><span class="sxs-lookup"><span data-stu-id="9e38f-584">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="9e38f-585">Para obter mais informações sobre como aumentar o limite, consulte a seção [limite de tamanho de conteúdo do IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="9e38f-585">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="9e38f-586">Falha de conexão</span><span class="sxs-lookup"><span data-stu-id="9e38f-586">Connection failure</span></span>

<span data-ttu-id="9e38f-587">Um erro de conexão e uma conexão de servidor de redefinição provavelmente indicam que o arquivo carregado excede o tamanho máximo do corpo de solicitação do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9e38f-587">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="9e38f-588">Para obter mais informações, consulte a seção [tamanho máximo de corpo de solicitação do Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="9e38f-588">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="9e38f-589">Os limites de conexão de cliente Kestrel também podem exigir ajuste.</span><span class="sxs-lookup"><span data-stu-id="9e38f-589">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="9e38f-590">Exceção de referência nula com IFormFile</span><span class="sxs-lookup"><span data-stu-id="9e38f-590">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="9e38f-591">Se o controlador estiver aceitando arquivos carregados usando <xref:Microsoft.AspNetCore.Http.IFormFile>, mas o valor for `null`, confirme se o formulário HTML está especificando um valor `enctype` de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-591">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="9e38f-592">Se esse atributo não estiver definido no elemento `<form>`, o upload do arquivo não ocorrerá e os argumentos de <xref:Microsoft.AspNetCore.Http.IFormFile> associados serão `null`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-592">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="9e38f-593">Além disso, confirme se a [nomenclatura de carregamento em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="9e38f-593">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="9e38f-594">O fluxo era muito longo</span><span class="sxs-lookup"><span data-stu-id="9e38f-594">Stream was too long</span></span>

<span data-ttu-id="9e38f-595">Os exemplos neste tópico dependem de <xref:System.IO.MemoryStream> para manter o conteúdo do arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="9e38f-595">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="9e38f-596">O limite de tamanho de um `MemoryStream` é `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="9e38f-596">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="9e38f-597">Se o cenário de upload de arquivo do aplicativo exigir a retenção de conteúdo de arquivo com mais de 50 MB, use uma abordagem alternativa que não dependa de um único `MemoryStream` para manter o conteúdo de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="9e38f-597">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="9e38f-598">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9e38f-598">Additional resources</span></span>

* <span data-ttu-id="9e38f-599">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="9e38f-599">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)</span></span>
* [<span data-ttu-id="9e38f-600">Segurança do Azure: estrutura de segurança: validação de entrada | Mitigações</span><span class="sxs-lookup"><span data-stu-id="9e38f-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="9e38f-601">Padrões de design de nuvem do Azure: padrão valet Key</span><span class="sxs-lookup"><span data-stu-id="9e38f-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)

---
title: Auxiliar de Marca de Imagem no ASP.NET Core
author: pkellner
description: Mostra como trabalhar com o Auxiliar de Marca de Imagem.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: 964072ad276f7e3e411ee41cb03a2efb9d05c585
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663992"
---
# <a name="image-tag-helper-in-aspnet-core"></a><span data-ttu-id="09c98-103">Auxiliar de Marca de Imagem no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="09c98-103">Image Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="09c98-104">Por [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="09c98-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="09c98-105">O Auxiliar de Marca de Imagem aprimora a marca `<img>` para fornecer comportamento de extrapolação de cache para arquivos de imagem estática.</span><span class="sxs-lookup"><span data-stu-id="09c98-105">The Image Tag Helper enhances the `<img>` tag to provide cache-busting behavior for static image files.</span></span>

<span data-ttu-id="09c98-106">Uma cadeia de caracteres de extrapolação de cache é um valor exclusivo que representa o hash do arquivo de imagem estática acrescentado à URL do ativo.</span><span class="sxs-lookup"><span data-stu-id="09c98-106">A cache-busting string is a unique value representing the hash of the static image file appended to the asset's URL.</span></span> <span data-ttu-id="09c98-107">A cadeia de caracteres exclusiva solicita que os clientes (e alguns proxies) recarreguem a imagem do servidor Web do host, e não do cache do cliente.</span><span class="sxs-lookup"><span data-stu-id="09c98-107">The unique string prompts clients (and some proxies) to reload the image from the host web server and not from the client's cache.</span></span>

<span data-ttu-id="09c98-108">Se a origem da imagem (`src`) for um arquivo estático no servidor Web de host:</span><span class="sxs-lookup"><span data-stu-id="09c98-108">If the image source (`src`) is a static file on the host web server:</span></span>

* <span data-ttu-id="09c98-109">Uma cadeia de caracteres exclusiva de extrapolação de cache será anexada como um parâmetro de consulta à origem da imagem.</span><span class="sxs-lookup"><span data-stu-id="09c98-109">A unique cache-busting string is appended as a query parameter to the image source.</span></span>
* <span data-ttu-id="09c98-110">Se o arquivo no servidor Web host for alterado, será gerada uma URL de solicitação exclusiva que inclua o parâmetro de solicitação atualizado.</span><span class="sxs-lookup"><span data-stu-id="09c98-110">If the file on the host web server changes, a unique request URL is generated that includes the updated request parameter.</span></span>

<span data-ttu-id="09c98-111">Para obter uma visão geral dos Auxiliares de Marca, confira <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="09c98-111">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="image-tag-helper-attributes"></a><span data-ttu-id="09c98-112">Atributos de Auxiliar de Marca de Imagem</span><span class="sxs-lookup"><span data-stu-id="09c98-112">Image Tag Helper Attributes</span></span>

### <a name="src"></a><span data-ttu-id="09c98-113">src</span><span class="sxs-lookup"><span data-stu-id="09c98-113">src</span></span>

<span data-ttu-id="09c98-114">Para ativar o Auxiliar de Marca de Imagem, o atributo `src` é obrigatório no elemento `<img>`.</span><span class="sxs-lookup"><span data-stu-id="09c98-114">To activate the Image Tag Helper, the `src` attribute is required on the `<img>` element.</span></span>

<span data-ttu-id="09c98-115">A origem da imagem (`src`) deve apontar para um arquivo estático físico no servidor.</span><span class="sxs-lookup"><span data-stu-id="09c98-115">The image source (`src`) must point to a physical static file on the server.</span></span> <span data-ttu-id="09c98-116">Se `src` for um URI remoto, o parâmetro de cadeia de caracteres de consulta de extrapolação de cache não será gerado.</span><span class="sxs-lookup"><span data-stu-id="09c98-116">If the `src` is a remote URI, the cache-busting query string parameter isn't generated.</span></span>

### <a name="asp-append-version"></a><span data-ttu-id="09c98-117">asp-append-version</span><span class="sxs-lookup"><span data-stu-id="09c98-117">asp-append-version</span></span>

<span data-ttu-id="09c98-118">Quando `asp-append-version` for especificado com um valor `true` junto com um atributo `src`, o Auxiliar de Marca de Imagem será invocado.</span><span class="sxs-lookup"><span data-stu-id="09c98-118">When `asp-append-version` is specified with a `true` value along with a `src` attribute, the Image Tag Helper is invoked.</span></span>

<span data-ttu-id="09c98-119">O exemplo a seguir usa um Auxiliar de Marca de Imagem:</span><span class="sxs-lookup"><span data-stu-id="09c98-119">The following example uses an Image Tag Helper:</span></span>

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

<span data-ttu-id="09c98-120">Se o arquivo estático existe no diretório */wwwroot/images/* , o HTML gerado é semelhante ao seguinte (o hash será diferente):</span><span class="sxs-lookup"><span data-stu-id="09c98-120">If the static file exists in the directory */wwwroot/images/*, the generated HTML is similar to the following (the hash will be different):</span></span>

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

<span data-ttu-id="09c98-121">O valor atribuído ao parâmetro `v` é o valor de hash do arquivo *asplogo.png* em disco.</span><span class="sxs-lookup"><span data-stu-id="09c98-121">The value assigned to the parameter `v` is the hash value of the *asplogo.png* file on disk.</span></span> <span data-ttu-id="09c98-122">Se o servidor Web não conseguir obter acesso de leitura ao arquivo estático, nenhum parâmetro `v` será adicionado ao atributo `src` na marcação renderizada.</span><span class="sxs-lookup"><span data-stu-id="09c98-122">If the web server is unable to obtain read access to the static file, no `v` parameter is added to the `src` attribute in the rendered markup.</span></span>

## <a name="hash-caching-behavior"></a><span data-ttu-id="09c98-123">Comportamento de armazenamento em cache de hash</span><span class="sxs-lookup"><span data-stu-id="09c98-123">Hash caching behavior</span></span>

<span data-ttu-id="09c98-124">O Auxiliar de Marca de Imagem usa o provedor de cache no servidor Web local para armazenar o hash `Sha512` calculado de um determinado arquivo.</span><span class="sxs-lookup"><span data-stu-id="09c98-124">The Image Tag Helper uses the cache provider on the local web server to store the calculated `Sha512` hash of a given file.</span></span> <span data-ttu-id="09c98-125">Se o arquivo for solicitado várias vezes, o hash não será recalculado.</span><span class="sxs-lookup"><span data-stu-id="09c98-125">If the file is requested multiple times, the hash isn't recalculated.</span></span> <span data-ttu-id="09c98-126">O cache é invalidado por um observador de arquivo anexado ao arquivo quando o hash `Sha512` do arquivo é calculado.</span><span class="sxs-lookup"><span data-stu-id="09c98-126">The cache is invalidated by a file watcher that's attached to the file when the file's `Sha512` hash is calculated.</span></span> <span data-ttu-id="09c98-127">Quando o arquivo muda no disco, um novo hash é calculado e armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="09c98-127">When the file changes on disk, a new hash is calculated and cached.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="09c98-128">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="09c98-128">Additional resources</span></span>

* <xref:performance/caching/memory>

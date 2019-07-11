---
title: Compactação de resposta no ASP.NET Core
author: guardrex
description: Saiba mais sobre a compactação de resposta e como usar o Middleware de compactação de resposta em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: performance/response-compression
ms.openlocfilehash: d5d2da3dc0a8a452de97d98161d429389d2f7638
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815612"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="289aa-103">Compactação de resposta no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="289aa-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="289aa-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="289aa-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="289aa-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="289aa-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="289aa-106">Largura de banda de rede é um recurso limitado.</span><span class="sxs-lookup"><span data-stu-id="289aa-106">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="289aa-107">Reduzindo o tamanho da resposta geralmente aumenta a capacidade de resposta de um aplicativo, muitas vezes drasticamente.</span><span class="sxs-lookup"><span data-stu-id="289aa-107">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="289aa-108">É uma maneira de reduzir os tamanhos do conteúdo compactar respostas do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="289aa-108">One way to reduce payload sizes is to compress an app's responses.</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="289aa-109">Quando usar o Middleware de compactação de resposta</span><span class="sxs-lookup"><span data-stu-id="289aa-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="289aa-110">Use as tecnologias de compactação de resposta com base em servidor no IIS, Apache ou Nginx.</span><span class="sxs-lookup"><span data-stu-id="289aa-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="289aa-111">O desempenho do middleware provavelmente não corresponderá dos módulos de servidor.</span><span class="sxs-lookup"><span data-stu-id="289aa-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="289aa-112">[Servidor HTTP. sys](xref:fundamentals/servers/httpsys) servidor e [Kestrel](xref:fundamentals/servers/kestrel) server atualmente não oferecem suporte à compactação interna.</span><span class="sxs-lookup"><span data-stu-id="289aa-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="289aa-113">Use o Middleware de compactação de resposta quando você estiver:</span><span class="sxs-lookup"><span data-stu-id="289aa-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="289aa-114">Não é possível usar as seguintes tecnologias de compactação baseada em servidor:</span><span class="sxs-lookup"><span data-stu-id="289aa-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="289aa-115">Módulo de compactação dinâmica do IIS</span><span class="sxs-lookup"><span data-stu-id="289aa-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="289aa-116">Módulo do Apache mod_deflate</span><span class="sxs-lookup"><span data-stu-id="289aa-116">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="289aa-117">Nginx compactação e descompactação</span><span class="sxs-lookup"><span data-stu-id="289aa-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="289aa-118">Hospedagem diretamente em:</span><span class="sxs-lookup"><span data-stu-id="289aa-118">Hosting directly on:</span></span>
  * <span data-ttu-id="289aa-119">[Servidor HTTP. sys](xref:fundamentals/servers/httpsys) (anteriormente chamado WebListener)</span><span class="sxs-lookup"><span data-stu-id="289aa-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="289aa-120">Servidor kestrel</span><span class="sxs-lookup"><span data-stu-id="289aa-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="289aa-121">Compactação de resposta</span><span class="sxs-lookup"><span data-stu-id="289aa-121">Response compression</span></span>

<span data-ttu-id="289aa-122">Geralmente, nenhuma resposta compactada nativamente não pode se beneficiar da compactação de resposta.</span><span class="sxs-lookup"><span data-stu-id="289aa-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="289aa-123">As respostas não nativamente compactadas normalmente incluem: CSS, JavaScript, HTML, XML e JSON.</span><span class="sxs-lookup"><span data-stu-id="289aa-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="289aa-124">Você não deve compactar os ativos nativamente compactados, como arquivos PNG.</span><span class="sxs-lookup"><span data-stu-id="289aa-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="289aa-125">Se você tentar compactar ainda mais uma resposta compactada nativamente, qualquer redução adicional pequena em tempo de tamanho e a transmissão será provavelmente ser superada pelo tempo levado para processar a compactação.</span><span class="sxs-lookup"><span data-stu-id="289aa-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="289aa-126">Não compacte arquivos menores do que cerca de 150 e 1000 bytes (dependendo do conteúdo do arquivo e a eficiência da compactação).</span><span class="sxs-lookup"><span data-stu-id="289aa-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="289aa-127">A sobrecarga de compactação de arquivos pequenos pode produzir um arquivo compactado maior do que o arquivo descompactado.</span><span class="sxs-lookup"><span data-stu-id="289aa-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="289aa-128">Quando um cliente pode processar o conteúdo compactado, o cliente deve informar o servidor de seus recursos, enviando o `Accept-Encoding` cabeçalho com a solicitação.</span><span class="sxs-lookup"><span data-stu-id="289aa-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="289aa-129">Quando um servidor envia o conteúdo compactado, ele deve incluir informações no `Content-Encoding` cabeçalho em como a resposta compactada é codificada.</span><span class="sxs-lookup"><span data-stu-id="289aa-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="289aa-130">Conteúdo designações de codifica com suporte pelo middleware são mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="289aa-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="289aa-131">`Accept-Encoding` valores de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="289aa-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="289aa-132">Middleware com suporte</span><span class="sxs-lookup"><span data-stu-id="289aa-132">Middleware Supported</span></span> | <span data-ttu-id="289aa-133">Descrição</span><span class="sxs-lookup"><span data-stu-id="289aa-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="289aa-134">Sim (padrão)</span><span class="sxs-lookup"><span data-stu-id="289aa-134">Yes (default)</span></span>        | [<span data-ttu-id="289aa-135">Formato de dados compactados Brotli</span><span class="sxs-lookup"><span data-stu-id="289aa-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="289aa-136">Não</span><span class="sxs-lookup"><span data-stu-id="289aa-136">No</span></span>                   | [<span data-ttu-id="289aa-137">Formato de dados compactados DEFLATE</span><span class="sxs-lookup"><span data-stu-id="289aa-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="289aa-138">Não</span><span class="sxs-lookup"><span data-stu-id="289aa-138">No</span></span>                   | [<span data-ttu-id="289aa-139">Intercâmbio de eficiente de XML do W3C</span><span class="sxs-lookup"><span data-stu-id="289aa-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="289aa-140">Sim</span><span class="sxs-lookup"><span data-stu-id="289aa-140">Yes</span></span>                  | [<span data-ttu-id="289aa-141">Formato de arquivo gzip</span><span class="sxs-lookup"><span data-stu-id="289aa-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="289aa-142">Sim</span><span class="sxs-lookup"><span data-stu-id="289aa-142">Yes</span></span>                  | <span data-ttu-id="289aa-143">Identificador "Nenhuma codificação": A resposta não deve ser codificada.</span><span class="sxs-lookup"><span data-stu-id="289aa-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="289aa-144">Não</span><span class="sxs-lookup"><span data-stu-id="289aa-144">No</span></span>                   | [<span data-ttu-id="289aa-145">Formato de transferência de rede para arquivos mortos de Java</span><span class="sxs-lookup"><span data-stu-id="289aa-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="289aa-146">Sim</span><span class="sxs-lookup"><span data-stu-id="289aa-146">Yes</span></span>                  | <span data-ttu-id="289aa-147">Qualquer conteúdo disponível não codificação explicitamente solicitada</span><span class="sxs-lookup"><span data-stu-id="289aa-147">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="289aa-148">`Accept-Encoding` valores de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="289aa-148">`Accept-Encoding` header values</span></span> | <span data-ttu-id="289aa-149">Middleware com suporte</span><span class="sxs-lookup"><span data-stu-id="289aa-149">Middleware Supported</span></span> | <span data-ttu-id="289aa-150">Descrição</span><span class="sxs-lookup"><span data-stu-id="289aa-150">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="289aa-151">Não</span><span class="sxs-lookup"><span data-stu-id="289aa-151">No</span></span>                   | [<span data-ttu-id="289aa-152">Formato de dados compactados Brotli</span><span class="sxs-lookup"><span data-stu-id="289aa-152">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="289aa-153">Não</span><span class="sxs-lookup"><span data-stu-id="289aa-153">No</span></span>                   | [<span data-ttu-id="289aa-154">Formato de dados compactados DEFLATE</span><span class="sxs-lookup"><span data-stu-id="289aa-154">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="289aa-155">Não</span><span class="sxs-lookup"><span data-stu-id="289aa-155">No</span></span>                   | [<span data-ttu-id="289aa-156">Intercâmbio de eficiente de XML do W3C</span><span class="sxs-lookup"><span data-stu-id="289aa-156">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="289aa-157">Sim (padrão)</span><span class="sxs-lookup"><span data-stu-id="289aa-157">Yes (default)</span></span>        | [<span data-ttu-id="289aa-158">Formato de arquivo gzip</span><span class="sxs-lookup"><span data-stu-id="289aa-158">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="289aa-159">Sim</span><span class="sxs-lookup"><span data-stu-id="289aa-159">Yes</span></span>                  | <span data-ttu-id="289aa-160">Identificador "Nenhuma codificação": A resposta não deve ser codificada.</span><span class="sxs-lookup"><span data-stu-id="289aa-160">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="289aa-161">Não</span><span class="sxs-lookup"><span data-stu-id="289aa-161">No</span></span>                   | [<span data-ttu-id="289aa-162">Formato de transferência de rede para arquivos mortos de Java</span><span class="sxs-lookup"><span data-stu-id="289aa-162">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="289aa-163">Sim</span><span class="sxs-lookup"><span data-stu-id="289aa-163">Yes</span></span>                  | <span data-ttu-id="289aa-164">Qualquer conteúdo disponível não codificação explicitamente solicitada</span><span class="sxs-lookup"><span data-stu-id="289aa-164">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

<span data-ttu-id="289aa-165">Para obter mais informações, consulte o [IANA lista oficial de conteúdo de codificação](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span><span class="sxs-lookup"><span data-stu-id="289aa-165">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="289aa-166">O middleware permite que você adicione provedores de compactação adicional para custom `Accept-Encoding` valores de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="289aa-166">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="289aa-167">Para obter mais informações, consulte [provedores personalizados](#custom-providers) abaixo.</span><span class="sxs-lookup"><span data-stu-id="289aa-167">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="289aa-168">O middleware é capaz de reagir a valor de qualidade (qvalue, `q`) quando enviado pelo cliente para priorizar os esquemas de compactação de ponderação.</span><span class="sxs-lookup"><span data-stu-id="289aa-168">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="289aa-169">Para obter mais informações, consulte [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span><span class="sxs-lookup"><span data-stu-id="289aa-169">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="289aa-170">Algoritmos de compactação estão sujeitos a uma compensação entre a velocidade de compactação e a eficiência da compactação.</span><span class="sxs-lookup"><span data-stu-id="289aa-170">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="289aa-171">*Eficácia* neste contexto refere-se ao tamanho da saída após a compactação.</span><span class="sxs-lookup"><span data-stu-id="289aa-171">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="289aa-172">O menor tamanho é obtido com a maioria *ideal* compactação.</span><span class="sxs-lookup"><span data-stu-id="289aa-172">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="289aa-173">Cabeçalhos envolvidas na solicitação, enviar, armazenamento em cache e receber conteúdo compactado são descritas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="289aa-173">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="289aa-174">Cabeçalho</span><span class="sxs-lookup"><span data-stu-id="289aa-174">Header</span></span>             | <span data-ttu-id="289aa-175">Função</span><span class="sxs-lookup"><span data-stu-id="289aa-175">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="289aa-176">Enviada do cliente para o servidor para indicar a codificação esquemas aceitáveis para o cliente de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="289aa-176">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="289aa-177">Enviados do servidor para o cliente para indicar a codificação do conteúdo na carga.</span><span class="sxs-lookup"><span data-stu-id="289aa-177">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="289aa-178">Quando ocorre a compressão, a `Content-Length` cabeçalho for removido, desde que as alterações de conteúdo do corpo quando a resposta é compactada.</span><span class="sxs-lookup"><span data-stu-id="289aa-178">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="289aa-179">Quando ocorre a compressão, a `Content-MD5` cabeçalho for removido, uma vez que o conteúdo do corpo foi alterado e o hash não é mais válido.</span><span class="sxs-lookup"><span data-stu-id="289aa-179">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="289aa-180">Especifica o tipo MIME do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="289aa-180">Specifies the MIME type of the content.</span></span> <span data-ttu-id="289aa-181">Cada resposta deve especificar seu `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="289aa-181">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="289aa-182">O middleware verifica esse valor para determinar se a resposta deve ser compactada.</span><span class="sxs-lookup"><span data-stu-id="289aa-182">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="289aa-183">O middleware Especifica um conjunto de [padrão de tipos MIME](#mime-types) que ele pode codificar, mas você pode substituir ou adicionar tipos MIME.</span><span class="sxs-lookup"><span data-stu-id="289aa-183">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="289aa-184">Quando enviadas pelo servidor com um valor de `Accept-Encoding` para clientes e proxies, o `Vary` cabeçalho indica para o cliente ou um proxy que ele deve armazenar em cache (variar) respostas com base no valor da `Accept-Encoding` cabeçalho da solicitação.</span><span class="sxs-lookup"><span data-stu-id="289aa-184">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="289aa-185">O resultado de retorno de conteúdo com o `Vary: Accept-Encoding` cabeçalho é que ambos compactados e descompactadas respostas são armazenadas em cache separadamente.</span><span class="sxs-lookup"><span data-stu-id="289aa-185">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="289aa-186">Explore os recursos do Middleware de compactação de resposta com o [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span><span class="sxs-lookup"><span data-stu-id="289aa-186">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="289aa-187">O exemplo ilustra:</span><span class="sxs-lookup"><span data-stu-id="289aa-187">The sample illustrates:</span></span>

* <span data-ttu-id="289aa-188">A compactação de respostas do aplicativo usando o Gzip e provedores de compactação personalizado.</span><span class="sxs-lookup"><span data-stu-id="289aa-188">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="289aa-189">Como adicionar um tipo de MIME para a lista padrão de tipos MIME para compactação.</span><span class="sxs-lookup"><span data-stu-id="289aa-189">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="289aa-190">Pacote</span><span class="sxs-lookup"><span data-stu-id="289aa-190">Package</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="289aa-191">Para incluir o middleware em um projeto, adicione uma referência para o [metapacote do Microsoft](xref:fundamentals/metapackage-app), que inclui o [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) pacote.</span><span class="sxs-lookup"><span data-stu-id="289aa-191">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="289aa-192">Para incluir o middleware em um projeto, adicione uma referência para o [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage), que inclui o [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) pacote.</span><span class="sxs-lookup"><span data-stu-id="289aa-192">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="289aa-193">Para incluir o middleware em um projeto, adicione uma referência para o [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) pacote.</span><span class="sxs-lookup"><span data-stu-id="289aa-193">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="289aa-194">Configuração</span><span class="sxs-lookup"><span data-stu-id="289aa-194">Configuration</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="289aa-195">O código a seguir mostra como habilitar o Middleware de compactação de resposta para provedores de compactação e tipos MIME padrão ([Brotli](#brotli-compression-provider) e [Gzip](#gzip-compression-provider)):</span><span class="sxs-lookup"><span data-stu-id="289aa-195">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="289aa-196">O código a seguir mostra como habilitar o Middleware de compactação de resposta para tipos MIME padrão e o [provedor de compactação Gzip](#gzip-compression-provider):</span><span class="sxs-lookup"><span data-stu-id="289aa-196">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

::: moniker-end

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

<span data-ttu-id="289aa-197">Notas:</span><span class="sxs-lookup"><span data-stu-id="289aa-197">Notes:</span></span>

* <span data-ttu-id="289aa-198">`app.UseResponseCompression` deve ser chamado antes de `app.UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="289aa-198">`app.UseResponseCompression` must be called before `app.UseMvc`.</span></span>
* <span data-ttu-id="289aa-199">Usar uma ferramenta como [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), ou [Postman](https://www.getpostman.com/) para definir o `Accept-Encoding` cabeçalho de solicitação e estudar os cabeçalhos de resposta, o tamanho e o corpo.</span><span class="sxs-lookup"><span data-stu-id="289aa-199">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="289aa-200">Enviar uma solicitação para o aplicativo de exemplo sem o `Accept-Encoding` cabeçalho e observe que a resposta é descompactada.</span><span class="sxs-lookup"><span data-stu-id="289aa-200">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="289aa-201">O `Content-Encoding` e `Vary` cabeçalhos não estiverem presentes na resposta.</span><span class="sxs-lookup"><span data-stu-id="289aa-201">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Janela do Fiddler mostrando o resultado de uma solicitação sem o cabeçalho Accept-Encoding.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="289aa-204">Enviar uma solicitação para o aplicativo de exemplo com o `Accept-Encoding: br` cabeçalho (a compactação Brotli) e observe que a resposta é compactada.</span><span class="sxs-lookup"><span data-stu-id="289aa-204">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="289aa-205">O `Content-Encoding` e `Vary` cabeçalhos estão presentes na resposta.</span><span class="sxs-lookup"><span data-stu-id="289aa-205">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Janela do Fiddler mostrando o resultado de uma solicitação com o cabeçalho Accept-Encoding e um valor de br.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="289aa-209">Enviar uma solicitação para o aplicativo de exemplo com o `Accept-Encoding: gzip` cabeçalho e observe que a resposta é compactada.</span><span class="sxs-lookup"><span data-stu-id="289aa-209">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="289aa-210">O `Content-Encoding` e `Vary` cabeçalhos estão presentes na resposta.</span><span class="sxs-lookup"><span data-stu-id="289aa-210">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Janela do Fiddler mostrando o resultado de uma solicitação com o cabeçalho Accept-Encoding e um valor de gzip.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a><span data-ttu-id="289aa-214">Provedores</span><span class="sxs-lookup"><span data-stu-id="289aa-214">Providers</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a><span data-ttu-id="289aa-215">Provedor de compactação Brotli</span><span class="sxs-lookup"><span data-stu-id="289aa-215">Brotli Compression Provider</span></span>

<span data-ttu-id="289aa-216">Use o <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> para compactar respostas com o [formato de dados compactados Brotli](https://tools.ietf.org/html/rfc7932).</span><span class="sxs-lookup"><span data-stu-id="289aa-216">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="289aa-217">Se nenhum provedor de compactação é adicionados explicitamente a <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="289aa-217">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="289aa-218">O provedor de compactação Brotli é adicionado por padrão para a matriz de provedores de compactação juntamente com o [provedor de compactação Gzip](#gzip-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="289aa-218">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="289aa-219">Padrões de compactação para a compactação Brotli quando o formato de dados compactados Brotli é suportado pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="289aa-219">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="289aa-220">Se Brotli não é suportada pelo cliente, a compactação padrão Gzip quando o cliente oferece suporte à compactação Gzip.</span><span class="sxs-lookup"><span data-stu-id="289aa-220">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="289aa-221">O provedor de compactação Brotoli devem ser adicionado ao quaisquer provedores de compactação são adicionados explicitamente:</span><span class="sxs-lookup"><span data-stu-id="289aa-221">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="289aa-222">Definir a compactação de nível com <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span><span class="sxs-lookup"><span data-stu-id="289aa-222">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="289aa-223">O provedor de compactação Brotli assume como padrão o nível de compactação mais rápido ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), que não pode produzir a compactação mais eficiente.</span><span class="sxs-lookup"><span data-stu-id="289aa-223">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="289aa-224">Se a compactação mais eficiente é desejada, configure o middleware de compactação ideal.</span><span class="sxs-lookup"><span data-stu-id="289aa-224">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="289aa-225">Nível de compactação</span><span class="sxs-lookup"><span data-stu-id="289aa-225">Compression Level</span></span> | <span data-ttu-id="289aa-226">Descrição</span><span class="sxs-lookup"><span data-stu-id="289aa-226">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="289aa-227">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="289aa-227">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="289aa-228">A compactação deve ser concluída assim que possível, mesmo se a saída resultante não é compactada da maneira ideal.</span><span class="sxs-lookup"><span data-stu-id="289aa-228">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="289aa-229">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="289aa-229">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="289aa-230">Nenhuma compactação deve ser executada.</span><span class="sxs-lookup"><span data-stu-id="289aa-230">No compression should be performed.</span></span> |
| [<span data-ttu-id="289aa-231">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="289aa-231">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="289aa-232">As respostas devem ser compactadas ideal, mesmo se a compactação leva mais tempo para concluir.</span><span class="sxs-lookup"><span data-stu-id="289aa-232">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

::: moniker-end

### <a name="gzip-compression-provider"></a><span data-ttu-id="289aa-233">Provedor de compactação gzip</span><span class="sxs-lookup"><span data-stu-id="289aa-233">Gzip Compression Provider</span></span>

<span data-ttu-id="289aa-234">Use o <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> para compactar respostas com o [formato de arquivo Gzip](https://tools.ietf.org/html/rfc1952).</span><span class="sxs-lookup"><span data-stu-id="289aa-234">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="289aa-235">Se nenhum provedor de compactação é adicionados explicitamente a <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="289aa-235">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="289aa-236">O provedor de compactação Gzip é adicionado por padrão para a matriz de provedores de compactação juntamente com o [provedor de compactação Brotli](#brotli-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="289aa-236">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="289aa-237">Padrões de compactação para a compactação Brotli quando o formato de dados compactados Brotli é suportado pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="289aa-237">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="289aa-238">Se Brotli não é suportada pelo cliente, a compactação padrão Gzip quando o cliente oferece suporte à compactação Gzip.</span><span class="sxs-lookup"><span data-stu-id="289aa-238">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="289aa-239">O provedor de compactação Gzip é adicionado por padrão para a matriz de provedores de compactação.</span><span class="sxs-lookup"><span data-stu-id="289aa-239">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="289aa-240">Padrões de compactação como Gzip, quando o cliente oferece suporte à compactação Gzip.</span><span class="sxs-lookup"><span data-stu-id="289aa-240">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="289aa-241">O provedor de compactação Gzip devem ser adicionado ao quaisquer provedores de compactação são adicionados explicitamente:</span><span class="sxs-lookup"><span data-stu-id="289aa-241">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5)]

::: moniker-end

<span data-ttu-id="289aa-242">Definir a compactação de nível com <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span><span class="sxs-lookup"><span data-stu-id="289aa-242">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="289aa-243">O provedor de compactação Gzip assume como padrão o nível de compactação mais rápido ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), que não pode produzir a compactação mais eficiente.</span><span class="sxs-lookup"><span data-stu-id="289aa-243">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="289aa-244">Se a compactação mais eficiente é desejada, configure o middleware de compactação ideal.</span><span class="sxs-lookup"><span data-stu-id="289aa-244">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="289aa-245">Nível de compactação</span><span class="sxs-lookup"><span data-stu-id="289aa-245">Compression Level</span></span> | <span data-ttu-id="289aa-246">Descrição</span><span class="sxs-lookup"><span data-stu-id="289aa-246">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="289aa-247">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="289aa-247">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="289aa-248">A compactação deve ser concluída assim que possível, mesmo se a saída resultante não é compactada da maneira ideal.</span><span class="sxs-lookup"><span data-stu-id="289aa-248">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="289aa-249">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="289aa-249">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="289aa-250">Nenhuma compactação deve ser executada.</span><span class="sxs-lookup"><span data-stu-id="289aa-250">No compression should be performed.</span></span> |
| [<span data-ttu-id="289aa-251">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="289aa-251">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="289aa-252">As respostas devem ser compactadas ideal, mesmo se a compactação leva mais tempo para concluir.</span><span class="sxs-lookup"><span data-stu-id="289aa-252">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a><span data-ttu-id="289aa-253">Provedores personalizados</span><span class="sxs-lookup"><span data-stu-id="289aa-253">Custom providers</span></span>

<span data-ttu-id="289aa-254">Criar implementações de compactação personalizado com <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span><span class="sxs-lookup"><span data-stu-id="289aa-254">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="289aa-255">O <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> representa o conteúdo de codificação que este `ICompressionProvider` produz.</span><span class="sxs-lookup"><span data-stu-id="289aa-255">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="289aa-256">O middleware usa essas informações para escolher o provedor de acordo com a lista especificada no `Accept-Encoding` cabeçalho da solicitação.</span><span class="sxs-lookup"><span data-stu-id="289aa-256">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="289aa-257">Usando o aplicativo de exemplo, o cliente envia uma solicitação com o `Accept-Encoding: mycustomcompression` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="289aa-257">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="289aa-258">O middleware usa a implementação de compactação personalizado e retorna a resposta com um `Content-Encoding: mycustomcompression` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="289aa-258">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="289aa-259">O cliente deve ser capaz de descompactar a codificação personalizada para que uma implementação de compactação personalizado trabalhar.</span><span class="sxs-lookup"><span data-stu-id="289aa-259">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=6)]

[!code-csharp[](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="289aa-260">Enviar uma solicitação para o aplicativo de exemplo com o `Accept-Encoding: mycustomcompression` cabeçalho e observar os cabeçalhos de resposta.</span><span class="sxs-lookup"><span data-stu-id="289aa-260">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="289aa-261">O `Vary` e `Content-Encoding` cabeçalhos estão presentes na resposta.</span><span class="sxs-lookup"><span data-stu-id="289aa-261">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="289aa-262">O corpo de resposta (não mostrado) não será compactado pelo exemplo.</span><span class="sxs-lookup"><span data-stu-id="289aa-262">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="289aa-263">Não existe uma implementação da compactação no `CustomCompressionProvider` classe do exemplo.</span><span class="sxs-lookup"><span data-stu-id="289aa-263">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="289aa-264">No entanto, o exemplo mostra onde você poderia implementar tal um algoritmo de compactação.</span><span class="sxs-lookup"><span data-stu-id="289aa-264">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Janela do Fiddler mostrando o resultado de uma solicitação com o cabeçalho Accept-Encoding e um valor de mycustomcompression.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="289aa-267">tipos MIME</span><span class="sxs-lookup"><span data-stu-id="289aa-267">MIME types</span></span>

<span data-ttu-id="289aa-268">O middleware Especifica um conjunto de tipos MIME para a compactação padrão:</span><span class="sxs-lookup"><span data-stu-id="289aa-268">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="289aa-269">Substituir ou acrescentar os tipos MIME com as opções de Middleware de compactação de resposta.</span><span class="sxs-lookup"><span data-stu-id="289aa-269">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="289aa-270">Observe que curinga MIME tipos, como `text/*` não são suportados.</span><span class="sxs-lookup"><span data-stu-id="289aa-270">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="289aa-271">O aplicativo de exemplo adiciona um tipo MIME `image/svg+xml` e compacta e serve o ASP.NET Core a imagem da faixa (*banner.svg*).</span><span class="sxs-lookup"><span data-stu-id="289aa-271">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7-9)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="289aa-272">Compactação com protocolo seguro</span><span class="sxs-lookup"><span data-stu-id="289aa-272">Compression with secure protocol</span></span>

<span data-ttu-id="289aa-273">Respostas compactadas através de conexões seguras podem ser controladas com o `EnableForHttps` opção, que é desabilitada por padrão.</span><span class="sxs-lookup"><span data-stu-id="289aa-273">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="289aa-274">Usar a compactação com páginas geradas dinamicamente pode levar a problemas de segurança, como o [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) e [violação](https://wikipedia.org/wiki/BREACH_(security_exploit)) ataques.</span><span class="sxs-lookup"><span data-stu-id="289aa-274">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="289aa-275">Adicionando o cabeçalho Vary</span><span class="sxs-lookup"><span data-stu-id="289aa-275">Adding the Vary header</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="289aa-276">Quando a compactação de respostas com base no `Accept-Encoding` cabeçalho, há potencialmente várias versões compactadas de resposta e uma versão não compactada.</span><span class="sxs-lookup"><span data-stu-id="289aa-276">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="289aa-277">Para instruir os caches de cliente e o proxy que várias versões existirem e devem ser armazenadas, o `Vary` cabeçalho é adicionado com um `Accept-Encoding` valor.</span><span class="sxs-lookup"><span data-stu-id="289aa-277">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="289aa-278">No ASP.NET Core 2.0 ou posterior, o middleware adiciona o `Vary` cabeçalho automaticamente quando a resposta é compactada.</span><span class="sxs-lookup"><span data-stu-id="289aa-278">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="289aa-279">Quando a compactação de respostas com base no `Accept-Encoding` cabeçalho, há potencialmente várias versões compactadas de resposta e uma versão não compactada.</span><span class="sxs-lookup"><span data-stu-id="289aa-279">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="289aa-280">Para instruir os caches de cliente e o proxy que várias versões existirem e devem ser armazenadas, o `Vary` cabeçalho é adicionado com um `Accept-Encoding` valor.</span><span class="sxs-lookup"><span data-stu-id="289aa-280">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="289aa-281">No ASP.NET Core 1.x, adicionando o `Vary` cabeçalho à resposta é realizado manualmente:</span><span class="sxs-lookup"><span data-stu-id="289aa-281">In ASP.NET Core 1.x, adding the `Vary` header to the response is accomplished manually:</span></span>

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="289aa-282">Problema de middleware quando atrás de um proxy reverso do Nginx</span><span class="sxs-lookup"><span data-stu-id="289aa-282">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="289aa-283">Quando uma solicitação é transmitida por proxy pelo Nginx, o `Accept-Encoding` cabeçalho é removido.</span><span class="sxs-lookup"><span data-stu-id="289aa-283">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="289aa-284">Remoção do `Accept-Encoding` cabeçalho impede que o middleware de compactação de resposta.</span><span class="sxs-lookup"><span data-stu-id="289aa-284">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="289aa-285">Confira mais informações em [NGINX: Compactação e descompactação](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span><span class="sxs-lookup"><span data-stu-id="289aa-285">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="289aa-286">Esse problema é acompanhado pelo [descobrir a compactação de passagem do Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span><span class="sxs-lookup"><span data-stu-id="289aa-286">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="289aa-287">Trabalhando com a compactação dinâmica do IIS</span><span class="sxs-lookup"><span data-stu-id="289aa-287">Working with IIS dynamic compression</span></span>

<span data-ttu-id="289aa-288">Se você tiver um Active Directory dinâmico compactação de módulo do IIS configurado no nível do servidor que você deseja desabilitar para um aplicativo, desabilite o módulo com uma adição à *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="289aa-288">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="289aa-289">Para obter mais informações, consulte [Desabilitando módulos do IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="289aa-289">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="289aa-290">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="289aa-290">Troubleshooting</span></span>

<span data-ttu-id="289aa-291">Use uma ferramenta como [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), ou [Postman](https://www.getpostman.com/), que permitem que você defina o `Accept-Encoding` cabeçalho de solicitação e estudar os cabeçalhos de resposta, o tamanho e o corpo.</span><span class="sxs-lookup"><span data-stu-id="289aa-291">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="289aa-292">Por padrão, o Middleware de compactação de resposta compacta as respostas que atendem às seguintes condições:</span><span class="sxs-lookup"><span data-stu-id="289aa-292">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="289aa-293">O `Accept-Encoding` cabeçalho está presente com um valor de `br`, `gzip`, `*`, ou codificação personalizada que corresponde a um provedor de compactação personalizado estabelecida por você.</span><span class="sxs-lookup"><span data-stu-id="289aa-293">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="289aa-294">O valor não deve ser `identity` ou tem um valor de qualidade (qvalue, `q`) configuração de 0 (zero).</span><span class="sxs-lookup"><span data-stu-id="289aa-294">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="289aa-295">O tipo MIME (`Content-Type`) deve ser definido e deve corresponder a um tipo MIME configurado no <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span><span class="sxs-lookup"><span data-stu-id="289aa-295">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="289aa-296">A solicitação não deve incluir o `Content-Range` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="289aa-296">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="289aa-297">A solicitação deve usar protocolo inseguro (http), a menos que o protocolo seguro (https) é configurado nas opções de Middleware de compactação de resposta.</span><span class="sxs-lookup"><span data-stu-id="289aa-297">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="289aa-298">*Observe o perigo [descritos acima](#compression-with-secure-protocol) ao habilitar a compactação de conteúdo segura.*</span><span class="sxs-lookup"><span data-stu-id="289aa-298">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="289aa-299">O `Accept-Encoding` cabeçalho está presente com um valor de `gzip`, `*`, ou codificação personalizada que corresponde a um provedor de compactação personalizado estabelecida por você.</span><span class="sxs-lookup"><span data-stu-id="289aa-299">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="289aa-300">O valor não deve ser `identity` ou tem um valor de qualidade (qvalue, `q`) configuração de 0 (zero).</span><span class="sxs-lookup"><span data-stu-id="289aa-300">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="289aa-301">O tipo MIME (`Content-Type`) deve ser definido e deve corresponder a um tipo MIME configurado no <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span><span class="sxs-lookup"><span data-stu-id="289aa-301">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="289aa-302">A solicitação não deve incluir o `Content-Range` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="289aa-302">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="289aa-303">A solicitação deve usar protocolo inseguro (http), a menos que o protocolo seguro (https) é configurado nas opções de Middleware de compactação de resposta.</span><span class="sxs-lookup"><span data-stu-id="289aa-303">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="289aa-304">*Observe o perigo [descritos acima](#compression-with-secure-protocol) ao habilitar a compactação de conteúdo segura.*</span><span class="sxs-lookup"><span data-stu-id="289aa-304">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="289aa-305">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="289aa-305">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="289aa-306">Rede de desenvolvedor do Mozilla: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="289aa-306">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="289aa-307">RFC 7231 seção 3.1.2.1: Conteúdo Codings</span><span class="sxs-lookup"><span data-stu-id="289aa-307">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="289aa-308">RFC 7230 seção 4.2.3: A codificação gzip</span><span class="sxs-lookup"><span data-stu-id="289aa-308">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="289aa-309">Versão de especificação de formato de arquivo GZIP 4.3</span><span class="sxs-lookup"><span data-stu-id="289aa-309">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

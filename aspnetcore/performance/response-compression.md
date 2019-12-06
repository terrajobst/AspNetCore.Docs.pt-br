---
title: Compactação de resposta no ASP.NET Core
author: guardrex
description: Saiba mais sobre a compactação de resposta e como usar o Middleware de compactação de resposta em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: performance/response-compression
ms.openlocfilehash: 04b2ffd7047e8b127968adb5d40e0141365fb5fe
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880904"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="f3eaf-103">Compactação de resposta no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3eaf-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="f3eaf-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f3eaf-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f3eaf-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f3eaf-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f3eaf-106">A largura de banda da rede é um recurso limitado.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-106">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="f3eaf-107">Reduzir o tamanho da resposta geralmente aumenta a capacidade de resposta de um aplicativo, muitas vezes drasticamente.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-107">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="f3eaf-108">Uma maneira de reduzir os tamanhos de carga é compactar as respostas de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-108">One way to reduce payload sizes is to compress an app's responses.</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="f3eaf-109">Quando usar o middleware de compactação de resposta</span><span class="sxs-lookup"><span data-stu-id="f3eaf-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="f3eaf-110">Use tecnologias de compactação de resposta baseadas em servidor no IIS, Apache ou nginx.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="f3eaf-111">O desempenho do middleware provavelmente não corresponderá ao dos módulos do servidor.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="f3eaf-112">O servidor de [servidor http. sys](xref:fundamentals/servers/httpsys) e o servidor [Kestrel](xref:fundamentals/servers/kestrel) não oferecem suporte de compactação interna no momento.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="f3eaf-113">Use o middleware de compactação de resposta quando você estiver:</span><span class="sxs-lookup"><span data-stu-id="f3eaf-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="f3eaf-114">Não é possível usar as seguintes tecnologias de compactação baseadas em servidor:</span><span class="sxs-lookup"><span data-stu-id="f3eaf-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="f3eaf-115">Módulo de compactação dinâmica do IIS</span><span class="sxs-lookup"><span data-stu-id="f3eaf-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="f3eaf-116">Módulo mod_deflate do Apache</span><span class="sxs-lookup"><span data-stu-id="f3eaf-116">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="f3eaf-117">Compactação e descompactação de Nginx</span><span class="sxs-lookup"><span data-stu-id="f3eaf-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="f3eaf-118">Hospedando diretamente em:</span><span class="sxs-lookup"><span data-stu-id="f3eaf-118">Hosting directly on:</span></span>
  * <span data-ttu-id="f3eaf-119">[Servidor http. sys](xref:fundamentals/servers/httpsys) (anteriormente chamado WebListener)</span><span class="sxs-lookup"><span data-stu-id="f3eaf-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="f3eaf-120">Servidor Kestrel</span><span class="sxs-lookup"><span data-stu-id="f3eaf-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="f3eaf-121">Compactação de resposta</span><span class="sxs-lookup"><span data-stu-id="f3eaf-121">Response compression</span></span>

<span data-ttu-id="f3eaf-122">Normalmente, qualquer resposta não compactada nativamente pode se beneficiar da compactação de resposta.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="f3eaf-123">As respostas não compactadas nativamente normalmente incluem: CSS, JavaScript, HTML, XML e JSON.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="f3eaf-124">Você não deve compactar ativos compactados nativamente, como arquivos PNG.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="f3eaf-125">Se você tentar compactar ainda mais uma resposta compactada nativamente, qualquer pequena redução adicional no tamanho e no tempo de transmissão provavelmente será ofuscada no tempo necessário para processar a compactação.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="f3eaf-126">Não compacte arquivos com menos de 150-1000 bytes (dependendo do conteúdo do arquivo e da eficiência da compactação).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="f3eaf-127">A sobrecarga de compactação de arquivos pequenos pode produzir um arquivo compactado maior do que o arquivo descompactado.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="f3eaf-128">Quando um cliente pode processar conteúdo compactado, o cliente deve informar o servidor de seus recursos enviando o cabeçalho de `Accept-Encoding` com a solicitação.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="f3eaf-129">Quando um servidor envia conteúdo compactado, ele deve incluir informações no cabeçalho `Content-Encoding` sobre como a resposta compactada é codificada.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="f3eaf-130">As designações de codificação de conteúdo com suporte no middleware são mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="f3eaf-131">`Accept-Encoding` valores de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="f3eaf-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="f3eaf-132">Suporte do middleware</span><span class="sxs-lookup"><span data-stu-id="f3eaf-132">Middleware Supported</span></span> | <span data-ttu-id="f3eaf-133">Descrição</span><span class="sxs-lookup"><span data-stu-id="f3eaf-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="f3eaf-134">Sim (padrão)</span><span class="sxs-lookup"><span data-stu-id="f3eaf-134">Yes (default)</span></span>        | [<span data-ttu-id="f3eaf-135">Formato de dados compactados Brotli</span><span class="sxs-lookup"><span data-stu-id="f3eaf-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="f3eaf-136">Não</span><span class="sxs-lookup"><span data-stu-id="f3eaf-136">No</span></span>                   | [<span data-ttu-id="f3eaf-137">Desinflar formato de dados compactados</span><span class="sxs-lookup"><span data-stu-id="f3eaf-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="f3eaf-138">Não</span><span class="sxs-lookup"><span data-stu-id="f3eaf-138">No</span></span>                   | [<span data-ttu-id="f3eaf-139">Intercâmbio de XML eficiente do W3C</span><span class="sxs-lookup"><span data-stu-id="f3eaf-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="f3eaf-140">Sim</span><span class="sxs-lookup"><span data-stu-id="f3eaf-140">Yes</span></span>                  | [<span data-ttu-id="f3eaf-141">Formato de arquivo gzip</span><span class="sxs-lookup"><span data-stu-id="f3eaf-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="f3eaf-142">Sim</span><span class="sxs-lookup"><span data-stu-id="f3eaf-142">Yes</span></span>                  | <span data-ttu-id="f3eaf-143">Identificador "sem codificação": a resposta não deve ser codificada.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="f3eaf-144">Não</span><span class="sxs-lookup"><span data-stu-id="f3eaf-144">No</span></span>                   | [<span data-ttu-id="f3eaf-145">Formato de transferência de rede para arquivos Java</span><span class="sxs-lookup"><span data-stu-id="f3eaf-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="f3eaf-146">Sim</span><span class="sxs-lookup"><span data-stu-id="f3eaf-146">Yes</span></span>                  | <span data-ttu-id="f3eaf-147">Qualquer codificação de conteúdo disponível não explicitamente solicitada</span><span class="sxs-lookup"><span data-stu-id="f3eaf-147">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="f3eaf-148">`Accept-Encoding` valores de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="f3eaf-148">`Accept-Encoding` header values</span></span> | <span data-ttu-id="f3eaf-149">Suporte do middleware</span><span class="sxs-lookup"><span data-stu-id="f3eaf-149">Middleware Supported</span></span> | <span data-ttu-id="f3eaf-150">Descrição</span><span class="sxs-lookup"><span data-stu-id="f3eaf-150">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="f3eaf-151">Não</span><span class="sxs-lookup"><span data-stu-id="f3eaf-151">No</span></span>                   | [<span data-ttu-id="f3eaf-152">Formato de dados compactados Brotli</span><span class="sxs-lookup"><span data-stu-id="f3eaf-152">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="f3eaf-153">Não</span><span class="sxs-lookup"><span data-stu-id="f3eaf-153">No</span></span>                   | [<span data-ttu-id="f3eaf-154">Desinflar formato de dados compactados</span><span class="sxs-lookup"><span data-stu-id="f3eaf-154">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="f3eaf-155">Não</span><span class="sxs-lookup"><span data-stu-id="f3eaf-155">No</span></span>                   | [<span data-ttu-id="f3eaf-156">Intercâmbio de XML eficiente do W3C</span><span class="sxs-lookup"><span data-stu-id="f3eaf-156">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="f3eaf-157">Sim (padrão)</span><span class="sxs-lookup"><span data-stu-id="f3eaf-157">Yes (default)</span></span>        | [<span data-ttu-id="f3eaf-158">Formato de arquivo gzip</span><span class="sxs-lookup"><span data-stu-id="f3eaf-158">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="f3eaf-159">Sim</span><span class="sxs-lookup"><span data-stu-id="f3eaf-159">Yes</span></span>                  | <span data-ttu-id="f3eaf-160">Identificador "sem codificação": a resposta não deve ser codificada.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-160">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="f3eaf-161">Não</span><span class="sxs-lookup"><span data-stu-id="f3eaf-161">No</span></span>                   | [<span data-ttu-id="f3eaf-162">Formato de transferência de rede para arquivos Java</span><span class="sxs-lookup"><span data-stu-id="f3eaf-162">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="f3eaf-163">Sim</span><span class="sxs-lookup"><span data-stu-id="f3eaf-163">Yes</span></span>                  | <span data-ttu-id="f3eaf-164">Qualquer codificação de conteúdo disponível não explicitamente solicitada</span><span class="sxs-lookup"><span data-stu-id="f3eaf-164">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

<span data-ttu-id="f3eaf-165">Para obter mais informações, consulte a [lista de código de conteúdo oficial da IANA](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-165">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="f3eaf-166">O middleware permite que você adicione outros provedores de compactação para valores de cabeçalho de `Accept-Encoding` personalizados.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-166">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="f3eaf-167">Para obter mais informações, consulte [provedores personalizados](#custom-providers) abaixo.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-167">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="f3eaf-168">O middleware é capaz de reagir à importância do valor de qualidade (qvalue, `q`) quando enviado pelo cliente para priorizar os esquemas de compactação.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-168">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="f3eaf-169">Para obter mais informações, consulte [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-169">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="f3eaf-170">Os algoritmos de compactação estão sujeitos a uma compensação entre a velocidade de compactação e a eficácia da compactação.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-170">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="f3eaf-171">A *eficácia* neste contexto refere-se ao tamanho da saída após a compactação.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-171">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="f3eaf-172">O menor tamanho é obtido pela compactação *ideal* .</span><span class="sxs-lookup"><span data-stu-id="f3eaf-172">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="f3eaf-173">Os cabeçalhos envolvidos na solicitação, no envio, no cache e no recebimento de conteúdo compactado são descritos na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-173">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="f3eaf-174">Cabeçalho</span><span class="sxs-lookup"><span data-stu-id="f3eaf-174">Header</span></span>             | <span data-ttu-id="f3eaf-175">Função</span><span class="sxs-lookup"><span data-stu-id="f3eaf-175">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="f3eaf-176">Enviado do cliente para o servidor para indicar os esquemas de codificação de conteúdo aceitáveis para o cliente.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-176">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="f3eaf-177">Enviado do servidor para o cliente para indicar a codificação do conteúdo na carga.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-177">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="f3eaf-178">Quando ocorre a compactação, o cabeçalho de `Content-Length` é removido, pois o conteúdo do corpo é alterado quando a resposta é compactada.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-178">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="f3eaf-179">Quando ocorre a compactação, o cabeçalho de `Content-MD5` é removido, pois o conteúdo do corpo foi alterado e o hash não é mais válido.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-179">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="f3eaf-180">Especifica o tipo MIME do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-180">Specifies the MIME type of the content.</span></span> <span data-ttu-id="f3eaf-181">Cada resposta deve especificar seu `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-181">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="f3eaf-182">O middleware verifica esse valor para determinar se a resposta deve ser compactada.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-182">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="f3eaf-183">O middleware especifica um conjunto de [tipos MIME padrão](#mime-types) que ele pode codificar, mas você pode substituir ou adicionar tipos de MIME.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-183">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="f3eaf-184">Quando enviado pelo servidor com um valor de `Accept-Encoding` para clientes e proxies, o cabeçalho `Vary` indica ao cliente ou ao proxy que ele deve armazenar em cache (variar) as respostas com base no valor do cabeçalho `Accept-Encoding` da solicitação.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-184">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="f3eaf-185">O resultado do retorno do conteúdo com o cabeçalho `Vary: Accept-Encoding` é que as respostas compactadas e não compactadas são armazenadas em cache separadamente.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-185">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="f3eaf-186">Explore os recursos do middleware de compactação de resposta com o [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-186">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="f3eaf-187">O exemplo ilustra:</span><span class="sxs-lookup"><span data-stu-id="f3eaf-187">The sample illustrates:</span></span>

* <span data-ttu-id="f3eaf-188">A compactação de respostas de aplicativo usando gzip e provedores de compactação personalizados.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-188">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="f3eaf-189">Como adicionar um tipo de MIME à lista padrão de tipos de MIME para compactação.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-189">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="f3eaf-190">Pacote</span><span class="sxs-lookup"><span data-stu-id="f3eaf-190">Package</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3eaf-191">O middleware de compactação de resposta é fornecido pelo pacote [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) , que é incluído implicitamente em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-191">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f3eaf-192">Para incluir o middleware em um projeto, adicione uma referência ao [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), que inclui o pacote [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) .</span><span class="sxs-lookup"><span data-stu-id="f3eaf-192">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="f3eaf-193">Configuração do</span><span class="sxs-lookup"><span data-stu-id="f3eaf-193">Configuration</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f3eaf-194">O código a seguir mostra como habilitar o middleware de compactação de resposta para tipos MIME padrão e provedores de compactação ([Brotli](#brotli-compression-provider) e [gzip](#gzip-compression-provider)):</span><span class="sxs-lookup"><span data-stu-id="f3eaf-194">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f3eaf-195">O código a seguir mostra como habilitar o middleware de compactação de resposta para tipos MIME padrão e o [provedor de compactação Gzip](#gzip-compression-provider):</span><span class="sxs-lookup"><span data-stu-id="f3eaf-195">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="f3eaf-196">Observações:</span><span class="sxs-lookup"><span data-stu-id="f3eaf-196">Notes:</span></span>

* <span data-ttu-id="f3eaf-197">`app.UseResponseCompression` deve ser chamado antes de `app.UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-197">`app.UseResponseCompression` must be called before `app.UseMvc`.</span></span>
* <span data-ttu-id="f3eaf-198">Use uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler), o [Firebug](https://getfirebug.com/)ou o [postmaster](https://www.getpostman.com/) para definir o cabeçalho de solicitação `Accept-Encoding` e estudar os cabeçalhos, o tamanho e o corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-198">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="f3eaf-199">Envie uma solicitação para o aplicativo de exemplo sem o cabeçalho `Accept-Encoding` e observe que a resposta é descompactada.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-199">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="f3eaf-200">Os cabeçalhos `Content-Encoding` e `Vary` não estão presentes na resposta.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-200">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Janela Fiddler mostrando o resultado de uma solicitação sem o cabeçalho Accept-Encoding.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f3eaf-203">Envie uma solicitação para o aplicativo de exemplo com o cabeçalho `Accept-Encoding: br` (compactação Brotli) e observe que a resposta está compactada.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-203">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="f3eaf-204">Os cabeçalhos `Content-Encoding` e `Vary` estão presentes na resposta.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-204">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Janela Fiddler mostrando o resultado de uma solicitação com o cabeçalho Accept-Encoding e um valor de br.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f3eaf-208">Envie uma solicitação para o aplicativo de exemplo com o cabeçalho `Accept-Encoding: gzip` e observe que a resposta está compactada.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-208">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="f3eaf-209">Os cabeçalhos `Content-Encoding` e `Vary` estão presentes na resposta.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-209">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Janela Fiddler mostrando o resultado de uma solicitação com o cabeçalho Accept-Encoding e um valor de gzip.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a><span data-ttu-id="f3eaf-213">Provedores</span><span class="sxs-lookup"><span data-stu-id="f3eaf-213">Providers</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a><span data-ttu-id="f3eaf-214">Provedor de compactação Brotli</span><span class="sxs-lookup"><span data-stu-id="f3eaf-214">Brotli Compression Provider</span></span>

<span data-ttu-id="f3eaf-215">Use o <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> para compactar respostas com o [formato de dados compactado Brotli](https://tools.ietf.org/html/rfc7932).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-215">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="f3eaf-216">Se nenhum provedor de compactação for explicitamente adicionado à <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="f3eaf-216">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="f3eaf-217">O provedor de compactação Brotli é adicionado por padrão à matriz de provedores de compactação junto com o [provedor de compactação Gzip](#gzip-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-217">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="f3eaf-218">O padrão de compactação é a compactação Brotli quando o formato de dados compactados Brotli é suportado pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-218">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="f3eaf-219">Se Brotli não for suportado pelo cliente, a compactação padronizará para gzip quando o cliente oferecer suporte à compactação Gzip.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-219">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="f3eaf-220">O provedor de compactação Brotoli deve ser adicionado quando qualquer provedor de compactação for explicitamente adicionado:</span><span class="sxs-lookup"><span data-stu-id="f3eaf-220">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f3eaf-221">Defina o nível de compactação com <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-221">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="f3eaf-222">O provedor de compactação Brotli usa como padrão o nível de compactação mais rápido ([CompressionLevel. mais rápido](xref:System.IO.Compression.CompressionLevel)), que pode não produzir a compactação mais eficiente.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-222">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="f3eaf-223">Se a compactação mais eficiente for desejada, configure o middleware para uma compactação ideal.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-223">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="f3eaf-224">Nível de Compactação</span><span class="sxs-lookup"><span data-stu-id="f3eaf-224">Compression Level</span></span> | <span data-ttu-id="f3eaf-225">Descrição</span><span class="sxs-lookup"><span data-stu-id="f3eaf-225">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="f3eaf-226">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="f3eaf-226">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f3eaf-227">A compactação deve ser concluída o mais rápido possível, mesmo que a saída resultante não seja compactada de forma ideal.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-227">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="f3eaf-228">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="f3eaf-228">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f3eaf-229">Nenhuma compactação deve ser executada.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-229">No compression should be performed.</span></span> |
| [<span data-ttu-id="f3eaf-230">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="f3eaf-230">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f3eaf-231">As respostas devem ser compactadas de forma ideal, mesmo que a compactação leve mais tempo para ser concluída.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-231">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="f3eaf-232">Provedor de compactação Gzip</span><span class="sxs-lookup"><span data-stu-id="f3eaf-232">Gzip Compression Provider</span></span>

<span data-ttu-id="f3eaf-233">Use o <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> para compactar respostas com o [formato de arquivo gzip](https://tools.ietf.org/html/rfc1952).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-233">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="f3eaf-234">Se nenhum provedor de compactação for explicitamente adicionado à <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="f3eaf-234">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f3eaf-235">O provedor de compactação Gzip é adicionado por padrão à matriz de provedores de compactação junto com o [provedor de compactação Brotli](#brotli-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-235">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="f3eaf-236">O padrão de compactação é a compactação Brotli quando o formato de dados compactados Brotli é suportado pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-236">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="f3eaf-237">Se Brotli não for suportado pelo cliente, a compactação padronizará para gzip quando o cliente oferecer suporte à compactação Gzip.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-237">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="f3eaf-238">O provedor de compactação Gzip é adicionado por padrão à matriz de provedores de compactação.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-238">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="f3eaf-239">O padrão de compactação é gzip quando o cliente dá suporte à compactação Gzip.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-239">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="f3eaf-240">O provedor de compactação Gzip deve ser adicionado quando qualquer provedor de compactação for explicitamente adicionado:</span><span class="sxs-lookup"><span data-stu-id="f3eaf-240">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

<span data-ttu-id="f3eaf-241">Defina o nível de compactação com <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-241">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="f3eaf-242">O provedor de compactação Gzip usa como padrão o nível de compactação mais rápido ([CompressionLevel. mais rápido](xref:System.IO.Compression.CompressionLevel)), que pode não produzir a compactação mais eficiente.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-242">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="f3eaf-243">Se a compactação mais eficiente for desejada, configure o middleware para uma compactação ideal.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-243">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="f3eaf-244">Nível de Compactação</span><span class="sxs-lookup"><span data-stu-id="f3eaf-244">Compression Level</span></span> | <span data-ttu-id="f3eaf-245">Descrição</span><span class="sxs-lookup"><span data-stu-id="f3eaf-245">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="f3eaf-246">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="f3eaf-246">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f3eaf-247">A compactação deve ser concluída o mais rápido possível, mesmo que a saída resultante não seja compactada de forma ideal.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-247">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="f3eaf-248">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="f3eaf-248">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f3eaf-249">Nenhuma compactação deve ser executada.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-249">No compression should be performed.</span></span> |
| [<span data-ttu-id="f3eaf-250">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="f3eaf-250">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f3eaf-251">As respostas devem ser compactadas de forma ideal, mesmo que a compactação leve mais tempo para ser concluída.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-251">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="f3eaf-252">Provedores personalizados</span><span class="sxs-lookup"><span data-stu-id="f3eaf-252">Custom providers</span></span>

<span data-ttu-id="f3eaf-253">Crie implementações de compactação personalizadas com <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-253">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="f3eaf-254">O <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> representa a codificação de conteúdo que esse `ICompressionProvider` produz.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-254">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="f3eaf-255">O middleware usa essas informações para escolher o provedor com base na lista especificada no cabeçalho `Accept-Encoding` da solicitação.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-255">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="f3eaf-256">Usando o aplicativo de exemplo, o cliente envia uma solicitação com o cabeçalho `Accept-Encoding: mycustomcompression`.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-256">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="f3eaf-257">O middleware usa a implementação de compactação personalizada e retorna a resposta com um cabeçalho `Content-Encoding: mycustomcompression`.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-257">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="f3eaf-258">O cliente deve ser capaz de descompactar a codificação personalizada para que uma implementação de compactação personalizada funcione.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-258">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="f3eaf-259">Envie uma solicitação para o aplicativo de exemplo com o cabeçalho `Accept-Encoding: mycustomcompression` e observe os cabeçalhos de resposta.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-259">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="f3eaf-260">Os cabeçalhos `Vary` e `Content-Encoding` estão presentes na resposta.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-260">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="f3eaf-261">O corpo da resposta (não mostrado) não é compactado pelo exemplo.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-261">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="f3eaf-262">Não há uma implementação de compactação na classe `CustomCompressionProvider` do exemplo.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-262">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="f3eaf-263">No entanto, o exemplo mostra onde você implementaria esse algoritmo de compactação.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-263">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Janela Fiddler mostrando o resultado de uma solicitação com o cabeçalho Accept-Encoding e um valor de mycustomcompression.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="f3eaf-266">tipos MIME</span><span class="sxs-lookup"><span data-stu-id="f3eaf-266">MIME types</span></span>

<span data-ttu-id="f3eaf-267">O middleware especifica um conjunto padrão de tipos MIME para compactação:</span><span class="sxs-lookup"><span data-stu-id="f3eaf-267">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="f3eaf-268">Substitua ou acrescente tipos MIME com as opções de middleware de compactação de resposta.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-268">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="f3eaf-269">Observe que não há suporte para os tipos MIME curinga, como `text/*`.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-269">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="f3eaf-270">O aplicativo de exemplo adiciona um tipo MIME para `image/svg+xml` e compacta e serve a imagem ASP.NET Core banner (*banner. svg*).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-270">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="f3eaf-271">Compactação com protocolo seguro</span><span class="sxs-lookup"><span data-stu-id="f3eaf-271">Compression with secure protocol</span></span>

<span data-ttu-id="f3eaf-272">As respostas compactadas em conexões seguras podem ser controladas com a opção `EnableForHttps`, que é desabilitada por padrão.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-272">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="f3eaf-273">O uso da compactação com páginas geradas dinamicamente pode levar a problemas de segurança, como ataques de [crime](https://wikipedia.org/wiki/CRIME_(security_exploit)) e [violação](https://wikipedia.org/wiki/BREACH_(security_exploit)) .</span><span class="sxs-lookup"><span data-stu-id="f3eaf-273">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="f3eaf-274">Adicionando o cabeçalho Vary</span><span class="sxs-lookup"><span data-stu-id="f3eaf-274">Adding the Vary header</span></span>

<span data-ttu-id="f3eaf-275">Ao compactar respostas com base no cabeçalho de `Accept-Encoding`, há potencialmente várias versões compactadas da resposta e uma versão não compactada.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-275">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="f3eaf-276">Para instruir os caches de cliente e proxy de que várias versões existem e devem ser armazenadas, o cabeçalho de `Vary` é adicionado com um valor de `Accept-Encoding`.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-276">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="f3eaf-277">No ASP.NET Core 2,0 ou posterior, o middleware adiciona o cabeçalho de `Vary` automaticamente quando a resposta é compactada.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-277">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="f3eaf-278">Problema de middleware quando por trás de um proxy reverso Nginx</span><span class="sxs-lookup"><span data-stu-id="f3eaf-278">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="f3eaf-279">Quando uma solicitação é modificada por proxy pelo Nginx, o cabeçalho `Accept-Encoding` é removido.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-279">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="f3eaf-280">A remoção do cabeçalho de `Accept-Encoding` impede que o middleware compacte a resposta.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-280">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="f3eaf-281">Para obter mais informações, consulte [Nginx: compactação e descompactação](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-281">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="f3eaf-282">Esse problema é rastreado por [descobrir a compactação de passagem para Nginx (ASPNET/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-282">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="f3eaf-283">Trabalhando com compactação dinâmica do IIS</span><span class="sxs-lookup"><span data-stu-id="f3eaf-283">Working with IIS dynamic compression</span></span>

<span data-ttu-id="f3eaf-284">Se você tiver um módulo de compactação dinâmica do IIS ativo configurado no nível do servidor que deseja desabilitar para um aplicativo, desabilite o módulo com uma adição ao arquivo *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="f3eaf-284">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="f3eaf-285">Para obter mais informações, consulte [Desabilitando módulos do IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-285">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f3eaf-286">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="f3eaf-286">Troubleshooting</span></span>

<span data-ttu-id="f3eaf-287">Use uma ferramenta como [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)ou [postmaster](https://www.getpostman.com/), que permite definir o cabeçalho de solicitação `Accept-Encoding` e estudar os cabeçalhos de resposta, o tamanho e o corpo.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-287">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="f3eaf-288">Por padrão, o middleware de compactação de resposta compacta as respostas que atendem às seguintes condições:</span><span class="sxs-lookup"><span data-stu-id="f3eaf-288">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f3eaf-289">O cabeçalho `Accept-Encoding` está presente com um valor de `br`, `gzip`, `*`ou codificação personalizada que corresponde a um provedor de compactação personalizado que você estabeleceu.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-289">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="f3eaf-290">O valor não deve ser `identity` ou ter uma configuração de valor de qualidade (qvalue, `q`) igual a 0 (zero).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-290">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="f3eaf-291">O tipo MIME (`Content-Type`) deve ser definido e deve corresponder a um tipo MIME configurado no <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-291">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="f3eaf-292">A solicitação não deve incluir o cabeçalho de `Content-Range`.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-292">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="f3eaf-293">A solicitação deve usar o protocolo http, a menos que o protocolo SSL (https) esteja configurado nas opções de middleware de compactação de resposta.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-293">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="f3eaf-294">*Observe o perigo [descrito acima](#compression-with-secure-protocol) ao habilitar a compactação de conteúdo seguro.*</span><span class="sxs-lookup"><span data-stu-id="f3eaf-294">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="f3eaf-295">O cabeçalho de `Accept-Encoding` está presente com um valor de `gzip`, `*`ou codificação personalizada que corresponde a um provedor de compactação personalizado que você estabeleceu.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-295">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="f3eaf-296">O valor não deve ser `identity` ou ter uma configuração de valor de qualidade (qvalue, `q`) igual a 0 (zero).</span><span class="sxs-lookup"><span data-stu-id="f3eaf-296">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="f3eaf-297">O tipo MIME (`Content-Type`) deve ser definido e deve corresponder a um tipo MIME configurado no <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-297">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="f3eaf-298">A solicitação não deve incluir o cabeçalho de `Content-Range`.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-298">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="f3eaf-299">A solicitação deve usar o protocolo http, a menos que o protocolo SSL (https) esteja configurado nas opções de middleware de compactação de resposta.</span><span class="sxs-lookup"><span data-stu-id="f3eaf-299">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="f3eaf-300">*Observe o perigo [descrito acima](#compression-with-secure-protocol) ao habilitar a compactação de conteúdo seguro.*</span><span class="sxs-lookup"><span data-stu-id="f3eaf-300">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f3eaf-301">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f3eaf-301">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="f3eaf-302">Rede de desenvolvedores do Mozilla: aceitação-codificação</span><span class="sxs-lookup"><span data-stu-id="f3eaf-302">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="f3eaf-303">RFC 7231 seção 3.1.2.1: codificações de conteúdo</span><span class="sxs-lookup"><span data-stu-id="f3eaf-303">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="f3eaf-304">RFC 7230 seção 4.2.3: codificação gzip</span><span class="sxs-lookup"><span data-stu-id="f3eaf-304">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="f3eaf-305">Especificação de formato de arquivo GZIP versão 4,3</span><span class="sxs-lookup"><span data-stu-id="f3eaf-305">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

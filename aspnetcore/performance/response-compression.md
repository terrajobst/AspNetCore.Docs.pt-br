---
title: Compactação de resposta no ASP.NET Core
author: guardrex
description: Saiba mais sobre a compactação de resposta e como usar o Middleware de compactação de resposta em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
uid: performance/response-compression
ms.openlocfilehash: b8a84418a3258e9ac43b4eadd8564c0708590bce
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726967"
---
# <a name="response-compression-in-aspnet-core"></a>Compactação de resposta no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([como baixar](xref:index#how-to-download-a-sample))

A largura de banda da rede é um recurso limitado. Reduzir o tamanho da resposta geralmente aumenta a capacidade de resposta de um aplicativo, muitas vezes drasticamente. Uma maneira de reduzir os tamanhos de carga é compactar as respostas de um aplicativo.

## <a name="when-to-use-response-compression-middleware"></a>Quando usar o middleware de compactação de resposta

Use tecnologias de compactação de resposta baseadas em servidor no IIS, Apache ou nginx. O desempenho do middleware provavelmente não corresponderá ao dos módulos do servidor. O servidor de [servidor http. sys](xref:fundamentals/servers/httpsys) e o servidor [Kestrel](xref:fundamentals/servers/kestrel) não oferecem suporte de compactação interna no momento.

Use o middleware de compactação de resposta quando você estiver:

* Não é possível usar as seguintes tecnologias de compactação baseadas em servidor:
  * [Módulo de compactação dinâmica do IIS](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Módulo mod_deflate do Apache](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Compactação e descompactação de Nginx](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Hospedando diretamente em:
  * [Servidor http. sys](xref:fundamentals/servers/httpsys) (anteriormente chamado WebListener)
  * [Servidor Kestrel](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Compactação de resposta

Normalmente, qualquer resposta não compactada nativamente pode se beneficiar da compactação de resposta. As respostas não compactadas nativamente normalmente incluem: CSS, JavaScript, HTML, XML e JSON. Você não deve compactar ativos compactados nativamente, como arquivos PNG. Se você tentar compactar ainda mais uma resposta compactada nativamente, qualquer pequena redução adicional no tamanho e no tempo de transmissão provavelmente será ofuscada no tempo necessário para processar a compactação. Não compacte arquivos com menos de 150-1000 bytes (dependendo do conteúdo do arquivo e da eficiência da compactação). A sobrecarga de compactação de arquivos pequenos pode produzir um arquivo compactado maior do que o arquivo descompactado.

Quando um cliente pode processar conteúdo compactado, o cliente deve informar o servidor de seus recursos enviando o cabeçalho de `Accept-Encoding` com a solicitação. Quando um servidor envia conteúdo compactado, ele deve incluir informações no cabeçalho `Content-Encoding` sobre como a resposta compactada é codificada. As designações de codificação de conteúdo com suporte no middleware são mostradas na tabela a seguir.

::: moniker range=">= aspnetcore-2.2"

| `Accept-Encoding` valores de cabeçalho | Suporte do middleware | Descrição |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Sim (padrão)        | [Formato de dados compactados Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Não                   | [Desinflar formato de dados compactados](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Não                   | [Intercâmbio de XML eficiente do W3C](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Sim                  | [Formato de arquivo gzip](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Sim                  | Identificador "sem codificação": a resposta não deve ser codificada. |
| `pack200-gzip`                  | Não                   | [Formato de transferência de rede para arquivos Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Sim                  | Qualquer codificação de conteúdo disponível não explicitamente solicitada |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| `Accept-Encoding` valores de cabeçalho | Suporte do middleware | Descrição |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Não                   | [Formato de dados compactados Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Não                   | [Desinflar formato de dados compactados](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Não                   | [Intercâmbio de XML eficiente do W3C](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Sim (padrão)        | [Formato de arquivo gzip](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Sim                  | Identificador "sem codificação": a resposta não deve ser codificada. |
| `pack200-gzip`                  | Não                   | [Formato de transferência de rede para arquivos Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Sim                  | Qualquer codificação de conteúdo disponível não explicitamente solicitada |

::: moniker-end

Para obter mais informações, consulte a [lista de código de conteúdo oficial da IANA](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

O middleware permite que você adicione outros provedores de compactação para valores de cabeçalho de `Accept-Encoding` personalizados. Para obter mais informações, consulte [provedores personalizados](#custom-providers) abaixo.

O middleware é capaz de reagir à importância do valor de qualidade (qvalue, `q`) quando enviado pelo cliente para priorizar os esquemas de compactação. Para obter mais informações, consulte [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Os algoritmos de compactação estão sujeitos a uma compensação entre a velocidade de compactação e a eficácia da compactação. A *eficácia* neste contexto refere-se ao tamanho da saída após a compactação. O menor tamanho é obtido pela compactação *ideal* .

Os cabeçalhos envolvidos na solicitação, no envio, no cache e no recebimento de conteúdo compactado são descritos na tabela a seguir.

| Cabeçalho             | Função |
| ------------------ | ---- |
| `Accept-Encoding`  | Enviado do cliente para o servidor para indicar os esquemas de codificação de conteúdo aceitáveis para o cliente. |
| `Content-Encoding` | Enviado do servidor para o cliente para indicar a codificação do conteúdo na carga. |
| `Content-Length`   | Quando ocorre a compactação, o cabeçalho de `Content-Length` é removido, pois o conteúdo do corpo é alterado quando a resposta é compactada. |
| `Content-MD5`      | Quando ocorre a compactação, o cabeçalho de `Content-MD5` é removido, pois o conteúdo do corpo foi alterado e o hash não é mais válido. |
| `Content-Type`     | Especifica o tipo MIME do conteúdo. Cada resposta deve especificar seu `Content-Type`. O middleware verifica esse valor para determinar se a resposta deve ser compactada. O middleware especifica um conjunto de [tipos MIME padrão](#mime-types) que ele pode codificar, mas você pode substituir ou adicionar tipos de MIME. |
| `Vary`             | Quando enviado pelo servidor com um valor de `Accept-Encoding` para clientes e proxies, o cabeçalho `Vary` indica ao cliente ou ao proxy que ele deve armazenar em cache (variar) as respostas com base no valor do cabeçalho `Accept-Encoding` da solicitação. O resultado do retorno do conteúdo com o cabeçalho `Vary: Accept-Encoding` é que as respostas compactadas e não compactadas são armazenadas em cache separadamente. |

Explore os recursos do middleware de compactação de resposta com o [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples). O exemplo ilustra:

* A compactação de respostas de aplicativo usando gzip e provedores de compactação personalizados.
* Como adicionar um tipo de MIME à lista padrão de tipos de MIME para compactação.

## <a name="package"></a>Pacote

::: moniker range=">= aspnetcore-3.0"

O middleware de compactação de resposta é fornecido pelo pacote [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) , que é incluído implicitamente em aplicativos ASP.NET Core.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Para incluir o middleware em um projeto, adicione uma referência ao [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), que inclui o pacote [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) .

::: moniker-end

## <a name="configuration"></a>Configuração do

::: moniker range=">= aspnetcore-2.2"

O código a seguir mostra como habilitar o middleware de compactação de resposta para tipos MIME padrão e provedores de compactação ([Brotli](#brotli-compression-provider) e [gzip](#gzip-compression-provider)):

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O código a seguir mostra como habilitar o middleware de compactação de resposta para tipos MIME padrão e o [provedor de compactação Gzip](#gzip-compression-provider):

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

Observações:

* `app.UseResponseCompression` deve ser chamado antes de qualquer middleware que compacte as respostas. Para obter mais informações, consulte <xref:fundamentals/middleware/index#middleware-order>.
* Use uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler), o [Firebug](https://getfirebug.com/)ou o [postmaster](https://www.getpostman.com/) para definir o cabeçalho de solicitação `Accept-Encoding` e estudar os cabeçalhos, o tamanho e o corpo da resposta.

Envie uma solicitação para o aplicativo de exemplo sem o cabeçalho `Accept-Encoding` e observe que a resposta é descompactada. Os cabeçalhos `Content-Encoding` e `Vary` não estão presentes na resposta.

![Janela Fiddler mostrando o resultado de uma solicitação sem o cabeçalho Accept-Encoding. A resposta não está compactada.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

Envie uma solicitação para o aplicativo de exemplo com o cabeçalho `Accept-Encoding: br` (compactação Brotli) e observe que a resposta está compactada. Os cabeçalhos `Content-Encoding` e `Vary` estão presentes na resposta.

![Janela Fiddler mostrando o resultado de uma solicitação com o cabeçalho Accept-Encoding e um valor de br. Os cabeçalhos Vary e codificação de conteúdo são adicionados à resposta. A resposta é compactada.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Envie uma solicitação para o aplicativo de exemplo com o cabeçalho `Accept-Encoding: gzip` e observe que a resposta está compactada. Os cabeçalhos `Content-Encoding` e `Vary` estão presentes na resposta.

![Janela Fiddler mostrando o resultado de uma solicitação com o cabeçalho Accept-Encoding e um valor de gzip. Os cabeçalhos Vary e codificação de conteúdo são adicionados à resposta. A resposta é compactada.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a>Provedores

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a>Provedor de compactação Brotli

Use o <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> para compactar respostas com o [formato de dados compactado Brotli](https://tools.ietf.org/html/rfc7932).

Se nenhum provedor de compactação for explicitamente adicionado à <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

* O provedor de compactação Brotli é adicionado por padrão à matriz de provedores de compactação junto com o [provedor de compactação Gzip](#gzip-compression-provider).
* O padrão de compactação é a compactação Brotli quando o formato de dados compactados Brotli é suportado pelo cliente. Se Brotli não for suportado pelo cliente, a compactação padronizará para gzip quando o cliente oferecer suporte à compactação Gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

O provedor de compactação Brotoli deve ser adicionado quando qualquer provedor de compactação for explicitamente adicionado:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Defina o nível de compactação com <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>. O provedor de compactação Brotli usa como padrão o nível de compactação mais rápido ([CompressionLevel. mais rápido](xref:System.IO.Compression.CompressionLevel)), que pode não produzir a compactação mais eficiente. Se a compactação mais eficiente for desejada, configure o middleware para uma compactação ideal.

| Nível de compactação | Descrição |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | A compactação deve ser concluída o mais rápido possível, mesmo que a saída resultante não seja compactada de forma ideal. |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | Nenhuma compactação deve ser executada. |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | As respostas devem ser compactadas de forma ideal, mesmo que a compactação leve mais tempo para ser concluída. |

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

### <a name="gzip-compression-provider"></a>Provedor de compactação Gzip

Use o <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> para compactar respostas com o [formato de arquivo gzip](https://tools.ietf.org/html/rfc1952).

Se nenhum provedor de compactação for explicitamente adicionado à <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

::: moniker range=">= aspnetcore-2.2"

* O provedor de compactação Gzip é adicionado por padrão à matriz de provedores de compactação junto com o [provedor de compactação Brotli](#brotli-compression-provider).
* O padrão de compactação é a compactação Brotli quando o formato de dados compactados Brotli é suportado pelo cliente. Se Brotli não for suportado pelo cliente, a compactação padronizará para gzip quando o cliente oferecer suporte à compactação Gzip.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* O provedor de compactação Gzip é adicionado por padrão à matriz de provedores de compactação.
* O padrão de compactação é gzip quando o cliente dá suporte à compactação Gzip.

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

O provedor de compactação Gzip deve ser adicionado quando qualquer provedor de compactação for explicitamente adicionado:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

Defina o nível de compactação com <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>. O provedor de compactação Gzip usa como padrão o nível de compactação mais rápido ([CompressionLevel. mais rápido](xref:System.IO.Compression.CompressionLevel)), que pode não produzir a compactação mais eficiente. Se a compactação mais eficiente for desejada, configure o middleware para uma compactação ideal.

| Nível de compactação | Descrição |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | A compactação deve ser concluída o mais rápido possível, mesmo que a saída resultante não seja compactada de forma ideal. |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | Nenhuma compactação deve ser executada. |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | As respostas devem ser compactadas de forma ideal, mesmo que a compactação leve mais tempo para ser concluída. |

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

### <a name="custom-providers"></a>Provedores personalizados

Crie implementações de compactação personalizadas com <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>. O <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> representa a codificação de conteúdo que esse `ICompressionProvider` produz. O middleware usa essas informações para escolher o provedor com base na lista especificada no cabeçalho `Accept-Encoding` da solicitação.

Usando o aplicativo de exemplo, o cliente envia uma solicitação com o cabeçalho `Accept-Encoding: mycustomcompression`. O middleware usa a implementação de compactação personalizada e retorna a resposta com um cabeçalho `Content-Encoding: mycustomcompression`. O cliente deve ser capaz de descompactar a codificação personalizada para que uma implementação de compactação personalizada funcione.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

Envie uma solicitação para o aplicativo de exemplo com o cabeçalho `Accept-Encoding: mycustomcompression` e observe os cabeçalhos de resposta. Os cabeçalhos `Vary` e `Content-Encoding` estão presentes na resposta. O corpo da resposta (não mostrado) não é compactado pelo exemplo. Não há uma implementação de compactação na classe `CustomCompressionProvider` do exemplo. No entanto, o exemplo mostra onde você implementaria esse algoritmo de compactação.

![Janela Fiddler mostrando o resultado de uma solicitação com o cabeçalho Accept-Encoding e um valor de mycustomcompression. Os cabeçalhos Vary e codificação de conteúdo são adicionados à resposta.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>tipos MIME

O middleware especifica um conjunto padrão de tipos MIME para compactação:

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

Substitua ou acrescente tipos MIME com as opções de middleware de compactação de resposta. Observe que não há suporte para os tipos MIME curinga, como `text/*`. O aplicativo de exemplo adiciona um tipo MIME para `image/svg+xml` e compacta e serve a imagem ASP.NET Core banner (*banner. svg*).

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a>Compactação com protocolo seguro

As respostas compactadas em conexões seguras podem ser controladas com a opção `EnableForHttps`, que é desabilitada por padrão. O uso da compactação com páginas geradas dinamicamente pode levar a problemas de segurança, como ataques de [crime](https://wikipedia.org/wiki/CRIME_(security_exploit)) e [violação](https://wikipedia.org/wiki/BREACH_(security_exploit)) .

## <a name="adding-the-vary-header"></a>Adicionando o cabeçalho Vary

Ao compactar respostas com base no cabeçalho de `Accept-Encoding`, há potencialmente várias versões compactadas da resposta e uma versão não compactada. Para instruir os caches de cliente e proxy de que várias versões existem e devem ser armazenadas, o cabeçalho de `Vary` é adicionado com um valor de `Accept-Encoding`. No ASP.NET Core 2,0 ou posterior, o middleware adiciona o cabeçalho de `Vary` automaticamente quando a resposta é compactada.

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Problema de middleware quando por trás de um proxy reverso Nginx

Quando uma solicitação é modificada por proxy pelo Nginx, o cabeçalho `Accept-Encoding` é removido. A remoção do cabeçalho de `Accept-Encoding` impede que o middleware compacte a resposta. Para obter mais informações, consulte [Nginx: compactação e descompactação](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Esse problema é rastreado por [descobrir a compactação de passagem para Nginx (ASPNET/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Trabalhando com compactação dinâmica do IIS

Se você tiver um módulo de compactação dinâmica do IIS ativo configurado no nível do servidor que deseja desabilitar para um aplicativo, desabilite o módulo com uma adição ao arquivo *Web. config* . Para obter mais informações, consulte [Desabilitando módulos do IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Solução de problemas

Use uma ferramenta como [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)ou [postmaster](https://www.getpostman.com/), que permite definir o cabeçalho de solicitação `Accept-Encoding` e estudar os cabeçalhos de resposta, o tamanho e o corpo. Por padrão, o middleware de compactação de resposta compacta as respostas que atendem às seguintes condições:

::: moniker range=">= aspnetcore-2.2"

* O cabeçalho `Accept-Encoding` está presente com um valor de `br`, `gzip`, `*`ou codificação personalizada que corresponde a um provedor de compactação personalizado que você estabeleceu. O valor não deve ser `identity` ou ter uma configuração de valor de qualidade (qvalue, `q`) igual a 0 (zero).
* O tipo MIME (`Content-Type`) deve ser definido e deve corresponder a um tipo MIME configurado no <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.
* A solicitação não deve incluir o cabeçalho de `Content-Range`.
* A solicitação deve usar o protocolo http, a menos que o protocolo SSL (https) esteja configurado nas opções de middleware de compactação de resposta. *Observe o perigo [descrito acima](#compression-with-secure-protocol) ao habilitar a compactação de conteúdo seguro.*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* O cabeçalho de `Accept-Encoding` está presente com um valor de `gzip`, `*`ou codificação personalizada que corresponde a um provedor de compactação personalizado que você estabeleceu. O valor não deve ser `identity` ou ter uma configuração de valor de qualidade (qvalue, `q`) igual a 0 (zero).
* O tipo MIME (`Content-Type`) deve ser definido e deve corresponder a um tipo MIME configurado no <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.
* A solicitação não deve incluir o cabeçalho de `Content-Range`.
* A solicitação deve usar o protocolo http, a menos que o protocolo SSL (https) esteja configurado nas opções de middleware de compactação de resposta. *Observe o perigo [descrito acima](#compression-with-secure-protocol) ao habilitar a compactação de conteúdo seguro.*

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Rede de desenvolvedores do Mozilla: aceitação-codificação](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 seção 3.1.2.1: codificações de conteúdo](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 seção 4.2.3: codificação gzip](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [Especificação de formato de arquivo GZIP versão 4,3](https://www.ietf.org/rfc/rfc1952.txt)

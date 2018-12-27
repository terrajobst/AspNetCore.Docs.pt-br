---
title: Compactação de resposta no ASP.NET Core
author: guardrex
description: Saiba mais sobre a compactação de resposta e como usar o Middleware de compactação de resposta em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: performance/response-compression
ms.openlocfilehash: 51ab51652a7b3f9b4ef97b3abbffe2e398c0bfb5
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637749"
---
# <a name="response-compression-in-aspnet-core"></a>Compactação de resposta no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples) ([como baixar](xref:index#how-to-download-a-sample))

Largura de banda de rede é um recurso limitado. Reduzindo o tamanho da resposta geralmente aumenta a capacidade de resposta de um aplicativo, muitas vezes drasticamente. É uma maneira de reduzir os tamanhos do conteúdo compactar respostas do aplicativo.

## <a name="when-to-use-response-compression-middleware"></a>Quando usar o Middleware de compactação de resposta

Use as tecnologias de compactação de resposta com base em servidor no IIS, Apache ou Nginx. O desempenho do middleware provavelmente não corresponderá dos módulos de servidor. [Servidor HTTP. sys](xref:fundamentals/servers/httpsys) servidor e [Kestrel](xref:fundamentals/servers/kestrel) server atualmente não oferecem suporte à compactação interna.

Use o Middleware de compactação de resposta quando você estiver:

* Não é possível usar as seguintes tecnologias de compactação baseada em servidor:
  * [Módulo de compactação dinâmica do IIS](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Módulo do Apache mod_deflate](http://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx compactação e descompactação](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Hospedagem diretamente em:
  * [Servidor HTTP. sys](xref:fundamentals/servers/httpsys) (anteriormente chamado WebListener)
  * [Servidor kestrel](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Compactação de resposta

Geralmente, nenhuma resposta compactada nativamente não pode se beneficiar da compactação de resposta. As respostas não nativamente compactadas normalmente incluem: CSS, JavaScript, HTML, XML e JSON. Você não deve compactar os ativos nativamente compactados, como arquivos PNG. Se você tentar compactar ainda mais uma resposta compactada nativamente, qualquer redução adicional pequena em tempo de tamanho e a transmissão será provavelmente ser superada pelo tempo levado para processar a compactação. Não compacte arquivos menores do que cerca de 150 e 1000 bytes (dependendo do conteúdo do arquivo e a eficiência da compactação). A sobrecarga de compactação de arquivos pequenos pode produzir um arquivo compactado maior do que o arquivo descompactado.

Quando um cliente pode processar o conteúdo compactado, o cliente deve informar o servidor de seus recursos, enviando o `Accept-Encoding` cabeçalho com a solicitação. Quando um servidor envia o conteúdo compactado, ele deve incluir informações no `Content-Encoding` cabeçalho em como a resposta compactada é codificada. Conteúdo designações de codifica com suporte pelo middleware são mostradas na tabela a seguir.

::: moniker range=">= aspnetcore-2.2"

| `Accept-Encoding` valores de cabeçalho | Middleware com suporte | Descrição |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Sim (padrão)        | [Formato de dados compactados Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Não                   | [Formato de dados compactados DEFLATE](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Não                   | [Intercâmbio de eficiente de XML do W3C](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Sim                  | [Formato de arquivo gzip](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Sim                  | Identificador "Nenhuma codificação": A resposta não deve ser codificada. |
| `pack200-gzip`                  | Não                   | [Formato de transferência de rede para arquivos mortos de Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Sim                  | Qualquer conteúdo disponível não codificação explicitamente solicitada |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| `Accept-Encoding` valores de cabeçalho | Middleware com suporte | Descrição |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Não                   | [Formato de dados compactados Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Não                   | [Formato de dados compactados DEFLATE](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Não                   | [Intercâmbio de eficiente de XML do W3C](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Sim (padrão)        | [Formato de arquivo gzip](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Sim                  | Identificador "Nenhuma codificação": A resposta não deve ser codificada. |
| `pack200-gzip`                  | Não                   | [Formato de transferência de rede para arquivos mortos de Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Sim                  | Qualquer conteúdo disponível não codificação explicitamente solicitada |

::: moniker-end

Para obter mais informações, consulte o [IANA lista oficial de conteúdo de codificação](http://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

O middleware permite que você adicione provedores de compactação adicional para custom `Accept-Encoding` valores de cabeçalho. Para obter mais informações, consulte [provedores personalizados](#custom-providers) abaixo.

O middleware é capaz de reagir a valor de qualidade (qvalue, `q`) quando enviado pelo cliente para priorizar os esquemas de compactação de ponderação. Para obter mais informações, consulte [RFC 7231: Codificação aceita](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Algoritmos de compactação estão sujeitos a uma compensação entre a velocidade de compactação e a eficiência da compactação. *Eficácia* neste contexto refere-se ao tamanho da saída após a compactação. O menor tamanho é obtido com a maioria *ideal* compactação.

Cabeçalhos envolvidas na solicitação, enviar, armazenamento em cache e receber conteúdo compactado são descritas na tabela a seguir.

| Cabeçalho             | Função |
| ------------------ | ---- |
| `Accept-Encoding`  | Enviada do cliente para o servidor para indicar a codificação esquemas aceitáveis para o cliente de conteúdo. |
| `Content-Encoding` | Enviados do servidor para o cliente para indicar a codificação do conteúdo na carga. |
| `Content-Length`   | Quando ocorre a compressão, a `Content-Length` cabeçalho for removido, desde que as alterações de conteúdo do corpo quando a resposta é compactada. |
| `Content-MD5`      | Quando ocorre a compressão, a `Content-MD5` cabeçalho for removido, uma vez que o conteúdo do corpo foi alterado e o hash não é mais válido. |
| `Content-Type`     | Especifica o tipo MIME do conteúdo. Cada resposta deve especificar seu `Content-Type`. O middleware verifica esse valor para determinar se a resposta deve ser compactada. O middleware Especifica um conjunto de [padrão de tipos MIME](#mime-types) que ele pode codificar, mas você pode substituir ou adicionar tipos MIME. |
| `Vary`             | Quando enviadas pelo servidor com um valor de `Accept-Encoding` para clientes e proxies, o `Vary` cabeçalho indica para o cliente ou um proxy que ele deve armazenar em cache (variar) respostas com base no valor da `Accept-Encoding` cabeçalho da solicitação. O resultado de retorno de conteúdo com o `Vary: Accept-Encoding` cabeçalho é que ambos compactados e descompactadas respostas são armazenadas em cache separadamente. |

Explore os recursos do Middleware de compactação de resposta com o [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples). O exemplo ilustra:

* A compactação de respostas do aplicativo usando o Gzip e provedores de compactação personalizado.
* Como adicionar um tipo de MIME para a lista padrão de tipos MIME para compactação.

## <a name="package"></a>Pacote

::: moniker range=">= aspnetcore-2.1"

Para incluir o middleware em um projeto, adicione uma referência para o [metapacote do Microsoft](xref:fundamentals/metapackage-app), que inclui o [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) pacote.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Para incluir o middleware em um projeto, adicione uma referência para o [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage), que inclui o [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) pacote.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Para incluir o middleware em um projeto, adicione uma referência para o [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) pacote.

::: moniker-end

## <a name="configuration"></a>Configuração

::: moniker range=">= aspnetcore-2.2"

O código a seguir mostra como habilitar o Middleware de compactação de resposta para provedores de compactação e tipos MIME padrão ([Brotli](#brotli-compression-provider) e [Gzip](#gzip-compression-provider)):

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O código a seguir mostra como habilitar o Middleware de compactação de resposta para tipos MIME padrão e o [provedor de compactação Gzip](#gzip-compression-provider):

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

> [!NOTE]
> Use uma ferramenta como [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), ou [Postman](https://www.getpostman.com/) para definir o `Accept-Encoding` cabeçalho de solicitação e estudar os cabeçalhos de resposta, o tamanho e o corpo.

Enviar uma solicitação para o aplicativo de exemplo sem o `Accept-Encoding` cabeçalho e observe que a resposta é descompactada. O `Content-Encoding` e `Vary` cabeçalhos não estiverem presentes na resposta.

![Janela do Fiddler mostrando o resultado de uma solicitação sem o cabeçalho Accept-Encoding. A resposta não é compactada.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

Enviar uma solicitação para o aplicativo de exemplo com o `Accept-Encoding: br` cabeçalho (a compactação Brotli) e observe que a resposta é compactada. O `Content-Encoding` e `Vary` cabeçalhos estão presentes na resposta.

![Janela do Fiddler mostrando o resultado de uma solicitação com o cabeçalho Accept-Encoding e um valor de br. Os cabeçalhos podem variar e codificação de conteúdo são adicionados à resposta. A resposta é compactada.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Enviar uma solicitação para o aplicativo de exemplo com o `Accept-Encoding: gzip` cabeçalho e observe que a resposta é compactada. O `Content-Encoding` e `Vary` cabeçalhos estão presentes na resposta.

![Janela do Fiddler mostrando o resultado de uma solicitação com o cabeçalho Accept-Encoding e um valor de gzip. Os cabeçalhos podem variar e codificação de conteúdo são adicionados à resposta. A resposta é compactada.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a>Provedores

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a>Provedor de compactação Brotli

Use o `BrotliCompressionProvider` para compactar respostas com o [formato de dados compactados Brotli](https://tools.ietf.org/html/rfc7932).

Se nenhum provedor de compactação é adicionados explicitamente a <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

* O provedor de compactação Brotli é adicionado por padrão para a matriz de provedores de compactação juntamente com o [provedor de compactação Gzip](#gzip-compression-provider).
* Padrões de compactação para a compactação Brotli quando o formato de dados compactados Brotli é suportado pelo cliente. Se Brotli não é suportada pelo cliente, a compactação padrão Gzip quando o cliente oferece suporte à compactação Gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

O provedor de compactação Brotoli devem ser adicionado ao quaisquer provedores de compactação são adicionados explicitamente:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

Definir a compactação de nível com `BrotliCompressionProviderOptions`. O provedor de compactação Brotli assume como padrão o nível de compactação mais rápido ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), que não pode produzir a compactação mais eficiente. Se a compactação mais eficiente é desejada, configure o middleware de compactação ideal.

| Nível de compactação | Descrição |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | A compactação deve ser concluída assim que possível, mesmo se a saída resultante não é compactada da maneira ideal. |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | Nenhuma compactação deve ser executada. |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | As respostas devem ser compactadas ideal, mesmo se a compactação leva mais tempo para concluir. |

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

### <a name="gzip-compression-provider"></a>Provedor de compactação gzip

Use o <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> para compactar respostas com o [formato de arquivo Gzip](https://tools.ietf.org/html/rfc1952).

Se nenhum provedor de compactação é adicionados explicitamente a <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

::: moniker range=">= aspnetcore-2.2"

* O provedor de compactação Gzip é adicionado por padrão para a matriz de provedores de compactação juntamente com o [provedor de compactação Brotli](#brotli-compression-provider).
* Padrões de compactação para a compactação Brotli quando o formato de dados compactados Brotli é suportado pelo cliente. Se Brotli não é suportada pelo cliente, a compactação padrão Gzip quando o cliente oferece suporte à compactação Gzip.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* O provedor de compactação Gzip é adicionado por padrão para a matriz de provedores de compactação.
* Padrões de compactação como Gzip, quando o cliente oferece suporte à compactação Gzip.

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

O provedor de compactação Gzip devem ser adicionado ao quaisquer provedores de compactação são adicionados explicitamente:

::: moniker range=">= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-2.1"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5)]

::: moniker-end

Definir a compactação de nível com <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>. O provedor de compactação Gzip assume como padrão o nível de compactação mais rápido ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), que não pode produzir a compactação mais eficiente. Se a compactação mais eficiente é desejada, configure o middleware de compactação ideal.

| Nível de compactação | Descrição |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | A compactação deve ser concluída assim que possível, mesmo se a saída resultante não é compactada da maneira ideal. |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | Nenhuma compactação deve ser executada. |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | As respostas devem ser compactadas ideal, mesmo se a compactação leva mais tempo para concluir. |

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

Criar implementações de compactação personalizado com <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>. O <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> representa o conteúdo de codificação que este `ICompressionProvider` produz. O middleware usa essas informações para escolher o provedor de acordo com a lista especificada no `Accept-Encoding` cabeçalho da solicitação.

Usando o aplicativo de exemplo, o cliente envia uma solicitação com o `Accept-Encoding: mycustomcompression` cabeçalho. O middleware usa a implementação de compactação personalizado e retorna a resposta com um `Content-Encoding: mycustomcompression` cabeçalho. O cliente deve ser capaz de descompactar a codificação personalizada para que uma implementação de compactação personalizado trabalhar.

::: moniker range=">= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

```csharp
public class CustomCompressionProvider : ICompressionProvider
{
    public string EncodingName => "mycustomcompression";
    public bool SupportsFlush => true;

    public Stream CreateStream(Stream outputStream)
    {
        // Create a custom compression stream wrapper here
        return outputStream;
    }
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-2.1"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=6,12-15)]

[!code-csharp[](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=6,12-15)]

[!code-csharp[](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

Enviar uma solicitação para o aplicativo de exemplo com o `Accept-Encoding: mycustomcompression` cabeçalho e observar os cabeçalhos de resposta. O `Vary` e `Content-Encoding` cabeçalhos estão presentes na resposta. O corpo de resposta (não mostrado) não será compactado pelo exemplo. Não existe uma implementação da compactação no `CustomCompressionProvider` classe do exemplo. No entanto, o exemplo mostra onde você poderia implementar tal um algoritmo de compactação.

![Janela do Fiddler mostrando o resultado de uma solicitação com o cabeçalho Accept-Encoding e um valor de mycustomcompression. Os cabeçalhos podem variar e codificação de conteúdo são adicionados à resposta.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>tipos MIME

O middleware Especifica um conjunto de tipos MIME para a compactação padrão:

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

Substituir ou acrescentar os tipos MIME com as opções de Middleware de compactação de resposta. Observe que curinga MIME tipos, como `text/*` não são suportados. O aplicativo de exemplo adiciona um tipo MIME `image/svg+xml` e compacta e serve o ASP.NET Core a imagem da faixa (*banner.svg*).

::: moniker range=">= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-2.1"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=7-9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7-9)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a>Compactação com protocolo seguro

Respostas compactadas através de conexões seguras podem ser controladas com o `EnableForHttps` opção, que é desabilitada por padrão. Usar a compactação com páginas geradas dinamicamente pode levar a problemas de segurança, como o [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) e [violação](https://wikipedia.org/wiki/BREACH_(security_exploit)) ataques.

## <a name="adding-the-vary-header"></a>Adicionando o cabeçalho Vary

::: moniker range=">= aspnetcore-2.0"

Quando a compactação de respostas com base no `Accept-Encoding` cabeçalho, há potencialmente várias versões compactadas de resposta e uma versão não compactada. Para instruir os caches de cliente e o proxy que várias versões existirem e devem ser armazenadas, o `Vary` cabeçalho é adicionado com um `Accept-Encoding` valor. No ASP.NET Core 2.0 ou posterior, o middleware adiciona o `Vary` cabeçalho automaticamente quando a resposta é compactada.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Quando a compactação de respostas com base no `Accept-Encoding` cabeçalho, há potencialmente várias versões compactadas de resposta e uma versão não compactada. Para instruir os caches de cliente e o proxy que várias versões existirem e devem ser armazenadas, o `Vary` cabeçalho é adicionado com um `Accept-Encoding` valor. No ASP.NET Core 1.x, adicionando o `Vary` cabeçalho à resposta é realizado manualmente:

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Problema de middleware quando atrás de um proxy reverso do Nginx

Quando uma solicitação é transmitida por proxy pelo Nginx, o `Accept-Encoding` cabeçalho é removido. Remoção do `Accept-Encoding` cabeçalho impede que o middleware de compactação de resposta. Para obter mais informações, consulte [NGINX: Compactação e descompactação](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Esse problema é acompanhado pelo [descobrir a compactação de passagem do Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Trabalhando com a compactação dinâmica do IIS

Se você tiver um Active Directory dinâmico compactação de módulo do IIS configurado no nível do servidor que você deseja desabilitar para um aplicativo, desabilite o módulo com uma adição à *Web. config* arquivo. Para obter mais informações, consulte [Desabilitando módulos do IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Solução de problemas

Use uma ferramenta como [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), ou [Postman](https://www.getpostman.com/), que permitem que você defina o `Accept-Encoding` cabeçalho de solicitação e estudar os cabeçalhos de resposta, o tamanho e o corpo. Por padrão, o Middleware de compactação de resposta compacta as respostas que atendem às seguintes condições:

::: moniker range=">= aspnetcore-2.2"

* O `Accept-Encoding` cabeçalho está presente com um valor de `br`, `gzip`, `*`, ou codificação personalizada que corresponde a um provedor de compactação personalizado estabelecida por você. O valor não deve ser `identity` ou tem um valor de qualidade (qvalue, `q`) configuração de 0 (zero).
* O tipo MIME (`Content-Type`) deve ser definido e deve corresponder a um tipo MIME configurado no <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.
* A solicitação não deve incluir o `Content-Range` cabeçalho.
* A solicitação deve usar protocolo inseguro (http), a menos que o protocolo seguro (https) é configurado nas opções de Middleware de compactação de resposta. *Observe o perigo [descritos acima](#compression-with-secure-protocol) ao habilitar a compactação de conteúdo segura.*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* O `Accept-Encoding` cabeçalho está presente com um valor de `gzip`, `*`, ou codificação personalizada que corresponde a um provedor de compactação personalizado estabelecida por você. O valor não deve ser `identity` ou tem um valor de qualidade (qvalue, `q`) configuração de 0 (zero).
* O tipo MIME (`Content-Type`) deve ser definido e deve corresponder a um tipo MIME configurado no <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.
* A solicitação não deve incluir o `Content-Range` cabeçalho.
* A solicitação deve usar protocolo inseguro (http), a menos que o protocolo seguro (https) é configurado nas opções de Middleware de compactação de resposta. *Observe o perigo [descritos acima](#compression-with-secure-protocol) ao habilitar a compactação de conteúdo segura.*

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Rede de desenvolvedor do Mozilla: Codificação aceita](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 seção 3.1.2.1: Conteúdo Codings](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 seção 4.2.3: A codificação gzip](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [Versão de especificação de formato de arquivo GZIP 4.3](http://www.ietf.org/rfc/rfc1952.txt)

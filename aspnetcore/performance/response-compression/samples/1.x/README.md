# <a name="response-compression-sample-application-aspnet-core-1x"></a>Aplicativo de exemplo de compactação de resposta (ASP.NET Core 1.x)

Este exemplo ilustra o uso do ASP.NET Core 1.x Middleware de compactação de resposta para compactar respostas HTTP para. O exemplo demonstra os provedores de compactação personalizado para respostas de texto e imagem e Gzip e mostra como adicionar um tipo MIME para compactação. Para o exemplo do ASP.NET Core 2.x, consulte [aplicativo de exemplo de compactação de resposta (ASP.NET Core 2.x)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x).

## <a name="examples-in-this-sample"></a>Exemplos desta amostra

* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Resposta de arquivo de texto Lorem Ipsum em 2,044 bytes que serão compactados em bytes 927
    * **/testfile1kb.txt** -resposta do arquivo de texto em bytes 1,033 que serão compactados em bytes 47
    * **/ surjam** -resposta emitido como caracteres únicos em intervalos de 1 segundo
  * `image/svg+xml`
    * **/banner.SVG** -resposta A Scalable Vector Graphics (SVG) da imagem em 9,707 bytes que serão compactados em bytes 4,459
* `CustomCompressionProvider`<br>Mostra como implementar um provedor de compactação personalizado para uso com o middleware

Quando a solicitação inclui o `Accept-Encoding` cabeçalho, o exemplo adiciona um `Vary: Accept-Encoding` cabeçalho à resposta. O `Vary` cabeçalho instrui caches manter várias cópias da resposta com base nos valores alternativos de `Accept-Encoding`, portanto, uma versão não compactada e compactado (gzip) são armazenados em caches de sistemas que podem aceitam o compactado ou o resposta não compactada.

## <a name="using-the-sample"></a>Usando o exemplo

1. Fazer uma solicitação usando [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), ou [Postman](https://www.getpostman.com/) para o aplicativo sem um `Accept-Encoding` cabeçalho e observe a carga de resposta, o tamanho da resposta, e cabeçalhos de resposta.
1. Adicionar um `Accept-Encoding: gzip` cabeçalho e observe o tamanho da resposta compactada e cabeçalhos de resposta. Você verá que o tamanho da resposta drop e o `Content-Encoding: gzip` cabeçalho de resposta é incluído pelo aplicativo de exemplo. Quando você examinar o corpo da resposta para o Lorem Ipsum ou **testfile1kb.txt** resposta, você verá que o texto é compactada e não pode ser lido.
1. Adicionar um `Accept-Encoding: mycustomcompression` cabeçalho e observe os cabeçalhos de resposta. O `CustomCompressionProvider` é uma implementação vazia que realmente não compactar a resposta, mas você pode criar um wrapper de fluxo de compactação personalizado para o `CreateStream()` método.

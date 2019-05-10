# <a name="response-compression-sample-application-aspnet-core-2x"></a>Aplicativo de exemplo de compactação de resposta (ASP.NET Core 2.x)

Este exemplo ilustra o uso do ASP.NET Core 2.x Middleware de compactação de resposta para compactar respostas HTTP. O exemplo demonstra como provedores de compactação personalizado para respostas de texto e imagem, Brotli e Gzip e mostra como adicionar um tipo MIME para compactação. Para o exemplo do ASP.NET Core 1.x, consulte [aplicativo de exemplo de compactação de resposta (ASP.NET Core 1.x)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/1.x).

## <a name="examples-in-this-sample"></a>Exemplos desta amostra

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum arquivo resposta de texto em bytes 2,044 que compacta a ~ 979 bytes.
    * **/testfile1kb.txt** -resposta de arquivo de texto em bytes 1,033 que compacta a ~ 36 bytes.
    * **/ surjam** -resposta emitido como caracteres únicos em intervalos de 1 segundo.
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum arquivo resposta de texto em bytes 2,044 que compacta a ~ 927 bytes.
    * **/testfile1kb.txt** -resposta de arquivo de texto em bytes 1,033 que compacta a ~ 47 bytes.
    * **/ surjam** -resposta emitido como caracteres únicos em intervalos de 1 segundo.
  * `image/svg+xml`
    * **/banner.SVG** -resposta de imagem de um Scalable Vector Graphics (SVG) em bytes 9,707 que compacta a ~ 4,459 bytes.
* `CustomCompressionProvider`<br>Mostra como implementar um provedor de compactação personalizado para uso com o middleware.

Quando a solicitação inclui o `Accept-Encoding` compactação de cabeçalho e a resposta for bem-sucedida, o middleware adiciona automaticamente um `Vary: Accept-Encoding` cabeçalho à resposta. O `Vary` cabeçalho instrui caches manter várias cópias da resposta com base nos valores alternativos de `Accept-Encoding`, portanto, ambos um compactado (Gzip ou Brotli) e versão não compactada são armazenados em caches de sistemas que podem aceitam o compactado ou descompactada resposta.

## <a name="use-the-sample"></a>Use o exemplo

1. Fazer uma solicitação usando [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), ou [Postman](https://www.getpostman.com/) para o aplicativo sem um `Accept-Encoding` cabeçalho e observe a carga de resposta, o tamanho da resposta, e cabeçalhos de resposta.
1. Adicionar um `Accept-Encoding: br` ou `Accept-Encoding: gzip` cabeçalho e observe o tamanho da resposta compactada e cabeçalhos de resposta. Descarta o tamanho da resposta e o `Content-Encoding` cabeçalho de resposta é incluído pelo middleware que indica que a compactação com qualquer um dos Gzip ou Brotli ocorreu. Quando você examinar o corpo da resposta para o Lorem Ipsum ou **testfile1kb.txt** resposta, você verá que o texto é compactada e não pode ser lido.
1. Adicionar um `Accept-Encoding: mycustomcompression` cabeçalho e observe os cabeçalhos de resposta. O `CustomCompressionProvider` é uma implementação vazia que realmente não compactar a resposta, mas você pode criar um wrapper de fluxo de compactação personalizado para o `CreateStream()` método.

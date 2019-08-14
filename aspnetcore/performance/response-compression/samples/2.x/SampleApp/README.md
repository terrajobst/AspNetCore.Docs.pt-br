# <a name="response-compression-sample-application-aspnet-core-2x"></a>Aplicativo de exemplo de compactação de resposta (ASP.NET Core 2. x)

Este exemplo ilustra o uso do middleware de compactação de resposta ASP.NET Core 2. x para compactar respostas HTTP. O exemplo demonstra os provedores de compactação Gzip, Brotli e Custom para respostas de texto e imagem e mostra como adicionar um tipo MIME para compactação. Para o exemplo ASP.NET Core 1. x, consulte [aplicativo de exemplo de compactação de resposta (ASP.NET Core 1. x)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/1.x).

## <a name="examples-in-this-sample"></a>Exemplos desta amostra

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -O Lorem Ipsum resposta de arquivo de texto a 2.044 bytes que compacta até ~ 979 bytes.
    * **/testfile1kb.txt** -resposta do arquivo de texto em 1.033 bytes que compacta até ~ 36 bytes.
    * **/trickle** -resposta emitida como caracteres únicos em intervalos de 1 segundo.
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -O Lorem Ipsum resposta de arquivo de texto a 2.044 bytes que compacta até ~ 927 bytes.
    * **/testfile1kb.txt** -resposta do arquivo de texto em 1.033 bytes que compacta até ~ 47 bytes.
    * **/trickle** -resposta emitida como caracteres únicos em intervalos de 1 segundo.
  * `image/svg+xml`
    * **/banner.svg** -uma resposta de imagem SVG (gráficos vetoriais escalonáveis) em 9.707 bytes que compacta até ~ 4.459 bytes.
* `CustomCompressionProvider`<br>Mostra como implementar um provedor de compactação personalizado para uso com o middleware.

Quando a solicitação inclui o `Accept-Encoding` cabeçalho e a compactação de resposta é bem-sucedida, o middleware adiciona `Vary: Accept-Encoding` automaticamente um cabeçalho à resposta. O `Vary` cabeçalho instrui os caches para manter várias cópias da resposta com base em valores alternativos de `Accept-Encoding`, para que uma versão compactada (gzip ou Brotli) e não compactada seja armazenada em caches para sistemas que podem aceitar o resposta compactada ou não compactada.

## <a name="use-the-sample"></a>Usar o exemplo

1. Faça uma solicitação usando [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)ou [postmaster](https://www.getpostman.com/) `Accept-Encoding` para o aplicativo sem cabeçalho e anote a carga da resposta, o tamanho da resposta e os cabeçalhos de resposta.
1. Adicione um `Accept-Encoding: br` cabeçalho `Accept-Encoding: gzip` ou e observe o tamanho de resposta compactado e os cabeçalhos de resposta. O tamanho da resposta cai e o `Content-Encoding` cabeçalho de resposta é incluído pelo middleware indicando que a compactação com gzip ou Brotli ocorreu. Ao examinar o corpo da resposta da resposta Lorem Ipsum ou **testfile1kb. txt** , você verá que o texto está compactado e ilegível.
1. Adicione um `Accept-Encoding: mycustomcompression` cabeçalho e observe os cabeçalhos de resposta. A `CustomCompressionProvider` é uma implementação vazia que, na verdade, não compacta a resposta, mas você pode criar um wrapper de fluxo `CreateStream()` de compactação personalizado para o método.

# <a name="response-compression-sample-application-aspnet-core-3x"></a>Aplicativo de exemplo de compactação de resposta (ASP.NET Core 3. x)

Este exemplo ilustra o uso do ASP.NET Core o middleware de compactação de resposta 3. x para compactar respostas HTTP. O exemplo demonstra os provedores de compactação Gzip, Brotli e Custom para respostas de texto e imagem e mostra como adicionar um tipo MIME para compactação. Para o exemplo de ASP.NET Core 2. x, consulte [aplicativo de exemplo de compactação de resposta (ASP.NET Core 2. x)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x).

## <a name="examples-in-this-sample"></a>Exemplos desta amostra

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum a resposta do arquivo de texto em 2.044 bytes que é compactado em aproximadamente 979 bytes.
    * **/testfile1kb.txt** -resposta do arquivo de texto em 1.033 bytes que compacta até ~ 36 bytes.
    * **/trickle** -resposta emitida como caracteres únicos em intervalos de 1 segundo.
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum a resposta do arquivo de texto em 2.044 bytes que é compactado em aproximadamente 927 bytes.
    * **/testfile1kb.txt** -resposta do arquivo de texto em 1.033 bytes que compacta até ~ 47 bytes.
    * **/trickle** -resposta emitida como caracteres únicos em intervalos de 1 segundo.
  * `image/svg+xml`
    * **/banner.svg** -uma resposta de imagem SVG (gráficos vetoriais escalonáveis) em 9.707 bytes que compacta até ~ 4.459 bytes.
* `CustomCompressionProvider`<br>Mostra como implementar um provedor de compactação personalizado para uso com o middleware.

Quando a solicitação inclui o cabeçalho de `Accept-Encoding` e a compactação de resposta é bem-sucedida, o middleware adiciona automaticamente um cabeçalho de `Vary: Accept-Encoding` à resposta. O cabeçalho `Vary` instrui os caches a manterem várias cópias da resposta com base em valores alternativos de `Accept-Encoding`, para que uma versão compactada (gzip ou Brotli) e não compactada seja armazenada em caches para sistemas que possam aceitar a resposta compactada ou descompactada.

## <a name="use-the-sample"></a>Usar o exemplo

1. Faça uma solicitação usando o [Fiddler](https://www.telerik.com/fiddler), o [Firebug](https://getfirebug.com/)ou o [postmaster](https://www.getpostman.com/) para o aplicativo sem um cabeçalho `Accept-Encoding` e observe o conteúdo da resposta, o tamanho da resposta e os cabeçalhos de resposta.
1. Adicione um cabeçalho `Accept-Encoding: br` ou `Accept-Encoding: gzip` e observe o tamanho da resposta compactada e os cabeçalhos de resposta. O tamanho da resposta cai e o cabeçalho de resposta `Content-Encoding` é incluído pelo middleware indicando que a compactação com gzip ou Brotli ocorreu. Ao examinar o corpo da resposta da resposta Lorem Ipsum ou **testfile1kb. txt** , você verá que o texto está compactado e ilegível.
1. Adicione um cabeçalho de `Accept-Encoding: mycustomcompression` e observe os cabeçalhos de resposta. A `CustomCompressionProvider` é uma implementação vazia que, na verdade, não compacta a resposta, mas você pode criar um wrapper de fluxo de compactação personalizado para o método `CreateStream()`.

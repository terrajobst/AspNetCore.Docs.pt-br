---
title: Arquivos estáticos no ASP.NET Core
author: rick-anderson
description: Saiba como fornecer e proteger arquivos estáticos e configurar comportamentos do middleware de hospedagem de arquivos estáticos em um aplicativo Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/static-files
ms.openlocfilehash: b989b90100318ac874dc399daf65ef7d21c5549f
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799477"
---
# <a name="static-files-in-aspnet-core"></a>Arquivos estáticos no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Scott Addie](https://twitter.com/Scott_Addie)

Arquivos estáticos, como HTML, CSS, imagens e JavaScript, são ativos que um aplicativo ASP.NET Core fornece diretamente para os clientes. Algumas etapas de configuração são necessárias para habilitar o fornecimento desses arquivos.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Fornecer arquivos estáticos

Os arquivos estáticos são armazenados no diretório [raiz da Web](xref:fundamentals/index#web-root) do projeto. O diretório padrão é *{Content root}/wwwroot*, mas pode ser alterado por meio do método [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) . Consulte [Raiz de conteúdo](xref:fundamentals/index#content-root) e [Diretório base](xref:fundamentals/index#web-root) para obter mais informações.

O host Web do aplicativo deve ser informado do diretório raiz do conteúdo.

::: moniker range=">= aspnetcore-2.0"

O método `WebHost.CreateDefaultBuilder` define a raiz do conteúdo como o diretório atual:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Defina a raiz do conteúdo com o diretório atual invocando [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) dentro de `Program.Main`:

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

Os arquivos estáticos podem ser acessados por meio de um caminho relativo à [raiz da Web](xref:fundamentals/index#web-root). Por exemplo, o modelo de projeto do **Aplicativo Web** contém várias pastas dentro da pasta *wwwroot*:

* **wwwroot**
  * **css**
  * **images**
  * **js**

O formato de URI para acessar um arquivo na subpasta *images* é *http://\<server_address>/images/\<image_file_name>* . Por exemplo, *http://localhost:9189/images/banner3.svg* .

::: moniker range=">= aspnetcore-2.1"

Se estiver direcionando o .NET Framework, adicione o pacote [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto. Se você estiver direcionando para o .NET Core, o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluirá esse pacote.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Se estiver direcionando o .NET Framework, adicione o pacote [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto. Se estiver direcionando o .NET Core, o [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage) incluirá esse pacote.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Adicione o pacote [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto.

::: moniker-end

Configure o [middleware](xref:fundamentals/middleware/index) que permite o fornecimento de arquivos estáticos.

### <a name="serve-files-inside-of-web-root"></a>Fornecer arquivos dentro do diretório base

Invoque o método [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dentro de `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

A sobrecarga do método `UseStaticFiles` sem parâmetros marca os arquivos na [raiz da Web](xref:fundamentals/index#web-root) como servable. A seguinte marcação referencia *wwwroot/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

No código anterior, o caractere til `~/` aponta para a [raiz da Web](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Fornecer arquivos fora do diretório base

Considere uma hierarquia de diretório na qual os arquivos estáticos a serem servidos residem fora da [raiz da Web](xref:fundamentals/index#web-root):

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*

Uma solicitação pode acessar o arquivo *banner1.svg* configurando o middleware de arquivos estáticos da seguinte maneira:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

No código anterior, a hierarquia de diretórios *MyStaticFiles* é exposta publicamente por meio do segmento do URI *StaticFiles*. Uma solicitação para *http://\<server_address>/StaticFiles/images/banner1.svg* atende ao arquivo *banner1.svg*.

A seguinte marcação referencia *MyStaticFiles/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Definir cabeçalhos de resposta HTTP

Um objeto [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) pode ser usado para definir cabeçalhos de resposta HTTP. Além de configurar o serviço de arquivos estáticos na [raiz da Web](xref:fundamentals/index#web-root), o código a seguir define o cabeçalho `Cache-Control`:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]

O método [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) existe no pacote [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).

Os arquivos se tornaram armazenáveis em cache publicamente por 10 minutos (600 segundos) no ambiente de desenvolvimento:

![Cabeçalho de resposta mostrando que o cabeçalho Cache-Control foi adicionado](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorização de arquivo estático

O middleware de arquivos estáticos não fornece verificações de autorização. Todos os arquivos atendidos, incluindo aqueles em *wwwroot*, estão acessíveis publicamente. Para fornecer arquivos com base na autorização:

* Armazene-os fora do *wwwroot* e de qualquer diretório acessível ao middleware de arquivos estáticos.
* Forneça-os por meio de um método de ação ao qual a autorização é aplicada. Retorne um objeto [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult):

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Habilitar navegação no diretório

A navegação no diretório permite que os usuários do aplicativo Web vejam uma listagem de diretório e arquivos em um diretório especificado. A navegação no diretório está desabilitada por padrão por motivos de segurança (consulte [Considerações](#considerations)). Habilite a navegação no diretório invocando o método [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) em `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Adicione os serviços necessários invocando o método [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) em `Startup.ConfigureServices`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

O código anterior permite a navegação no diretório da pasta *wwwroot/images* usando a URL *http://\<server_address>/MyImages*, com links para cada arquivo e pasta:

![navegação no diretório](static-files/_static/dir-browse.png)

Consulte [Considerações](#considerations) para saber mais sobre os riscos de segurança ao habilitar a navegação.

Observe as duas chamadas `UseStaticFiles` no exemplo a seguir. A primeira chamada permite o fornecimento de arquivos estáticos na pasta *wwwroot*. A segunda chamada habilita a navegação no diretório da pasta *wwwroot/images* usando a URL *http://\<server_address>/MyImages*:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Fornecer um documento padrão

Definir uma home page padrão fornece ao visitantes um ponto de partida lógico de ao visitar o site. Para fornecer uma página padrão sem qualificar totalmente o URI do usuário, chame o [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) método de `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles` deve ser chamado antes de `UseStaticFiles` para fornecer o arquivo padrão. `UseDefaultFiles` é um rewriter de URL que, na verdade, não fornece o arquivo. Habilite o middleware de arquivos estáticos por meio de `UseStaticFiles` para fornecer o arquivo.

Com `UseDefaultFiles`, as solicitações para uma pasta pesquisam:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

O primeiro arquivo encontrado na lista é fornecido como se a solicitação fosse o URI totalmente qualificado. A URL do navegador continua refletindo o URI solicitado.

O seguinte código altera o nome de arquivo padrão para *mydefault.html*:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina a funcionalidade de `UseStaticFiles`, `UseDefaultFiles`e, opcionalmente, `UseDirectoryBrowser`.

O código a seguir permite o fornecimento de arquivos estáticos e do arquivo padrão. A navegação no diretório não está habilitada.

```csharp
app.UseFileServer();
```

O seguinte código baseia-se na sobrecarga sem parâmetros, habilitando a navegação no diretório:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Considere a seguinte hierarquia de diretórios:

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

O seguinte código habilita arquivos estáticos, arquivos padrão e a navegação no diretório de `MyStaticFiles`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser` precisa ser chamado quando o valor da propriedade `EnableDirectoryBrowsing` é `true`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Com o uso da hierarquia de arquivos e do código anterior, as URLs são resolvidas da seguinte maneira:

| URI            |                             Resposta  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/StaticFiles*             |     MyStaticFiles/default.html |

Se nenhum arquivo nomeado como padrão existir no diretório *MyStaticFiles*, *http://\<server_address>/StaticFiles* retornará a listagem de diretórios com links clicáveis:

![Lista de arquivos estáticos](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> e <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> executam um redirecionamento do lado do cliente de `http://{SERVER ADDRESS}/StaticFiles` (sem uma barra à direita) para `http://{SERVER ADDRESS}/StaticFiles/` (com uma barra à direita). As URLs relativas dentro do diretório *StaticFiles* são inválidas sem uma barra "\" à direita.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

A classe [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) contém uma propriedade `Mappings` que serve como um mapeamento de extensões de arquivo para tipos de conteúdo MIME. Na amostra a seguir, várias extensões de arquivo são registradas para tipos MIME conhecidos. A extensão *.rtf* é substituída, e *.mp4* é removida.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Consulte [Tipos de conteúdo MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Tipos de conteúdo não padrão

O middleware de arquivo estático compreende quase 400 tipos de conteúdo de arquivo conhecidos. Se o usuário solicitar um arquivo com um tipo desconhecido, o middleware de arquivo estático passará a solicitação para o próximo middleware no pipeline. Se nenhum middleware manipular a solicitação, uma resposta *404 Não Encontrado* será retornada. Se a navegação no diretório estiver habilitada, um link para o arquivo será exibido na lista do diretório.

O seguinte código habilita o fornecimento de tipos desconhecidos e renderiza o arquivo desconhecido como uma imagem:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

Com o código anterior, uma solicitação para um arquivo com um tipo de conteúdo desconhecido é retornada como uma imagem.

> [!WARNING]
> A habilitação de [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) é um risco de segurança. Ela está desabilitada por padrão, e seu uso não é recomendado. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fornece uma alternativa mais segura para o fornecimento de arquivos com extensões não padrão.

### <a name="considerations"></a>Considerações

> [!WARNING]
> `UseDirectoryBrowser` e `UseStaticFiles` podem causar a perda de segredos. A desabilitação da navegação no diretório em produção é altamente recomendada. Examine com atenção os diretórios que são habilitados por meio de `UseStaticFiles` ou `UseDirectoryBrowser`. Todo o diretório e seus subdiretórios se tornam publicamente acessíveis. Armazene arquivos adequados para fornecimento ao público em um diretório dedicado, como *\<content_root>/wwwroot*. Separe esses arquivos das exibições MVC, Páginas do Razor (somente 2.x), arquivos de configuração, etc.

* As URLs para o conteúdo exposto com `UseDirectoryBrowser` e `UseStaticFiles` estão sujeitas à diferenciação de maiúsculas e minúsculas e a restrições de caracteres do sistema de arquivos subjacente. Por exemplo, o Windows diferencia maiúsculas de minúsculas, o macOS e o Linux não.

* Os aplicativos ASP.NET Core hospedados no IIS usam o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para encaminhar todas as solicitações ao aplicativo, inclusive as solicitações de arquivo estático. O manipulador de arquivos estáticos do IIS não é usado. Ele não tem nenhuma possibilidade de manipular as solicitações antes que elas sejam manipuladas pelo módulo.

* Conclua as etapas seguinte no Gerenciador do IIS para remover o manipulador de arquivos estáticos no IIS no nível do servidor ou do site:
    1. Navegue para o recurso **Módulos**.
    1. Selecione **StaticFileModule** na lista.
    1. Clique em **Remover** na barra lateral **Ações**.

> [!WARNING]
> Se o manipulador de arquivo estático do IIS estiver habilitado **e** o Módulo do ASP.NET Core não estiver configurado corretamente, os arquivos estáticos serão atendidos. Isso acontece, por exemplo, se o arquivo *web.config* não foi implantado.

* Coloque os arquivos de código (incluindo *. cs* e *. cshtml*) fora da [raiz da Web](xref:fundamentals/index#web-root)do projeto de aplicativo. Portanto, uma separação lógica é criada entre o conteúdo do lado do cliente do aplicativo e o código baseado em servidor. Isso impede a perda de código do lado do servidor.

## <a name="additional-resources"></a>Recursos adicionais

* [Middleware](xref:fundamentals/middleware/index)
* [Introdução ao ASP.NET Core](xref:index)

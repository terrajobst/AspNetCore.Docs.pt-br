---
uid: mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view
title: Examinando os métodos de edição e o modo de exibição editar | Microsoft Docs
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 01/06/2019
ms.assetid: 52a4d5fe-aa31-4471-b3cb-a064f82cb791
msc.legacyurl: /mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view
msc.type: authoredcontent
ms.openlocfilehash: 75fd3a7dd55107cbdb9095d5b54b616133b4f65e
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099390"
---
<a name="examining-the-edit-methods-and-edit-view"></a>Examinando os métodos de edição e exibição de edição
====================
por [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

Nesta seção, você examinará o gerado `Edit` métodos de ação e modos de exibição para o controlador de filmes. Mas primeiro, levará um breve desvio para fazer com que a data de lançamento parecer melhor. Abra o *Models\Movie.cs* arquivo e adicione as linhas realçadas mostradas abaixo:

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample1.cs?highlight=2,12-14)]

Você também pode tornar a cultura da data específica, como este:

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample2.cs?highlight=3)]

Abordaremos [DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) no próximo tutorial. O atributo [Display](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayattribute.aspx) especifica o que deve ser exibido no nome de um campo (neste caso, “Release Date” em vez de “ReleaseDate”). O [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atributo especifica o tipo de dados, nesse caso, é uma data, portanto, as informações de hora armazenadas no campo não são exibidas. O [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) atributo é necessário para um bug no navegador Chrome que renderiza os formatos de data incorretamente.

Execute o aplicativo e navegue até o `Movies` controlador. Mantenha o ponteiro do mouse sobre um **editar** link para ver a URL para o qual ela está vinculada.

![EditLink_sm](examining-the-edit-methods-and-edit-view/_static/image1.png)

O **edite** link foi gerado pelo `Html.ActionLink` método na *Views\Movies\Index.cshtml* exibição:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample3.cshtml)]

![Html.ActionLink](examining-the-edit-methods-and-edit-view/_static/image2.png)

O `Html` objeto é um auxiliar exposto usando uma propriedade sobre a [System.Web.Mvc.WebViewPage](https://msdn.microsoft.com/library/gg402107(VS.98).aspx) classe base. O `ActionLink` método do auxiliar de torna mais fácil gerar dinamicamente os hiperlinks HTML que vinculam a métodos de ação nos controladores. O primeiro argumento para o `ActionLink` método é para renderizar o texto do link (por exemplo, `<a>Edit Me</a>`). O segundo argumento é o nome do método de ação ser invocado (nesse caso, o `Edit` ação). O argumento final é um [objeto anônimo](https://weblogs.asp.net/scottgu/archive/2007/05/15/new-orcas-language-feature-anonymous-types.aspx) que gera os dados de rota (nesse caso, a ID de 4).

O link gerado mostrado na imagem anterior é `http://localhost:1234/Movies/Edit/4`. A rota padrão (estabelecido nos *App\_Start\RouteConfig.cs*) usa o padrão de URL `{controller}/{action}/{id}`. Portanto, o ASP.NET converte `http://localhost:1234/Movies/Edit/4` em uma solicitação para o `Edit` método de ação a `Movies` controlador com o parâmetro `ID` igual a 4. Examinar o código a seguir da *App\_Start\RouteConfig.cs* arquivo. O [MapRoute](../../older-versions-1/controllers-and-routing/asp-net-mvc-routing-overview-cs.md) método é usado para rotear solicitações HTTP para o método de controlador e ação correto e fornecer o parâmetro ID opcional. O [MapRoute](../../older-versions-1/controllers-and-routing/asp-net-mvc-routing-overview-cs.md) método também é usado pelas [HtmlHelpers](https://msdn.microsoft.com/library/system.web.mvc.htmlhelper(v=vs.108).aspx) como `ActionLink` para gerar URLs, considerando o controlador, o método de ação e quaisquer dados de rota.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample4.cs?highlight=7)]

Você também pode passar parâmetros de método de ação usando uma cadeia de caracteres de consulta. Por exemplo, a URL `http://localhost:1234/Movies/Edit?ID=3` também passa o parâmetro `ID` de 3 para o `Edit` método de ação do `Movies` controlador.

![EditQueryString](examining-the-edit-methods-and-edit-view/_static/image3.png)

Abra o `Movies` controlador. Os dois `Edit` métodos de ação são mostrados abaixo.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample5.cs?highlight=19-21)]

Observe se o segundo método de ação `Edit` é precedido pelo atributo `HttpPost`. Esse atributo especifica que a sobrecarga da `Edit` método pode ser chamado somente para as solicitações POST. Você pode aplicar o `HttpGet` editar o atributo para o primeiro método, mas isso não é necessário porque é o padrão. (Vamos nos referir aos métodos de ação são implicitamente atribuídos a `HttpGet` do atributo como `HttpGet` métodos.) O [associar](https://msdn.microsoft.com/library/system.web.mvc.bindattribute(v=vs.108).aspx) atributo é outro mecanismo de segurança importantes que impede que os hackers de overposting dados ao seu modelo. Você deve incluir apenas as propriedades no atributo de associação que você deseja alterar. Você pode ler sobre excesso de postagem e o atributo bind no meu [Observação de segurança de excesso de postagem](https://go.microsoft.com/fwlink/?LinkId=317598). No modelo simples usado neste tutorial, haverá a ligação todos os dados no modelo. O [ValidateAntiForgeryToken](https://msdn.microsoft.com/library/system.web.mvc.validateantiforgerytokenattribute(v=vs.108).aspx) atributo é usado para evitar a falsificação de uma solicitação e é associado a `@Html.AntiForgeryToken()` no arquivo de exibição de edição (*Views\Movies\Edit.cshtml*), uma parte é mostrada abaixo:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample6.cshtml?highlight=9)]

`@Html.AntiForgeryToken()` gera um token antifalsificação de formulário oculto que deve ser iguais a `Edit` método da `Movies` controlador. Você pode ler mais sobre Cross-site solicitação forjada (também conhecida como XSRF ou CSRF) no meu tutorial [prevenção de XSRF/CSRF no MVC](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md).

O `HttpGet` `Edit` método utiliza o parâmetro de ID de filme, pesquisa o filme usando o Entity Framework `Find` método e retorna o filme selecionado para a exibição de edição. Se não for encontrado um filme, [HttpNotFound](https://msdn.microsoft.com/library/gg453938(VS.98).aspx) é retornado. Quando o sistema de scaffolding criou a exibição de Edição, ele examinou a classe `Movie` e o código criado para renderizar os elementos `<label>` e `<input>` de cada propriedade da classe. O exemplo a seguir mostra a exibição de edição que foi gerada pelo sistema de scaffolding do visual studio:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample7.cshtml)]

Observe como o modelo de exibição tem um `@model MvcMovie.Models.Movie` instrução na parte superior do arquivo — Isso especifica que a exibição espera que o modelo para o modelo de exibição ser do tipo `Movie`.

O código gerado por scaffolding usa vários *métodos auxiliares* para simplificar a marcação HTML. O [ `Html.LabelFor` ](https://msdn.microsoft.com/library/gg401864(VS.98).aspx) auxiliar exibe o nome do campo (&quot;título&quot;, &quot;ReleaseDate&quot;, &quot;gênero&quot;, ou &quot;preço &quot;). O [ `Html.EditorFor` ](https://msdn.microsoft.com/library/system.web.mvc.html.editorextensions.editorfor(VS.98).aspx) auxiliar renderiza uma marca HTML `<input>` elemento. O [ `Html.ValidationMessageFor` ](https://msdn.microsoft.com/library/system.web.mvc.html.validationextensions.validationmessagefor(VS.98).aspx) auxiliar exibe quaisquer mensagens de validação associadas com aquela propriedade.

Execute o aplicativo e navegue até a */Movies* URL. Clique em um link **Editar**. No navegador, exiba a origem da página. O HTML para o elemento de formulário é mostrado abaixo.

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample8.cshtml?highlight=1-2)]

O `<input>` elementos estão em um HTML `<form>` elemento cujo `action` atributo é definido para postar a */Movies/Edit* URL. Os dados de formulário serão postados no servidor quando o **salvar** botão é clicado. A segunda linha mostra o oculto [XSRF](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) token gerado pela `@Html.AntiForgeryToken()` chamar.

## <a name="processing-the-post-request"></a>Processando a solicitação POST

A lista a seguir mostra a versão `HttpPost` do método de ação `Edit`.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample9.cs)]

O [ValidateAntiForgeryToken](https://msdn.microsoft.com/library/system.web.mvc.validateantiforgerytokenattribute(v=vs.108).aspx) atributo valida a [XSRF](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) token gerado pela `@Html.AntiForgeryToken()` chamar no modo de exibição.

O [associador de modelo ASP.NET MVC](https://msdn.microsoft.com/library/dd410405.aspx) usa os valores de formulário postados e cria um `Movie` objeto que é passado como o `movie` parâmetro. O `ModelState.IsValid` verifica se os dados enviados no formulário podem ser usados para modificar (Editar ou atualizar) um `Movie` objeto. Se os dados forem válidos, os dados do filme será salvo a `Movies` coleção do `db`(`MovieDBContext` instância). Os novos dados de filme é salvo no banco de dados chamando o `SaveChanges` método de `MovieDBContext`. Depois de salvar os dados, o código redireciona o usuário para o método de ação `Index` da classe `MoviesController`, que exibe a coleção de filmes, incluindo as alterações feitas recentemente.

Assim que a validação do lado do cliente determina que o valor de um campo não é válido, uma mensagem de erro é exibida. Se o JavaScript está desabilitado, a validação do lado do cliente está desabilitada. No entanto, o servidor detecta os valores postados não são válidos e os valores de formulário são exibida novamente com mensagens de erro.

Validação é examinada em detalhes mais adiante no tutorial.

O `Html.ValidationMessageFor` auxiliares na *Edit. cshtml* exibir modelo cuidar de exibir mensagens de erro apropriado.

![abcNotValid](examining-the-edit-methods-and-edit-view/_static/image4.png)

Todos os `HttpGet` métodos seguem um padrão semelhante. Obtêm um objeto de filme (ou uma lista de objetos, no caso de `Index`) e passar o modelo para o modo de exibição. O `Create` método passa um objeto de filme vazio para o modo de exibição de criar. Todos os métodos que criam, editam, excluem ou, de outro modo, modificam dados fazem isso na sobrecarga `HttpPost` do método. Modificando dados em um método HTTP GET é um risco de segurança, conforme descrito na entrada de postagem de blog [ASP.NET MVC dica n º 46 – não use Excluir Links porque eles criaram buracos na segurança](http://stephenwalther.com/blog/archive/2009/01/21/asp.net-mvc-tip-46-ndash-donrsquot-use-delete-links-because.aspx). Modificando dados em um método GET também viola as práticas recomendadas HTTP e a arquitetura [REST](http://en.wikipedia.org/wiki/Representational_State_Transfer) padrão, que especifica que as solicitações GET não devem alterar o estado do seu aplicativo. Em outras palavras, a execução de uma operação GET deve ser uma operação segura que não tem efeitos colaterais e não modifica os dados persistentes.

## <a name="jquery-validation-for-non-english-locales"></a>validação do jQuery para idiomas diferentes do inglês

Se você estiver usando um computador de inglês dos EUA, você pode ignorar esta seção e vá para o próximo tutorial. Você pode baixar a versão de Globalize deste tutorial [aqui](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=16475). Para obter um tutorial de duas partes excelente sobre internacionalização, consulte [ASP.NET MVC 5 internacionalização do Nadeem](http://afana.me/post/aspnet-mvc-internationalization.aspx).


> [!NOTE]
> para dar suporte a validação do jQuery para idiomas diferentes do inglês que usam uma vírgula (&quot;,&quot;) para um ponto decimal e formatos de data do inglês dos EUA, você deve incluir *globalize.js* seu específicas e  *cultures/globalize.cultures.js* arquivo (do [ https://github.com/jquery/globalize ](https://github.com/jquery/globalize) ) e JavaScript para usar `Globalize.parseFloat`. Você pode obter a validação do jQuery diferente do inglês do NuGet. (Não instale Globalize se você estiver usando uma localidade em inglês.)

1. Dos **ferramentas** menu, clique em **Gerenciador de pacotes NuGet**e, em seguida, clique em **gerenciar pacotes NuGet para solução**.

    ![](examining-the-edit-methods-and-edit-view/_static/image5.png)
2. No painel esquerdo, selecione <strong>navegue *.</strong>* (Veja a imagem abaixo).
3. Na caixa de entrada, insira * Globalize * *.

    ![](examining-the-edit-methods-and-edit-view/_static/image6.png) Escolher `jQuery.Validation.Globalize`, escolha `MvcMovie` e clique em **instalar**. O *Scripts\jquery.globalize\globalize.js* arquivo será adicionado ao seu projeto. O *Scripts\jquery.globalize\cultures\* pasta conterá muitos arquivos JavaScript de cultura. Observe que pode levar cinco minutos para instalar este pacote.

   O código a seguir mostra as modificações no arquivo Views\Movies\Edit.cshtml:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample10.cshtml)]

Para evitar repetir este código em cada exibição de edição, você pode movê-lo para o arquivo de layout. Para otimizar o download do script, consulte o tutorial de minha [agrupamento e Minificação](../../performance/bundling-and-minification.md).

Para obter mais informações, consulte [internacionalização do ASP.NET MVC 3](http://afana.me/post/aspnet-mvc-internationalization.aspx) e [internacionalização de 3 do ASP.NET MVC – parte 2 (NerdDinner)](http://afana.me/post/aspnet-mvc-internationalization-part-2.aspx).

Como uma correção temporária, se você não conseguir validação trabalhando em sua localidade, você pode forçar o computador para usar o inglês dos EUA, ou você pode desabilitar o JavaScript no navegador. Para forçar o computador para usar o inglês dos EUA, você pode adicionar o elemento de globalização para a raiz de projetos *Web. config* arquivo. O código a seguir mostra o elemento de globalização com a cultura definida como inglês dos Estados Unidos.

[!code-xml[Main](examining-the-edit-methods-and-edit-view/samples/sample11.xml)]

<a id="gettingstarted"></a><a id="jQueryAjaxJSON"></a> No próximo tutorial, implementaremos a funcionalidade de pesquisa.

> [!div class="step-by-step"]
> [Anterior](accessing-your-models-data-from-a-controller.md)
> [Próximo](adding-search.md)

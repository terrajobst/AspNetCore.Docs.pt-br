---
title: Migrar do ASP.NET MVC para o ASP.NET Core MVC
author: ardalis
description: Saiba como começar a migrar um projeto MVC do ASP.NET para ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: 6c9449fb43960d05db8aa6dcba64d3d830834cdb
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371878"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migrar do ASP.NET MVC para o ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)e [Scott Addie](https://scottaddie.com)

Este artigo mostra como começar a migração de um projeto MVC ASP.NET para [ASP.NET Core MVC](../mvc/overview.md). No processo, ele realça muitas das coisas que foram alteradas do ASP.NET MVC. A migração do ASP.NET MVC é um processo de várias etapas e este artigo aborda a configuração inicial, os controladores básicos e as exibições, o conteúdo estático e as dependências do lado do cliente. Artigos adicionais abordam a migração de configuração e o código de identidade encontrados em muitos projetos do ASP.NET MVC.

> [!NOTE]
> Os números de versão nos exemplos podem não ser atuais. Talvez seja necessário atualizar seus projetos adequadamente.

## <a name="create-the-starter-aspnet-mvc-project"></a>Criar o projeto do MVC do ASP.NET do início

Para demonstrar a atualização, começaremos criando um aplicativo MVC ASP.NET. Crie-o com o nome *WebApp1* para que o namespace corresponda ao projeto ASP.NET Core que criamos na próxima etapa.

![Caixa de diálogo novo projeto do Visual Studio](mvc/_static/new-project.png)

![Caixa de diálogo novo aplicativo Web: Modelo de projeto MVC selecionado no painel modelos de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Adicional* Altere o nome da solução de *WebApp1* para *Mvc5*. O Visual Studio exibe o nome da nova solução (*Mvc5*), o que torna mais fácil dizer a esse projeto do próximo projeto.

## <a name="create-the-aspnet-core-project"></a>Criar o projeto de ASP.NET Core

Crie um novo aplicativo Web *vazio* ASP.NET Core com o mesmo nome do projeto anterior (*WebApp1*) para que os namespaces nos dois projetos correspondam. Ter o mesmo namespace torna mais fácil copiar o código entre os dois projetos. Você precisará criar esse projeto em um diretório diferente do projeto anterior para usar o mesmo nome.

![Caixa de diálogo Novo Projeto](mvc/_static/new_core.png)

![Caixa de diálogo novo aplicativo Web ASP.NET: Modelo de projeto vazio selecionado no painel modelos de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Adicional* Crie um novo aplicativo ASP.NET Core usando o modelo de projeto de *aplicativo Web* . Nomeie o projeto *WebApp1*e selecione uma opção de autenticação de **contas de usuário individuais**. Renomeie este aplicativo como *FullAspNetCore*. A criação desse projeto economiza tempo na conversão. Você pode examinar o código gerado por modelo para ver o resultado final ou copiar o código para o projeto de conversão. Também é útil quando você fica preso em uma etapa de conversão para comparar com o projeto gerado por modelo.

## <a name="configure-the-site-to-use-mvc"></a>Configurar o site para usar o MVC

::: moniker range=">= aspnetcore-2.1"

* Ao direcionar o .NET Core, o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) é referenciado por padrão. Este pacote contém pacotes comumente usados por aplicativos MVC. Se estiver direcionando .NET Framework, as referências de pacote deverão ser listadas individualmente no arquivo de projeto.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Ao direcionar o .NET Core, o [metapacote Microsoft. AspNetCore. All](xref:fundamentals/metapackage) é referenciado por padrão. Este pacote contém pacotes comumente usados por aplicativos MVC. Se estiver direcionando .NET Framework, as referências de pacote deverão ser listadas individualmente no arquivo de projeto.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* Ao direcionar .NET Core ou .NET Framework, pacotes usados por aplicativos MVC são listados individualmente no arquivo do projeto.

::: moniker-end

`Microsoft.AspNetCore.Mvc`é o ASP.NET Core MVC Framework. `Microsoft.AspNetCore.StaticFiles`é o manipulador de arquivo estático. O tempo de execução do ASP.NET Core é modular e você deve optar explicitamente por fornecer arquivos estáticos (consulte [arquivos estáticos](xref:fundamentals/static-files)).

* Abra o arquivo *Startup.cs* e altere o código para corresponder ao seguinte:

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

O `UseStaticFiles` método de extensão adiciona o manipulador de arquivo estático. Conforme mencionado anteriormente, o tempo de execução do ASP.NET é modular e você deve optar explicitamente por fornecer arquivos estáticos. O `UseMvc` método de extensão adiciona o roteamento. Para obter mais informações, consulte inicialização e [Roteamento](xref:fundamentals/routing)de [aplicativos](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Adicionar um controlador e uma exibição

Nesta seção, você adicionará um controlador e uma exibição mínima para servir como espaços reservados para o controlador MVC ASP.NET e exibições que serão migradas na próxima seção.

* Adicione uma pasta de *controladores* .

* Adicione uma **classe de controlador** denominada *HomeController.cs* à pasta Controllers.

![Caixa de diálogo Adicionar Novo Item](mvc/_static/add_mvc_ctl.png)

* Adicione uma  pasta views.

* Adicione uma pasta *views/Home* .

* Adicione uma **exibição do Razor** denominada *index. cshtml* à pasta *views/Home* .

![Caixa de diálogo Adicionar Novo Item](mvc/_static/view.png)

A estrutura do projeto é mostrada abaixo:

![Gerenciador de Soluções mostrando arquivos e pastas do WebApp1](mvc/_static/project-structure-controller-view.png)

Substitua o conteúdo do arquivo *views/home/index. cshtml* pelo seguinte:

```html
<h1>Hello world!</h1>
```

Execute o aplicativo.

![Aplicativo Web aberto no Microsoft Edge](mvc/_static/hello-world.png)

Consulte [controladores](xref:mvc/controllers/actions) e [exibições](xref:mvc/views/overview) para obter mais informações.

Agora que temos um projeto de ASP.NET Core de trabalho mínimo, podemos começar a migrar a funcionalidade do projeto ASP.NET MVC. Precisamos mover o seguinte:

* conteúdo do lado do cliente (CSS, fontes e scripts)

* controladores

* modos de exibição

* modelos

* reagrupamento

* filtros

* Logon/saída, identidade (isso é feito no próximo tutorial.)

## <a name="controllers-and-views"></a>Controladores e exibições

* Copie cada um dos métodos do ASP.NET MVC `HomeController` para o novo. `HomeController` Observe que no ASP.NET MVC, o tipo de retorno do método de ação do controlador interno do modelo é [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); no ASP.NET Core MVC, os métodos de ação `IActionResult` retornam. `ActionResult`implementa `IActionResult`, portanto, não é necessário alterar o tipo de retorno de seus métodos de ação.

* Copie os arquivos de exibição *about. cshtml*, *Contact. cshtml*e *index. cshtml* Razor do projeto ASP.NET MVC para o projeto ASP.NET Core.

* Execute o aplicativo ASP.NET Core e teste cada método. Ainda não migramos o arquivo de layout ou os estilos, portanto, as exibições renderizadas contêm apenas o conteúdo nos arquivos de exibição. Você não terá os links gerados pelo arquivo de layout `About` para `Contact` as exibições e, portanto, precisará chamá-los do navegador (substitua **4492** pelo número da porta usada em seu projeto).

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Página de contato](mvc/_static/contact-page.png)

Observe a falta de estilos e itens de menu. Corrigiremos isso na próxima seção.

## <a name="static-content"></a>Conteúdo estático

Nas versões anteriores do ASP.NET MVC, o conteúdo estático era hospedado da raiz do projeto Web e foi misturado com arquivos do lado do servidor. No ASP.NET Core, o conteúdo estático é hospedado na pasta *wwwroot* . Você desejará copiar o conteúdo estático do seu aplicativo MVC ASP.NET antigo para a pasta *wwwroot* em seu projeto ASP.NET Core. Nesta conversão de exemplo:

* Copie o arquivo *favicon. ico* do projeto MVC antigo para a pasta *wwwroot* no projeto ASP.NET Core.

O antigo projeto MVC ASP.NET usa a [inicialização](https://getbootstrap.com/) para seu estilo e armazena os arquivos de inicialização nas pastas *conteúdo* e *scripts* . O modelo, que gerou o antigo projeto MVC do ASP.NET, faz referência à inicialização no arquivo de layout (*views/Shared/_ layout. cshtml*). Você pode copiar os arquivos *bootstrap. js* e *bootstrap. css* do projeto MVC ASP.net para a pasta *wwwroot* no novo projeto. Em vez disso, adicionaremos suporte para bootstrap (e outras bibliotecas do lado do cliente) usando CDNs na próxima seção.

## <a name="migrate-the-layout-file"></a>Migrar o arquivo de layout

* Copie o arquivo *_ViewStart. cshtml* da pasta views do projeto MVC  ASP.net antigo para a pasta views  do ASP.NET Core Project. O arquivo *_ViewStart. cshtml* não foi alterado no ASP.NET Core MVC.

* Crie uma pasta *views/Shared* .

* *Adicional* Copie *_ViewImports. cshtml* da pasta views do projeto  MVC *FullAspNetCore* para a pasta views  do ASP.NET Core Project. Remova qualquer declaração de namespace no arquivo *_ViewImports. cshtml* . O arquivo *_ViewImports. cshtml* fornece namespaces para todos os arquivos de exibição e apresenta [auxiliares de marca](xref:mvc/views/tag-helpers/intro). Os auxiliares de marca são usados no novo arquivo de layout. O arquivo *_ViewImports. cshtml* é novo para ASP.NET Core.

* Copie o arquivo *_ layout. cshtml* da pasta *views/Shared* do projeto do ASP.net antigo para a pasta *compartilhada/exibições* do projeto ASP.NET Core.

Abra o arquivo *_ layout. cshtml* e faça as seguintes alterações (o código completo é mostrado abaixo):

* Substituir `@Styles.Render("~/Content/css")` por um `<link>` elemento para carregar *bootstrap. css* (veja abaixo).

* Remova `@Scripts.Render("~/bundles/modernizr")`.

* Comente a `@Html.Partial("_LoginPartial")` linha (circundando a linha com `@*...*@`). Para obter mais informações, consulte [migrar autenticação e identidade para ASP.NET Core](xref:migration/identity)

* Substituir `@Scripts.Render("~/bundles/jquery")` por um `<script>` elemento (veja abaixo).

* Substituir `@Scripts.Render("~/bundles/bootstrap")` por um `<script>` elemento (veja abaixo).

A marcação de substituição para inclusão de CSS de Bootstrap:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

A marcação de substituição para a inclusão do jQuery e do JavaScript de Bootstrap:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

O arquivo *_ layout. cshtml* atualizado é mostrado abaixo:

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Exibir o site no navegador. Agora ele deve ser carregado corretamente, com os estilos esperados em vigor.

* *Adicional* Talvez você queira tentar usar o novo arquivo de layout. Para este projeto, você pode copiar o arquivo de layout do projeto *FullAspNetCore* . O novo arquivo de layout usa auxiliares de [marca](xref:mvc/views/tag-helpers/intro) e tem outras melhorias.

## <a name="configure-bundling-and-minification"></a>Configurar agrupamento e minificação

Para obter informações sobre como configurar o agrupamento e o minificação, consulte [agrupamento e minificação](../client-side/bundling-and-minification.md).

## <a name="solve-http-500-errors"></a>Resolver erros HTTP 500

Há muitos problemas que podem causar uma mensagem de erro HTTP 500 que não contém informações sobre a origem do problema. Por exemplo, se o arquivo *views/_ViewImports. cshtml* contiver um namespace que não exista em seu projeto, você receberá um erro http 500. Por padrão, em aplicativos ASP.NET Core, `UseDeveloperExceptionPage` a extensão é adicionada `IApplicationBuilder` ao e executada quando a configuração está em *desenvolvimento*. Isso é detalhado no código a seguir:

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.NET Core converte exceções sem tratamento em um aplicativo Web em respostas de erro HTTP 500. Normalmente, os detalhes do erro não são incluídos nessas respostas para evitar a divulgação de informações potencialmente confidenciais sobre o servidor. Consulte **usando a página de exceção do desenvolvedor** em [manipular erros](../fundamentals/error-handling.md) para obter mais informações.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

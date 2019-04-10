---
title: Migrar do ASP.NET MVC para ASP.NET Core MVC
author: ardalis
description: Saiba como começar a migrar um projeto ASP.NET MVC para ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: a85b9f15be8ad9ca66b20ef1f4422fe67806a797
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468534"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migrar do ASP.NET MVC para ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), e [Scott Addie](https://scottaddie.com)

Este artigo mostra como começar a migrar um projeto ASP.NET MVC para [ASP.NET Core MVC](../mvc/overview.md). No processo, ele destaca muitas das coisas que mudaram do ASP.NET MVC. Migrando do ASP.NET MVC é um processo de várias etapas e este artigo aborda a configuração inicial, básicas controladores e modos de exibição, conteúdo estático e as dependências do lado do cliente. Artigos adicionais abrangem a migração de configuração e o código de identidade encontrados em muitos projetos do ASP.NET MVC.

> [!NOTE]
> Os números de versão nos exemplos podem não ser atuais. Talvez você precise atualizar seus projetos de acordo.

## <a name="create-the-starter-aspnet-mvc-project"></a>Criar o projeto do MVC do ASP.NET starter

Para demonstrar a atualização, vamos começar criando um aplicativo ASP.NET MVC. Criá-lo com o nome *WebApp1* para o namespace coincida com o projeto do ASP.NET Core que criamos na próxima etapa.

![Caixa de diálogo Novo projeto do Studio Visual](mvc/_static/new-project.png)

![Nova caixa de diálogo do aplicativo Web: Modelo de projeto do MVC selecionado no painel de modelos do ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Opcional:* Altere o nome da solução do *WebApp1* à *Mvc5*. O Visual Studio exibe o novo nome da solução (*Mvc5*), que torna mais fácil de informar deste projeto no próximo projeto.

## <a name="create-the-aspnet-core-project"></a>Criar o projeto do ASP.NET Core

Criar um novo *vazio* aplicativo de web do ASP.NET Core com o mesmo nome que o projeto anterior (*WebApp1*) para que os namespaces em dois projetos sejam correspondentes. Ter o mesmo namespace torna mais fácil copiar o código entre os dois projetos. Você precisará criar esse projeto em um diretório diferente do projeto anterior para usar o mesmo nome.

![Caixa de diálogo Novo Projeto](mvc/_static/new_core.png)

![Nova caixa de diálogo de aplicativo Web ASP.NET: Modelo de projeto vazio selecionado no painel de modelos do ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Opcional:* Criar um novo aplicativo de ASP.NET Core usando o *aplicativo Web* modelo de projeto. Nomeie o projeto *WebApp1*e selecione uma opção de autenticação do **contas de usuário individuais**. Renomear este aplicativo seja *FullAspNetCore*. Isso poupa de projeto tempo criando a conversão. Você pode examinar o código gerado pelo modelo para ver o resultado final ou copiar o código para o projeto de conversão. Também é útil quando você ficar preso em uma etapa de conversão a ser comparado com o projeto de modelo gerado.

## <a name="configure-the-site-to-use-mvc"></a>Configurar o site para usar o MVC

::: moniker range=">= aspnetcore-2.1"

* Ao direcionar o .NET Core, o [metapacote Microsoft](xref:fundamentals/metapackage-app) é referenciado por padrão. Este pacote contém pacotes de pacotes usados pelos aplicativos MVC. Se o destino do .NET Framework, referências de pacote devem estar listadas individualmente no arquivo de projeto.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Ao direcionar o .NET Core, o [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage) é referenciado por padrão. Este pacote contém pacotes de pacotes usados pelos aplicativos MVC. Se o destino do .NET Framework, referências de pacote devem estar listadas individualmente no arquivo de projeto.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* Ao direcionar o .NET Core ou .NET Framework, pacotes de pacotes usados pelos aplicativos MVC estão listados individualmente no arquivo de projeto.

::: moniker-end

`Microsoft.AspNetCore.Mvc` é a estrutura do ASP.NET Core MVC. `Microsoft.AspNetCore.StaticFiles` é o manipulador de arquivo estático. O tempo de execução do ASP.NET Core é modular e você deve explicitamente optar por fornecer arquivos estáticos (consulte [arquivos estáticos](xref:fundamentals/static-files)).

* Abra o *Startup.cs* de arquivo e altere o código para corresponder ao seguinte:

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

O `UseStaticFiles` método de extensão adiciona o manipulador de arquivo estático. Conforme mencionado anteriormente, o tempo de execução do ASP.NET é modular e você deve explicitamente optar por fornecer arquivos estáticos. O `UseMvc` adiciona o método de extensão roteamento. Para obter mais informações, consulte [inicialização do aplicativo](xref:fundamentals/startup) e [roteamento](xref:fundamentals/routing).

## <a name="add-a-controller-and-view"></a>Adicionar um controlador e o modo de exibição

Nesta seção, você adicionará um controlador de mínimo e o modo de exibição para servir como espaços reservados para o controlador do ASP.NET MVC e exibições que na próxima seção, você migrará.

* Adicionar um *controladores* pasta.

* Adicionar um **classe Controller** denominado *HomeController.cs* para o *controladores* pasta.

![Caixa de diálogo Adicionar Novo Item](mvc/_static/add_mvc_ctl.png)

* Adicionar um *modos de exibição* pasta.

* Adicionar um *exibições/inicial* pasta.

* Adicionar um **modo de exibição do Razor** denominado *index. cshtml* para o *exibições/inicial* pasta.

![Caixa de diálogo Adicionar Novo Item](mvc/_static/view.png)

A estrutura do projeto é mostrada abaixo:

![Gerenciador de soluções mostrando arquivos e pastas do WebApp1](mvc/_static/project-structure-controller-view.png)

Substitua o conteúdo do *Views/Home/Index.cshtml* arquivo com o seguinte:

```html
<h1>Hello world!</h1>
```

Execute o aplicativo.

![Aplicativo Web aberto no Microsoft Edge](mvc/_static/hello-world.png)

Ver [controladores](xref:mvc/controllers/actions) e [modos de exibição](xref:mvc/views/overview) para obter mais informações.

Agora que temos um projeto de núcleo do ASP.NET mínimo do trabalho, podemos começar a migrar a funcionalidade do projeto ASP.NET MVC. É preciso mover o seguinte:

* conteúdo do lado do cliente (CSS, fontes e scripts)

* controladores

* modos de exibição

* modelos

* Agrupamento

* filtros

* Log de entrada/saída, a identidade (Isso é feito no próximo tutorial.)

## <a name="controllers-and-views"></a>Controladores e exibições

* Copie cada um dos métodos do ASP.NET MVC `HomeController` para o novo `HomeController`. Observe que, no ASP.NET MVC, tipo de método de ação retorno controlador interno do modelo é [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); no ASP.NET Core MVC, os retorno de métodos de ação `IActionResult` em vez disso. `ActionResult` implementa `IActionResult`, portanto, não é necessário alterar o tipo de retorno de métodos de ação.

* Cópia de *About. cshtml*, *Contact. cshtml*, e *index. cshtml* arquivos de exibição do Razor do projeto ASP.NET MVC para o projeto ASP.NET Core.

* Execute o aplicativo ASP.NET Core e cada método de teste. Ainda não migramos o arquivo de layout ou estilos ainda, portanto, os modos de exibição renderizados contêm apenas o conteúdo nos arquivos de exibição. Você não terá os links de arquivo gerado de layout para o `About` e `Contact` modos de exibição, portanto, você precisará invocá-los a partir do navegador (substitua **4492** com o número da porta usado em seu projeto).

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Página de contato](mvc/_static/contact-page.png)

Observe a falta de estilo e itens de menu. Corrigiremos isso na próxima seção.

## <a name="static-content"></a>Conteúdo estático

Nas versões anteriores do ASP.NET MVC, o conteúdo estático foi hospedado da raiz do projeto da web e foi Intercalado com arquivos do lado do servidor. No ASP.NET Core, conteúdo estático é hospedado na *wwwroot* pasta. Você desejará copiar o conteúdo estático de seu aplicativo ASP.NET MVC antigo para o *wwwroot* pasta em seu projeto ASP.NET Core. Nessa conversão de exemplo:

* Cópia de */favicon.ico* arquivo de projeto MVC antigo para o *wwwroot* pasta no projeto do ASP.NET Core.

O ASP.NET MVC antigo projeto usa [Bootstrap](https://getbootstrap.com/) para seu estilo e armazena a inicialização de arquivos no *conteúdo* e *Scripts* pastas. O modelo, que gerou o antigo projeto do ASP.NET MVC, faz referência a inicialização no arquivo de layout (*Views/Shared/_Layout.cshtml*). Você pode copiar o *bootstrap. js* e *Bootstrap* arquivos do ASP.NET MVC de projeto para o *wwwroot* pasta no novo projeto. Em vez disso, vamos adicionar suporte para o Bootstrap (e outras bibliotecas do lado do cliente) usando as CDNs na próxima seção.

## <a name="migrate-the-layout-file"></a>Migrar o arquivo de layout

* Cópia de *viewstart* arquivo do antigo do projeto ASP.NET MVC *exibições* pasta para do projeto ASP.NET Core *modos de exibição* pasta. O *viewstart* arquivo não foi alterado no ASP.NET Core MVC.

* Criar uma *Views/Shared* pasta.

* *Opcional:* Cópia *viewimports. cshtml* da *FullAspNetCore* do projeto MVC *modos de exibição* pasta para do projeto ASP.NET Core *exibições* pasta. Remover qualquer declaração de namespace na *viewimports. cshtml* arquivo. O *viewimports. cshtml* fornece os namespaces para todos os arquivos de exibição de arquivo e traz [auxiliares de marca](xref:mvc/views/tag-helpers/intro). Os auxiliares de marca são usados no novo arquivo de layout. O *viewimports. cshtml* arquivo é novo para o ASP.NET Core.

* Cópia de *layout. cshtml* arquivo do antigo do projeto ASP.NET MVC *Views/Shared* pasta para do projeto ASP.NET Core *Views/Shared* pasta.

Abra *layout. cshtml* de arquivo e faça as seguintes alterações (o código completo é mostrado abaixo):

* Substitua `@Styles.Render("~/Content/css")` com um `<link>` elemento do qual carregar *Bootstrap* (veja abaixo).

* Remova `@Scripts.Render("~/bundles/modernizr")`.

* Comente a `@Html.Partial("_LoginPartial")` linha (envolvem a linha com `@*...*@`). Para obter mais informações, consulte [migrar autenticação e identidade para o ASP.NET Core](xref:migration/identity)

* Substitua `@Scripts.Render("~/bundles/jquery")` com um `<script>` elemento (veja abaixo).

* Substitua `@Scripts.Render("~/bundles/bootstrap")` com um `<script>` elemento (veja abaixo).

A marcação de substituição para inclusão de CSS do Bootstrap:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

A marcação de substituição para o jQuery e Bootstrap JavaScript inclusão:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Atualizada *layout. cshtml* arquivo é mostrado abaixo:

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Exiba o site no navegador. Ele agora deve carregar corretamente, com os estilos esperados em vigor.

* *Opcional:* Você talvez queira tentar usar o novo arquivo de layout. Para este projeto, você pode copiar o arquivo de layout do *FullAspNetCore* projeto. O novo arquivo de layout utiliza [auxiliares de marca](xref:mvc/views/tag-helpers/intro) e tem outras melhorias.

## <a name="configure-bundling-and-minification"></a>Configurar o agrupamento e minificação

Para obter informações sobre como configurar o agrupamento e minificação, consulte [agrupamento e Minificação](../client-side/bundling-and-minification.md).

## <a name="solve-http-500-errors"></a>Resolver erros HTTP 500

Há muitos problemas que podem causar uma mensagem de erro HTTP 500 que não contêm informações sobre a origem do problema. Por exemplo, se o *viewimports* arquivo contém um namespace que não existe em seu projeto, você obterá um erro HTTP 500. Por padrão em aplicativos ASP.NET Core, o `UseDeveloperExceptionPage` extensão é adicionada para o `IApplicationBuilder` e executados quando a configuração é *desenvolvimento*. Isso é detalhado no código a seguir:

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.NET Core converte as exceções sem tratamento em um aplicativo web em respostas de erro HTTP 500. Normalmente, os detalhes do erro não estão incluídos nessas respostas para evitar a divulgação de informações possivelmente confidenciais sobre o servidor. Ver **usando a página de exceção do desenvolvedor** na [tratar erros](../fundamentals/error-handling.md) para obter mais informações.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-components/index>
* <xref:mvc/views/tag-helpers/intro>

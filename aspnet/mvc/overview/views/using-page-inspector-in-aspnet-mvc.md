---
uid: mvc/overview/views/using-page-inspector-in-aspnet-mvc
title: Usando o Inspetor de página no ASP.NET MVC | Microsoft Docs
author: rick-anderson
description: O Inspetor de página no Visual Studio 2012 é uma ferramenta de desenvolvimento na web com um navegador integrado. Selecione qualquer elemento no navegador integrado e o Page Inspector i...
ms.author: riande
ms.date: 08/15/2012
ms.assetid: c7e4e1ab-4932-4614-9f53-aaf7c706d498
msc.legacyurl: /mvc/overview/views/using-page-inspector-in-aspnet-mvc
msc.type: authoredcontent
ms.openlocfilehash: 0dea8b077878139a3f513cb51447b86a93fe55b8
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236530"
---
<a name="using-page-inspector-in-aspnet-mvc"></a>Usando o Inspetor de Página no ASP.NET MVC
====================
por Tim Ammann

> O Inspetor de página no Visual Studio 2012 é uma ferramenta de desenvolvimento na web com um navegador integrado. Selecione qualquer elemento no navegador integrado e Inspetor de página instantaneamente destaca o código-fonte e o CSS do elemento. Você pode procurar qualquer modo de exibição do MVC, rapidamente encontrar as fontes de marcação renderizada e usar as ferramentas de navegador dentro do ambiente do Visual Studio.
> 
> [Assista ao vídeo](../../videos/mvc-4/using-page-inspector-in-aspnet-mvc.md)
> 
> Este tutorial mostra como habilitar o modo de inspeção e, em seguida, rapidamente localizar e editar a marcação e CSS dentro do seu projeto da web. O tutorial usa um projeto do MVC, mas você também pode usar o Inspetor de página para [Web Forms](https://go.microsoft.com/?linkid=9802001) e outros aplicativos ASP.NET.
> 
> O tutorial tem as seguintes seções:
> 
> - [Pré-requisitos](#_1_prerequisites)
> - [Criar um aplicativo Web](#_2_creating_a)
> - [Usar o Inspetor de página para procurar a uma exibição](#_3_using_page)
> - [Habilitar o modo de inspeção](#_4_inspection_mode)
> - [Use o Inspetor de página para fazer alterações à marcação](#_5_using_page)
> - [Modo de inspeção e a janela do HTML](#_6_inspection_mode)
> - [Visualizar alterações de CSS na janela estilos](#_7_previewing_css)
> - [CSS Auto Sync](#css_auto_sync)
> - [Usando o seletor de cor CSS](#css_color_picker)
> - [Mapeamento de elementos de página dinâmica para JavaScript](#map_dynamic_elements)


<a id="_prerequisites"></a><a id="_1_prerequisites"></a>

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2012](https://www.microsoft.com/visualstudio/11) ou [Visual Studio Express 2012 para Web](https://www.microsoft.com/visualstudio/11/downloads#express-web).

> [!NOTE]
> Para obter a versão mais recente do Inspetor de página, use [Web Platform Installer](https://go.microsoft.com/fwlink/?LinkId=255386) para instalar o SDK do Windows Azure para .NET 2.0.


O Page Inspector é fornecido com o Microsoft Web Developer Tools. A versão mais recente é 1.3. Para verificar qual versão você tem, execute o Visual Studio e selecione **sobre o Microsoft Visual Studio** da **ajudar** menu.

<a id="_creating_a_web"></a><a id="_2_creating_a"></a>

## <a name="create-a-web-application"></a>Criar um aplicativo Web

Primeiro, crie um aplicativo web que você usará com o Inspetor de página. No Visual Studio, escolha **arquivo** &gt; **novo projeto**. À esquerda, expanda **Visual c#**, selecione **Web**e, em seguida, selecione **aplicativo Web do ASP.NET MVC4**.

![Novo aplicativo ASP.NET MVC](using-page-inspector-in-aspnet-mvc/_static/image2.png)

Clique em **OK**.

No **novo projeto ASP.NET MVC 4** caixa de diálogo, selecione **aplicativo de Internet**. Deixe **Razor** como o mecanismo de exibição padrão.

![Novo projeto ASP.NET MVC - aplicativo de Internet](using-page-inspector-in-aspnet-mvc/_static/image4.png)

O aplicativo é aberto no **origem** modo de exibição.

![Novo aplicativo ASP.NET MVC no modo de exibição de fonte](using-page-inspector-in-aspnet-mvc/_static/image6.png)

Agora que você tem um aplicativo para trabalhar com, você pode usar o Inspetor de página para examinar e modificá-lo.

<a id="_starting_page_inspector"></a><a id="_3_using_page"></a>

## <a name="use-page-inspector-to-browse-to-a-view"></a>Usar o Inspetor de página para procurar a uma exibição

No Visual Studio 2012, clique com botão direito o qualquer modo de exibição em seu projeto, selecione **exibir no Page Inspector**, e o Page Inspector será calcular a rota e exibir a página.

Na **Gerenciador de soluções**, expanda o **exibições** pasta e, em seguida, o **Home** pasta. Clique com botão direito no arquivo index. cshtml e escolha **exibir no Page Inspector**.

![Modo de exibição index. cshtml no Inspetor de página](using-page-inspector-in-aspnet-mvc/_static/image8.png)

Por padrão, o Page Inspector é encaixado como uma janela no lado esquerdo do ambiente do Visual Studio. Se você preferir, você pode encaixá-la em outro lugar ou desencaixar a janela. Confira [Como Organizar e encaixar janelas](https://msdn.microsoft.com/library/z4y0hsax.aspx).

O painel superior da janela do Inspetor de página mostra a página atual em uma janela do navegador. O painel inferior mostra a página na marcação HTML, juntamente com alguns guias que permitem que você inspecione os diferentes aspectos da página. O painel inferior é semelhante para o [ferramentas de desenvolvedor F12](https://msdn.microsoft.com/ie/aa740478) no Internet Explorer.

![Aplicativo ASP.NET MVC no Inspetor de página](using-page-inspector-in-aspnet-mvc/_static/image10.png)

Neste tutorial, você aprenderá a usar o **HTML** e **estilos** guias para navegar rapidamente e fazer alterações no aplicativo.

<a id="_examining_(&quot;decomposing&quot;)_the"></a><a id="_inspection_mode_and"></a><a id="_4_inspection_mode"></a>

## <a name="enableinspection-mode"></a>Modo de EnableInspection

Para colocar o Inspetor de página no modo de inspeção, clique o **inspecionar** botão. Nesse modo, quando você mantém o ponteiro do mouse sobre qualquer parte da página renderizada, o código ou marcação de origem correspondente é realçado.

![Ativar/desativar modo de inspeção](using-page-inspector-in-aspnet-mvc/_static/image12.png)

Agora mova o mouse sobre diferentes partes da página dentro do Inspetor de página. Como você, o ponteiro do mouse muda para um grande sinal de adição e o elemento abaixo é realçado:

![Passar o mouse sobre o wrapper div.content](using-page-inspector-in-aspnet-mvc/_static/image14.png)

À medida que você move o ponteiro do mouse, o Visual Studio destaca a sintaxe do Razor correspondente no arquivo de origem. Se o elemento HTML vem de outro arquivo de origem, o Visual Studio abre automaticamente o arquivo.

No Inspetor de página, o **HTML** guia mostra o HTML que foi gerado a partir da sintaxe do Razor. À medida que você move o ponteiro do mouse, os elementos HTML são realçados. O **estilos** guia mostra as regras de CSS para o elemento.

<a id="_5_using_page"></a>

## <a name="use-page-inspector-to-make-changes-to-markup"></a>Use o Inspetor de página para fazer alterações à marcação

Inspetor de página permite que você encontre a marcação cujo local pode não ser óbvio. Você pode modificar a marcação e ver as alterações resultantes.

Para ver isso, clique em **inspecionar** e, em seguida, role até a parte inferior da página na janela do Inspetor de página.

Quando você move o ponteiro do mouse na área de rodapé, o Inspetor de página abre o \_arquivo layout. cshtml e realça a seção da página de layout que você selecionou. Como você pode ver, o rodapé são é definido no arquivo de layout e não a própria exibição.

![Rodapé](using-page-inspector-in-aspnet-mvc/_static/image16.png)

Agora mova o ponteiro do mouse sobre a linha com os direitos autorais <a id="a"> </a>Observe. No \_página de layout. cshtml, a linha correspondente é realçada.

![Linha de direitos autorais do rodapé realçada](using-page-inspector-in-aspnet-mvc/_static/image18.png)

Adicione algum texto ao final da linha no \_arquivo layout. cshtml.

&lt;p&gt;&amp;copiarem; @DateTime.Now.Year -Meu aplicativo ASP.NET MVC é o máximo!  &lt; /p&gt;

Agora, pressione Ctrl + Alt + Enter ou clique na barra de atualização para ver os resultados na janela do navegador Inspetor de página.

![É o máximo meu aplicativo ASP.NET!](using-page-inspector-in-aspnet-mvc/_static/image20.png)

Você deve ter imaginado que rodapé definidos no index. cshtml, mas ela tornou-se no \_layout. cshtml e Inspetor de página encontrado-lo para você.

<a id="_inspection_mode_and_1"></a><a id="_6_inspection_mode"></a>

## <a name="inspection-mode-and-the-html-window"></a>Modo de inspeção e a janela do HTML

Em seguida, você terá uma olhada rápida na janela do HTML e como ele mapeia elementos para você.

Clique em **inspecionar** para colocar o Inspetor de página no modo de inspeção.

Clique na parte superior da página que diz "Seu logohere". Você está examinando um determinado elemento mais detalhadamente, portanto, a exibição na janela do navegador não é alterado à medida que você move o ponteiro do mouse.

Agora mova o ponteiro do mouse para o **HTML** janela. À medida que você move o ponteiro do mouse, o Inspetor de página descreve o elemento dentro de **HTML** janela e realça o elemento correspondente na janela do navegador.

![Janela do HTML](using-page-inspector-in-aspnet-mvc/_static/image22.png)

Como antes, o Inspetor de página abre o \_arquivo de layout. cshtml para você em uma guia temporária. Clique o \_guia temporária do layout. cshtml e a marcação correspondente serão realçado em de &lt;cabeçalho&gt; seção para você:

![Marcação realçada](using-page-inspector-in-aspnet-mvc/_static/image24.png)

<a id="_using_page_inspector"></a><a id="_7_previewing_css"></a>

## <a name="preview-css-changes-in-the-styles-window"></a>Visualizar alterações CSS na janela estilos

Em seguida, você usará o Page Inspector **estilos** janela para visualizar as alterações à CSS.

Clique em **inspecionar** para colocar o Inspetor de página no modo de inspeção.

Na janela do navegador Inspetor de página, mova o ponteiro do mouse sobre a seção de "Home Page" até que o **div.content wrapper** rótulo aparece.

![Passar o mouse sobre o wrapper div.content](using-page-inspector-in-aspnet-mvc/_static/image26.png)

Clique dentro da seção de wrapper div.content uma vez e, em seguida, mova o ponteiro do mouse para o **estilos** janela. O **estilos** janela mostra todas as regras CSS para este elemento. Role para baixo até localizar o wrapper de. Content .featured seletor de classe. Agora, desmarque a caixa de seleção para a propriedade de cor do plano de fundo.

![Cor do plano de fundo clara](using-page-inspector-in-aspnet-mvc/_static/image28.png)

Observe como a alteração visualiza instantaneamente na janela do navegador Inspetor de página.

Selecione a caixa de seleção novamente, clique duas vezes o valor da propriedade e alterá-la para vermelho. A alteração mostra imediatamente:

![Cor do plano de fundo vermelho](using-page-inspector-in-aspnet-mvc/_static/image30.png)

O **estilos** torna a janela fácil de testar e visualizar o CSS é alterado antes de confirmar as alterações para o estilo da folha em si.

<a id="css_auto_sync"></a>
## <a name="css-auto-sync"></a>CSS Auto Sync

> [!NOTE]
> Este recurso requer a versão 1.3 do Inspetor de página.


O recurso de sincronização automática de CSS permite que você editar um arquivo CSS diretamente e ver as alterações imediatamente no navegador Inspetor de página.

Clique em **inspecionar** para colocar o Inspetor de página no modo de inspeção.

No navegador Inspetor de página, mova o ponteiro do mouse sobre a seção de "Home Page" até que o **div.content wrapper** rótulo aparece. Clique para selecionar esse elemento.

O **estilos** janela mostra todas as regras CSS para este elemento. Role para baixo até localizar o wrapper de. Content .featured seletor de classe. Clique em .featured. Content-"wrapper". Inspetor de página abre o arquivo CSS que define esse estilo (CSS) e realça o estilo CSS correspondente.

![](using-page-inspector-in-aspnet-mvc/_static/image32.png)

Agora, altere o valor para `background-color` para "vermelho". A alteração aparece imediatamente no navegador Inspetor de página.

![](using-page-inspector-in-aspnet-mvc/_static/image34.png)

<a id="css_color_picker"></a>
## <a name="using-the-css-color-picker"></a>Usando o seletor de cor CSS

O editor de CSS no Visual Studio 2012 tem um seletor de cores que torna mais fácil escolher e inserir as cores. O seletor de cor inclui uma paleta de cores padrão, dá suporte a nomes de cores padrão, os códigos de hash, as cores RGB, RGBA, HSL e HSLA e mantém uma lista das cores usados mais recentemente no documento.

Na seção anterior, você alterou o valor da `background-color` propriedade. Para invocar o seletor de cores, coloque o ponto de inserção depois do nome da propriedade e digite **#** ou **rgb (**.

![A barra de seletor de cores CSS](using-page-inspector-in-aspnet-mvc/_static/image36.png)

Clique em uma cor para selecioná-lo, ou pressione a tecla de seta para baixo e, em seguida, use as teclas de seta esquerda e direita para percorrer as cores. Quando você visita uma cor, o valor hexadecimal correspondente é visualizado:

![valor da propriedade de cor do plano de fundo visualizado](using-page-inspector-in-aspnet-mvc/_static/image38.png)

Se a barra de cores não tem a cor exata que você deseja, você pode usar o seletor de cor pop-down. Para abri-lo, clique na divisa dupla na extremidade direita da barra de cores, ou pressione a seta para baixo uma ou duas vezes no teclado.

![Seletor de cor CSS Pop-Down](using-page-inspector-in-aspnet-mvc/_static/image40.png)

Clique em uma cor da barra de ferramentas vertical no lado direito. Isso mostra uma gradação de cor na janela principal. Escolha uma cor diretamente na barra vertical, pressionando Enter ou clique em qualquer ponto na janela principal, escolha com maior precisão.

Se houver uma cor na tela do computador que você deseja usar (ele não precisa estar dentro da interface de usuário do Visual Studio), você pode capturar o seu valor usando a ferramenta de conta-gotas no canto inferior direito.

Você também pode alterar a opacidade de uma cor movendo o controle deslizante na parte inferior do seletor de cores. Isso muda a cor valores aos valores RGBA, porque o formato de RGBA pode representar a opacidade.

Depois de escolher uma cor, pressione Enter e, em seguida, digite um ponto e vírgula para concluir a entrada de cor do plano de fundo na *CSS* arquivo.

<a id="_the_update_bar"></a>

### <a name="the-page-inspector-update-bar"></a>A barra de atualização do Inspetor de página

Inspetor de página imediatamente detecta a alteração para o *CSS* de arquivo e exibe um alerta em uma barra de atualização.

![Barra de atualização](using-page-inspector-in-aspnet-mvc/_static/image42.png)

Para salvar todos os seus arquivos e atualize o navegador Inspetor de página, pressione Ctrl + Alt + Enter ou clique na barra de atualização. A alteração na cor de realce é exibida no navegador.

<a id="map_dynamic_elements"></a>
## <a name="mapping-dynamic-page-elements-to-javascript"></a>Mapeamento de elementos de página dinâmica para JavaScript

Aplicativos web modernos, elementos da página geralmente são gerados dinamicamente com JavaScript. Isso significa que não há nenhuma marcação estática (HTML ou Razor) que corresponde a esses elementos de página.

Com a versão 1.3, o Page Inspector agora pode mapear itens que foram adicionados dinamicamente para a página de volta para o código JavaScript correspondente. Para demonstrar esse recurso, vamos usar o [modelo de aplicativo de página única (SPA)](../../../single-page-application/overview/introduction/knockoutjs-template.md).

> [!NOTE]
> O modelo SPA requer o [ASP.NET e Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650) atualizar.


No Visual Studio, escolha **arquivo** &gt; **novo projeto**. À esquerda, expanda **Visual c#**, selecione **Web**e, em seguida, selecione **aplicativo Web do ASP.NET MVC4**. Clique em **OK**.

No **novo projeto ASP.NET MVC 4** caixa de diálogo, selecione **aplicativo de página única**.

No Gerenciador de soluções, expanda o **modos de exibição** pasta e, em seguida, o **Home** pasta. Clique com botão direito no arquivo index. cshtml e escolha **exibir no Page Inspector**.

A primeira coisa que é exibido no navegador Inspetor de página é uma página de logon. Clique em "Inscrever-se" e crie um nome de usuário e senha. Depois de se inscrever, o aplicativo faz seu logon e cria uma lista de tarefas pendentes com alguns itens de exemplo.

Clique em **inspecionar** para colocar o Inspetor de página no modo de inspeção. No navegador Inspetor de página, clique em um dos itens de tarefas pendentes. Observe que, em vez de ser realçado em azul, o elemento é realçado em laranja, com "JS" ao lado do nome do elemento. Isso indica que o elemento foi criado dinamicamente por meio de script.

![](using-page-inspector-in-aspnet-mvc/_static/image44.png)

Além disso, um sublinhado laranja aparece na **pilha de chamadas** guia. Isso indica que o **pilha de chamadas** painel tem mais informações sobre o elemento.

Clique no **pilha de chamadas** guia. O **pilha de chamadas** painel mostra a pilha de chamadas para a chamada de JavaScript que criou o elemento. Chamadas para bibliotecas externas, como jQuery são recolhidas, para que você possa ver facilmente as chamadas para o seu script de aplicativo.

![](using-page-inspector-in-aspnet-mvc/_static/image46.png)

Para ver a pilha completa, inclusive chamadas para bibliotecas externas, você pode expandir os nós de rotulado como "Bibliotecas externas":

![](using-page-inspector-in-aspnet-mvc/_static/image48.png)

Se você clicar em um item na pilha de chamadas, o Visual Studio abre o arquivo de código e realça o script correspondente.

![](using-page-inspector-in-aspnet-mvc/_static/image50.png)

## <a name="see-also"></a>Consulte também

[Introdução ao ASP.NET MVC 4 com o Visual Studio](../older-versions/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4.md) (site do ASP.net)

[Introdução ao Inspetor de página](https://channel9.msdn.com/posts/visual-studio-vnext-introducing-page-inspector/) (vídeo do Channel 9)

[Mensagens de erro do Page Inspector](https://go.microsoft.com/?linkid=9813062) (MSDN)

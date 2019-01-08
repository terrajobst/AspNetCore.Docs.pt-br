---
uid: web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-partial-page-updates-with-asp-net-ajax
title: Noções básicas sobre atualizações de página parcial com o ASP.NET AJAX | Microsoft Docs
author: scottcate
description: Talvez o recurso mais visível das extensões do AJAX ASP.NET é a capacidade de fazer atualizações de uma página parcial ou incremental sem fazer um postback completo para t...
ms.author: riande
ms.date: 03/28/2008
ms.assetid: 54d9df99-1161-4899-b4e8-2679c85915e7
msc.legacyurl: /web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-partial-page-updates-with-asp-net-ajax
msc.type: authoredcontent
ms.openlocfilehash: 4883046aa16d5e67b7f0c92e15c897ef1a933b67
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098929"
---
<a name="understanding-partial-page-updates-with-aspnet-ajax"></a>Página parcial de Noções básicas sobre atualizações com o ASP.NET AJAX
====================
por [Scott Cate](https://github.com/scottcate)

[Baixar PDF](http://download.microsoft.com/download/C/1/9/C19A3451-1D14-477C-B703-54EF22E197EE/AJAX_tutorial01_Partial_Page_Updates_cs.pdf)

> Talvez o recurso mais visível das extensões do AJAX ASP.NET é a capacidade de fazer atualizações de uma página parcial ou incremental sem fazer um postback completo para o servidor, sem alterações de código e alterações mínimas de marcação. As vantagens são abrangentes – o estado das suas multimídias (por exemplo, Adobe Flash ou o Windows Media) é alterado, os custos de largura de banda são reduzidos e o cliente não Experimente a cintilação geralmente associada a um postback.


## <a name="introduction"></a>Introdução

Tecnologia ASP.NET da Microsoft oferece um modelo de programação orientada a objeto e controlada por evento e une-lo com os benefícios do código compilado. No entanto, seu modelo de processamento do lado do servidor tem várias desvantagens inerentes da tecnologia:

- Atualizações de página exigem uma ida e volta ao servidor, que requer uma atualização de página.
- Ida e volta não persistem efeitos gerados pelo Javascript ou outra tecnologia do lado do cliente (como o Adobe Flash)
- Durante um postback, navegadores diferentes do Microsoft Internet Explorer não dão suporte a restauração automática a posição de rolagem. E, até mesmo no Internet Explorer, ainda há uma cintilação enquanto a página é atualizada.
- Postagens podem envolver uma grande quantidade de largura de banda como o \_ \_campo de formulário VIEWSTATE pode crescer, especialmente ao lidar com controles, como o controle GridView ou repetidores.
- Não há nenhum modelo unificado para acessar serviços Web por meio de JavaScript ou outra tecnologia do lado do cliente.

Insira as extensões do ASP.NET AJAX da Microsoft. AJAX, o que significa **um** síncrona **J** avaScript **um** nd **X** ML, é uma estrutura integrada para fornecimento de página incremental as atualizações por meio de JavaScript de plataforma cruzada, composto de código do lado do servidor que compõem a estrutura do Microsoft AJAX e um componente de script chamado a biblioteca de scripts do Microsoft AJAX. As extensões do ASP.NET AJAX também fornecem suporte de plataforma cruzada para acessar serviços Web do ASP.NET por meio de JavaScript.

Este white paper examina a funcionalidade de atualizações de página parcial das extensões AJAX do ASP.NET, que inclui o componente ScriptManager, o controle UpdatePanel e o controle UpdateProgress e considera os cenários em que eles devem ou não devem ser utilizado.

Este white paper se baseia na versão Beta 2 do Visual Studio 2008 e o .NET Framework 3.5, que integra as extensões do AJAX ASP.NET a biblioteca de classes Base (em que era anteriormente um componente complementar disponível para o ASP.NET 2.0). Este white paper também pressupõe que você está usando o Visual Studio 2008 e não o Visual Web Developer Express Edition; Alguns modelos de projeto que são referenciados não podem estar disponíveis para os usuários do Visual Web Developer Express.

## <a name="partial-page-updates"></a>Atualizações de página parcial

Talvez o recurso mais visível das extensões do AJAX ASP.NET é a capacidade de fazer atualizações de uma página parcial ou incremental sem fazer um postback completo para o servidor, sem alterações de código e alterações mínimas de marcação. As vantagens são abrangentes – o estado das suas multimídias (por exemplo, Adobe Flash ou o Windows Media) é alterado, os custos de largura de banda são reduzidos e o cliente não Experimente a cintilação geralmente associada a um postback.

A capacidade de integrar a renderização parcial da página é integrada ao ASP.NET com alterações mínimas em seu projeto.

## <a name="walkthrough-integrating-partial-rendering-into-an-existing-project"></a>Passo a passo: A integração do processamento parcial em um projeto existente


1. No Microsoft Visual Studio 2008, crie um novo projeto de Site da Web ASP.NET acessando <em>arquivo</em>  <em>- &gt; New</em>  <em>- &gt; Web Site</em> e selecionando o Site da Web ASP.NET na caixa de diálogo. Você pode chamá-lo que você quiser, e você pode instalá-lo para o sistema de arquivos ou em serviços de informações da Internet (IIS).
2. Você verá a página padrão em branco com marcação básica do ASP.NET (um formulário do lado do servidor e um `@Page` diretiva). Remover um rótulo denominado `Label1` e um botão chamado `Button1` até a página dentro do elemento de formulário. Você pode definir suas propriedades de texto para que você quiser.
3. No modo Design, clique duas vezes em `Button1` para gerar um manipulador de eventos de lógica. Dentro do manipulador de eventos, definir `Label1.Text` para você clicou no botão! .

**Listagem 1: Marcação para default. aspx antes da renderização parcial é habilitada**

[!code-aspx[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample1.aspx)]

**Listagem 2: Code-behind (cortado) em default.aspx.cs**

[!code-csharp[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample2.cs)]

1. Pressione F5 para iniciar seu site da web. Visual Studio solicitará que você adicione um arquivo Web. config para habilitar a depuração; fazer isso. Quando você clica no botão, observe que a página é atualizada para alterar o texto no rótulo, e há uma breve cintilação conforme a página é redesenhada.
2. Depois de fechar a janela do navegador, retorne ao Visual Studio e para a página de marcação. Role para baixo na caixa de ferramentas do Visual Studio e localize a guia chamada AJAX Extensions. (Se você não tiver essa guia porque você está usando uma versão mais antiga das extensões AJAX ou Atlas, consulte o passo a passo para registrar os itens de caixa de ferramentas de extensões do AJAX mais tarde neste white paper ou instalar a versão atual com o instalador do Windows que pode ser baixado do site).


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image2.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image1.png)

([Clique para exibir a imagem em tamanho normal](understanding-partial-page-updates-with-asp-net-ajax/_static/image3.png))


1. <em>Problema conhecido:</em>se você instalar o Visual Studio 2008 em um computador que já tenha o Visual Studio 2005 instalado com o ASP.NET 2.0 AJAX Extensions, o Visual Studio 2008 importará os itens de caixa de ferramentas de extensões do AJAX. Você pode determinar se esse for o caso, examinando a dica de ferramenta dos componentes; eles devem dizem versão 3.5.0.0. Se dizem que a versão 2.0.0.0, em seguida, você importou seus itens de caixa de ferramentas antigos e precisará importá-las manualmente usando a caixa de diálogo Escolher itens da caixa de ferramentas no Visual Studio. Não será possível adicionar controles de versão 2 por meio do designer.

2. Antes do `<asp:Label>` marca começa, crie uma linha de espaço em branco e clique duas vezes no controle UpdatePanel na caixa de ferramentas. Observe que uma nova `@Register` diretiva está incluída na parte superior da página, indicando que os controles dentro do namespace de UI devem ser importados usando o `asp:` prefixo.
3. Arraste o fechamento `</asp:UpdatePanel>` marca após o fim do elemento Button, para que o elemento está bem formado com os controles de rótulo e botão encapsulados.
4. Após a abertura `<asp:UpdatePanel>` marca, começar a abrir uma nova marca. Observe que o IntelliSense mostrará duas opções. Nesse caso, crie um `<ContentTemplate>` marca. Certifique-se de quebrar essa marca ao redor de seu rótulo e o botão para que a marcação está bem formada.


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image5.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image4.png)

([Clique para exibir a imagem em tamanho normal](understanding-partial-page-updates-with-asp-net-ajax/_static/image6.png))


1. Em qualquer lugar dentro do `<form>` elemento, incluem um controle ScriptManager clicando duas vezes no `ScriptManager` item na caixa de ferramentas.
2. Editar o `<asp:ScriptManager>` para que ela inclua o atributo de marca `EnablePartialRendering= true`.

**Listagem 3: Marcação para default. aspx com a renderização parcial habilitada**

[!code-aspx[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample3.aspx)]

1. Abra o arquivo Web. config. Observe que o Visual Studio adicionou uma referência de compilação para Extensions automaticamente.

1. O que há de novo no Visual Studio 2008: O Web. config que vem com o Site da Web ASP.NET automaticamente modelos de projeto inclui todas as referências necessárias para extensões AJAX do ASP.NET e inclui comentado seções de informações de configuração que podem ser não comentada para habilitar adicionais funcionalidade. Visual Studio 2005 tinha modelos semelhantes ao ASP.NET 2.0 AJAX Extensions foram instalados. No entanto, no Visual Studio 2008, as extensões AJAX são recusá-la por padrão (ou seja, eles são referenciados por padrão, mas pode ser removidos como referências).


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image8.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image7.png)

([Clique para exibir a imagem em tamanho normal](understanding-partial-page-updates-with-asp-net-ajax/_static/image9.png))


1. Pressione F5 para iniciar seu site. Observe como sem alterações de código de origem foram necessárias para dar suporte à renderização parcial – somente a marcação foi alterada.

Quando você inicia seu site, você deve ver que a renderização parcial está agora habilitado, porque quando você clica no botão lá será sem cintilação, nem haverá qualquer alteração na posição de rolagem de página (neste exemplo não Demonstre que). Se você examinar a código-fonte renderizada da página depois de clicar no botão, ele confirmará que na verdade um POST-back não ocorreu - o texto do rótulo original ainda é parte da marcação de origem e o rótulo foi alterada por meio de JavaScript.

O Visual Studio 2008 não parece vêm com um modelo predefinido para um site ASP.NET habilitado por AJAX. No entanto, um modelo desse tipo estava disponível no Visual Studio 2005 se o Visual Studio 2005 e o ASP.NET 2.0 AJAX Extensions foram instalados. Consequentemente, configurar um site da web e iniciar com o modelo de Site de Web habilitado para AJAX provavelmente será ainda mais fácil, pois o modelo deve incluir um arquivo Web. config totalmente configurado (suporte a todas as extensões AJAX do ASP.NET, incluindo o acesso de serviços da Web e serialização JSON - JavaScript Object Notation) e inclui um UpdatePanel e ContentTemplate dentro da página principal do Web Forms, por padrão. Habilitar o processamento parcial com essa página padrão é tão simple quanto Revisitando as 10 etapa deste passo a passo e soltar controles na página.

## <a name="the-scriptmanager-control"></a>O controle ScriptManager

## <a name="scriptmanager-control-reference"></a>Referência de controle do ScriptManager

Propriedades de marcação habilitada:

| **Nome da propriedade** | **Tipo** | **Descrição** |
| --- | --- | --- |
| AllowCustomErrors-Redirect | Bool | Especifica se deve usar a seção de erro personalizada do arquivo Web. config para lidar com erros. |
| AsyncPostBackError-Message | Cadeia de Caracteres | Obtém ou define a mensagem de erro enviada ao cliente, se um erro será gerado. |
| AsyncPostBack-Timeout | Int32 | Obtém ou define a quantidade padrão de uma hora em que um cliente deve aguardar a solicitação assíncrona ser concluída. |
| EnableScript-Globalization | Bool | Obtém ou define se a globalização de script está habilitada. |
| EnableScript-Localization | Bool | Obtém ou define se a localização por script está habilitada. |
| ScriptLoadTimeout | Int32 | Determina o número de segundos permitido para carregamento de scripts no cliente |
| ScriptMode | Enum (Auto, depuração, versão, herdar) | Obtém ou define se deve processar versões de lançamento de scripts |
| ScriptPath | Cadeia de Caracteres | Obtém ou define o caminho raiz para o local dos arquivos de script a ser enviada ao cliente. |

Propriedades de código:

| **Nome da propriedade** | **Tipo** | **Descrição** |
| --- | --- | --- |
| AuthenticationService | AuthenticationService-Manager | Obtém os detalhes sobre o proxy do serviço de autenticação do ASP.NET que será enviada ao cliente. |
| IsDebuggingEnabled | Bool | Obtém se criar scripts e depuração de código está habilitada. |
| IsInAsyncPostback | Bool | Determina se a página está atualmente em uma solicitação de postagem assíncrona. |
| ProfileService | ProfileService-Manager | Obtém os detalhes sobre o proxy do serviço de criação de perfil do ASP.NET que será enviada ao cliente. |
| Scripts | Coleção&lt;referência de Script&gt; | Obtém uma coleção de referências de script que será enviada ao cliente. |
| Serviços | Coleção&lt;referência de serviço&gt; | Obtém uma coleção de referências de proxy de serviço Web que será enviada ao cliente. |
| SupportsPartialRendering | Bool | Obtém se o cliente atual oferece suporte à renderização parcial. Se essa propriedade retornará **falsos**, todas as solicitações de página será postagens padrão. |

Métodos de código público:

| **Nome do método** | **Tipo** | **Descrição** |
| --- | --- | --- |
| SetFocus(string) | Void | Define o foco do cliente para um determinado controle quando a solicitação é concluída. |

Descendentes de marcação:

| **Marca** | **Descrição** |
| --- | --- |
| &lt;AuthenticationService&gt; | Fornece detalhes sobre o proxy para o serviço de autenticação do ASP.NET. |
| &lt;ProfileService&gt; | Fornece detalhes sobre o proxy para o serviço de criação de perfil do ASP.NET. |
| &lt;Scripts&gt; | Fornece referências de script adicionais. |
| &lt;asp:ScriptReference&gt; | Indica uma referência de script específico. |
| &lt;Serviço&gt; | Fornece referências adicionais do serviço Web que terão classes proxy geradas. |
| &lt;asp:ServiceReference&gt; | Indica uma referência de serviço da Web específica. |

O controle ScriptManager é o núcleo essencial para as extensões do AJAX ASP.NET. Ele fornece acesso à biblioteca de scripts (incluindo o sistema de tipos de script extensivo do lado do cliente), dá suporte à renderização parcial e fornece suporte extensivo para serviços ASP.NET adicionais (como autenticação e criação de perfil, mas também outros serviços da Web). O controle ScriptManager também fornece suportam de globalização e localização para os scripts de cliente.

## <a name="providing-alternative-and-supplemental-scripts"></a>Fornecer Scripts alternativos e complementares

Embora o Microsoft ASP.NET 2.0 AJAX Extensions incluir o código de script inteiro em ambos os depuração e versão edições como recursos incorporados em assemblies referenciados, os desenvolvedores são livres para redirecionar o ScriptManager para os arquivos de script personalizado, bem como registrar scripts de adicionais necessários.

Para substituir a associação padrão para os scripts incluídos normalmente (como aqueles que oferecem suporte o namespace Sys. WebForms e o sistema de digitação personalizado), você pode se registrar para o `ResolveScriptReference` evento da classe ScriptManager. Quando este método é chamado, o manipulador de eventos tem a oportunidade de alterar o caminho para o arquivo de script em questão; o Gerenciador de scripts, em seguida, enviará uma cópia diferente ou personalizada dos scripts para o cliente.

Além disso, referências de script (representado pela `ScriptReference` classe) pode ser incluído programaticamente ou por meio de marcação. Para fazer isso, modifique programaticamente a `ScriptManager.Scripts` coleção, ou incluir `<asp:ScriptReference>` marcas sob o `<Scripts>` marca, que é um filho de primeiro nível do controle ScriptManager.

## <a name="custom-error-handling-for-updatepanels"></a>Tratamento UpdatePanels de erro personalizado

Embora as atualizações são manipuladas por gatilhos especificados por controles UpdatePanel, o suporte para tratamento de erros e mensagens de erro personalizadas é tratado pela instância de controle do ScriptManager uma página. Isso é feito, expondo um evento, `AsyncPostBackError`, para a página que pode então fornecer lógica de manipulação de exceção personalizada.

Consumindo o evento AsyncPostBackError, você pode especificar o `AsyncPostBackErrorMessage` propriedade, que, em seguida, faz com que uma caixa de alerta a ser gerado após a conclusão do retorno de chamada.

Personalização do lado do cliente também é possível em vez de usar a caixa de alerta padrão; Por exemplo, você talvez queira exibir um personalizado `<div>` elemento, em vez de diálogo modal de navegador padrão. Nesse caso, você pode tratar o erro no script de cliente:

**Listagem 5: Script do lado do cliente para exibir os erros personalizados**

[!code-html[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample4.html)]

Muito simplesmente, o script acima registra um retorno de chamada com o tempo de execução do AJAX do lado do cliente para que a solicitação assíncrona for concluída. Em seguida, ele verifica se um erro foi relatado e nesse caso, processa os detalhes dele, que, por fim, que indica o tempo de execução que o erro foi tratado em um script personalizado.

## <a name="globalization-and-localization-support"></a>Suporte de localização e globalização

O controle ScriptManager fornece amplo suporte para localização de cadeias de caracteres de script e componentes de interface do usuário; No entanto, esse tópico está fora do escopo deste white paper. Para obter mais informações, consulte o white paper, suporte de globalização no ASP.NET AJAX Extensions.

## <a name="the-updatepanel-control"></a>O controle UpdatePanel

## <a name="updatepanel-control-reference"></a>Referência de controle UpdatePanel

Propriedades de marcação habilitada:

| **Nome da propriedade** | **Tipo** | **Descrição** |
| --- | --- | --- |
| ChildrenAsTriggers | bool | Especifica se os controles filho invocar automaticamente a atualização em um postback. |
| RenderMode | Enum (bloco, embutido) | Especifica a maneira como o conteúdo será apresentado visualmente. |
| UpdateMode | Enum (sempre, condicional) | Especifica se o UpdatePanel é sempre atualizado durante uma renderização parcial ou se ele é atualizado somente quando um disparador for atingido. |

Propriedades de código:

| **Nome da propriedade** | **Tipo** | **Descrição** |
| --- | --- | --- |
| IsInPartialRendering | bool | Determina se o UpdatePanel é dar suporte a renderização parcial para a solicitação atual. |
| ContentTemplate | ITemplate | Obtém o modelo de marcação para a solicitação de atualização. |
| ContentTemplateContainer | Controle | Obtém o modelo de programação para a solicitação de atualização. |
| Gatilhos | UpdatePanel - TriggerCollection | Obtém a lista de gatilhos associados com o UpdatePanel atual. |

Métodos de código público:

| **Nome do método** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Update) | Void | Atualiza o UpdatePanel especificado por meio de programação. Permite que uma solicitação de servidor disparar uma renderização parcial de um UpdatePanel caso contrário, não seja disparado. |

Descendentes de marcação:

| **Marca** | **Descrição** |
| --- | --- |
| &lt;ContentTemplate&gt; | Especifica a marcação a ser usado para renderizar o resultado da renderização parcial. Filho de &lt;asp: UpdatePanel&gt;. |
| &lt;Gatilhos&gt; | Especifica uma coleção de *n* controles associados à atualização esse UpdatePanel. Filho de &lt;asp: UpdatePanel&gt;. |
| &lt;asp:AsyncPostBackTrigger&gt; | Especifica um gatilho que invoca uma renderização de página parcial para o UpdatePanel determinado. Isso pode ou não ser um controle como um descendente do UpdatePanel em questão. Granular para o nome do evento. Filho de &lt;gatilhos&gt;. |
| &lt;asp:PostBackTrigger&gt; | Especifica um controle que faz com que toda a página Atualizar. Isso pode ou não ser um controle como um descendente do UpdatePanel em questão. Granular para o objeto. Filho de &lt;gatilhos&gt;. |

O `UpdatePanel` controle é o controle que delimita o conteúdo do lado do servidor que levará a parte na funcionalidade das extensões AJAX do processamento parcial. Não há nenhum limite para o número de controles UpdatePanel que pode estar em uma página, e eles podem ser aninhados. Cada UpdatePanel é isolado, de forma que cada um pode trabalhar de forma independente (você pode ter dois UpdatePanels em execução ao mesmo tempo, renderização de diferentes partes da página, independentemente do postback da página).

O UpdatePanel controlar principalmente negociações com gatilhos de controle – por padrão, qualquer controle contido dentro de um UpdatePanel `ContentTemplate` que cria uma nova postagem for registrado como um disparador para o UpdatePanel. Isso significa que o UpdatePanel pode trabalhar com os controles de associação de dados padrão (como GridView), com controles de usuário, e eles podem ser programados no script.

Por padrão, quando uma renderização de página parcial é disparada, todos os controles UpdatePanel na página serão atualizados, ou não o UpdatePanel controla definidos gatilhos para tal ação. Por exemplo, se um UpdatePanel define um controle de botão, e esse controle de botão é clicado, todos os controles UpdatePanel na página serão atualizados por padrão. Isso ocorre porque, por padrão, o `UpdateMode` propriedade do UpdatePanel é definida como `Always`. Como alternativa, você pode definir a propriedade UpdateMode `Conditional`, o que significa que o UpdatePanel só será atualizado se um gatilho específico for atingido.

## <a name="custom-control-notes"></a>Notas de controle personalizado

Um UpdatePanel pode ser adicionado a qualquer controle de usuário ou um controle personalizado; No entanto, a página em que esses controles estão incluídos também deve incluir um controle ScriptManager com a propriedade EnablePartialRendering definida como **verdadeira**.

Uma maneira na qual você pode levar isso em conta ao usar controles personalizados da Web é substituir o protegido `CreateChildControls()` método da `CompositeControl` classe. Ao fazer isso, você pode injetar um UpdatePanel entre os filhos do controle e o mundo se você determinar que a página dá suporte à renderização parcial; Caso contrário, você pode simplesmente dispor em camadas os controles filho em um contêiner `Control` instância.

## <a name="updatepanel-considerations"></a>Considerações de UpdatePanel

O UpdatePanel opera como uma caixa preta, quebra automática de postbacks ASP.NET dentro do contexto de um JavaScript XMLHttpRequest. No entanto, há considerações de desempenho significativos para se ter em mente, tanto em termos de comportamento e a velocidade. Para entender como funciona o UpdatePanel, para que você possa decidir com melhor quando seu uso é apropriado, você deve examinar a troca de AJAX. O exemplo a seguir usa um site existente e, Mozilla Firefox com a extensão Firebug (o Firebug captura dados de XMLHttpRequest).

Considere a possibilidade de um formulário, entre outras coisas, com uma código postal de caixa de texto que deve para preencher um campo de cidade e estado em um formulário ou controle. Esse formulário, por fim, coleta informações de associação, incluindo nome, endereço e informações de contato de um usuário. Existem muitas considerações de design para levar em conta, com base nos requisitos de um projeto específico.


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image11.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image10.png)

([Clique para exibir a imagem em tamanho normal](understanding-partial-page-updates-with-asp-net-ajax/_static/image12.png))


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image14.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image13.png)

([Clique para exibir a imagem em tamanho normal](understanding-partial-page-updates-with-asp-net-ajax/_static/image15.png))


Na iteração original deste aplicativo, um controle foi criado que incorporado a totalidade dos dados de registro do usuário, incluindo o código postal, cidade e estado. Todo o controle foi encapsulado dentro de um UpdatePanel e solto em um formulário da Web. Quando o código postal é inserido pelo usuário, o UpdatePanel detecta o evento (o evento TextChanged correspondente no back-end, especificando os gatilhos ou usando a propriedade ChildrenAsTriggers definida como verdadeira). AJAX publica todos os campos dentro de UpdatePanel, capturados pelo FireBug (consulte o diagrama à direita).

Como indica a captura de tela, os valores de todos os controles dentro do UpdatePanel são entregues (nesse caso, eles estão vazios), bem como o campo de ViewState. No total, mais de 9kb de dados é enviada, quando na verdade apenas cinco bytes de dados eram necessários para fazer essa solicitação específica. A resposta é ainda mais inflada: no total, 57kb é enviado ao cliente, simplesmente para atualizar um campo de texto e um campo de lista suspensa.

Também pode ser de interesse para ver como o ASP.NET AJAX atualiza a apresentação. A parte da resposta da solicitação de atualização do UpdatePanel é mostrada na exibição do console Firebug à esquerda; ele é uma cadeia de delimitados desenvolvidas especialmente que é dividida pelo script de cliente e, em seguida, remontada na página. Especificamente, o ASP.NET AJAX define o *innerHTML* propriedade do elemento HTML no cliente que representa o UpdatePanel. Como o navegador gera novamente o DOM, há um pequeno atraso, dependendo da quantidade de informações que precisam ser processada.

A regeneração do DOM dispara uma série de problemas adicionais:


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image17.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image16.png)

([Clique para exibir a imagem em tamanho normal](understanding-partial-page-updates-with-asp-net-ajax/_static/image18.png))


- Se o elemento HTML com foco está inserido no UpdatePanel, ele perderá o foco. Portanto, para usuários que pressionado a tecla Tab para sair da caixa de texto de código postal, seu próximo destino teria sido a caixa de texto de cidade. No entanto, depois que o UpdatePanel atualizado a exibição, o formulário não teria foco e pressionar Tab teria iniciado realce os elementos de foco (como links).
- Se qualquer tipo de script personalizado do lado do cliente está em uso que acessa elementos de DOM, as referências mantidas pelas funções pode se tornarão extinto após um postback parcial.

UpdatePanels não pretendem ser pega-tudo soluções. Em vez disso, eles fornecem uma solução rápida para determinadas situações, incluindo a criação de protótipos, atualizações de controle pequeno e fornecem uma interface familiar para os desenvolvedores do ASP.NET que podem estar familiarizado com o modelo de objeto do .NET, mas menor do que isso com o DOM. Há uma série de alternativas que podem resultar em melhor desempenho, dependendo do cenário de aplicativo:

- Considere usar o PageMethods e JSON (JavaScript Object Notation) permite que o desenvolvedor chamar métodos estáticos em uma página como se uma chamada de serviço da web foi que está sendo invocada. Como os métodos são estáticos, sem estado é necessário; o chamador do script fornece os parâmetros, e o resultado é retornado de forma assíncrona.
- Considere o uso de um serviço Web e JSON se precisar de um único controle a ser usado em vários lugares através de um aplicativo. Novamente, isso requer muito pouco trabalho especial e funciona de forma assíncrona.

Incorporar a funcionalidade por meio de serviços Web ou métodos de página tem desvantagens também. Primeiramente, os desenvolvedores do ASP.NET é geralmente tendem a criar pequenos componentes de funcionalidade em controles de usuário (arquivos. ascx). Métodos de página não podem ser hospedados nesses arquivos; eles devem ser hospedados dentro da classe de página. aspx real. Da mesma forma, os serviços da Web, devem ser hospedados dentro da classe. asmx. Dependendo do aplicativo, essa arquitetura pode violar o princípio da responsabilidade única, em que a funcionalidade de um único componente agora é distribuída entre dois ou mais componentes físicos que podem ter pouco ou nenhum ties coesas.

Por fim, se um aplicativo exigir que os UpdatePanels sejam usados, as diretrizes a seguir devem ajudar a solução de problemas e manutenção.

- **Aninhar UpdatePanels mínimo possível, não apenas em unidades, mas também em unidades de código.** Por exemplo, um UpdatePanel em uma página que encapsula um controle, enquanto que o controle também contém um UpdatePanel, que contém o outro controle que contém um UpdatePanel, é ter o aninhamento de unidade entre. Isso ajuda a manter claras, quais elementos devem ser atualizados e impede que atualizações inesperadas para o filho UpdatePanels.
- **Manter o *ChildrenAsTriggers* propriedade definida como false e definir explicitamente o disparo de eventos.** Utilizando o `<Triggers>` coleção é uma maneira muito mais clara para manipular eventos e pode impedir que um comportamento inesperado, ajudando com tarefas de manutenção e forçar um desenvolvedor de participar de um evento.
- **Use a menor unidade possíveis para obter uma funcionalidade.** Conforme observado na discussão sobre o serviço de código postal, quebra automática de que apenas o mínimo necessário reduz o tempo para o servidor, o processamento total e a superfície da troca de cliente-servidor, melhorando o desempenho.

## <a name="the-updateprogress-control"></a>O controle UpdateProgress

## <a name="updateprogress-control-reference"></a>Referência de controle UpdateProgress

Propriedades de marcação habilitada:

| **Nome da propriedade** | **Tipo** | **Descrição** |
| --- | --- | --- |
| AssociatedUpdate-PanelID | Cadeia de Caracteres | Especifica a ID do UpdatePanel que este UpdateProgress deve relatar. |
| DisplayAfter | int | Especifica o tempo limite em milissegundos antes que esse controle é exibido após o início da solicitação assíncrona. |
| DataDrivenLayout | bool | Especifica se o progresso é renderizado dinamicamente. |

Descendentes de marcação:

| **Marca** | **Descrição** |
| --- | --- |
| &lt;ProgressTemplate&gt; | Contém o modelo de controle definido para o conteúdo que será exibido com esse controle. |

O controle UpdateProgress fornece uma medida de comentários para manter o interesse dos seus usuários ao fazer o trabalho necessário para o transporte para o servidor. Isso pode ajudar os usuários a saber o que você está fazendo algo, embora talvez não seja aparente, especialmente porque a maioria dos usuários são usados para a página de atualização e ver a barra de status de realce.

Como uma observação, controles UpdateProgress podem aparecer em qualquer lugar em uma hierarquia de página. No entanto, em casos em que um postback parcial é iniciado a partir de um UpdatePanel (onde um UpdatePanel é aninhado dentro de outro UpdatePanel) filho, postbacks que disparam o filho UpdatePanel fará com que os modelos de UpdateProgress a serem exibidos para o filho UpdatePanel, bem como o pai do UpdatePanel. Mas, se o gatilho é um filho direto do pai UpdatePanel, apenas os modelos de UpdateProgress associados ao pai serão exibidos.

## <a name="summary"></a>Resumo

As extensões do Microsoft ASP.NET AJAX são produtos sofisticados, projetados para ajudar na criação de conteúdo da web mais acessíveis e fornecer uma experiência de usuário mais rica para seus aplicativos web. Como parte das extensões AJAX do ASP.NET, os controles de renderização parcial da página, incluindo os controles ScriptManager, o UpdatePanel e UpdateProgress é alguns dos componentes mais visíveis do Kit de ferramentas.

O componente ScriptManager integra-se o provisionamento do cliente JavaScript para as extensões, bem como permite que os vários componentes de servidor e cliente trabalhar junto com o investimento de desenvolvimento mínimo.

O controle UpdatePanel é a caixa de mágico aparente - marcação dentro do UpdatePanel pode ter o code-behind do lado do servidor e não disparam uma atualização de página. Controles UpdatePanel podem ser aninhados e podem ser dependentes de controles em demais UpdatePanels. Por padrão, o UpdatePanels tratar quaisquer postagens invocadas por seus controles descendentes, embora essa funcionalidade pode ser bem ajustada, ou declarativamente por meio de programação.

Ao usar o controle UpdatePanel, os desenvolvedores devem estar cientes do impacto no desempenho que potencialmente pode surgir. Possíveis alternativas incluem serviços da web e os métodos de página, embora o design do aplicativo deve ser considerado.

O UpdateProgress controle permite que o usuário saiba o que ele não está sendo ignorado, e que a solicitação nos bastidores está acontecendo enquanto a página não fazer nada para responder à entrada do usuário. Ele também inclui a capacidade de anular os resultados do processamento parcial.

Juntas, essas ferramentas ajudam a criar uma experiência de usuário rica e perfeita, fazendo o trabalho de servidor menos aparente ao usuário e interromper o fluxo de trabalho menor.

## <a name="bio"></a>Biografia

Scott Cate tem trabalhado com tecnologias Web Microsoft desde 1997 e é o presidente da myKB.com ([www.myKB.com](http://www.myKB.com)) onde ele é especialista na escrita de ASP.NET com base em aplicativos com foco em soluções de Software da Base de dados de Conhecimento. Scott pode ser contatado através do email [ scott.cate@myKB.com ](mailto:scott.cate@myKB.com) ou em seu blog em [ScottCate.com](http://ScottCate.com)

> [!div class="step-by-step"]
> [Avançar](understanding-asp-net-ajax-updatepanel-triggers.md)

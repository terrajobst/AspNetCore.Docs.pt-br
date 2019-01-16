---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
title: Introdução ao ASP.NET 4.7 do Web Forms e Visual Studio 2017 | Microsoft Docs
author: Erikre
description: Esta série de tutoriais passo a passo lhe ensinará os conceitos básicos da criação de um aplicativo de Web Forms do ASP.NET usando o Microsoft Visual Studio e o ASP.NET 4.7
ms.author: riande
ms.date: 01/09/2019
ms.assetid: 9b96eaa1-8ef0-4338-a2e8-e0f970bfaf68
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
msc.type: authoredcontent
ms.openlocfilehash: fb41ce72e9454d8d670a0b95234d2bc3f909f0ee
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341543"
---
<a name="getting-started-with-aspnet-45-web-forms-and-visual-studio-2017"></a>Introdução ao ASP.NET 4.5 do Web Forms e Visual Studio 2017
====================

[Baixe o projeto de exemplo do Wingtip Toys (c#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) ou [Baixe o livro eletrônico (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)

Esta série de tutoriais mostra como criar um aplicativo de Web Forms do ASP.NET com o ASP.NET 4.5 e do Microsoft Visual Studio 2017. 

## <a name="introduction"></a>Introdução

Esta série de tutoriais orienta a criação de um aplicativo de Web Forms do ASP.NET usando o Visual Studio 2017 e o ASP.NET 4.5. Você criará um aplicativo chamado **Wingtip Toys** – um site da web vitrine simplificada vender itens online. Durante a série, novos recursos do ASP.NET 4.5 são realçados.

### <a name="target-audience"></a>Público-alvo

Desenvolvedores novos no Web Forms do ASP.NET são o público-alvo desta série de tutoriais.

Você deve ter algum conhecimento nas seguintes áreas:

- Programação orientada a objeto (OOP) e linguagens
- Desenvolvimento da Web (HTML, CSS e JavaScript)
- bancos de dados relacionais
- Arquitetura de N camadas

Para examinar essas áreas, considere a estudar o conteúdo a seguir:

- [Introdução ao Visual c#](https://msdn.microsoft.com/library/a72418yk.aspx)
- [Web Development](https://msdn.microsoft.com/beginner/bb308760.aspx), [HTML, CSS, JavaScript, SQL, PHP, JQuery](http://w3schools.com/)
- [Banco de dados relacional](http://en.wikipedia.org/wiki/Relational_database)
- [Arquitetura de várias camadas](http://en.wikipedia.org/wiki/Multitier_architecture)

### <a name="application-features"></a>Recursos do aplicativo

Os recursos do Web Form do ASP.NET apresentados nesta série incluem:

- O projeto de aplicativo Web (não o projeto de Site da Web)
- Web Forms
- Páginas mestras, configuração
- Bootstrap
- Entity Framework Code First, LocalDB
- Validação de solicitação
- Controles de dados fortemente tipados
- Model binding
- Anotações de dados
- Provedores de valor
- SSL e OAuth
- O ASP.NET Identity, configuração e autorização
- Validação não invasiva
- Roteamento
- Tratamento de erros do ASP.NET

### <a name="application-scenarios-and-tasks"></a>Tarefas e cenários de aplicativo

Tarefas de série de tutoriais incluem:

- Criando, revisando e execução de um novo projeto
- Criando uma estrutura de banco de dados
- Inicializando e a propagação de um banco de dados
- Personalizando a interface do usuário com estilos, gráficos e uma página mestra
- Adicionar páginas e navegação
- Exibindo detalhes de menu e os dados de produto
- Criando um carrinho de compras
- Suporte a adição de SSL e OAuth
- Adicionando um método de pagamento
- Incluindo uma função de administrador e um usuário para o aplicativo
- Restringir o acesso a páginas específicas e pasta
- Carregar um arquivo para o aplicativo web
- Implementar validação de entrada
- Registrando as rotas para o aplicativo web
- Implementando o tratamento de erros e log de erros

## <a name="overview"></a>Visão geral

Esta série de tutoriais é pretendido para alguém que esteja familiarizado com conceitos de programação, mas é novo no Web Forms do ASP.NET. Se você já estiver familiarizado com Web Forms do ASP.NET, esta série pode ainda ajudá-lo a aprender sobre novos recursos do ASP.NET 4.5. Para quem não está familiarizado com conceitos de programação e Web Forms do ASP.NET, consulte os tutoriais de formulários da Web adicionais fornecidos na [guia de Introdução](../../../index.md) seção no site da Web do ASP.NET.

O ASP.NET 4.5 fornecido nessa série de tutoriais inclui os seguintes recursos:

- Uma interface do usuário simple para a criação de projetos que oferece [suporte para muitas estruturas ASP.NET](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add) (Web Forms, MVC e API da Web).
- [O Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap), um layout, temas e estrutura de design responsivo.
- [O ASP.NET Identity](../../../../identity/index.md), um novo sistema de associação do ASP.NET que funciona da mesma em todas as estruturas do ASP.NET e funciona com o software que não seja o IIS de hospedagem na web.
- [Entity Framework 6](https://msdn.microsoft.com/data/ef.aspx)

  Uma atualização para o Entity Framework, permitindo que você:
  - Recuperar e manipular dados como objetos fortemente tipados
  - Acessar dados de forma assíncrona
  - Tratar falhas transitórias de conexão
  - Instruções SQL de log

Para obter uma lista completa de recurso ASP.NET 4.5, consulte [ASP.NET e Web Tools para notas de versão do Visual Studio 2013](../../../../visual-studio/overview/2013/release-notes.md).

### <a name="the-wingtip-toys-sample-application"></a>O aplicativo de exemplo Wingtip Toys

São as seguintes capturas de tela do aplicativo Web Forms do ASP.NET que você criar nessa série de tutoriais. Quando você executa o aplicativo no Visual Studio, a seguinte página inicial da web é exibida.

![A Wingtip Toys - página padrão](introduction-and-overview/_static/image1.png)

Você pode se registrar como um novo usuário ou entrar como um usuário existente. Painel de navegação superior contém links para as categorias de produto e seus produtos do banco de dados.

Se você selecionar **produtos**, todos os produtos disponíveis são exibidos. 

![A Wingtip Toys - produtos](introduction-and-overview/_static/image2.png)

Se você selecionar um produto específico, detalhes do produto são exibidos.


![A Wingtip Toys - detalhes do produto](introduction-and-overview/_static/image3.png)

Como um usuário, você pode registrar e entrar com a funcionalidade de padrão de modelo de formulários da Web. Este tutorial também explica como a entrar usando uma conta existente do Gmail. Além disso, você pode entrar como administrador para adicionar e remover os produtos de banco de dados.

![A Wingtip Toys - entrar](introduction-and-overview/_static/image4.png)

Depois que você entrar como um usuário, você pode adicionar produtos ao carrinho de compras e check-out com o PayPal. O aplicativo de exemplo é projetado para trabalhar em área restrita do desenvolvedor do PayPal. Nenhuma transação de dinheiro real ocorre.

![A Wingtip Toys - carrinho de compras](introduction-and-overview/_static/image5.png)

PayPal confirma suas informações de conta, a ordem e o pagamento.

![A Wingtip Toys - PayPal](introduction-and-overview/_static/image6.png)

Depois de retornar do PayPal, você pode revisar e concluir o pedido.

![A Wingtip Toys - revisão de ordem](introduction-and-overview/_static/image7.png)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se que o seguinte software está instalado no seu computador:

- [Microsoft Visual Studio 2017 ou o Microsoft Visual Studio Community 2017](https://visualstudio.microsoft.com/downloads/).

O .NET Framework é instalado automaticamente.

Esta série de tutoriais usa o Microsoft Visual Studio Community 2017. Você pode usar qualquer um dos que ou Microsoft Visual Studio 2017 para concluir esta série de tutoriais.

Observe o seguinte sobre o Visual Studio:

* Microsoft Visual Studio 2017 e Microsoft Visual Studio Community 2017 são denominados *Visual Studio* em toda esta série de tutoriais.

* Visual Studio 2017 está instalado ao lado de versões mais antigas já instaladas. Sites criados em versões anteriores podem ser abertos no Visual Studio 2017 e continuam a abrir nas versões anteriores.

* Na primeira vez que você iniciou o Visual Studio, supõe-se você selecionou o *desenvolvimento Web* configurações. Para obter mais informações, confira [Como: Selecione as configurações de ambiente de desenvolvimento da Web](https://msdn.microsoft.com/library/ff521558.aspx).

Depois de instalar os pré-requisitos, você está pronto para começar a criar o projeto Web apresentado nessa série de tutoriais.

## <a name="download-the-sample-application"></a>Baixe o aplicativo de exemplo

 Você pode baixar o applicatiion exemplo concluído na qualquer momento do site de exemplos do MSDN:

[Introdução ao ASP.NET 4.5 do Web Forms e Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (c#) 

 Este download tem os seguintes itens:

- O aplicativo de exemplo na *WingtipToys* pasta.
- Os recursos usados para criar o aplicativo de exemplo na *ativos WingtipToys* pasta na *WingtipToys* pasta.

O download é um *. zip* arquivo. Para ver o projeto completo que cria esta série de tutoriais, encontre e selecione os *C#* pasta do arquivo. zip. Salve o C# para a pasta que você pode usar para trabalhar com projetos do Visual Studio. Por padrão, a pasta de projetos do Visual Studio 2017 é:

<strong>C:\Users&#92;</strong><strong><em>&lt;username&gt;</em></strong><strong>\source\repos</strong>

Renomeie o ***c#*** pasta a ser ***WingtipToys***.

> [!NOTE]
> Se você já tiver uma pasta chamada *WingtipToys* na sua pasta de projetos, renomeie temporariamente essa pasta existente antes de renomear o *c#* pasta a ser *WingtipToys*.

Para executar o projeto concluído, abra o *WingtipToys* pasta e clique duas vezes o *WingtipToys.sln* arquivo. Visual Studio 2017 abre o projeto. Em seguida, clique com botão direito do *default. aspx* arquivo no **Gerenciador de soluções** e selecione **exibir no navegador**.

## <a name="take-a-aspnet-web-forms-quiz-to-review-content"></a>Faça um teste de Web Forms do ASP.NET para examinar o conteúdo

Depois de concluir a série de tutoriais, faça um teste para testar seu conhecimento e reforçar os conceitos-chave. Cada pergunta fornece uma explicação e links para orientações adicionais.

 * [ASP.NET Web Forms do teste](https://blogs.msdn.microsoft.com/erikreitan/2016/01/08/asp-net-web-forms-quiz/) 

## <a name="tutorial-support-and-comments"></a>Comentários e suporte de tutorial

Para perguntas e comentários, use a seção de p e r incluída na [Introdução ao Web Forms do ASP.NET 4.5 e Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) página de exemplo.

Comentários sobre esta série de tutoriais são bem-vindos. Quando esta série de tutoriais é atualizada, todos os esforços é feito para considerar as correções ou sugestões para melhorias.

Se ocorrer um erro, as mensagens de erro correspondente pode ser confusas, sem nenhuma boa explicação sobre como corrigi-lo. Para obter ajuda, você pode verificar a [fóruns do ASP.NET](https://forums.asp.net/). Outra boa fonte é a seção p e r a [Introdução ao Web Forms do ASP.NET 4.5 e Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) página de exemplo. 

> [!div class="step-by-step"]
> [Avançar](create-the-project.md)

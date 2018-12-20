---
uid: web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs
title: 'Laboratório prático: Criar um aplicativo de página única (SPA) com o API Web ASP.NET e angular. js | Microsoft Docs'
author: rick-anderson
description: Em aplicativos web tradicionais, o cliente (navegador) inicia a comunicação com o servidor, solicitando uma página. O servidor, em seguida, processa a solicitação...
ms.author: riande
ms.date: 09/30/2015
ms.assetid: 719727b7-bef3-45ad-bfe9-ba5bcdb2305f
msc.legacyurl: /web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs
msc.type: authoredcontent
ms.openlocfilehash: 3c3557bb2be2807b11874937fcc629b5b773e463
ms.sourcegitcommit: a4dcca4f1cb81227c5ed3c92dc0e28be6e99447b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48912248"
---
<a name="hands-on-lab-build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs"></a>Laboratório prático: Criar um aplicativo de página única (SPA) com o API Web ASP.NET e angular. js
====================
por [Web Camps equipe](https://twitter.com/webcamps)

[Baixe o Kit de treinamento do Web Camps](http://aka.ms/webcamps-training-kit)

> Em aplicativos web tradicionais, o cliente (navegador) inicia a comunicação com o servidor, solicitando uma página. O servidor, em seguida, processa a solicitação e envia o HTML da página para o cliente. As interações subsequentes com a página – por exemplo, o usuário navega para um link ou envia um formulário com dados – uma nova solicitação é enviada ao servidor, e o fluxo será iniciado novamente: o servidor processa a solicitação e envia uma nova página para o navegador em resposta à nova solicitação de ação ED pelo cliente.
> 
> Em aplicativos de única página (SPAs) toda a página é carregada no navegador após a solicitação inicial, mas as interações subsequentes ocorrem por meio de solicitações Ajax. Isso significa que o navegador é necessário atualizar somente a parte da página que foi alterado; Não há nenhuma necessidade de recarregar a página inteira. A abordagem SPA reduz o tempo gasto pelo aplicativo para responder às ações do usuário, resultando em uma experiência mais fluida.
> 
> A arquitetura de um SPA envolve determinados desafios que não estão presentes nos aplicativos web tradicionais. No entanto, tecnologias, como API Web ASP.NET emergentes, estruturas de JavaScript como AngularJS e novos recursos de estilo fornecidos pelo CSS3 facilitam realmente a design e criação de SPAs.
> 
> Neste laboratório em mãos, você irá tirar proveito dessas tecnologias para implementar o nerd do teste, um site de desafio com base no conceito SPA. Primeiro, você implementará a camada de serviço com a API Web do ASP.NET para expor os pontos de extremidade necessários para recuperar as perguntas de teste e armazenar as respostas. Em seguida, você irá criar uma interface de usuário rica e responsiva usando efeitos de transformação do AngularJS e CSS3.
> 
> Todo o código de exemplo e trechos de código são incluídos no Web Camps treinamento Kit, disponível em [ http://aka.ms/webcamps-training-kit ](http://aka.ms/webcamps-training-kit).


## <a name="overview"></a>Visão geral

<a id="Objectives"></a>
### <a name="objectives"></a>Objetivos

Neste laboratório prático, você aprenderá como:

- Crie um serviço de API Web ASP.NET para enviar e receber dados JSON
- Criar uma interface de usuário responsiva, usando o AngularJS
- Aprimorar a experiência de interface do usuário com transformações do CSS3

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Pré-requisitos

O exemplo a seguir é necessário para concluir este laboratório prático:

- [O Visual Studio Express 2013 para Web](https://www.microsoft.com/visualstudio/) ou maior

<a id="Setup"></a>
### <a name="setup"></a>Configuração

Para executar os exercícios neste laboratório prático, você precisará configurar seu ambiente pela primeira vez.

1. Abra o Windows Explorer e navegue para o laboratório **origem** pasta.
2. Clique duas vezes em **Setup. cmd** e selecione **executar como administrador** para iniciar o processo de instalação que irá configurar seu ambiente e instalem os trechos de código do Visual Studio para este laboratório.
3. Se a caixa de diálogo controle de conta de usuário for mostrada, confirme a ação para continuar.

> [!NOTE]
> Verifique se que você tiver marcado todas as dependências para este laboratório antes de executar a instalação.


<a id="CodeSnippets"></a>
### <a name="using-the-code-snippets"></a>Usando os trechos de código

Em todo o documento de laboratório, você será instruído a inserir blocos de código. Para sua conveniência, a maioria desse código é fornecido como o Visual Studio trechos de código que pode ser acessada de dentro do Visual Studio 2013 para evitar ter que adicioná-lo manualmente.

> [!NOTE]
> Cada exercício é acompanhado por uma solução inicial localizada na **começar** pasta do exercício que permite que você siga cada exercício independentemente dos outros. Esteja ciente de que os trechos de código são adicionados durante um exercício estão ausentes desses iniciando soluções e podem não funcionar até concluir o exercício. Dentro do código-fonte para um exercício, você também encontrará uma **final** pasta que contém uma solução do Visual Studio com o código que é o resultado de concluir as etapas no exercício correspondente. Você pode usar essas soluções como uma diretriz se você precisar de ajuda adicional ao trabalhar com este laboratório prático.


* * *

<a id="Exercises"></a>
## <a name="exercises"></a>Exercícios

Este laboratório prático inclui os seguintes exercícios:

1. [Criando uma API da Web](#Exercise1)
2. [Criando uma Interface do SPA](#Exercise2)

Tempo estimado para concluir este laboratório: **60 minutos**

> [!NOTE]
> Quando você inicia o Visual Studio pela primeira vez, você deve selecionar uma das coleções de configurações predefinidas. Cada coleção predefinida foi projetada para corresponder a um estilo de desenvolvimento específico e determina o comportamento do editor, layouts de janela, trechos de código IntelliSense e opções da caixa de diálogo. Os procedimentos neste laboratório descrevem as ações necessárias para realizar uma determinada tarefa no Visual Studio ao usar o **configurações gerais de desenvolvimento** coleção. Se você escolher uma coleção de configurações diferentes para seu ambiente de desenvolvimento, pode haver diferenças nas etapas que você deve levar em conta.


<a id="Exercise1"></a>
### <a name="exercise-1-creating-a-web-api"></a>Exercício 1: Criar uma API da Web

Uma das principais partes de um SPA é a camada de serviço. Ele é responsável por processar chamadas Ajax enviadas pela interface do usuário e os dados retornados em resposta a essa chamada. Os dados recuperados devem ser apresentados em um formato legível para ser analisado e consumido pelo cliente.

A estrutura da API Web é parte da pilha do ASP.NET e é projetada para facilitar a implementar os serviços HTTP, geralmente, enviar e receber dados formatados em XML ou JSON por meio de uma API RESTful. Neste exercício, você criará o site da Web para hospedar o aplicativo Geek Quiz e, em seguida, implementar o serviço de back-end para expor e persistir os dados de teste usando a API Web do ASP.NET.

<a id="Ex1Task1"></a>
#### <a name="task-1--creating-the-initial-project-for-geek-quiz"></a>Tarefa 1 – criar o projeto inicial para Geek Quiz

Nesta tarefa você começará criando um novo projeto ASP.NET MVC com suporte para API Web ASP.NET com base nas **One ASP.NET** tipo que vem com o Visual Studio de projeto. **One ASP.NET** unifica todas as tecnologias do ASP.NET e lhe dá a opção de misturar e combiná-los conforme desejado. Em seguida, você irá adicionar classes de modelo do Entity Framework e o initializator de banco de dados para inserir as perguntas de teste.

1. Abra **Visual Studio Express 2013 para Web** e selecione **arquivo | Novo projeto...**  para iniciar uma nova solução.

    ![Criar um novo projeto](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image1.png "criando um novo projeto")

    *Criar um novo projeto*
2. No **novo projeto** caixa de diálogo, selecione **aplicativo Web ASP.NET** sob o **Visual c# | Web** guia. Certifique-se **.NET Framework 4.5** é selecionada, nomeie- *GeekQuiz*, escolha um **local** e clique em **Okey**.

    ![Criar um novo projeto de aplicativo Web ASP.NET](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image2.png "criando um novo projeto de aplicativo Web ASP.NET")

    *Criar um novo projeto de aplicativo Web ASP.NET*
3. No **novo projeto ASP.NET** caixa de diálogo, selecione o **MVC** modelo e selecione o **API da Web** opção. Além disso, certifique-se de que o **autenticação** opção for definida como **contas de usuário individuais**. Clique em **OK** para continuar.

    ![Criar um novo projeto com o modelo MVC, incluindo componentes de API da Web](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image3.png)

    *Criar um novo projeto com o modelo MVC, incluindo componentes de API da Web*
4. No **Gerenciador de soluções**, clique com botão direito a **modelos** pasta da **GeekQuiz** do projeto e selecione **adicionar | Item existente...** .

    ![Adicionando um item existente](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image4.png "adicionando um item existente")

    *Adicionando um item existente*
5. No **Adicionar Item existente** caixa de diálogo, navegue até a **modelos/de ativos de origem** pasta e selecione todos os arquivos. Clique em **Adicionar**.

    ![Adicionar os ativos de modelo](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image5.png "adicionando os ativos de modelo")

    *Adicionar os ativos de modelo*

    > [!NOTE]
    > Ao adicionar esses arquivos, você está adicionando o modelo de dados, o contexto de banco de dados do Entity Framework e o inicializador de banco de dados para o aplicativo de teste "geek".
    > 
    > **Entity Framework (EF)** é um mapeador relacional de objeto (ORM) que permite que você crie aplicativos de acesso de dados por meio da programação com um modelo de aplicativo conceitual em vez de programação diretamente usando um esquema de armazenamento relacional. Você pode aprender mais sobre o Entity Framework [aqui](../../../entity-framework.md).
    > 
    > A seguir está uma descrição das classes que você acabou de adicionar:
    > 
    > - **TriviaOption:** representa uma única opção associada a uma pergunta de teste
    > - **TriviaQuestion:** representa uma pergunta de teste e expõe as opções associadas por meio de **opções** propriedade
    > - **TriviaAnswer:** representa a opção selecionada pelo usuário em resposta a uma pergunta de teste
    > - **TriviaContext:** representa o contexto de banco de dados do Entity Framework do aplicativo Geek do teste. Essa classe deriva **DContext** e expõe **DbSet** propriedades que representam coleções de entidades descritas acima.
    > - **TriviaDatabaseInitializer:** a implementação do inicializador do Entity Framework para o **TriviaContext** classe que herda de **CreateDatabaseIfNotExists**. O comportamento padrão dessa classe é criar o banco de dados somente se ele não existir, a inserção de entidades especificado na **semente** método.
6. Abra o **Global.asax.cs** de arquivo e adicione a seguinte instrução using.

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample1.cs)]
7. Adicione o seguinte código no início do **Application\_começar** método para definir a **TriviaDatabaseInitializer** do inicializador de banco de dados.

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample2.cs)]
8. Modificar a **Home** controlador para restringir o acesso a usuários autenticados. Para fazer isso, abra o **HomeController.cs** dentro do arquivo a **controladores** pasta e adicione o **autorizar** atributo para o **HomeController**definição de classe.

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample3.cs)]

    > [!NOTE]
    > O **autorizar** filtrar verifica se o usuário é autenticado. Se o usuário não é autenticado, ele retorna o código de status HTTP 401 (não autorizado) sem invocar a ação. Você pode aplicar o filtro globalmente, no nível do controlador ou no nível de ações individuais.
9. Agora você irá personalizar o layout de páginas da web e a identidade visual. Para fazer isso, abra o  **\_layout. cshtml** dentro do arquivo a **exibições | Compartilhado** pasta e atualizar o conteúdo do **&lt;título&gt;** elemento, substituindo *meu aplicativo ASP.NET* com *Geek Quiz* .

    [!code-cshtml[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample4.cshtml)]
10. No mesmo arquivo, atualizar a barra de navegação, removendo o *sobre* e *entre em contato com* links e renomear o *página inicial* vincular ao *reproduzir*. Além disso, renomeie o *nome do aplicativo* vincular ao *Geek Quiz*. O HTML para a barra de navegação deve parecer com o código a seguir.

    [!code-cshtml[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample5.cshtml)]
11. Atualize o rodapé da página de layout, substituindo *meu aplicativo ASP.NET* com *Geek Quiz*. Para fazer isso, substitua o conteúdo a **&lt;rodapé&gt;** elemento com o seguinte código realçado.

    [!code-html[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample6.html)]

<a id="Ex1Task2"></a>
#### <a name="task-2--creating-the-triviacontroller-web-api"></a>Tarefa 2 – criar a API da Web TriviaController

Na tarefa anterior, você criou a estrutura inicial do aplicativo web Geek do teste. Agora, você criará um serviço de API da Web simples que interage com o modelo de dados de teste e expõe as seguintes ações:

- **GET/api/desafios**: recupera a próxima pergunta na lista de teste a serem respondidas pelo usuário autenticado.
- **POST/api/desafios**: armazena a resposta de teste especificada pelo usuário autenticado.

Você usará as ferramentas de Scaffolding do ASP.NET fornecidas pelo Visual Studio para criar a linha de base para a classe de controlador de API da Web.

1. Abra o **WebApiConfig.cs** dentro do arquivo a **App\_iniciar** pasta. Esse arquivo define a configuração do serviço de API da Web, semelhante a como as rotas são mapeadas para ações do controlador de API da Web.
2. Adicione a seguinte instrução using no início do arquivo.

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample7.cs)]
3. Adicione o seguinte código realçado para o **registrar** método para definir globalmente o formatador para os dados JSON recuperados pelos métodos de ação de API da Web.

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample8.cs)]

    > [!NOTE]
    > O **CamelCasePropertyNamesContractResolver** converte automaticamente os nomes de propriedade *camel* caso, que é a convenção geral para nomes de propriedade em JavaScript.
4. No **Gerenciador de soluções**, clique com botão direito o **controladores** pasta do **GeekQuiz** do projeto e selecione **adicionar | Novo Item com Scaffold...** .

    ![Criando um novo item com Scaffold](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image6.png "criando um novo item com Scaffold")

    *Criando um novo item com Scaffold*
5. No **adicionar Scaffold** diálogo caixa, certifique-se de que o **comum** nó é selecionado no painel esquerdo. Em seguida, selecione a **controlador Web API 2 - vazio** modelo no painel central e clique **Add**.

    ![Selecionando o modelo Web API 2 Controller vazio](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image7.png "selecionando o modelo Web API 2 Controller vazio")

    *Selecionando o modelo Web API 2 Controller vazio*

    > [!NOTE]
    > **Scaffolding do ASP.NET** é uma estrutura de geração de código para aplicativos Web do ASP.NET. Visual Studio 2013 inclui geradores de código previamente instalados para projetos MVC e API da Web. Você deve usar o scaffolding em seu projeto quando você deseja adicionar rapidamente o código que interage com os modelos de dados para reduzir a quantidade de tempo necessário para desenvolver as operações de dados padrão.
    > 
    > O processo de scaffolding também garante que todas as dependências necessárias são instaladas no projeto. Por exemplo, se você iniciar com um projeto ASP.NET vazio e, em seguida, utilizar o scaffolding para adicionar um controlador de API da Web, os pacotes do NuGet da API Web necessários e as referências são adicionadas ao seu projeto automaticamente.
6. No **Adicionar controlador** caixa de diálogo, digite *TriviaController* no **nome do controlador** caixa de texto e clique em **adicionar**.

    ![Adicionando o controlador de desafios](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image8.png "adicionando o controlador de desafios")

    *Adicionando o controlador de desafios*
7. O **TriviaController.cs** arquivo, em seguida, é adicionado à **controladores** pasta dos **GeekQuiz** projeto que contém um vazio **TriviaController** classe. Adicione o seguinte usando instruções no início do arquivo.

    (Código de trecho de código – *TriviaControllerUsings AspNetWebApiSpa - Ex1 -*)

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample9.cs)]
8. Adicione o seguinte código no início do **TriviaController** classe para definir, inicializar e descartar os **TriviaContext** instância no controlador.

    (Código de trecho de código – *TriviaControllerContext AspNetWebApiSpa - Ex1 -*)

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample10.cs)]

    > [!NOTE]
    > O **Dispose** método de **TriviaController** invoca o **Dispose** o método da **TriviaContext** instância, o que garante que todos os os recursos usados pelo objeto de contexto são liberados quando a **TriviaContext** instância é descartada ou jogada fora. Isso inclui o fechamento de todas as conexões de banco de dados abertas pelo Entity Framework.
9. Adicione o seguinte método auxiliar no final de **TriviaController** classe. Esse método recupera as seguintes perguntas de teste do banco de dados a serem respondidas pelo usuário especificado.

    (Código de trecho de código – *TriviaControllerNextQuestion AspNetWebApiSpa - Ex1 -*)

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample11.cs)]
10. Adicione o seguinte **Obtenha** método de ação para o **TriviaController** classe. Esse método de ação chama o **NextQuestionAsync** método auxiliar definido na etapa anterior para recuperar a próxima pergunta para o usuário autenticado.

    (Código de trecho de código – *TriviaControllerGetAction AspNetWebApiSpa - Ex1 -*)

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample12.cs)]
11. Adicione o seguinte método auxiliar no final de **TriviaController** classe. Este método armazena a resposta especificada no banco de dados e retorna um valor booliano que indica se a resposta está correta.

    (Código de trecho de código – *TriviaControllerStoreAsync AspNetWebApiSpa - Ex1 -*)

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample13.cs)]
12. Adicione o seguinte **Post** método de ação para o **TriviaController** classe. Esse método de ação associa a resposta para o usuário autenticado e chama o **StoreAsync** método auxiliar. Em seguida, ele envia uma resposta com o valor booliano retornado pelo método auxiliar.

    (Código de trecho de código – *TriviaControllerPostAction AspNetWebApiSpa - Ex1 -*)

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample14.cs)]
13. Modificar o controlador de API da Web para restringir o acesso a usuários autenticados, adicionando a **Authorize** atributo para o **TriviaController** definição de classe.

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample15.cs)]

<a id="Ex1Task3"></a>
#### <a name="task-3--running-the-solution"></a>Tarefa 3 – executar a solução

Nesta tarefa, você verificará se o serviço de API da Web que você criou na tarefa anterior está funcionando conforme o esperado. Você usará o Internet Explorer **ferramentas de desenvolvedor F12** para capturar o tráfego de rede e inspecione a resposta completa do serviço de API da Web.

> [!NOTE]
> Certifique-se de que **Internet Explorer** está selecionado na **iniciar** botão localizado na barra de ferramentas do Visual Studio.
> 
> ![Opção do Internet Explorer](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image9.png)


1. Pressione **F5** para executar a solução. O **faça logon no** página deve ser exibida no navegador.

    > [!NOTE]
    > Quando o aplicativo é iniciado, a rota do padrão MVC é disparada, o que, por padrão é mapeado para o **índice** ação da **HomeController** classe. Uma vez que **HomeController** é restrito a usuários autenticados (Lembre-se de que você decorado que dessa classe com o **autorizar** atributo no Exercício 1) e não há nenhum usuário autenticado ao mesmo tempo, o aplicativo Redireciona a solicitação original para a página de logon.

    ![Executar a solução](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image10.png "executar a solução")

    *Executar a solução*
2. Clique em **registrar** para criar um novo usuário.

    ![Registrar um novo usuário](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image11.png "registrar um novo usuário")

    *Registrar um novo usuário*
3. No **registre** , insira um **nome de usuário** e **senha**e, em seguida, clique em **registrar**.

    ![Página registrar](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image12.png "página de registro")

    *Página de registro*
4. O aplicativo registra a nova conta e o usuário é autenticado e redirecionado de volta para a home page.

    ![Usuário é autenticado](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image13.png "usuário autenticado")

    *Autenticação do usuário*
5. No navegador, pressione **F12** para abrir o **ferramentas de desenvolvedor** painel. Pressione **CTRL + 4** ou clique no **rede** ícone e, em seguida, clique botão de seta verde para começar a capturar o tráfego de rede.

    ![Iniciando captura de rede da API Web](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image14.png "captura de rede iniciando a API da Web")

    *Iniciando captura de rede de API da Web*
6. Acrescente **api/desafios** para a URL na barra de endereços do navegador. Agora, você inspecionará os detalhes da resposta do **Obtenha** método de ação **TriviaController**.

    ![Recuperando os próxima pergunta que os dados por meio da API Web](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image15.png "recuperar os dados da próxima pergunta por meio da API da Web")

    *Recuperando os próxima pergunta que os dados por meio da API da Web*

    > [!NOTE]
    > Quando o download for concluído, você precisará fazer uma ação com o arquivo baixado. Deixe a caixa de diálogo aberta para que seja possível assistir o conteúdo da resposta por meio da janela de ferramenta de desenvolvedores.
7. Agora você irá inspecionar o corpo da resposta. Para fazer isso, clique o **detalhes** guia e, em seguida, clique em **corpo da resposta**. Você pode verificar que os dados baixados são um objeto com as propriedades **opções** (que é uma lista de **TriviaOption** objetos), **id** e **título** que correspondam ao **TriviaQuestion** classe.

    ![Exibindo o corpo de resposta da API Web](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image16.png "exibindo o corpo de resposta da API da Web")

    *Corpo de resposta da API da Web exibindo*
8. Volte para o Visual Studio e pressione **SHIFT + F5** para parar a depuração.

<a id="Exercise2"></a>
### <a name="exercise-2-creating-the-spa-interface"></a>Exercício 2: Criar a Interface do SPA

Neste exercício primeiro você criará a parte de front-end da web do Geek Quiz, concentrando-se no aplicativo de página única interação usando **AngularJS**. Em seguida, você será aprimorar a experiência do usuário com o CSS3 para executar animações avançadas e fornecer um efeito visual de alternância quando a transição de uma pergunta para a próxima de contexto.

<a id="Ex2Task1"></a>
#### <a name="task-1--creating-the-spa-interface-using-angularjs"></a>Tarefa 1 – criar a Interface do SPA usando o AngularJS

Essa tarefa, você usará **AngularJS** para implementar o lado do cliente do aplicativo Geek do teste. **AngularJS** é uma estrutura de JavaScript de código-fonte aberto que aumenta a aplicativos baseados em navegador com *Model-View-Controller* capacidade (MVC), facilitando tanto de desenvolvimento e teste.

Você irá iniciar instalando AngularJS por meio do Console do Gerenciador de pacotes do Visual Studio. Em seguida, você criará o controlador para fornecer o comportamento do aplicativo Geek de teste e o modo de exibição para renderizar as perguntas de teste e respostas usando o mecanismo de modelo do AngularJS.

> [!NOTE]
> Para obter mais informações sobre como AngularJS, consulte [ [ http://angularjs.org/ ](http://angularjs.org/) ](http://angularjs.org/).


1. Abra **Visual Studio Express 2013 para Web** e abra o **GeekQuiz.sln** solução localizada no **origem/o Ex2-CreatingASPAInterface/início** pasta. Como alternativa, você pode continuar com a solução que você obteve no exercício anterior.
2. Abra o **Package Manager Console** de **ferramentas** > **NuGet Package Manager**. Digite o seguinte comando para instalar o **AngularJS.Core** pacote do NuGet.

    [!code-powershell[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample16.ps1)]
3. No **Gerenciador de soluções**, com o botão direito do **Scripts** pasta do **GeekQuiz** do projeto e selecione **adicionar | Nova pasta**. Nomeie a pasta **app** e pressione **Enter**.
4. Clique com botão direito do **app** pasta que você acabou de criar e selecione **adicionar | Arquivo JavaScript**.

    ![Criando um novo arquivo JavaScript](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image17.png)

    *Criando um novo arquivo JavaScript*
5. No **especificar nome para o Item** caixa de diálogo, digite *controlador de teste* no **nome do Item** caixa de texto e clique em **Okey**.

    ![Nomear o novo arquivo JavaScript](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image18.png)

    *Nomear o novo arquivo JavaScript*
6. No **teste controller.js** do arquivo, adicione o seguinte código para declarar e inicializar o AngularJS **QuizCtrl** controlador.

    (Código de trecho de código – *AngularQuizController AspNetWebApiSpa - o Ex2 -*)

    [!code-javascript[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample17.js)]

    > [!NOTE]
    > Função do construtor de **QuizCtrl** controlador espera um parâmetro injectable chamado **$scope**. O estado inicial do escopo deve ser configurado na função de construtor, anexando propriedades para o **$scope** objeto. As propriedades contêm o **modelo de exibição**e poderá ser acessado no modelo quando o controlador é registrado.
    > 
    > O **QuizCtrl** controlador é definido dentro de um módulo chamado **QuizApp**. Os módulos são unidades de trabalho que permitem a você dividir seu aplicativo em componentes separados. As principais vantagens do uso de módulos é que o código é mais fácil de entender e facilita os testes de unidade, a capacidade de reutilização e a facilidade de manutenção.
7. Agora você irá adicionar comportamento para o escopo para reagir a eventos disparados do modo de exibição. Adicione o seguinte código no final do **QuizCtrl** controlador para definir o **nextQuestion** funcionar a **$scope** objeto.

    (Código de trecho de código – *AngularQuizControllerNextQuestion AspNetWebApiSpa - o Ex2 -*)

    [!code-javascript[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample18.js)]

    > [!NOTE]
    > Essa função recupera a próxima pergunta do **Trívia** API da Web criado no exercício anterior e anexa os pergunta que os dados para o **$scope** objeto.
8. Insira o seguinte código no final dos **QuizCtrl** controlador para definir o **sendAnswer** funcionar no **$scope** objeto.

    (Código de trecho de código – *AngularQuizControllerSendAnswer AspNetWebApiSpa - o Ex2 -*)

    [!code-javascript[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample19.js)]

    > [!NOTE]
    > Essa função envia a resposta selecionada pelo usuário para o **Trívia** API Web e armazena o resultado – por exemplo, se a resposta estiver correta ou não – na **$scope** objeto.
    > 
    > O **nextQuestion** e **sendAnswer** funções acima usam o AngularJS **$http** objeto abstrair a comunicação com a API da Web via XMLHttpRequest Objeto de JavaScript do navegador. AngularJS oferece suporte a outro serviço que traz um nível mais alto de abstração para executar operações CRUD em relação a um recurso por meio de APIs RESTful. O AngularJS **$resource** objeto tem métodos de ação que fornecem os comportamentos de alto nível sem a necessidade de interagir com o **$http** objeto. Considere o uso de **$resource** objeto em cenários que requer o modelo CRUD (para obter informações, consulte o [$resource documentação](https://docs.angularjs.org/api/ngResource/service/$resource)).
9. A próxima etapa é criar o modelo de AngularJS que define o modo de exibição para o teste. Para fazer isso, abra o **index. cshtml** dentro do arquivo a **exibições | Página inicial** pasta e substitua o conteúdo pelo código a seguir.

    (Código de trecho de código – *GeekQuizView AspNetWebApiSpa - o Ex2 -*)

    [!code-cshtml[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample20.cshtml)]

    > [!NOTE]
    > O modelo de AngularJS é uma especificação declarativa que usa as informações do modelo e o controlador para transformar a marcação estática em exibição dinâmica do que o usuário vê no navegador. A seguir está exemplos de AngularJS elementos e atributos do elemento que podem ser usados em um modelo:
    > 
    > - O **app-ng** diretiva diz ao AngularJS do elemento DOM que representa o elemento raiz do aplicativo.
    > - O **ng-controller** diretiva anexa um controlador para o DOM no ponto onde a diretiva é declarada.
    > - A notação de chave de abertura **{{}}** denota ligações para as propriedades de escopo definidas no controlador.
    > - O **ng-click** diretiva é usada para invocar as funções definidas no escopo em resposta a cliques do usuário.
10. Abra o **CSS** dentro do arquivo a **conteúdo** pasta e adicione os seguintes estilos realçados no final do arquivo para fornecer uma aparência para o modo de exibição de teste.

    (Código de trecho de código – *GeekQuizStyles AspNetWebApiSpa - o Ex2 -*)

    [!code-css[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample21.css)]

<a id="Ex2Task2"></a>
#### <a name="task-2--running-the-solution"></a>Tarefa 2 – executar a solução

Nesta tarefa, você vai executar a solução usando o novo usuário interface compilado com o AngularJS para responder a algumas das perguntas de teste.

1. Pressione **F5** para executar a solução.
2. Registre uma nova conta de usuário. Para fazer isso, siga as etapas de Registro descritas no Exercício 1, tarefa 3.

    > [!NOTE]
    > Se você estiver usando a solução do exercício anterior, você pode fazer logon com a conta de usuário que você criou antes.
3. O **Home** página deve ser exibida, mostrando a primeira pergunta do teste. Responda à pergunta, clicando em uma das opções. Isso vai disparar a **sendAnswer** função definida anteriormente, que envia a opção selecionada para o **Trívia** API da Web.

    ![Responder a uma pergunta](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image19.png "responder a uma pergunta")

    *Responder a uma pergunta*
4. Depois de clicar em um dos botões, a resposta deverá aparecer. Clique em **próxima pergunta** para mostrar a pergunta a seguir. Isso vai disparar a **nextQuestion** função definida no controlador.

    ![Solicitando a próxima pergunta](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image20.png "solicitando a próxima pergunta")

    *Solicitando a próxima pergunta*
5. A próxima pergunta deve aparecer. Continue respondendo perguntas quantas vezes desejar. Depois de concluir todas as perguntas, você deve retornar à primeira pergunta.

    ![Outra pergunta](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image21.png "outra pergunta")

    *Próxima pergunta*
6. Volte para o Visual Studio e pressione **SHIFT + F5** para parar a depuração.

<a id="Ex2Task3"></a>
#### <a name="task-3--creating-a-flip-animation-using-css3"></a>Tarefa 3 – criar uma animação de Flip usando CSS3

Nesta tarefa, você usará propriedades CSS3 para executar animações avançadas, adicionando um efeito de flip quando uma pergunta é respondida e quando a próxima pergunta é recuperada.

1. No **Gerenciador de soluções**, com o botão direito do **conteúdo** pasta do **GeekQuiz** do projeto e selecione **adicionar | Item existente...** .

    ![Adicionando um item existente para a pasta Content](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image22.png "adicionando um item existente para a pasta de conteúdo")

    *Adicionando um item existente para a pasta de conteúdo*
2. No **Adicionar Item existente** caixa de diálogo, navegue até a **origem/ativos** pasta e selecione **Flip.css**. Clique em **Adicionar**.

    ![Adicionando o arquivo de Flip.css de ativos](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image23.png "adicionando o arquivo de Flip.css de ativos")

    *Adicionando o arquivo de Flip.css de ativos*
3. Abra o **Flip.css** arquivo que você acabou de adicionar e inspecione seu conteúdo.
4. Localize o **inverter transformação** comentário. Os estilos inferiores a esse comentário usam o CSS **perspectiva** e **rotateY** transformações para gerar um &quot;cartão flip&quot; efeito.

    [!code-css[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample22.css)]
5. Localize o **ocultar parte traseira do painel durante flip** comentário. O estilo abaixo desse comentário oculta o lado de trás as faces quando eles estão enfrentando distante do visualizador, definindo o **visibilidade de seleção de face** propriedade CSS *oculto*.

    [!code-css[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample23.css)]
6. Abra o **BundleConfig.cs** dentro do arquivo a **aplicativo\_iniciar** pasta e adicionar a referência ao **Flip.css** de arquivo no **&quot;~/Content/css&quot;** pacote de estilo

    [!code-csharp[Main](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/samples/sample24.cs)]
7. Pressione **F5** para executar a solução e faça logon com suas credenciais.
8. Responda a uma pergunta, clicando em uma das opções. Observe o efeito de flip durante a transição entre os modos de exibição.

    ![Responder a uma pergunta com o efeito de flip](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image24.png "responder a uma pergunta com o efeito de flip")

    *Responder a uma pergunta com o efeito de flip*
9. Clique em **próxima pergunta** para recuperar a pergunta a seguir. O efeito de flip deverá aparecer novamente.

    ![Recuperando a pergunta a seguir com o efeito de flip](build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs/_static/image25.png "Recuperando a pergunta a seguir com o efeito de flip")

    *Recuperando a pergunta a seguir com o efeito de flip*

* * *

<a id="Summary"></a>
## <a name="summary"></a>Resumo

Ao concluir este laboratório prático que você aprendeu como:

- Criar um controlador de API Web ASP.NET usando o Scaffolding do ASP.NET
- Implementar uma ação Get de API da Web para recuperar a próxima pergunta de teste
- Implementar uma ação de postagem da API da Web para armazenar as respostas dos teste
- Instalar o AngularJS no Visual Studio Package Manager Console
- Implemente AngularJS modelos e controladores
- Use as transições do CSS3 para executar os efeitos de animação

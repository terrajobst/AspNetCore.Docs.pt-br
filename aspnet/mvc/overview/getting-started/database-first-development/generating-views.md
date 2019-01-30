---
uid: mvc/overview/getting-started/database-first-development/generating-views
title: 'Tutorial: Gerar exibições para Database First do EF com o aplicativo ASP.NET MVC'
description: Este tutorial se concentra no uso de Scaffolding do ASP.NET para gerar os controladores e modos de exibição.
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: 669367cf-8e30-4eb6-821d-10a7d9bb906c
msc.legacyurl: /mvc/overview/getting-started/database-first-development/generating-views
msc.type: authoredcontent
ms.openlocfilehash: 7a56c0f9197a99427bcde6103ebc69d245e8ce63
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236413"
---
# <a name="tutorial-generate-views-for-ef-database-first-with-aspnet-mvc-app"></a>Tutorial: Gerar exibições para Database First do EF com o aplicativo ASP.NET MVC

Usando o MVC, Entity Framework e o Scaffolding do ASP.NET, você pode criar um aplicativo web que fornece uma interface para um banco de dados existente. Esta série de tutoriais mostra como automaticamente gerar um código que permite aos usuários exibir, editar, criar e excluir dados que residem em uma tabela de banco de dados. O código gerado corresponde às colunas na tabela de banco de dados.

Este tutorial se concentra no uso de Scaffolding do ASP.NET para gerar os controladores e modos de exibição.

Neste tutorial, você:

> [!div class="checklist"]
> * Adicionar scaffold
> * Adicionar links às novas exibições
> * Mostrar exibições de aluno
> * Mostrar exibições de registro

## <a name="prerequisite"></a>Pré-requisito

* [Criar a web application e modelos de dados](creating-the-web-application.md)

## <a name="add-scaffold"></a>Adicionar scaffold

Você está pronto para gerar o código que irá fornecer operações de dados padrão para as classes de modelo. Adicione o código adicionando um item de scaffold. Há muitas opções para o tipo de scaffolding que você pode adicionar; Neste tutorial, o scaffold incluirá um controlador e exibições que correspondem aos modelos de aluno e registro que você criou na seção anterior.

Para manter a consistência em seu projeto, você adicionará o novo controlador ao existente **controladores** pasta. Clique com botão direito do **controladores** pasta e selecione **Add** > **New Scaffolded Item**.

Selecione o **controlador MVC 5 com modos de exibição usando o Entity Framework** opção. Esta opção irá gerar o controlador e exibições para atualizar, excluir, criando e exibindo os dados em seu modelo.

![Adicionar controlador mvc](generating-views/_static/image2.png)

Selecione **aluno (ContosoSite.Models)** para a classe de modelo e selecione o **ContosoUniversityDataEntities (ContosoSite.Models)** para a classe de contexto. Mantenha o nome do controlador como **StudentsController**.

Clique em **Adicionar**.

Se você receber um erro, pode ser porque você não tiver criado o projeto na seção anterior. Nesse caso, tente compilar o projeto e, em seguida, adicione o item com Scaffold novamente.

Após a conclusão do processo de geração de código, você verá um novo controlador e exibições em seu projeto **controladores** e **exibições** > **alunos** pastas .


Execute as mesmas etapas novamente, mas adicionar um scaffold para o **registro** classe. Quando terminar, você tem um **EnrollmentsController.cs** arquivo e uma pasta sob **exibições** denominada **registros** com as exibições de criar, excluir, detalhes, editar e índice.

## <a name="add-links-to-new-views"></a>Adicionar links às novas exibições

Para tornar mais fácil de navegar para seus novos modos de exibição, você pode adicionar alguns dos hiperlinks para as exibições de índice para estudantes e registros. Abra o arquivo no **modos de exibição** > **Home** > *index. cshtml*, que é a home page do seu site. Adicione o código a seguir o jumbotron.

[!code-cshtml[Main](generating-views/samples/sample1.cshtml)]

Para o método ActionLink, o primeiro parâmetro é o texto a ser exibido no link. O segundo parâmetro é a ação e o terceiro parâmetro é o nome do controlador. Por exemplo, o primeiro link aponta para a ação de índice em StudentsController. O hiperlink real é construído com esses valores. O primeiro link, por fim, leva os usuários para o **index. cshtml** dentro do arquivo a **modos de exibição/alunos** pasta.

## <a name="display-student-views"></a>Mostrar exibições de aluno

Você verificará que o código adicionado ao seu projeto corretamente exibe uma lista dos alunos e permite aos usuários editar, criar ou excluir os registros de alunos no banco de dados.

Com o botão direito do **modos de exibição** > **Home** > *index. cshtml* arquivo e selecione **exibir no navegador**. Na home page do aplicativo, selecione **lista de alunos**.

![](generating-views/_static/image6.png)

Sobre o **índice** página, observe a lista de alunos e links para modificar esses dados. Selecione o **criar novo** vincular e fornecer alguns valores para um novo aluno. Clique em **criar**e observe o novo aluno é adicionado à sua lista.

Volta a **índice** página, selecione o **editar** link e alterar alguns dos valores de um aluno. Clique em **salvar**e observe o registro de aluno foi alterado.

Por fim, selecione o **exclua** vincular e confirme que você deseja excluir o registro clicando o **excluir** botão.

Sem escrever nenhum código, você adicionou as exibições que executam operações comuns nos dados na tabela aluno.

Você deve ter notado que o rótulo de texto para um campo se baseia na propriedade de banco de dados (como **LastName**) que não é necessariamente o que você deseja exibir na página da web. Por exemplo, você pode preferir o rótulo seja **Sobrenome**. Você corrigirá esse problema de exibição posteriormente no tutorial.

## <a name="display-enrollment-views"></a>Mostrar exibições de registro

Seu banco de dados inclui uma relação um-para-muitos entre as tabelas Student e registro e uma relação um-para-muitos entre as tabelas de registro e curso. Os modos de exibição para o registro corretamente lidar com essas relações. Navegue até a home page do seu site e selecione o **lista de registros** link e, em seguida, o **criar novo** link.

O modo de exibição exibe um formulário para criar um novo registro de registro. Em particular, observe que o formulário contém um **CourseID** lista suspensa e uma **StudentID** lista suspensa. Ambos são preenchidas com valores das tabelas relacionadas.

Além disso, a validação dos valores fornecidos é aplicada automaticamente com base no tipo de dados do campo. **Nível empresarial** requer um número, portanto, uma mensagem de erro será exibida se você tentar fornecer um valor incompatível: *O campo de nível empresarial deve ser um número.*

Você verificou que os modos de exibição gerados automaticamente permitem que os usuários trabalhar com os dados no banco de dados. No próximo tutorial desta série, você irá atualizar o banco de dados e faça as alterações correspondentes no aplicativo web.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Adicionado scaffold
> * Links adicionados para novos modos de exibição
> * Modos de exibição do aluno exibido
> * Modos de exibição do registro exibido

Avance para o próximo tutorial para saber como alterar o banco de dados.
> [!div class="nextstepaction"]
> [Alterar o banco de dados](changing-the-database.md)
---
uid: mvc/overview/getting-started/database-first-development/customizing-a-view
title: 'Tutorial: Personalizar a exibição para Database First do EF com o aplicativo ASP.NET MVC'
description: Este tutorial concentra-se sobre como alterar os modos de exibição gerados automaticamente para aprimorar a apresentação.
author: Rick-Anderson
ms.author: riande
ms.date: 01/24/2019
ms.topic: tutorial
ms.assetid: 269380ff-d7e1-4035-8ad1-fe1316a25f76
msc.legacyurl: /mvc/overview/getting-started/database-first-development/customizing-a-view
msc.type: authoredcontent
ms.openlocfilehash: 89b8a0eb84b6e287c45bc141c68a2c76e63b0e41
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236491"
---
# <a name="tutorial-customize-view-for-ef-database-first-with-aspnet-mvc-app"></a>Tutorial: Personalizar a exibição para Database First do EF com o aplicativo ASP.NET MVC

Usando o MVC, Entity Framework e o Scaffolding do ASP.NET, você pode criar um aplicativo web que fornece uma interface para um banco de dados existente. Esta série de tutoriais mostra como automaticamente gerar um código que permite aos usuários exibir, editar, criar e excluir dados que residem em uma tabela de banco de dados. O código gerado corresponde às colunas na tabela de banco de dados.

Este tutorial concentra-se sobre como alterar os modos de exibição gerados automaticamente para aprimorar a apresentação.

Neste tutorial, você:

> [!div class="checklist"]
> * Adicionar cursos para a página de detalhes do aluno
> * Confirme que os cursos são adicionados à página

## <a name="prerequisites"></a>Pré-requisitos

* [Alterar o banco de dados](changing-the-database.md)

## <a name="add-courses-to-student-detail"></a>Adicionar cursos para detalhes do aluno

O código gerado fornece um bom ponto de partida para seu aplicativo, mas não necessariamente fornece toda a funcionalidade que você precisa em seu aplicativo. Você pode personalizar o código para atender a determinados requisitos de seu aplicativo. No momento, seu aplicativo não exibe os cursos registrados aluno selecionado. Nesta seção, você adicionará os cursos registrados para cada aluno para o **detalhes** exibição aluno.

Abra **modos de exibição** > **alunos** > *details. cshtml*. Abaixo do último &lt;/dl&gt; marca, mas antes do fechamento &lt;/div&gt; de marca, adicione o código a seguir.

[!code-cshtml[Main](customizing-a-view/samples/sample1.cshtml)]

Esse código cria uma tabela que exibe uma linha para cada registro na tabela Enrollment aluno selecionado. O **exibição** método renderiza o HTML para o objeto (modelItem) que representa a expressão. Use o método de exibição (em vez de simplesmente inserindo o valor da propriedade no código) para garantir que o valor é formatado corretamente com base no seu tipo e o modelo para esse tipo. Neste exemplo, cada expressão retorna uma única propriedade de registro atual no loop, e os valores são tipos primitivos que são renderizados como texto.

## <a name="confirm-courses-are-added"></a>Confirme os cursos são adicionados

Execute a solução. Clique em **lista de alunos** e selecione **detalhes** para um dos alunos. Você verá que os cursos registrados foram incluídos no modo de exibição.

![aluno com registro](customizing-a-view/_static/image1.png)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você:

> [!div class="checklist"]
> * Cursos adicionados para a página de detalhes do aluno
> * Confirmado que os cursos são adicionados à página

Avance para o próximo tutorial para aprender a adicionar anotações de dados para especificar os requisitos de validação e formatação de exibição.
> [!div class="nextstepaction"]
> [Aprimorar a validação de dados](enhancing-data-validation.md)

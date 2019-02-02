---
uid: mvc/overview/getting-started/database-first-development/changing-the-database
title: 'Tutorial: Alterar o banco de dados para o Database First do EF com o aplicativo ASP.NET MVC'
description: Este tutorial se concentra em fazer uma atualização para a estrutura de banco de dados e propagar essa alteração em todo o aplicativo web.
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: cfd5c083-a319-482e-8f25-5b38caa93954
msc.legacyurl: /mvc/overview/getting-started/database-first-development/changing-the-database
msc.type: authoredcontent
ms.openlocfilehash: 52cad1120908cf0d4f85770f8e2690f9415c5f56
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667629"
---
# <a name="tutorial-change-the-database-for-ef-database-first-with-aspnet-mvc-app"></a>Tutorial: Alterar o banco de dados para o Database First do EF com o aplicativo ASP.NET MVC

Usando o MVC, Entity Framework e o Scaffolding do ASP.NET, você pode criar um aplicativo web que fornece uma interface para um banco de dados existente. Esta série de tutoriais mostra como automaticamente gerar um código que permite aos usuários exibir, editar, criar e excluir dados que residem em uma tabela de banco de dados. O código gerado corresponde às colunas na tabela de banco de dados.

Este tutorial se concentra em fazer uma atualização para a estrutura de banco de dados e propagar essa alteração em todo o aplicativo web.

Neste tutorial, você:

> [!div class="checklist"]
> * Adicionar uma coluna
> * Adicione a propriedade aos modos de exibição

## <a name="prerequisites"></a>Pré-requisitos

* [Gerando exibições](generating-views.md)

## <a name="add-a-column"></a>Adicionar uma coluna

Se você atualizar a estrutura de uma tabela no banco de dados, você precisa garantir que sua alteração seja propagada para o modelo de dados, modos de exibição e controlador.

Para este tutorial, você adicionará uma nova coluna à tabela aluno para registrar o nome do meio do aluno. Para adicionar essa coluna, abra o projeto de banco de dados e abra o arquivo Student.sql. Por meio do designer ou o código T-SQL, adicione uma coluna denominada **MiddleName** que é um nvarchar (50) e permite valores nulos.

Implante essa alteração em seu banco de dados local Iniciando a seu projeto de banco de dados (ou F5). O novo campo é adicionado à tabela. Se você não vê-lo no Pesquisador de objetos do SQL Server, clique no botão Atualizar no painel.

![Mostrar a nova coluna](changing-the-database/_static/image2.png)

A nova coluna existe na tabela de banco de dados, mas ele não existe atualmente na classe de modelo de dados. Você deve atualizar o modelo para incluir sua nova coluna. No **modelos** pasta, abra o **ContosoModel.edmx** arquivo para exibir o diagrama de modelo. Observe que o modelo aluno não contém a propriedade MiddleName. Clique com botão direito em qualquer lugar na superfície de design e, em seguida, selecione **modelo de atualização do banco de dados**.

No Assistente de atualização, selecione o **Refresh** e, em seguida, selecione **tabelas** > **dbo** > **aluno**. Clique em **Finalizar**.

Depois que o processo de atualização for concluído, o diagrama de banco de dados inclui o novo **MiddleName** propriedade. Salvar a **ContosoModel.edmx** arquivo. Você deve salvar este arquivo para a nova propriedade ser propagada para o **Student.cs** classe. Você atualizou o banco de dados e o modelo.

Compile a solução.

## <a name="add-the-property-to-the-views"></a>Adicione a propriedade aos modos de exibição

Infelizmente, os modos de exibição ainda não contêm a nova propriedade. Para atualizar os modos de exibição você tem duas opções – você pode gerar novamente as exibições, adicionando mais uma vez o scaffolding para a classe de aluno ou você pode adicionar manualmente a nova propriedade para exibições existentes. Neste tutorial, você adicionará o scaffolding novamente porque você não tiver feito alterações personalizadas aos modos de exibição gerados automaticamente. Você pode considerar adicionar manualmente a propriedade quando você tiver feito alterações aos modos de exibição e não quiser perder essas alterações.

Para garantir que as exibições são recriadas, exclua o **alunos** pasta sob **exibições**e excluir os **StudentsController**. Em seguida, clique com botão direito do **controladores** pasta e adicionar o scaffolding para o **aluno** modelo. Novamente, nomeie o controlador **StudentsController**. Selecione **Adicionar**.

Compile a solução novamente. As exibições contêm agora a propriedade MiddleName.

![Mostrar o nome do meio](changing-the-database/_static/image5.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Adicionada uma coluna
> * Adicionada a propriedade aos modos de exibição

Avance para o próximo tutorial para aprender a personalizar o modo de exibição para mostrar os detalhes sobre um registro de aluno.
> [!div class="nextstepaction"]
> [Personalizar um modo de exibição](customizing-a-view.md)
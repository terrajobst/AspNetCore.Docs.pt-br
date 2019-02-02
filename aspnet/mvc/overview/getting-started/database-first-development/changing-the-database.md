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
# <a name="tutorial-change-the-database-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="77de7-103">Tutorial: Alterar o banco de dados para o Database First do EF com o aplicativo ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="77de7-103">Tutorial: Change the database for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="77de7-104">Usando o MVC, Entity Framework e o Scaffolding do ASP.NET, você pode criar um aplicativo web que fornece uma interface para um banco de dados existente.</span><span class="sxs-lookup"><span data-stu-id="77de7-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="77de7-105">Esta série de tutoriais mostra como automaticamente gerar um código que permite aos usuários exibir, editar, criar e excluir dados que residem em uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="77de7-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="77de7-106">O código gerado corresponde às colunas na tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="77de7-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="77de7-107">Este tutorial se concentra em fazer uma atualização para a estrutura de banco de dados e propagar essa alteração em todo o aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="77de7-107">This tutorial focuses on making an update to the database structure and propagating that change throughout the web application.</span></span>

<span data-ttu-id="77de7-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="77de7-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="77de7-109">Adicionar uma coluna</span><span class="sxs-lookup"><span data-stu-id="77de7-109">Add a column</span></span>
> * <span data-ttu-id="77de7-110">Adicione a propriedade aos modos de exibição</span><span class="sxs-lookup"><span data-stu-id="77de7-110">Add the property to the views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="77de7-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="77de7-111">Prerequisites</span></span>

* [<span data-ttu-id="77de7-112">Gerando exibições</span><span class="sxs-lookup"><span data-stu-id="77de7-112">Generating views</span></span>](generating-views.md)

## <a name="add-a-column"></a><span data-ttu-id="77de7-113">Adicionar uma coluna</span><span class="sxs-lookup"><span data-stu-id="77de7-113">Add a column</span></span>

<span data-ttu-id="77de7-114">Se você atualizar a estrutura de uma tabela no banco de dados, você precisa garantir que sua alteração seja propagada para o modelo de dados, modos de exibição e controlador.</span><span class="sxs-lookup"><span data-stu-id="77de7-114">If you update the structure of a table in your database, you need to ensure that your change is propagated to the data model, views, and controller.</span></span>

<span data-ttu-id="77de7-115">Para este tutorial, você adicionará uma nova coluna à tabela aluno para registrar o nome do meio do aluno.</span><span class="sxs-lookup"><span data-stu-id="77de7-115">For this tutorial, you will add a new column to the Student table to record the middle name of the student.</span></span> <span data-ttu-id="77de7-116">Para adicionar essa coluna, abra o projeto de banco de dados e abra o arquivo Student.sql.</span><span class="sxs-lookup"><span data-stu-id="77de7-116">To add this column, open the database project, and open the Student.sql file.</span></span> <span data-ttu-id="77de7-117">Por meio do designer ou o código T-SQL, adicione uma coluna denominada **MiddleName** que é um nvarchar (50) e permite valores nulos.</span><span class="sxs-lookup"><span data-stu-id="77de7-117">Through either the designer or the T-SQL code, add a column named **MiddleName** that is an NVARCHAR(50) and allows NULL values.</span></span>

<span data-ttu-id="77de7-118">Implante essa alteração em seu banco de dados local Iniciando a seu projeto de banco de dados (ou F5).</span><span class="sxs-lookup"><span data-stu-id="77de7-118">Deploy this change to your local database by starting your database project (or F5).</span></span> <span data-ttu-id="77de7-119">O novo campo é adicionado à tabela.</span><span class="sxs-lookup"><span data-stu-id="77de7-119">The new field is added to the table.</span></span> <span data-ttu-id="77de7-120">Se você não vê-lo no Pesquisador de objetos do SQL Server, clique no botão Atualizar no painel.</span><span class="sxs-lookup"><span data-stu-id="77de7-120">If you do not see it in the SQL Server Object Explorer, click the Refresh button in the pane.</span></span>

![Mostrar a nova coluna](changing-the-database/_static/image2.png)

<span data-ttu-id="77de7-122">A nova coluna existe na tabela de banco de dados, mas ele não existe atualmente na classe de modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="77de7-122">The new column exists in the database table, but it does not currently exist in the data model class.</span></span> <span data-ttu-id="77de7-123">Você deve atualizar o modelo para incluir sua nova coluna.</span><span class="sxs-lookup"><span data-stu-id="77de7-123">You must update the model to include your new column.</span></span> <span data-ttu-id="77de7-124">No **modelos** pasta, abra o **ContosoModel.edmx** arquivo para exibir o diagrama de modelo.</span><span class="sxs-lookup"><span data-stu-id="77de7-124">In the **Models** folder, open the **ContosoModel.edmx** file to display the model diagram.</span></span> <span data-ttu-id="77de7-125">Observe que o modelo aluno não contém a propriedade MiddleName.</span><span class="sxs-lookup"><span data-stu-id="77de7-125">Notice that the Student model does not contain the MiddleName property.</span></span> <span data-ttu-id="77de7-126">Clique com botão direito em qualquer lugar na superfície de design e, em seguida, selecione **modelo de atualização do banco de dados**.</span><span class="sxs-lookup"><span data-stu-id="77de7-126">Right-click anywhere on the design surface, and select **Update Model from Database**.</span></span>

<span data-ttu-id="77de7-127">No Assistente de atualização, selecione o **Refresh** e, em seguida, selecione **tabelas** > **dbo** > **aluno**.</span><span class="sxs-lookup"><span data-stu-id="77de7-127">In the Update Wizard, select the **Refresh** tab and then select **Tables** > **dbo** > **Student**.</span></span> <span data-ttu-id="77de7-128">Clique em **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="77de7-128">Click **Finish**.</span></span>

<span data-ttu-id="77de7-129">Depois que o processo de atualização for concluído, o diagrama de banco de dados inclui o novo **MiddleName** propriedade.</span><span class="sxs-lookup"><span data-stu-id="77de7-129">After the update process is finished, the database diagram includes the new **MiddleName** property.</span></span> <span data-ttu-id="77de7-130">Salvar a **ContosoModel.edmx** arquivo.</span><span class="sxs-lookup"><span data-stu-id="77de7-130">Save the **ContosoModel.edmx** file.</span></span> <span data-ttu-id="77de7-131">Você deve salvar este arquivo para a nova propriedade ser propagada para o **Student.cs** classe.</span><span class="sxs-lookup"><span data-stu-id="77de7-131">You must save this file for the new property to be propagated to the **Student.cs** class.</span></span> <span data-ttu-id="77de7-132">Você atualizou o banco de dados e o modelo.</span><span class="sxs-lookup"><span data-stu-id="77de7-132">You have now updated the database and the model.</span></span>

<span data-ttu-id="77de7-133">Compile a solução.</span><span class="sxs-lookup"><span data-stu-id="77de7-133">Build the solution.</span></span>

## <a name="add-the-property-to-the-views"></a><span data-ttu-id="77de7-134">Adicione a propriedade aos modos de exibição</span><span class="sxs-lookup"><span data-stu-id="77de7-134">Add the property to the views</span></span>

<span data-ttu-id="77de7-135">Infelizmente, os modos de exibição ainda não contêm a nova propriedade.</span><span class="sxs-lookup"><span data-stu-id="77de7-135">Unfortunately, the views still do not contain the new property.</span></span> <span data-ttu-id="77de7-136">Para atualizar os modos de exibição você tem duas opções – você pode gerar novamente as exibições, adicionando mais uma vez o scaffolding para a classe de aluno ou você pode adicionar manualmente a nova propriedade para exibições existentes.</span><span class="sxs-lookup"><span data-stu-id="77de7-136">To update the views you have two options - you can either re-generate the views by once again adding scaffolding for the Student class, or you can manually add the new property to your existing views.</span></span> <span data-ttu-id="77de7-137">Neste tutorial, você adicionará o scaffolding novamente porque você não tiver feito alterações personalizadas aos modos de exibição gerados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="77de7-137">In this tutorial, you will add the scaffolding again because you have not made any customized changes to the automatically-generated views.</span></span> <span data-ttu-id="77de7-138">Você pode considerar adicionar manualmente a propriedade quando você tiver feito alterações aos modos de exibição e não quiser perder essas alterações.</span><span class="sxs-lookup"><span data-stu-id="77de7-138">You might consider manually adding the property when you have made changes to the views and do not want to lose those changes.</span></span>

<span data-ttu-id="77de7-139">Para garantir que as exibições são recriadas, exclua o **alunos** pasta sob **exibições**e excluir os **StudentsController**.</span><span class="sxs-lookup"><span data-stu-id="77de7-139">To ensure the views are re-created, delete the **Students** folder under **Views**, and delete the **StudentsController**.</span></span> <span data-ttu-id="77de7-140">Em seguida, clique com botão direito do **controladores** pasta e adicionar o scaffolding para o **aluno** modelo.</span><span class="sxs-lookup"><span data-stu-id="77de7-140">Then, right-click the **Controllers** folder and add scaffolding for the **Student** model.</span></span> <span data-ttu-id="77de7-141">Novamente, nomeie o controlador **StudentsController**.</span><span class="sxs-lookup"><span data-stu-id="77de7-141">Again, name the controller **StudentsController**.</span></span> <span data-ttu-id="77de7-142">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="77de7-142">Select **Add**.</span></span>

<span data-ttu-id="77de7-143">Compile a solução novamente.</span><span class="sxs-lookup"><span data-stu-id="77de7-143">Build the solution again.</span></span> <span data-ttu-id="77de7-144">As exibições contêm agora a propriedade MiddleName.</span><span class="sxs-lookup"><span data-stu-id="77de7-144">The views now contain the MiddleName property.</span></span>

![Mostrar o nome do meio](changing-the-database/_static/image5.png)

## <a name="next-steps"></a><span data-ttu-id="77de7-146">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="77de7-146">Next steps</span></span>

<span data-ttu-id="77de7-147">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="77de7-147">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="77de7-148">Adicionada uma coluna</span><span class="sxs-lookup"><span data-stu-id="77de7-148">Added a column</span></span>
> * <span data-ttu-id="77de7-149">Adicionada a propriedade aos modos de exibição</span><span class="sxs-lookup"><span data-stu-id="77de7-149">Added the property to the views</span></span>

<span data-ttu-id="77de7-150">Avance para o próximo tutorial para aprender a personalizar o modo de exibição para mostrar os detalhes sobre um registro de aluno.</span><span class="sxs-lookup"><span data-stu-id="77de7-150">Advance to the next tutorial to learn how to customize the view for showing details about a student record.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="77de7-151">Personalizar um modo de exibição</span><span class="sxs-lookup"><span data-stu-id="77de7-151">Customize a view</span></span>](customizing-a-view.md)
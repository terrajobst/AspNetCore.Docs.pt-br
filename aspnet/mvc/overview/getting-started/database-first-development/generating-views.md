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
# <a name="tutorial-generate-views-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="cba43-103">Tutorial: Gerar exibições para Database First do EF com o aplicativo ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="cba43-103">Tutorial: Generate views for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="cba43-104">Usando o MVC, Entity Framework e o Scaffolding do ASP.NET, você pode criar um aplicativo web que fornece uma interface para um banco de dados existente.</span><span class="sxs-lookup"><span data-stu-id="cba43-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="cba43-105">Esta série de tutoriais mostra como automaticamente gerar um código que permite aos usuários exibir, editar, criar e excluir dados que residem em uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cba43-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="cba43-106">O código gerado corresponde às colunas na tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cba43-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="cba43-107">Este tutorial se concentra no uso de Scaffolding do ASP.NET para gerar os controladores e modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="cba43-107">This tutorial focuses on using ASP.NET Scaffolding to generate the controllers and views.</span></span>

<span data-ttu-id="cba43-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="cba43-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cba43-109">Adicionar scaffold</span><span class="sxs-lookup"><span data-stu-id="cba43-109">Add scaffold</span></span>
> * <span data-ttu-id="cba43-110">Adicionar links às novas exibições</span><span class="sxs-lookup"><span data-stu-id="cba43-110">Add links to new views</span></span>
> * <span data-ttu-id="cba43-111">Mostrar exibições de aluno</span><span class="sxs-lookup"><span data-stu-id="cba43-111">Display student views</span></span>
> * <span data-ttu-id="cba43-112">Mostrar exibições de registro</span><span class="sxs-lookup"><span data-stu-id="cba43-112">Display enrollment views</span></span>

## <a name="prerequisite"></a><span data-ttu-id="cba43-113">Pré-requisito</span><span class="sxs-lookup"><span data-stu-id="cba43-113">Prerequisite</span></span>

* [<span data-ttu-id="cba43-114">Criar a web application e modelos de dados</span><span class="sxs-lookup"><span data-stu-id="cba43-114">Create the web application and data models</span></span>](creating-the-web-application.md)

## <a name="add-scaffold"></a><span data-ttu-id="cba43-115">Adicionar scaffold</span><span class="sxs-lookup"><span data-stu-id="cba43-115">Add scaffold</span></span>

<span data-ttu-id="cba43-116">Você está pronto para gerar o código que irá fornecer operações de dados padrão para as classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="cba43-116">You are ready to generate code that will provide standard data operations for the model classes.</span></span> <span data-ttu-id="cba43-117">Adicione o código adicionando um item de scaffold.</span><span class="sxs-lookup"><span data-stu-id="cba43-117">You add the code by adding a scaffold item.</span></span> <span data-ttu-id="cba43-118">Há muitas opções para o tipo de scaffolding que você pode adicionar; Neste tutorial, o scaffold incluirá um controlador e exibições que correspondem aos modelos de aluno e registro que você criou na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="cba43-118">There are many options for the type of scaffolding you can add; in this tutorial, the scaffold will include a controller and views that correspond to the Student and Enrollment models you created in the previous section.</span></span>

<span data-ttu-id="cba43-119">Para manter a consistência em seu projeto, você adicionará o novo controlador ao existente **controladores** pasta.</span><span class="sxs-lookup"><span data-stu-id="cba43-119">To maintain consistency in your project, you will add the new controller to the existing **Controllers** folder.</span></span> <span data-ttu-id="cba43-120">Clique com botão direito do **controladores** pasta e selecione **Add** > **New Scaffolded Item**.</span><span class="sxs-lookup"><span data-stu-id="cba43-120">Right-click the **Controllers** folder, and select **Add** > **New Scaffolded Item**.</span></span>

<span data-ttu-id="cba43-121">Selecione o **controlador MVC 5 com modos de exibição usando o Entity Framework** opção.</span><span class="sxs-lookup"><span data-stu-id="cba43-121">Select the **MVC 5 Controller with views, using Entity Framework** option.</span></span> <span data-ttu-id="cba43-122">Esta opção irá gerar o controlador e exibições para atualizar, excluir, criando e exibindo os dados em seu modelo.</span><span class="sxs-lookup"><span data-stu-id="cba43-122">This option will generate the controller and views for updating, deleting, creating and displaying the data in your model.</span></span>

![Adicionar controlador mvc](generating-views/_static/image2.png)

<span data-ttu-id="cba43-124">Selecione **aluno (ContosoSite.Models)** para a classe de modelo e selecione o **ContosoUniversityDataEntities (ContosoSite.Models)** para a classe de contexto.</span><span class="sxs-lookup"><span data-stu-id="cba43-124">Select **Student (ContosoSite.Models)** for the model class and select the **ContosoUniversityDataEntities (ContosoSite.Models)** for the context class.</span></span> <span data-ttu-id="cba43-125">Mantenha o nome do controlador como **StudentsController**.</span><span class="sxs-lookup"><span data-stu-id="cba43-125">Keep the controller name as **StudentsController**.</span></span>

<span data-ttu-id="cba43-126">Clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="cba43-126">Click **Add**.</span></span>

<span data-ttu-id="cba43-127">Se você receber um erro, pode ser porque você não tiver criado o projeto na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="cba43-127">If you receive an error, it may be because you did not build the project in the previous section.</span></span> <span data-ttu-id="cba43-128">Nesse caso, tente compilar o projeto e, em seguida, adicione o item com Scaffold novamente.</span><span class="sxs-lookup"><span data-stu-id="cba43-128">If so, try building the project, and then add the scaffolded item again.</span></span>

<span data-ttu-id="cba43-129">Após a conclusão do processo de geração de código, você verá um novo controlador e exibições em seu projeto **controladores** e **exibições** > **alunos** pastas .</span><span class="sxs-lookup"><span data-stu-id="cba43-129">After the code generation process is complete, you will see a new controller and views in your project's **Controllers** and **Views** > **Students** folders.</span></span>


<span data-ttu-id="cba43-130">Execute as mesmas etapas novamente, mas adicionar um scaffold para o **registro** classe.</span><span class="sxs-lookup"><span data-stu-id="cba43-130">Perform the same steps again, but add a scaffold for the **Enrollment** class.</span></span> <span data-ttu-id="cba43-131">Quando terminar, você tem um **EnrollmentsController.cs** arquivo e uma pasta sob **exibições** denominada **registros** com as exibições de criar, excluir, detalhes, editar e índice.</span><span class="sxs-lookup"><span data-stu-id="cba43-131">When finished, you have an **EnrollmentsController.cs** file, and a folder under **Views** named **Enrollments** with the Create, Delete, Details, Edit and Index views.</span></span>

## <a name="add-links-to-new-views"></a><span data-ttu-id="cba43-132">Adicionar links às novas exibições</span><span class="sxs-lookup"><span data-stu-id="cba43-132">Add links to new views</span></span>

<span data-ttu-id="cba43-133">Para tornar mais fácil de navegar para seus novos modos de exibição, você pode adicionar alguns dos hiperlinks para as exibições de índice para estudantes e registros.</span><span class="sxs-lookup"><span data-stu-id="cba43-133">To make it easier for you to navigate to your new views, you can add a couple of hyperlinks to the Index views for students and enrollments.</span></span> <span data-ttu-id="cba43-134">Abra o arquivo no **modos de exibição** > **Home** > *index. cshtml*, que é a home page do seu site.</span><span class="sxs-lookup"><span data-stu-id="cba43-134">Open the file at **Views** > **Home** > *Index.cshtml*, which is the home page for your site.</span></span> <span data-ttu-id="cba43-135">Adicione o código a seguir o jumbotron.</span><span class="sxs-lookup"><span data-stu-id="cba43-135">Add the following code below the jumbotron.</span></span>

[!code-cshtml[Main](generating-views/samples/sample1.cshtml)]

<span data-ttu-id="cba43-136">Para o método ActionLink, o primeiro parâmetro é o texto a ser exibido no link.</span><span class="sxs-lookup"><span data-stu-id="cba43-136">For the ActionLink method, the first parameter is the text to display in the link.</span></span> <span data-ttu-id="cba43-137">O segundo parâmetro é a ação e o terceiro parâmetro é o nome do controlador.</span><span class="sxs-lookup"><span data-stu-id="cba43-137">The second parameter is the action and the third parameter is the name of the controller.</span></span> <span data-ttu-id="cba43-138">Por exemplo, o primeiro link aponta para a ação de índice em StudentsController.</span><span class="sxs-lookup"><span data-stu-id="cba43-138">For example, the first link points to the Index action in StudentsController.</span></span> <span data-ttu-id="cba43-139">O hiperlink real é construído com esses valores.</span><span class="sxs-lookup"><span data-stu-id="cba43-139">The actual hyperlink is constructed from these values.</span></span> <span data-ttu-id="cba43-140">O primeiro link, por fim, leva os usuários para o **index. cshtml** dentro do arquivo a **modos de exibição/alunos** pasta.</span><span class="sxs-lookup"><span data-stu-id="cba43-140">The first link ultimately takes users to the **Index.cshtml** file within the **Views/Students** folder.</span></span>

## <a name="display-student-views"></a><span data-ttu-id="cba43-141">Mostrar exibições de aluno</span><span class="sxs-lookup"><span data-stu-id="cba43-141">Display student views</span></span>

<span data-ttu-id="cba43-142">Você verificará que o código adicionado ao seu projeto corretamente exibe uma lista dos alunos e permite aos usuários editar, criar ou excluir os registros de alunos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cba43-142">You will verify that the code added to your project correctly displays a list of the students, and enables users to edit, create, or delete the student records in the database.</span></span>

<span data-ttu-id="cba43-143">Com o botão direito do **modos de exibição** > **Home** > *index. cshtml* arquivo e selecione **exibir no navegador**.</span><span class="sxs-lookup"><span data-stu-id="cba43-143">Right-click the **Views** > **Home** > *Index.cshtml* file, and select **View in Browser**.</span></span> <span data-ttu-id="cba43-144">Na home page do aplicativo, selecione **lista de alunos**.</span><span class="sxs-lookup"><span data-stu-id="cba43-144">On the application home page, select **List of students**.</span></span>

![](generating-views/_static/image6.png)

<span data-ttu-id="cba43-145">Sobre o **índice** página, observe a lista de alunos e links para modificar esses dados.</span><span class="sxs-lookup"><span data-stu-id="cba43-145">On the **Index** page, notice the list of the students and links to modify this data.</span></span> <span data-ttu-id="cba43-146">Selecione o **criar novo** vincular e fornecer alguns valores para um novo aluno.</span><span class="sxs-lookup"><span data-stu-id="cba43-146">Select the **Create New** link and provide some values for a new student.</span></span> <span data-ttu-id="cba43-147">Clique em **criar**e observe o novo aluno é adicionado à sua lista.</span><span class="sxs-lookup"><span data-stu-id="cba43-147">Click **Create**, and notice the new student is added to your list.</span></span>

<span data-ttu-id="cba43-148">Volta a **índice** página, selecione o **editar** link e alterar alguns dos valores de um aluno.</span><span class="sxs-lookup"><span data-stu-id="cba43-148">Back on the **Index** page, select the **Edit** link, and change some of the values for a student.</span></span> <span data-ttu-id="cba43-149">Clique em **salvar**e observe o registro de aluno foi alterado.</span><span class="sxs-lookup"><span data-stu-id="cba43-149">Click **Save**, and notice the student record has been changed.</span></span>

<span data-ttu-id="cba43-150">Por fim, selecione o **exclua** vincular e confirme que você deseja excluir o registro clicando o **excluir** botão.</span><span class="sxs-lookup"><span data-stu-id="cba43-150">Finally, select the **Delete** link and confirm that you want to delete the record by clicking the **Delete** button.</span></span>

<span data-ttu-id="cba43-151">Sem escrever nenhum código, você adicionou as exibições que executam operações comuns nos dados na tabela aluno.</span><span class="sxs-lookup"><span data-stu-id="cba43-151">Without writing any code, you have added views that perform common operations on the data in the Student table.</span></span>

<span data-ttu-id="cba43-152">Você deve ter notado que o rótulo de texto para um campo se baseia na propriedade de banco de dados (como **LastName**) que não é necessariamente o que você deseja exibir na página da web.</span><span class="sxs-lookup"><span data-stu-id="cba43-152">You may have noticed that the text label for a field is based on the database property (such as **LastName**) which is not necessarily what you want to display on the web page.</span></span> <span data-ttu-id="cba43-153">Por exemplo, você pode preferir o rótulo seja **Sobrenome**.</span><span class="sxs-lookup"><span data-stu-id="cba43-153">For example, you may prefer the label to be **Last Name**.</span></span> <span data-ttu-id="cba43-154">Você corrigirá esse problema de exibição posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="cba43-154">You will fix this display issue later in the tutorial.</span></span>

## <a name="display-enrollment-views"></a><span data-ttu-id="cba43-155">Mostrar exibições de registro</span><span class="sxs-lookup"><span data-stu-id="cba43-155">Display enrollment views</span></span>

<span data-ttu-id="cba43-156">Seu banco de dados inclui uma relação um-para-muitos entre as tabelas Student e registro e uma relação um-para-muitos entre as tabelas de registro e curso.</span><span class="sxs-lookup"><span data-stu-id="cba43-156">Your database includes a one-to-many relationship between the Student and Enrollment tables, and a one-to-many relationship between the Course and Enrollment tables.</span></span> <span data-ttu-id="cba43-157">Os modos de exibição para o registro corretamente lidar com essas relações.</span><span class="sxs-lookup"><span data-stu-id="cba43-157">The views for Enrollment correctly handle these relationships.</span></span> <span data-ttu-id="cba43-158">Navegue até a home page do seu site e selecione o **lista de registros** link e, em seguida, o **criar novo** link.</span><span class="sxs-lookup"><span data-stu-id="cba43-158">Navigate to the home page for your site and select the **List of enrollments** link and then the **Create New** link.</span></span>

<span data-ttu-id="cba43-159">O modo de exibição exibe um formulário para criar um novo registro de registro.</span><span class="sxs-lookup"><span data-stu-id="cba43-159">The view displays a form for creating a new enrollment record.</span></span> <span data-ttu-id="cba43-160">Em particular, observe que o formulário contém um **CourseID** lista suspensa e uma **StudentID** lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="cba43-160">In particular, notice that the form contains a **CourseID** drop-down list and a **StudentID** drop-down list.</span></span> <span data-ttu-id="cba43-161">Ambos são preenchidas com valores das tabelas relacionadas.</span><span class="sxs-lookup"><span data-stu-id="cba43-161">Both are populated with values from the related tables.</span></span>

<span data-ttu-id="cba43-162">Além disso, a validação dos valores fornecidos é aplicada automaticamente com base no tipo de dados do campo.</span><span class="sxs-lookup"><span data-stu-id="cba43-162">Furthermore, validation of the provided values is automatically applied based on the data type of the field.</span></span> <span data-ttu-id="cba43-163">**Nível empresarial** requer um número, portanto, uma mensagem de erro será exibida se você tentar fornecer um valor incompatível: *O campo de nível empresarial deve ser um número.*</span><span class="sxs-lookup"><span data-stu-id="cba43-163">**Grade** requires a number, so an error message is displayed if you try to provide an incompatible value: *The field Grade must be a number.*</span></span>

<span data-ttu-id="cba43-164">Você verificou que os modos de exibição gerados automaticamente permitem que os usuários trabalhar com os dados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cba43-164">You have verified that the automatically-generated views enable users to work with the data in the database.</span></span> <span data-ttu-id="cba43-165">No próximo tutorial desta série, você irá atualizar o banco de dados e faça as alterações correspondentes no aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="cba43-165">In the next tutorial in this series, you will update the database and make the corresponding changes in the web application.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cba43-166">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="cba43-166">Next steps</span></span>

<span data-ttu-id="cba43-167">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="cba43-167">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cba43-168">Adicionado scaffold</span><span class="sxs-lookup"><span data-stu-id="cba43-168">Added scaffold</span></span>
> * <span data-ttu-id="cba43-169">Links adicionados para novos modos de exibição</span><span class="sxs-lookup"><span data-stu-id="cba43-169">Added links to new views</span></span>
> * <span data-ttu-id="cba43-170">Modos de exibição do aluno exibido</span><span class="sxs-lookup"><span data-stu-id="cba43-170">Displayed student views</span></span>
> * <span data-ttu-id="cba43-171">Modos de exibição do registro exibido</span><span class="sxs-lookup"><span data-stu-id="cba43-171">Displayed enrollment views</span></span>

<span data-ttu-id="cba43-172">Avance para o próximo tutorial para saber como alterar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cba43-172">Advance to the next tutorial to learn how to change the database.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="cba43-173">Alterar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="cba43-173">Change the database</span></span>](changing-the-database.md)
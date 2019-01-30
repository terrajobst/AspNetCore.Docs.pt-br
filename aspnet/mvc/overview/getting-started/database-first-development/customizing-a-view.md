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
# <a name="tutorial-customize-view-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="c2e8f-103">Tutorial: Personalizar a exibição para Database First do EF com o aplicativo ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="c2e8f-103">Tutorial: Customize view for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="c2e8f-104">Usando o MVC, Entity Framework e o Scaffolding do ASP.NET, você pode criar um aplicativo web que fornece uma interface para um banco de dados existente.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="c2e8f-105">Esta série de tutoriais mostra como automaticamente gerar um código que permite aos usuários exibir, editar, criar e excluir dados que residem em uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="c2e8f-106">O código gerado corresponde às colunas na tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="c2e8f-107">Este tutorial concentra-se sobre como alterar os modos de exibição gerados automaticamente para aprimorar a apresentação.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-107">This tutorial focuses on changing the automatically-generated views to enhance the presentation.</span></span>

<span data-ttu-id="c2e8f-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="c2e8f-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c2e8f-109">Adicionar cursos para a página de detalhes do aluno</span><span class="sxs-lookup"><span data-stu-id="c2e8f-109">Add courses to the student detail page</span></span>
> * <span data-ttu-id="c2e8f-110">Confirme que os cursos são adicionados à página</span><span class="sxs-lookup"><span data-stu-id="c2e8f-110">Confirm that the courses are added to the page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c2e8f-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="c2e8f-111">Prerequisites</span></span>

* [<span data-ttu-id="c2e8f-112">Alterar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="c2e8f-112">Change the database</span></span>](changing-the-database.md)

## <a name="add-courses-to-student-detail"></a><span data-ttu-id="c2e8f-113">Adicionar cursos para detalhes do aluno</span><span class="sxs-lookup"><span data-stu-id="c2e8f-113">Add courses to student detail</span></span>

<span data-ttu-id="c2e8f-114">O código gerado fornece um bom ponto de partida para seu aplicativo, mas não necessariamente fornece toda a funcionalidade que você precisa em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-114">The generated code provides a good starting point for your application but it does not necessarily provide all of the functionality that you need in your application.</span></span> <span data-ttu-id="c2e8f-115">Você pode personalizar o código para atender a determinados requisitos de seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-115">You can customize the code to meet the particular requirements of your application.</span></span> <span data-ttu-id="c2e8f-116">No momento, seu aplicativo não exibe os cursos registrados aluno selecionado.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-116">Currently, your application does not display the enrolled courses for the selected student.</span></span> <span data-ttu-id="c2e8f-117">Nesta seção, você adicionará os cursos registrados para cada aluno para o **detalhes** exibição aluno.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-117">In this section, you will add the enrolled courses for each student to the **Details** view for the student.</span></span>

<span data-ttu-id="c2e8f-118">Abra **modos de exibição** > **alunos** > *details. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-118">Open **Views** > **Students** > *Details.cshtml*.</span></span> <span data-ttu-id="c2e8f-119">Abaixo do último &lt;/dl&gt; marca, mas antes do fechamento &lt;/div&gt; de marca, adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-119">Below the last &lt;/dl&gt; tag, but before the closing &lt;/div&gt; tag, add the following code.</span></span>

[!code-cshtml[Main](customizing-a-view/samples/sample1.cshtml)]

<span data-ttu-id="c2e8f-120">Esse código cria uma tabela que exibe uma linha para cada registro na tabela Enrollment aluno selecionado.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-120">This code creates a table that displays a row for each record in the Enrollment table for the selected student.</span></span> <span data-ttu-id="c2e8f-121">O **exibição** método renderiza o HTML para o objeto (modelItem) que representa a expressão.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-121">The **Display** method renders HTML for the object (modelItem) that represents the expression.</span></span> <span data-ttu-id="c2e8f-122">Use o método de exibição (em vez de simplesmente inserindo o valor da propriedade no código) para garantir que o valor é formatado corretamente com base no seu tipo e o modelo para esse tipo.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-122">You use the Display method (rather than simply embedding the property value in the code) to make sure the value is formatted correctly based on its type and the template for that type.</span></span> <span data-ttu-id="c2e8f-123">Neste exemplo, cada expressão retorna uma única propriedade de registro atual no loop, e os valores são tipos primitivos que são renderizados como texto.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-123">In this example, each expression returns a single property from the current record in the loop, and the values are primitive types which are rendered as text.</span></span>

## <a name="confirm-courses-are-added"></a><span data-ttu-id="c2e8f-124">Confirme os cursos são adicionados</span><span class="sxs-lookup"><span data-stu-id="c2e8f-124">Confirm courses are added</span></span>

<span data-ttu-id="c2e8f-125">Execute a solução.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-125">Run the solution.</span></span> <span data-ttu-id="c2e8f-126">Clique em **lista de alunos** e selecione **detalhes** para um dos alunos.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-126">Click **List of students** and select **Details** for one of the students.</span></span> <span data-ttu-id="c2e8f-127">Você verá que os cursos registrados foram incluídos no modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-127">You will see the enrolled courses have been included in the view.</span></span>

![aluno com registro](customizing-a-view/_static/image1.png)

## <a name="next-steps"></a><span data-ttu-id="c2e8f-129">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="c2e8f-129">Next steps</span></span>
<span data-ttu-id="c2e8f-130">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="c2e8f-130">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c2e8f-131">Cursos adicionados para a página de detalhes do aluno</span><span class="sxs-lookup"><span data-stu-id="c2e8f-131">Added courses to the student detail page</span></span>
> * <span data-ttu-id="c2e8f-132">Confirmado que os cursos são adicionados à página</span><span class="sxs-lookup"><span data-stu-id="c2e8f-132">Confirmed that the courses are added to the page</span></span>

<span data-ttu-id="c2e8f-133">Avance para o próximo tutorial para aprender a adicionar anotações de dados para especificar os requisitos de validação e formatação de exibição.</span><span class="sxs-lookup"><span data-stu-id="c2e8f-133">Advance to the next tutorial to learn how to add data annotations to specify validation requirements and display formatting.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="c2e8f-134">Aprimorar a validação de dados</span><span class="sxs-lookup"><span data-stu-id="c2e8f-134">Enhance data validation</span></span>](enhancing-data-validation.md)

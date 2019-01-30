---
uid: mvc/overview/getting-started/database-first-development/enhancing-data-validation
title: 'Tutorial: Aprimorar a validação de dados para o Database First do EF com o aplicativo ASP.NET MVC'
description: Este tutorial concentra-se sobre como adicionar anotações de dados para o modelo de dados para especificar os requisitos de validação e formatação de exibição.
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: 0ed5e67a-34c0-4b57-84a6-802b0fb3cd00
msc.legacyurl: /mvc/overview/getting-started/database-first-development/enhancing-data-validation
msc.type: authoredcontent
ms.openlocfilehash: 85299d70c6cba52c1d40a42edfd429c96318134a
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236478"
---
# <a name="tutorial-enhance-data-validation-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="fe6c9-103">Tutorial: Aprimorar a validação de dados para o Database First do EF com o aplicativo ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="fe6c9-103">Tutorial: Enhance data validation for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="fe6c9-104">Usando o MVC, Entity Framework e o Scaffolding do ASP.NET, você pode criar um aplicativo web que fornece uma interface para um banco de dados existente.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="fe6c9-105">Esta série de tutoriais mostra como automaticamente gerar um código que permite aos usuários exibir, editar, criar e excluir dados que residem em uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="fe6c9-106">O código gerado corresponde às colunas na tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="fe6c9-107">Este tutorial concentra-se sobre como adicionar anotações de dados para o modelo de dados para especificar os requisitos de validação e formatação de exibição.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-107">This tutorial focuses on adding data annotations to the data model to specify validation requirements and display formatting.</span></span> <span data-ttu-id="fe6c9-108">Ele foi aprimorado com base nos comentários dos usuários na seção de comentários.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-108">It was improved based on feedback from users in the comments section.</span></span>

<span data-ttu-id="fe6c9-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="fe6c9-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fe6c9-110">Adicionar anotações de dados</span><span class="sxs-lookup"><span data-stu-id="fe6c9-110">Add data annotations</span></span>
> * <span data-ttu-id="fe6c9-111">Adicionar classes de metadados</span><span class="sxs-lookup"><span data-stu-id="fe6c9-111">Add metadata classes</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fe6c9-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="fe6c9-112">Prerequisites</span></span>

* [<span data-ttu-id="fe6c9-113">Personalizar um modo de exibição</span><span class="sxs-lookup"><span data-stu-id="fe6c9-113">Customize a view</span></span>](customizing-a-view.md)

## <a name="add-data-annotations"></a><span data-ttu-id="fe6c9-114">Adicionar anotações de dados</span><span class="sxs-lookup"><span data-stu-id="fe6c9-114">Add data annotations</span></span>

<span data-ttu-id="fe6c9-115">Como você viu em um tópico anterior, algumas regras de validação de dados são aplicadas automaticamente para a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-115">As you saw in an earlier topic, some data validation rules are automatically applied to the user input.</span></span> <span data-ttu-id="fe6c9-116">Por exemplo, você só pode fornecer um número para a propriedade de nível empresarial.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-116">For example, you can only provide a number for the Grade property.</span></span> <span data-ttu-id="fe6c9-117">Para especificar mais regras de validação de dados, você pode adicionar anotações de dados à sua classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-117">To specify more data validation rules, you can add data annotations to your model class.</span></span> <span data-ttu-id="fe6c9-118">Essas anotações são aplicadas em todo o seu aplicativo web para a propriedade especificada.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-118">These annotations are applied throughout your web application for the specified property.</span></span> <span data-ttu-id="fe6c9-119">Você também pode aplicar atributos de formatação que alterar como as propriedades são exibidas; Por exemplo, alterando o valor usado para rótulos de texto.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-119">You can also apply formatting attributes that change how the properties are displayed; such as, changing the value used for text labels.</span></span>

<span data-ttu-id="fe6c9-120">Neste tutorial, você irá adicionar anotações de dados para restringir o tamanho dos valores fornecidos para as propriedades FirstName, LastName e MiddleName.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-120">In this tutorial, you will add data annotations to restrict the length of the values provided for the FirstName, LastName, and MiddleName properties.</span></span> <span data-ttu-id="fe6c9-121">No banco de dados, esses valores são limitados a 50 caracteres. No entanto, em seu aplicativo web que limite de caracteres no momento, não é imposta.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-121">In the database, these values are limited to 50 characters; however, in your web application that character limit is currently not enforced.</span></span> <span data-ttu-id="fe6c9-122">Se um usuário fornece mais de 50 caracteres para um desses valores, a página falhará ao tentar salvar o valor para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-122">If a user provides more than 50 characters for one of those values, the page will crash when attempting to save the value to the database.</span></span> <span data-ttu-id="fe6c9-123">Também você restringirá a nível empresarial para valores entre 0 e 4.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-123">You will also restrict Grade to values between 0 and 4.</span></span>

<span data-ttu-id="fe6c9-124">Selecione **modelos** > **ContosoModel.edmx** > **ContosoModel.tt** e abra o *Student.cs* arquivo.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-124">Select **Models** > **ContosoModel.edmx** > **ContosoModel.tt** and open the *Student.cs* file.</span></span> <span data-ttu-id="fe6c9-125">Adicione o seguinte código realçado à classe.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-125">Add the following highlighted code to the class.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample1.cs?highlight=5,15,17,20)]

<span data-ttu-id="fe6c9-126">Abra *Enrollment.cs* e adicione o seguinte código realçado.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-126">Open *Enrollment.cs* and add the following highlighted code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample2.cs?highlight=5,10)]

<span data-ttu-id="fe6c9-127">Compile a solução.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-127">Build the solution.</span></span>

<span data-ttu-id="fe6c9-128">Clique em **lista de alunos** e selecione **editar**.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-128">Click **List of students** and select **Edit**.</span></span> <span data-ttu-id="fe6c9-129">Se você tentar inserir mais de 50 caracteres, uma mensagem de erro será exibida.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-129">If you attempt to enter more than 50 characters, an error message is displayed.</span></span>

![Mostrar mensagem de erro](enhancing-data-validation/_static/image1.png)

<span data-ttu-id="fe6c9-131">Volte para a home page.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-131">Go back to the home page.</span></span> <span data-ttu-id="fe6c9-132">Clique em **lista de registros** e selecione **editar**.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-132">Click **List of enrollments** and select **Edit**.</span></span> <span data-ttu-id="fe6c9-133">Tentativa de fornecer uma nota superior a 4.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-133">Attempt to provide a grade above 4.</span></span> <span data-ttu-id="fe6c9-134">Você receberá esse erro: *O campo de que nível empresarial deve estar entre 0 e 4.*</span><span class="sxs-lookup"><span data-stu-id="fe6c9-134">You will receive this error: *The field Grade must be between 0 and 4.*</span></span>

## <a name="add-metadata-classes"></a><span data-ttu-id="fe6c9-135">Adicionar classes de metadados</span><span class="sxs-lookup"><span data-stu-id="fe6c9-135">Add metadata classes</span></span>

<span data-ttu-id="fe6c9-136">Adicionar os atributos de validação diretamente para a classe de modelo funciona quando você não espera que o banco de dados a ser alterado; No entanto, se as alterações de banco de dados e você precisar regenerar a classe de modelo, você perderá todos os atributos que você tivesse aplicado à classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-136">Adding the validation attributes directly to the model class works when you do not expect the database to change; however, if your database changes and you need to regenerate the model class, you will lose all of the attributes you had applied to the model class.</span></span> <span data-ttu-id="fe6c9-137">Essa abordagem pode ser muito ineficiente e propenso a perder as regras de validação importantes.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-137">This approach can be very inefficient and prone to losing important validation rules.</span></span>

<span data-ttu-id="fe6c9-138">Para evitar esse problema, você pode adicionar uma classe de metadados que contém os atributos.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-138">To avoid this problem, you can add a metadata class that contains the attributes.</span></span> <span data-ttu-id="fe6c9-139">Quando você associa a classe de modelo para a classe de metadados, esses atributos são aplicados ao modelo.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-139">When you associate the model class to the metadata class, those attributes are applied to the model.</span></span> <span data-ttu-id="fe6c9-140">Nessa abordagem, a classe de modelo pode ser regenerada sem perder todos os atributos que foram aplicados à classe de metadados.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-140">In this approach, the model class can be regenerated without losing all of the attributes that have been applied to the metadata class.</span></span>

<span data-ttu-id="fe6c9-141">No **modelos** pasta, adicione uma classe chamada *Metadata.cs*.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-141">In the **Models** folder, add a class named *Metadata.cs*.</span></span>

<span data-ttu-id="fe6c9-142">Substitua o código em *Metadata.cs* com o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-142">Replace the code in *Metadata.cs* with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample3.cs)]

<span data-ttu-id="fe6c9-143">Essas classes de metadados contêm todos os atributos de validação aplicados anteriormente para as classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-143">These metadata classes contain all of the validation attributes that you had previously applied to the model classes.</span></span> <span data-ttu-id="fe6c9-144">O **exibição** atributo é usado para alterar o valor usado para rótulos de texto.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-144">The **Display** attribute is used to change the value used for text labels.</span></span>

<span data-ttu-id="fe6c9-145">Agora, você deve associar as classes de modelo com as classes de metadados.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-145">Now, you must associate the model classes with the metadata classes.</span></span>

<span data-ttu-id="fe6c9-146">No **modelos** pasta, adicione uma classe chamada *PartialClasses.cs*.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-146">In the **Models** folder, add a class named *PartialClasses.cs*.</span></span>

<span data-ttu-id="fe6c9-147">Substitua o conteúdo do arquivo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-147">Replace the contents of the file with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample4.cs)]

<span data-ttu-id="fe6c9-148">Observe que cada classe é marcada como um `partial` classe e cada uma corresponde ao nome e namespace como a classe que é gerada automaticamente.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-148">Notice that each class is marked as a `partial` class, and each matches the name and namespace as the class that is automatically generated.</span></span> <span data-ttu-id="fe6c9-149">Aplicando o atributo de metadados para a classe parcial, você certifique-se de que os atributos de validação de dados serão aplicados à classe gerada automaticamente.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-149">By applying the metadata attribute to the partial class, you ensure that the data validation attributes will be applied to the automatically-generated class.</span></span> <span data-ttu-id="fe6c9-150">Esses atributos não serão perdidos quando você regenerar as classes de modelo porque o atributo de metadados é aplicado em classes parciais que não serão regeneradas.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-150">These attributes will not be lost when you regenerate the model classes because the metadata attribute is applied in partial classes that are not regenerated.</span></span>

<span data-ttu-id="fe6c9-151">Para regenerar as classes geradas automaticamente, abra o *ContosoModel.edmx* arquivo.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-151">To regenerate the automatically-generated classes, open the *ContosoModel.edmx* file.</span></span> <span data-ttu-id="fe6c9-152">Mais uma vez, com o botão direito na superfície do design e selecione **modelo de atualização do banco de dados**.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-152">Once again, right-click on the design surface and select **Update Model from Database**.</span></span> <span data-ttu-id="fe6c9-153">Embora você não alterou o banco de dados, esse processo regenerará as classes.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-153">Even though you have not changed the database, this process will regenerate the classes.</span></span> <span data-ttu-id="fe6c9-154">No **Refresh** guia, selecione **tabelas** e **concluir**.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-154">In the **Refresh** tab, select **Tables** and **Finish**.</span></span>

<span data-ttu-id="fe6c9-155">Salvar a *ContosoModel.edmx* arquivo para aplicar as alterações.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-155">Save the *ContosoModel.edmx* file to apply the changes.</span></span>

<span data-ttu-id="fe6c9-156">Abra o *Student.cs* arquivo ou o *Enrollment.cs* arquivo e observe que os atributos de validação de dados que você aplicou anteriormente não estão mais no arquivo.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-156">Open the *Student.cs* file or the *Enrollment.cs* file, and notice that the data validation attributes you applied earlier are no longer in the file.</span></span> <span data-ttu-id="fe6c9-157">No entanto, executar o aplicativo e observe que as regras de validação ainda são aplicadas quando você insere dados.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-157">However, run the application, and notice that the validation rules are still applied when you enter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe6c9-158">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fe6c9-158">Additional resources</span></span>

<span data-ttu-id="fe6c9-159">Para obter uma lista completa de anotações de validação de dados, você pode aplicar a classes e propriedades, consulte [DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).</span><span class="sxs-lookup"><span data-stu-id="fe6c9-159">For a full list of data validation annotations you can apply to properties and classes, see [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).</span></span>

## <a name="next-steps"></a><span data-ttu-id="fe6c9-160">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="fe6c9-160">Next steps</span></span>

<span data-ttu-id="fe6c9-161">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="fe6c9-161">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fe6c9-162">Anotações de dados adicionados</span><span class="sxs-lookup"><span data-stu-id="fe6c9-162">Added data annotations</span></span>
> * <span data-ttu-id="fe6c9-163">Classes de metadados adicionados</span><span class="sxs-lookup"><span data-stu-id="fe6c9-163">Added metadata classes</span></span>

<span data-ttu-id="fe6c9-164">Avance para o próximo tutorial para saber como publicar o aplicativo web e o banco de dados no Azure.</span><span class="sxs-lookup"><span data-stu-id="fe6c9-164">Advance to the next tutorial to learn how to publish the web app and database to Azure.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="fe6c9-165">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="fe6c9-165">Publish to Azure</span></span>](publish-to-azure.md)
---
uid: web-forms/overview/presenting-and-managing-data/model-binding/retrieving-data
title: Recuperando e exibindo dados com formulários da web e de associação de modelo | Microsoft Docs
author: Rick-Anderson
description: Esta série de tutoriais demonstra aspectos básicos de como usar a associação de modelo com um projeto de Web Forms do ASP.NET. Associação de modelo torna a interação de dados mais simples-...
ms.author: riande
ms.date: 02/27/2014
ms.assetid: 9f24fb82-c7ac-48da-b8e2-51b3da17e365
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/retrieving-data
msc.type: authoredcontent
ms.openlocfilehash: c53c27f4852eab9813bd917315111e7cd3b04953
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396279"
---
<a name="retrieving-and-displaying-data-with-model-binding-and-web-forms"></a><span data-ttu-id="64ced-104">Recuperando e exibindo dados com a associação de modelos e formulários da web</span><span class="sxs-lookup"><span data-stu-id="64ced-104">Retrieving and displaying data with model binding and web forms</span></span>
====================

> <span data-ttu-id="64ced-105">Esta série de tutoriais demonstra aspectos básicos de como usar a associação de modelo com um projeto de Web Forms do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="64ced-105">This tutorial series demonstrates basic aspects of using model binding with an ASP.NET Web Forms project.</span></span> <span data-ttu-id="64ced-106">Associação de modelo torna a interação de dados mais simples que lidam com dados de objetos de origem (como ObjectDataSource ou SqlDataSource).</span><span class="sxs-lookup"><span data-stu-id="64ced-106">Model binding makes data interaction more straight-forward than dealing with data source objects (such as ObjectDataSource or SqlDataSource).</span></span> <span data-ttu-id="64ced-107">Esta série começa com material introdutório e move para conceitos mais avançados em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="64ced-107">This series starts with introductory material and moves to more advanced concepts in later tutorials.</span></span>
> 
>  <span data-ttu-id="64ced-108">O padrão de associação de modelo funciona com qualquer tecnologia de acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="64ced-108">The model binding pattern works with any data access technology.</span></span> <span data-ttu-id="64ced-109">Neste tutorial, você usará o Entity Framework, mas você pode usar a tecnologia de acesso de dados que é mais familiar para você.</span><span class="sxs-lookup"><span data-stu-id="64ced-109">In this tutorial, you will use Entity Framework, but you could use the data access technology that is most familiar to you.</span></span> <span data-ttu-id="64ced-110">De um controle de servidor associado a dados, como um controle ListView, GridView, DetailsView ou FormView, você deve especificar os nomes dos métodos a ser usado para selecionar, atualizando, excluindo e criação de dados.</span><span class="sxs-lookup"><span data-stu-id="64ced-110">From a data-bound server control, such as a GridView, ListView, DetailsView, or FormView control, you specify the names of the methods to use for selecting, updating, deleting, and creating data.</span></span> <span data-ttu-id="64ced-111">Neste tutorial, você especificará um valor para o SelectMethod.</span><span class="sxs-lookup"><span data-stu-id="64ced-111">In this tutorial, you will specify a value for the SelectMethod.</span></span> 
> 
> <span data-ttu-id="64ced-112">Dentro desse método, você pode fornecer a lógica para recuperar os dados.</span><span class="sxs-lookup"><span data-stu-id="64ced-112">Within that method, you provide the logic for retrieving the data.</span></span> <span data-ttu-id="64ced-113">O próximo tutorial, você definirá valores para InsertMethod e DeleteMethod, o UpdateMethod.</span><span class="sxs-lookup"><span data-stu-id="64ced-113">In the next tutorial, you will set values for UpdateMethod, DeleteMethod and InsertMethod.</span></span>
>
> <span data-ttu-id="64ced-114">Você pode [Baixe](https://go.microsoft.com/fwlink/?LinkId=286116) o projeto completo no C# ou o Visual Basic.</span><span class="sxs-lookup"><span data-stu-id="64ced-114">You can [download](https://go.microsoft.com/fwlink/?LinkId=286116) the complete project in C# or Visual Basic.</span></span> <span data-ttu-id="64ced-115">O código para download funciona com o Visual Studio 2012 e posterior.</span><span class="sxs-lookup"><span data-stu-id="64ced-115">The downloadable code works with Visual Studio 2012 and later.</span></span> <span data-ttu-id="64ced-116">Ele usa o modelo do Visual Studio 2012, que é ligeiramente diferente do que o modelo do Visual Studio 2017 mostrado neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="64ced-116">It uses the Visual Studio 2012 template, which is slightly different than the Visual Studio 2017 template shown in this tutorial.</span></span>
> 
> <span data-ttu-id="64ced-117">No tutorial, você executar o aplicativo no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="64ced-117">In the tutorial you run the application in Visual Studio.</span></span> <span data-ttu-id="64ced-118">Você também pode implantar o aplicativo em um provedor de hospedagem e disponibilizá-lo pela internet.</span><span class="sxs-lookup"><span data-stu-id="64ced-118">You can also deploy the application to a hosting provider and make it available over the internet.</span></span> <span data-ttu-id="64ced-119">A Microsoft oferece a hospedagem de web gratuita para até 10 sites em um</span><span class="sxs-lookup"><span data-stu-id="64ced-119">Microsoft offers free web hosting for up to 10 web sites in a</span></span>  
>  <span data-ttu-id="64ced-120">[conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A443DD604).</span><span class="sxs-lookup"><span data-stu-id="64ced-120">[free Azure trial account](https://azure.microsoft.com/free/?WT.mc_id=A443DD604).</span></span> <span data-ttu-id="64ced-121">Para obter informações sobre como implantar um projeto de web do Visual Studio para aplicativos de Web do serviço de aplicativo do Azure, consulte a [implantação de Web do ASP.NET usando o Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md) série.</span><span class="sxs-lookup"><span data-stu-id="64ced-121">For information about how to deploy a Visual Studio web project to Azure App Service Web Apps, see the [ASP.NET Web Deployment using Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md) series.</span></span> <span data-ttu-id="64ced-122">Esse tutorial também mostra como usar o Entity Framework Code First Migrations para implantar seu banco de dados do SQL Server no banco de dados SQL.</span><span class="sxs-lookup"><span data-stu-id="64ced-122">That tutorial also shows how to use Entity Framework Code First Migrations to deploy your SQL Server database to Azure SQL Database.</span></span>
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="64ced-123">Versões de software usadas no tutorial</span><span class="sxs-lookup"><span data-stu-id="64ced-123">Software versions used in the tutorial</span></span>
> 
> - <span data-ttu-id="64ced-124">Microsoft Visual Studio 2017 ou o Microsoft Visual Studio Community 2017</span><span class="sxs-lookup"><span data-stu-id="64ced-124">Microsoft Visual Studio 2017 or Microsoft Visual Studio Community 2017</span></span>
>   
> <span data-ttu-id="64ced-125">Este tutorial também funciona com o Visual Studio 2012 e o Visual Studio 2013, mas há algumas diferenças no modelo de projeto e de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="64ced-125">This tutorial also works with Visual Studio 2012 and Visual Studio 2013, but there are some differences in the user interface and project template.</span></span>


## <a name="what-youll-build"></a><span data-ttu-id="64ced-126">O que você vai criar</span><span class="sxs-lookup"><span data-stu-id="64ced-126">What you'll build</span></span>

<span data-ttu-id="64ced-127">Neste tutorial, você vai:</span><span class="sxs-lookup"><span data-stu-id="64ced-127">In this tutorial, you'll:</span></span>

* <span data-ttu-id="64ced-128">Criar objetos de dados que refletem uma universidade com os alunos matriculados em cursos</span><span class="sxs-lookup"><span data-stu-id="64ced-128">Build data objects that reflect a university with students enrolled in courses</span></span>
* <span data-ttu-id="64ced-129">Criar tabelas de banco de dados dos objetos</span><span class="sxs-lookup"><span data-stu-id="64ced-129">Build database tables from the objects</span></span>
* <span data-ttu-id="64ced-130">Preencher o banco de dados com dados de teste</span><span class="sxs-lookup"><span data-stu-id="64ced-130">Populate the database with test data</span></span>
* <span data-ttu-id="64ced-131">Exibir dados em um formulário da web</span><span class="sxs-lookup"><span data-stu-id="64ced-131">Display data in a web form</span></span>

## <a name="create-the-project"></a><span data-ttu-id="64ced-132">Criar o projeto</span><span class="sxs-lookup"><span data-stu-id="64ced-132">Create the project</span></span>

1. <span data-ttu-id="64ced-133">No Visual Studio 2017, crie uma **aplicativo Web ASP.NET (.NET Framework)** projeto chamado **ContosoUniversityModelBinding**.</span><span class="sxs-lookup"><span data-stu-id="64ced-133">In Visual Studio 2017, create a **ASP.NET Web Application (.NET Framework)** project called **ContosoUniversityModelBinding**.</span></span>

   ![Criar projeto](retrieving-data/_static/image19.png)

2. <span data-ttu-id="64ced-135">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="64ced-135">Select **OK**.</span></span> <span data-ttu-id="64ced-136">A caixa de diálogo Selecionar um modelo é exibido.</span><span class="sxs-lookup"><span data-stu-id="64ced-136">The dialog box to select a template appears.</span></span>

   ![Selecione os formulários da web](retrieving-data/_static/image3.png)

3. <span data-ttu-id="64ced-138">Selecione o **Web Forms** modelo.</span><span class="sxs-lookup"><span data-stu-id="64ced-138">Select the **Web Forms** template.</span></span> 

4. <span data-ttu-id="64ced-139">Se necessário, altere a autenticação do **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="64ced-139">If necessary, change the authentication to **Individual User Accounts**.</span></span> 

5. <span data-ttu-id="64ced-140">Selecione **OK** para criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="64ced-140">Select **OK** to create the project.</span></span>

## <a name="modify-site-appearance"></a><span data-ttu-id="64ced-141">Modificar a aparência do site</span><span class="sxs-lookup"><span data-stu-id="64ced-141">Modify site appearance</span></span>

   <span data-ttu-id="64ced-142">Fazer algumas alterações para personalizar a aparência do site.</span><span class="sxs-lookup"><span data-stu-id="64ced-142">Make a few changes to customize site appearance.</span></span> 
   
   1. <span data-ttu-id="64ced-143">Abra o arquivo site.</span><span class="sxs-lookup"><span data-stu-id="64ced-143">Open the Site.Master file.</span></span>
   
   2. <span data-ttu-id="64ced-144">Alterar o título a ser exibido **Contoso University** e não **meu aplicativo ASP.NET**.</span><span class="sxs-lookup"><span data-stu-id="64ced-144">Change the title to display **Contoso University** and not **My ASP.NET Application**.</span></span>

      [!code-aspx-csharp[Main](retrieving-data/samples/sample1.aspx?highlight=1)]

   3. <span data-ttu-id="64ced-145">Alterar o texto do cabeçalho da **nome do aplicativo** à **Contoso University**.</span><span class="sxs-lookup"><span data-stu-id="64ced-145">Change the header text from **Application name** to **Contoso University**.</span></span>

      [!code-aspx-csharp[Main](retrieving-data/samples/sample2.aspx?highlight=7)]

   4. <span data-ttu-id="64ced-146">Altere os links de cabeçalho de navegação para aqueles apropriados do site.</span><span class="sxs-lookup"><span data-stu-id="64ced-146">Change the navigation header links to site appropriate ones.</span></span> 
   
      <span data-ttu-id="64ced-147">Remover os links para **sobre** e **contato** e, em vez disso, vincular a um **alunos** página, que você criará.</span><span class="sxs-lookup"><span data-stu-id="64ced-147">Remove the links for **About** and **Contact** and, instead, link to a **Students** page, which you will create.</span></span>

      [!code-aspx-csharp[Main](retrieving-data/samples/sample3.aspx)]

   5. <span data-ttu-id="64ced-148">Save Site.Master.</span><span class="sxs-lookup"><span data-stu-id="64ced-148">Save Site.Master.</span></span>

## <a name="add-a-web-form-to-display-student-data"></a><span data-ttu-id="64ced-149">Adicionar um formulário da web para exibir dados de alunos</span><span class="sxs-lookup"><span data-stu-id="64ced-149">Add a web form to display student data</span></span>

   1. <span data-ttu-id="64ced-150">Na **Gerenciador de soluções**, clique em seu projeto, selecione **Add** e, em seguida, **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="64ced-150">In **Solution Explorer**, right-click your project, select **Add** and then **New Item**.</span></span> 
   
   2. <span data-ttu-id="64ced-151">No **Adicionar Novo Item** caixa de diálogo, selecione o **Web Form com página mestra** modelo e nomeie- **Students.aspx**.</span><span class="sxs-lookup"><span data-stu-id="64ced-151">In the **Add New Item** dialog box, select the **Web Form with Master Page** template and name it **Students.aspx**.</span></span>

      ![Criar página](retrieving-data/_static/image5.png)

   3. <span data-ttu-id="64ced-153">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="64ced-153">Select **Add**.</span></span>
   
   4. <span data-ttu-id="64ced-154">Para a página de mestre do formulário da web, selecione **Master**.</span><span class="sxs-lookup"><span data-stu-id="64ced-154">For the web form's master page, select **Site.Master**.</span></span>
   
   5. <span data-ttu-id="64ced-155">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="64ced-155">Select **OK**.</span></span>
   

## <a name="add-the-data-model"></a><span data-ttu-id="64ced-156">Adicionar o modelo de dados</span><span class="sxs-lookup"><span data-stu-id="64ced-156">Add the data model</span></span>

<span data-ttu-id="64ced-157">No **modelos** pasta, adicione uma classe chamada **UniversityModels.cs**.</span><span class="sxs-lookup"><span data-stu-id="64ced-157">In the **Models** folder, add a class named **UniversityModels.cs**.</span></span>

   1. <span data-ttu-id="64ced-158">Clique com botão direito **modelos**, selecione **Add**e então **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="64ced-158">Right-click **Models**, select **Add**, and then **New Item**.</span></span> <span data-ttu-id="64ced-159">A caixa de diálogo **Adicionar Novo Item** é exibida.</span><span class="sxs-lookup"><span data-stu-id="64ced-159">The **Add New Item** dialog box appears.</span></span>

   2. <span data-ttu-id="64ced-160">No menu de navegação à esquerda, selecione **código**, em seguida, **classe**.</span><span class="sxs-lookup"><span data-stu-id="64ced-160">From the left navigation menu, select **Code**, then **Class**.</span></span>

      ![Criar classe de modelo](retrieving-data/_static/image20.png)

   3. <span data-ttu-id="64ced-162">Nomeie a classe **UniversityModels.cs** e selecione **Add**.</span><span class="sxs-lookup"><span data-stu-id="64ced-162">Name the class **UniversityModels.cs** and select **Add**.</span></span>

      <span data-ttu-id="64ced-163">Nesse arquivo, defina as `SchoolContext`, `Student`, `Enrollment`, e `Course` classes da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="64ced-163">In this file, define the `SchoolContext`, `Student`, `Enrollment`, and `Course` classes as follows:</span></span>

      [!code-csharp[Main](retrieving-data/samples/sample4.cs)]

      <span data-ttu-id="64ced-164">O `SchoolContext` deriva de classe `DbContext`, que gerencia a conexão de banco de dados e alterações nos dados.</span><span class="sxs-lookup"><span data-stu-id="64ced-164">The `SchoolContext` class derives from `DbContext`, which manages the database connection and changes in the data.</span></span>

      <span data-ttu-id="64ced-165">No `Student` classe, observe os atributos aplicados ao `FirstName`, `LastName`, e `Year` propriedades.</span><span class="sxs-lookup"><span data-stu-id="64ced-165">In the `Student` class, notice the attributes applied to the `FirstName`, `LastName`, and `Year` properties.</span></span> <span data-ttu-id="64ced-166">Este tutorial usa esses atributos para validação de dados.</span><span class="sxs-lookup"><span data-stu-id="64ced-166">This tutorial uses these attributes for data validation.</span></span> <span data-ttu-id="64ced-167">Para simplificar o código, apenas essas propriedades são marcadas com atributos de validação de dados.</span><span class="sxs-lookup"><span data-stu-id="64ced-167">To simplify the code, only these properties are marked with data-validation attributes.</span></span> <span data-ttu-id="64ced-168">Em um projeto real, você aplicaria atributos de validação para todas as propriedades que precisam de validação.</span><span class="sxs-lookup"><span data-stu-id="64ced-168">In a real project, you would apply validation attributes to all properties needing validation.</span></span>

   4. <span data-ttu-id="64ced-169">Salve UniversityModels.cs.</span><span class="sxs-lookup"><span data-stu-id="64ced-169">Save UniversityModels.cs.</span></span>

## <a name="set-up-the-database-based-on-classes"></a><span data-ttu-id="64ced-170">Configurar o banco de dados com base em classes</span><span class="sxs-lookup"><span data-stu-id="64ced-170">Set up the database based on classes</span></span>

<span data-ttu-id="64ced-171">Este tutorial usa [migrações do Code First](https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/migrations/) para criar objetos e tabelas de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="64ced-171">This tutorial uses [Code First Migrations](https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/migrations/) to create objects and database tables.</span></span> <span data-ttu-id="64ced-172">Essas tabelas armazenam informações sobre os alunos e seus cursos.</span><span class="sxs-lookup"><span data-stu-id="64ced-172">These tables store information about the students and their courses.</span></span>

   1. <span data-ttu-id="64ced-173">Selecione **ferramentas** > **Gerenciador de pacotes NuGet** > **Package Manager Console**.</span><span class="sxs-lookup"><span data-stu-id="64ced-173">Select **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>

   2. <span data-ttu-id="64ced-174">Na **Package Manager Console**, execute este comando:</span><span class="sxs-lookup"><span data-stu-id="64ced-174">In **Package Manager Console**, run this command:</span></span>  
      `enable-migrations -ContextTypeName ContosoUniversityModelBinding.Models.SchoolContext`

      <span data-ttu-id="64ced-175">Se o comando for concluído com êxito, será exibida uma mensagem informando que as migrações foram habilitadas.</span><span class="sxs-lookup"><span data-stu-id="64ced-175">If the command completes successfully, a message stating migrations have been enabled appears.</span></span>

      ![Habilitar migrações](retrieving-data/_static/image8.png)

      <span data-ttu-id="64ced-177">Observe que um arquivo chamado *Configuration.cs* foi criado.</span><span class="sxs-lookup"><span data-stu-id="64ced-177">Notice that a file named *Configuration.cs* has been created.</span></span> <span data-ttu-id="64ced-178">O `Configuration` classe tem um `Seed` método, que pode preencher previamente as tabelas de banco de dados com dados de teste.</span><span class="sxs-lookup"><span data-stu-id="64ced-178">The `Configuration` class has a `Seed` method, which can pre-populate the database tables with test data.</span></span>

## <a name="pre-populate-the-database"></a><span data-ttu-id="64ced-179">Preencher previamente o banco de dados</span><span class="sxs-lookup"><span data-stu-id="64ced-179">Pre-populate the database</span></span>

   1. <span data-ttu-id="64ced-180">Abra Configuration.cs.</span><span class="sxs-lookup"><span data-stu-id="64ced-180">Open Configuration.cs.</span></span>
   
   2. <span data-ttu-id="64ced-181">Adicione o seguinte código ao método de `Seed` .</span><span class="sxs-lookup"><span data-stu-id="64ced-181">Add the following code to the `Seed` method.</span></span> <span data-ttu-id="64ced-182">Além disso, adicione uma `using` instrução para o `ContosoUniversityModelBinding. Models` namespace.</span><span class="sxs-lookup"><span data-stu-id="64ced-182">Also, add a `using` statement for the `ContosoUniversityModelBinding. Models` namespace.</span></span>

      [!code-csharp[Main](retrieving-data/samples/sample5.cs)]

   3. <span data-ttu-id="64ced-183">Salve Configuration.cs.</span><span class="sxs-lookup"><span data-stu-id="64ced-183">Save Configuration.cs.</span></span>

   4. <span data-ttu-id="64ced-184">No Console do Gerenciador de pacotes, execute o comando **inicial de migração adicionar**.</span><span class="sxs-lookup"><span data-stu-id="64ced-184">In the Package Manager Console, run the command **add-migration initial**.</span></span>

   5. <span data-ttu-id="64ced-185">Execute o comando **update-database**.</span><span class="sxs-lookup"><span data-stu-id="64ced-185">Run the command **update-database**.</span></span>

      <span data-ttu-id="64ced-186">Se você receber uma exceção ao executar esse comando, o `StudentID` e `CourseID` valores podem ser diferentes de `Seed` valores do método.</span><span class="sxs-lookup"><span data-stu-id="64ced-186">If you receive an exception when running this command, the `StudentID` and `CourseID` values might be different from the `Seed` method values.</span></span> <span data-ttu-id="64ced-187">Abra as tabelas de banco de dados e localizar os valores existentes para `StudentID` e `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="64ced-187">Open those database tables and find existing values for `StudentID` and `CourseID`.</span></span> <span data-ttu-id="64ced-188">Adicione esses valores para o código para a propagação de `Enrollments` tabela.</span><span class="sxs-lookup"><span data-stu-id="64ced-188">Add those values to the code for seeding the `Enrollments` table.</span></span>

## <a name="add-a-gridview-control"></a><span data-ttu-id="64ced-189">Adicionar um controle GridView</span><span class="sxs-lookup"><span data-stu-id="64ced-189">Add a GridView control</span></span>

<span data-ttu-id="64ced-190">Com os dados do banco de dados preenchido, agora você está pronto para recuperar dados e exibi-lo.</span><span class="sxs-lookup"><span data-stu-id="64ced-190">With populated database data, you're now ready to retrieve that data and display it.</span></span> 

1. <span data-ttu-id="64ced-191">Abra Students.aspx.</span><span class="sxs-lookup"><span data-stu-id="64ced-191">Open Students.aspx.</span></span>

2. <span data-ttu-id="64ced-192">Localize o `MainContent` espaço reservado.</span><span class="sxs-lookup"><span data-stu-id="64ced-192">Locate the `MainContent` placeholder.</span></span> <span data-ttu-id="64ced-193">Dentro do espaço reservado, adicione uma **GridView** controle que inclui esse código.</span><span class="sxs-lookup"><span data-stu-id="64ced-193">Within that placeholder, add a **GridView** control that includes this code.</span></span>

   [!code-aspx-csharp[Main](retrieving-data/samples/sample6.aspx)]

   <span data-ttu-id="64ced-194">Coisas a observar:</span><span class="sxs-lookup"><span data-stu-id="64ced-194">Things to note:</span></span>
   * <span data-ttu-id="64ced-195">Observe que o valor definido para o `SelectMethod` propriedade no elemento GridView.</span><span class="sxs-lookup"><span data-stu-id="64ced-195">Notice the value set for the `SelectMethod` property in the GridView element.</span></span> <span data-ttu-id="64ced-196">Esse valor Especifica o método usado para recuperar dados do GridView, o que você criar na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="64ced-196">This value specifies the method used to retrieve GridView data, which you create in the next step.</span></span> 
   
   * <span data-ttu-id="64ced-197">O `ItemType` estiver definida como o `Student` classe criada anteriormente.</span><span class="sxs-lookup"><span data-stu-id="64ced-197">The `ItemType` property is set to the `Student` class created earlier.</span></span> <span data-ttu-id="64ced-198">Essa configuração permite que você faça referência a propriedades de classe na marcação.</span><span class="sxs-lookup"><span data-stu-id="64ced-198">This setting allows you to reference class properties in the markup.</span></span> <span data-ttu-id="64ced-199">Por exemplo, o `Student` classe tem uma coleção denominada `Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="64ced-199">For example, the `Student` class has a collection named `Enrollments`.</span></span> <span data-ttu-id="64ced-200">Você pode usar `Item.Enrollments` para recuperar essa coleção e, em seguida, use [sintaxe LINQ](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) recuperar cada aluno do registrados soma de créditos.</span><span class="sxs-lookup"><span data-stu-id="64ced-200">You can use `Item.Enrollments` to retrieve that collection and then use [LINQ syntax](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) to retrieve each student's enrolled credits sum.</span></span>
   
3. <span data-ttu-id="64ced-201">Salve Students.aspx.</span><span class="sxs-lookup"><span data-stu-id="64ced-201">Save Students.aspx.</span></span>

## <a name="add-code-to-retrieve-data"></a><span data-ttu-id="64ced-202">Adicione código para recuperar dados</span><span class="sxs-lookup"><span data-stu-id="64ced-202">Add code to retrieve data</span></span>

   <span data-ttu-id="64ced-203">No arquivo code-behind a Students.aspx, adicione o método especificado para o `SelectMethod` valor.</span><span class="sxs-lookup"><span data-stu-id="64ced-203">In the Students.aspx code-behind file, add the method specified for the `SelectMethod` value.</span></span> 
   
   1. <span data-ttu-id="64ced-204">Abra Students.aspx.cs.</span><span class="sxs-lookup"><span data-stu-id="64ced-204">Open Students.aspx.cs.</span></span>
   
   2. <span data-ttu-id="64ced-205">Adicione `using` instruções para o `ContosoUniversityModelBinding. Models` e `System.Data.Entity` namespaces.</span><span class="sxs-lookup"><span data-stu-id="64ced-205">Add `using` statements for the `ContosoUniversityModelBinding. Models` and `System.Data.Entity` namespaces.</span></span>

      [!code-csharp[Main](retrieving-data/samples/sample7.cs)]

   3. <span data-ttu-id="64ced-206">Adicione o método especificado para `SelectMethod`:</span><span class="sxs-lookup"><span data-stu-id="64ced-206">Add the method you specified for `SelectMethod`:</span></span>

      [!code-csharp[Main](retrieving-data/samples/sample8.cs)]

      <span data-ttu-id="64ced-207">O `Include` cláusula melhora o desempenho de consulta, mas não é obrigatório.</span><span class="sxs-lookup"><span data-stu-id="64ced-207">The `Include` clause improves query performance but isn't required.</span></span> <span data-ttu-id="64ced-208">Sem o `Include` cláusula, os dados é recuperada usando [ *carregamento lento*](https://en.wikipedia.org/wiki/Lazy_loading), que envolve o envio de uma consulta separada no banco de dados cada vez relacionadas a dados são recuperados.</span><span class="sxs-lookup"><span data-stu-id="64ced-208">Without the `Include` clause, the data is retrieved using [*lazy loading*](https://en.wikipedia.org/wiki/Lazy_loading), which involves sending a separate query to the database each time related data is retrieved.</span></span> <span data-ttu-id="64ced-209">Com o `Include` cláusula, os dados é recuperada usando *carregamento adiantado*, que significa que uma consulta de banco de dados único recupera todos os dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="64ced-209">With the `Include` clause, data is retrieved using *eager loading*, which means a single database query retrieves all related data.</span></span> <span data-ttu-id="64ced-210">Se os dados relacionados não for usados, o carregamento adiantado é menos eficiente, pois mais dados são recuperados.</span><span class="sxs-lookup"><span data-stu-id="64ced-210">If related data isn't used, eager loading is less efficient because more data is retrieved.</span></span> <span data-ttu-id="64ced-211">No entanto, nesse caso, o carregamento adiantado lhe dá o melhor desempenho porque os dados relacionados são exibidos para cada registro.</span><span class="sxs-lookup"><span data-stu-id="64ced-211">However, in this case, eager loading gives you the best performance because the related data is displayed for each record.</span></span>

      <span data-ttu-id="64ced-212">Para obter mais informações sobre considerações de desempenho ao carregar dados relacionados, consulte o **Lazy, adiantado e explícito carregamento de dados relacionados** seção o [leitura de dados relacionados com o Entity Framework em um ASP.NET Aplicativo MVC](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) artigo.</span><span class="sxs-lookup"><span data-stu-id="64ced-212">For more information about performance considerations when loading related data, see the **Lazy, Eager, and Explicit Loading of Related Data** section in the [Reading Related Data with the Entity Framework in an ASP.NET MVC Application](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) article.</span></span>

      <span data-ttu-id="64ced-213">Por padrão, eles são classificados pelos valores da propriedade marcada como a chave.</span><span class="sxs-lookup"><span data-stu-id="64ced-213">By default, the data is sorted by the values of the property marked as the key.</span></span> <span data-ttu-id="64ced-214">Você pode adicionar um `OrderBy` cláusula para especificar um valor de classificação diferentes.</span><span class="sxs-lookup"><span data-stu-id="64ced-214">You can add an `OrderBy` clause to specify a different sort value.</span></span> <span data-ttu-id="64ced-215">Neste exemplo, o padrão `StudentID` propriedade é usada para classificação.</span><span class="sxs-lookup"><span data-stu-id="64ced-215">In this example, the default `StudentID` property is used for sorting.</span></span> <span data-ttu-id="64ced-216">No [classificação, paginação e filtragem de dados](sorting-paging-and-filtering-data.md) artigo, o usuário está habilitado para selecionar uma coluna para classificação.</span><span class="sxs-lookup"><span data-stu-id="64ced-216">In the [Sorting, Paging, and Filtering Data](sorting-paging-and-filtering-data.md) article, the user is enabled to select a column for sorting.</span></span>
 
   4. <span data-ttu-id="64ced-217">Salve Students.aspx.cs.</span><span class="sxs-lookup"><span data-stu-id="64ced-217">Save Students.aspx.cs.</span></span>

## <a name="run-your-application"></a><span data-ttu-id="64ced-218">Executar seu aplicativo</span><span class="sxs-lookup"><span data-stu-id="64ced-218">Run your application</span></span> 

<span data-ttu-id="64ced-219">Executar o aplicativo web (**F5**) e navegue até a **alunos** página, que exibe o seguinte:</span><span class="sxs-lookup"><span data-stu-id="64ced-219">Run your web application (**F5**) and navigate to the **Students** page, which displays the following:</span></span>

   ![Mostrar dados](retrieving-data/_static/image16.png)

## <a name="automatic-generation-of-model-binding-methods"></a><span data-ttu-id="64ced-221">Geração automática de métodos de associação de modelo</span><span class="sxs-lookup"><span data-stu-id="64ced-221">Automatic generation of model binding methods</span></span>

<span data-ttu-id="64ced-222">Ao trabalhar com esta série de tutoriais, você pode simplesmente copiar o código do tutorial ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="64ced-222">When working through this tutorial series, you can simply copy the code from the tutorial to your project.</span></span> <span data-ttu-id="64ced-223">No entanto, uma desvantagem dessa abordagem é que você não ficar ciente do recurso fornecido pelo Visual Studio para gerar automaticamente o código para métodos de associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="64ced-223">However, one disadvantage of this approach is that you may not become aware of the feature provided by Visual Studio to automatically generate code for model binding methods.</span></span> <span data-ttu-id="64ced-224">Ao trabalhar em seus próprios projetos, geração automática de código pode economizar tempo e ajudar a você obter uma ideia de como implementar uma operação.</span><span class="sxs-lookup"><span data-stu-id="64ced-224">When working on your own projects, automatic code generation can save you time and help you gain a sense of how to implement an operation.</span></span> <span data-ttu-id="64ced-225">Esta seção descreve o recurso de geração de código automática.</span><span class="sxs-lookup"><span data-stu-id="64ced-225">This section describes the automatic code generation feature.</span></span> <span data-ttu-id="64ced-226">Esta seção é apenas informativa e não contém qualquer código que você precisa implementar em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="64ced-226">This section is only informational and does not contain any code you need to implement in your project.</span></span> 

<span data-ttu-id="64ced-227">Ao definir um valor para o `SelectMethod`, `UpdateMethod`, `InsertMethod`, ou `DeleteMethod` as propriedades no código de marcação, você pode selecionar o **criar novo método** opção.</span><span class="sxs-lookup"><span data-stu-id="64ced-227">When setting a value for the `SelectMethod`, `UpdateMethod`, `InsertMethod`, or `DeleteMethod` properties in the markup code, you can select the **Create New Method** option.</span></span>

![criar um método](retrieving-data/_static/image18.png)

<span data-ttu-id="64ced-229">Visual Studio não só cria um método no code-behind, com a assinatura correta, mas também gera o código de implementação para executar a operação.</span><span class="sxs-lookup"><span data-stu-id="64ced-229">Visual Studio not only creates a method in the code-behind with the proper signature, but also generates implementation code to perform the operation.</span></span> <span data-ttu-id="64ced-230">Se você definir primeiro o `ItemType` propriedade antes de usar a geração de código automático de recursos, os usos de código gerado de tipo para as operações.</span><span class="sxs-lookup"><span data-stu-id="64ced-230">If you first set the `ItemType` property before using the automatic code generation feature, the generated code uses that type for the operations.</span></span> <span data-ttu-id="64ced-231">Por exemplo, ao definir o `UpdateMethod` propriedade, o código a seguir é gerada automaticamente:</span><span class="sxs-lookup"><span data-stu-id="64ced-231">For example, when setting the `UpdateMethod` property, the following code is automatically generated:</span></span>

[!code-csharp[Main](retrieving-data/samples/sample9.cs)]

<span data-ttu-id="64ced-232">Novamente, esse código não precisa ser adicionado ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="64ced-232">Again, this code doesn't need to be added to your project.</span></span> <span data-ttu-id="64ced-233">O próximo tutorial, você implementará métodos para atualizar, excluir e adicionar novos dados.</span><span class="sxs-lookup"><span data-stu-id="64ced-233">In the next tutorial, you'll implement methods for updating, deleting, and adding new data.</span></span>

## <a name="summary"></a><span data-ttu-id="64ced-234">Resumo</span><span class="sxs-lookup"><span data-stu-id="64ced-234">Summary</span></span>

<span data-ttu-id="64ced-235">Neste tutorial, você criou classes de modelo de dados e gerado um banco de dados a partir dessas classes.</span><span class="sxs-lookup"><span data-stu-id="64ced-235">In this tutorial, you created data model classes and generated a database from those classes.</span></span> <span data-ttu-id="64ced-236">As tabelas de banco de dados é preenchido com dados de teste.</span><span class="sxs-lookup"><span data-stu-id="64ced-236">You filled the database tables with test data.</span></span> <span data-ttu-id="64ced-237">Você usado a associação de modelo para recuperar dados do banco de dados e exibidos, em seguida, os dados em um GridView.</span><span class="sxs-lookup"><span data-stu-id="64ced-237">You used model binding to retrieve data from the database, and then displayed the data in a GridView.</span></span>

<span data-ttu-id="64ced-238">Nos próximos [tutorial](updating-deleting-and-creating-data.md) nesta série, você habilitará o atualização, exclusão e criação de dados.</span><span class="sxs-lookup"><span data-stu-id="64ced-238">In the next [tutorial](updating-deleting-and-creating-data.md) in this series, you'll enable updating, deleting, and creating data.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="64ced-239">Avançar</span><span class="sxs-lookup"><span data-stu-id="64ced-239">Next</span></span>](updating-deleting-and-creating-data.md)

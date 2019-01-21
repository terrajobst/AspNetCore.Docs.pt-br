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
<a name="retrieving-and-displaying-data-with-model-binding-and-web-forms"></a>Recuperando e exibindo dados com a associação de modelos e formulários da web
====================

> Esta série de tutoriais demonstra aspectos básicos de como usar a associação de modelo com um projeto de Web Forms do ASP.NET. Associação de modelo torna a interação de dados mais simples que lidam com dados de objetos de origem (como ObjectDataSource ou SqlDataSource). Esta série começa com material introdutório e move para conceitos mais avançados em tutoriais posteriores.
> 
>  O padrão de associação de modelo funciona com qualquer tecnologia de acesso a dados. Neste tutorial, você usará o Entity Framework, mas você pode usar a tecnologia de acesso de dados que é mais familiar para você. De um controle de servidor associado a dados, como um controle ListView, GridView, DetailsView ou FormView, você deve especificar os nomes dos métodos a ser usado para selecionar, atualizando, excluindo e criação de dados. Neste tutorial, você especificará um valor para o SelectMethod. 
> 
> Dentro desse método, você pode fornecer a lógica para recuperar os dados. O próximo tutorial, você definirá valores para InsertMethod e DeleteMethod, o UpdateMethod.
>
> Você pode [Baixe](https://go.microsoft.com/fwlink/?LinkId=286116) o projeto completo no C# ou o Visual Basic. O código para download funciona com o Visual Studio 2012 e posterior. Ele usa o modelo do Visual Studio 2012, que é ligeiramente diferente do que o modelo do Visual Studio 2017 mostrado neste tutorial.
> 
> No tutorial, você executar o aplicativo no Visual Studio. Você também pode implantar o aplicativo em um provedor de hospedagem e disponibilizá-lo pela internet. A Microsoft oferece a hospedagem de web gratuita para até 10 sites em um  
>  [conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A443DD604). Para obter informações sobre como implantar um projeto de web do Visual Studio para aplicativos de Web do serviço de aplicativo do Azure, consulte a [implantação de Web do ASP.NET usando o Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md) série. Esse tutorial também mostra como usar o Entity Framework Code First Migrations para implantar seu banco de dados do SQL Server no banco de dados SQL.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versões de software usadas no tutorial
> 
> - Microsoft Visual Studio 2017 ou o Microsoft Visual Studio Community 2017
>   
> Este tutorial também funciona com o Visual Studio 2012 e o Visual Studio 2013, mas há algumas diferenças no modelo de projeto e de interface do usuário.


## <a name="what-youll-build"></a>O que você vai criar

Neste tutorial, você vai:

* Criar objetos de dados que refletem uma universidade com os alunos matriculados em cursos
* Criar tabelas de banco de dados dos objetos
* Preencher o banco de dados com dados de teste
* Exibir dados em um formulário da web

## <a name="create-the-project"></a>Criar o projeto

1. No Visual Studio 2017, crie uma **aplicativo Web ASP.NET (.NET Framework)** projeto chamado **ContosoUniversityModelBinding**.

   ![Criar projeto](retrieving-data/_static/image19.png)

2. Selecione **OK**. A caixa de diálogo Selecionar um modelo é exibido.

   ![Selecione os formulários da web](retrieving-data/_static/image3.png)

3. Selecione o **Web Forms** modelo. 

4. Se necessário, altere a autenticação do **contas de usuário individuais**. 

5. Selecione **OK** para criar o projeto.

## <a name="modify-site-appearance"></a>Modificar a aparência do site

   Fazer algumas alterações para personalizar a aparência do site. 
   
   1. Abra o arquivo site.
   
   2. Alterar o título a ser exibido **Contoso University** e não **meu aplicativo ASP.NET**.

      [!code-aspx-csharp[Main](retrieving-data/samples/sample1.aspx?highlight=1)]

   3. Alterar o texto do cabeçalho da **nome do aplicativo** à **Contoso University**.

      [!code-aspx-csharp[Main](retrieving-data/samples/sample2.aspx?highlight=7)]

   4. Altere os links de cabeçalho de navegação para aqueles apropriados do site. 
   
      Remover os links para **sobre** e **contato** e, em vez disso, vincular a um **alunos** página, que você criará.

      [!code-aspx-csharp[Main](retrieving-data/samples/sample3.aspx)]

   5. Save Site.Master.

## <a name="add-a-web-form-to-display-student-data"></a>Adicionar um formulário da web para exibir dados de alunos

   1. Na **Gerenciador de soluções**, clique em seu projeto, selecione **Add** e, em seguida, **Novo Item**. 
   
   2. No **Adicionar Novo Item** caixa de diálogo, selecione o **Web Form com página mestra** modelo e nomeie- **Students.aspx**.

      ![Criar página](retrieving-data/_static/image5.png)

   3. Selecione **Adicionar**.
   
   4. Para a página de mestre do formulário da web, selecione **Master**.
   
   5. Selecione **OK**.
   

## <a name="add-the-data-model"></a>Adicionar o modelo de dados

No **modelos** pasta, adicione uma classe chamada **UniversityModels.cs**.

   1. Clique com botão direito **modelos**, selecione **Add**e então **Novo Item**. A caixa de diálogo **Adicionar Novo Item** é exibida.

   2. No menu de navegação à esquerda, selecione **código**, em seguida, **classe**.

      ![Criar classe de modelo](retrieving-data/_static/image20.png)

   3. Nomeie a classe **UniversityModels.cs** e selecione **Add**.

      Nesse arquivo, defina as `SchoolContext`, `Student`, `Enrollment`, e `Course` classes da seguinte maneira:

      [!code-csharp[Main](retrieving-data/samples/sample4.cs)]

      O `SchoolContext` deriva de classe `DbContext`, que gerencia a conexão de banco de dados e alterações nos dados.

      No `Student` classe, observe os atributos aplicados ao `FirstName`, `LastName`, e `Year` propriedades. Este tutorial usa esses atributos para validação de dados. Para simplificar o código, apenas essas propriedades são marcadas com atributos de validação de dados. Em um projeto real, você aplicaria atributos de validação para todas as propriedades que precisam de validação.

   4. Salve UniversityModels.cs.

## <a name="set-up-the-database-based-on-classes"></a>Configurar o banco de dados com base em classes

Este tutorial usa [migrações do Code First](https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/migrations/) para criar objetos e tabelas de banco de dados. Essas tabelas armazenam informações sobre os alunos e seus cursos.

   1. Selecione **ferramentas** > **Gerenciador de pacotes NuGet** > **Package Manager Console**.

   2. Na **Package Manager Console**, execute este comando:  
      `enable-migrations -ContextTypeName ContosoUniversityModelBinding.Models.SchoolContext`

      Se o comando for concluído com êxito, será exibida uma mensagem informando que as migrações foram habilitadas.

      ![Habilitar migrações](retrieving-data/_static/image8.png)

      Observe que um arquivo chamado *Configuration.cs* foi criado. O `Configuration` classe tem um `Seed` método, que pode preencher previamente as tabelas de banco de dados com dados de teste.

## <a name="pre-populate-the-database"></a>Preencher previamente o banco de dados

   1. Abra Configuration.cs.
   
   2. Adicione o seguinte código ao método de `Seed` . Além disso, adicione uma `using` instrução para o `ContosoUniversityModelBinding. Models` namespace.

      [!code-csharp[Main](retrieving-data/samples/sample5.cs)]

   3. Salve Configuration.cs.

   4. No Console do Gerenciador de pacotes, execute o comando **inicial de migração adicionar**.

   5. Execute o comando **update-database**.

      Se você receber uma exceção ao executar esse comando, o `StudentID` e `CourseID` valores podem ser diferentes de `Seed` valores do método. Abra as tabelas de banco de dados e localizar os valores existentes para `StudentID` e `CourseID`. Adicione esses valores para o código para a propagação de `Enrollments` tabela.

## <a name="add-a-gridview-control"></a>Adicionar um controle GridView

Com os dados do banco de dados preenchido, agora você está pronto para recuperar dados e exibi-lo. 

1. Abra Students.aspx.

2. Localize o `MainContent` espaço reservado. Dentro do espaço reservado, adicione uma **GridView** controle que inclui esse código.

   [!code-aspx-csharp[Main](retrieving-data/samples/sample6.aspx)]

   Coisas a observar:
   * Observe que o valor definido para o `SelectMethod` propriedade no elemento GridView. Esse valor Especifica o método usado para recuperar dados do GridView, o que você criar na próxima etapa. 
   
   * O `ItemType` estiver definida como o `Student` classe criada anteriormente. Essa configuração permite que você faça referência a propriedades de classe na marcação. Por exemplo, o `Student` classe tem uma coleção denominada `Enrollments`. Você pode usar `Item.Enrollments` para recuperar essa coleção e, em seguida, use [sintaxe LINQ](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) recuperar cada aluno do registrados soma de créditos.
   
3. Salve Students.aspx.

## <a name="add-code-to-retrieve-data"></a>Adicione código para recuperar dados

   No arquivo code-behind a Students.aspx, adicione o método especificado para o `SelectMethod` valor. 
   
   1. Abra Students.aspx.cs.
   
   2. Adicione `using` instruções para o `ContosoUniversityModelBinding. Models` e `System.Data.Entity` namespaces.

      [!code-csharp[Main](retrieving-data/samples/sample7.cs)]

   3. Adicione o método especificado para `SelectMethod`:

      [!code-csharp[Main](retrieving-data/samples/sample8.cs)]

      O `Include` cláusula melhora o desempenho de consulta, mas não é obrigatório. Sem o `Include` cláusula, os dados é recuperada usando [ *carregamento lento*](https://en.wikipedia.org/wiki/Lazy_loading), que envolve o envio de uma consulta separada no banco de dados cada vez relacionadas a dados são recuperados. Com o `Include` cláusula, os dados é recuperada usando *carregamento adiantado*, que significa que uma consulta de banco de dados único recupera todos os dados relacionados. Se os dados relacionados não for usados, o carregamento adiantado é menos eficiente, pois mais dados são recuperados. No entanto, nesse caso, o carregamento adiantado lhe dá o melhor desempenho porque os dados relacionados são exibidos para cada registro.

      Para obter mais informações sobre considerações de desempenho ao carregar dados relacionados, consulte o **Lazy, adiantado e explícito carregamento de dados relacionados** seção o [leitura de dados relacionados com o Entity Framework em um ASP.NET Aplicativo MVC](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) artigo.

      Por padrão, eles são classificados pelos valores da propriedade marcada como a chave. Você pode adicionar um `OrderBy` cláusula para especificar um valor de classificação diferentes. Neste exemplo, o padrão `StudentID` propriedade é usada para classificação. No [classificação, paginação e filtragem de dados](sorting-paging-and-filtering-data.md) artigo, o usuário está habilitado para selecionar uma coluna para classificação.
 
   4. Salve Students.aspx.cs.

## <a name="run-your-application"></a>Executar seu aplicativo 

Executar o aplicativo web (**F5**) e navegue até a **alunos** página, que exibe o seguinte:

   ![Mostrar dados](retrieving-data/_static/image16.png)

## <a name="automatic-generation-of-model-binding-methods"></a>Geração automática de métodos de associação de modelo

Ao trabalhar com esta série de tutoriais, você pode simplesmente copiar o código do tutorial ao seu projeto. No entanto, uma desvantagem dessa abordagem é que você não ficar ciente do recurso fornecido pelo Visual Studio para gerar automaticamente o código para métodos de associação de modelo. Ao trabalhar em seus próprios projetos, geração automática de código pode economizar tempo e ajudar a você obter uma ideia de como implementar uma operação. Esta seção descreve o recurso de geração de código automática. Esta seção é apenas informativa e não contém qualquer código que você precisa implementar em seu projeto. 

Ao definir um valor para o `SelectMethod`, `UpdateMethod`, `InsertMethod`, ou `DeleteMethod` as propriedades no código de marcação, você pode selecionar o **criar novo método** opção.

![criar um método](retrieving-data/_static/image18.png)

Visual Studio não só cria um método no code-behind, com a assinatura correta, mas também gera o código de implementação para executar a operação. Se você definir primeiro o `ItemType` propriedade antes de usar a geração de código automático de recursos, os usos de código gerado de tipo para as operações. Por exemplo, ao definir o `UpdateMethod` propriedade, o código a seguir é gerada automaticamente:

[!code-csharp[Main](retrieving-data/samples/sample9.cs)]

Novamente, esse código não precisa ser adicionado ao seu projeto. O próximo tutorial, você implementará métodos para atualizar, excluir e adicionar novos dados.

## <a name="summary"></a>Resumo

Neste tutorial, você criou classes de modelo de dados e gerado um banco de dados a partir dessas classes. As tabelas de banco de dados é preenchido com dados de teste. Você usado a associação de modelo para recuperar dados do banco de dados e exibidos, em seguida, os dados em um GridView.

Nos próximos [tutorial](updating-deleting-and-creating-data.md) nesta série, você habilitará o atualização, exclusão e criação de dados.

> [!div class="step-by-step"]
> [Avançar](updating-deleting-and-creating-data.md)

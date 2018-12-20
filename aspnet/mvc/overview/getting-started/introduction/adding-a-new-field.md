---
uid: mvc/overview/getting-started/introduction/adding-a-new-field
title: Adicionando um novo campo | Microsoft Docs
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 10/17/2013
ms.assetid: 4085de68-d243-4378-8a64-86236ea8d2da
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-a-new-field
msc.type: authoredcontent
ms.openlocfilehash: 87bb2c5f64e714268f5e2631b44fbb8a93a6a4b6
ms.sourcegitcommit: 7b4e3936feacb1a8fcea7802aab3e2ea9c8af5b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48578076"
---
<a name="adding-a-new-field"></a>Adicionando um Novo Campo
====================
por [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

Nesta seção, você usará migrações do Entity Framework Code First para migrar algumas alterações para as classes de modelo para que a alteração será aplicada ao banco de dados.

Por padrão, quando você usa o Entity Framework Code First para criar automaticamente um banco de dados, como você fez neste tutorial, Code First adiciona uma tabela no banco de dados para ajudar a controlar se o esquema do banco de dados está em sincronizado com as classes de modelo, que ele foi gerado. Se não estiverem sincronizados, o Entity Framework gera um erro. Isso torna mais fácil rastrear problemas em tempo de desenvolvimento que caso contrário, apenas talvez (por erros obscuros) em tempo de execução.

## <a name="setting-up-code-first-migrations-for-model-changes"></a>Configuração de migrações do Code First para alterações do modelo

Navegue até o Gerenciador de soluções. Clique com botão direito do *Movies.mdf* do arquivo e selecione **excluir** para remover o banco de dados de filmes. Se você não vir as *Movies.mdf* do arquivo, clique no **Mostrar todos os arquivos** ícone mostrado abaixo no contorno vermelho.

![](adding-a-new-field/_static/image1.png)

Compile o aplicativo para garantir que não existem erros.

Dos **ferramentas** menu, clique em **Gerenciador de pacotes NuGet** e, em seguida, **Package Manager Console**.

![Adicionar pacote Man](adding-a-new-field/_static/image2.png)

No **Package Manager Console** janela no `PM>` prompt insira

Enable-Migrations -ContextTypeName MvcMovie.Models.MovieDBContext

![](adding-a-new-field/_static/image3.png)

O **Enable-Migrations** comando (mostrado acima) cria um *Configuration.cs* arquivo em uma nova *migrações* pasta.

![](adding-a-new-field/_static/image4.png)

O Visual Studio abre o *Configuration.cs* arquivo. Substitua os `Seed` método na *Configuration.cs* arquivo pelo código a seguir:

[!code-csharp[Main](adding-a-new-field/samples/sample1.cs)]

Passe o mouse sobre a linha vermelha ondulada sob `Movie` e clique em `Show Potential Fixes` e, em seguida, clique em **usando** **mvcmovie. Models;**

![](adding-a-new-field/_static/image5.png)

Isso adiciona a seguinte instrução using:

[!code-csharp[Main](adding-a-new-field/samples/sample2.cs)]

> [!NOTE]
> 
> Code First Migrations chamadas a `Seed` método após cada migração (ou seja, chamando **Atualizar banco de dados** no Console do Gerenciador de pacotes), e esse método atualiza as linhas que já foram inseridas ou insere-os se eles ainda não existem.
> 
> O [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) método no código a seguir executa uma operação "upsert":
> 
> [!code-csharp[Main](adding-a-new-field/samples/sample3.cs)]
> 
> Porque o [semente](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) método é executado com cada migração, você simplesmente não é possível inserir dados, porque as linhas que você está tentando adicionar já estará lá após a primeira migração que cria o banco de dados. O "[upsert](http://en.wikipedia.org/wiki/Upsert)" operação impede que os erros que acontecem se você tentar inserir uma linha que já existe, mas ela substitui as alterações nos dados feitas durante o teste o aplicativo. Com os dados de teste em algumas tabelas você talvez não queira que aconteça: em alguns casos quando você altera os dados durante o teste você deseja as suas alterações para permanecer após as atualizações do banco de dados. Nesse caso, você deseja fazer uma operação de inserção condicional: inserir uma linha apenas se ele ainda não existir.   
> 
> O primeiro parâmetro passado para o [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) método Especifica a propriedade a ser usada para verificar se já existe uma linha. Para os dados de filme de teste que você fornecer, o `Title` propriedade pode ser usada para essa finalidade, pois cada cargo da lista é exclusivo:
> 
> [!code-csharp[Main](adding-a-new-field/samples/sample4.cs)]
> 
> Esse código supõe que os títulos sejam exclusivos. Se você adicionar um título duplicado manualmente, você obterá a seguinte exceção na próxima vez que você executar uma migração.   
> 
>  *Sequência contém mais de um elemento*  
> 
> Para obter mais informações sobre o [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) método, consulte [tome cuidado com o EF 4.3 AddOrUpdate método](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/)...


**Pressione CTRL-SHIFT-B para compilar o projeto.** (As etapas a seguir falhará se você não criar neste momento).

A próxima etapa é criar um `DbMigration` classe para a migração inicial. Essa migração cria um novo banco de dados, é por isso que você excluiu o *movie.mdf* arquivo em uma etapa anterior.

No **Package Manager Console** janela, digite o comando `add-migration Initial` para criar a migração inicial. O nome "Inicial" é arbitrário e é usado para nomear o arquivo de migração criado.

![](adding-a-new-field/_static/image6.png)

Migrações do Code First cria outro arquivo de classe na *migrações* pasta (com o nome *{carimbo de data}\_Initial.cs* ), e essa classe contém código que cria o esquema de banco de dados. O nome do arquivo de migração é previamente corrigido com um carimbo de hora para ajudar com a ordenação. Examine os *{carimbo de data}\_Initial.cs* arquivo, ele contém as instruções para criar o `Movies` tabela para o banco de dados do filme. Quando você atualiza o banco de dados nas instruções a seguir, isso *{carimbo de data}\_Initial.cs* arquivo será executado e criar o esquema de banco de dados. Em seguida, a **semente** método será executado para preencher o banco de dados com dados de teste.

No **Package Manager Console**, digite o comando `update-database` para criar o banco de dados e executar o `Seed` método.

![](adding-a-new-field/_static/image7.png)

Se você receber um erro que indica uma tabela já existe e não pode ser criada, provavelmente é porque você executou o aplicativo depois que você excluiu o banco de dados e antes da execução `update-database`. Nesse caso, exclua o *Movies.mdf* novamente e repita a `update-database` comando. Se você ainda receber um erro, exclua a pasta migrações e o conteúdo, comece com as instruções na parte superior desta página (que é exclusão a *Movies.mdf* de arquivo e em seguida, vá para Enable-Migrations). Se você ainda receber um erro, abra o Pesquisador de objetos do SQL Server e remover o banco de dados da lista.

Execute o aplicativo e navegue até a */Movies* URL. Os dados de semente são exibidos.

![](adding-a-new-field/_static/image8.png)

## <a name="adding-a-rating-property-to-the-movie-model"></a>Adicionando uma propriedade de classificação ao modelo de filme

Comece adicionando um novo `Rating` propriedade à existente `Movie` classe. Abra o *Models\Movie.cs* arquivo e adicione o `Rating` propriedade parecida com esta:

[!code-csharp[Main](adding-a-new-field/samples/sample5.cs)]

Completo `Movie` classe agora parece o código a seguir:

[!code-csharp[Main](adding-a-new-field/samples/sample6.cs?highlight=12)]

Compile o aplicativo (Ctrl + Shift + B).

Como você adicionou um novo campo para o `Movie` classe, você também precisará atualizar a associação *lista de permissões* para que essa nova propriedade seja incluída. Atualizar o `bind` de atributo para `Create` e `Edit` métodos de ação para incluir o `Rating` propriedade:

[!code-csharp[Main](adding-a-new-field/samples/sample7.cs?highlight=1)]

Você também precisa atualizar os modelos de exibição para exibir, criar e editar a nova propriedade `Rating` na exibição do navegador.

Abra o *\Views\Movies\Index.cshtml* arquivo e adicione um `<th>Rating</th>` título de coluna logo após o **preço** coluna. Em seguida, adicione uma `<td>` coluna, próximo ao final do modelo para renderizar o `@item.Rating` valor. Abaixo está o que a atualização *index. cshtml* modelo de exibição se parece com:

[!code-cshtml[Main](adding-a-new-field/samples/sample8.cshtml?highlight=31-33,52-54)]

Em seguida, abra o *\Views\Movies\Create.cshtml* arquivo e adicione o `Rating` campo pela seguinte marcação destacadas. Isso renderiza uma caixa de texto para que você possa especificar uma classificação quando um novo filme é criado.

[!code-cshtml[Main](adding-a-new-field/samples/sample9.cshtml?highlight=9-15)]

Agora você atualizou o código do aplicativo para dar suporte a novos `Rating` propriedade.

Execute o aplicativo e navegue até a */Movies* URL. Quando você fizer isso, no entanto, você verá um dos seguintes erros:

![](adding-a-new-field/_static/image9.png)  
  
O modelo fazendo o contexto de 'MovieDBContext' foi alterado desde que o banco de dados foi criado. Considere o uso de migrações do Code First para atualizar o banco de dados (https://go.microsoft.com/fwlink/?LinkId=238269).

![](adding-a-new-field/_static/image10.png)

Você está vendo esse erro porque atualizada `Movie` classe de modelo no aplicativo agora é diferente do esquema do `Movie` tabela do banco de dados existente. (Não há nenhuma coluna `Rating` na tabela de banco de dados.)


Existem algumas abordagens para resolver o erro:

1. Faça com que o Entity Framework remova automaticamente e recrie o banco de dados com base no novo esquema de classe de modelo. Essa abordagem é muito conveniente no início do ciclo de desenvolvimento, quando você está fazendo o desenvolvimento ativo em um banco de dados de teste; ela permite que você desenvolva rapidamente o modelo e o esquema de banco de dados juntos. A desvantagem, no entanto, é que você perde os dados existentes no banco de dados — para que você *não* para usar essa abordagem em um banco de dados de produção! Muitas vezes, o uso de um inicializador para propagar um banco de dados com os dados de teste automaticamente é uma maneira produtiva de desenvolver um aplicativo. Para obter mais informações sobre inicializadores de banco de dados do Entity Framework, consulte [tutorial do ASP.NET MVC/Entity Framework](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).
2. Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo. A vantagem dessa abordagem é que você mantém os dados. Faça essa alteração manualmente ou criando um script de alteração de banco de dados.
3. Use as Migrações do Code First para atualizar o esquema de banco de dados.


Para este tutorial, usaremos as Migrações do Code First.

Atualize o método de propagação para que ele forneça um valor para a nova coluna. Abra o arquivo Migrations\Configuration.cs e adicionar um campo de classificação para cada objeto de filme.

[!code-csharp[Main](adding-a-new-field/samples/sample10.cs?highlight=6)]

Compile a solução e, em seguida, abra o **Package Manager Console** janela e digite o seguinte comando:

`add-migration Rating`

O `add-migration` comando informa a estrutura de migração para examinar o modelo de filme atual com o esquema de banco de dados do filme atual e crie o código necessário para migrar o banco de dados para o novo modelo. O nome *classificação* é arbitrário e é usado para nomear o arquivo de migração. É útil usar um nome significativo para a etapa de migração.

Quando esse comando for concluído, o Visual Studio abre o arquivo de classe que define o novo `DbMigration` classe, derivada e, no `Up` método, você pode ver o código que cria a nova coluna.

[!code-csharp[Main](adding-a-new-field/samples/sample11.cs)]

Compile a solução e, em seguida, insira o `update-database` comando os **Package Manager Console** janela.

A imagem a seguir mostra a saída na **Package Manager Console** janela (o carimbo de data acrescentando *classificação* será diferente.)

![](adding-a-new-field/_static/image11.png)

Execute novamente o aplicativo e navegue até a URL /Movies. Você pode ver o novo campo de classificação.

![](adding-a-new-field/_static/image12.png)

Clique o **criar novo** link para adicionar um novo filme. Observe que você pode adicionar uma classificação.

![7_CreateRioII](adding-a-new-field/_static/image13.png)

Clique em **Criar**. O novo filme, incluindo a classificação é exibido nos listagem de filmes:

![7_ourNewMovie_SM](adding-a-new-field/_static/image14.png)

Agora que o projeto está usando as migrações, você não precisará remover o banco de dados quando você adiciona um novo campo ou caso contrário, atualize o esquema. Na próxima seção, vamos fazer mais alterações de esquema e usar as migrações para atualizar o banco de dados.

Você também deve adicionar o `Rating` campo para os modelos de exibição de edição, detalhes e exclusão.

Você poderia digitar o comando "update-database" a **Package Manager Console** janela novamente e nenhum código de migração seriam executado, porque o esquema coincide com o modelo. No entanto, em execução "update-database" será executado o `Seed` método novamente, e se você alterou os dados de semente, as alterações serão perdidas porque o `Seed` dados upserts de método. Você pode ler mais sobre o `Seed` método no de Tom Dykstra popular [tutorial do ASP.NET MVC/Entity Framework](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).

Nesta seção, você viu como você pode modificar objetos de modelo e manter o banco de dados em sincronia com as alterações. Você também aprendeu uma maneira de preencher um banco de dados recém-criado com dados de exemplo para que você pode experimentar os cenários. Isso era apenas uma rápida introdução ao Code First, consulte [criando um modelo de dados do Entity Framework para um aplicativo ASP.NET MVC](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) para obter um tutorial mais completo sobre o assunto. Em seguida, vamos dar uma olhada em como você pode adicionar lógica de validação mais avançada para as classes de modelo e habilitar algumas regras de negócio a serem impostos.

> [!div class="step-by-step"]
> [Anterior](adding-search.md)
> [Próximo](adding-validation.md)

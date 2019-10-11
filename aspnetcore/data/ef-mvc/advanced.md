---
title: 'Tutorial: Saiba mais sobre cenários avançados - ASP.NET MVC com EF Core'
description: Este tutorial apresenta tópicos úteis para ir além das noções básicas de desenvolvimento de aplicativos Web ASP.NET Core que usam o Entity Framework Core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/advanced
ms.openlocfilehash: d4a2aad6d93cc9a53c730323620de59fead6d5ab
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259595"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a>Tutorial: Saiba mais sobre cenários avançados - ASP.NET MVC com EF Core

No tutorial anterior, você implementou a herança de tabela por hierarquia. Este tutorial apresenta vários tópicos que são úteis para consideração quando você vai além dos conceitos básicos de desenvolvimento de aplicativos Web ASP.NET Core que usam o Entity Framework Core.

Neste tutorial, você:

> [!div class="checklist"]
> * Executar consultas SQL brutas
> * Chamar uma consulta para retornar entidades
> * Chamar uma consulta para outros tipos
> * Chamar uma consulta de atualização
> * Examinar consultas SQL
> * Criar uma camada de abstração
> * Aprender sobre a Detecção automática de alterações
> * Aprender sobre o código-fonte e planos de desenvolvimento do EF Core
> * Aprender a usar o LINQ dinâmico para simplificar o código

## <a name="prerequisites"></a>Pré-requisitos

* [Implementar herança](inheritance.md)

## <a name="perform-raw-sql-queries"></a>Executar consultas SQL brutas

Uma das vantagens de usar o Entity Framework é que ele evita vincular o código de forma muito próxima a um método específico de armazenamento de dados. Ele faz isso pela geração de consultas SQL e comandos para você, que também libera você da necessidade de escrevê-los. Mas há casos excepcionais em que você precisa executar consultas SQL específicas criadas manualmente. Para esses cenários, a API do Code First do Entity Framework inclui métodos que permitem passar comandos SQL diretamente para o banco de dados. Você tem as seguintes opções no EF Core 1.0:

* Use o método `DbSet.FromSql` para consultas que retornam tipos de entidade. Os objetos retornados precisam ser do tipo esperado pelo objeto `DbSet` e são controlados automaticamente pelo contexto de banco de dados, a menos que você [desative o controle](crud.md#no-tracking-queries).

* Use o `Database.ExecuteSqlCommand` para comandos que não sejam de consulta.

Caso precise executar uma consulta que retorna tipos que não são entidades, use o ADO.NET com a conexão de banco de dados fornecida pelo EF. Os dados retornados não são controlados pelo contexto de banco de dados, mesmo se esse método é usado para recuperar tipos de entidade.

Como é sempre verdadeiro quando você executa comandos SQL em um aplicativo Web, é necessário tomar precauções para proteger o site contra ataques de injeção de SQL. Uma maneira de fazer isso é usar consultas parametrizadas para garantir que as cadeias de caracteres enviadas por uma página da Web não possam ser interpretadas como comandos SQL. Neste tutorial, você usará consultas parametrizadas ao integrar a entrada do usuário a uma consulta.

## <a name="call-a-query-to-return-entities"></a>Chamar uma consulta para retornar entidades

A classe `DbSet<TEntity>` fornece um método que você pode usar para executar uma consulta que retorna uma entidade do tipo `TEntity`. Para ver como isso funciona, você alterará o código no método `Details` do controlador Departamento.

Em *DepartmentsController.cs*, no método `Details`, substitua o código que recupera um departamento com uma chamada de método `FromSql`, conforme mostrado no seguinte código realçado:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

Para verificar se o novo código funciona corretamente, selecione a guia **Departamentos** e, em seguida, **Detalhes** de um dos departamentos.

![Detalhes do departamento](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a>Chamar uma consulta para outros tipos

Anteriormente, você criou uma grade de estatísticas de alunos para a página Sobre que mostrava o número de alunos para cada data de registro. Você obteve os dados do conjunto de entidades Students (`_context.Students`) e usou o LINQ para projetar os resultados em uma lista de objetos de modelo de exibição `EnrollmentDateGroup`. Suponha que você deseje gravar o próprio SQL em vez de usar LINQ. Para fazer isso, você precisa executar uma consulta SQL que retorna algo diferente de objetos de entidade. No EF Core 1.0, uma maneira de fazer isso é escrever um código ADO.NET e obter a conexão de banco de dados do EF.

Em *HomeController.cs*, substitua o método `About` pelo seguinte código:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

Adicionar uma instrução using:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

Execute o aplicativo e acesse a página Sobre. Ela exibe os mesmos dados que antes.

![Página Sobre](advanced/_static/about.png)

## <a name="call-an-update-query"></a>Chamar uma consulta de atualização

Suponha que os administradores do Contoso University desejem executar alterações globais no banco de dados, como alterar o número de créditos para cada curso. Se a universidade tiver uma grande quantidade de cursos, poderá ser ineficiente recuperá-los como entidades e alterá-los individualmente. Nesta seção, você implementará uma página da Web que permite ao usuário especificar um fator pelo qual alterar o número de créditos para todos os cursos e você fará a alteração executando uma instrução SQL UPDATE. A página da Web será semelhante à seguinte ilustração:

![Página Atualizar Créditos de Curso](advanced/_static/update-credits.png)

Em *CoursesController.cs*, adicione métodos UpdateCourseCredits para HttpGet e HttpPost:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

Quando o controlador processa uma solicitação HttpGet, nada é retornado em `ViewData["RowsAffected"]`, e a exibição mostra uma caixa de texto vazia e um botão Enviar, conforme mostrado na ilustração anterior.

Quando o botão **Atualizar** recebe um clique, o método HttpPost é chamado e multiplicador tem o valor inserido na caixa de texto. Em seguida, o código executa o SQL que atualiza os cursos e retorna o número de linhas afetadas para a exibição em `ViewData`. Quando a exibição obtém um valor `RowsAffected`, ela mostra o número de linhas atualizadas.

No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Views/Courses* e, em seguida, clique em **Adicionar > Novo Item**.

Na caixa de diálogo **Adicionar Novo Item**, clique em **ASP.NET Core** em **Instalado** no painel esquerdo, clique em **Razor View** e nomeie a nova exibição *UpdateCourseCredits.cshtml*.

Em *Views/Courses/UpdateCourseCredits.cshtml*, substitua o código de modelo pelo seguinte código:

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

Execute o método `UpdateCourseCredits` selecionando a guia **Cursos**, adicionando, em seguida, "/UpdateCourseCredits" ao final da URL na barra de endereços do navegador (por exemplo: `http://localhost:5813/Courses/UpdateCourseCredits`). Insira um número na caixa de texto:

![Página Atualizar Créditos de Curso](advanced/_static/update-credits.png)

Clique em **Atualizar**. O número de linhas afetadas é exibido:

![Linhas afetadas na página Atualizar Créditos de Curso](advanced/_static/update-credits-rows-affected.png)

Clique em **Voltar para a Lista** para ver a lista de cursos com o número revisado de créditos.

Observe que o código de produção deve garantir que as atualizações sempre resultem em dados válidos. O código simplificado mostrado aqui pode multiplicar o número de créditos o suficiente para resultar em números maiores que 5. (A propriedade `Credits` tem um atributo `[Range(0, 5)]`.) A consulta de atualização funciona, mas os dados inválidos podem causar resultados inesperados em outras partes do sistema que supõem que o número de créditos seja 5 ou inferior.

Para obter mais informações sobre consultas SQL brutas, consulte [Consultas SQL brutas](/ef/core/querying/raw-sql).

## <a name="examine-sql-queries"></a>Examinar consultas SQL

Às vezes, é útil poder ver as consultas SQL reais que são enviadas ao banco de dados. A funcionalidade de log interno do ASP.NET Core é usada automaticamente pelo EF Core para gravar logs que contêm o SQL de consultas e atualizações. Nesta seção, você verá alguns exemplos de log do SQL.

Abra *StudentsController.cs* e, no método `Details`, defina um ponto de interrupção na instrução `if (student == null)`.

Execute o aplicativo no modo de depuração e acesse a página Detalhes de um aluno.

Acesse a janela de **Saída** mostrando a saída de depuração e você verá a consulta:

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

Você observará algo aqui que pode ser surpreendente: o SQL seleciona até 2 linhas (`TOP(2)`) da tabela Person. O método `SingleOrDefaultAsync` não é resolvido para uma 1 linha no servidor. Eis o porquê:

* Se a consulta retorna várias linhas, o método retorna nulo.
* Para determinar se a consulta retorna várias linhas, o EF precisa verificar se ela retorna pelo menos 2.

Observe que você não precisa usar o modo de depuração e parar em um ponto de interrupção para obter a saída de log na janela de **Saída**. É apenas um modo conveniente de parar o log no ponto em que você deseja examinar a saída. Se você não fizer isso, o log continuará e você precisará rolar para baixo para localizar as partes de seu interesse.

## <a name="create-an-abstraction-layer"></a>Criar uma camada de abstração

Muitos desenvolvedores escrevem um código para implementar padrões de repositório e unidade de trabalho como um wrapper em torno do código que funciona com o Entity Framework. Esses padrões destinam-se a criar uma camada de abstração entre a camada de acesso a dados e a camada da lógica de negócios de um aplicativo. A implementação desses padrões pode ajudar a isolar o aplicativo de alterações no armazenamento de dados e pode facilitar o teste de unidade automatizado ou TDD (desenvolvimento orientado por testes). No entanto, escrever um código adicional para implementar esses padrões nem sempre é a melhor escolha para aplicativos que usam o EF, por vários motivos:

* A própria classe de contexto do EF isola o código de código específico a um armazenamento de dados.

* A classe de contexto do EF pode atuar como uma classe de unidade de trabalho para as atualizações de banco de dados feitas com o EF.

* O EF inclui recursos para implementar o TDD sem escrever um código de repositório.

Para obter informações sobre como implementar os padrões de repositório e unidade de trabalho, consulte [a versão do Entity Framework 5 desta série de tutoriais](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).

O Entity Framework Core implementa um provedor de banco de dados em memória que pode ser usado para teste. Para obter mais informações, confira [Testar com InMemory](/ef/core/miscellaneous/testing/in-memory).

## <a name="automatic-change-detection"></a>Detecção automática de alterações

O Entity Framework determina como uma entidade foi alterada (e, portanto, quais atualizações precisam ser enviadas ao banco de dados), comparando os valores atuais de uma entidade com os valores originais. Os valores originais são armazenados quando a entidade é consultada ou anexada. Alguns dos métodos que causam a detecção automática de alterações são os seguintes:

* DbContext.SaveChanges

* DbContext.Entry

* ChangeTracker.Entries

Se você estiver controlando um grande número de entidades e chamar um desses métodos muitas vezes em um loop, poderá obter melhorias significativas de desempenho desativando temporariamente a detecção automática de alterações usando a propriedade `ChangeTracker.AutoDetectChangesEnabled`. Por exemplo:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a>Código-fonte e planos de desenvolvimento do EF Core

O código-fonte do Entity Framework Core está em [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore). O repositório do EF Core contém builds noturnos, acompanhamento de questões, especificações de recurso, notas de reuniões de design e [o roteiro para desenvolvimento futuro](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap). Arquive ou encontre bugs e contribua.

Embora o código-fonte seja aberto, há suporte completo para o Entity Framework Core como um produto Microsoft. A equipe do Microsoft Entity Framework mantém controle sobre quais contribuições são aceitas e testa todas as alterações de código para garantir a qualidade de cada versão.

## <a name="reverse-engineer-from-existing-database"></a>Fazer engenharia reversa do banco de dados existente

Para fazer engenharia reversa de um modelo de dados, incluindo classes de entidade de um banco de dados existente, use o comando [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext). Consulte o [tutorial de introdução](/ef/core/get-started/aspnetcore/existing-db).

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a>Usar o LINQ dinâmico para simplificar o código

O [terceiro tutorial desta série](sort-filter-page.md) mostra como escrever um código LINQ embutindo nomes de colunas em código em uma instrução `switch`. Com duas colunas para escolha, isso funciona bem, mas se você tiver muitas colunas, o código poderá ficar detalhado. Para resolver esse problema, use o método `EF.Property` para especificar o nome da propriedade como uma cadeia de caracteres. Para usar essa abordagem, substitua o método `Index` no `StudentsController` pelo código a seguir.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a>Agradecimentos

Tom Dykstra e Rick Anderson (twitter @RickAndMSFT) escreveram este tutorial. Rowan Miller, Diego Vega e outros membros da equipe do Entity Framework auxiliaram com revisões de código e ajudaram com problemas de depuração que surgiram durante a codificação para os tutoriais. John Parente e Paul Goldman trabalharam na atualização do tutorial do ASP.NET Core 2.2.

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a>Solucionar erros comuns

### <a name="contosouniversitydll-used-by-another-process"></a>ContosoUniversity.dll usada por outro processo

Mensagem de erro:

> Não é possível abrir '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' para gravação – 'O processo não pode acessar o arquivo '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' porque ele está sendo usado por outro processo.

Solução:

Pare o site no IIS Express. Acesse a Bandeja do Sistema do Windows, localize o IIS Express e clique com o botão direito do mouse em seu ícone, selecione o site da Contoso University e, em seguida, clique em **Parar Site**.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>Migração gerada por scaffolding sem nenhum código nos métodos Up e Down

Possível causa:

Os comandos da CLI do EF não fecham e salvam arquivos de código automaticamente. Se você tiver alterações não salvas ao executar o comando `migrations add`, o EF não encontrará as alterações.

Solução:

Execute o comando `migrations remove`, salve as alterações de código e execute o comando `migrations add` novamente.

### <a name="errors-while-running-database-update"></a>Erros durante a execução da atualização de banco de dados

É possível receber outros erros ao fazer alterações de esquema em um banco de dados que contém dados existentes. Se você receber erros de migração que não consegue resolver, altere o nome do banco de dados na cadeia de conexão ou exclua o banco de dados. Com um novo banco de dados, não há nenhum dado a ser migrado e o comando de atualização de banco de dados terá uma probabilidade muito maior de ser concluído sem erros.

A abordagem mais simples é renomear o banco de dados em *appsettings.json*. Na próxima vez que você executar `database update`, um novo banco de dados será criado.

Para excluir um banco de dados no SSOX, clique com o botão direito do mouse no banco de dados, clique **Excluir** e, em seguida, na caixa de diálogo **Excluir Banco de Dados**, selecione **Fechar conexões existentes** e clique em **OK**.

Para excluir um banco de dados usando a CLI, execute o comando `database drop` da CLI:

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>Erro ao localizar a instância do SQL Server

Mensagem de erro:

> Ocorreu um erro relacionado à rede ou específico a uma instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível. Verifique se o nome da instância está correto e se o SQL Server está configurado para permitir conexões remotas. (provedor: Adaptadores de Rede do SQL, erro: 26 – Erro ao localizar a instância/o servidor especificado)

Solução:

Verifique a cadeia de conexão. Se você excluiu o arquivo de banco de dados manualmente, altere o nome do banco de dados na cadeia de caracteres de construção para começar novamente com um novo banco de dados.

## <a name="get-the-code"></a>Obter o código

[Baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre o EF Core, consulte a [documentação do Entity Framework Core](/ef/core). Também há um livro disponível: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action) (Entity Framework Core em ação).

Para obter informações sobre como implantar um aplicativo Web, confira <xref:host-and-deploy/index>.

Para obter informações sobre outros tópicos relacionados ao ASP.NET Core MVC, como autenticação e autorização, confira <xref:index>.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Executou consultas SQL brutas
> * Chamou uma consulta para retornar entidades
> * Chamou uma consulta para outros tipos
> * Chamou uma consulta de atualização
> * Examinou consultas SQL
> * Criou uma camada de abstração
> * Aprendeu sobre a Detecção automática de alterações
> * Aprendeu sobre o código-fonte e planos de desenvolvimento do EF Core
> * Aprendeu a usar o LINQ dinâmico para simplificar o código

Isso conclui esta série de tutoriais sobre como usar o Entity Framework Core em um aplicativo ASP.NET Core MVC. Esta série trabalhou com um novo banco de dados; uma alternativa é fazer engenharia reversa com um modelo de um banco de dados existente.

> [!div class="nextstepaction"]
> [Tutorial: EF Core com MVC, banco de dados existente](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)

---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Tutorial: Usar async e procedimentos armazenados com o EF em um aplicativo do ASP.NET MVC'
description: Neste tutorial, você verá como implementar o modelo de programação assíncrono e saiba como usar procedimentos armazenados.
author: tdykstra
ms.author: riande
ms.date: 01/18/2019
ms.topic: tutorial
ms.assetid: 27d110fc-d1b7-4628-a763-26f1e6087549
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 9041167af076d80ebf294e054ffe51293d11e888
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889867"
---
# <a name="tutorial-use-async-and-stored-procedures-with-ef-in-an-aspnet-mvc-app"></a>Tutorial: Usar async e procedimentos armazenados com o EF em um aplicativo do ASP.NET MVC

Nos tutoriais anteriores, você aprendeu como ler e atualizar dados usando o modelo de programação síncrono. Neste tutorial, você verá como implementar o modelo de programação assíncrono. O código assíncrono pode ajudar a um aplicativo têm melhor desempenho porque faz melhor uso dos recursos de servidor.

Neste tutorial, você também verá como usar procedimentos armazenados para inserção, atualização e operações de exclusão em uma entidade.

Por fim, você reimplantar o aplicativo no Azure, juntamente com todas as alterações de banco de dados que você já implementou desde a primeira vez que você implantou.

As ilustrações a seguir mostram algumas das páginas com as quais você trabalhará.

![Página de departamentos](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Criar departamento](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

Neste tutorial, você:

> [!div class="checklist"]
> * Saiba mais sobre o código assíncrono
> * Criar um controlador de departamento
> * Use os procedimentos armazenados
> * Implantar no Azure

## <a name="prerequisites"></a>Pré-requisitos

* [Atualização de dados relacionados](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="why-use-asynchronous-code"></a>Por que usar o código assíncrono

Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso. Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados. Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S. Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações. Como resultado, o código assíncrono permite que os recursos de servidor a ser usar com mais eficiência e o servidor está habilitado para manipular mais tráfego sem atrasos.

Em versões anteriores do .NET, escrever e testar o código assíncrono eram complexo, difícil de depurar e propensa a erros. É muito mais fácil do que em geral você deve escrever código assíncrono, a menos que você tenha um motivo para não escrevendo, testando e depurando código assíncrono no .NET 4.5. O código assíncrono introduz uma pequena quantidade de sobrecarga, mas para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.

Para obter mais informações sobre a programação assíncrona, consulte [suporte de assíncrono do uso .NET 4.5 para evitar o bloqueio de chamadas](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices.md#async).

## <a name="create-department-controller"></a>Criar um controlador de departamento

Criar um controlador de departamento da mesma maneira que você fez controladores anteriores, exceto que desta vez selecione o **usar ações do controlador assíncrono** caixa de seleção.

Os seguintes destaques mostram o que foi adicionado para o código síncrono para o `Index` método para torná-la assíncrona:

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=1,4)]

Quatro alterações foram aplicadas para habilitar a consulta de banco de dados do Entity Framework executar de forma assíncrona:

- O método é marcado com o `async` palavra-chave, que informa ao compilador para gerar retornos de chamada para partes do corpo do método e para criar automaticamente o `Task<ActionResult>` objeto que é retornado.
- O tipo de retorno foi alterado de `ActionResult` para `Task<ActionResult>`. O `Task<T>` tipo representa um trabalho em andamento com um resultado do tipo `T`.
- O `await` palavra-chave foi aplicada para a chamada de serviço web. Quando o compilador vê essa palavra-chave, nos bastidores ele divide o método em duas partes. A primeira parte termina com a operação que é iniciada de forma assíncrona. A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação for concluída.
- A versão assíncrona do `ToList` método de extensão foi chamado.

Por que é o `departments.ToList` instrução foi modificado, mas não o `departments = db.Departments` instrução? O motivo é que apenas as instruções que fazem com que consultas ou comandos sejam enviados para o banco de dados são executadas de forma assíncrona. O `departments = db.Departments` instrução define uma consulta, mas a consulta não é executada até que o `ToList` método é chamado. Portanto, apenas o `ToList` método é executado de forma assíncrona.

No `Details` método e o `HttpGet` `Edit` e `Delete` métodos, o `Find` método é o que faz com que uma consulta a ser enviado ao banco de dados, portanto, que é o método que é executado de forma assíncrona:

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs?highlight=7)]

No `Create`, `HttpPost Edit`, e `DeleteConfirmed` métodos, ele é o `SaveChanges` chamada de método que faz com que um comando a ser executado, não instruções como `db.Departments.Add(department)` que apenas fazer com que entidades na memória a ser modificado.

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs?highlight=6)]

Abra *Views\Department\Index.cshtml*e substitua o código de modelo pelo código a seguir:

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cshtml?highlight=3,5,20-22,36-38)]

Esse código altera o título do índice para departamentos, move o nome do administrador para a direita e fornece o nome completo do administrador.

Em criar, excluir, detalhes e editar modos de exibição, alterar a legenda para o `InstructorID` campo para "Administrator" da mesma forma que você alterou o campo de nome de departamento para "Departamento" nas exibições do curso.

Na criar e editar modos de exibição usam o seguinte código:

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml)]

Nas exibições de excluir e detalhes, use o seguinte código:

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml)]

Executar o aplicativo e, em seguida, clique no **departamentos** guia.

Tudo o que funciona da mesma maneira os outros controladores, mas neste controlador todas as consultas SQL estão em execução assíncrona.

Algumas coisas a serem consideradas quando você estiver usando a programação assíncrona com o Entity Framework:

- O código assíncrono não é thread-safe. Em outras palavras, em outras palavras, não tente realizar várias operações em paralelo usando a mesma instância de contexto.
- Se desejar aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca que você está usando (por exemplo, para paginação) também usam o código assíncrono se eles chamam métodos do Entity Framework que fazem com que consultas sejam enviadas ao banco de dados.

## <a name="use-stored-procedures"></a>Use os procedimentos armazenados

Alguns desenvolvedores e DBAs preferem usar os procedimentos armazenados para acesso de banco de dados. Em versões anteriores do Entity Framework, você pode recuperar dados usando um procedimento armazenado pelo [executando uma consulta SQL bruta](advanced-entity-framework-scenarios-for-an-mvc-web-application.md), mas não é possível instruir o EF para usar procedimentos armazenados para operações de atualização. No EF 6 é fácil de configurar o Code First para usar procedimentos armazenados.

1. Na *DAL\SchoolContext.cs*, adicione o código realçado para o `OnModelCreating` método.

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=9)]

    Este código instrui o Entity Framework para procedimentos armazenado de uso para inserção, atualizar e excluir operações o `Department` entidade.
2. No Console de gerenciamento de pacote, digite o seguinte comando:

    `add-migration DepartmentSP`

    Abra *migrações\&lt; carimbo de hora&gt;\_DepartmentSP.cs* para ver o código no `Up` método que cria Insert, Update e Delete de procedimentos armazenados:

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs?highlight=3-4,26-27,42-43)]
3. No Console de gerenciamento de pacote, digite o seguinte comando:

     `update-database`
4. Executar o aplicativo no modo de depuração, clique o **departamentos** guia e, em seguida, clique em **criar novo**.
5. Insira dados para outro departamento e, em seguida, clique em **criar**.

6. No Visual Studio, examine os logs na **saída** janela para ver se um procedimento armazenado foi usado para inserir a nova linha de departamento.

     ![SP de inserção do departamento](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

Primeiro, o código cria nomes de procedimento armazenado de padrão. Se você estiver usando um banco de dados existente, talvez você precise personalizar os nomes de procedimento armazenado para usar procedimentos armazenados já definidos no banco de dados. Para obter informações sobre como fazer isso, consulte [Entity Framework código primeiro inserir/atualizar/excluir procedimentos armazenados](https://msdn.microsoft.com/data/dn468673).

Se você quiser personalizar quais gerados procedimentos armazenados, você pode editar o código gerado automaticamente para as migrações `Up` método que cria o procedimento armazenado. Dessa forma, suas alterações serão refletidas sempre que a migração é executada e será aplicada ao banco de dados de produção quando as migrações é executado automaticamente em produção após a implantação.

Se você quiser alterar um procedimento armazenado existente que foi criado em uma migração anterior, você pode usar o comando Add-Migration para gerar uma migração em branco e, em seguida, escrever manualmente o código que chama o [AlterStoredProcedure](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.alterstoredprocedure.aspx) método .

## <a name="deploy-to-azure"></a>Implantar no Azure

Esta seção requer que você concluiu a opcional **Implantando o aplicativo do Azure** seção o [migrações e implantação](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md) tutorial desta série. Se você tiver erros de migrações que resolvido excluindo o banco de dados em seu projeto local, ignore esta seção.

1. No Visual Studio, clique com botão direito no projeto no **Gerenciador de soluções** e selecione **publicar** no menu de contexto.
2. Clique em **Publicar**.

    O Visual Studio implanta o aplicativo no Azure e o aplicativo é aberto no navegador padrão, em execução no Azure.
3. Testar o aplicativo para verificar se ele está funcionando.

    Na primeira vez que você executa uma página que acessa o banco de dados, o Entity Framework é executado todas as migrações `Up` métodos necessários para colocar o banco de dados atualizado com o modelo de dados atual. Agora você pode usar todas as páginas da web que você adicionou desde a última vez implantado, incluindo as páginas de departamento que você adicionou neste tutorial.

## <a name="get-the-code"></a>Obter o código

[Baixe o projeto concluído](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>Recursos adicionais

Links para outros recursos do Entity Framework podem ser encontradas na [acesso a dados ASP.NET – recursos recomendados](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Aprendeu sobre o código assíncrono
> * Criou um controlador de departamento
> * Procedimentos armazenados usados
> * Implantado no Azure

Avance para o próximo artigo para aprender a lidar com conflitos quando vários usuários atualizam a mesma entidade ao mesmo tempo.
> [!div class="nextstepaction"]
> [Tratamento de simultaneidade](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)

---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Tutorial: Usar a interceptação de comando e resiliência de conexão com o EF em um aplicativo ASP.NET MVC'
author: tdykstra
description: Neste tutorial, você aprenderá como usar a interceptação de comando e resiliência de conexão. Eles são dois recursos importantes do Entity Framework 6.
ms.author: riande
ms.date: 01/14/2018
ms.topic: tutorial
ms.assetid: c89d809f-6c65-4425-a3fa-c9f6e8ac89f2
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: fae5c7e1ad1000ed90630c3620b853de3a735d60
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341715"
---
# <a name="tutorial-use-connection-resiliency-and-command-interception-with-entity-framework-in-an-aspnet-mvc-app"></a>Tutorial: Usar a interceptação de comando e resiliência de conexão com o Entity Framework em um aplicativo ASP.NET MVC

Até agora o aplicativo tiver sido executado localmente no IIS Express no computador de desenvolvimento. Para disponibilizar um aplicativo real para que outras pessoas usem a Internet, você precisará implantá-lo em um provedor de hospedagem na web, e você precisa implantar o banco de dados em um servidor de banco de dados.

Neste tutorial, você aprenderá como usar a interceptação de comando e resiliência de conexão. Eles são dois recursos importantes do Entity Framework 6 são especialmente valiosos quando você estiver implantando no ambiente de nuvem: (novas tentativas automáticas para erros transitórios) de resiliência de conexão e interceptação de comando (catch todas as consultas SQL enviadas ao banco de dados Para fazer logon ou alterá-los).

Este tutorial de interceptação de resiliência e o comando de conexão é opcional. Se você ignorar este tutorial, alguns pequenos ajustes terá de ser feitos em tutoriais subsequentes.

Neste tutorial, você:

> [!div class="checklist"]
> * Habilitar a resiliência de conexão
> * Habilitar a interceptação de comando
> * A nova configuração de teste

## <a name="prerequisites"></a>Pré-requisitos

* [Classificação, filtragem e paginação](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="enable-connection-resiliency"></a>Habilitar a resiliência de conexão

Quando você implanta o aplicativo no Windows Azure, você implantará o banco de dados para o Windows Azure SQL Database, um serviço de banco de dados de nuvem. Erros transitórios de conexão são geralmente mais frequentes quando você se conecta a um serviço de banco de dados de nuvem que quando o servidor web e o servidor de banco de dados estão diretamente conectados juntos no mesmo data center. Mesmo se um servidor web na nuvem e um serviço de banco de dados de nuvem são hospedadas no mesmo data center, há mais conexões de rede entre elas, pode ter problemas, como balanceadores de carga.

Também um serviço de nuvem é normalmente compartilhado por outros usuários, que significa que sua capacidade de resposta pode ser afetada por eles. E seu acesso ao banco de dados pode estar sujeito à limitação. Limitação significa que o serviço de banco de dados gera exceções quando você tenta acessá-la mais frequentemente, que é permitido no contrato de nível de serviço (SLA).

Muitos ou a maioria dos problemas de conexão, quando você estiver acessando um serviço de nuvem são transitórios, ou seja, eles resolvem sozinho em um curto período de tempo. Portanto, quando você tentar uma operação de banco de dados e obter um tipo de erro que é normalmente transitório, você poderia Repita a operação após uma breve espera e a operação podem ser bem-sucedida. Você pode fornecer uma experiência muito melhor para seus usuários, se você tratar erros transitórios automaticamente tentando novamente, tornando a maioria deles invisível para o cliente. O recurso de resiliência de conexão no Entity Framework 6 automatiza a que o processo de repetição falhou consultas SQL.

O recurso de resiliência de conexão deve ser configurado adequadamente para um serviço de banco de dados específico:

- Ele precisa saber quais exceções são possivelmente é transitório. Você deseja repetir erros causados por uma perda temporária na conectividade de rede, não há suporte para os erros causados por bugs de programa, por exemplo.
- Ele deve esperar uma quantidade apropriada de tempo entre as repetições de uma operação com falha. Você pode esperar mais entre as repetições para um processo em lote, que é possível para uma página da web online em que um usuário está aguardando uma resposta.
- Ele tem que repetir um número apropriado de vezes antes de ele desiste. Talvez você queira repetir mais vezes em um processo em lote que você usaria em um aplicativo online.

Você pode definir essas configurações manualmente para qualquer ambiente de banco de dados com suporte por um provedor de Entity Framework, mas os valores padrão que normalmente funcionam bem para um aplicativo online que usa o banco de dados SQL do Windows Azure já foram configurados para você, e Essas são as configurações que você implementará para o aplicativo Contoso University.

Tudo o que você precisa fazer para habilitar a resiliência de conexão é criar uma classe em seu assembly derivado do [DbConfiguration](https://msdn.microsoft.com/data/jj680699.aspx) classe e nessa classe, defina o banco de dados SQL *estratégia de execução*, que no EF é outro termo para *política de repetição*.

1. Na pasta DAL, adicione um arquivo de classe chamado *SchoolConfiguration.cs*.
2. Substitua o código do modelo pelo seguinte código:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

    O Entity Framework é executado automaticamente o código que ele se encontra em uma classe que deriva de `DbConfiguration`. Você pode usar o `DbConfiguration` classe para realizar tarefas de configuração no código que você faria no caso contrário, o *Web. config* arquivo. Para obter mais informações, consulte [EntityFramework configuração baseada em código](https://msdn.microsoft.com/data/jj680699).
3. Na *StudentController.cs*, adicione uma `using` instrução para `System.Data.Entity.Infrastructure`.

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]
4. Alterar todos os as `catch` bloqueia que capturam `DataException` exceções para que eles catch `RetryLimitExceededException` exceções em vez disso. Por exemplo:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs?highlight=1)]

    Você estava usando `DataException` para tentar identificar erros que podem ser transitórios para fornecer uma mensagem amigável "tente novamente". Mas agora que você ativou a em uma política de repetição, os probabilidade de serem transitórias somente os erros serão já ter sido tentados e falhados várias vezes e a exceção real retornada será encapsulada no `RetryLimitExceededException` exceção.

Para obter mais informações, consulte [resiliência de Conexão do Entity Framework / lógica de repetição](https://msdn.microsoft.com/data/dn456835).

## <a name="enable-command-interception"></a>Habilitar a interceptação de comando

Agora que você ativou a em uma política de repetição, como você testar para verificar se ele está funcionando conforme o esperado? Não é tão fácil forçar um erro transitório ocorrer, especialmente quando você estiver executando localmente, e ele seria especialmente difícil de integrar erros transitórios reais em um teste de unidade automatizados. Para testar o recurso de resiliência de conexão, você precisa de uma maneira de interceptar consultas do Entity Framework envia para o SQL Server e substituir a resposta do SQL Server com um tipo de exceção que é normalmente transitório.

Você também pode usar a interceptação de consulta para implementar a prática recomendada para aplicativos em nuvem: [efetuar a latência e o êxito ou a falha de todas as chamadas a serviços externos](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry.md#log) como os serviços de banco de dados. EF6 fornece um [dedicados a API de log](https://msdn.microsoft.com/data/dn469464) que pode tornar mais fácil de fazer o registro em log, mas essa seção do tutorial, você aprenderá como usar o Entity Framework [recurso de interceptação](https://msdn.microsoft.com/data/dn469464) diretamente, tanto para registro em log e para simular erros transitórios.

### <a name="create-a-logging-interface-and-class"></a>Criar uma interface de registro em log e a classe

Um [práticas recomendadas para registro em log](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry.md#log) é fazer isso usando uma interface em vez de embutir chamadas para Diagnostics. Trace ou uma classe de registro em log. Isso facilita alterar seu mecanismo de log mais tarde, se você precisar fazer isso. Portanto, nesta seção você criará a interface de registro em log e uma classe para implementar o proprietário. / p >

1. Crie uma pasta no projeto e denomine *registro em log*.
2. No *registro em log* pasta, crie um arquivo de classe chamado *ILogger.cs*e substitua o código de modelo pelo código a seguir:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

    A interface fornece três níveis de rastreamento para indicar a importância relativa de logs e uma projetada para fornecer informações de latência para chamadas de serviço externo, como consultas de banco de dados. Os métodos de registro em log têm sobrecargas que permitem que você passe uma exceção. Isso é para que informações de exceção, incluindo as exceções interna e o rastreamento de pilha confiável são registradas pela classe que implementa a interface, em vez de depender de ter feito isso em cada chamada de método de registro em log em todo o aplicativo.

    Os métodos TraceApi permitem rastrear a latência de cada chamada para um serviço externo, como o banco de dados SQL.
3. No *registro em log* pasta, crie um arquivo de classe chamado *Logger.cs*e substitua o código de modelo pelo código a seguir:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

    A implementação usa System. Diagnostics para fazer o rastreamento. Isso é um recurso interno do .NET que torna mais fácil gerar e usar informações de rastreamento. Há muitas "ouvintes" você pode usar com o rastreamento de System. Diagnostics, para gravar logs em arquivos, por exemplo, ou para gravá-los no armazenamento de blob no Azure. Veja algumas das opções e links para outros recursos para obter mais informações, no [solução de problemas de Sites da Web do Azure no Visual Studio](https://docs.microsoft.com/azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio). Para este tutorial, você verá apenas os logs no Visual Studio **saída** janela.

    Em um aplicativo de produção você talvez queira considerar a pacotes de rastreamento que não seja de System. Diagnostics e a interface ILogger torna relativamente fácil alternar para um mecanismo de rastreamento diferente se você optar por fazer isso.

### <a name="create-interceptor-classes"></a>Criar classes de interceptor

Em seguida, você criará as classes que o Entity Framework irá chamar toda vez que ele vai enviar uma consulta para o banco de dados, um para simular erros transitórios e para fazer o registro em log. Essas classes de interceptor devem derivar de `DbCommandInterceptor` classe. Neles você escrever substituições de métodos que são chamadas automaticamente quando a consulta está prestes a ser executada. Esses métodos, você pode examinar ou a consulta que está sendo enviada ao banco de dados de log, e você pode alterar a consulta antes de serem enviado ao banco de dados ou retornar algo para o Entity Framework por conta própria sem nem mesmo a passagem da consulta ao banco de dados.

1. Para criar a classe de interceptador que registrará em log todas as consultas SQL que é enviada para o banco de dados, crie um arquivo de classe chamado *SchoolInterceptorLogging.cs* na *DAL* pasta e substitua o modelo de código com o código a seguir:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

    Para comandos ou consultas com êxito, esse código grava um log de informações com as informações de latência. Para exceções, ele cria um log de erros.
2. Para criar a classe de interceptador que gerarão erros transitórios fictícios ao digitar "Throw" no **pesquisa** caixa, crie um arquivo de classe chamado *SchoolInterceptorTransientErrors.cs* na *DAL* pasta e substitua o código de modelo com o código a seguir:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

    Esse código substitui apenas o `ReaderExecuting` método, que é chamado para consultas que podem retornar várias linhas de dados. Se você quiser verificar a resiliência de conexão para outros tipos de consultas, você também pode substituir a `NonQueryExecuting` e `ScalarExecuting` métodos, como o interceptador de registro em log faz.

    Quando você executa a página de alunos e insira "Throw" como a cadeia de caracteres de pesquisa, esse código cria uma exceção de banco de dados SQL fictícia do número de erro 20, um tipo conhecido por ser normalmente transitórios. Outros números de erro reconhecidos atualmente como transitório são 64, 233, 10053, 10054, 10060, 10928, 10929, 40197, 40501 e 40613, mas eles estão sujeitos a alterações nas novas versões do banco de dados SQL.

    O código retorna a exceção para o Entity Framework em vez de executar a consulta e passar os resultados da consulta back. A exceção transitória é retornada quatro vezes e, em seguida, o código será revertido para o procedimento normal de passar a consulta ao banco de dados.

    Porque tudo o que está conectado, você poderá ver que o Entity Framework tenta executar a consulta quatro vezes antes de finalmente ter êxito e a única diferença no aplicativo é que ele leva mais tempo para renderizar uma página com os resultados da consulta.

    O número de vezes que o Entity Framework tentará é configurável; o código especifica quatro vezes, porque esse é o valor padrão para a política de execução do banco de dados SQL. Se você alterar a política de execução, você precisava também alterar o código que especifica quantas vezes os erros transitórios são gerados. Você também pode alterar o código para gerar mais exceções para que o Entity Framework gerará o `RetryLimitExceededException` exceção.

    O valor inserido na caixa de pesquisa será em `command.Parameters[0]` e `command.Parameters[1]` (um é usado para o nome e uma para o último nome). Quando o valor "% Throw %" é encontrado, "Throw" é substituída desses parâmetros por "um" para que alguns alunos serão encontrados e retornados.

    Isso é apenas uma maneira conveniente para testar a resiliência de conexão com base na alteração alguma entrada para o aplicativo de interface do usuário. Você também pode escrever código que gera erros transitórios para todas as consultas ou atualizações, conforme explicado posteriormente os comentários sobre o *DbInterception.Add* método.
3. Na *global. asax*, adicione o seguinte `using` instruções:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]
4. Adicione as linhas realçadas para o `Application_Start` método:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs?highlight=7-8)]

    Essas linhas de código são o que faz com que seu código de interceptador ser executada quando o Entity Framework envia consultas ao banco de dados. Observe que, como você criou classes de interceptador separado para a simulação de erro transitório e registro em log, você pode independentemente habilitar e desabilitá-los.

    Você pode adicionar interceptores usando o `DbInterception.Add` método em qualquer lugar no seu código; ele não precisa estar no `Application_Start` método. Outra opção é colocar esse código na classe DbConfiguration que você criou anteriormente para configurar a política de execução.

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs?highlight=6-7)]

    Onde quer que você coloque esse código, tome cuidado para executar não `DbInterception.Add` para o interceptador mesmo mais de uma vez, ou você obterá instâncias adicionais do interceptor. Por exemplo, se você adicionar o interceptador de registro em log duas vezes, você verá dois logs para todas as consultas SQL.

    Os interceptores são executados na ordem de registro (a ordem na qual o `DbInterception.Add` método é chamado). A ordem pode ser relevante, dependendo do que você está fazendo no interceptador. Por exemplo, um interceptador pode alterar o comando SQL que ele obtém no `CommandText` propriedade. Se ele altera o comando SQL, o interceptador próxima receberá o comando SQL alterados, não o comando SQL original.

    Você escreveu o código de erro transitório de simulação de uma maneira que permite que você causar erros transitórios, inserindo um valor diferente na interface do usuário. Como alternativa, você poderia escrever o código de interceptador para sempre gerar a sequência de exceções transitórias sem verificar se há um valor de parâmetro específico. Em seguida, você pode adicionar o interceptador somente quando você deseja gerar erros transitórios. Se você fizer isso, no entanto, não adicione o interceptador até após a inicialização do banco de dados foi concluída. Em outras palavras, fazer a operação de pelo menos um banco de dados como uma consulta em um dos seus conjuntos de entidades antes de começar gerando erros transitórios. O Entity Framework executa várias consultas durante a inicialização do banco de dados, e não são executadas em uma transação, portanto, erros durante a inicialização pode fazer com que o contexto para entrar em um estado inconsistente.

## <a name="test-the-new-configuration"></a>A nova configuração de teste

1. Pressione **F5** para executar o aplicativo no modo de depuração e, em seguida, clique no **alunos** guia.
2. Examinar o Visual Studio **saída** janela para ver a saída de rastreamento. Você talvez precise rolar para cima, alguns erros de JavaScript para obter os logs gravados pelo seu agente de log anteriores.

    Observe que você pode ver as consultas SQL reais enviadas para o banco de dados. Você verá algumas consultas inicias e comandos que o Entity Framework para começar, verificando a versão do banco de dados e tabela de histórico de migração (você saberá mais sobre as migrações no próximo tutorial). E você verá uma consulta para a paginação, descubra quantos alunos existem, e, por fim, você pode ver a consulta que obtém os dados de alunos.

    ![Registro em log para consulta normal](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)
3. No **alunos** página, insira "Throw" como a cadeia de caracteres de pesquisa e clique em **pesquisa**.

    ![Gerar a cadeia de caracteres de pesquisa](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

    Você observará que o navegador parece parar de responder por alguns segundos, enquanto o Entity Framework está tentando novamente a consulta várias vezes. A primeira nova tentativa acontecerá muito rapidamente, em seguida, a espera antes de aumentos antes de cada repetição adicional. Esse processo de espera mais antes de é chamada a cada repetição *retirada exponencial*.

    Quando a página é exibida, mostrando os alunos, que tenham "um" em seus nomes, observe a janela de saída, e você verá que a mesma consulta foi tentada cinco vezes, os primeiros quatro vezes retornando transitório exceções. Para cada erro transitório, você verá o que você escreve ao gerar o erro transitório no log de `SchoolInterceptorTransientErrors` classe ("retornar erro transitório do comando...") e você verá o log gravado quando `SchoolInterceptorLogging` obtém a exceção.

    ![Saída de log, mostrando as repetições](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

    Uma vez que você inseriu uma cadeia de caracteres de pesquisa, a consulta que retorna dados de alunos é parametrizada:

    [!code-sql[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.sql)]

    Você não está fazendo o valor dos parâmetros, mas você pode fazer isso. Se você quiser ver os valores de parâmetro, você pode escrever o código de registro em log para obter valores de parâmetro de `Parameters` propriedade do `DbCommand` objeto obter nos métodos de interceptor.

    Observe que você não pode repetir esse teste, a menos que você interrompa o aplicativo e reiniciá-lo. Se você quiser ser capaz de testar a resiliência de conexão várias vezes em uma única execução do aplicativo, você pode escrever código para redefinir o contador de erro em `SchoolInterceptorTransientErrors`.
4. Para ver a diferença a estratégia de execução (política de repetição) faz, comentário a `SetExecutionStrategy` de linha no *SchoolConfiguration.cs*, execute novamente a página de alunos no modo de depuração e pesquisar novamente "Throw".

    Desta vez o depurador para na primeira exceção gerada imediatamente quando ele tenta executar a consulta na primeira vez.

    ![Exceção fictícia](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)
5. Remova os *SetExecutionStrategy* linha *SchoolConfiguration.cs*.

## <a name="additional-resources"></a>Recursos adicionais

Links para outros recursos do Entity Framework pode ser encontrado na [acesso a dados ASP.NET – recursos recomendados](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Resiliência de conexão habilitada
> * Interceptação de comando habilitado
> * Testado a nova configuração

Avance para o próximo artigo para saber mais sobre migrações do Code First e a implantação do Azure.
> [!div class="nextstepaction"]
> [Code First migrations e implantação do Azure](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md)

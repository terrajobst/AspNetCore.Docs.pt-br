---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/strategies-for-database-development-and-deployment-vb
title: Estratégias de desenvolvimento de banco de dados e de implantação (VB) | Microsoft Docs
author: rick-anderson
description: Ao implantar um aplicativo controlado por dados pela primeira vez você cegamente pode copiar o banco de dados no ambiente de desenvolvimento para o ambiente de produção. B....
ms.author: riande
ms.date: 04/23/2009
ms.assetid: 07b8905d-78ac-4252-97fb-8675b3fb0bbf
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/strategies-for-database-development-and-deployment-vb
msc.type: authoredcontent
ms.openlocfilehash: e07da4b5263ac3c6db19c375ca00cbcf87e0b35a
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "41825003"
---
<a name="strategies-for-database-development-and-deployment-vb"></a>Estratégias de desenvolvimento de banco de dados e de implantação (VB)
====================
por [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Baixar PDF](http://download.microsoft.com/download/C/3/9/C391A649-B357-4A7B-BAA4-48C96871FEA6/aspnet_tutorial10_DBDevel_vb.pdf)

> Ao implantar um aplicativo controlado por dados pela primeira vez você cegamente pode copiar o banco de dados no ambiente de desenvolvimento para o ambiente de produção. Mas, executando uma cópia oculta nas próximas implantações substituirá todos os dados inseridos no banco de dados de produção. Em vez disso, implantar um banco de dados envolve a aplicação das alterações feitas no banco de dados de desenvolvimento, desde a última implantação no banco de dados de produção. Este tutorial examina esses desafios e oferece várias estratégias para ajudá-lo com o chronicling e aplicando as alterações feitas no banco de dados desde a última implantação.


## <a name="introduction"></a>Introdução

Conforme discutido nos tutoriais anteriores, implantando um aplicativo ASP.NET envolve copiando o conteúdo pertinente do ambiente de desenvolvimento para o ambiente de produção. Implantação não é um evento único, mas em vez disso, algo que acontece sempre que uma nova versão do software é liberada ou quando bugs ou preocupações de segurança foram identificadas e resolvidas. Ao copiar as páginas do ASP.NET, imagens, arquivos JavaScript e outros esses arquivos ao ambiente de produção, que você não precisa se preocupar com como esses arquivos foram alterados desde a última implantação. Cegamente você pode copiar o arquivo para a produção, substituindo o conteúdo existente. Infelizmente, essa simplicidade não se estende para implantar o banco de dados.

Ao implantar um aplicativo controlado por dados pela primeira vez você cegamente pode copiar o banco de dados no ambiente de desenvolvimento para o ambiente de produção. Mas, executando uma cópia oculta nas próximas implantações substituirá todos os dados inseridos no banco de dados de produção. Em vez disso, a implantação de um banco de dados envolve a aplicação de *alterações* feitas no banco de dados de desenvolvimento, desde a última implantação no banco de dados de produção. Este tutorial examina esses desafios e oferece várias estratégias para ajudá-lo com o chronicling e aplicando as alterações feitas no banco de dados desde a última implantação.

## <a name="the-challenges-of-deploying-a-database"></a>Os desafios de implantação de um banco de dados

Antes de um aplicativo orientado a dados foi implantado pela primeira vez, há apenas um banco de dados, ou seja, o banco de dados no ambiente de desenvolvimento, por isso, ao implantar um aplicativo controlado por dados pela primeira vez você cegamente pode copiar o banco de dados a ambiente de desenvolvimento para o ambiente de produção. Mas quando o aplicativo tiver sido implantado há duas cópias do banco de dados: um no desenvolvimento e outro na produção.

Entre as implantações de bancos de dados de desenvolvimento e produção podem se tornar fora de sincronia. Enquanto o esquema de s de banco de dados de produção permanecerá inalterado, o esquema de s de banco de dados de desenvolvimento pode alterar como os novos recursos são adicionados. Você pode adicionar ou remover colunas, tabelas, exibições ou procedimentos armazenados. Também pode haver dados importantes que são adicionados ao banco de dados de desenvolvimento. Muitos aplicativos controlados por dados incluem tabelas de pesquisa, preenchidas com dados embutidos, específicos do aplicativo que não são editáveis pelo usuário. Por exemplo, um site de leilão pode ter uma lista suspensa com as opções que descrevem a condição do item que está sendo auctioned: New, como o novo, BOM e Fair. Em vez de embutir essas opções diretamente na lista suspensa é geralmente melhor colocá-los em uma tabela de banco de dados. Se, durante o desenvolvimento, uma nova condição chamada fraco é adicionada à tabela, em seguida, ao implantar o aplicativo esse mesmo registro precisa ser adicionada à tabela de pesquisa no banco de dados de produção.

Idealmente, implantar o banco de dados envolveria copiando o banco de dados de desenvolvimento para produção. Mas tenha em mente que, depois que você implantou o aplicativo e retomada de desenvolvimento, o banco de dados de produção está sendo preenchido com dados reais de usuários reais. Portanto, se você fosse simplesmente copiar o banco de dados de desenvolvimento em produção na próxima implantação substituir o banco de dados de produção e perder seus dados existentes. O resultado líquido é que a implantação de banco de dados se resume a aplicar as alterações feitas no banco de dados de desenvolvimento, desde a última implantação.

Como implantar um banco de dados envolve a aplicação das alterações no esquema e, possivelmente, os dados desde a última implantação, um histórico das alterações deve ser mantido (ou determinado no momento da implantação) para que essas alterações podem ser aplicadas em produção. Há uma variedade de técnicas para gerenciar e aplicar as alterações ao modelo de dados.

### <a name="defining-the-baseline"></a>Definindo a linha de base

Para manter as alterações em seu banco de dados do aplicativo s, você precisa ter algum estado inicial, uma linha de base para o qual as alterações são aplicadas ao. Por um lado, o estado inicial pode ser um banco de dados vazio com nenhum tabelas, exibições ou procedimentos armazenados. Uma linha de base resulta em um log de alterações grandes, porque ele deve incluir a criação de todas as tabelas do banco de dados s, exibições e procedimentos armazenados, juntamente com todas as alterações feitas após a implantação inicial. Na outra extremidade do espectro, você pode definir a linha de base como a versão do banco de dados que é inicialmente implantado no ambiente de produção. Essa opção resulta em um log de alterações muito menor, porque ela inclui apenas as alterações feitas no banco de dados após a primeira implantação. Essa é a abordagem que eu prefiro. E, certamente você pode escolher um meio mais a abordagem de estrada, definindo a linha de base como algum ponto entre a criação inicial do banco de dados e quando o banco de dados é implantado pela primeira vez.

Depois que você tiver escolhido uma linha de base considere gerar um script SQL que pode ser executado para recriar a versão de linha de base. Esse script torna possível recriar rapidamente a versão de linha de base do banco de dados. Essa funcionalidade é especialmente útil em projetos grandes, onde pode haver vários desenvolvedores trabalhando no projeto ou ambientes adicionais, como teste ou preparo, que cada precisa de sua própria cópia do banco de dados.

Há uma variedade de ferramentas à sua disposição para gerar um script SQL da versão de linha de base. Do SSMS SQL Server Management Studio () pode clicar duas vezes no banco de dados, vá para o submenu de tarefas e escolha a opção de gerar Scripts. Isso inicia o Assistente de Script, você pode instruir o para gerar um arquivo que contém os comandos SQL para criar objetos de s de seu banco de dados. Outra opção é o Assistente de publicação de banco de dados, que pode gerar os comandos SQL para criar não apenas o esquema de banco de dados, mas também os dados nas tabelas de banco de dados. O Assistente de publicação de banco de dados foi examinado em detalhes na *Implantando um banco de dados* tutorial. Independentemente de qual ferramenta usar, no final das contas, você deve ter um arquivo de script que você pode usar para recriar a versão de linha de base do seu banco de dados, caso seja necessário surgir.

## <a name="documenting-the-database-changes-in-prose"></a>Documentar as alterações de banco de dados em um texto

É a maneira mais simples para manter um log de alterações para o modelo de dados durante a fase de desenvolvimento registrar as alterações em um texto. Por exemplo, se durante o desenvolvimento de um aplicativo já implantado, você adiciona uma nova coluna para o `Employees` da tabela, remover uma coluna do `Orders` da tabela e adicionar uma nova tabela (`ProductCategories`), você manteria um arquivo de texto ou documento do Microsoft Word com o histórico do seguinte:

<a id="0.8_table01"></a>


| **Data da alteração** | **Alterar detalhes** |
| --- | --- |
| 2009-02-03: | Coluna adicionada `DepartmentID` (`int`, NOT NULL) para o `Employees` tabela. Adicionada uma restrição de chave estrangeira da `Departments.DepartmentID` para `Employees.DepartmentID`. |
| 2009-02-05: | A coluna removida `TotalWeight` do `Orders` tabela. Associado a dados já capturados no `OrderDetails` registros. |
| 2009-02-12: | Criado o `ProductCategories` tabela. Há três colunas: `ProductCategoryID` (`int`, `IDENTITY`, `NOT NULL`), `CategoryName` (`nvarchar(50)`, `NOT NULL`), e `Active` (`bit`, `NOT NULL`). Adicionada uma restrição primary key para `ProductCategoryID`e o valor padrão de 1 a `Active`. |


Há várias desvantagens nessa abordagem. Para começar, não há nenhuma esperança para automação. A qualquer momento essas alterações precisam ser aplicados a um banco de dados -, como quando o aplicativo é implantado – um desenvolvedor deve implementar manualmente cada alteração, um de cada vez. Além disso, se você precisar reconstruir uma versão específica do banco de dados da linha de base usando o log de alterações, fazendo assim levará mais tempo à medida que aumenta o tamanho do log. Outra desvantagem para esse método é que a clareza e o nível de detalhes de cada entrada de log de alteração é da esquerda para a pessoa que registra a alteração. Em uma equipe com vários desenvolvedores alguns podem tornar as entradas mais detalhadas, mais legíveis ou mais precisas que outros. Além disso, os erros de digitação e outros erros de entrada de dados relacionados a humanos são possíveis.

O principal benefício de documentar as alterações de banco de dados em um texto é a simplicidade. Você não a familiaridade do t necessidade com a sintaxe SQL para criar e alterar objetos de banco de dados. Em vez disso, você pode registrar as alterações em um texto e implementá-los por meio da interface gráfica do usuário do SQL Server Management Studio s.

Manter seu log de alterações em um texto, sem dúvida, não é muito sofisticado e ganhas t trabalho bem com certos projetos, como aqueles que são grandes no escopo, têm alterações frequentes ao modelo de dados ou envolver vários desenvolvedores. Mas eu já vi essa abordagem funciona muito bem em pequenos, com projetos que têm somente as alterações ocasionais ao modelo de dados e onde o desenvolvedor solo não tem um plano de fundo forte na sintaxe SQL criando e alterando objetos de banco de dados.

> [!NOTE]
> Enquanto as informações no log de alterações são, tecnicamente, só é necessário até o momento da implantação, é recomendável manter um histórico das alterações. Mas, em vez de manter um único, crescendo cada arquivo de log de alteração, considere ter um arquivo de log de alteração diferente para cada versão do banco de dados. Normalmente você desejará versão do banco de dados cada vez que ele é implantado. Ao manter um log dos logs de alteração pode, a partir da linha de base, recriar qualquer versão do banco de dados executando os scripts de log de alteração a partir da versão 1 e continuando até que a versão que você precise recriar.


## <a name="recording-the-sql-change-statements"></a>Gravando as instruções de alteração SQL

A principal desvantagem de manter o log de alterações em um texto é a falta de automação. Idealmente, implementar as alterações de banco de dados no banco de dados de produção no momento da implantação seria tão fácil quanto clicar em um botão para executar um script em vez de ter que executar manualmente uma lista de instruções. Essa automação é possível, mantendo um log de alterações que contém os comandos SQL usados para alterar o modelo de dados.

A sintaxe SQL inclui um número de instruções para criar e modificar vários objetos de banco de dados. Por exemplo, o [ *instrução CREATE TABLE*](https://msdn.microsoft.com/library/ms174979.aspx), quando executada, cria uma nova tabela com as restrições e colunas especificadas. O [ *instrução ALTER TABLE* ](https://msdn.microsoft.com/library/ms190273.aspx) modifica uma tabela existente, adicionando, removendo ou modificando suas colunas ou restrições. Também há instruções para criar, modificar e descartar índices, exibições, funções definidas pelo usuário, procedimentos armazenados, gatilhos e outros objetos de banco de dados.

Voltando ao nosso exemplo anterior, da imagem que durante o desenvolvimento de um aplicativo já implantado, você adiciona uma nova coluna para o `Employees` da tabela, remover uma coluna do `Orders` da tabela e adicionar uma nova tabela (`ProductCategories`). Tais ações pode resultar em um arquivo de log de alteração com comandos SQL a seguir:

[!code-sql[Main](strategies-for-database-development-and-deployment-vb/samples/sample1.sql)]

Enviar por push essas alterações no banco de dados de produção no momento da implantação é uma operação de um clique: Abra o SQL Server Management Studio, conecte-se ao banco de dados de produção, abra uma janela nova consulta, cole o conteúdo do log de alterações e clique em executar para executar o script.

## <a name="using-a-comparison-tool-to-synchronize-the-data-models"></a>Usando uma ferramenta de comparação para sincronizar os modelos de dados

Documentar as alterações de banco de dados em um texto é fácil, mas implementar as alterações exige que um desenvolvedor fazer cada alteração no banco de dados de produção um por vez; Documentar os comandos SQL de alteração torna a implementar essas alterações no banco de dados de produção tão fácil e rápido quanto clicar em um botão, mas requer a aprender e dominar as instruções SQL e sintaxe para criação e alteração de objetos de banco de dados. Ferramentas de comparação do banco de dados tirar o melhor de ambas as abordagens e descartar o pior.

Uma ferramenta de comparação do banco de dados compara o esquema ou os dados de dois bancos de dados e exibe um relatório de resumo mostrando a você como diferem os bancos de dados. Em seguida, com o clique de um botão, você pode gerar os comandos SQL para a sincronização de um ou mais objetos de banco de dados. Em resumo, você pode usar uma ferramenta de comparação do banco de dados para comparar o desenvolvimento e comandos de bancos de dados de produção no momento da implantação, gerando um arquivo que contém o SQL que, quando executado, aplicará as alterações no esquema de banco de dados s produção até que ele espelha o esquema de banco de dados s de desenvolvimento.

Há uma variedade de ferramentas de comparação do banco de dados de terceiros oferecidos por fornecedores diferentes. Um exemplo é [ *SQL Compare*](http://www.red-gate.com/products/SQL_Compare/), por [ *Red Gate Software*](http://www.red-gate.com/). Deixe o s percorrer o processo de usar o SQL Compare para comparar e sincronizar esquemas de bancos de dados de desenvolvimento e produção.

> [!NOTE]
> No momento da redação deste artigo, a versão atual do SQL Compare era versão 7.1, com a edição Standard de US $395 de custos. Você pode acompanhar baixando uma avaliação gratuita de 14 dias.


Quando a comparação do SQL inicia a caixa de diálogo de projetos de comparação é aberta, mostrando os projetos de SQL Compare salvos. Crie um novo projeto. Isso inicia o Assistente de configuração de projeto, que solicita informações sobre os bancos de dados a ser comparado (veja a Figura 1). Insira as informações para os bancos de dados de desenvolvimento e produção de ambiente.


[![Comparar o desenvolvimento e os bancos de dados de produção](strategies-for-database-development-and-deployment-vb/_static/image2.jpg)](strategies-for-database-development-and-deployment-vb/_static/image1.jpg)

**Figura 1**: comparar o desenvolvimento e os bancos de dados de produção ([clique para exibir a imagem em tamanho normal](strategies-for-database-development-and-deployment-vb/_static/image3.jpg))


> [!NOTE]
> Se seu banco de dados do ambiente de desenvolvimento é um arquivo de banco de dados do SQL Express Edition no `App_Data` pasta do seu site, você precisará registrar o banco de dados no servidor de banco de dados do SQL Server Express para selecioná-lo na caixa de diálogo mostrada na Figura 1. A maneira mais fácil de fazer isso é abrir o SQL Server Management Studio (SSMS), conecte-se ao servidor de banco de dados SQL Server Express e anexar o banco de dados. Se você não tiver o SSMS instalado em seu computador, você pode baixar e instalar a versão gratuita [ *versão do SQL Server 2008 Management Studio Basic*](https://www.microsoft.com/downloads/details.aspx?FamilyId=7522A683-4CB2-454E-B908-E805E9BD4E28&amp;displaylang=en).


Além de selecionar os bancos de dados a ser comparado, você também pode especificar uma variedade de configurações na guia Opções de comparação. Uma opção que você talvez queira ativar é os "Ignorar restrição e índice nomes". Lembre-se de que o tutorial anterior, adicionamos que o aplicativo de serviços de objetos de banco de dados para os bancos de dados de desenvolvimento e produção. Se você tiver usado o `aspnet_regsql.exe` ferramenta para criar esses objetos no banco de dados de produção, em seguida, você descobrirá que a chave primária e nomes de restrição exclusiva diferem entre os bancos de dados de desenvolvimento e produção. Consequentemente, a comparação do SQL sinalizará todas as tabelas dos serviços de aplicativo como diferentes. Você pode deixar os "Ignorar restrição e índice nomes" desmarcada e sincronizar os nomes de restrição ou instruir a comparação do SQL para ignorar essas diferenças.

Depois de selecionar os bancos de dados compare (e revisar as opções de comparação), clique no botão comparar agora para começar a comparação. Ao longo de alguns segundos, a comparação do SQL examina os esquemas de dois bancos de dados e gera um relatório de diferenças entre eles. Eu ve propositadamente feitas algumas modificações no banco de dados de desenvolvimento para mostrar como tais discrepâncias são indicadas na interface de comparação do SQL. Como mostra a Figura 2, eu ve adicionado um `BirthDate` coluna para o `Authors` tabela, removida o `ISBN` coluna a partir o `Books` da tabela e adicionou uma nova tabela, `Ratings`, que é destinado para permitir que os usuários que visitam a taxa de site os livros revisados.

> [!NOTE]
> As alterações do modelo de dados feitas neste tutorial foram feitas para ilustrar o uso de uma ferramenta de comparação do banco de dados. Você não encontrará essas alterações no banco de dados em tutoriais futuros.


[![Comparação SQL lista as diferenças entre o desenvolvimento e os bancos de dados de produção](strategies-for-database-development-and-deployment-vb/_static/image5.jpg)](strategies-for-database-development-and-deployment-vb/_static/image4.jpg)

**Figura 2**: SQL Compare lista as diferenças entre o desenvolvimento e os bancos de dados de produção ([clique para exibir a imagem em tamanho normal](strategies-for-database-development-and-deployment-vb/_static/image6.jpg))


Comparação do SQL divide os objetos de banco de dados em grupos, rapidamente mostrando a você quais objetos existem em ambos os bancos de dados, mas são diferente, que existem objetos em um banco de dados, mas não a outra e quais objetos são idênticos. Como você pode ver, há dois objetos que existem em ambos os bancos de dados, mas são diferentes: a `Authors` tabela, que tinha uma coluna adicionada, e o `Books` tabela, que tinha um removido. Há um objeto que existe apenas no desenvolvimento banco de dados, ou seja, recém-criado `Ratings` tabela. E há 117 objetos que são idênticos em ambos os bancos de dados.

Selecionando um objeto de banco de dados exibe a janela de diferenças de SQL, que mostra como esses objetos são diferentes. A janela de diferenças de SQL, exibida na parte inferior da Figura 2 destaca que o `Authors` tabela no banco de dados de desenvolvimento tem o `BirthDate` coluna, que não é encontrada no `Authors` tabela no banco de dados de produção.

Depois de revisar as diferenças e selecionar quais objetos você deseja sincronizar, a próxima etapa é gerar comandos SQL necessários para atualizar o esquema de s de banco de dados de produção para coincidir com o banco de dados de desenvolvimento. Isso é feito através do Assistente de sincronização. O Assistente de sincronização confirma que objetos para sincronizar e resume a ação de plano (consulte a Figura 3). Você pode sincronizar os bancos de dados imediatamente ou gerar um script com os comandos SQL que pode ser executado em seu tempo livre.


[![Use o Assistente de sincronização para sincronizar seus esquemas de bancos de dados](strategies-for-database-development-and-deployment-vb/_static/image8.jpg)](strategies-for-database-development-and-deployment-vb/_static/image7.jpg)

**Figura 3**: Use o Assistente de sincronização para sincronizar seus esquemas de bancos de dados ([clique para exibir a imagem em tamanho normal](strategies-for-database-development-and-deployment-vb/_static/image9.jpg))


Ferramentas de comparação do banco de dados, como Red Gate Software s SQL Compare tornar aplicando as alterações no esquema de banco de dados de desenvolvimento para o banco de dados de produção tão fácil quanto apontar e clicar.

> [!NOTE]
> Comparação do SQL compara e sincroniza dois bancos de dados *esquemas*. Infelizmente, ele não comparar e sincronizar os dados nas tabelas de dois bancos de dados. Red Gate Software oferece um produto chamado [ *SQL Data Compare* ](http://www.red-gate.com/products/SQL_Data_Compare/) que compara e sincroniza os dados entre dois bancos de dados, mas ele é um produto separado do SQL Compare e outro US $395 de custos.


## <a name="taking-the-application-offline-during-deployment"></a>Colocar o aplicativo Offline durante a implantação

Como podemos ve visto durante esses tutoriais, a implantação é um processo que envolve várias etapas: copiando as páginas do ASP.NET, páginas mestras, CSS arquivos, JavaScript arquivos, imagens e outro conteúdo necessário do ambiente de desenvolvimento para a produção ambiente; copiar as informações de configuração específicos ao ambiente de produção, se necessário; e aplicando as alterações ao modelo de dados desde a última implantação. Dependendo do número de arquivos e a complexidade de suas alterações de banco de dados, essas etapas podem levar de alguns segundos a vários minutos para ser concluída. Durante a janela de aplicativo da web está em fluxo e os usuários que visitam o site poderão enfrentar erros ou comportamento inesperado.

Ao implantar um site da Web é melhor colocar o aplicativo web "offline" até que a implantação for concluída. Desconectar o aplicativo (e colocá-lo a fazer backup depois que o processo de implantação for concluída) é tão fácil como carregar um arquivo e, em seguida, excluí-lo. Começando com o ASP.NET 2.0, a simples presença de um arquivo chamado `app_offline.htm` em aplicativo s diretório raiz coloca todo o site "offline". Todas as solicitações para uma página ASP.NET no site em questão está respondida automaticamente com o conteúdo do `app_offline.htm` arquivo. Depois que esse arquivo é removido, o aplicativo fica online novamente.

Colocar um aplicativo offline durante a implantação, em seguida, é tão simple quanto carregar um `app_offline.htm` arquivo para o ambiente de produção s diretório raiz de antes de começar o processo de implantação e, em seguida, excluí-lo (ou renomeá-lo para algo diferente de) uma vez implantação foi concluída. Para obter mais informações sobre essa técnica, consulte o artigo de s de John Peterson, levando um [ *ASP.NET aplicativo Offline*](http://www.15seconds.com/issue/061207.htm).

## <a name="summary"></a>Resumo

O principal desafio na implantação de um aplicativo orientado a dados gira em torno de implantar o banco de dados. Porque há duas versões do banco de dados - um no ambiente de desenvolvimento e outro no ambiente de produção esses esquemas de dois bancos de dados podem se tornar fora de sincronia conforme novos recursos são adicionados no desenvolvimento. Novidades mais, porque o banco de dados de produção como sendo populado com dados reais de usuários reais, não é possível substituir o banco de dados de produção com o banco de dados de desenvolvimento modificado como você pode fazer ao implantar os arquivos que compõem o aplicativo (as páginas ASP.NET, arquivos de imagem e assim por diante). Em vez disso, a implantação de um banco de dados envolve Implementando o conjunto exato das alterações feitas no banco de dados de desenvolvimento no banco de dados de produção desde a última implantação.

Este tutorial analisamos três técnicas de manutenção e aplicação de um log de alterações do banco de dados. A abordagem mais simples é registrar as alterações em um texto. Embora essa tática torna a implementação dessas alterações do banco de dados de produção um processo manual, ele não requer conhecimento sobre os comandos SQL para Criando e alterando objetos de banco de dados. Uma abordagem mais sofisticada e outro que é muito mais aceitável em projetos ou projetos maiores com vários desenvolvedores, é registrar as alterações como uma série de comandos SQL. Isso acelera consideravelmente muito distribuindo essas alterações para o banco de dados de destino. O melhor de ambas as abordagens pode ser obtido usando uma ferramenta de comparação do banco de dados, como s Red Gate Software comparação do SQL.

Esse tutorial conclui nosso foco sobre como implantar um aplicativo controlado por dados. O próximo conjunto de tutoriais examina como responder a erros no ambiente de produção. Vamos examinar como exibir uma página de erro amigável em vez disso, em vez da tela amarela de morte. E veremos como registrar em log os detalhes do erro s e como alertar você quando esses erros ocorrem.

Boa programação!

> [!div class="step-by-step"]
> [Anterior](configuring-a-website-that-uses-application-services-vb.md)
> [Próximo](displaying-a-custom-error-page-vb.md)

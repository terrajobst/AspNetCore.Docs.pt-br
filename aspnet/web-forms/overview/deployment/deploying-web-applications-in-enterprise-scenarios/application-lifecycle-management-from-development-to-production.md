---
uid: web-forms/overview/deployment/deploying-web-applications-in-enterprise-scenarios/application-lifecycle-management-from-development-to-production
title: 'Gerenciamento de ciclo de vida do aplicativo: Do desenvolvimento à produção | Microsoft Docs'
author: jrjlee
description: Este tópico ilustra como uma empresa fictícia gerencia a implantação de um aplicativo da web ASP.NET por meio de ambientes de teste, preparação e produção como par...
ms.author: riande
ms.date: 05/04/2012
ms.assetid: f97a1145-6470-4bca-8f15-ccfb25fb903c
msc.legacyurl: /web-forms/overview/deployment/deploying-web-applications-in-enterprise-scenarios/application-lifecycle-management-from-development-to-production
msc.type: authoredcontent
ms.openlocfilehash: 7cb9c949936c3af73d4c904d401c36d4d83f3e18
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "41830434"
---
<a name="application-lifecycle-management-from-development-to-production"></a>Gerenciamento de ciclo de vida do aplicativo: Do desenvolvimento à produção
====================
por [Jason Lee](https://github.com/jrjlee)

[Baixar PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Este tópico ilustra como uma empresa fictícia gerencia a implantação de um aplicativo da web ASP.NET por meio de ambientes de teste, preparação e produção como parte de um processo de desenvolvimento contínuo. Em todo o tópico, são fornecidos links para obter mais informações e instruções passo a passo sobre como executar tarefas específicas.
> 
> Este tópico foi criado para fornecer uma visão geral de alto nível para um [série de tutoriais](deploying-web-applications-in-enterprise-scenarios.md) na implantação da web na empresa. Não se preocupe se você não estiver familiarizado com alguns dos conceitos descritos aqui&#x2014;os tutoriais a seguir fornecem informações detalhadas sobre todas essas tarefas e técnicas.
> 
> > [!NOTE]
> > Para não complicar, este tópico não aborda Atualizando bancos de dados como parte do processo de implantação. No entanto, fazer atualizações incrementais para os recursos de bancos de dados é um requisito de muitos cenários de implantação empresarial, e você pode encontrar diretrizes sobre como fazer isso mais tarde nessa série de tutoriais. Para obter mais informações, consulte [implantar projetos de banco de dados](../web-deployment-in-the-enterprise/deploying-database-projects.md).


## <a name="overview"></a>Visão geral

O processo de implantação ilustrado aqui se baseia no cenário de implantação de Fabrikam, Inc. descrito em [implantação da Web corporativa: Visão geral do cenário](enterprise-web-deployment-scenario-overview.md). Leia a visão geral do cenário antes que você estude neste tópico. Essencialmente, o cenário examina como uma organização gerencia a implantação de um aplicativo web razoavelmente complexo, o [entre em contato com o Gerenciador soluções](../web-deployment-in-the-enterprise/the-contact-manager-solution.md), por meio de várias fases em um ambiente empresarial típico.

Em um alto nível, a solução do Contact Manager passa por essas fases como parte do processo de implantação e desenvolvimento:

1. Um desenvolvedor faz check algum código no Team Foundation Server (TFS) 2010.
2. O TFS compila o código e executa os testes de unidade associados com o projeto de equipe.
3. TFS implanta a solução ao ambiente de teste.
4. A equipe de desenvolvedores verifica e valida a solução no ambiente de teste.
5. O administrador de ambiente de preparo executa uma implantação "what if" para o ambiente de preparo, para estabelecer se a implantação fará com que quaisquer problemas.
6. O administrador de ambiente de preparo executa uma implantação em tempo real para o ambiente de preparo.
7. A solução passar por teste no ambiente de preparo de aceitação do usuário.
8. Os pacotes de implantação da web são importados manualmente no ambiente de produção.

Esses estágios formam parte de um ciclo de desenvolvimento contínuo.

![](application-lifecycle-management-from-development-to-production/_static/image1.png)

Na prática, o processo é um pouco mais complicado do que isso, como você verá quando examinarmos cada estágio em mais detalhes. A Fabrikam, Inc. usa uma abordagem diferente para implantação para cada ambiente de destino.

![](application-lifecycle-management-from-development-to-production/_static/image2.png)

O restante deste tópico examina esses estágios-chave desse ciclo de vida de implantação:

- **Pré-requisitos**: como você precisa configurar sua infraestrutura de servidor antes de colocar sua lógica de implantação em vigor.
- **Desenvolvimento e implantação inicial**: o que você precisa fazer antes de implantar sua solução pela primeira vez.
- **Implantação para teste**: como empacotar e implantar conteúdo para um ambiente de teste automaticamente quando um desenvolvedor faz check-in do novo código.
- **Implantação de preparo**: compilações de como implantar específico para um ambiente de preparo e como executar "what if" implantações para garantir que uma implantação não causará problemas.
- **Implantação na produção**: como importar pacotes da web para um ambiente de produção quando a infraestrutura de rede impede que a implantação remota.

## <a name="prerequisites"></a>Pré-requisitos

A primeira tarefa em qualquer cenário de implantação é garantir que sua infraestrutura de servidor atende aos requisitos de suas ferramentas de implantação e técnicas. Nesse caso, a Fabrikam, Inc. tiver configurado sua infra-estrutura de servidor como este:

- TFS está configurado para incluir uma coleção de projetos de equipe, controladores de compilação e agentes de compilação. Ver [Configurando o Team Foundation Server para a implantação automatizada de Web](../configuring-team-foundation-server-for-web-deployment/configuring-team-foundation-server-for-web-deployment.md) para obter mais informações.
- O ambiente de teste está configurado para aceitar as implantações remotas usando o serviço de agente de implantação da Web (o "agente remoto"), conforme descrito em [cenário: configurar um ambiente de teste para implantação da Web](../configuring-server-environments-for-web-deployment/scenario-configuring-a-test-environment-for-web-deployment.md) e [ Configurar um servidor Web para publicação (agente remoto) de implantação da Web](../configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-remote-agent.md).
- O ambiente de preparo está configurado para aceitar as implantações remotas usando o ponto de extremidade do manipulador de implantação da Web, conforme descrito em [cenário: configurar um ambiente de preparo para a implantação da Web](../configuring-server-environments-for-web-deployment/scenario-configuring-a-staging-environment-for-web-deployment.md) e [configurar um servidor Web Publicação de implantação para a Web (manipulador de implantação da Web)](../configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler.md).
- O ambiente de produção está configurado para permitir que um administrador importar manualmente os pacotes de implantação da web no Internet Information Services (IIS), conforme descrito em [cenário: configurar um ambiente de produção para implantação da Web](../configuring-server-environments-for-web-deployment/scenario-configuring-a-production-environment-for-web-deployment.md) e [configurar um servidor Web para publicação (implantação Offline) de implantação da Web](../configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-offline-deployment.md).

## <a name="initial-development-and-deployment"></a>Desenvolvimento inicial e implantação

Antes da equipe de desenvolvimento Fabrikam, Inc. pode implantar a solução do Contact Manager pela primeira vez, ele precisa executar estas tarefas:

- Crie um novo projeto de equipe no TFS.
- Crie os arquivos de projeto do Microsoft Build Engine (MSBuild) que contêm a lógica de implantação.
- Crie as definições de compilação do TFS que disparam os processos de implantação.

### <a name="create-a-new-team-project"></a>Criar um novo projeto de equipe

- O administrador do TFS, Rob Walters, cria um novo projeto de equipe para o aplicativo, conforme descrito em [criando um projeto de equipe no TFS](../configuring-team-foundation-server-for-web-deployment/creating-a-team-project-in-tfs.md). Em seguida, o desenvolvedor-chefe, Matt Hink, cria uma solução de esqueleto. Ele verifica seus arquivos para o novo projeto de equipe no TFS, conforme descrito em [adicionando conteúdo ao controle do código-fonte](../configuring-team-foundation-server-for-web-deployment/adding-content-to-source-control.md).

### <a name="create-the-deployment-logic"></a>Criar a lógica de implantação

Matt Hink cria vários arquivos personalizados de projeto do MSBuild, usando a abordagem de arquivo de projeto divisão descrita [Noções básicas sobre o arquivo de projeto](../web-deployment-in-the-enterprise/understanding-the-project-file.md). Matt cria:

- Um arquivo de projeto chamado *Publish.proj* que executa o processo de implantação. Esse arquivo contém os destinos do MSBuild que compilar os projetos na solução, criar pacotes da web e implantar os pacotes em um ambiente de servidor de destino.
- Arquivos de projeto específicas do ambiente denominados *Env Dev.proj* e *Env Stage.proj*. Eles contêm configurações que são específicas para o ambiente de teste e o ambiente de preparo, respectivamente, como cadeias de caracteres de conexão, pontos de extremidade de serviço e os detalhes do serviço remoto que receberá o pacote da web. Para obter orientação sobre como escolher as configurações corretas para ambientes de destino específico, consulte [configurar propriedades de implantação para um ambiente de destino](../configuring-server-environments-for-web-deployment/configuring-deployment-properties-for-a-target-environment.md).

Para executar a implantação, um usuário executa o *Publish.proj* de arquivos usando o MSBuild ou o Team Build e especifica o local do arquivo de projeto de ambiente específicas relevantes (*Env Dev.proj* ou *Stage.proj Env*) como um argumento de linha de comando. O *Publish.proj* arquivo, em seguida, importa o arquivo de projeto específicas do ambiente para criar um conjunto completo de instruções para cada ambiente de destino de publicação.

> [!NOTE]
> A forma como esses arquivos de projeto personalizados funcionam é independente do mecanismo usado para invocar o MSBuild. Por exemplo, você pode usar a linha de comando do MSBuild diretamente, conforme descrito em [Noções básicas sobre o arquivo de projeto](../web-deployment-in-the-enterprise/understanding-the-project-file.md). Você pode executar os arquivos de projeto de um arquivo de comando, conforme descrito em [criar e executar um arquivo de comando de implantação](../web-deployment-in-the-enterprise/creating-and-running-a-deployment-command-file.md). Como alternativa, você pode executar os arquivos de projeto de uma definição de compilação no TFS, conforme descrito em [criando uma definição de compilação que dá suporte à implantação](../configuring-team-foundation-server-for-web-deployment/creating-a-build-definition-that-supports-deployment.md).  
> Em cada caso, o resultado final é o mesmo&#x2014;MSBuild executa o arquivo de projeto mesclada e implanta sua solução para o ambiente de destino. Isso proporciona muita flexibilidade em como você disparar o processo de publicação.


Quando ele tiver criado os arquivos de projeto personalizado, Matt adiciona-os para uma pasta de solução e verifica no controle do código-fonte.

### <a name="create-build-definitions"></a>Criar definições de compilação

Como uma tarefa de preparação final, Matt e Rob trabalham juntos para criar três definições de compilação para o novo projeto de equipe:

- **DeployToTest**. Isso compila a solução de Gerenciador de contatos e implanta o ambiente de teste sempre que um check-in ocorre.
- **DeployToStaging**. Isso implanta os recursos de um build anterior especificado para o ambiente de preparo quando um desenvolvedor enfileira a compilação.
- **DeployToStaging-WhatIf**. Isso executa uma implantação "what if" para o ambiente de preparo quando um desenvolvedor enfileira a compilação.

As seções a seguir fornecem mais detalhes sobre cada uma dessas definições de compilação.

## <a name="deployment-to-test"></a>Implantação no teste

A equipe de desenvolvimento Fabrikam, Inc. mantém os ambientes de teste para realizar uma variedade de atividades, como verificação e validação, teste de usabilidade, teste de compatibilidade e teste exploratório ou ad hoc de teste de software.

A equipe de desenvolvimento criou uma definição de compilação no TFS denominado **DeployToTest**. Essa definição de compilação usa um gatilho de integração contínua, o que significa que o processo de compilação é executado sempre que um membro da equipe de desenvolvimento Fabrikam, Inc. realiza um check-in. Quando uma compilação é disparada, a definição de compilação será:

- Compile a solução ContactManager.sln. Por sua vez, isso compila todos os projetos na solução.
- Execute os testes de unidade na estrutura de pasta da solução (se a solução é compilada com êxito).
- Execute os arquivos de projeto personalizado que controlam o processo de implantação (se a solução é compilado com êxito e passa os testes de unidade).

O resultado final é que se a solução é compilado com êxito e aprovado em testes de unidade, os pacotes da web e quaisquer outros recursos de implantação sejam implantados em um ambiente de teste.

![](application-lifecycle-management-from-development-to-production/_static/image3.png)

### <a name="how-does-the-deployment-process-work"></a>Como funciona o processo de implantação?

O **DeployToTest** build definition fornece esses argumentos para o MSBuild:


[!code-console[Main](application-lifecycle-management-from-development-to-production/samples/sample1.cmd)]


O **DeployOnBuild = true** e **DeployTarget 2=pacote** propriedades são usadas quando o Team Build compila projetos dentro da solução. Quando o projeto é um projeto de aplicativo web, essas propriedades instruem o MSBuild para criar um pacote de implantação da web para o projeto. O **TargetEnvPropsFile** propriedade informa o *Publish.proj* onde encontrar o arquivo de projeto específicas do ambiente para importar do arquivo.

> [!NOTE]
> Para obter instruções detalhadas sobre como criar uma definição de compilação como este, consulte [criando uma definição de compilação que dá suporte à implantação](../configuring-team-foundation-server-for-web-deployment/creating-a-build-definition-that-supports-deployment.md).


O *Publish.proj* arquivo contém os destinos que compila cada projeto na solução. No entanto, ele também inclui a lógica condicional que ignora esses destinos de compilação se você estiver executando o arquivo no Team Build. Isso permite que você aproveite a funcionalidade de compilação adicionais que o Team Build oferece, como a capacidade de executar testes de unidade. Se o build da solução ou a unidade de testes falham, o *Publish.proj* o arquivo não será executado e o aplicativo não será implantado.

A lógica condicional é realizada, avaliando os **BuildingInTeamBuild** propriedade. É uma propriedade de MSBuild que é definida automaticamente como **verdadeira** quando você usa o Team Build para compilar seus projetos.

## <a name="deployment-to-staging"></a>Implantação de preparo

Quando uma compilação atende aos requisitos da equipe do desenvolvedor no ambiente de teste, a equipe talvez queira implantar o mesmo build em um ambiente de preparo. Ambientes de preparo geralmente são configurados de acordo com as características do ambiente de produção ou "ativo" como perto quanto possível, por exemplo, em termos de especificações de servidor, sistemas operacionais e software e configuração de rede. Ambientes de preparo geralmente são usadas para teste de carga, teste de aceitação do usuário e revisões internas mais amplas. Compilações são implantadas no ambiente de preparo diretamente do servidor de compilação.

![](application-lifecycle-management-from-development-to-production/_static/image4.png)

As definições de compilação usadas para implantar a solução para o ambiente de preparo **DeployToStaging-WhatIf** e **DeployToStaging**, compartilham as seguintes características:

- Na verdade, eles não criam qualquer coisa. Rob implanta a solução ao ambiente de preparo, ele deseja implantar uma compilação específica, existente que já foi verificada e validada no ambiente de teste. As definições de compilação basta executar os arquivos de projeto personalizado que controlam o processo de implantação.
- Quando Rob dispara uma compilação, ele usa os parâmetros de compilação para especificar qual build contém os recursos que ele deseja implantar a partir do servidor de compilação.
- As definições de compilação não são disparadas automaticamente. Rob enfileira manualmente uma compilação quando ele deseja implantar a solução ao ambiente de preparo.

Este é o processo de alto nível para uma implantação no ambiente de preparo:

1. O administrador de ambiente preparo, Rob Walters, enfileira uma compilação usando o **DeployToStaging-WhatIf** definição de compilação. Rob usa os parâmetros de definição de compilação para especificar qual versão ele quer implantar.
2. O **DeployToStaging-WhatIf** compilar os arquivos de projeto personalizado no modo "what if" de execuções de definição. Isso gera arquivos de log como se Rob estava executando uma implantação em tempo real, mas, na verdade, ele não faz quaisquer alterações no ambiente de destino.
3. Rob examina os arquivos de log para determinar os efeitos da implantação no ambiente de preparo. Em particular, Rob quer verificar o que será adicionado, o que será atualizado e o que será excluído.
4. Se for Rob convencido de que a implantação não faça alterações indesejáveis para recursos existentes ou dados, ele coloca na fila um build usando o **DeployToStaging** definição de compilação.
5. O **DeployToStaging** compilar os arquivos de projeto personalizado de execuções de definição. Eles publicar os recursos de implantação no servidor web principal no ambiente de preparo.
6. O controlador do Web Farm Framework (WFF) sincroniza os servidores web no ambiente de preparo. Isso disponibiliza o aplicativo em todos os servidores web no farm de servidores.

### <a name="how-does-the-deployment-process-work"></a>Como funciona o processo de implantação?

O **DeployToStaging** build definition fornece esses argumentos para o MSBuild:


[!code-console[Main](application-lifecycle-management-from-development-to-production/samples/sample2.cmd)]


O **TargetEnvPropsFile** propriedade informa o *Publish.proj* onde encontrar o arquivo de projeto específicas do ambiente para importar do arquivo. O **OutputRoot** propriedade substitui o valor interno e indica o local da pasta de compilação que contém os recursos que você deseja implantar. Quando Rob enfileira a compilação, ele usa o **parâmetros** tab para fornecer um valor atualizado para o **OutputRoot** propriedade.

![](application-lifecycle-management-from-development-to-production/_static/image5.png)

> [!NOTE]
> Para obter mais informações sobre como criar uma definição de compilação como este, consulte [implantar uma compilação específica](../configuring-team-foundation-server-for-web-deployment/deploying-a-specific-build.md).


O **DeployToStaging-WhatIf** definição de compilação contém a mesma lógica de implantação que o **DeployToStaging** definição de compilação. No entanto, ele inclui o argumento adicional **WhatIf = true**:


[!code-console[Main](application-lifecycle-management-from-development-to-production/samples/sample3.cmd)]


Dentro de *Publish.proj* arquivo, o **WhatIf** propriedade indica que todos os recursos de implantação devem ser publicados no modo "what if". Em outras palavras, os arquivos de log são gerados como se a implantação fui em frente, mas na verdade, nada será alterado no ambiente de destino. Isso lhe permite avaliar o impacto de uma implantação de proposta&#x2014;em particular, o que serão adicionados ao, o que será atualizado, e o que será excluído&#x2014;antes de realmente fazer as alterações.

> [!NOTE]
> Para obter mais informações sobre como configurar implantações "what if", consulte [executando uma implantação "What If"](../advanced-enterprise-web-deployment/performing-a-what-if-deployment.md).


Depois de implantar seu aplicativo para o servidor web principal no ambiente de preparo, o WFF sincronizará automaticamente o aplicativo em todos os servidores no farm de servidores.

> [!NOTE]
> Para obter mais informações sobre como configurar o WFF para sincronizar servidores web, consulte [criar um Farm de servidores com o Web Farm Framework](../configuring-server-environments-for-web-deployment/creating-a-server-farm-with-the-web-farm-framework.md).


## <a name="deployment-to-production"></a>Implantação na produção

Quando um build tiver sido aprovado no ambiente de preparo, a equipe da Fabrikam, Inc. pode publicar o aplicativo no ambiente de produção. O ambiente de produção é onde o aplicativo fica "ativo" e atinge seu público-alvo de usuários finais.

O ambiente de produção está em uma rede de perímetro para a Internet. Isso é isolado da rede interna que contém o servidor de compilação. Manualmente, o administrador de ambiente de produção, Lisa Andrews, deve copiar os pacotes de implantação da web do servidor de compilação e importá-los para o IIS no servidor da web de produção principal.

![](application-lifecycle-management-from-development-to-production/_static/image6.png)

Este é o processo de alto nível para uma implantação no ambiente de produção:

1. A equipe de desenvolvedores aconselha Lisa que uma compilação está pronta para implantação de produção. A equipe aconselha Lisa da localização dos pacotes de implantação da web dentro da pasta de destino no servidor de compilação.
2. Lisa coleta os pacotes da web do servidor de compilação e os copia para o servidor web principal no ambiente de produção.
3. Lisa usa o Gerenciador do IIS para importar e publicar os pacotes da web no servidor web principal.
4. O controlador WFF sincroniza os servidores web no ambiente de produção. Isso disponibiliza o aplicativo em todos os servidores web no farm de servidores.

### <a name="how-does-the-deployment-process-work"></a>Como funciona o processo de implantação?

O Gerenciador do IIS inclui um Assistente de pacote de aplicativo de importação que torna mais fácil publicar pacotes da web em um site do IIS. Para obter instruções sobre como executar este procedimento, consulte [manualmente instalando os pacotes da Web](../web-deployment-in-the-enterprise/manually-installing-web-packages.md).

## <a name="conclusion"></a>Conclusão

Este tópico forneceu uma ilustração do ciclo de vida de implantação para um aplicativo web de escala empresarial típico.

Este tópico faz parte de uma série de tutoriais que fornecem orientação sobre vários aspectos da implantação de aplicativos web. Na prática, há muitas considerações e tarefas adicionais em cada estágio do processo de implantação e não é possível abordá-los todos em um única passo a passo. Para obter mais informações, consulte estes tutoriais:

- [Implantação na empresa Web](../web-deployment-in-the-enterprise/web-deployment-in-the-enterprise.md). Este tutorial fornece uma introdução abrangente às técnicas de implantação da web usando o MSBuild e a ferramenta de implantação da Web de IIS (implantação da Web).
- [Configurar ambientes de servidor para implantação da Web](../configuring-server-environments-for-web-deployment/configuring-server-environments-for-web-deployment.md). Este tutorial fornece orientações sobre como configurar ambientes de servidor do Windows para dar suporte a vários cenários de implantação.
- [Configurando o Team Foundation Server para a implantação de Web automatizada](../configuring-team-foundation-server-for-web-deployment/configuring-team-foundation-server-for-web-deployment.md). Este tutorial fornece orientações sobre como integrar a lógica de implantação em processos de compilação do TFS.
- [Avançadas de implantação da Web corporativa](../advanced-enterprise-web-deployment/advanced-enterprise-web-deployment.md). Este tutorial fornece orientações sobre como atender a alguns dos desafios de implantação mais complexos que as organizações enfrentam.

> [!div class="step-by-step"]
> [Anterior](enterprise-web-deployment-scenario-overview.md)

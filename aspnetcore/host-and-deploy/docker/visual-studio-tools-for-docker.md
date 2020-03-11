---
title: Ferramentas do Visual Studio para Contêineres com ASP.NET Core
author: spboyer
description: Saiba como usar as ferramentas do Visual Studio e o Docker for Windows para colocar um aplicativo ASP.NET Core em um contêiner.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 0e6747a3de220b97cc7a84f9cd42b0da54b57ee9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664062"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a>Ferramentas do Visual Studio para Contêineres com ASP.NET Core

O Visual Studio 2017 e as versões posteriores dão suporte à criação, à depuração e à execução de aplicativos ASP.NET Core em contêineres direcionados ao .NET Core. Há suporte para contêineres do Windows e Linux.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* [Docker para Windows](https://docs.docker.com/docker-for-windows/install/)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a carga de trabalho **de desenvolvimento multiplataforma do .NET Core**

## <a name="installation-and-setup"></a>Instalação e configuração

Para a instalação do Docker, primeiro examine as informações em [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) (Docker para Windows: o que saber antes de instalar). Em seguida, instale o [Docker for Windows](https://docs.docker.com/docker-for-windows/install/).

As **[Unidades Compartilhadas](https://docs.docker.com/docker-for-windows/#shared-drives)** do Docker para Windows devem ser configuradas para dar suporte ao mapeamento do volume e à depuração. Clique com o botão direito do mouse no ícone do Docker na Bandeja do Sistema, selecione **Configurações** e selecione **Unidades Compartilhadas**. Selecione a unidade em que o Docker armazena arquivos. Clique em **Aplicar**.

![Caixa de diálogo para selecionar a unidade C local de compartilhamento para contêineres](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> A versão 15.6 e as versões posteriores do Visual Studio 2017 exibem um aviso quando as **Unidades Compartilhadas** não estão configuradas.

## <a name="add-a-project-to-a-docker-container"></a>Adicionar um projeto a um contêiner do Docker

Para colocar um projeto do ASP.NET Core em contêineres, o projeto deve ser destinado ao .Net Core. Contêineres do Linux e Windows são ambos compatíveis.

Ao adicionar o suporte do Docker a um projeto, escolha um contêiner do Windows ou Linux. O host do Docker deve estar executando o mesmo tipo de contêiner. Para alterar o tipo de contêiner na instância do Docker em execução, clique com o botão direito do mouse no ícone do Docker na Bandeja do Sistema e escolha **Alternar para contêineres do Windows...** ou **Alternar para contêineres do Linux...** .

### <a name="new-app"></a>Novo aplicativo

Ao criar um novo aplicativo com os modelos de projeto do **Aplicativo Web ASP.NET Core**, marque a caixa de seleção **Habilitar Suporte do Docker**:

![Caixa de seleção Habilitar Suporte do Docker](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

Se a estrutura de destino for o .NET Core, a lista suspensa **SO** permitirá a seleção de um tipo de contêiner.

### <a name="existing-app"></a>Aplicativo existente

Para projetos do ASP.NET Core direcionados ao .NET Core, há duas opções para adicionar o suporte do Docker por meio das ferramentas. Abra o projeto no Visual Studio e escolha uma das seguintes opções:

* Selecione **Suporte do Docker** no menu **Projeto**.
* Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Adicionar** > **Suporte do Docker**.

As Ferramentas de Contêiner do Visual Studio não são compatíveis com a adição do Docker a um projeto ASP.NET Core existente direcionado ao .NET Framework.

## <a name="dockerfile-overview"></a>Visão geral do Dockerfile

Um *Dockerfile*, a receita para criar uma imagem final do Docker, é adicionado à raiz do projeto. Consulte [Referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) para compreender seus comandos. Esse *Dockerfile* específico usa um [build de vários estágios](https://docs.docker.com/engine/userguide/eng-image/multistage-build/), com quatro estágios de build nomeados distintos:

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

O *Dockerfile* precedente se baseia na imagem [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/). Essa imagem base inclui o runtime do ASP.NET Core e os pacotes NuGet. Os pacotes são compilados JIT (Just-In-Time) para melhorar o desempenho de inicialização.

Quando a caixa de seleção **Configurar para HTTPS** do novo projeto estiver marcada, o *Dockerfile* exibirá duas portas. Uma porta é usada para o tráfego HTTP; a outra porta é usada para o HTTPS. Se a caixa de seleção não estiver marcada, uma única porta (80) será exposta para o tráfego HTTP.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

O *Dockerfile* precedente se baseia na imagem [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/). Essa imagem base inclui os pacotes NuGet do ASP.NET Core, que são compilados JIT (Just-In-Time) para melhorar o desempenho de inicialização.

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a>Adicionar o suporte de orquestrador de contêineres a um aplicativo

As versões 15.7 ou posteriores do Visual Studio 2017 são compatíveis com o [Docker Compose](https://docs.docker.com/compose/overview/) como a única solução de orquestração de contêineres. Os artefatos do Docker Compose são adicionados por meio da opção **Adicionar** > **Suporte ao Docker**.

As versões 15.8 ou posteriores do Visual Studio 2017 adicionam uma solução de orquestração apenas quando instruído. Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Adicionar** > **Suporte do orquestrador de contêineres**. Duas opções diferentes são oferecidas: [Docker Compose](#docker-compose) e [Service Fabric](#service-fabric).

### <a name="docker-compose"></a>Docker Compose

As Ferramentas de Contêiner do Visual Studio adicionam um projeto *docker-compose* à solução com os seguintes arquivos:

* *Docker-Compose. dcproj* &ndash; o arquivo que representa o projeto. Inclui um elemento `<DockerTargetOS>` que especifica o sistema operacional a ser utilizado.
* *. dockerignore* &ndash; lista os padrões de arquivo e diretório a serem excluídos ao gerar um contexto de compilação.
* *Docker-Compose. yml* &ndash; o arquivo de [Docker Compose](https://docs.docker.com/compose/overview/) base usado para definir a coleção de imagens compiladas e executadas com `docker-compose build` e `docker-compose run`, respectivamente.
* *Docker-Compose. Override. yml* &ndash; um arquivo opcional, lido por Docker Compose, com substituições de configuração para serviços. O Visual Studio executa `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` para mesclar esses arquivos.

O arquivo *docker-compose.yml* faz referência ao nome da imagem que é criada quando o projeto é executado:

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

No exemplo anterior, `image: hellodockertools` gera a imagem `hellodockertools:dev` quando o aplicativo é executado no modo **Depuração**. A imagem `hellodockertools:latest` é gerada quando o aplicativo é executado no modo **Versão**.

Prefixe o nome da imagem com o nome de usuário do [hub do Docker](https://hub.docker.com/) (por exemplo, `dockerhubusername/hellodockertools`) se a imagem for enviada por push para o registro. Como alternativa, altere o nome da imagem para incluir a URL do registro privado (por exemplo, `privateregistry.domain.com/hellodockertools`), dependendo da configuração.

Se você desejar um comportamento diferente com base na configuração de build (por exemplo, Depuração ou Versão), adicione arquivos *docker-compose* específicos para a configuração. Os arquivos precisam ser nomeados de acordo com a configuração de build (por exemplo, *docker-compose.vs.debug.yml* e *docker-compose.vs.release.yml*) e colocado no mesmo local que o arquivo *docker-compose-override.yml*. 

Usando os arquivos de substituição específicos da configuração, é possível especificar definições de configuração diferentes (como variáveis de ambiente ou pontos de entrada) para as configurações de build de Depuração e Versão.

Para Docker Compose exibir uma opção para executar no Visual Studio, o projeto do Docker deve ser o projeto de inicialização.

### <a name="service-fabric"></a>Service Fabric

Além dos [pré-requisitos](#prerequisites) básicos, a solução de orquestração do [Service Fabric](/azure/service-fabric/) exige os seguintes pré-requisitos:

* [SDK do Microsoft Azure Service Fabric](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) versão 2.6 ou posterior
* Carga de trabalho de **Desenvolvimento do Azure** do Visual Studio

O Service Fabric não é compatível com a execução de contêineres do Linux no cluster de desenvolvimento local no Windows. Se o projeto já estiver usando um contêiner do Linux, o Visual Studio pedirá para alternar para os contêineres do Windows.

As Ferramentas de Contêiner do Visual Studio realizam as seguintes tarefas:

* Adiciona um *&lt;project_name&gt;aplicativo* **Service Fabric** projeto de aplicativo à solução.
* Adiciona um *Dockerfile* e um arquivo *.dockerignore* ao projeto ASP.NET Core. Se um *Dockerfile* já existir no projeto do ASP.NET Core, ele já terá sido renomeado para *Dockerfile.original*. Um novo *Dockerfile*, semelhante ao seguinte, foi criado:

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* Adiciona um elemento `<IsServiceFabricServiceProject>` ao arquivo *.csproj* do projeto do ASP.NET Core:

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* Adiciona uma pasta *PackageRoot* ao projeto ASP.NET Core. A pasta inclui o manifesto do serviço e as configurações para o novo serviço.

Para obter mais informações, consulte [Implantar um aplicativo .NET em um contêiner do Windows no Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).

## <a name="debug"></a>Depuração

Selecione **Docker** no menu suspenso de depuração na barra de ferramentas e inicie a depuração do aplicativo. A exibição **Docker** da janela **Saída** mostra as seguintes ações em andamento:

::: moniker range=">= aspnetcore-2.1"

* A marcação *2.1-aspnetcore-runtime* da imagem do tempo de execução *microsoft/dotnet* foi adquirida (se ainda não estiver no cache). A imagem instala os runtimes do ASP.NET Core e do .Net Core e bibliotecas associadas. Ela é otimizada para executar aplicativos do ASP.NET Core em produção.
* A variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida como `Development` dentro do contêiner.
* Duas portas atribuídas dinamicamente são expostas: uma para HTTP e outra para HTTPS. A porta atribuída ao localhost pode ser consultada com o comando `docker ps`.
* O aplicativo é copiado para o contêiner.
* O navegador padrão é iniciado com o depurador anexado ao contêiner, usando a porta atribuída dinamicamente.

A imagem resultante do Docker do aplicativo é marcada como *dev*. A imagem é baseada na marcação *2.1-aspnetcore-runtime* da imagem base *microsoft/dotnet*. Execute o comando `docker images` na janela do PMC **(Console do Gerenciador de Pacotes)** . As imagens no computador são exibidas:

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* A imagem em runtime *microsoft/aspnetcore* é adquirida (se ainda não está no cache).
* A variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida como `Development` dentro do contêiner.
* A porta 80 é exposta e mapeada para uma porta atribuída dinamicamente para o localhost. A porta é determinada pelo host do Docker e pode ser consultada com o comando `docker ps`.
* O aplicativo é copiado para o contêiner.
* O navegador padrão é iniciado com o depurador anexado ao contêiner, usando a porta atribuída dinamicamente.

A imagem resultante do Docker do aplicativo é marcada como *dev*. A imagem é baseada na imagem base *microsoft/aspnetcore*. Execute o comando `docker images` na janela do PMC **(Console do Gerenciador de Pacotes)** . As imagens no computador são exibidas:

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> A imagem *dev* não inclui o conteúdo do aplicativo, pois as configurações de **Depuração** usam a montagem de volume para fornecer uma experiência iterativa. Para enviar uma imagem por push, use a configuração **Versão**.

Execute o comando `docker ps` no PMC. Observe que o aplicativo está em execução usando o contêiner:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>Editar e continuar

As alterações em arquivos estáticos e exibições do Razor são atualizadas automaticamente sem a necessidade de uma etapa de compilação. Faça a alteração, salve e atualize o navegador para exibir a atualização.

As modificações nos arquivos de código exigem a compilação e a reinicialização do Kestrel dentro do contêiner. Depois de fazer a alteração, use `CTRL+F5` para executar o processo e iniciar o aplicativo dentro do contêiner. O contêiner do Docker não é recompilado nem interrompido. Execute o comando `docker ps` no PMC. Observe que o contêiner original ainda está em execução como há 10 minutos:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>Publicar imagens do Docker

Após concluir o ciclo de desenvolvimento e depuração do aplicativo, as Ferramentas de Contêiner do Visual Studio ajudarão você a criar a imagem de produção do aplicativo. Altere a lista suspensa de configuração para **Versão** e compile o aplicativo. O conjunto de ferramentas adquire a imagem de compilação/publicação do hub do Docker (se ainda não estiver no cache). Uma imagem é produzida com a marcação *latest*, que você pode enviar por push para seu registro privado ou para o hub do Docker.

Execute o comando `docker images` no PMC para ver a lista de imagens. Uma saída semelhante à apresentada a seguir será exibida:

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

As imagens `microsoft/aspnetcore-build` e `microsoft/aspnetcore` listadas na saída anterior são substituídas pelas imagens `microsoft/dotnet` com o .Net Core 2.1. Para obter mais informações, consulte [o comunicado de migração de repositórios do Docker](https://github.com/aspnet/Announcements/issues/298).

::: moniker-end

> [!NOTE]
> O comando `docker images` retorna imagens intermediárias com nomes de repositório e marcas identificadas como *\<none>* (não listadas acima). Essas imagens sem nome são produzidas pelo *Dockerfile*de [compilação de vários estágios](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) . Elas melhoram a eficiência da compilação da imagem final &mdash; apenas as camadas necessárias são recompiladas quando ocorrem alterações. Quando você não precisar mais das imagens intermediárias, exclua-as usando o comando [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

Pode haver uma expectativa de que a imagem de produção ou versão seja menor em comparação com a imagem *dev*. Devido ao mapeamento do volume, o depurador e o aplicativo estavam em execução no computador local e não dentro do contêiner. A *última* imagem empacotou o código do aplicativo necessário para executar o aplicativo em um computador host. Portanto, o delta é o tamanho do código do aplicativo.

## <a name="additional-resources"></a>Recursos adicionais

* [Desenvolvimento de contêiner com o Visual Studio](/visualstudio/containers)
* [Azure Service Fabric: prepare seu ambiente de desenvolvimento](/azure/service-fabric/service-fabric-get-started)
* [Implantar um aplicativo .NET em um contêiner do Windows no Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [Solucionar problemas de desenvolvimento do Visual Studio com o Docker](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [Repositório do GitHub e Ferramentas do Visual Studio para Contêineres](https://github.com/Microsoft/DockerTools)

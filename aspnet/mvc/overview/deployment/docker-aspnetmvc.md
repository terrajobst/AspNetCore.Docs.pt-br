---
uid: mvc/overview/deployment/docker
title: Migrando aplicativos ASP.NET MVC para contêineres do Windows
description: Saiba como selecionar um aplicativo ASP.NET MVC existente e executá-lo em um contêiner do Docker do Windows
keywords: Windows Containers,Docker,ASP.NET MVC
author: BillWagner
ms.author: wiwagn
ms.date: 12/14/2018
ms.assetid: c9f1d52c-b4bd-4b5d-b7f9-8f9ceaf778c4
ms.openlocfilehash: ef184f4256c20e2a66de8fd2d4f8e67f07d9a086
ms.sourcegitcommit: 6548c19f345850ee22b50f7ef9fca732895d9e08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53425153"
---
# <a name="migrating-aspnet-mvc-applications-to-windows-containers"></a>Migrando aplicativos ASP.NET MVC para contêineres do Windows

Executar um aplicativo existente baseado no .NET Framework em um contêiner do Windows não requer alterações ao seu aplicativo. Para executar seu aplicativo em um contêiner do Windows, crie uma imagem de Docker contendo seu aplicativo e inicie o contêiner. Este tópico explica como selecionar um [aplicativo ASP.NET MVC](http://www.asp.net/mvc) existente e implantá-lo em um contêiner do Windows.

Você começa com um aplicativo ASP.NET MVC existente e cria os ativos publicados usando o Visual Studio. Você usa o Docker para criar a imagem que contém e executa o seu aplicativo. Você navegará para o site em execução em um contêiner do Windows e verificará se que o aplicativo está funcionando.

Este artigo pressupõe uma compreensão básica do Docker. Saiba mais sobre o Docker lendo a [Visão Geral do Docker](https://docs.docker.com/engine/understanding-docker/).

O aplicativo que você executará em um contêiner é um site simples que responde a perguntas aleatoriamente. Esse aplicativo é um aplicativo MVC básico sem autenticação ou armazenamento de banco de dados; ele permite que você se concentre em mover a camada da Web para um contêiner. Os tópicos futuros mostrarão como mover e gerenciar o armazenamento persistente em aplicativos em contêineres.

Mover seu aplicativo envolve estas etapas:

1. [Criar uma tarefa de publicação para compilar os ativos para uma imagem.](#publish-script)
1. [Criar uma imagem do Docker que executará o aplicativo.](#build-the-image)
1. [Iniciar um contêiner do Docker que execute sua imagem.](#start-a-container)
1. [Verificar o aplicativo usando o navegador.](#verify-in-the-browser)

O [aplicativo finalizado](https://github.com/dotnet/samples/tree/master/framework/docker/MVCRandomAnswerGenerator) está no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

A máquina de desenvolvimento deve ter o seguinte software:

- [Atualização de aniversário do Windows 10](https://www.microsoft.com/software-download/windows10/) (ou superior) ou [Windows Server 2016](https://www.microsoft.com/cloud-platform/windows-server) (ou superior)
- [Docker para Windows](https://docs.docker.com/docker-for-windows/) – versão Stable 1.13.0 ou 1.12 Beta 26 (ou versões mais recentes)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)

> [!IMPORTANT]
> Se você estiver usando o Windows Server 2016, siga as instruções para [Implantação do Host do Contêiner – Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/deployment/deployment).

Depois de instalar e iniciar o Docker, clique com botão direito no ícone de bandeja e selecione **alternar para contêineres do Windows**. Isso é necessário para executar imagens do Docker com base no Windows. Este comando demora alguns segundos para ser executado:

![Contêiner do Windows][windows-container]

## <a name="publish-script"></a>Script de publicação

Colete todos os ativos que você precisa carregar em uma imagem do Docker em um único lugar. Você pode usar o comando **Publicar** do Visual Studio para criar um perfil de publicação para seu aplicativo. Esse perfil colocará todos os ativos em uma árvore de diretório que você copiará para sua imagem de destino posteriormente neste tutorial.

**Etapas de publicação**

1. Clique com o botão direito do mouse no projeto Web no Visual Studio e selecione **Publicar**.
1. Clique no **botão Perfil personalizado** e selecione **Sistema de Arquivo** como o método.
1. Selecione o diretório. Por convenção, o exemplo baixado usa `bin\Release\PublishOutput`.

![Conexão da publicação][publish-connection]

Abra a seção **Opções de Publicação de Arquivo** da guia **Configurações**. Selecione **Pré-compilar durante a publicação**. Essa otimização significa que você compilará as exibições no contêiner do Docker, está copiando as exibições pré-compiladas.

![Configurações de publicação][publish-settings]

Clique em **Publicar** e o Visual Studio copiará todos os ativos necessários para a pasta de destino.

## <a name="build-the-image"></a>Criar a imagem

Criar um novo arquivo chamado *Dockerfile* para definir a imagem do Docker. *Dockerfile* contém instruções para criar a imagem final e inclui quaisquer nomes de imagem base, componentes necessários, o aplicativo que você deseja executar e outras imagens de configuração. *Dockerfile* é a entrada para o `docker build` comando que cria a imagem.

Para este exercício, você criará uma imagem baseada na `microsoft/aspnet` imagem localizada em [Hub do Docker](https://hub.docker.com/r/microsoft/aspnet/).
A imagem base, `microsoft/aspnet`, é uma imagem do Windows Server. Ele contém o Windows Server Core, IIS e ASP.NET 4.7.2. Quando você executar essa imagem em seu contêiner, ela iniciará automaticamente o IIS e sites instalados.

O Dockerfile que cria a imagem tem esta aparência:

```console
# The `FROM` instruction specifies the base image. You are
# extending the `microsoft/aspnet` image.

FROM microsoft/aspnet

# The final instruction copies the site you published earlier into the container.
COPY ./bin/Release/PublishOutput/ /inetpub/wwwroot
```

Não há nenhum comando `ENTRYPOINT` neste Dockerfile. Você não precisa de um. Ao executar o Windows Server com o IIS, o processo do IIS é o ponto de entrada, que é configurado para iniciar a imagem base do aspnet.

Execute o comando de compilação do Docker para criar a imagem que executa seu aplicativo ASP.NET. Para fazer isso, abra uma janela do PowerShell no diretório do projeto e digite o seguinte comando no diretório da solução:

```console
docker build -t mvcrandomanswers .
```

Esse comando criará a nova imagem usando as instruções no seu Dockerfile de nomenclatura (-t marcação) a imagem como mvcrandomanswers. Isso pode incluir efetuar o pull da imagem base do [Hub do Docker](http://hub.docker.com) e, em seguida, adicionar seu aplicativo àquela imagem.

Depois que o comando for concluído, você poderá executar o comando `docker images` para ver informações sobre a nova imagem:

```console
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
mvcrandomanswers              latest              86838648aab6        2 minutes ago       10.1 GB
```

A ID da IMAGEM será diferente em seu computador. Agora, vamos executar o aplicativo.

## <a name="start-a-container"></a>Iniciar um contêiner

Inicie um contêiner executando o seguinte comando `docker run`:

```console
docker run -d --name randomanswers mvcrandomanswers
```

O argumento `-d` diz ao Docker para iniciar a imagem no modo desconectado. Isso significa que a imagem do Docker é executada desconectada do shell atual.

Em muitos exemplos de docker, você poderá ver -p para mapear as portas de contêiner e o host. A imagem padrão do aspnet já tiver configurado o contêiner para escutar na porta 80 e expô-lo.

O `--name randomanswers` fornece um nome para o contêiner em execução. Você pode usar esse nome em vez da ID do contêiner na maioria dos comandos.

O `mvcrandomanswers` é o nome da imagem a ser iniciada.

## <a name="verify-in-the-browser"></a>Verificar no navegador

Depois que o contêiner for iniciado, conecte-se para o contêiner em execução usando `http://localhost` no exemplo mostrado. Digite a URL no navegador e você deverá ver o site em execução.

> [!NOTE]
> Alguns softwares de proxy ou VPN podem impedir que você navegue para seu site.
> Você pode desabilitá-los temporariamente para se certificar de que seu contêiner está funcionando.

O diretório de exemplo no GitHub contém um [script do PowerShell](https://github.com/dotnet/samples/blob/master/framework/docker/MVCRandomAnswerGenerator/run.ps1) que executa esses comandos para você. Abra uma janela do PowerShell, altere o diretório para o diretório da solução e digite:

```console
./run.ps1
```

O comando acima cria a imagem, exibe a lista de imagens no seu computador e inicia um contêiner.

Para parar o seu contêiner, emita um comando `docker stop`:

```console
docker stop randomanswers
```

Para remover o contêiner, emita um comando `docker rm`:

```console
docker rm randomanswers
```

[windows-container]: media/aspnetmvc/SwitchContainer.png "Alternar para o contêiner do Windows"
[publish-connection]: media/aspnetmvc/PublishConnection.png "Publicar no sistema de arquivos"
[publish-settings]: media/aspnetmvc/PublishSettings.png "Configurações de publicação"

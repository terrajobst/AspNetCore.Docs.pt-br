---
title: Ferramentas do Visual Studio para Docker com ASP.NET Core
author: spboyer
description: Saiba como usar as ferramentas do Visual Studio 2017 e o Docker para o Windows para colocar um aplicativo ASP.NET Core em um contêiner.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 42f8071eadabba3eb8cb738be1720f4c6195808c
ms.sourcegitcommit: 375e9a67f5e1f7b0faaa056b4b46294cc70f55b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50207232"
---
# <a name="visual-studio-tools-for-docker-with-aspnet-core"></a><span data-ttu-id="a3c23-103">Ferramentas do Visual Studio para Docker com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a3c23-103">Visual Studio Tools for Docker with ASP.NET Core</span></span>

<span data-ttu-id="a3c23-104">O Visual Studio 2017 é compatível com a criação, depuração e execução de aplicativos do ASP.NET Core em contêineres direcionados ao .Net Core.</span><span class="sxs-lookup"><span data-stu-id="a3c23-104">Visual Studio 2017 supports building, debugging, and running containerized ASP.NET Core apps targeting .NET Core.</span></span> <span data-ttu-id="a3c23-105">Contêineres do Windows e do Linux são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="a3c23-105">Both Windows and Linux containers are supported.</span></span>

<span data-ttu-id="a3c23-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a3c23-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a3c23-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="a3c23-107">Prerequisites</span></span>

* [<span data-ttu-id="a3c23-108">Docker para Windows</span><span class="sxs-lookup"><span data-stu-id="a3c23-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)
* <span data-ttu-id="a3c23-109">O [Visual Studio 2017](https://www.visualstudio.com/) com a carga de trabalho **Desenvolvimento de multiplataforma do .NET Core**</span><span class="sxs-lookup"><span data-stu-id="a3c23-109">[Visual Studio 2017](https://www.visualstudio.com/) with the **.NET Core cross-platform development** workload</span></span>

## <a name="installation-and-setup"></a><span data-ttu-id="a3c23-110">Instalação e configuração</span><span class="sxs-lookup"><span data-stu-id="a3c23-110">Installation and setup</span></span>

<span data-ttu-id="a3c23-111">Para a instalação do Docker, primeiro examine as informações em [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) (Docker para Windows: o que saber antes de instalar).</span><span class="sxs-lookup"><span data-stu-id="a3c23-111">For Docker installation, first review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span></span> <span data-ttu-id="a3c23-112">Em seguida, instale o [Docker for Windows](https://docs.docker.com/docker-for-windows/install/).</span><span class="sxs-lookup"><span data-stu-id="a3c23-112">Next, install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="a3c23-113">As **[Unidades Compartilhadas](https://docs.docker.com/docker-for-windows/#shared-drives)** do Docker para Windows devem ser configuradas para dar suporte ao mapeamento do volume e à depuração.</span><span class="sxs-lookup"><span data-stu-id="a3c23-113">**[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows must be configured to support volume mapping and debugging.</span></span> <span data-ttu-id="a3c23-114">Clique com o botão direito do mouse no ícone do Docker na Bandeja do Sistema, selecione **Configurações** e selecione **Unidades Compartilhadas**.</span><span class="sxs-lookup"><span data-stu-id="a3c23-114">Right-click the System Tray's Docker icon, select **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="a3c23-115">Selecione a unidade em que o Docker armazena arquivos.</span><span class="sxs-lookup"><span data-stu-id="a3c23-115">Select the drive where Docker stores files.</span></span> <span data-ttu-id="a3c23-116">Clique em **Aplicar**.</span><span class="sxs-lookup"><span data-stu-id="a3c23-116">Click **Apply**.</span></span>

![Caixa de diálogo para selecionar a unidade C local de compartilhamento para contêineres](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> <span data-ttu-id="a3c23-118">A versão 15.6 e as versões posteriores do Visual Studio 2017 exibem um aviso quando as **Unidades Compartilhadas** não estão configuradas.</span><span class="sxs-lookup"><span data-stu-id="a3c23-118">Visual Studio 2017 versions 15.6 and later prompt when **Shared Drives** aren't configured.</span></span>

## <a name="add-a-project-to-a-docker-container"></a><span data-ttu-id="a3c23-119">Adicionar um projeto a um contêiner do Docker</span><span class="sxs-lookup"><span data-stu-id="a3c23-119">Add a project to a Docker container</span></span>

<span data-ttu-id="a3c23-120">Para colocar um projeto do ASP.NET Core em contêineres, o projeto deve ser destinado ao .Net Core.</span><span class="sxs-lookup"><span data-stu-id="a3c23-120">To containerize an ASP.NET Core project, the project must target .NET Core.</span></span> <span data-ttu-id="a3c23-121">Contêineres do Linux e Windows são ambos compatíveis.</span><span class="sxs-lookup"><span data-stu-id="a3c23-121">Both Linux and Windows containers are supported.</span></span>

<span data-ttu-id="a3c23-122">Ao adicionar o suporte do Docker a um projeto, escolha um contêiner do Windows ou Linux.</span><span class="sxs-lookup"><span data-stu-id="a3c23-122">When adding Docker support to a project, choose either a Windows or a Linux container.</span></span> <span data-ttu-id="a3c23-123">O host do Docker deve estar executando o mesmo tipo de contêiner.</span><span class="sxs-lookup"><span data-stu-id="a3c23-123">The Docker host must be running the same container type.</span></span> <span data-ttu-id="a3c23-124">Para alterar o tipo de contêiner na instância do Docker em execução, clique com o botão direito do mouse no ícone do Docker na Bandeja do Sistema e escolha **Alternar para contêineres do Windows...** ou **Alternar para contêineres do Linux...**.</span><span class="sxs-lookup"><span data-stu-id="a3c23-124">To change the container type in the running Docker instance, right-click the System Tray's Docker icon and choose **Switch to Windows containers...** or **Switch to Linux containers...**.</span></span>

### <a name="new-app"></a><span data-ttu-id="a3c23-125">Novo aplicativo</span><span class="sxs-lookup"><span data-stu-id="a3c23-125">New app</span></span>

<span data-ttu-id="a3c23-126">Ao criar um novo aplicativo com os modelos de projeto do **Aplicativo Web ASP.NET Core**, marque a caixa de seleção **Habilitar Suporte do Docker**:</span><span class="sxs-lookup"><span data-stu-id="a3c23-126">When creating a new app with the **ASP.NET Core Web Application** project templates, select the **Enable Docker Support** check box:</span></span>

![Caixa de seleção Habilitar Suporte do Docker](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

<span data-ttu-id="a3c23-128">Se a estrutura de destino for o .NET Core, a lista suspensa **SO** permitirá a seleção de um tipo de contêiner.</span><span class="sxs-lookup"><span data-stu-id="a3c23-128">If the target framework is .NET Core, the **OS** drop-down allows for the selection of a container type.</span></span>

### <a name="existing-app"></a><span data-ttu-id="a3c23-129">Aplicativo existente</span><span class="sxs-lookup"><span data-stu-id="a3c23-129">Existing app</span></span>

<span data-ttu-id="a3c23-130">Para projetos do ASP.NET Core direcionados ao .NET Core, há duas opções para adicionar o suporte do Docker por meio das ferramentas.</span><span class="sxs-lookup"><span data-stu-id="a3c23-130">For ASP.NET Core projects targeting .NET Core, there are two options for adding Docker support via the tooling.</span></span> <span data-ttu-id="a3c23-131">Abra o projeto no Visual Studio e escolha uma das seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="a3c23-131">Open the project in Visual Studio, and choose one of the following options:</span></span>

* <span data-ttu-id="a3c23-132">Selecione **Suporte do Docker** no menu **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="a3c23-132">Select **Docker Support** from the **Project** menu.</span></span>
* <span data-ttu-id="a3c23-133">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Adicionar** > **Suporte do Docker**.</span><span class="sxs-lookup"><span data-stu-id="a3c23-133">Right-click the project in **Solution Explorer** and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="a3c23-134">As Ferramentas do Visual Studio para Docker não dão suporte à adição do Docker a um projeto ASP.NET Core existente direcionado ao .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a3c23-134">The Visual Studio Tools for Docker don't support adding Docker to an existing ASP.NET Core project targeting .NET Framework.</span></span>

## <a name="dockerfile-overview"></a><span data-ttu-id="a3c23-135">Visão geral do Dockerfile</span><span class="sxs-lookup"><span data-stu-id="a3c23-135">Dockerfile overview</span></span>

<span data-ttu-id="a3c23-136">Um *Dockerfile*, a receita para criar uma imagem final do Docker, é adicionado à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="a3c23-136">A *Dockerfile*, the recipe for creating a final Docker image, is added to the project root.</span></span> <span data-ttu-id="a3c23-137">Consulte [Referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) para compreender seus comandos.</span><span class="sxs-lookup"><span data-stu-id="a3c23-137">Refer to [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) for an understanding of the commands within it.</span></span> <span data-ttu-id="a3c23-138">Esse *Dockerfile* específico usa um [build de vários estágios](https://docs.docker.com/engine/userguide/eng-image/multistage-build/), com quatro estágios de build nomeados distintos:</span><span class="sxs-lookup"><span data-stu-id="a3c23-138">This particular *Dockerfile* uses a [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) with four distinct, named build stages:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

<span data-ttu-id="a3c23-139">O *Dockerfile* precedente se baseia na imagem [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/).</span><span class="sxs-lookup"><span data-stu-id="a3c23-139">The preceding *Dockerfile* is based on the [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) image.</span></span> <span data-ttu-id="a3c23-140">Essa imagem base inclui o tempo de execução do ASP.NET Core e os pacotes NuGet.</span><span class="sxs-lookup"><span data-stu-id="a3c23-140">This base image includes the ASP.NET Core runtime and NuGet packages.</span></span> <span data-ttu-id="a3c23-141">Os pacotes são compilados JIT (Just-In-Time) para melhorar o desempenho de inicialização.</span><span class="sxs-lookup"><span data-stu-id="a3c23-141">The packages are just-in-time (JIT) compiled to improve startup performance.</span></span>

<span data-ttu-id="a3c23-142">Quando a caixa de seleção **Configurar para HTTPS** do novo projeto estiver marcada, o *Dockerfile* exibirá duas portas.</span><span class="sxs-lookup"><span data-stu-id="a3c23-142">When the new project dialog's **Configure for HTTPS** check box is checked, the *Dockerfile* exposes two ports.</span></span> <span data-ttu-id="a3c23-143">Uma porta é usada para o tráfego HTTP; a outra porta é usada para o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a3c23-143">One port is used for HTTP traffic; the other port is used for HTTPS.</span></span> <span data-ttu-id="a3c23-144">Se a caixa de seleção não estiver marcada, uma única porta (80) será exposta para o tráfego HTTP.</span><span class="sxs-lookup"><span data-stu-id="a3c23-144">If the check box isn't checked, a single port (80) is exposed for HTTP traffic.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

<span data-ttu-id="a3c23-145">O *Dockerfile* precedente se baseia na imagem [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/).</span><span class="sxs-lookup"><span data-stu-id="a3c23-145">The preceding *Dockerfile* is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) image.</span></span> <span data-ttu-id="a3c23-146">Essa imagem base inclui os pacotes NuGet do ASP.NET Core, que são compilados JIT (Just-In-Time) para melhorar o desempenho de inicialização.</span><span class="sxs-lookup"><span data-stu-id="a3c23-146">This base image includes the ASP.NET Core NuGet packages, which are just-in-time (JIT) compiled to improve startup performance.</span></span>

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a><span data-ttu-id="a3c23-147">Adicionar o suporte de orquestrador de contêineres a um aplicativo</span><span class="sxs-lookup"><span data-stu-id="a3c23-147">Add container orchestrator support to an app</span></span>

<span data-ttu-id="a3c23-148">As versões 15.7 ou posteriores do Visual Studio 2017 são compatíveis com o [Docker Compose](https://docs.docker.com/compose/overview/) como a única solução de orquestração de contêineres.</span><span class="sxs-lookup"><span data-stu-id="a3c23-148">Visual Studio 2017 versions 15.7 or earlier support [Docker Compose](https://docs.docker.com/compose/overview/) as the sole container orchestration solution.</span></span> <span data-ttu-id="a3c23-149">Os artefatos do Docker Compose são adicionados por meio da opção **Adicionar** > **Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="a3c23-149">The Docker Compose artifacts are added via **Add** > **Docker Support**.</span></span>

<span data-ttu-id="a3c23-150">As versões 15.8 ou posteriores do Visual Studio 2017 adicionam uma solução de orquestração apenas quando instruído.</span><span class="sxs-lookup"><span data-stu-id="a3c23-150">Visual Studio 2017 versions 15.8 or later add an orchestration solution only when instructed.</span></span> <span data-ttu-id="a3c23-151">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Adicionar** > **Suporte do orquestrador de contêineres**.</span><span class="sxs-lookup"><span data-stu-id="a3c23-151">Right-click the project in **Solution Explorer** and select **Add** > **Container Orchestrator Support**.</span></span> <span data-ttu-id="a3c23-152">Duas opções diferentes são oferecidas: [Docker Compose](#docker-compose) e [Service Fabric](#service-fabric).</span><span class="sxs-lookup"><span data-stu-id="a3c23-152">Two different choices are offered: [Docker Compose](#docker-compose) and [Service Fabric](#service-fabric).</span></span>

### <a name="docker-compose"></a><span data-ttu-id="a3c23-153">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="a3c23-153">Docker Compose</span></span>

<span data-ttu-id="a3c23-154">As Ferramentas do Visual Studio para Docker adicionam um projeto *docker-compose* à solução com os seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="a3c23-154">The Visual Studio Tools for Docker add a *docker-compose* project to the solution with the following files:</span></span>

* <span data-ttu-id="a3c23-155">*docker-compose.dcproj* &ndash; O arquivo que representa o projeto.</span><span class="sxs-lookup"><span data-stu-id="a3c23-155">*docker-compose.dcproj* &ndash; The file representing the project.</span></span> <span data-ttu-id="a3c23-156">Inclui um elemento `<DockerTargetOS>` que especifica o sistema operacional a ser utilizado.</span><span class="sxs-lookup"><span data-stu-id="a3c23-156">Includes a `<DockerTargetOS>` element specifying the OS to be used.</span></span>
* <span data-ttu-id="a3c23-157">*.dockerignore* &ndash; Lista os padrões de arquivo e diretório a serem excluídos ao gerar um contexto de build.</span><span class="sxs-lookup"><span data-stu-id="a3c23-157">*.dockerignore* &ndash; Lists the file and directory patterns to exclude when generating a build context.</span></span>
* <span data-ttu-id="a3c23-158">*docker-compose.yml* &ndash; O arquivo base do [Docker Compose](https://docs.docker.com/compose/overview/) usado para definir a coleção de imagens compiladas e executadas com o `docker-compose build` e `docker-compose run`, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="a3c23-158">*docker-compose.yml* &ndash; The base [Docker Compose](https://docs.docker.com/compose/overview/) file used to define the collection of images built and run with `docker-compose build` and `docker-compose run`, respectively.</span></span>
* <span data-ttu-id="a3c23-159">*docker-compose.override.yml* &ndash; Um arquivo opcional, lido pelo Docker Compose, com substituições de configuração para os serviços.</span><span class="sxs-lookup"><span data-stu-id="a3c23-159">*docker-compose.override.yml* &ndash; An optional file, read by Docker Compose, with configuration overrides for services.</span></span> <span data-ttu-id="a3c23-160">O Visual Studio executa `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` para mesclar esses arquivos.</span><span class="sxs-lookup"><span data-stu-id="a3c23-160">Visual Studio executes `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` to merge these files.</span></span>

<span data-ttu-id="a3c23-161">O arquivo *docker-compose.yml* faz referência ao nome da imagem que é criada quando o projeto é executado:</span><span class="sxs-lookup"><span data-stu-id="a3c23-161">The *docker-compose.yml* file references the name of the image that's created when the project runs:</span></span>

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

<span data-ttu-id="a3c23-162">No exemplo anterior, `image: hellodockertools` gera a imagem `hellodockertools:dev` quando o aplicativo é executado no modo **Depuração**.</span><span class="sxs-lookup"><span data-stu-id="a3c23-162">In the preceding example, `image: hellodockertools` generates the image `hellodockertools:dev` when the app runs in **Debug** mode.</span></span> <span data-ttu-id="a3c23-163">A imagem `hellodockertools:latest` é gerada quando o aplicativo é executado no modo **Versão**.</span><span class="sxs-lookup"><span data-stu-id="a3c23-163">The `hellodockertools:latest` image is generated when the app runs in **Release** mode.</span></span>

<span data-ttu-id="a3c23-164">Prefixe o nome da imagem com o nome de usuário do [hub do Docker](https://hub.docker.com/) (por exemplo, `dockerhubusername/hellodockertools`) se a imagem for enviada por push para o registro.</span><span class="sxs-lookup"><span data-stu-id="a3c23-164">Prefix the image name with the [Docker Hub](https://hub.docker.com/) username (for example, `dockerhubusername/hellodockertools`) if the image is pushed to the registry.</span></span> <span data-ttu-id="a3c23-165">Como alternativa, altere o nome da imagem para incluir a URL do registro privado (por exemplo, `privateregistry.domain.com/hellodockertools`), dependendo da configuração.</span><span class="sxs-lookup"><span data-stu-id="a3c23-165">Alternatively, change the image name to include the private registry URL (for example, `privateregistry.domain.com/hellodockertools`) depending on the configuration.</span></span>

<span data-ttu-id="a3c23-166">Se você desejar um comportamento diferente com base na configuração de build (por exemplo, Depuração ou Versão), adicione arquivos *docker-compose* específicos para a configuração.</span><span class="sxs-lookup"><span data-stu-id="a3c23-166">If you want different behavior based on the build configuration (for example, Debug or Release), add configuration-specific *docker-compose* files.</span></span> <span data-ttu-id="a3c23-167">Os arquivos precisam ser nomeados de acordo com a configuração de build (por exemplo, *docker-compose.vs.debug.yml* e *docker-compose.vs.release.yml*) e colocado no mesmo local que o arquivo *docker-compose-override.yml*.</span><span class="sxs-lookup"><span data-stu-id="a3c23-167">The files should be named according to the build configuration (for example, *docker-compose.vs.debug.yml* and *docker-compose.vs.release.yml*) and placed in the same location as the *docker-compose-override.yml* file.</span></span> 

<span data-ttu-id="a3c23-168">Usando os arquivos de substituição específicos da configuração, é possível especificar definições de configuração diferentes (como variáveis de ambiente ou pontos de entrada) para as configurações de build de Depuração e Versão.</span><span class="sxs-lookup"><span data-stu-id="a3c23-168">Using the configuration-specific override files, you can specify different configuration settings (such as environment variables or entry points) for Debug and Release build configurations.</span></span>

### <a name="service-fabric"></a><span data-ttu-id="a3c23-169">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="a3c23-169">Service Fabric</span></span>

<span data-ttu-id="a3c23-170">Além dos [pré-requisitos](#prerequisites) básicos, a solução de orquestração do [Service Fabric](/azure/service-fabric/) exige os seguintes pré-requisitos:</span><span class="sxs-lookup"><span data-stu-id="a3c23-170">In addition to the base [Prerequisites](#prerequisites), the [Service Fabric](/azure/service-fabric/) orchestration solution demands the following prerequisites:</span></span>

* <span data-ttu-id="a3c23-171">[SDK do Microsoft Azure Service Fabric](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) versão 2.6 ou posterior</span><span class="sxs-lookup"><span data-stu-id="a3c23-171">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 or later</span></span>
* <span data-ttu-id="a3c23-172">Carga de trabalho de **desenvolvimento do Azure** no Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="a3c23-172">Visual Studio 2017's **Azure Development** workload</span></span>

<span data-ttu-id="a3c23-173">O Service Fabric não é compatível com a execução de contêineres do Linux no cluster de desenvolvimento local no Windows.</span><span class="sxs-lookup"><span data-stu-id="a3c23-173">Service Fabric doesn't support running Linux containers in the local development cluster on Windows.</span></span> <span data-ttu-id="a3c23-174">Se o projeto já estiver usando um contêiner do Linux, o Visual Studio pedirá para alternar para os contêineres do Windows.</span><span class="sxs-lookup"><span data-stu-id="a3c23-174">If the project is already using a Linux container, Visual Studio prompts to switch to Windows containers.</span></span>

<span data-ttu-id="a3c23-175">As Ferramentas do Visual Studio para Docker realizam as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="a3c23-175">The Visual Studio Tools for Docker do the following tasks:</span></span>

* <span data-ttu-id="a3c23-176">Adiciona um projeto *&lt;project_name&gt;do* **Aplicativo do Service Fabric** à solução.</span><span class="sxs-lookup"><span data-stu-id="a3c23-176">Adds a *&lt;project_name&gt;Application* **Service Fabric Application** project to the solution.</span></span>
* <span data-ttu-id="a3c23-177">Adiciona um *Dockerfile* e um arquivo *.dockerignore* ao projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a3c23-177">Adds a *Dockerfile* and a *.dockerignore* file to the ASP.NET Core project.</span></span> <span data-ttu-id="a3c23-178">Se um *Dockerfile* já existir no projeto do ASP.NET Core, ele já terá sido renomeado para *Dockerfile.original*.</span><span class="sxs-lookup"><span data-stu-id="a3c23-178">If a *Dockerfile* already exists in the ASP.NET Core project, it's renamed to *Dockerfile.original*.</span></span> <span data-ttu-id="a3c23-179">Um novo *Dockerfile*, semelhante ao seguinte, foi criado:</span><span class="sxs-lookup"><span data-stu-id="a3c23-179">A new *Dockerfile*, similar to the following, is created:</span></span>

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* <span data-ttu-id="a3c23-180">Adiciona um elemento `<IsServiceFabricServiceProject>` ao arquivo *.csproj* do projeto do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a3c23-180">Adds an `<IsServiceFabricServiceProject>` element to the ASP.NET Core project's *.csproj* file:</span></span>

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* <span data-ttu-id="a3c23-181">Adiciona uma pasta *PackageRoot* ao projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a3c23-181">Adds a *PackageRoot* folder to the ASP.NET Core project.</span></span> <span data-ttu-id="a3c23-182">A pasta inclui o manifesto do serviço e as configurações para o novo serviço.</span><span class="sxs-lookup"><span data-stu-id="a3c23-182">The folder includes the service manifest and settings for the new service.</span></span>

<span data-ttu-id="a3c23-183">Para obter mais informações, consulte [Implantar um aplicativo .NET em um contêiner do Windows no Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span><span class="sxs-lookup"><span data-stu-id="a3c23-183">For more information, see [Deploy a .NET app in a Windows container to Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span></span>

## <a name="debug"></a><span data-ttu-id="a3c23-184">Depurar</span><span class="sxs-lookup"><span data-stu-id="a3c23-184">Debug</span></span>

<span data-ttu-id="a3c23-185">Selecione **Docker** no menu suspenso de depuração na barra de ferramentas e inicie a depuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3c23-185">Select **Docker** from the debug drop-down in the toolbar, and start debugging the app.</span></span> <span data-ttu-id="a3c23-186">A exibição **Docker** da janela **Saída** mostra as seguintes ações em andamento:</span><span class="sxs-lookup"><span data-stu-id="a3c23-186">The **Docker** view of the **Output** window shows the following actions taking place:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="a3c23-187">A marcação *2.1-aspnetcore-runtime* da imagem do tempo de execução *microsoft/dotnet* foi adquirida (se ainda não estiver no cache).</span><span class="sxs-lookup"><span data-stu-id="a3c23-187">The *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* runtime image is acquired (if not already in the cache).</span></span> <span data-ttu-id="a3c23-188">A imagem instala os tempos de execução do ASP.NET Core e do .Net Core e bibliotecas associadas.</span><span class="sxs-lookup"><span data-stu-id="a3c23-188">The image installs the ASP.NET Core and .NET Core runtimes and associated libraries.</span></span> <span data-ttu-id="a3c23-189">Ela é otimizada para executar aplicativos do ASP.NET Core em produção.</span><span class="sxs-lookup"><span data-stu-id="a3c23-189">It's optimized for running ASP.NET Core apps in production.</span></span>
* <span data-ttu-id="a3c23-190">A variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida como `Development` dentro do contêiner.</span><span class="sxs-lookup"><span data-stu-id="a3c23-190">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="a3c23-191">Duas portas atribuídas dinamicamente são expostas: uma para HTTP e outra para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a3c23-191">Two dynamically assigned ports are exposed: one for HTTP and one for HTTPS.</span></span> <span data-ttu-id="a3c23-192">A porta atribuída ao localhost pode ser consultada com o comando `docker ps`.</span><span class="sxs-lookup"><span data-stu-id="a3c23-192">The port assigned to localhost can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="a3c23-193">O aplicativo é copiado para o contêiner.</span><span class="sxs-lookup"><span data-stu-id="a3c23-193">The app is copied to the container.</span></span>
* <span data-ttu-id="a3c23-194">O navegador padrão é iniciado com o depurador anexado ao contêiner, usando a porta atribuída dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="a3c23-194">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="a3c23-195">A imagem resultante do Docker do aplicativo é marcada como *dev*.</span><span class="sxs-lookup"><span data-stu-id="a3c23-195">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="a3c23-196">A imagem é baseada na marcação *2.1-aspnetcore-runtime* da imagem base *microsoft/dotnet*.</span><span class="sxs-lookup"><span data-stu-id="a3c23-196">The image is based on the *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* base image.</span></span> <span data-ttu-id="a3c23-197">Execute o comando `docker images` na janela do PMC **(Console do Gerenciador de Pacotes)**.</span><span class="sxs-lookup"><span data-stu-id="a3c23-197">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="a3c23-198">As imagens no computador são exibidas:</span><span class="sxs-lookup"><span data-stu-id="a3c23-198">The images on the machine are displayed:</span></span>

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* <span data-ttu-id="a3c23-199">A imagem em tempo de execução *microsoft/aspnetcore* é adquirida (se ainda não está no cache).</span><span class="sxs-lookup"><span data-stu-id="a3c23-199">The *microsoft/aspnetcore* runtime image is acquired (if not already in the cache).</span></span>
* <span data-ttu-id="a3c23-200">A variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida como `Development` dentro do contêiner.</span><span class="sxs-lookup"><span data-stu-id="a3c23-200">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="a3c23-201">A porta 80 é exposta e mapeada para uma porta atribuída dinamicamente para o localhost.</span><span class="sxs-lookup"><span data-stu-id="a3c23-201">Port 80 is exposed and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="a3c23-202">A porta é determinada pelo host do Docker e pode ser consultada com o comando `docker ps`.</span><span class="sxs-lookup"><span data-stu-id="a3c23-202">The port is determined by the Docker host and can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="a3c23-203">O aplicativo é copiado para o contêiner.</span><span class="sxs-lookup"><span data-stu-id="a3c23-203">The app is copied to the container.</span></span>
* <span data-ttu-id="a3c23-204">O navegador padrão é iniciado com o depurador anexado ao contêiner, usando a porta atribuída dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="a3c23-204">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="a3c23-205">A imagem resultante do Docker do aplicativo é marcada como *dev*.</span><span class="sxs-lookup"><span data-stu-id="a3c23-205">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="a3c23-206">A imagem é baseada na imagem base *microsoft/aspnetcore*.</span><span class="sxs-lookup"><span data-stu-id="a3c23-206">The image is based on the *microsoft/aspnetcore* base image.</span></span> <span data-ttu-id="a3c23-207">Execute o comando `docker images` na janela do PMC **(Console do Gerenciador de Pacotes)**.</span><span class="sxs-lookup"><span data-stu-id="a3c23-207">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="a3c23-208">As imagens no computador são exibidas:</span><span class="sxs-lookup"><span data-stu-id="a3c23-208">The images on the machine are displayed:</span></span>

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a3c23-209">A imagem *dev* não inclui o conteúdo do aplicativo, pois as configurações de **Depuração** usam a montagem de volume para fornecer uma experiência iterativa.</span><span class="sxs-lookup"><span data-stu-id="a3c23-209">The *dev* image lacks the app contents, as **Debug** configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="a3c23-210">Para enviar uma imagem por push, use a configuração **Versão**.</span><span class="sxs-lookup"><span data-stu-id="a3c23-210">To push an image, use the **Release** configuration.</span></span>

<span data-ttu-id="a3c23-211">Execute o comando `docker ps` no PMC.</span><span class="sxs-lookup"><span data-stu-id="a3c23-211">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="a3c23-212">Observe que o aplicativo está em execução usando o contêiner:</span><span class="sxs-lookup"><span data-stu-id="a3c23-212">Notice the app is running using the container:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a><span data-ttu-id="a3c23-213">Editar e continuar</span><span class="sxs-lookup"><span data-stu-id="a3c23-213">Edit and continue</span></span>

<span data-ttu-id="a3c23-214">As alterações em arquivos estáticos e exibições do Razor são atualizadas automaticamente sem a necessidade de uma etapa de compilação.</span><span class="sxs-lookup"><span data-stu-id="a3c23-214">Changes to static files and Razor views are automatically updated without the need for a compilation step.</span></span> <span data-ttu-id="a3c23-215">Faça a alteração, salve e atualize o navegador para exibir a atualização.</span><span class="sxs-lookup"><span data-stu-id="a3c23-215">Make the change, save, and refresh the browser to view the update.</span></span>

<span data-ttu-id="a3c23-216">As modificações nos arquivos de código exigem a compilação e a reinicialização do Kestrel dentro do contêiner.</span><span class="sxs-lookup"><span data-stu-id="a3c23-216">Code file modifications require compilation and a restart of Kestrel within the container.</span></span> <span data-ttu-id="a3c23-217">Depois de fazer a alteração, use `CTRL+F5` para executar o processo e iniciar o aplicativo dentro do contêiner.</span><span class="sxs-lookup"><span data-stu-id="a3c23-217">After making the change, use `CTRL+F5` to perform the process and start the app within the container.</span></span> <span data-ttu-id="a3c23-218">O contêiner do Docker não é recompilado nem interrompido.</span><span class="sxs-lookup"><span data-stu-id="a3c23-218">The Docker container isn't rebuilt or stopped.</span></span> <span data-ttu-id="a3c23-219">Execute o comando `docker ps` no PMC.</span><span class="sxs-lookup"><span data-stu-id="a3c23-219">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="a3c23-220">Observe que o contêiner original ainda está em execução como há 10 minutos:</span><span class="sxs-lookup"><span data-stu-id="a3c23-220">Notice the original container is still running as of 10 minutes ago:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a><span data-ttu-id="a3c23-221">Publicar imagens do Docker</span><span class="sxs-lookup"><span data-stu-id="a3c23-221">Publish Docker images</span></span>

<span data-ttu-id="a3c23-222">Depois de concluir o ciclo de desenvolvimento e depuração de seu aplicativo, as Ferramentas do Visual Studio para Docker ajudarão você a criar a imagem de produção do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3c23-222">Once the develop and debug cycle of the app is completed, the Visual Studio Tools for Docker assist in creating the production image of the app.</span></span> <span data-ttu-id="a3c23-223">Altere a lista suspensa de configuração para **Versão** e compile o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3c23-223">Change the configuration drop-down to **Release** and build the app.</span></span> <span data-ttu-id="a3c23-224">O conjunto de ferramentas adquire a imagem de compilação/publicação do hub do Docker (se ainda não estiver no cache).</span><span class="sxs-lookup"><span data-stu-id="a3c23-224">The tooling acquires the compile/publish image from Docker Hub (if not already in the cache).</span></span> <span data-ttu-id="a3c23-225">Uma imagem é produzida com a marcação *latest*, que você pode enviar por push para seu registro privado ou para o hub do Docker.</span><span class="sxs-lookup"><span data-stu-id="a3c23-225">An image is produced with the *latest* tag, which can be pushed to the private registry or Docker Hub.</span></span>

<span data-ttu-id="a3c23-226">Execute o comando `docker images` no PMC para ver a lista de imagens.</span><span class="sxs-lookup"><span data-stu-id="a3c23-226">Run the `docker images` command in PMC to see the list of images.</span></span> <span data-ttu-id="a3c23-227">Uma saída semelhante à apresentada a seguir será exibida:</span><span class="sxs-lookup"><span data-stu-id="a3c23-227">Output similar to the following is displayed:</span></span>

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

<span data-ttu-id="a3c23-228">As imagens `microsoft/aspnetcore-build` e `microsoft/aspnetcore` listadas na saída anterior são substituídas pelas imagens `microsoft/dotnet` com o .Net Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="a3c23-228">The `microsoft/aspnetcore-build` and `microsoft/aspnetcore` images listed in the preceding output are replaced with `microsoft/dotnet` images as of .NET Core 2.1.</span></span> <span data-ttu-id="a3c23-229">Para obter mais informações, consulte [o comunicado de migração de repositórios do Docker](https://github.com/aspnet/Announcements/issues/298).</span><span class="sxs-lookup"><span data-stu-id="a3c23-229">For more information, see [the Docker repositories migration announcement](https://github.com/aspnet/Announcements/issues/298).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a3c23-230">O comando `docker images` retorna imagens intermediárias com nomes de repositório e marcas identificadas como *\<none>* (não listadas acima).</span><span class="sxs-lookup"><span data-stu-id="a3c23-230">The `docker images` command returns intermediary images with repository names and tags identified as *\<none>* (not listed above).</span></span> <span data-ttu-id="a3c23-231">Essas imagens sem nome são produzidas pelo *Dockerfile* no [build de vários estágios](https://docs.docker.com/engine/userguide/eng-image/multistage-build/).</span><span class="sxs-lookup"><span data-stu-id="a3c23-231">These unnamed images are produced by the [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span></span> <span data-ttu-id="a3c23-232">Elas melhoram a eficiência da compilação da imagem final &mdash; apenas as camadas necessárias são recompiladas quando ocorrem alterações.</span><span class="sxs-lookup"><span data-stu-id="a3c23-232">They improve the efficiency of building the final image&mdash;only the necessary layers are rebuilt when changes occur.</span></span> <span data-ttu-id="a3c23-233">Quando você não precisar mais das imagens intermediárias, exclua-as usando o comando [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).</span><span class="sxs-lookup"><span data-stu-id="a3c23-233">When the intermediary images are no longer needed, delete them using the [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) command.</span></span>

<span data-ttu-id="a3c23-234">Pode haver uma expectativa de que a imagem de produção ou versão seja menor em comparação com a imagem *dev*.</span><span class="sxs-lookup"><span data-stu-id="a3c23-234">There may be an expectation for the production or release image to be smaller in size by comparison to the *dev* image.</span></span> <span data-ttu-id="a3c23-235">Devido ao mapeamento do volume, o depurador e o aplicativo estavam em execução no computador local e não dentro do contêiner.</span><span class="sxs-lookup"><span data-stu-id="a3c23-235">Because of the volume mapping, the debugger and app were running from the local machine and not within the container.</span></span> <span data-ttu-id="a3c23-236">A *última* imagem empacotou o código do aplicativo necessário para executar o aplicativo em um computador host.</span><span class="sxs-lookup"><span data-stu-id="a3c23-236">The *latest* image has packaged the necessary app code to run the app on a host machine.</span></span> <span data-ttu-id="a3c23-237">Portanto, o delta é o tamanho do código do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3c23-237">Therefore, the delta is the size of the app code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a3c23-238">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a3c23-238">Additional resources</span></span>

* [<span data-ttu-id="a3c23-239">Desenvolvimento de contêiner com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3c23-239">Container development with Visual Studio</span></span>](/visualstudio/containers)
* [<span data-ttu-id="a3c23-240">Azure Service Fabric: prepare seu ambiente de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="a3c23-240">Azure Service Fabric: Prepare your development environment</span></span>](/azure/service-fabric/service-fabric-get-started)
* [<span data-ttu-id="a3c23-241">Implantar um aplicativo .NET em um contêiner do Windows no Azure Service Fabric</span><span class="sxs-lookup"><span data-stu-id="a3c23-241">Deploy a .NET app in a Windows container to Azure Service Fabric</span></span>](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [<span data-ttu-id="a3c23-242">Solucionar problemas de desenvolvimento do Visual Studio 2017 com o Docker</span><span class="sxs-lookup"><span data-stu-id="a3c23-242">Troubleshoot Visual Studio 2017 development with Docker</span></span>](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [<span data-ttu-id="a3c23-243">Repositório do GitHub para Ferramentas do Visual Studio para Docker</span><span class="sxs-lookup"><span data-stu-id="a3c23-243">Visual Studio Tools for Docker GitHub repository</span></span>](https://github.com/Microsoft/DockerTools)

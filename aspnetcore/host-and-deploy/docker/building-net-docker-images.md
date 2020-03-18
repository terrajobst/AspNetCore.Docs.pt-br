---
title: Imagens do Docker para o ASP.NET Core
author: rick-anderson
description: Saiba como usar as imagens publicadas do Docker do .NET Core no Registro do Docker. Efetuar pull de imagens e compilar suas próprias imagens.
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: b55235ffc44f0bacf44bd1644e45210b37f19607
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511321"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="80920-104">Imagens do Docker para o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80920-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="80920-105">Este tutorial mostra como executar um aplicativo ASP.NET Core em contêineres do Docker.</span><span class="sxs-lookup"><span data-stu-id="80920-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="80920-106">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="80920-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="80920-107">Saiba mais sobre as imagens do Docker no Microsoft .NET Core</span><span class="sxs-lookup"><span data-stu-id="80920-107">Learn about Microsoft .NET Core Docker images</span></span>
> * <span data-ttu-id="80920-108">Baixar um aplicativo de exemplo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80920-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="80920-109">Executar o aplicativo de exemplo localmente</span><span class="sxs-lookup"><span data-stu-id="80920-109">Run the sample app locally</span></span>
> * <span data-ttu-id="80920-110">Executar o aplicativo de exemplo em contêineres do Linux</span><span class="sxs-lookup"><span data-stu-id="80920-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="80920-111">Executar o aplicativo de exemplo em contêineres do Windows</span><span class="sxs-lookup"><span data-stu-id="80920-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="80920-112">Criar e implantar manualmente</span><span class="sxs-lookup"><span data-stu-id="80920-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="80920-113">Imagens do ASP.NET Core Docker</span><span class="sxs-lookup"><span data-stu-id="80920-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="80920-114">Para este tutorial, você deve baixar um aplicativo de exemplo ASP.NET Core e executá-lo em contêineres do Docker.</span><span class="sxs-lookup"><span data-stu-id="80920-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="80920-115">O exemplo funciona com contêineres do Linux e do Windows.</span><span class="sxs-lookup"><span data-stu-id="80920-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="80920-116">O exemplo de Dockerfile usa o [recurso de build de vários estágios do Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) para compilar e executar em contêineres diferentes.</span><span class="sxs-lookup"><span data-stu-id="80920-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="80920-117">Os contêineres de build e execução são criados a partir de imagens que são fornecidas pela Microsoft no Hub do Docker:</span><span class="sxs-lookup"><span data-stu-id="80920-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="80920-118">O exemplo usa essa imagem para compilar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80920-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="80920-119">A imagem contém o SDK do .NET Core, que inclui as ferramentas de linha de comando (CLI).</span><span class="sxs-lookup"><span data-stu-id="80920-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="80920-120">A imagem é otimizada para desenvolvimento local, depuração e teste de unidade.</span><span class="sxs-lookup"><span data-stu-id="80920-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="80920-121">As ferramentas instaladas para compilação e desenvolvimento a tornam uma imagem relativamente grande.</span><span class="sxs-lookup"><span data-stu-id="80920-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet`

   <span data-ttu-id="80920-122">O exemplo usa essa imagem para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80920-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="80920-123">A imagem contém o runtime do ASP.NET Core e as bibliotecas e é otimizada para executar aplicativos em produção.</span><span class="sxs-lookup"><span data-stu-id="80920-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="80920-124">Desenvolvida para acelerar a implantação e a inicialização do aplicativo, a imagem é relativamente pequena, portanto, o desempenho da rede no registro do Docker para o host do Docker é otimizado.</span><span class="sxs-lookup"><span data-stu-id="80920-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="80920-125">Somente os binários e o conteúdo necessários para executar o aplicativo são copiados para o contêiner.</span><span class="sxs-lookup"><span data-stu-id="80920-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="80920-126">O conteúdo está pronto para ser executado, permitindo mais rapidez do `Docker run` para a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80920-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="80920-127">A compilação de código dinâmico não é necessária no modelo do Docker.</span><span class="sxs-lookup"><span data-stu-id="80920-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="80920-128">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="80920-128">Prerequisites</span></span>
::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="80920-129">SDK do .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="80920-129">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="80920-130">SDK do .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="80920-130">.NET Core SDK 3.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

* <span data-ttu-id="80920-131">Cliente do Docker 18.03 ou posterior</span><span class="sxs-lookup"><span data-stu-id="80920-131">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="80920-132">Distribuições Linux</span><span class="sxs-lookup"><span data-stu-id="80920-132">Linux distributions</span></span>
    * [<span data-ttu-id="80920-133">CentOS</span><span class="sxs-lookup"><span data-stu-id="80920-133">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="80920-134">Debian</span><span class="sxs-lookup"><span data-stu-id="80920-134">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="80920-135">Fedora</span><span class="sxs-lookup"><span data-stu-id="80920-135">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="80920-136">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="80920-136">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="80920-137">macOS</span><span class="sxs-lookup"><span data-stu-id="80920-137">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="80920-138">Windows</span><span class="sxs-lookup"><span data-stu-id="80920-138">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="80920-139">Git</span><span class="sxs-lookup"><span data-stu-id="80920-139">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="80920-140">Baixar o aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="80920-140">Download the sample app</span></span>

* <span data-ttu-id="80920-141">Baixe o exemplo por meio da clonagem do [repositório do Docker .NET Core](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="80920-141">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="80920-142">Executar o aplicativo localmente</span><span class="sxs-lookup"><span data-stu-id="80920-142">Run the app locally</span></span>

* <span data-ttu-id="80920-143">Navegue até a pasta do projeto em *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="80920-143">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="80920-144">Execute o seguinte comando para compilar e executar o aplicativo localmente:</span><span class="sxs-lookup"><span data-stu-id="80920-144">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="80920-145">Vá para `http://localhost:5000` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80920-145">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="80920-146">Pressione Ctrl+C no prompt de comando para interromper o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80920-146">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="80920-147">Executar em um contêiner do Linux</span><span class="sxs-lookup"><span data-stu-id="80920-147">Run in a Linux container</span></span>

* <span data-ttu-id="80920-148">No cliente do Docker, alterne para contêineres do Linux.</span><span class="sxs-lookup"><span data-stu-id="80920-148">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="80920-149">Navegue até a pasta do Dockerfile em *dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="80920-149">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="80920-150">Execute os seguintes comandos para compilar e executar a amostra no Docker:</span><span class="sxs-lookup"><span data-stu-id="80920-150">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="80920-151">Os argumentos de comando `build`:</span><span class="sxs-lookup"><span data-stu-id="80920-151">The `build` command arguments:</span></span>
  * <span data-ttu-id="80920-152">Dê o nome aspnetapp para a imagem.</span><span class="sxs-lookup"><span data-stu-id="80920-152">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="80920-153">Procure o Dockerfile na pasta atual (o ponto no final).</span><span class="sxs-lookup"><span data-stu-id="80920-153">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="80920-154">Os argumentos de comando de execução:</span><span class="sxs-lookup"><span data-stu-id="80920-154">The run command arguments:</span></span>
  * <span data-ttu-id="80920-155">Aloque um pseudo-TTY e mantenha-o aberto, mesmo se não estiver anexado.</span><span class="sxs-lookup"><span data-stu-id="80920-155">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="80920-156">(Mesmo efeito de `--interactive --tty`).</span><span class="sxs-lookup"><span data-stu-id="80920-156">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="80920-157">Remova automaticamente o contêiner quando ele é encerrado.</span><span class="sxs-lookup"><span data-stu-id="80920-157">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="80920-158">Mapeie a porta 5000 no computador local para a porta 80 no contêiner.</span><span class="sxs-lookup"><span data-stu-id="80920-158">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="80920-159">Dê o nome aspnetcore_sample ao contêiner.</span><span class="sxs-lookup"><span data-stu-id="80920-159">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="80920-160">Especifique a imagem aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="80920-160">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="80920-161">Vá para `http://localhost:5000` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80920-161">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="80920-162">Executar em um contêiner do Windows</span><span class="sxs-lookup"><span data-stu-id="80920-162">Run in a Windows container</span></span>

* <span data-ttu-id="80920-163">No cliente do Docker, alterne para os contêineres do Windows.</span><span class="sxs-lookup"><span data-stu-id="80920-163">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="80920-164">Navegue até a pasta do arquivo do docker em `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="80920-164">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="80920-165">Execute os seguintes comandos para compilar e executar a amostra no Docker:</span><span class="sxs-lookup"><span data-stu-id="80920-165">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="80920-166">Para os contêineres do Windows, você precisará do endereço IP do contêiner (navegar até `http://localhost:5000` não funcionará):</span><span class="sxs-lookup"><span data-stu-id="80920-166">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="80920-167">Abra outro prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="80920-167">Open up another command prompt.</span></span>
  * <span data-ttu-id="80920-168">Execute `docker ps` para ver os contêineres em execução.</span><span class="sxs-lookup"><span data-stu-id="80920-168">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="80920-169">Verifique se o contêiner "aspnetcore_sample" está lá.</span><span class="sxs-lookup"><span data-stu-id="80920-169">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="80920-170">Execute `docker exec aspnetcore_sample ipconfig` para exibir o endereço IP do contêiner.</span><span class="sxs-lookup"><span data-stu-id="80920-170">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="80920-171">A saída do comando é semelhante a este exemplo:</span><span class="sxs-lookup"><span data-stu-id="80920-171">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="80920-172">Copie o endereço IPv4 (por exemplo, 172.29.245.43) do contêiner e cole na barra de endereços do navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80920-172">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="80920-173">Criar e implantar manualmente</span><span class="sxs-lookup"><span data-stu-id="80920-173">Build and deploy manually</span></span>

<span data-ttu-id="80920-174">Em alguns cenários, talvez você queira implantar um aplicativo em um contêiner copiando nele os arquivos do aplicativo que são necessários no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="80920-174">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="80920-175">Esta seção mostra como realizar a implantação manual.</span><span class="sxs-lookup"><span data-stu-id="80920-175">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="80920-176">Navegue até a pasta do projeto em *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="80920-176">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="80920-177">Execute o comando [dotnet publish](/dotnet/core/tools/dotnet-publish):</span><span class="sxs-lookup"><span data-stu-id="80920-177">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="80920-178">Os argumentos do comando:</span><span class="sxs-lookup"><span data-stu-id="80920-178">The command arguments:</span></span>
  * <span data-ttu-id="80920-179">Compile o aplicativo no modo de versão (o padrão é o modo de depuração).</span><span class="sxs-lookup"><span data-stu-id="80920-179">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="80920-180">Crie os arquivos na pasta *published*.</span><span class="sxs-lookup"><span data-stu-id="80920-180">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="80920-181">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80920-181">Run the application.</span></span>

  * <span data-ttu-id="80920-182">Windows:</span><span class="sxs-lookup"><span data-stu-id="80920-182">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="80920-183">Linux:</span><span class="sxs-lookup"><span data-stu-id="80920-183">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="80920-184">Navegue até `http://localhost:5000` para ver a página inicial.</span><span class="sxs-lookup"><span data-stu-id="80920-184">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="80920-185">Para usar o aplicativo publicado manualmente em um contêiner do Docker, crie um Dockerfile e use o comando `docker build .` para criar o contêiner.</span><span class="sxs-lookup"><span data-stu-id="80920-185">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

::: moniker range="< aspnetcore-3.0"

```console
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="80920-186">O Dockerfile</span><span class="sxs-lookup"><span data-stu-id="80920-186">The Dockerfile</span></span>

<span data-ttu-id="80920-187">Aqui está o *Dockerfile* usado pelo comando `docker build` que você executou anteriormente.</span><span class="sxs-lookup"><span data-stu-id="80920-187">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="80920-188">Ele usa `dotnet publish` da mesma maneira que foi feito nesta seção para realizar a criação e implantação.</span><span class="sxs-lookup"><span data-stu-id="80920-188">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out


FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```console
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="80920-189">O Dockerfile</span><span class="sxs-lookup"><span data-stu-id="80920-189">The Dockerfile</span></span>

<span data-ttu-id="80920-190">Aqui está o *Dockerfile* usado pelo comando `docker build` que você executou anteriormente.</span><span class="sxs-lookup"><span data-stu-id="80920-190">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="80920-191">Ele usa `dotnet publish` da mesma maneira que foi feito nesta seção para realizar a criação e implantação.</span><span class="sxs-lookup"><span data-stu-id="80920-191">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out


FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

```console
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

## <a name="additional-resources"></a><span data-ttu-id="80920-192">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="80920-192">Additional resources</span></span>

* [<span data-ttu-id="80920-193">Comando de build do Docker</span><span class="sxs-lookup"><span data-stu-id="80920-193">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="80920-194">Comando de execução do Docker</span><span class="sxs-lookup"><span data-stu-id="80920-194">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="80920-195">[Exemplo do Docker do ASP.NET Core](https://github.com/dotnet/dotnet-docker) (aquele usado neste tutorial).</span><span class="sxs-lookup"><span data-stu-id="80920-195">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="80920-196">Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga</span><span class="sxs-lookup"><span data-stu-id="80920-196">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [<span data-ttu-id="80920-197">Trabalhar com ferramentas de Docker do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="80920-197">Working with Visual Studio Docker Tools</span></span>](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [<span data-ttu-id="80920-198">Depurar com o Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="80920-198">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers) 

## <a name="next-steps"></a><span data-ttu-id="80920-199">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="80920-199">Next steps</span></span>

<span data-ttu-id="80920-200">O repositório Git que contém o aplicativo de exemplo também inclui a documentação.</span><span class="sxs-lookup"><span data-stu-id="80920-200">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="80920-201">Para obter uma visão geral dos recursos disponíveis no repositório, confira [o arquivo Leiame](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="80920-201">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="80920-202">Em particular, saiba como implementar o HTTPS:</span><span class="sxs-lookup"><span data-stu-id="80920-202">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="80920-203">Como desenvolver aplicativos ASP.NET Core com o Docker em HTTPS</span><span class="sxs-lookup"><span data-stu-id="80920-203">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)

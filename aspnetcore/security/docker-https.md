---
title: Hospedando ASP.NET Core imagens com o Docker via HTTPS
author: rick-anderson
description: Saiba como hospedar ASP.NET Core imagens com o Docker via HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
uid: security/docker-https
ms.openlocfilehash: c13ba02845eef5c53a939feec2be8a01bc4ca128
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082540"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="17d5d-103">Hospedando ASP.NET Core imagens com o Docker via HTTPS</span><span class="sxs-lookup"><span data-stu-id="17d5d-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="17d5d-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="17d5d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="17d5d-105">O ASP.NET Core usa [https por padrão](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="17d5d-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="17d5d-106">O [https](https://en.wikipedia.org/wiki/HTTPS) depende de [certificados](https://en.wikipedia.org/wiki/Public_key_certificate) para confiança, identidade e criptografia.</span><span class="sxs-lookup"><span data-stu-id="17d5d-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="17d5d-107">Este documento explica como executar imagens de contêiner predefinidas com HTTPS.</span><span class="sxs-lookup"><span data-stu-id="17d5d-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="17d5d-108">Consulte [desenvolvendo aplicativos ASP.NET Core com o Docker sobre HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="17d5d-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="17d5d-109">Este exemplo requer o [docker 17, 6](https://docs.docker.com/release-notes/docker-ce) ou posterior do [cliente do Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="17d5d-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="17d5d-110">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="17d5d-110">Prerequisites</span></span>

<span data-ttu-id="17d5d-111">O [SDK do .NET Core 2,2](https://www.microsoft.com/net/download) ou posterior é necessário para algumas das instruções neste documento.</span><span class="sxs-lookup"><span data-stu-id="17d5d-111">The [.NET Core 2.2 SDK](https://www.microsoft.com/net/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="17d5d-112">Certificados</span><span class="sxs-lookup"><span data-stu-id="17d5d-112">Certificates</span></span>

<span data-ttu-id="17d5d-113">Um certificado de uma [autoridade de certificação](https://en.wikipedia.org/wiki/Certificate_authority) é necessário para [Hospedagem de produção](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) para um domínio.</span><span class="sxs-lookup"><span data-stu-id="17d5d-113">A certificate from a [certificate authority](https://en.wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span>  <span data-ttu-id="17d5d-114">[Vamos criptografar](https://letsencrypt.org/) é uma autoridade de certificação que oferece certificados gratuitos.</span><span class="sxs-lookup"><span data-stu-id="17d5d-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="17d5d-115">Este documento usa [certificados de desenvolvimento autoassinados](https://en.wikipedia.org/wiki/Self-signed_certificate) para hospedar imagens `localhost`predefinidas.</span><span class="sxs-lookup"><span data-stu-id="17d5d-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="17d5d-116">As instruções são semelhantes ao uso de certificados de produção.</span><span class="sxs-lookup"><span data-stu-id="17d5d-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="17d5d-117">Para certificados de produção:</span><span class="sxs-lookup"><span data-stu-id="17d5d-117">For production certs:</span></span>

* <span data-ttu-id="17d5d-118">A `dotnet dev-certs` ferramenta não é necessária.</span><span class="sxs-lookup"><span data-stu-id="17d5d-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="17d5d-119">Os certificados não precisam ser armazenados no local usado nas instruções.</span><span class="sxs-lookup"><span data-stu-id="17d5d-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="17d5d-120">Qualquer local deve funcionar, embora o armazenamento de certificados no diretório do site não seja recomendado.</span><span class="sxs-lookup"><span data-stu-id="17d5d-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="17d5d-121">As instruções montam os certificados de montagem em contêineres.</span><span class="sxs-lookup"><span data-stu-id="17d5d-121">The instructions volume mount certificates into containers.</span></span> <span data-ttu-id="17d5d-122">Você pode adicionar certificados em imagens de contêiner com `COPY` um comando em um Dockerfile.</span><span class="sxs-lookup"><span data-stu-id="17d5d-122">You can add certificates into container images with a `COPY` command in a Dockerfile.</span></span> <span data-ttu-id="17d5d-123">Não é recomendável copiar certificados em uma imagem:</span><span class="sxs-lookup"><span data-stu-id="17d5d-123">Copying certificates into an image is not recommended:</span></span>

* <span data-ttu-id="17d5d-124">Torna difícil usar a mesma imagem para teste com certificados de desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="17d5d-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="17d5d-125">Dificulta o uso da mesma imagem para hospedagem com certificados de produção.</span><span class="sxs-lookup"><span data-stu-id="17d5d-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="17d5d-126">Há um risco significativo de divulgação de certificado.</span><span class="sxs-lookup"><span data-stu-id="17d5d-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="17d5d-127">Executando imagens de contêiner predefinidas com HTTPS</span><span class="sxs-lookup"><span data-stu-id="17d5d-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="17d5d-128">Use as instruções a seguir para a configuração do seu sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="17d5d-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="17d5d-129">Windows usando contêineres do Linux</span><span class="sxs-lookup"><span data-stu-id="17d5d-129">Windows using Linux containers</span></span>

<span data-ttu-id="17d5d-130">Gerar certificado e configurar computador local:</span><span class="sxs-lookup"><span data-stu-id="17d5d-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="17d5d-131">Nos comandos anteriores, substitua `{ password here }` por uma senha.</span><span class="sxs-lookup"><span data-stu-id="17d5d-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="17d5d-132">Execute a imagem de contêiner com ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="17d5d-132">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="17d5d-133">A senha deve corresponder à senha usada para o certificado.</span><span class="sxs-lookup"><span data-stu-id="17d5d-133">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="17d5d-134">macOS ou Linux</span><span class="sxs-lookup"><span data-stu-id="17d5d-134">macOS or Linux</span></span>

<span data-ttu-id="17d5d-135">Gerar certificado e configurar computador local:</span><span class="sxs-lookup"><span data-stu-id="17d5d-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="17d5d-136">`dotnet dev-certs https --trust`Só tem suporte no macOS e no Windows.</span><span class="sxs-lookup"><span data-stu-id="17d5d-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="17d5d-137">Você precisa confiar em certificados no Linux na forma com que o seu distribuição dá suporte.</span><span class="sxs-lookup"><span data-stu-id="17d5d-137">You need to trust certs on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="17d5d-138">É provável que você precise confiar no certificado em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="17d5d-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="17d5d-139">Nos comandos anteriores, substitua `{ password here }` por uma senha.</span><span class="sxs-lookup"><span data-stu-id="17d5d-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="17d5d-140">Execute a imagem de contêiner com ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="17d5d-140">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="17d5d-141">A senha deve corresponder à senha usada para o certificado.</span><span class="sxs-lookup"><span data-stu-id="17d5d-141">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="17d5d-142">Windows usando contêineres do Windows</span><span class="sxs-lookup"><span data-stu-id="17d5d-142">Windows using Windows containers</span></span>

<span data-ttu-id="17d5d-143">Gerar certificado e configurar computador local:</span><span class="sxs-lookup"><span data-stu-id="17d5d-143">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="17d5d-144">Nos comandos anteriores, substitua `{ password here }` por uma senha.</span><span class="sxs-lookup"><span data-stu-id="17d5d-144">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="17d5d-145">Execute a imagem de contêiner com ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="17d5d-145">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="17d5d-146">A senha deve corresponder à senha usada para o certificado.</span><span class="sxs-lookup"><span data-stu-id="17d5d-146">The password must match the password used for the certificate.</span></span>
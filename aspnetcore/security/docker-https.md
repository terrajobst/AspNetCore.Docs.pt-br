---
title: Hospedando ASP.NET Core imagens com o Docker via HTTPS
author: rick-anderson
description: Saiba como hospedar ASP.NET Core imagens com o Docker via HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
- Let's Encrypt
uid: security/docker-https
ms.openlocfilehash: f2a615093e7b1190962bd1c6ecbcc63f65bfbe7e
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511581"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a>Hospedando ASP.NET Core imagens com o Docker via HTTPS

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

O ASP.NET Core usa [https por padrão](/aspnet/core/security/enforcing-ssl). O [https](https://en.wikipedia.org/wiki/HTTPS) depende de [certificados](https://en.wikipedia.org/wiki/Public_key_certificate) para confiança, identidade e criptografia.

Este documento explica como executar imagens de contêiner predefinidas com HTTPS.

Consulte [desenvolvendo aplicativos ASP.NET Core com o Docker sobre HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) para cenários de desenvolvimento.

Este exemplo requer o [docker 17, 6](https://docs.docker.com/release-notes/docker-ce) ou posterior do [cliente do Docker](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

O [SDK do .NET Core 2,2](https://dotnet.microsoft.com/download) ou posterior é necessário para algumas das instruções neste documento.

## <a name="certificates"></a>Certificados

Um certificado de uma [autoridade de certificação](https://wikipedia.org/wiki/Certificate_authority) é necessário para [Hospedagem de produção](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) para um domínio. [Let's Encrypt](https://letsencrypt.org/) é uma autoridade de certificação que oferece certificados gratuitos.

Este documento usa [certificados de desenvolvimento autoassinado](https://en.wikipedia.org/wiki/Self-signed_certificate) para hospedar imagens predefinidas em `localhost`. As instruções são semelhantes ao uso de certificados de produção.

Para certificados de produção:

* A ferramenta de `dotnet dev-certs` não é necessária.
* Os certificados não precisam ser armazenados no local usado nas instruções. Qualquer local deve funcionar, embora o armazenamento de certificados no diretório do site não seja recomendado.

As instruções contidas na seção a seguir montam os certificados de montagem em contêineres usando a opção de linha de comando `-v` do Docker. Você pode adicionar certificados em imagens de contêiner com um comando `COPY` em um *Dockerfile*, mas isso não é recomendado. A cópia de certificados em uma imagem não é recomendada pelos seguintes motivos:

* Torna difícil usar a mesma imagem para teste com certificados de desenvolvedor.
* Dificulta o uso da mesma imagem para hospedagem com certificados de produção.
* Há um risco significativo de divulgação de certificado.

## <a name="running-pre-built-container-images-with-https"></a>Executando imagens de contêiner predefinidas com HTTPS

Use as instruções a seguir para a configuração do seu sistema operacional.

### <a name="windows-using-linux-containers"></a>Windows usando contêineres do Linux

Gerar certificado e configurar computador local:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Nos comandos anteriores, substitua `{ password here }` por uma senha.

Execute a imagem de contêiner com ASP.NET Core configurado para HTTPS:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

A senha deve corresponder à senha usada para o certificado.

### <a name="macos-or-linux"></a>macOS ou Linux

Gerar certificado e configurar computador local:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Só há suporte para `dotnet dev-certs https --trust` no macOS e no Windows. Você precisa confiar em certificados no Linux na forma com que o seu distribuição dá suporte. É provável que você precise confiar no certificado em seu navegador.

Nos comandos anteriores, substitua `{ password here }` por uma senha.

Execute a imagem de contêiner com ASP.NET Core configurado para HTTPS:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

A senha deve corresponder à senha usada para o certificado.

### <a name="windows-using-windows-containers"></a>Windows usando contêineres do Windows

Gerar certificado e configurar computador local:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Nos comandos anteriores, substitua `{ password here }` por uma senha.

Execute a imagem de contêiner com ASP.NET Core configurado para HTTPS:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

A senha deve corresponder à senha usada para o certificado.

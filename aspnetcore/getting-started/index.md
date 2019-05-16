---
title: Introdução ao ASP.NET Core
author: rick-anderson
description: Um breve tutorial que cria e executa um aplicativo básico Olá, Mundo usando o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 5/15/2019
uid: getting-started
ms.openlocfilehash: 9227dcfbc84376d9d73bc6fc0dd76085779acae1
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610312"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Tutorial: Introdução ao ASP.NET Core

Este tutorial mostra como usar a interface de linha de comando do .NET Core para criar e executar um aplicativo Web ASP.NET Core.

Você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto de aplicativo Web.
> * Confiar no certificado de desenvolvimento.
> * Execute o aplicativo.
> * Editar uma página do Razor.

No final, você terá um aplicativo Web de trabalho em execução no seu computador local.

![Página inicial do aplicativo Web](_static/home-page.png)

## <a name="prerequisites"></a>Pré-requisitos

* [SDK do .NET Core 2.2](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a>Criar um projeto de aplicativo Web

Abra um shell de comando e insira o seguinte comando:

```console
dotnet new webapp -o aspnetcoreapp
```

### <a name="trust-the-development-certificate"></a>Confiar no certificado de desenvolvimento

Confie no certificado de desenvolvimento HTTPS:

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
dotnet dev-certs https --trust
```

O comando anterior exibe a caixa de diálogo a seguir:

![Caixa de diálogo de aviso de segurança](~/getting-started/_static/cert.png)

Selecione **Sim** se você concordar com confiar no certificado de desenvolvimento.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

```console
dotnet dev-certs https --trust
```

O comando anterior exibe a mensagem a seguir:

*Foi solicitada confiança no certificado de desenvolvimento HTTPS. Se o certificado já não for confiável, executaremos o seguinte comando:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`

Esse comando pode solicitar que você insira sua senha para instalar o certificado no conjunto de chaves do sistema. Insira sua senha se você concordar em confiar no certificado de desenvolvimento.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Para o Subsistema do Windows para Linux, confira [certificado de confiança HTTPS do Subsistema do Windows para Linux](xref:security/enforcing-ssl#wsl).

Consulte a documentação para sua distribuição do Linux sobre como confiar no certificado de desenvolvimento HTTPS.

---

Para obter mais informações, confira [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)

## <a name="run-the-app"></a>Executar o aplicativo

Execute os seguintes comandos:

```console
cd aspnetcoreapp
dotnet run
```

Depois que o shell de comando indicar que o aplicativo foi iniciado, navegue até [https://localhost:5001](https://localhost:5001). Clique em **Aceitar** para aceitar a política de privacidade e cookies. Este aplicativo não armazena informações pessoais.

## <a name="edit-a-razor-page"></a>Editar uma página do Razor

Abra *Pages/Index.cshtml* e modifique a página com a seguinte marcação realçada:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Navegue até [https://localhost:5001](https://localhost:5001) e verifique se as alterações são exibidas.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um projeto de aplicativo Web.
> * Confiar no certificado de desenvolvimento.
> * Execute o projeto.
> * Faça uma alteração.

Para saber mais sobre o ASP.NET Core, veja o caminho de aprendizado recomendados na introdução:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>

---
title: Introdução ao ASP.NET Core
author: rick-anderson
description: Um breve tutorial que cria e executa um aplicativo básico Olá, Mundo usando o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
uid: getting-started
ms.openlocfilehash: 047fd7a74d3d53f68a730d67b63c65fe6bda529f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658462"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Tutorial: introdução ao ASP.NET Core

Este tutorial mostra como criar e executar um aplicativo Web ASP.NET Core usando o CLI do .NET Core.

Você aprenderá a:

> [!div class="checklist"]
> * Criar um projeto de aplicativo Web.
> * Confiar no certificado de desenvolvimento.
> * Execute o aplicativo.
> * Editar uma página do Razor.

No final, você terá um aplicativo Web de trabalho em execução no seu computador local.

![Página inicial do aplicativo Web](_static/home-page.png)

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a>Criar um projeto do aplicativo Web

Abra um shell de comando e insira o seguinte comando:

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

O comando anterior:

* Cria um novo aplicativo Web.  
* O parâmetro `-o aspnetcoreapp` cria um diretório chamado *aspnetcoreapp* com os arquivos de origem para o aplicativo.

### <a name="trust-the-development-certificate"></a>Confiar no certificado de desenvolvimento

Confie no certificado de desenvolvimento HTTPS:

# <a name="windows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

O comando anterior exibe a caixa de diálogo a seguir:

![Caixa de diálogo de aviso de segurança](~/getting-started/_static/cert.png)

Selecione **Sim** se você concordar com confiar no certificado de desenvolvimento.

# <a name="macos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

O comando anterior exibe a mensagem a seguir:

*A confiança do certificado de desenvolvimento https foi solicitada. Se o certificado ainda não for confiável, executaremos o seguinte comando:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`

Esse comando pode solicitar que você insira sua senha para instalar o certificado no conjunto de chaves do sistema. Insira sua senha se você concordar em confiar no certificado de desenvolvimento.

# <a name="linux"></a>[Linux](#tab/linux)

Consulte a documentação para sua distribuição do Linux sobre como confiar no certificado de desenvolvimento HTTPS.

---

Para obter mais informações, confira [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)

## <a name="run-the-app"></a>Executar o aplicativo

Execute os seguintes comandos:

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

Depois que o shell de comando indicar que o aplicativo foi iniciado, navegue até [https://localhost:5001](https://localhost:5001).

## <a name="edit-a-razor-page"></a>Editar uma página do Razor

Abra *pages/index. cshtml* e modifique e salve a página com a seguinte marcação realçada:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Navegue até [https://localhost:5001](https://localhost:5001), atualize a página e verifique se as alterações são exibidas.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um projeto de aplicativo Web.
> * Confiar no certificado de desenvolvimento.
> * Execute o projeto.
> * Faça uma alteração.

Para saber mais sobre o ASP.NET Core, veja o caminho de aprendizado recomendados na introdução:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>

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
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="17af4-103">Tutorial: introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17af4-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="17af4-104">Este tutorial mostra como criar e executar um aplicativo Web ASP.NET Core usando o CLI do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="17af4-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="17af4-105">Você aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="17af4-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="17af4-106">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="17af4-106">Create a web app project.</span></span>
> * <span data-ttu-id="17af4-107">Confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="17af4-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="17af4-108">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17af4-108">Run the app.</span></span>
> * <span data-ttu-id="17af4-109">Editar uma página do Razor.</span><span class="sxs-lookup"><span data-stu-id="17af4-109">Edit a Razor page.</span></span>

<span data-ttu-id="17af4-110">No final, você terá um aplicativo Web de trabalho em execução no seu computador local.</span><span class="sxs-lookup"><span data-stu-id="17af4-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Página inicial do aplicativo Web](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="17af4-112">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="17af4-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="17af4-113">Criar um projeto do aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="17af4-113">Create a web app project</span></span>

<span data-ttu-id="17af4-114">Abra um shell de comando e insira o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="17af4-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="17af4-115">O comando anterior:</span><span class="sxs-lookup"><span data-stu-id="17af4-115">The preceding command:</span></span>

* <span data-ttu-id="17af4-116">Cria um novo aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="17af4-116">Creates a new web app.</span></span>  
* <span data-ttu-id="17af4-117">O parâmetro `-o aspnetcoreapp` cria um diretório chamado *aspnetcoreapp* com os arquivos de origem para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17af4-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="17af4-118">Confiar no certificado de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="17af4-118">Trust the development certificate</span></span>

<span data-ttu-id="17af4-119">Confie no certificado de desenvolvimento HTTPS:</span><span class="sxs-lookup"><span data-stu-id="17af4-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="17af4-120">Windows</span><span class="sxs-lookup"><span data-stu-id="17af4-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="17af4-121">O comando anterior exibe a caixa de diálogo a seguir:</span><span class="sxs-lookup"><span data-stu-id="17af4-121">The preceding command displays the following dialog:</span></span>

![Caixa de diálogo de aviso de segurança](~/getting-started/_static/cert.png)

<span data-ttu-id="17af4-123">Selecione **Sim** se você concordar com confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="17af4-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="17af4-124">macOS</span><span class="sxs-lookup"><span data-stu-id="17af4-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="17af4-125">O comando anterior exibe a mensagem a seguir:</span><span class="sxs-lookup"><span data-stu-id="17af4-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="17af4-126">*A confiança do certificado de desenvolvimento https foi solicitada. Se o certificado ainda não for confiável, executaremos o seguinte comando:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="17af4-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="17af4-127">Esse comando pode solicitar que você insira sua senha para instalar o certificado no conjunto de chaves do sistema.</span><span class="sxs-lookup"><span data-stu-id="17af4-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="17af4-128">Insira sua senha se você concordar em confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="17af4-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="17af4-129">Linux</span><span class="sxs-lookup"><span data-stu-id="17af4-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="17af4-130">Consulte a documentação para sua distribuição do Linux sobre como confiar no certificado de desenvolvimento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="17af4-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="17af4-131">Para obter mais informações, confira [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="17af4-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="17af4-132">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="17af4-132">Run the app</span></span>

<span data-ttu-id="17af4-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="17af4-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="17af4-134">Depois que o shell de comando indicar que o aplicativo foi iniciado, navegue até [https://localhost:5001](https://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="17af4-134">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="17af4-135">Editar uma página do Razor</span><span class="sxs-lookup"><span data-stu-id="17af4-135">Edit a Razor page</span></span>

<span data-ttu-id="17af4-136">Abra *pages/index. cshtml* e modifique e salve a página com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="17af4-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="17af4-137">Navegue até [https://localhost:5001](https://localhost:5001), atualize a página e verifique se as alterações são exibidas.</span><span class="sxs-lookup"><span data-stu-id="17af4-137">Browse to [https://localhost:5001](https://localhost:5001), refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="17af4-138">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="17af4-138">Next steps</span></span>

<span data-ttu-id="17af4-139">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="17af4-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="17af4-140">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="17af4-140">Create a web app project.</span></span>
> * <span data-ttu-id="17af4-141">Confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="17af4-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="17af4-142">Execute o projeto.</span><span class="sxs-lookup"><span data-stu-id="17af4-142">Run the project.</span></span>
> * <span data-ttu-id="17af4-143">Faça uma alteração.</span><span class="sxs-lookup"><span data-stu-id="17af4-143">Make a change.</span></span>

<span data-ttu-id="17af4-144">Para saber mais sobre o ASP.NET Core, veja o caminho de aprendizado recomendados na introdução:</span><span class="sxs-lookup"><span data-stu-id="17af4-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>

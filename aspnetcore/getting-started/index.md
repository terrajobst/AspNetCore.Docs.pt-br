---
title: Introdução ao ASP.NET Core
author: rick-anderson
description: Um breve tutorial que cria e executa um aplicativo básico Olá, Mundo usando o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: getting-started
ms.openlocfilehash: 798f1ee87c05d886d8991e3f0230c8ebc6341ba8
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925100"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="17efb-103">Tutorial: Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17efb-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="17efb-104">Este tutorial mostra como usar a interface de linha de comando do .NET Core para criar e executar um aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="17efb-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="17efb-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="17efb-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="17efb-106">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="17efb-106">Create a web app project.</span></span>
> * <span data-ttu-id="17efb-107">Confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="17efb-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="17efb-108">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17efb-108">Run the app.</span></span>
> * <span data-ttu-id="17efb-109">Editar uma página do Razor.</span><span class="sxs-lookup"><span data-stu-id="17efb-109">Edit a Razor page.</span></span>

<span data-ttu-id="17efb-110">No final, você terá um aplicativo Web de trabalho em execução no seu computador local.</span><span class="sxs-lookup"><span data-stu-id="17efb-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Página inicial do aplicativo Web](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="17efb-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="17efb-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.0-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="17efb-113">Criar um projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="17efb-113">Create a web app project</span></span>

<span data-ttu-id="17efb-114">Abra um shell de comando e insira o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="17efb-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="17efb-115">O comando anterior:</span><span class="sxs-lookup"><span data-stu-id="17efb-115">The preceding command:</span></span>

* <span data-ttu-id="17efb-116">Cria um novo aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="17efb-116">Creates a new web app.</span></span>  
* <span data-ttu-id="17efb-117">O `-o aspnetcoreapp` parâmetro cria um diretório chamado *aspnetcoreapp* com os arquivos de origem para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17efb-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="17efb-118">Confiar no certificado de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="17efb-118">Trust the development certificate</span></span>

<span data-ttu-id="17efb-119">Confie no certificado de desenvolvimento HTTPS:</span><span class="sxs-lookup"><span data-stu-id="17efb-119">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="17efb-120">Windows</span><span class="sxs-lookup"><span data-stu-id="17efb-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="17efb-121">O comando anterior exibe a caixa de diálogo a seguir:</span><span class="sxs-lookup"><span data-stu-id="17efb-121">The preceding command displays the following dialog:</span></span>

![Caixa de diálogo de aviso de segurança](~/getting-started/_static/cert.png)

<span data-ttu-id="17efb-123">Selecione **Sim** se você concordar com confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="17efb-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="17efb-124">macOS</span><span class="sxs-lookup"><span data-stu-id="17efb-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="17efb-125">O comando anterior exibe a mensagem a seguir:</span><span class="sxs-lookup"><span data-stu-id="17efb-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="17efb-126">*Foi solicitada confiança no certificado de desenvolvimento HTTPS. Se o certificado já não for confiável, executaremos o seguinte comando:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="17efb-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="17efb-127">Esse comando pode solicitar que você insira sua senha para instalar o certificado no conjunto de chaves do sistema.</span><span class="sxs-lookup"><span data-stu-id="17efb-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="17efb-128">Insira sua senha se você concordar em confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="17efb-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="17efb-129">Linux</span><span class="sxs-lookup"><span data-stu-id="17efb-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="17efb-130">Para o Subsistema do Windows para Linux, confira [certificado de confiança HTTPS do Subsistema do Windows para Linux](xref:security/enforcing-ssl#wsl).</span><span class="sxs-lookup"><span data-stu-id="17efb-130">For Windows Subsystem for Linux, see [Trust HTTPS certificate from Windows Subsystem for Linux](xref:security/enforcing-ssl#wsl).</span></span>

<span data-ttu-id="17efb-131">Consulte a documentação para sua distribuição do Linux sobre como confiar no certificado de desenvolvimento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="17efb-131">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="17efb-132">Para obter mais informações, confira [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="17efb-132">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="17efb-133">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="17efb-133">Run the app</span></span>

<span data-ttu-id="17efb-134">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="17efb-134">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="17efb-135">Depois que o shell de comando indicar que o aplicativo foi iniciado, navegue até [https://localhost:5001](https://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="17efb-135">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="17efb-136">Editar uma página do Razor</span><span class="sxs-lookup"><span data-stu-id="17efb-136">Edit a Razor page</span></span>

<span data-ttu-id="17efb-137">Abra *pages/index. cshtml* e modifique e salve a página com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="17efb-137">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="17efb-138">Navegue até [https://localhost:5001](https://localhost:5001), atualize a página e verifique se as alterações são exibidas.</span><span class="sxs-lookup"><span data-stu-id="17efb-138">Browse to [https://localhost:5001](https://localhost:5001), refresh the page and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="17efb-139">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="17efb-139">Next steps</span></span>

<span data-ttu-id="17efb-140">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="17efb-140">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="17efb-141">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="17efb-141">Create a web app project.</span></span>
> * <span data-ttu-id="17efb-142">Confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="17efb-142">Trust the development certificate.</span></span>
> * <span data-ttu-id="17efb-143">Execute o projeto.</span><span class="sxs-lookup"><span data-stu-id="17efb-143">Run the project.</span></span>
> * <span data-ttu-id="17efb-144">Faça uma alteração.</span><span class="sxs-lookup"><span data-stu-id="17efb-144">Make a change.</span></span>

<span data-ttu-id="17efb-145">Para saber mais sobre o ASP.NET Core, veja o caminho de aprendizado recomendados na introdução:</span><span class="sxs-lookup"><span data-stu-id="17efb-145">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>

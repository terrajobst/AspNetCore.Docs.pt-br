---
title: Introdução ao ASP.NET Core
author: rick-anderson
description: Um breve tutorial que cria e executa um aplicativo básico Olá, Mundo usando o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 05/15/2019
uid: getting-started
ms.openlocfilehash: d1edf91f1b37ba2b69732471dc6c1f306ac5ad24
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081119"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="553a6-103">Tutorial: Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="553a6-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="553a6-104">Este tutorial mostra como usar a interface de linha de comando do .NET Core para criar e executar um aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="553a6-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="553a6-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="553a6-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="553a6-106">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="553a6-106">Create a web app project.</span></span>
> * <span data-ttu-id="553a6-107">Confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="553a6-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="553a6-108">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="553a6-108">Run the app.</span></span>
> * <span data-ttu-id="553a6-109">Editar uma página do Razor.</span><span class="sxs-lookup"><span data-stu-id="553a6-109">Edit a Razor page.</span></span>

<span data-ttu-id="553a6-110">No final, você terá um aplicativo Web de trabalho em execução no seu computador local.</span><span class="sxs-lookup"><span data-stu-id="553a6-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Página inicial do aplicativo Web](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="553a6-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="553a6-112">Prerequisites</span></span>

* [<span data-ttu-id="553a6-113">SDK do .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="553a6-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="553a6-114">Criar um projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="553a6-114">Create a web app project</span></span>

<span data-ttu-id="553a6-115">Abra um shell de comando e insira o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="553a6-115">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

### <a name="trust-the-development-certificate"></a><span data-ttu-id="553a6-116">Confiar no certificado de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="553a6-116">Trust the development certificate</span></span>

<span data-ttu-id="553a6-117">Confie no certificado de desenvolvimento HTTPS:</span><span class="sxs-lookup"><span data-stu-id="553a6-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="553a6-118">Windows</span><span class="sxs-lookup"><span data-stu-id="553a6-118">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="553a6-119">O comando anterior exibe a caixa de diálogo a seguir:</span><span class="sxs-lookup"><span data-stu-id="553a6-119">The preceding command displays the following dialog:</span></span>

![Caixa de diálogo de aviso de segurança](~/getting-started/_static/cert.png)

<span data-ttu-id="553a6-121">Selecione **Sim** se você concordar com confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="553a6-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="553a6-122">macOS</span><span class="sxs-lookup"><span data-stu-id="553a6-122">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="553a6-123">O comando anterior exibe a mensagem a seguir:</span><span class="sxs-lookup"><span data-stu-id="553a6-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="553a6-124">*Foi solicitada confiança no certificado de desenvolvimento HTTPS. Se o certificado já não for confiável, executaremos o seguinte comando:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="553a6-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="553a6-125">Esse comando pode solicitar que você insira sua senha para instalar o certificado no conjunto de chaves do sistema.</span><span class="sxs-lookup"><span data-stu-id="553a6-125">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="553a6-126">Insira sua senha se você concordar em confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="553a6-126">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="553a6-127">Linux</span><span class="sxs-lookup"><span data-stu-id="553a6-127">Linux</span></span>](#tab/linux)

<span data-ttu-id="553a6-128">Para o Subsistema do Windows para Linux, confira [certificado de confiança HTTPS do Subsistema do Windows para Linux](xref:security/enforcing-ssl#wsl).</span><span class="sxs-lookup"><span data-stu-id="553a6-128">For Windows Subsystem for Linux, see [Trust HTTPS certificate from Windows Subsystem for Linux](xref:security/enforcing-ssl#wsl).</span></span>

<span data-ttu-id="553a6-129">Consulte a documentação para sua distribuição do Linux sobre como confiar no certificado de desenvolvimento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="553a6-129">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="553a6-130">Para obter mais informações, confira [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="553a6-130">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="553a6-131">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="553a6-131">Run the app</span></span>

<span data-ttu-id="553a6-132">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="553a6-132">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="553a6-133">Depois que o shell de comando indicar que o aplicativo foi iniciado, navegue até [https://localhost:5001](https://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="553a6-133">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="553a6-134">Clique em **Aceitar** para aceitar a política de privacidade e cookies.</span><span class="sxs-lookup"><span data-stu-id="553a6-134">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="553a6-135">Este aplicativo não armazena informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="553a6-135">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="553a6-136">Editar uma página do Razor</span><span class="sxs-lookup"><span data-stu-id="553a6-136">Edit a Razor page</span></span>

<span data-ttu-id="553a6-137">Abra *Pages/Index.cshtml* e modifique a página com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="553a6-137">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="553a6-138">Navegue até [https://localhost:5001](https://localhost:5001) e verifique se as alterações são exibidas.</span><span class="sxs-lookup"><span data-stu-id="553a6-138">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="553a6-139">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="553a6-139">Next steps</span></span>

<span data-ttu-id="553a6-140">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="553a6-140">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="553a6-141">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="553a6-141">Create a web app project.</span></span>
> * <span data-ttu-id="553a6-142">Confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="553a6-142">Trust the development certificate.</span></span>
> * <span data-ttu-id="553a6-143">Execute o projeto.</span><span class="sxs-lookup"><span data-stu-id="553a6-143">Run the project.</span></span>
> * <span data-ttu-id="553a6-144">Faça uma alteração.</span><span class="sxs-lookup"><span data-stu-id="553a6-144">Make a change.</span></span>

<span data-ttu-id="553a6-145">Para saber mais sobre o ASP.NET Core, veja o caminho de aprendizado recomendados na introdução:</span><span class="sxs-lookup"><span data-stu-id="553a6-145">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>

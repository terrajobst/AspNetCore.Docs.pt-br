---
title: Introdução ao ASP.NET Core
author: rick-anderson
description: Um tutorial rápido que cria e executa um aplicativo simples Olá, Mundo usando o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2018
uid: getting-started
ms.openlocfilehash: cf9e731f7638687b3f40b42864ef7ee8f5522b39
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284339"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="54b79-103">Tutorial: Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54b79-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="54b79-104">Este tutorial mostra como usar a interface de linha de comando do .NET Core para criar um aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54b79-104">This tutorial shows how to use the .NET Core command-line interface to create an ASP.NET Core web app.</span></span>

<span data-ttu-id="54b79-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="54b79-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="54b79-106">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="54b79-106">Create a web app project.</span></span>
> * <span data-ttu-id="54b79-107">Habilitar o HTTPS local.</span><span class="sxs-lookup"><span data-stu-id="54b79-107">Enable local HTTPS.</span></span>
> * <span data-ttu-id="54b79-108">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="54b79-108">Run the app.</span></span>
> * <span data-ttu-id="54b79-109">Editar uma página do Razor.</span><span class="sxs-lookup"><span data-stu-id="54b79-109">Edit a Razor page.</span></span>

<span data-ttu-id="54b79-110">No final, você terá um aplicativo Web de trabalho em execução no seu computador local.</span><span class="sxs-lookup"><span data-stu-id="54b79-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Página inicial do aplicativo Web](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="54b79-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="54b79-112">Prerequisites</span></span>

* [<span data-ttu-id="54b79-113">SDK do .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="54b79-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="54b79-114">Criar um projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="54b79-114">Create a web app project</span></span>

<span data-ttu-id="54b79-115">Abra um shell de comando e insira o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="54b79-115">Open a command shell, and enter the following command:</span></span>

```console
dotnet new webapp -o aspnetcoreapp
```

## <a name="enable-local-https"></a><span data-ttu-id="54b79-116">Habilitar o HTTPS local</span><span class="sxs-lookup"><span data-stu-id="54b79-116">Enable local HTTPS</span></span>

<span data-ttu-id="54b79-117">Confie no certificado de desenvolvimento HTTPS:</span><span class="sxs-lookup"><span data-stu-id="54b79-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="54b79-118">Windows</span><span class="sxs-lookup"><span data-stu-id="54b79-118">Windows</span></span>](#tab/windows)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="54b79-119">O comando anterior exibe a caixa de diálogo a seguir:</span><span class="sxs-lookup"><span data-stu-id="54b79-119">The preceding command displays the following dialog:</span></span>

![Caixa de diálogo de aviso de segurança](_static/cert.png)

<span data-ttu-id="54b79-121">Selecione **Sim** se você concordar com confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="54b79-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="54b79-122">macOS</span><span class="sxs-lookup"><span data-stu-id="54b79-122">macOS</span></span>](#tab/macos)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="54b79-123">O comando anterior exibe a mensagem a seguir:</span><span class="sxs-lookup"><span data-stu-id="54b79-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="54b79-124">*Foi solicitada confiança no certificado de desenvolvimento HTTPS. Se o certificado ainda não for confiável, executaremos o seguinte comando:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span><span class="sxs-lookup"><span data-stu-id="54b79-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span></span>  
<span data-ttu-id="54b79-125">\*Esse comando pode solicitar que você insira sua senha para instalar o certificado no conjunto de chaves do sistema.</span><span class="sxs-lookup"><span data-stu-id="54b79-125">\*This command might prompt you for your password to install the certificate on the system keychain.</span></span>

<span data-ttu-id="54b79-126">Senha:\*</span><span class="sxs-lookup"><span data-stu-id="54b79-126">Password:\*</span></span>

<span data-ttu-id="54b79-127">Insira sua senha se você concordar em confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="54b79-127">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="54b79-128">Linux</span><span class="sxs-lookup"><span data-stu-id="54b79-128">Linux</span></span>](#tab/linux)

<span data-ttu-id="54b79-129">Consulte a documentação para sua distribuição do Linux sobre como confiar no certificado de desenvolvimento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="54b79-129">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

## <a name="run-the-app"></a><span data-ttu-id="54b79-130">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="54b79-130">Run the app</span></span>

<span data-ttu-id="54b79-131">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="54b79-131">Run the following commands:</span></span>

```console
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="54b79-132">Procure [https://localhost:5001](https://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="54b79-132">Browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="54b79-133">Clique em **Aceitar** para aceitar a política de privacidade e cookies.</span><span class="sxs-lookup"><span data-stu-id="54b79-133">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="54b79-134">Este aplicativo não armazena informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="54b79-134">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="54b79-135">Editar uma página do Razor</span><span class="sxs-lookup"><span data-stu-id="54b79-135">Edit a Razor page</span></span>

<span data-ttu-id="54b79-136">Abra *Pages/Index.cshtml* e modifique a página com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="54b79-136">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="54b79-137">Navegue até [https://localhost:5001](https://localhost:5001) e verifique se as alterações são exibidas.</span><span class="sxs-lookup"><span data-stu-id="54b79-137">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="54b79-138">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="54b79-138">Next steps</span></span>

<span data-ttu-id="54b79-139">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="54b79-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="54b79-140">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="54b79-140">Create a web app project.</span></span>
> * <span data-ttu-id="54b79-141">Habilitar o HTTPS local.</span><span class="sxs-lookup"><span data-stu-id="54b79-141">Enable local HTTPS.</span></span>
> * <span data-ttu-id="54b79-142">Execute o projeto.</span><span class="sxs-lookup"><span data-stu-id="54b79-142">Run the project.</span></span>
> * <span data-ttu-id="54b79-143">Faça uma alteração.</span><span class="sxs-lookup"><span data-stu-id="54b79-143">Make a change.</span></span>

<span data-ttu-id="54b79-144">Para saber mais sobre o ASP.NET Core, confira a introdução:</span><span class="sxs-lookup"><span data-stu-id="54b79-144">To learn more about ASP.NET Core, see the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index>

> [!NOTE]
> <span data-ttu-id="54b79-145">Estamos testando a usabilidade de uma nova estrutura proposta para o sumário do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54b79-145">We're testing the usability of a proposed new structure for the ASP.NET Core table of contents.</span></span> <span data-ttu-id="54b79-146">Se você tiver alguns minutos para experimentar um exercício de localização de sete tópicos diferentes no sumário atual ou proposto, [clique aqui para participar do estudo](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).</span><span class="sxs-lookup"><span data-stu-id="54b79-146">If you have a few minutes to try an exercise of finding seven different topics in the current or proposed table of contents, please [click here to participate in the study](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).</span></span>

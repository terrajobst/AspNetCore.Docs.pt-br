---
title: Aquisição de biblioteca do lado do cliente no ASP.NET Core com LibMan
author: scottaddie
description: Saiba como instalar ativos da biblioteca do lado do cliente em um projeto do ASP.NET Core usando o Gerenciador de Bibliotecas (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: 87987446b7f2c625da90951510e697e06569ba36
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664664"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a><span data-ttu-id="0cf71-103">Aquisição de biblioteca do lado do cliente no ASP.NET Core com LibMan</span><span class="sxs-lookup"><span data-stu-id="0cf71-103">Client-side library acquisition in ASP.NET Core with LibMan</span></span>

<span data-ttu-id="0cf71-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="0cf71-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="0cf71-105">O Gerenciador de Bibliotecas (LibMan) é uma ferramenta leve de aquisição de bibliotecas do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="0cf71-105">Library Manager (LibMan) is a lightweight, client-side library acquisition tool.</span></span> <span data-ttu-id="0cf71-106">O LibMan baixa bibliotecas e estruturas populares do sistema de arquivos ou de uma [CDN (rede de distribuição de conteúdo)](https://wikipedia.org/wiki/Content_delivery_network).</span><span class="sxs-lookup"><span data-stu-id="0cf71-106">LibMan downloads popular libraries and frameworks from the file system or from a [content delivery network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span></span> <span data-ttu-id="0cf71-107">Os CDNs com suporte incluem [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)e [unpkg](https://unpkg.com/#/).</span><span class="sxs-lookup"><span data-stu-id="0cf71-107">The supported CDNs include [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/), and [unpkg](https://unpkg.com/#/).</span></span> <span data-ttu-id="0cf71-108">Os arquivos de biblioteca selecionados são buscados e colocados no local adequado dentro do projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0cf71-108">The selected library files are fetched and placed in the appropriate location within the ASP.NET Core project.</span></span>

## <a name="libman-use-cases"></a><span data-ttu-id="0cf71-109">Casos de uso do LibMan</span><span class="sxs-lookup"><span data-stu-id="0cf71-109">LibMan use cases</span></span>

<span data-ttu-id="0cf71-110">O LibMan oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="0cf71-110">LibMan offers the following benefits:</span></span>

* <span data-ttu-id="0cf71-111">Somente os arquivos de biblioteca necessários são baixados.</span><span class="sxs-lookup"><span data-stu-id="0cf71-111">Only the library files you need are downloaded.</span></span>
* <span data-ttu-id="0cf71-112">Ferramentas adicionais, tais como [Node.js](https://nodejs.org), [npm](https://www.npmjs.com) e [WebPack](https://webpack.js.org), não são necessárias para adquirir um subconjunto de arquivos em uma biblioteca.</span><span class="sxs-lookup"><span data-stu-id="0cf71-112">Additional tooling, such as [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), and [WebPack](https://webpack.js.org), isn't necessary to acquire a subset of files in a library.</span></span>
* <span data-ttu-id="0cf71-113">Arquivos podem ser colocados em um local específico sem recorrer a tarefas de build ou à cópia manual de arquivos.</span><span class="sxs-lookup"><span data-stu-id="0cf71-113">Files can be placed in a specific location without resorting to build tasks or manual file copying.</span></span>

<span data-ttu-id="0cf71-114">Para obter mais informações sobre os benefícios do LibMan, assista a [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s) (Desenvolvimento de front-end da Web moderno no Visual Studio 2017: segmento LibMan).</span><span class="sxs-lookup"><span data-stu-id="0cf71-114">For more information about LibMan's benefits, watch [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span></span>

<span data-ttu-id="0cf71-115">O LibMan não é um sistema de gerenciamento de pacotes.</span><span class="sxs-lookup"><span data-stu-id="0cf71-115">LibMan isn't a package management system.</span></span> <span data-ttu-id="0cf71-116">Se você já está usando um gerenciador de pacotes, assim como o npm ou [yarn](https://yarnpkg.com), continue fazendo isso.</span><span class="sxs-lookup"><span data-stu-id="0cf71-116">If you're already using a package manager, such as npm or [yarn](https://yarnpkg.com), continue doing so.</span></span> <span data-ttu-id="0cf71-117">O LibMan não foi desenvolvido para substituir essas ferramentas.</span><span class="sxs-lookup"><span data-stu-id="0cf71-117">LibMan wasn't developed to replace those tools.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0cf71-118">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0cf71-118">Additional resources</span></span>

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="0cf71-119">Repositório do GitHub do LibMan</span><span class="sxs-lookup"><span data-stu-id="0cf71-119">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)

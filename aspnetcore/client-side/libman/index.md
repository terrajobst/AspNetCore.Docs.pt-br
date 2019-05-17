---
title: Aquisição de biblioteca do lado do cliente no ASP.NET Core com LibMan
author: scottaddie
description: Saiba como instalar ativos da biblioteca do lado do cliente em um projeto do ASP.NET Core usando o Gerenciador de Bibliotecas (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: a6ff0cc3342cfac74739387aa17046ed5050232f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64882201"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>Aquisição de biblioteca do lado do cliente no ASP.NET Core com LibMan

Por [Scott Addie](https://twitter.com/Scott_Addie)

O Gerenciador de Bibliotecas (LibMan) é uma ferramenta leve de aquisição de bibliotecas do lado do cliente. O LibMan baixa bibliotecas e estruturas populares do sistema de arquivos ou de uma [CDN (rede de distribuição de conteúdo)](https://wikipedia.org/wiki/Content_delivery_network). As CDNs compatíveis incluem [CDNJS](https://cdnjs.com/) e [unpkg](https://unpkg.com/#/). Os arquivos de biblioteca selecionados são buscados e colocados no local adequado dentro do projeto do ASP.NET Core.

## <a name="libman-use-cases"></a>Casos de uso do LibMan

O LibMan oferece os seguintes benefícios:

* Somente os arquivos de biblioteca necessários são baixados.
* Ferramentas adicionais, tais como [Node.js](https://nodejs.org), [npm](https://www.npmjs.com) e [WebPack](https://webpack.js.org), não são necessárias para adquirir um subconjunto de arquivos em uma biblioteca.
* Arquivos podem ser colocados em um local específico sem recorrer a tarefas de build ou à cópia manual de arquivos.

Para obter mais informações sobre os benefícios do LibMan, assista a [Modern front-end web development in Visual Studio 2017: LibMan segment (Desenvolvimento de front-end da Web moderno no Visual Studio 2017: segmento LibMan)](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).

O LibMan não é um sistema de gerenciamento de pacotes. Se você já está usando um gerenciador de pacotes, assim como o npm ou [yarn](https://yarnpkg.com), continue fazendo isso. O LibMan não foi desenvolvido para substituir essas ferramentas.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [Repositório do GitHub do LibMan](https://github.com/aspnet/LibraryManager)

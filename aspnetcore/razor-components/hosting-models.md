---
title: Modelos de hospedagem de componentes do Razor
author: guardrex
description: Entenda Blazor do lado do cliente e componentes do lado do servidor ASP.NET Core Razor modelos de hospedagem.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/28/2019
uid: razor-components/hosting-models
ms.openlocfilehash: 8ed70117c94bf1a3e4c208f70310bbf0473bae44
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515341"
---
# <a name="razor-components-hosting-models"></a>Modelos de hospedagem de componentes do Razor

Por [Daniel Roth](https://github.com/danroth27)

Componentes do Razor é uma estrutura de web projetada para ser executado lado do cliente no navegador em um [WebAssembly](http://webassembly.org/)-com base em tempo de execução do .NET (*Blazor*) ou do servidor no ASP.NET Core (*Razor do ASP.NET Core Componentes*). Independentemente dos modelos de hospedagem de modelo, o aplicativo e o componente *permanecem os mesmos*. Este artigo discute os modelos de hospedagem disponíveis:

* [Blazor no lado do cliente](#client-side-hosting-model)
* [Componentes Razor ASP.NET Core no lado do servidor](#server-side-hosting-model)

## <a name="client-side-hosting-model"></a>Modelo de hospedagem do lado do cliente

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

O principal modelo de hospedagem para Blazor é o lado do cliente em execução no navegador WebAssembly. O aplicativo do Blazor, suas dependências e o tempo de execução do .NET são baixados no navegador. O aplicativo é executado diretamente no thread da interface do usuário do navegador. Atualizações da interface do usuário e a manipulação de eventos ocorrerem dentro do mesmo processo. Os ativos do aplicativo são implantados como arquivos estáticos em um servidor web ou serviço capaz de servir conteúdo estático para os clientes.

![Blazor lado do cliente: O aplicativo Blazor é executado em um thread de interface do usuário dentro do navegador.](hosting-models/_static/client-side.png)

Para criar um aplicativo de Blazor usando o modelo de hospedagem do lado do cliente, use os seguintes modelos:

* **Blazor** ([blazor de novo dotnet](/dotnet/core/tools/dotnet-new)) &ndash; implantado como um conjunto de arquivos estáticos.
* **Blazor (ASP.NET Core hospedados)** ([blazorhosted de novo dotnet](/dotnet/core/tools/dotnet-new)) &ndash; hospedadas por um servidor do ASP.NET Core. O aplicativo ASP.NET Core atua como o aplicativo Blazor para clientes. O aplicativo de Blazor do lado do cliente pode interagir com o servidor pela rede usando chamadas à API web ou [SignalR](xref:signalr/introduction).

Os modelos incluem o *components.webassembly.js* script que manipula:

* Baixando o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.
* Inicialização de tempo de execução para executar o aplicativo.

O modelo de hospedagem do lado do cliente oferece vários benefícios. Blazor do lado do cliente:

* Não tenha nenhuma dependência do lado do servidor de .NET.
* Aproveita totalmente funções e recursos de cliente.
* Descarregamentos de trabalham do servidor para o cliente.
* Dá suporte a cenários offline.

Há desvantagens para hospedagem do lado do cliente. Blazor do lado do cliente:

* Restringe o aplicativo para os recursos do navegador.
* Requer hardware compatível com cliente e software (por exemplo, WebAssembly suporte).
* Tem um tamanho maior de download e o aplicativo mais do que o tempo de carregamento.
* Tem menos mature .NET runtime e o suporte de ferramentas (por exemplo, as limitações no [.NET Standard](/dotnet/standard/net-standard) suporte e depuração).

## <a name="server-side-hosting-model"></a>Modelo de hospedagem do lado do servidor

Com o modelo de hospedagem de servidor de componentes do ASP.NET Core Razor, o aplicativo é executado no servidor de dentro de um aplicativo ASP.NET Core. Atualizações da interface do usuário, a manipulação de eventos e chamadas de JavaScript são tratadas por um [SignalR](xref:signalr/introduction) conexão.

![ASP.NET Core Razor componentes do servidor: O navegador interage com o aplicativo (hospedado dentro de um aplicativo ASP.NET Core) no servidor ao longo de uma conexão SignalR.](hosting-models/_static/server-side.png)

Para criar um aplicativo de componentes do Razor usando o modelo de hospedagem do lado do servidor, use o ASP.NET Core **componentes do Razor** modelo ([razorcomponents de novo dotnet](/dotnet/core/tools/dotnet-new)). O aplicativo ASP.NET Core hospeda o aplicativo do lado do servidor de componentes do Razor e configura o ponto de extremidade do SignalR em que os clientes se conectam. O aplicativo ASP.NET Core faz referência do aplicativo `Startup` classe a ser adicionada:

* Serviços de componentes do Razor do lado do servidor.
* O aplicativo para o pipeline de tratamento de solicitação.

[!code-csharp[](hosting-models/samples_snapshot/Startup.cs?highlight=5,27)]

O *components.server.js* script&dagger; estabelece a conexão de cliente. É responsabilidade do aplicativo para manter e restaurar o estado do aplicativo conforme necessário (por exemplo, no caso de uma conexão de rede perdidas).

O modelo de hospedagem do lado do servidor oferece vários benefícios. Componentes do lado do servidor Razor:

* Ter um tamanho de aplicativo significativamente menor do que um aplicativo do lado do cliente Blazor e carregados com mais rapidez.
* Podem tirar total proveito dos recursos do servidor, incluindo o uso de quaisquer APIs compatíveis do .NET Core.
* Executados no .NET Core no servidor, portanto, o .NET existentes das ferramentas, como depuração, funciona conforme o esperado.
* Funciona com clientes finos (por exemplo, os navegadores que não dão suporte a recursos e WebAssembly restrita dispositivos).
* .NET /C# base de código, incluindo o código de componente do aplicativo, não é atendido aos clientes.

Há desvantagens para o lado do servidor de hospedagem. Componentes do lado do servidor Razor:

* Ter latência mais alta: Cada interação do usuário envolve um salto de rede.
* Não há suporte offline da oferta: Se a conexão de cliente falhar, o aplicativo deixará de funcionar.
* Reduziram escalabilidade: O servidor deve gerenciar várias conexões de cliente e manipular o estado do cliente.
* Exige um servidor do ASP.NET Core para servir o aplicativo. Implantação sem um servidor (por exemplo, de uma CDN) não é possível.

&dagger;O *components.server.js* script é publicado para o seguinte caminho: *bin / {depurar | Versão} / {TARGET FRAMEWORK} /publish/ {nome do aplicativo}. Aplicativo/dist/estrutura de _*.

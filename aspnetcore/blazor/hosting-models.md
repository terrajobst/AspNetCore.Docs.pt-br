---
title: Modelos de Hospedagem de Blazor ASP.NET Core
author: guardrex
description: Entenda Blazor Webassembly e modelos de hospedagem do Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: e6ce2be53c35268854e0e8d408b649a8c6ef497e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658315"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>Modelos de Hospedagem de Blazor ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor é uma estrutura da Web criada para executar o lado do cliente no navegador em um tempo de execução .NET baseado em [Webassembly](https://webassembly.org/)( *Blazor Webassembly*) ou no lado do servidor no ASP.NET Core ( *Blazor Server*). Independentemente do modelo de hospedagem, os modelos de aplicativo e componente *são os mesmos*.

Para criar um projeto para os modelos de hospedagem descritos neste artigo, consulte <xref:blazor/get-started>.

Para configuração avançada, consulte <xref:blazor/hosting-model-configuration>.

## <a name="opno-locblazor-webassembly"></a>Blazor Webassembly

O modelo de hospedagem principal para Blazor está executando o lado do cliente no navegador no Webassembly. O aplicativo Blazor, suas dependências e o tempo de execução do .NET são baixados para o navegador. O aplicativo é executado diretamente no thread da interface do usuário do navegador. As atualizações da interface do usuário e o tratamento de eventos ocorrem no mesmo processo. Os ativos do aplicativo são implantados como arquivos estáticos em um servidor Web ou serviço capaz de fornecer conteúdo estático aos clientes.

![Blazor Webassembly: o aplicativo Blazor é executado em um thread de interface do usuário dentro do navegador.](hosting-models/_static/blazor-webassembly.png)

Para criar um aplicativo Blazor usando o modelo de hospedagem do lado do cliente, use o modelo de **aplicativo Webassembly doBlazor** ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).

Depois de selecionar o modelo de **aplicativo WebassemblyBlazor** , você tem a opção de configurar o aplicativo para usar um back-end ASP.NET Core marcando a caixa de seleção **ASP.NET Core hospedado** ([dotnet New blazorwasm – hospedado](/dotnet/core/tools/dotnet-new)). O aplicativo ASP.NET Core serve o aplicativo Blazor aos clientes. O aplicativo Webassembly Blazor pode interagir com o servidor pela rede usando chamadas de API Web ou [SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>).

Os modelos incluem o script de `blazor.webassembly.js` que manipula:

* Baixar o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.
* Inicialização do tempo de execução para executar o aplicativo.

O modelo de hospedagem Webassembly Blazor oferece vários benefícios:

* Não há nenhuma dependência do lado do servidor .NET. O aplicativo está totalmente funcionando depois de baixado para o cliente.
* Recursos e funcionalidades do cliente são totalmente aproveitados.
* O trabalho é descarregado do servidor para o cliente.
* Um servidor Web ASP.NET Core não é necessário para hospedar o aplicativo. Cenários de implantação sem servidor são possíveis (por exemplo, servindo o aplicativo de uma CDN).

Há desvantagens em Blazor Hospedagem de Webassembly:

* O aplicativo é restrito aos recursos do navegador.
* Hardware e software de cliente com capacidade (por exemplo, suporte a Webassembly) é necessário.
* O tamanho do download é maior e os aplicativos demoram mais para serem carregados.
* O suporte ao tempo de execução e às ferramentas do .NET é menos maduro. Por exemplo, existem limitações em [.net Standard](/dotnet/standard/net-standard) suporte e depuração.

## <a name="opno-locblazor-server"></a>Servidor de Blazor

Com o modelo de hospedagem do servidor Blazor, o aplicativo é executado no servidor de dentro de um aplicativo ASP.NET Core. As atualizações de interface do usuário, manipulação de eventos e chamadas JavaScript são manipuladas por uma conexão [SignalR](xref:signalr/introduction) .

![o navegador interage com o aplicativo (hospedado dentro de um aplicativo ASP.NET Core) no servidor por uma conexão SignalR.](hosting-models/_static/blazor-server.png)

Para criar um aplicativo Blazor usando o modelo de hospedagem do Blazor Server, use o modelo de **aplicativo ASP.NET CoreBlazor Server** ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)). O aplicativo ASP.NET Core hospeda o aplicativo Blazor Server e cria o ponto de extremidade SignalR onde os clientes se conectam.

O aplicativo ASP.NET Core referencia a classe `Startup` do aplicativo a ser adicionada:

* Serviços do lado do servidor.
* O aplicativo para o pipeline de tratamento de solicitação.

O script de `blazor.server.js`&dagger; estabelece a conexão do cliente. É responsabilidade do aplicativo persistir e restaurar o estado do aplicativo, conforme necessário (por exemplo, no caso de uma conexão de rede perdida).

O modelo de hospedagem do Blazor Server oferece vários benefícios:

* O tamanho do download é significativamente menor do que um aplicativo Webassembly Blazor e o aplicativo é carregado muito mais rapidamente.
* O aplicativo aproveita totalmente os recursos do servidor, incluindo o uso de qualquer API compatível com o .NET Core.
* O .NET Core no servidor é usado para executar o aplicativo, portanto, as ferramentas .NET existentes, como depuração, funcionam conforme o esperado.
* Há suporte para clientes finos. Por exemplo, os aplicativos do Blazor Server funcionam com navegadores que não dão suporte ao Webassembly e a dispositivos com restrição de recursos.
* A base .NET/C# código do aplicativo, incluindo o código de componente do aplicativo, não é servida aos clientes.

Há desvantagens em Blazor Hospedagem de servidor:

* A latência mais alta geralmente existe. Cada interação do usuário envolve um salto de rede.
* Não há suporte offline. Se a conexão do cliente falhar, o aplicativo para de funcionar.
* A escalabilidade é desafiadora para aplicativos com muitos usuários. O servidor deve gerenciar várias conexões de cliente e manipular o estado do cliente.
* Um servidor de ASP.NET Core é necessário para atender ao aplicativo. Cenários de implantação sem servidor não são possíveis (por exemplo, servir o aplicativo de uma CDN).

&dagger;o script de `blazor.server.js` é servido por meio de um recurso inserido na estrutura compartilhada ASP.NET Core.

### <a name="comparison-to-server-rendered-ui"></a>Comparação com a interface do usuário renderizada pelo servidor

Uma maneira de entender Blazor aplicativos de servidor é entender como ele difere dos modelos tradicionais para renderizar a interface do usuário em aplicativos ASP.NET Core usando exibições do Razor ou Razor Pages. Ambos os modelos usam a linguagem Razor para descrever o conteúdo HTML, mas são significativamente diferentes na forma como a marcação é renderizada.

Quando uma página Razor ou exibição é renderizada, cada linha do código do Razor emite HTML na forma de texto. Após a renderização, o servidor descarta a página ou a instância de exibição, incluindo qualquer estado produzido. Quando outra solicitação para a página ocorre, por exemplo, quando a validação do servidor falha e o resumo de validação é exibido:

* A página inteira é reprocessada para o texto HTML novamente.
* A página é enviada ao cliente.

Um aplicativo Blazor é composto por elementos reutilizáveis da interface do usuário chamada *componentes*. Um componente contém C# código, marcação e outros componentes. Quando um componente é renderizado, Blazor produz um grafo dos componentes incluídos de forma semelhante a um XML (Modelo de Objeto do Documento) ou HTML (DOM). Esse grafo inclui o estado do componente mantido em Propriedades e campos. Blazor avalia o grafo de componente para produzir uma representação binária da marcação. O formato binário pode ser:

* Transformado em texto HTML (durante o pré-processamento&dagger;).
* Usado para atualizar a marcação com eficiência durante a renderização regular.

&dagger;o *pré-processamento* &ndash; o componente do Razor solicitado é compilado no servidor em HTML estático e enviado ao cliente, onde ele é renderizado para o usuário. Depois que a conexão é feita entre o cliente e o servidor, os elementos preprocessados estáticos do componente são substituídos por elementos interativos. O pré-processamento faz com que o aplicativo sinta-se mais responsivo para o usuário.

Uma atualização de interface do usuário no Blazor é disparada por:

* Interação do usuário, como selecionar um botão.
* Gatilhos de aplicativo, como um temporizador.

O grafo é rerenderizado e uma *comparação* de interface do usuário (diferença) é calculada. Essa diferença é o menor conjunto de edições DOM necessárias para atualizar a interface do usuário no cliente. A comparação é enviada ao cliente em um formato binário e aplicada pelo navegador.

Um componente é descartado depois que o usuário navega para fora dele no cliente. Embora um usuário esteja interagindo com um componente, o estado do componente (serviços, recursos) deve ser mantido na memória do servidor. Como o estado de muitos componentes pode ser mantido pelo servidor simultaneamente, o esgotamento de memória é uma preocupação que deve ser resolvida. Para obter orientação sobre como criar um aplicativo do Blazor Server para garantir o melhor uso da memória do servidor, consulte <xref:security/blazor/server>.

### <a name="circuits"></a>Circuitos

Um aplicativo do Blazor Server é criado sobre [ASP.NET Core SignalR](xref:signalr/introduction). Cada cliente se comunica com o servidor por meio de uma ou mais conexões SignalR chamadas de *circuito*. Um circuito é A abstração Blazorde SignalR conexões que podem tolerar interrupções de rede temporárias. Quando um cliente Blazor vê que a conexão SignalR está desconectada, ele tenta se reconectar ao servidor usando uma nova conexão SignalR.

Cada tela do navegador (guia do navegador ou iframe) que está conectada a um aplicativo do Blazor Server usa uma conexão SignalR. Essa é ainda outra distinção importante em comparação com os aplicativos típicos renderizados pelo servidor. Em um aplicativo renderizado pelo servidor, a abertura do mesmo aplicativo em várias telas do navegador geralmente não se traduz em demandas de recursos adicionais no servidor. Em um aplicativo do Blazor Server, cada tela do navegador requer um circuito separado e instâncias separadas do estado do componente a serem gerenciadas pelo servidor.

Blazor considera fechar uma guia do navegador ou navegar até uma URL externa um encerramento *normal* . No caso de um encerramento normal, o circuito e os recursos associados são lançados imediatamente. Um cliente também pode se desconectar não normalmente, por exemplo, devido a uma interrupção de rede. Blazor Server armazena circuitos desconectados para um intervalo configurável para permitir que o cliente se reconecte.

Blazor Server permite que o código defina um *manipulador de circuito*, que permite a execução de código em alterações no estado do circuito de um usuário. Para obter mais informações, consulte <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Latência da interface do usuário

A latência da interface do usuário é o tempo que leva de uma ação iniciada até a hora em que a interface do usuário é atualizada. Valores menores para latência de interface do usuário são imperativos para um aplicativo se sentir responsivo a um usuário. Em um aplicativo do Blazor Server, cada ação é enviada ao servidor, processada e uma comparação da interface do usuário é enviada de volta. Consequentemente, a latência da interface do usuário é a soma da latência da rede e a latência do servidor no processamento da ação.

Para um aplicativo de linha de negócios limitado a uma rede corporativa privada, o efeito sobre as percepções do usuário de latência devido à latência da rede geralmente é imperceptível. Para um aplicativo implantado pela Internet, a latência pode ser perceptível para os usuários, especialmente se os usuários forem amplamente distribuídos geograficamente.

O uso de memória também pode contribuir para a latência do aplicativo. O aumento do uso da memória resulta em uma coleta de lixo frequente ou na paginação da memória no disco, e conseqüentemente aumenta a latência da interface do usuário. Para obter mais informações, consulte <xref:security/blazor/server>.

os aplicativos do Blazor Server devem ser otimizados para minimizar a latência da interface do usuário ao reduzir a latência de rede e o uso de memória Para obter uma abordagem para medir a latência de rede, consulte <xref:host-and-deploy/blazor/server#measure-network-latency>. Para obter mais informações sobre SignalR e Blazor, consulte:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>Conexão com o servidor

os aplicativos do Blazor Server exigem uma conexão de SignalR ativa com o servidor. Se a conexão for perdida, o aplicativo tentará se reconectar ao servidor. Desde que o estado do cliente ainda esteja na memória, a sessão do cliente será retomada sem perder o estado.

Um aplicativo do Blazor Server é renderizado em resposta à primeira solicitação do cliente, que configura o estado da interface do usuário no servidor. Quando o cliente tenta criar uma conexão SignalR, o cliente deve se reconectar ao mesmo servidor. os aplicativos do Blazor Server que usam mais de um servidor back-end devem implementar *sessões adesivas* para conexões de SignalR.

É recomendável usar o [serviço de SignalR do Azure](/azure/azure-signalr) para aplicativos do Blazor Server. O serviço permite escalar verticalmente um aplicativo do Blazor Server para um grande número de conexões de SignalR simultâneas. As sessões adesivas são habilitadas para o serviço de SignalR do Azure definindo a opção `ServerStickyMode` do serviço ou o valor de configuração como `Required`. Para obter mais informações, consulte <xref:host-and-deploy/blazor/server#signalr-configuration>.

Ao usar o IIS, as sessões adesivas são habilitadas com Application Request Routing. Para obter mais informações, consulte [balanceamento de carga http usando Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/hosting-model-configuration>
* <xref:tutorials/signalr-blazor-webassembly>

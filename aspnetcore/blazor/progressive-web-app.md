---
title: Crie aplicativos Web progressivos com ASP.NET Core Blazor Webassembly
author: guardrex
description: Saiba como criar PWAs (aplicativos Web progressivos) baseados em Blazor, aplicativos Web que usam recursos de navegador modernos para se comportarem como aplicativos da área de trabalho.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: blazor/progressive-web-app
ms.openlocfilehash: f1c1ce50f20bf579e67e1d4eb02cc7d9d681e354
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083716"
---
# <a name="build-progressive-web-applications-with-aspnet-core-opno-locblazor-webassembly"></a>Crie aplicativos Web progressivos com ASP.NET Core Blazor Webassembly

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Um aplicativo Web progressivo (PWA) é um aplicativo baseado na Web que usa APIs e recursos modernos de navegador para se comportar como um aplicativo de área de trabalho. Esses recursos podem incluir:

* Trabalhando offline e sempre carregando instantaneamente, independentemente da velocidade da rede
* Ser capaz de executar em sua própria janela de aplicativo, não apenas em uma janela de navegador
* Sendo iniciado no menu Iniciar do sistema operacional do host (SO), no Dock ou na tela inicial
* Receber notificações por push de um servidor de back-end, mesmo que o usuário não esteja usando o aplicativo
* Atualizando automaticamente em segundo plano

Um usuário pode primeiro descobrir e usar o aplicativo em seu navegador da Web, como qualquer outro aplicativo de página única (SPA), depois progredir posteriormente para instalá-lo em seu sistema operacional e habilitar notificações por push. É por isso que usamos o termo *progressivo*.

Blazor Webassembly é uma plataforma de aplicativo Web baseada em padrões verdadeira, para que possa usar qualquer API de navegador, incluindo as APIs do PWA necessárias para os recursos listados acima. Ele pode trabalhar offline, assim como qualquer outra tecnologia da Web do lado do cliente.

## <a name="pwa-template"></a>Modelo do PWA

Ao criar um novo aplicativo Webassembly Blazor, você terá a opção de adicionar recursos do PWA. No Visual Studio, a opção é fornecida como uma caixa de seleção na caixa de diálogo criação do projeto:

![image](https://user-images.githubusercontent.com/1101362/76207411-a6b54200-61f5-11ea-9dfc-6acd87a91428.png)

Se você estiver criando o projeto na linha de comando, poderá usar o sinalizador `--pwa`. Por exemplo,

```dotnetcli
dotnet new blazorwasm --pwa -o MyNewProject
```

Em ambos os casos, você está livre para combinar isso com a opção "ASP.NET Core hospedado", se desejar, mas não precisa fazer isso. Os recursos do PWA são independentes do modelo de hospedagem.

## <a name="installation-and-app-manifest"></a>Instalação e manifesto do aplicativo

Ao visitar um aplicativo criado usando a opção de modelo do PWA, os usuários têm a opção de instalar o aplicativo no menu Iniciar, no Dock ou na tela inicial do sistema operacional.

A maneira como essa opção é apresentada depende do navegador do usuário. Por exemplo, ao usar navegadores baseados em desktop Chromium, como Edge ou Chrome, um botão *Adicionar* aparece na barra de URL:

![image](https://user-images.githubusercontent.com/1101362/76208127-f7796a80-61f6-11ea-8aea-7fba704be787.png)

No iOS, os visitantes podem instalar o PWA usando o botão *compartilhar* do Safari e sua opção *Adicionar à homescreen* . No Chrome para Android, os usuários devem tocar no botão de *menu* no canto superior direito e, em seguida, escolher *Adicionar à tela inicial*.

Uma vez instalado, o aplicativo aparece em sua própria janela, sem nenhuma barra de endereço.

![image](https://user-images.githubusercontent.com/1101362/76208588-e2e9a200-61f7-11ea-85e1-8c3fc849b252.png)

Para personalizar o título, o esquema de cores, o ícone ou outros detalhes da janela, consulte o arquivo `manifest.json` no diretório *wwwroot* do seu projeto. O esquema desse arquivo é definido por padrões da Web. Para obter a documentação detalhada, consulte https://developer.mozilla.org/docs/Web/Manifest.

## <a name="offline-support"></a>Suporte offline

Por padrão, os aplicativos criados usando a opção de modelo do PWA têm suporte para execução offline. Um usuário deve primeiro visitar o aplicativo enquanto estiver online, então o navegador baixará automaticamente e armazenará em cache todos os recursos necessários para operar offline.

> [!IMPORTANT]
> O suporte offline só está habilitado para aplicativos *publicados* . Ele não é habilitado durante o desenvolvimento. Isso ocorre porque ele interferiria com o ciclo de desenvolvimento normal de fazer alterações e testá-las.

> [!WARNING]
> Se você pretende enviar um PWA habilitado para offline, há [vários avisos e advertências importantes](#caveats-for-offline-pwas) que você precisa entender. Elas são inerentes a PWAs offline e não específicas a Blazor. Certifique-se de ler e entender essas advertências antes de fazer suposições sobre como seu aplicativo habilitado para offline funcionará.

Para ver como o suporte offline funciona, primeiro [Publique seu aplicativo](https://docs.microsoft.com/aspnet/core/host-and-deploy/blazor/?view=aspnetcore-3.1&tabs=visual-studio#publish-the-app)e hospede-o em um servidor com suporte para https. Ao visitar o aplicativo, você poderá abrir as ferramentas de desenvolvimento do navegador e verificar se um *trabalhador de serviço* está registrado para o host:

![image](https://user-images.githubusercontent.com/1101362/76210294-bd5e9780-61fb-11ea-9869-65c55c62803d.png)

Além disso, se você recarregar a página, na guia *rede* , você verá que todos os recursos necessários para carregar sua página estão sendo recuperados do *trabalho de serviço* ou do *cache de memória*:

![image](https://user-images.githubusercontent.com/1101362/76210472-1d553e00-61fc-11ea-84c6-291644df709e.png)

Isso mostra que o navegador não depende do acesso à rede para carregar seu aplicativo. Para verificar isso, você pode desligar o servidor Web ou instruir o navegador para simular o modo offline:

![image](https://user-images.githubusercontent.com/1101362/76210556-47a6fb80-61fc-11ea-9d12-20a8f6528744.png)

Agora, mesmo sem acesso ao seu servidor Web, você deve ser capaz de recarregar a página e ver que seu aplicativo ainda é carregado e executado. Da mesma forma, mesmo se você simular uma conexão de rede muito lenta, sua página ainda será carregada imediatamente, pois é carregada independentemente da rede.

### <a name="service-worker"></a>Trabalhador do serviço

O suporte offline é obtido usando um trabalhador de serviço. Este é um padrão da Web, não específico para Blazor. Para obter a documentação sobre trabalhadores de serviço, consulte https://developer.mozilla.org/docs/Web/API/Service_Worker_API. Para saber mais sobre padrões de uso comuns para trabalhadores de serviço, confira o artigo excelente [do ciclo de vida do trabalho do serviço](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

o modelo do PWA do Blazorproduz dois arquivos de trabalho do serviço:

* *wwwroot/Service-Worker. js*, que é usado durante o desenvolvimento
* *wwwroot/Service-Worker. published. js*, que é usado quando o aplicativo é publicado

Se você quiser compartilhar lógica entre esses dois arquivos, considere adicionar um terceiro arquivo JavaScript para manter a lógica comum e usar [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) para carregar essa lógica em ambos os arquivos.

#### <a name="cache-first-fetch-strategy"></a>Estratégia de busca de cache-primeiro

O trabalho de serviço interno *Service-Worker. published. js* resolve solicitações usando uma estratégia *de cache-First* . Isso significa que ele sempre prefere retornar o conteúdo armazenado em cache, se disponível, independentemente de o usuário ter acesso à rede ou se o conteúdo mais recente está disponível no servidor.

Há dois motivos para isso ser valioso:

* **Ele garante a confiabilidade.** O acesso à rede não é um estado booliano. Um usuário não está simplesmente "online" ou "offline". Na realidade, o dispositivo do usuário pode acreditar que está online, mas a rede pode ser tão lenta quanto ser impraticável de esperar. Ou a rede pode estar retornando resultados inválidos para determinadas URLs, como quando há um portal WIFI cativo que está atualmente bloqueando ou redirecionando determinadas solicitações. É por isso que a API de `navigator.onLine` do navegador não é confiável e não deve ser dependente.
* **Ele garante a exatidão.** Ao criar um cache de recursos offline, o trabalho de serviço usa o hash de conteúdo para garantir que ele tenha buscado um instantâneo completo e autoconsistente de recursos em um único instante no tempo. Esse cache é usado como uma unidade atômica. Considerando isso, não há nenhum ponto de solicitar aos recursos mais recentes da rede, pois as únicas versões que você deseja são aquelas que você já armazenou em cache. Qualquer outro risco de inconsistência e incompatibilidade (por exemplo, tentar usar versões de assemblies do .NET que não foram compiladas juntas).

#### <a name="background-updates"></a>Atualizações em segundo plano

Como um modelo mental, você pode considerar um PWA offline como se comportando como um aplicativo móvel que pode ser instalado. Ele sempre é iniciado imediatamente, independentemente da conectividade de rede, mas a lógica do aplicativo instalado vem de um instantâneo pontual que pode não ser a versão mais recente.

O modelo do Blazor PWA produz aplicativos que tentam se atualizar automaticamente em segundo plano sempre que o usuário visita e tem uma conexão de rede em funcionamento. A maneira como isso funciona é a seguinte:

* Durante a compilação, seu projeto gera um *manifesto de ativos de trabalho de serviço*. Por padrão, isso é chamado de *Service-Worker-assets. js*. Isso lista todos os recursos estáticos que seu aplicativo precisa para funcionar offline, como assemblies .NET, arquivos JavaScript, CSS, etc., incluindo seus hashes de conteúdo. Essa lista é carregada pelo seu trabalhador de serviço para que ele saiba quais recursos armazenar em cache.
* Cada vez que o usuário visita seu aplicativo, o navegador solicita novamente *Service-Worker. js* e *Service-Worker-assets. js* em segundo plano. Se o servidor retornar o conteúdo alterado para qualquer um desses arquivos (comparado byte a byte com o trabalho do serviço instalado existente), o trabalho do serviço tentará instalar uma nova versão de si mesmo.
* Ao instalar uma nova versão de si mesma, o trabalho de serviço cria um novo cache separado para recursos offline e começa a preenchê-lo com os recursos listados em *Service-Worker-assets. js*. Essa lógica é implementada na função `onInstall` dentro de *Service-Worker. published. js*.
* Se o processo for concluído com êxito (ou seja, todos os recursos forem carregados sem erros e todos os hashes de conteúdo corresponderem), o novo trabalho de serviço entrará em um estado "aguardando ativação". Assim que o usuário fecha seu aplicativo (ou seja, não há guias restantes ou janelas exibindo seu aplicativo), o novo trabalho de serviço se torna "ativo" e será usado para visitas subsequentes ao seu aplicativo. O antigo trabalho de serviço e seu cache são excluídos.
* Se o processo não for concluído com êxito, a nova instância de trabalho do serviço será descartada. O processo de atualização será tentado novamente na próxima visita do usuário, quando espero que eles tenham uma conexão de rede melhor que possa concluir as solicitações.

Você pode personalizar qualquer aspecto desse processo editando a lógica de trabalho do serviço. Nenhuma das opções acima é específica para Blazor, mas é meramente uma sugestão fornecida pela opção de modelo do PWA. Consulte a [documentação do trabalhador do serviço](https://developer.mozilla.org/docs/Web/API/Service_Worker_API.) para obter mais informações.

#### <a name="how-requests-are-resolved"></a>Como as solicitações são resolvidas

Conforme descrito acima, o operador de serviço padrão usa uma estratégia de *cache-First* , o que significa que ele tenta fornecer conteúdo em cache quando disponível. Se não houver nenhum conteúdo armazenado em cache para uma determinada URL, por exemplo, ao solicitar dados de uma API de back-end, o trabalho do serviço voltará a uma solicitação de rede regular que só poderá ser realizada se o servidor estiver acessível. Essa lógica é implementada dentro de `onFetch` em *Service-Worker. published. js*.

Se seus componentes de Blazor dependem da solicitação de dados de APIs de back-end e você deseja fornecer uma experiência de usuário amigável no caso em que tais solicitações falham devido à indisponibilidade da rede, você precisa implementar a lógica em seus componentes. Por exemplo, use `try/catch` em `HttpClient` solicitações.

#### <a name="support-server-rendered-pages"></a>Suporte a páginas renderizadas pelo servidor

Considere o que acontece quando o usuário navega pela primeira vez para uma URL como `/counter` ou qualquer outro link profundo em seu aplicativo. Nesses casos, você não deseja retornar o conteúdo armazenado em cache como `/counter`, mas, em vez disso, precisa do navegador para carregar o conteúdo armazenado em cache como `/index.html` para iniciar seu aplicativo Webassembly Blazor. Essas solicitações iniciais são conhecidas como solicitações de *navegação* (em oposição às solicitações de *subrecurso* para imagens/CSS/etc, ou solicitações de *busca/XHR* para dados de API).

O operador de serviço padrão contém uma lógica de caso especial para solicitações de navegação. Ele os resolve retornando o conteúdo armazenado em cache para `/index.html`, independentemente da URL solicitada. Essa lógica é implementada na função `onFetch` dentro de *Service-Worker. published. js*.

Se seu aplicativo tiver determinadas URLs que devem retornar o HTML renderizado pelo servidor (e não atender `/index.html` do cache), você precisará editar a lógica em seu trabalho de serviço. Por exemplo, se todas as URLs que contêm `/Identity/` precisam ser tratadas como solicitações regulares somente online para o servidor, modifique a lógica de `onFetch` *Service-Worker. published. js* . Localize o código a seguir:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Altere o código para o seguinte:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

Se você não fizer isso, independentemente da conectividade de rede, o trabalhador do serviço interceptará solicitações para essas URLs e as resolverá usando `/index.html`.

#### <a name="control-asset-caching"></a>Controlar o cache de ativos

Se o projeto definir uma propriedade do MSBuild chamada `ServiceWorkerAssetsManifest`, as ferramentas de compilação de Blazorgerarão um manifesto de ativos de trabalho do serviço com o nome especificado. O modelo do PWA padrão produz um arquivo de projeto que contém o seguinte:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

O arquivo é colocado no diretório de saída *wwwroot* , para que o navegador possa recuperar esse arquivo solicitando `/service-worker-assets.js`. Para ver o conteúdo, abra *YourProject\bin\Debug\netstandard2.1\wwwroot\service-Worker-assets.js* em um editor de texto. No entanto, não edite o arquivo, pois ele será gerado novamente em cada compilação.

Por padrão, este manifesto lista:

* Todos os recursos gerenciados pelo Blazor, como assemblies .NET e os arquivos de tempo de execução do .NET Webassembly necessários para funcionar offline
* Todos os recursos que serão publicados no diretório *wwwroot* , como imagens, arquivos CSS e arquivos JavaScript. Isso inclui ativos da Web estáticos fornecidos por projetos externos e pacotes NuGet.

Você pode controlar quais desses recursos serão buscados e armazenados em cache pelo trabalhador do serviço editando a lógica em `onInstall` em *Service-Worker. published. js*. Por padrão, ele buscará e armazenará em cache os arquivos que correspondem às extensões de nome de arquivo da Web típicas, como *. html*, *. css*, *. js*, *. WASM*e outros, além de tipos de arquivo específicos para Blazor Webassembly ( *. dll*, *. pdb*).

Se você quiser incluir recursos adicionais que não estão presentes no diretório *wwwroot* , poderá fazer isso definindo entradas extras do grupo de itens do MSBuild. Por exemplo, em seu arquivo de projeto, adicione:

```xml
<ItemGroup>
    <ServiceWorkerAssetsManifestItem
        Include="MyDirectory\AnotherFile.json"
        RelativePath="MyDirectory\AnotherFile.json"
        AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

O `AssetUrl` metadados especifica a URL relativa à base que o navegador deve usar ao buscar o recurso para armazenar em cache. Isso pode ser independente de seu nome de arquivo de origem original no disco.

> [!IMPORTANT]
> Adicionar um `ServiceWorkerAssetsManifestItem` não faz com que o arquivo seja publicado no diretório *wwwroot* . Até você controlar sua saída de publicação separadamente. A `ServiceWorkerAssetsManifestItem` apenas faz com que uma entrada adicional apareça no manifesto de ativos de trabalho do serviço.

## <a name="push-notifications"></a>Notificações por push

Como qualquer outro PWA, um Blazor Webassembly PWA pode receber notificações por push de um servidor de back-end. O servidor pode enviá-los a qualquer momento, mesmo quando o usuário não estiver usando seu aplicativo ativamente (por exemplo, quando um usuário diferente executa uma ação que pode ser relevante).

O mecanismo para enviar uma notificação por push é totalmente independente do Blazor Webassembly, pois ele é implementado pelo servidor de back-end que pode usar qualquer tecnologia. Se você quiser enviar notificações por push de um servidor de ASP.NET Core, considere [usar uma técnica semelhante àquela no workshop de pizza incrivelmente](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).

O mecanismo para receber e exibir uma notificação por push no cliente também é independente do Webassembly Blazor, já que ele é implementado no trabalhador do serviço, que é um arquivo JavaScript. Como exemplo, você pode ver novamente [a abordagem usada no workshop de pizza incrivelmente](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Advertências para PWAs offline

Nem todos os aplicativos devem tentar dar suporte ao uso offline. Ele adiciona uma complexidade significativa, embora nem sempre seja relevante.

O suporte offline geralmente é relevante apenas:

* Se o armazenamento de dados primário for local para o navegador. Por exemplo, ao criar uma interface do usuário para um dispositivo [IOT](https://en.wikipedia.org/wiki/Internet_of_things) que armazena dados em `localStorage` ou [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).

* Se você fizer um trabalho significativo para buscar e armazenar em cache os dados de API de back-end relevantes para cada usuário, eles poderão navegar por ele offline. Se você der suporte à edição, também precisará criar um sistema para controlar as alterações e sincronizá-las com o back-end.

* Se seu objetivo é garantir que o aplicativo seja carregado imediatamente, independentemente das condições de rede. Em seguida, você precisará implementar uma experiência de usuário adequada em relação às solicitações da API de back-end para mostrar o progresso das solicitações e se comportar normalmente quando elas falharem devido à indisponibilidade da rede.

Além disso, o PWAs com capacidade offline precisa lidar com uma variedade de complicações extras. Os desenvolvedores devem se familiarizar com cuidado com as seguintes advertências.

### <a name="offline-support-only-when-published"></a>Suporte offline somente quando publicado

o modelo do PWA do Blazorhabilita o suporte offline somente quando publicado. Isso ocorre porque, durante o desenvolvimento, você normalmente deseja ver cada alteração refletida imediatamente no navegador, sem passar por um processo de atualização em segundo plano.

Portanto, ao criar um aplicativo com capacidade offline, não é suficiente testar seu aplicativo no modo de desenvolvimento. Você deve testar seu aplicativo em seu estado publicado para entender como ele responderá a diferentes condições de rede.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Atualizar a conclusão após a navegação do usuário para fora do aplicativo

As atualizações não são concluídas até que o usuário saia do seu aplicativo em todas as guias. Conforme explicado em [atualizações em segundo plano](#background-updates), depois de implantar uma atualização em seu aplicativo, o navegador buscará os arquivos de trabalho do serviço atualizados e iniciará um processo de atualização.

O que surpreende muitos desenvolvedores é que, mesmo quando essa atualização é concluída, ela **não** tem efeito até que o usuário tenha navegado para fora em todas as guias. **Não** é suficiente atualizar a guia que exibe seu aplicativo, mesmo se for a única guia que exibe seu aplicativo. Até que seu aplicativo seja completamente fechado, o novo trabalhador de serviço permanecerá em um status de "aguardando ativação". **Isso não é específico para Blazor, mas sim um comportamento de plataforma da Web padrão.**

Isso normalmente problemas os desenvolvedores que estão tentando testar atualizações para seu trabalho de serviço ou recursos armazenados em cache offline. Se você fizer check-in das ferramentas de desenvolvimento do navegador, poderá ver algo semelhante ao seguinte:

![image](https://user-images.githubusercontent.com/1101362/76226394-b93f7380-6215-11ea-8572-7d52afee2dd8.png)

Desde que a lista de "clientes" (ou seja, tabulações ou janelas exibindo seu aplicativo) não esteja vazia, o trabalho continuará aguardando. O motivo pelo qual os trabalhadores de serviço fazem isso é garantir a consistência, ou seja, que todos os recursos sejam buscados do mesmo cache atômico.

Ao testar alterações, talvez seja conveniente clicar no link "skipWaiting", conforme mostrado na captura de tela acima, e recarregar a página. Se desejar, você pode automatizar isso para todos os usuários codificando seu operador de serviço para [ignorar a fase de "espera" e ativá-la imediatamente na atualização](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). No entanto, se você fizer isso, estará fazendo a garantia de que os recursos são sempre buscados consistentemente da mesma instância de cache.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Os usuários podem executar qualquer versão histórica do aplicativo

Os desenvolvedores da Web recompensadores esperam que os usuários executem apenas a versão mais recente implantada de seu aplicativo Web, pois isso é normal no modelo de distribuição da Web tradicional. No entanto, um PWA offline primeiro é mais semelhante a um aplicativo móvel nativo, no qual os usuários não estão necessariamente executando a versão mais recente.

Conforme explicado em [atualizações em segundo plano](#background-updates), depois de implantar uma atualização em seu aplicativo, **cada usuário existente continuará a usar uma versão anterior para pelo menos uma visita adicional** (porque a atualização ocorre em segundo plano e não é ativada até que o usuário navegue para fora). Além disso, a versão anterior que está sendo usada não é necessariamente a anterior que você implantou-pode ser *qualquer* versão histórica, dependendo de quando o usuário concluiu a última vez uma atualização.

Isso pode ser um problema se as partes de frontend e back-end do seu aplicativo exigirem contrato sobre o esquema para solicitações de API. Você não deve implantar alterações de esquema de API incompatíveis com versões anteriores até ter certeza de que todos os usuários tenham atualizado ou, pelo menos, impedir que os usuários usem versões mais antigas e incompatíveis do aplicativo. Isso é exatamente como um aplicativo móvel nativo. Se você implantar uma alteração significativa nas APIs do servidor, o aplicativo cliente será interrompido para pessoas que ainda não foram atualizadas.

Se possível, não implante alterações significativas em suas APIs de back-end. Mas se você precisar fazer isso, considere o uso de [APIs de trabalho de serviço padrão](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) , como `ServiceWorkerRegistration`para determinar se o aplicativo está atualizado e, se não, para evitar o uso.

### <a name="interference-with-server-rendered-pages"></a>Interferência com páginas renderizadas pelo servidor

[Conforme descrito acima](#support-server-rendered-pages), se você quiser ignorar o comportamento do trabalhador de serviço de retornar `/index.html` conteúdo para todas as solicitações de navegação, você precisará editar a lógica em seu operador de serviço.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Todos os conteúdos do manifesto de ativos de trabalho de serviço são armazenados em cache por padrão

[Conforme descrito acima](#control-asset-caching), o arquivo *Service-Worker-assets. js* é gerado durante a compilação e lista todos os ativos que o trabalho de serviço deve buscar e armazenar em cache.

Como essa lista, por padrão, inclui tudo emitido para *wwwroot* (incluindo o conteúdo fornecido por pacotes e projetos externos), você deve ter cuidado para não colocar muito conteúdo lá. Se, por exemplo, seu diretório *wwwroot* contiver milhões de imagens, o trabalho de serviço tentará buscar e armazenar em cache todas elas, consumindo largura de banda excessiva e provavelmente não será concluído com êxito.

Você pode implementar uma lógica arbitrária para controlar qual subconjunto do conteúdo do manifesto deve ser buscado e armazenado em cache editando a função `onInstall` em *Service-Worker. published. js*.

### <a name="interaction-with-authentication"></a>Interação com autenticação

É possível usar a opção de modelo do PWA em conjunto com as opções de autenticação. Um PWA compatível com offline também pode dar suporte à autenticação quando o usuário tem conectividade de rede.

No entanto, quando um usuário não tem conectividade de rede, ele não será capaz de autenticar ou obter tokens de acesso. A tentativa de visitar a página "logon" exibirá, por padrão, uma mensagem dizendo "erro de rede".

Dessa forma, seu trabalho é criar um fluxo de interface do usuário que permite que ele faça coisas úteis enquanto estiver offline, sem tentar autenticar ou obter tokens de acesso ou, pelo menos, falhar de forma normal nesses casos. Se isso não for possível em seu aplicativo, talvez você não queira habilitar o suporte offline.

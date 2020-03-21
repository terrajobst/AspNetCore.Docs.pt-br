---
title: Crie aplicativos Web progressivos com ASP.NET Core Blazor Webassembly
author: guardrex
description: Saiba como criar um aplicativo Web progressivo baseado em Blazor(PWA) que usa recursos de navegador modernos para se comportar como um aplicativo de desktop.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/progressive-web-app
ms.openlocfilehash: 53e1c4d043c0e8faf13668989cda1f1245c7157a
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989581"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a>Crie aplicativos Web progressivos com ASP.NET Core Webassembly de mais incrivelmente

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Um aplicativo Web progressivo (PWA) é um aplicativo de página única (SPA) que usa APIs de navegador e recursos modernos para se comportar como um aplicativo de desktop. Webassembly mais robusto é uma plataforma de aplicativo Web do lado do cliente baseada em padrões, para que possa usar qualquer API do navegador, incluindo as APIs do PWA necessárias para os seguintes recursos:

* Trabalhar offline e carregar instantaneamente, independentemente da velocidade da rede.
* Em execução em sua própria janela de aplicativo, não apenas em uma janela de navegador.
* Sendo iniciado no menu Iniciar, no Dock ou na tela inicial do sistema operacional do host.
* Receber notificações por push de um servidor de back-end, mesmo que o usuário não esteja usando o aplicativo.
* Atualizando automaticamente em segundo plano.

A palavra *progressiva* é usada para descrever tais aplicativos porque:

* Um usuário pode primeiro descobrir e usar o aplicativo no navegador da Web, como qualquer outro SPA.
* Posteriormente, o usuário progride para instalá-lo em seu sistema operacional e habilitar notificações por push.

## <a name="create-a-project-from-the-pwa-template"></a>Criar um projeto do modelo do PWA

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ao criar um novo **aplicativo Webassembly** de mais novos no diálogo **criar um novo projeto** , marque a caixa de seleção **progresso do aplicativo Web** :

![A caixa de seleção ' aplicativo Web progressivo ' é marcada na diálogo novo projeto do Visual Studio.](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/CLI do .NET Core](#tab/visual-studio-code+netcore-cli)

Crie um projeto do PWA em um shell de comando com a opção `--pwa`:

```dotnetcli
dotnet new blazorwasm -o MyNewProject --pwa
```

---

Opcionalmente, o PWA pode ser configurado para um aplicativo criado a partir do modelo hospedado ASP.NET Core. O cenário do PWA é independente do modelo de hospedagem.

## <a name="installation-and-app-manifest"></a>Instalação e manifesto do aplicativo

Ao visitar um aplicativo criado usando o modelo do PWA, os usuários têm a opção de instalar o aplicativo no menu Iniciar, no Dock ou na tela inicial do sistema operacional. A maneira como essa opção é apresentada depende do navegador do usuário. Ao usar navegadores baseados em desktop Chromium, como Edge ou Chrome, um botão **Adicionar** é exibido na barra de URL. Depois que o usuário selecionar o botão **Adicionar** , ele receberá uma caixa de diálogo de confirmação:

![O diaglog de confirmação no Google Chrome apresenta um botão de botão de instalação para o usuário para o aplicativo ' MyBlazorPwa '.](progressive-web-app/_static/image2.png)

No iOS, os visitantes podem instalar o PWA usando o botão **compartilhar** do Safari e sua opção **Adicionar à homescreen** . No Chrome para Android, os usuários devem selecionar o botão de **menu** no canto superior direito, seguido por **Adicionar à tela inicial**.

Uma vez instalado, o aplicativo aparece em sua própria janela sem uma barra de endereços:

![O aplicativo ' MyBlazorPwa ' é executado no Google Chrome sem uma barra de endereços.](progressive-web-app/_static/image3.png)

Para personalizar o título, o esquema de cores, o ícone ou outros detalhes da janela, consulte o arquivo *manifest. JSON* no diretório *wwwroot* do projeto. O esquema desse arquivo é definido por padrões da Web. Para obter mais informações, consulte [MDN Web docs: manifesto do aplicativo Web](https://developer.mozilla.org/docs/Web/Manifest).

## <a name="offline-support"></a>Suporte offline

Por padrão, os aplicativos criados usando a opção de modelo do PWA têm suporte para execução offline. Um usuário deve primeiro visitar o aplicativo enquanto estiver online. O navegador baixa e armazena em cache automaticamente todos os recursos necessários para operar offline.

> [!IMPORTANT]
> O suporte ao desenvolvimento interferiria com o ciclo de desenvolvimento normal de fazer alterações e testá-las. Portanto, o suporte offline só é habilitado para aplicativos *publicados* . 

> [!WARNING]
> Se você pretende distribuir um PWA habilitado para offline, há [vários avisos e advertências importantes](#caveats-for-offline-pwas). Esses cenários são inerentes a PWAs offline e não específicos para Blazor. Certifique-se de ler e entender essas advertências antes de fazer suposições sobre como seu aplicativo habilitado para offline funcionará.

Para ver como funciona o suporte offline:

1. Publique o aplicativo. Para obter mais informações, consulte <xref:host-and-deploy/blazor/index#publish-the-app>.
1. Implante o aplicativo em um servidor que dê suporte a HTTPS e acesse o aplicativo em um navegador em seu endereço HTTPS seguro.
1. Abra as ferramentas de desenvolvimento do navegador e verifique se um *trabalhador de serviço* está registrado para o host na guia **aplicativo** :

   ![A guia ' aplicativo ' das ferramentas de desenvolvedor do Google Chrome mostra um trabalho de serviço ativado e em execução.](progressive-web-app/_static/image4.png)

1. Recarregue a página e examine a guia **rede** . o **trabalho de serviço** ou o cache de **memória** são listados como fontes para todos os ativos da página:

   ![Guia ' rede ' das ferramentas de desenvolvedor do Google Chrome mostrando fontes para todos os ativos da página.](progressive-web-app/_static/image5.png)

1. Para verificar se o navegador não depende do acesso à rede para carregar o aplicativo, seja:

   * Desligue o servidor Web e veja como o aplicativo continua a funcionar normalmente, o que inclui recargas de página. Da mesma forma, o aplicativo continua a funcionar normalmente quando há uma conexão de rede lenta.
   * Instrua o navegador a simular o modo offline na guia **rede** :

   ![Guia ' rede ' das ferramentas de desenvolvedor do Google Chrome com a lista suspensa do modo navegador sendo alterada de ' online ' para ' offline '.](progressive-web-app/_static/image6.png)

O suporte offline usando um operador de serviço é um padrão da Web, não específico do Blazor. Para obter mais informações sobre os operadores de serviço, consulte [MDN Web docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API). Para saber mais sobre os padrões de uso comuns para trabalhadores de serviço, consulte [Google Web: o ciclo de vida do trabalho do serviço](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

o modelo do PWA do Blazorproduz dois arquivos de trabalho do serviço:

* *wwwroot/Service-Worker. js*, que é usado durante o desenvolvimento.
* *wwwroot/Service-Worker. published. js*, que é usado depois que o aplicativo é publicado.

Para compartilhar a lógica entre os dois arquivos de trabalho do serviço, considere a seguinte abordagem:

* Adicione um terceiro arquivo JavaScript para manter a lógica comum.
* Use [Self. importScripts](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) para carregar a lógica comum em ambos os arquivos de trabalho do serviço.

### <a name="cache-first-fetch-strategy"></a>Estratégia de busca de cache-primeiro

O trabalho de serviço interno *Service-Worker. published. js* resolve solicitações usando uma estratégia *de cache-First* . Isso significa que o operador de serviço prefere retornar o conteúdo armazenado em cache, independentemente de o usuário ter acesso à rede ou conteúdo mais recente estar disponível no servidor.

A estratégia de cache-First é valiosa porque:

* **Ele garante a confiabilidade.** &ndash; acesso à rede não é um estado booliano. Um usuário não está simplesmente online ou offline:

  * O dispositivo do usuário pode pressupor que está online, mas a rede pode ser tão lenta quanto ser impraticável de esperar.
  * a rede de ti pode retornar resultados inválidos para determinadas URLs, como quando há um portal WIFI cativo que está bloqueando ou redirecionando determinadas solicitações.
  
  É por isso que a API de `navigator.onLine` do navegador não é confiável e não deve ser dependente.

* **Ele garante a exatidão.** &ndash; ao criar um cache de recursos offline, o trabalho de serviço usa o hash de conteúdo para garantir que ele tenha buscado um instantâneo completo e autoconsistente de recursos em um único instante no tempo. Esse cache é usado como uma unidade atômica. Não há nenhum ponto perguntando à rede os recursos mais recentes, pois as únicas versões necessárias são as já armazenadas em cache. Qualquer outra coisa afeta a inconsistência e a incompatibilidade (por exemplo, tentar usar versões de assemblies do .NET que não foram compiladas juntas).

### <a name="background-updates"></a>Atualizações em segundo plano

Como um modelo mental, você pode considerar um PWA offline como se comportando como um aplicativo móvel que pode ser instalado. O aplicativo é iniciado imediatamente, independentemente da conectividade de rede, mas a lógica do aplicativo instalado vem de um instantâneo pontual que pode não ser a versão mais recente.

O modelo do Blazor PWA produz aplicativos que tentam se atualizar automaticamente em segundo plano sempre que o usuário visita e tem uma conexão de rede em funcionamento. A maneira como isso funciona é a seguinte:

* Durante a compilação, o projeto gera um *manifesto de ativos de trabalho de serviço*. Por padrão, isso é chamado de *Service-Worker-assets. js*. O manifesto lista todos os recursos estáticos que o aplicativo requer para funcionar offline, como assemblies .NET, arquivos JavaScript e CSS, incluindo seus hashes de conteúdo. A lista de recursos é carregada pelo trabalhador do serviço para que ele saiba quais recursos armazenar em cache.
* Cada vez que o usuário visita o aplicativo, o navegador solicita novamente *Service-Worker. js* e *Service-Worker-assets. js* em segundo plano. Os arquivos são comparados byte por byte com o trabalho de serviço instalado existente. Se o servidor retornar o conteúdo alterado para qualquer um desses arquivos, o trabalho de serviço tentará instalar uma nova versão de si mesmo.
* Ao instalar uma nova versão de si mesma, o trabalho de serviço cria um novo cache separado para recursos offline e começa a popular o cache com os recursos listados em *Service-Worker-assets. js*. Essa lógica é implementada na função `onInstall` dentro de *Service-Worker. published. js*.
* O processo é concluído com êxito quando todos os recursos são carregados sem erros e todos os hashes de conteúdo correspondem. Se for bem-sucedido, o novo trabalho de serviço entrará *em aguardando o estado de ativação* . Assim que o usuário fecha o aplicativo (não há guias de aplicativo restantes ou janelas), o novo trabalho de serviço torna-se *ativo* e é usado para visitas de aplicativos subsequentes. O antigo trabalho de serviço e seu cache são excluídos.
* Se o processo não for concluído com êxito, a nova instância de trabalho do serviço será descartada. O processo de atualização foi tentado novamente na próxima visita do usuário, quando espero que o cliente tenha uma conexão de rede melhor que possa concluir as solicitações.

Personalize esse processo editando a lógica de trabalho do serviço. Nenhum dos comportamentos anteriores é específico para Blazor mas é meramente a experiência padrão fornecida pela opção de modelo do PWA. Para obter mais informações, consulte [MDN Web docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).

### <a name="how-requests-are-resolved"></a>Como as solicitações são resolvidas

Conforme descrito na seção de [estratégia de busca em cache – primeiro](#cache-first-fetch-strategy) , o operador de serviço padrão usa uma estratégia de *cache-First* , o que significa que ele tenta fornecer conteúdo em cache quando disponível. Se não houver nenhum conteúdo armazenado em cache para uma determinada URL, por exemplo, ao solicitar dados de uma API de back-end, o trabalho de serviço retornará uma solicitação de rede regular. A solicitação de rede terá sucesso se o servidor estiver acessível. Essa lógica é implementada dentro de `onFetch` função dentro de *Service-Worker. published. js*.

Se os componentes do Razor do aplicativo dependerem da solicitação de dados de APIs de back-end e você quiser fornecer uma experiência de usuário amigável para solicitações com falha devido à indisponibilidade da rede, implemente a lógica nos componentes do aplicativo. Por exemplo, use `try/catch` em `HttpClient` solicitações.

### <a name="support-server-rendered-pages"></a>Suporte a páginas renderizadas pelo servidor

Considere o que acontece quando o usuário navega pela primeira vez para uma URL como `/counter` ou qualquer outro link profundo no aplicativo. Nesses casos, você não deseja retornar o conteúdo armazenado em cache como `/counter`, mas, em vez disso, precisa do navegador para carregar o conteúdo armazenado em cache como `/index.html` para iniciar seu aplicativo Webassembly Blazor. Essas solicitações iniciais são conhecidas como solicitações de *navegação* , em oposição a:

* solicitações de *subrecurso* para imagens, folhas de estilos ou outros arquivos.
* solicitações de *busca/XHR* para dados de API.

O operador de serviço padrão contém uma lógica de caso especial para solicitações de navegação. O trabalho do serviço resolve as solicitações retornando o conteúdo armazenado em cache para `/index.html`, independentemente da URL solicitada. Essa lógica é implementada na função `onFetch` dentro de *Service-Worker. published. js*.

Se seu aplicativo tiver determinadas URLs que devem retornar o HTML renderizado pelo servidor e não atender `/index.html` do cache, você precisará editar a lógica em seu trabalho de serviço. Se todas as URLs que contêm `/Identity/` precisam ser tratadas como solicitações regulares somente online para o servidor, modifique *Service-Worker. published. js* `onFetch` lógica. Localize o seguinte código:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Altere o código para o seguinte:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

Se você não fizer isso, independentemente da conectividade de rede, o trabalho de serviço interceptará as solicitações para essas URLs e as resolverá usando `/index.html`.

### <a name="control-asset-caching"></a>Controlar o cache de ativos

Se o projeto definir a propriedade `ServiceWorkerAssetsManifest` do MSBuild, as ferramentas de Build do Blazorgerarão um manifesto de ativos de trabalho do serviço com o nome especificado. O modelo do PWA padrão produz um arquivo de projeto que contém a seguinte propriedade:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

O arquivo é colocado no diretório de saída *wwwroot* , para que o navegador possa recuperar esse arquivo solicitando `/service-worker-assets.js`. Para ver o conteúdo desse arquivo, abra */bin/Debug/{Target Framework}/wwwroot/Service-Worker-assets.js* em um editor de texto. No entanto, não edite o arquivo, pois ele é regenerado em cada compilação.

Por padrão, este manifesto lista:

* Quaisquer recursos gerenciados Blazor, como assemblies .NET e os arquivos de tempo de execução .NET Webassembly necessários para funcionar offline.
* Todos os recursos para publicação no diretório *wwwroot* do aplicativo, como imagens, folhas de estilos e arquivos JavaScript, incluindo ativos estáticos da Web fornecidos por projetos externos e pacotes NuGet.

Você pode controlar quais desses recursos são buscados e armazenados em cache pelo trabalhador do serviço editando a lógica em `onInstall` em *Service-Worker. published. js*. Por padrão, o trabalho de serviço busca e armazena em cache os arquivos que correspondem às extensões de nome de arquivo da Web típicas, como *. html*, *. css*, *. js*e *. WASM*, além de tipos de arquivos específicos para Blazor Webassembly ( *. dll*, *. pdb*).

Para incluir recursos adicionais que não estão presentes no diretório *wwwroot* do aplicativo, defina entradas de `ItemGroup` MSBuild adicionais, conforme mostrado no exemplo a seguir:

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

O `AssetUrl` metadados especifica a URL relativa à base que o navegador deve usar ao buscar o recurso para armazenar em cache. Isso pode ser independente de seu nome de arquivo de origem original no disco.

> [!IMPORTANT]
> Adicionar um `ServiceWorkerAssetsManifestItem` não faz com que o arquivo seja publicado no diretório *wwwroot* do aplicativo. A saída de publicação deve ser controlada separadamente. A `ServiceWorkerAssetsManifestItem` apenas faz com que uma entrada adicional apareça no manifesto de ativos de trabalho do serviço.

## <a name="push-notifications"></a>Notificações por push

Como qualquer outro PWA, um Blazor Webassembly PWA pode receber notificações por push de um servidor de back-end. O servidor pode enviar notificações por push a qualquer momento, mesmo quando o usuário não está usando o aplicativo ativamente. Por exemplo, notificações por push podem ser enviadas quando um usuário diferente executa uma ação relevante.

O mecanismo para enviar uma notificação por push é totalmente independente do Blazor Webassembly, pois ele é implementado pelo servidor de back-end que pode usar qualquer tecnologia. Se você quiser enviar notificações por push de um servidor de ASP.NET Core, considere [usar uma técnica semelhante à abordagem adotada no workshop de pizza incrivelmente](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).

O mecanismo para receber e exibir uma notificação por push no cliente também é independente do Webassembly Blazor, já que ele é implementado no arquivo JavaScript de trabalho do serviço. Para obter um exemplo, consulte [a abordagem usada no workshop de pizza incrivelmente](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Advertências para PWAs offline

Nem todos os aplicativos devem tentar dar suporte ao uso offline. O suporte offline adiciona uma complexidade significativa, embora nem sempre seja relevante para os casos de uso necessários.

O suporte offline geralmente é relevante apenas:

* Se o repositório de dados primário for local para o navegador. Por exemplo, a abordagem é relevante em um aplicativo com uma interface do usuário para um dispositivo [IOT](https://en.wikipedia.org/wiki/Internet_of_things) que armazena dados em `localStorage` ou [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).
* Se o aplicativo executar uma quantidade significativa de trabalho para buscar e armazenar em cache os dados de API de back-end relevantes para cada usuário para que eles possam navegar pelos dados offline. Se o aplicativo precisar dar suporte à edição, um sistema para controlar as alterações e sincronizar dados com o back-end deve ser criado.
* Se o objetivo é garantir que o aplicativo seja carregado imediatamente, independentemente das condições de rede. Implemente uma experiência de usuário adequada em relação a solicitações de API de back-end para mostrar o progresso das solicitações e se comportar normalmente quando as solicitações falharem devido à indisponibilidade da rede

Além disso, o PWAs com capacidade offline deve lidar com uma variedade de complicações adicionais. Os desenvolvedores devem se familiarizar cuidadosamente com as advertências nas seções a seguir.

### <a name="offline-support-only-when-published"></a>Suporte offline somente quando publicado

Durante o desenvolvimento, normalmente você desejará ver cada alteração refletida imediatamente no navegador sem passar por um processo de atualização em segundo plano. Portanto, o modelo do PWA Blazorhabilita o suporte offline somente quando publicado.

Ao criar um aplicativo com capacidade offline, não é suficiente testar o aplicativo no ambiente de desenvolvimento. Você deve testar o aplicativo em seu estado publicado para entender como ele responde a condições de rede diferentes.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Atualizar a conclusão após a navegação do usuário para fora do aplicativo

As atualizações não são concluídas até que o usuário navegue para fora do aplicativo em todas as guias. Conforme explicado na seção [atualizações em segundo plano](#background-updates) , depois de implantar uma atualização para o aplicativo, o navegador busca os arquivos de trabalho do serviço atualizados para iniciar o processo de atualização.

O que surpreende muitos desenvolvedores é que, mesmo quando essa atualização é concluída, ela **não** tem efeito até que o usuário tenha navegado para fora em todas as guias. **Não** é suficiente atualizar a guia exibindo o aplicativo, mesmo se for a única guia que exibe o aplicativo. Até que seu aplicativo seja completamente fechado, o novo trabalho de serviço permanecerá no status *aguardando para ativar* . **Isso não é específico para Blazor, mas sim um comportamento de plataforma da Web padrão.**

Isso normalmente problemas os desenvolvedores que estão tentando testar atualizações para seu trabalho de serviço ou recursos armazenados em cache offline. Se você fizer check-in das ferramentas de desenvolvedor do navegador, poderá ver algo semelhante ao seguinte:

![A guia "aplicativo" do Google Chrome mostra que o trabalho do serviço do aplicativo é "aguardando para ativar".](progressive-web-app/_static/image7.png)

Enquanto a lista de "clientes", que são guias ou janelas que exibem seu aplicativo, não está vazia, o trabalho continua aguardando. O motivo pelo qual os trabalhadores de serviço fazem isso é garantir a consistência. Consistência significa que todos os recursos são buscados do mesmo cache atômico.

Ao testar alterações, talvez seja conveniente clicar no link "skipWaiting", conforme mostrado na captura de tela anterior, e recarregar a página. Você pode automatizar isso para todos os usuários codificando seu operador de serviço para [ignorar a fase de "espera" e ativá-la imediatamente na atualização](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). Se você ignorar a fase de espera, estará fazendo a garantia de que os recursos são sempre buscados consistentemente da mesma instância de cache.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Os usuários podem executar qualquer versão histórica do aplicativo

Os desenvolvedores da Web recompensadores esperam que os usuários executem apenas a versão mais recente implantada de seu aplicativo Web, pois isso é normal no modelo de distribuição da Web tradicional. No entanto, um PWA offline primeiro é mais semelhante a um aplicativo móvel nativo, onde os usuários não estão necessariamente executando a versão mais recente.

Conforme explicado na seção [atualizações em segundo plano](#background-updates) , depois de implantar uma atualização em seu aplicativo, **cada usuário existente continua a usar uma versão anterior para pelo menos uma visita adicional** , pois a atualização ocorre em segundo plano e não é ativada até que o usuário saia de fora. Além disso, a versão anterior que está sendo usada não é necessariamente a anterior que você implantou. A versão anterior pode ser *qualquer* versão histórica, dependendo de quando o usuário concluiu a última vez uma atualização.

Isso pode ser um problema se as partes de frontend e back-end do seu aplicativo exigirem contrato sobre o esquema para solicitações de API. Você não deve implantar alterações de esquema de API incompatíveis com versões anteriores até que possa ter certeza de que todos os usuários foram atualizados. Como alternativa, bloqueie os usuários de usar versões mais antigas incompatíveis do aplicativo. Esse requisito de cenário é o mesmo para aplicativos móveis nativos. Se você implantar uma alteração significativa nas APIs do servidor, o aplicativo cliente será interrompido para os usuários que ainda não foram atualizados.

Se possível, não implante alterações significativas em suas APIs de back-end. Se você precisar fazer isso, considere o uso de [APIs de trabalho de serviço padrão](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) , como ServiceWorkerRegistration, para determinar se o aplicativo está atualizado e, se não, para evitar o uso.

### <a name="interference-with-server-rendered-pages"></a>Interferência com páginas renderizadas pelo servidor

Conforme descrito na seção [support Server-](#support-server-rendered-pages) requested pages, se você quiser ignorar o comportamento do trabalhador de serviço de retornar `/index.html` conteúdo para todas as solicitações de navegação, edite a lógica em seu trabalho de serviço.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Todos os conteúdos do manifesto de ativos de trabalho de serviço são armazenados em cache por padrão

Conforme descrito na seção [controlar ativo Caching](#control-asset-caching) , o arquivo *Service-Worker-assets. js* é gerado durante a compilação e lista todos os ativos que o trabalho de serviço deve buscar e armazenar em cache.

Como essa lista, por padrão, inclui tudo emitido para *wwwroot*, incluindo o conteúdo fornecido por pacotes e projetos externos, você deve ter cuidado para não colocar muito conteúdo lá. Se o diretório *wwwroot* contiver milhões de imagens, o trabalho de serviço tentará buscar e armazenar em cache todas elas, consumindo largura de banda excessiva e provavelmente não será concluído com êxito.

Implemente uma lógica arbitrária para controlar qual subconjunto do conteúdo do manifesto deve ser buscado e armazenado em cache editando a função `onInstall` em *Service-Worker. published. js*.

### <a name="interaction-with-authentication"></a>Interação com autenticação

É possível usar a opção de modelo do PWA em conjunto com as opções de autenticação. Um PWA compatível com offline também pode dar suporte à autenticação quando o usuário tem conectividade de rede.

Quando um usuário não tem conectividade de rede, ele não pode autenticar ou obter tokens de acesso. Por padrão, a tentativa de visitar a página de logon sem acesso à rede resulta em uma mensagem de "erro de rede".

Você deve criar um fluxo de interface do usuário que permita que ele faça coisas úteis enquanto estiver offline sem tentar autenticar ou obter tokens de acesso. Como alternativa, você pode criar o aplicativo para falhar de forma normal quando a rede não estiver disponível. Se isso não for possível em seu aplicativo, talvez você não queira habilitar o suporte offline.

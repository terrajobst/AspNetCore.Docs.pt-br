---
title: Use os serviços JavaScript para criar aplicativos de página única no ASP.NET Core
author: scottaddie
description: Saiba mais sobre os benefícios de usar os serviços JavaScript para criar um aplicativo de página única (SPA) apoiado por ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: caff1a735de3274b371f67e6e485dc42e579452c
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828211"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Use os serviços JavaScript para criar aplicativos de página única no ASP.NET Core

Por [Scott Addie](https://github.com/scottaddie) e [Fiyaz Hasan](https://fiyazhasan.me/)

Um aplicativo de página única (SPA) é um tipo popular de aplicativo web devido à sua experiência de usuário avançada inerente. A integração de estruturas ou bibliotecas SPA do lado do cliente, como [angular](https://angular.io/) ou [reagir](https://facebook.github.io/react/), com estruturas do lado do servidor, como ASP.NET Core pode ser difícil. Os serviços JavaScript foram desenvolvidos para reduzir o conflito no processo de integração. Ele permite que a operação contínua entre o cliente diferentes e pilhas de tecnologia do servidor.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Os recursos descritos neste artigo são obsoletos a partir do ASP.NET Core 3,0. Um mecanismo de integração de estruturas SPA mais simples está disponível no pacote NuGet [Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) . Para obter mais informações, consulte [[comunicado] Obsoleting Microsoft. AspNetCore. SpaServices e Microsoft. AspNetCore. nodeservices](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>O que são os serviços JavaScript

Os serviços JavaScript são uma coleção de tecnologias do lado do cliente para ASP.NET Core. Sua meta é posicionar o ASP.NET Core como plataforma de servidor preferencial dos desenvolvedores para a criação de SPAs.

Os serviços JavaScript consistem em dois pacotes do NuGet distintos:

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Esses pacotes são úteis nos seguintes cenários:

* Executar o JavaScript no servidor
* Usar uma estrutura de SPA ou biblioteca
* Criar ativos do lado do cliente com o Webpack

O foco deste artigo é colocado sobre como usar o pacote SpaServices.

## <a name="what-is-spaservices"></a>O que é SpaServices

SpaServices foi criado para posicionar o ASP.NET Core como plataforma de servidor preferencial dos desenvolvedores para a criação de SPAs. SpaServices não é necessário para desenvolver SPAs com ASP.NET Core e não bloqueia os desenvolvedores em uma estrutura de cliente específica.

SpaServices fornece infraestrutura úteis, como:

* [Pré-processamento do lado do servidor](#server-side-prerendering)
* [Middleware de desenvolvimento webpack](#webpack-dev-middleware)
* [Substituição do módulo quente](#hot-module-replacement)
* [Auxiliares de roteamentos](#routing-helpers)

Coletivamente, esses componentes de infraestrutura aprimoram o fluxo de trabalho de desenvolvimento e a experiência de tempo de execução. Os componentes podem ser adotados individualmente.

## <a name="prerequisites-for-using-spaservices"></a>Pré-requisitos para usar SpaServices

Para trabalhar com SpaServices, instale o seguinte:

* [Node. js](https://nodejs.org/) (versão 6 ou posterior) com npm

  * Para verificar se esses componentes estão instalados e podem ser encontrados, execute o seguinte na linha de comando:

    ```console
    node -v && npm -v
    ```

  * Se estiver implantando em um site do Azure, nenhuma ação será necessária&mdash;node. js estiver instalado e disponível nos ambientes de servidor.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * No Windows usando o Visual Studio 2017, o SDK é instalado selecionando a carga de trabalho de **desenvolvimento de plataforma cruzada do .NET Core** .

* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) pacote do NuGet

## <a name="server-side-prerendering"></a>Pré-processamento do lado do servidor

Um aplicativo universal de (também conhecido como isomórficos) é um aplicativo de JavaScript pode ser executada tanto no servidor e cliente. Angular, React e outras estruturas populares fornecem uma plataforma universal para esse estilo de desenvolvimento do aplicativo. A ideia é renderizado primeiro os componentes do framework no servidor por meio do Node. js e delegar ainda mais a execução para o cliente.

ASP.NET Core [auxiliares de marca](xref:mvc/views/tag-helpers/intro) fornecidos pelo SpaServices simplificar a implementação de pré-processamento do lado do servidor, chamando as funções de JavaScript no servidor.

### <a name="server-side-prerendering-prerequisites"></a>Pré-requisitos de pré-processamento do lado do servidor

Instale o pacote NPM do [ASPNET-prerenderizando](https://www.npmjs.com/package/aspnet-prerendering) :

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Configuração de pré-processamento do lado do servidor

Os auxiliares de marca são feitos podem ser descobertos por meio do registro do namespace do projeto *viewimports. cshtml* arquivo:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Esses auxiliares de marcação abstraem as complexidades de se comunicar diretamente com as APIs de baixo nível, utilizando uma sintaxe semelhante ao HTML dentro a exibição do Razor:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>Auxiliar de marca do ASP-PreRender-Module

O `asp-prerender-module` auxiliar de marca, usado no exemplo de código anterior executa *ClientApp/dist/main-server.js* no servidor por meio do Node. js. Para clareza, *main-Server. js* arquivo é um artefato da tarefa em que a transcompilação TypeScript e JavaScript a [Webpack](https://webpack.github.io/) processo de compilação. Webpack define um alias de ponto de entrada de `main-server`; e, passagem de grafo de dependência para este alias começa em de *ClientApp/inicialização-server.ts* arquivo:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

No exemplo a seguir Angular, o *ClientApp/inicialização-server.ts* arquivo utiliza a `createServerRenderer` função e `RenderResult` tipo do `aspnet-prerendering` pacote npm para configurar a renderização do servidor por meio do Node. js. A marcação HTML destinada a renderização do lado do servidor é passada para uma chamada de função de resolução, que é encapsulada em um JavaScript fortemente tipada `Promise` objeto. O `Promise` significância do objeto é que ele fornece assincronamente a marcação HTML para a página para injeção no elemento de espaço reservado do DOM.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>ASP-PreRender-auxiliar de marca de dados

Quando combinado com o `asp-prerender-module` auxiliar de marca, o `asp-prerender-data` auxiliar de marca pode ser usado para passar informações contextuais da exibição do Razor para o JavaScript do lado do servidor. Por exemplo, a marcação a seguir passa os dados do usuário para o `main-server` módulo:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Recebido `UserName` argumento for serializado usando o serializador JSON interno e é armazenado no `params.data` objeto. No exemplo a seguir Angular, os dados são usados para construir uma saudação personalizada dentro de um `h1` elemento:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Os nomes de propriedade passados em auxiliares de marca são representados com notação de **PascalCase** . Compare isso com JavaScript, onde os mesmos nomes de propriedade são representados com **camelCase**. A configuração de serialização JSON padrão é responsável por essa diferença.

Para expandir o exemplo de código anterior, dados podem ser passados do servidor para o modo de exibição por hydrating a `globals` propriedade fornecida para o `resolve` função:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

O `postList` matriz definida dentro de `globals` objeto está anexado para o navegador global `window` objeto. Essa elevação de variáveis em escopo global elimina a duplicação de esforço, particularmente, pois pertence ao carregar os mesmos dados, uma vez no servidor e novamente no cliente.

![variável global de postList anexada ao objeto de janela](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Middleware de desenvolvimento webpack

[Middleware de desenvolvimento webpack](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduz um fluxo de trabalho de desenvolvimento simplificado no qual o Webpack cria recursos sob demanda. O middleware automaticamente compila e atende recursos do lado do cliente quando uma página é recarregada no navegador. A abordagem alternativa é invocar manualmente Webpack por meio do script de compilação do projeto npm quando uma dependência de terceiros ou o código personalizado é alterado. Script de construção de um npm *Package. JSON* arquivo é mostrado no exemplo a seguir:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Pré-requisitos de middleware de dev do webpack

Instale o pacote [ASPNET-webpack](https://www.npmjs.com/package/aspnet-webpack) NPM:

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Configuração de middleware de dev do webpack

Webpack Dev Middleware é registrado no pipeline de solicitação HTTP por meio de código a seguir na *Startup.cs* do arquivo `Configure` método:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

O `UseWebpackDevMiddleware` método de extensão deve ser chamado antes [Registrando a hospedagem de arquivos estáticos](xref:fundamentals/static-files) por meio de `UseStaticFiles` método de extensão. Por motivos de segurança, registre o middleware somente quando o aplicativo é executado no modo de desenvolvimento.

O *webpack.config.js* do arquivo `output.publicPath` propriedade informa o middleware para observar o `dist` pasta para que as alterações:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Substituição do módulo quente

Pense do Webpack [módulo de substituição a quente](https://webpack.js.org/concepts/hot-module-replacement/) recurso (HMR) como uma evolução do [Webpack Dev Middleware](#webpack-dev-middleware). HMR apresenta todos os mesmos benefícios, mas ela simplifica ainda mais o fluxo de trabalho de desenvolvimento, atualizando automaticamente o conteúdo da página depois de compilar as alterações. Não confunda isso com uma atualização do navegador, que poderia interferir com o estado atual de na memória e a sessão de depuração do SPA. Há um link em tempo real entre o serviço de Middleware de desenvolvimento Webpack e o navegador, o que significa que alterações são enviadas para o navegador.

### <a name="hot-module-replacement-prerequisites"></a>Pré-requisitos de substituição de módulo quente

Instalar o pacote [webpack – Hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) NPM:

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Configuração de substituição de módulo quente

O componente HMR deve ser registrado no pipeline de solicitação HTTP do MVC no `Configure` método:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Como ocorria com [Webpack Dev Middleware](#webpack-dev-middleware), o `UseWebpackDevMiddleware` método de extensão deve ser chamado antes do `UseStaticFiles` método de extensão. Por motivos de segurança, registre o middleware somente quando o aplicativo é executado no modo de desenvolvimento.

O *webpack.config.js* arquivo deve definir um `plugins` de matriz, mesmo se ela for deixada em branco:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Depois de carregar o aplicativo no navegador, a guia Console de ferramentas de desenvolvedor fornece confirmação de ativação de HMR:

![Hot mensagem conectada de substituição do módulo](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Auxiliares de roteamentos

Na maioria dos SPAs baseados em ASP.NET Core, o roteamento do lado do cliente geralmente é desejado, além do roteamento do lado do servidor. Os sistemas de roteamento do SPA e o MVC podem trabalhar de forma independente, sem interferência. Há, no entanto, os desafios de uma borda caso apresentando: identificando as respostas HTTP 404.

Considere o cenário em que uma rota sem extensão de `/some/page` é usado. Suponha que a solicitação não-correspondência de padrão uma rota do lado do servidor, mas seu padrão corresponde a uma rota do lado do cliente. Agora, considere uma solicitação de entrada para `/images/user-512.png`, que geralmente espera encontrar um arquivo de imagem no servidor. Se esse caminho de recurso solicitado não corresponder a uma rota no lado do servidor ou a um arquivo estático, é improvável que o aplicativo do lado do cliente manipule isso&mdash;geralmente retornar um código de status HTTP 404 é desejado.

### <a name="routing-helpers-prerequisites"></a>Pré-requisitos de auxiliares de roteamento

Instale o pacote NPM de roteamento do lado do cliente. Usando Angular como um exemplo:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Configuração de auxiliares de roteamento

Um método de extensão denominado `MapSpaFallbackRoute` é usado no `Configure` método:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

As rotas são avaliadas na ordem em que estão configuradas. Consequentemente, o `default` rota no exemplo de código anterior é usada primeiro para correspondência de padrões.

## <a name="create-a-new-project"></a>Crie um novo projeto

Os serviços JavaScript fornecem modelos de aplicativo pré-configurados. O SpaServices é usado nesses modelos em conjunto com estruturas e bibliotecas diferentes, como angular, reagir e Redux.

Esses modelos podem ser instalados por meio da CLI do .NET Core, executando o seguinte comando:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Uma lista de modelos SPA disponíveis é exibida:

| Modelos                                 | Nome Curto | {1&gt;Idioma&lt;1} | Marcas        |
| ------------------------------------------| :--------: | :------: | :---------: |
| MVC ASP.NET Core com Angular             | angular    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core com React. js            | react      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core com React. js e Redux  | reactredux | [C#]     | Web/MVC/SPA |

Para criar um novo projeto usando um dos modelos do SPA, inclua o **nome curto** do modelo na [dotnet novo](/dotnet/core/tools/dotnet-new) comando. O comando a seguir cria um aplicativo Angular com ASP.NET Core MVC configurado para o lado do servidor:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Definir o modo de configuração de tempo de execução

Existem dois modos de configuração de tempo de execução principal:

* **Desenvolvimento**:
  * Inclui mapas de código-fonte para facilitar a depuração.
  * Não Otimize o código do lado do cliente para o desempenho.
* **Produção**:
  * Exclui os mapas de origem.
  * Otimiza o código do lado do cliente por meio de agrupamento e minificação.

O ASP.NET Core usa uma variável de ambiente denominada `ASPNETCORE_ENVIRONMENT` para armazenar o modo de configuração. Para obter mais informações, consulte [definir o ambiente](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Executar com CLI do .NET Core

Restaure os pacotes de npm e NuGet necessários, executando o seguinte comando na raiz do projeto:

```dotnetcli
dotnet restore && npm i
```

Compilar e executar o aplicativo:

```dotnetcli
dotnet run
```

O aplicativo é iniciado no localhost de acordo com o [modo de configuração de tempo de execução](#set-the-runtime-configuration-mode). Navegando até `http://localhost:5000` no navegador exibe a página de aterrissagem.

### <a name="run-with-visual-studio-2017"></a>Executar com o Visual Studio 2017

Abra o *. csproj* arquivo gerado pelo [dotnet novo](/dotnet/core/tools/dotnet-new) comando. Os pacotes NuGet e npm necessários são restaurados automaticamente ao projeto aberto. Esse processo de restauração pode demorar alguns minutos e o aplicativo está pronto para ser executado quando ele for concluído. Clique no botão de execução verde ou pressione `Ctrl + F5`, e o navegador abre a página de aterrissagem do aplicativo. O aplicativo é executado no localhost de acordo com o [modo de configuração de tempo de execução](#set-the-runtime-configuration-mode).

## <a name="test-the-app"></a>Testar o aplicativo

Modelos SpaServices são pré-configuradas para executar testes do lado do cliente usando [Karma](https://karma-runner.github.io/1.0/index.html) e [Jasmine](https://jasmine.github.io/). Jasmine é uma estrutura de teste para JavaScript, de unidade popular enquanto Karma é um executor de teste para que esses testes. Karma está configurado para funcionar com o [Webpack Dev Middleware](#webpack-dev-middleware) , de modo que o desenvolvedor não é necessário parar e executar o teste sempre que forem feitas alterações. Se ele é o código em execução no caso de teste ou caso de teste em si, o teste é executado automaticamente.

Usando o aplicativo Angular como exemplo, dois casos de teste Jasmine já são fornecidos para o `CounterComponent` no *counter.component.spec.ts* arquivo:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Abra o prompt de comando de *ClientApp* diretório. Execute o seguinte comando:

```console
npm test
```

O script inicia o executor de teste Karma, que lê as configurações definidas na *karma.conf.js* arquivo. Entre outras configurações, o *karma.conf.js* identifica os arquivos de teste para ser executada por meio do seu `files` matriz:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Publique o aplicativo

Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/12474) para obter mais informações sobre como publicar no Azure.

Combinando os ativos gerados do lado do cliente e os artefatos publicados do ASP.NET Core em um pacote pronto para implantar pode ser complicado. Felizmente, SpaServices orquestra o processo de publicação inteira com um destino MSBuild personalizado chamado `RunWebpack`:

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

O destino do MSBuild tem as seguintes responsabilidades:

1. Restaure os pacotes NPM.
1. Crie um Build de nível de produção dos ativos de terceiros do lado do cliente.
1. Crie uma compilação de nível de produção dos ativos personalizados do lado do cliente.
1. Copie os ativos gerados pelo webpack para a pasta de publicação.

O destino do MSBuild é chamado durante a execução:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Recursos adicionais

* [Docs angular](https://angular.io/docs)

---
title: Usar o modelo de projeto Angular com o ASP.NET Core
author: SteveSandersonMS
description: Saiba como começar a trabalhar com o modelo de projeto do SPA (Aplicativo de Página Única) do ASP.NET Core para Angular e a CLI do Angular.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 03/07/2019
uid: spa/angular
ms.openlocfilehash: 150b2176eac2e68c1ef9ec6deabb087836ff84ce
ms.sourcegitcommit: cb6015f737b6a93127016ab0f21b58e34b624ff3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "77004260"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a>Usar o modelo de projeto Angular com o ASP.NET Core

O modelo de projeto do Angular atualizado fornece um ponto inicial conveniente para aplicativos do ASP.NET Core usando o Angular e a CLI do Angular para implementar uma IU (interface do usuário) avançada do lado do cliente.

O modelo é equivalente à criação de um projeto do ASP.NET Core para atuar como um back-end de API e um projeto de CLI do Angular para atuar como uma interface do usuário. O modelo oferece a conveniência de hospedagem de ambos os tipos de projeto em um projeto de aplicativo único. Consequentemente, o projeto de aplicativo pode ser criado e publicado como uma única unidade.

## <a name="create-a-new-app"></a>Criar um novo aplicativo

Se você tiver o ASP.NET Core 2.1 instalado, não será necessário instalar o modelo de projeto Angular.

Crie um novo projeto de um prompt de comando usando o comando `dotnet new angular` em um diretório vazio. Por exemplo, os seguintes comandos criam o aplicativo em um diretório *my-new-app* e mudam para esse diretório:

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

Execute o aplicativo do Visual Studio ou da CLI do .NET Core:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)

Abra o arquivo *.csproj* gerado e execute o aplicativo normalmente de lá.

O processo de build restaura dependências npm na primeira execução, o que pode levar vários minutos. Builds subsequentes são muito mais rápidos.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Verifique se você tem uma variável de ambiente chamada `ASPNETCORE_Environment` com um valor de `Development`. No Windows (em prompts que não são do PowerShell), execute `SET ASPNETCORE_Environment=Development`. No Linux ou macOS, execute `export ASPNETCORE_Environment=Development`.

Execute [dotnet build](/dotnet/core/tools/dotnet-build) para verificar se o aplicativo compila corretamente. O processo de build restaura dependências npm na primeira execução, o que pode levar vários minutos. Builds subsequentes são muito mais rápidos.

Execute [dotnet run](/dotnet/core/tools/dotnet-run) para iniciar o aplicativo. Uma mensagem semelhante ao seguinte é registrada em log:

```console
Now listening on: http://localhost:<port>
```

Navegue até essa URL em um navegador.

O aplicativo inicia uma instância do servidor da CLI do Angular em segundo plano. Uma mensagem semelhante à seguinte será registrada: *O NG Live Development Server está escutando em localhost:&lt;otherport&gt;, abra seu navegador em http://localhost:&lt;otherport&gt;/* . Ignore essa mensagem&mdash;**não** se trata da URL para o aplicativo combinado do ASP.NET Core e da CLI do Angular.

---

O modelo de projeto cria um aplicativo ASP.NET Core e um aplicativo do Angular. O aplicativo ASP.NET Core destina-se a ser usado para acesso a dados, autorização e outras questões do lado do servidor. O aplicativo do Angular, que reside no subdiretório *ClientApp*, destina-se a ser usado para todas as questões de interface do usuário.

## <a name="add-pages-images-styles-modules-etc"></a>Adicione páginas, imagens, estilos, módulos, etc.

O diretório *ClientApp* contém um aplicativo padrão da CLI do Angular. Veja a [documentação oficial do Angular](https://https://angular.io) para obter mais informações.

Há pequenas diferenças entre o aplicativo do Angular criado por este modelo e um criado pela CLI do Angular propriamente dita (por meio de `ng new`); no entanto, os recursos do aplicativo são inalterados. O aplicativo criado pelo modelo contém um layout com base em [Bootstrap](https://getbootstrap.com/) e um exemplo básico de roteamento.

## <a name="run-ng-commands"></a>Executar comandos ng

Em um prompt de comando, mude para o subdiretório *ClientApp*:

```console
cd ClientApp
```

Se você tiver a ferramenta `ng` instalada globalmente, você poderá executar qualquer um dos seus comandos. Por exemplo, você poderá executar `ng lint`, `ng test` ou qualquer um dos outros [comandos da CLI do Angular](https://angular.io/cli). No entanto, não é necessário executar `ng serve`, porque o seu aplicativo ASP.NET Core dá conta de servir tanto a parte do lado do servidor quanto a do lado do cliente do seu aplicativo. Internamente, ele usa `ng serve` em desenvolvimento.

Se você não tiver a ferramenta `ng` instalada, execute `npm run ng` em vez dela. Por exemplo, você pode executar `npm run ng lint` ou `npm run ng test`.

## <a name="install-npm-packages"></a>Instalar pacotes npm

Para instalar pacotes npm de terceiros, use um prompt de comando no subdiretório *ClientApp*. Por exemplo:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publicar e implantar

No desenvolvimento, o aplicativo é executado de um modo otimizado para conveniência do desenvolvedor. Por exemplo, pacotes JavaScript incluem mapas de origem (de modo que durante a depuração, você pode ver o código TypeScript original). O aplicativo observa alterações em arquivos TypeScript, HTML e CSS no disco e recompila e recarrega automaticamente quando as detecta.

Em produção, atende a uma versão de seu aplicativo que é otimizada para desempenho. Isso é configurado para ocorrer automaticamente. Quando você publica, a configuração de build emite um build minificado em compilação AoT (Ahead Of Time) do código do lado do cliente. Ao contrário da compilação de desenvolvimento, a compilação de produção não exige que o Node. js seja instalado no servidor (a menos que você tenha habilitado a renderização do lado do servidor (SSR)).

Você pode usar os [métodos padrão de implantação e hospedagem do ASP.NET Core](xref:host-and-deploy/index).

## <a name="run-ng-serve-independently"></a>Executar "ng serve" independentemente

O projeto está configurado para iniciar sua própria instância do servidor da CLI do Angular em segundo plano quando o aplicativo ASP.NET Core é iniciado no modo de desenvolvimento. Isso é conveniente, porque você não precisa executar um servidor separado manualmente.

Há uma desvantagem nessa configuração padrão. Cada vez que você modificar seu código C# e o aplicativo ASP.NET Core precisar ser reiniciado, o servidor da CLI do Angular será reiniciado também. São necessários aproximadamente 10 segundos para iniciar um backup. Se você estiver fazendo edições frequentes de código C# e não quiser esperar a CLI do Angular reiniciar, execute o servidor da CLI do Angular externamente, independentemente do processo do ASP.NET Core. Para fazer isso:

1. Em um prompt de comando, vá para o subdiretório *ClientApp* e inicie o Development Server da CLI do Angular:

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > Use `npm start` para iniciar o Development Server da CLI do Angular, não `ng serve`, de modo que a configuração no *package.json* seja respeitada. Para passar parâmetros adicionais para o servidor da CLI do Angular, adicione-os à linha `scripts` relevante em seu arquivo *package.json*.

2. Modifique o aplicativo ASP.NET Core para, em vez de iniciar uma instância da CLI do Angular própria, usar a externa. Na classe *Startup*, substitua a invocação `spa.UseAngularCliServer` pelo seguinte:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

Quando você iniciar seu aplicativo ASP.NET Core, ele não inicializará um servidor da CLI do Angular. Em vez disso, a instância que você iniciou manualmente é usada. Isso permite a ele iniciar e reiniciar mais rapidamente. Ele não está mais aguardando a CLI do Angular recompilar o aplicativo cliente a cada vez.

### <a name="pass-data-from-net-code-into-typescript-code"></a>Passar dados de código .NET para código do TypeScript

Durante a SSR, convém passar dados por solicitação, do aplicativo ASP.NET Core para o aplicativo do Angular. Por exemplo, você pode transmitir informações de cookie ou algo lido de um banco de dados. Para fazer isso, edite a classe *Startup*. No retorno de chamada para `UseSpaPrerendering`, defina um valor para `options.SupplyData` como o seguinte:

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

O retorno de chamada `SupplyData` permite que você passe dados serializáveis por JSON arbitrários e por solicitação (por exemplo, cadeias de caracteres, boolianos ou números). O código de *main.server.ts* recebe isso como `params.data`. Por exemplo, o exemplo de código anterior passa um valor booliano como `params.data.isHttpsRequest` para o retorno de chamada `createServerRenderer`. Você pode passar isso para outras partes do seu aplicativo de alguma forma compatível com o Angular. Por exemplo, veja como *main.server.ts* passa o valor `BASE_URL` para qualquer componente cujo construtor é declarado para recebê-lo.

### <a name="drawbacks-of-ssr"></a>Desvantagens da SSR

Nem todos os aplicativos se beneficiam da SSR. O principal benefício é o desempenho percebido. Os visitantes que chegam ao aplicativo por uma conexão de rede lenta ou em dispositivos móveis lentos veem a interface do usuário inicial rapidamente, mesmo que leve algum tempo para buscar ou para analisar os pacotes de JavaScript. No entanto, muitos SPAs são usados principalmente em redes de empresa internas e rápidas, em computadores rápidos nos quais o aplicativo aparece quase instantaneamente.

Ao mesmo tempo, há desvantagens significativas em habilitar a SSR. Ele adiciona complexidade ao seu processo de desenvolvimento. O código deve ser executado em dois ambientes diferentes: no lado do cliente e no do servidor (em um ambiente Node.js invocado do ASP.NET Core). Estes são alguns pontos a considerar:

* A SSR requer uma instalação de Node.js nos servidores de produção. Isso ocorre automaticamente para alguns cenários de implantação como Serviços de Aplicativos do Azure, mas não para outros como o Azure Service Fabric.
* Habilitar o sinalizador de build `BuildServerSideRenderer` faz com que o diretório *node_modules* seja publicado. Esta pasta contém mais de 20.000 arquivos, o que aumenta o tempo de implantação.
* Para executar o código em um ambiente Node.js, ele não pode depender da existência de APIs de JavaScript específicas a um navegador, tais como `window` ou `localStorage`. Se seu código (ou alguma biblioteca de terceiros à qual você faz referência) tentar usar essas APIs, você obterá um erro durante a SSR. Por exemplo, não use jQuery porque faz referência a APIs específicas a um navegador em vários locais. Para evitar erros, você deve evitar a SSR ou então evitar APIs ou bibliotecas específicas a um navegador. Você pode encapsular todas as chamadas para essas APIs em verificações para garantir que elas não sejam invocadas durante a SSR. Por exemplo, use uma verificação como a seguinte no código JavaScript ou TypeScript:

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authentication/identity/spa>

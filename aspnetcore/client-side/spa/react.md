---
title: Usar o modelo de projeto do React com o ASP.NET Core
author: SteveSandersonMS
description: Saiba como começar a trabalhar com o modelo de projeto do SPA (Aplicativo de Página Única) do ASP.NET Core para React e criar-aplicativo-do-React.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
uid: spa/react
ms.openlocfilehash: aa08a9d8d06d8b52be564fabf904d4923684d4e4
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248024"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>Usar o modelo de projeto do React com o ASP.NET Core

O modelo de projeto do React atualizado fornece um ponto inicial conveniente para aplicativos do ASP.NET Core usando convenções do React e de CRA [(criar-aplicativo-do-React)](https://github.com/facebookincubator/create-react-app) para implementar uma IU (interface do usuário) avançada do lado do cliente.

O modelo é equivalente à criação de dois projetos: um projeto do ASP.NET Core, para atuar como um back-end de API, e um projeto do React CRA padrão, para atuar como uma interface do usuário, mas com a praticidade de hospedar ambos em um único projeto de aplicativo que pode ser criado e publicado como uma única unidade.

## <a name="create-a-new-app"></a>Criar um novo aplicativo

Se você tiver o ASP.NET Core 2.1 instalado, não será necessário instalar o modelo de projeto React.

Crie um novo projeto de um prompt de comando usando o comando `dotnet new react` em um diretório vazio. Por exemplo, os seguintes comandos criam o aplicativo em um diretório *my-new-app* e mudam para esse diretório:

```console
dotnet new react -o my-new-app
cd my-new-app
```

Execute o aplicativo do Visual Studio ou da CLI do .NET Core:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Abra o arquivo *.csproj* gerado e execute o aplicativo normalmente de lá.

O processo de build restaura dependências npm na primeira execução, o que pode levar vários minutos. Builds subsequentes são muito mais rápidos.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Verifique se você tem uma variável de ambiente chamada `ASPNETCORE_Environment` com valor `Development`. No Windows (em prompts que não são do PowerShell), execute `SET ASPNETCORE_Environment=Development`. No Linux ou macOS, execute `export ASPNETCORE_Environment=Development`.

Execute [dotnet build](/dotnet/core/tools/dotnet-build) para verificar se seu aplicativo compila corretamente. O processo de build restaura dependências npm na primeira execução, o que pode levar vários minutos. Builds subsequentes são muito mais rápidos.

Execute [dotnet run](/dotnet/core/tools/dotnet-run) para iniciar o aplicativo.

---

O modelo de projeto cria um aplicativo ASP.NET Core e um aplicativo do React. O aplicativo ASP.NET Core destina-se a ser usado para acesso a dados, autorização e outras questões do lado do servidor. O aplicativo do React, que reside no subdiretório *ClientApp*, destina-se a ser usado para todas as questões de interface do usuário.

## <a name="add-pages-images-styles-modules-etc"></a>Adicione páginas, imagens, estilos, módulos, etc.

O diretório *ClientApp* é um aplicativo do React CRA padrão. Veja a [documentação oficial do CRA](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) para obter mais informações.

Há pequenas diferenças entre o aplicativo do React criado por este modelo e um criado pelo CRA propriamente dito; no entanto, os recursos do aplicativo são inalterados. O aplicativo criado pelo modelo contém um layout com base em [Bootstrap](https://getbootstrap.com/) e um exemplo básico de roteamento.

## <a name="install-npm-packages"></a>Instalar pacotes npm

Para instalar pacotes npm de terceiros, use um prompt de comando no subdiretório *ClientApp*. Por exemplo:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publicar e implantar

No desenvolvimento, o aplicativo é executado de um modo otimizado para conveniência do desenvolvedor. Por exemplo, pacotes JavaScript incluem mapas de origem (de modo que durante a depuração, você pode ver o código-fonte original). O aplicativo observa alterações em arquivos JavaScript, HTML e CSS no disco e recompila e recarrega automaticamente quando as detecta.

Em produção, atende a uma versão de seu aplicativo que é otimizada para desempenho. Isso é configurado para ocorrer automaticamente. Quando você publica, a configuração de build emite um build minificado e transpilado do seu código do lado do cliente. Diferentemente do build de desenvolvimento, o build de produção não requer que o Node.js esteja instalado no servidor.

Você pode usar os [métodos padrão de implantação e hospedagem do ASP.NET Core](xref:host-and-deploy/index).

## <a name="run-the-cra-server-independently"></a>Executar o servidor CRA independentemente

O projeto está configurado para iniciar sua própria instância do Development Server do CRA em segundo plano quando o aplicativo ASP.NET Core é iniciado no modo de desenvolvimento. Isso é conveniente, porque significa que você não precisa executar um servidor separado manualmente.

Há uma desvantagem nessa configuração padrão. Cada vez que você modificar seu código C# e o aplicativo ASP.NET Core precisar ser reiniciado, o servidor CRA será reiniciado também. São necessários alguns segundos para iniciar um backup. Se você estiver fazendo edições frequentes de código C# e não quiser esperar o servidor CRA reiniciar, execute o servidor CRA externamente, independentemente do processo do ASP.NET Core. Para fazer isso:

1. Adicionar um *. env* do arquivo para o *ClientApp* subdiretório com a seguinte configuração:

    ```
    BROWSER=none
    ```
    
    Isso impedirá o navegador da web seja aberto ao iniciar o servidor CRA externamente.

2. Em um prompt de comando, vá para o subdiretório *ClientApp* e inicie o Development Server do CRA:

    ```console
    cd ClientApp
    npm start
    ```

3. Modifique o aplicativo ASP.NET Core para, em vez de iniciar uma instância do servidor CRA própria, usar a externa. Na classe *Startup*, substitua a invocação `spa.UseReactDevelopmentServer` pelo seguinte:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

Quando você iniciar seu aplicativo ASP.NET Core, ele não inicializará um servidor CRA. Em vez disso, a instância que você iniciou manualmente é usada. Isso permite a ele iniciar e reiniciar mais rapidamente. Ele não aguarda mais que o aplicativo do React seja recompilado a cada vez.

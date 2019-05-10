---
title: Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio Code
author: ricardoserradas
description: Saiba como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio Code
ms.author: ricardoserradas
ms.custom: mvc
ms.date: 04/16/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 64d82835f6a47a458802692c99658b964c07f807
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64889601"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio Code

Por [Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Para solucionar um problema de implantação do Serviço de Aplicativo, confira <xref:host-and-deploy/azure-apps/troubleshoot>.

## <a name="intro"></a>Introdução

Com este tutorial, você aprenderá a criar um aplicativo ASP.NET Core MVC e implantá-lo no Visual Studio Code.

## <a name="set-up"></a>Configurar

- Abra uma [conta do Azure gratuita](https://azure.microsoft.com/free/dotnet/) se você não tiver uma.
- Instale o [SDK do .NET Core](https://dotnet.microsoft.com/download)
- Instale o [Visual Studio Code](https://code.visualstudio.com/Download)
  - Instale a [Extensão do C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) para o Visual Studio Code
  - Instale a [Extensão do Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) no Visual Studio Code e configure-a antes de continuar

## <a name="create-an-aspnet-core-mvc-project"></a>Criar um projeto ASP.NET Core MVC

Usando um terminal, navegue para a pasta em que deseja criar o projeto e use o seguinte comando:

```cmd
> dotnet new mvc
```

Você terá uma estrutura de pastas semelhante à seguinte:

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a>Abri-lo com o Visual Studio Code

Depois que o projeto for criado, abra-o com o Visual Studio Code usando uma das seguintes opções:

### <a name="through-the-command-line"></a>Por meio da linha de comando

Use o seguinte comando na pasta em que você criou o projeto:

```cmd
> code .
```

Se o comando abaixo não funcionar, verifique se a instalação está configurada corretamente referenciando [este link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).

### <a name="through-visual-studio-code-interface"></a>Por meio da interface do Visual Studio Code

- Abra o Visual Studio Code
- No menu, selecione `File > Open Folder`
- Selecione a raiz da pasta em que você criou o projeto MVC

Ao abrir a pasta do projeto, você receberá uma mensagem informando que os ativos necessários para o build e a depuração estão ausentes. Aceite a ajuda para adicioná-los.

![Interface do Visual Studio Code com o projeto carregado](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

Uma pasta `.vscode` será criada com a estrutura do projeto. Ela conterá os seguintes arquivos:

```cmd
launch.json
tasks.json
```

Eles são arquivos de utilitário para ajudá-lo a criar e depurar seu aplicativo Web .NET Core.

## <a name="run-the-app"></a>Executar o aplicativo

Antes de implantar o aplicativo no Azure, verifique se ele está sendo executado corretamente no computador local.

- Pressione F5 para executar o projeto

Seu aplicativo Web iniciará a execução em uma nova guia do navegador padrão. Você poderá observar um aviso de privacidade assim que ele for iniciado. Isso ocorre porque o aplicativo será iniciado usando HTTP e HTTPS, e ele navega para o ponto de extremidade HTTPS por padrão.

![Aviso de privacidade durante a depuração do aplicativo localmente](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

Para manter a sessão de depuração, clique em `Advanced` e, em seguida, `Continue to localhost (unsafe)`.

## <a name="generate-the-deployment-package-locally"></a>Gerar o pacote de implantação localmente

- Abrir o terminal do Visual Studio Code
- Use o seguinte comando para gerar um pacote `Release` para uma subpasta chamada `publish`:
  - `dotnet publish -c Release -o ./publish`
- Uma pasta `publish` será criada com a estrutura do projeto

![Estrutura de pastas de publicação](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Publicar no Serviço de Aplicativo do Azure

Aproveitando a extensão do Serviço de Aplicativo do Azure para Visual Studio Code, siga as etapas a seguir para publicar o site diretamente no Serviço de Aplicativo do Azure.

### <a name="if-youre-creating-a-new-web-app"></a>Se você estiver criando um aplicativo Web

- Clique com o botão direito do mouse na pasta `publish` e selecione `Deploy to Web App...`
- Selecione a assinatura em que deseja criar o aplicativo Web
- Selecione `Create New Web App`
- Insira um nome para o aplicativo Web

A extensão criará o aplicativo Web e começará automaticamente a implantar o pacote nele. Depois que a implantação for concluída, clique em `Browse Website` para validar a implantação.

![Mensagem de implantação bem-sucedida](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

Depois de clicar em `Browse Website`, você navegará para ele usando o navegador padrão:

![Novo aplicativo Web implantado com êxito](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>Se você estiver fazendo uma implantação em um aplicativo Web existente

- Clique com o botão direito do mouse na pasta `publish` e selecione `Deploy to Web App...`
- Selecione a assinatura na qual reside o aplicativo Web existente
- Selecione o aplicativo Web na lista
- O Visual Studio Code perguntará se você deseja substituir o conteúdo existente. Clique em `Deploy` para confirmar

A extensão implantará o conteúdo atualizado no aplicativo Web. Após a conclusão, clique em `Browse Website` para validar a implantação.

![Aplicativo Web existente implantado com êxito](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>Próximas etapas

- [Criar seu primeiro pipeline do Azure DevOps](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>Recursos adicionais

- [Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-overview)
- [Grupo de recursos do Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
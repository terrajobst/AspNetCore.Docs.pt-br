---
title: Suporte ao IIS no tempo de desenvolvimento no Visual Studio para ASP.NET Core
author: guardrex
description: Descubra o suporte para depuração de aplicativos do ASP.NET Core durante a execução com IIS no Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2019
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: e5da4f7202bf31e65c366d6f7de54212f5b0fed7
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259807"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>Suporte ao IIS no tempo de desenvolvimento no Visual Studio para ASP.NET Core

Por [Sourabh Shirhatti](https://twitter.com/sshirhatti) e [Luke Latham](https://github.com/guardrex)

Este artigo descreve o suporte do [Visual Studio](https://visualstudio.microsoft.com) para a depuração de aplicativos do ASP.NET Core em execução com o IIS no Windows Server. Este tópico orienta como habilitar esse cenário e configurar um projeto.

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio para Windows](https://visualstudio.microsoft.com/downloads/)
* Carga de trabalho **ASP.NET e desenvolvimento para a Web**
* Carga de trabalho de **desenvolvimento multiplataforma do .NET Core**
* Certificado de segurança X.509 (para o suporte a HTTPS)

## <a name="enable-iis"></a>Habilitar o IIS

1. No Windows, navegue até **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).
1. Selecione a caixa de seleção **Serviços de Informações da Internet**. Selecione **OK**.

A instalação do IIS pode exigir uma reinicialização do sistema.

## <a name="configure-iis"></a>Configurar o IIS

O IIS deve ter um site configurado com o seguinte:

* **Nome do host** &ndash; Normalmente, o **Site padrão** é usado com um **nome do Host** de `localhost`. No entanto, qualquer site válido do IIS com um nome de host exclusivo funciona.
* **Associação do site**
  * Para aplicativos que exijam HTTPS, crie uma associação à porta 443 com um certificado. Tipicamente, o **Certificado de Desenvolvimento do IIS Express** é usado, mas qualquer certificado válido funciona.
  * Para aplicativos que usam HTTP, confirme a existência de uma associação à porta 80 ou crie uma para um novo site.
  * Uso de uma associação simples para HTTP ou HTTPS. **Não há suporte para a associação simultânea às portas HTTP e HTTPS.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Habilitar o suporte ao IIS no tempo de desenvolvimento no Visual Studio

1. Inicie o Instalador do Visual Studio.
1. Selecione **Modificar** para a instalação do Visual Studio que você planeja usar para o suporte de tempo de desenvolvimento do IIS.
1. Para a carga de trabalho de **desenvolvimento da web e ASP.NET**, localize e instale o componente de**suporte IIS ao tempo de desenvolvimento**.

   O componente está listado na seção **Opcional**, em **Suporte IIS ao tempo de desenvolvimento** no painel **Detalhes da instalação** à direita das cargas de trabalho. O componente instala o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), que é um módulo nativo do IIS necessário para executar aplicativos ASP.NET Core com o IIS.

## <a name="configure-the-project"></a>Configurar o projeto

### <a name="https-redirection"></a>Redirecionamento para HTTPS

Para um novo projeto que exija HTTPS, selecione a caixa de seleção para **Configurar para HTTPS** na janela **Criar um novo Aplicativo Web ASP.NET Core**. A marcação da caixa de seleção adiciona ao aplicativo um [Redirecionamento de HTTPS e Middleware HSTS](xref:security/enforcing-ssl) quando ele é criado.

Para um projeto existente que exija HTTPS, use o Redirecionamento de HTTPS e Middleware HSTS em `Startup.Configure`. Para obter mais informações, consulte <xref:security/enforcing-ssl>.

Para um projeto que usa HTTP, o [Redirecionamento de HTTPS e o Middleware HSTS](xref:security/enforcing-ssl) não são adicionados ao aplicativo. Nenhuma configuração de aplicativo é necessária.

### <a name="iis-launch-profile"></a>Perfil de inicialização do IIS

Crie um novo perfil de inicialização para adicionar suporte ao IIS no tempo de desenvolvimento:

::: moniker range=">= aspnetcore-3.0"

1. Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções**. Selecione **Propriedades**. Abra a guia **Depurar**.
1. Para **Perfil**, selecione o botão **Novo**. Nomeie o perfil "IIS" na janela pop-up. Selecione **OK** para criar o perfil.
1. Para a configuração **Iniciar**, selecione **IIS** da lista.
1. Selecione a caixa de seleção **Iniciar navegador** e forneça a URL de ponto de extremidade.

   Quando o aplicativo exigir HTTPS, use um ponto de extremidade HTTPS (`https://`). Para HTTP, use um ponto de extremidade HTTP (`http://`).

   Forneça o mesmo nome de host e porta usados pela [configuração do IIS especificada anteriormente](#configure-iis), normalmente `localhost`.

   Forneça o nome do aplicativo no final da URL.

   Por exemplo, `https://localhost/WebApplication1` (HTTPS) ou `http://localhost/WebApplication1` (HTTP) são URLs de ponto de extremidade válidas.
1. Na seção **Variáveis de ambiente**, selecione o botão **Adicionar**. Forneça uma variável de ambiente com um **Nome** de `ASPNETCORE_ENVIRONMENT` e um **Valor** de `Development`.
1. Na área de **Configurações do servidor Web**, defina a **URL do aplicativo** com o mesmo valor usado para a URL de ponto de extremidade **Iniciar navegador**.
1. Para a configuração de **Modelo de hospedagem** no Visual Studio 2019 ou posterior, selecione **Padrão** para usar o modelo de hospedagem usado pelo projeto. Se o projeto definir a propriedade `<AspNetCoreHostingModel>` no arquivo de projeto, o valor da propriedade (`InProcess` ou `OutOfProcess`) será usado. Se a propriedade não estiver presente, o modelo do aplicativo de hospedagem padrão será usado, que está em processo. Se o aplicativo exigir uma configuração explícita de modelo de hospedagem diferente da do modelo de hospedagem normal do aplicativo, defina o **Modelo de hospedagem** para `In Process` ou `Out Of Process`, conforme necessário.
1. Salve o perfil.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

1. Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções**. Selecione **Propriedades**. Abra a guia **Depurar**.
1. Para **Perfil**, selecione o botão **Novo**. Nomeie o perfil "IIS" na janela pop-up. Selecione **OK** para criar o perfil.
1. Para a configuração **Iniciar**, selecione **IIS** da lista.
1. Selecione a caixa de seleção **Iniciar navegador** e forneça a URL de ponto de extremidade.

   Quando o aplicativo exigir HTTPS, use um ponto de extremidade HTTPS (`https://`). Para HTTP, use um ponto de extremidade HTTP (`http://`).

   Forneça o mesmo nome de host e porta usados pela [configuração do IIS especificada anteriormente](#configure-iis), normalmente `localhost`.

   Forneça o nome do aplicativo no final da URL.

   Por exemplo, `https://localhost/WebApplication1` (HTTPS) ou `http://localhost/WebApplication1` (HTTP) são URLs de ponto de extremidade válidas.
1. Na seção **Variáveis de ambiente**, selecione o botão **Adicionar**. Forneça uma variável de ambiente com um **Nome** de `ASPNETCORE_ENVIRONMENT` e um **Valor** de `Development`.
1. Na área de **Configurações do servidor Web**, defina a **URL do aplicativo** com o mesmo valor usado para a URL de ponto de extremidade **Iniciar navegador**.
1. Para a configuração de **Modelo de hospedagem** no Visual Studio 2019 ou posterior, selecione **Padrão** para usar o modelo de hospedagem usado pelo projeto. Se o projeto definir a propriedade `<AspNetCoreHostingModel>` no arquivo de projeto, o valor da propriedade (`InProcess` ou `OutOfProcess`) será usado. Se a propriedade não estiver presente, o modelo do aplicativo de hospedagem padrão será usado, que está fora de processo. Se o aplicativo exigir uma configuração explícita de modelo de hospedagem diferente da do modelo de hospedagem normal do aplicativo, defina o **Modelo de hospedagem** para `In Process` ou `Out Of Process`, conforme necessário.
1. Salve o perfil.

::: moniker-end

Quando não estiver usando o Visual Studio, adicione manualmente um perfil de inicialização ao arquivo [launchSettings.json](https://json.schemastore.org/launchsettings) na pasta *Propriedades*. O exemplo a seguir configura o perfil para usar o protocolo HTTPS:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

Confirme se os pontos de extremidade `applicationUrl` e `launchUrl` correspondem e usam o mesmo protocolo que a configuração de associação do IIS, seja HTTP ou HTTPS.

## <a name="run-the-project"></a>Executar o projeto

Execute o Visual Studio como um administrador:

* Confirme se a lista de lista suspensa de configuração de compilação está definida para **Depurar**.
* Defina o [botão Iniciar Depuração](/visualstudio/debugger/debugger-feature-tour) para o perfil do **IIS** e selecione o botão para iniciar o aplicativo.

O Visual Studio poderá solicitar uma reinicialização se não estiver executando como administrador. Se solicitado, reinicie o Visual Studio.

Se for usado um certificado de desenvolvimento não confiável, o navegador poderá exigir a criação de uma exceção para o certificado não confiável.

> [!NOTE]
> A depuração de uma configuração de Compilação de versão com [Apenas Meu Código](/visualstudio/debugger/just-my-code) e otimizações de compilador resulta em uma experiência inadequada. Por exemplo, os pontos de interrupção não são atingidos.

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução ao Gerenciador do IIS no IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Hospedar o ASP.NET Core no Windows com o IIS](xref:host-and-deploy/iis/index)
* [Introdução ao Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module)
* [Referência de configuração do Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module)
* [Impor o HTTPS](xref:security/enforcing-ssl)

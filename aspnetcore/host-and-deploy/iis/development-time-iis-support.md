---
title: Suporte ao IIS no tempo de desenvolvimento no Visual Studio para ASP.NET Core
author: shirhatti
description: Descubra o suporte para depuração de aplicativos do ASP.NET Core quando executado por trás do IIS no Windows Server.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 44570bb28451ce4c5fde12ec77e3856fb5bd3062
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637651"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>Suporte ao IIS no tempo de desenvolvimento no Visual Studio para ASP.NET Core

Por [Sourabh Shirhatti](https://twitter.com/sshirhatti) e [Luke Latham](https://github.com/guardrex)

Este artigo descreve o suporte do [Visual Studio](https://www.visualstudio.com/vs/) a depuração de aplicativos do ASP.NET Core em execução por trás do IIS no Windows Server. Este tópico orienta você sobre como habilitar esse recurso e configurar um projeto.

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio para Windows](https://www.microsoft.com/net/download/windows)
* Carga de trabalho **ASP.NET e desenvolvimento para a Web**
* Carga de trabalho de **desenvolvimento multiplataforma do .NET Core**
* Certificado de segurança X.509

## <a name="enable-iis"></a>Habilitar o IIS

1. Navegue para **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).
1. Selecione a caixa de seleção **Serviços de Informações da Internet**.

![Recursos do Windows mostrando a caixa de seleção Serviços de Informações da Internet marcada como um quadrado preto (não uma marca de seleção), indicando que alguns dos recursos do IIS estão habilitados](development-time-iis-support/_static/enable_iis.png)

A instalação do IIS pode exigir uma reinicialização do sistema.

## <a name="configure-iis"></a>Configurar o IIS

O IIS deve ter um site configurado com o seguinte:

* Um nome do host que corresponda ao nome do host da URL do perfil de inicialização do aplicativo.
* Associação para a porta 443, com um certificado atribuído.

Por exemplo, o **Nome do host** para um site adicionado é definido como "localhost" (o perfil de inicialização também usará "localhost" posteriormente neste tópico). A porta é definida para "443" (HTTPS). O **Certificado de Desenvolvimento do IIS Express** é atribuído ao site, mas nenhum certificado válido funciona:

![Adicione a janela Site no IIS, mostrando o conjunto de associação para o localhost na porta 443 com um certificado atribuído.](development-time-iis-support/_static/add-website-window.png)

Se a instalação do IIS já tiver um **site da Web padrão** com um nome do host que corresponde ao nome do host da URL do perfil de inicialização do aplicativo:

* Adicione uma associação de porta para a porta 443 (HTTPS).
* Atribua um certificado válido para o site.

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Habilitar o suporte ao IIS no tempo de desenvolvimento no Visual Studio

1. Inicie o Instalador do Visual Studio.
1. Selecione o componente **Suporte ao IIS no tempo de desenvolvimento**. O componente está listado como opcional no painel **Resumo** para a carga de trabalho **Desenvolvimento Web e ASP.NET**. O componente instala o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), que é um módulo nativo do IIS necessário para executar aplicativos ASP.NET Core com o IIS.

![Modificando os recursos do Visual Studio: A guia Cargas de trabalho está selecionada. Na seção Web e Nuvem, o painel ASP.NET e desenvolvimento Web é selecionado. À direita, na área Opcional do painel Resumo, há uma caixa de seleção para Suporte ao IIS no tempo de desenvolvimento.](development-time-iis-support/_static/development_time_support.png)

## <a name="configure-the-project"></a>Configurar o projeto

### <a name="https-redirection"></a>Redirecionamento para HTTPS

Para um novo projeto, selecione a caixa de seleção para **Configurar para HTTPS** na janela **Novo Aplicativo Web ASP.NET Core**:

![Janela Novo Aplicativo Web ASP.NET Core com a caixa de seleção Configurar para HTTPS selecionada.](development-time-iis-support/_static/new-app.png)

Em um projeto existente, use o middleware de redirecionamento para HTTPS no `Startup.Configure` chamando o método de extensão [UseHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpspolicybuilderextensions.usehttpsredirection):

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();

    app.UseMvc();
}
```

### <a name="iis-launch-profile"></a>Perfil de inicialização do IIS

Crie um novo perfil de inicialização para adicionar suporte ao IIS no tempo de desenvolvimento:

1. Para **Perfil**, selecione o botão **Novo**. Nomeie o perfil "IIS" na janela pop-up. Selecione **OK** para criar o perfil.
1. Para a configuração **Iniciar**, selecione **IIS** da lista.
1. Selecione a caixa de seleção **Iniciar navegador** e forneça a URL de ponto de extremidade. Use o protocolo HTTPS. Este exemplo usa `https://localhost/WebApplication1`.
1. Na seção **Variáveis de ambiente**, selecione o botão **Adicionar**. Fornecer uma variável de ambiente com uma chave `ASPNETCORE_ENVIRONMENT` e um valor `Development`.
1. Na área **Configurações do Servidor Web**, defina a **URL do Aplicativo**. Este exemplo usa `https://localhost/WebApplication1`.
1. Salve o perfil.

![Janela de propriedades de projeto com a guia Depurar selecionada. As configurações de Perfil e de Inicialização são definidas para o IIS. O recurso de Inicialização do navegador está habilitado com um endereço de https://localhost/WebApplication1. O mesmo endereço também é fornecido no campo URL do Aplicativo da área Configurações do Servidor Web.](development-time-iis-support/_static/project_properties.png)

Como alternativa, adicione manualmente um perfil de inicialização para o arquivo [launchSettings.json](http://json.schemastore.org/launchsettings) no aplicativo:

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

## <a name="run-the-project"></a>Executar o projeto

No Visual Studio:

* Confirme se a lista de lista suspensa de configuração de compilação está definida para **Depurar**.
* Defina o botão Executar para o perfil do **IIS** e selecione o botão para iniciar o aplicativo.

![O botão Executar, na barra de ferramentas do VS, é definido para o perfil do IIS com a lista suspensa de configuração de compilação definida para Versão.](development-time-iis-support/_static/toolbar.png)

O Visual Studio poderá solicitar uma reinicialização se não estiver executando como administrador. Se solicitado, reinicie o Visual Studio.

Se for usado um certificado de desenvolvimento não confiável, o navegador poderá exigir a criação de uma exceção para o certificado não confiável.

> [!NOTE]
> A depuração de uma configuração de Compilação de versão com [Apenas Meu Código](/visualstudio/debugger/just-my-code) e otimizações de compilador resulta em uma experiência inadequada. Por exemplo, os pontos de interrupção não são atingidos.

## <a name="additional-resources"></a>Recursos adicionais

* [Hospedar o ASP.NET Core no Windows com o IIS](xref:host-and-deploy/iis/index)
* [Introdução ao Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module)
* [Referência de configuração do Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module)
* [Impor o HTTPS](xref:security/enforcing-ssl)

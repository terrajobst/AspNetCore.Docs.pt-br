---
title: Hospedar e implantar o ASP.NET Core Blazor no lado do cliente
author: guardrex
description: Veja como hospedar e implantar um aplicativo do Blazor usando o ASP.NET Core, a CDN (Rede de Distribuição de Conteúdo), servidores de arquivos e páginas do GitHub.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: c9822205d38f765cf80748bc2b379c11ec7c1c57
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773600"
---
# <a name="host-and-deploy-aspnet-core-blazor-client-side"></a>Hospedar e implantar o ASP.NET Core Blazor no lado do cliente

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

Com o [modelo de hospedagem do lado do cliente](xref:blazor/hosting-models#client-side):

* O aplicativo do Blazor, suas dependências e o tempo de execução do .NET são baixados no navegador.
* O aplicativo é executado diretamente no thread da interface do usuário do navegador.

Há suporte para as seguintes estratégias de implantação:

* O aplicativo do Blazor é atendido por um aplicativo ASP.NET Core. Esta estratégia é abordada na seção [Implantação hospedada com o ASP.NET Core](#hosted-deployment-with-aspnet-core).
* O aplicativo do Blazor é colocado em um serviço ou um servidor Web de hospedagem estático, em que o .NET não é usado para atender ao aplicativo do Blazor. Essa estratégia é abordada na seção [implantação autônoma](#standalone-deployment) , que inclui informações sobre como hospedar um aplicativo de alto nível do cliente como um subaplicativo do IIS.

## <a name="rewrite-urls-for-correct-routing"></a>Reescrever as URLs para obter o roteamento correto

O roteamento de solicitações para componentes de página em um aplicativo do lado do cliente não é tão simples quanto o roteamento de solicitações em um aplicativo hospedado no lado do servidor. Considere um aplicativo do lado do cliente com dois componentes:

* *Main.razor* &ndash; É carregado na raiz do aplicativo e contém um link para o componente `About` (`href="About"`).
* *About.Razor* &ndash; componente `About`.

Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):

1. O navegador faz uma solicitação.
1. A página padrão é retornada, que é geralmente é *index.html*.
1. A *index.html* inicia o aplicativo.
1. O roteador do Blazor é carregado e o componente `Main` Razor é renderizado.

Na página principal, a seleção do link para o componente `About` funciona no cliente porque o roteador do Blazor impede que o navegador faça uma solicitação na Internet para `www.contoso.com` de `About` e atende ao componente `About` renderizado. Todas as solicitações de pontos de extremidades internos *no aplicativo do lado do cliente* funcionam da mesma maneira: Não são disparadas solicitações baseadas em navegador para os recursos hospedados no servidor na Internet. O roteador trata das solicitações internamente.

Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará. Este recurso não existe no host do aplicativo na Internet; portanto, uma resposta *404 – Não Encontrado* é retornada.

Como os navegadores fazem solicitações aos hosts baseados na Internet de páginas do lado do cliente, os servidores Web e os serviços de hospedagem precisam reescrever todas as solicitações de recursos que não estão fisicamente no servidor para a página *index.html*. Quando a *index.html* for retornada, o roteador do lado do cliente do aplicativo assumirá o controle e responderá com o recurso correto.

Ao implantar em um servidor IIS, você pode usar o módulo de reescrita de URL com o arquivo *Web. config* publicado do aplicativo. Para obter mais informações, consulte a seção [IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Implantação hospedada com o ASP.NET Core

Uma *implantação hospedada* atende ao aplicativo do lado do cliente do Blazor para navegadores de um [aplicativo do ASP.NET Core](xref:index) que é executado em um servidor da web.

O aplicativo do Blazor é incluído com o aplicativo do ASP.NET Core na saída publicada para que ambos sejam implantados juntos. É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core. Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto **Blazor WebAssembly App** (modelo `blazorwasm` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)) com a opção **Hospedado** selecionada.

Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.

Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Implantação autônoma

Uma *implantação autônoma* atende ao aplicativo do lado do cliente do Blazor como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes. Qualquer servidor de arquivos estático é capaz de atender ao aplicativo Blazor.

Ativos de implantação autônomos são publicados na pasta */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist*.

### <a name="iis"></a>IIS

O IIS é um servidor de arquivos estático com capacidade para aplicativos do Blazor. Para configurar o IIS para hospedar o Blazor, confira [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Criar um site estático no IIS).

Os ativos publicados são criados na pasta */bin/Release/{TARGET FRAMEWORK}/publish*. Hospede o conteúdo da pasta *publish* no servidor Web ou no serviço de hospedagem.

#### <a name="webconfig"></a>web.config

Quando um projeto Blazor é publicado, um arquivo *web.config* é criado com a seguinte configuração do IIS:

* Os tipos MIME são definidos para as seguintes extensões de arquivo:
  * *.dll* &ndash; `application/octet-stream`
  * *.json* &ndash; `application/json`
  * *.wasm* &ndash; `application/wasm`
  * *.woff* &ndash; `application/font-woff`
  * *.woff2* &ndash; `application/font-woff`
* A compactação HTTP está habilitada para os seguintes tipos MIME:
  * `application/octet-stream`
  * `application/wasm`
* As regras do Módulo de Reescrita de URL são estabelecidas:
  * Atender ao subdiretório em que residem os ativos estáticos do aplicativo ( *{ASSEMBLY NAME}/dist/{PATH REQUESTED}* ).
  * Criar o roteamento de fallback do SPA, de modo que as solicitações de ativos, que não sejam arquivos, sejam redirecionadas ao documento padrão do aplicativo na pasta de ativos estáticos dele ( *{ASSEMBLY NAME}/dist/index.html*).

#### <a name="install-the-url-rewrite-module"></a>Instalação do Módulo de Regeneração de URL

O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs. Por padrão, o módulo não está instalado e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS). O módulo precisa ser baixado do site do IIS. Use o Web Platform Installer para instalar o módulo:

1. Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI. Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.
1. Copie o instalador para o servidor. Execute o instalador. Selecione o botão **Instalar** e aceite os termos de licença. Uma reinicialização do servidor não será necessária após a conclusão da instalação.

#### <a name="configure-the-website"></a>Configuração do site

Defina o **Caminho físico** do site como a pasta do aplicativo. A pasta contém:

* O arquivo *web.config* que o IIS usa para configurar o site, incluindo as regras de redirecionamento e os tipos de conteúdo do arquivo necessários.
* A pasta de ativos estática do aplicativo.

#### <a name="host-as-an-iis-sub-app"></a>Hospedar como um subaplicativo do IIS

Se um aplicativo autônomo for hospedado como um subaplicativo do IIS, execute um dos seguintes procedimentos:

* Desabilite o manipulador do módulo ASP.NET Core herdado.

  Remova o manipulador do arquivo *Web. config* publicado de um aplicativo mais incrivelmente adicionando uma `<handlers>` seção ao arquivo:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Desabilite a herança da seção `<system.webServer>` do aplicativo raiz (pai) usando um `<location>` elemento `inheritInChildApplications` com definido `false`como:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

A remoção do manipulador ou a desabilitação da herança é executada além da [configuração do caminho base do aplicativo](xref:host-and-deploy/blazor/index#app-base-path). Defina o caminho base do aplicativo no arquivo *index.html* do aplicativo do alias do IIS usado ao configurar o subaplicativo no IIS.

#### <a name="troubleshooting"></a>Solução de problemas

Se um *500 – Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Regeneração de URL está instalado. Quando o módulo não estiver instalado, o arquivo *web.config* não poderá ser analisado pelo IIS. Isso impede que o Gerenciador do IIS carregue a configuração do site e que o site atenda aos arquivos estáticos do Blazor.

Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Armazenamento do Azure

A hospedagem de arquivos estáticos de [armazenamento do Azure](/azure/storage/) permite Hospedagem de aplicativos com mais de um servidor. Nomes de domínio personalizados, CDN (Rede de Distribuição de Conteúdo) do Azure e HTTPS são compatíveis.

Quando o serviço de blob está habilitado para hospedagem de site estático em uma conta de armazenamento:

* Defina o **Nome do documento de índice** como `index.html`.
* Defina o **Caminho do documento de erro** como `index.html`. Os componentes do Razor e outros pontos de extremidade que não são arquivos não residem em caminhos físicos no conteúdo estático armazenado pelo serviço de blob. Quando uma solicitação para um desses recursos é recebida, a qual deve passar pelo Blazor, o erro *404 - Não Encontrado* gerado pelo serviço de blob encaminha a solicitação para o **Caminho do documento de erro**. O blob *index.html* retorna, e o roteador do Blazor carrega e processa o caminho.

Para saber mais, confira [Hospedagem de site estático no Armazenamento do Azure](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

O arquivo *nginx.conf* a seguir é simplificado para mostrar como configurar o Nginx para enviar o arquivo *index.html* sempre que ele não puder encontrar um arquivo correspondente no disco.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).

### <a name="nginx-in-docker"></a>Nginx no Docker

Para hospedar o Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem do Nginx baseada no Alpine. Atualize o Dockerfile para copiar o arquivo *nginx.config* no contêiner.

Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a>Páginas do GitHub

Para lidar com as reescritas de URL, adicione um arquivo *404.html* com um script que manipule o redirecionamento de solicitação para a página *index.html*. Para obter uma implementação de exemplo fornecida pela comunidade, confira [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Aplicativos de página única das Páginas do GitHub) ([rafrex/spa-github-pages no GitHub](https://github.com/rafrex/spa-github-pages#readme)). Um exemplo usando a abordagem da comunidade pode ser visto em [blazor-demo/blazor-demo.github.io no GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site dinâmico](https://blazor-demo.github.io/)).

Ao usar um site de projeto em vez de um site de empresa, adicione ou atualize a tag `<base>` no *index.html*. Defina o valor de atributo `href` com o nome do repositório GitHub com uma barra à direita (por exemplo, `my-repository/`).

## <a name="host-configuration-values"></a>Valores de configuração do host

Os aplicativos do Blazor que usam o [modelo de hospedagem do lado do cliente](xref:blazor/hosting-models#client-side) podem aceitar os seguintes valores de configuração do host como argumentos de linha de comando no tempo de execução no ambiente de desenvolvimento.

### <a name="content-root"></a>Raiz do conteúdo

O argumento `--contentroot` define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo. Nos exemplos a seguir, `/content-root-path` é o caminho raiz do conteúdo do aplicativo.

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**. Esta configuração é usada quando o aplicativo é executado com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**. A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Caminho base

O `--pathbase` argumento define o caminho base do aplicativo para um aplicativo executado localmente com um caminho de URL relativo não raiz ( `<base>` a `href` marca é definida para um caminho diferente `/` de para preparo e produção). Nos exemplos a seguir, `/relative-URL-path` é o caminho base do aplicativo. Para obter mais informações, consulte [caminho base do aplicativo](xref:host-and-deploy/blazor/index#app-base-path).

> [!IMPORTANT]
> Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`. Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/">` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```console
  dotnet run --pathbase=/relative-URL-path
  ```

* Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**. Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**. A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URLs

O argumento `--urls` define os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**. Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**. A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Configurar o vinculador

O Blazor executa a vinculação de IL (linguagem intermediária) em cada build para remover a IL desnecessária dos assemblies de saída. A vinculação de assembly pode ser controlada no build. Para obter mais informações, consulte <xref:host-and-deploy/blazor/configure-linker>.

---
uid: web-api/overview/security/enabling-cross-origin-requests-in-web-api
title: Permitindo solicitações entre origens na API Web ASP.NET 2 | Microsoft Docs
author: MikeWasson
description: Mostra como dar suporte a compartilhamento de recursos entre origens (CORS) na API Web ASP.NET.
ms.author: riande
ms.date: 01/29/2019
ms.assetid: 9b265a5a-6a70-4a82-adce-2d7c56ae8bdd
msc.legacyurl: /web-api/overview/security/enabling-cross-origin-requests-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 97a0027194b019b09e220493dcb593e682027fe3
ms.sourcegitcommit: d22b3c23c45a076c4f394a70b1c8df2fbcdf656d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55428441"
---
<a name="enable-cross-origin-requests-in-aspnet-web-api-2"></a>Habilitar solicitações entre origens na API Web ASP.NET 2
====================
por [Mike Wasson](https://github.com/MikeWasson)

> Segurança do navegador impede que uma página da web faça solicitações AJAX para outro domínio. Essa restrição é chamada de *política de mesma origem*e impede que um site mal-intencionado lendo dados confidenciais de outro site. No entanto, às vezes, talvez queira permitir que outros sites chamar sua API da web.
>
> [Entre o compartilhamento de recursos de origem](http://www.w3.org/TR/cors/) (CORS) é um padrão W3C que permite que um servidor relaxar a política de mesma origem. Usando o CORS, um servidor pode explicitamente permitir algumas solicitações entre origens enquanto rejeita outras. O CORS é mais seguro e flexível que técnicas anteriores, como [JSONP](http://en.wikipedia.org/wiki/JSONP). Este tutorial mostra como habilitar o CORS em seu aplicativo de API da Web.
>
> ## <a name="software-used-in-the-tutorial"></a>Software usado no tutorial
>
> - [Visual Studio](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
> - API Web 2.2

## <a name="introduction"></a>Introdução

Este tutorial demonstra o que suporte a CORS na API Web ASP.NET. Vamos começar criando dois projetos do ASP.NET – um chamado "serviço Web", que hospeda um controlador de API da Web, e outro chamado "WebClient", que chama o serviço Web. Porque os dois aplicativos hospedados em diferentes domínios, uma solicitação de AJAX do WebClient para o serviço Web é uma solicitação entre origens.

![](enabling-cross-origin-requests-in-web-api/_static/image1.png)

### <a name="what-is-same-origin"></a>O que é "mesma origem"?

Duas URLs têm a mesma origem se eles tiverem as portas, hosts e esquemas idênticas. ([RFC 6454](http://tools.ietf.org/html/rfc6454))

Essas duas URLs têm a mesma origem:

- `http://example.com/foo.html`
- `http://example.com/bar.html`

Essas URLs tem diferentes origens que o anterior dois:

- `http://example.net` -Domínio diferente
- `http://example.com:9000/foo.html` -Porta diferente
- `https://example.com/foo.html` -Esquema diferente
- `http://www.example.com/foo.html` -Subdomínio diferente

> [!NOTE]
> Internet Explorer não considera a porta ao comparar as origens.

## <a name="create-the-webservice-project"></a>Criar o projeto WebService

> [!NOTE]
> Esta seção pressupõe que você já sabe como criar projetos de API da Web. Caso contrário, consulte [Introdução ao ASP.NET Web API](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).

1. Inicie o Visual Studio e crie um novo **aplicativo Web ASP.NET (.NET Framework)** projeto.
2. No **novo aplicativo Web ASP.NET** caixa de diálogo, selecione o **vazia** modelo de projeto. Sob **adicionar pastas e os principais referências para**, selecione o **API da Web** caixa de seleção.

   ![Novo diálogo de projeto do ASP.NET no Visual Studio](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog.png)

3. Adicionar um controlador de API da Web chamado `TestController` com o código a seguir:

   [!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample1.cs)]

4. Você pode executar o aplicativo localmente ou implantar no Azure. (Para as capturas de tela neste tutorial, o aplicativo é implantado para aplicativos de Web do serviço de aplicativo do Azure.) Para verificar se a API da web está funcionando, navegue até `http://hostname/api/test/`, onde *hostname* é o domínio onde você implantou o aplicativo. Você deve ver o texto de resposta, &quot;obter: Mensagem de teste&quot;.

   ![Mensagem de teste de mostrando de navegador da Web](enabling-cross-origin-requests-in-web-api/_static/image4.png)

## <a name="create-the-webclient-project"></a>Criar o projeto de WebClient

1. Criar outra **aplicativo Web ASP.NET (.NET Framework)** do projeto e selecione o **MVC** modelo de projeto. Opcionalmente, selecione **alterar autenticação** > **sem autenticação**. Autenticação não é necessário para este tutorial.

   ![Modelo MVC na caixa de diálogo Novo projeto ASP.NET no Visual Studio](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog-mvc.png)

2. Na **Gerenciador de soluções**, abra o arquivo *Views/Home/Index.cshtml*. Substitua o código nesse arquivo com o seguinte:

   [!code-cshtml[Main](enabling-cross-origin-requests-in-web-api/samples/sample2.cshtml?highlight=13)]

   Para o *serviceUrl* variável, use o URI do aplicativo de serviço Web.

3. Executar o aplicativo de WebClient localmente ou publicá-lo em outro site.

Quando você clica no botão "Experimente", uma solicitação AJAX é enviada para o aplicativo de serviço Web usando o método HTTP listado na caixa suspensa (GET, POST ou PUT). Isso permite que você examinar as solicitações entre origens diferentes. Atualmente, o aplicativo de serviço Web não dá suporte a CORS, portanto, se você clicar no botão, você obterá um erro.

!['Try' erro no navegador](enabling-cross-origin-requests-in-web-api/_static/image7.png)

> [!NOTE]
> Se você observar o tráfego HTTP em uma ferramenta como [Fiddler](https://www.telerik.com/fiddler), você verá que o navegador envie a solicitação GET e a solicitação for bem-sucedida, mas a chamada do AJAX retorna um erro. É importante entender a política de mesma origem não impede que o navegador do *enviando* a solicitação. Em vez disso, ele impede que o aplicativo vendo os *resposta*.

![Depurador da web Fiddler mostrando solicitações da web](enabling-cross-origin-requests-in-web-api/_static/image8.png)

## <a name="enable-cors"></a>Habilitar o CORS

Agora vamos habilitar CORS no aplicativo do serviço Web. Primeiro, adicione o pacote NuGet de CORS. No Visual Studio, do **ferramentas** menu, selecione **Gerenciador de pacotes NuGet**, em seguida, selecione **Package Manager Console**. Na janela do Console do Gerenciador de pacotes, digite o seguinte comando:

[!code-powershell[Main](enabling-cross-origin-requests-in-web-api/samples/sample3.ps1)]

Este comando instala o pacote mais recente e atualiza todas as dependências, incluindo as bibliotecas de API da Web principal. Use o `-Version` sinalizador para direcionar uma versão específica. O pacote CORS requer Web API 2.0 ou posterior.

Abra o arquivo *App\_Start/WebApiConfig.cs*. Adicione o seguinte código para o **Webapiconfig** método:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample4.cs?highlight=9)]

Em seguida, adicione a **[EnableCors]** atributo para o `TestController` classe:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample5.cs?highlight=3,7)]

Para o *origens* parâmetro, use o URI de onde você implantou o aplicativo de WebClient. Isso permite que solicitações entre origens do WebClient, enquanto ainda desautorizando todas as outras solicitações entre domínios. Posteriormente, descreverei os parâmetros para **[EnableCors]** em mais detalhes.

Não inclua uma barra invertida no final de *origens* URL.

Reimplante o aplicativo de serviço Web atualizado. Você não precisa atualizar o WebClient. Agora a solicitação AJAX do WebClient deve ser bem-sucedida. Os métodos GET, PUT e POST todos são permitidos.

![Mensagem de teste com êxito de mostrando de navegador da Web](enabling-cross-origin-requests-in-web-api/_static/image9.png)

## <a name="how-cors-works"></a>Como funciona o CORS

Esta seção descreve o que acontece em uma solicitação CORS no nível das mensagens HTTP. É importante entender como funciona o CORS, para que você possa configurar o **[EnableCors]** atributo corretamente e solucionar problemas se as coisas não funcionam conforme o esperado.

A especificação CORS apresenta vários novos cabeçalhos HTTP que permitem que solicitações entre origens. Se um navegador oferece suporte a CORS, ele define esses cabeçalhos automaticamente para solicitações entre origens; Você não precisa fazer nada especial em seu código JavaScript.

Aqui está um exemplo de uma solicitação entre origens. O cabeçalho de "Origem" fornece o domínio do site que está fazendo a solicitação.

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample6.cmd?highlight=5)]

Se o servidor permite que a solicitação, ele define o cabeçalho Access-Control-Allow-Origin. O valor desse cabeçalho corresponde o cabeçalho de origem, ou é o valor de curinga "\*", o que significa que qualquer origem é permitida.

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample7.cmd?highlight=5)]

Se a resposta não incluir o cabeçalho Access-Control-Allow-Origin, a solicitação AJAX falha. Especificamente, o navegador não permite a solicitação. Mesmo se o servidor retorna uma resposta bem-sucedida, o navegador não disponibiliza a resposta para o aplicativo cliente.

**Solicitações de simulação**

Para algumas solicitações CORS, o navegador envia uma solicitação adicional, chamada de uma "solicitação de simulação," antes de enviar a solicitação real para o recurso.

O navegador pode ignorar a solicitação de simulação se as seguintes condições forem verdadeiras:

- O método de solicitação é GET, HEAD ou POST, *e*
- O aplicativo não definir quaisquer cabeçalhos de solicitação que não seja Accept, Accept-Language, Content-Language, Content-Type ou última--ID do evento, *e*
- O cabeçalho Content-Type (se definido) é um dos seguintes:

    - application/x-www-form-urlencoded
    - multipart/form-data
    - texto/simples

A regra sobre cabeçalhos de solicitação se aplica aos cabeçalhos que o aplicativo define chamando **setRequestHeader** sobre o **XMLHttpRequest** objeto. (A especificação CORS chama esses cabeçalhos de solicitação"autor".) A regra não se aplica aos cabeçalhos de *navegador* pode definir, como o agente do usuário, o Host ou o comprimento do conteúdo.

Aqui está um exemplo de uma solicitação de simulação:

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample8.cmd?highlight=4-5)]

A solicitação de simulação usa o método HTTP OPTIONS. Ele inclui dois cabeçalhos especiais:

- Access-Control-Request-Method: O método HTTP que será usado para a solicitação real.
- Access-Control-Request-Headers: Uma lista de cabeçalhos de solicitação que o *aplicativo* configurado na solicitação real. (Novamente, isso não inclui cabeçalhos que define o navegador.)

Aqui está um exemplo de resposta, supondo que o servidor permite que a solicitação:

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample9.cmd?highlight=6-7)]

A resposta inclui um cabeçalho Access-Control-Allow-Methods que lista os métodos permitidos e, opcionalmente, um cabeçalho Access-Control-Allow-Headers, que lista os cabeçalhos permitidos. Se a solicitação de simulação for bem-sucedida, o navegador envia a solicitação real, conforme descrito anteriormente.

Ferramentas usadas comumente para testar os pontos de extremidade com solicitações de simulação OPTIONS (por exemplo, [Fiddler](https://www.telerik.com/fiddler) e [Postman](https://www.getpostman.com/)) não enviam os cabeçalhos necessários de opções por padrão. Confirme se o `Access-Control-Request-Method` e `Access-Control-Request-Headers` cabeçalhos são enviados com a solicitação e cabeçalhos de opções cheguem ao aplicativo por meio do IIS.

Para configurar o IIS para permitir que um aplicativo ASP.NET receber e lidar com a opção as solicitações, adicione a seguinte configuração para o aplicativo *Web. config* arquivo no `<system.webServer><handlers>` seção:

```xml
<system.webServer>
  <handlers>
    <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
    <remove name="OPTIONSVerbHandler" />
    <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="*" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
  </handlers>
</system.webServer>
```

A remoção de `OPTIONSVerbHandler` impede que o IIS tratar as solicitações de opções. A substituição de `ExtensionlessUrlHandler-Integrated-4.0` permite opções solicitações cheguem ao aplicativo, porque o registro do módulo padrão permite somente solicitações GET, HEAD, POST e depuração com URLs sem extensão.

## <a name="scope-rules-for-enablecors"></a>Regras de escopo para [EnableCors]

Você pode habilitar o CORS por ação, por controlador ou globalmente para todos os controladores de API da Web em seu aplicativo.

**Por ação**

Para habilitar o CORS para uma única ação, defina as **[EnableCors]** atributo no método de ação. O exemplo a seguir habilita o CORS para o `GetItem` somente no método.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample10.cs)]

**Por controlador**

Se você definir **[EnableCors]** na classe do controlador, ele se aplica a todas as ações no controlador. Para desabilitar CORS para uma ação, adicione a **[DisableCors]** atributo à ação. O exemplo a seguir habilita o CORS para cada método, exceto `PutItem`.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample11.cs)]

**Globalmente**

Para habilitar o CORS para todos os controladores de API da Web em seu aplicativo, passe uma **EnableCorsAttribute** da instância para o **EnableCors** método:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample12.cs)]

Se você definir o atributo em mais de um escopo, a ordem de precedência é:

1. Ação
2. Controlador
3. Global

## <a name="set-the-allowed-origins"></a>Defina as origens permitidas

O *origens* parâmetro do **[EnableCors]** atributo especifica quais origens têm permissão para acessar o recurso. O valor é uma lista separada por vírgulas das origens permitidas.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample13.cs)]

Você também pode usar o valor de curinga "\*" para permitir solicitações de qualquer origem.

Considere cuidadosamente antes de permitir que solicitações de qualquer origem. Isso significa que, literalmente, qualquer site possa fazer chamadas AJAX para sua API da web.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample14.cs)]

## <a name="set-the-allowed-http-methods"></a>Defina os métodos HTTP permitidos

O *métodos* parâmetro do **[EnableCors]** atributo especifica quais métodos HTTP têm permissão para acessar o recurso. Para permitir que todos os métodos, use o valor de curinga "\*". O exemplo a seguir permite que somente as solicitações GET e POST.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample15.cs)]

## <a name="set-the-allowed-request-headers"></a>Definir os cabeçalhos de solicitação permitido

Este artigo descreveu anteriormente como uma solicitação de simulação pode incluir um cabeçalho Access-Control-Request-Headers, listando os cabeçalhos HTTP definidos pelo aplicativo (os chamados "author cabeçalhos de solicitação"). O *cabeçalhos* parâmetro do **[EnableCors]** atributo especifica quais cabeçalhos de solicitação do autor são permitidos. Para permitir que todos os cabeçalhos, defina *cabeçalhos* para "\*". Para cabeçalhos específicos de lista de permissões, defina *cabeçalhos* a uma lista separada por vírgula de cabeçalhos permitidos:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample16.cs)]

No entanto, os navegadores não são totalmente consistentes em como eles definir Access-Control-Request-Headers. Por exemplo, o Chrome atualmente inclui "origem". FireFox não inclui cabeçalhos padrão, como "Aceitar", mesmo quando o aplicativo define-los no script.

Se você definir *cabeçalhos* para qualquer coisa diferente de "\*", você deve incluir pelo menos "accept", "content-type" e "origem", além de quaisquer cabeçalhos personalizados que você deseja dar suporte.

## <a name="set-the-allowed-response-headers"></a>Definir os cabeçalhos de resposta permitidos

Por padrão, o navegador não expõe todos os cabeçalhos de resposta para o aplicativo. Os cabeçalhos de resposta que estão disponíveis por padrão são:

- Cache-Control
- Idioma do conteúdo
- Tipo de conteúdo
- Expira
- Última modificação
- Pragma

A especificação CORS chama esses [cabeçalhos de resposta simples](https://dvcs.w3.org/hg/cors/raw-file/tip/Overview.html#simple-response-header). Para disponibilizar outros cabeçalhos para o aplicativo, defina as *exposedHeaders* parâmetro do **[EnableCors]**.

No exemplo a seguir, o controlador `Get` método define um cabeçalho personalizado chamado 'X-Custom-Header'. Por padrão, o navegador não irá expor esse cabeçalho em uma solicitação entre origens. Para disponibilizar o cabeçalho, incluir 'X-Custom-Header' em *exposedHeaders*.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample17.cs)]

## <a name="pass-credentials-in-cross-origin-requests"></a>Passar credenciais nas solicitações entre origens

Credenciais exigem tratamento especial em uma solicitação CORS. Por padrão, o navegador não envia todas as credenciais com uma solicitação entre origens. As credenciais incluem cookies, bem como os esquemas de autenticação HTTP. Para enviar as credenciais com uma solicitação entre origens, o cliente deve definir **XMLHttpRequest.withCredentials** como true.

Usando o **XMLHttpRequest** diretamente:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample18.cs)]

No jQuery:

[!code-javascript[Main](enabling-cross-origin-requests-in-web-api/samples/sample19.js)]

Além disso, o servidor deve permitir que as credenciais. Para permitir credenciais entre origens na API da Web, defina as **SupportsCredentials** propriedade como true na **[EnableCors]** atributo:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample20.cs)]

Se essa propriedade for true, a resposta HTTP incluirá um cabeçalho Access-Control-Allow-Credentials. Esse cabeçalho informa ao navegador que o servidor permite que as credenciais para uma solicitação entre origens.

Se o navegador envia as credenciais, mas a resposta não inclui um cabeçalho Access-Control-Allow-Credentials válido, o navegador não exporá a resposta para o aplicativo e a solicitação AJAX falhar.

Tenha cuidado sobre a configuração **SupportsCredentials** como true, porque significa que um site em outro domínio pode enviar credenciais do usuário conectado à sua API da Web em nome do usuário, sem que o usuário estar ciente. A especificação CORS também declara que a configuração *origens* à &quot; \* &quot; não é válido se **SupportsCredentials** é verdadeiro.

## <a name="custom-cors-policy-providers"></a>Provedores de política CORS personalizados

O **[EnableCors]** atributo implementa o **ICorsPolicyProvider** interface. Você pode fornecer sua própria implementação, criando uma classe que deriva de **atributo** e implementa **ICorsProlicyProvider**.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample21.cs)]

Agora você pode aplicar o atributo de qualquer lugar que você colocaria **[EnableCors]**.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample22.cs)]

Por exemplo, um provedor de política CORS personalizado pode ler as configurações de um arquivo de configuração.

Como uma alternativa ao uso de atributos, você pode registrar um **ICorsPolicyProviderFactory** objeto cria **ICorsPolicyProvider** objetos.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample23.cs)]

Para definir a **ICorsPolicyProviderFactory**, chame o **SetCorsPolicyProviderFactory** método de extensão na inicialização, da seguinte maneira:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample24.cs)]

## <a name="browser-support"></a>Suporte ao navegador

O pacote de CORS da API Web é uma tecnologia do lado do servidor. O navegador do usuário também precisa oferecer suporte a CORS. Felizmente, as versões atuais de todos os principais navegadores incluem [suporte para CORS](http://caniuse.com/cors).

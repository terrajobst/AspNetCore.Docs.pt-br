---
uid: web-api/overview/security/working-with-ssl-in-web-api
title: Trabalhando com SSL na API Web | Microsoft Docs
author: MikeWasson
description: Mostra como usar o SSL com a API da Web do ASP.NET, incluindo o uso de certificados de cliente SSL.
ms.author: riande
ms.date: 02/22/2019
ms.assetid: 97f6164f-59cf-45c0-b820-e4aa29b45396
msc.legacyurl: /web-api/overview/security/working-with-ssl-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 69c0d217f605096d968435c062ee9931f8dff75f
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56744099"
---
<a name="working-with-ssl-in-web-api"></a>Trabalhando com SSL na API Web
====================
por [Mike Wasson](https://github.com/MikeWasson)

Vários esquemas de autenticação comuns não são seguras por HTTP puro. Em particular, a autenticação básica e autenticação de formulários enviam credenciais sem criptografia. Para ser seguro, esses esquemas de autenticação *deve* usar SSL. Além disso, os certificados de cliente SSL podem ser usados para autenticar clientes.

## <a name="enabling-ssl-on-the-server"></a>Habilitar o SSL no servidor

Para configurar o SSL no IIS 7 ou posterior:

- Criar ou obter um certificado. Para testar, você pode criar um certificado autoassinado.
- Adicione uma associação de HTTPS.

Para obter detalhes, consulte [como configurar o SSL no IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).

Para testes locais, você pode habilitar o SSL no IIS Express do Visual Studio. Na janela Propriedades, defina **SSL habilitado** à **verdadeiro**. Observe o valor de **URL do SSL**; use essa URL para testar as conexões HTTPS.

![](working-with-ssl-in-web-api/_static/image1.png)

### <a name="enforcing-ssl-in-a-web-api-controller"></a>Impondo o SSL em um controlador da API da Web

Se você tiver um HTTPS e uma associação HTTP, os clientes ainda podem usar HTTP para acessar o site. Você pode permitir que alguns recursos estejam disponíveis por meio de HTTP, enquanto outros recursos exigem SSL. Nesse caso, use um filtro de ação para exigir SSL para os recursos protegidos. O código a seguir mostra um filtro de autenticação de API da Web que verifica o SSL:

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample1.cs)]

Adicione esse filtro às ações de API da Web que exigem SSL:

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample2.cs)]

## <a name="ssl-client-certificates"></a>Certificados de cliente SSL

O SSL fornece autenticação usando certificados de infraestrutura de chave pública. O servidor deve fornecer um certificado que autentica o servidor para o cliente. É menos comum para o cliente fornecer um certificado para o servidor, mas essa é uma opção para autenticar clientes. Para usar certificados de cliente com SSL, você precisa de uma maneira para distribuir certificados autoassinados para seus usuários. Para muitos tipos de aplicativo, isso não será uma boa experiência do usuário, mas em alguns ambientes (por exemplo, enterprise) pode ser viável.

| Vantagens | Desvantagens |
| --- | --- |
| -As credenciais de certificado são mais seguras do que o nome de usuário e senha. -SSL fornece um canal seguro completo, com autenticação, a integridade da mensagem e a criptografia de mensagens. | -Você deve obter e gerenciar certificados PKI. -A plataforma de cliente deve oferecer suporte a certificados de cliente SSL. |

Para configurar o IIS para aceitar certificados de cliente, abra o Gerenciador do IIS e execute as seguintes etapas:

1. Clique no nó do site no modo de exibição de árvore.
2. Clique duas vezes o **configurações de SSL** recurso no painel central.
3. Sob **certificados de cliente**, selecione uma destas opções: 

    - **Aceitar**: IIS aceitará um certificado do cliente, mas não exigem um.
    - **Exigir**: Exigir um certificado de cliente. (Para habilitar essa opção, você também deve selecionar "Exigir SSL")

Você também pode definir essas opções no arquivo applicationHost. config:

[!code-xml[Main](working-with-ssl-in-web-api/samples/sample3.xml)]

O **SslNegotiateCert** sinalizador significa IIS aceitará um certificado do cliente, mas não exige um (equivalente à opção "Aceitar" no Gerenciador do IIS). Para exigir um certificado, defina as **SslRequireCert** sinalizador. Para teste, você também pode definir essas opções no IIS Express, em que o applicationhost local. Arquivo de configuração localizado em "Documents\IISExpress\config".

### <a name="creating-a-client-certificate-for-testing"></a>Criação de um certificado de cliente para testes

Para fins de teste, você pode usar [MakeCert.exe](/windows/desktop/SecCrypto/makecert) para criar um certificado de cliente. Primeiro, crie uma autoridade raiz de teste:

[!code-console[Main](working-with-ssl-in-web-api/samples/sample4.cmd)]

Makecert solicitará que você insira uma senha para a chave privada.

Em seguida, adicione o certificado para o teste "Raiz autoridades de certificação confiáveis" do servidor armazenar, da seguinte maneira:

1. Abra o MMC.
2. Sob **arquivo**, selecione **Adicionar/Remover Snap-In**.
3. Selecione **conta de computador**.
4. Selecione **computador Local** e conclua o assistente.
5. No painel de navegação, expanda o nó de "Raiz autoridades de certificação confiáveis".
6. Sobre o **ação** , aponte para **todas as tarefas**e, em seguida, clique em **importação** para iniciar o Assistente de importação de certificado.
7. Navegue até o arquivo de certificado, TempCA.cer.
8. Clique em **aberto**, em seguida, clique em **próxima** e conclua o assistente. (Você será solicitado a reinserir a senha.)

Agora, crie um certificado de cliente que está assinado pelo certificado primeiro:

[!code-console[Main](working-with-ssl-in-web-api/samples/sample5.cmd)]

### <a name="using-client-certificates-in-web-api"></a>Usando certificados de cliente na API Web

No lado do servidor, você pode obter o certificado do cliente chamando [GetClientCertificate](https://msdn.microsoft.com/library/system.net.http.httprequestmessageextensions.getclientcertificate.aspx) na mensagem de solicitação. O método retornará nulo se não houver nenhum certificado do cliente. Caso contrário, retornará um **X509Certificate2** instância. Use esse objeto para obter informações de certificado, como o assunto e emissor. Em seguida, você pode usar essas informações para autenticação e/ou autorização.

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample6.cs)]

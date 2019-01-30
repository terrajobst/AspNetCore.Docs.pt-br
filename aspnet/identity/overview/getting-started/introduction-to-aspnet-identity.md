---
uid: identity/overview/getting-started/introduction-to-aspnet-identity
title: Introdução ao ASP.NET Identity | Microsoft Docs
author: jongalloway
description: O sistema de associação do ASP.NET foi introduzido com o ASP.NET 2.0 back em 2005 e desde a época houve muitas alterações em geralmente de aplicativos de web de maneiras...
ms.author: riande
ms.date: 01/22/2019
ms.assetid: 38717fc1-5989-43cf-952d-4007cc1dd923
msc.legacyurl: /identity/overview/getting-started/introduction-to-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: 4a545e52d2d9ea04a10c37c116fd326c60de9f8f
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236439"
---
<a name="introduction-to-aspnet-identity"></a>Introdução ao ASP.NET Identity
====================

> O sistema de associação do ASP.NET foi introduzido com o ASP.NET 2.0 voltar em 2005 e desde a época houve muitas alterações das maneiras a aplicativos da web geralmente lidar com a autenticação e autorização. O ASP.NET Identity é um novo olhar sobre o que o sistema de associação deve ser durante a criação de aplicativos modernos para a web, telefone ou tablet.


## <a name="background-membership-in-aspnet"></a>Segundo plano: Associação no ASP.NET

### <a name="aspnet-membership"></a>Associação do ASP.NET

[Associação do ASP.NET](https://msdn.microsoft.com/library/yh26yfzy(v=VS.100).aspx) foi projetado para resolver os requisitos de associação de site que foram comuns no 2005, que envolvia a autenticação de formulários e um banco de dados do SQL Server para nomes de usuário, senhas e dados de perfil. Atualmente, há uma matriz mais ampla de opções de armazenamento de dados para aplicativos da web e a maioria dos desenvolvedores deseja permitir que seus sites usar provedores de identidade social para a funcionalidade de autenticação e autorização. As limitações de design da associação do ASP.NET dificultam a essa transição:

- O esquema de banco de dados foi projetado para o SQL Server e você não pode alterá-lo. Você pode adicionar informações de perfil, mas os dados adicionais são compactados em uma tabela diferente, o que torna difícil para o acesso por qualquer meio, exceto por meio da API do provedor de perfil.
- O sistema do provedor permite que você altere o repositório de dados de backup, mas o sistema foi projetado em torno de suposições apropriadas para um banco de dados relacional. Você pode escrever um provedor para armazenar informações de associação em um mecanismo de armazenamento não relacional, como tabelas de armazenamento do Azure, mas, em seguida, você precisa contornar o design relacional ao escrever muito código e muita `System.NotImplementedException` exceções para os métodos que não se aplicam a bancos de dados NoSQL.
- Como a funcionalidade de log-in/log-out se baseia na autenticação de formulários, o sistema de associação não é possível usar [OWIN](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md). OWIN inclui componentes de middleware para autenticação, incluindo suporte para logons usando provedores de identidade externas (como Accounts da Microsoft, Facebook, Google, Twitter) e log-ins usando contas organizacionais do Active Directory local ou Azure Active Directory. OWIN também inclui suporte para OAuth 2.0, o JWT e CORS.

### <a name="aspnet-simple-membership"></a>Associação do ASP.NET simples

[Associação simple de ASP.NET](../../../web-pages/overview/security/16-adding-security-and-membership.md) foi desenvolvido como um sistema de associação para páginas da Web do ASP.NET. Ele foi lançado com o WebMatrix e Visual Studio 2010 SP1. O objetivo da associação simples foi tornar mais fácil de adicionar a funcionalidade de associação a um aplicativo de páginas da Web.

Associação simples tornar mais fácil de personalizar as informações de perfil do usuário, mas ainda compartilha os outros problemas com a associação do ASP.NET e ele tem algumas limitações:

- Ele era difícil de manter os dados de sistema de associação em um repositório não relacional.
- Você não pode usá-lo com o OWIN.
- Ele não funciona bem com provedores de associação do ASP.NET existentes, e não é extensível.

### <a name="aspnet-universal-providers"></a>Provedores Universais ASP.NET

[ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx) foram desenvolvidos para que seja possível manter informações de associação no Microsoft Azure SQL Database e elas também funcionam com o SQL Server Compact. Os provedores universais foram criados no Entity Framework Code First, que significa que os provedores universais pode ser usados para manter os dados em qualquer armazenamento com suporte pelo EF. Com os provedores universais, o esquema de banco de dados foi limpo bastante muito bem.

Os provedores universais são criados na infraestrutura de associação do ASP.NET, para que eles ainda têm as mesmas limitações que o provedor SqlMembership. Ou seja, eles foram projetados para bancos de dados relacionais e é difícil personalizar as informações de perfil e o usuário. Esses provedores também usam a autenticação de formulários para a funcionalidade de entrada e saída.

## <a name="aspnet-identity"></a>ASP.NET Identity

Como a associação de história no ASP.NET evoluiu ao longo dos anos, a equipe do ASP.NET, aprendeu muito com comentários de clientes.

A suposição de que os usuários farão logon digitando um nome de usuário e senha que eles foram registrados no seu próprio aplicativo não é mais válida. A web se tornou mais social. Os usuários estão interagindo com o outro em tempo real por meio de canais sociais como Facebook, Twitter e outros sites sociais. Os desenvolvedores querem que os usuários possam entrar com suas identidades sociais para que eles possam ter uma experiência avançada em seus sites. Um sistema de associação modernos deve habilitar o redirecionamento com base em logons para provedores de autenticação, como Facebook, Twitter e outros.

Conforme a evolução do desenvolvimento da web, portanto, fez os padrões de desenvolvimento da web. Unidade de teste do código do aplicativo se tornou uma preocupação principal para desenvolvedores de aplicativos. Em 2008 ASP.NET adicionou uma nova estrutura com base no padrão Model-View-Controller (MVC), em parte, ao ajudar desenvolvedores a criar aplicativos do ASP.NET que pode ser testados de unidade. Os desenvolvedores que desejam unidade testar sua lógica de aplicativo também queria ser capaz de fazer isso com o sistema de associação.

Considerando essas alterações no desenvolvimento de aplicativos web, o ASP.NET Identity foi desenvolvido com os seguintes objetivos:

- **Um sistema de identidade do ASP.NET**

    - Identidade do ASP.NET pode ser usada com todas as estruturas do ASP.NET, como o ASP.NET MVC, Web Forms, páginas da Web, API da Web e SignalR.
    - Identidade do ASP.NET pode ser usada durante a criação de aplicativos web, telefone, repositório ou híbrida.
- **Facilidade de conexão de dados de perfil sobre o usuário**

    - Você tem controle sobre o esquema de usuário e informações de perfil. Por exemplo, você pode habilitar facilmente o sistema armazenar as datas de nascimento inseridas pelos usuários quando eles se registram uma conta em seu aplicativo.

- **Controle de persistência**

    - Por padrão, o sistema de identidade do ASP.NET armazena todas as informações de usuário em um banco de dados. Identidade do ASP.NET usa Entity Framework Code First para implementar todos os seu mecanismo de persistência.
    - Como controlar o esquema de banco de dados, tarefas comuns, como alterar nomes de tabela ou alterar o tipo de dados de chaves primárias é simple de fazer.
    - É fácil de plug-in diferentes mecanismos de armazenamento, como SharePoint, o serviço de tabela de armazenamento do Azure, bancos de dados NoSQL, etc., sem ter que acionar `System.NotImplementedExceptions` exceções.
- **Capacidade de teste de unidade**

    - O ASP.NET Identity torna o aplicativo web mais unidade testável. Você pode escrever testes de unidade para as partes do seu aplicativo que usam a identidade do ASP.NET.
- **Provedor de função**

    - Há um provedor de função que lhe permite restringir o acesso a partes do seu aplicativo pelas funções. Você pode facilmente criar funções, como "Admin" e adicionar usuários às funções.
- **Baseada em declarações**

    - Identidade do ASP.NET oferece suporte à autenticação baseada em declarações, onde a identidade do usuário é representada como um conjunto de declarações. Declarações permitem que os desenvolvedores a ser muito mais expressiva na descrição de uma identidade de usuário não permita que as funções. Enquanto a associação de função é apenas um booleano (ou um membro não-), uma declaração pode incluir informações detalhadas sobre a identidade do usuário e de associação.
- **Provedores de logon social**

    - Você pode facilmente adicionar logons sociais, como Account da Microsoft, Facebook, Twitter, Google e outros usuários ao seu aplicativo e armazenar os dados específicos do usuário em seu aplicativo.

- **Integração de OWIN**

    - Autenticação do ASP.NET agora se baseia o OWIN middleware que pode ser usado em qualquer host baseado em OWIN. Identidade do ASP.NET não tem qualquer dependência em System. Web. Ele é uma estrutura OWIN totalmente compatível e pode ser usado em qualquer aplicativo hospedado de OWIN.
    - Identidade do ASP.NET usa a autenticação do OWIN para log-in/log-out de usuários no site da web. Isso significa que, em vez de usar FormsAuthentication para gerar o cookie, o aplicativo usa CookieAuthentication OWIN para fazer isso.
- **Pacote do NuGet**

    - O ASP.NET Identity é redistribuído como um pacote do NuGet que é instalado nos modelos do ASP.NET MVC, Web Forms e API da Web que acompanham o Visual Studio 2017. Você pode baixar este pacote NuGet da Galeria do NuGet.
    - Liberando a identidade do ASP.NET como um NuGet pacote torna mais fácil para a equipe do ASP.NET iterar nos novos recursos e correções de bugs e entregar para os desenvolvedores de maneira ágil.

## <a name="get-started-with-aspnet-identity"></a>Introdução ao ASP.NET Identity

O ASP.NET Identity é usado nos modelos de projeto do Visual Studio 2017 para ASP.NET MVC, Web Forms, API da Web e SPA. Neste passo a passo, podemos ilustrar como os modelos de projeto usam o ASP.NET Identity para adicionar funcionalidade para se registrar, entrar e sair de um usuário.

Identidade do ASP.NET é implementada usando o procedimento a seguir. A finalidade deste artigo é fornecer uma visão geral de alto nível da identidade do ASP.NET; Você pode segui-lo passo a passo ou apenas ler os detalhes. Para obter instruções mais detalhadas sobre a criação de aplicativos usando a identidade do ASP.NET, inclusive usando a nova API para adicionar usuários, funções e as informações de perfil, consulte a seção de próximas etapas no final deste artigo.

1. Crie um aplicativo ASP.NET MVC com contas individuais. Você pode usar o ASP.NET Identity no ASP.NET MVC, Web Forms, API da Web, o SignalR etc. Neste artigo vamos começar com um aplicativo ASP.NET MVC.  
  
    ![](introduction-to-aspnet-identity/_static/image1.png)
2. O projeto criado contém os seguintes três pacotes para a identidade do ASP.NET.

    - [`Microsoft.AspNet.Identity.EntityFramework`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.EntityFramework/)  
   Este pacote tem a implementação do Entity Framework do ASP.NET Identity, que irá manter os dados de identidade do ASP.NET e o esquema para o SQL Server.
    - [`Microsoft.AspNet.Identity.Core`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.Core/)  
   Esse pacote possui interfaces principais para a identidade do ASP.NET. Esse pacote pode ser usado para gravar uma implementação para a identidade do ASP.NET que persistência de diferentes destinos armazena, como o armazenamento de tabela do Azure, NoSQL, bancos de dados etc.
    - [`Microsoft.AspNet.Identity.OWIN`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.Owin/)  
   Este pacote contém a funcionalidade que é usada para conectar autenticação OWIN com o ASP.NET Identity em aplicativos ASP.NET. Isso é usado quando você adiciona o logon na funcionalidade ao seu aplicativo e a chamada para o middleware de autenticação de Cookie OWIN para gerar um cookie.
3. Criando um usuário.  
   Iniciar o aplicativo e, em seguida, clique no **registrar** link para criar um usuário. A imagem a seguir mostra a página de registro que coleta o nome de usuário e senha.  
  
    ![](introduction-to-aspnet-identity/_static/image2.png)  
  
   Quando o usuário seleciona o **registre** botão, o `Register` ação do controlador de conta cria o usuário chamando a API de identidade do ASP.NET, conforme realçado abaixo:

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample1.cs?highlight=8-9)]
4. Entrar.  
   Se o usuário foi criado com êxito, ela está assinada pelo `SignInAsync` método.  

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample6.cs?highlight=12)]


   O `SignInManager.SignInAsync` método gera uma [ClaimsIdentity](https://msdn.microsoft.com/library/system.security.claims.claimsidentity.aspx). Como o ASP.NET Identity e autenticação de Cookie OWIN são sistema baseado em declarações, o framework requer que o aplicativo para gerar uma ClaimsIdentity para o usuário. ClaimsIdentity tem informações sobre todas as declarações para o usuário, como as funções que o usuário pertence.   
 
5. Faça logoff.  
   Selecione o **fazer logoff** link para chamar a ação de LogOff no controlador da conta. 

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample5.cs?highlight=6)]

   O código realçado acima mostra o OWIN `AuthenticationManager.SignOut` método. Isso é análogo à [FormsAuthentication.SignOut](https://msdn.microsoft.com/library/system.web.security.formsauthentication.signout.aspx) método usado pelas [FormsAuthentication](https://msdn.microsoft.com/library/system.web.security.formsauthenticationmodule.aspx) módulo em formulários da Web.

## <a name="components-of-aspnet-identity"></a>Componentes do ASP.NET Identity

O diagrama a seguir mostra os componentes do sistema ASP.NET Identity (Selecione no [isso](introduction-to-aspnet-identity/_static/image3.png) ou no diagrama para ampliá-la). Os pacotes em verde compõem o sistema ASP.NET Identity. Todos os outros pacotes são dependências que são necessários para usar o sistema de identidade do ASP.NET em aplicativos ASP.NET.

[![](introduction-to-aspnet-identity/_static/image5.png)](introduction-to-aspnet-identity/_static/image4.png)

A seguir está uma breve descrição dos pacotes NuGet não mencionadas anteriormente:

- [Microsoft.Owin.Security.Cookies](http://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/)  
 Middleware que permite que um aplicativo para usar o cookie de autenticação, semelhante ao ASP com base em. Autenticação de formulários do NET.
- [EntityFramework](http://www.nuget.org/packages/EntityFramework/)  
 Entity Framework é uma tecnologia de acesso de dados recomendados da Microsoft para bancos de dados relacionais.

## <a name="migrating-from-membership-to-aspnet-identity"></a>Migrando da associação para o ASP.NET Identity

Esperamos que assim que fornecem orientação sobre como migrar seus aplicativos existentes que usam a associação do ASP.NET ou simples para o novo sistema ASP.NET Identity.

## <a name="next-steps"></a>Próximas etapas

- [Criar um aplicativo ASP.NET MVC 5 com o Facebook e Google OAuth2 e OpenID Sign-on](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md)  
 O tutorial usa a API do ASP.NET Identity para adicionar informações de perfil para o banco de dados de usuário e como autenticar com o Google e Facebook.
- [Criar um aplicativo ASP.NET MVC com autenticação e o banco de dados SQL e implantar no serviço de aplicativo do Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data)  
 Este tutorial mostra como usar a API de identidade para adicionar usuários e funções.
- [https://github.com/rustd/AspnetIdentitySample](https://github.com/rustd/AspnetIdentitySample)  
 Aplicativo de exemplo que mostra como adicionar funções básicas e suporte ao usuário e como fazer o gerenciamento de usuários e funções.

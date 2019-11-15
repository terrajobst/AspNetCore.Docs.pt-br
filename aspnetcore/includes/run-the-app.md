# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Pressione Ctrl + F5 para execução sem o depurador.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local. Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Pressione **Ctrl-F5** para execução sem o depurador.

  O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`. A barra de endereços mostra `localhost:port#` e não algo como `example.com`. Isso ocorre porque `localhost` é o nome do host padrão do computador local. Localhost serve somente solicitações da Web do computador local.

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* No Visual Studio, pressione **ALT-CMD-Enter** para executar sem o depurador. Como alternativa, navegue até a barra de menus e vá para **executar > iniciar sem depuração**.

  O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.

<!-- End of VS tabs -->

---
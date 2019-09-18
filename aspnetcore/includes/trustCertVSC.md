* Confie no certificado de desenvolvimento HTTPS executando o seguinte comando:

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  O comando anterior não funciona no Linux. Veja a documentação da distribuição do Linux para confiar em um certificado.

  O comando anterior exibe a caixa de diálogo a seguir:

  ![Caixa de diálogo de aviso de segurança](~/getting-started/_static/cert.png)

* Selecione **Sim** se você concordar com confiar no certificado de desenvolvimento.

  Para obter mais informações, veja [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).
  

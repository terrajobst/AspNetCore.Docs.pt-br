* Confie no certificado de desenvolvimento HTTPS executando o seguinte comando:

    ```console
    dotnet dev-certs https --trust
    ```

* O comando anterior exibe o resultado a seguir:

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* Insira o nome do usuário administrador e a senha, se solicitado.  O certificado será instalado e se tornará confiável.

    Para obter mais informações, veja [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).
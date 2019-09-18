* <span data-ttu-id="ff20c-101">Confie no certificado de desenvolvimento HTTPS executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff20c-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="ff20c-102">O comando anterior não funciona no Linux.</span><span class="sxs-lookup"><span data-stu-id="ff20c-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="ff20c-103">Veja a documentação da distribuição do Linux para confiar em um certificado.</span><span class="sxs-lookup"><span data-stu-id="ff20c-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="ff20c-104">O comando anterior exibe a caixa de diálogo a seguir:</span><span class="sxs-lookup"><span data-stu-id="ff20c-104">The preceding command displays the following dialog:</span></span>

  ![Caixa de diálogo de aviso de segurança](~/getting-started/_static/cert.png)

* <span data-ttu-id="ff20c-106">Selecione **Sim** se você concordar com confiar no certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ff20c-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="ff20c-107">Para obter mais informações, veja [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="ff20c-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  

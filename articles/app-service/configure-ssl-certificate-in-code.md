---
title: Używanie certyfikatu TLS/SSL w kodzie
description: Dowiedz się, jak używać certyfikatów klienta w kodzie. Uwierzytelnianie za pomocą zasobów zdalnych przy użyciu certyfikatu klienta lub uruchamianie zadań kryptograficznych przy ich użyciu.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 294587fde846a3774f7d74f64029e0bca00e9c08
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829408"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Używanie certyfikatu TLS/SSL w kodzie w usłudze Azure App Service

W kodzie aplikacji możesz uzyskać dostęp do certyfikatów publicznych lub prywatnych, [które dodasz](configure-ssl-certificate.md)do App Service . Kod aplikacji może działać jako klient i uzyskać dostęp do usługi zewnętrznej, która wymaga uwierzytelniania certyfikatu, lub może być konieczne wykonanie zadań kryptograficznych. W tym przewodniku pokazano, jak używać certyfikatów publicznych lub prywatnych w kodzie aplikacji.

To podejście do używania certyfikatów w kodzie korzysta z funkcji TLS w u App Service, która wymaga, aby aplikacja była w warstwie **Podstawowa** lub wyższych. Jeśli twoja aplikacja znajduje  się w **warstwie Bezpłatna** lub Współdzielona, możesz dołączyć plik certyfikatu [do repozytorium aplikacji.](#load-certificate-from-file)

Jeśli pozwolisz App Service certyfikatami TLS/SSL, możesz oddzielnie utrzymywać certyfikaty i kod aplikacji oraz chronić poufne dane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby postępować zgodnie z tym przewodnikiem:

- [Utwórz aplikację usługi App Service](./index.yml)
- [Dodawanie certyfikatu do aplikacji](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Znajdowanie odcisku palca

W menu <a href="https://portal.azure.com" target="_blank">Azure Portal</a>menu po lewej stronie wybierz pozycję **App Services**  >  **\<app-name>** .

W lewym panelu nawigacyjnym aplikacji wybierz pozycję Ustawienia protokołu **TLS/SSL,** a następnie wybierz pozycję Certyfikaty kluczy prywatnych **(pfx)** lub Certyfikaty kluczy **publicznych (cer).**

Znajdź certyfikat, którego chcesz użyć, i skopiuj odcisk palca.

![Kopiowanie odcisku palca certyfikatu](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Udostępnij certyfikat

Aby uzyskać dostęp do certyfikatu w kodzie aplikacji, dodaj jego odcisk palca do ustawienia aplikacji, uruchamiając następujące polecenie w Cloud Shell `WEBSITE_LOAD_CERTIFICATES` : <a target="_blank" href="https://shell.azure.com" ></a>

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Aby wszystkie certyfikaty były dostępne, ustaw wartość `*` na .

## <a name="load-certificate-in-windows-apps"></a>Ładowanie certyfikatu w aplikacjach systemu Windows

Ustawienie aplikacji sprawia, że określone certyfikaty są dostępne dla aplikacji hostowanej w systemie Windows w magazynie certyfikatów systemu `WEBSITE_LOAD_CERTIFICATES` Windows w katalogu Bieżący [użytkownik\Mój](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

W kodzie języka C# uzyskujesz dostęp do certyfikatu za pomocą odcisku palca certyfikatu. Poniższy kod ładuje certyfikat z odciskiem palca `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` .

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

W kodzie Java uzyskujesz dostęp do certyfikatu ze sklepu "Windows-MY" przy użyciu pola Nazwa pospolita podmiotu (zobacz [Certyfikat klucza publicznego](https://en.wikipedia.org/wiki/Public_key_certificate)). Poniższy kod pokazuje, jak załadować certyfikat klucza prywatnego:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

W przypadku języków, które nie obsługują lub nie oferują wystarczającej obsługi magazynu certyfikatów systemu Windows, zobacz [Ładowanie certyfikatu z pliku](#load-certificate-from-file).

## <a name="load-certificate-from-file"></a>Ładowanie certyfikatu z pliku

Jeśli musisz załadować plik certyfikatu, który przekaż ręcznie, lepiej jest przekazać certyfikat przy użyciu protokołu [FTPS](deploy-ftp.md) zamiast usługi [Git,](deploy-local-git.md)na przykład . Należy przechowywać poufne dane, takie jak prywatny certyfikat, poza kontrolą źródła.

> [!NOTE]
> ASP.NET i ASP.NET Core w systemie Windows muszą uzyskać dostęp do magazynu certyfikatów, nawet jeśli certyfikat jest ładowany z pliku. Aby załadować plik certyfikatu w aplikacji .NET dla systemu Windows, załaduj bieżący profil użytkownika za pomocą następującego polecenia w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Takie podejście do używania certyfikatów w kodzie korzysta z funkcji TLS w u App Service, która wymaga, aby aplikacja była w warstwie **Podstawowa** lub wyższych.

Poniższy przykład w języku C# ładuje certyfikat publiczny ze ścieżki względnej w aplikacji:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Aby dowiedzieć się, jak załadować certyfikat TLS/SSL z pliku w języku Node.js, PHP, Python, Java lub Ruby, zapoznaj się z dokumentacją odpowiedniego języka lub platformy internetowej.

## <a name="load-certificate-in-linuxwindows-containers"></a>Ładowanie certyfikatu w kontenerach systemu Linux/Windows

Ustawienia aplikacji sprawiają, że określone certyfikaty są dostępne dla aplikacji kontenerów systemu Windows lub Linux (w tym wbudowanych kontenerów `WEBSITE_LOAD_CERTIFICATES` systemu Linux) jako plików. Pliki znajdują się w następujących katalogach:

| Platforma kontenerów | Certyfikaty publiczne | Certyfikaty prywatne |
| - | - | - |
| Kontener systemu Windows | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Kontener systemu Linux | `/var/ssl/certs` | `/var/ssl/private` |

Nazwy plików certyfikatów to odciski palca certyfikatu. 

> [!NOTE]
> App Service wstrzyknąć ścieżki certyfikatów do kontenerów systemu Windows jako następujące zmienne środowiskowe `WEBSITE_PRIVATE_CERTS_PATH` , `WEBSITE_INTERMEDIATE_CERTS_PATH` , i `WEBSITE_PUBLIC_CERTS_PATH` `WEBSITE_ROOT_CERTS_PATH` . Lepiej jest odwoływać się do ścieżki certyfikatu ze zmiennymi środowiskowymi, zamiast skodytować ścieżkę certyfikatu na wypadek zmiany ścieżek certyfikatów w przyszłości.
>

Ponadto kontenery [Windows Server Core](configure-custom-container.md#supported-parent-images) automatycznie ładują certyfikaty do magazynu certyfikatów w lokalizacji **LocalMachine\My**. Aby załadować certyfikaty, postępuj zgodnie z tym samym wzorcem co w przypadku ładowania [certyfikatu w aplikacjach systemu Windows.](#load-certificate-in-windows-apps) W przypadku kontenerów opartych na systemie Windows Nano użyj ścieżek plików podanych powyżej, aby załadować [certyfikat bezpośrednio z pliku](#load-certificate-from-file).

Poniższy kod w języku C# pokazuje, jak załadować certyfikat publiczny w aplikacji systemu Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Poniższy kod w języku C# pokazuje, jak załadować certyfikat prywatny w aplikacji systemu Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;
...
var bytes = File.ReadAllBytes("/var/ssl/private/<thumbprint>.p12");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Aby dowiedzieć się, jak załadować certyfikat TLS/SSL z pliku w języku Node.js, PHP, Python, Java lub Ruby, zapoznaj się z dokumentacją odpowiedniego języka lub platformy internetowej.

## <a name="more-resources"></a>Więcej zasobów

* [Zabezpieczanie niestandardowej nazwy DNS przy użyciu powiązania protokołu TLS/SSL w Azure App Service](configure-ssl-bindings.md)
* [Wymuszanie protokołu HTTPS](configure-ssl-bindings.md#enforce-https)
* [Wymuszanie protokołu TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Często zadawane pytania: App Service certyfikatów](./faq-configuration-and-management.md)

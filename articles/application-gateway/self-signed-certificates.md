---
title: Generowanie certyfikatu z podpisem własnym przy użyciu niestandardowego głównego urzędu certyfikacji
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak wygenerować certyfikat z podpisem własnym platformy Azure Application Gateway przy użyciu niestandardowego głównego urzędu certyfikacji
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: e60aa9f072a447af97aa7cc66534e6e893fdbcf6
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93396944"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Generowanie certyfikatu z podpisem własnym na platformie Azure Application Gateway przy użyciu niestandardowego głównego urzędu certyfikacji

Jednostka SKU Application Gateway v2 wprowadza użycie zaufanych certyfikatów głównych, aby umożliwić korzystanie z serwerów zaplecza. Spowoduje to usunięcie certyfikatów uwierzytelniania wymaganych w jednostce SKU w wersji 1. *Certyfikat główny* to podstawowy-64 zakodowany X. 509 (. CER) format certyfikatu głównego z serwera certyfikatów zaplecza. Identyfikuje główny urząd certyfikacji, który wystawił certyfikat serwera, i certyfikat serwera jest używany do komunikacji TLS/SSL.

Application Gateway domyślnie ufa certyfikatowi witryny sieci Web, jeśli jest podpisany przez dobrze znany urząd certyfikacji (na przykład GoDaddy lub DigiCert). W takim przypadku nie trzeba jawnie przekazywać certyfikatu głównego. Aby uzyskać więcej informacji, zobacz [Omówienie kończenia protokołu TLS i kompleksowej usługi TLS z Application Gateway](ssl-overview.md). Jeśli jednak masz środowisko deweloperskie/testowe i nie chcesz kupić zweryfikowanego certyfikatu podpisanego urzędu certyfikacji, możesz utworzyć własny niestandardowy urząd certyfikacji i utworzyć z nim certyfikat z podpisem własnym. 

> [!NOTE]
> Certyfikaty z podpisem własnym nie są domyślnie zaufane i mogą być trudne do utrzymania. Ponadto mogą używać przestarzałych algorytmów skrótu i szyfrowania, które mogą nie być silne. Aby zapewnić lepsze zabezpieczenia, należy zakupić certyfikat podpisany przez dobrze znany urząd certyfikacji.

W tym artykule dowiesz się, jak:

- Tworzenie własnego niestandardowego urzędu certyfikacji
- Tworzenie certyfikatu z podpisem własnym podpisanego przez niestandardowy urząd certyfikacji
- Przekazywanie certyfikatu głównego z podpisem własnym do Application Gateway w celu uwierzytelnienia serwera wewnętrznej bazy danych

## <a name="prerequisites"></a>Wymagania wstępne

- **[OpenSSL](https://www.openssl.org/) na komputerze z systemem Windows lub Linux** 

   Mimo że dostępne są inne narzędzia do zarządzania certyfikatami, w tym samouczku jest stosowane OpenSSL. Możesz znaleźć OpenSSL z wieloma dystrybucjami systemu Linux, takimi jak Ubuntu.
- **Serwer sieci Web**

   Na przykład, Apache, IIS lub NGINX do testowania certyfikatów.

- **Jednostka SKU Application Gateway v2**
   
  Jeśli nie masz istniejącej bramy aplikacji, zobacz [Szybki Start: bezpośredni ruch internetowy za pomocą usługi Azure Application Gateway — Azure Portal](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Tworzenie certyfikatu głównego urzędu certyfikacji

Utwórz certyfikat głównego urzędu certyfikacji za pomocą OpenSSL.

### <a name="create-the-root-key"></a>Utwórz klucz główny

1. Zaloguj się na komputerze, na którym zainstalowano OpenSSL i uruchom następujące polecenie. Spowoduje to utworzenie klucza chronionego hasłem.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. W wierszu polecenia wpisz silne hasło. Na przykład, co najmniej dziewięć znaków, przy użyciu wielkich liter, małych liter, cyfr i symboli.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Tworzenie certyfikatu głównego i samodzielnego podpisywania

1. Użyj następujących poleceń, aby wygenerować CSR i certyfikat.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Poprzednie polecenia tworzą certyfikat główny. Ta funkcja zostanie użyta do podpisania certyfikatu serwera.

1. Po wyświetleniu monitu wpisz hasło klucza głównego oraz informacje o organizacji dla niestandardowego urzędu certyfikacji, takie jak kraj/region, stan, organizacja, jednostka organizacyjna i w pełni kwalifikowana nazwa domeny (jest to domena wystawcy).

   ![Utwórz certyfikat główny](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Tworzenie certyfikatu serwera

Następnie utworzysz certyfikat serwera za pomocą OpenSSL.

### <a name="create-the-certificates-key"></a>Utwórz klucz certyfikatu

Użyj następującego polecenia, aby wygenerować klucz dla certyfikatu serwera.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Tworzenie CSR (żądanie podpisania certyfikatu)

CSR jest kluczem publicznym przyznanym do urzędu certyfikacji podczas żądania certyfikatu. Urząd certyfikacji wystawia certyfikat dla tego konkretnego żądania.

> [!NOTE]
> Nazwa POSPOLITa dla certyfikatu serwera musi być różna od domeny wystawcy. Na przykład w tym przypadku CN dla wystawcy jest `www.contoso.com` i CN certyfikatu serwera `www.fabrikam.com` .


1. Użyj następującego polecenia, aby wygenerować CSR:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Po wyświetleniu monitu wpisz hasło klucza głównego oraz informacje o organizacji dla niestandardowego urzędu certyfikacji: kraj/region, stan, organizacja, jednostka organizacyjna i w pełni kwalifikowana nazwa domeny. Jest to domena witryny sieci Web i powinna być różna od wystawcy.

   ![Certyfikat serwera](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Wygeneruj certyfikat z użyciem CSR i klucza i podpisz go przy użyciu klucza głównego urzędu certyfikacji

1. Użyj następującego polecenia, aby utworzyć certyfikat:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Weryfikowanie nowo utworzonego certyfikatu

1. Użyj poniższego polecenia, aby wydrukować dane wyjściowe pliku CRT i zweryfikować jego zawartość:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Weryfikacja certyfikatu](media/self-signed-certificates/verify-cert.png)

1. Sprawdź pliki w katalogu i upewnij się, że masz następujące pliki:

   - contoso. CRT
   - contoso. Key
   - fabrikam. CRT
   - fabrikam. Key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>Skonfiguruj certyfikat w ustawieniach protokołu TLS serwera sieci Web

Na serwerze sieci Web Skonfiguruj protokół TLS przy użyciu plików fabrikam. CRT i fabrikam. Key. Jeśli serwer sieci Web nie może pobrać dwóch plików, można połączyć je z pojedynczym plikiem PEM lub PFX przy użyciu poleceń OpenSSL.

### <a name="iis"></a>IIS

Aby uzyskać instrukcje dotyczące sposobu importowania certyfikatu i przekazywania ich jako certyfikatu serwera w usługach IIS, zobacz [How to: Install import Certificates on a serwer sieci Web w systemie Windows server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Instrukcje dotyczące powiązań TLS znajdują się w temacie [jak skonfigurować protokół SSL w usługach IIS 7](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

Następująca konfiguracja jest przykładem [hosta wirtualnego skonfigurowanego dla protokołu SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) w programie Apache:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

Poniżej przedstawiono konfigurację [bloku serwera Nginx](https://nginx.org/docs/http/configuring_https_servers.html) z konfiguracją protokołu TLS:

![NGINX z protokołem TLS](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Uzyskiwanie dostępu do serwera w celu zweryfikowania konfiguracji

1. Dodaj certyfikat główny do zaufanego magazynu głównego maszyny. Podczas uzyskiwania dostępu do witryny sieci Web upewnij się, że cały łańcuch certyfikatów jest widoczny w przeglądarce.

   ![Zaufane certyfikaty główne](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Przyjęto założenie, że usługa DNS została skonfigurowana w taki sposób, aby wskazywała nazwę serwera sieci Web (w tym przykładzie www.fabrikam.com) na adres IP serwera sieci Web. W przeciwnym razie można edytować [plik hosts](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) w celu rozpoznania nazwy.
1. Przejdź do witryny sieci Web, a następnie kliknij ikonę kłódki w polu adres przeglądarki, aby zweryfikować informacje o lokacji i certyfikacie.

## <a name="verify-the-configuration-with-openssl"></a>Weryfikowanie konfiguracji za pomocą OpenSSL

Lub można użyć OpenSSL do zweryfikowania certyfikatu.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Weryfikacja certyfikatu OpenSSL](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Przekaż certyfikat główny do Application Gateway ustawień HTTP

Aby przekazać certyfikat w Application Gateway, należy wyeksportować certyfikat CRT do formatu. cer Base-64 szyfrowanego. Ponieważ. CRT zawiera już klucz publiczny w zakodowanym formacie Base-64, po prostu zmień nazwę rozszerzenia pliku z. CRT na. cer. 

### <a name="azure-portal"></a>Azure Portal

Aby przekazać zaufany certyfikat główny z portalu, wybierz **Ustawienia protokołu HTTP** i wybierz protokół **https** .

![Dodawanie certyfikatu przy użyciu portalu](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Możesz też użyć interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby przekazać certyfikat główny. Poniższy kod jest przykładem Azure PowerShell.

> [!NOTE]
> Poniższy przykład dodaje zaufany certyfikat główny do bramy aplikacji, tworzy nowe ustawienie HTTP i dodaje nową regułę, przy założeniu, że Pula zaplecza i odbiornik już istnieją.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that TLS handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```

### <a name="verify-the-application-gateway-backend-health"></a>Weryfikowanie kondycji zaplecza bramy aplikacji

1. Kliknij widok **kondycja zaplecza** bramy aplikacji, aby sprawdzić, czy sonda jest w dobrej kondycji.
1. Należy sprawdzić, czy stan jest w **dobrej kondycji** dla sondy https.

![Sonda HTTPS](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat SSL\TLS w Application Gateway, zobacz [Omówienie kończenia protokołu TLS i kompleksowej usługi TLS z Application Gateway](ssl-overview.md).
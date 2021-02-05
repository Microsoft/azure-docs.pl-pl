---
title: Migrowanie z wersji od 1 do v2 — Application Gateway platformy Azure
description: W tym artykule opisano sposób migrowania usługi Azure Application Gateway i zapory aplikacji sieci Web z wersji 1 do wersji 2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 4757a8237aa6226b78e7c1e79ba50710e31d28e3
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594269"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrowanie usługi Azure Application Gateway i zapory aplikacji sieci Web z wersji 1 do wersji 2

[Usługa Azure Application Gateway i Zapora aplikacji sieci Web (WAF) v2](application-gateway-autoscaling-zone-redundant.md) jest teraz dostępna, oferując dodatkowe funkcje, takie jak Skalowanie automatyczne i nadmiarowość stref dostępności. Jednak istniejące bramy w wersji 1 nie są automatycznie uaktualniane do wersji 2. Jeśli chcesz przeprowadzić migrację z wersji 1 do wersji 2, wykonaj kroki opisane w tym artykule.

Migracja obejmuje dwa etapy:

1. Migrowanie konfiguracji
2. Migrowanie ruchu klienta

W tym artykule opisano migrację konfiguracji. Migracja ruchu klienta różni się w zależności od konkretnego środowiska. [Dostępne są](#migrate-client-traffic)jednak pewne ogólne zalecenia dotyczące wysokiego poziomu.

## <a name="migration-overview"></a>Omówienie migracji

Dostępny jest skrypt Azure PowerShell, który wykonuje następujące czynności:

* Tworzy nową bramę Standard_v2 lub WAF_v2 w podsieci sieci wirtualnej, którą określisz.
* Bezproblemowo kopiuje konfigurację skojarzoną z bramą standardu v1 lub WAF do nowo utworzonej Standard_V2 lub WAF_V2 bramy.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Nowa brama V2 ma nowe publiczne i prywatne adresy IP. Nie można przenieść adresów IP skojarzonych z istniejącą bramą w wersji 1 do wersji 2. Istnieje jednak możliwość przydzielenia istniejącego (nieprzydzielony) publicznego lub prywatnego adresu IP do nowej bramy v2.
* Musisz podać przestrzeń adresów IP dla innej podsieci w sieci wirtualnej, w której znajduje się Brama v1. Skrypt nie może utworzyć bramy v2 w żadnej istniejącej podsieci, która ma już bramę w wersji 1. Jeśli jednak istniejąca podsieć ma już bramę w wersji 2, to może nadal mieć miejsce, gdzie jest wystarczająca przestrzeń adresów IP.
* Jeśli masz sieciową grupę zabezpieczeń lub trasy zdefiniowane przez użytkownika skojarzone z podsiecią bramy v2, upewnij się, że spełniają one [wymagania sieciowej grupy zabezpieczeń](../application-gateway/configuration-infrastructure.md#network-security-groups) i [UDR wymagania](../application-gateway/configuration-infrastructure.md#supported-user-defined-routes) dotyczące pomyślnej migracji
* [Zasady punktu końcowego usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoint-policies-overview.md) nie są obecnie obsługiwane w podsieci Application Gateway.
* Aby przeprowadzić migrację konfiguracji protokołu TLS/SSL, należy określić wszystkie certyfikaty protokołu TLS/SSL używane w bramie w wersji 1.
* Jeśli włączono tryb FIPS dla bramy V1, nie zostanie on zmigrowany do nowej bramy v2. Tryb FIPS nie jest obsługiwany w wersji 2.
* V2 nie obsługuje protokołu IPv6, więc nie są migrowane bramy w wersji 1 obsługującej protokół IPv6. Uruchomienie skryptu może nie być możliwe.
* Jeśli Brama V1 ma tylko prywatny adres IP, skrypt tworzy publiczny adres IP i prywatny adres IP dla nowej bramy v2. bramy v2 obecnie nie obsługują tylko prywatnych adresów IP.
* Nagłówki o nazwach zawierających coś innego niż litery, cyfry, łączniki i podkreślenia nie są przesyłane do aplikacji. Dotyczy to tylko nazw nagłówków, a nie wartości nagłówków. Jest to istotna zmiana w wersji 1.

## <a name="download-the-script"></a>Pobierz skrypt

Pobierz skrypt migracji z  [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Używanie skryptu

W zależności od konfiguracji i preferencji lokalnego środowiska programu PowerShell dostępne są dwie opcje:

* Jeśli nie masz zainstalowanych modułów platformy Azure AZ lub nie chcesz odinstalować modułów Azure AZ modules, najlepszą opcją jest użycie `Install-Script` opcji do uruchomienia skryptu.
* Jeśli zachodzi potrzeba zachowania usługi Azure AZ modules, najlepszym trafieniem jest pobranie skryptu i uruchomienie go bezpośrednio.

Aby określić, czy masz zainstalowane moduły Azure AZ, uruchom polecenie `Get-InstalledModule -Name az` . Jeśli nie widzisz żadnych zainstalowanych modułów AZ, możesz użyć `Install-Script` metody.

### <a name="install-using-the-install-script-method"></a>Instalowanie przy użyciu metody Install-Script

Aby można było użyć tej opcji, na komputerze nie trzeba mieć zainstalowanych modułów platformy Azure. Jeśli są zainstalowane, następujące polecenie wyświetla błąd. Możesz odinstalować usługę Azure AZ module lub użyć innej opcji, aby ręcznie pobrać skrypt i uruchomić go.
  
Uruchom skrypt za pomocą następującego polecenia, aby uzyskać najnowszą wersję:

`Install-Script -Name AzureAppGWMigration -Force`

To polecenie instaluje również wymagane AZ modules.  

### <a name="install-using-the-script-directly"></a>Instalowanie przy użyciu skryptu bezpośrednio

Jeśli zainstalowano kilka modułów na platformie Azure i nie można ich odinstalować (lub nie chcesz ich odinstalować), możesz ręcznie pobrać skrypt, korzystając z karty **pobranej ręcznie** w linku pobierania skryptu. Skrypt zostanie pobrany jako plik pierwotny NUPKG. Aby zainstalować skrypt z tego pliku NUPKG, zobacz [ręczne pobieranie pakietu](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Aby uruchomić skrypt:

1. Użyj `Connect-AzAccount` , aby nawiązać połączenie z platformą Azure.

1. Użyj `Import-Module Az` do zaimportowania AZ modules.

1. Uruchom, `Get-Help AzureAppGWMigration.ps1` Aby przeanalizować wymagane parametry:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parametry skryptu:
   * **ResourceID: [ciąg]: wymagane** — jest to identyfikator zasobu platformy Azure dla istniejącej, standardowej bramy v1 lub WAF v1. Aby znaleźć tę wartość ciągu, przejdź do Azure Portal, wybierz pozycję Brama aplikacji lub zasób WAF, a następnie kliknij link **Właściwości** dla bramy. Identyfikator zasobu znajduje się na tej stronie.

     Aby uzyskać identyfikator zasobu, można również uruchomić następujące polecenia Azure PowerShell:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [ciąg]: wymagane** — jest to przestrzeń adresów IP, która została przydzielona (lub która ma zostać przydzielona) dla nowej podsieci, która zawiera nową bramę w wersji 2. Ta wartość musi być określona w notacji CIDR. Na przykład: 10.0.0.0/24. Nie musisz tworzyć tej podsieci z góry. Skrypt tworzy go, jeśli nie istnieje.
   * **appgwName: [ciąg]: opcjonalne**. Jest to ciąg określony jako Nazwa nowej Standard_v2 lub WAF_v2 bramy. Jeśli ten parametr nie zostanie podany, Nazwa istniejącej bramy V1 zostanie użyta z sufiksem *_v2* dołączenia.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: opcjonalne**.  Rozdzielana przecinkami lista obiektów PSApplicationGatewaySslCertificate, które tworzysz do reprezentowania certyfikatów TLS/SSL z bramy V1, musi zostać przekazana do nowej bramy w wersji 2. Dla każdego certyfikatu TLS/SSL skonfigurowanego dla standardowej bramy v1 lub WAF V1 można utworzyć nowy obiekt PSApplicationGatewaySslCertificate za pomocą `New-AzApplicationGatewaySslCertificate` polecenia pokazanego tutaj. Wymagana jest ścieżka do pliku certyfikatu TLS/SSL i hasła.

     Ten parametr jest opcjonalny tylko wtedy, gdy nie masz skonfigurowanych odbiorników HTTPS dla bramy v1 lub WAF. Jeśli masz co najmniej jedną konfigurację odbiornika HTTPS, musisz określić ten parametr.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     W `$mySslCert1, $mySslCert2` poprzednim przykładzie można przekazać (rozdzielone przecinkami) jako wartości tego parametru w skrypcie.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: opcjonalne**. Rozdzielana przecinkami lista obiektów PSApplicationGatewayTrustedRootCertificate, które są tworzone w celu reprezentowania [zaufanych certyfikatów głównych](ssl-overview.md) na potrzeby uwierzytelniania wystąpień zaplecza z poziomu bramy v2.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Aby utworzyć listę obiektów PSApplicationGatewayTrustedRootCertificate, zobacz [New-AzApplicationGatewayTrustedRootCertificate](/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate).
   * **privateIpAddress: [ciąg]: opcjonalne**. Określony prywatny adres IP, który ma zostać skojarzony z nową bramą w wersji 2.  Ta wartość musi należeć do tej samej sieci wirtualnej, która została przydzielona dla nowej bramy v2. Jeśli ta wartość nie zostanie określona, skrypt przydziela prywatny adres IP dla bramy v2.
   * **publicIpResourceId: [ciąg]: opcjonalne**. Identyfikator resourceId istniejącego zasobu publicznego adresu IP (standardowej jednostki SKU) w subskrypcji, który ma zostać przydzielony do nowej bramy w wersji 2. Jeśli ta wartość nie zostanie określona, skrypt przydziela nowy publiczny adres IP w tej samej grupie zasobów. Nazwa to nazwa bramy w wersji 2 z dołączoną opcją *-IP* .
   * **validateMigration: [przełącznik]: opcjonalny**. Użyj tego parametru, jeśli chcesz, aby skrypt wykonał pewne podstawowe operacje porównujące konfigurację po utworzeniu bramy v2 i kopii konfiguracji. Domyślnie żadna weryfikacja nie jest wykonywana.
   * **enableAutoScale: [przełącznik]: opcjonalny**. Użyj tego parametru, jeśli chcesz, aby skrypt umożliwiał automatyczne skalowanie w nowej bramie v2 po jej utworzeniu. Domyślnie Skalowanie automatyczne jest wyłączone. Zawsze można włączyć ją później na nowo utworzonej bramie w wersji 2.

1. Uruchom skrypt przy użyciu odpowiednich parametrów. Ukończenie tego procesu może potrwać od 5 do siedmiu minut.

    **Przykład**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migruj ruch klienta

Najpierw należy sprawdzić, czy skrypt pomyślnie utworzył nową bramę v2 o dokładnej konfiguracji migrowanej z bramy w wersji 1. Można to sprawdzić z poziomu Azure Portal.

Należy również wysłać mały ruch przez bramę w wersji 2 jako test ręczny.
  
Poniżej przedstawiono kilka scenariuszy, w których bieżąca Brama Application Gateway (standardowa) może odbierać ruch klienta i nasze zalecenia dotyczące każdego z nich:

* **Niestandardowa strefa DNS (na przykład contoso.com), która wskazuje adres IP frontonu (przy użyciu rekordu A) skojarzone ze standardową bramą v1 lub WAF V1**.

    Rekord DNS można zaktualizować tak, aby wskazywał adres IP frontonu lub etykietę DNS skojarzoną z bramą aplikacji Standard_v2. W zależności od czasu wygaśnięcia skonfigurowanego w rekordzie DNS może upłynąć trochę czasu, aż cały ruch klienta zostanie zmigrowany do nowej bramy w wersji 2.
* **Niestandardowa strefa DNS (na przykład contoso.com), która wskazuje na etykietę DNS (na przykład: *myappgw.eastus.cloudapp.Azure.com* przy użyciu rekordu CNAME) skojarzonego z bramą V1**.

   Dostępne są dwie opcje:

  * W przypadku używania publicznych adresów IP na bramie aplikacji można przeprowadzić kontrolowanej, szczegółowej migracji przy użyciu profilu Traffic Manager, aby przyrostowo kierować ruchem (metodą routingu ruchu ważonego) do nowej bramy v2.

    Można to zrobić przez dodanie etykiet DNS zarówno bram aplikacji V1, jak i V2 do [profilu Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method), a także CNAME niestandardowy rekord DNS (na przykład `www.contoso.com` ) do domeny Traffic Manager (na przykład contoso.trafficmanager.NET).
  * Można też zaktualizować rekord DNS domeny niestandardowej, aby wskazywał etykietę DNS nowej bramy aplikacji w wersji 2. W zależności od czasu wygaśnięcia skonfigurowanego w rekordzie DNS może upłynąć trochę czasu, aż cały ruch klienta zostanie zmigrowany do nowej bramy w wersji 2.
* **Klienci łączą się z adresem IP frontonu bramy aplikacji**.

   Zaktualizuj klientów tak, aby korzystał z adresów IP skojarzonych z nowo utworzoną bramą aplikacji w wersji 2. Zalecamy, aby adresy IP nie były używane bezpośrednio. Rozważ użycie etykiety nazwy DNS (na przykład yourgateway.eastus.cloudapp.azure.com) skojarzonej z bramą aplikacji, która może być CNAME do własnej niestandardowej strefy DNS (na przykład contoso.com).

## <a name="common-questions"></a>Często zadawane pytania

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Czy istnieją jakieś ograniczenia dotyczące skryptu Azure PowerShell w celu migrowania konfiguracji z wersji od 1 do v2?

Tak. Zobacz [zastrzeżenia/ograniczenia](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Czy ten artykuł i skrypt Azure PowerShell dotyczą także Application Gateway produktu WAF? 

Tak.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Czy skrypt Azure PowerShell przełączać się także przez ruch z bramy V1 do nowo utworzonej bramy v2?

Nie. Skrypt Azure PowerShell tylko migruje konfigurację. Rzeczywista migracja ruchu jest odpowiedzialna i w Twoim formancie.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Czy nowa brama v2 utworzona przez skrypt Azure PowerShell ma odpowiednio rozmiar, aby obsługiwać cały ruch, który jest obecnie obsługiwany przez bramę w wersji 1?

Skrypt Azure PowerShell tworzy nową bramę v2 o odpowiednim rozmiarze do obsługi ruchu w istniejącej bramie w wersji 1. Skalowanie automatyczne jest domyślnie wyłączone, ale podczas uruchamiania skryptu można włączyć skalowanie automatyczne.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Mam skonfigurowaną bramę V1 do wysyłania dzienników do usługi Azure Storage. Czy skrypt replikuje tę konfigurację również w wersji 2?

Nie. Skrypt nie replikuje tej konfiguracji w wersji 2. Konfigurację dziennika należy dodać oddzielnie do zmigrowanej bramy v2.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Czy ten skrypt obsługuje certyfikaty przekazane do magazynu kluczy platformy Azure?

Nie. Obecnie skrypt nie obsługuje certyfikatów w magazynie kluczy. Jest to jednak brane pod uwagę w przyszłej wersji.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Wystąpił problem z używaniem tego skryptu. Jak uzyskać pomoc?
  
Możesz skontaktować się z pomocą techniczną platformy Azure w temacie "Konfiguracja i instalacja/migracja do wersji 2 SKU". Więcej informacji na temat [pomocy technicznej platformy Azure znajdziesz tutaj](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Następne kroki

[Informacje o Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)

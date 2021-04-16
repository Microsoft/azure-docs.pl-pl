---
title: Rozwiązywanie problemów z przekierowywaniem do App Service URL
titleSuffix: Azure Application Gateway
description: Ten artykuł zawiera informacje na temat rozwiązywania problemu z przekierowywaniem, Azure Application Gateway jest używany z Azure App Service
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/15/2021
ms.author: jaysoni
ms.openlocfilehash: 6aad1cf1269a7c3dc082482c39fdc4a079fc3240
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514890"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Rozwiązywanie App Service problemów z programem Application Gateway

Dowiedz się, jak diagnozować i rozwiązywać problemy, które mogą Azure App Service, gdy element jest używany jako obiekt docelowy Azure Application Gateway.

## <a name="overview"></a>Omówienie

Z tego artykułu dowiesz się, jak rozwiązywać następujące problemy:

* Adres URL usługi App Service jest ujmowany w przeglądarce po przekierowaniu.
* Domena pliku cookie ARRAffinity usługi App Service jest ustawiona na nazwę hosta usługi aplikacji, example.azurewebsites.net, a nie na oryginalnego hosta.

Gdy aplikacja back-end wysyła odpowiedź przekierowania, możesz chcieć przekierować klienta do innego adresu URL niż określony przez aplikację tego typu. Można to zrobić, gdy usługa aplikacji jest hostowana za bramą aplikacji i wymaga od klienta przekierowania do ścieżki względnej. Przykładem jest przekierowanie z witryny contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2. 

Gdy usługa App Service wysyła odpowiedź przekierowania, używa tej samej nazwy hosta w nagłówku lokalizacji odpowiedzi, co nazwa w żądaniu odbieranej z bramy aplikacji. Na przykład klient wysyła żądanie bezpośrednio do usługi contoso.azurewebsites.net/path2 zamiast przechodzić przez bramę aplikacji contoso.com/path2. Nie chcesz pomijać bramy aplikacji.

Ten problem może wystąpić z następujących głównych powodów:

- W usłudze App Service skonfigurowano przekierowanie. Przekierowywanie może być tak proste, jak dodanie końcowego ukośnika do żądania.
- Uwierzytelnianie Azure Active Directory, co powoduje przekierowanie.

Ponadto w przypadku korzystania z usług app services za bramą aplikacji nazwa domeny skojarzona z bramą aplikacji (example.com) różni się od nazwy domeny usługi App Service (na przykład example.azurewebsites.net). Wartość domeny dla pliku cookie ARRAffinity ustawionego przez usługę aplikacji zawiera example.azurewebsites.net nazwę domeny, co nie jest pożądane. Oryginalna nazwa hosta, example.com, powinna być wartością nazwy domeny w pliku cookie.

## <a name="sample-configuration"></a>Przykładowa konfiguracja

- Odbiornik HTTP: podstawowy lub wielowiterowy
- Pula adresów App Service
- Ustawienia HTTP: **wybierz pozycję Nazwa hosta z włączonego adresu zaplecza**
- Sonda: **wybierz nazwę hosta z włączonych ustawień PROTOKOŁU** HTTP

## <a name="cause"></a>Przyczyna

App Service jest usługą wielowątkową, dlatego używa nagłówka hosta w żądaniu do przekierowania żądania do poprawnego punktu końcowego. Domyślna nazwa domeny usługi App Services, *.azurewebsites.net (na przykład contoso.azurewebsites.net), różni się od nazwy domeny bramy aplikacji (np. contoso.com). 

Oryginalne żądanie klienta ma nazwę domeny bramy aplikacji, contoso.com jako nazwę hosta. Musisz skonfigurować bramę aplikacji, aby zmienić nazwę hosta w oryginalnym żądaniu na nazwę hosta usługi App Service podczas przekierowania żądania do usługi App Service Back end. Użyj przełącznika **Wybierz nazwę hosta z adresu zaplecza** w konfiguracji ustawień PROTOKOŁU HTTP bramy aplikacji. Użyj przełącznika **Wybierz nazwę hosta z ustawień HTTP zaplecza** w konfiguracji sondy kondycji.



![Brama aplikacji zmienia nazwę hosta](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Gdy usługa App Service przekierowuje, używa zastąpionej nazwy hosta contoso.azurewebsites.net w nagłówku lokalizacji zamiast oryginalnej nazwy hosta contoso.com, chyba że określono inaczej. Sprawdź następujące przykładowe nagłówki żądań i odpowiedzi.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
W poprzednim przykładzie zwróć uwagę, że nagłówek odpowiedzi ma kod stanu 301 dla przekierowania. Nagłówek lokalizacji zawiera nazwę hosta usługi App Service, a nie oryginalną nazwę `www.contoso.com` hosta.

## <a name="solution-rewrite-the-location-header"></a>Rozwiązanie: Ponowne przepisanie nagłówka lokalizacji

Ustaw nazwę hosta w nagłówku lokalizacji na nazwę domeny bramy aplikacji. Aby to zrobić, utwórz regułę ponownego [pisalicą](./rewrite-http-headers.md) warunek, który ocenia, czy nagłówek lokalizacji w odpowiedzi zawiera azurewebsites.net. Musi również wykonać akcję, aby ponownie napisać nagłówek lokalizacji tak, aby miał nazwę hosta bramy aplikacji. Aby uzyskać więcej informacji, zobacz instrukcje [dotyczące ponownego pisano nagłówek lokalizacji](./rewrite-http-headers.md#modify-a-redirection-url).

> [!NOTE]
> Obsługa ponownego pisali nagłówka HTTP jest dostępna tylko dla Standard_v2 i [WAF_v2 SKU](./application-gateway-autoscaling-zone-redundant.md) Application Gateway. Zalecamy [migrowanie do wersji 2 dla](./migrate-v1-v2.md) [](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) opcji Ponowne zapisuje nagłówek i innych zaawansowanych funkcji dostępnych w wersji 2 dla wersji SKU.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Rozwiązanie alternatywne: używanie niestandardowej nazwy domeny

Użycie App Service funkcji Custom Domain to inne rozwiązanie, które zawsze przekierowuje ruch do Application Gateway domeny (w `www.contoso.com` naszym przykładzie). Ta konfiguracja służy również jako rozwiązanie problemu z plikiem cookie koligacji ARR. Domyślnie domena plików cookie ARRAffinity jest ustawiona na domyślną nazwę hosta App Service (example.azurewebsites.net) zamiast nazwę domeny Application Gateway użytkownika. W związku z tym przeglądarka w takich przypadkach odrzuci plik cookie ze względu na różnicę w nazwach domen żądania i pliku cookie.

Możesz postępować zgodnie z podaną metodą w przypadku problemów z niezgodnością domeny przekierowania i pliku cookie aRRAffinity. Ta metoda wymaga dostępu do strefy DNS domeny niestandardowej.

**Krok 1.** Ustaw Custom Domain w App Service i sprawdź własność domeny, dodając rekord [CNAME](../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)& TXT DNS .
Rekordy będą wyglądać podobnie do:
-  `www.contoso.com` IN CNAME `contoso.azurewebsite.net`
-  `asuid.www.contoso.com` W TXT " `<verification id string>` "


**Krok 2.** Rekord CNAME w poprzednim kroku był potrzebny tylko do weryfikacji domeny. Ostatecznie potrzebujemy ruchu do przekierowywu przez Application Gateway. W związku z tym `www.contoso.com` można teraz zmodyfikować rekord CNAME Application Gateway nazwę FQDN użytkownika. Aby ustawić nazwę FQDN dla Application Gateway, przejdź do jego zasobu publicznego adresu IP i przypisz do niego "etykietę nazwy DNS". Zaktualizowany rekord CNAME powinien teraz wyglądać jako 
-  `www.contoso.com` IN CNAME `contoso.eastus.cloudapp.azure.com`


**Krok 3.** Wyłącz opcję "Wybierz nazwę hosta z adresu zaplecza" dla skojarzonego ustawienia HTTP.

W programie PowerShell nie używaj `-PickHostNameFromBackendAddress` przełącznika w `Set-AzApplicationGatewayBackendHttpSettings` poleceniu .


**Krok 4.** Aby sondy określały zaplecza jako w dobrej kondycji i ruch operacyjny, ustaw niestandardową sondę kondycji z polem Host jako domeną niestandardową lub domyślną App Service.

W programie PowerShell nie używaj przełącznika w poleceniu i używaj domeny niestandardowej lub domyślnej serwera `-PickHostNameFromBackendHttpSettings` `Set-AzApplicationGatewayProbeConfig` App Service przełączniku -HostName sondy.

Aby zaimplementować poprzednie kroki przy użyciu programu PowerShell dla istniejącej konfiguracji, użyj poniższego przykładowego skryptu programu PowerShell. Zwróć uwagę, że **przełączniki -PickHostname** nie zostały użyte w konfiguracji ustawień sondy i protokołu HTTP.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Następne kroki

Jeśli powyższe kroki nie rozwiązały problemu, otwórz bilet [pomocy technicznej](https://azure.microsoft.com/support/options/).

---
title: Rozwiązywanie problemów z przekierowaniem do adresu URL App Service
titleSuffix: Azure Application Gateway
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z przekierowaniami, gdy usługa Azure Application Gateway jest używana z usługą Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 1cc7df755198461643703cac988c8c31f2ac25db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182890"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Rozwiązywanie problemów z App Service w programie Application Gateway

Dowiedz się, jak diagnozować i rozwiązywać problemy, które mogą wystąpić, gdy Azure App Service jest używany jako cel zaplecza przy użyciu usługi Azure Application Gateway.

## <a name="overview"></a>Omówienie

W tym artykule dowiesz się, jak rozwiązywać następujące problemy:

* Adres URL usługi App Service jest ujawniany w przeglądarce w przypadku przekierowania.
* Domena plików cookie ARRAffinity usługi App Service jest ustawiona na nazwę hosta usługi App Service, example.azurewebsites.net zamiast oryginalnego hosta.

Gdy aplikacja zaplecza wysyła odpowiedź przekierowania, możesz chcieć przekierować klienta na inny adres URL niż określony przez aplikację zaplecza. Można to zrobić, gdy usługa App Service jest hostowana za bramą aplikacji i wymaga, aby klient przetworzył przekierowanie do swojej ścieżki względnej. Przykładem jest przekierowanie z contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2. 

Gdy usługa App Service wyśle odpowiedź przekierowania, używa takiej samej nazwy hosta w nagłówku lokalizacji swojej odpowiedzi, jak ta w żądaniu odbieranym od bramy aplikacji. Na przykład klient wysyła żądanie bezpośrednio do contoso.azurewebsites.net/path2 zamiast przechodzenia przez contoso.com/path2 Application Gateway. Nie chcesz pomijać bramy aplikacji.

Ten problem może wystąpić z następujących powodów:

- Skonfigurowano przekierowanie w usłudze App Service. Przekierowanie może być proste, dodając końcowy ukośnik do żądania.
- Masz Azure Active Directory uwierzytelnianie, które powoduje przeprowadzenie przekierowania.

Ponadto w przypadku korzystania z usług App Services za bramą aplikacji nazwa domeny skojarzona z bramą Application Gateway (example.com) różni się od nazwy domeny usługi App Service (Powiedz, example.azurewebsites.net). Wartość domeny dla pliku cookie ARRAffinity ustawianego przez usługę App Service przenosi nazwę domeny example.azurewebsites.net, która nie jest pożądana. Oryginalna nazwa hosta, example.com, powinna być wartością nazwy domeny w pliku cookie.

## <a name="sample-configuration"></a>Przykładowa konfiguracja

- Odbiornik HTTP: podstawowa lub wielolokacjowa
- Pula adresów zaplecza: App Service
- Ustawienia protokołu HTTP: **Wybierz nazwę hosta z włączonego adresu zaplecza**
- Sonda: **Wybieranie nazwy hosta z włączonych ustawień protokołu HTTP**

## <a name="cause"></a>Przyczyna

App Service to usługa wielodostępna, dlatego używa nagłówka hosta w żądaniu do kierowania żądania do właściwego punktu końcowego. Domyślna nazwa domeny App Services, *. azurewebsites.net (Powiedz, contoso.azurewebsites.net) różni się od nazwy domeny bramy aplikacji (Powiedz, contoso.com). 

Oryginalne żądanie od klienta ma nazwę domeny bramy aplikacji, contoso.com, jako nazwę hosta. Należy skonfigurować bramę aplikacji, aby zmienić nazwę hosta w oryginalnym żądaniu na nazwę hosta usługi App Service, gdy kieruje żądanie do zaplecza usługi App Service. Użyj przełącznika **Wybierz nazwę hosta z adresu zaplecza** w konfiguracji ustawień http bramy aplikacji. Użyj przełącznika **Wybierz nazwę hosta z ustawień protokołu HTTP zaplecza** w konfiguracji sondy kondycji.



![Nazwa hosta zmian bramy aplikacji](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Gdy usługa App Service przetworzy przekierowanie, używa zastąpionej nazwy hosta contoso.azurewebsites.net w nagłówku lokalizacji zamiast oryginalnej nazwy hosta contoso.com, chyba że została skonfigurowana inaczej. Sprawdź następujące przykładowe nagłówki żądania i odpowiedzi.
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
W poprzednim przykładzie Zwróć uwagę, że nagłówek odpowiedzi ma kod stanu 301 dla przekierowania. Nagłówek lokalizacji zawiera nazwę hosta usługi App Service zamiast oryginalnej nazwy hosta `www.contoso.com` .

## <a name="solution-rewrite-the-location-header"></a>Rozwiązanie: Zapisz ponownie nagłówek lokalizacji

W nagłówku lokalizacji Ustaw nazwę hosta na nazwę domeny bramy aplikacji. W tym celu należy utworzyć [regułę ponownego zapisywania](./rewrite-http-headers.md) z warunkiem, który oblicza, czy nagłówek lokalizacji w odpowiedzi zawiera azurewebsites.NET. Należy również wykonać akcję, aby ponownie zapisać nagłówek lokalizacji w celu posiadania nazwy hosta bramy aplikacji. Aby uzyskać więcej informacji, zobacz instrukcje dotyczące [ponownego zapisywania nagłówka lokalizacji](./rewrite-http-headers.md#modify-a-redirection-url).

> [!NOTE]
> Obsługa ponownego zapisywania nagłówka HTTP jest dostępna tylko dla [Standard_v2 i WAF_V2 SKU](./application-gateway-autoscaling-zone-redundant.md) Application Gateway. W przypadku korzystania z jednostki SKU w wersji 1 zalecamy [Migrowanie z wersji od 1 do v2](./migrate-v1-v2.md). Chcesz użyć funkcji ponownego zapisywania i innych [zaawansowanych możliwości](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) dostępnych w wersji 2 jednostki SKU.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternatywne rozwiązanie: Użyj niestandardowej nazwy domeny

W przypadku korzystania z jednostki SKU w wersji 1 nie można ponownie zapisać nagłówka lokalizacji. Ta funkcja jest dostępna tylko dla jednostki SKU w wersji 2. Aby rozwiązać problem z przekierowaniem, należy przekazać tę samą nazwę hosta, którą Brama aplikacji otrzymuje również do usługi App Service, a nie przesłonięcie hosta.

Usługa App Service wykonuje teraz przekierowanie (jeśli istnieje) w tym samym oryginalnym nagłówku hosta, który wskazuje na bramę aplikacji, a nie jako własny.

Musisz być członkiem domeny niestandardowej i wykonać następujący proces:

- Zarejestruj domenę na liście domen niestandardowych usługi App Service. W domenie niestandardowej musi znajdować się rekord CNAME, który wskazuje na nazwę FQDN usługi App Service. Aby uzyskać więcej informacji, zobacz [Mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service](../app-service/app-service-web-tutorial-custom-domain.md).

    ![Niestandardowa lista domen usługi App Service](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Usługa App Service jest gotowa do zaakceptowania nazwy hosta `www.contoso.com` . Zmień wpis CNAME w systemie DNS, aby wskazywał z powrotem na nazwę FQDN bramy aplikacji, na przykład `appgw.eastus.cloudapp.azure.com` .

- Upewnij się, że Twoja domena `www.contoso.com` jest rozpoznawana jako nazwa FQDN bramy aplikacji podczas wykonywania zapytania DNS.

- Ustaw niestandardową sondę, aby wyłączyć **Wybieranie nazwy hosta z ustawień protokołu HTTP zaplecza**. W Azure Portal wyczyść pole wyboru w obszarze Ustawienia sondy. W programie PowerShell nie należy używać przełącznika **-PickHostNameFromBackendHttpSettings** w poleceniu **Set-AzApplicationGatewayProbeConfig** . W polu Nazwa hosta sondy wprowadź nazwę FQDN usługi App Service, example.azurewebsites.net. Żądania sondowania wysyłane z bramy aplikacji przenoszą tę nazwę FQDN w nagłówku hosta.

  > [!NOTE]
  > W następnym kroku upewnij się, że niestandardowa Sonda nie jest skojarzona z ustawieniami protokołu HTTP zaplecza. Ustawienia protokołu HTTP nadal mają włączoną pozycję **Wybierz nazwę hosta z przełącznika adresu zaplecza** .

- Ustaw ustawienia HTTP bramy aplikacji, aby wyłączyć **Wybieranie nazwy hosta z adresu zaplecza**. W Azure Portal wyczyść pole wyboru. W programie PowerShell nie należy używać przełącznika **-PickHostNameFromBackendAddress** w poleceniu **Set-AzApplicationGatewayBackendHttpSettings** .

- Skojarz niestandardową sondę z powrotem z ustawieniami protokołu HTTP zaplecza i sprawdź, czy zaplecze jest w dobrej kondycji.

- Brama aplikacji powinna teraz przesłać dalej tę samą nazwę hosta, `www.contoso.com` do usługi App Service. Przekierowanie odbywa się na tej samej nazwie hosta. Sprawdź następujące przykładowe nagłówki żądania i odpowiedzi.

Aby zaimplementować poprzednie kroki przy użyciu programu PowerShell dla istniejącej instalacji, użyj poniższego skryptu programu PowerShell. Zwróć uwagę, jak nie używamy przełączników **-PickHostname** w konfiguracji ustawień sondowania i http.

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

Jeśli powyższe kroki nie rozwiążą problemu, Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).
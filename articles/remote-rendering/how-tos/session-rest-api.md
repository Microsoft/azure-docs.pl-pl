---
title: Interfejs API REST zarządzania sesją
description: Opisuje sposób zarządzania sesjami
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: d957c5d6521010c7393e2297be16cd7bef41c35f
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724072"
---
# <a name="use-the-session-management-rest-api"></a>Korzystanie z interfejsu API REST zarządzania sesją

Aby korzystać z funkcji renderowania zdalnego platformy Azure, należy utworzyć *sesję*. Każda sesja odpowiada maszynie wirtualnej (VM), która jest przypisana na platformie Azure i oczekuje na połączenie urządzenia klienckiego. Gdy urządzenie zostanie połączone, maszyna wirtualna renderuje żądane dane i zachowuje wynik jako strumień wideo. Podczas tworzenia sesji wybierasz rodzaj serwera, na którym chcesz pracować, co określa Cennik. Gdy sesja nie jest już wymagana, powinna zostać zatrzymana. Jeśli nie zostanie zatrzymana ręcznie, zostanie ona ZAMKNIĘTA automatycznie po wygaśnięciu *czasu dzierżawy* sesji.

Udostępniamy skrypt programu PowerShell w [repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) w folderze *scripts* o nazwie *RenderingSession.ps1*, który demonstruje korzystanie z naszej usługi. Skrypt i jego konfigurację są opisane tutaj: [przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)

> [!TIP]
> Polecenia programu PowerShell wymienione na tej stronie są przeznaczone do uzupełniania siebie nawzajem. Jeśli w tym samym wierszu polecenia programu PowerShell zostanie uruchomione wszystkie skrypty, zostaną one skompilowane na początku.

## <a name="regions"></a>Regiony

Zapoznaj się z [listą regionów dostępnych](../reference/regions.md) dla podstawowych adresów URL, do których mają być wysyłane żądania.

Dla przykładowych skryptów poniżej wybieramy region *westus2*.

### <a name="example-script-choose-an-endpoint"></a>Przykładowy skrypt: Wybieranie punktu końcowego

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Konta

Jeśli nie masz konta renderowania zdalnego, [Utwórz je](create-an-account.md). Każdy zasób jest identyfikowany przez *accountId*, który jest używany przez interfejsy API sesji.

### <a name="example-script-set-accountid-accountkey-and-account-domain"></a>Przykładowy skrypt: Ustaw accountId, accountKey i domenę konta

Domena konta to lokalizacja konta renderowania zdalnego. W tym przykładzie Lokalizacja konta to *wschód*.

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
$accountDomain = "eastus.mixedreality.azure.com"
```

## <a name="common-request-headers"></a>Typowe nagłówki żądań

* Nagłówek *autoryzacji* musi mieć wartość " `Bearer TOKEN` ", gdzie " `TOKEN` " jest tokenem uwierzytelniania [zwracanym przez usługę bezpiecznego tokenu](tokens.md).

### <a name="example-script-request-a-token"></a>Przykładowy skrypt: żądanie tokenu

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.$accountDomain/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Typowe nagłówki odpowiedzi

* Nagłówek *MS-CV* może być używany przez zespół produktu do śledzenia wywołania w ramach usługi.

## <a name="create-a-session"></a>Tworzenie sesji

To polecenie tworzy sesję. Zwraca identyfikator nowej sesji. Potrzebujesz identyfikatora sesji dla wszystkich innych poleceń.

| URI | Metoda |
|-----------|:-----------|
| /V1/accounts/*accountId*/Sessions/Create | POST |

**Treść żądania:**

* maxLeaseTime (TimeSpan): wartość limitu czasu, gdy sesja zostanie automatycznie zlikwidowana
* modele (Array): adresy URL kontenerów zasobów do wstępnego załadowania
* rozmiar (ciąg): rozmiar serwera do skonfigurowania ([**"Standardowa"**](../reference/vm-sizes.md) lub [**"Premium"**](../reference/vm-sizes.md)). Zobacz ograniczenia dotyczące określonego [rozmiaru](../reference/limits.md#overall-number-of-polygons).

**Reagowani**

| Kod stanu | Ładunek JSON | Komentarze |
|-----------|:-----------|:-----------|
| 202 | -sessionId: GUID | Success |

### <a name="example-script-create-a-session"></a>Przykładowy skrypt: Tworzenie sesji

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

Przykładowe dane wyjściowe:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>Przykładowy skrypt: identyfikator sesji magazynu

Odpowiedź z powyższego żądania zawiera **Identyfikator sesji**, który jest wymagany dla wszystkich żądań monitowania.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="modify-and-query-session-properties"></a>Modyfikuj i badaj właściwości sesji

Istnieje kilka poleceń służących do wykonywania zapytań lub modyfikacji parametrów istniejących sesji.

> [!CAUTION]
> Podobnie jak w przypadku wszystkich wywołań REST, wysłanie tych poleceń zbyt często spowoduje ograniczenie i zwrócenie błędu. Kod stanu w tym przypadku jest 429 ("zbyt wiele żądań"). Zgodnie z zasadą dla elementu kciuka należy mieć opóźnienie **5-10 sekund między kolejnymi wywołaniami**.

### <a name="update-session-parameters"></a>Aktualizuj parametry sesji

To polecenie aktualizuje parametry sesji. Obecnie można zwiększyć tylko czas dzierżawy sesji.

> [!IMPORTANT]
> Czas dzierżawy jest zawsze wyrażony jako łączny czas od momentu rozpoczęcia sesji. Oznacza to, że jeśli utworzono sesję z upływem czasu dzierżawy o wartości jednej godziny i chcesz zwiększyć jej czas dzierżawy przez inną godzinę, musisz zaktualizować jej maxLeaseTime na dwie godziny.

| URI | Metoda |
|-----------|:-----------|
| /V1/accounts/*accountID*/Sessions/*SessionID* | WYSŁANA |

**Treść żądania:**

* maxLeaseTime (TimeSpan): wartość limitu czasu, gdy sesja zostanie automatycznie zlikwidowana

**Reagowani**

| Kod stanu | Ładunek JSON | Komentarze |
|-----------|:-----------|:-----------|
| 200 | | Success |

#### <a name="example-script-update-a-session"></a>Przykładowy skrypt: aktualizowanie sesji

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

Przykładowe dane wyjściowe:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

### <a name="get-active-sessions"></a>Pobierz aktywne sesje

To polecenie zwraca listę aktywnych sesji.

| URI | Metoda |
|-----------|:-----------|
| /V1/accounts/*accountId*/Sessions | GET |

**Reagowani**

| Kod stanu | Ładunek JSON | Komentarze |
|-----------|:-----------|:-----------|
| 200 | -Sessions: Tablica właściwości sesji | Opis właściwości sesji znajduje się w sekcji "Uzyskiwanie właściwości sesji". |

#### <a name="example-script-query-active-sessions"></a>Przykładowy skrypt: wykonywanie zapytań dotyczących aktywnych sesji

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Przykładowe dane wyjściowe:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

### <a name="get-sessions-properties"></a>Pobierz właściwości sesji

To polecenie zwraca informacje o sesji, takie jak nazwa hosta maszyny wirtualnej.

| URI | Metoda |
|-----------|:-----------|
| /V1/accounts/*accountId*/Sessions/*SessionID*/Properties | GET |

**Reagowani**

| Kod stanu | Ładunek JSON | Komentarze |
|-----------|:-----------|:-----------|
| 200 | -Message: ciąg<br/>-sessionElapsedTime: TimeSpan<br/>-sessionHostname: ciąg<br/>-sessionId: ciąg<br/>-sessionMaxLeaseTime: TimeSpan<br/>-sessionSize: enum<br/>-sessionStatus: enum | Wyliczenie sessionStatus {Start, gotowe, zatrzymywanie, zatrzymane, wygasłe, błąd}<br/>Jeśli stan ma wartość "Error" lub "wygasła", komunikat będzie zawierać więcej informacji |

#### <a name="example-script-get-session-properties"></a>Przykładowy skrypt: pobieranie właściwości sesji

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Przykładowe dane wyjściowe:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>Zatrzymaj sesję

To polecenie powoduje zatrzymanie sesji. Przypisana maszyna wirtualna zostanie wkrótce ododzyskiwana.

| URI | Metoda |
|-----------|:-----------|
| /V1/accounts/*accountId*/Sessions/*SessionID* | DELETE |

**Reagowani**

| Kod stanu | Ładunek JSON | Komentarze |
|-----------|:-----------|:-----------|
| 204 | | Success |

### <a name="example-script-stop-a-session"></a>Przykładowy skrypt: zatrzymywanie sesji

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

Przykładowe dane wyjściowe:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>Następne kroki

* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)

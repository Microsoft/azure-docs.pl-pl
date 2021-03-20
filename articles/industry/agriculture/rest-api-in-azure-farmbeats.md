---
title: Interfejsy API usługi Azure FarmBeats
description: Dowiedz się więcej na temat interfejsów API usługi Azure FarmBeats, które zapewniają firmom rolniczym interfejs RESTful ustandaryzowany z odpowiedziami opartymi na notacji JSON.
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 292374bcce90da042dc6bd672feaad3750759aa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674517"
---
# <a name="azure-farmbeats-apis"></a>Interfejsy API usługi Azure FarmBeats

W tym artykule opisano interfejsy API usługi Azure FarmBeats. Interfejsy API usługi Azure FarmBeats zapewniają firmy rolnicze ze standardowym interfejsem RESTful z odpowiedziami opartymi na notacji JSON, które ułatwiają korzystanie z możliwości usługi Azure FarmBeats, takich jak:

- Interfejsy API umożliwiające uzyskiwanie danych z czujnika, aparatu, drona, pogody, satelity i nadzoru z terenu.
- Normalizacja i contextualization danych między typowymi dostawcami danych.
- Schematized możliwości dostępu i zapytania dotyczące wszystkich pozyskanych danych.
- Automatyczne generowanie metadanych, które mogą być badane w oparciu o funkcje agronomicznych.
- Automatycznie generowane zagregowane sekwencje czasu do szybkiego kompilowania modeli.
- Zintegrowany aparat Azure Data Factory, aby łatwo tworzyć niestandardowe potoki przetwarzania danych.

## <a name="application-development"></a>Opracowywanie aplikacji

Interfejsy API FarmBeats zawierają dokumentację techniczną struktury Swagger. Aby uzyskać informacje na temat wszystkich interfejsów API i odpowiadających im żądań lub odpowiedzi, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

Poniższa tabela zawiera podsumowanie wszystkich obiektów i zasobów w programie FarmBeats Datahub:

| Obiekty i zasoby | Opis
--- | ---|
Hodowli | Farma odpowiada fizycznej lokalizacji interesu w systemie FarmBeats. Każda Farma ma nazwę farmy i unikatowy identyfikator farmy. |
Urządzenie  | Urządzenie odpowiada urządzeniu fizycznemu znajdującemu się w farmie. Każde urządzenie ma unikatowy identyfikator urządzenia. Urządzenie jest zazwyczaj obsługiwane w farmie z IDENTYFIKATORem farmy.
DeviceModel  | DeviceModel odnosi się do metadanych urządzenia, takich jak producent i typ urządzenia, który jest bramą lub węzłem.
Czujnik  | Czujnik odnosi się do czujnika fizycznego, który rejestruje wartości. Czujnik jest zwykle podłączony do urządzenia z IDENTYFIKATORem urządzenia.
SensorModel  | SensorModel odnosi się do metadanych czujnika, takich jak producent, typ czujnika, który jest analogowy lub cyfrowy i pomiar czujnika, taki jak temperatura otoczenia i ciśnienie.
Telemetria  | Funkcja telemetrii umożliwia odczytywanie komunikatów telemetrycznych dla określonego czujnika i zakresu czasu.
Zadanie  | Zadanie odnosi się do dowolnego przepływu pracy działań, które są wykonywane w systemie FarmBeats w celu uzyskania żądanych danych wyjściowych. Każde zadanie jest skojarzone z IDENTYFIKATORem zadania i typem zadania.
Typu zadania  | Typu zadania odnosi się do różnych typów zadań obsługiwanych przez system. Typy zadań zdefiniowane przez system i zdefiniowane przez użytkownika są uwzględniane.
ExtendedType  | Rozszerzony ElementType odpowiada liście typów zdefiniowanych przez użytkownika i systemu w systemie. Rozszerzony typ ułatwia skonfigurowanie nowego typu czujnika, sceny lub pliku sceny w systemie FarmBeats.
Partner  | Partner odpowiada partnerowi integracji czujników i obrazów dla FarmBeats.
Miejscu  | Scena odnosi się do wszystkich wygenerowanych danych wyjściowych w kontekście farmy. Każda scena ma identyfikator sceny, źródło sceny, typ sceny i skojarzony z nią identyfikator farmy. Każdemu IDENTYFIKATORowi sceny może być skojarzonych wiele plików sceny.
SceneFile |SceneFile odnosi się do wszystkich plików, które są generowane dla jednej sceny. Z pojedynczym IDENTYFIKATORem sceny może być skojarzonych wiele identyfikatorów SceneFile.
Reguła  |Reguła odnosi się do warunku dla danych związanych z farmą w celu wyzwolenia alertu. Każda reguła znajduje się w kontekście danych farmy.
Alerty  | Alert odpowiada powiadomienia, które jest generowane po spełnieniu warunku reguły. Każdy alert znajduje się w kontekście reguły.
Definicji  | Definicji definiuje dozwolone i niedozwolone akcje dla roli.
RoleAssignment  |RoleAssignment odpowiada przypisaniu roli do użytkownika lub nazwy głównej usługi.

### <a name="data-format"></a>Format danych

JSON to typowy format danych niezależny od języka, który zapewnia prostą reprezentację dowolnych struktur danych. Aby uzyskać więcej informacji, zobacz [witrynę sieci Web JSON](https://www.json.org/).

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

Żądania HTTP do interfejsu API REST są chronione za pomocą Azure Active Directory (Azure AD).
Aby wysłać żądanie do interfejsów API REST, kod klienta wymaga uwierzytelnienia z prawidłowymi poświadczeniami, zanim będzie można wywołać interfejs API. Uwierzytelnianie jest koordynowane między różnymi aktorami przez usługę Azure AD. Zapewnia klientowi token dostępu jako potwierdzenie uwierzytelniania. Token jest następnie wysyłany w nagłówku autoryzacji HTTP żądań interfejsu API REST. Aby dowiedzieć się więcej o uwierzytelnianiu usługi Azure AD, zobacz [Azure Active Directory](https://portal.azure.com) dla deweloperów.

Token dostępu musi być wysyłany w kolejnych żądaniach interfejsu API, w sekcji nagłówka, jako:

```http
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>Nagłówki żądań HTTP

Oto najczęstsze nagłówki żądań, które należy określić podczas wywołania interfejsu API do usługi Azure FarmBeats Datahub.


**Nagłówek** | **Opis i przykład**
--- | ---
Content-Type  | Format żądania (Content-Type: Application/ <format> ). W przypadku interfejsów API usługi Azure FarmBeats Datahub format to JSON. Content-Type: Application/JSON
Autoryzacja  | Określa token dostępu wymagany do wywołania interfejsu API. Autoryzacja: <okaziciela Access-Token>
Zaakceptuj | Format odpowiedzi. W przypadku interfejsów API usługi Azure FarmBeats Datahub format to JSON. Akceptuj: Application/JSON

### <a name="api-requests"></a>Żądania interfejsu API

Aby wykonać żądanie interfejsu API REST, należy połączyć metodę HTTP (GET, POST, PUT lub DELETE), adres URL usługi API Service, identyfikator URI do zasobu do zapytania, przesłać dane do, zaktualizować lub usunąć, a następnie dodać co najmniej jeden nagłówek żądania HTTP.

Adres URL usługi interfejsu API to adres URL Datahub, na przykład https:// \<yourdatahub-website-name> . azurewebsites.NET.

Opcjonalnie można uwzględnić parametry zapytania dla wywołań GET do filtrowania, ograniczyć rozmiar i sortować dane w odpowiedziach.

Następujące przykładowe żądanie służy do uzyskiwania listy urządzeń:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

Większość wywołań GET, POST i PUT wymaga treści żądania JSON.

Poniższe przykładowe żądanie tworzy urządzenie. To żądanie zawiera dane wejściowe JSON z treścią żądania.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Parametry zapytania

W przypadku wywołań REST GET można filtrować, ograniczać rozmiar i sortować dane w odpowiedzi interfejsu API przez uwzględnienie co najmniej jednego parametru zapytania w identyfikatorze URI żądania. Aby poznać parametry zapytania, zobacz dokumentację interfejsu API i poszczególne wywołania GET.
Na przykład podczas wysyłania zapytania do listy urządzeń (GET Call on/Device) można określić następujące parametry zapytania:

![Lista urządzeń](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Obsługa błędów

Interfejsy API usługi Azure FarmBeats Datahub zwracają standardowe błędy HTTP. Najbardziej typowe kody błędów są następujące:

 |Kod błędu             | Opis |
 |---                    | --- |
 |200                    | Powodzenie |
 |201                    | Powodzenie tworzenia (post) |
 |400                    | Nieprawidłowe żądanie. Wystąpił błąd w żądaniu. |
 |401                    | Próby. Obiekt wywołujący interfejsu API nie ma autoryzacji do uzyskania dostępu do zasobu. |
 |404                    | Nie znaleziono zasobu |
 |5XX                    | Wewnętrzny błąd serwera. Kody błędów zaczynające się od 5XX oznacza, że na serwerze Wystąpił błąd. Aby uzyskać więcej informacji, zapoznaj się z dziennikami serwera i poniższą sekcją. |


Oprócz standardowych błędów HTTP, interfejsy API usługi Azure FarmBeats Datahub zwracają również błędy wewnętrzne w następującym formacie:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

W tym przykładzie podczas tworzenia farmy nie określono obowiązkowego pola "name" w ładunku wejściowym. Następujący komunikat o błędzie:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Dodawanie użytkowników lub rejestracji aplikacji do Azure Active Directory

Dostęp do interfejsów API platformy Azure FarmBeats można uzyskać za pomocą użytkownika lub rejestracji aplikacji w Azure Active Directory. Aby utworzyć rejestrację aplikacji w Azure Active Directory, wykonaj następujące kroki:

1. Przejdź do [Azure Portal](https://portal.azure.com)i wybierz pozycję **Azure Active Directory**  >  **rejestracje aplikacji**  >  **Nowa rejestracja**. Alternatywnie możesz użyć istniejącego konta.
2. W przypadku nowego konta wykonaj następujące czynności:

    - Wprowadź nazwę.
    - Wybierz **konta tylko w tym katalogu organizacji (pojedynczy dzierżawca)**.
    - Użyj wartości domyślnych w pozostałych polach.
    - Wybierz pozycję **Zarejestruj**.

3. W okienku **Przegląd** nowych i istniejących aplikacji wykonaj następujące czynności:

    - Przechwyć **Identyfikator klienta** i **Identyfikator dzierżawy**.
    - Przejdź do pozycji **Certyfikaty i wpisy tajne** , aby wygenerować nowy klucz tajny klienta i przechwycić **wpis tajny klienta**.
    - Wróć do **omówienia** i wybierz link obok pozycji **Zarządzaj aplikacją w katalogu lokalnym**.
    - Przejdź do pozycji **Właściwości** , aby przechwycić **Identyfikator obiektu**.

4. Przejdź do Datahub Swagger (https:// <yourdatahub> . azurewebsites.net/swagger/index.html) i wykonaj następujące czynności:
    - Przejdź do **interfejsu API RoleAssignment**.
    - Wykonaj wpis, aby utworzyć obiekt **RoleAssignment** dla właśnie utworzonego **identyfikatora obiektu** .
 
```json
{
  "roleDefinitionId": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "objectId": "objectId from step 3 above",
  "objectIdType": "ServicePrincipalId",
  "tenantId": "tenant id of your Azure subscription"
}
```

  > [!NOTE]
  > Aby uzyskać więcej informacji na temat dodawania użytkowników i rejestrowania Active Directory, zobacz [Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md).

Po wykonaniu powyższych kroków Rejestracja aplikacji (klienta) może wywołać interfejsy API usługi Azure FarmBeats przy użyciu tokenu dostępu za pośrednictwem uwierzytelniania okaziciela.

Użyj tokenu dostępu, aby wysłać go w kolejnych żądaniach interfejsu API w sekcji nagłówka jako:

```http
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
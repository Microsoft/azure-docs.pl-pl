---
title: Zarządzanie kosztami platformy Azure przy użyciu automatyzacji
description: W tym artykule wyjaśniono, jak zarządzać kosztami platformy Azure za pomocą automatyzacji.
author: bandersmsft
ms.author: banders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 47d9c2838c5c806214e3be2f9ba7ce335bc0af67
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956096"
---
# <a name="manage-costs-with-automation"></a>Zarządzanie kosztami przy użyciu automatyzacji

Automatyzacja w usłudze Cost Management pozwala utworzyć niestandardowy zestaw rozwiązań do pobierania danych kosztów i zarządzania nimi. W tym artykule opisano typowe scenariusze zastosowania automatyzacji w usłudze Cost Management i opcje dostępne w zależności od sytuacji. Jeśli chcesz programować z użyciem interfejsów API, zaprezentowano przykłady typowych żądań kierowanych do interfejsu API, które przyspieszą proces programowania.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Automatyzowanie pobierania danych kosztów do analizy w trybie offline

Pobranie danych kosztów platformy Azure może być konieczne w celu scalenia ich z innymi zestawami danych. Możesz też potrzebować zintegrować dane kosztów z własnymi systemami. Zależnie od ilości danych, których to dotyczy, dostępne są różne opcje. W każdym przypadku do korzystania z interfejsów API i narzędzi trzeba mieć odpowiedni zakres uprawnień w usłudze Cost Management. Aby uzyskać więcej informacji, zobacz [Przypisywanie dostępu do danych](./assign-access-acm-data.md).

## <a name="suggestions-for-handling-large-datasets"></a>Sugestie dotyczące obsługi dużych zestawów danych

Jeśli w organizacji używanych jest wiele zasobów i subskrypcji platformy Azure, masz dużą ilość danych ze szczegółami użycia. Program Excel często nie może ładować tak dużych plików. W tej sytuacji zalecamy następujące opcje:

**Power BI**

Usługa Power BI służy do pozyskiwania i obsługi dużych ilości danych. Jeśli jesteś klientem z umową Enterprise Agreement, możesz użyć aplikacji szablonu usługi Power BI do analizowania kosztów dla konta rozliczeniowego. Raport zawiera najważniejsze widoki używane przez klientów. Aby uzyskać więcej informacji, zobacz [Analizowanie kosztów platformy Azure za pomocą aplikacji szablonu usługi Power BI](./analyze-cost-data-azure-cost-management-power-bi-template-app.md).

**Łącznik danych usługi Power BI**

Jeśli chcesz analizować dane codziennie, zalecamy użycie [łącznika danych usługi Power BI](/power-bi/connect-data/desktop-connect-azure-cost-management) w celu pobierania danych do szczegółowej analizy. Wszystkie raporty, które tworzysz, są aktualizowane przez łącznik w miarę naliczania kolejnych kosztów.

**Eksporty z usługi Cost Management**

Możesz nie mieć potrzeby codziennego analizowania danych. W takim przypadku rozważ użycie funkcji [Eksporty](./tutorial-export-acm-data.md) w usłudze Cost Management w celu zaplanowania eksportów danych na konto usługi Azure Storage. Następnie możesz zgodnie z potrzebami ładować dane do usługi Power BI lub analizować je w programie Excel, jeśli plik będzie dostatecznie mały. Eksporty są dostępne w witrynie Azure Portal, ale można też je skonfigurować za pomocą [interfejsu API eksportów](/rest/api/cost-management/exports).

**Interfejs API szczegółów użycia**

Rozważ zastosowanie [interfejsu API szczegółów użycia](/rest/api/consumption/usageDetails), jeśli masz niewielki zestaw danych kosztów. W przypadku dużej ilości danych kosztów powinno się żądać najmniejszej możliwej ilości danych użycia dla okresu. W tym celu należy określić niewielki zakres czasu lub użyć filtru w żądaniu. Na przykład w scenariuszu, w którym potrzebne są dane kosztów z trzech lat, interfejs API radzi sobie lepiej, gdy wykonujesz wiele wywołań dla różnych zakresów czasu zamiast pojedynczego wywołania. Z tego miejsca możesz załadować dane do programu Excel w celu dalszej analizy.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatyzacja pobierania za pomocą interfejsu API szczegółów użycia

[Interfejs API szczegółów użycia](/rest/api/consumption/usageDetails) zapewnia łatwy sposób uzyskiwania nieprzetworzonych, niezagregowanych danych kosztów, które odnoszą się do rachunku za platformę Azure. Interfejs API jest przydatny, gdy organizacja potrzebuje rozwiązania pobierającego dane w sposób programistyczny. Rozważ użycie interfejsu API, jeśli chcesz analizować mniejsze zestawy danych kosztów. W przypadku większych zestawów danych powinno się jednak korzystać z innych rozwiązań wskazanych wcześniej. Dane szczegółów użycia są udostępniane dla poszczególnych liczników i dni. Są stosowane do obliczania rachunków miesięcznych. Ogólnie dostępna wersja tych interfejsów API to `2019-10-01`. Użyj wersji `2019-04-01-preview`, aby uzyskać dostęp do wersji zapoznawczej dla rezerwacji i zakupów w witrynie Azure Marketplace przy użyciu interfejsów API.

### <a name="usage-details-api-suggestions"></a>Sugestie dotyczące interfejsu API szczegółów użycia

**Harmonogram żądań**

Zalecamy, aby do interfejsu API szczegółów użycia kierować _nie więcej niż jedno żądanie_ dziennie. Aby uzyskać więcej informacji o tym, jak często dane kosztów są odświeżane i jak obsługiwane jest zaokrąglanie, zobacz [Omówienie danych usługi Cost Management](./understand-cost-mgt-data.md).

**Ukierunkowanie na zakresy najwyższego poziomu bez filtrowania**

Użyj interfejsu API, aby uzyskać wszystkie potrzebne dane w zakresie o najwyższym dostępnym poziomie. Przed wykonaniem jakiejkolwiek operacji filtrowania, grupowania lub agregowania albo analizy zagregowanych danych zaczekaj, aż wszystkie potrzebne dane zostaną pozyskane. Interfejs API jest zoptymalizowany pod kątem dostarczania dużej ilości niezagregowanych i nieprzetworzonych danych kosztów. Aby dowiedzieć się więcej o zakresach dostępnych w usłudze Cost Management, zobacz [Opis zakresów i praca z nimi](./understand-work-scopes.md). Po pobraniu potrzebnych danych dla zakresu użyj programu Excel do dalszej analizy danych za pomocą filtrów i tabel przestawnych.

## <a name="example-usage-details-api-requests"></a>Przykładowe żądania do interfejsu API dotyczące szczegółów użycia

Poniższe przykładowe żądania są używane przez klientów firmy Microsoft w typowych scenariuszach, które możesz napotkać.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Pobieranie szczegółów użycia dla zakresu w określonym zakresie dat

Dane zwracane przez żądanie odpowiadają dacie, kiedy informacje o użyciu zostały odebrane przez system rozliczeniowy. Mogą obejmować koszty z wielu faktur. Wywołanie, którego należy użyć, różni się w zależności od typu subskrypcji.

W przypadku starszych klientów z subskrypcją Enterprise Agreement (EA) lub z płatnością zgodnie z rzeczywistym użyciem użyj następującego wywołania:

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01
```

W przypadku klientów nowoczesnych z Umową z Klientem firmy Microsoft użyj następującego wywołania:

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?startDate=2020-08-01&endDate=&2020-08-05$top=1000&api-version=2019-10-01
```

### <a name="get-amortized-cost-details"></a>Pobieranie szczegółów amortyzowanego kosztu

Jeśli potrzebujesz rzeczywistych kosztów, aby pokazać zakupy w miarę ich naliczania, zmień *metrykę* na `ActualCost` w poniższym żądaniu. Aby skorzystać z kosztów amortyzowanych i rzeczywistych, musisz użyć wersji `2019-04-01-preview`. Bieżąca wersja interfejsu API działa tak samo jak wersja `2019-10-01`, z wyjątkiem nowego atrybutu typu/metryki i zmienionych nazw właściwości. Jeśli masz Umowę z Klientem Microsoft, Twoje filtry to `startDate` i `endDate` w poniższym przykładzie.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Cykliczne pobieranie dużych zestawów danych kosztów za pomocą eksportów

Za pomocą eksportów z usługi Cost Management można regularnie eksportować duże ilości danych. Eksportowanie jest zalecanym sposobem pobierania niezagregowanych danych dotyczących kosztów. Szczególnie gdy pliki użycia są zbyt duże, aby można było w niezawodny sposób wywoływać je i pobierać przy użyciu interfejsu API szczegółów użycia. Wyeksportowane dane są umieszczane na wybranym koncie usługi Azure Storage. Stamtąd możesz ładować je do własnych systemów i analizować zgodnie z potrzebami. Aby skonfigurować eksporty w witrynie Azure Portal, zobacz [Eksportowanie danych](tutorial-export-acm-data.md).

Jeśli chcesz zautomatyzować eksporty w różnych zakresach, dobrym punktem wyjścia jest przykładowe żądanie interfejsu API w następnej sekcji. Za pomocą interfejsu API eksportów możesz tworzyć automatyczne eksporty w ramach ogólnej konfiguracji środowiska. Automatyczne eksporty pomagają upewnić się, że masz potrzebne dane. W miarę rozszerzania zakresu zastosowań platformy Azure możesz używać ich w systemach swojej organizacji.

### <a name="common-export-configurations"></a>Typowe konfiguracje eksportu

Zanim utworzysz pierwszy eksport, rozważ scenariusz i opcje konfiguracji niezbędne do jego realizacji. Rozważ następujące opcje eksportu:

- **Cykl** — określa częstotliwość uruchamiania zadania eksportu oraz moment umieszczenia pliku na koncie usługi Azure Storage. Do wyboru są opcje Codziennie, Co tydzień i Co miesiąc. Spróbuj skonfigurować cykl w taki sposób, aby pasował do zadań importu danych używanych przez wewnętrzny system w organizacji.
- **Okres cyklu** — określa, jak długo eksport pozostaje ważny. Pliki są eksportowane tylko w okresie cyklu.
- **Horyzont czasowy** — określa ilość danych wygenerowanych przez eksport w danym przebiegu. Typowe opcje to Bieżący miesiąc i Bieżący tydzień.
- **Data rozpoczęcia** — konfiguruje czas rozpoczęcia harmonogramu eksportu. Eksport jest tworzony w dniu rozpoczęcia i później, w zależności od ustawień cyklu.
- **Typ** — istnieją trzy typy eksportu:
  - Koszt rzeczywisty — pokazuje łączne użycie i koszty dla określonego okresu, gdy są naliczane i widoczne na rachunku.
  - Koszt amortyzowany — pokazuje łączne użycie i koszty dla określonego okresu z zastosowaną amortyzacją obowiązujących kosztów zakupu rezerwacji.
  - Użycie — wszystkie eksporty utworzone przed 20 lipca 2020 r. mają typ Użycie. Zaktualizuj wszystkie zaplanowane eksporty jako Koszt rzeczywisty lub Koszt amortyzowany.
- **Kolumny** — definiuje pola danych, które mają być uwzględnione w pliku eksportu. Odpowiadają one polom dostępnym w interfejsie API szczegółów użycia. Aby uzyskać więcej informacji, zobacz [Interfejs API szczegółów użycia](/rest/api/consumption/usagedetails/list).

### <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Tworzenie codziennego eksportu z bieżącego miesiąca na potrzeby subskrypcji

Adres URL żądania: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

### <a name="automate-alerts-and-actions-with-budgets"></a>Automatyzowanie alertów i akcji przy użyciu budżetów

Istnieją dwa kluczowe składniki umożliwiające maksymalizowanie wartości inwestycji w chmurze. Jednym z nich jest automatyczne tworzenie budżetu. Drugi to konfigurowanie aranżacji opartej na kosztach w odpowiedzi na alerty budżetowe. Tworzenie budżetu platformy Azure można zautomatyzować dwiema różnymi metodami. Po przekroczeniu skonfigurowanych progów alertów zdarzają się różne odpowiedzi na alerty.

W poniższych sekcjach opisano dostępne opcje i przedstawiono przykładowe żądania interfejsu API umożliwiające rozpoczęcie pracy z automatyzacją budżetu.

#### <a name="how-costs-are-evaluated-against-your-budget-threshold"></a>Sposób oceniania kosztów względem progu budżetu

Koszty są oceniane względem progu budżetu raz dziennie. Po utworzeniu nowego budżetu lub w dniu resetowania budżetu koszty w porównaniu z progiem będą równe zero/null, ponieważ ocena mogła jeszcze nie zostać przeprowadzona.

Gdy platforma Azure wykryje, że koszty przekroczyły wartość progową, w ciągu godziny od wykrycia zostanie wyzwolone powiadomienie.

#### <a name="view-your-current-cost"></a>Wyświetlanie bieżącego kosztu

Aby wyświetlić bieżące koszty, musisz wykonać wywołanie GET przy użyciu [interfejsu API zapytania](/rest/api/cost-management/query).

Wywołanie GET do interfejsu API budżetów nie zwróci bieżących kosztów wyświetlanych w analizie kosztów. Zamiast tego wywołanie zwróci ostatnio oceniony koszt.

### <a name="automate-budget-creation"></a>Automatyzowanie tworzenia budżetu

Tworzenie budżetu można zautomatyzować za pomocą [interfejsu API budżetów](/rest/api/consumption/budgets). Można również utworzyć budżet za pomocą [szablonu budżetu](quick-create-budget-template.md). Szablony to łatwy sposób na ustandaryzowanie wdrożeń platformy Azure z zapewnieniem prawidłowej konfiguracji i wymuszania kontroli kosztów.

#### <a name="supported-locales-for-budget-alert-emails"></a>Ustawienia regionalne obsługiwane w przypadku wiadomości e-mail z alertami budżetowymi

W przypadku budżetów otrzymasz alert, gdy koszty przekroczą ustawiony próg. Możesz skonfigurować maksymalnie pięciu adresatów wiadomości e-mail na budżet. Adresaci będą otrzymywać alerty e-mail w ciągu 24 godzin od przekroczenia progu budżetu. Może być jednak konieczne wysłanie do adresata wiadomości e-mail w innym języku. W interfejsie API budżetów można używać następujących kodów kultur języków. Ustaw kod kultury przy użyciu parametru `locale` podobnie jak w poniższym przykładzie.

```json
{
  "eTag": "\"1d681a8fc67f77a\"",
  "properties": {
    "timePeriod": {
      "startDate": "2020-07-24T00:00:00Z",
      "endDate": "2022-07-23T00:00:00Z"
    },
    "timeGrain": "BillingMonth",
    "amount": 1,
    "currentSpend": {
      "amount": 0,
      "unit": "USD"
    },
    "category": "Cost",
    "notifications": {
      "actual_GreaterThan_10_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 20,
        "locale": "en-us",
        "contactEmails": [
          "user@contoso.com"
        ],
        "contactRoles": [],
        "contactGroups": [],
        "thresholdType": "Actual"
      }
    }
  }
}

```

Języki obsługiwane przez kod kultury:

| Kod kultury| Język |
| --- | --- |
| en-us | Angielski (Stany Zjednoczone) |
| ja-jp | Japoński (Japonia) |
| zh-cn | Chiński (uproszczony, Chiny) |
| de-de | Niemiecki (Niemcy) |
| es-es | Hiszpański (Hiszpania — międzynarodowy) |
| fr-fr | Francuski (Francja) |
| it-it | Włoski (Włochy) |
| ko-kr | Koreański (Korea) |
| pt-br | Portugalski (Brazylia) |
| ru-ru | Rosyjski (Rosja) |
| zh-tw | Chiński (tradycyjny, Tajwan) |
| cs-cz | Czeski (Czechy) |
| pl-pl | Polski (Polska) |
| tr-tr | Turecki (Turcja) |
| da-dk | Duński (Dania) |
| en-gb | Angielski (Zjednoczone Królestwo) |
| hu-hu | Węgierski (Węgry) |
| nb-bo | Norweski, Bokmal (Norwegia) |
| nl-nl | Niderlandzki (Holandia) |
| pt-pt | Portugalski (Portugalia) |
| sv-se | Szwedzki (Szwecja) |

#### <a name="common-budgets-api-configurations"></a>Typowe konfiguracje interfejsu API budżetów

Istnieje wiele sposobów konfigurowania budżetu w środowisku platformy Azure. Najpierw rozważ scenariusz, a następnie zidentyfikuj opcje konfiguracji, które pozwolą go zrealizować. Zapoznaj się z następującymi opcjami:

- **Ziarno czasu** — reprezentuje okres cyklu używany przez budżet do naliczania i oceniania kosztów. Najbardziej popularne opcje to: Miesięcznie, Kwartalnie i Rocznie.
- **Okres** — określa, jak długo budżet jest ważny. Budżet aktywnie monitoruje i wyświetla alerty tylko w okresie swojej ważności.
- **Powiadomienia**
  - Kontakty e-mail — gdy budżet nalicza koszty i przekracza zdefiniowane progi, na adresy e-mail są wysyłane alerty.
  - Role kontaktu — ta opcja powoduje, że alerty e-mail otrzymują wszyscy użytkownicy z odpowiednią rolą na platformie Azure. Na przykład właściciele subskrypcji mogą otrzymać alert o utworzeniu budżetu w zakresie subskrypcji.
  - Grupy kontaktów — po przekroczeniu progu alertu budżet wywołuje skonfigurowane grupy akcji.
- **Filtry wymiaru kosztu** — to samo filtrowanie, które można przeprowadzić w analizie kosztów lub interfejsie API zapytania, jest również dostępne w budżecie. Ten filtr umożliwia ograniczenie zakresu kosztów monitorowanych w budżecie.

Po zidentyfikowaniu opcji tworzenia budżetu, które spełniają Twoje wymagania, utwórz budżet przy użyciu interfejsu API. Poniższy przykład ułatwia rozpoczęcie pracy z typową konfiguracją budżetu.

**Tworzenie budżetu przefiltrowanego według wielu zasobów i tagów**

Adres URL żądania: `PUT https://management.azure.com/subscriptions/{SubscriptionId} /providers/Microsoft.Consumption/budgets/{BudgetName}/?api-version=2019-10-01`

```json
{
  "eTag": "\"1d34d016a593709\"",
  "properties": {
    "category": "Cost",
    "amount": 100.65,
    "timeGrain": "Monthly",
    "timePeriod": {
      "startDate": "2017-10-01T00:00:00Z",
      "endDate": "2018-10-31T00:00:00Z"
    },
    "filter": {
      "and": [
        {
          "dimensions": {
            "name": "ResourceId",
            "operator": "In",
            "values": [
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}",
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}"
            ]
          }
        },
        {
          "tags": {
            "name": "category",
            "operator": "In",
            "values": [
              "Dev",
              "Prod"
            ]
          }
        },
        {
          "tags": {
            "name": "department",
            "operator": "In",
            "values": [
              "engineering",
              "sales"
            ]
          }
        }
      ]
    },
    "notifications": {
      "Actual_GreaterThan_80_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 80,
        "contactEmails": [
          "user1@contoso.com",
          "user2@contoso.com"
        ],
        "contactRoles": [
          "Contributor",
          "Reader"
        ],
        "contactGroups": [
          "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.insights/actionGroups/{actionGroupName}
        ],
        "thresholdType": "Actual"
      }
    }
  }
}
```

### <a name="configure-cost-based-orchestration-for-budget-alerts"></a>Konfigurowanie aranżacji opartej na kosztach dla alertów budżetu

Budżety można skonfigurować tak, aby uruchamiały zautomatyzowane akcje, używając grup akcji platformy Azure. Aby dowiedzieć się więcej na temat automatyzowania akcji przy użyciu budżetów, zobacz [Automatyzacja za pomocą usługi Azure Budgets](../manage/cost-management-budget-scenario.md).

## <a name="data-latency-and-rate-limits"></a>Opóźnienie danych i limity szybkości

Zalecamy wywoływanie interfejsów API nie częściej niż raz dziennie. Dane usługi Cost Management są odświeżane co cztery godziny, gdy nowe dane użycia są odbierane od dostawców zasobów platformy Azure. Częstsze wywołania nie dadzą dostępu do żadnych dodatkowych danych. Zamiast tego wzrośnie obciążenie. Aby dowiedzieć się więcej o tym, jak często zmieniają się dane i jak są obsługiwane opóźnienia danych, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md).

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Kod błędu 429 — Liczba wywołań przekroczyła limity szybkości

Aby zapewnić jednakowe działanie dla wszystkich subskrybentów usługi Cost Management, interfejsy API tej usługi mają ograniczenia szybkości. Po osiągnięciu limitu zostanie wyświetlony kod stanu HTTP `429: Too many requests`. Bieżące limity przepływności dla naszych interfejsów API są następujące:

- 30 wywołań na minutę — na zakres, na użytkownika lub na aplikację.
- 200 wywołań na minutę — na dzierżawę, na użytkownika lub na aplikację.

## <a name="next-steps"></a>Następne kroki

- [Analizowanie kosztów platformy Azure za pomocą aplikacji szablonu usługi Power BI](./analyze-cost-data-azure-cost-management-power-bi-template-app.md)
- [Eksportowanie danych i zarządzanie nimi](./tutorial-export-acm-data.md) za pomocą funkcji Eksporty.
- Dowiedz się więcej na temat [interfejsu API szczegółów użycia](/rest/api/consumption/usageDetails).
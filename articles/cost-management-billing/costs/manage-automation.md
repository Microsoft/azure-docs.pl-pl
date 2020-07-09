---
title: Zarządzanie kosztami platformy Azure przy użyciu automatyzacji
description: W tym artykule wyjaśniono, jak zarządzać kosztami platformy Azure za pomocą automatyzacji.
author: bandersmsft
ms.author: banders
ms.date: 04/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 0727f98b917944f3721c6c6758fde05c2efd8773
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449835"
---
# <a name="manage-costs-with-automation"></a>Zarządzanie kosztami przy użyciu automatyzacji

Automatyzacja w usłudze Cost Management pozwala utworzyć niestandardowy zestaw rozwiązań do pobierania danych kosztów i zarządzania nimi. W tym artykule opisano typowe scenariusze zastosowania automatyzacji w usłudze Cost Management i opcje dostępne w zależności od sytuacji. Jeśli chcesz programować z użyciem interfejsów API, zaprezentowano przykłady typowych żądań kierowanych do interfejsu API, które przyspieszą proces programowania.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Automatyzowanie pobierania danych kosztów do analizy w trybie offline

Pobranie danych kosztów platformy Azure może być konieczne w celu scalenia ich z innymi zestawami danych. Możesz też potrzebować zintegrować dane kosztów z własnymi systemami. Zależnie od ilości danych, których to dotyczy, dostępne są różne opcje. W każdym przypadku do korzystania z interfejsów API i narzędzi trzeba mieć odpowiedni zakres uprawnień w usłudze Cost Management. Aby uzyskać więcej informacji, zobacz [Przypisywanie dostępu do danych](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data).

## <a name="suggestions-for-handling-large-datasets"></a>Sugestie dotyczące obsługi dużych zestawów danych

Jeśli w organizacji używanych jest wiele zasobów i subskrypcji platformy Azure, masz dużą ilość danych ze szczegółami użycia. Program Excel często nie może ładować tak dużych plików. W tej sytuacji zalecamy następujące opcje:

**Power BI**

Usługa Power BI służy do pozyskiwania i obsługi dużych ilości danych. Jeśli jesteś klientem z umową Enterprise Agreement, możesz użyć aplikacji szablonu usługi Power BI do analizowania kosztów dla konta rozliczeniowego. Raport zawiera najważniejsze widoki używane przez klientów. Aby uzyskać więcej informacji, zobacz [Analizowanie kosztów platformy Azure za pomocą aplikacji szablonu usługi Power BI](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).

**Łącznik danych usługi Power BI**

Jeśli chcesz analizować dane codziennie, zalecamy użycie [łącznika danych usługi Power BI](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management) w celu pobierania danych do szczegółowej analizy. Wszystkie raporty, które tworzysz, są aktualizowane przez łącznik w miarę naliczania kolejnych kosztów.

**Eksporty z usługi Cost Management**

Możesz nie mieć potrzeby codziennego analizowania danych. W takim przypadku rozważ użycie funkcji [Eksporty](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) w usłudze Cost Management w celu zaplanowania eksportów danych na konto usługi Azure Storage. Następnie możesz zgodnie z potrzebami ładować dane do usługi Power BI lub analizować je w programie Excel, jeśli plik będzie dostatecznie mały. Eksporty są dostępne w witrynie Azure Portal, ale można też je skonfigurować za pomocą [interfejsu API eksportów](https://docs.microsoft.com/rest/api/cost-management/exports).

**Interfejs API szczegółów użycia**

Rozważ zastosowanie [interfejsu API szczegółów użycia](https://docs.microsoft.com/rest/api/consumption/usageDetails), jeśli masz niewielki zestaw danych kosztów. W przypadku dużej ilości danych kosztów powinno się żądać najmniejszej możliwej ilości danych użycia dla okresu. W tym celu należy określić niewielki zakres czasu lub użyć filtru w żądaniu. Na przykład w scenariuszu, w którym potrzebne są dane kosztów z trzech lat, interfejs API radzi sobie lepiej, gdy wykonujesz wiele wywołań dla różnych zakresów czasu zamiast pojedynczego wywołania. Z tego miejsca możesz załadować dane do programu Excel w celu dalszej analizy.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatyzacja pobierania za pomocą interfejsu API szczegółów użycia

[Interfejs API szczegółów użycia](https://docs.microsoft.com/rest/api/consumption/usageDetails) zapewnia łatwy sposób uzyskiwania nieprzetworzonych, niezagregowanych danych kosztów, które odnoszą się do rachunku za platformę Azure. Interfejs API jest przydatny, gdy organizacja potrzebuje rozwiązania pobierającego dane w sposób programistyczny. Rozważ użycie interfejsu API, jeśli chcesz analizować mniejsze zestawy danych kosztów. W przypadku większych zestawów danych powinno się jednak korzystać z innych rozwiązań wskazanych wcześniej. Dane szczegółów użycia są udostępniane dla poszczególnych liczników i dni. Są stosowane do obliczania rachunków miesięcznych. Ogólnie dostępna wersja tych interfejsów API to `2019-10-01`. Użyj wersji `2019-04-01-preview`, aby uzyskać dostęp do wersji zapoznawczej dla rezerwacji i zakupów w witrynie Azure Marketplace przy użyciu interfejsów API.

### <a name="usage-details-api-suggestions"></a>Sugestie dotyczące interfejsu API szczegółów użycia

**Harmonogram żądań**

Zalecamy, aby do interfejsu API szczegółów użycia kierować _nie więcej niż jedno żądanie_ dziennie. Aby uzyskać więcej informacji o tym, jak często dane kosztów są odświeżane i jak obsługiwane jest zaokrąglanie, zobacz [Omówienie danych usługi Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule).

**Ukierunkowanie na zakresy najwyższego poziomu bez filtrowania**

Użyj interfejsu API, aby uzyskać wszystkie potrzebne dane w zakresie o najwyższym dostępnym poziomie. Przed wykonaniem jakiejkolwiek operacji filtrowania, grupowania lub agregowania albo analizy zagregowanych danych zaczekaj, aż wszystkie potrzebne dane zostaną pozyskane. Interfejs API jest zoptymalizowany pod kątem dostarczania dużej ilości niezagregowanych i nieprzetworzonych danych kosztów. Aby dowiedzieć się więcej o zakresach dostępnych w usłudze Cost Management, zobacz [Opis zakresów i praca z nimi](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes). Po pobraniu potrzebnych danych dla zakresu użyj programu Excel do dalszej analizy danych za pomocą filtrów i tabel przestawnych.

## <a name="example-usage-details-api-requests"></a>Przykładowe żądania do interfejsu API dotyczące szczegółów użycia

Poniższe przykładowe żądania są używane przez klientów firmy Microsoft w typowych scenariuszach, które możesz napotkać.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Pobieranie szczegółów użycia dla zakresu w określonym zakresie dat

Dane zwracane przez żądanie odpowiadają dacie, kiedy informacje o użyciu zostały odebrane przez system rozliczeniowy. Mogą obejmować koszty z wielu faktur.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>Pobieranie szczegółów amortyzowanego kosztu

Jeśli potrzebujesz rzeczywistych kosztów, aby pokazać zakupy w miarę ich naliczania, zmień *metrykę* na `ActualCost` w poniższym żądaniu. Aby skorzystać z kosztów amortyzowanych i rzeczywistych, musisz użyć wersji `2019-04-01-preview`. Bieżąca wersja interfejsu API działa tak samo jak wersja `2019-10-01`, z wyjątkiem nowego atrybutu typu/metryki i zmienionych nazw właściwości. Jeśli masz Umowę z Klientem Microsoft, Twoje filtry to `startDate` i `endDate` w poniższym przykładzie.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Cykliczne pobieranie dużych zestawów danych kosztów za pomocą eksportów

Funkcja Eksporty to rozwiązanie do planowania regularnych zrzutów danych kosztów. Jest to zalecany sposób pobierania niezagregowanych danych kosztów dla organizacji, których pliki użycia mogą być zbyt duże, aby niezawodnie wywoływać i pobierać dane przy użyciu interfejsu API szczegółów użycia. Dane są umieszczane na wybranym koncie usługi Azure Storage. Z tego miejsca możesz załadować je do własnych systemów i analizować zgodnie z potrzebami swoich zespołów. Aby skonfigurować eksporty w witrynie Azure Portal, zobacz [Eksportowanie danych](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data).

## <a name="data-latency-and-rate-limits"></a>Opóźnienie danych i limity szybkości

Zalecamy wywoływanie interfejsów API nie częściej niż raz dziennie. Dane usługi Cost Management są odświeżane co cztery godziny, gdy nowe dane użycia są odbierane od dostawców zasobów platformy Azure. Częstsze wywołania nie dadzą dostępu do żadnych dodatkowych danych. Zamiast tego wzrośnie obciążenie. Aby dowiedzieć się więcej o tym, jak często zmieniają się dane i jak są obsługiwane opóźnienia danych, zobacz [Omówienie danych usługi Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data).

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Kod błędu 429 — Liczba wywołań przekroczyła limity szybkości

Aby zapewnić jednakowe działanie dla wszystkich subskrybentów usługi Cost Management, interfejsy API tej usługi mają ograniczenia szybkości. Po osiągnięciu limitu zostanie wyświetlony kod stanu HTTP `429: Too many requests`. Bieżące limity przepływności dla naszych interfejsów API są następujące:

- 30 wywołań na minutę — na zakres, na użytkownika lub na aplikację.
- 200 wywołań na minutę — na dzierżawę, na użytkownika lub na aplikację.

## <a name="next-steps"></a>Następne kroki

- [Analizowanie kosztów platformy Azure za pomocą aplikacji szablonu usługi Power BI](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)
- [Eksportowanie danych i zarządzanie nimi](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) za pomocą funkcji Eksporty.
- Dowiedz się więcej na temat [interfejsu API szczegółów użycia](https://docs.microsoft.com/rest/api/consumption/usageDetails).
---
title: Wskazówki dotyczące danych osobowych przechowywanych w usłudze Azure Log Analytics | Microsoft Docs
description: W tym artykule opisano sposób zarządzania danymi osobowymi przechowywanymi w usłudze Azure Log Analytics i metodami ich identyfikacji i usuwania.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: 64c461c5d3e1bb34f480e5173621f8753eadbbd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318321"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Wskazówki dotyczące danych osobowych przechowywanych w usługach Log Analytics i Application Insights

Log Analytics to magazyn danych, w którym można znaleźć dane osobowe. Application Insights przechowuje dane w partycji Log Analytics. W tym artykule omówiono, gdzie w Log Analytics i Application Insights takie dane są zwykle znajdowane, a także możliwości obsługi takich danych.

> [!NOTE]
> Na potrzeby tego artykułu _dane dziennika_ odnoszą się do danych wysyłanych do obszaru roboczego log Analytics, podczas gdy _dane aplikacji_ odwołują się do danych zbieranych przez Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategia obsługi danych osobowych

Mimo że będzie to ty i Twoja firma ostatecznie określi strategię, w której będą obsługiwane dane prywatne (Jeśli w ogóle), poniżej przedstawiono kilka możliwych metod. Są one wymienione w kolejności preferencji z punktu widzenia technicznego od najbardziej do najmniej lepszych:

* Jeśli to możliwe, Zatrzymaj zbieranie, zaciemniania, zachowywanie anonimowości lub w inny sposób Dostosuj zbierane dane, aby wykluczyć je z nich jako "prywatne". _Jest to zdecydowanie_ preferowane podejście, co pozwala zaoszczędzić konieczność utworzenia bardzo kosztownej i wpływającej strategii obsługi danych.
* Gdy nie jest to możliwe, spróbuj znormalizować dane, aby zmniejszyć wpływ na platformę danych i wydajność. Na przykład zamiast rejestrowania jawnego identyfikatora użytkownika, należy utworzyć dane wyszukiwania, które będą skorelować nazwę użytkownika i ich szczegóły do identyfikatora wewnętrznego, który można następnie zarejestrować w innym miejscu. W ten sposób, jeśli jeden z użytkowników poprosił Cię o usunięcie danych osobowych, istnieje możliwość, że tylko usunięcie wiersza z tabeli odnośników odpowiadającego użytkownikowi będzie wystarczające. 
* Na koniec Jeśli dane prywatne muszą być zbierane, należy skompilować proces wokół ścieżki interfejsu API przeczyszczania i istniejącej ścieżki interfejsu API zapytania, aby spełnić wszelkie zobowiązania, które mogą wystąpić podczas eksportowania i usuwania wszelkich prywatnych danych skojarzonych z użytkownikiem. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Gdzie mają być wyszukiwane dane prywatne w Log Analytics?

Log Analytics to elastyczny magazyn, który jednocześnie określa schemat dla danych, umożliwia przesłonięcie każdego pola z wartościami niestandardowymi. Ponadto można pozyskać wszystkie schematy niestandardowe. W związku z tym nie można mówić dokładnie, gdzie dane prywatne zostaną znalezione w określonym obszarze roboczym. Jednak następujące lokalizacje są dobrym punktem początkowym w spisie:

### <a name="log-data"></a>Dane dziennika

* *Adresy IP*: log Analytics zbiera różne informacje o adresie IP w wielu różnych tabelach. Na przykład następujące zapytanie pokazuje wszystkie tabele, w których zostały zebrane adresy IPv4 w ciągu ostatnich 24 godzin:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Identyfikatory użytkowników*: identyfikatory użytkowników są dostępne w wielu różnych rozwiązaniach i tabelach. Można wyszukać konkretną nazwę użytkownika w całym zestawie danych przy użyciu polecenia Search:
    ```
    search "[username goes here]"
    ```
  Pamiętaj, aby wyszukać nie tylko dla użytkowników, których nazwy są czytelne, ale także identyfikatory GUID, które mogą być bezpośrednio wyśledzone przez określonego użytkownika.
* *Identyfikatory urządzeń*: takie jak identyfikatory użytkowników, identyfikatory urządzeń są czasami uznawane za prywatne. Użyj takiego samego podejścia jak wymienione powyżej dla identyfikatorów użytkowników, aby identyfikować tabele, w których może to być problem. 
* *Dane niestandardowe*: log Analytics zezwala na zbieranie w różnych metodach: dzienniki niestandardowe i pola niestandardowe, [interfejs API modułu zbierającego dane http](./data-collector-api.md) oraz dane niestandardowe zbierane w ramach dzienników zdarzeń systemu. Wszystkie z nich są podatne na dane prywatne i powinny być zbadane w celu sprawdzenia, czy istnieją takie dane.
* *Dane przechwycone przez rozwiązanie*: ponieważ mechanizm rozwiązania jest otwartym zakończono, zalecamy przejrzenie wszystkich tabel generowanych przez rozwiązania w celu zapewnienia zgodności.

### <a name="application-data"></a>Dane aplikacji

* *Adresy IP*: podczas Application Insights domyślnie zasłania wszystkie pola adresu IP do "0.0.0.0", jest to dość typowy wzorzec, który zastępuje tę wartość za pomocą rzeczywistego adresu IP użytkownika w celu przechowywania informacji o sesji. Poniższe zapytanie analityczne służy do znajdowania dowolnej tabeli zawierającej wartości w kolumnie adres IP inną niż "0.0.0.0" w ciągu ostatnich 24 godzin:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Identyfikatory użytkowników*: domyślnie Application Insights będą używać losowo generowanych identyfikatorów do śledzenia użytkowników i sesji. Jednak często są wyświetlane te pola, które zostały zastąpione, aby przechowywać identyfikator bardziej istotny dla aplikacji. Na przykład: usernames, identyfikatory GUID usługi AAD itd. Te identyfikatory często są uważane za jako dane osobowe, a w związku z tym powinny być odpowiednio obsługiwane. Nasze zalecenie zawsze próbuje zamieszanie lub zachowywanie anonimowości tych identyfikatorów. Pola, w których te wartości są często dostępne, obejmują session_Id, user_Id, user_AuthenticatedId, user_AccountId, a także customDimensions.
* *Dane niestandardowe*: Application Insights umożliwia dołączanie zestawu niestandardowych wymiarów do dowolnego typu danych. Te wymiary mogą być *dowolnymi* danymi. Użyj następującego zapytania, aby zidentyfikować wszystkie niestandardowe wymiary zebrane w ciągu ostatnich 24 godzin:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Dane znajdujące się w pamięci i w tranzycie*: Application Insights będą śledzić wyjątki, żądania, wywołania zależności i ślady. Dane prywatne mogą być często zbierane na poziomie kodu i wywołania HTTP. Przejrzyj tabele wyjątki, żądania, zależności i ślady, aby zidentyfikować takie dane. Użyj [inicjatorów telemetrii](../app/api-filtering-sampling.md) , gdy jest to możliwe, aby zasłaniać te dane.
* *Przechwytywanie Snapshot Debugger*: funkcja [Snapshot Debugger](../app/snapshot-debugger.md) w Application Insights umożliwia zbieranie migawek debugowania za każdym razem, gdy wyjątek jest przechwytywany w wystąpieniu produkcyjnym aplikacji. Migawki będą uwidaczniać pełny ślad stosu prowadzący do wyjątków oraz wartości zmiennych lokalnych w każdym kroku stosu. Niestety, ta funkcja nie zezwala na selektywne usuwanie punktów przystawek ani dostęp programistyczny do danych w ramach migawki. W związku z tym, jeśli domyślna częstotliwość przechowywania migawek nie spełnia wymagań dotyczących zgodności, zaleca się wyłączenie tej funkcji.

## <a name="how-to-export-and-delete-private-data"></a>Jak eksportować i usuwać dane prywatne

Jak wspomniano wcześniej w [strategii dotyczącej obsługi danych osobowych](#strategy-for-personal-data-handling) , __zdecydowanie__ zaleca się, aby można było zmienić strukturę zasad zbierania danych w celu wyłączenia zbierania danych prywatnych, zasłaniania lub anonymizing go albo w inny sposób, aby usunąć go z uznawania za "Private". Obsługa danych spowoduje powstanie kosztów dla Ciebie i Twojego zespołu w celu zdefiniowania i zautomatyzowania strategii, skompilowania interfejsu dla klientów w celu korzystania z danych za pomocą i bieżących kosztów konserwacji. Dodatkowo jest to kosztowne kosztowo dla Log Analytics i Application Insights, a wiele wywołań interfejsu API współbieżnych zapytań lub przeczyszczania może mieć negatywny wpływ na wszystkie inne interakcje z funkcjami Log Analytics. Wspomniane w rzeczywistości istnieją pewne prawidłowe scenariusze, w których należy zebrać dane prywatne. W takich przypadkach dane powinny być obsługiwane zgodnie z opisem w tej sekcji.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Wyświetl i Eksportuj

W przypadku żądań danych widoku i eksportu należy użyć [interfejsu API zapytania log Analytics](https://dev.loganalytics.io/) lub  [interfejsu API zapytania Application Insights](https://dev.applicationinsights.io/quickstart) . Logika umożliwiająca przekonwertowanie kształtu danych na odpowiedni, który zostanie zaimplementowana dla użytkowników. [Azure Functions](https://azure.microsoft.com/services/functions/) jest doskonałym miejscem do hostowania takich logiki.

> [!IMPORTANT]
>  Chociaż większość operacji przeczyszczania może zakończyć się znacznie szybciej niż umowa SLA, **formalna umowa SLA na zakończenie operacji przeczyszczania jest ustawiana na 30 dni** z powodu dużego wpływu na używanej platformy danych. Jest to proces zautomatyzowany; nie ma sposobu na zażądanie, aby operacja była szybsza.

### <a name="delete"></a>Usuwanie

> [!WARNING]
> Usuwanie w Log Analytics są destrukcyjne i nieodwracalne! W ich wykonaniu należy zachować szczególną ostrożność.

Udostępniono jako część obsługi prywatności ścieżkę interfejsu API *przeczyszczania* . Ta ścieżka powinna być stosowana oszczędnie ze względu na ryzyko związane z tym, potencjalny wpływ na wydajność oraz możliwość pochylania wszystkich agregacji, pomiarów i innych aspektów danych Log Analytics. Zapoznaj się z sekcją [strategia obsługi danych osobowych](#strategy-for-personal-data-handling) , aby poznać alternatywne podejścia do obsługi prywatnych danych.

Przeczyszczanie jest operacją o wysokim poziomie uprawnień, która nie ma uprawnień do Azure Resource Manager wykonania aplikacji ani użytkownika na platformie Azure (w tym nawet Właściciel zasobu). Ta rola jest w trakcie _przeczyszczania danych_ i powinna być ostrożnie delegowana ze względu na potencjalną utratę danych. 

> [!IMPORTANT]
> Aby można było zarządzać zasobami systemu, żądania przeczyszczania są ograniczone o 50 żądań na godzinę. Należy wykonać przetwarzanie wsadowe żądań przeczyszczenia, wysyłając pojedyncze polecenie, którego predykat zawiera wszystkie tożsamości użytkowników, które wymagają przeczyszczania. Użyj [operatora in](/azure/kusto/query/inoperator) , aby określić wiele tożsamości. Przed wykonaniem żądania przeczyszczenia należy uruchomić zapytanie, aby sprawdzić, czy wyniki są oczekiwane. 



Po przypisaniu roli Azure Resource Manager dostępne są dwie nowe ścieżki interfejsu API: 

#### <a name="log-data"></a>Dane dziennika

* [Po przeczyszczeniu](/rest/api/loganalytics/workspacepurge/purge) — pobiera parametry danych do usunięcia i zwraca identyfikator GUID odwołania 
* Pobieranie stanu przeczyszczania — wywołanie po przeczyszczeniu zwróci nagłówek "x-MS-status-Location", który będzie zawierać adres URL, który można wywołać w celu określenia stanu interfejsu API przeczyszczania. Na przykład:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Chociaż oczekujemy, że większość operacji przeczyszczania jest realizowana znacznie szybciej niż w przypadku umowy SLA, ze względu na ich duży wpływ na platformę danych używaną przez Log Analytics, **formalna umowa SLA dla ukończenia operacji przeczyszczania jest ustawiana na 30 dni**. 

#### <a name="application-data"></a>Dane aplikacji

* [Po przeczyszczeniu](/rest/api/application-insights/components/purge) — pobiera parametry danych do usunięcia i zwraca identyfikator GUID odwołania
* Pobieranie stanu przeczyszczania — wywołanie po przeczyszczeniu zwróci nagłówek "x-MS-status-Location", który będzie zawierać adres URL, który można wywołać w celu określenia stanu interfejsu API przeczyszczania. Na przykład:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Chociaż większość operacji przeczyszczania może zakończyć się znacznie szybciej niż umowa SLA, ze względu na ich duży wpływ na platformę danych używaną przez Application Insights, **formalna umowa SLA dla ukończenia operacji przeczyszczania jest ustawiana na 30 dni**.

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej o tym, jak Log Analytics dane są zbierane, przetwarzane i zabezpieczone, zobacz [log Analytics zabezpieczenia danych](./data-security.md).
- Aby dowiedzieć się więcej o tym, jak Application Insights dane są zbierane, przetwarzane i zabezpieczone, zobacz [Application Insights zabezpieczenia danych](../app/data-retention-privacy.md).


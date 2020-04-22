---
title: Omówienie monitorowania alertów i powiadomień na platformie Azure
description: Omówienie alertów na platformie Azure. Alerty, klasyczne alerty i interfejs alertów.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: e02c23623062f5cb3e4c597b0bb257b30aa9f44a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769766"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Przegląd alertów na platformie Microsoft Azure 

W tym artykule opisano, jakie są alerty, ich korzyści i jak rozpocząć korzystanie z nich.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Co to są alerty na platformie Microsoft Azure?
Alerty proaktywnie powiadamiają o znalezieniu ważnych warunków w danych monitorowania. Umożliwiają one identyfikowanie i rozwiązywanie problemów, zanim użytkownicy systemu zauważą je. 

W tym artykule omówiono ujednolicone środowisko alertów w usłudze Azure Monitor, które obejmuje alerty, które wcześniej były zarządzane przez usługę Log Analytics i usługa Application Insights. [Poprzednie środowisko alertów](alerts-classic.overview.md) i typy alertów są nazywane *alertami klasycznymi*. Możesz wyświetlić to starsze środowisko i starszy typ alertu, wybierając **pozycję Wyświetl klasyczne alerty** u góry strony alertu. 

## <a name="overview"></a>Omówienie

Poniższy diagram przedstawia przepływ alertów. 

![Diagram przepływu alertów](media/alerts-overview/Azure-Monitor-Alerts.svg)

Reguły alertów są oddzielone od alertów i akcji podejmowanych podczas uruchamiania alertu. Reguła alertu przechwytuje obiekt docelowy i kryteria alertów. Reguła alertu może być w stanie włączonym lub wyłączonym. Alerty uruchamiają się tylko wtedy, gdy są włączone. 

Poniżej przedstawiono kluczowe atrybuty reguły alertu:

**Zasób docelowy:** Definiuje zakres i sygnały dostępne do ostrzegania. Obiekt docelowy może być dowolnym zasobem platformy Azure. Przykładowe obiekty docelowe: maszyna wirtualna, konto magazynu, zestaw skalowania maszyny wirtualnej, obszar roboczy usługi Log Analytics lub zasób usługi Application Insights. Dla niektórych zasobów (takich jak maszyny wirtualne) można określić wiele zasobów jako miejsce docelowe reguły alertu.

**Sygnał:** Emitowany przez zasób docelowy. Sygnały mogą być następujących typów: metryki, dziennik aktywności, usługa Application Insights i dziennik.

**Kryteria:** Kombinacja sygnału i logiki zastosowana na zasób docelowy. Przykłady: 

- Procent cpu > 70%
- Czas odpowiedzi serwera > 4 ms 
- Liczba wyników kwerendy dziennika > 100

**Nazwa alertu:** Określona nazwa reguły alertu skonfigurowana przez użytkownika.

**Opis alertu:** Opis reguły alertu skonfigurowany przez użytkownika.

**Ważność:** ważność alertu po spełnieniu kryteriów określonych w regule alertu. Ważność może wynosić od 0 do 4.

- Sev 0 = Krytyczne
- Sev 1 = Błąd
- Sev 2 = Ostrzeżenie
- Sev 3 = Informacje
- Sev 4 = Pełne 

**Akcja**: Określona akcja podjęta po uruchomieniu alertu. Aby uzyskać więcej informacji, zobacz [Grupy akcji](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Co można ostrzec na

Można alertować o metrykach i dziennikach, zgodnie z opisem w [źródłach danych monitorowania](../../azure-monitor/platform/data-sources.md). Są to między innymi następujące kwestie:

- Wartości metryk
- Zapytania przeszukiwania dzienników
- Zdarzenia dzienników aktywności
- Kondycja podstawowej platformy Azure
- Testy dostępności witryny internetowej

Wcześniej metryki usługi Azure Monitor, usługa Application Insights, usługa Log Analytics i kondycja usługi miały oddzielne funkcje alertów. Z biegiem czasu platforma Azure udoskonaliła i połączyła zarówno interfejs użytkownika, jak i różne metody alertów. Ta konsolidacja jest nadal w toku. W rezultacie nadal istnieją pewne funkcje alertów, które nie są jeszcze dostępne w nowym systemie alertów.  

| **Źródło monitora** | **Typ sygnału**  | **Opis** |
|-------------|----------------|-------------|
| Kondycja usługi | Dziennik aktywności  | Bez pomocy technicznej. Zobacz [Tworzenie alertów dziennika aktywności w powiadomieniach o usługach](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Testy dostępności sieci Web | Bez pomocy technicznej. Zobacz [Alerty testów sieci Web](../../azure-monitor/app/monitor-web-app-availability.md). Dostępne dla każdej witryny sieci Web, która jest instrumentowana do wysyłania danych do usługi Application Insights. Otrzymuj powiadomienie, gdy dostępność lub czas reakcji strony internetowej jest niższy od oczekiwań. |

## <a name="manage-alerts"></a>Zarządzanie alertami
Można ustawić stan alertu, aby określić, gdzie znajduje się w procesie rozpoznawania. Po spełnieniu kryteriów określonych w regule alertu zostanie utworzony lub zwolniony alert, który ma stan *Nowy*. Stan można zmienić podczas potwierdzania alertu i jego zamykania. Wszystkie zmiany stanu są przechowywane w historii alertu.

Obsługiwane są następujące stany alertów.

| Stan | Opis |
|:---|:---|
| Nowa | Problem został właśnie wykryty i nie został jeszcze zweryfikowany. |
| Potwierdzony | Administrator przejrzał alert i rozpoczął nad nim pracę. |
| Zamknięte | Problem został rozwiązany. Po zamknięciu alertu można go ponownie otworzyć, zmieniając go na inny stan. |

*Stan alertu* jest inny i jest niezależny od *stanu monitora*. Stan alertu jest ustawiany przez użytkownika. Stan monitora jest ustawiany przez system. Po uruchomieniu alertu stan monitora alertu jest ustawiony na *odpalenie*. Gdy podstawowy warunek, który spowodował ostrzeżenie do ognia czyści, warunek monitora jest ustawiony na *rozwiązany*. Stan alertu nie zostanie zmieniony, dopóki użytkownik go nie zmieni. Dowiedz [się, jak zmienić stan alertów i grup inteligentnych.](https://aka.ms/managing-alert-smart-group-states)

## <a name="smart-groups"></a>Grupy inteligentne 

Inteligentne grupy to agregacje alertów oparte na algorytmach uczenia maszynowego, które mogą pomóc zmniejszyć hałas alertów i pomóc w rozwiązywaniu problemów. [Dowiedz się więcej o grupach inteligentnych](https://aka.ms/smart-groups) i [zarządzaniu grupami inteligentnymi.](https://aka.ms/managing-smart-groups)


## <a name="alerts-experience"></a>Alerty 
Domyślna strona Alerty zawiera podsumowanie alertów, które są tworzone w określonym zakresie czasu. Wyświetla całkowitą liczbę alertów dla każdej ważności, z kolumnami, które identyfikują całkowitą liczbę alertów w każdym stanie dla każdego ważności. Wybierz dowolną z ważności, aby otworzyć stronę [Wszystkie alerty](#all-alerts-page) filtrowane przez tę ważność.

Alternatywnie można [programowo wyliczyć wystąpienia alertów wygenerowane w ramach subskrypcji przy użyciu interfejsów API REST.](#manage-your-alert-instances-programmatically)

> [!NOTE]
   >  Dostęp do alertów generowanych w ciągu ostatnich 30 dni można uzyskać tylko.

Nie pokazuje ani nie śledzi klasycznych alertów. Można zmienić subskrypcje lub parametry filtrowania, aby zaktualizować stronę. 

![Zrzut ekranu strony Alerty](media/alerts-overview/alerts-page.png)

Ten widok można filtrować, wybierając wartości w menu rozwijanym u góry strony.

| Kolumna | Opis |
|:---|:---|
| Subskrypcja | Wybierz subskrypcje platformy Azure, dla których chcesz wyświetlić alerty. Opcjonalnie możesz wybrać wszystkie subskrypcje. W widoku uwzględniono tylko alerty, do których masz dostęp w wybranych subskrypcjach. |
| Grupa zasobów | Wybierz jedną grupę zasobów. W widoku uwzględniane są tylko alerty z obiektami docelowymi w wybranej grupie zasobów. |
| Przedział czasu | W widoku uwzględniane są tylko alerty uruchamiane w wybranym zakresie czasu. Obsługiwane wartości to ostatnia godzina, ostatnie 24 godziny, ostatnie 7 dni i ostatnie 30 dni. |

Wybierz następujące wartości u góry strony Alerty, aby otworzyć inną stronę:

| Wartość | Opis |
|:---|:---|
| Łączna liczba alertów | Całkowita liczba alertów spełniających wybrane kryteria. Wybierz tę wartość, aby otworzyć widok Wszystkie alerty bez filtru. |
| Grupy inteligentne | Całkowita liczba grup inteligentnych utworzonych na podstawie alertów spełniających wybrane kryteria. Wybierz tę wartość, aby otworzyć listę grup inteligentnych w widoku Wszystkie alerty.
| Łączna liczba reguł alertów | Całkowita liczba reguł alertów w wybranej subskrypcji i grupie zasobów. Wybierz tę wartość, aby otworzyć widok Reguły filtrowany w wybranej subskrypcji i grupie zasobów.


## <a name="manage-alert-rules"></a>Zarządzaj regułami alertów
Aby wyświetlić stronę **Reguły,** wybierz pozycję **Zarządzaj regułami alertów**. Strona Reguły jest jednym miejscem do zarządzania wszystkimi regułami alertów w ramach subskrypcji platformy Azure. Zawiera listę wszystkich reguł alertów i może być sortowana na podstawie zasobów docelowych, grup zasobów, nazwy reguły lub stanu. Reguły alertów można również edytować, włączać lub wyłączać na tej stronie.  

 ![Zrzut ekranu przedstawiający stronę Reguły](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Tworzenie reguły alertu
Reguły alertów można tworzyć w spójny sposób, niezależnie od usługi monitorowania lub typu sygnału.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
Aby utworzyć nową regułę alertu:
1. Wybierz _obiekt docelowy_ dla alertu.
1. Wybierz _sygnał_ z dostępnych sygnałów dla celu.
1. Określ _logikę,_ która ma być stosowana do danych z sygnału.
 
Ten uproszczony proces tworzenia nie wymaga już poznania źródła monitorowania lub sygnałów, które są obsługiwane przed wybraniem zasobu platformy Azure. Lista dostępnych sygnałów jest automatycznie filtrowana na podstawie wybranego zasobu docelowego. Również na podstawie tego obiektu docelowego, są prowadzone przez definiowanie logiki reguły alertu automatycznie.  

Więcej informacji na temat tworzenia reguł alertów można tworzyć w obszarze [Tworzenie, wyświetlanie i zarządzanie alertami za pomocą usługi Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Alerty są dostępne w kilku usługach monitorowania platformy Azure. Aby uzyskać informacje o tym, jak i kiedy korzystać z każdej z tych usług, zobacz [Monitorowanie aplikacji i zasobów platformy Azure.](../../azure-monitor/overview.md) 


## <a name="all-alerts-page"></a>Strona Wszystkie alerty 
Aby wyświetlić stronę **Wszystkie alerty,** wybierz **pozycję Suma alertów**. W tym miejscu można wyświetlić listę alertów utworzonych w wybranym czasie. Można wyświetlić listę poszczególnych alertów lub listę grup inteligentnych zawierających alerty. Wybierz baner w górnej części strony, aby przełączać się między widokami.

![Zrzut ekranu przedstawiający stronę Wszystkie alerty](media/alerts-overview/all-alerts-page.png)

Widok można filtrować, zaznaczając następujące wartości w menu rozwijanym u góry strony:

| Kolumna | Opis |
|:---|:---|
| Subskrypcja | Wybierz subskrypcje platformy Azure, dla których chcesz wyświetlić alerty. Opcjonalnie możesz wybrać wszystkie subskrypcje. W widoku uwzględniono tylko alerty, do których masz dostęp w wybranych subskrypcjach. |
| Grupa zasobów | Wybierz jedną grupę zasobów. W widoku uwzględniane są tylko alerty z obiektami docelowymi w wybranej grupie zasobów. |
| Typ zasobu | Wybierz jeden lub więcej typów zasobów. W widoku uwzględniane są tylko alerty z obiektami docelowymi wybranego typu. Ta kolumna jest dostępna tylko po określeniu grupy zasobów. |
| Zasób | Wybierz zasób. Tylko alerty z tego zasobu jako miejsce docelowe są uwzględniane w widoku. Ta kolumna jest dostępna tylko po określeniu typu zasobu. |
| Ważność | Wybierz ważność alertu lub wybierz **opcję Wszystkie,** aby uwzględnić alerty o wszystkich ważnościach. |
| Stan monitora | Wybierz warunek monitora lub wybierz **opcję Wszystkie,** aby uwzględnić alerty wszystkich warunków. |
| Stan alertu | Wybierz stan alertu lub wybierz **opcję Wszystkie,** aby uwzględnić alerty wszystkich stanów. |
| Usługa monitorowania | Wybierz usługę lub wybierz **pozycję Wszystkie,** aby uwzględnić wszystkie usługi. Uwzględniane są tylko alerty utworzone przez reguły korzystające z usługi jako obiektu docelowego. |
| Przedział czasu | W widoku uwzględniane są tylko alerty uruchamiane w wybranym zakresie czasu. Obsługiwane wartości to ostatnia godzina, ostatnie 24 godziny, ostatnie 7 dni i ostatnie 30 dni. |

Wybierz **pozycję Kolumny** u góry strony, aby wybrać kolumny, które mają być wyświetlane. 

## <a name="alert-details-page"></a>Strona szczegółów alertu
Po wybraniu alertu ta strona zawiera szczegóły alertu i umożliwia zmianę jego stanu.

![Zrzut ekranu przedstawiający stronę Szczegółów alertu](media/alerts-overview/alert-detail2.png)

Strona Szczegóły alertu zawiera następujące sekcje:

| Sekcja | Opis |
|:---|:---|
| Podsumowanie | Wyświetla właściwości i inne istotne informacje o alertie. |
| Historia | Wyświetla listę każdej akcji podjętej przez alert i wszelkich zmian wprowadzonych w alertie. Obecnie ograniczone do zmian stanu. |
| Diagnostyka | Informacje o grupie inteligentnej, w której znajduje się alert. *Liczba alertów* odnosi się do liczby alertów, które są zawarte w grupie inteligentnej. Zawiera inne alerty w tej samej grupie inteligentnej, które zostały utworzone w ciągu ostatnich 30 dni, niezależnie od filtru czasu na stronie listy alertów. Wybierz alert, aby wyświetlić jego szczegóły. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Kontrola dostępu oparta na rolach (RBAC) dla wystąpień alertów

Zużycie i zarządzanie wystąpieniami alertów wymaga, aby użytkownik miał wbudowane role RBAC [albo monitorowania współautora](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) lub [monitorowania czytnika](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader). Te role są obsługiwane w dowolnym zakresie usługi Azure Resource Manager, od poziomu subskrypcji do szczegółowych przypisań na poziomie zasobu. Na przykład, jeśli użytkownik ma tylko monitorowanie `ContosoVM1`dostępu współautora dla maszyny wirtualnej, użytkownik może korzystać i zarządzać tylko alerty generowane na `ContosoVM1`.

## <a name="manage-your-alert-instances-programmatically"></a>Programowo zarządzanie wystąpieniami alertów

Można podjąć kwerendy programowo dla alertów generowanych w ramach subskrypcji. Może to być utworzenie widoków niestandardowych poza witryną Azure portal lub analizowanie alertów w celu zidentyfikowania wzorców i trendów.

Można wyszukiwać alerty generowane w ramach subskrypcji przy użyciu [interfejsu API REST zarządzania alertami](https://aka.ms/alert-management-api) lub przy użyciu [programu Azure Resource Graph](../../governance/resource-graph/overview.md) i [interfejsu API REST dla zasobów.](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)

Interfejs API REST wykresu zasobów dla zasobów umożliwia wykonywanie zapytań o wystąpienia alertów na dużą skalę. Jest to zalecane, gdy trzeba zarządzać alertami generowanymi w wielu subskrypcjach. 

Następujące przykładowe żądanie do interfejsu API REST wykresu zasobów zwraca liczbę alertów w ramach jednej subskrypcji:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

Wynik tej kwerendy wykresu zasobów można również zobaczyć w portalu za pomocą Eksploratora wykresu zasobów platformy Azure: [portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

Można wysyłać zapytania do alertów dla ich [podstawowych](alerts-common-schema-definitions.md#essentials) pól.

Użyj [interfejsu API REST zarządzania alertami,](https://aka.ms/alert-management-api) aby uzyskać więcej informacji na temat określonych alertów, w tym ich pól [kontekstu alertów.](alerts-common-schema-definitions.md#alert-context)

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o grupach inteligentnych](https://aka.ms/smart-groups)
- [Dowiedz się więcej o grupach akcji](../../azure-monitor/platform/action-groups.md)
- [Zarządzanie wystąpieniami alertów na platformie Azure](https://aka.ms/managing-alert-instances)
- [Zarządzanie grupami inteligentnymi](https://aka.ms/managing-smart-groups)
- [Dowiedz się więcej o cenach alertów platformy Azure](https://azure.microsoft.com/pricing/details/monitor/)







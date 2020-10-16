---
title: Omówienie alertów i monitorowania powiadomień na platformie Azure
description: Przegląd alertów na platformie Azure. Alerty, alerty klasyczne i interfejs alertów.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: e71f048a0a96323552b426663a235ed66fa2ef87
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108800"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Przegląd alertów na platformie Microsoft Azure 

W tym artykule opisano alerty, ich zalety oraz sposób rozpoczynania korzystania z nich.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Co to są alerty w Microsoft Azure?

Alerty z wyprzedzeniem powiadamiają Cię o znalezieniu problemów z infrastrukturą lub aplikacją przy użyciu danych monitorowania w Azure Monitor. Umożliwiają identyfikowanie i rozwiązywanie problemów przed zapisaniem ich przez użytkowników systemu. 

## <a name="overview"></a>Omówienie

Poniższy diagram przedstawia przepływ alertów. 

![Diagram przepływu alertu](media/alerts-overview/Azure-Monitor-Alerts.svg)

Reguły alertów są oddzielone od alertów i akcji podejmowanych podczas uruchamiania alertu. Reguła alertu przechwytuje cel i kryteria alertów. Reguła alertu może być w stanie włączony lub wyłączony. Alerty są wyzwalane tylko wtedy, gdy są włączone. 

Poniżej przedstawiono kluczowe atrybuty reguły alertu:

**Zasób docelowy** — definiuje zakres i sygnały dostępne dla alertów. Obiektem docelowym może być dowolny zasób platformy Azure. Przykładowe elementy docelowe:

- Maszyny wirtualne.
- Konta magazynu.
- Log Analytics obszar roboczy.
- Application Insights. 

W przypadku niektórych zasobów (takich jak maszyny wirtualne) można określić wiele zasobów jako obiekt docelowy reguły alertu.

**Sygnał** emitowany przez zasób docelowy. Sygnały mogą być następujących typów: Metryka, dziennik aktywności, Application Insights i dziennik.

**Kryteria** — Kombinacja sygnałów i logiki zastosowana w zasobie docelowym. Przykłady: 

- Procent > procesora CPU 70%
- Czas odpowiedzi serwera > 4 MS 
- Liczba wyników zapytania dziennika > 100

**Nazwa alertu** — nazwa określona dla reguły alertu skonfigurowanej przez użytkownika.

**Opis alertu** — opis reguły alertu skonfigurowanej przez użytkownika.

**Ważność** — ważność alertu po spełnieniu kryteriów określonych w regule alertu. Ważność może być z zakresu od 0 do 4.

- Ważność 0 = krytyczny
- Ważność 1 = błąd
- Ważność 2 = ostrzeżenie
- Ważność 3 = informacyjny
- Ważność 4 = pełne 

**Akcja** — określona Akcja podejmowana po wyzwoleniu alertu. Aby uzyskać więcej informacji, zobacz [grupy akcji](./action-groups.md).

## <a name="what-you-can-alert-on"></a>Co można ostrzec

Można generować alerty dotyczące metryk i dzienników, zgodnie z opisem w temacie [monitorowanie źródeł danych](./data-sources.md). Sygnały obejmują m.in.:

- Wartości metryk
- Zapytania przeszukiwania dzienników
- Zdarzenia dzienników aktywności
- Kondycja podstawowej platformy Azure
- Testy dostępności witryny internetowej

## <a name="manage-alerts"></a>Zarządzanie alertami

Można ustawić stan alertu, aby określić, gdzie znajduje się w procesie rozwiązywania. Po spełnieniu kryteriów określonych w regule alertu jest tworzony lub uruchamiany alert, który ma stan *Nowy*. Stan można zmienić po potwierdzeniu alertu i po jego zamknięciu. Wszystkie zmiany stanu są przechowywane w historii alertu.

Obsługiwane są następujące stany alertów.

| Stan | Opis |
|:---|:---|
| Nowy | Problem został wykryty i jeszcze nie został zweryfikowany. |
| Potwierdzony | Administrator sprawdził alert i rozpoczął jego pracę. |
| Zamknięte | Problem został rozwiązany. Po zamknięciu alertu można go otworzyć ponownie, zmieniając go na inny stan. |

*Stan alertu* jest różny i niezależny od *warunku monitora*. Stan alertu jest ustawiany przez użytkownika. Warunek monitora jest ustawiany przez system. Po uruchomieniu alertu warunek monitora alertu jest ustawiony na *"wyzwolone"*, a gdy podstawowy warunek, który spowodował wyczyszczenie alertu, ma stan " *rozwiązany"*. 

Stan alertu nie jest zmieniany, dopóki użytkownik nie zmieni go. Dowiedz się [, jak zmienić stan alertów i grup inteligentnych](./alerts-managing-alert-states.md?toc=%252fazure%252fazure-monitor%252ftoc.json).

## <a name="alerts-experience"></a>Środowisko alertów 
Domyślna strona alerty zawiera podsumowanie alertów, które są tworzone w określonym zakresie czasu. Wyświetla łączną liczbę alertów dla każdej ważności, z kolumnami, które identyfikują sumę alertów w każdym stanie dla każdej ważności. Wybierz dowolną z serwerów, aby otworzyć stronę [wszystkie alerty](#all-alerts-page) odfiltrowaną o tej ważności.

Zamiast tego można [programowo wyliczyć wystąpienia alertów generowanych w ramach subskrypcji za pomocą interfejsów API REST](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Można uzyskać dostęp tylko do alertów wygenerowanych w ciągu ostatnich 30 dni.

Nie pokazuje ani nie śledzi klasycznego alertu. Można zmienić subskrypcje lub parametry filtru, aby zaktualizować stronę. 

![Zrzut ekranu przedstawiający stronę alertów](media/alerts-overview/alerts-page.png)

Możesz filtrować ten widok, wybierając wartości z menu rozwijanego w górnej części strony.

| Kolumna | Opis |
|:---|:---|
| Subskrypcja | Wybierz subskrypcje platformy Azure, dla których chcesz wyświetlić alerty. Opcjonalnie możesz wybrać wszystkie subskrypcje. W widoku są uwzględniane tylko alerty, do których masz dostęp w wybranych subskrypcjach. |
| Grupa zasobów | Wybierz pojedynczą grupę zasobów. W widoku są uwzględniane tylko alerty z obiektami docelowymi w wybranej grupie zasobów. |
| Przedział czasu | W widoku są uwzględniane tylko alerty wywoływane w wybranym zakresie czasu. Obsługiwane wartości to Ostatnia godzina, ostatnie 24 godziny, ostatnie 7 dni i ostatnie 30 dni. |

Wybierz następujące wartości w górnej części strony alerty, aby otworzyć kolejną stronę:

| Wartość | Opis |
|:---|:---|
| Łączna liczba alertów | Całkowita liczba alertów spełniających wybrane kryteria. Wybierz tę wartość, aby otworzyć widok wszystkie alerty bez filtrowania. |
| Grupy inteligentne | Całkowita liczba grup inteligentnych, które zostały utworzone na podstawie alertów spełniających wybrane kryteria. Wybierz tę wartość, aby otworzyć listę grup inteligentnych w widoku wszystkie alerty.
| Łączna liczba reguł alertów | Łączna liczba reguł alertów w wybranej subskrypcji i grupie zasobów. Wybierz tę wartość, aby otworzyć widok reguły przefiltrowanych według wybranej subskrypcji i grupy zasobów.


## <a name="manage-alert-rules"></a>Zarządzaj regułami alertów
Aby wyświetlić stronę **reguły** , wybierz pozycję **Zarządzaj regułami alertów**. Strona reguły jest pojedynczym miejscem do zarządzania wszystkimi regułami alertów w ramach subskrypcji platformy Azure. Wyświetla listę wszystkich reguł alertów i można ją sortować na podstawie zasobów docelowych, grup zasobów, nazwy reguły lub stanu. Na tej stronie można także edytować, włączać lub wyłączać reguły alertów.  

 ![Zrzut ekranu przedstawiający stronę reguł](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Tworzenie reguły alertu
Reguły alertów można tworzyć w spójny sposób, niezależnie od usługi monitorowania lub typu sygnału.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
Oto jak utworzyć nową regułę alertu:
1. Wybierz _element docelowy_ dla alertu.
1. Wybierz _sygnał_ z dostępnych sygnałów dla elementu docelowego.
1. Określ _logikę_ , która ma zostać zastosowana do danych ze sygnału.

Ten uproszczony proces tworzenia nie wymaga już znajomości źródła monitorowania lub sygnałów, które są obsługiwane przed wybraniem zasobów platformy Azure. Lista dostępnych sygnałów jest automatycznie filtrowana na podstawie wybranych zasobów docelowych. W oparciu o ten obiekt docelowy prowadzi się również do automatycznego definiowania logiki reguły alertu.  

Więcej informacji o sposobie tworzenia reguł alertów można znaleźć w temacie [Tworzenie i wyświetlanie alertów oraz zarządzanie nimi przy użyciu Azure monitor](./alerts-metric.md).

Alerty są dostępne w ramach kilku usług monitorowania platformy Azure. Informacje o tym, jak i kiedy należy używać poszczególnych usług, można znaleźć w temacie [monitorowanie aplikacji i zasobów platformy Azure](../overview.md). 


## <a name="all-alerts-page"></a>Strona wszystkie alerty 
Aby wyświetlić stronę **wszystkie alerty** , wybierz pozycję **Podsumowanie alertów**. W tym miejscu można wyświetlić listę alertów utworzonych w wybranym czasie. Można wyświetlić listę indywidualnych alertów lub listę grup inteligentnych, które zawierają alerty. Wybierz transparent w górnej części strony, aby przełączać się między widokami.

![Zrzut ekranu przedstawiający stronę wszystkie alerty](media/alerts-overview/all-alerts-page.png)

Widok można filtrować, wybierając następujące wartości z menu rozwijanego w górnej części strony:

| Kolumna | Opis |
|:---|:---|
| Subskrypcja | Wybierz subskrypcje platformy Azure, dla których chcesz wyświetlić alerty. Opcjonalnie możesz wybrać wszystkie subskrypcje. W widoku są uwzględniane tylko alerty, do których masz dostęp w wybranych subskrypcjach. |
| Grupa zasobów | Wybierz pojedynczą grupę zasobów. W widoku są uwzględniane tylko alerty z obiektami docelowymi w wybranej grupie zasobów. |
| Typ zasobu | Wybierz co najmniej jeden typ zasobu. W widoku są uwzględniane tylko alerty z obiektami docelowymi wybranego typu. Ta kolumna jest dostępna tylko po określeniu grupy zasobów. |
| Zasób | Wybierz zasób. W widoku są uwzględniane tylko alerty z tym zasobem. Ta kolumna jest dostępna tylko po określeniu typu zasobu. |
| Ważność | Wybierz ważność alertu lub wybierz pozycję **wszystkie** , aby uwzględnić alerty wszystkich serwerów. |
| Warunek monitorowania | Wybierz warunek monitorowania lub wybierz pozycję **wszystkie** , aby uwzględnić alerty wszystkich warunków. |
| Stan alertu | Wybierz stan alertu lub wybierz pozycję **wszystkie** , aby uwzględnić alerty wszystkich stanów. |
| Monitorowanie usługi | Wybierz usługę lub wybierz pozycję **wszystkie** , aby uwzględnić wszystkie usługi. Uwzględniane są tylko alerty utworzone przez reguły korzystające z usługi jako celu. |
| Przedział czasu | W widoku są uwzględniane tylko alerty wywoływane w wybranym zakresie czasu. Obsługiwane wartości to Ostatnia godzina, ostatnie 24 godziny, ostatnie 7 dni i ostatnie 30 dni. |

Zaznacz **kolumny** w górnej części strony, aby wybrać kolumny, które mają być wyświetlane. 

## <a name="alert-details-page"></a>Strona szczegółów alertu
Po wybraniu alertu ta strona zawiera szczegółowe informacje dotyczące alertu i umożliwia zmianę jego stanu.

![Zrzut ekranu przedstawiający stronę szczegółów alertu](media/alerts-overview/alert-detail2.png)

Strona szczegóły alertu zawiera następujące sekcje:

| Sekcja | Opis |
|:---|:---|
| Podsumowanie | Wyświetla właściwości i inne istotne informacje dotyczące alertu. |
| Historia | Wyświetla listę wszystkich akcji podejmowanych przez alert oraz wszelkich zmian wprowadzonych w alercie. Obecnie ograniczone do zmian stanu. |
| Diagnostyka | Informacje o grupie inteligentnej, w której uwzględniony jest alert. *Liczba alertów* odnosi się do liczby alertów uwzględnionych w grupie inteligentnej. Zawiera inne alerty w tej samej grupie inteligentnej, które zostały utworzone w ciągu ostatnich 30 dni, niezależnie od filtru czasu na stronie listy alertów. Wybierz Alert, aby wyświetlić jego szczegóły. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Kontrola dostępu oparta na rolach (RBAC) dla wystąpień alertów

Użycie i Zarządzanie wystąpieniami alertów wymaga, aby użytkownik miał wbudowaną rolę platformy Azure dla [współautora](../../role-based-access-control/built-in-roles.md#monitoring-contributor) lub [czytnika monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader). Role te są obsługiwane w dowolnym zakresie Azure Resource Manager od poziomu subskrypcji do szczegółowych przypisań na poziomie zasobów. Jeśli na przykład użytkownik ma tylko kontrolę dostępu współautora dla maszyny wirtualnej `ContosoVM1` , może używać tylko alertów, które zostały wygenerowane `ContosoVM1` .

## <a name="manage-your-alert-instances-programmatically"></a>Programowe Zarządzanie wystąpieniami alertów

Możesz chcieć programowo wykonywać zapytania dotyczące alertów generowanych w ramach subskrypcji. Zapytania mogą polegać na tworzeniu niestandardowych widoków poza Azure Portal lub do analizowania alertów w celu identyfikowania wzorców i trendów.

Możesz wykonywać zapytania dotyczące alertów generowanych w ramach subskrypcji za pomocą [interfejsu API REST usługi alert Management](/rest/api/monitor/alertsmanagement/alerts) lub przy użyciu [grafu zasobów platformy Azure](../../governance/resource-graph/overview.md) i [interfejsu API REST dla zasobów](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources).

Interfejs API REST grafu zasobów dla zasobów umożliwia wykonywanie zapytań o wystąpienia alertów w odpowiedniej skali. Wykres zasobów jest zalecany, gdy konieczne jest zarządzanie alertami wygenerowanymi w wielu subskrypcjach. 

Następujące przykładowe żądanie do interfejsu API REST grafu zasobów zwraca liczbę alertów w ramach jednej subskrypcji:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

Wyniki tego zapytania dotyczącego wykresu zasobów można także zobaczyć w portalu przy użyciu Eksploratora Azure Resource Graph: [Portal.Azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

W celu uzyskania [odpowiednich pól można](alerts-common-schema-definitions.md#essentials) wysyłać zapytania do alertów.

Użyj [interfejsu API REST alert Management](/rest/api/monitor/alertsmanagement/alerts) , aby uzyskać więcej informacji na temat określonych alertów, w tym ich pól [kontekstu alertu](alerts-common-schema-definitions.md#alert-context) .

## <a name="smart-groups"></a>Grupy inteligentne

Grupy inteligentne to agregacja alertów w oparciu o algorytmy uczenia maszynowego, co może pomóc w zmniejszeniu szumu i pomocy w rozwiązywaniu problemów. [Dowiedz się więcej o grupach inteligentnych](./alerts-smartgroups-overview.md?toc=%252fazure%252fazure-monitor%252ftoc.json) i [sposobach zarządzania grupami inteligentnymi](./alerts-managing-smart-groups.md?toc=%252fazure%252fazure-monitor%252ftoc.json).

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o grupach inteligentnych](./alerts-smartgroups-overview.md?toc=%252fazure%252fazure-monitor%252ftoc.json)
- [Więcej informacji na temat grup akcji](./action-groups.md)
- [Zarządzanie wystąpieniami alertów na platformie Azure](./alerts-managing-alert-instances.md?toc=%252fazure%252fazure-monitor%252ftoc.json)
- [Zarządzanie grupami inteligentnymi](./alerts-managing-smart-groups.md?toc=%252fazure%252fazure-monitor%252ftoc.json)
- [Dowiedz się więcej o cenniku usługi Azure Alerts](https://azure.microsoft.com/pricing/details/monitor/)
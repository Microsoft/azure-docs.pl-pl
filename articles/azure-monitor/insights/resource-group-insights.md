---
title: Azure Monitor szczegółowych informacji o grupie zasobów | Microsoft Docs
description: Zapoznaj się z kondycją i wydajnością rozproszonych aplikacji i usług na poziomie grupy zasobów przy użyciu Azure Monitor
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ab2c56b5e5cfc3dcb7411db27e7faabb2589be6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704331"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Monitorowanie grup zasobów przy użyciu Azure Monitor (wersja zapoznawcza)

Nowoczesne aplikacje są często skomplikowane i wysoce dystrybuowane z wieloma dyskretnymi częściami, które współpracują ze sobą w celu świadczenia usługi. W celu rozpoznania tej złożoności Azure Monitor zapewnia monitorowanie szczegółowych informacji o grupach zasobów. Dzięki temu można łatwo Klasyfikacja i zdiagnozować problemy napotykane przez poszczególne zasoby, a także w kontekście kondycji i wydajności grupy zasobów &mdash; i aplikacji &mdash; jako całości.

## <a name="access-insights-for-resource-groups"></a>Dostęp do szczegółowych informacji dla grup zasobów

1. Wybierz pozycję **grupy zasobów**  na pasku nawigacyjnym po lewej stronie.
2. Wybierz jedną z grup zasobów, które chcesz zbadać. (Jeśli wiele grup zasobów jest filtrowanych przez subskrypcję, może być czasami przydatne).
3. Aby uzyskać dostęp do usługi Insights dla grupy zasobów, kliknij pozycję **szczegółowe informacje** w menu po lewej stronie dowolnej grupy zasobów.

![Zrzut ekranu strony Przegląd szczegółowych informacji o grupach zasobów](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Zasoby z aktywnymi alertami i problemami z kondycją

Na stronie Przegląd przedstawiono liczbę alertów, które zostały wyzwolone i są nadal aktywne, a także bieżące Azure Resource Health poszczególnych zasobów. Te informacje mogą pomóc w szybkim wykorzystaniu wszystkich zasobów, w których występują problemy. Alerty pomagają w wykrywaniu problemów w kodzie oraz sposobie konfigurowania infrastruktury. Azure Resource Health powierzchnie dotyczą platformy platformy Azure, które nie są specyficzne dla poszczególnych aplikacji.

![Zrzut ekranu przedstawiający okienko Azure Resource Health](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Aby wyświetlić Azure Resource Health, zaznacz pole **pokaż Azure Resource Health** powyżej tabeli. Ta kolumna jest domyślnie ukryta, aby pomóc w szybkim załadowaniu strony.

![Zrzut ekranu przedstawiający wykres kondycji zasobów dodany](./media/resource-group-insights/0003-overview.png)

Domyślnie zasoby są pogrupowane według warstwy aplikacji i typu zasobu. **Warstwa aplikacji** jest prostą kategoryzacją typów zasobów, która istnieje tylko w kontekście strony Przegląd szczegółowych informacji o grupach zasobów. Istnieją typy zasobów związane z kodem aplikacji, infrastrukturą obliczeniową, siecią, magazynem i bazami danych. Narzędzia do zarządzania umożliwiają uzyskanie własnych warstw aplikacji, a każdy inny zasób jest kategoryzowany jako należący do **innej** warstwy aplikacji. Takie grupowanie może pomóc w szybkim wykorzystaniu, jakie podsystemy aplikacji są w dobrej kondycji.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnozuj problemy z grupą zasobów

Strona szczegółowe informacje o grupie zasobów zawiera kilka innych narzędzi objętych zakresem, które ułatwiają diagnozowanie problemów

   |         |          |
   | ---------------- |:-----|
   | [**Alerty**](../alerts/alerts-overview.md)      |  Wyświetlaj i twórz Alerty oraz zarządzaj nimi. |
   | [**Metryki**](../data-platform.md) | Wizualizowanie i Eksplorowanie danych opartych na metrykach.    |
   | [**Dzienniki aktywności**](../essentials/platform-logs-overview.md) | Zdarzenia poziomu subskrypcji, które wystąpiły na platformie Azure.  |
   | [**Mapa aplikacji**](../app/app-map.md) | Przejdź do topologii aplikacji rozproszonej, aby zidentyfikować wąskie gardła wydajności lub punkty awarii. |

## <a name="failures-and-performance"></a>Awarie i wydajność

Co w przypadku, gdy zauważysz, że aplikacja działa wolno, a użytkownicy zgłosili błędy? Jest to czasochłonne wyszukiwanie wszystkich zasobów w celu wyizolowania problemów.

Karty **wydajność** i **Błędy** upraszczają ten proces przez połączenie diagnostyki wydajności i niepowodzeń dla wielu wspólnych typów zasobów.

Większość typów zasobów otworzy galerię Azure Monitor szablonów skoroszytów. Każdy utworzony skoroszyt może być dostosowany, zapisywany, współużytkowany z zespołem i ponownie używany w przyszłości do diagnozowania podobnych problemów.

### <a name="investigate-failures"></a>Zbadaj błędy

Aby przetestować kartę błędy, wybierz pozycję **Błędy** w obszarze **Zbadaj** w menu po lewej stronie.

Po wprowadzeniu wybranej opcji pasek menu po lewej stronie zostanie zmieniony, oferując nowe opcje.

![Zrzut ekranu przedstawiający okienko przegląd niepowodzeń](./media/resource-group-insights/00004-failures.png)

Po wybraniu App Service zostanie wyświetlona Galeria Azure Monitor szablonów skoroszytów.

![Zrzut ekranu przedstawiający galerię skoroszytu aplikacji](./media/resource-group-insights/0005-failure-insights-workbook.png)

Wybranie szablonu do wglądu w szczegółowe dane spowoduje otwarcie skoroszytu.

![Zrzut ekranu przedstawiający raport o niepowodzeniu](./media/resource-group-insights/0006-failure-visual.png)

Możesz wybrać dowolny z wierszy. Wybór zostanie wyświetlony w graficznym widoku szczegółów.

![Zrzut ekranu przedstawiający szczegóły błędu](./media/resource-group-insights/0007-failure-details.png)

Skoroszyty są od razu trudne w tworzeniu niestandardowych raportów i wizualizacji w łatwym do użycia formacie. Niektórzy użytkownicy mogą chcieć tylko dostosować wstępnie skompilowane parametry. skoroszyty są w pełni dostosowywane.

Aby poznać sposób wewnętrznego działania tego skoroszytu, wybierz pozycję **Edytuj** na górnym pasku.

![Zrzut ekranu przedstawiający dodatkową opcję edycji](./media/resource-group-insights/0008-failure-edit.png)

Kilka pól **edycji** pojawia się blisko różnych elementów skoroszytu. Zaznacz pole **edycji** poniżej tabeli operacji.

![Zrzut ekranu pól edycji](./media/resource-group-insights/0009-failure-edit-graph.png)

To pokazuje bazowe zapytanie dziennika, które prowadzi do wizualizacji tabeli.

 ![Zrzut ekranu przedstawiający okno zapytania dziennika](./media/resource-group-insights/0010-failure-edit-query.png)

Zapytanie można modyfikować bezpośrednio. Można też użyć go jako odwołania i pożyczek od niego podczas projektowania własnych niestandardowych skoroszytów.

### <a name="investigate-performance"></a>Badanie wydajności

Wydajność oferuje własną galerię skoroszytów. W przypadku App Service prekompilowany skoroszyt wydajności aplikacji oferuje następujący widok:

 ![Zrzut ekranu przedstawiający widok wydajności](./media/resource-group-insights/0011-performance.png)

W takim przypadku w przypadku wybrania opcji Edytuj zobaczysz, że ten zestaw wizualizacji jest obsługiwany przez Azure Monitor metryki.

 ![Zrzut ekranu przedstawiający widok wydajności z metrykami platformy Azure](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="enabling-access-to-alerts"></a>Włączanie dostępu do alertów

Aby wyświetlić alerty w Azure Monitor dla grup zasobów, ktoś z rolą właściciela lub współautora dla tej subskrypcji musi otworzyć Azure Monitor dla grup zasobów dla dowolnej grupy zasobów w subskrypcji. Umożliwi to osobom z dostępem do odczytu wyświetlanie alertów w Azure Monitor dla grup zasobów dla wszystkich grup zasobów w subskrypcji. Jeśli masz rolę właściciela lub współautora, Odśwież Tę stronę w ciągu kilku minut.

Azure Monitor grupy zasobów bazują na systemie zarządzania alertami Azure Monitor w celu pobrania stanu alertu. Zarządzanie alertami nie jest skonfigurowane domyślnie dla każdej grupy zasobów i subskrypcji i może być włączone tylko przez kogoś z rolą właściciela lub współautora. Można ją włączyć przez:
* Otwieranie Azure Monitor dla grup zasobów dla dowolnej grupy zasobów w subskrypcji.
* Możesz też przejść do subskrypcji, kliknąć pozycję **dostawcy zasobów**, a następnie kliknąć pozycję **Zarejestruj dla alertów. Zarządzanie**.

## <a name="next-steps"></a>Następne kroki

- [Azure Monitor skoroszyty](../visualize/workbooks-overview.md)
- [Azure Resource Health](../../service-health/resource-health-overview.md)
- [Alerty usługi Azure Monitor](../alerts/alerts-overview.md)

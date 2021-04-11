---
title: Oceń wpływ nowej definicji Azure Policy
description: Zapoznaj się z procesem tworzenia nowej definicji zasad w środowisku platformy Azure.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 187a64ce3581c4aaa893e2a805ce787a0bfd6c79
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091722"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy-definition"></a>Oceń wpływ nowej definicji Azure Policy

Azure Policy to zaawansowane narzędzie do zarządzania zasobami platformy Azure z normami biznesowymi i w celu spełnienia wymagań dotyczących zgodności. Gdy użytkownicy, procesy lub potoki tworzą lub aktualizują zasoby, Azure Policy przegląda żądanie. Gdy efekt definicji zasad to [Modify](./effects.md#modify), [append](./effects.md#deny)lub [DeployIfNotExists](./effects.md#deployifnotexists), zasady zmieniają żądanie lub dodaje do niego. Gdy działanie definicji zasad ma wartość [Audit](./effects.md#audit) lub [AuditIfNotExists](./effects.md#auditifnotexists), zasady spowodują utworzenie wpisu dziennika aktywności dla nowych i zaktualizowanych zasobów. W przypadku [odmowy](./effects.md#deny)działania definicji zasad zasady zatrzymają Tworzenie lub zmianę żądania.

Te wyniki są dokładnie wymagane, gdy wiadomo, że zasady są prawidłowo zdefiniowane. Należy jednak sprawdzić, czy nowe zasady działają zgodnie z założeniami przed zezwoleniem na zmianę lub zablokowanie pracy. Sprawdzanie poprawności musi upewnić się, że tylko odpowiednie zasoby są określone jako niezgodne, a żadne zgodne zasoby nie są prawidłowo uwzględniane (znane jako _fałszywe pozytywne_) w wynikach.

Zalecanym podejściem do walidacji nowej definicji zasad jest wykonanie następujących czynności:

- Ścisłe Definiowanie zasad
- Inspekcja istniejących zasobów
- Inspekcja nowych lub zaktualizowanych żądań zasobów
- Wdrażanie zasad w zasobach
- Monitorowanie ciągłe

## <a name="tightly-define-your-policy"></a>Ścisłe Definiowanie zasad

Ważne jest, aby zrozumieć, w jaki sposób zasady biznesowe są implementowane jako definicja zasad i relacje zasobów platformy Azure z innymi usługami platformy Azure. Ten krok jest realizowany przez [zidentyfikowanie wymagań](../tutorials/create-custom-policy-definition.md#identify-requirements) i [określenie właściwości zasobów](../tutorials/create-custom-policy-definition.md#determine-resource-properties).
Jednak ważne jest również, aby zobaczyć poza wąskim definicją zasad firmy. Czy stan zasad na przykład "wszystkie Virtual Machines muszą..."? Co z innymi usługami platformy Azure, które korzystają z maszyn wirtualnych, takich jak HDInsight lub AKS? Podczas definiowania zasad należy rozważyć sposób, w jaki te zasady mają wpływ na zasoby używane przez inne usługi.

Z tego powodu definicje zasad powinny być ściśle zdefiniowane i skoncentrowane na zasobach oraz właściwości, które należy oszacować pod kątem zgodności, jak to możliwe.

## <a name="audit-existing-resources"></a>Inspekcja istniejących zasobów

Przed rozpoczęciem zarządzania nowymi lub zaktualizowanymi zasobami przy użyciu nowej definicji zasad najlepiej sprawdzić, w jaki sposób ta metoda szacuje ograniczony podzbiór istniejących zasobów, takich jak testowa Grupa zasobów. Użyj [trybu wymuszania](./assignment-structure.md#enforcement-mode) _wyłączonego_ (DoNotEnforce) w przypisaniu zasad, aby zapobiec utworzeniu [efektu](./effects.md) wyzwalacza lub wpisów dziennika aktywności.

Ten krok daje możliwość oceny wyników zgodności nowych zasad w istniejących zasobach bez wpływu na przepływ pracy. Sprawdź, czy żadne zgodne zasoby nie są oznaczone jako niezgodne (_fałszywie dodatnie_) i czy wszystkie zasoby, które powinny być niezgodne, są oznaczone jako poprawne.
Gdy początkowy podzestaw zasobów zostanie zweryfikowany zgodnie z oczekiwaniami, wolno zwiększyć ocenę do wszystkich istniejących zasobów.

Ocenianie istniejących zasobów w ten sposób umożliwia również korygowanie niezgodnych zasobów przed pełną implementacją nowych zasad. To oczyszczanie można wykonać ręcznie lub za pomocą [zadania korygowania](../how-to/remediate-resources.md) , jeśli efektem definicji zasad jest _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Inspekcja nowych lub zaktualizowanych zasobów

Po sprawdzeniu poprawności nowej definicji zasad raporty są raportowane prawidłowo w istniejących zasobach, ale zapoznaj się z wpływem zasad, gdy zasoby zostaną utworzone lub zaktualizowane. Jeśli definicja zasad obsługuje efekt parametryzacja, należy użyć [inspekcji](./effects.md#audit). Ta konfiguracja pozwala monitorować tworzenie i aktualizowanie zasobów, aby sprawdzić, czy nowa definicja zasad wyzwala wpis w dzienniku aktywności platformy Azure dla zasobu, który jest niezgodny bez wpływu na istniejące zadania lub żądania.

Zaleca się aktualizowanie i tworzenie nowych zasobów, które pasują do definicji zasad, aby sprawdzić, czy efekt _inspekcji_ jest prawidłowo wyzwalany, gdy jest oczekiwany. Należy do Lookout dla żądań zasobów, które nie powinny mieć wpływu na nową definicję zasad, która wyzwala efekt _inspekcji_ .
Te zasoby, których to dotyczy, to inny przykład _fałszywych wartości dodatnich_ i muszą zostać ustalone w definicji zasad przed pełną implementacją.

W przypadku zmiany definicji zasad na tym etapie testowania zaleca się rozpoczęcie procesu sprawdzania poprawności przy użyciu inspekcji istniejących zasobów. Zmiana definicji zasad dla _fałszywych wartości dodatnich_ dla nowych lub zaktualizowanych zasobów prawdopodobnie również ma wpływ na istniejące zasoby.

## <a name="deploy-your-policy-to-resources"></a>Wdrażanie zasad w zasobach

Po zakończeniu walidacji nowej definicji zasad z istniejącymi zasobami i nowymi lub zaktualizowanymi żądaniami zasobów rozpocznie się proces implementowania zasad. Zaleca się utworzenie przypisania zasad dla nowej definicji zasad do podzestawu najpierw wszystkich zasobów, takich jak grupa zasobów. Po zweryfikowaniu wdrożenia początkowego należy zwiększyć zakres zasad na szerszy i szerszy poziomy, takie jak subskrypcje i grupy zarządzania. To rozszerzenie jest osiągane przez usunięcie przypisania i utworzenie nowego elementu w zakresach docelowych do momentu przypisania do pełnego zakresu zasobów, które mają być objęte nową definicją zasad.

Podczas wdrażania, jeśli znajdują się zasoby, które powinny zostać wykluczone z nowej definicji zasad, należy je rozwiązać w jeden z następujących sposobów:

- Aktualizowanie definicji zasad tak, aby były bardziej jawne, aby ograniczyć niezamierzony wpływ
- Zmień zakres przydziału zasad (przez usunięcie i utworzenie nowego przypisania)
- Dodawanie grupy zasobów do listy wykluczeń dla przypisania zasad

Wszelkie zmiany zakresu (poziomu lub wykluczeń) powinny być w pełni sprawdzone i przekazywane do organizacji zabezpieczeń i zgodności, aby mieć pewność, że nie ma przerw w objęcia.

## <a name="monitor-your-policy-and-compliance"></a>Monitorowanie zasad i zgodności

Implementowanie i przypisywanie definicji zasad nie jest ostatnim krokiem. Stale Monitoruj poziom [zgodności](../how-to/get-compliance-data.md) zasobów do nowej definicji zasad i skonfiguruj odpowiednie [Azure monitor alertów i powiadomień](../../../azure-monitor/alerts/alerts-overview.md) dla niezgodnych urządzeń. Zalecane jest również, aby regularnie oszacować definicję zasad i powiązane przypisania, aby sprawdzić, czy definicja zasad spełnia wymagania zasad firmy i zgodności. Zasady powinny zostać usunięte, jeśli nie są już potrzebne. Zasady wymagają również aktualizacji od czasu do czasu, gdy podstawowe zasoby platformy Azure są rozwijane i dodają nowe właściwości i możliwości.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strukturze definicji zasad](./definition-structure.md).
- Dowiedz się więcej na temat [struktury przypisywania zasad](./assignment-structure.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
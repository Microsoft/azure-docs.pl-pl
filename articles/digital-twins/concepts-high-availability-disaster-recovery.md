---
title: Wysoka dostępność i odzyskiwanie po awarii
titleSuffix: Azure Digital Twins
description: Opisuje funkcje usługi Azure Digital bliźniaczych reprezentacji, które ułatwiają tworzenie rozwiązań usługi Azure IoT o wysokiej dostępności przy użyciu funkcji odzyskiwania po awarii.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 35f4aae246f105d832aaf92c5c5797c8a65b44f1
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938550"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digital bliźniaczych reprezentacji — wysoka dostępność i odzyskiwanie po awarii

Najważniejszym zagadnieniem dotyczącym odpornych rozwiązań IoT jest ciągłość działania i odzyskiwanie po awarii. Projektowanie pod kątem **wysokiej dostępności** i **odzyskiwania po awarii (Dr)** może pomóc zdefiniować i osiągnąć odpowiednie cele w zakresie przestoju dla rozwiązania.

W tym artykule omówiono funkcje HA i DR oferowane w ramach usługi Digital bliźniaczych reprezentacji systemu Azure.

Usługa Azure Digital bliźniaczych reprezentacji obsługuje następujące opcje funkcji:
* *Międzyregionowa ha* — wbudowana nadmiarowość, która zapewnia czas pracy usługi
* *Międzyregionowe* odzyskiwanie po awarii do regionu platformy Azure z parą geograficzną w przypadku nieoczekiwanego błędu centrum danych

Możesz również zapoznać się z sekcją [*najlepszych*](#best-practices) rozwiązań dla ogólnych wskazówek dotyczących platformy Azure dotyczących projektowania pod kątem usług ha/Dr.

## <a name="intra-region-ha"></a>W regionie HA
 
Usługa Azure Digital bliźniaczych reprezentacji zapewnia międzyregionową HA przez implementację nadmiarowości w ramach usługi. Jest to odzwierciedlone w [umowie SLA usługi](https://azure.microsoft.com/support/legal/sla/digital-twins) w celu zapewnienia przestoju. **Deweloperzy rozwiązania Digital bliźniaczych reprezentacji systemu Azure nie muszą wykonywać żadnych dodatkowych czynności, aby korzystać z tych funkcji HA.** Mimo że usługa Azure Digital bliźniaczych reprezentacji oferuje rozsądną gwarancję o wysokiej dostępności, nadal można oczekiwać przejściowych błędów, podobnie jak w przypadku dowolnej platformy przetwarzania rozproszonego. Odpowiednie zasady ponawiania powinny być wbudowane w składniki, które współdziałają z aplikacją w chmurze w celu zaspokojenia błędów przejściowych.

## <a name="cross-region-dr"></a>Międzyregionowe odzyskiwanie po awarii

Zdarza się sytuacje, w których wystąpiły przerwy w działaniu centrum danych z powodu awarii lub innych zdarzeń w regionie. Takie zdarzenia są rzadkimi wystąpieniami, a w przypadku takich niepowodzeń funkcja HA o dużej okolicy opisana powyżej może nie pomóc. Usługa Azure Digital bliźniaczych reprezentacji rozwiązuje ten adres z zainicjowaną przez firmę Microsoft trybem failover.

**Zainicjowane przez firmę Microsoft tryb failover** jest realizowane przez firmę Microsoft w rzadkich sytuacjach, gdy wszystkie wystąpienia usługi Azure Digital bliźniaczych reprezentacji są wykonywane w trybie failover z regionu, którego to dotyczy, do odpowiadającego im regionu geograficznego. Ten proces jest opcją domyślną (bez możliwości wyboru użytkowników) i nie wymaga interwencji użytkownika. Firma Microsoft zastrzega sobie prawo do określenia, kiedy ta opcja zostanie zrealizowana. Ten mechanizm nie obejmuje zgody użytkownika, zanim wystąpienie użytkownika zostanie przełączone w tryb failover.

>[!NOTE]
> Niektóre usługi platformy Azure udostępniają również dodatkową opcję o nazwie **zainicjowanej przez klienta w trybie failover**, która umożliwia klientom inicjowanie pracy awaryjnej dla ich wystąpienia, na przykład w celu uruchomienia testowania odzyskiwania po awarii. Ten mechanizm nie jest obecnie **obsługiwany** przez usługę Azure Digital bliźniaczych reprezentacji. 

## <a name="monitor-service-health"></a>Monitorowanie kondycji usługi

Ponieważ wystąpienia usługi Azure Digital bliźniaczych reprezentacji są w trybie failover i zostały odzyskane, można monitorować ten proces za pomocą narzędzia [Azure Service Health](https://docs.microsoft.com/azure/service-health/service-health-overview) . Service Health śledzi kondycję usług platformy Azure w różnych regionach i subskrypcjach, a następnie udostępnia komunikację wpływającą na usługę na temat przestoju i przerw w działaniu.

Podczas pracy w trybie failover Service Health może wskazywać, kiedy usługa nie działa, i po utworzeniu kopii zapasowej.

Aby wyświetlić zdarzenia Service Health...
1. Przejdź do [Service Health](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) w Azure Portal (możesz użyć tego linku lub wyszukać go, korzystając z paska wyszukiwania portalu).
1. Użyj menu po lewej stronie, aby przejść do strony *historia kondycji* .
1. Znajdź *nazwę problemu* rozpoczynającą się od **usługi Azure Digital bliźniaczych reprezentacji** i wybierz ją.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="Zrzut ekranu przedstawiający Azure Portal wyświetlenia strony historia kondycji. Istnieje kilka problemów występujących w ciągu ostatnich kilku dni, a problem o nazwie &quot;Azure Digital bliźniaczych reprezentacji — Europa Zachodnia —&quot; z ograniczeniami &quot;został wyróżniony." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. Aby uzyskać ogólne informacje o awarii, Wyświetl kartę *Podsumowanie* .

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="Na stronie Historia kondycji zostanie wyróżniona karta Podsumowanie. Na karcie wyświetlane są ogólne informacje, takie jak zasób, którego dotyczyły, jego region i jego subskrypcja." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. Aby uzyskać więcej informacji i aktualizacji dotyczących problemu w czasie, Wyświetl kartę *aktualizacje problemów* .

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="Na stronie Historia kondycji zostanie wyróżniona karta aktualizacje problemów. Na karcie wyświetlane są kilka wpisów przedstawiających bieżący stan z dnia temu." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


Należy zauważyć, że informacje wyświetlane w tym narzędziu nie są specyficzne dla jednego wystąpienia cyfrowego platformy Azure. Po użyciu Service Health, aby zrozumieć, co dzieje się z usługą Azure Digital bliźniaczych reprezentacji w określonym regionie lub w ramach subskrypcji, możesz wykonać dalsze czynności monitorujące za pomocą [Narzędzia Kondycja zasobów](troubleshoot-resource-health.md) , aby przejść do szczegółów określonych wystąpień i sprawdzić, czy mają one wpływ.

## <a name="best-practices"></a>Najlepsze rozwiązania

Aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi usług HA/DR, zobacz następujące wskazówki dotyczące platformy Azure w tym temacie: 
* Artykuł [*Wskazówki techniczne dotyczące ciągłości działania platformy Azure*](/azure/architecture/framework/resiliency/overview) zawiera opis ogólnej struktury, która pomaga w rozważaniu ciągłości działalności biznesowej i odzyskiwania po awarii. 
* Dokument dotyczący [*odzyskiwania po awarii i wysokiej dostępności dla aplikacji platformy Azure*](/azure/architecture/framework/resiliency/backup-and-recovery) zawiera wskazówki dotyczące architektury dla aplikacji platformy Azure w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii (Dr).

## <a name="next-steps"></a>Następne kroki 

Przeczytaj więcej na temat rozpoczynania pracy z rozwiązaniami Digital bliźniaczych reprezentacji systemu Azure:
 
* [*Co to jest usługa Azure Digital Twins?*](overview.md)
* [*Szybki Start: Eksplorowanie przykładowego scenariusza*](quickstart-adt-explorer.md)
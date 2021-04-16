---
title: Wysoka dostępność i odzyskiwanie po awarii
titleSuffix: Azure Digital Twins
description: Opisuje funkcje platformy Azure Azure Digital Twins, które ułatwiają tworzenie rozwiązań Azure IoT o wysokiej dostępie z możliwościami odzyskiwania po awarii.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 41edef58910fe2b772831ef083e5aca8bb52a321
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482272"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digital Twins wysokiej dostępności i odzyskiwania po awarii

Kluczowym obszarem, który należy wziąć pod uwagę w przypadku odpornych rozwiązań IoT, jest ciągłość działalności biznesowej i odzyskiwanie po awarii. Projektowanie pod **potrzeby wysokiej dostępności (HA)** i odzyskiwania po awarii **(DR)** może pomóc w zdefiniowaniu i osiągnięciu odpowiednich celów czasu pracy dla rozwiązania.

W tym artykule omówiono funkcje ha i dr oferowane specjalnie przez Azure Digital Twins usługi.

Azure Digital Twins obsługuje następujące opcje funkcji:
* *Ha Ha (Ha)* wewnątrz regionu — wbudowana nadmiarowość zapewniająca czas pracy usługi
* *Przetwarzanie po awarii między* regionami — tryb failover do sparowanego geograficznie regionu świadczenia usługi Azure w przypadku nieoczekiwanej awarii centrum danych

Zapoznaj się również z [*sekcją Najlepsze rozwiązania,*](#best-practices) aby uzyskać ogólne wskazówki dotyczące projektowania rozwiązań ha/DR na platformie Azure.

## <a name="intra-region-ha"></a>Ha ha wewnątrz regionu
 
Azure Digital Twins zapewnia ha ha w obrębie regionu przez zaimplementowanie nadmiarowości w usłudze. Jest to odzwierciedlone w umowy [SLA usługi](https://azure.microsoft.com/support/legal/sla/digital-twins) dotyczącej czasu pracy. **Deweloperzy rozwiązania z dostępem do Azure Digital Twins nie muszą nic więcej pracować.** Mimo Azure Digital Twins zapewnia względnie wysoką gwarancję czasu pracy, nadal można oczekiwać przejściowych awarii, podobnie jak w przypadku dowolnej rozproszonej platformy obliczeniowej. Odpowiednie zasady ponawiania powinny być wbudowane w składniki współdziałające z aplikacją w chmurze w celu radzenia sobie z przejściowymi błędami.

## <a name="cross-region-dr"></a>Między regionami : dr

Mogą wystąpić rzadkie sytuacje, w których w centrum danych występują dłuższe awarie spowodowane awariami zasilania lub innymi zdarzeniami w regionie. Takie zdarzenia występują rzadko, a w przypadku takich awarii opisana powyżej funkcja ha ha w regionie wewnątrz regionu może nie być pomocna. Azure Digital Twins rozwiązać ten problem w przypadku trybu failover zainicjowanego przez firmę Microsoft.

**Inicjowane przez firmę Microsoft** tryb failover jest wykonywane przez firmę Microsoft w rzadkich sytuacjach w celu trybu failover wszystkich wystąpień Azure Digital Twins z regionu, którego dotyczy problem, do odpowiedniego sparowanego geograficznie regionu. Ten proces jest opcją domyślną (bez możliwości rezygnacji przez użytkowników) i nie wymaga interwencji użytkownika. Firma Microsoft zastrzega sobie prawo do ustalenia, kiedy ta opcja będzie dostępna. Ten mechanizm nie obejmuje zgody użytkownika przed rozpoczęciem pracy wystąpienia użytkownika w trybu fail over.

>[!NOTE]
> Niektóre usługi platformy Azure oferują również dodatkową opcję o nazwie trybu failover zainicjowaną przez klienta, która umożliwia klientom zainicjowanie trybu **failover** tylko dla swojego wystąpienia, na przykład uruchomienie awarii po awarii. Ten mechanizm nie **jest obecnie obsługiwany przez** Azure Digital Twins. 

## <a name="monitor-service-health"></a>Monitorowanie kondycji usługi

Ponieważ Azure Digital Twins są w awarii i odzyskiwane, można monitorować proces przy użyciu [Azure Service Health](../service-health/service-health-overview.md) danych. Service Health śledzi kondycję usług platformy Azure w różnych regionach i subskrypcjach oraz udostępnia komunikaty wpływające na usługę dotyczące przestojów i przestojów.

Podczas zdarzenia trybu failover usługa Service Health wskazanie, kiedy usługa nie istnieje i kiedy jest wróci do kopii zapasowej.

Aby wyświetlić Service Health zdarzeń...
1. Przejdź do [Service Health](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) na pasku Azure Portal (możesz użyć tego linku lub wyszukać go przy użyciu paska wyszukiwania portalu).
1. Użyj menu po lewej stronie, aby przełączyć się na *stronę Historia* kondycji.
1. Poszukaj nazwy problemu *rozpoczynającej się* **od Azure Digital Twins** i wybierz ją.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="Zrzut ekranu przedstawiający Azure Portal stronę Historia kondycji. Istnieje lista kilku problemów z ostatnich kilku dni, a problem o nazwie &quot;Azure Digital Twins — Europa Zachodnia — skorygowane&quot; został wyróżniony." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. Aby uzyskać ogólne informacje o omdliwu, wyświetl *kartę* Podsumowanie.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="Na stronie Historia kondycji wyróżniona jest karta Podsumowanie. Karta zawiera ogólne informacje, takie jak zasób, którego dotyczy problem, jego region i subskrypcję." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. Aby uzyskać więcej informacji i aktualizacji dotyczących problemu w czasie, wyświetl *kartę Aktualizacje* problemu.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="Na stronie Historia kondycji wyróżniona jest karta Aktualizacje problemu. Na karcie zostanie wyświetlonych kilka wpisów przedstawiających bieżący stan z dnia temu." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


Należy pamiętać, że informacje wyświetlane w tym narzędziu nie są specyficzne dla jednego wystąpienia usługi Azure Digital. Po użyciu usługi Service Health, aby zrozumieć, co dzieje się z usługą Azure Digital Twins w określonym regionie [](troubleshoot-resource-health.md) lub subskrypcji, możesz zrobić krok dalej, korzystając z narzędzia Kondycja zasobów, aby przejść do szczegółów określonych wystąpień i sprawdzić, czy ma to wpływ.

## <a name="best-practices"></a>Najlepsze rozwiązania

Aby uzyskać najlepsze rozwiązania dotyczące ha/dr, zobacz następujące wskazówki dotyczące platformy Azure w tym temacie: 
* W artykule [*Azure Business Continuity Technical Guidance (Wskazówki*](/azure/architecture/framework/resiliency/overview) techniczne dotyczące ciągłości działania platformy Azure) opisano ogólną platformę, która pomaga myśleć o ciągłości działania i odzyskiwaniu po awarii. 
* Dokument [*Odzyskiwanie po awarii i wysoka*](/azure/architecture/framework/resiliency/backup-and-recovery) dostępność dla aplikacji platformy Azure zawiera wskazówki dotyczące architektury strategii dla aplikacji platformy Azure w celu osiągnięcia wysokiej dostępności i odzyskiwania po awarii.

## <a name="next-steps"></a>Następne kroki 

Przeczytaj więcej na temat rozpoczynania pracy z Azure Digital Twins rozwiązaniami:
 
* [*Co to jest usługa Azure Digital Twins?*](overview.md)
* [*Szybki start: eksplorowanie przykładowego scenariusza*](quickstart-azure-digital-twins-explorer.md)
---
title: Ulepszanie operacyjnego Excellency dla subskrypcji platformy Azure za pomocą Azure Advisor
description: Użyj usługi Advisor, aby zoptymalizować i uzyskać dojrzałość operacyjną dla subskrypcji platformy Azure
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 4133fbe40c0fc1bdaf9c3f734bc19f94437c9acb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187420"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Osiągaj doskonałości operacyjnej dzięki Azure Advisor

Azure Advisor zalecenia dotyczące doskonałości operacyjnej pomagają klientom z wydajnością procesów i przepływów pracy, możliwościach zarządzania zasobami i wdrażania. Te zalecenia można uzyskać od klasyfikatora na karcie **doskonałości działania** na pulpicie nawigacyjnym usługi Advisor.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Utwórz alerty Azure Service Health, aby otrzymywać powiadomienia o problemach z platformą Azure

Zalecamy skonfigurowanie alertów Azure Service Health, aby otrzymywać powiadomienia o problemach z usługą platformy Azure. [Azure Service Health](https://azure.microsoft.com/features/service-health/) to bezpłatna usługa, która zapewnia spersonalizowane wskazówki i pomoc techniczną, gdy ma to wpływ na problem z usługą platformy Azure. Klasyfikator identyfikuje subskrypcje, dla których nie skonfigurowano alertów i zaleca ich utworzenie.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Zaprojektuj konta magazynu, aby zapobiec osiągnięciu maksymalnego limitu subskrypcji

Region świadczenia usługi Azure może obsłużyć maksymalnie 250 kont magazynu na subskrypcję. Po osiągnięciu limitu nie będzie można utworzyć więcej kont magazynu w ramach tej kombinacji regionu/subskrypcji. Doradca sprawdzi Twoje subskrypcje i zalecenia dotyczące powierzchni, aby zaprojektować mniejszą liczbę kont magazynu dla dowolnego miejsca, które zbliżają się do maksymalnego limitu.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Upewnij się, że masz dostęp do ekspertów w chmurze platformy Azure, gdy ich potrzebujesz

W przypadku wykonywania obciążeń o krytycznym znaczeniu dla firmy ważne jest, aby mieć dostęp do pomocy technicznej w razie potrzeby. Doradca identyfikuje potencjalne subskrypcje o krytycznym znaczeniu dla firmy, które nie mają wsparcia technicznego zawartego w planie pomocy technicznej i zalecają uaktualnienie do opcji, która obejmuje pomoc techniczną.

## <a name="repair-invalid-log-alert-rules"></a>Napraw nieprawidłowe reguły alertów dziennika

Azure Advisor wykryje reguły alertów, które mają nieprawidłowe zapytania określone w sekcji warunku. Reguły alertów dziennika są tworzone w usłudze Azure Monitor i służą do uruchamiania zapytań analitycznych w określonych odstępach czasu. Wyniki zapytania określają, czy trzeba wyzwolić alert. Zapytania analityczne z upływem czasu mogą stać się nieprawidłowe z powodu zmian w zasobach, tabelach lub poleceniach, do których się odwołują. Usługa Advisor zaleca poprawienie zapytania w regule alertu, aby uniemożliwić jego ręczne pobranie i zapewnienie monitorowania zasobów na platformie Azure. [Dowiedz się więcej o rozwiązywaniu problemów z regułami alertów](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Postępuj zgodnie z najlepszymi rozwiązaniami przy użyciu Azure Policy

Azure Policy to usługa platformy Azure, która umożliwia tworzenie i przypisywanie zasad oraz zarządzanie nimi. Te zasady wymuszają różne reguły i efekty względem zasobów. Poniżej znajdują się zalecenia dotyczące Azure Policy, które pomogą Ci w osiągnięciu Excellency operacyjnego: 
1. Zarządzaj tagami przy użyciu Azure Policy: te zasady dodają lub zastępują określony tag i wartość podczas tworzenia lub aktualizowania dowolnego zasobu. Istniejące zasoby można skorygować, wyzwalając zadanie korygowania. Nie powoduje to również modyfikacji tagów w grupach zasobów.
2. Wymuś wymagania dotyczące zgodności geograficznej przy użyciu Azure Policy: zasady umożliwiają ograniczenie lokalizacji, które organizacja może określić podczas wdrażania zasobów. 
3. Określ dozwolone jednostki SKU maszyny wirtualnej dla wdrożeń: te zasady umożliwiają określenie zestawu jednostek SKU maszyn wirtualnych, które organizacja może wdrażać.
4. Wymuszanie zasad „Przeprowadź inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych” przy użyciu usługi Azure Policy
5. Użyj polecenia "Dziedzicz tag z grup zasobów" przy użyciu Azure Policy: zasada dodaje lub zastępuje określony tag oraz wartość z nadrzędnej grupy zasobów po utworzeniu lub zaktualizowaniu dowolnego zasobu. Istniejące zasoby można skorygować, wyzwalając zadanie korygowania.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Wprowadzenie](advisor-get-started.md)
* [Zalecenia dotyczące kosztów usługi Advisor](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące wysokiej dostępności usługi Advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)

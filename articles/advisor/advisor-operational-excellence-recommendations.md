---
title: Ulepszanie Excellency operacyjnego za pomocą usługi Advisor
description: Użyj Azure Advisor, aby zoptymalizować i uzyskać doskonałą komfort działania w ramach subskrypcji platformy Azure.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 1cac5a3f93df8422a3896b1251857bf552731fb4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85125399"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Uzyskiwanie doskonałości operacyjnej przy użyciu Azure Advisor

Zalecenia dotyczące doskonałości operacyjnej w Azure Advisor mogą pomóc w: 
- Wydajność procesu i przepływu pracy.
- Zarządzanie zasobami.
- Najlepsze rozwiązania dotyczące wdrażania. 

Te zalecenia można uzyskać na karcie **doskonałości działania** na pulpicie nawigacyjnym usługi Advisor.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Utwórz alerty Azure Service Health, aby otrzymywać powiadomienia o problemach z platformą Azure

Zalecamy skonfigurowanie alertów Azure Service Health, aby otrzymywać powiadomienia o problemach z usługą platformy Azure. [Azure Service Health](https://azure.microsoft.com/features/service-health/) to bezpłatna usługa, która zapewnia spersonalizowane wskazówki i pomoc techniczną, gdy występuje problem z usługą platformy Azure. Klasyfikator identyfikuje subskrypcje, dla których nie skonfigurowano alertów, i zaleca ich skonfigurowanie.


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Zaprojektuj konta magazynu, aby zapobiec osiągnięciu maksymalnego limitu subskrypcji

Region świadczenia usługi Azure może obsłużyć maksymalnie 250 kont magazynu na subskrypcję. Po osiągnięciu tego limitu nie będzie można tworzyć kont magazynu w ramach tej kombinacji regionu/subskrypcji. Program Advisor sprawdza subskrypcje i oferuje zalecenia dotyczące projektowania w poszukiwaniu mniejszej liczby kont magazynu dla każdego regionu/subskrypcji, który jest bliski osiągnięcia limitu.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Upewnij się, że masz dostęp do ekspertów w chmurze platformy Azure, gdy ich potrzebujesz

W przypadku wykonywania obciążeń o krytycznym znaczeniu dla firmy ważne jest, aby mieć dostęp do pomocy technicznej, gdy będzie potrzebna. Doradca identyfikuje potencjalne subskrypcje o krytycznym znaczeniu dla firmy, które nie mają wsparcia technicznego zawartego w planie pomocy technicznej. Zalecamy uaktualnienie do opcji, która obejmuje pomoc techniczną.

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>Usuń i ponownie Utwórz pulę, aby usunąć przestarzały wewnętrzny składnik

Jeśli pula używa przestarzałego składnika wewnętrznego, Usuń i ponownie Utwórz pulę, aby zwiększyć stabilność i wydajność.

## <a name="repair-invalid-log-alert-rules"></a>Napraw nieprawidłowe reguły alertów dziennika

Azure Advisor wykrywa reguły alertów, które mają nieprawidłowe zapytania określone w sekcji warunku. Reguły alertów dzienników można tworzyć w Azure Monitor i używać ich do uruchamiania zapytań analitycznych w określonych odstępach czasu. Wyniki zapytania określają, czy trzeba wyzwolić alert. Zapytania analityczne mogą stać się nieprawidłowe w miarę upływu czasu ze względu na zmiany w przywoływanych zasobach, tabelach lub poleceniach. Usługa Advisor zaleca poprawienie zapytania w regule alertu, aby zapobiec jego automatycznemu wyłączaniu i zapewnić monitorowanie zasobów na platformie Azure. [Dowiedz się więcej o rozwiązywaniu problemów z regułami alertów.](https://aka.ms/aa_logalerts_queryrepair)

## <a name="use-azure-policy-recommendations"></a>Użyj zaleceń Azure Policy

Azure Policy to usługa platformy Azure, za pomocą której można tworzyć i przypisywać zasady oraz zarządzać nimi. Te zasady wymuszają reguły i efekty dotyczące zasobów. Poniższe zalecenia Azure Policy mogą pomóc w osiągnięciu Excellency operacyjnego: 

**Zarządzaj tagami.** Te zasady dodają lub zastępują określony tag oraz wartość podczas tworzenia lub aktualizowania dowolnego zasobu. Możesz skorygować istniejące zasoby, wyzwalając zadanie korygowania. Te zasady nie modyfikują tagów w grupach zasobów.

**Wymuś wymagania dotyczące zgodności geograficznej.** Ta zasada umożliwia ograniczenie lokalizacji, które organizacja może określić podczas wdrażania zasobów. 

**Określ dozwolone jednostki SKU maszyny wirtualnej dla wdrożeń.** Te zasady umożliwiają określenie zestawu jednostek SKU maszyn wirtualnych, które organizacja może wdrażać.

**Wymuś *inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych*.**

**Włącz *dziedziczenie znacznika z grup zasobów*.** Te zasady dodają lub zastępują określony tag oraz wartość z nadrzędnej grupy zasobów podczas tworzenia lub aktualizowania dowolnego zasobu. Możesz skorygować istniejące zasoby, wyzwalając zadanie korygowania.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Wprowadzenie](advisor-get-started.md)
* [Zalecenia usługi Advisor dotyczące kosztów](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące niezawodności usługi Advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Interfejs API REST usługi Advisor](https://docs.microsoft.com/rest/api/advisor/)

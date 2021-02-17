---
title: Ulepszanie Excellency operacyjnego za pomocą usługi Advisor
description: Użyj Azure Advisor, aby zoptymalizować i uzyskać doskonałą komfort działania w ramach subskrypcji platformy Azure.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 0b938a0c7a42182bb8d2a50b48d65a0844d952a6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579962"
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

Azure Advisor wykrywa reguły alertów, które mają nieprawidłowe zapytania określone w sekcji warunku. Reguły alertów dzienników można tworzyć w Azure Monitor i używać ich do uruchamiania zapytań analitycznych w określonych odstępach czasu. Wyniki zapytania określają, czy trzeba wyzwolić alert. Zapytania analityczne mogą stać się nieprawidłowe w miarę upływu czasu ze względu na zmiany w przywoływanych zasobach, tabelach lub poleceniach. Usługa Advisor zaleca poprawienie zapytania w regule alertu, aby zapobiec jego automatycznemu wyłączaniu i zapewnić monitorowanie zasobów na platformie Azure. [Dowiedz się więcej o rozwiązywaniu problemów z regułami alertów.](../azure-monitor/alerts/alerts-troubleshoot-log.md)

## <a name="use-azure-policy-recommendations"></a>Użyj zaleceń Azure Policy

Azure Policy to usługa platformy Azure, za pomocą której można tworzyć i przypisywać zasady oraz zarządzać nimi. Te zasady wymuszają reguły i efekty dotyczące zasobów. Poniższe zalecenia Azure Policy mogą pomóc w osiągnięciu Excellency operacyjnego: 

**Zarządzaj tagami.** Te zasady dodają lub zastępują określony tag oraz wartość podczas tworzenia lub aktualizowania dowolnego zasobu. Możesz skorygować istniejące zasoby, wyzwalając zadanie korygowania. Te zasady nie modyfikują tagów w grupach zasobów.

**Wymuś wymagania dotyczące zgodności geograficznej.** Ta zasada umożliwia ograniczenie lokalizacji, które organizacja może określić podczas wdrażania zasobów. 

**Określ dozwolone jednostki SKU maszyny wirtualnej dla wdrożeń.** Te zasady umożliwiają określenie zestawu jednostek SKU maszyn wirtualnych, które organizacja może wdrażać.

**Wymuś *inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych*.**

**Włącz *dziedziczenie znacznika z grup zasobów*.** Te zasady dodają lub zastępują określony tag oraz wartość z nadrzędnej grupy zasobów podczas tworzenia lub aktualizowania dowolnego zasobu. Możesz skorygować istniejące zasoby, wyzwalając zadanie korygowania.

Usługa Advisor zaleca kilka indywidualnych zasad platformy Azure, które ułatwiają klientom osiąganie najważniejszych rozwiązań. Jeśli klient zdecyduje się na przypisanie zalecanych zasad, zostanie pominięte zalecenie. Jeśli klient zdecyduje się na późniejsze usunięcie zasad, usługa Advisor nadal pomija zalecenie, ponieważ interpretuje jego usuwanie jako silnego sygnału:

1.  Klient usunął zasady, ponieważ pomimo zalecenia klasyfikatora nie ma zastosowania do ich określonego przypadku użycia. 
2.  Klient ma świadomość i znajomość zasad po jego przypisaniu i usunięciu. można go również przypisać lub usunąć, jeśli okaże się to konieczne, bez wskazówki, jeśli później odnosi się do ich przypadku użycia. Jeśli klient znajdzie go w najlepszym interesie, aby ponownie przypisać te same zasady, może to zrobić w Azure Policy bez wymagania zalecenia w usłudze Advisor. Należy pamiętać, że ta logika jest stosowana w odnosieniu do zalecenia dotyczącego zasad w kategorii doskonałości operacyjnej. Te reguły nie mają zastosowania do zaleceń dotyczących zabezpieczeń.  


## <a name="no-validation-environment-enabled"></a>Nie włączono środowiska weryfikacji
Azure Advisor określa, że nie masz włączonego środowiska sprawdzania poprawności w bieżącej subskrypcji. Podczas tworzenia pul hostów, na karcie właściwości, wybrano opcję \" nie \" \" sprawdzaj poprawności środowiska \" . Posiadanie co najmniej jednej puli hostów z włączonym środowiskiem walidacji zapewnia ciągłość działania usług pulpitu wirtualnego systemu Windows przy wczesnym wykrywaniu potencjalnych problemów. [Dowiedz się więcej](../virtual-desktop/create-validation-host-pool.md)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>Zapewnij środowisko produkcyjne (bez walidacji), aby korzystać ze stabilnej funkcjonalności
Azure Advisor wykryje, że zbyt wiele pul hostów ma włączone środowisko sprawdzania poprawności. Aby środowiska walidacji były najlepszym rozwiązaniem, należy mieć co najmniej jedną, ale nigdy więcej niż połowę pul hostów w środowisku walidacji. Dzięki wykorzystaniu dobrej równowagi między pulami hostów a włączonym środowiskiem walidacji, najlepszym rozwiązaniem jest korzystanie z zalet wdrożeń potokach wieloetapowych oferowanych przez pulpity wirtualne systemu Windows z niektórymi aktualizacjami. Aby rozwiązać ten problem, Otwórz właściwości puli hostów i wybierz pozycję \" nie \" obok \" Ustawienia środowisko walidacji \" .

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>Włączanie analizy ruchu na potrzeby wglądu we wzorce ruchu w zasobach platformy Azure
Analiza ruchu to rozwiązanie oparte na chmurze, które pozwala uzyskać wgląd w aktywność użytkowników i aplikacji na platformie Azure. Analiza ruchu analizuje dzienniki przepływu sieciowych grup zabezpieczeń (NSG) usługi Network Watcher w celu zapewnienia wglądu w przepływ ruchu. Za pomocą analizy ruchu można wyświetlać najintensywniej nadające elementy na platformie Azure i we wdrożeniach spoza tej platformy, badać otwarte porty, protokoły i złośliwe przepływy w środowisku oraz optymalizować wdrożenie sieci pod kątem wydajności. Dzienniki przepływów można przetwarzać w 10-minutowych i 60-minutowych interwałach przetwarzania, co zapewnia szybszą analizę ruchu. Dobrym sposobem jest włączenie Analiza ruchu dla zasobów platformy Azure. 


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Wprowadzenie](advisor-get-started.md)
* [Zalecenia usługi Advisor dotyczące kosztów](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące niezawodności usługi Advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Interfejs API REST usługi Advisor](/rest/api/advisor/)

---
title: Obniż koszty usług za pomocą usługi Azure Advisor
description: Użyj usługi Azure Advisor, aby zoptymalizować koszt wdrożeń platformy Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0237feab59551ecab87d78b0d4d66b9fc7b47e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259697"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Obniż koszty usług za pomocą usługi Azure Advisor

Doradca pomaga zoptymalizować i zmniejszyć ogólne wydatki na platformę Azure, identyfikując bezczynne i niedostatecznie użyczane zasoby.Rekomendacje dotyczące kosztów można uzyskać na karcie **Koszt** na pulpicie nawigacyjnym klasyfikatora.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Zoptymalizuj wydatki na maszyny wirtualne przez zmianę rozmiaru lub zamknięcie nie w pełni wykorzystywanych wystąpień 

Chociaż niektóre scenariusze aplikacji może spowodować niskie wykorzystanie przez projekt, często można zaoszczędzić pieniądze, zarządzając rozmiar i liczbę maszyn wirtualnych. Zaawansowane modele oceny advisor uwzględnia maszyny wirtualne do zamykania, gdy P95th maksymalnej wartości maksymalnego wykorzystania procesora CPU jest mniejsza niż 3%, a wykorzystanie sieci jest mniejsze niż 2% w okresie 7 dni. Maszyny wirtualne są uważane za odpowiedni rozmiar, gdy jest możliwe, aby dopasować bieżące obciążenie w mniejszej jednostki SKU (w tej samej rodzinie jednostek SKU) lub mniejszą liczbę # wystąpienia, takie, że bieżące obciążenie nie przekracza 80% wykorzystania, gdy nie-użytkownik stoi obciążeń i nie powyżej 40% w przypadku obciążenia użytkownika. W tym miejscu typ obciążenia jest określany przez analizowanie charakterystyk wykorzystania procesora CPU obciążenia.

Zalecane akcje są zamykane lub zmienić rozmiar, specyficzne dla zasobu zalecane dla. Doradca pokazuje szacowane oszczędności kosztów dla zalecanych działań — zmienić rozmiar lub zamknąć. Ponadto w celu zmiany rozmiaru zalecanej akcji, Doradca udostępnia bieżące i docelowe informacje o jednostce SKU. 

Jeśli chcesz być bardziej agresywny w identyfikowaniu niedostatecznie wykorzystanych maszyn wirtualnych, możesz dostosować regułę wykorzystania procesora CPU na podstawie subskrypcji.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Redukcja kosztów dzięki wyeliminowaniu nieobjętych do obsługi administracyjnej obwodów usługi ExpressRoute

Doradca identyfikuje obwody usługi ExpressRoute, które były w stanie dostawcy *Nie aprowizowana* przez więcej niż jeden miesiąc, i zaleca usunięcie obwodu, jeśli nie planujesz aprowizować obwodu z dostawcą łączności.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Zmniejsz koszty, usuwając lub ponownie konfigurując bezczynne bramy sieci wirtualnej

Doradca identyfikuje bramy sieci wirtualnej, które były bezczynne przez ponad 90 dni. Ponieważ te bramy są rozliczane co godzinę, należy rozważyć ponowne skonfigurowanie lub usunięcie ich, jeśli nie zamierzasz ich już używać. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Kup wystąpienia zarezerwowane maszyn wirtualnych w celu zaoszczędzenia pieniędzy w porównaniu z płatnością zgodnie z rzeczywistym użyciem

Doradca przejrzy użycie maszyny wirtualnej w ciągu ostatnich 30 dni i określi, czy można zaoszczędzić pieniądze, kupując rezerwację platformy Azure. Doradca pokaże Ci regiony i rozmiary, w których potencjalnie masz najwięcej oszczędności i pokaże ci szacunkowe oszczędności z zakupu rezerwacji. Za pomocą rezerwacji platformy Azure można zakupić w przedsprzedaży koszty podstawowe dla maszyn wirtualnych. Rabaty będą automatycznie stosowane do nowych lub istniejących maszyn wirtualnych, które mają taki sam rozmiar i region jak rezerwacje. [Dowiedz się więcej o wystąpieniach zarezerwowanych maszyn wirtualnych platformy Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Doradca powiadomi cię również o wystąpieniach zarezerwowanych, które wygasną w ciągu najbliższych 30 dni. Zaleca się zakup nowych zarezerwowanych wystąpień, aby uniknąć płacenia cen płatności zgodnie z rzeczywistym użyciem.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Usuń nieskoczywiczone publiczne adresy IP, aby zaoszczędzić pieniądze

Doradca identyfikuje publiczne adresy IP, które nie są obecnie skojarzone z zasobami platformy Azure, takimi jak moduły równoważenia obciążenia lub maszyny wirtualne. Te publiczne adresy IP są pobierane z opłatą nominalną. Jeśli nie zamierzasz ich używać, ich usunięcie może spowodować oszczędności.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Usuń potoki usługi Azure Data Factory, które kończą się niepowodzeniem

Usługa Azure Advisor wykryje potoki usługi Azure Data Factory, które wielokrotnie nie powiodą się, i zaleci rozwiązanie problemów lub usunięcie nieudanych potoków, jeśli nie są już potrzebne. Będą naliczane na te potoki, nawet jeśli nie służą ci, gdy nie. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Używanie standardowych migawek dla dysków zarządzanych
Aby obniżyć koszt o 60%, zalecamy przechowywanie migawek w usłudze Standard Storage niezależnie od typu magazynu na dysku nadrzędnym. Ta opcja jest domyślną opcją dla migawek dysków zarządzanych. Usługa Azure Advisor zidentyfikuje migawki, które są przechowywane w magazynie w wersji Premium i zaleci migrację migawki z usługi Premium do magazynu standardowego. [Dowiedz się więcej o cenach dysków zarządzanych](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Korzystanie z zarządzania cyklem życia
Azure Advisor will utilize intelligence regarding your Azure blob storage object count, total size, and transactions to detect if one or more of your storage accounts would be best suited to enable lifecycle management to tier data. Zostanie wyświetlony monit o utworzenie reguł zarządzania cyklem życia, aby automatycznie warstwy danych do cool lub archiwum w celu optymalizacji kosztów magazynu przy jednoczesnym zachowaniu danych w magazynie obiektów blob platformy Azure dla zgodności aplikacji.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Jak uzyskać dostęp do zaleceń dotyczących kosztów w usłudze Azure Advisor

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wyszukaj i wybierz [**Doradcę**](https://aka.ms/azureadvisordashboard) z dowolnej strony.

1. Na pulpicie nawigacyjnym **klasyfikatora** wybierz kartę **Koszt.**

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zaleceniach doradcy, zobacz:
* [Wprowadzenie do doradcy](advisor-overview.md)
* [Rozpocząć](advisor-get-started.md)
* [Zalecenia dotyczące wydajności doradcy](advisor-performance-recommendations.md)
* [Zalecenia dotyczące wysokiej dostępności advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń doradcy](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej doradcy](advisor-operational-excellence-recommendations.md)

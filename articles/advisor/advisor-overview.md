---
title: Wprowadzenie do Azure Advisor
description: Użyj Azure Advisor, aby zoptymalizować wdrożenia platformy Azure.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 600bda282d46f86979d0366719826c3a6c1323e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75443096"
---
# <a name="introduction-to-azure-advisor"></a>Wprowadzenie do Azure Advisor

Poznaj kluczowe możliwości Azure Advisor i uzyskaj odpowiedzi na często zadawane pytania.

## <a name="what-is-advisor"></a>Co to jest Doradca?
Usługa Advisor to spersonalizowany konsultant ds. rozwiązań w chmurze, który pomaga stosować najlepsze rozwiązania w celu optymalizacji wdrożeń platformy Azure. Analizuje konfigurację zasobów i dane telemetryczne dotyczące użycia, a następnie zaleca rozwiązania, które mogą pomóc w zapewnieniu wysokiej dostępności, bezpieczeństwa, wydajności i efektywności kosztowej zasobów platformy Azure.

Za pomocą usługi Advisor można:
* uzyskiwanie proaktywnych, spersonalizowanych zaleceń dotyczących najlepszych rozwiązań umożliwiających podjęcie działań; 
* Zwiększ wydajność, bezpieczeństwo i wysoką dostępność zasobów, ponieważ możesz identyfikować możliwości zmniejszania ogólnych wydatków związanych z platformą Azure.
* uzyskiwanie zaleceń dotyczących proponowanych wbudowanych akcji.

Dostęp do usługi Advisor można uzyskać za pomocą [Azure Portal](https://aka.ms/azureadvisordashboard). Zaloguj się do [portalu](https://portal.azure.com), Znajdź polecenie **Advisor** w menu nawigacji lub wyszukaj je w menu **wszystkie usługi** .

Pulpit nawigacyjny klasyfikatora Wyświetla spersonalizowane zalecenia dotyczące wszystkich subskrypcji.  Filtry mogą być stosowane do wyświetlania zaleceń dotyczących określonych subskrypcji i typów zasobów.  Rekomendacje są podzielone na cztery kategorie: 

* **Wysoka dostępność**: aby zapewnić i poprawić ciągłość aplikacji o krytycznym znaczeniu dla firmy. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące wysokiej dostępności usługi Advisor](advisor-high-availability-recommendations.md).
* **Zabezpieczenia**: aby wykrywać zagrożenia i luki w zabezpieczeniach, które mogą prowadzić do naruszeń zabezpieczeń. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md).
* **Wydajność**: aby zwiększyć szybkość działania aplikacji. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md).
* **Koszt**: aby zoptymalizować i zmniejszyć ogólne wydatki na platformę Azure. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące kosztów usługi Advisor](advisor-cost-recommendations.md).
* **Doskonałości operacyjna**: aby pomóc w osiągnięciu wydajności procesu i przepływu pracy, najlepszych rozwiązań w zakresie zarządzania zasobami i wdrażania. . Aby uzyskać więcej informacji, zapoznaj się z [zaleceniami dotyczącymi działania usługi Advisor](advisor-operational-excellence-recommendations.md).

  ![Typy rekomendacji usługi Advisor](./media/advisor-overview/advisor-dashboard.png)

Możesz kliknąć kategorię, aby wyświetlić listę zaleceń w tej kategorii, a następnie wybrać zalecenie, aby dowiedzieć się więcej na jego temat.  Możesz również zapoznać się z akcjami, które można wykonać, aby skorzystać z szansy sprzedaży lub rozwiązać problem.

![Kategoria rekomendacji klasyfikatora](./media/advisor-overview/advisor-ha-category-example.png) 

Wybierz zalecaną akcję, aby zalecić wdrożenie zalecenia.  Zostanie otwarty prosty interfejs, który umożliwia wdrożenie zalecenia lub zapoznaj się z dokumentacją, która pomaga w implementacji.  Po zaimplementowaniu zalecenia może upłynąć do dnia, aby klasyfikator mógł go rozpoznać.

Jeśli nie zamierzasz wykonać natychmiastowej akcji, możesz odłożyć ją od określonego czasu lub odrzucić.  Jeśli nie chcesz otrzymywać zaleceń dotyczących określonej subskrypcji lub grupy zasobów, możesz skonfigurować usługę Advisor, aby generować tylko zalecenia dotyczące określonych subskrypcji i grup zasobów.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-do-i-access-advisor"></a>Jak mogę dostęp do usługi Advisor?
Dostęp do usługi Advisor można uzyskać za pomocą [Azure Portal](https://aka.ms/azureadvisordashboard). Zaloguj się do [portalu](https://portal.azure.com), Znajdź polecenie **Advisor** w menu nawigacji lub wyszukaj je w menu **wszystkie usługi** .

Możesz również wyświetlić zalecenia doradcy za pomocą interfejsu zasobów maszyny wirtualnej. Wybierz maszynę wirtualną, a następnie przewiń do opcji zalecenia klasyfikatora w menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Jakie uprawnienia muszę uzyskać dostęp do usługi Advisor?
 
Możesz uzyskać dostęp do zaleceń klasyfikatora jako *właściciela*, *współautora*lub *czytelnika* subskrypcji.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Dla jakich zasobów usługa Advisor udostępnia zalecenia?

Doradca zawiera zalecenia dotyczące Application Gateway, App Services, zestawów dostępności, pamięci podręcznej platformy Azure, Azure Data Factory, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database for MariaDB, Azure ExpressRoute, Azure Cosmos DB, publicznych adresów IP platformy Azure, SQL Data Warehouse, serwerów SQL, kont magazynu, profilów Traffic Manager i maszyn wirtualnych.

Azure Advisor również zawiera zalecenia z [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) , które mogą obejmować zalecenia dotyczące dodatkowych typów zasobów.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Czy mogę odłożyć lub odrzucić rekomendację?

Aby odłożyć lub odrzucić zalecenie, kliknij link **Odłożenie** . Możesz określić okres odroczenia lub wybrać opcję **nigdy nie** należy odrzucić zalecenia.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zaleceń klasyfikatora, zobacz:

* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia dotyczące wysokiej dostępności usługi Advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące kosztów usługi Advisor](advisor-cost-recommendations.md)

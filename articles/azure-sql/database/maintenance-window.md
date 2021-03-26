---
title: Okno obsługi
description: Dowiedz się, jak można skonfigurować Azure SQL Database i okno obsługi wystąpienia zarządzanego.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/23/2021
ms.openlocfilehash: 9d7ab0498673ad7006087b66575eea9371b96d11
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565906"
---
# <a name="maintenance-window-preview"></a>Okno obsługi (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Funkcja okna obsługi umożliwia skonfigurowanie harmonogramu konserwacji dla [Azure SQL Database](sql-database-paas-overview.md) i zasobów [wystąpienia zarządzanego usługi Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) , co wpływa na przewidywalność i mniejszą przerwę w obciążeniu. 

> [!Note]
> Funkcja okna obsługi nie chroni przed nieplanowanymi zdarzeniami, takimi jak awarie sprzętu, co może spowodować krótkie przerwy w połączeniu.

## <a name="overview"></a>Omówienie

Platforma Azure okresowo przeprowadza [planowaną konserwację](planned-maintenance.md) zasobów wystąpień zarządzanych SQL Database i SQL. Podczas zdarzenia konserwacji usługi Azure SQL bazy danych są w pełni dostępne, ale mogą być poddawane krótkim ponownym konfiguracjom w ramach odpowiednich umowy SLA dostępności dla [wystąpień zarządzanych](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance) [SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database) i SQL.

Okno obsługi jest przeznaczone do obciążeń produkcyjnych, które nie są odporne na ponowne konfiguracje bazy danych lub wystąpień i nie mogą wchłonąć krótkich przerw w działaniu spowodowanych przez planowane zdarzenia konserwacji. Wybierając preferowane okno obsługi, możesz zminimalizować wpływ planowanej konserwacji w miarę ich występowania poza szczytowymi godzinami pracy. Obciążenia odporne na błędy i obciążenia nieprodukcyjne mogą opierać się na domyślnych zasadach konserwacji usługi Azure SQL.

Okno obsługi można skonfigurować podczas tworzenia lub dla istniejących zasobów usługi Azure SQL. Można ją skonfigurować przy użyciu interfejsu API Azure Portal, PowerShell, interfejsu wiersza polecenia lub platformy Azure.

> [!Important]
> Konfigurowanie okna obsługi jest długotrwałą operacją asynchroniczną, podobną do zmiany warstwy usług zasobu SQL platformy Azure. Zasób jest dostępny podczas operacji, z wyjątkiem krótkiej ponownej konfiguracji, która odbywa się na koniec operacji i zazwyczaj trwa do 8 sekund nawet w przypadku przerwanych długotrwałych transakcji. Aby zminimalizować wpływ ponownej konfiguracji, należy wykonać operację poza godzinami szczytu.

### <a name="gain-more-predictability-with-maintenance-window"></a>Zwiększ przewidywalność przy użyciu okna obsługi

Domyślnie zasady konserwacji usługi Azure SQL mają wpływ na aktualizacje, które w trakcie okresu **8:00 na czas lokalny 17:00 każdego dnia** , aby uniknąć przerw w czasie wykonywania typowych godzin pracy. Czas lokalny jest określany na podstawie lokalizacji [regionu platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , w którym znajduje się zasób, i może obserwować czas letni zgodnie z lokalną definicją strefy czasowej. 

Możesz dodatkowo dostosować aktualizacje konserwacji do czasu odpowiedniego dla zasobów usługi Azure SQL, wybierając spośród dwóch dodatkowych gniazd okna obsługi:
 
* Okno dnia tygodnia, 10PM do 6:00 czasu lokalnego poniedziałek-czwartek
* Okno weekendowe, 10PM do 6:00 czas lokalny piątek — niedziela

Po dokonaniu wyboru okna obsługi i zakończeniu konfiguracji usługi planowana konserwacja będzie odbywać się tylko w wybranym oknie.   

> [!Important]
> W bardzo rzadkich sytuacjach, gdy dowolne odroczenie akcji może skutkować poważnym wpływem, takich jak stosowanie krytycznej poprawki zabezpieczeń, skonfigurowane okno obsługi może być tymczasowo przesłonięta. 

### <a name="cost-and-eligibility"></a>Koszt i kwalifikowanie się

Konfigurowanie i korzystanie z okna obsługi jest bezpłatne za wszystkie kwalifikujące się [typy ofert](https://azure.microsoft.com/support/legal/offer-details/): płatność zgodnie z rzeczywistym użyciem, dostawca rozwiązań w chmurze (CSP), Microsoft Enterprise Agreement lub umowa klienta firmy Microsoft.

> [!Note]
> Oferta platformy Azure to typ posiadanej subskrypcji platformy Azure. Na przykład subskrypcja z [stawką płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/)użyciem, [platforma Azure w ramach usługi Open](https://azure.microsoft.com/offers/ms-azr-0111p/)i [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) to wszystkie oferty platformy Azure. Każda oferta lub plan ma inne warunki i zalety. Twoja oferta lub plan są wyświetlane na stronie Przegląd subskrypcji. Aby uzyskać więcej informacji na temat przełączania subskrypcji na inną ofertę, zobacz [Zmienianie subskrypcji platformy Azure na inną ofertę](../../cost-management-billing/manage/switch-azure-offer.md).

## <a name="advance-notifications"></a>Powiadomienia z wyprzedzeniem

Powiadomienia dotyczące konserwacji można skonfigurować w taki sposób, aby ostrzegał o nadchodzących planowanych zdarzeniach konserwacji dla Azure SQL Database 24 godzin z wyprzedzeniem, w czasie konserwacji i po zakończeniu konserwacji. Aby uzyskać więcej informacji, zobacz [powiadomienia z wyprzedzeniem](advance-notifications.md).

## <a name="availability"></a>Dostępność

### <a name="supported-service-level-objectives"></a>Obsługiwane cele poziomu usługi

Wybór okna obsługi inne niż domyślne jest dostępne dla wszystkich SLO **z wyjątkiem**:
* Hiperskala 
* Pule wystąpień
* Starsza wersja obliczenia rdzeń wirtualny
* Basic, S0 i S1 
* DC, Fsv2, Seria M

### <a name="azure-region-support"></a>Obsługa regionów platformy Azure

Wybranie okna obsługi innego niż domyślne jest obecnie dostępne w następujących regionach:

- Australia Wschodnia
- Australia Południowo-Wschodnia
- Brazylia Południowa
- Kanada Środkowa
- Central US
- East US
- Wschodnie stany USA 2
- Azja Wschodnia
- Japonia Wschodnia
- NorthCentral nam
- Europa Północna
- SouthCentral nam
- Azja Południowo-Wschodnia
- Południowe Zjednoczone Królestwo
- West Europe
- Zachodnie stany USA
- Zachodnie stany USA 2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Konserwacja bramy dla Azure SQL Database

Aby uzyskać maksymalną korzyść z okien obsługi, upewnij się, że aplikacje klienckie używają zasad połączenia przekierowania. Przekierowywanie jest zalecanymi zasadami połączeń, w których klienci nawiązują połączenia bezpośrednio z węzłem hostującym bazę danych, co prowadzi do zmniejszenia opóźnień i zwiększonej przepływności.  

* W Azure SQL Database wszystkie połączenia korzystające z zasad połączenia serwera proxy mogą mieć wpływ zarówno z wybranego okna obsługi, jak i okna obsługi węzła bramy. Jednak połączenia klienckie korzystające z zalecanych zasad połączenia przekierowania nie mają wpływ na ponowną konfigurację konserwacji węzła bramy. 

* W wystąpieniu zarządzanym usługi Azure SQL węzły bramy są hostowane [w klastrze wirtualnym](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) i mają takie samo okno obsługi jak wystąpienie zarządzane, ale przy użyciu zasad połączenia przekierowania nadal zaleca się zminimalizowanie liczby przerw w czasie trwania zdarzenia konserwacji.

Aby uzyskać więcej informacji na temat zasad połączenia klienta w Azure SQL Database, zobacz [Azure SQL Database Policy Connection](../database/connectivity-architecture.md#connection-policy). 

Aby uzyskać więcej informacji na temat zasad połączenia klienta w wystąpieniu zarządzanym Azure SQL, zobacz [typy połączeń wystąpienia zarządzanego Azure SQL](../../azure-sql/managed-instance/connection-types-overview.md).

## <a name="considerations-for-azure-sql-managed-instance"></a>Zagadnienia dotyczące wystąpienia zarządzanego usługi Azure SQL

Wystąpienie zarządzane Azure SQL obejmuje składniki usługi hostowane w dedykowanym zestawie izolowanych maszyn wirtualnych, które działają w podsieci sieci wirtualnej klienta. Te maszyny wirtualne tworzą [klastry wirtualne](../managed-instance/connectivity-architecture-overview.md#high-level-connectivity-architecture) , które mogą hostować wiele wystąpień zarządzanych. Okno obsługi skonfigurowane w wystąpieniach jednej podsieci może mieć wpływ na liczbę klastrów wirtualnych w podsieci i dystrybucję wystąpień między klastrami wirtualnymi. Może to wymagać uwzględnienia kilku efektów.

### <a name="maintenance-window-configuration-is-long-running-operation"></a>Konfiguracja okna obsługi jest operacją długotrwałą 
Wszystkie wystąpienia hostowane w klastrze wirtualnym korzystają z okna obsługi. Domyślnie wszystkie wystąpienia zarządzane są hostowane w klastrze wirtualnym przy użyciu domyślnego okna obsługi. Określenie innego okna obsługi dla wystąpienia zarządzanego podczas jego tworzenia lub po nim oznacza, że musi być umieszczony w klastrze wirtualnym z odpowiednim oknem obsługi. Jeśli w podsieci nie ma takiego klastra wirtualnego, należy najpierw utworzyć nową, aby pomieścić wystąpienie. Dopełnienie dodatkowego wystąpienia w istniejącym klastrze wirtualnym może wymagać zmiany rozmiaru klastra. Obie operacje przyczyniają się do czasu trwania konfigurowania okna obsługi dla wystąpienia zarządzanego.
Oczekiwany czas trwania konfigurowania okna obsługi w wystąpieniu zarządzanym można obliczyć przy użyciu [szacowanego czasu trwania operacji zarządzania wystąpieniami](../managed-instance/management-operations-overview.md#duration).

> [!Important]
> Krótka ponowna konfiguracja odbywa się na koniec operacji konserwacji i zazwyczaj trwa do 8 sekund nawet w przypadku przerwanych długotrwałych transakcji. Aby zminimalizować wpływ ponownej konfiguracji, należy zaplanować operację poza godzinami szczytu.

### <a name="ip-address-space-requirements"></a>Wymagania dotyczące przestrzeni adresów IP
Każdy nowy klaster wirtualny w podsieci wymaga dodatkowych adresów IP zgodnie z [alokacją adresów IP klastra wirtualnego](../managed-instance/vnet-subnet-determine-size.md#determine-subnet-size). Zmiana okna obsługi istniejącego wystąpienia zarządzanego wymaga również [tymczasowej dodatkowej pojemności IP](../managed-instance/vnet-subnet-determine-size.md#address-requirements-for-update-scenarios) , jak w przypadku skalowania rdzeni wirtualnych dla odpowiedniej warstwy usług.

### <a name="ip-address-change"></a>Zmiana adresu IP
Skonfigurowanie i zmiana okna obsługi powoduje zmianę adresu IP wystąpienia w zakresie adresów IP podsieci.

> [!Important]
>  Upewnij się, że reguły sieciowej grupy zabezpieczeń i zapory nie blokują ruchu danych po zmianie adresu IP. 

## <a name="next-steps"></a>Następne kroki

* [Powiadomienia z wyprzedzeniem](advance-notifications.md)
* [Konfigurowanie okna obsługi](maintenance-window-configure.md)

## <a name="learn-more"></a>Więcej tutaj

* [Okno obsługi — często zadawane pytania](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [Wystąpienie zarządzane SQL](../managed-instance/sql-managed-instance-paas-overview.md)
* [Zaplanuj zdarzenia konserwacji platformy Azure w Azure SQL Database i wystąpieniu zarządzanym Azure SQL](planned-maintenance.md)
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
ms.date: 03/04/2021
ms.openlocfilehash: 0a9a4b2de03c62640bb1c643d3ff3da4139d42a4
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101209"
---
# <a name="maintenance-window-preview"></a>Okno obsługi (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Funkcja okna obsługi umożliwia konfigurację przewidywalnych harmonogramów okna obsługi dla [Azure SQL Database](sql-database-paas-overview.md) i [wystąpienia zarządzanego SQL](../managed-instance/sql-managed-instance-paas-overview.md). 

Aby uzyskać więcej informacji na temat zdarzeń konserwacji, zobacz [Planowanie zdarzeń konserwacji platformy Azure w Azure SQL Database i wystąpienia zarządzanego Azure SQL](planned-maintenance.md).

## <a name="overview"></a>Omówienie

Na platformie Azure są wykonywane planowane aktualizacje konserwacji dotyczące Azure SQL Database i zasobów wystąpienia zarządzanego SQL okresowo, które często obejmują aktualizacje sprzętu podstawowego, oprogramowania, w tym podstawowy system operacyjny (OS) i aparat SQL. W trakcie aktualizacji konserwacji zasoby są w pełni dostępne i dostępne, ale niektóre aktualizacje konserwacji wymagają przełączenia w tryb failover, ponieważ usługa Azure pobiera wystąpienia w trybie offline przez krótki czas, aby zastosować aktualizacje konserwacji (osiem sekund w czasie trwania).  Aktualizacje planowanej konserwacji są przeprowadzane raz na 35 dni, co oznacza, że klient może oczekiwać około jednego zaplanowanego zdarzenia konserwacji miesięcznie na Azure SQL Database lub wystąpienie zarządzane SQL i tylko w gniazdach okna obsługi wybranych przez klienta.   

Okno obsługi jest przeznaczone dla obciążeń firmowych, które nie są odporne na sporadyczne problemy z łącznością, które mogą wynikać z planowanych zdarzeń konserwacji.

Okno obsługi można skonfigurować przy użyciu interfejsu API Azure Portal, PowerShell, interfejsu wiersza polecenia lub platformy Azure. Można go skonfigurować podczas tworzenia lub dla istniejących baz danych SQL i wystąpień zarządzanych przez program SQL.

> [!Important]
> Konfigurowanie okna obsługi jest długotrwałą operacją asynchroniczną, podobną do zmiany warstwy usług zasobu SQL platformy Azure. Zasób jest dostępny podczas operacji, z wyjątkiem krótkiej pracy w trybie failover, która jest wykonywana na końcu operacji i zazwyczaj trwa do 8 sekund nawet w przypadku przerwanych długotrwałych transakcji. Aby zminimalizować wpływ trybu failover, należy wykonać operację poza godzinami szczytu.

### <a name="gain-more-predictability-with-maintenance-window"></a>Zwiększ przewidywalność przy użyciu okna obsługi

Domyślnie wszystkie bazy danych Azure SQL Database i zarządzane wystąpienia bazy danych są aktualizowane tylko podczas 17:00 8:00 czasu lokalnego, aby uniknąć przerw w pracy w godzinach pracy. Czas lokalny jest określany na podstawie [regionu platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , który hostuje zasób. Możesz dodatkowo dostosować aktualizacje konserwacji do czasu odpowiedniego dla bazy danych, wybierając spośród dwóch dodatkowych gniazd okna obsługi:
 
* Okno dnia tygodnia, 10PM do 6:00 lokalnego czasu poniedziałek — czwartek
* Okno weekendowe, 10PM do 6:00 czas lokalny piątek — niedziela

Po dokonaniu wyboru okna obsługi i zakończeniu konfigurowania usługi wszystkie planowane aktualizacje konserwacji będą wykonywane tylko w wybranym oknie.   

> [!Note]
> Oprócz planowanych aktualizacji konserwacji w rzadkich przypadkach nieplanowane zdarzenia konserwacji mogą spowodować niedostępność. 

### <a name="cost-and-eligibility"></a>Koszt i kwalifikowanie się

Konfigurowanie i korzystanie z okna obsługi jest bezpłatne za wszystkie kwalifikujące się [typy ofert](https://azure.microsoft.com/support/legal/offer-details/): płatność zgodnie z rzeczywistym użyciem, dostawca rozwiązań w chmurze (CSP), Microsoft Enterprise lub umowa klienta firmy Microsoft.

> [!Note]
> Oferta platformy Azure to typ posiadanej subskrypcji platformy Azure. Na przykład subskrypcja z [stawką płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/)użyciem, [platforma Azure w ramach usługi Open](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/)i [Visual Studio Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) to wszystkie oferty platformy Azure. Każda oferta lub plan ma inne warunki i zalety. Twoja oferta lub plan są wyświetlane na stronie Przegląd subskrypcji. Aby uzyskać więcej informacji na temat przełączania subskrypcji na inną ofertę, zobacz [Zmienianie subskrypcji platformy Azure na inną ofertę](/azure/cost-management-billing/manage/switch-azure-offer).

## <a name="advance-notifications"></a>Powiadomienia z wyprzedzeniem

Powiadomienia dotyczące konserwacji można skonfigurować w taki sposób, aby ostrzegał o przyszłych planowanych zdarzeniach konserwacyjnych Azure SQL Database 24-godzinnym wyprzedzeniu, w czasie konserwacji i po zakończeniu okna obsługi. Aby uzyskać więcej informacji, zobacz [powiadomienia z wyprzedzeniem](advance-notifications.md).

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

* W Azure SQL Database wszystkie połączenia korzystające z zasad połączenia serwera proxy mogą mieć wpływ zarówno z wybranego okna obsługi, jak i okna obsługi węzła bramy. Jednak połączenia klienckie korzystające z zalecanych zasad połączenia przekierowania nie mają wpływ na tryb failover konserwacji węzła bramy. 

* W wystąpieniu zarządzanym usługi Azure SQL węzły bramy są hostowane [w klastrze wirtualnym](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) i mają takie samo okno obsługi jak wystąpienie zarządzane, ale przy użyciu zasad połączenia przekierowania nadal zaleca się zminimalizowanie liczby przerw w czasie trwania zdarzenia konserwacji.

Więcej informacji na temat zasad połączenia klienta w Azure SQL Database można znaleźć w temacie [Azure SQL Database Policy Connection](../database/connectivity-architecture.md#connection-policy). 

Aby uzyskać więcej informacji na temat zasad połączenia klienta w wystąpieniu zarządzanym Azure SQL, zobacz [typy połączeń wystąpienia zarządzanego Azure SQL](../../azure-sql/managed-instance/connection-types-overview.md).


## <a name="next-steps"></a>Następne kroki

* [Powiadomienia z wyprzedzeniem](advance-notifications.md)
* [Konfiguruj okno obsługi](maintenance-window-configure.md)

## <a name="learn-more"></a>Więcej tutaj

* [Okno obsługi — często zadawane pytania](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [Wystąpienie zarządzane SQL](../managed-instance/sql-managed-instance-paas-overview.md)
* [Zaplanuj zdarzenia konserwacji platformy Azure w Azure SQL Database i wystąpieniu zarządzanym Azure SQL](planned-maintenance.md)





---
title: Często zadawane pytania — Azure Database Migration Service
description: Często zadawane pytania dotyczące korzystania z Azure Database Migration Service w celu przeprowadzenia migracji baz danych.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: f4d65c97bfccd223453583b25ee0586c5bc0b1ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091450"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Często zadawane pytania dotyczące korzystania z Azure Database Migration Service

Ten artykuł zawiera listę często zadawanych pytań dotyczących używania Azure Database Migration Service razem z powiązanymi odpowiedziami.

## <a name="overview"></a>Omówienie

**Pytania. Co to jest Azure Database Migration Service?**
Azure Database Migration Service to w pełni zarządzana usługa, która umożliwia bezproblemowe Migrowanie z wielu źródeł baz danych do platform danych platformy Azure z minimalnym czasem przestoju. Usługa jest obecnie ogólnie dostępna, z trwającymi działaniami programistycznymi, które koncentrują się na:

* Niezawodność i wydajność.
* Iteracyjne dodawanie par źródłowych-docelowych.
* Kontynuowanie inwestycji w bezpłatnej migracji.

**Pytania. Jakie pary źródłowe i docelowe Azure Database Migration Service obecnie obsługiwane?**
Usługa obecnie obsługuje różne pary źródłowe/docelowe lub scenariusze migracji. Aby zapoznać się z pełną listą stanu każdego dostępnego scenariusza migracji, zapoznaj się ze [stanem artykułu scenariusze migracji obsługiwane przez Azure Database Migration Service](./resource-scenario-status.md).

Inne scenariusze migracji są w wersji zapoznawczej i wymagają przesłania nominacji za pośrednictwem witryny DMS Preview. Aby zapoznać się z pełną listą scenariuszy w wersji zapoznawczej i zarejestrować się w celu wzięcia udziału w jednej z tych ofert, zobacz [witrynę DMS w wersji zapoznawczej](https://aka.ms/dms-preview/).

**Pytania. Jakie wersje SQL Server są obsługiwane przez Azure Database Migration Service jako źródło?**
W przypadku migrowania z SQL Server obsługiwane źródła dla Azure Database Migration Service są SQL Server 2005 do SQL Server 2019.

**P: w przypadku używania Azure Database Migration Service, jaka jest różnica między trybem offline a migracją online?**
Azure Database Migration Service można użyć do przeprowadzenia migracji w trybie offline i online. W przypadku migracji w *trybie offline* przestój aplikacji rozpoczyna się po rozpoczęciu migracji. W przypadku migracji w *trybie online* przestoje są ograniczone do czasu, w którym można wyciąć na końcu migracji. Zalecamy przetestowanie migracji offline w celu ustalenia, czy przestój jest dopuszczalny. Jeśli nie jest on dopuszczalny, przeprowadź migrację online.

> [!NOTE]
> Użycie Azure Database Migration Service do przeprowadzenia migracji w trybie online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium. Więcej informacji znajduje się na stronie [cennika](https://azure.microsoft.com/pricing/details/database-migration/) usługi Azure Database Migration Service.

**Pytania. Jak Azure Database Migration Service porównać z innymi narzędziami do migracji bazy danych firmy Microsoft, takimi jak baza danych Asystent migracji (DMA) czy Asystent migracji do programu SQL Server (ASYSTENCIE migracji)?**
Azure Database Migration Service jest preferowaną metodą migracji bazy danych na Microsoft Azure na dużą skalę. Aby uzyskać szczegółowe informacje o tym, jak Azure Database Migration Service porównać z innymi narzędziami do migracji bazy danych firmy Microsoft i zalecenia dotyczące korzystania z usługi dla różnych scenariuszy, zobacz ogłaszanie w blogu [odróżnienie narzędzi i usług migracji bazy danych firmy Microsoft](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529).

**Pytania. Jak Azure Database Migration Service porównać z ofertą Azure Migrate?**
Azure Migrate pomaga w migracji lokalnych maszyn wirtualnych do usługi Azure IaaS. Usługa ocenia przydatność migracji i ustalanie rozmiarów na podstawie wydajności, a także umożliwia oszacowanie kosztów uruchamiania lokalnych maszyn wirtualnych na platformie Azure. Azure Migrate jest przydatne do przenoszenia i przesunięcia lokalnych obciążeń opartych na maszynach wirtualnych do maszyn wirtualnych platformy Azure IaaS. Jednak, w przeciwieństwie do Azure Database Migration Service, Azure Migrate nie jest wyspecjalizowaną ofertą usługi migracji bazy danych dla platform Azure PaaS relacyjnych baz danych, takich jak Azure SQL Database lub wystąpienie zarządzane usługi Azure SQL.

**Pytania. Czy Database Migration Service przechowywać dane klienta?**
Nie. Database Migration Service nie przechowuje danych klienta.

## <a name="setup"></a>Konfiguracja

**Pytania. Jakie są wymagania wstępne dotyczące korzystania z Azure Database Migration Service?**
Istnieje kilka wymagań wstępnych, aby zapewnić bezproblemowy przebieg Azure Database Migration Service podczas przeprowadzania migracji baz danych. Niektóre wymagania wstępne dotyczą wszystkich scenariuszy (par Source-Target) obsługiwanych przez usługę, a inne wymagania wstępne są unikatowe dla konkretnego scenariusza.

Azure Database Migration Service wymagania wstępne, które są wspólne dla wszystkich obsługiwanych scenariuszy migracji, to m.in.:

* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](../expressroute/expressroute-introduction.md) lub [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Upewnij się, że reguły grupy zabezpieczeń sieci wirtualnej nie blokują portu 443 dla tagów serviceServiceBus, Storage i AzureMonitor. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* W przypadku korzystania z urządzenia zapory przed źródłowymi bazami danych może zajść potrzeba dodania reguł zapory, aby umożliwić Azure Database Migration Service dostęp do źródłowych baz danych na potrzeby migracji.

Aby zapoznać się z listą wszystkich wymagań wstępnych, które są wymagane do konkurowania określonych scenariuszy migracji przy użyciu Azure Database Migration Service, zobacz pokrewne samouczki w [dokumentacji](./dms-overview.md) Azure Database Migration Service w witrynie docs.Microsoft.com.

**Pytania. Jak mogę znaleźć adresu IP dla Azure Database Migration Service, aby można było utworzyć listę dozwolonych reguł zapory używanych do uzyskiwania dostępu do mojej źródłowej bazy danych na potrzeby migracji?**
Może być konieczne dodanie reguł zapory umożliwiających Azure Database Migration Service dostępu do źródłowej bazy danych na potrzeby migracji. Adres IP usługi jest dynamiczny, ale jeśli używasz ExpressRoute, ten adres jest przypisywany prywatnie przez sieć firmową. Najprostszym sposobem identyfikacji odpowiedniego adresu IP jest wyszukanie w tej samej grupie zasobów co zasób Azure Database Migration Service zainicjowany, aby znaleźć skojarzony interfejs sieciowy. Zwykle nazwa zasobu interfejsu sieciowego zaczyna się od prefiksu karty sieciowej i następuje unikatowy znak i sekwencja numerów, na przykład karta sieciowa-jj6tnztnmarpsskr82rbndyp. Po wybraniu tego zasobu interfejsu sieciowego można zobaczyć adres IP, który musi być uwzględniony na liście dozwolonych na stronie Przegląd zasobów Azure Portal.

Może być również konieczne dołączenie źródła portu, które SQL Server nasłuchuje na liście dozwolonych. Domyślnie jest to port 1433, ale źródło SQL Server można skonfigurować do nasłuchiwania na innych portach. W takim przypadku należy również uwzględnić te porty na liście dozwolonych. Możesz określić port, na którym nasłuchuje SQL Server przy użyciu zapytania dynamicznego widoku zarządzania:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Możesz również określić port, który SQL Server nasłuchuje, badając dziennik błędów SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**Pytania. Jak mogę skonfigurować Microsoft Azure Virtual Network?**
Chociaż wiele samouczków firmy Microsoft, które mogą przeprowadzić Cię przez proces konfigurowania sieci wirtualnej, oficjalna dokumentacja jest wyświetlana w artykule [Virtual Network platformy Azure](../virtual-network/virtual-networks-overview.md).

## <a name="usage"></a>Użycie

**Pytania. Co to jest podsumowanie kroków wymaganych do przeprowadzenia migracji bazy danych za pomocą Azure Database Migration Service?**
Podczas typowej migracji prostej bazy danych:

1. Tworzenie docelowych baz danych.
2. Oceń źródłowe bazy danych.
    * W przypadku jednorodnych migracji należy ocenić istniejące bazy danych za pomocą usługi [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * W przypadku migracji heterogenicznych (ze źródeł konkurowania) Oceń istniejące bazy danych za pomocą [Asystencie migracji](/sql/ssma/sql-server-migration-assistant). Program ASYSTENCIE migracji umożliwia również Konwertowanie obiektów bazy danych i migrowanie schematu na platformę docelową.
3. Utwórz wystąpienie usługi Azure Database Migration Service.
4. Utwórz projekt migracji określający źródłową bazę danych, docelowe bazy danych i tabele do migracji.
5. Rozpocznij pełne ładowanie.
6. Wybierz kolejną weryfikację.
7. Ręczne przełączenie środowiska produkcyjnego do nowej bazy danych opartej na chmurze.

## <a name="troubleshooting-and-optimization"></a>Rozwiązywanie problemów i optymalizacja

**Pytania. Konfiguruję projekt migracji w usłudze DMS i mam problemy z nawiązywaniem połączenia z moją źródłową bazą danych. Co mam zrobić?**
Jeśli występują problemy z połączeniem ze źródłowym systemem bazy danych podczas pracy z migracją, należy utworzyć maszynę wirtualną w tej samej podsieci sieci wirtualnej, dla której skonfigurowano wystąpienie DMS. Na maszynie wirtualnej powinno być możliwe uruchomienie testu połączenia, takiego jak użycie pliku UDL do przetestowania połączenia w celu SQL Server lub pobrania Robo 3T w celu przetestowania połączeń MongoDB. Jeśli test połączenia zakończy się pomyślnie, nie trzeba mieć problemu z połączeniem ze źródłową bazą danych. Jeśli test połączenia nie powiedzie się, skontaktuj się z administratorem sieci.

**Pytania. Dlaczego moje Azure Database Migration Service są niedostępne lub zatrzymane?**
Jeśli użytkownik jawnie zatrzyma Azure Database Migration Service (DMS) lub jeśli usługa jest nieaktywna przez okres 24 godzin, usługa będzie w stanie zatrzymania lub jest wstrzymana. W każdym przypadku usługa będzie niedostępna i stan zatrzymany.  Aby wznowić migracje aktywne, należy ponownie uruchomić usługę.

**Pytania. Czy istnieją jakieś zalecenia dotyczące optymalizacji wydajności Azure Database Migration Service?**
Aby przyspieszyć migrację bazy danych przy użyciu usługi, można wykonać kilka czynności:

* Użyj warstwy cenowej wielu procesorów Ogólnego przeznaczenia podczas tworzenia wystąpienia usługi, aby umożliwić usłudze korzystanie z wielu procesorów wirtualnych vCPU na potrzeby przetwarzanie równoległe i szybszych transferów danych.
* Tymczasowo Skaluj wystąpienie obiektu docelowego Azure SQL Database do jednostki SKU warstwy Premium podczas operacji migracji danych, aby zminimalizować Azure SQL Database ograniczanie wydajności, które mogą mieć wpływ na działania transferu danych w przypadku korzystania z jednostek SKU niższego poziomu.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem Azure Database Migration Service i regionalnej dostępności, zobacz artykuł [co to jest Azure Database Migration Service](dms-overview.md).

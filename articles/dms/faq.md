---
title: Często zadawane pytania — Azure Database Migration Service
description: Często zadawane pytania dotyczące używania Azure Database Migration Service do przeprowadzania migracji bazy danych.
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
ms.openlocfilehash: 83ffd405bf8e39737d2d36c294eac27f9bc76daf
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588823"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Często zadawane pytania dotyczące korzystania z Azure Database Migration Service

W tym artykule wymieniono często zadawane pytania dotyczące używania Azure Database Migration Service wraz z powiązanymi odpowiedziami.

## <a name="overview"></a>Omówienie

**P. Co to jest Azure Database Migration Service?**
Azure Database Migration Service to w pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemowej migracji z wielu źródeł baz danych do platform danych platformy Azure przy minimalnych przestojach. Usługa jest obecnie ogólnie dostępna, a ciągłe prace programowe koncentrują się na:

* Niezawodność i wydajność.
* Iteracyjne dodawanie par źródło-cel.
* Dalsze inwestycje w bezproblemowe migracje.

**P. Jakie pary źródło/cel Azure Database Migration Service obecnie?**
Usługa obsługuje obecnie różne pary źródłowy/docelowy lub scenariusze migracji. Aby uzyskać pełną listę stanu każdego dostępnego scenariusza migracji, zobacz artykuł [Status of migration scenarios supported by the Azure Database Migration Service](https://github.com/MicrosoftDocs/azure-docs-pr/pull/resource-scenario-status.md).

**P. Jakie wersje SQL Server są Azure Database Migration Service jako źródło?**
W przypadku migracji z SQL Server obsługiwane źródła dla Azure Database Migration Service są SQL Server 2005 r. do SQL Server 2019 r.

**Pytanie: Jaka jest Azure Database Migration Service migracji w trybie offline i migracji online?**
Możesz użyć Azure Database Migration Service do przeprowadzania migracji w trybie offline i online. W przypadku *migracji w trybie offline* przestój aplikacji rozpoczyna się po jej zakończeniu. W przypadku *migracji online* przestój jest ograniczony do czasu przeciętych na końcu migracji. Zalecamy przetestowanie migracji offline w celu ustalenia, czy przestój jest dopuszczalny. Jeśli nie jest on dopuszczalny, przeprowadź migrację online.

> [!NOTE]
> Używanie Azure Database Migration Service do przeprowadzania migracji online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium. Więcej informacji znajduje się na stronie [cennika](https://azure.microsoft.com/pricing/details/database-migration/) usługi Azure Database Migration Service.

**P. Jak wygląda Azure Database Migration Service z innymi narzędziami do migracji baz danych firmy Microsoft, takimi jak Database Migration Assistant (DMA) lub Asystent migracji do programu SQL Server (SSMA)?**
Azure Database Migration Service jest preferowaną metodą migracji bazy danych do Microsoft Azure na dużą skalę. Aby uzyskać więcej informacji na temat porównania usług Azure Database Migration Service z innymi narzędziami do migracji baz danych firmy Microsoft oraz zalecenia dotyczące korzystania z usługi w różnych scenariuszach, zobacz wpis w blogu [Differentiating Microsoft Database Migration Tools and Services](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)(Różne narzędzia i usługi migracji baz danych firmy Microsoft).

**P. Jak Azure Database Migration Service się z ofertą Azure Migrate usług?**
Azure Migrate pomaga w migracji lokalnych maszyn wirtualnych do usługi Azure IaaS. Usługa ocenia przydatność migracji i rozmiaru na podstawie wydajności oraz zapewnia szacowane koszty uruchamiania lokalnych maszyn wirtualnych na platformie Azure. Azure Migrate jest przydatna w przypadku migracji typu "lift-and-shift" lokalnych obciążeń maszyn wirtualnych do maszyn wirtualnych IaaS platformy Azure. Jednak w przeciwieństwie Azure Database Migration Service, Azure Migrate nie jest wyspecjalizowaną ofertą usługi migracji baz danych dla platform relacyjnych baz danych PaaS platformy Azure, takich jak Azure SQL Database lub Azure SQL Managed Instance.

**P. Czy Database Migration Service dane klientów?**
Nie. Database Migration Service nie przechowuje danych klientów.

## <a name="setup"></a>Konfiguracja

**P. Jakie są wymagania wstępne dotyczące korzystania z Azure Database Migration Service?**
Istnieje kilka wymagań wstępnych wymaganych do zapewnienia bezproblemowego Azure Database Migration Service podczas przeprowadzania migracji bazy danych. Niektóre wymagania wstępne dotyczą wszystkich scenariuszy (par źródłowych i docelowych) obsługiwanych przez usługę, a inne wymagania wstępne są unikatowe dla konkretnego scenariusza.

Azure Database Migration Service wymagania wstępne, które są wspólne dla wszystkich obsługiwanych scenariuszy migracji, obejmują:

* Utwórz Microsoft Azure Virtual Network dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność lokacja-lokacja z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](../expressroute/expressroute-introduction.md) lub sieci [VPN.](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* Upewnij się, że reguły sieciowej grupy zabezpieczeń sieci wirtualnej nie blokują portu 443 dla tagów usług ServiceBus, Storage i AzureMonitor. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł Filtrowanie ruchu [sieciowego przy użyciu sieciowych grup zabezpieczeń.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* W przypadku korzystania z urządzenia zapory przed źródłową bazą danych może być konieczne dodanie reguł zapory, aby umożliwić Azure Database Migration Service dostęp do źródłowych baz danych na potrzeby migracji.

Aby uzyskać listę wszystkich wymagań wstępnych wymaganych do konkurowania w określonych scenariuszach migracji przy użyciu usługi Azure Database Migration Service, zobacz powiązane samouczki w dokumentacji usługi Azure Database Migration Service [na](./dms-overview.md) docs.microsoft.com.

**P. Jak mogę znaleźć adres IP dla usługi Azure Database Migration Service, aby można było utworzyć listę zezwalania dla reguł zapory używanych do uzyskiwania dostępu do mojej źródłowej bazy danych w celu migracji?**
Może być konieczne dodanie reguł zapory umożliwiających Azure Database Migration Service dostępu do źródłowej bazy danych na potrzeby migracji. Adres IP usługi jest dynamiczny, ale jeśli używasz usługi ExpressRoute, ten adres jest przypisywany prywatnie przez sieć firmową. Najprostszym sposobem zidentyfikowania odpowiedniego adresu IP jest znalezienie skojarzonego interfejsu sieciowego w tej samej grupie zasobów, w Azure Database Migration Service zasobów aprowizowanych. Zazwyczaj nazwa zasobu interfejsu sieciowego zaczyna się od prefiksu karty sieciowej, po którym następuje unikatowy znak i sekwencja liczb, na przykład NIC-jj6tnztnmarpsskr82rbndyp. Wybierając ten zasób interfejsu sieciowego, możesz zobaczyć adres IP, który musi zostać uwzględniony na liście zezwalań na stronie przeglądu Azure Portal zasobów.

Może być również konieczne dołączenie źródła portu, SQL Server nasłuchuje na liście zezwalań. Domyślnie jest to port 1433, ale źródło SQL Server może być również skonfigurowane do nasłuchiwać na innych portach. W takim przypadku należy również uwzględnić te porty na liście zezwalań. Możesz określić port, na SQL Server nasłuchuje przy użyciu zapytania dynamicznego widoku zarządzania:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Możesz również określić port, SQL Server nasłuchuje, odpytując dziennik SQL Server błędu:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**P. Jak mogę skonfigurować Microsoft Azure Virtual Network?**
Chociaż wiele samouczków firmy Microsoft, które mogą przejść przez proces konfigurowania sieci wirtualnej, oficjalna dokumentacja jest wyświetlana w artykule [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

## <a name="usage"></a>Użycie

**P. Jakie jest podsumowanie kroków wymaganych do użycia Azure Database Migration Service do przeprowadzenia migracji bazy danych?**
Podczas typowej, prostej migracji bazy danych:

1. Tworzenie docelowych baz danych.
2. Oceń źródłowe bazy danych.
    * Aby uzyskać jednorodne migracje, oceń istniejące bazy danych przy użyciu narzędzia [DMA.](https://www.microsoft.com/download/details.aspx?id=53595)
    * W przypadku migracji heterogenicznych (ze źródeł konkurencji) oceń istniejące bazy danych za pomocą [usługi SSMA.](/sql/ssma/sql-server-migration-assistant) Za pomocą usługi SSMA można również konwertować obiekty bazy danych i migrować schemat na platformę docelową.
3. Utwórz wystąpienie usługi Azure Database Migration Service.
4. Utwórz projekt migracji, określając źródłowe bazy danych, docelowe bazy danych i tabele do migracji.
5. Uruchom pełne ładowanie.
6. Wybierz kolejną weryfikację.
7. Wykonaj ręczną zmianę środowiska produkcyjnego na nową bazę danych opartą na chmurze.

## <a name="troubleshooting-and-optimization"></a>Rozwiązywanie problemów i optymalizacja

**P. Mam problemy z nawiązaniem połączenia ze źródłową bazą danych, gdy projekt migracji jest ustawiany w u użytkownikach usługi DMS. Co mam zrobić?**
Jeśli masz problemy z nawiązaniem połączenia ze źródłowym systemem bazy danych podczas pracy nad migracją, utwórz maszynę wirtualną w tej samej podsieci sieci wirtualnej, za pomocą której jest ustawione wystąpienie usługi DMS. Na maszynie wirtualnej powinno być możliwe uruchomienie testu połączenia, na przykład użycie pliku UDL do przetestowania połączenia z usługą SQL Server lub pobranie programu Robo 3T w celu przetestowania połączeń z bazą danych MongoDB. Jeśli test połączenia zakończy się pomyślnie, nie powinien być problem z nawiązaniem połączenia ze źródłową bazą danych. Jeśli test połączenia nie powiedzie się, skontaktuj się z administratorem sieci.

**P. Dlaczego moja Azure Database Migration Service jest niedostępna lub zatrzymana?**
Jeśli użytkownik jawnie zatrzyma Azure Database Migration Service (DMS) lub jeśli usługa jest nieaktywna przez okres 24 godzin, usługa będzie w stanie zatrzymania lub automatycznego wstrzymania. W każdym przypadku usługa będzie niedostępna i będzie w stanie zatrzymania.  Aby wznowić aktywne migracje, uruchom ponownie usługę.

**P. Czy istnieją jakieś zalecenia dotyczące optymalizacji wydajności Azure Database Migration Service?**
Możesz wykonać kilka czynności, aby przyspieszyć migrację bazy danych przy użyciu usługi :

* Użyj warstwy cenowej procesora Ogólnego przeznaczenia podczas tworzenia wystąpienia usługi, aby umożliwić usłudze korzystanie z wielu procesorów wirtualnych w celu przetwarzania równoległego i szybszego transferu danych.
* Tymczasowe skalowanie w górę Azure SQL Database docelowego do jednostki SKU warstwy Premium podczas operacji migracji danych w celu zminimalizowania ograniczenia przepustowości usługi Azure SQL Database, które może mieć wpływ na działania transferu danych w przypadku korzystania z jednostki SKU niższego poziomu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie Azure Database Migration Service i dostępności regionalnej, zobacz artykuł [Co to jest Azure Database Migration Service](dms-overview.md).

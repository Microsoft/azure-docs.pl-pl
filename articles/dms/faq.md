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
ms.openlocfilehash: 29240f371d39ef2d3234e3207cc91768478bf3ff
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748507"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Często zadawane pytania dotyczące korzystania z Azure Database Migration Service

W tym artykule wymieniono często zadawane pytania dotyczące używania Azure Database Migration Service wraz z powiązanymi odpowiedziami.

## <a name="overview"></a>Omówienie

**P. Co to jest Azure Database Migration Service?**
Azure Database Migration Service to w pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemowej migracji z wielu źródeł baz danych do platform danych platformy Azure przy minimalnych przestojach. Usługa jest obecnie ogólnie dostępna, a ciągłe prace programowe koncentrują się na:

* Niezawodność i wydajność.
* Iteracyjne dodawanie par źródłowych i docelowych.
* Dalsze inwestycje w bezproblemowe migracje.

**P. Jakie pary źródło/cel Azure Database Migration Service obecnie obsługuje?**
Usługa obsługuje obecnie różne pary źródłowy/docelowy lub scenariusze migracji. Aby uzyskać pełną listę stanu każdego dostępnego scenariusza migracji, zobacz artykuł [Status of migration scenarios supported by the Azure Database Migration Service](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/dms/resource-scenario-status.md).

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
* Upewnij się, że reguły sieciowej grupy zabezpieczeń sieci wirtualnej nie blokują portu 443 dla tagów usługi ServiceTags usług ServiceBus, Storage i AzureMonitor. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł Filtrowanie ruchu sieciowego [przy użyciu sieciowych grup zabezpieczeń.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* W przypadku korzystania z urządzenia zapory przed źródłową bazą danych może być konieczne dodanie reguł zapory w celu umożliwienia Azure Database Migration Service dostępu do źródłowych baz danych na potrzeby migracji.

Aby uzyskać listę wszystkich wymagań wstępnych wymaganych do konkurowania w określonych scenariuszach migracji przy użyciu programu Azure Database Migration Service, zobacz powiązane samouczki w dokumentacji usługi Azure Database Migration Service [na](./dms-overview.md) docs.microsoft.com.

**P. Jak mogę znaleźć adres IP dla usługi Azure Database Migration Service, aby można było utworzyć listę zezwalań dla reguł zapory używanych do uzyskiwania dostępu do źródłowej bazy danych w celu migracji?**
Może być konieczne dodanie reguł zapory umożliwiających Azure Database Migration Service dostępu do źródłowej bazy danych na potrzeby migracji. Adres IP usługi jest dynamiczny, ale jeśli używasz usługi ExpressRoute, ten adres jest przypisywany prywatnie przez sieć firmową. Najprostszym sposobem zidentyfikowania odpowiedniego adresu IP jest znalezienie skojarzonego interfejsu sieciowego w tej samej grupie zasobów, w Azure Database Migration Service zasobów aprowizowanych. Zazwyczaj nazwa zasobu interfejsu sieciowego zaczyna się od prefiksu karty sieciowej, po którym następuje unikatowy znak i sekwencja liczb, na przykład NIC-jj6tnztnmarpsskr82rbndyp. Po wybraniu tego zasobu interfejsu sieciowego możesz zobaczyć adres IP, który musi zostać uwzględniony na liście zezwalań na stronie przeglądu Azure Portal zasobów.

Może być również konieczne dołączenie źródła portu, które SQL Server nasłuchuje na liście zezwalań. Domyślnie jest to port 1433, ale źródło danych SQL Server może być również skonfigurowane do nasłuchiwać na innych portach. W takim przypadku należy również uwzględnić te porty na liście zezwalań. Port, na SQL Server nasłuchuje, można określić za pomocą zapytania dynamicznego widoku zarządzania:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Możesz również określić port, który SQL Server nasłuchuje, odpytując dziennik SQL Server błędu:

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
    * W przypadku jednorodnych migracji oceń istniejące bazy danych za pomocą narzędzia [DMA.](https://www.microsoft.com/download/details.aspx?id=53595)
    * W przypadku migracji heterogenicznych (ze źródeł konkurencji) oceń istniejące bazy danych za pomocą [SSMA.](/sql/ssma/sql-server-migration-assistant) Za pomocą funkcji SSMA można również konwertować obiekty bazy danych i migrować schemat na platformę docelową.
3. Utwórz wystąpienie usługi Azure Database Migration Service.
4. Utwórz projekt migracji, określając źródłowe bazy danych, docelowe bazy danych i tabele do migracji.
5. Uruchom pełne ładowanie.
6. Wybierz kolejną weryfikację.
7. Wykonaj ręczną zmianę środowiska produkcyjnego na nową bazę danych opartą na chmurze.

## <a name="troubleshooting-and-optimization"></a>Rozwiązywanie problemów i optymalizacja

**P. Mam problemy z nawiązaniem połączenia z źródłową bazą danych podczas konfigurowania projektu migracji w u usługi DMS. Co mam zrobić?**
Jeśli podczas pracy nad migracją masz problemy z nawiązaniem połączenia ze źródłowym systemem bazy danych, utwórz maszynę wirtualną w tej samej podsieci sieci wirtualnej, za pomocą której jest ustawione wystąpienie usługi DMS. Na maszynie wirtualnej powinno być możliwe uruchomienie testu połączenia, takiego jak użycie pliku UDL do przetestowania połączenia z programem SQL Server lub pobranie programu Robo 3T w celu przetestowania połączeń z bazą danych MongoDB. Jeśli test połączenia zakończy się pomyślnie, nie powinien być problem z nawiązywanie połączenia ze źródłową bazą danych. Jeśli test połączenia nie powiedzie się, skontaktuj się z administratorem sieci.

**P. Dlaczego mój Azure Database Migration Service jest niedostępny lub zatrzymany?**
Jeśli użytkownik jawnie zatrzyma Azure Database Migration Service (DMS) lub jeśli usługa jest nieaktywna przez okres 24 godzin, usługa będzie w stanie zatrzymania lub automatycznego wstrzymania. W każdym przypadku usługa będzie niedostępna i będzie w stanie zatrzymania.  Aby wznowić aktywne migracje, uruchom ponownie usługę.

**P. Czy istnieją jakieś zalecenia dotyczące optymalizacji wydajności Azure Database Migration Service?**
Możesz wykonać kilka czynności, aby przyspieszyć migrację bazy danych przy użyciu usługi :

* Warstwy cenowej z wieloma procesorami OGÓLNEGO PRZEZNACZENIA podczas tworzenia wystąpienia usługi, aby umożliwić usłudze korzystanie z wielu procesorów wirtualnych w celu przetwarzania równoległego i szybszego transferu danych.
* Tymczasowe skalowanie w górę Azure SQL Database do jednostki SKU w warstwie Premium podczas operacji migracji danych w celu zminimalizowania ograniczenia przepustowości, które może mieć wpływ na działania transferu danych w przypadku korzystania z jednostki S Azure SQL Database KU niższego poziomu.

## <a name="next-steps"></a>Następne kroki

Omówienie dostępności Azure Database Migration Service regionalnych można znaleźć w artykule [Co to jest Azure Database Migration Service](dms-overview.md).

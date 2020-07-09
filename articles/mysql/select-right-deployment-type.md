---
title: Wybieranie odpowiedniego typu wdrożenia — Azure Database for MySQL
description: W tym artykule opisano czynniki, które należy wziąć pod uwagę przed wdrożeniem Azure Database for MySQL jak infrastruktura jako usługa (IaaS) lub platforma jako usługa (PaaS).
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 827c1954456ee7943c48525919ed411836f610aa
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114027"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Wybieranie odpowiedniej opcji serwera MySQL na platformie Azure

Na platformie Azure obciążenia serwera MySQL mogą działać w hostowanej infrastrukturze maszyny wirtualnej jako usługi (IaaS) lub jako hostowanej platformy jako usługi (PaaS). PaaS ma wiele opcji wdrażania, a w każdej opcji wdrożenia znajdują się warstwy usług. Po wybraniu opcji między IaaS i PaaS należy zdecydować, czy chcesz zarządzać bazą danych, stosować poprawki i tworzyć kopie zapasowe, czy też chcesz delegować te operacje na platformę Azure.

Podczas podejmowania decyzji należy wziąć pod uwagę następujące dwie opcje:

- **Azure Database for MySQL**. Ta opcja to w pełni zarządzany aparat bazy danych MySQL oparty na stabilnej wersji programu MySQL Community Edition. Ta relacyjna baza danych jako usługa (DBaaS) hostowana na platformie Azure Cloud Platform znajduje się w kategorii branża PaaS.

  W przypadku wystąpienia zarządzanego programu MySQL na platformie Azure można używać wbudowanych funkcji, które w przeciwnym razie wymagają obszernej konfiguracji, gdy serwer MySQL jest lokalnie lub na maszynie wirtualnej platformy Azure.

  W przypadku korzystania z programu MySQL jako usługi Płatność odbywa się zgodnie z opcjami skalowania w górę lub w poziomie w celu uzyskania większej kontroli bez przerw w działaniu. W przeciwieństwie do autonomicznego serwera MySQL, Azure Database for MySQL oferuje dodatkowe funkcje, takie jak wbudowana wysoka dostępność, analiza i zarządzanie.

- **Baza danych MySQL na maszynach wirtualnych platformy Azure**. Ta opcja znajduje się w kategorii branża IaaS. Za pomocą tej usługi można uruchomić serwer MySQL wewnątrz w pełni zarządzanej maszyny wirtualnej na platformie Azure w chmurze. Wszystkie najnowsze wersje i wersje programu MySQL można zainstalować na maszynie wirtualnej IaaS.

  W najbardziej znaczącej różnicy między Azure Database for MySQL, MySQL na maszynach wirtualnych platformy Azure oferuje kontrolę nad aparatem bazy danych. Jednak ten formant jest kosztem odpowiedzialności za zarządzanie maszynami wirtualnymi i wieloma zadaniami administracyjnymi bazy danych (DBA). Te zadania obejmują obsługę i stosowanie poprawek do serwerów baz danych, odzyskiwania bazy danych i projektu o wysokiej dostępności.

Główne różnice między tymi opcjami są wymienione w poniższej tabeli:

| Atrybut          | Azure Database for MySQL | Baza danych MySQL na maszynach wirtualnych platformy Azure    |
|:-------------------|:-----------------------------|:--------------------|
| Umowa dotycząca poziomu usług (SLA)                | Oferuje umowę SLA na 99,99% dostępności| Do 99,95% dostępności z co najmniej dwoma wystąpieniami w tym samym zestawie dostępności.<br/><br/>dostępność na 99,9% z maszyną wirtualną o pojedynczym wystąpieniu przy użyciu usługi Premium Storage.<br/><br/>99,99% przy użyciu Strefy dostępności z wieloma wystąpieniami w wielu zestawach dostępności.<br/><br/>Zobacz umowę [SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)dotyczącą Virtual Machines. |
| Stosowanie poprawek systemu operacyjnego        | Automatyczny  | Zarządzane przez klientów |
| Stosowanie poprawek MySQL     | Automatyczny  | Zarządzane przez klientów |
| Wysoka dostępność | Model wysokiej dostępności (HA) jest oparty na wbudowanych mechanizmach trybu failover w przypadku wystąpienia przerwy na poziomie węzła. W takich przypadkach usługa automatycznie tworzy nowe wystąpienie i dołącza magazyn do tego wystąpienia. | Klienci mogą korzystać z architektów, implementować, testować i obsługiwać wysoką dostępność. Możliwości mogą obejmować klastrowanie, replikację itp.|
| Nadmiarowość stref | Obecnie nieobsługiwane | Maszyny wirtualne platformy Azure można skonfigurować do uruchamiania w różnych strefach dostępności. W przypadku rozwiązania lokalnego klienci muszą tworzyć i obsługiwać własne pomocnicze centrum danych oraz zarządzać nimi.|
| Scenariusze hybrydowe | Za pomocą [replikacja typu Data-in](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)można synchronizować dane z zewnętrznego serwera MySQL do usługi Azure Database for MySQL. Serwer zewnętrzny może być lokalny, w maszynach wirtualnych lub w usłudze bazy danych hostowanej przez innych dostawców chmury.<br/><br/> Funkcja [Read Replica](https://docs.microsoft.com/azure/mysql/concepts-read-replicas) umożliwia replikowanie danych z serwera głównego Azure Database for MySQL do maksymalnie pięciu serwerów repliki tylko do odczytu. Repliki są w tym samym regionie świadczenia usługi Azure lub w różnych regionach. Repliki tylko do odczytu są asynchronicznie aktualizowane przy użyciu technologii replikacji binlog.| Zarządzane przez klientów
| Tworzenie kopii zapasowej i przywracanie | Program automatycznie tworzy [kopie zapasowe serwera](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) i przechowuje je w magazynie skonfigurowanym przez użytkownika, który jest lokalnie nadmiarowy lub geograficznie nadmiarowy. Usługa przyjmuje kopie zapasowe z pełnymi, różnicami i dziennikami transakcji | Zarządzane przez klientów |
| Operacje monitorowania bazy danych | Oferuje klientom możliwość [ustawiania alertów](https://docs.microsoft.com/azure/mysql/concepts-monitoring) w ramach operacji bazy danych i działania po osiągnięciu progów. | Zarządzane przez klientów |
| Zaawansowana ochrona przed zagrożeniami | Zapewnia [zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal). Ta ochrona wykrywa nietypowe działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. | Klienci muszą samodzielnie skompilować tę ochronę.
| Odzyskiwanie po awarii | Przechowuje automatyczne kopie zapasowe w [magazynie lokalnie nadmiarowy lub geograficznie nadmiarowy](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal). Kopie zapasowe mogą również przywracać serwer do punktu w czasie. Okres przechowywania jest w dowolnym miejscu od 7 do 35 dni. Przywracanie jest realizowane przy użyciu Azure Portal. | W pełni zarządzane przez klientów. Obowiązki obejmują m.in. Planowanie, testowanie, archiwizowanie, przechowywanie i przechowywanie. Dodatkowa opcja polega na użyciu magazynu Recovery Services platformy Azure do tworzenia kopii zapasowych maszyn wirtualnych i baz danych platformy Azure na maszynach wirtualnych. Ta opcja jest w wersji zapoznawczej. |
| Zalecenia dotyczące wydajności | Zapewnia klientom [zalecenia dotyczące wydajności](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) na podstawie generowanych przez system plików dziennika użycia. Zalecenia ułatwiają optymalizację obciążeń. | Zarządzane przez klientów |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motywacje biznesowe do wyboru PaaS lub IaaS

Istnieje kilka czynników, które mogą mieć wpływ na decyzję, aby wybrać PaaS lub IaaS do hostowania baz danych MySQL.

### <a name="cost"></a>Koszty

Ograniczone finansowanie jest często podstawowym zagadnieniem, które określa najlepsze rozwiązanie do hostowania baz danych. Jest to prawdziwe, niezależnie od tego, czy jesteś w trakcie uruchamiania z małym środkiem pieniężnym czy zespołem w firmie, który działa w ramach ścisłych ograniczeń budżetowych. W tej sekcji opisano podstawowe informacje dotyczące rozliczeń i licencjonowania na platformie Azure, które mają zastosowanie do Azure Database for MySQL i MySQL na maszynach wirtualnych platformy Azure.

#### <a name="billing"></a>Rozliczenia

Azure Database for MySQL jest obecnie dostępna jako usługa w kilku warstwach z różnymi cenami dla zasobów. Wszystkie zasoby są rozliczane godzinowo według ustalonej stawki. Najnowsze informacje na temat aktualnie obsługiwanych warstw usług, rozmiarów obliczeń i ilości miejsca do magazynowania można znaleźć w temacie [model zakupów oparty na rdzeń wirtualny](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Możesz dynamicznie dostosować warstwy usług i rozmiary obliczeń, aby odpowiadały potrzebom różnych przepływności aplikacji. Opłaty są naliczane za wychodzący ruch internetowy przy zwykłych [stawkach za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/).

W przypadku Azure Database for MySQL firma Microsoft automatycznie konfiguruje, poprawki i uaktualnia oprogramowanie bazy danych. Te automatyczne akcje zmniejszają koszty administracyjne. Ponadto Azure Database for MySQL ma [wbudowaną funkcję tworzenia kopii zapasowych](https://docs.microsoft.com/azure/mysql/concepts-backup) . Te funkcje pomagają osiągnąć znaczący koszt oszczędności, zwłaszcza w przypadku dużej liczby baz danych. W przeciwieństwie do programu MySQL na maszynach wirtualnych platformy Azure można wybrać i uruchomić dowolną wersję programu MySQL. Niezależnie od używanej wersji programu MySQL można płacić za zainicjowaną maszynę wirtualną oraz koszty dla określonego typu licencji MySQL.

Azure Database for MySQL zapewnia wbudowaną wysoką dostępność dla dowolnego rodzaju przerw na poziomie węzła, utrzymując gwarancję SLA na 99,99% dla usługi. Jednak w przypadku wysokiej dostępności bazy danych w ramach maszyn wirtualnych klienci powinni korzystać z opcji wysokiej dostępności, takich jak [replikacja MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication.html) , która jest dostępna w bazie danych MySQL. Korzystanie z obsługiwanej opcji wysokiej dostępności nie zapewnia dodatkowej umowy SLA. Jednak pozwala to osiągnąć więcej niż 99,99% dostępności bazy danych przy dodatkowych kosztach i obciążeniu administracyjnym.

Aby uzyskać więcej informacji na temat cen, zobacz następujące artykuły:
* [Cennik Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/)
* [Cennik maszyny wirtualnej](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administracja

W przypadku wielu firm decyzja o przejściu do usługi w chmurze polega na tym, że jest to bardzo dużo na odciążeniu złożoności administracji, ponieważ ma ona koszt. Z IaaS i PaaS, firma Microsoft:

- Zarządza podstawową infrastrukturą.
- Automatycznie replikuje wszystkie dane w celu zapewnienia odzyskiwania po awarii.
- Konfiguruje i uaktualnia oprogramowanie bazy danych.
- Zarządza równoważeniem obciążenia.
- Działa w trybie failover, jeśli wystąpi błąd serwera.

Na poniższej liście opisano zagadnienia dotyczące zagadnień administracyjnych dla każdej z nich:

* Za pomocą Azure Database for MySQL można nadal administrować swoją bazą danych. Nie trzeba jednak już zarządzać aparatem bazy danych, systemem operacyjnym ani sprzętem. Przykłady elementów, którymi można zarządzać, to m.in.:

  - Bazy danych
  - Logowanie
  - Dostrajanie indeksu
  - Dostrajanie zapytania
  - Inspekcja
  - Zabezpieczenia

  Ponadto skonfigurowanie wysokiej dostępności w innym centrum danych wymaga minimalnej konfiguracji lub jej administracji.

* Za pomocą programu MySQL na maszynach wirtualnych platformy Azure masz pełną kontrolę nad systemem operacyjnym i konfiguracją wystąpienia serwera MySQL. Za pomocą maszyny wirtualnej Zdecyduj, kiedy zaktualizować lub uaktualnić system operacyjny i oprogramowanie bazy danych. Należy również określić, kiedy zainstalować dodatkowe oprogramowanie, np. aplikację antywirusową. Niektóre funkcje automatyczne zapewniają znacznie uproszczenie stosowania poprawek, kopii zapasowych i wysokiej dostępności. Można kontrolować rozmiar maszyny wirtualnej, liczbę dysków i ich konfiguracje magazynu. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych i usług w chmurze dla platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="time-to-move-to-azure"></a>Czas przejścia na platformę Azure

* Azure Database for MySQL to odpowiednie rozwiązanie dla aplikacji zaprojektowanych w chmurze, gdy produktywność deweloperów i krótki czas wprowadzenia na rynek nowych rozwiązań mają kluczowe znaczenie. Dzięki funkcjom programistycznym, takim jak DBA, usługa jest odpowiednia dla architektów i deweloperów rozwiązań w chmurze, ponieważ obniża potrzebę zarządzania bazowym systemem operacyjnym i bazą danych.

* Aby uniknąć czasu i wydatków na pozyskiwanie nowego sprzętu lokalnego, program MySQL na maszynach wirtualnych platformy Azure to odpowiednie rozwiązanie dla aplikacji, które wymagają bazy danych MySQL lub dostępu do funkcji MySQL w systemie Windows lub Linux. To rozwiązanie jest również odpowiednie do migrowania istniejących lokalnych aplikacji i baz danych na platformę Azure bez zmian, w przypadku których Azure Database for MySQL jest słabo dopasowany.

  Ze względu na to, że nie trzeba zmieniać warstw prezentacji, aplikacji i danych, możesz zaoszczędzić czas i budżet na potrzeby ponownej architektury istniejącego rozwiązania. Zamiast tego można skupić się na migrowaniu wszystkich rozwiązań na platformę Azure i rozwiązaniu niektórych optymalizacji wydajności, które mogą być wymagane przez platformę Azure.

## <a name="next-steps"></a>Następne kroki

* Zobacz [cennik Azure Database for MySQL](https://azure.microsoft.com/pricing/details/MySQL/).
* Zacznij od [utworzenia pierwszego serwera](https://docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal).

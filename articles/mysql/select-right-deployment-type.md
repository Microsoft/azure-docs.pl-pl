---
title: Wybieranie odpowiedniego typu wdrożenia — Azure Database for MySQL
description: W tym artykule opisano czynniki, które należy wziąć pod uwagę przed wdrożeniem Azure Database for MySQL jak infrastruktura jako usługa (IaaS) lub platforma jako usługa (PaaS).
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: a1b66528bee63fb123271e4277e122603ced2e75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906506"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Wybieranie odpowiedniej opcji serwera MySQL na platformie Azure

Na platformie Azure obciążenia serwera MySQL mogą działać w hostowanej infrastrukturze maszyny wirtualnej jako usługi (IaaS) lub jako hostowanej platformy jako usługi (PaaS). PaaS ma wiele opcji wdrażania, a w każdej opcji wdrożenia znajdują się warstwy usług. Po wybraniu opcji między IaaS i PaaS należy zdecydować, czy chcesz zarządzać bazą danych, stosować poprawki i tworzyć kopie zapasowe, czy też chcesz delegować te operacje na platformę Azure.

Podczas podejmowania decyzji należy wziąć pod uwagę następujące dwie opcje:

- **Azure Database for MySQL**. Ta opcja to w pełni zarządzany aparat bazy danych MySQL oparty na stabilnej wersji programu MySQL Community Edition. Ta relacyjna baza danych jako usługa (DBaaS) hostowana na platformie Azure Cloud Platform znajduje się w kategorii branża PaaS.

  W przypadku wystąpienia zarządzanego programu MySQL na platformie Azure można używać wbudowanych funkcji, takich jak zautomatyzowane stosowanie poprawek, wysoka dostępność, zautomatyzowane kopie zapasowe, elastyczne skalowanie, zabezpieczenia klasy korporacyjnej, zgodność i zarządzanie, monitorowanie i alerty, które w przeciwnym razie wymagają obszernej konfiguracji, gdy serwer MySQL jest lokalnie lub na maszynie wirtualnej platformy Azure. W przypadku korzystania z programu MySQL jako usługi jest to płatność zgodnie z rzeczywistym użyciem z opcjami skalowania w górę lub w poziomie w celu zapewnienia większej kontroli bez przerw w działaniu. 
  
  [Azure Database for MySQL](overview.md)obsługiwane przez program MySQL Community Edition jest dostępny w dwóch trybach wdrożenia:
    - [Pojedynczy serwer](single-server-overview.md) to w pełni zarządzana usługa bazy danych o minimalnych wymaganiach dotyczących dostosowań bazy danych. Platforma jednoserwerowa została zaprojektowana tak, aby obsługiwała większość funkcji zarządzania bazami danych, takich jak stosowanie poprawek, kopii zapasowych, wysoka dostępność, zabezpieczenia z minimalną konfiguracją i kontrolą użytkownika. Architektura jest zoptymalizowana pod kątem zapewnienia dostępności na 99,99% w ramach pojedynczej strefy dostępności. Pojedyncze serwery najlepiej nadają się do obsługi natywnych aplikacji w chmurze, które mają obsługiwać automatyczne stosowanie poprawek, bez konieczności przeprowadzania szczegółowej kontroli nad harmonogramem poprawek i niestandardowych ustawień konfiguracji programu MySQL. 
    
    - [Elastyczny serwer (wersja zapoznawcza)](flexible-server/overview.md) to w pełni zarządzana usługa bazy danych, która umożliwia dokładniejszą kontrolę i elastyczność w zakresie funkcji zarządzania bazami danych i ustawień konfiguracji. Ogólnie rzecz biorąc, usługa zapewnia większą elastyczność i dostosowanie konfiguracji serwera w porównaniu z wdrożeniem pojedynczego serwera na podstawie wymagań użytkownika. Elastyczna architektura serwera umożliwia użytkownikom wybór wysokiej dostępności w ramach jednej strefy dostępności i wielu stref dostępności. Elastyczne serwery zapewniają również lepszą kontrolę kosztów dzięki możliwości uruchamiania/zatrzymywania serwerów oraz magazynowych jednostek SKU, idealnym rozwiązaniem w przypadku obciążeń, które nie wymagają ciągłej pojemności obliczeniowej. 
    Serwery elastyczne najlepiej nadają się do:
     
      - Programowanie aplikacji wymaga lepszej kontroli i dostosowań aparatu MySQL.
      - Strefa nadmiarowa wysokiej dostępności
      - Zarządzane okna obsługi

- **Baza danych MySQL na maszynach wirtualnych platformy Azure**. Ta opcja znajduje się w kategorii branża IaaS. Za pomocą tej usługi można uruchomić serwer MySQL wewnątrz zarządzanej maszyny wirtualnej na platformie Azure w chmurze. Wszystkie najnowsze wersje i wersje programu MySQL można zainstalować na maszynie wirtualnej.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>Porównanie opcji wdrażania programu MySQL na platformie Azure

Główne różnice między tymi opcjami są wymienione w poniższej tabeli:


| Atrybut          | Azure Database for MySQL<br/>Pojedynczy serwer |Azure Database for MySQL<br/>Elastyczny serwer  |Baza danych MySQL na maszynach wirtualnych platformy Azure                      |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:---------------------------------------|
| Obsługa wersji MySQL | 5,6, 5,7 & 8,0| 5.7 | Dowolna wersja|
| Skalowanie obliczeniowe | Obsługiwane (skalowanie od i do warstwy Podstawowa nie jest obsługiwane)| Obsługiwane | Obsługiwane|
| Rozmiar magazynu | 5 GiB do 16 TiB| 5 GiB do 16 TiB | 32 GiB do 32 767 GiB|
| Skalowanie magazynu online | Obsługiwane| Obsługiwane| Nieobsługiwane|
| Automatyczne skalowanie magazynu | Obsługiwane| Nieobsługiwane w wersji zapoznawczej| Nieobsługiwane|
| Łączność sieciowa | — Publiczne punkty końcowe z zaporą serwera.<br/> — Prywatny dostęp z obsługą linku prywatnego.|— Publiczne punkty końcowe z zaporą serwera.<br/> -Prywatny dostęp z integracją Virtual Network.| — Publiczne punkty końcowe z zaporą serwera.<br/> — Prywatny dostęp z obsługą linku prywatnego.|
| Umowa dotycząca poziomu usług (SLA) | dostępność na 99,99% umowy SLA |Brak umowy SLA w wersji zapoznawczej| 99,99% przy użyciu Strefy dostępności|
| Stosowanie poprawek systemu operacyjnego| Automatyczny  | Automatyczna z kontrolką okna obsługi niestandardowej | Zarządzane przez użytkowników końcowych |
| Stosowanie poprawek MySQL     | Automatyczny  | Automatyczna z kontrolką okna obsługi niestandardowej | Zarządzane przez użytkowników końcowych |
| Wysoka dostępność | Wbudowana HA w ramach pojedynczej strefy dostępności| Wbudowana HA w ramach i między strefami dostępności | Niestandardowe zarządzane przy użyciu klastrowania, replikacja itp.|
| Nadmiarowość stref | Nieobsługiwane | Obsługiwane | Obsługiwane|
| Scenariusze hybrydowe | Obsługiwane w przypadku [replikacja typu Data-in](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)| Niedostępne w wersji zapoznawczej | Zarządzane przez użytkowników końcowych |
| Repliki do odczytu | Obsługiwane| Obsługiwane | Zarządzane przez użytkowników końcowych |
| Backup | Okresowe przechowywanie z 7-35 dni | Okresowe przechowywanie z 1-35 dni | Zarządzane przez użytkowników końcowych |
| Operacje monitorowania bazy danych | Obsługiwane | Obsługiwane | Zarządzane przez użytkowników końcowych |
| Odzyskiwanie po awarii | Obsługiwane z geograficznie nadmiarowym magazynem kopii zapasowych i replikami odczytu między regionami | Nieobsługiwane w wersji zapoznawczej| Niestandardowe zarządzane przy użyciu technologii replikacji |
| Analiza wydajności zapytań | Obsługiwane | Niedostępne w wersji zapoznawczej| Zarządzane przez użytkowników końcowych |
| Cennik wystąpienia zarezerwowanego | Obsługiwane | Niedostępne w wersji zapoznawczej | Obsługiwane |
| Uwierzytelnianie w usłudze Azure AD | Obsługiwane | Niedostępne w wersji zapoznawczej | Nieobsługiwane|
| Szyfrowanie danych magazynowanych | Obsługiwane przez klucze zarządzane przez klienta | Obsługiwane z użyciem kluczy zarządzanych przez usługę | Nieobsługiwane|
| Protokół SSL/TLS | Domyślnie włączona z obsługą protokołu TLS v 1.2, 1,1 i 1,0 | Wymuszone przy użyciu protokołu TLS v 1.2 | Obsługiwane w przypadku protokołu TLS v 1.2, 1,1 i 1,0 | 
| Zarządzanie flotą | Obsługiwane za pomocą interfejsu wiersza polecenia platformy Azure, programu PowerShell, REST i Azure Resource Manager | Obsługiwane za pomocą interfejsu wiersza polecenia platformy Azure, programu PowerShell, REST i Azure Resource Manager  | Obsługiwane w przypadku maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, REST i Azure Resource Manager |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motywacje biznesowe do wyboru PaaS lub IaaS

Istnieje kilka czynników, które mogą mieć wpływ na decyzję, aby wybrać PaaS lub IaaS do hostowania baz danych MySQL.

### <a name="cost"></a>Koszty

Zmniejszenie kosztów jest często podstawowym zagadnieniem, które określa najlepsze rozwiązanie do hostowania baz danych. Jest to prawdziwe, niezależnie od tego, czy jesteś w trakcie uruchamiania z małym środkiem pieniężnym czy zespołem w firmie, który działa w ramach ścisłych ograniczeń budżetowych. W tej sekcji opisano podstawowe informacje dotyczące rozliczeń i licencjonowania na platformie Azure, które mają zastosowanie do Azure Database for MySQL i MySQL na maszynach wirtualnych platformy Azure.

#### <a name="billing"></a>Rozliczenia

Azure Database for MySQL jest obecnie dostępna jako usługa w kilku warstwach z różnymi cenami dla zasobów. Wszystkie zasoby są rozliczane godzinowo według ustalonej stawki. Najnowsze informacje dotyczące aktualnie obsługiwanych warstw usług, rozmiarów obliczeń i ilości miejsca do magazynowania znajdują się na [stronie cennika](https://azure.microsoft.com/pricing/details/mysql/). Możesz dynamicznie dostosować warstwy usług i rozmiary obliczeń, aby odpowiadały potrzebom różnych przepływności aplikacji. Opłaty są naliczane za wychodzący ruch internetowy przy zwykłych [stawkach za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/).

W przypadku Azure Database for MySQL firma Microsoft automatycznie konfiguruje, poprawki i uaktualnia oprogramowanie bazy danych. Te automatyczne akcje zmniejszają koszty administracyjne. Ponadto Azure Database for MySQL ma możliwości [automatycznego tworzenia kopii zapasowych](https://docs.microsoft.com/azure/mysql/concepts-backup) . Te funkcje pomagają osiągnąć znaczący koszt oszczędności, zwłaszcza w przypadku dużej liczby baz danych. W przeciwieństwie do programu MySQL na maszynach wirtualnych platformy Azure można wybrać i uruchomić dowolną wersję programu MySQL. Niezależnie od używanej wersji programu MySQL można płacić za zainicjowaną maszynę wirtualną, koszt magazynu związany z danymi, kopiami zapasowymi, danymi monitorowania i magazynem dzienników oraz kosztami określonego typu licencji programu MySQL (jeśli istnieje).

Azure Database for MySQL zapewnia wbudowaną wysoką dostępność dla dowolnego rodzaju przerw na poziomie węzła, utrzymując gwarancję SLA na 99,99% dla usługi. Jednak aby zapewnić wysoką dostępność bazy danych w ramach maszyn wirtualnych, należy użyć opcji wysokiej dostępności, takich jak [replikacja MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication.html) , która jest dostępna w bazie danych MySQL. Korzystanie z obsługiwanej opcji wysokiej dostępności nie zapewnia dodatkowej umowy SLA. Jednak pozwala to osiągnąć więcej niż 99,99% dostępności bazy danych przy dodatkowych kosztach i obciążeniu administracyjnym.

Aby uzyskać więcej informacji na temat cen, zobacz następujące artykuły:
* [Cennik Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/)
* [Cennik maszyny wirtualnej](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administracja

W przypadku wielu firm decyzja o przejściu do usługi w chmurze polega na tym, że jest to bardzo dużo na odciążeniu złożoności administracji, ponieważ ma ona koszt. 

W programie IaaS firmy Microsoft:

- Zarządza podstawową infrastrukturą.
- Zapewnia zautomatyzowane stosowanie poprawek dla podstawowego sprzętu i systemu operacyjnego.
  
W programie PaaS firmy Microsoft:

- Zarządza podstawową infrastrukturą.
- Zapewnia zautomatyzowane stosowanie poprawek dla podstawowego sprzętu, systemu operacyjnego i aparatu bazy danych.
- Zarządza wysoką dostępnością bazy danych.
- Automatycznie wykonuje kopie zapasowe i replikuje wszystkie dane w celu zapewnienia odzyskiwania po awarii.
- Domyślnie szyfruje dane przechowywane i ruchowo.
- Monitoruje serwer i oferuje funkcje do uzyskiwania szczegółowych informacji o wydajności zapytań i wydajności

Na poniższej liście opisano zagadnienia dotyczące zagadnień administracyjnych dla każdej z nich:

* Za pomocą Azure Database for MySQL można nadal administrować swoją bazą danych. Nie trzeba jednak już zarządzać aparatem bazy danych, systemem operacyjnym ani sprzętem. Przykłady elementów, którymi można zarządzać, to m.in.:

  - Bazy danych
  - Logowanie
  - Dostrajanie indeksu
  - Dostrajanie zapytania
  - Inspekcja
  - Zabezpieczenia

  Ponadto skonfigurowanie wysokiej dostępności w innym centrum danych wymaga minimalnej konfiguracji lub jej administracji.

* Za pomocą programu MySQL na maszynach wirtualnych platformy Azure masz pełną kontrolę nad systemem operacyjnym i konfiguracją wystąpienia serwera MySQL. W przypadku maszyny wirtualnej należy określić, kiedy należy zaktualizować lub uaktualnić system operacyjny i oprogramowanie bazy danych oraz jakie poprawki mają być stosowane. Należy również określić, kiedy zainstalować dodatkowe oprogramowanie, np. aplikację antywirusową. Niektóre funkcje automatyczne zapewniają znacznie uproszczenie stosowania poprawek, kopii zapasowych i wysokiej dostępności. Można kontrolować rozmiar maszyny wirtualnej, liczbę dysków i ich konfiguracje magazynu. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych i usług w chmurze dla platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="time-to-move-to-azure"></a>Czas przejścia na platformę Azure

* Azure Database for MySQL to odpowiednie rozwiązanie dla aplikacji zaprojektowanych w chmurze, gdy produktywność deweloperów i krótki czas wprowadzenia na rynek nowych rozwiązań mają kluczowe znaczenie. Dzięki funkcjom programistycznym, takim jak DBA, usługa jest odpowiednia dla architektów i deweloperów rozwiązań w chmurze, ponieważ obniża potrzebę zarządzania bazowym systemem operacyjnym i bazą danych.

* Aby uniknąć czasu i wydatków pozyskiwania nowego sprzętu lokalnego, baza danych MySQL na maszynach wirtualnych platformy Azure to odpowiednie rozwiązanie dla aplikacji wymagających szczegółowej kontroli i dostosowywania aparatu MySQL nieobsługiwanego przez usługę lub wymagającego dostępu do podstawowego systemu operacyjnego. To rozwiązanie jest również odpowiednie do migrowania istniejących lokalnych aplikacji i baz danych na platformę Azure bez zmian, w przypadku których Azure Database for MySQL jest słabo dopasowany.

Ze względu na to, że nie trzeba zmieniać warstw prezentacji, aplikacji i danych, możesz zaoszczędzić czas i budżet na potrzeby ponownej architektury istniejącego rozwiązania. Zamiast tego można skupić się na migrowaniu wszystkich rozwiązań na platformę Azure i rozwiązaniu niektórych optymalizacji wydajności, które mogą być wymagane przez platformę Azure.

## <a name="next-steps"></a>Następne kroki

* Zobacz [cennik Azure Database for MySQL](https://azure.microsoft.com/pricing/details/MySQL/).
* Zacznij od [utworzenia pierwszego serwera](https://docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal).

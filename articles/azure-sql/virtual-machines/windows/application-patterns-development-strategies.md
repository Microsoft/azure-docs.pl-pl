---
title: SQL Server wzorców aplikacji na maszynach wirtualnych | Microsoft Docs
description: W tym artykule opisano wzorce aplikacji dla SQL Server w usłudze Azure Virtual Machines. Zapewnia architektom rozwiązań i deweloperom podstawę dla dobrej architektury i projektowania aplikacji.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mathoma
ms.openlocfilehash: 46adbfee24ab463acdc4687c0465bbf50527a329
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790648"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-on-azure-virtual-machines"></a>Wzorce aplikacji i strategie programowania dla SQL Server na platformie Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Podsumowanie:
Określanie wzorca lub wzorców aplikacji, które mają być używane dla aplikacji opartych na SQL Serverach w środowisku platformy Azure, jest ważną decyzją projektową i wymaga pełnego poznania sposobu, w jaki SQL Server i każdy składnik infrastruktury platformy Azure współpracują ze sobą. Dzięki SQL Server w usługach infrastruktury platformy Azure można łatwo migrować, konserwować i monitorować istniejące aplikacje SQL Server utworzone w systemie Windows Server na maszynach wirtualnych na platformie Azure.

Celem tego artykułu jest zapewnienie architektów rozwiązań i deweloperów podstaw dla dobrej architektury i projektowania aplikacji, które mogą być stosowane podczas migrowania istniejących aplikacji na platformę Azure, a także tworzenia nowych aplikacji na platformie Azure.

Dla każdego wzorca aplikacji znajduje się scenariusz lokalny, jego odpowiednie rozwiązanie z obsługą chmury oraz powiązane zalecenia techniczne. Ponadto w artykule omówiono strategie deweloperskie specyficzne dla platformy Azure, dzięki którym można prawidłowo projektować aplikacje. Ze względu na wiele możliwych wzorców aplikacji zaleca się, aby architektzy i deweloperzy mogli wybrać najbardziej odpowiedni wzorzec dla swoich aplikacji i użytkowników.

**Współautorzy techniczni:** Luis Carlos Vargas śledzi, Madhan Arumugam Ramakrishnan

**Recenzenci techniczni:** Corey Sanders, McDaniel, Narayan Annamalai, imię Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Wprowadzenie
Można opracowywać wiele typów aplikacji n-warstwowych, oddzielając składniki różnych warstw aplikacji na różnych maszynach, a także w oddzielnych składnikach. Można na przykład umieścić składniki klienta i reguły biznesowe w jednej maszynie, warstwie sieci Web frontonu i składnikach warstwy dostępu do danych na innej maszynie, a warstwa bazy danych zaplecza w innej maszynie. Ten rodzaj struktury ułatwia odizolowanie każdej warstwy od siebie. Jeśli zmienisz miejsce, z którego pochodzą dane, nie musisz zmieniać klienta ani aplikacji sieci Web, ale tylko składników warstwy dostępu do danych.

Typowa aplikacja *n-warstwowa* zawiera warstwę prezentacji, warstwę biznesową i warstwę danych:

| Warstwa | Opis |
| --- | --- |
| **Prezentacja** |Warstwa *prezentacji* (warstwa sieci Web, warstwa frontonu) to warstwa, w której użytkownicy współpracują z aplikacją. |
| **Firmowe** |Warstwa *biznesowa* (warstwa środkowa) to warstwa, z której warstwa prezentacji i warstwa danych używają do komunikacji między sobą i obejmuje podstawowe funkcje systemu. |
| **Dane** |*Warstwa danych* jest zasadniczo serwerem, w którym są przechowywane dane aplikacji (na przykład serwer z uruchomionym SQL Server). |

Warstwy aplikacji opisują logiczne grupy funkcji i składników w aplikacji; w warstwach opisano fizyczną dystrybucję funkcji i składników na oddzielnych serwerach fizycznych, komputerach, sieciach lub lokalizacjach zdalnych. Warstwy aplikacji mogą znajdować się na tym samym komputerze fizycznym (w tej samej warstwie) lub mogą być rozproszone na oddzielnych komputerach (n-warstwowej), a składniki w poszczególnych warstwach komunikują się ze składnikami w innych warstwach za pomocą dobrze zdefiniowanych interfejsów. Możesz traktować warstwę warunków jako odnoszącą się do wzorców dystrybucji fizycznej, takich jak dwie warstwy, trzy warstwy i n-warstwowa. **Wzorzec dwuwarstwowej aplikacji** zawiera dwie warstwy aplikacji: serwer aplikacji i serwer baz danych. Bezpośrednia komunikacja odbywa się między serwerem aplikacji a serwerem bazy danych. Serwer aplikacji zawiera składniki warstwy sieci Web i warstwy biznesowej. W przypadku **wzorca aplikacji 3-warstwowej** istnieją trzy warstwy aplikacji: serwer sieci Web, serwer aplikacji, który zawiera warstwę logiki biznesowej i/lub składniki dostępu do danych w warstwie biznesowej oraz serwer bazy danych. Komunikacja między serwerem sieci Web a serwerem bazy danych odbywa się za pośrednictwem serwera aplikacji. Aby uzyskać szczegółowe informacje na temat warstw aplikacji i warstw, zobacz [Przewodnik po architekturze aplikacji firmy Microsoft](/previous-versions/msp-n-p/ff650706(v=pandp.10)).

Przed rozpoczęciem odczytywania tego artykułu należy znać podstawowe koncepcje SQL Server i platformy Azure. Aby uzyskać więcej informacji, zobacz [SQL Server Books Online](/sql/sql-server/), [SQL Server na platformie Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) i [Azure.com](https://azure.microsoft.com/).

W tym artykule opisano kilka wzorców aplikacji, które mogą być odpowiednie dla aplikacji prostych oraz wysoce złożonych aplikacji dla przedsiębiorstw. Przed szczegółami dotyczącymi poszczególnych wzorców zalecamy zapoznanie się z dostępnymi usługami magazynu danych na platformie Azure, takimi jak [Azure Storage](../../../storage/common/storage-introduction.md), [Azure SQL Database](../../database/sql-database-paas-overview.md)i [SQL Server na maszynie wirtualnej platformy Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Aby zapewnić najlepszą decyzję projektową dla aplikacji, należy zrozumieć, kiedy należy używać usługi magazynu danych jasno.

### <a name="choose-sql-server-on-azure-virtual-machines-when"></a>Wybierz SQL Server w usłudze Azure Virtual Machines, gdy:

* Musisz mieć kontrolę nad SQL Server i Windows. Przykładowo może to być wersja SQL Server, specjalna poprawka, Konfiguracja wydajności itp.
* Wymagana jest pełna zgodność z SQL Server i chcemy przenieść istniejące aplikacje na platformę Azure jako-is.
* Chcesz korzystać z możliwości środowiska platformy Azure, ale Azure SQL Database nie obsługują wszystkich funkcji wymaganych przez aplikację. Może to obejmować następujące obszary:
  
  * **Rozmiar bazy danych** : w momencie aktualizacji tego artykułu SQL Database obsługuje bazę danych o rozmiarze do 1 TB. Jeśli aplikacja wymaga więcej niż 1 TB danych i nie chcesz zaimplementować niestandardowych rozwiązań fragmentowania, zaleca się używanie SQL Server na maszynie wirtualnej platformy Azure. Aby uzyskać najnowsze informacje, zobacz [skalowanie](/previous-versions/azure/dn495641(v=azure.100))w poziomie Azure SQL Database, [model zakupów oparty na](../../database/service-tiers-dtu.md)jednostkach DTU oraz [model zakupu oparty na rdzeń wirtualny](../../database/service-tiers-vcore.md)(wersja zapoznawcza).
  * **HIPAA zgodność** : klienci usługi opieki zdrowotnej i niezależni dostawcy oprogramowania (ISV) mogą wybrać [SQL Server na platformie azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) zamiast [Azure SQL Database](../../database/sql-database-paas-overview.md) , ponieważ SQL Server na platformie Azure Virtual Machines jest objęty umową Business Partner HIPAA (BAA). Aby uzyskać informacje na temat zgodności, zobacz [Microsoft Azure Centrum zaufania: zgodność](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Funkcje na poziomie wystąpienia** : w tej chwili SQL Database nie obsługuje funkcji, które znajdują się na żywo poza bazą danych (np. połączone serwery, zadania agenta, FileStream, Service Broker itp.). Aby uzyskać więcej informacji, zobacz [Azure SQL Database wytycznych i ograniczeń](/previous-versions/azure/ff394102(v=azure.100)).

## <a name="1-tier-simple-single-virtual-machine"></a>1 — warstwa (prosta): Pojedyncza maszyna wirtualna
W tym wzorcu aplikacji wdrażasz aplikację SQL Server i bazę danych na autonomiczną maszynę wirtualną na platformie Azure. Ta sama maszyna wirtualna zawiera aplikację klienta/sieci Web, składniki biznesowe, warstwę dostępu do danych i serwer bazy danych. Prezentacja, biznes i kod dostępu do danych są logicznie oddzielone, ale fizycznie znajdują się na komputerze z jednym serwerem. Większość klientów zaczyna się od tego wzorca aplikacji, a następnie skalowanie w poziomie przez dodanie większej liczby ról sieci Web lub maszyn wirtualnych do ich systemu.

Ten wzorzec aplikacji jest przydatny w przypadku:

* Chcesz przeprowadzić prostą migrację do platformy Azure, aby sprawdzić, czy platforma odbierze wymagania aplikacji.
* Chcesz zachować wszystkie warstwy aplikacji hostowane na tej samej maszynie wirtualnej w tym samym centrum danych platformy Azure, aby zmniejszyć opóźnienie między warstwami.
* Chcesz szybko zainicjować obsługę środowiska deweloperskiego i testowego przez krótki okres czasu.
* Chcesz przeprowadzić testowanie obciążeniowe dla różnych poziomów obciążenia, ale w tym samym czasie nie chcesz, aby na wielu maszynach fizycznych były przechowywane i utrzymywane przez cały czas.

Na poniższym diagramie przedstawiono prosty scenariusz lokalny oraz sposób wdrożenia rozwiązania z obsługą chmury w ramach jednej maszyny wirtualnej na platformie Azure.

![wzorzec jednowarstwowej aplikacji](./media/application-patterns-development-strategies/IC728008.png)

Wdrożenie warstwy biznesowej (składniki logiki biznesowej i dostępu do danych) w tej samej warstwie fizycznej co warstwa prezentacji może zmaksymalizować wydajność aplikacji, chyba że trzeba użyć oddzielnej warstwy z powodu skalowalności lub problemów z bezpieczeństwem.

Ponieważ jest to bardzo typowy wzorzec, który zaczyna się od, można znaleźć następujący artykuł dotyczący migracji, przydatny do przenoszenia danych do maszyny wirtualnej SQL Server: [Migrowanie bazy danych do SQL Server na maszynie wirtualnej platformy Azure](migrate-to-vm-from-sql-server.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3 — warstwa (prosta): wiele maszyn wirtualnych
W tym wzorcu aplikacji wdrażasz 3-warstwową aplikację na platformie Azure, umieszczając każdą warstwę aplikacji w innej maszynie wirtualnej. Zapewnia to elastyczne środowisko umożliwiające łatwe skalowanie w górę i w poziomie. Jeśli jedna maszyna wirtualna zawiera aplikację klienta/sieci Web, drugi Host udostępnia składniki biznesowe, a drugi hostuje serwer bazy danych.

Ten wzorzec aplikacji jest przydatny w przypadku:

* Chcesz przeprowadzić migrację złożonych aplikacji baz danych do usługi Azure Virtual Machines.
* Różne warstwy aplikacji mają być hostowane w różnych regionach. Można na przykład udostępnić udostępnione bazy danych wdrożone w wielu regionach na potrzeby raportowania.
* Chcesz przenieść aplikacje dla przedsiębiorstw z lokalnych platform zwirtualizowanych do usługi Azure Virtual Machines. Aby uzyskać szczegółową dyskusję na temat aplikacji dla przedsiębiorstw, zobacz [co to jest aplikacja dla przedsiębiorstw](/previous-versions/visualstudio/aa267045(v=vs.60)).
* Chcesz szybko zainicjować obsługę środowiska deweloperskiego i testowego przez krótki okres czasu.
* Chcesz przeprowadzić testowanie obciążeniowe dla różnych poziomów obciążenia, ale w tym samym czasie nie chcesz, aby na wielu maszynach fizycznych były przechowywane i utrzymywane przez cały czas.

Na poniższym diagramie przedstawiono sposób umieszczania prostej aplikacji 3-warstwowej na platformie Azure przez umieszczenie każdej warstwy aplikacji na innej maszynie wirtualnej.

![Wzorzec aplikacji 3-warstwowej](./media/application-patterns-development-strategies/IC728009.png)

W tym wzorcu aplikacji istnieje tylko jedna maszyna wirtualna w każdej warstwie. Jeśli masz wiele maszyn wirtualnych na platformie Azure, zalecamy skonfigurowanie sieci wirtualnej. [Usługa Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) tworzy zaufaną granicę zabezpieczeń, a także umożliwia maszynom wirtualnym komunikowanie się między sobą za pośrednictwem prywatnego adresu IP. Ponadto zawsze upewnij się, że wszystkie połączenia internetowe przejdą do warstwy prezentacji. Gdy ten wzorzec aplikacji jest następujący, Zarządzaj regułami sieciowych grup zabezpieczeń, aby kontrolować dostęp. Aby uzyskać więcej informacji, zobacz [Zezwalanie na dostęp zewnętrzny do maszyny wirtualnej przy użyciu Azure Portal](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Na diagramie mogą być obsługiwane protokoły TCP, UDP, HTTP lub HTTPS.

> [!NOTE]
> Konfigurowanie sieci wirtualnej na platformie Azure jest bezpłatne. Jest jednak naliczana opłata za bramę sieci VPN, która nawiązuje połączenie z lokalną usługą. Ta opłata jest naliczana na podstawie czasu, przez jaki połączenie jest obsługiwane i dostępne.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>skalowalne w poziomie warstwy 2 i 3 warstwy prezentacji
W tym wzorcu aplikacji wdrażana jest 2-warstwowa lub 3-warstwowa aplikacja bazy danych na platformie Azure Virtual Machines przez umieszczenie każdej warstwy aplikacji na innej maszynie wirtualnej. Ponadto można skalować warstwę prezentacji ze względu na zwiększoną ilość przychodzących żądań klientów.

Ten wzorzec aplikacji jest przydatny w przypadku:

* Chcesz przenieść aplikacje dla przedsiębiorstw z lokalnych platform zwirtualizowanych do usługi Azure Virtual Machines.
* Chcesz skalować warstwę prezentacji ze względu na zwiększoną ilość przychodzących żądań klientów.
* Chcesz szybko zainicjować obsługę środowiska deweloperskiego i testowego przez krótki okres czasu.
* Chcesz przeprowadzić testowanie obciążeniowe dla różnych poziomów obciążenia, ale w tym samym czasie nie chcesz, aby na wielu maszynach fizycznych były przechowywane i utrzymywane przez cały czas.
* Chcesz utworzyć środowisko infrastruktury, które można skalować w górę i w dół na żądanie.

Na poniższym diagramie przedstawiono sposób umieszczania warstw aplikacji na wielu maszynach wirtualnych na platformie Azure przez skalowanie warstwy prezentacji ze względu na zwiększoną ilość przychodzących żądań klientów. Jak widać na diagramie, Azure Load Balancer jest odpowiedzialny za dystrybuowanie ruchu między wieloma maszynami wirtualnymi, a także Określanie serwera sieci Web, z którym ma zostać nawiązane połączenie. Posiadanie wielu wystąpień serwerów sieci Web za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwy prezentacji.

![Wzorzec aplikacji — skalowanie warstwy prezentacji](./media/application-patterns-development-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Najlepsze rozwiązania dla wzorców dwuwarstwowych, 3-warstwowych lub n-warstwowych, które mają wiele maszyn wirtualnych w jednej warstwie
Zaleca się umieszczenie maszyn wirtualnych należących do tej samej warstwy w tej samej usłudze w chmurze i w tym samym zestawie dostępności. Na przykład Umieść zestaw serwerów sieci Web w **CloudService1** i **AvailabilitySet1** oraz zestaw serwerów baz danych w **CloudService2** i **AvailabilitySet2** . Zestaw dostępności na platformie Azure umożliwia umieszczenie węzłów o wysokiej dostępności w oddzielnych domenach błędów i domenach uaktualnienia.

Aby korzystać z wielu wystąpień maszyn wirtualnych w warstwie, należy skonfigurować Azure Load Balancer między warstwami aplikacji. Aby skonfigurować Load Balancer w każdej warstwie, należy oddzielnie utworzyć punkt końcowy o zrównoważonym obciążeniu na maszynach wirtualnych każdej warstwy. W przypadku określonej warstwy należy najpierw utworzyć maszyny wirtualne w tej samej usłudze w chmurze. Daje to pewność, że mają one ten sam publiczny wirtualny adres IP. Następnie Utwórz punkt końcowy na jednej z maszyn wirtualnych w tej warstwie. Następnie przypisz ten sam punkt końcowy do innych maszyn wirtualnych w tej warstwie na potrzeby równoważenia obciążenia. Tworząc zestaw o zrównoważonym obciążeniu, można dystrybuować ruch między wieloma maszynami wirtualnymi, a także zezwalać Load Balancer na określenie, który węzeł ma nawiązać połączenie w przypadku awarii węzła maszyny wirtualnej zaplecza. Na przykład posiadanie wielu wystąpień serwerów sieci Web za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwy prezentacji.

Najlepszym rozwiązaniem jest upewnienie się, że wszystkie połączenia z Internetem najpierw przejdą do warstwy prezentacji. Warstwa prezentacji uzyskuje dostęp do warstwy biznesowej, a następnie warstwa biznesowa uzyskuje dostęp do warstwy danych. Aby uzyskać więcej informacji na temat zezwalania na dostęp do warstwy prezentacji, zobacz [Zezwalanie na dostęp zewnętrzny do maszyny wirtualnej przy użyciu Azure Portal](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Należy pamiętać, że Load Balancer na platformie Azure działa podobnie do modułów równoważenia obciążenia w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz [równoważenie obciążenia dla usług infrastruktury platformy Azure](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ponadto zalecamy skonfigurowanie sieci prywatnej dla maszyn wirtualnych przy użyciu usługi Azure Virtual Network. Dzięki temu można komunikować się między sobą za pośrednictwem prywatnego adresu IP. Aby uzyskać więcej informacji, zobacz [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>skalowanie w poziomie warstwy 2 i 3 warstwy biznesowej
W tym wzorcu aplikacji należy wdrożyć wielowarstwową lub 3-warstwową aplikację bazy danych na platformie Azure Virtual Machines przez umieszczenie każdej warstwy aplikacji na innej maszynie wirtualnej. Ponadto może być konieczne dystrybuowanie składników serwera aplikacji do wielu maszyn wirtualnych ze względu na złożoność aplikacji.

Ten wzorzec aplikacji jest przydatny w przypadku:

* Chcesz przenieść aplikacje dla przedsiębiorstw z lokalnych platform zwirtualizowanych do usługi Azure Virtual Machines.
* Chcesz dystrybuować składniki serwera aplikacji do wielu maszyn wirtualnych ze względu na złożoność aplikacji.
* Chcesz przenieść logiki biznesowej duże lokalne aplikacje LOB (biznesowe) do usługi Azure Virtual Machines. Aplikacje LOB to zestaw krytycznych aplikacji komputerowych, które są niezbędne do uruchamiania przedsiębiorstwa, takie jak ewidencjonowanie aktywności, zasoby ludzkie (HR), płace, zarządzanie łańcuchem dostaw i aplikacje do planowania zasobów.
* Chcesz szybko zainicjować obsługę środowiska deweloperskiego i testowego przez krótki okres czasu.
* Chcesz przeprowadzić testowanie obciążeniowe dla różnych poziomów obciążenia, ale w tym samym czasie nie chcesz, aby na wielu maszynach fizycznych były przechowywane i utrzymywane przez cały czas.
* Chcesz utworzyć środowisko infrastruktury, które można skalować w górę i w dół na żądanie.

Na poniższym diagramie przedstawiono scenariusz lokalny i rozwiązanie z obsługą chmury. W tym scenariuszu warstwy aplikacji są umieszczane na wielu maszynach wirtualnych na platformie Azure przez skalowanie w poziomie warstwy biznesowej, która zawiera warstwę logiki biznesowej i składniki dostępu do danych. Jak widać na diagramie, Azure Load Balancer jest odpowiedzialny za dystrybuowanie ruchu między wieloma maszynami wirtualnymi, a także Określanie serwera sieci Web, z którym ma zostać nawiązane połączenie. Posiadanie wielu wystąpień serwerów aplikacji za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwy biznesowej. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dla wzorców aplikacji dwuwarstwowych, 3-warstwowych lub n-warstwowych, które mają wiele maszyn wirtualnych w jednej warstwie](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Wzorzec aplikacji z warstwą biznesową skalowanie w poziomie](./media/application-patterns-development-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>warstwa 2-warstwowa i 3-warstwowa z prezentacją i warstwami biznesowymi skalowalnymi w poziomie i HADR Cluster
W tym wzorcu aplikacji wdrażana jest dwuwarstwowa lub 3-warstwowa aplikacja bazy danych na platformie Azure Virtual Machines przez dystrybuowanie warstwy prezentacji (serwera sieci Web) i składników warstwy biznesowej (serwera aplikacji) do wielu maszyn wirtualnych. Dodatkowo wdrożono rozwiązania wysokiej dostępności i odzyskiwania po awarii (HADR cluster) dla baz danych w usłudze Azure Virtual Machines.

Ten wzorzec aplikacji jest przydatny w przypadku:

* Chcesz przenieść aplikacje dla przedsiębiorstw z zwirtualizowanych platform do platformy Azure, implementując SQL Server możliwości wysokiej dostępności i odzyskiwania po awarii.
* Chcesz skalować warstwę prezentacji i warstwę biznesową ze względu na zwiększoną ilość przychodzących żądań klientów i złożoność aplikacji.
* Chcesz szybko zainicjować obsługę środowiska deweloperskiego i testowego przez krótki okres czasu.
* Chcesz przeprowadzić testowanie obciążeniowe dla różnych poziomów obciążenia, ale w tym samym czasie nie chcesz, aby na wielu maszynach fizycznych były przechowywane i utrzymywane przez cały czas.
* Chcesz utworzyć środowisko infrastruktury, które można skalować w górę i w dół na żądanie.

Na poniższym diagramie przedstawiono scenariusz lokalny i rozwiązanie z obsługą chmury. W tym scenariuszu można skalować warstwę prezentacji oraz składniki warstwy biznesowej na wielu maszynach wirtualnych na platformie Azure. Dodatkowo wdrożono techniki wysokiej dostępności i odzyskiwania po awarii (HADR cluster) dla baz danych SQL Server na platformie Azure.

Uruchamianie wielu kopii aplikacji na różnych maszynach wirtualnych upewnij się, że są na nich przesyłane żądania równoważenia obciążenia. Jeśli masz wiele maszyn wirtualnych, musisz upewnić się, że wszystkie maszyny wirtualne są dostępne i działają w jednym punkcie w czasie. W przypadku skonfigurowania równoważenia obciążenia Azure Load Balancer śledzi kondycję maszyn wirtualnych i kieruje przychodzące wywołania do prawidłowo działających węzłów maszyny wirtualnej. Aby uzyskać informacje na temat konfigurowania równoważenia obciążenia maszyn wirtualnych, zobacz [równoważenie obciążenia dla usług infrastruktury platformy Azure](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Posiadanie wielu wystąpień serwerów sieci Web i aplikacji za modułem równoważenia obciążenia zapewnia wysoką dostępność prezentacji i warstw biznesowych.

![Skalowanie w poziomie i wysoka dostępność](./media/application-patterns-development-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Najlepsze rozwiązania dotyczące wzorców aplikacji wymagających programu SQL HADR Cluster
Po skonfigurowaniu SQL Server rozwiązania wysokiej dostępności i odzyskiwania po awarii w usłudze Azure Virtual Machines skonfigurowanie sieci wirtualnej dla maszyn wirtualnych przy użyciu [usługi azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) jest obowiązkowe. Maszyny wirtualne w ramach Virtual Network będą mieć stabilny prywatny adres IP nawet po przestoju usługi, dzięki czemu można uniknąć czasu aktualizacji wymaganego do rozpoznawania nazw DNS. Ponadto sieć wirtualna umożliwia rozszerzonie sieci lokalnej na platformę Azure i utworzenie zaufanej granicy zabezpieczeń. Na przykład jeśli aplikacja ma ograniczenia domeny firmowej (takie jak uwierzytelnianie systemu Windows, Active Directory), wymagane jest skonfigurowanie [usługi Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) .

Większość klientów, którzy uruchamiają kod produkcyjny na platformie Azure, zachowują zarówno podstawową, jak i pomocniczą replikę na platformie Azure.

Aby uzyskać wyczerpujące informacje i samouczki dotyczące technik wysokiej dostępności i odzyskiwania po awarii, zobacz [wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="2-tier-and-3-tier-using-azure-virtual-machines-and-cloud-services"></a>warstwa 2-warstwowa i 3-warstwowa przy użyciu Virtual Machines platformy Azure i Cloud Services
W tym wzorcu aplikacji wdrażana jest aplikacja dwuwarstwowa lub 3-warstwowa na platformie Azure przy użyciu [usługi azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (role sieć Web i proces roboczy — platforma jako usługa (PaaS)) i [Azure Virtual Machines](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (infrastruktura jako usługa (IaaS)). Korzystanie z [usługi azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) dla warstwy prezentacji/warstwy biznesowej i SQL Server w [usłudze Azure Virtual Machines](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) dla warstwy danych jest korzystne dla większości aplikacji działających na platformie Azure. Przyczyną jest to, że wystąpienie obliczeniowe działające w Cloud Services zapewnia łatwe zarządzanie, wdrażanie, monitorowanie i skalowanie w poziomie.

Dzięki Cloud Services platforma Azure obsługuje infrastrukturę, przeprowadza rutynowe czynności konserwacyjne, poprawki systemów operacyjnych i próbuje odzyskać sprawność po awarii usług i sprzętu. Gdy aplikacja wymaga skalowania w poziomie, automatyczne i ręczne opcje skalowania w poziomie, są dostępne dla projektu usługi w chmurze przez zwiększenie lub zmniejszenie liczby wystąpień lub maszyn wirtualnych używanych przez aplikację. Ponadto możesz użyć lokalnego programu Visual Studio do wdrożenia aplikacji w projekcie usługi w chmurze na platformie Azure.

Podsumowując, jeśli nie chcesz mieć zadań administracyjnych, które mają być używane w przypadku prezentacji/warstwy biznesowej, a aplikacja nie wymaga żadnej złożonej konfiguracji oprogramowania lub systemu operacyjnego, Użyj usługi Azure Cloud Services. Jeśli Azure SQL Database nie obsługuje wszystkich funkcji, których szukasz, użyj SQL Server na maszynie wirtualnej platformy Azure dla warstwy danych. Uruchomienie aplikacji na platformie Azure Cloud Services i przechowywanie danych w usłudze Azure Virtual Machines wiąże się z korzyściami z obu tych usług. Szczegółowe porównanie zawiera sekcja w tym temacie dotycząca [porównywania strategii rozwoju na platformie Azure](#comparing-web-development-strategies-in-azure).

W tym wzorcu aplikacji warstwa prezentacji obejmuje rolę sieci Web, która jest składnikiem Cloud Services uruchomionym w środowisku wykonywania platformy Azure i jest dostosowywany do programowania aplikacji sieci Web jako obsługiwane przez usługi IIS i ASP.NET. Warstwa firmy lub zaplecza obejmuje rolę procesu roboczego, która jest składnikiem Cloud Services działającym w środowisku wykonywania platformy Azure i jest przydatna do tworzenia uogólnionych rozwiązań i może wykonywać przetwarzanie w tle dla roli sieci Web. Warstwa bazy danych znajduje się w SQL Server maszynie wirtualnej na platformie Azure. Komunikacja między warstwą prezentacji a warstwą bazy danych odbywa się bezpośrednio lub za pośrednictwem składników roli proces roboczy w warstwie biznesowej.

Ten wzorzec aplikacji jest przydatny w przypadku:

* Chcesz przenieść aplikacje dla przedsiębiorstw z zwirtualizowanych platform do platformy Azure, implementując SQL Server możliwości wysokiej dostępności i odzyskiwania po awarii.
* Chcesz utworzyć środowisko infrastruktury, które można skalować w górę i w dół na żądanie.
* Azure SQL Database nie obsługuje wszystkich funkcji, których potrzebuje aplikacja lub baza danych.
* Chcesz przeprowadzić testowanie obciążeniowe dla różnych poziomów obciążenia, ale w tym samym czasie nie chcesz, aby na wielu maszynach fizycznych były przechowywane i utrzymywane przez cały czas.

Na poniższym diagramie przedstawiono scenariusz lokalny i rozwiązanie z obsługą chmury. W tym scenariuszu należy umieścić warstwę prezentacji w obszarze Role sieci Web, warstwę biznesową w rolach procesów roboczych, ale warstwę danych w maszynach wirtualnych na platformie Azure. Uruchamianie wielu kopii warstwy prezentacji w różnych rolach sieci Web gwarantuje Równoważenie obciążenia między nimi żądania. W przypadku łączenia Cloud Services platformy Azure z usługą Azure Virtual Machines zalecamy również skonfigurowanie [Virtual Network platformy Azure](../../../virtual-network/virtual-networks-overview.md) . Dzięki [usłudze Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)można mieć stabilne i trwałe prywatne adresy IP w ramach tej samej usługi w chmurze w chmurze. Po zdefiniowaniu sieci wirtualnej dla maszyn wirtualnych i usług w chmurze mogą oni zacząć komunikować się między sobą za pośrednictwem prywatnego adresu IP. Oprócz tego, że maszyny wirtualne i role sieci Web/proces roboczy platformy Azure w tym samym systemie [azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) zapewniają małe opóźnienia i bardziej bezpieczne połączenia. Aby uzyskać więcej informacji, zobacz [co to jest usługa w chmurze](../../../cloud-services/cloud-services-choose-me.md).

Jak pokazano na diagramie, Azure Load Balancer dystrybuuje ruch między wieloma maszynami wirtualnymi, a także określa serwer sieci Web lub serwer aplikacji, z którymi ma zostać nawiązane połączenie. Posiadanie wielu wystąpień serwerów sieci Web i aplikacji za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwy prezentacji i warstwy biznesowej. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące wzorców aplikacji wymagających programu SQL HADR Cluster](#best-practices-for-application-patterns-requiring-sql-hadr).

![Wzorce aplikacji z Cloud Services](./media/application-patterns-development-strategies/IC728013.png)

Innym podejściem do implementowania tego wzorca aplikacji jest użycie skonsolidowanej roli sieci Web, która zawiera zarówno składniki prezentacji, jak i warstwy biznesowej, jak pokazano na poniższym diagramie. Ten wzorzec aplikacji jest przydatny w przypadku aplikacji wymagających projektu stanowego. Ponieważ platforma Azure udostępnia bezstanowe węzły obliczeniowe w roli sieć Web i proces roboczy, zalecamy wdrożenie logiki do przechowywania stanu sesji przy użyciu jednej z następujących technologii: [buforowanie platformy Azure](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [azure Table Storage](../../../cosmos-db/tutorial-develop-table-dotnet.md) lub [Azure SQL Database](../../database/sql-database-paas-overview.md).

![Wzorce aplikacji z Cloud Services](./media/application-patterns-development-strategies/IC728014.png)

## <a name="pattern-with-azure-virtual-machines-azure-sql-database-and-azure-app-service-web-apps"></a>Wzorzec z usługą Azure Virtual Machines, Azure SQL Database i Azure App Service (Web Apps)
Głównym celem tego wzorca aplikacji jest przedstawienie sposobu łączenia składników infrastruktury platformy Azure jako usługi (IaaS) z składnikami platformy platformy Azure jako usługi (PaaS) w rozwiązaniu. Ten wzorzec koncentruje się na Azure SQL Database dla magazynu danych relacyjnych. Nie obejmuje SQL Server na maszynie wirtualnej platformy Azure, która jest częścią infrastruktury platformy Azure jako oferty usługi.

W tym wzorcu aplikacji należy wdrożyć aplikację bazy danych na platformie Azure, umieszczając prezentację i warstwy biznesowe na tej samej maszynie wirtualnej i uzyskując dostęp do bazy danych na serwerach Azure SQL Database (SQL Database). Warstwę prezentacji można zaimplementować przy użyciu tradycyjnych rozwiązań sieci Web opartych na usługach IIS. Można też zaimplementować łączoną prezentację i warstwę biznesową przy użyciu [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/).

Ten wzorzec aplikacji jest przydatny w przypadku:

* Masz już istniejący serwer SQL Database skonfigurowany na platformie Azure i chcesz szybko przetestować swoją aplikację.
* Chcesz przetestować możliwości środowiska platformy Azure.
* Chcesz szybko zainicjować obsługę środowiska deweloperskiego i testowego przez krótki okres czasu.
* Składniki logiki biznesowej i dostępu do danych mogą być samodzielne w aplikacji sieci Web.

Na poniższym diagramie przedstawiono scenariusz lokalny i rozwiązanie z obsługą chmury. W tym scenariuszu warstwy aplikacji są umieszczane na jednej maszynie wirtualnej na platformie Azure i uzyskują dostęp do danych w Azure SQL Database.

![Wzorzec aplikacji mieszanej](./media/application-patterns-development-strategies/IC728015.png)

Jeśli zdecydujesz się na wdrożenie połączonej warstwy sieci Web i aplikacji przy użyciu usługi Azure Web Apps, zalecamy zachowanie warstwy warstwy środkowej lub aplikacji jako bibliotek dołączanych dynamicznie (dll) w kontekście aplikacji sieci Web.

Ponadto zapoznaj się z zaleceniami podaną w sekcji [Porównanie strategii rozwoju sieci Web w systemie Azure](#comparing-web-development-strategies-in-azure) na końcu tego artykułu, aby dowiedzieć się więcej o technikach programowania.

## <a name="n-tier-hybrid-application-pattern"></a>Wzorzec aplikacji hybrydowej N-warstwowej
W przypadku wzorca aplikacji hybrydowych n-warstwowej aplikacja jest wdrażana w wielu warstwach rozproszonych między środowiskiem lokalnym i platformą Azure. W związku z tym należy utworzyć elastyczny system hybrydowy i wielokrotnego użytku, który można zmodyfikować lub dodać określoną warstwę bez zmiany innych warstw. Aby zwiększyć sieć firmową do chmury, Użyj usługi [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) .

Ten wzorzec aplikacji hybrydowej jest przydatny w przypadku:

* Chcesz tworzyć aplikacje, które działają częściowo w chmurze i częściowo lokalnie.
* Chcesz migrować niektóre lub wszystkie elementy istniejącej aplikacji lokalnej do chmury.
* Chcesz przenieść aplikacje dla przedsiębiorstw z lokalnych zwirtualizowanych platform na platformę Azure.
* Chcesz utworzyć środowisko infrastruktury, które można skalować w górę i w dół na żądanie.
* Chcesz szybko zainicjować obsługę środowiska deweloperskiego i testowego przez krótki okres czasu.
* Chcesz korzystać z ekonomicznej metody tworzenia kopii zapasowych dla aplikacji baz danych przedsiębiorstwa.

Na poniższym diagramie przedstawiono wzorzec wielowarstwowej aplikacji hybrydowej, który obejmuje między środowiskiem lokalnym i platformą Azure. Jak pokazano na diagramie, infrastruktura lokalna obejmuje [Active Directory Domain Services](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831484(v=ws.11)) kontrolerem domeny w celu obsługi uwierzytelniania i autoryzacji użytkowników. Należy zauważyć, że diagram ilustruje scenariusz, w którym niektóre części warstwy danych znajdują się na żywo w lokalnym centrum danych, a niektóre części warstwy danych na żywo na platformie Azure. W zależności od potrzeb aplikacji można zaimplementować kilka innych scenariuszy hybrydowych. Na przykład można zachować warstwę prezentacji i warstwę biznesową w środowisku lokalnym, ale warstwę danych na platformie Azure.

![Wzorzec aplikacji N-warstwowej](./media/application-patterns-development-strategies/IC728016.png)

Na platformie Azure Możesz użyć Active Directory jako autonomicznego katalogu w chmurze dla swojej organizacji lub można także zintegrować istniejące Active Directory lokalne z [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Jak widać na diagramie, składniki warstwy biznesowej mogą uzyskać dostęp do wielu źródeł danych, takich jak [SQL Server na platformie Azure](sql-server-on-azure-vm-iaas-what-is-overview.md) za pośrednictwem prywatnego wewnętrznego adresu IP, do lokalnych SQL Server za pośrednictwem [usługi azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)lub [SQL Database](../../database/sql-database-paas-overview.md) przy użyciu technologii dostawcy danych .NET Framework. Na tym diagramie Azure SQL Database to opcjonalna usługa magazynu danych.

W wzorcu wielowarstwowej aplikacji hybrydowej można zaimplementować następujący przepływ pracy w określonej kolejności:

1. Zidentyfikuj aplikacje baz danych przedsiębiorstwa, które mają zostać przeniesione do chmury przy użyciu [zestawu narzędzi Microsoft Assessment and Planning (map)](https://microsoft.com/map). Zestaw narzędzi mapy zbiera dane dotyczące spisu i wydajności z komputerów rozważanych pod kątem wirtualizacji i oferuje zalecenia dotyczące wydajności i planowania oceny.
2. Zaplanuj zasoby i konfigurację wymaganą na platformie Azure, takie jak konta magazynu i maszyny wirtualne.
3. Skonfiguruj łączność sieciową między firmowymi sieciami lokalnymi i [Virtual Network platformy Azure](../../../virtual-network/virtual-networks-overview.md). Aby skonfigurować połączenie między lokalną siecią firmową i maszyną wirtualną na platformie Azure, użyj jednej z następujących dwóch metod:
   
   1. Nawiąż połączenie między środowiskiem lokalnym i platformą Azure za pośrednictwem publicznych punktów końcowych na maszynie wirtualnej na platformie Azure. Ta metoda zapewnia łatwą konfigurację i umożliwia korzystanie z uwierzytelniania SQL Server na maszynie wirtualnej. Ponadto Skonfiguruj reguły sieciowej grupy zabezpieczeń, aby kontrolować ruch publiczny do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Zezwalanie na dostęp zewnętrzny do maszyny wirtualnej przy użyciu Azure Portal](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Ustanów połączenie między środowiskiem lokalnym i platformą Azure za pośrednictwem tunelu wirtualnej sieci prywatnej (VPN) platformy Azure. Ta metoda umożliwia rozszerzonie zasad domeny na maszynę wirtualną na platformie Azure. Ponadto można skonfigurować reguły zapory i użyć uwierzytelniania systemu Windows na maszynie wirtualnej. Obecnie platforma Azure obsługuje bezpieczne połączenia sieci VPN typu lokacja-lokacja i połączeń VPN typu punkt-lokacja:
      
      * Bezpieczne połączenie między lokacjami umożliwia nawiązanie połączenia sieciowego między siecią lokalną a siecią wirtualną na platformie Azure. Zalecamy połączenie lokalnego środowiska centrum danych z platformą Azure.
      * Za pomocą bezpiecznego połączenia punkt-lokacja można nawiązać połączenie sieciowe między siecią wirtualną na platformie Azure a indywidualnymi komputerami z dowolnego miejsca. Jest to zalecane głównie do celów deweloperskich i testowych.
      
      Aby uzyskać informacje na temat nawiązywania połączenia z SQL Server na platformie Azure, zobacz [nawiązywanie połączenia z SQL Server maszyną wirtualną na platformie Azure](ways-to-connect-to-sql.md).
4. Konfigurowanie zaplanowanych zadań i alertów, które wykonują kopie zapasowe danych lokalnych na dysku maszyny wirtualnej na platformie Azure. Aby uzyskać więcej informacji, zobacz [SQL Server kopia zapasowa i przywracanie za pomocą usługi Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) oraz [kopia zapasowa i przywracanie dla SQL Server na platformie Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md).
5. W zależności od potrzeb aplikacji można zaimplementować jeden z następujących trzech typowych scenariuszy:
   
   1. Serwer sieci Web, serwer aplikacji i dane niepoufne mogą być przechowywane na serwerze bazy danych na platformie Azure, co pozwala zachować poufne dane lokalnie.
   2. Serwer sieci Web i serwer aplikacji można zachować lokalnie, a serwer bazy danych na maszynie wirtualnej na platformie Azure.
   3. Serwer bazy danych, serwer sieci Web i serwer aplikacji można zachować lokalnie, zachowując repliki baz danych na maszynach wirtualnych na platformie Azure. To ustawienie umożliwia serwerom lokalnym sieci Web lub aplikacjom raportowania dostęp do replik bazy danych na platformie Azure. W związku z tym można osiągnąć obniżenie obciążenia w lokalnej bazie danych. Zalecamy zaimplementowanie tego scenariusza na potrzeby intensywnie odczytywania obciążeń i celów programistycznych. Aby uzyskać informacje na temat tworzenia replik bazy danych na platformie Azure, zobacz Zawsze włączone grupy dostępności pod [kątem wysokiej dostępności i odzyskiwania po awarii dla SQL Server na platformie azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Porównanie strategii rozwoju sieci Web na platformie Azure
Aby zaimplementować i wdrożyć wielowarstwową aplikację opartą na SQL Server na platformie Azure, można użyć jednej z następujących dwóch metod programowania:

* Skonfiguruj tradycyjny serwer sieci Web (IIS-Internet Information Services) na platformie Azure i bazy danych programu Access w SQL Server na platformie Azure Virtual Machines.
* Zaimplementuj i Wdróż usługę w chmurze na platformie Azure. Następnie upewnij się, że ta usługa w chmurze może uzyskać dostęp do baz danych w SQL Server na platformie Azure Virtual Machines. Usługa w chmurze może obejmować wiele ról sieci Web i procesów roboczych.

Poniższa tabela zawiera porównanie tradycyjnego programowania w sieci Web za pomocą usługi Azure Cloud Services i usługi Azure Web Apps w odniesieniu do SQL Server na platformie Azure Virtual Machines. Ta tabela zawiera Web Apps platformy Azure, ponieważ możliwe jest użycie SQL Server na maszynie wirtualnej platformy Azure jako źródła danych dla usługi Azure Web Apps za pośrednictwem publicznego wirtualnego adresu IP lub nazwy DNS.

|  | Tradycyjne programowanie w sieci Web na platformie Azure Virtual Machines | Usługi w chmurze na platformie Azure | Hosting w sieci Web za pomocą usługi Azure Web Apps |
| --- | --- | --- | --- |
| **Migracja aplikacji z lokalizacji lokalnej** |Istniejące aplikacje. |Aplikacje muszą mieć role sieci Web i procesu roboczego. |Istniejące aplikacje są dostosowane do własnych aplikacji sieci Web i usług sieci Web, które wymagają szybkiej skalowalności. |
| **Programowanie i wdrażanie** |Visual Studio, WebMatrix, Visual Web Developer, webdeploy, FTP, TFS, Menedżer usług IIS, PowerShell. |Visual Studio, Azure SDK, TFS, PowerShell. Każda usługa w chmurze ma dwa środowiska, w których można wdrożyć pakiet i konfigurację usługi: przemieszczanie i tworzenie. Usługę chmurową można wdrożyć w środowisku przejściowym, aby przetestować ją przed podwyższeniem poziomu do produkcji. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web Deploy, PowerShell. |
| **Administracja i konfiguracja** |Użytkownik jest odpowiedzialny za zadania administracyjne dotyczące aplikacji, danych, reguł zapory, sieci wirtualnej i systemu operacyjnego. |Użytkownik jest odpowiedzialny za zadania administracyjne dotyczące aplikacji, danych, reguł zapory i sieci wirtualnej. |Użytkownik jest odpowiedzialny za zadania administracyjne dotyczące tylko aplikacji i danych. |
| **Wysoka dostępność i odzyskiwanie po awarii (HADR cluster)** |Zalecamy umieszczenie maszyn wirtualnych w tym samym zestawie dostępności i w tej samej usłudze w chmurze. Przechowywanie maszyn wirtualnych w tym samym zestawie dostępności umożliwi platformie Azure umieszczenie węzłów o wysokiej dostępności w oddzielnych domenach błędów i domenach uaktualnienia. Podobnie utrzymywanie maszyn wirtualnych w tej samej usłudze w chmurze umożliwia Równoważenie obciążenia i maszyn wirtualnych bezpośrednio ze sobą za pośrednictwem sieci lokalnej w centrum danych platformy Azure.<br/><br/>Użytkownik jest odpowiedzialny za wdrożenie rozwiązania wysokiej dostępności i odzyskiwania po awarii dla SQL Server na platformie Azure Virtual Machines, aby uniknąć przestoju. Obsługiwane technologie HADR Cluster można znaleźć w temacie [wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).<br/><br/>Użytkownik jest odpowiedzialny za tworzenie kopii zapasowych własnych danych i aplikacji.<br/><br/>Platforma Azure może przenieść maszyny wirtualne, jeśli maszyna hosta w centrum danych nie powiedzie się z powodu problemów ze sprzętem. Ponadto może być planowane przestoje maszyny wirtualnej, gdy komputer hosta został zaktualizowany do zabezpieczeń lub aktualizacji oprogramowania. Dlatego zalecamy zachowanie co najmniej dwóch maszyn wirtualnych w każdej warstwie aplikacji, aby zapewnić ciągłą dostępność. Platforma Azure nie zapewnia umowy SLA dla pojedynczej maszyny wirtualnej. |System Azure zarządza niepowodzeńmi wynikającymi z bazowego sprzętu lub oprogramowania systemu operacyjnego. Zalecamy implementację wielu wystąpień roli sieć Web lub proces roboczy, aby zapewnić wysoką dostępność aplikacji. Aby uzyskać więcej informacji, zobacz [Cloud Services, Virtual Machines i Virtual Network umowa dotycząca poziomu usług](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Użytkownik jest odpowiedzialny za tworzenie kopii zapasowych własnych danych i aplikacji.<br/><br/>W przypadku baz danych znajdujących się w bazie danych SQL Server na maszynie wirtualnej platformy Azure użytkownik jest odpowiedzialny za wdrożenie rozwiązania wysokiej dostępności i odzyskiwania po awarii, aby uniknąć przestoju. Obsługiwane technologie HDAR można znaleźć w temacie wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines.<br/><br/>**SQL Server dublowanie bazy danych** : Użyj z usługą Azure Cloud Services (role sieci Web/proces roboczy). SQL Server maszyny wirtualne i projekt usługi w chmurze mogą znajdować się w tym samym Virtual Network platformy Azure. Jeśli SQL Server VM nie znajduje się w tym samym Virtual Network, należy utworzyć alias SQL Server do kierowania komunikacji do wystąpienia SQL Server. Ponadto Nazwa aliasu musi być zgodna z nazwą SQL Server. |Wysoka dostępność jest dziedziczona z ról procesów roboczych platformy Azure, usługi Azure Blob Storage i Azure SQL Database. Na przykład usługa Azure Storage przechowuje trzy repliki wszystkich danych obiektów blob, tabel i kolejek. W dowolnym momencie Azure SQL Database utrzymuje trzy repliki danych, które działają — jedną replikę podstawową i dwie repliki pomocnicze. Aby uzyskać więcej informacji, zobacz [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) i [Azure SQL Database](../../database/sql-database-paas-overview.md).<br/><br/>W przypadku używania SQL Server na maszynie wirtualnej platformy Azure jako źródła danych dla usługi Azure Web Apps należy pamiętać, że usługa Azure Web Apps nie obsługuje Virtual Network platformy Azure. Innymi słowy, wszystkie połączenia z usługi Azure Web Apps do SQL Server maszyn wirtualnych na platformie Azure muszą przejść przez publiczne punkty końcowe maszyn wirtualnych. Może to spowodować pewne ograniczenia dotyczące scenariuszy wysokiej dostępności i odzyskiwania po awarii. Na przykład aplikacja kliencka na platformie Azure Web Apps łączenie się z maszyną wirtualną SQL Serverj przy użyciu funkcji dublowania baz danych nie będzie mogła nawiązać połączenia z nowym serwerem podstawowym, ponieważ funkcja dublowania baz danych wymaga skonfigurowania Virtual Network platformy Azure między maszynami wirtualnymi SQL Server hosta na platformie Azure. W związku z tym używanie **dublowania SQL Server Database** z usługą Azure Web Apps nie jest obecnie obsługiwane.<br/><br/>**SQL Server zawsze włączone grupy dostępności** : można skonfigurować zawsze włączone grupy dostępności podczas korzystania z platformy Azure Web Apps z maszynami wirtualnymi SQL Server na platformie Azure. Ale należy skonfigurować odbiornik grupy dostępności AlwaysOn, aby kierowanie komunikacji do repliki podstawowej za pośrednictwem publicznych punktów końcowych ze zrównoważonym obciążeniem. |
| **Połączenia obejmujące wiele lokalizacji** |Korzystając z usługi Azure Virtual Network, możesz nawiązać połączenie z lokalnymi. |Korzystając z usługi Azure Virtual Network, możesz nawiązać połączenie z lokalnymi. |Usługa Azure Virtual Network jest obsługiwana. Aby uzyskać więcej informacji, zobacz [Web Apps Virtual Network integrację](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Skalowalność** |Skalowanie jest dostępne przez zwiększenie rozmiaru maszyny wirtualnej lub dodanie większej liczby dysków. Aby uzyskać więcej informacji o rozmiarach maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych na platformie Azure](../../../virtual-machines/sizes.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json).<br/><br/>**W przypadku serwera bazy danych** : skalowanie w poziomie jest dostępne za pośrednictwem technik partycjonowania bazy danych i SQL Server zawsze włączonych grup dostępności.<br/><br/>W przypadku dużych obciążeń odczytu można użyć [zawsze włączone grupy dostępności](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) na wielu węzłach pomocniczych, a także w SQL Server replikacji.<br/><br/>W przypadku dużych obciążeń zapisu można zaimplementować poziome dane partycjonowania na wielu serwerach fizycznych w celu zapewnienia skalowalności aplikacji.<br/><br/>Ponadto można zaimplementować skalowanie w poziomie przy użyciu [SQL Server z routingiem zależnym od danych](/previous-versions/sql/sql-server-2005/administrator/cc966448(v=technet.10)). W przypadku routingu zależnego od danych (DDR) należy zaimplementować mechanizm partycjonowania w aplikacji klienckiej, zazwyczaj w warstwie warstwy biznesowej, aby skierować żądania bazy danych do wielu węzłów SQL Server. Warstwa biznesowa zawiera mapowania dotyczące partycjonowania danych oraz tego, który węzeł zawiera dane.<br/><br/>Można skalować aplikacje, na których działają maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [Jak skalować aplikację](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Ważna Uwaga** : Funkcja automatycznego **skalowania** na platformie Azure umożliwia automatyczne zwiększanie lub zmniejszanie maszyn wirtualnych, które są używane przez aplikację. Ta funkcja gwarantuje, że środowisko użytkownika końcowego nie ma negatywnego oddziaływania w okresach szczytu, a maszyny wirtualne są zamykane, gdy zapotrzebowanie jest niskie. Zaleca się, aby nie ustawiać opcji automatycznego skalowania dla usługi w chmurze, jeśli zawiera ona SQL Server maszyny wirtualne. Przyczyną jest to, że funkcja automatycznego skalowania umożliwia platformie Azure włączenie maszyny wirtualnej, gdy użycie procesora CPU w tej maszynie wirtualnej jest wyższe niż pewne wartości progowe i wyłączyć maszynę wirtualną, gdy użycie procesora spadnie poniżej. Funkcja automatycznego skalowania jest przydatna w przypadku aplikacji bezstanowych, takich jak serwery sieci Web, gdzie każda maszyna wirtualna może zarządzać obciążeniem bez żadnych odwołań do poprzedniego stanu. Funkcja automatycznego skalowania nie jest jednak przydatna w przypadku aplikacji stanowych, takich jak SQL Server, gdy tylko jedno wystąpienie umożliwia zapisywanie w bazie danych. |Skalowanie jest dostępne przy użyciu wielu ról sieci Web i procesów roboczych. Aby uzyskać więcej informacji o rozmiarach maszyn wirtualnych dla ról sieci Web i ról procesów roboczych, zobacz [Konfigurowanie rozmiarów Cloud Services](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Korzystając z **Cloud Services** , można zdefiniować wiele ról do dystrybucji przetwarzania, a także osiągnąć elastyczne skalowanie aplikacji. Każda usługa w chmurze zawiera co najmniej jedną rolę sieci Web i/lub role procesu roboczego, z których każda ma własne pliki i konfiguracje aplikacji. Możesz skalować usługę w chmurze, zwiększając liczbę wystąpień roli (maszyn wirtualnych) wdrożonych dla roli i skalując w dół usługę w chmurze, zmniejszając liczbę wystąpień roli. Aby uzyskać szczegółowe informacje, zobacz [modele wykonywania platformy Azure](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Skalowanie w poziomie jest dostępne za pośrednictwem wbudowanej obsługi wysokiej dostępności platformy Azure za pośrednictwem [Cloud Services, Virtual Machines i Virtual Network umowa dotycząca poziomu usług](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) i Load Balancer.<br/><br/>W przypadku aplikacji wielowarstwowej zalecamy połączenie aplikacji sieci Web/procesu roboczego z maszynami wirtualnymi serwera bazy danych za pośrednictwem usługi Azure Virtual Network. Ponadto platforma Azure zapewnia Równoważenie obciążenia dla maszyn wirtualnych w tej samej usłudze w chmurze, rozproszenie żądań użytkowników między nimi. Maszyny wirtualne połączone w ten sposób mogą komunikować się bezpośrednio ze sobą za pośrednictwem sieci lokalnej w centrum danych platformy Azure.<br/><br/>Można skonfigurować **Skalowanie automatyczne** na Azure Portal oraz czasy planowania. Aby uzyskać więcej informacji, zobacz [jak skonfigurować automatyczne skalowanie dla usługi w chmurze w portalu](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Skalowanie w górę i w dół** : można zwiększyć/zmniejszyć rozmiar wystąpienia (VM) zarezerwowanego dla witryny sieci Web.<br/><br/>Skalowanie w poziomie: możesz dodać bardziej zastrzeżone wystąpienia (maszyny wirtualne) dla witryny sieci Web.<br/><br/>Można skonfigurować **Automatyczne skalowanie** w portalu oraz czasy planowania. Aby uzyskać więcej informacji, zobacz [Jak skalować Web Apps](../../../app-service/manage-scale-up.md). |

Aby uzyskać więcej informacji na temat wybierania tych metod programowania, zobacz [Web Apps platformy Azure, Cloud Services i maszyny wirtualne: Kiedy używać](/azure/architecture/guide/technology-choices/compute-decision-tree)tego programu.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uruchamiania SQL Server na platformie Azure Virtual Machines, zobacz [SQL Server na platformie Virtual Machines Azure — omówienie](sql-server-on-azure-vm-iaas-what-is-overview.md).
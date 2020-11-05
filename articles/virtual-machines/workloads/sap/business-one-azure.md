---
title: Oprogramowanie SAP Business One na platformie Azure Virtual Machines | Microsoft Docs
description: SAP Business One na platformie Azure.
author: msjuergent
ms.service: virtual-machines
ms.topic: article
ms.date: 07/15/2018
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: b076045f6a106f77717159d3095ffd5973ab27aa
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359888"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One na maszynach wirtualnych platformy Azure
Ten dokument zawiera wskazówki dotyczące wdrażania oprogramowania SAP Business One na platformie Azure Virtual Machines. Dokumentacja programu nie zastępuje dokumentacji dotyczącej instalacji dla oprogramowania SAP. Dokumentacja powinna obejmować podstawowe wytyczne dotyczące planowania i wdrażania infrastruktury platformy Azure w celu uruchamiania aplikacji firmowych.

Firma 1 obsługuje dwie różne bazy danych:
- SQL Server — zobacz temat [SAP uwagi #928839 — planowanie wydania dla Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA — aby zapoznać się z przykładową matrycą wsparcia SAP Business dla SAP HANA, Zaewidencjonuj [macierz dostępności produktu SAP](https://support.sap.com/pam)

W odniesieniu do SQL Server należy zastosować podstawowe zagadnienia dotyczące wdrażania, zgodnie z opisem w artykule [wdrażanie systemu Azure Virtual Machines DBMS dla oprogramowania SAP NetWeaver](./dbms_guide_general.md) . w przypadku SAP HANA uwagi zostały wymienione w tym dokumencie.

## <a name="prerequisites"></a>Wymagania wstępne
Aby skorzystać z tego przewodnika, potrzebna jest podstawowa znajomość następujących składników platformy Azure:

- [Azure Virtual Machines w systemie Windows](../../windows/tutorial-manage-vm.md)
- [Azure Virtual Machines w systemie Linux](../../linux/tutorial-manage-vm.md)
- [Zarządzanie sieciami i sieciami wirtualnymi platformy Azure za pomocą programu PowerShell](../../windows/tutorial-virtual-network.md)
- [Obsługa sieci i sieci wirtualnych platformy Azure przy użyciu interfejsu wiersza polecenia](../../linux/tutorial-virtual-network.md)
- [Zarządzanie dyskami platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](../../linux/tutorial-manage-disks.md)

Nawet jeśli interesuje Cię podmiot gospodarczy, dokument [Azure Virtual Machines Planning and implementation for SAP NetWeaver](./planning-guide.md) może być dobrym źródłem informacji.

Przyjęto założenie, że wystąpienie wdrożenia SAP Business to:

- Znajomość instalacji SAP HANA w danej infrastrukturze, takiej jak maszyna wirtualna
- Znajomość instalacji aplikacji SAP Business na infrastrukturze, takiej jak maszyny wirtualne platformy Azure
- Zapoznaj się z usługą SAP Business i wybranym systemem DBMS
- Znajomość wdrażania infrastruktury na platformie Azure

Wszystkie te obszary nie zostaną omówione w tym dokumencie.

Oprócz dokumentacji platformy Azure należy zapoznać się z głównymi uwagami dotyczącymi oprogramowania SAP, które odnoszą się do współpracy z firmą 1 lub które są centralnymi uwagami dotyczącymi oprogramowania SAP dla firm:

- [528296 — ogólne omówienie wersji SAP Business i produktów pokrewnych](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 — aktualizacje wersji dla oprogramowania SAP Business 1 9,2, wersja dla SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 — Centralna Uwaga dla SAP Business 1 9,3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 — informacje o wersji dla oprogramowania SAP Business 1 9,3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 — Zbiorcza Uwaga dla oprogramowania SAP Business 1 9,3 ogólne problemy](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 — Zbiorcza konsultacja dotycząca oprogramowania SAP HANA-Related tematy SAP Business One, wersja dla SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Biznes — jedna architektura
Firma to aplikacja, która ma dwie warstwy:

- Warstwa klienta z klientem "Fat"
- Warstwa bazy danych, która zawiera schemat bazy danych dla dzierżawy

Lepszy przegląd składników uruchomionych w części klienta i części, które są uruchomione w części serwera, został udokumentowany w [podręczniku administratora oprogramowania SAP Business](https://help.sap.com/doc/601fbd9113be4240b81d74626439cfa9/10.0/en-US/AdministratorGuide_SQL.pdf) . 

Ponieważ występuje duże opóźnienie krytyczne między warstwą klienta i warstwą DBMS, obie warstwy muszą znajdować się na platformie Azure podczas wdrażania na platformie Azure. zwykle jest to, że użytkownicy będą następnie RDS w jednej lub wielu maszynach wirtualnych z uruchomioną usługą RDS dla firmowego składnika klienta.

### <a name="sizing-vms-for-sap-business-one"></a>Ustalanie wielkości maszyn wirtualnych dla oprogramowania SAP Business One

W odniesieniu do wielkości maszyn wirtualnych klienta wymagania dotyczące zasobów są udokumentowane przez SAP w dokumencie [SAP Business — wymagania sprzętowe](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). W przypadku platformy Azure należy skoncentrować się i obliczać zgodnie z wymaganiami podanymi w rozdziale 2,4 dokumentu.

Jako że usługa Azure Virtual Machines na potrzeby hostowania firmowych składników klienta i hosta DBMS, dozwolone są tylko maszyny wirtualne, które są obsługiwane przez SAP NetWeaver. Aby znaleźć listę obsługiwanych maszyn wirtualnych platformy SAP NetWeaver, przeczytaj temat [SAP uwagi #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Uruchamianie SAP HANA jako bazy danych DBMS dla firm, tylko maszyny wirtualne, które są wymienione dla firm na platformie HANA na [liście platform certyfikowanej IaaS platformy Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) , są obsługiwane w programie Hana. Te silniejsze ograniczenia dotyczące SAP HANA jako systemu DBMS nie mają wpływ na działalność biznesową jednego klienta.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Wersje systemu operacyjnego do użycia w oprogramowaniu SAP Business One

W zasadzie zawsze najlepiej używać najnowszych wersji systemu operacyjnego. W szczególności w miejscu w systemie Linux wprowadzono nowe funkcje platformy Azure z innymi nowszymi wersjami oprogramowania SUSE i Red Hat. Po stronie systemu Windows, w którym jest używany system Windows Server 2016, jest zdecydowanie zalecane.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Wdrażanie infrastruktury na platformie Azure dla oprogramowania SAP Business One
W następnych kilku działach, fragmenty infrastruktury, które dotyczą wdrażania SAP.

### <a name="azure-network-infrastructure"></a>Infrastruktura sieci platformy Azure
Infrastruktura sieci, którą należy wdrożyć na platformie Azure, zależy od tego, czy w danym systemie wdrożono jedną firmę. Lub niezależnie od tego, czy jesteś gospodarzem, który hostuje dziesiątki firmy dla klientów. W projekcie mogą być również nieznaczne zmiany dotyczące sposobu łączenia się z platformą Azure. Korzystając z różnych możliwości, jeden projekt, w którym masz połączenie sieci VPN na platformę Azure, i dowiesz się, Active Directory za pośrednictwem [sieci VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [ExpressRoute](../../../expressroute/expressroute-introduction.md) na platformę Azure.

![Prosta konfiguracja sieci z firmą 1](./media/business-one-azure/simple-network-with-VPN.PNG)

W uproszczonej konfiguracji przedstawiono kilka wystąpień zabezpieczeń, które umożliwiają kontrolowanie i ograniczanie routingu. Zaczyna się od 

- Router/zapora po stronie klienta.
- Następnym wystąpieniem jest [Grupa zabezpieczeń sieci platformy Azure](../../../virtual-network/network-security-groups-overview.md) , której można użyć w celu wprowadzenia reguł routingu i zabezpieczeń dla usługi Azure VNET, w której jest uruchamiana konfiguracja oprogramowania SAP Business w systemie.
- Aby uniknąć, że użytkownicy biznesowi jednego klienta mogą również zobaczyć serwer, na którym działa serwer biznesowy, na którym działa baza danych programu, należy oddzielić maszynę wirtualną, która jest hostem biznesowym i firmą jeden serwer w dwóch różnych podsieciach w ramach połączenia z siecią wirtualną.
- Należy ponownie użyć usługi Azure sieciowej grupy zabezpieczeń przypisanej do dwóch różnych podsieci w celu ograniczenia dostępu do jednego serwera biznesowego.

Bardziej rozbudowana wersja konfiguracji sieci platformy Azure jest oparta na [udokumentowanych najlepszych rozwiązaniach dotyczących architektury gwiazdy i szprych](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Wzorzec architektury piasty i szprychy spowoduje zmianę pierwszej uproszczonej konfiguracji na taką, jak to:


![Konfiguracja gwiazdy i gwiazdy z firmą 1](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

W przypadku, gdy użytkownicy nawiązują połączenie za pośrednictwem Internetu bez jakichkolwiek połączeń prywatnych z platformą Azure, projekt sieci na platformie Azure powinien być wyrównany do zasad udokumentowanych w architekturze referencyjnej platformy Azure dla [strefy DMZ między platformą Azure i Internetem](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>Biznesowy serwer bazy danych
Dla typu bazy danych dostępne są SQL Server i SAP HANA. Niezależnie od systemu DBMS należy przeczytać zagadnienia dotyczące dokumentu [dotyczące wdrożenia platformy azure Virtual Machines DBMS dla obciążenia SAP](./dbms_guide_general.md) w celu uzyskania ogólnych informacji na temat wdrożeń systemów DBMS na maszynach wirtualnych platformy Azure oraz związanych z nimi tematów dotyczących sieci i magazynu.

Chociaż są już wyróżnione w określonych i ogólnych dokumentach baz danych, należy zaznajomić się z:

- [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure](../../manage-availability.md) i [Zarządzanie dostępnością maszyn wirtualnych z systemem Linux na platformie Azure](../../manage-availability.md)
- [Umowa SLA dla maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Te dokumenty powinny ułatwić podjęcie decyzji dotyczącej wyboru typów magazynu i konfiguracji wysokiej dostępności.

W zasadzie należy:

- Korzystaj z dysków SSD Premium w warstwie Standardowa HDD. Aby dowiedzieć się więcej o dostępnych typach dysków, zapoznaj się z artykułem [Wybieranie typu dysku](../../disks-types.md)
- Korzystanie z usługi Azure Managed disks za pośrednictwem dysków niezarządzanych
- Upewnij się, że masz wystarczającą liczbę operacji we/wy i przepływności operacji wejścia/wyjścia skonfigurowanej z konfiguracją dysku
- Połącz woluminy/Hana/Data i/Hana/log w celu zapewnienia ekonomicznej konfiguracji magazynu


#### <a name="sql-server-as-dbms"></a>SQL Server jako system DBMS
Aby wdrożyć SQL Server jako system DBMS dla firm, przejdź do dokumentu [SQL Server wdrożenia systemu Azure Virtual Machines DBMS dla oprogramowania SAP NetWeaver](./dbms_guide_sqlserver.md). 

Przybliżone określanie wielkości szacunków dla strony systemu DBMS dla SQL Server są następujące:

| Liczba użytkowników | Procesory wirtualne | Pamięć | Przykładowe typy maszyn wirtualnych |
| --- | --- | --- | --- |
| do 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| do 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| do 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| do 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

Wymienione powyżej zmiany wielkości powinny dawać pomysł, gdzie zaczyna się od. Może być potrzebne mniejsze lub więcej zasobów, w takim przypadku dostosowanie na platformie Azure jest proste. Zmiana między typami maszyn wirtualnych jest możliwa po ponownym uruchomieniu maszyny wirtualnej.

#### <a name="sap-hana-as-dbms"></a>SAP HANA jako system DBMS
Korzystanie z SAP HANA jako systemu DBMS następujące sekcje powinny być zgodne z zagadnieniami [SAP HANA dokumentu w Przewodniku obsługi platformy Azure](./hana-vm-operations.md).

Aby uzyskać wysoką dostępność i konfiguracje odzyskiwania po awarii dotyczące SAP HANA jako bazy danych dla firm jednej na platformie Azure, należy zapoznać się z dokumentacją [SAP HANA wysokiej dostępności dla maszyn wirtualnych platformy Azure](./sap-hana-availability-overview.md) i dokumentacji wskazywanej w tym dokumencie.

Aby uzyskać SAP HANA strategii tworzenia kopii zapasowych i przywracania, należy przeczytać [Przewodnik po kopiach zapasowych dokumentu dotyczący SAP HANA na platformie Azure Virtual Machines](./sap-hana-backup-guide.md) oraz dokumentacji wskazanej w tym dokumencie.

 
### <a name="business-one-client-server"></a>Biznes jeden serwer kliencki
Zagadnienia związane z przechowywaniem tych składników nie są zasadniczym problemem. Niemniej jednak chcesz mieć niezawodną platformę. W związku z tym należy używać Premium Storage platformy Azure dla tej maszyny wirtualnej, nawet w przypadku podstawowego wirtualnego dysku twardego. Ustalanie rozmiarów maszyny wirtualnej przy użyciu danych z [przewodnika dotyczącego wymagań sprzętowych SAP Business](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). W przypadku platformy Azure należy skoncentrować się i obliczać zgodnie z wymaganiami podanymi w rozdziale 2,4 dokumentu. Podczas obliczania wymagań należy porównać je z następującymi dokumentami, aby znaleźć idealną maszynę wirtualną:

- [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](../../sizes.md)
- [#1928533 uwagi SAP](https://launchpad.support.sap.com/#/notes/1928533)

Porównanie liczby procesorów i pamięci wymaganej przez firmę Microsoft. Należy również zachować przepustowość sieci podczas wybierania maszyn wirtualnych.

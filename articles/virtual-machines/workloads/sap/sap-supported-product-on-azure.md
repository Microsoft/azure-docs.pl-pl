---
title: 'SAP na platformie Azure: jakie oprogramowanie SAP jest obsługiwane na platformie Azure'
description: Wyjaśnia, jakie oprogramowanie SAP jest obsługiwane do wdrożenia na platformie Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a02c30423ab3c6de87eac7d34b5f0004e54a8d5d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957813"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Jakie oprogramowanie SAP jest obsługiwane w przypadku wdrożeń platformy Azure
W tym artykule opisano sposób, w jaki można dowiedzieć się, jakie oprogramowanie SAP jest obsługiwane przez wdrożenia platformy Azure oraz jakie są niezbędne wersje systemu operacyjnego lub wersje DBMS.

Ocenianie, czy bieżące oprogramowanie SAP jest obsługiwane i jakie wersje systemów operacyjnych i DBMS są obsługiwane przez oprogramowanie SAP na platformie Azure, będziesz potrzebować dostępu do:

- Uwagi dotyczące pomocy technicznej SAP
- Macierz dostępności produktu SAP



## <a name="general-restrictions-for-sap-workload"></a>Ogólne ograniczenia dotyczące obciążeń SAP
Usługi Azure IaaS Services, które mogą być używane na potrzeby obciążeń SAP, są ograniczone do sprzętu x86-64 lub x64. Nie ma żadnych ofert opartych na PROCESORAch SPARC lub mocy, które dotyczą obciążeń SAP. Klienci, którzy działają w swoich aplikacjach w systemach operacyjnych, które są zgodne z architekturą sprzętową, takimi jak IBM mainframe lub systemu AS400, lub gdy systemy operacyjne HP-UX, Solaris lub AIX są używane, muszą zmienić swoje aplikacje SAP, w tym system DBMS, na jeden z następujących systemów operacyjnych:

- 64-bitowy system Windows Server dla platformy x86-64
- System SUSE Linux 64 dla platformy x86-64
- System Red Hat Linux (64) dla platformy x86-64
- Oracle Linux 64-bitowy dla platformy x86-64

W połączeniu z oprogramowaniem SAP nie są obsługiwane żadne inne wersje systemów operacyjnych i dystrybucje systemu Linux. Dokładne szczegółowe informacje o określonych wersjach i przypadkach zostały udokumentowane w dalszej części dokumentu.


## <a name="you-start-here"></a>Zacznij tutaj
Punktem początkowym dla Ciebie jest [Pomoc techniczna SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Po przejściu do tej notatki SAP od góry do dołu są wyświetlane różne obszary obsługiwanego oprogramowania i maszyn wirtualnych

W pierwszej sekcji wymieniono minimalne wymagania dotyczące wydań operacyjnych, które są ogólnie obsługiwane przez oprogramowanie SAP na maszynach wirtualnych platformy Azure. Jeśli nie dotrzesz do tych minimalnych wymagań i uruchamiasz starsze wersje tych systemów operacyjnych, musisz uaktualnić wersję systemu operacyjnego do takiej minimalnej wersji lub nawet w nowszych wersjach. Jest to rozwiązanie, że platforma Azure ogólnie obsługuje starsze wersje niektórych systemów operacyjnych. Jednak ograniczenia lub minimalne wersje wymienione na liście są oparte na testach i zadawanych kwalifikacjach i nie zostaną jeszcze rozszerzone. 


> [!NOTE]
>Istnieją pewne typy maszyn wirtualnych, Duże wystąpienia HANA lub obciążenia SAP, które będą wymagały zastosowania nowszych wersji systemu operacyjnego. Takie przypadki jak te, które zostaną wymienione w całym dokumencie. Takie przypadki jak te, które są wyraźnie udokumentowane w uwagi SAP lub innych publikacji SAP.

W poniższej sekcji wymieniono ogólne platformy SAP obsługiwane przez obsługiwane wersje i ważniejsze, obsługiwane przez nie jądra SAP. Wyświetla listę obsługiwanych stosów NetWeaver/ABAP lub Java, które wymagają minimalnych wersji jądra. Najnowsze stosy ABAP są obsługiwane na platformie Azure, ale nie wymagają minimalnych wersji jądra, ponieważ zmiany dotyczące platformy Azure zostały zaimplementowane od początku opracowywania najnowszych stosów

Należy sprawdzić:

- Czy używane aplikacje SAP są objęte minimalnymi wydaniami. W przeciwnym razie należy zdefiniować nową wersję docelową, zaewidencjonować macierz dostępności produktu SAP, jakie kompilacje systemu operacyjnego i w systemie DBMS są obsługiwane przez nową wersję docelową. Dlatego możesz wybrać odpowiednią wersję systemu operacyjnego i wersję DBMS
- Czy należy zaktualizować jądra SAP w przejściu na platformę Azure
- Czy należy zaktualizować pakiety pomocy technicznej SAP. W szczególności pakiety pomocy technicznej, które mogą być wymagane w przypadku, gdy wymagane jest przejście do nowszej wersji systemu DBMS


W następnej sekcji znajdują się szczegółowe informacje o innych produktach SAP i w systemie DBMS, które są obsługiwane przez oprogramowanie SAP na platformie Azure dla systemów Windows i Linux. 

> [!NOTE]
> Minimalne wersje systemu DBMS są starannie wybierane i mogą nie odzwierciedlać całego spektrum systemu DBMS, które w ogóle nie obsługują wielu dostawców systemów DBMS. Wiele zagadnień związanych z obciążeniem SAP zostało uwzględnionych w celu zdefiniowania tych minimalnych wersji. Nie ma wysiłku do testowania i kwalifikowania starszych wersji systemu DBMS. 

> [!NOTE]
> Wymienione poniżej minimalne wersje przedstawiają starszą wersję systemów operacyjnych i wydań baz danych. Zdecydowanie zachęcamy do korzystania z najnowszych wersji systemu operacyjnego i wydań baz danych. W wielu przypadkach nowsza wersja systemu operacyjnego i bazy danych zajęła przypadek użycia w chmurze publicznej w celu rozważenia i dostosowania kodu do optymalizacji pod kątem działania w chmurze publicznej lub bardziej szczegółowej platformy Azure

## <a name="oracle-dbms-support"></a>Obsługa systemu Oracle DBMS
System operacyjny, wersje systemu Oracle DBMS i funkcje Oracle obsługiwane na platformie Azure są wymienione w temacie [uwagi dotyczące pomocy technicznej SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). Część tej notatki może być podsumowana, na przykład:

- Minimalna wersja programu Oracle obsługiwana na maszynach wirtualnych platformy Azure certyfikowanych do NetWeaver to Oracle 11g Release 2 Z zestawem poprawek 3 (11.2.0.4)
- Jako systemy operacyjne gościa tylko Windows i Oracle Linux. Dokładne wersje systemu operacyjnego i powiązane z nim minimalne wersje DBMS są wymienione w notatce
- Obsługa Oracle Linux obejmuje również klienta Oracle DBMS. Oznacza to, że wszystkie składniki SAP, takie jak wystąpienia okna dialogowego stosu ABAP lub Java, muszą działać również na Oracle Linux. Tylko składniki SAP w systemie SAP, które nie łączą się z systemem Oracle DBMS, mogą uruchomić inny system operacyjny Linux
- Certyfikat certyfikatu Oracle nie jest obsługiwany 
- W przypadku niektórych przypadków jest obsługiwany Oracle ASM. Szczegóły znajdują się na liście w notatce
- Systemy SAP inne niż Unicode są obsługiwane tylko na serwerach aplikacji z systemem operacyjnym gościa systemu Windows. Systemem operacyjnym gościa w systemie DBMS może być Oracle Linux lub Windows. Powód tego ograniczenia jest oczywisty podczas sprawdzania platformy SAP Product Availability Matrix (PAM). W przypadku Oracle Linux, SAP nigdy nie wydano jądra SAP inne niż Unicode

Znajomość wersji systemu DBMS, które są obsługiwane przez docelową infrastrukturę platformy Azure, należy sprawdzić macierz dostępności produktu SAP w przypadku, gdy wersje systemów operacyjnych i DBMS są obsługiwane w ramach Twoich wersji produktu SAP przeznaczonych do uruchomienia. 


## <a name="sap-hana-support"></a>Obsługa SAP HANA
Na platformie Azure są dostępne dwie usługi, których można użyć do uruchomienia bazy danych HANA:

- Maszyny wirtualne platformy Azure
- [Duże wystąpienia HANA](./hana-overview-architecture.md)

W przypadku uruchamiania SAP HANA rozwiązanie SAP ma więcej i silniejsze warunki, które muszą spełniać infrastruktura niż w przypadku uruchamiania NetWeaver lub innych aplikacji SAP i systemu DBMS. W związku z tym mniejsza liczba maszyn wirtualnych platformy Azure kwalifikuje się do uruchamiania SAP HANA systemu DBMS. Listę obsługiwanych infrastruktury platformy Azure obsługiwaną dla SAP HANA można znaleźć w [katalogu sprzętowym SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

> [!NOTE]
> Jednostki zaczynające się od litery "są jednostkami [dużej liczby wystąpień Hana](./hana-overview-architecture.md) . 

> [!NOTE]
> System SAP nie ma określonej certyfikacji zależnej od SAP HANA głównych wersji. W przeciwieństwie do typowej opinii **scenariusz certyfikacji** kolumny na [platformach IaaS Certified z certyfikatem Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) **nie zawiera żadnych instrukcji dotyczących głównych i pomocniczych wersji platformy Hana**. Należy założyć, że wszystkie jednostki wymienione na liście mogą być używane dla platformy HANA 1,0 i HANA 2,0, o ile certyfikowane wersje systemu operacyjnego dla określonych jednostek są również obsługiwane przez usługi HANA 1,0. 

W przypadku użycia SAP HANA różne minimalne wersje systemu operacyjnego mogą być stosowane niż w przypadku ogólnych przypadków NetWeaver. Należy sprawdzić obsługiwane systemy operacyjne osobno dla każdej jednostki, ponieważ mogą się one różnić. Możesz to zrobić, klikając każdą jednostkę. Zostanie wyświetlonych więcej szczegółów. Jednym z wymienionych szczegółów jest różne systemy operacyjne obsługiwane przez tę konkretną jednostkę.

> [!NOTE]
> Jednostki duże wystąpienia platformy Azure HANA są bardziej restrykcyjne w porównaniu z obsługiwanymi systemami operacyjnymi w odniesieniu do maszyn wirtualnych platformy Azure Z drugiej strony maszyny wirtualne platformy Azure mogą wymuszać nowsze wersje operacyjne jako minimalne wersje. Jest to szczególnie ważne w przypadku niektórych większych jednostek maszyn wirtualnych, które wymagają zmian w jądrach systemu Linux

Znajomość obsługiwanego systemu operacyjnego dla infrastruktury platformy Azure wymaga sprawdzenia, czy [#2235581 uwagi dotyczące pomocy technicznej SAP](https://launchpad.support.sap.com/#/notes/2235581) dla dokładnych wydań SAP HANA i poziomów poprawek, które są obsługiwane w przypadku docelowych jednostek platformy Azure. 

> [!IMPORTANT]
> Najważniejszym krokiem jest sprawdzenie dokładnego wydania SAP HANA i obsługiwanych poziomów poprawek. W wielu przypadkach obsługa określonej wersji systemu operacyjnego zależy od określonego poziomu poprawek SAP HANA plików wykonywalnych.

Znając konkretne wersje platformy HANA, które można uruchomić w ramach dostosowanej infrastruktury platformy Azure, należy zaewidencjonować macierz dostępności produktu SAP, aby dowiedzieć się, czy istnieją ograniczenia dotyczące wydań produktów SAP, które obsługują odfiltrowane wersje platformy HANA.


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Certyfikowane jednostki maszyn wirtualnych platformy Azure oraz duże wystąpienia usługi HANA i przepływność transakcji biznesowej
Oprócz oceny obsługiwanych wersji systemu operacyjnego, wydań DBMS i obsługiwane wersje oprogramowania SAP dla jednostek infrastruktury platformy Azure należy zakwalifikować te jednostki według przepływności transakcji biznesowej, która jest wyrażona w jednostce "SAP" przez SAP. Wszystkie rozmiary SAP są zależne od obliczeń dla punktów SAP. Oceniając istniejące systemy SAP, zazwyczaj można, przy pomocy dostawcy infrastruktury, obliczyć SAP jednostek. Dla warstwy DBMS oraz dla warstwy aplikacji. W innych przypadkach, gdy zostanie utworzona nowa funkcja, naprawka zmiany wielkości z SAP może ujawnić wymagane numery punktów SAP dla warstwy aplikacji i warstwy DBMS. Jako dostawca infrastruktury firma Microsoft jest zobowiązana do zapewnienia charakterystyki przepływności SAP dla różnych jednostek, które są certyfikowane jako NetWeaver i/lub HANA.

W przypadku maszyn wirtualnych platformy Azure te numery przepływności SAP są udokumentowane w [uwagach pomocy technicznej SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). W przypadku jednostek dużych wystąpień usługi Azure HANA numery przepływności SAP są udokumentowane w [uwagach pomocy technicznej SAP #2316233](https://launchpad.support.sap.com/#/notes/2316233)

W #1928533 uwagi na temat [pomocy technicznej SAP](https://launchpad.support.sap.com/#/notes/1928533)są stosowane następujące uwagi:

- **W przypadku maszyn wirtualnych platformy Azure z serii M i Mv2-Series maszyn wirtualnych platformy Azure są stosowane różne minimalne wersje systemu operacyjnego niż w przypadku innych typów maszyn wirtualnych platformy Azure**. Wymagania dotyczące nowszych wersji systemu operacyjnego są oparte na zmianach wprowadzonych przez różnych dostawców systemów operacyjnych w ich wersjach systemu operacyjnego w celu umożliwienia ich systemów operacyjnych działających na określonych typach maszyn wirtualnych platformy Azure lub optymalizacji wydajności i przepływności obciążeń SAP na tych typach maszyn wirtualnych
- Istnieją dwie tabele, które określają różne typy maszyn wirtualnych. Druga tabela określa przepływność SAP dla typów maszyn wirtualnych platformy Azure, które obsługują tylko usługę Azure Standard Storage. Wdrożenie systemu DBMS w jednostkach określonych w drugiej tabeli notatki nie jest obsługiwane.


## <a name="other-sap-products-supported-on-azure"></a>Inne produkty SAP obsługiwane na platformie Azure
Ogólnie rzecz biorąc, zakłada się, że wraz ze stanem chmur w ramach skalowania, takich jak platforma Azure, większość oprogramowania SAP powinna działać bez problemów funkcjonalnych na platformie Azure. Jednak w przeciwieństwie do wizualizacji w chmurze prywatnej system SAP nadal obsługuje różne produkty SAP dla różnych dostawców chmury hyerpscale. W związku z tym istnieją różne uwagi dotyczące pomocy technicznej SAP wskazujące pomoc techniczną dla platformy Azure dla różnych produktów SAP. 

W przypadku platformy BI dla obiektów biznesowych, [Uwaga dotycząca pomocy technicznej SAP #2145537](https://launchpad.support.sap.com/#/notes/2145537) zawiera listę produktów SAP Business Objects obsługiwanych na platformie Azure. Jeśli występują pytania dotyczące składników lub kombinacji wydań oprogramowania i wersji systemu operacyjnego, które nie są wyświetlane ani obsługiwane, a które są nowsze niż wymienione w minimalnych wersjach, należy otworzyć żądanie pomocy technicznej SAP względem składnika, dla którego chcesz uzyskać pomoc techniczną.

W przypadku obiektów firmowych Data Services [Uwaga dotycząca pomocy technicznej sap #22288344](https://launchpad.support.sap.com/#/notes/2288344) wyjaśnia minimalną obsługę oprogramowania SAP Data Services działającego na platformie Azure. 

> [!NOTE]
> Zgodnie z opisem w uwagach dotyczących pomocy technicznej SAP należy zaewidencjonować usługę SAP PAM w celu zidentyfikowania poprawnego poziomu pakietu dla pomocy technicznej na platformie Azure

Wsparcie SAP Datahub/Vora w usłudze Azure Kubernetes Services (AKS) zostało szczegółowo opisane w [uwagach pomocy technicznej sap #2464722](https://launchpad.support.sap.com/#/notes/2464722)

Pomoc techniczna dla oprogramowania SAP BPC 10,1 SP08 jest opisana w artykule [dotyczącym pomocy technicznej sap #2451795](https://launchpad.support.sap.com/#/notes/2451795)

Pomoc techniczna dla platformy SAP Hybris Commerce platform na platformie Azure znajduje się szczegółowo w [dokumentacji Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html). Jak w przypadku obsługiwanej platformy SAP Hybris Commerce platform system DBMS wygląda następująco:

- SQL Server i Oracle na platformie systemu operacyjnego Windows. W przypadku oprogramowania SAP NetWeaver stosowane są te same wersje minimalne. Aby uzyskać szczegółowe informacje, zobacz [uwagi dotyczące pomocy technicznej SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- SAP HANA w systemach Red Hat i SUSE Linux. SAP HANA typy certyfikowanych maszyn wirtualnych są wymagane zgodnie z opisem we wcześniejszej części [tego dokumentu](#sap-hana-support). Platforma SAP (Hybris) Commerce platform jest traktowana jako obciążenie OLTP
- Baza danych SQL Azure jako platformy SAP (Hybris) w wersji 1811




## <a name="next-steps"></a>Następne kroki
Przeczytaj następne kroki w temacie [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver](./planning-guide.md)

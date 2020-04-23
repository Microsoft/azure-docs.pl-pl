---
title: 'SAP na platformie Azure: Jakie oprogramowanie SAP jest obsługiwane na platformie Azure'
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
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b2f4e7a16c967b26b545d1405f973bf8b8afaae
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086134"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Jakie oprogramowanie SAP jest obsługiwane we wdrożeniach platformy Azure
W tym artykule opisano, jak dowiedzieć się, jakie oprogramowanie SAP jest obsługiwane dla wdrożeń platformy Azure i jakie są niezbędne wersje systemu operacyjnego lub dbms.

Oceniając, czy bieżące oprogramowanie SAP jest obsługiwane i jakie wersje systemu operacyjnego i dbms są obsługiwane przez oprogramowanie SAP na platformie Azure, będziesz potrzebować dostępu do:

- Uwagi dotyczące pomocy technicznej SAP
- Macierz dostępności produktów SAP



## <a name="general-restrictions-for-sap-workload"></a>Ogólne ograniczenia dotyczące obciążenia SAP
Usługi Azure IaaS, które mogą być używane dla obciążenia SAP są ograniczone do sprzętu x86-64 lub x64. Nie ma ofert opartych na sparc lub power cpu, które mają zastosowanie do obciążenia SAP. Klienci, którzy korzystają ze swoich aplikacji w systemach operacyjnych zastrzeżonych do architektury sprzętowej, takich jak IBM mainframe lub AS400, lub w których używane są systemy operacyjne HP-UX, Solaris lub AIX, muszą zmienić swoje aplikacje SAP, w tym DBMS, na jeden z następujących systemów operacyjnych:

- Windows server 64bit dla platformy x86-64
- SUSE linux 64bit dla platformy x86-64
- Czerwony kapelusz Linux 64Bit dla platformy x86-64
- Oracle Linux 64bit dla platformy x86-64

W połączeniu z oprogramowaniem SAP nie są obsługiwane żadne inne wersje systemu operacyjnego ani dystrybucje linuksa. Dokładne szczegóły dotyczące określonych wersji i spraw są udokumentowane w dalszej części dokumentu.


## <a name="you-start-here"></a>Zaczynasz tutaj
Punktem wyjścia jest [notatka o obsłudze sap #1928533](https://launchpad.support.sap.com/#/notes/1928533). W miarę przechodzenia przez tę notatkę SAP od góry do dołu wyświetlanych jest kilka obszarów obsługiwanego oprogramowania i maszyn wirtualnych

Pierwsza sekcja zawiera listę minimalnych wymagań dotyczących wersji operacyjnych, które są ogólnie obsługiwane przez oprogramowanie SAP na maszynach wirtualnych platformy Azure. Jeśli nie spełniasz tych minimalnych wymagań i uruchamiasz starsze wersje tych systemów operacyjnych, musisz uaktualnić wydanie systemu operacyjnego do takiej minimalnej wersji lub jeszcze nowszych wersji. Prawdą jest, że platforma Azure w ogóle będzie obsługiwać starsze wersje niektórych z tych systemów operacyjnych. Ale ograniczenia lub minimalne zwolnienia wymienione są oparte na testach i kwalifikacjach wykonanych i nie zostaną rozszerzone dalej wstecz. 


> [!NOTE]
>Istnieją pewne określone typy maszyn wirtualnych, duże wystąpienia HANA lub obciążeń SAP, które będą wymagać nowszych wersji systemu operacyjnego. Takie przypadki będą wymienione w całym dokumencie. Takie przypadki są wyraźnie udokumentowane w notatkach SAP lub innych publikacjach SAP.

W poniższej sekcji wymieniono ogólne platformy SAP, które są obsługiwane z wersjami, które są obsługiwane i ważniejsze jądra SAP, które są obsługiwane. Wyświetla listę stosów NetWeaver/ABAP lub Java, które są obsługiwane i, które wymagają minimalnych wersji jądra. Nowsze stosy ABAP są obsługiwane na platformie Azure, ale nie wymagają minimalnych wersji jądra, ponieważ zmiany na platformie Azure zostały zaimplementowane od początku opracowywania nowszych stosów

Musisz sprawdzić:

- Czy aplikacje SAP, które są uruchomione, są objęte minimalnych wersji podanych. Jeśli nie, należy zdefiniować nową wersję docelową, sprawdź w macierzy dostępności produktów SAP, jakie kompilacje systemu operacyjnego i kombinacje DBMS są obsługiwane w nowej wersji docelowej. Tak, że można wybrać odpowiednią wersję systemu operacyjnego i wersji DBMS
- Czy chcesz zaktualizować jądra SAP w przejściu na platformę Azure
- Czy trzeba zaktualizować pakiety pomocy technicznej SAP. Szczególnie pakiety obsługi bazy, które mogą być wymagane w przypadkach, gdy wymagane jest przejście do nowszej wersji DBMS


Następna sekcja zawiera więcej szczegółów na temat innych produktów SAP i wersji DBMS, które są obsługiwane przez SAP na platformie Azure dla systemów Windows i Linux. 

> [!NOTE]
> Minimalne wersje różnych dbms jest starannie dobrane i nie zawsze może odzwierciedlać całe spektrum dbms zwalnia różnych dostawców dbms wsparcia na platformie Azure w ogóle. Wiele zagadnień związanych z obciążeniem SAP zostały uwzględnione w celu zdefiniowania tych minimalnych wydań. Nie ma żadnego wysiłku, aby przetestować i zakwalifikować starsze wersje DBMS. 

> [!NOTE]
> Wymienione minimalne wersje reprezentują starszą wersję systemów operacyjnych i wersji bazy danych. Gorąco zachęcamy do korzystania z najnowszych wersji systemu operacyjnego i wersji baz danych. W wielu przypadkach nowsze wersje systemu operacyjnego i bazy danych uwzględniały przypadek użycia działającego w chmurze publicznej i dostosowywały kod w celu optymalizacji pod kątem działania w chmurze publicznej lub w szczególności na platformie Azure

## <a name="oracle-dbms-support"></a>Pomoc techniczna w programie Oracle DBMS
System operacyjny, wersje Oracle DBMS i funkcje Oracle obsługiwane na platformie Azure są wyraźnie wymienione w [notatce technicznej SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). Esencja z tej notatki można podsumować w ten sposób:

- Minimalna wersja Oracle obsługiwana na maszynach wirtualnych platformy Azure, które są certyfikowane dla NetWeaver, to Oracle 11g Release 2 Patchset 3 (11.2.0.4)
- Jako systemy operacyjne dla gości kwalifikują się tylko systemy Windows i Oracle Linux. Dokładne wersje systemu operacyjnego i związane z nimi minimalne wersje DBMS są wymienione w nocie
- Obsługa oracle linux rozciąga się również na klienta Oracle DBMS. Oznacza to, że wszystkie składniki SAP, takie jak wystąpienia okien dialogowych ABAP lub Java Stack, muszą być również uruchamiane w systemie Oracle Linux. Tylko składniki SAP w takim systemie SAP, które nie połączyłyby się z Oracle DBMS, będą mogły uruchamiać inny system operacyjny Linux
- Oracle RAC nie jest obsługiwany 
- Oracle ASM jest obsługiwany w niektórych przypadkach. Szczegóły są wymienione w nocie
- Systemy SAP inne niż Unicode są obsługiwane tylko z serwerami aplikacji z systemem operacyjnym gościa systemu Windows. Gościnnym systemem operacyjnym dbms może być Oracle Linux lub Windows. Przyczyna tego ograniczenia jest widoczna podczas sprawdzania macierzy dostępności produktów SAP (PAM). W przypadku systemu Oracle Linux sap nigdy nie wydał jąder SAP innych niż Unicode

Znając wersje DBMS, które są obsługiwane przy pomocy docelowej infrastruktury platformy Azure, należy sprawdzić macierz dostępności produktów SAP, czy wersje systemu operacyjnego i wymagane usługi dbms są obsługiwane w wersjach produktów SAP, które zamierzasz uruchomić. 


## <a name="sap-hana-support"></a>Pomoc techniczna firmy SAP HANA
Na platformie Azure istnieją dwie usługi, które mogą służyć do uruchamiania bazy danych HANA:

- Azure Virtual Machines
- [Duże wystąpienia HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

W przypadku uruchamiania systemu SAP HANA sap ma więcej i silniejsze warunki infrastruktury musi spełniać niż do uruchamiania NetWeaver lub innych aplikacji SAP i DBMS. W rezultacie mniejsza liczba maszyn wirtualnych platformy Azure kwalifikuje się do uruchamiania sap HANA DBMS. Listę obsługiwanej infrastruktury platformy Azure obsługiwanej dla sap HANA można znaleźć w tak zwanym [katalogu sprzętowym SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

> [!NOTE]
> Jednostki zaczynające się od litery "S" są [jednostkami dużych wystąpień HANA.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 

> [!NOTE]
> Sap nie ma określonego certyfikatu zależnego od głównych wersji SAP HANA. Wbrew powszechnej opinii, **scenariusz certyfikacji** kolumny w [certyfikowanych platformach IaaS z certyfikatem HANA, kolumna](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)nie zawiera **oświadczenia o certyfikowanym wydaniu głównym lub niewielkim HANA.** Należy założyć, że wszystkie wymienione jednostki, które mogą być używane dla HANA 1.0 i HANA 2.0 tak długo, jak certyfikowane wersje systemu operacyjnego dla określonych jednostek są obsługiwane przez wersje HANA 1.0, jak również. 

Do użycia sap HANA, różne minimalne wersje systemu operacyjnego mogą mieć zastosowanie niż w przypadku ogólnych przypadków NetWeaver. Należy sprawdzić obsługiwane systemy operacyjne dla każdej jednostki indywidualnie, ponieważ mogą one się różnić. Można to zrobić, klikając na każdej jednostce. Pojawią się więcej szczegółów. Jednym z wymienionych szczegółów jest różne systemy operacyjne obsługiwane dla tej konkretnej jednostki.

> [!NOTE]
> Jednostki dużych wystąpień platformy Azure HANA są bardziej restrykcyjne w przypadku obsługiwanych systemów operacyjnych w porównaniu z maszynami wirtualnymi platformy Azure. Z drugiej strony maszyny wirtualne platformy Azure mogą wymuszać nowsze wersje operacyjne jako minimalne wersje. Jest to szczególnie ważne w przypadku niektórych większych jednostek maszyn wirtualnych, które wymagały zmian w jądrach Linuksa

Znając obsługiwany system operacyjny dla infrastruktury platformy Azure, należy sprawdzić [uwagę pomocy technicznej SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) dla dokładnych wersji SAP HANA i poziomów poprawek, które są obsługiwane w jednostkach platformy Azure, które są kierowane. 

> [!IMPORTANT]
> Krok sprawdzania dokładnych wersji SAP HANA i obsługiwanych poziomów poprawek jest bardzo ważny. W wielu przypadkach obsługa określonej wersji systemu operacyjnego zależy od określonego poziomu poprawek plików wykonywalnych SAP HANA.

Jak wiesz, konkretne wersje HANA można uruchomić na docelowej infrastruktury platformy Azure, należy sprawdzić w Macierzy dostępności produktów SAP, aby dowiedzieć się, czy istnieją ograniczenia z wersji produktów SAP, które obsługują wersje HANA, które zostały odfiltrowane


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Certyfikowane maszyny wirtualne platformy Azure i jednostki dużych wystąpień HANA i przepływność transakcji biznesowych
Oprócz oceny obsługiwanych wersji systemu operacyjnego, wersji DBMS i zależnych wersji oprogramowania SAP dla jednostek infrastruktury platformy Azure, musisz zakwalifikować te jednostki według przepływności transakcji biznesowych, która jest wyrażona w jednostce "SAP" przez SAP. Wszystkie rozmiary SAP zależy od obliczeń SAPS. Oceniając istniejące systemy SAP, zwykle można, za pomocą dostawcy infrastruktury, obliczyć SAPS jednostek. Dla warstwy DBMS, a także dla warstwy aplikacji. W innych przypadkach, gdy tworzona jest nowa funkcjonalność, ćwiczenie zmiany rozmiaru za pomocą sap może ujawnić wymagane numery SAPS dla warstwy aplikacji i warstwy DBMS. Jako dostawca infrastruktury firma Microsoft jest zobowiązana do zapewnienia charakterystyki przepływności SAP różnych jednostek, które mają certyfikat NetWeaver i/lub HANA.

W przypadku maszyn wirtualnych platformy Azure te numery przepływności SAPS są udokumentowane w [nocie pomocy technicznej SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). W przypadku jednostek dużych wystąpień platformy Azure HANA numery przepływności SAPS są udokumentowane w [nocie pomocy technicznej SAP #2316233](https://launchpad.support.sap.com/#/notes/2316233)

Patrząc na [notatkę techniczną SAP #1928533,](https://launchpad.support.sap.com/#/notes/1928533)obowiązują następujące uwagi:

- **W przypadku maszyn wirtualnych platformy Azure z serii M i maszyn wirtualnych platformy Azure z serii Mv2 obowiązują różne minimalne wersje systemu operacyjnego niż w przypadku innych typów maszyn wirtualnych platformy Azure.** Wymaganie dotyczące nowszych wersji systemu operacyjnego opiera się na zmianach, które różni dostawcy systemów operacyjnych musieli podać w swoich wersjach systemu operacyjnego, aby włączyć swoje systemy operacyjne uruchomione na określonych typach maszyn wirtualnych platformy Azure lub zoptymalizować wydajność i przepustowość obciążenia SAP dla tych typów maszyn wirtualnych
- Istnieją dwie tabele, które określają różne typy maszyn wirtualnych. Druga tabela określa przepływność SAPS dla typów maszyn wirtualnych platformy Azure, które obsługują tylko usługę Azure standard Storage. Wdrożenie dbms na jednostkach określonych w drugiej tabeli notatki nie jest obsługiwane


## <a name="other-sap-products-supported-on-azure"></a>Inne produkty SAP obsługiwane na platformie Azure
Ogólnie rzecz biorąc założenie jest to, że w stanie chmur hiperskalowych, takich jak Azure, większość oprogramowania SAP powinny działać bez problemów funkcjonalnych na platformie Azure. Niemniej jednak i w przeciwieństwie do wizualizacji chmury prywatnej, SAP nadal wyraża obsługę różnych produktów SAP jawnie dla różnych dostawców chmury hyerpscale. W rezultacie istnieją różne uwagi dotyczące obsługi SAP wskazujące obsługę platformy Azure dla różnych produktów SAP. 

Dla platformy BI obiektów biznesowych, [uwaga pomocy technicznej SAP #2145537](https://launchpad.support.sap.com/#/notes/2145537) zawiera listę produktów SAP Business Objects obsługiwanych na platformie Azure. Jeśli istnieją pytania dotyczące składników lub kombinacji wersji oprogramowania i wersji systemu operacyjnego, które wydają się nie być wymienione lub obsługiwane i które są nowsze niż minimalne wersje wymienione, należy otworzyć żądanie pomocy technicznej SAP względem składnika, o który pytasz o pomoc techniczną.

W przypadku usług danych obiektów biznesowych [#22288344 pkt.](https://launchpad.support.sap.com/#/notes/2288344) 

> [!NOTE]
> Jak wskazano w nocie pomocy technicznej SAP, należy sprawdzić w SAP PAM, aby zidentyfikować poprawny poziom pakietu pomocy technicznej, który ma być obsługiwany na platformie Azure

Obsługa sap datahub/Vora w usługach Azure Kubernetes Services (AKS) jest szczegółowo opisana w [nocie pomocy technicznej SAP #2464722](https://launchpad.support.sap.com/#/notes/2464722)

Obsługa sap BPC 10.1 SP08 jest opisana w [nocie pomocy technicznej SAP #2451795](https://launchpad.support.sap.com/#/notes/2451795)

Pomoc techniczna dla platformy SAP Hybris Commerce Platform na platformie Azure jest szczegółowo opisana w [dokumentacji hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html). W przypadku obsługiwanych programów DBMS dla platformy SAP Hybris Commerce, wymienia się w nim:

- SQL Server i Oracle na platformie systemu operacyjnego Windows. Te same minimalne zwolnienia mają zastosowanie, jak w przypadku SAP NetWeaver. Szczegółowe informacje na temat informacji na temat pomocy [technicznej sap #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- SAP HANA na Red Hat i SUSE Linux. Typy maszyn wirtualnych z certyfikatem SAP HANA są wymagane zgodnie z wcześniejszymi dokumentami w [tym dokumencie.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure#sap-hana-support) Platforma handlowa SAP (Hybris) jest uważana za obciążenie OLTP
- Baza danych SQL Azure DB od platformy handlowej SAP (Hybris) w wersji 1811




## <a name="next-steps"></a>Następne kroki
Przeczytaj kolejne kroki w [planowaniu i implementacji maszyn wirtualnych platformy Azure dla sap NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)


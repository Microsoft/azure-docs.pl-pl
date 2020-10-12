---
title: Przenoszenie zasobów obliczeniowych komputerów mainframe do usługi Azure Virtual Machines
description: Zasoby obliczeniowe platformy Azure porównują favorably z pojemnością komputera mainframe, dzięki czemu można migrować i unowocześnieniować aplikacje IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 9c5941ec88cd793961ad66245d0dc0b5e0d7772f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86998939"
---
# <a name="move-mainframe-compute-to-azure"></a>Przenoszenie obliczeń mainframe na platformę Azure

Komputery mainframe mają reputację wysokiej niezawodności i dostępności i nadal są zaufanymi szkieletami wielu przedsiębiorstw. Często są uważane za praktycznie nieograniczoną skalowalność i moc obliczeniową. Jednak niektóre przedsiębiorstwa pomogły obrosnąć możliwości największych dostępnych komputerów mainframe. Jeśli te dźwięki są takie same, platforma Azure oferuje elastyczność, zasięg i oszczędności infrastruktury.

Aby uruchamiać obciążenia mainframe na Microsoft Azure, musisz wiedzieć, jak możliwości obliczeniowe komputera mainframe są porównywane z platformą Azure. W tym artykule opisano, jak uzyskać porównywalne wyniki na platformie Azure w oparciu o mainframe firmy IBM z14 (najbardziej aktualny model w przypadku tego zapisu).

Aby rozpocząć, należy rozważyć środowiska obok siebie. Poniższy rysunek zawiera porównanie środowiska mainframe na potrzeby uruchamiania aplikacji w środowisku hostingu platformy Azure.

![Usługi platformy Azure i środowiska emulacji oferują porównywalną pomoc techniczną i upraszczają migrację](media/mainframe-compute-azure.png)

Możliwości komputerów mainframe są często używane w systemach przetwarzania transakcji online (OLTP), które obsługują miliony aktualizacji tysięcy użytkowników. Te aplikacje często używają oprogramowania do przetwarzania transakcji, obsługi ekranu i wprowadzania tekstu. Mogą korzystać z systemu kontroli informacji o klientach (CICS), systemu zarządzania informacjami (ISP) lub pakietu interfejsu transakcji (TIP).

Jak widać na ilustracji, Emulator modułu TPM na platformie Azure może obsłużyć obciążenia CICS i IMS. Emulator systemu wsadowego na platformie Azure wykonuje rolę języka kontroli zadań (JCL). Dane mainframe są migrowane do baz danych platformy Azure, takich jak Azure SQL Database. Usługi platformy Azure lub inne oprogramowanie hostowane w usłudze Azure Virtual Machines mogą służyć do zarządzania systemem.

## <a name="mainframe-compute-at-a-glance"></a>Szybkie obliczanie komputera mainframe

W z14 mainframe, procesory są rozmieszczone w maksymalnie czterech *szufladach*. *Szuflada* to po prostu klaster procesorów i układów. Każda szuflada może mieć sześć mikroprocesorów aktywnych procesorów (CP), a każdy z nich ma 10 mikroukładów kontrolera systemowego. W terminologii Intel x86 istnieje sześć gniazd na szufladę, 10 rdzeni na gniazdo i cztery szuflady. Ta architektura zapewnia sztywny odpowiednik 24 gniazd i 240 rdzeni, maksimum dla z14.

Szybki z14 CP ma szybkość zegara 5,2 GHz. Zazwyczaj z14 jest dostarczany ze wszystkimi CPs w polu. Są one aktywowane zgodnie z wymaganiami. Klient jest często obciążany przez co najmniej cztery godziny obliczeń na miesiąc, pomimo faktycznego użycia.

Procesor mainframe można skonfigurować jako jeden z następujących typów:

- Procesor Ogólnego przeznaczenia (GP)
- System z zintegrowanym procesorem informacji (zIIP)
- Procesor zintegrowany z systemem Linux (IFL)
- Procesor pomocy systemu (SAP)
- Zintegrowany procesor funkcji sprzęgania (ICF)

## <a name="scaling-mainframe-compute-up-and-out"></a>Skalowanie obliczeń komputera mainframe w górę i w dół

Systemy mainframe firmy IBM umożliwiają skalowanie do 240 rdzeni (bieżący rozmiar z14 dla pojedynczego systemu). Ponadto Komputery mainframe firmy IBM można skalować w poziomie za pomocą funkcji zwanej obiektem sprzęgającym (CF). CF umożliwia wielu systemom mainframe dostęp do tych samych danych jednocześnie. Korzystając z CF, Komputery mainframe Parallel Sysplex Technology groups. Po napisaniu tego przewodnika funkcja Parallel Sysplex obsługuje grupy 32 procesorów 64. Do 2 048 procesorów można grupować w ten sposób w celu skalowania wydajności obliczeniowej.

CF umożliwia Klastrom obliczeniowym udostępnianie danych za pomocą bezpośredniego dostępu. Jest on używany do blokowania informacji, pamięci podręcznej i listy udostępnionych zasobów danych. Sysplex równoległy przy użyciu co najmniej jednego CFsu można traktować jako klaster obliczeniowy skalowalny w poziomie. Aby uzyskać więcej informacji o tych funkcjach, zobacz [Parallel Sysplex on the IBM z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) w witrynie sieci Web firmy IBM.

Aplikacje mogą korzystać z tych funkcji w celu zapewnienia wydajności skalowalności i wysokiej dostępności. Aby uzyskać informacje o tym, jak CICS może używać równoległych Sysplex z CF, Pobierz [IBM CICS i obiekt sprzęgający: poza podstawowymi](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) metodami.

## <a name="azure-compute-at-a-glance"></a>Usługa Azure COMPUTE w skrócie

Niektórzy z nich uważali, że serwery z technologią Intel nie są tak wydajne jak Komputery mainframe. Jednak nowe komputery z systemami opartymi na architekturze Intel mają takie same możliwości obliczeniowe jak Komputery mainframe. W tej sekcji opisano opcje "infrastruktura jako usługa" platformy Azure (IaaS) na potrzeby przetwarzania i magazynowania. Platforma Azure udostępnia również opcje platformy jako usługi (PaaS), ale ten artykuł koncentruje się na opcjach IaaS, które zapewniają porównywalną pojemność komputera mainframe.

Platforma Azure Virtual Machines zapewnia moc obliczeniową w różnych rozmiarach i typach. Na platformie Azure wirtualne użycie procesora (vCPU) jest w przybliżeniu równe rdzeniu na komputerze mainframe.

Obecnie zakres rozmiarów maszyn wirtualnych platformy Azure zapewnia od 1 do 128 procesorów wirtualnych vCPU. Typy maszyn wirtualnych są zoptymalizowane pod kątem określonych obciążeń. Na przykład na poniższej liście przedstawiono typy maszyn wirtualnych (bieżące w przypadku tego zapisu) i ich Zalecane zastosowania:

| Rozmiar     | Typ i opis                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Seria D | Ogólnego przeznaczenia z 64 vCPU i maksymalnie 3,5 częstotliwości zegara                           |
| Seria E | Zoptymalizowane pod kątem pamięci z maksymalnie 64 procesorów wirtualnych vCPU                                                 |
| Seria F | Obliczenia zoptymalizowane pod kątem maksymalnie 64 procesorów wirtualnych vCPU i 3.. 7 GHz                       |
| Seria H | Optymalizacja pod kątem aplikacji obliczeniowych o wysokiej wydajności (HPC)                          |
| Seria L | Magazyn zoptymalizowany pod kątem aplikacji o wysokiej przepływności, które są objęte bazami danych, takimi jak NoSQL |
| Seria M | Największe maszyny wirtualne obliczeniowe i zoptymalizowane pod kątem pamięci z maksymalnie 128 procesorów wirtualnych vCPU                        |

Aby uzyskać szczegółowe informacje o dostępnych maszynach wirtualnych, zobacz [serie maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Z14 mainframe może mieć do 240 rdzeni. Jednak Komputery mainframe z14 niemal nigdy nie używają wszystkich rdzeni dla jednej aplikacji lub obciążenia. Zamiast tego Komputery mainframe dzielą obciążenia na partycje logiczne (LPARs), a LPARs mają klasyfikacje — MIPS (miliony instrukcji na sekundę) lub MSU (mln jednostek usługi). Podczas określania porównywalnego rozmiaru maszyny wirtualnej, który jest wymagany do uruchomienia obciążenia komputera mainframe na platformie Azure, należy wziąć pod uwagę klasyfikację MIPS (lub MSU).

Poniżej przedstawiono ogólne oszacowania:

-   150 MIPS na vCPU

-   1 000 MIPS na procesor

Aby określić prawidłowy rozmiar maszyny wirtualnej dla danego obciążenia w LPAR, najpierw Zoptymalizuj maszynę wirtualną dla obciążenia. Następnie określ wymaganą liczbę procesorów wirtualnych vCPU. Szacunkowa ocena to 150 MIPS na vCPU. Na przykład na podstawie tego szacunku maszyna wirtualna z serii F z 16 procesorów wirtualnych vCPU może łatwo obsłużyć obciążenie IBM DB2 pochodzące z LPAR z obsługą funkcji 2 400 MIPS.

## <a name="azure-compute-scale-up"></a>Skalowanie obliczeniowe platformy Azure — w górę

Maszyny wirtualne serii M można skalować w górę do 128 procesorów wirtualnych vCPU (w momencie zapisania tego artykułu). Przy użyciu zachowawczego oszacowania 150 MIPS na vCPU, maszyna wirtualna serii M jest równa około 19 000 MIPS. Ogólna zasada szacowania MIPS dla komputera mainframe to 1 000 MIPS na procesor. Z14 mainframe może mieć do 24 procesorów i zapewnić około 24 000 MIPS dla jednego systemu mainframe.

Największy z14 mainframe ma około 5 000 MIPS więcej niż największą maszynę wirtualną dostępną na platformie Azure. Należy pamiętać, że należy porównać sposób wdrażania obciążeń. Jeśli system mainframe ma zarówno aplikację, jak i relacyjną bazę danych, są one zwykle wdrażane na tym samym fizycznym komputerze mainframe — każdy w osobnym LPAR. To samo rozwiązanie na platformie Azure jest często wdrażane przy użyciu jednej maszyny wirtualnej dla aplikacji oraz oddzielnej, odpowiednio dopasowanej maszyny wirtualnej dla bazy danych.

Na przykład jeśli system M64 vCPU obsługuje aplikację, a M96 vCPU jest używany dla bazy danych, około 150 procesorów wirtualnych vCPU jest konieczna — lub około 24 000 MIPS, jak pokazano na poniższej ilustracji.

![Porównywanie wdrożeń obciążeń 24 000 MIPS](media/mainframe-compute-mips.png)

Podejście polega na migracji LPARs do poszczególnych maszyn wirtualnych. Następnie system Azure można łatwo skalować do rozmiaru wymaganego dla większości aplikacji wdrożonych w jednym systemie mainframe.

## <a name="azure-compute-scale-out"></a>Skalowanie obliczeniowe na platformie Azure

Jedną z zalet rozwiązań opartych na platformie Azure jest możliwość skalowania w poziomie. Skalowanie sprawia, że dla aplikacji jest dostępna niemal nieograniczona pojemność obliczeniowa. Platforma Azure obsługuje wiele metod skalowania mocy obliczeniowej:

- **Równoważenie obciążenia w klastrze.** W tym scenariuszu aplikacja może użyć [modułu równoważenia obciążenia](../../../../load-balancer/load-balancer-overview.md) lub Menedżera zasobów, aby rozłożyć obciążenie między wieloma maszynami wirtualnymi w klastrze. Jeśli wymagana jest większa pojemność obliczeniowa, do klastra dodawane są dodatkowe maszyny wirtualne.

- **Zestawy skalowania maszyn wirtualnych.** W tym scenariuszu pierścieniowym aplikacja może skalować do dodatkowych [zasobów obliczeniowych](../../../../virtual-machine-scale-sets/overview.md) na podstawie użycia maszyny wirtualnej. Gdy zajdzie taka potrzeba, liczba maszyn wirtualnych w zestawie skalowania może również się nie pomniejszać, co zapewnia efektywne wykorzystanie mocy obliczeniowej.

- **Skalowanie PaaS.** Oferty usługi Azure PaaS umożliwiają skalowanie zasobów obliczeniowych. Na przykład [platforma Azure Service Fabric](../../../../service-fabric/service-fabric-overview.md) przydziela zasoby obliczeniowe w celu osiągnięcia wzrostu liczby żądań.

- **Klastry Kubernetes.** Aplikacje na platformie Azure mogą używać [klastrów Kubernetes](../../../../aks/concepts-clusters-workloads.md) dla usług obliczeniowych dla określonych zasobów. Azure Kubernetes Service (AKS) to usługa zarządzana, która organizuje węzły, pule i klastry Kubernetes na platformie Azure.

Aby wybrać odpowiednią metodę skalowania zasobów obliczeniowych, ważne jest, aby zrozumieć, w jaki sposób platforma Azure i Komputery mainframe różnią się. Klucz polega na tym, czy dane są współużytkowane przez zasoby obliczeniowe. Na platformie Azure dane (domyślnie) nie są zazwyczaj udostępniane przez wiele maszyn wirtualnych. Jeśli udostępnianie danych jest wymagane przez wiele maszyn wirtualnych w klastrze obliczeniowym skalowalnym w poziomie, udostępnione dane muszą znajdować się w zasobie, który obsługuje tę funkcję. W przypadku usługi Azure Data Sharing obejmuje magazyn, jak opisano w poniższej sekcji.

## <a name="azure-compute-optimization"></a>Optymalizacja obliczeń na platformie Azure

Każdą warstwę przetwarzania można zoptymalizować w architekturze platformy Azure. Używaj najbardziej odpowiedniego typu maszyn wirtualnych i funkcji dla każdego środowiska. Na poniższej ilustracji przedstawiono jeden potencjalny wzorzec wdrażania maszyn wirtualnych na platformie Azure w celu obsługi aplikacji CICS, która korzysta z bazy danych DB2. W lokacji głównej wdrożenia maszyn wirtualnych, produkcji wstępnej i testowania są wdrażane z wysoką dostępnością. Lokacja dodatkowa służy do tworzenia kopii zapasowych i odzyskiwania po awarii.

Każda warstwa może również zapewnić odpowiednie usługi odzyskiwania po awarii. Na przykład maszyny wirtualne z maszynami wirtualnymi i bazami danych mogą wymagać odzyskiwania gorącego lub ciepłego, podczas gdy maszyny wirtualne do tworzenia i testowania obsługują zimne odzyskiwanie.

![Wdrożenie o wysokiej dostępności, które obsługuje odzyskiwanie po awarii](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Następne kroki

- [Migracja komputera mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Rehostowanie komputera mainframe na platformie Azure Virtual Machines](../overview.md)
- [Przenoszenie magazynu mainframe na platformę Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Zasoby IBM

- [Równoległa Sysplex na IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS i funkcja sprzęgania: poza podstawowymi](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Tworzenie wymaganych użytkowników dla instalacji funkcji programu DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt — Utwórz wystąpienie polecenia](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Rozwiązanie klastrowanej bazy danych DB2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government w chmurze dla aplikacji dla komputerów mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft i FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Więcej zasobów migracji

- [Przewodnik po podnoszenia i przesunięć wirtualnego centrum danych platformy Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

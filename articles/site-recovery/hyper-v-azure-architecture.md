---
title: Architektura odzyskiwania po awarii funkcji Hyper-V w Azure Site Recovery
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas wdrażania odzyskiwania po awarii dla lokalnych maszyn wirtualnych funkcji Hyper-V (bez programu VMM) na platformie Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: c5025b83619b505728bfdf5c4e1ccc81d3bb225e
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654765"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Hyper-V to Azure disaster recovery architecture (Architektura odzyskiwania po awarii z maszyn wirtualnych funkcji Hyper-V na platformę Azure)


W tym artykule opisano architekturę i procesy używane podczas replikowania, przełączania awaryjnego i odzyskiwania maszyn wirtualnych funkcji Hyper-V między lokalnymi hostami funkcji Hyper-V i platformą Azure przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .

Hosty funkcji Hyper-V można opcjonalnie zarządzać w chmurach prywatnych System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Składniki architektury — funkcja Hyper-V bez programu VMM

W poniższej tabeli i grafice przedstawiono ogólny widok składników służących do replikacji funkcji Hyper-V do platformy Azure, gdy hosty funkcji Hyper-V nie są zarządzane przez program VMM.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konto usługi Azure Storage i sieć platformy Azure. | Zreplikowane dane z obciążeń lokalnych maszyn wirtualnych są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są tworzone przy użyciu replikowanych danych obciążenia, gdy wystąpi tryb failover z lokacji lokalnej.<br/><br/> Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Hyper-V** | Podczas wdrażania Site Recovery należy zebrać hosty i klastry funkcji Hyper-V do lokacji funkcji Hyper-V. Na każdym autonomicznym hoście funkcji Hyper-V lub na każdym węźle klastra funkcji Hyper-V należy zainstalować dostawcę Azure Site Recovery i agenta Recovery Services. | Dostawca koordynuje replikację za pomocą usługi Site Recovery przez Internet. Agent usługi Recovery Services obsługuje replikację danych.<br/><br/> Komunikacja zarówno ze strony dostawcy, jak i agenta, jest bezpieczna i szyfrowana. Zreplikowane dane w usłudze Azure Storage również są szyfrowane.
**Maszyny wirtualne funkcji Hyper-V** | Co najmniej jedna maszyna wirtualna działająca w funkcji Hyper-V. | Niczego nie trzeba jawnie instalować na maszynach wirtualnych.


**Architektura funkcji Hyper-V do platformy Azure (bez programu VMM)**

![Diagram przedstawiający lokalną lokację funkcji Hyper-V do architektury platformy Azure bez programu VMM.](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Składniki architektury — funkcja Hyper-V z programem VMM

W poniższej tabeli i grafice przedstawiono ogólny widok składników służących do replikacji funkcji Hyper-V do platformy Azure, gdy hosty funkcji Hyper-V są zarządzane w chmurach programu VMM.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konto usługi Azure Storage i sieć platformy Azure. | Zreplikowane dane z obciążeń lokalnych maszyn wirtualnych są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są tworzone przy użyciu replikowanych danych, gdy wystąpi przejście w tryb failover z lokacji lokalnej.<br/><br/> Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Serwer VMM** | Serwer VMM ma co najmniej jedną chmurę zawierającą hosty funkcji Hyper-V. | Należy zainstalować dostawcę Site Recovery na serwerze programu VMM, aby zorganizować replikację z Site Recovery i zarejestrować serwer w magazynie Recovery Services.
**Host funkcji Hyper-V** | Co najmniej jeden host/klaster funkcji Hyper-V zarządzany przez program VMM. |  Agenta Recovery Services należy zainstalować na każdym hoście funkcji Hyper-V lub w węźle klastra.
**Maszyny wirtualne funkcji Hyper-V** | Co najmniej jedna maszyna wirtualna uruchomiona na serwerze hosta funkcji Hyper-V. | Niczego nie trzeba jawnie instalować na maszynach wirtualnych.
**Sieć** | Sieci logiczne i maszyn wirtualnych skonfigurowane na serwerze VMM. Sieć maszyn wirtualnych powinna być połączona z siecią logiczną skojarzoną z chmurą. | Sieci VMNETWORK są mapowane na sieci wirtualne platformy Azure. Gdy maszyny wirtualne platformy Azure są tworzone po przejściu w tryb failover, zostaną dodane do sieci platformy Azure zamapowanej do sieci maszyn wirtualnych.

**Architektura funkcji Hyper-V do platformy Azure (z programem VMM)**

![Diagram przedstawiający lokalną lokację funkcji Hyper-V w architekturze platformy Azure przy użyciu programu VMM.](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)

## <a name="set-up-outbound-network-connectivity"></a>Konfigurowanie wychodzącej łączności sieciowej

Aby Site Recovery działały zgodnie z oczekiwaniami, należy zmodyfikować wychodzącą łączność sieciową, aby umożliwić replikację danego środowiska.

> [!NOTE]
> Usługa Site Recovery nie obsługuje sterowania łącznością sieciową za pomocą uwierzytelniającego serwera proxy.

### <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Zezwól na dostęp do tych adresów URL:

| **Nazwa**                  | **Commercial**                               | **Instytucje rządowe**                                 | **Opis** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Magazyn                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej znajdującym się w regionie źródłowym. |
| Usługa Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Umożliwia autoryzację i uwierzytelnianie przy użyciu adresów URL usługi Site Recovery. |
| Replikacja               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Umożliwia komunikację między maszyną wirtualną a usługą Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Umożliwia maszynie wirtualnej zapisywanie danych monitorowania i danych diagnostycznych usługi Site Recovery. |


## <a name="replication-process"></a>Proces replikacji

![Diagram przedstawiający proces replikacji funkcji Hyper-V do platformy Azure](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Proces replikacji i odzyskiwania**


### <a name="enable-protection"></a>Włączanie ochrony

1. Po włączeniu ochrony dla maszyny wirtualnej funkcji Hyper-V, w witrynie Azure Portal lub środowisku lokalnym, zostanie uruchomione zadanie **Włącz ochronę**.
2. To zadanie sprawdza, czy maszyna spełnia wymagania wstępne, a następnie wywołuje metodę [CreateReplicationRelationship](/windows/win32/hyperv_v2/createreplicationrelationship-msvm-replicationservice), aby skonfigurować replikację za pomocą określonych ustawień.
3. Zadanie uruchamia replikację początkową, wywołując metodę [StartReplication](/windows/win32/hyperv_v2/startreplication-msvm-replicationservice), aby zainicjować pełną replikację maszyny wirtualnej i wysłać dyski wirtualne maszyny wirtualnej na platformę Azure.
4. Zadanie można monitorować na karcie **zadania** .      ![Zrzut ekranu przedstawiający listę zadań na karcie zadania. ](media/hyper-v-azure-architecture/image1.png) Zrzut ekranu przedstawiający ![ ekran Włącz ochronę z dodatkowymi szczegółami.](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Początkowa replikacja danych

1. Gdy replikacja początkowa jest wyzwalana, wykonywana jest migawka [migawki maszyny wirtualnej funkcji Hyper-V](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560637(v=ws.10)) .
2. Wirtualne dyski twarde w maszynie wirtualnej są replikowane pojedynczo, dopóki nie zostaną skopiowane na platformę Azure. Może to chwilę potrwać, w zależności od rozmiaru maszyny wirtualnej i przepustowości sieci. [Dowiedz się, jak](https://support.microsoft.com/kb/3056159) zwiększyć przepustowość sieci.
3. Jeśli podczas początkowej replikacji wystąpią zmiany dysku, śledzenie replikacji funkcji Hyper-V Replica śledzi zmiany w postaci dzienników replikacji funkcji Hyper-V (. HRL). Te pliki dzienników znajdują się w tym samym folderze co dyski. Każdy dysk ma skojarzony plik. HRL, który jest wysyłany do magazynu pomocniczego. Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
4. Po zakończeniu replikacji początkowej migawka maszyny wirtualnej jest usuwana.
5. Różnicowe zmiany dysku w dzienniku są synchronizowane i scalane z dyskiem nadrzędnym.


### <a name="finalize-protection-process"></a>Finalizowanie procesu ochrony

1. Po zakończeniu replikacji początkowej zostanie uruchomione zadanie **finalizowanie ochrony na maszynie wirtualnej** . Konfiguruje sieć i inne ustawienia po replikacji, aby maszyna wirtualna była chroniona.
2. Na tym etapie można sprawdzić ustawienia maszyny wirtualnej, aby upewnić się, że jest ono gotowe do pracy w trybie failover. Aby sprawdzić, czy maszyna wirtualna jest w trybie failover zgodnie z oczekiwaniami, można uruchomić polecenie drążenia odzyskiwania po awarii. 


## <a name="delta-replication"></a>Replikacja różnicowa

1. Po replikacji początkowej rozpoczyna się replikacja różnicowa, zgodnie z zasadami replikacji.
2. Śledzenie replikacji funkcji Hyper-V Replica śledzi zmiany w wirtualnym dysku twardym jako pliki. HRL. Z każdym dyskiem skonfigurowanym pod kątem replikacji jest skojarzony plik hrl.
3. Dziennik jest wysyłany do konta magazynu klienta. Gdy dziennik jest przesyłany do platformy Azure, zmiany na dysku podstawowym są śledzone w innym pliku dziennika, w tym samym folderze.
4. Podczas replikacji początkowej i różnicowej można monitorować maszynę wirtualną w Azure Portal.

### <a name="resynchronization-process"></a>Proces ponownej synchronizacji

1. Jeśli replikacja różnicowa nie powiedzie się, a pełna replikacja byłaby kosztowna pod względem przepustowości lub czasu, maszyna wirtualna jest oznaczana do ponownej synchronizacji.
    - Jeśli na przykład pliki hrl będą zajmować 50% rozmiaru dysku, to maszyna wirtualna zostanie oznaczona do ponownej synchronizacji.
    -  Domyślnie ponowna synchronizacja jest zaplanowana do automatycznego uruchamiania poza godzinami pracy.
1.  Ponowna synchronizacja wysyła tylko dane różnicowe.
    - Minimalizuje ilość danych wysyłanych przez Obliczanie sum kontrolnych źródłowej i docelowej maszyny wirtualnej.
    - Używa algorytmu fragmentacji o stałym bloku, w którym pliki źródłowe i docelowe są podzielone na stałe fragmenty.
    - Generowane są sumy kontrolne dla każdego fragmentu. Są one porównywane w celu określenia, które bloki ze źródła mają być stosowane do obiektu docelowego.
2. Po ukończeniu ponownej synchronizacji replikacja różnicowa powinna zostać wznowiona.
3. Jeśli nie chcesz czekać na domyślną ponowną synchronizację poza godzinami, możesz ponownie zsynchronizować maszynę wirtualną ręcznie. Na przykład jeśli wystąpi awaria. W tym celu w Azure Portal wybierz maszynę wirtualną > ponownie **zsynchronizuj**.

    ![Zrzut ekranu przedstawiający opcję ponownej synchronizacji.](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Ponów próbę wykonania procesu

Jeśli wystąpi błąd replikacji, może zostać użyty wbudowany mechanizm ponawiania. Ponowna próba została sklasyfikowana zgodnie z opisem w tabeli.

**Kategoria** | **Szczegóły**
--- | ---
**Błąd nieodwracalny** | Nie jest podejmowana próba ponowienia. Maszyna wirtualna będzie mieć stan **Krytyczny** i będzie wymagana interwencja administratora.<br/><br/> Przykłady tych błędów obejmują przerwany łańcuch wirtualnego dysku twardego, nieprawidłowy stan maszyny wirtualnej repliki, błędy uwierzytelniania sieci, błędy autoryzacji i błędy maszyn wirtualnych (w przypadku autonomicznych serwerów funkcji Hyper-V).
**Błędy odwracalne** | Ponowne próby są wykonywane co interwał replikacji przy użyciu wycofywania wykładniczego zwiększającego interwał ponawiania prób od początku pierwszej próby o 1, 2, 4, 8 i 10 minut. Jeśli błąd będzie się powtarzać, ponowne próby będą wykonywane co 30 minut. Przykładami mogą być błędy sieci, błędy niskiego dysku i Stany małej ilości pamięci.



## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

1. Planowane lub nieplanowane przejście w tryb failover można uruchomić z maszyn wirtualnych funkcji Hyper-V do platformy Azure. Jeśli zostanie uruchomione planowane przejście w tryb failover, źródłowe maszyny wirtualne zostaną wyłączone w celu zapewnienia, że nie będzie miała miejsca utrata danych. Uruchom nieplanowaną pracę w trybie failover, Jeśli lokacja główna jest niedostępna.
2. W tryb failover można przełączyć pojedynczą maszynę lub można utworzyć plany odzyskiwania, aby zaaranżować tryb failover na wielu maszynach.
3. Uruchamiasz tryb failover. Po zakończeniu pierwszego etapu pracy w trybie failover powinno być możliwe wyświetlenie utworzonych maszyn wirtualnych repliki na platformie Azure. Jeśli jest to wymagane, do maszyny wirtualnej można przypisać publiczny adres IP.
4. Następnie możesz zatwierdzić tryb failover, aby rozpocząć uzyskiwanie dostępu do obciążenia z repliki maszyny wirtualnej platformy Azure.

Po ponownym uruchomieniu infrastruktury lokalnej można wrócić do trybu powrotu po awarii. Powrót po awarii występuje w trzech etapach:

1. Rozpocznij pracę w trybie failover z platformy Azure do lokacji lokalnej:
    - **Minimalizuj czas przestoju**: Jeśli używasz tej opcji, Site Recovery synchronizuje dane przed przełączeniem w tryb failover. Sprawdza on zmiany bloków danych i pobiera je do lokacji lokalnej, podczas gdy maszyna wirtualna platformy Azure działa, co minimalizuje przestoje. Gdy ręcznie określisz, że praca w trybie failover powinna zakończyć działanie, maszyna wirtualna platformy Azure zostanie wyłączona, zostaną skopiowane wszystkie końcowe zmiany różnicowe i rozpocznie się przełączanie do trybu failover.
    - **Pełne pobieranie**: w przypadku tej opcji dane są synchronizowane podczas pracy w trybie failover. Ta opcja powoduje pobranie całego dysku. Jest to szybsze, ponieważ nie są obliczane sumy kontrolne, ale występuje więcej przestojów. Użyj tej opcji, jeśli korzystasz z maszyn wirtualnych platformy Azure z repliką przez jakiś czas lub jeśli lokalna maszyna wirtualna została usunięta.
    - **Tworzenie maszyny wirtualnej**: możesz wybrać powrót po awarii do tej samej maszyny wirtualnej lub do alternatywnej maszyny wirtualnej. Możesz określić, że Site Recovery powinna utworzyć maszynę wirtualną, jeśli jeszcze nie istnieje.

2. Po zakończeniu synchronizacji początkowej należy wybrać opcję ukończenia pracy w trybie failover. Po zakończeniu możesz zalogować się na lokalnej maszynie wirtualnej, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami. W Azure Portal można zobaczyć, że maszyny wirtualne platformy Azure zostały zatrzymane.
3.  Następnie należy zatwierdzić tryb failover, aby zakończyć pracę, i ponownie rozpocząć dostęp do obciążenia z lokalnej maszyny wirtualnej.
4. Po niepomyślnym zakończeniu obciążeń należy włączyć replikację odwrotną, aby lokalne maszyny wirtualne zostały ponownie zreplikowane na platformę Azure.



## <a name="next-steps"></a>Następne kroki


Postępuj zgodnie z [tym samouczkiem](tutorial-prepare-azure.md) , aby rozpocząć pracę z funkcją Hyper-V do replikacji platformy Azure.



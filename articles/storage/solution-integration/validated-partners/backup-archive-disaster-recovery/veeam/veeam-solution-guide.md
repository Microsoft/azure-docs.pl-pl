---
title: Tworzenie kopii zapasowych danych na platformie Azure za pomocą Veeam
titleSuffix: Azure Blob Storage Docs
description: Strona sieci Web zawiera przegląd czynników, które należy wziąć pod uwagę, i kroki, które należy wykonać, aby wykorzystać platformę Azure jako miejsce docelowe magazynu i lokalizację odzyskiwania kopii zapasowych i odzyskiwania Veeam
keywords: Veeam,, tworzenie kopii zapasowych w chmurze, kopii zapasowych, tworzenie kopii zapasowych na platformie Azure, odzyskiwanie po awarii i ciągłość działania
author: karauten
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: Storage
ms.subservice: Blob Storage
ms.openlocfilehash: b7bc20d80887df5a5ad235c87238b0239abadc0a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101583"
---
# <a name="backup-to-azure-with-veeam"></a>Tworzenie kopii zapasowych na platformie Azure za pomocą usługi Veeam

Ten artykuł zawiera Przewodnik integrowania infrastruktury Veeam z usługą Azure Blob Storage. Obejmuje to wymagania wstępne, zasady usługi Azure Storage, implementację i wskazówki operacyjne. W tym artykule są używane tylko adresy platformy Azure jako miejsce docelowe kopii zapasowych poza siedzibą firmy oraz lokacja odzyskiwania w przypadku awarii, co uniemożliwia wykonywanie zwykłych operacji w lokacji głównej. Usługa Veeam oferuje również niższe rozwiązanie RTO, replikację Veeam, co oznacza, że maszyna wirtualna w stanie gotowości jest gotowa do szybszego uruchamiania i odzyskiwania w przypadku awarii i ochrony zasobów w środowisku produkcyjnym platformy Azure. Program Veeam również zawiera narzędzia do tworzenia kopii zapasowych zasobów platformy Azure i pakietu Office 365. Te możliwości są poza zakresem tego dokumentu. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>Architektura referencyjna dla środowiska lokalnego na platformie Azure i wdrożenia In-Azure

![Veeam do architektury referencyjnej platformy Azure](../media/veeam-architecture.png)

Istniejące wdrożenie Veeam można łatwo zintegrować z platformą Azure, dodając konto usługi Azure Storage lub wiele kont jako repozytorium kopii zapasowych w chmurze. Veeam umożliwia również odzyskiwanie kopii zapasowych z lokalizacji lokalnej na platformę Azure, zapewniając witrynę odzyskiwania na żądanie na platformie Azure.

## <a name="veeam-interoperability-matrix"></a>Macierz współdziałania Veeam
| Obciążenie | GPv2 i Blob Storage | Obsługa warstwy chłodna | Obsługa warstwy Archiwum | Pomoc techniczna urządzenie Data Box Family |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Lokalne maszyny wirtualne/dane | v10a | v10a | NA | 10a |
| Maszyny wirtualne platformy Azure | v10a | v10a | NA | 10a |
| Obiekt bob Azure | v10a | v10a | NA | 10a |
| Azure Files | v10a | v10a | NA | 10a | 

> [!Note]
Usługa Veeam Backup i replikacja obsługuje interfejs API REST tylko dla Azure Data Box, dlatego Azure Data Box Disk nie jest obsługiwana. Oczekiwana jest obsługa warstwy archiwum platformy Azure Blob Storage Veeam v11.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Niewielki plan z góry spowoduje dołączenie do wielu najlepszych klientów korzystających z platformy Azure jako miejsca docelowego kopii zapasowej poza siedzibą firmy i lokacji odzyskiwania.

### <a name="are-you-new-to-azure"></a>Jesteś nowym na platformie Azure?

Firma Microsoft oferuje platformę do rozpoczęcia pracy z platformą Azure. CAF [wdrażania w chmurze](https://docs.microsoft.com/azure/architecture/cloud-adoption/) \( \) to szczegółowe podejście do transformacji cyfrowej przedsiębiorstwa oraz kompleksowego przewodnika po zaplanowaniu wdrożenia w chmurze klasy produkcyjnej. CAF zawiera [Przewodnik konfigurowania platformy Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) krok po kroku dotyczący nowych wersji platformy Azure, które ułatwiają szybkie i bezpieczne rozpoczęcie pracy. można też znaleźć wersję interaktywną w [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Możesz znaleźć przykładowe architektury i konkretne najlepsze rozwiązania dotyczące wdrażania aplikacji i bezpłatnych zasobów szkoleniowych, aby uzyskać informacje na temat ścieżki do wiedzy platformy Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Weź pod uwagę sieć między lokalizacją i platformą Azure

Bez względu na to, czy zasoby w chmurze mają być uruchamiane w środowisku produkcyjnym, testowym, programistycznym, czy jako miejsce docelowe kopii zapasowej i lokacja odzyskiwania, ważne jest, aby zrozumieć zapotrzebowanie na przepustowość na potrzeby wstępnego tworzenia kopii zapasowych i przeprowadzania codziennych transferów.

Azure Data Box zapewnia metodę transferu początkowej kopii zapasowej na platformę Azure bez konieczności dodatkowej przepustowości, jeśli oszacowanie planu bazowego będzie trwać dłużej niż można tolerować. Możesz użyć Transfer danych szacowania podczas tworzenia konta magazynu, aby oszacować czas wymagany do przetransferowania początkowej kopii zapasowej.

![Usługa Azure Storage Transfer danych szacowania](../media/az-storage-transfer.png)

Pamiętaj, że będziesz potrzebować wystarczającej pojemności sieci do obsługi codziennych transferów danych w wymaganym oknie transferu (okno kopii zapasowej) bez wpływu na aplikacje produkcyjne. Ta sekcja zawiera konspekt narzędzi i technik dostępnych do oceny potrzeb sieci.

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>Jak można określić wymaganą przepustowość?

Dostępne są różne opcje oceny, aby określić stopień zmian i łączny rozmiar zestawu kopii zapasowych na potrzeby wstępnego transferu bazowego do platformy Azure. Poniżej przedstawiono kilka przykładów narzędzi do oceny i raportowania:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="how-will-i-know-how-much-headroom-i-have-with-my-current-internet-connection"></a>Jak będzie wiadomo, ile miejsca mam z bieżącym połączeniem internetowym?

Ważne jest, aby wiedzieć, ile pasma lub zwykle nie jest niewykorzystana, dostępnej przepustowości w codziennym okresie. Dzięki temu można prawidłowo ocenić, czy można osiągnąć cele czasu wstępnego przekazywania, gdy nie są używane Azure Data Box do rozsadzenia w trybie offline, oraz do wykonywania codziennych kopii zapasowych na podstawie współczynnika zmian podanego powyżej i okna kopii zapasowej. Poniżej znajdują się metody, których można użyć do zidentyfikowania przepustowości, której kopie zapasowe na platformie Azure mogą zużywać.

- Jesteś istniejącym klientem usługi Azure ExpressRoute? Wyświetl [użycie obwodu](https://docs.microsoft.com/azure/expressroute/expressroute-monitoring-metrics-alerts#circuits-metrics) w Azure Portal.
- Możesz skontaktować się z usługodawcą internetowym. Powinny one mieć raporty, które mogą być udostępniane na potrzeby prezentowania istniejących codziennych i miesięcznych użycia.
- Istnieje kilka narzędzi, które mogą mierzyć wykorzystanie przez monitorowanie ruchu sieciowego na poziomie routera/przełącznika, w tym:
  - [Pakiet analizatora przepustowości SolarWinds](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Asystent sieci Cisco](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp złoty](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choosing-the-right-storage-options"></a>Wybieranie odpowiednich opcji magazynu

W przypadku korzystania z platformy Azure jako miejsca docelowego kopii zapasowych klienci korzystają z [usługi azure BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)\. Usługa Azure Blob Storage to rozwiązanie magazynu obiektów firmy Microsoft. Usługa BLOB Storage jest zoptymalizowana pod kątem przechowywania dużych ilości danych bez struktury, które są danymi, które nie są zgodne z żadnym modelem danych lub definicją. Ponadto usługa Azure Storage ma trwałe, wysoce dostępne, bezpieczne i skalowalne. Platforma firmy Microsoft oferuje elastyczność pozwalającą wybrać odpowiedni magazyn dla właściwych obciążeń w celu zapewnienia [poziomu odporności](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json) , który będzie spełniał umowy SLA wewnętrzny. Blob Storage jest usługą płatność za użycie. Opłata jest [naliczana miesięcznie](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal#pricing-and-billing) za ilość przechowywanych danych, uzyskiwanie dostępu do tych danych i — w przypadku warstw chłodnych i archiwum — minimalny wymagany okres przechowywania. Opcje odporności i warstw mające zastosowanie do danych kopii zapasowej są podsumowane w poniższych tabelach.

**Opcje odporności Blob Storage platformy Azure:**

|  |Lokalnie nadmiarowy  |Strefa nadmiarowa  |Geograficznie nadmiarowy  |Strefa geograficzna nadmiarowa  |
|---------|---------|---------|---------|---------|
|Rzeczywista liczba kopii     | 3         | 3         | 6         | 6 |
|Liczba Strefy dostępności     | 1         | 3         | 2         | 4 |
|Liczba regionów     | 1         | 1         | 2         | 2 |
|Ręczne przejście w tryb failover do regionu pomocniczego     | Nie dotyczy         | Nie dotyczy         | Yes         | Tak |

**Warstwy usługi Azure Blob Storage:**

|  | Warstwa gorąca   |Warstwa chłodna   | Warstwa Archiwum |
| ----------- | ----------- | -----------  | -----------  |
| Dostępność | 99,9%         | 99%         | Tryb offline      |
| Opłaty za użycie | Wyższe koszty magazynowania, niższy dostęp i koszty transakcji | Niższe koszty magazynowania, wyższego poziomu dostępu i kosztów transakcji | Najniższe koszty magazynowania, najwyższy poziom dostępu i koszty transakcji |
| Wymagane jest minimalne przechowywanie danych | NA | 30 dni | 180 dni |
| Opóźnienie (czas do pierwszego bajtu) | ) | ) | Godziny |

#### <a name="sample-backup-to-azure-cost-model"></a>Przykładowa kopia zapasowa do modelu kosztów platformy Azure

Pojęcie "płatność za użycie" może być zniechęcające dla klientów, którzy są nowością w chmurze publicznej. Chociaż płacisz tylko za użyte miejsce, płacisz za transakcje (odczyt i zapis) oraz wychodzące [dane](https://azure.microsoft.com/pricing/details/bandwidth/) odczytane z powrotem do środowiska lokalnego, gdy plan danych oparty na usłudze Azure [Express Route Direct lub Express Route](https://azure.microsoft.com/pricing/details/expressroute/) jest używany w przypadku, gdy zostanie uwzględniony ruch wychodzący z platformy Azure. Możesz wykonać czynności w przypadku analizy opartej na cenniku [usługi Azure Storage lub zastrzeżonych cenach pojemności magazynu na platformie](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)Microsoft, które mogą oferować do 38% oszczędności na [kalkulatorze cen platformy Azure](https://azure.microsoft.com/pricing/calculator/). Poniżej przedstawiono przykładowe ceny, które umożliwiają modelowanie miesięcznych kosztów tworzenia kopii zapasowych na platformie Azure. jest to tylko przykład, a ***ceny mogą się różnić w zależności od działań, które nie zostały przechwycone tutaj:***


|Współczynnik kosztu  |Koszt miesięczny  |
|---------|---------|
|100 TB danych kopii zapasowej w chłodnym magazynie     |$1556,48         |
|2 TB nowych danych pisanych dziennie x 30 dni     |$72 w transakcjach          |
|Szacowana suma miesięczna     |$1628,48         |
|---------|---------|
|Jednorazowe przywrócenie 5 TB do lokalizacji lokalnej za pośrednictwem publicznego Internetu   | $527,26         |

> [!Note]
To oszacowanie zostało wygenerowane na Kalkulator cen platformy Azure przy użyciu cennika Wschodnie stany USA zgodnie z rzeczywistym użyciem i jest oparta na Veeam domyślnym rozmiaru fragmentu 256 KB dla transferów sieci WAN. Ten przykład może nie mieć zastosowania do Twoich wymagań.

## <a name="implementation-and-operational-guidance"></a>Implementacja i wskazówki dotyczące działania

Ta sekcja zawiera krótki przewodnik po dodaniu usługi Azure Storage do lokalnego wdrożenia Veeam. Jeśli interesują Cię szczegółowe wskazówki i kwestie związane z planowaniem, zalecamy zapoznanie się z [przewodnikiem tworzenia kopii zapasowych w programie Veeam Cloud Connect](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100).

1. Otwórz Azure Portal i wyszukaj ciąg "konta magazynu" lub kliknij ikonę usług domyślnych.

      ![Azure Portal](../media/azure-portal.png)

      ![Konta magazynu w witrynie Azure Portal](../media/locate-storage-account.png)

2. Wybierz, aby dodać konto, a następnie wybierz lub Utwórz grupę zasobów, podaj unikatową nazwę, wybierz region, wybierz pozycję "Standardowa" (wydajność), zawsze opuszczaj rodzaj konta jako "Storage v2", wybierz poziom replikacji, który jest zgodny z umowy SLA i warstwą domyślną, która będzie używana przez oprogramowanie do tworzenia kopii zapasowych. Konto usługi Azure Storage udostępnia warstwy gorąca, chłodna i archiwum dostępne na jednym koncie, a zasady Veeam umożliwiają wykorzystanie wielu warstw w celu efektywnego zarządzania cyklem życia danych. Przejdź do następnego kroku. 
    
      ![Tworzenie konta magazynu](../media/account-create-1.png)

3. Zachodź z domyślnymi opcjami sieci dla tej pory i przejdź do "ochrony danych". W tym miejscu można włączyć opcję "usuwanie nietrwałe", co pozwala odzyskać przypadkowo usunięty plik kopii zapasowej w określonym okresie przechowywania i zapewnia ochronę przed przypadkowym lub złośliwym usunięciem. 
    ![Tworzenie konta magazynu — część 2](../media/account-create-2.png)

4. Następnie zalecamy ustawienia domyślne z ekranu "Zaawansowane" w celu utworzenia kopii zapasowej w przypadku użycia na platformie Azure.

    ![Tworzenie konta magazynu — część 3](../media/account-create-3.png) 

5. Dodaj Tagi dla organizacji, jeśli używasz tagowania i tworzenia konta. Masz teraz petabajtów na żądanie w swojej dyspozycji!

6. Przed dodaniem konta do środowiska Veeam są teraz wymagane dwa szybkie kroki. Przejdź do konta utworzonego w witrynie Azure Portal i wybierz pozycję "kontenery" w menu "usługa BLOB Service" w bloku portalu. Dodaj nowy kontener i wybierz zrozumiałą nazwę. Następnie przejdź do elementu "klucze dostępu" w obszarze "Ustawienia" i skopiuj "nazwa konta magazynu" i jeden z dwóch kluczy dostępu. W następnych krokach będzie potrzebna nazwa kontenera, nazwa konta i klucz dostępu.

    ![Tworzenie kontenera](../media/container-b.png)
    
    ![Przechwyć te informacje o koncie](../media/access-key.png)
    
    > [!Note]
Tworzenie kopii zapasowych i replikacja Veeam zapewnia dodatkowe opcje łączenia się z platformą Azure. W przypadku użycia tego artykułu Microsoft Azure Blob Storage jako miejsce docelowe kopii zapasowych zalecanym najlepszym rozwiązaniem jest użycie powyższych metod.

7. ***(Opcjonalnie)*** Możesz dodać dodatkowe warstwy zabezpieczeń do wdrożenia.

     1. Skonfiguruj dostęp oparty na rolach, aby ograniczyć liczbę osób, które mogą wprowadzać zmiany na koncie magazynu. [Dowiedz się więcej tutaj](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)

    1. Ogranicz dostęp do konta do określonych segmentów sieci przy użyciu [zapory magazynu](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) , aby zapobiec próbom dostępu spoza sieci firmowej.

    ![Zapora magazynu](../media/storage-firewall.png) 

    1. Ustaw [blokadę usuwania](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) na koncie, aby zapobiec przypadkowemu usunięciu konta magazynu.

    ![Blokada zasobów ](../media/resource-lock.png) 1.) Skonfiguruj dodatkowe [najlepsze rozwiązania](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)w zakresie zabezpieczeń. 
8. W konsoli zarządzania kopiami zapasowymi i replikacją Veaam przejdź do "infrastruktura kopii zapasowych" — > kliknij prawym przyciskiem myszy w okienku Przegląd i wybierz pozycję "Dodaj repozytorium kopii zapasowych", aby otworzyć Kreatora konfiguracji. W oknie dialogowym wybierz pozycję Magazyn obiektów — > Microsoft Azure Blob Storage--> Blob Storage platformy Azure.
    
    ![Ekran kreatora repozytorium Veeam a](../media/veeam-repo-a.png)

    ![Ekran kreatora repozytorium Veeam b](../media/veeam-repo-b.png)

    ![Ekran kreatora repozytorium Veeam c](../media/veeam-repo-c.png)

9. Następnie podaj nazwę i opis nowego repozytorium Microsoft Azure obiektów BLOB.
    
    ![Ekran kreatora repozytorium Veeam d](../media/veeam-repo-d.png)

10. W następnym kroku Dodaj poświadczenia, aby uzyskać dostęp do konta usługi Azure Storage. Wybierz pozycję "Microsoft Azure Storage konto" w Menedżerze poświadczeń w chmurze, wprowadź nazwę konta magazynu i klucz dostępu. Wybierz pozycję Azure Global w selektorze regionów i dowolnym serwerze bramy, jeśli ma to zastosowanie.
    
    ![Ekran kreatora repozytorium Veeam e](../media/veeam-repo-e.png)

> [!Note]
Jeśli nie zdecydujesz się na korzystanie z serwera bramy Veeam, upewnij się, że wszystkie zakresy repozytorium skalowalnego w poziomie mają bezpośredni dostęp do Internetu.

11. Na stronie rejestr kontenerów wybierz kontener usługi Azure Storage, a następnie wybierz lub Utwórz folder, w którym mają być przechowywane kopie zapasowe. Można również zdefiniować limit niezmienny dla ogólnej pojemności magazynu, który będzie używany przez Veeam (zalecane). Przejrzyj wyświetlone informacje w sekcji Podsumowanie i wypełnij narzędzie konfiguracji. Nowe repozytorium można następnie wybrać w konfiguracji zadania tworzenia kopii zapasowej.

    ![Ekran kreatora repozytorium Veeam f](../media/veeam-repo-f.png)
    
    ![Ekran kreatora repozytorium Veeam g](../media/veeam-repo-g.png)

### <a name="azure-alerting-and-performance-monitoring"></a>Alerty i monitorowanie wydajności platformy Azure

Zaleca się monitorowanie zasobów platformy Azure i Veeam z możliwością ich używania w taki sam sposób, jak w przypadku każdego miejsca docelowego magazynu, z którego korzystasz do przechowywania kopii zapasowych. Kombinacja możliwości monitorowania Azure Monitor i Veeam (tj. karta statystyk w węźle zadania w konsoli zarządzania Veeam lub bardziej zaawansowane opcje, takie jak Veeam), ułatwi zachowanie kondycji środowiska.

#### <a name="microsoft-azure-portal"></a>Microsoft Azure Portal

Microsoft Azure zapewnia niezawodne rozwiązanie do monitorowania w postaci [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource). Azure Monitor można [skonfigurować](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) do śledzenia pojemności, transakcji, dostępności, uwierzytelniania i innych możliwości usługi Azure Storage. Pełne odwołanie do śledzonych metryk można znaleźć [tutaj](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference). Oto kilka przydatnych metryk do śledzenia BlobCapacity — aby zapewnić, że pozostanie poniżej maksymalnego [limitu pojemności konta magazynu](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account), ruchu przychodzącego i wychodzącego — w celu śledzenia ilości danych zapisywanych i odczytywanych z konta usługi Azure Storage oraz SuccessE2ELatency-do śledzenia czasu komunikacji między żądaniami do i z usługi Azure Storage i MediaAgent. 

Możesz również [utworzyć alerty dzienników](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) , aby śledzić kondycję usługi Azure Storage i w dowolnym momencie wyświetlić [pulpit nawigacyjny stanu platformy Azure](https://status.azure.com/status) .

#### <a name="veeam-reporting"></a>Raportowanie Veeam

[Konfigurowanie Veeam jedno raportowanie](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)

[Veeame alarmy dotyczące kopii zapasowych i replikacji](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100) 

### <a name="how-to-open-support-cases"></a>Jak otworzyć przypadki pomocy technicznej

Jeśli potrzebujesz pomocy przy korzystaniu z kopii zapasowej w rozwiązaniu platformy Azure, zalecamy otworzenie sprawy z systemami Veeam i Azure, dzięki czemu nasze organizacje pomocy technicznej mogą komunikować się wspólnie, w razie potrzeby.

#### <a name="how-to-open-a-case-with-veeam"></a>Jak otworzyć przypadek przy użyciu Veeam

Przejdź do [portalu obsługi klienta Veeam](https://www.veeam.com/support.html), zaloguj się i otwórz sprawę.

Jeśli musisz poznać opcje pomocy technicznej dostępne dla Ciebie przez Veeam, zobacz [Veeam Customer Support Policy](https://www.veeam.com/veeam_software_support_policy_ds.pdf)

Możesz również wywołać, aby otworzyć sprawę:

[Numery pomocy technicznej na całym świecie](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Jak otworzyć przypadek przy użyciu zespołu pomocy technicznej systemu Azure

W [Azure Portal](https://portal.azure.com) Wyszukaj ciąg "Pomoc techniczna" na pasku wyszukiwania w górnej części portalu i wybierz pozycję "+ nowe żądanie pomocy technicznej". 
> [!Note]
W przypadku otwierania sprawy należy koniecznie uzyskać pomoc dotyczącą usługi Azure Storage lub sieci platformy Azure, a **nie** "Azure Backup". Azure Backup jest usługą Microsoft Azure Native i Twoja sprawa zostanie niepoprawnie przekierowana.

### <a name="links-to-relevant-veeam-documentation"></a>Linki do odpowiedniej dokumentacji Veeam

Dokumentacja Veeam, która zapewnia dalsze szczegóły:

[Podręcznik użytkownika Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)

[Przewodnik po architekturze Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="link-to-marketplace-offering"></a>Link do oferty Marketplace

Możesz również nadal używać rozwiązania Veeam, które znasz i którym ufasz, aby chronić obciążenia działające na platformie Azure. Program Veeam ułatwia wdrażanie rozwiązań na platformie Azure i ochronę Virtual Machines platformy Azure oraz wielu innych usług platformy Azure.

[Wdrażanie Veeam B&R za pośrednictwem portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)

[Arkusz danych platformy Azure](https://www.veeam.com/backup-azure.html?ad=menu-products)


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dodatkowymi zasobami w tych zewnętrznych witrynach sieci Web, aby uzyskać informacje o wyspecjalizowanych scenariuszach użycia:

[Veeam, jak wideo](https://www.veeam.com/how-to-videos.html?ad=menu-resources)

[Dokumentacja techniczna Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)

[Veeam Knowledge Base i często zadawane pytania](https://www.veeam.com/knowledge-base.html?ad=menu-resources)

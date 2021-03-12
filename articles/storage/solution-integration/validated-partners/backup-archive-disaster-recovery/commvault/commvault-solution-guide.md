---
title: Tworzenie kopii zapasowych danych na platformie Azure za pomocą programu CommVault
titleSuffix: Azure Blob Storage Docs
description: Strona sieci Web zawiera przegląd czynników, które należy wziąć pod uwagę, i kroków, które należy wykonać w celu wykorzystania platformy Azure jako miejsca docelowego magazynu i lokalizacji odzyskiwania dla programu CommVault pełna kopia zapasowa i odzyskiwanie
keywords: CommVault, tworzenie kopii zapasowych w chmurze, kopii zapasowych, tworzenie kopii zapasowych na platformie Azure, odzyskiwanie po awarii i ciągłość działania
author: karauten
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: bc5bcca394fa66cea9cbf6bc20ac7d164c671cf7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632955"
---
# <a name="back-up-to-azure-with-commvault"></a>Tworzenie kopii zapasowej na platformie Azure za pomocą programu CommVault

Ten artykuł zawiera Przewodnik integracji infrastruktury CommVault z usługą Azure Blob Storage. Obejmuje to wymagania wstępne, zasady usługi Azure Storage, implementację i wskazówki operacyjne. W tym artykule są używane tylko adresy platformy Azure jako miejsce docelowe kopii zapasowych poza siedzibą firmy oraz lokacja odzyskiwania w przypadku awarii, co uniemożliwia wykonywanie zwykłych operacji w lokacji głównej. Usługa CommVault oferuje również niższą RTO rozwiązanie, a także umożliwia szybkie uruchamianie i odzyskiwanie danych przez maszynę wirtualną w stanie gotowości do uruchamiania i odzyskiwania w przypadku awarii i ochrony zasobów w środowisku produkcyjnym platformy Azure. Te możliwości są poza zakresem tego dokumentu. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>Architektura referencyjna dla środowiska lokalnego na platformie Azure i wdrożenia In-Azure

![Architektura programu CommVault w ramach architektury referencyjnej platformy Azure](../media/commvault-diagram.png)

Istniejące wdrożenie programu CommVault można łatwo zintegrować z platformą Azure, dodając konto usługi Azure Storage lub wiele kont jako miejsce docelowe magazynu w chmurze. Program CommVault umożliwia również odzyskiwanie kopii zapasowych z lokalizacji lokalnej na platformę Azure, zapewniając na platformie Azure witrynę odzyskiwania na żądanie.   

## <a name="commvault-interoperability-matrix"></a>Macierz zgodności programu CommVault
| Obciążenie | GPv2 i Blob Storage | Obsługa warstwy chłodna | Obsługa warstwy Archiwum | Pomoc techniczna urządzenie Data Box Family |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Lokalne maszyny wirtualne/dane | 11,5 v | 11,5 v | 11.10 v | 11.10 v |
| Maszyny wirtualne platformy Azure | 11,5 v | 11,5 v | 11,5 v | NA |
| Obiekt bob Azure | 11.6 v | 11.6 v | 11.6 v | NA |
| Azure Files | 11.6 v | 11.6 v | 11.6 v | NA | 

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Niewielki plan z góry spowoduje dołączenie do wielu najlepszych klientów korzystających z platformy Azure jako miejsca docelowego kopii zapasowej poza siedzibą firmy i lokacji odzyskiwania.

### <a name="are-you-new-to-azure"></a>Jesteś nowym na platformie Azure?

Firma Microsoft oferuje platformę do rozpoczęcia pracy z platformą Azure. CAF [wdrażania w chmurze](https://docs.microsoft.com/azure/architecture/cloud-adoption/) \( \) to szczegółowe podejście do transformacji cyfrowej przedsiębiorstwa oraz kompleksowego przewodnika po zaplanowaniu wdrożenia w chmurze klasy produkcyjnej. CAF zawiera [Przewodnik konfigurowania platformy Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) krok po kroku dla nowych platformy Azure, aby pomóc w szybkim i bezpiecznym uruchomieniu, a można znaleźć wersję interaktywną w [witrynie Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Możesz znaleźć przykładowe architektury i konkretne najlepsze rozwiązania dotyczące wdrażania aplikacji i bezpłatnych zasobów szkoleniowych, aby uzyskać informacje na temat ścieżki do wiedzy platformy Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Weź pod uwagę sieć między lokalizacją i platformą Azure

Bez względu na to, czy zasoby w chmurze mają być uruchamiane w środowisku produkcyjnym, testowym, programistycznym, czy jako miejsce docelowe kopii zapasowej i lokacja odzyskiwania, ważne jest, aby zrozumieć zapotrzebowanie na przepustowość na potrzeby wstępnego tworzenia kopii zapasowych i przeprowadzania codziennych transferów. 

Azure Data Box zapewnia metodę transferu początkowej kopii zapasowej na platformę Azure bez konieczności dodatkowej przepustowości, jeśli oszacowanie planu bazowego będzie trwać dłużej niż można tolerować. Możesz użyć Transfer danych szacowania podczas tworzenia konta magazynu, aby oszacować czas wymagany do przetransferowania początkowej kopii zapasowej.

![Usługa Azure Storage Transfer danych szacowania](../media/az-storage-transfer.png)

Pamiętaj, że będziesz potrzebować wystarczającej pojemności sieci do obsługi codziennych transferów danych w wymaganym oknie transferu (okno kopii zapasowej) bez wpływu na aplikacje produkcyjne. Ta sekcja zawiera konspekt narzędzi i technik dostępnych do oceny potrzeb sieci.

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>Jak można określić wymaganą przepustowość?

-  Raporty z oprogramowania do tworzenia kopii zapasowych. 
  Program CommVault udostępnia standardowe raporty pozwalające określić [stopień zmian](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) i [łączny rozmiar zestawu kopii zapasowych](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) dla początkowego transferu linii bazowej na platformę Azure.
- Tworzenie kopii zapasowej niezależnych od oprogramowania narzędzi do oceny i raportowania, takich jak:
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
|2 TB nowych danych pisanych dziennie x 30 dni     |$39 w transakcjach          |
|Szacowana suma miesięczna     |$1595,48         |
|---------|---------|
|Jednorazowe przywrócenie 5 TB do lokalizacji lokalnej za pośrednictwem publicznego Internetu   | $491,26         |


> [!Note] 
To oszacowanie zostało wygenerowane na Kalkulator cen platformy Azure przy użyciu cennika Wschodnie stany USA zgodnie z rzeczywistym użyciem. jest on oparty na domyślnym rozmiarze 32MB podfragmentu, który generuje żądania PUT 65 536, liczba transakcji zapisu na dzień. Ten przykład może nie mieć zastosowania do Twoich wymagań.

## <a name="implementation-and-operational-guidance"></a>Implementacja i wskazówki dotyczące działania

Ta sekcja zawiera krótki przewodnik dotyczący dodawania usługi Azure Storage do lokalnego wdrożenia usługi CommVault. Jeśli interesują Cię szczegółowe wskazówki i kwestie związane z planowaniem, zalecamy zapoznanie się z [przewodnikiem dotyczącym architektury platformy Azure](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16).

1. Otwórz Azure Portal i wyszukaj ciąg "konta magazynu" lub kliknij ikonę usług domyślnych.
    
    1. ![Azure Portal](../media/azure-portal.png)
  
    1. ![Konta magazynu w witrynie Azure Portal](../media/locate-storage-account.png)

2. Wybierz, aby dodać konto, a następnie wybierz lub Utwórz grupę zasobów, podaj unikatową nazwę, wybierz region, wybierz pozycję "Standardowa" (wydajność), zawsze opuszczaj rodzaj konta jako "Storage v2", wybierz poziom replikacji, który jest zgodny z umowy SLA i warstwą domyślną, która będzie używana przez oprogramowanie do tworzenia kopii zapasowych. Konto usługi Azure Storage udostępnia warstwy gorąca, chłodna i archiwalne dostępne w ramach jednego konta, a zasady CommVault umożliwiają wykorzystanie wielu warstw w celu efektywnego zarządzania cyklem życia danych. Przejdź do następnego kroku. 

    ![Tworzenie konta magazynu](../media/account-create-1.png)

3. Zachodź z domyślnymi opcjami sieci dla tej pory i przejdź do "ochrony danych". W tym miejscu można włączyć opcję "usuwanie nietrwałe", co pozwala odzyskać przypadkowo usunięty plik kopii zapasowej w określonym okresie przechowywania i zapewnia ochronę przed przypadkowym lub złośliwym usunięciem. 
    
    ![Tworzenie konta magazynu — część 2](../media/account-create-2.png)

4. Następnie zalecamy ustawienia domyślne z ekranu "Zaawansowane" w celu utworzenia kopii zapasowej w przypadku użycia na platformie Azure.

    ![Tworzenie konta magazynu — część 3](../media/account-create-3.png) 

5. Dodaj Tagi dla organizacji, jeśli używasz tagowania i tworzenia konta. Masz teraz petabajtów na żądanie w swojej dyspozycji!

6. Przed dodaniem konta do środowiska CommVault należy wykonać dwa szybkie kroki. Przejdź do konta utworzonego w Azure Portal i wybierz pozycję "kontenery" w menu "usługa BLOB Service" w bloku portalu. Dodaj nowy kontener i wybierz zrozumiałą nazwę. Następnie przejdź do elementu "klucze dostępu" w obszarze "Ustawienia" i skopiuj "nazwa konta magazynu" i jeden z dwóch kluczy dostępu. W następnych krokach będzie potrzebna nazwa kontenera, nazwa konta i klucz dostępu.
    
    ![Tworzenie kontenera](../media/container.png)
    
    ![Przechwyć te informacje o koncie](../media/access-key.png)

7. ***(Opcjonalnie)*** Możesz dodać dodatkowe warstwy zabezpieczeń do wdrożenia.
    
    1. Skonfiguruj dostęp oparty na rolach, aby ograniczyć liczbę osób, które mogą wprowadzać zmiany na koncie magazynu. [Dowiedz się więcej tutaj](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)
    1. Ogranicz dostęp do konta do określonych segmentów sieci przy użyciu [zapory magazynu](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) , aby zapobiec próbom dostępu spoza sieci firmowej.

    ![Zapora magazynu](../media/storage-firewall.png) 

    1. Ustaw [blokadę usuwania](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) na koncie, aby zapobiec przypadkowemu usunięciu konta magazynu.

    ![Blokada zasobów](../media/resource-lock.png)
    
    1. Skonfiguruj dodatkowe [najlepsze rozwiązania](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)w zakresie zabezpieczeń.
    
1. W centrum poleceń programu CommVault przejdź do "Zarządzanie"--> "zabezpieczenia" — > "Menedżer poświadczeń". Wybierz pozycję "konto w chmurze", "typ dostawcy" Microsoft Azure Storage, wybierz pozycję "MediaAgent", która spowoduje transfer danych do i z platformy Azure, Dodaj nazwę konta magazynu i klucz dostępu.
    
    ![Poświadczenie firmy CommVault](../media/commvault-credential.png)

9. Następnie przejdź do pozycji "magazyn" — > "Chmura" w centrum poleceń programu CommVault. Wybierz pozycję "Dodaj". Wprowadź przyjazną nazwę konta magazynu, a następnie wybierz pozycję "Microsoft Azure Storage" z listy "typ". Wybierz serwer programu Media Agent, który ma być używany do transferu kopii zapasowych do usługi Azure Storage. Dodaj utworzony kontener, wybierz warstwę magazynowania, która ma być używana w ramach konta usługi Azure Storage, a następnie wybierz poświadczenia utworzone w kroku #8. Na koniec wybierz, czy mają być transferowane deduplikowane kopie zapasowe, czy nie i lokalizacja bazy danych deduplikacji.
    
     ![Zrzut ekranu przedstawiający interfejs użytkownika Dodaj chmurę. W menu rozwijanym archiwum * jest zaznaczone polecenie * * archiwalne * *.](../media/commvault-add-storage.png)

10. Na koniec Dodaj nowy zasób usługi Azure Storage do istniejącego lub nowego planu w centrum poleceń programu CommVault za pośrednictwem "zarządzania"-> "plany" jako "miejsce docelowe kopii zapasowej".

    ![Zrzut ekranu przedstawiający interfejs użytkownika centrum poleceń programu COMMVAULT. W lewym okienku nawigacji w obszarze * * jest zaznaczone polecenie * * Zarządzanie * *, * * plany * *.](../media/commvault-plan.png)

11. ***(Opcjonalnie)*** Jeśli planujesz korzystać z platformy Azure jako lokacji odzyskiwania lub programu CommVault do migrowania serwerów i aplikacji na platformę Azure, najlepszym rozwiązaniem jest wdrożenie serwera proxy VSA na platformie Azure. Szczegółowe instrukcje można znaleźć [tutaj](https://documentation.commvault.com/commvault/v11/article?p=106208.htm).  

### <a name="azure-alerting-and-performance-monitoring"></a>Alerty i monitorowanie wydajności platformy Azure

Zaleca się monitorowanie zarówno zasobów platformy Azure, jak i zdolności firmy CommVault do używania ich w taki sposób, aby można było korzystać z nich w celu przechowywania kopii zapasowych. Kombinacja Azure Monitor i CommVault centrum poleceń umożliwia zachowanie kondycji środowiska.

#### <a name="microsoft-azure-portal"></a>Microsoft Azure Portal

Microsoft Azure zapewnia niezawodne rozwiązanie do monitorowania w postaci [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource). Azure Monitor można [skonfigurować](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) do śledzenia pojemności, transakcji, dostępności, uwierzytelniania i innych możliwości usługi Azure Storage. Pełne odwołanie do śledzonych metryk można znaleźć [tutaj](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference). Oto kilka przydatnych metryk do śledzenia BlobCapacity — aby upewnić się, że pozostanie poniżej maksymalnego [limitu pojemności konta magazynu](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account), ruchu przychodzącego i wychodzącego — w celu śledzenia ilości danych, które są zapisywane i odczytywane z konta usługi Azure Storage, a SuccessE2ELatency — do śledzenia czasu, w którym wysyłane są żądania do i z usługi Azure Storage i MediaAgent. 

Możesz również [utworzyć alerty dzienników](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) , aby śledzić kondycję usługi Azure Storage i w dowolnym momencie wyświetlić [pulpit nawigacyjny stanu platformy Azure](https://status.azure.com/status) .

#### <a name="commvault-command-center"></a>CommVault centrum poleceń

[Tworzenie alertów dla pul magazynu w chmurze](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)

[Gdzie klienci mogą przechodzić do wyświetlania raportów o wydajności, kończenia zadań i rozpocząć podstawowe Rozwiązywanie problemów](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm) 

### <a name="how-to-open-support-cases"></a>Jak otworzyć przypadki pomocy technicznej

Jeśli potrzebujesz pomocy przy korzystaniu z kopii zapasowej w rozwiązaniu platformy Azure, zalecamy otworzenie sprawy z programem CommVault i platformą Azure, dzięki czemu nasze organizacje pomocy technicznej mogą komunikować się wspólnie, w razie potrzeby.

#### <a name="how-to-open-a-case-with-commvault"></a>Jak otworzyć przypadek przy użyciu programu CommVault

Przejdź do [witryny pomocy technicznej firmy CommVault](https://www.commvault.com/support), zaloguj się i otwórz sprawę.

Jeśli chcesz poznać dostępne opcje kontraktu pomocy technicznej, zobacz [Opcje pomocy technicznej programu CommVault](https://ma.commvault.com/support)

Możesz również wywołać, aby otworzyć sprawę lub skontaktować się z pomocą techniczną w ramach programu CommVault:

Bezpłatny: + 1 877-780-3077

[Numery pomocy technicznej na całym świecie](https://ma.commvault.com/Support/TelephoneSupport)

[Obsługa wiadomości e-mail w programie CommVault](mailto:support@commvault.com)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Jak otworzyć przypadek przy użyciu zespołu pomocy technicznej systemu Azure

W [Azure Portal](https://portal.azure.com) Wyszukaj ciąg "Pomoc techniczna" na pasku wyszukiwania w górnej części portalu i wybierz pozycję "+ nowe żądanie pomocy technicznej". 
> [!Note]
W przypadku otwierania przypadku należy mieć szczególny dostęp do "usługi Azure Storage" lub "Azure Networking", a **nie** "Azure Backup". Azure Backup jest usługą Microsoft Azure Native i Twoja sprawa zostanie niepoprawnie przekierowana.

### <a name="links-to-relevant-commvault-documentation"></a>Linki do odpowiedniej dokumentacji programu CommVault

Dokumentacja firmy CommVault dostarczająca dalsze informacje:

[Podręcznik użytkownika firmy CommVault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)

[Przewodnik dotyczący architektury platformy Azure firmy CommVault](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16) 

### <a name="link-to-marketplace-offering"></a>Link do oferty Marketplace

Możesz również nadal korzystać z rozwiązania CommVault znanego i zaufanego do ochrony obciążeń uruchomionych na platformie Azure. Program CommVault ułatwia wdrażanie rozwiązań na platformie Azure oraz ochronę Virtual Machines platformy Azure i wielu innych usług platformy Azure.

[Wdrażanie usług CommVault za pośrednictwem portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)

[Arkusz danych platformy Azure](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)

[Kompleksowa Lista obsługiwanych funkcji i usług platformy Azure](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)

[Jak chronić SAP HANA na platformie Azure za pomocą programu CommVault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dodatkowymi zasobami w tych zewnętrznych witrynach sieci Web, aby uzyskać informacje o wyspecjalizowanych scenariuszach użycia:

[Migrowanie serwerów i aplikacji na platformę Azure za pomocą programu CommVault](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)

[Ochrona oprogramowania SAP na platformie Azure za pomocą programu CommVault](https://www.youtube.com/watch?v=4ZGGE53mGVI)

[Ochrona usługi Office 365 za pomocą programu CommVault](https://www.youtube.com/watch?v=dl3nvAacxZU)

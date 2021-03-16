---
title: Tworzenie kopii zapasowych danych na platformie Azure za pomocą Veeam
titleSuffix: Azure Storage
description: Zawiera omówienie czynników, które należy wziąć pod uwagę, i kroków, które należy wykonać, aby użyć platformy Azure jako miejsca docelowego magazynu i lokalizacji odzyskiwania dla kopii zapasowych i odzyskiwania Veeam
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 15ab808bcb8521b0bd3a5ebd0f56bf336571d676
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562029"
---
# <a name="backup-to-azure-with-veeam"></a>Tworzenie kopii zapasowych na platformie Azure za pomocą usługi Veeam

Ten artykuł ułatwia integrację infrastruktury Veeam z usługą Azure Blob Storage. Obejmuje to wymagania wstępne, zagadnienia, implementacje i wskazówki operacyjne. Ten artykuł dotyczy korzystania z platformy Azure jako miejsca docelowego kopii zapasowej poza siedzibą firmy oraz lokacji odzyskiwania w przypadku wystąpienia awarii, co uniemożliwia normalne działanie w lokacji głównej.

> [!NOTE]
> Veeam oferuje również niższą pozycję cel czasu odzyskiwania (RTO), Veeam replikację. To rozwiązanie umożliwia przeprowadzenie awaryjnej maszyny wirtualnej, która może pomóc w przewróceniu sprawności w przypadku awarii w środowisku produkcyjnym platformy Azure. Usługa Veeam oferuje również dedykowane narzędzia do tworzenia kopii zapasowych zasobów platformy Azure i pakietu Office 365. Te możliwości wykraczają poza zakres tego dokumentu.

## <a name="reference-architecture"></a>Architektura referencyjna

Na poniższym diagramie przedstawiono architekturę referencyjną dla platformy Azure i wdrożenia w ramach platformy Azure.

![Veeam na diagram architektury referencyjnej platformy Azure.](../media/veeam-architecture.png)

Istniejące wdrożenie Veeam można łatwo zintegrować z platformą Azure, dodając konto usługi Azure Storage lub wiele kont jako repozytorium kopii zapasowych w chmurze. Veeam umożliwia również odzyskiwanie kopii zapasowych z lokalizacji lokalnej na platformę Azure, zapewniając witrynę odzyskiwania na żądanie na platformie Azure.

## <a name="veeam-interoperability-matrix"></a>Macierz współdziałania Veeam

| Obciążenie | GPv2 i Blob Storage | Obsługa warstwy chłodna | Obsługa warstwy Archiwum | Pomoc techniczna urządzenie Data Box Family |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Lokalne maszyny wirtualne/dane | v10a | v10a | Nie dotyczy | 10a<sup>*</sup> |
| Maszyny wirtualne platformy Azure | v10a | v10a | Nie dotyczy | 10a<sup>*</sup> |
| Obiekt bob Azure | v10a | v10a | Nie dotyczy | 10a<sup>*</sup> |
| Azure Files | v10a | v10a | Nie dotyczy | 10a<sup>*</sup> |

<sup>*</sup>Interfejs API REST usługi Veeam Backup i replikacja obsługuje tylko Azure Data Box. W związku z tym Azure Data Box Disk nie jest obsługiwana.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Niewielki plan z góry pomoże Ci wykorzystać platformę Azure jako miejsce docelowe kopii zapasowych poza siedzibą firmy oraz lokację odzyskiwania.

### <a name="get-started-with-azure"></a>Pierwsze kroki z platformą Azure

Firma Microsoft oferuje platformę do rozpoczęcia pracy z platformą Azure. [Struktura wdrażania w chmurze](https://docs.microsoft.com/azure/architecture/cloud-adoption/) (CAF) jest szczegółowym podejściem do transformacji cyfrowej przedsiębiorstwa oraz kompleksowego przewodnika po zaplanowaniu wdrożenia w chmurze klasy produkcyjnej. CAF zawiera [Przewodnik konfigurowania platformy Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) krok po kroku ułatwiający szybkie i bezpieczne rozpoczęcie pracy. Możesz znaleźć interaktywną wersję w [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Znajdziesz przykładowe architektury, konkretne najlepsze rozwiązania dotyczące wdrażania aplikacji i bezpłatne zasoby szkoleniowe, aby uzyskać informacje na temat ścieżki do wiedzy na temat platformy Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Weź pod uwagę sieć między lokalizacją i platformą Azure

Bez względu na to, czy zasoby w chmurze mają być uruchamiane w środowisku produkcyjnym, testowym, programistycznym, czy jako lokalizacja docelowa kopii zapasowej i odzyskiwanie, ważne jest, aby zrozumieć zapotrzebowanie na przepustowość na potrzeby wstępnego tworzenia kopii zapasowych i przeprowadzania codziennych transferów

Azure Data Box umożliwia przeniesienie początkowej bazowej kopii zapasowej na platformę Azure bez konieczności zwiększania przepustowości. Jest to przydatne, jeśli szacowane przeniesienie linii bazowej trwa dłużej, niż można tolerować. Możesz użyć Transfer danych szacowania podczas tworzenia konta magazynu, aby oszacować czas wymagany do przetransferowania początkowej kopii zapasowej.

![Pokazuje szacowania transferu danych usługi Azure Storage w portalu.](../media/az-storage-transfer.png)

Pamiętaj, że będziesz potrzebować wystarczającej pojemności sieci do obsługi codziennych transferów danych w wymaganym oknie transferu (okno kopii zapasowej) bez wpływu na aplikacje produkcyjne. Ta sekcja zawiera opis narzędzi i technik, które są dostępne do oceny potrzeb sieci.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Określ, ile przepustowości potrzebujesz

Dostępne są różne opcje oceny, aby określić stopień zmian i łączny rozmiar zestawu kopii zapasowych na potrzeby wstępnego transferu bazowego do platformy Azure. Oto kilka przykładów narzędzi do oceny i raportowania:

- [MiTrend](https://mitrend.com/)
- [Apt są](https://www.veritas.com/insights/aptare-it-analytics)
- [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Określanie niewykorzystywanej przepustowości Internetu

Ważne jest, aby wiedzieć, jak często niewykorzystana przepustowość (lub *wysokość*) jest dostępna w codziennym okresie. Dzięki temu można ocenić, czy można spełniać cele:

- początkowy czas przekazywania, gdy nie używasz Azure Data Box do rozsadzenia w trybie offline
- wykonywanie codziennych kopii zapasowych na podstawie częstotliwości zmian zidentyfikowanej wcześniej i okna kopii zapasowej

Użyj następujących metod, aby zidentyfikować przepustowość, z której kopie zapasowe na platformie Azure mają być bezpłatne.

- Jeśli jesteś istniejącym klientem usługi Azure ExpressRoute, Wyświetl [użycie obwodu](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) w Azure Portal.
- Skontaktuj się z usługodawcą internetowym. Powinny być w stanie udostępniać raporty, które pokazują Twoje istniejące codzienne i miesięczne użycie.
- Istnieje kilka narzędzi, które mogą mierzyć wykorzystanie przez monitorowanie ruchu sieciowego na poziomie routera/przełącznika. Są one następujące:

  - [Pakiet analizatora przepustowości SolarWinds](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Asystent sieci Cisco](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp złoty](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Wybierz odpowiednie opcje magazynu

Jeśli używasz platformy Azure jako miejsca docelowego kopii zapasowej, będziesz korzystać z usługi [Azure Blob Storage](../../../../blobs/storage-blobs-introduction.md). BLOB Storage to rozwiązanie magazynu obiektów firmy Microsoft. Usługa BLOB Storage jest zoptymalizowana pod kątem przechowywania dużych ilości danych bez struktury, które są danymi, które nie są zgodne z żadnym modelem danych lub definicją. Ponadto usługa Azure Storage ma trwałe, wysoce dostępne, bezpieczne i skalowalne. Można wybrać odpowiedni magazyn dla danego obciążenia, aby zapewnić [poziom odporności](../../../../common/storage-redundancy.md) , który będzie spełniał umowy SLA wewnętrzny. Usługa BLOB Storage jest usługą z opłatami za użycie. Opłata jest [naliczana miesięcznie](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) za ilość przechowywanych danych, uzyskiwanie dostępu do tych danych, a w przypadku warstw chłodnych i archiwum — minimalny wymagany okres przechowywania. Opcje odporności i warstw mające zastosowanie do danych kopii zapasowej są podsumowane w poniższych tabelach.

**Opcje odporności magazynu obiektów blob:**

|  |Lokalnie nadmiarowy  |Strefa nadmiarowa  |Geograficznie nadmiarowy  |Strefa geograficzna — nadmiarowa  |
|---------|---------|---------|---------|---------|
|**Rzeczywista liczba kopii**     | 3         | 3         | 6         | 6 |
|**Liczba stref dostępności**     | 1         | 3         | 2         | 4 |
|liczba **regionów**     | 1         | 1         | 2         | 2 |
|**Ręczne przejście w tryb failover do regionu pomocniczego**     | NIE DOTYCZY         | Nie dotyczy         | Tak         | Tak |

**Warstwy magazynu obiektów blob:**

|  | Warstwa gorąca   |Warstwa chłodna   | Warstwa Archiwum |
| ----------- | ----------- | -----------  | -----------  |
| **Dostępność** | 99,9%         | 99%         | Tryb offline      |
| **Opłaty za użycie** | Wyższe koszty magazynowania, niższy dostęp i koszty transakcji | Niższe koszty magazynowania, wyższego poziomu dostępu i kosztów transakcji | Najniższe koszty magazynowania, najwyższy poziom dostępu i koszty transakcji |
| **Wymagane jest minimalne przechowywanie danych** | NA | 30 dni | 180 dni |
| **Opóźnienie (czas do pierwszego bajtu)** | ) | ) | Godziny |

#### <a name="sample-backup-to-azure-cost-model"></a>Przykładowa kopia zapasowa do modelu kosztów platformy Azure

Dzięki opłacie za użycie można zniechęcające klientom, którzy są nowym w chmurze. Chociaż płacisz tylko za użyte miejsce, płacisz za transakcje (odczyt i zapis) oraz wychodzące [dane](https://azure.microsoft.com/pricing/details/bandwidth/) odczytane z powrotem do środowiska lokalnego, gdy plan danych oparty na usłudze Azure [Express Route Direct lub Express Route](https://azure.microsoft.com/pricing/details/expressroute/) jest używany w przypadku, gdy zostanie uwzględniony ruch wychodzący z platformy Azure. Możesz użyć [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) , aby przeprowadzić analizę "co". Możesz podstawić analizę cennika na liście lub [ceny zarezerwowanej pojemności usługi Azure Storage](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md), co może zapewnić nawet 38% oszczędności. Poniżej przedstawiono przykładowe ceny, które umożliwiają modelowanie miesięcznych kosztów tworzenia kopii zapasowych na platformie Azure. Jest to tylko przykład. *Ceny mogą się różnić w zależności od tego, co nie zostało przechwycone.*

|Współczynnik kosztu  |Koszt miesięczny  |
|---------|---------|
|100 TB danych kopii zapasowej w chłodnym magazynie     |$1556,48         |
|2 TB nowych danych pisanych dziennie x 30 dni     |$72 w transakcjach          |
|Szacowana suma miesięczna     |$1628,48         |
|---------|---------|
|Jednorazowe przywrócenie 5 TB do lokalizacji lokalnej za pośrednictwem publicznego Internetu   | $527,26         |

> [!Note]
> To oszacowanie zostało wygenerowane na Kalkulator cen platformy Azure przy użyciu cennika Wschodnie stany USA zgodnie z rzeczywistym użyciem i bazuje na Veeam wartości domyślnej rozmiaru fragmentu 256 KB dla transferów sieci WAN. Ten przykład może nie mieć zastosowania do Twoich wymagań.

## <a name="implementation-guidance"></a>Wskazówki dotyczące implementacji

Ta sekcja zawiera krótki przewodnik dotyczący dodawania usługi Azure Storage do lokalnego wdrożenia Veeam. Aby uzyskać szczegółowe wskazówki i zagadnienia związane z planowaniem, zobacz [Przewodnik dotyczący tworzenia kopii zapasowych Veeam Cloud Connect](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100).

1. Otwórz Azure Portal i Wyszukaj **konta magazynu**. Możesz również kliknąć ikonę usługi domyślnej.

      ![Pokazuje Dodawanie kont magazynu w Azure Portal.](../media/azure-portal.png)

      ![Pokazuje, gdzie wpisano magazyn w polu wyszukiwania Azure Portal.](../media/locate-storage-account.png)

2. Wybierz pozycję **Utwórz** , aby dodać konto. Wybierz lub Utwórz grupę zasobów, podaj unikatową nazwę, wybierz region, wybierz pozycję wydajność **standardowa** , a następnie pozostaw pozycję rodzaj konta jako **Magazyn v2**, wybierz poziom replikacji, który spełnia Twoje umowy SLA, oraz domyślną warstwę, która będzie stosowana przez oprogramowanie do tworzenia kopii zapasowej. Konto usługi Azure Storage udostępnia warstwy gorąca, chłodna i archiwum dostępne na jednym koncie, a zasady Veeam umożliwiają używanie wielu warstw w celu efektywnego zarządzania cyklem życia danych.

    ![Pokazuje ustawienia konta magazynu w portalu](../media/account-create-1.png)

3. Pozostaw teraz domyślne opcje sieciowe i przejdź do **ochrony danych**. W tym miejscu można włączyć opcję usuwania nietrwałego, która pozwala odzyskać przypadkowo usunięty plik kopii zapasowej w określonym okresie przechowywania i zapewnia ochronę przed przypadkowym lub złośliwym usunięciem.

    ![Pokazuje ustawienia ochrony danych w portalu.](../media/account-create-2.png)

4. Następnie zalecamy ustawienia domyślne z ekranu **zaawansowanego** na potrzeby tworzenia kopii zapasowych w przypadku użycia na platformie Azure.

    ![Pokazuje kartę Ustawienia zaawansowane w portalu.](../media/account-create-3.png)

5. Dodaj Tagi dla organizacji, jeśli używasz tagowania, i utwórz swoje konto.

6. Przed dodaniem konta do środowiska Veeam są teraz wymagane dwa szybkie kroki. Przejdź do konta utworzonego w Azure Portal i wybierz pozycję **kontenery** w menu **BLOB Service** . Dodaj kontener i wybierz zrozumiałą nazwę. Następnie przejdź do pozycji **klucze dostępu** w obszarze **Ustawienia** i skopiuj **nazwę konta magazynu** i jeden z dwóch kluczy dostępu. W następnych krokach będzie potrzebna nazwa kontenera, nazwa konta i klucz dostępu.

    ![Pokazuje tworzenie kontenerów w portalu.](../media/container-b.png)

    ![Wyświetla ustawienia klucza dostępu w portalu.](../media/access-key.png)

    > [!Note]
    > Kopia zapasowa Veeam i replikacja oferują dodatkowe opcje łączenia się z platformą Azure. W przypadku użycia w tym artykule zalecanym najlepszym rozwiązaniem jest użycie powyższej metody Microsoft Azure Blob Storage jako miejsce docelowe kopii zapasowej.

7. *(Opcjonalnie)* Do wdrożenia można dodać więcej warstw zabezpieczeń.

     1. Skonfiguruj dostęp oparty na rolach, aby ograniczyć liczbę użytkowników, którzy mogą wprowadzać zmiany na koncie magazynu. Aby uzyskać więcej informacji, zobacz [wbudowane role dla operacji zarządzania](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).

     1. Ogranicz dostęp do konta do określonych segmentów sieci przy użyciu [ustawień zapory magazynu](../../../../common/storage-network-security.md) , aby zapobiec próbom dostępu spoza sieci firmowej.

        ![Pokazuje ustawienia zapory magazynu w portalu.](../media/storage-firewall.png)

     1. Ustaw [blokadę usuwania](../../../../../azure-resource-manager/management/lock-resources.md) na koncie, aby zapobiec przypadkowemu usunięciu konta magazynu.

        ![Blokada zasobów](../media/resource-lock.png)

     1. Skonfiguruj dodatkowe [najlepsze rozwiązania](../../../../../storage/blobs/security-recommendations.md)w zakresie zabezpieczeń.

8. W konsoli zarządzania kopiami zapasowymi i replikacją Veaam przejdź do **infrastruktury kopii zapasowych** — > kliknij prawym przyciskiem myszy w okienku Przegląd i wybierz polecenie **Dodaj repozytorium kopii zapasowych** , aby otworzyć Kreatora konfiguracji. W oknie dialogowym wybierz pozycję **Magazyn obiektów**  ->  **Microsoft Azure Blob Storage**  ->  **Azure Blob Storage**.

    ![Pokazuje Wybieranie magazynu obiektów w Kreatorze repozytorium Veeam.](../media/veeam-repo-a.png)

    ![Pokazuje Wybieranie Microsoft Azure Blob Storage w Kreatorze repozytorium Veeam.](../media/veeam-repo-b.png)

    ![Pokazuje Wybieranie Blob Storage platformy Azure w Kreatorze repozytorium Veeam.](../media/veeam-repo-c.png)

9. Następnie podaj nazwę i opis nowego repozytorium magazynu obiektów BLOB.

    ![Pokazuje wpisanie nazwy dla repozytorium w Kreatorze repozytorium Veeam.](../media/veeam-repo-d.png)

10. W następnym kroku Dodaj poświadczenia, aby uzyskać dostęp do konta usługi Azure Storage. Wybierz **konto Microsoft Azure Storage** w Menedżerze poświadczeń w chmurze, a następnie wprowadź nazwę konta magazynu i klucz dostępu. Wybierz pozycję **Azure Global** w selektorze regionów i dowolny serwer bramy, jeśli ma to zastosowanie.

    ![Pokazuje, jak określić konto w Kreatorze repozytorium Veeam.](../media/veeam-repo-e.png)

    > [!Note]
    > Jeśli nie zdecydujesz się na korzystanie z serwera bramy Veeam, upewnij się, że wszystkie zakresy repozytorium skalowalnego w poziomie mają bezpośredni dostęp do Internetu.

11. Na stronie rejestr kontenerów wybierz kontener usługi Azure Storage, a następnie wybierz lub Utwórz folder, w którym mają być przechowywane kopie zapasowe. Można również zdefiniować limit niezmienny dla ogólnej pojemności magazynu, który będzie używany przez Veeam, co jest zalecane. Przejrzyj wyświetlone informacje w sekcji Podsumowanie i wypełnij narzędzie konfiguracji. Teraz można wybrać nowe repozytorium w konfiguracji zadania tworzenia kopii zapasowej.

    ![Pokazuje Określanie kontenera w Kreatorze repozytorium Veeam.](../media/veeam-repo-f.png)

    ![Pokazuje Tworzenie folderu w Kreatorze repozytorium Veeam.](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>Wskazówki dotyczące działania

### <a name="azure-alerts-and-performance-monitoring"></a>Alerty i monitorowanie wydajności platformy Azure

Zaleca się monitorowanie zasobów platformy Azure i Veeam z możliwością ich używania w taki sam sposób, jak w przypadku każdego miejsca docelowego magazynu, z którego korzystasz do przechowywania kopii zapasowych. Kombinacja możliwości monitorowania Azure Monitor i Veeam (karta **statystyki** w węźle **zadania** w konsoli zarządzania Veeam lub bardziej zaawansowanych opcji, takich jak Veeam), pomoże zapewnić dobrą kondycję środowiska.

#### <a name="azure-portal"></a>Azure Portal

Platforma Azure oferuje niezawodne rozwiązanie do monitorowania w postaci [Azure monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Azure Monitor można [skonfigurować](../../../../common/monitor-storage.md) do śledzenia pojemności, transakcji, dostępności, uwierzytelniania i innych możliwości usługi Azure Storage. Pełne odwołanie do śledzonych metryk można znaleźć [tutaj](../../../../blobs/monitor-blob-storage-reference.md). Oto kilka przydatnych metryk do śledzenia BlobCapacity — aby upewnić się, że pozostanie poniżej maksymalnego [limitu pojemności konta magazynu](../../../../common/scalability-targets-standard-account.md), ruchu przychodzącego i wychodzącego — w celu śledzenia ilości danych, które są zapisywane i odczytywane z konta usługi Azure Storage, a SuccessE2ELatency — do śledzenia czasu, w którym wysyłane są żądania do i z usługi Azure Storage i MediaAgent.

Możesz również [utworzyć alerty dzienników](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) , aby śledzić kondycję usługi Azure Storage i w dowolnym momencie wyświetlać [pulpit nawigacyjny stanu platformy Azure](https://status.azure.com/status) .

#### <a name="veeam-reporting"></a>Raportowanie Veeam

- [Konfiguruj Veeam jedno raportowanie](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Veeame alarmy dotyczące kopii zapasowych i replikacji](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>Jak otworzyć przypadki pomocy technicznej

Jeśli potrzebujesz pomocy dotyczącej tworzenia kopii zapasowych w ramach rozwiązania Azure, musisz otworzyć przypadek z systemami Veeam i Azure. Dzięki temu nasze organizacje pomocy technicznej mogą współpracować w razie potrzeby.

#### <a name="to-open-a-case-with-veeam"></a>Aby otworzyć sprawę z Veeam

W [witrynie Veeam Customer Support](https://www.veeam.com/support.html)Zaloguj się i otwórz sprawę.

Aby poznać opcje pomocy technicznej dostępne dla użytkownika przez Veeam, zobacz [zasady obsługi klienta Veeam](https://www.veeam.com/veeam_software_support_policy_ds.pdf).

Możesz również wywołać, aby otworzyć przypadek: [numery pomocy technicznej na całym świecie](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>Aby otworzyć sprawę z platformą Azure

Na pasku wyszukiwania u góry w [Azure Portal](https://portal.azure.com) Wyszukaj **Pomoc techniczną** . Wybierz pozycję **Pomoc i obsługa**  ->  **nowe żądanie obsługi**.

> [!NOTE]
> W przypadku otwarcia przypadku należy koniecznie uzyskać pomoc dotyczącą usługi Azure Storage lub sieci platformy Azure. Nie określaj Azure Backup. Azure Backup to nazwa usługi platformy Azure, a Twoja sprawa zostanie niepoprawnie przekierowana.

### <a name="links-to-relevant-veeam-documentation"></a>Linki do odpowiedniej dokumentacji Veeam

Więcej szczegółowych informacji znajduje się w następującej dokumentacji Veeam:

- [Podręcznik użytkownika Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Przewodnik po architekturze Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>Oferty portalu Marketplace

Możesz nadal używać rozwiązania Veeam, które znasz i którym ufasz, aby chronić obciążenia działające na platformie Azure. Program Veeam ułatwia wdrażanie rozwiązań na platformie Azure i ochronę Virtual Machines platformy Azure oraz wielu innych usług platformy Azure.

- [Wdróż replikację kopii zapasowych Veeam & za pośrednictwem witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [Witryna sieci Web usługi Azure Backup i Recovery Veeam](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi zasobami w witrynie sieci Web Veeam, aby uzyskać informacje na temat wyspecjalizowanych scenariuszy użycia:

- [Veeam, jak wideo](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Dokumentacja techniczna Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Veeam Knowledge Base i często zadawane pytania](https://www.veeam.com/knowledge-base.html?ad=menu-resources)

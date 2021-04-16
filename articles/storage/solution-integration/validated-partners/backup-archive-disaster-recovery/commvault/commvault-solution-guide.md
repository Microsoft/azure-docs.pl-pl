---
title: Back up your data to Azure with Commvault
titleSuffix: Azure Storage
description: Zawiera omówienie czynników, które należy wziąć pod uwagę, oraz czynności, które należy wykonać, aby użyć platformy Azure jako miejsca docelowego magazynu i lokalizacji odzyskiwania dla kompletnej kopii zapasowej i odzyskiwania aplikacji Commvault
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: fa60b6f002e49babc1e1f014bcb941e7953a43a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484782"
---
# <a name="backup-to-azure-with-commvault"></a>Tworzenie kopii zapasowej na platformie Azure za pomocą programu Commvault

Ten artykuł pomaga zintegrować infrastrukturę commvault z usługą Azure Blob Storage. Obejmuje ona wymagania wstępne, zagadnienia, implementację i wskazówki operacyjne. Ten artykuł dotyczy używania platformy Azure jako miejsca docelowego kopii zapasowej poza lokacją oraz lokacji odzyskiwania w przypadku wystąpienia awarii, co uniemożliwia normalne działanie w lokacji głównej.

> [!NOTE]
> Program Commvault oferuje rozwiązanie z niższym celem czasu odzyskiwania (RTO, Recovery Time Objective), Commvault Live Sync. To rozwiązanie umożliwia korzystanie z rezerwowej maszyny wirtualnej, która może pomóc szybciej odzyskać dane w przypadku awarii w środowisku produkcyjnym platformy Azure. Te możliwości są poza zakresem tego dokumentu.

## <a name="reference-architecture"></a>Architektura referencyjna

Na poniższym diagramie przedstawiono architekturę referencyjną wdrożeń lokalnych i wdrożeń na platformie Azure.

![Commvault to Azure Reference Architecture](../media/commvault-diagram.png)

Istniejące wdrożenie aplikacji Commvault można łatwo zintegrować z platformą Azure, dodając konto usługi Azure Storage lub wiele kont jako miejsce docelowe magazynu w chmurze. Program Commvault umożliwia również odzyskiwanie kopii zapasowych ze środowisk lokalnych na platformie Azure, co zapewnia lokację odzyskiwania na żądanie na platformie Azure.

## <a name="commvault-interoperability-matrix"></a>Macierz współdziałania commvault

| Obciążenie | GPv2 i magazyn obiektów blob | Obsługa warstwy Chłodna | Obsługa warstwy Archiwum | urządzenie Data Box Family |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Lokalne maszyny wirtualne/dane | Wersja 11.5 | Wersja 11.5 | Wersja 11.10 | Wersja 11.10 |
| Maszyny wirtualne platformy Azure | Wersja 11.5 | Wersja 11.5 | Wersja 11.5 | Nie dotyczy |
| Obiekt bob Azure | Wersja 11.6 | Wersja 11.6 | Wersja 11.6 | Nie dotyczy |
| Azure Files | Wersja 11.6 | Wersja 11.6 | Wersja 11.6 | Nie dotyczy |

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Nieco z góry planowanie pomoże Ci użyć platformy Azure jako miejsca docelowego kopii zapasowej poza lokacją i lokacji odzyskiwania.

### <a name="get-started-with-azure"></a>Pierwsze kroki z platformą Azure

Firma Microsoft oferuje platformę do obserwowania, aby rozpocząć pracę z platformą Azure. Przewodnik [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) to szczegółowe podejście do transformacji cyfrowej przedsiębiorstwa i kompleksowy przewodnik planowania wdrożenia chmury klasy produkcyjnej. Przewodnik CAF zawiera przewodnik krok po kroku po konfiguracji platformy [Azure,](/azure/cloud-adoption-framework/ready/azure-setup-guide/) który pomoże Ci szybko i bezpiecznie rozpocząć korzystanie z usługi. Wersję interaktywną można znaleźć w Azure Portal [.](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade) Znajdziesz przykładowe architektury, konkretne najlepsze rozwiązania dotyczące wdrażania aplikacji i bezpłatne zasoby szkoleniowe, aby znaleźć cię na ścieżce do wiedzy na temat platformy Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Rozważ sieć między lokalizacją i platformą Azure

Niezależnie od tego, czy zasoby w chmurze są uruchamiane w środowisku produkcyjnym, testowym i programowym, czy jako miejsce docelowe kopii zapasowej i lokacja odzyskiwania, ważne jest, aby zrozumieć zapotrzebowanie na przepustowość na potrzeby wstępnego wstępnego inicjowania kopii zapasowych i bieżących transferów z dnia na dzień.

Azure Data Box umożliwia przeniesienie początkowej linii bazowej kopii zapasowej na platformę Azure bez konieczności większej przepustowości. Jest to przydatne, jeśli szacuje się, że transfer linii bazowej trwa dłużej niż można tolerować. Możesz użyć estymatora transferu danych podczas tworzenia konta magazynu, aby oszacować czas wymagany do przeniesienia początkowej kopii zapasowej.

![Wyświetla narzędzie do szacowania transferu danych usługi Azure Storage w portalu.](../media/az-storage-transfer.png)

Pamiętaj, że wymagana będzie wystarczająca pojemność sieci do obsługi codziennych transferów danych w wymaganym oknie transferu (oknie kopii zapasowej) bez wpływu na aplikacje produkcyjne. W tej sekcji przedstawiono narzędzia i techniki, które są dostępne do oceny potrzeb sieci.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Określanie potrzebnej przepustowości

Aby określić, jaka przepustowość będzie potrzebna, użyj następujących zasobów:

- Raporty z oprogramowania do tworzenia kopii zapasowych.
- Program Commvault udostępnia standardowe raporty do [określania](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) szybkości zmian i całkowitego rozmiaru zestawu kopii zapasowych [dla](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) początkowego transferu linii bazowej na platformę Azure.
- Tworzenie kopii zapasowych narzędzi do oceny i raportowania niezależnych od oprogramowania, takich jak:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Określanie nie w pełni większej przepustowości Internetu

Ważne jest, aby wiedzieć, ile zwykle nie w pełni w pełni wykorzystanie przepustowości (lub miejsca *pracy)* jest dostępne na co dzień. Ułatwia to ocenę, czy można osiągnąć cele dotyczące:

- początkowy czas przekazywania, gdy nie używasz funkcji Azure Data Box w trybie offline
- wykonywanie codziennych kopii zapasowych na podstawie szybkości zmian zidentyfikowaną wcześniej i okna tworzenia kopii zapasowej

Użyj następujących metod, aby zidentyfikować przepustowość, z których mogą korzystać kopie zapasowe na platformie Azure.

- Jeśli jesteś istniejącym klientem Azure ExpressRoute, wyświetl [użycie](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) obwodu w Azure Portal.
- Skontaktuj się z isp isp. Powinni mieć możliwość udostępniania raportów, które pokazują istniejące dzienne i miesięczne wykorzystanie.
- Istnieje kilka narzędzi, które mogą mierzyć wykorzystanie przez monitorowanie ruchu sieciowego na poziomie routera/przełącznika. Są one następujące:
  - [Pakiet Solarwinds Bandwidth Analyzer](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Wybieranie odpowiednich opcji magazynu

Jeśli używasz platformy Azure jako miejsca docelowego kopii zapasowej, użyjesz usługi [Azure Blob Storage.](../../../../blobs/storage-blobs-introduction.md) Blob Storage to rozwiązanie firmy Microsoft do magazynowania obiektów. Usługa Blob Storage jest zoptymalizowana pod kątem przechowywania ogromnych ilości danych bez struktury, czyli danych, które nie są zgodne z żadnym modelem danych ani definicją. Ponadto usługa Azure Storage jest trwała, wysoce dostępna, bezpieczna i skalowalna. Możesz wybrać odpowiedni magazyn dla obciążenia, [](../../../../common/storage-redundancy.md) aby zapewnić poziom odporności na spełnienie wewnętrznych wymagań sla. Blob Storage to usługa opłacana za użycie. Opłata jest [naliczana](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) co miesiąc za ilość przechowywanych danych, uzyskiwanie dostępu do tych danych, a w przypadku warstw Chłodna i Archiwum jest to minimalny wymagany okres przechowywania. Opcje odporności i warstw dotyczące danych kopii zapasowej zostały podsumowane w poniższych tabelach.

**Opcje odporności usługi Blob Storage:**

|  |Lokalnie nadmiarowy  |Strefowo nadmiarowy  |Geograficznie nadmiarowy  |Strefowo nadmiarowy  |
|---------|---------|---------|---------|---------|
|**Efektywna liczba kopii**     | 3         | 3         | 6         | 6 |
|**Liczba stref dostępności**     | 1         | 3         | 2         | 4 |
|**Liczba regionów**     | 1         | 1         | 2         | 2 |
|**Ręcznego trybu failover do regionu pomocniczego**     | NIE DOTYCZY         | Nie dotyczy         | Tak         | Tak |

**Warstwy magazynu obiektów blob:**

|  | Warstwa Gorąca   |Warstwa Chłodna   | Warstwa Archiwum |
| ----------- | ----------- | -----------  | -----------  |
| **Dostępność** | 99,9%         | 99%         | Tryb offline      |
| **Opłaty za użycie** | Wyższe koszty magazynowania, niższy dostęp i koszty transakcji | Niższe koszty magazynowania, wyższy dostęp i koszty transakcji | Najniższe koszty magazynowania, najwyższy dostęp i koszty transakcji |
| **Minimalne wymagane przechowywanie danych**| Nie dotyczy | 30 dni | 180 dni |
| **Opóźnienie (czas do pierwszego bajtu)** | Milisekund | Milisekund | Godziny |

#### <a name="sample-backup-to-azure-cost-model"></a>Przykładowa kopia zapasowa w modelu kosztów platformy Azure

Płatność za użycie może być dla klientów, którzy są nowi w chmurze. Płacisz tylko za używaną pojemność, ale płacisz również za transakcje [](https://azure.microsoft.com/pricing/details/bandwidth/) (odczyt i zapis) i ruch wychodzący dla danych odczytanych z powrotem do środowiska lokalnego, gdy jest używany plan danych bezpośrednich usługi Azure Express Route w środowisku lokalnym lub nieograniczony plan danych usługi [Express Route,](https://azure.microsoft.com/pricing/details/expressroute/) w którym uwzględniono dane wychodzące z platformy Azure. Możesz użyć [kalkulatora cen platformy Azure,](https://azure.microsoft.com/pricing/calculator/) aby przeprowadzić analizę "what if". Analizę można utworzyć na podstawie cennika listy lub cennika pojemności zarezerwowanej usługi [Azure Storage,](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)co pozwala uzyskać do 38% oszczędności. Oto przykładowe ćwiczenie dotyczące cen w celu modelowania miesięcznego kosztu wykonywania kopii zapasowej na platformie Azure. To tylko przykład. *Ceny mogą się różnić z powodu działań, które nie zostały przechwycone w tym miejscu.*

|Współczynnik kosztów  |Koszt miesięczny  |
|---------|---------|
|100 TB danych kopii zapasowej w chłodnym magazynie     |1556,48 USD         |
|2 TB nowych danych zapisywanych dziennie x 30 dni     |39 USD w transakcjach          |
|Szacowana suma miesięczna     |1595,48 USD         |
|---------|---------|
|Przywracanie raz o pojemności 5 TB do środowisk lokalnych za pośrednictwem publicznego Internetu   | 491,26 USD         |

> [!NOTE]
> To oszacowanie zostało wygenerowane w kalkulatorze cen platformy Azure przy użyciu cennika z płatnością zgodnie z użyciem użycia w regionach Wschodnie usa i jest oparte na domyślnym rozmiarze fragmentu podrzędnego commvault o rozmiarze 32 MB, co generuje 65 536 żądań PUT (transakcji zapisu) dziennie. Ten przykład może nie dotyczyć Twoich wymagań.

## <a name="implementation-guidance"></a>Wskazówki dotyczące implementacji

Ta sekcja zawiera krótki przewodnik dotyczący dodawania usługi Azure Storage do lokalnego wdrożenia aplikacji Commvault. Szczegółowe wskazówki i zagadnienia dotyczące planowania można znaleźć w [przewodniku po architekturze chmury](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)publicznej firmy Commvault dla Microsoft Azure .

1. Otwórz okno Azure Portal i wyszukaj **konta magazynu.** Możesz również kliknąć domyślną **ikonę Konta magazynu.**

    ![Pokazuje dodawanie kont magazynu w Azure Portal.](../media/azure-portal.png)
  
    ![Pokazuje miejsce, w którym wpisano magazyn w polu wyszukiwania Azure Portal.](../media/locate-storage-account.png)

2. Wybierz **pozycję Utwórz,** aby dodać konto. Wybierz lub utwórz grupę zasobów, podaj unikatową nazwę, wybierz region, wybierz pozycję Wydajność w warstwie **Standardowa,** zawsze pozostaw dla konta rodzaj konta Storage **V2,** wybierz poziom replikacji, który spełnia warunki umów SLA, oraz warstwę domyślną, którą będzie stosować oprogramowanie do tworzenia kopii zapasowych. Konto usługi Azure Storage udostępnia warstwy Gorąca, Chłodna i Archiwum w ramach jednego konta, a zasady commvault umożliwiają korzystanie z wielu warstw w celu efektywnego zarządzania cyklem życia danych.

    ![Wyświetla ustawienia konta magazynu w portalu](../media/account-create-1.png)

3. Zachowaj na razie domyślne opcje sieci i przejdź do **opcji Ochrona danych.** W tym miejscu możesz włączyć usuwanie nieumyślne, co umożliwia odzyskanie przypadkowo usuniętego pliku kopii zapasowej w zdefiniowanym okresie przechowywania i zapewnia ochronę przed przypadkowym lub złośliwym usunięciem.

    ![Wyświetla ustawienia ochrony danych w portalu.](../media/account-create-2.png)

4. Następnie zalecamy domyślne ustawienia z  ekranu Zaawansowane dotyczące tworzenia kopii zapasowych w przypadkach użycia platformy Azure.

    ![Wyświetla kartę Ustawienia zaawansowane w portalu.](../media/account-create-3.png)

5. Dodaj tagi dla organizacji, jeśli używasz tagowania, i utwórz konto.

6. Przed dodaniem konta do środowiska Commvault wymagane są dwa szybkie kroki. Przejdź do konta utworzonego w Azure Portal i wybierz pozycję **Kontenery** w Blob service **menu.** Dodaj kontener i wybierz zrozumiałą nazwę. Następnie przejdź do elementu **Klucze dostępu** w obszarze **Ustawienia** i skopiuj **nazwę** konta magazynu oraz jeden z dwóch kluczy dostępu. Nazwa kontenera, nazwa konta i klucz dostępu będą potrzebne w następnych krokach.

    ![Pokazuje tworzenie kontenera w portalu.](../media/container.png)

    ![Wyświetla ustawienia klucza dostępu w portalu.](../media/access-key.png)

7. *(Opcjonalnie*) Do wdrożenia można dodać dodatkowe warstwy zabezpieczeń.

    1. Konfigurowanie dostępu opartego na rolach w celu ograniczenia osób, które mogą wprowadzać zmiany na koncie magazynu. Aby uzyskać więcej informacji, [zobacz Wbudowane role dla operacji zarządzania](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
    1. Ogranicz dostęp do konta do określonych segmentów sieci za pomocą [ustawień](../../../../common/storage-network-security.md) zapory magazynu, aby zapobiec próbom dostępu spoza sieci firmowej.

        ![Wyświetla ustawienia zapory magazynu w portalu.](../media/storage-firewall.png)

    1. Ustaw [blokadę usuwania](../../../../../azure-resource-manager/management/lock-resources.md) na koncie, aby zapobiec przypadkowemu usunięciu konta magazynu.

        ![Pokazuje ustawianie blokady usuwania w portalu.](../media/resource-lock.png)

    1. Skonfiguruj dodatkowe [najlepsze rozwiązania w zakresie zabezpieczeń.](../../../../../storage/blobs/security-recommendations.md)

1. W Centrum poleceń commvault przejdź do tematu **Zarządzanie**  ->  **zabezpieczeniami**  ->  **Menedżer poświadczeń**. Wybierz konto w  **chmurze,** typ dostawcy **Microsoft Azure Storage**, wybierz usługę **MediaAgent,** która będzie przesyłać dane na platformę Azure i z platformy Azure, a następnie dodaj nazwę konta magazynu i klucz dostępu.

    ![Pokazuje dodawanie poświadczeń w centrum poleceń usługi Commvault.](../media/commvault-credential.png)

9. Następnie przejdź do **chmury**  ->  **magazynu w** Centrum poleceń usługi Commvault. Wybierz pozycję **Dodaj.** Wprowadź przyjazną nazwę konta magazynu, a następnie **wybierz** Microsoft Azure Storage z **listy** Typ. Wybierz serwer agenta multimediów, który będzie używany do transferu kopii zapasowych do usługi Azure Storage. Dodaj utworzony kontener, wybierz warstwę magazynowania do użycia w ramach konta usługi Azure Storage i wybierz poświadczenia utworzone w kroku #8. Na koniec wybierz, czy mają być transferowane deduplikowane kopie zapasowe, czy nie, oraz lokalizację deduplikacji bazy danych.

     ![Zrzut ekranu przedstawiający interfejs użytkownika dodawania chmury w aplikacji Commvault. Z menu rozwijanego Archiwum wybrano pozycję **Archiwum**.](../media/commvault-add-storage.png)

10. Na koniec dodaj nowy zasób usługi Azure Storage do istniejącego lub nowego planu w Centrum poleceń commvault za pomocą polecenia Zarządzaj planami  ->   jako miejsce docelowe kopii zapasowej.

    ![Zrzut ekranu przedstawiający interfejs użytkownika commvault Command Center. W obszarze nawigacji po lewej stronie w obszarze **Zarządzaj** wybrano opcję **Plany**.](../media/commvault-plan.png)

11. *(Opcjonalnie)* Jeśli planujesz używać platformy Azure jako lokacji odzyskiwania lub programu Commvault do migrowania serwerów i aplikacji na platformę Azure, najlepszym rozwiązaniem jest wdrożenie serwera proxy usługi VSA na platformie Azure. Szczegółowe instrukcje można znaleźć [tutaj.](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)

## <a name="operational-guidance"></a>Wskazówki operacyjne

### <a name="azure-alerts-and-performance-monitoring"></a>Alerty platformy Azure i monitorowanie wydajności

Zaleca się monitorowanie zarówno zasobów platformy Azure, jak i możliwości korzystania z nich przez program Commvault, tak jak w przypadku dowolnego docelowego magazynu, na których polegasz, do przechowywania kopii zapasowych. Połączenie monitorowania Azure Monitor i commvault Command Center pomoże zachować dobrej kondycji środowiska.

#### <a name="azure-portal"></a>Azure Portal

Platforma Azure oferuje niezawodne rozwiązanie do monitorowania w postaci [Azure Monitor.](../../../../../azure-monitor/essentials/monitor-azure-resource.md) Możesz skonfigurować [Azure Monitor](../../../../blobs/monitor-blob-storage.md) do śledzenia pojemności usługi Azure Storage, transakcji, dostępności, uwierzytelniania i nie tylko. Pełne odwołanie do metryk, które są zbierane, można znaleźć [tutaj.](../../../../blobs/monitor-blob-storage-reference.md) Kilka przydatnych metryk do śledzenia to BlobCapacity — aby upewnić się, że nie przekraczasz maksymalnego limitu pojemności konta magazynu [,](../../../../common/scalability-targets-standard-account.md)ruch przychodzący i wychodzący — do śledzenia ilości danych zapisywanych i odczytywanych z konta usługi Azure Storage oraz SuccessE2ELatency — aby śledzić czas transmisji w obie strony dla żądań do i z usługi Azure Storage i Twojego obiektu MediaAgent.

Alerty dzienników [można również tworzyć w](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) celu śledzenia kondycji usługi Azure Storage i wyświetlania pulpitu nawigacyjnego stanu platformy [Azure](https://status.azure.com/status) w dowolnym momencie.

#### <a name="commvault-command-center"></a>Commvault Command Center

- [Tworzenie alertu dla pul magazynu w chmurze](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Aby uzyskać informacje o tym, gdzie można wyświetlić raporty wydajności, ukończenie zadania i rozpocząć podstawowe rozwiązywanie problemów, zobacz [Pulpity nawigacyjne](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm).

### <a name="how-to-open-support-cases"></a>Jak otwierać przypadki pomocy technicznej

Jeśli potrzebujesz pomocy przy kopii zapasowej w rozwiązaniu platformy Azure, otwórz przypadek zarówno w aplikacji Commvault, jak i na platformie Azure. Ułatwia to naszym organizacjom pomocy technicznej współpracę w razie potrzeby.

#### <a name="to-open-a-case-with-commvault"></a>Aby otworzyć przypadek za pomocą commvault

W [witrynie pomocy technicznej firmy Commvault](https://www.commvault.com/support)zaloguj się i otwórz przypadek.

Aby poznać dostępne opcje umów pomocy technicznej, zobacz Opcje pomocy technicznej [firmy Commvault](https://ma.commvault.com/support)

Możesz również zadzwoń, aby otworzyć przypadek lub za pośrednictwem poczty e-mail na adres pomocy technicznej aplikacji Commvault:

- Bezpłatny: +1 877-780-3077
- [Numery pomocy technicznej na całym świecie](https://ma.commvault.com/Support/TelephoneSupport)
- [Wyślij wiadomość e-mail do pomocy technicznej usługi Commvault](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Aby otworzyć przypadek przy użyciu platformy Azure

Na pasku [Azure Portal](https://portal.azure.com) wyszukaj **pomoc techniczną** na pasku wyszukiwania u góry. Wybierz **pozycję Pomoc i obsługa techniczna Nowy**  ->  **wniosek o pomoc techniczną.**

> [!NOTE]
> Po otwarciu sprawy należy pamiętać, że potrzebujesz pomocy dotyczącej usługi Azure Storage lub Azure Networking. Nie należy określać Azure Backup. Azure Backup to nazwa usługi platformy Azure, a Twój przypadek zostanie nieprawidłowo rozsyłany.

### <a name="links-to-relevant-commvault-documentation"></a>Linki do odpowiedniej dokumentacji programu Commvault

Zobacz następującą dokumentację programu Commvault, aby uzyskać więcej szczegółów:

- [Podręcznik użytkownika aplikacji Commvault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Przewodnik po architekturze platformy Azure usługi Commvault](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)

### <a name="marketplace-offerings"></a>Oferty w witrynie Marketplace

Program Commvault ułatwia wdrażanie rozwiązania na platformie Azure w celu ochrony usługi Azure Virtual Machines i wielu innych usług platformy Azure. Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Wdrażanie aplikacji Commvault za pośrednictwem Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [Program Commvault dla arkusza danych platformy Azure](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [Lista obsługiwanych funkcji i usług platformy Azure firmy Commvault](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Jak chronić dane na platformie Azure przy użyciu SAP HANA Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat wyspecjalizowanych scenariuszy użycia, zobacz te dodatkowe zasoby aplikacji Commvault.

- [Migrowanie serwerów i aplikacji na platformę Azure za pomocą programu Commvault](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Ochrona oprogramowania SAP na platformie Azure za pomocą programu Commvault](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Ochrona usługi Office 365 za pomocą aplikacji Commvault](https://www.youtube.com/watch?v=dl3nvAacxZU)
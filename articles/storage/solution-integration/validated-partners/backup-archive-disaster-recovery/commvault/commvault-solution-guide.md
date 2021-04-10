---
title: Tworzenie kopii zapasowych danych na platformie Azure za pomocą programu CommVault
titleSuffix: Azure Storage
description: Zawiera omówienie czynników, które należy wziąć pod uwagę, i kroków, które należy wykonać, aby użyć platformy Azure jako miejsca docelowego magazynu i lokalizacji odzyskiwania dla programu CommVault kompletnej kopii zapasowej i odzyskiwania
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: ce321574ce2878f51864f55bf5618df2c96d1068
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589892"
---
# <a name="backup-to-azure-with-commvault"></a>Tworzenie kopii zapasowych na platformie Azure za pomocą programu CommVault

Ten artykuł ułatwia integrację infrastruktury programu CommVault z usługą Azure Blob Storage. Obejmuje to wymagania wstępne, zagadnienia, implementacje i wskazówki operacyjne. Ten artykuł dotyczy korzystania z platformy Azure jako miejsca docelowego kopii zapasowej poza siedzibą firmy oraz lokacji odzyskiwania w przypadku wystąpienia awarii, co uniemożliwia normalne działanie w lokacji głównej.

> [!NOTE]
> Usługa CommVault oferuje niższą RTO rozwiązanie do zamierzenia czasu odzyskiwania i usługi CommVault Live Sync. To rozwiązanie umożliwia przeprowadzenie awaryjnej maszyny wirtualnej, która może pomóc w przewróceniu sprawności w przypadku awarii w środowisku produkcyjnym platformy Azure. Te możliwości wykraczają poza zakres tego dokumentu.

## <a name="reference-architecture"></a>Architektura referencyjna

Na poniższym diagramie przedstawiono architekturę referencyjną dla platformy Azure i wdrożenia w ramach platformy Azure.

![Architektura programu CommVault w ramach architektury referencyjnej platformy Azure](../media/commvault-diagram.png)

Istniejące wdrożenie programu CommVault można łatwo zintegrować z platformą Azure, dodając konto usługi Azure Storage lub wiele kont jako miejsce docelowe magazynu w chmurze. Program CommVault umożliwia również odzyskiwanie kopii zapasowych z lokalizacji lokalnej na platformę Azure, zapewniając na platformie Azure witrynę odzyskiwania na żądanie.

## <a name="commvault-interoperability-matrix"></a>Macierz zgodności programu CommVault

| Obciążenie | GPv2 i BLOB Storage | Obsługa warstwy chłodna | Obsługa warstwy Archiwum | Pomoc techniczna urządzenie Data Box Family |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Lokalne maszyny wirtualne/dane | 11,5 v | 11,5 v | 11.10 v | 11.10 v |
| Maszyny wirtualne platformy Azure | 11,5 v | 11,5 v | 11,5 v | Nie dotyczy |
| Obiekt bob Azure | 11.6 v | 11.6 v | 11.6 v | Nie dotyczy |
| Azure Files | 11.6 v | 11.6 v | 11.6 v | Nie dotyczy |

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Niewielki plan z góry pomoże Ci wykorzystać platformę Azure jako miejsce docelowe kopii zapasowych poza siedzibą firmy oraz lokację odzyskiwania.

### <a name="get-started-with-azure"></a>Pierwsze kroki z platformą Azure

Firma Microsoft oferuje platformę do rozpoczęcia pracy z platformą Azure. [Struktura wdrażania w chmurze](/azure/architecture/cloud-adoption/) (CAF) jest szczegółowym podejściem do transformacji cyfrowej przedsiębiorstwa oraz kompleksowego przewodnika po zaplanowaniu wdrożenia w chmurze klasy produkcyjnej. CAF zawiera [Przewodnik konfigurowania platformy Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) krok po kroku ułatwiający szybkie i bezpieczne rozpoczęcie pracy. Możesz znaleźć interaktywną wersję w [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Znajdziesz przykładowe architektury, konkretne najlepsze rozwiązania dotyczące wdrażania aplikacji i bezpłatne zasoby szkoleniowe, aby uzyskać informacje na temat ścieżki do wiedzy na temat platformy Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Weź pod uwagę sieć między lokalizacją i platformą Azure

Bez względu na to, czy zasoby w chmurze mają być uruchamiane w środowisku produkcyjnym, testowym, programistycznym, czy jako lokalizacja docelowa kopii zapasowej i odzyskiwanie, ważne jest, aby zrozumieć zapotrzebowanie na przepustowość na potrzeby wstępnego tworzenia kopii zapasowych i przeprowadzania codziennych transferów

Azure Data Box umożliwia przeniesienie początkowej bazowej kopii zapasowej na platformę Azure bez konieczności zwiększania przepustowości. Jest to przydatne, jeśli szacowane przeniesienie linii bazowej trwa dłużej, niż można tolerować. Możesz użyć Transfer danych szacowania podczas tworzenia konta magazynu, aby oszacować czas wymagany do przetransferowania początkowej kopii zapasowej.

![Pokazuje szacowania transferu danych usługi Azure Storage w portalu.](../media/az-storage-transfer.png)

Pamiętaj, że będziesz potrzebować wystarczającej pojemności sieci do obsługi codziennych transferów danych w wymaganym oknie transferu (okno kopii zapasowej) bez wpływu na aplikacje produkcyjne. Ta sekcja zawiera opis narzędzi i technik, które są dostępne do oceny potrzeb sieci.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Określ, ile przepustowości potrzebujesz

Aby określić wymaganą przepustowość, użyj następujących zasobów:

- Raporty z oprogramowania do tworzenia kopii zapasowych.
- Program CommVault udostępnia standardowe raporty pozwalające określić [stopień zmian](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) i [łączny rozmiar zestawu kopii zapasowych](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) dla początkowego transferu linii bazowej na platformę Azure.
- Tworzenie kopii zapasowej niezależnych od oprogramowania narzędzi do oceny i raportowania, takich jak:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
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
|**Liczba regionów**     | 1         | 1         | 2         | 2 |
|**Ręczne przejście w tryb failover do regionu pomocniczego**     | NIE DOTYCZY         | Nie dotyczy         | Tak         | Tak |

**Warstwy magazynu obiektów blob:**

|  | Warstwa gorąca   |Warstwa chłodna   | Warstwa Archiwum |
| ----------- | ----------- | -----------  | -----------  |
| **Dostępność** | 99,9%         | 99%         | Tryb offline      |
| **Opłaty za użycie** | Wyższe koszty magazynowania, niższy dostęp i koszty transakcji | Niższe koszty magazynowania, wyższego poziomu dostępu i kosztów transakcji | Najniższe koszty magazynowania, najwyższy poziom dostępu i koszty transakcji |
| **Wymagane jest minimalne przechowywanie danych**| Nie dotyczy | 30 dni | 180 dni |
| **Opóźnienie (czas do pierwszego bajtu)** | ) | ) | Godziny |

#### <a name="sample-backup-to-azure-cost-model"></a>Przykładowa kopia zapasowa do modelu kosztów platformy Azure

Dzięki opłacie za użycie można zniechęcające klientom, którzy są nowym w chmurze. Chociaż płacisz tylko za użyte miejsce, płacisz za transakcje (odczyt i zapis) oraz wychodzące [dane](https://azure.microsoft.com/pricing/details/bandwidth/) odczytane z powrotem do środowiska lokalnego, gdy plan danych oparty na usłudze Azure [Express Route Direct lub Express Route](https://azure.microsoft.com/pricing/details/expressroute/) jest używany w przypadku, gdy zostanie uwzględniony ruch wychodzący z platformy Azure. Możesz użyć [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) , aby przeprowadzić analizę "co". Możesz podstawić analizę cennika na liście lub [ceny zarezerwowanej pojemności usługi Azure Storage](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md), co może zapewnić nawet 38% oszczędności. Poniżej przedstawiono przykładowe ceny, które umożliwiają modelowanie miesięcznych kosztów tworzenia kopii zapasowych na platformie Azure. Jest to tylko przykład. *Ceny mogą się różnić w zależności od tego, co nie zostało przechwycone.*

|Współczynnik kosztu  |Koszt miesięczny  |
|---------|---------|
|100 TB danych kopii zapasowej w chłodnym magazynie     |$1556,48         |
|2 TB nowych danych pisanych dziennie x 30 dni     |$39 w transakcjach          |
|Szacowana suma miesięczna     |$1595,48         |
|---------|---------|
|Jednorazowe przywrócenie 5 TB do lokalizacji lokalnej za pośrednictwem publicznego Internetu   | $491,26         |

> [!NOTE]
> To oszacowanie zostało wygenerowane na Kalkulator cen platformy Azure przy użyciu cennika Wschodnie stany USA zgodnie z rzeczywistym użyciem i jest oparta na domyślnym rozmiarze 32 MB podrzędnego rozmiaru fragmentu, który generuje żądania PUT 65 536 (transakcje zapisu) dziennie. Ten przykład może nie mieć zastosowania do Twoich wymagań.

## <a name="implementation-guidance"></a>Wskazówki dotyczące implementacji

Ta sekcja zawiera krótki przewodnik dotyczący dodawania usługi Azure Storage do lokalnego wdrożenia programu CommVault. Aby uzyskać szczegółowe wskazówki i zagadnienia związane z planowaniem, zobacz [Przewodnik po architekturze chmury publicznej dla programu CommVault dla Microsoft Azure](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf).

1. Otwórz Azure Portal i Wyszukaj **konta magazynu**. Możesz również kliknąć ikonę domyślne **konta magazynu** .

    ![Pokazuje Dodawanie kont magazynu w Azure Portal.](../media/azure-portal.png)
  
    ![Pokazuje, gdzie wpisano magazyn w polu wyszukiwania Azure Portal.](../media/locate-storage-account.png)

2. Wybierz pozycję **Utwórz** , aby dodać konto. Wybierz lub Utwórz grupę zasobów, podaj unikatową nazwę, wybierz region, wybierz pozycję wydajność **standardowa** , a następnie pozostaw pozycję rodzaj konta jako **Magazyn v2**, wybierz poziom replikacji, który spełnia Twoje umowy SLA, oraz domyślną warstwę, która będzie stosowana przez oprogramowanie do tworzenia kopii zapasowej. Konto usługi Azure Storage udostępnia warstwy gorąca, chłodna i archiwalne dostępne w ramach jednego konta, a zasady CommVault umożliwiają efektywne zarządzanie cyklem życia danych przy użyciu wielu warstw.

    ![Pokazuje ustawienia konta magazynu w portalu](../media/account-create-1.png)

3. Pozostaw teraz domyślne opcje sieciowe i przejdź do **ochrony danych**. W tym miejscu można włączyć opcję usuwania nietrwałego, która pozwala odzyskać przypadkowo usunięty plik kopii zapasowej w określonym okresie przechowywania i zapewnia ochronę przed przypadkowym lub złośliwym usunięciem.

    ![Pokazuje ustawienia ochrony danych w portalu.](../media/account-create-2.png)

4. Następnie zalecamy ustawienia domyślne z ekranu **zaawansowanego** na potrzeby tworzenia kopii zapasowych w przypadku użycia na platformie Azure.

    ![Pokazuje kartę Ustawienia zaawansowane w portalu.](../media/account-create-3.png)

5. Dodaj Tagi dla organizacji, jeśli używasz tagowania, i utwórz swoje konto.

6. Przed dodaniem konta do środowiska CommVault należy wykonać dwa szybkie kroki. Przejdź do konta utworzonego w Azure Portal i wybierz pozycję **kontenery** w menu **BLOB Service** . Dodaj kontener i wybierz zrozumiałą nazwę. Następnie przejdź do pozycji **klucze dostępu** w obszarze **Ustawienia** i skopiuj **nazwę konta magazynu** i jeden z dwóch kluczy dostępu. W następnych krokach będzie potrzebna nazwa kontenera, nazwa konta i klucz dostępu.

    ![Pokazuje tworzenie kontenerów w portalu.](../media/container.png)

    ![Wyświetla ustawienia klucza dostępu w portalu.](../media/access-key.png)

7. (*Opcjonalnie*) Możesz dodać dodatkowe warstwy zabezpieczeń do wdrożenia.

    1. Skonfiguruj dostęp oparty na rolach, aby ograniczyć liczbę użytkowników, którzy mogą wprowadzać zmiany na koncie magazynu. Aby uzyskać więcej informacji, zobacz [wbudowane role dla operacji zarządzania](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
    1. Ogranicz dostęp do konta do określonych segmentów sieci przy użyciu [ustawień zapory magazynu](../../../../common/storage-network-security.md) , aby zapobiec próbom dostępu spoza sieci firmowej.

        ![Pokazuje ustawienia zapory magazynu w portalu.](../media/storage-firewall.png)

    1. Ustaw [blokadę usuwania](../../../../../azure-resource-manager/management/lock-resources.md) na koncie, aby zapobiec przypadkowemu usunięciu konta magazynu.

        ![Pokazuje ustawienie blokady usuwania w portalu.](../media/resource-lock.png)

    1. Skonfiguruj dodatkowe [najlepsze rozwiązania](../../../../../storage/blobs/security-recommendations.md)w zakresie zabezpieczeń.

1. W centrum poleceń programu CommVault przejdź do okna **Zarządzanie**  ->    ->  **poświadczeniami** zabezpieczeń. Wybierz **konto w chmurze**, **Typ dostawcy** **Microsoft Azure Storage**, wybierz pozycję **MediaAgent**, która spowoduje przeniesienie danych do i z platformy Azure, a następnie Dodaj nazwę konta magazynu i klucz dostępu.

    ![Pokazuje Dodawanie poświadczeń w centrum poleceń programu CommVault.](../media/commvault-credential.png)

9. Następnie przejdź do usługi **Storage**  ->  w **chmurze** w centrum poleceń programu CommVault. Wybierz, aby **dodać**. Wprowadź przyjazną nazwę konta magazynu, a następnie wybierz **Microsoft Azure Storage** z listy **Typ** . Wybierz serwer programu Media Agent, który ma być używany do transferu kopii zapasowych do usługi Azure Storage. Dodaj utworzony kontener, wybierz warstwę magazynowania, która ma być używana w ramach konta usługi Azure Storage, a następnie wybierz poświadczenia utworzone w kroku #8. Na koniec wybierz, czy mają być transferowane deduplikowane kopie zapasowe, czy nie i lokalizacja bazy danych deduplikacji.

     ![Zrzut ekranu przedstawiający Dodawanie interfejsu użytkownika w chmurze programu CommVault. W menu rozwijanym archiwum * jest zaznaczone polecenie * * archiwalne * *.](../media/commvault-add-storage.png)

10. Na koniec Dodaj nowy zasób usługi Azure Storage do istniejącego lub nowego planu w centrum poleceń programu CommVault przez **Zarządzanie**  ->  **planami** jako miejscem docelowym kopii zapasowej.

    ![Zrzut ekranu przedstawiający interfejs użytkownika centrum poleceń programu CommVault. W lewym okienku nawigacji w obszarze * * jest zaznaczone polecenie * * Zarządzanie * *, * * plany * *.](../media/commvault-plan.png)

11. *(Opcjonalnie)* Jeśli planujesz używać platformy Azure jako lokacji odzyskiwania lub programu CommVault do migrowania serwerów i aplikacji na platformę Azure, najlepszym rozwiązaniem jest wdrożenie serwera proxy VSA na platformie Azure. Szczegółowe instrukcje można znaleźć [tutaj](https://documentation.commvault.com/commvault/v11/article?p=106208.htm).

## <a name="operational-guidance"></a>Wskazówki dotyczące działania

### <a name="azure-alerts-and-performance-monitoring"></a>Alerty i monitorowanie wydajności platformy Azure

Zaleca się monitorowanie zarówno zasobów platformy Azure, jak i zdolności firmy CommVault do używania ich w taki sposób, aby można było korzystać z nich w celu przechowywania kopii zapasowych. Kombinacja Azure Monitor i CommVault centrum poleceń umożliwia zachowanie kondycji środowiska.

#### <a name="azure-portal"></a>Azure Portal

Platforma Azure oferuje niezawodne rozwiązanie do monitorowania w postaci [Azure monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Azure Monitor można [skonfigurować](../../../../blobs/monitor-blob-storage.md) do śledzenia pojemności, transakcji, dostępności, uwierzytelniania i innych możliwości usługi Azure Storage. W [tym miejscu](../../../../blobs/monitor-blob-storage-reference.md)możesz znaleźć pełne odwołanie do zbieranych metryk. Oto kilka przydatnych metryk do śledzenia BlobCapacity — aby upewnić się, że pozostanie poniżej maksymalnego [limitu pojemności konta magazynu](../../../../common/scalability-targets-standard-account.md), ruchu przychodzącego i wychodzącego — w celu śledzenia ilości danych, które są zapisywane i odczytywane z konta usługi Azure Storage, a SuccessE2ELatency — do śledzenia czasu, w którym wysyłane są żądania do i z usługi Azure Storage i MediaAgent.

Możesz również [utworzyć alerty dzienników](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) , aby śledzić kondycję usługi Azure Storage i w dowolnym momencie wyświetlać [pulpit nawigacyjny stanu platformy Azure](https://status.azure.com/status) .

#### <a name="commvault-command-center"></a>CommVault centrum poleceń

- [Tworzenie alertu dla pul magazynu w chmurze](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Aby uzyskać informacje o tym, gdzie można wyświetlić raporty dotyczące wydajności, ukończenie zadania i rozpocząć podstawowe Rozwiązywanie problemów, zobacz [pulpity nawigacyjne](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm).

### <a name="how-to-open-support-cases"></a>Jak otworzyć przypadki pomocy technicznej

Jeśli potrzebujesz pomocy dotyczącej tworzenia kopii zapasowej rozwiązania Azure, należy otworzyć sprawę zarówno w programie CommVault, jak i na platformie Azure. Dzięki temu nasze organizacje pomocy technicznej mogą współpracować w razie potrzeby.

#### <a name="to-open-a-case-with-commvault"></a>Aby otworzyć sprawę przy użyciu programu CommVault

W [witrynie wsparcia firmy CommVault](https://www.commvault.com/support)Zaloguj się, a następnie otwórz sprawę.

Aby poznać dostępne opcje kontraktu pomocy technicznej, zobacz [Opcje pomocy technicznej programu CommVault](https://ma.commvault.com/support)

Możesz również wywołać, aby otworzyć sprawę lub skontaktować się z pomocą techniczną w ramach programu CommVault:

- Bezpłatny: + 1 877-780-3077
- [Numery pomocy technicznej na całym świecie](https://ma.commvault.com/Support/TelephoneSupport)
- [Obsługa wiadomości e-mail w programie CommVault](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Aby otworzyć sprawę z platformą Azure

Na pasku wyszukiwania u góry w [Azure Portal](https://portal.azure.com) Wyszukaj **Pomoc techniczną** . Wybierz pozycję **Pomoc i obsługa**  ->  **nowe żądanie obsługi**.

> [!NOTE]
> W przypadku otwarcia przypadku należy koniecznie uzyskać pomoc dotyczącą usługi Azure Storage lub sieci platformy Azure. Nie określaj Azure Backup. Azure Backup to nazwa usługi platformy Azure, a Twoja sprawa zostanie niepoprawnie przekierowana.

### <a name="links-to-relevant-commvault-documentation"></a>Linki do odpowiedniej dokumentacji programu CommVault

Więcej informacji można znaleźć w następującej dokumentacji programu CommVault:

- [Podręcznik użytkownika firmy CommVault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Przewodnik dotyczący architektury platformy Azure firmy CommVault](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16)

### <a name="marketplace-offerings"></a>Oferty portalu Marketplace

Program CommVault ułatwia wdrażanie rozwiązań na platformie Azure w celu ochrony Virtual Machines platformy Azure i wielu innych usług platformy Azure. Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Wdrażanie usług CommVault za pośrednictwem portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [Oprogramowanie CommVault dla usługi Azure Datasheet](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [Lista obsługiwanych funkcji i usług platformy Azure](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Jak chronić SAP HANA na platformie Azure za pomocą programu CommVault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tymi dodatkowymi zasobami usługi CommVault, aby uzyskać informacje na temat wyspecjalizowanych scenariuszy użycia.

- [Migrowanie serwerów i aplikacji na platformę Azure za pomocą programu CommVault](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Ochrona oprogramowania SAP na platformie Azure za pomocą programu CommVault](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Ochrona usługi Office 365 za pomocą programu CommVault](https://www.youtube.com/watch?v=dl3nvAacxZU)
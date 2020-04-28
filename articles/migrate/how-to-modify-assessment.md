---
title: Dostosowywanie ocen dla oceny Azure Migrate Server | Microsoft Docs
description: Opisuje sposób dostosowywania ocen utworzonych przy użyciu oceny Azure Migrate Server
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "68234267"
---
# <a name="customize-an-assessment"></a>Dostosowywanie oceny

W tym artykule opisano sposób dostosowywania ocen utworzonych przez Azure Migrate oceny serwera.

[Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych chmur prywatnych/publicznych na platformie Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.

Za pomocą narzędzia do oceny serwera Azure Migrate można tworzyć oceny dla lokalnych maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V w ramach przygotowania do migracji na platformę Azure.

## <a name="about-assessments"></a>Informacje o ocenach

Istnieją dwa typy ocen, które można uruchomić za pomocą Azure Migrate oceny serwera.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparta na wydajności** | Oceny oparte na zebranych danych wydajności | **Zalecany rozmiar maszyny wirtualnej**: na podstawie danych użycia procesora CPU i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany w warstwie Standardowa lub Premium)**: w zależności od liczby operacji we/wy na sekundę i przepływności dysków lokalnych.
**Jako lokalne** | Oceny oparte na wymiarach lokalnych. | **Zalecany rozmiar maszyny wirtualnej**: na podstawie rozmiaru lokalnego maszyny wirtualnej<br/><br> **Zalecany typ dysku**: na podstawie ustawienia typu magazynu wybieranego do oceny.


## <a name="how-is-an-assessment-done"></a>Jak odbywa się Ocena?

Ocena dokonana w ocenie serwera Azure Migrate ma trzy etapy. Ocena rozpoczyna się od analizy gotowości, a następnie ustalania rozmiarów, a wreszcie szacowania kosztów miesięcznych. Komputer jest przenoszony tylko do późniejszego etapu, jeśli przekaże poprzednią. Na przykład jeśli komputer nie może sprawdzić przydatności do platformy Azure, zostanie oznaczony jako nieodpowiedni dla platformy Azure, a zmiany rozmiarów i kosztów nie będą wykonywane. [Dowiedz się więcej.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Co obejmuje ocena?

**Właściwość** | **Szczegóły**
--- | ---
**Lokalizacja docelowa** | Lokalizacja platformy Azure, do której chcesz przeprowadzić migrację.<br/> Ocena serwera obsługuje obecnie następujące regiony docelowe: Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada środkowa, Kanada Wschodnia, Indie Środkowe, środkowe stany USA, Chiny Wschodnie, Chiny Północne, Azja Wschodnia, Wschodnie stany USA, East stany USA 2, Niemcy środkowe, Niemcy Wschodnie, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa, Południowe stany USA, Europa Północna, Południowe stany USA, Azja Południowo-Wschodnia, Indie Południowo-środkowe, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo , US Gov Teksas, US Gov Wirginia, zachodnio-środkowe stany USA, Europa Zachodnia, Indie Zachodnie, zachodnie stany USA i zachodnie stany USA 2.
**Typ magazynu** | Za pomocą tej właściwości można określić typ dysków, na które chcesz przenieść na platformę Azure.<br/><br/> W przypadku ustalania rozmiarów lokalnych można określić docelowy typ magazynu jako dyski zarządzane w warstwie Premium, dyski zarządzane SSD w warstwie Standardowa lub dyski zarządzane HDD w warstwie Standardowa. W przypadku ustalania rozmiarów na podstawie wydajności można określić typ dysku docelowego w postaci dysków zarządzanych w warstwie Premium, dysków zarządzanych przez program HDD w warstwie Standardowa lub dysków zarządzanych przy użyciu SSD w warstwie Standardowa.<br/><br/> Jeśli typ magazynu zostanie określony jako automatyczny, zalecenia dotyczące dysku są wykonywane na podstawie danych wydajności dysków (IOPS i przepływności). W przypadku określenia typu magazynu jako warstwy Premium/standardowa Ocena będzie polecać dyskową jednostkę SKU w wybranym typie magazynu. Jeśli chcesz uzyskać umowę SLA dotyczącą pojedynczej instancji na maszynę wirtualną o 99,9%, możesz określić typ magazynu jako dysk zarządzany w warstwie Premium. Gwarantuje to, że wszystkie dyski w ocenie są zalecane jako dyski zarządzane w warstwie Premium. Azure
**Wystąpienia zarezerwowane (RI)** | Ta właściwość pozwala określić, czy istnieją [wystąpienia zarezerwowane](https://azure.microsoft.com/pricing/reserved-vm-instances/) na platformie Azure, szacowania kosztów w ocenie są następnie wykonywane z uwzględnieniem zniżek na RI. Wystąpienia zarezerwowane są obecnie obsługiwane tylko w przypadku oferty z opcją płatność zgodnie z rzeczywistym użyciem w Azure Migrate.
**Kryterium ustalania wielkości** | Kryterium, które ma być używane dla maszyn wirtualnych o odpowiednim rozmiarze dla platformy Azure. Można albo zmienić rozmiar maszyny wirtualnej na *podstawie wydajności* , jak i *w ten sposób*, bez uwzględniania historii wydajności.
**Historia wydajności** | Czas, który należy wziąć pod uwagę przy ocenie danych wydajności maszyn. Ta właściwość ma zastosowanie tylko wtedy, gdy kryterium ustalania wielkości jest *oparte na wydajności*.
**Użycie percentyla** | Wartość percentyla próbki wydajności uwzględniana w celu prawidłowego określenia rozmiaru. Ta właściwość ma zastosowanie tylko wtedy, gdy rozmiar jest *oparty na wydajności*.
**Serie maszyn wirtualnych** |     Określić możesz serie maszyn wirtualnych, które mają być brane pod uwagę podczas ustalania właściwego rozmiaru. Na przykład jeśli masz środowisko produkcyjne, które nie jest planowane do migracji na maszyny wirtualne z serii A na platformie Azure, możesz wykluczyć serię z listy lub serii, a po prawej stronie jest wykonywane tylko w wybranej serii.
**Współczynnik komfortu** | Azure Migrate oceny serwera traktuje bufor (współczynnik komfortu) podczas oceny. Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną.
**Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/), w której dokonano rejestracji. Zgodnie z tym usługa Azure Migrate odpowiednio szacuje koszty.
**Waluta** | Twoja waluta rozliczeniowa.
**Rabat (%)** | Dowolny rabat skojarzony z daną subskrypcją, stosowany do całej oferty platformy Azure.<br/> Ustawienie domyślne to 0%.
**Czas pracy maszyny wirtualnej** | Jeśli maszyny wirtualne nie będą działały 24x7 na platformie Azure, możesz określić czas trwania (liczbę dni miesięcznie i liczbę godzin dziennie), dla których byłyby uruchomione, a oszacowania kosztów zostaną odpowiednio wykonane.<br/> Wartość domyślna to 31 dni w miesiącu i 24 godziny dziennie.
**Korzyść użycia hybrydowego platformy Azure** | Określ, czy masz program Software Assurance i czy masz uprawnienia do [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). W przypadku wybrania opcji Yes maszyny wirtualne z systemem Windows są wyceniane jak platforma Azure z systemami innymi niż Windows. Wartość domyślna to Yes.


## <a name="edit-assessment-properties"></a>Edytuj właściwości oceny

Aby edytować właściwości oceny po utworzeniu oceny, wykonaj następujące czynności:

1. W projekcie Azure Migrate kliknij pozycję **serwery**.
2. W **Azure Migrate: Ocena serwera**, kliknij licznik oceny wydajności.
3. W obszarze **Ocena**kliknij odpowiednią ocenę > **Edytuj właściwości**.
5. Dostosuj właściwości oceny zgodnie z powyższą tabelą.
6. Kliknij przycisk **Zapisz** , aby zaktualizować ocenę.


Możesz również edytować właściwości oceny podczas tworzenia oceny.


## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.

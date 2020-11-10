---
title: Zarządzanie punktami odzyskiwania
description: Dowiedz się, jak usługa Azure Backup zarządza punktami odzyskiwania dla maszyn wirtualnych
ms.topic: conceptual
ms.date: 11/08/2020
ms.openlocfilehash: 256df693aba0f799c24bcba6defe846e5c37ccaa
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428708"
---
# <a name="manage-recovery-points"></a>Zarządzanie punktami odzyskiwania

W tym artykule opisano, jak działa przechowywanie w przypadku maszyn wirtualnych. Za każdym razem, gdy wykonywane są kopie zapasowe, punkty odzyskiwania są tworzone, z których można wykonywać operacje przywracania.

W przypadku maszyn wirtualnych początkowa kopia zapasowa jest pełną kopią zapasową, a kolejne kopie zapasowe stanowią przyrostowe kopie zapasowe.

## <a name="recovery-points-and-retention"></a>Punkty odzyskiwania i przechowywanie

### <a name="scheduled-initial-and-incremental-backup"></a>Zaplanowane wstępne i przyrostowe kopie zapasowe

Wykonajmy uproszczoną przykładową maszynę wirtualną *V1* z dyskiem danych składającym się z czterech bloków: blok 1, blok 2, blok 3 i blok 4. Rozmiar każdego bloku wynosi 16 KB.

![Maszyna wirtualna z czterema blokami](./media/manage-recovery-points/four-blocks.png)

**Krok 1. początkowa kopia zapasowa:** Początkowa kopia zapasowa jest pełną kopią zapasową. Działa jako linia bazowa, w której są stosowane kolejne przyrostowe kopie zapasowe. Załóżmy, że dane są zapisywane w bloku 1 i bloku 2 na źródłowej maszynie wirtualnej. Te same dane zostaną zreplikowane jako D1 i D2 w magazynie magazynu Recovery Services.

![Początkowa kopia zapasowa jest replikowana](./media/manage-recovery-points/initial-backup.png)

**Krok 2 — przyrostowa kopia zapasowa 1:** Rozważ dodanie nowych danych do bloku 3 maszyny wirtualnej. Te same dane zostaną zreplikowane w następnej przyrostowej kopii zapasowej, a tylko blok, który został zmieniony, jest przechowywany jako D3.  W każdym kroku, nawet jeśli 1 KB bloku ulegnie zmianie, cały blok 16 KB zostanie przekazany w punkcie odzyskiwania.

![Pierwsza przyrostowa kopia zapasowa](./media/manage-recovery-points/first-incremental-backup.png)

**Krok 3 — przyrostowa kopia zapasowa 2:**  Teraz Rozważmy zmiany danych w bloku 3 i bloku 2 na źródłowej maszynie wirtualnej. Te zmiany zostaną zreplikowane na następnej przyrostowej kopii zapasowej jako D3 "i D2".

![Druga przyrostowa kopia zapasowa](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>Kopia zapasowa na żądanie

Można uruchomić kopię zapasową maszyny wirtualnej na żądanie w dowolnym momencie po skonfigurowaniu na niej ochrony.

- Kopia zapasowa na żądanie będzie pełną kopią zapasową, jeśli zostanie wyzwolona przed pierwszą zaplanowaną kopią zapasową.
- Jeśli początkowa kopia zapasowa zostanie zakończona, a kopia zapasowa na żądanie jest wyzwalana, jest to przyrostowa kopia zapasowa.
- Czas przechowywania punktów odzyskiwania utworzonych dla kopii zapasowej na żądanie jest wartością przechowywania określoną podczas wyzwalania kopii zapasowej.

### <a name="storage-cost"></a>Koszty magazynu

**Punkt odzyskiwania** utworzony dla początkowej kopii zapasowej zawiera wszystkie bloki, które mają dane. Kolejne przyrostowe punkty odzyskiwania składają się tylko z bloków, które mają zmienione dane. Koszty magazynu odpowiadają sumie wszystkich bloków obejmujących wszystkie punkty odzyskiwania.

Skorzystajmy z powyższego przykładu, aby zrozumieć koszt magazynu po każdym kroku:

|Krok  |Typ kopii zapasowej  |Bloki zostały zmienione  |Typ magazynu |
|------|---------|---------|---------|
|1     |     Początkowa kopia zapasowa    | Block 1, blok 2        |    Odpowiadające punktowi odzyskiwania 1 (D1 + D2)     |
|2     |  Przyrostowa kopia zapasowa 1       |  Blok 3       |   Odpowiadające punktowi odzyskiwania 1 (D1 + D2) + punkt odzyskiwania 2 (D3)      |
|3     |    Przyrostowa kopia zapasowa 2     |    Blok 2, blok 3     |   Odpowiadające punktowi odzyskiwania 1 (D1 + D2) + odzyskiwanie punktu 2 (D3) + punkt odzyskiwania 3 (D2 ' + D3 ')      |

### <a name="recovery-point-expiration"></a>Wygaśnięcie punktu odzyskiwania

Każdy punkt odzyskiwania ma czas przechowywania określony w zasadach tworzenia kopii zapasowych. Oczyszczanie odbywa się w regularnych odstępach czasu, a wszystkie punkty odzyskiwania, które wygasły, zostały wyczyszczone.

Po wygaśnięciu punktu odzyskiwania zostaje on usunięty lub scalony.

### <a name="case-1-initial-recovery-point-expires"></a>Przypadek 1: początkowy punkt odzyskiwania wygasa

Po wygaśnięciu początkowego punktu odzyskiwania zostaje on scalony z następnym przyrostowym punktem odzyskiwania. Wszystkie bloki danych, które są zastępowane w przyrostowym punkcie odzyskiwania, zostaną usunięte, a reszta zostanie scalona. Przyrostowe kopie zapasowe staną się początkową pełną kopią zapasową. Przejrzyjmy przykład:

- *Punkt odzyskiwania 1* tworzony podczas początkowej kopii zapasowej ma pełną kopię zapasową maszyny wirtualnej.
- Gdy *punkt odzyskiwania 1* wygaśnie, *punkt odzyskiwania 2* jest następną pełną kopią zapasową.
- Blok D1 zostanie scalony z *punktem odzyskiwania 2* , a D2 zostaje usunięty, ponieważ dane w bloku 2 są zastępowane w *punkcie odzyskiwania 2*. Ta zmiana jest przechwytywana jako blok D2.
- Blok D1 jest zachowywany w kolejnych punktach odzyskiwania w taki sam sposób, dopóki nie zostaną wprowadzone żadne zmiany przed następną kopią zapasową.

![Pierwszy przypadek](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>Przypadek 2: różnica między przyrostowym punktem odzyskiwania wygasa

- Jeśli *punkt odzyskiwania 2* wygaśnie przed *punktem odzyskiwania 1* , dane z *punktu odzyskiwania 2* zostaną scalone z następnym dostępnym punktem odzyskiwania: *punkt odzyskiwania 3*. Dlatego blok D3 jest scalany z *punktem odzyskiwania 3*.
- *Punkt odzyskiwania 1* to nadal pełna kopia zapasowa z blokami D1 i D2.

![Drugi przypadek](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>Przypadek 3: punkt odzyskiwania na żądanie wygasa

W tym przykładzie zaplanowano uruchomienie zasad harmonogramu (codziennej kopii zapasowej) z okresem przechowywania na *n* dni.  Jeśli kopia zapasowa na żądanie zostanie wyzwolona w czwartym dniu przed następną zaplanowaną kopią zapasową, a jej okres przechowywania jest określony jako 10 dni, nadal będzie to przyrostowa kopia zapasowa. Punkt odzyskiwania ( *na żądanie PO1* ) zostanie utworzony po *punkcie odzyskiwania 3* i przed *punktem odzyskiwania 4*.  Na koniec dnia 14 punkt odzyskiwania na żądanie ( *Po1 na żądanie* ) wygasa i zostanie scalony z następnym dostępnym punktem odzyskiwania. Bloki danych, które są nadal obecne na serwerze, są scalane, a bloki danych, które uległy zmianie (zastąpione lub usunięte), są usuwane z wygasłego punktu odzyskiwania.

![Trzecia sprawa](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>Wpływ zmiany zasad w punktach odzyskiwania

Gdy zasady są modyfikowane, są stosowane do nowych i istniejących punktów odzyskiwania. Aby uzyskać więcej informacji, zobacz [wpływ zmiany zasad w punktach odzyskiwania](backup-architecture.md#impact-of-policy-change-on-recovery-points).

### <a name="impact-of-stop-protection-on-recovery-points"></a>Wpływ zatrzymania ochrony w punktach odzyskiwania

Istnieją dwa sposoby na zatrzymanie ochrony maszyny wirtualnej:

- **Zatrzymaj ochronę i Usuń dane kopii zapasowej.** Ta opcja spowoduje zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej na podstawie ochrony maszyny wirtualnej i usunięcie wszystkich punktów odzyskiwania. Jeśli [usuwanie nietrwałe](backup-azure-security-feature-cloud.md) jest włączone, usunięte dane będą przechowywane przez 14 dni. Opłaty nie są naliczane w przypadku elementów w stanie nieusuniętym. Dane można cofnąć usunięcia w okresie 14 dni. Jeśli usuwanie nietrwałe nie jest włączone, dane zostaną natychmiast oczyszczone i nie będzie można przywrócić maszyny wirtualnej ani użyć opcji **Wznów tworzenie kopii zapasowej** .
- **Zatrzymaj ochronę i Zachowaj dane kopii zapasowej.** Ta opcja spowoduje zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej na podstawie ochrony maszyny wirtualnej. Niemniej jednak usługa Azure Backup nie zachowuje już kopii zapasowych punktów odzyskiwania. Musisz zanieść opłaty za przechowywanie punktów odzyskiwania w magazynie (zobacz [Azure Backup cennika](https://azure.microsoft.com/pricing/details/backup/) , aby uzyskać szczegółowe informacje). W razie potrzeby będzie można przywrócić maszynę wirtualną. Jeśli zdecydujesz się na wznowienie ochrony maszyny wirtualnej, możesz użyć opcji **Wznów tworzenie kopii zapasowej** . Po wznowieniu tworzenia kopii zapasowej reguły przechowywania zostaną zastosowane do punktów wygaśnięcia. Można również usunąć kopię zapasową danych przy użyciu opcji  **Usuń dane kopii zapasowej** .

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>Wpływ usunięcia maszyny wirtualnej bez zatrzymywania ochrony

Usuwanie maszyny wirtualnej bez zatrzymywania ochrony ma wpływ na punkty odzyskiwania i jest to niepożądany scenariusz. Idealne kopie zapasowe powinny zostać zatrzymane przed usunięciem maszyny wirtualnej. Ponieważ zasób nie istnieje, zaplanowane kopie zapasowe zakończą się niepowodzeniem z [powodu błędu VMNotFoundV2](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found). Punkty odzyskiwania będą czyszczone okresowo zgodnie z zasadami przechowywania, ale Ostatnia kopia maszyny wirtualnej pozostanie nieograniczona i zostanie naliczona odpowiednio. W zależności od danego scenariusza dostępne są następujące dwie opcje:

- **Opcja 1:** Przywróć maszynę wirtualną przy użyciu dowolnego punktu odzyskiwania. Jeśli chcesz odzyskać usuniętą maszynę wirtualną, przywróć ją, używając tej samej nazwy i w tej samej grupie zasobów. W przypadku ochrony przywróconej maszyny wirtualnej w tym samym magazynie istniejące punkty odzyskiwania zostaną automatycznie dołączone.
- **Opcja 2:** Przejdź do magazynu Recovery Services i Zatrzymaj ochronę za pomocą pozycji Usuń dane.

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>Wpływ wygasłych punktów odzyskiwania dla elementów w nietrwałej stanie usunięty

Jeśli funkcja [usuwania nietrwałego](backup-azure-security-feature-cloud.md) jest włączona dla magazynu usługi Recovery Services, wygasły punkt odzyskiwania pozostaje w stanie nietrwałego usunięcia i nie jest czyszczony. Nie są naliczane żadne opłaty, gdy punkt odzyskiwania jest w stanie usunięcia nietrwałego.

### <a name="impact-of-churn-on-backup-performance"></a>Wpływ zmiany wydajności kopii zapasowej

Załóżmy, że łączny magazyn maszyny wirtualnej wynosi 8 TB, a jego współczynnik zmian wynosi 5%. Następnie odpowiedni przyrostowy magazyn kopii zapasowych będzie wynosił 5% 8 TB, czyli 0,4 TB. Wyższe zmiany odnoszą się do większej ilości miejsca w magazynie zaplecza dla kolejnych przyrostowych kopii zapasowych. Zmiany mają wpływ na wydajność tworzenia kopii zapasowych. Im większa wartość, tym wolniejszy proces tworzenia kopii zapasowej i większe zużycie magazynu zaplecza.

Aby zrozumieć, jak zmiany mają wpływ na wydajność tworzenia kopii zapasowych, należy zapoznać się z tym scenariuszem:

|Maszyny wirtualne  |Maszyna wirtualna 1  |Maszyna wirtualna 2  |VM3  |
|---------|---------|---------|---------|
|Liczba dysków z danymi    | 4 (A1, A2, A3, A4)        | 4 (B1, B2, B3, B4)        |  4 (C1, C2, C3, C4)       |
|Rozmiar każdego dysku   |      4 TB   | 4 TB        |  4 TB       |
|Zmiany danych kopii zapasowej    |   A1 – 4 TB      | B1-1 TB; B2 — 1 TB <br> B3-1 TB; B4 — 1 TB  |   C1 – 2 TB; C4 – 2 TB      |

Wydajność kopii zapasowej będzie w kolejności VM2>VM3>VM1. Przyczyną jest to, że zmienione dane są rozkładane na różnych dyskach. Ponieważ tworzenie kopii zapasowych dysków odbywa się równolegle, VM2 będzie zawierać najlepszą wydajność.

## <a name="next-steps"></a>Następne kroki

- [Azure Backup architektura i składniki](backup-architecture.md)

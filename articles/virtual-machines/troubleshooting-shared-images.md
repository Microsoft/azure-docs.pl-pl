---
title: Rozwiązywanie problemów z obrazami udostępnionymi na platformie Azure
description: Dowiedz się, jak rozwiązywać problemy z udostępnionymi galeriami obrazów.
author: olayemio
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: 015fa201fe1c31dde2e30c2fe689ac13452b1b01
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607596"
---
# <a name="troubleshoot-shared-image-galleries-in-azure"></a>Rozwiązywanie problemów z udostępnionymi galeriami obrazów na platformie Azure

Jeśli masz problemy z wykonywaniem operacji na udostępnionych galeriach obrazów, definicjach obrazów i wersjach obrazu, uruchom ponownie polecenie w trybie debugowania. Tryb debugowania można aktywować, przechodząc do `--debug` przełącznika przy użyciu interfejsu wiersza polecenia platformy Azure i `-Debug` przełącznika przy użyciu programu PowerShell. Po zlokalizowaniu błędu postępuj zgodnie z tym artykułem, aby rozwiązać ten problem.


## <a name="creating-or-modifying-a-gallery"></a>Tworzenie lub modyfikowanie galerii ##

**Komunikat**: *Nazwa galerii jest nieprawidłowa. Dozwolone znaki to angielskie znaki alfanumeryczne ze znakami podkreślenia i kropki dozwolone w środku, do 80 znaków. Wszystkie inne znaki specjalne, w tym łączniki, są niedozwolone.*  
**Przyczyna**: Nazwa galerii nie spełnia wymagań dotyczących nazewnictwa.  
**Obejście**: wybierz nazwę, która spełnia następujące warunki: 
- Ma limit 80 znaków
- Zawiera tylko litery angielskie, cyfry, podkreślenia i kropki
- Zaczyna się i kończą z angielską literą lub cyframi

**Komunikat**: *Nazwa jednostki "galleryname" jest nieprawidłowa zgodnie z jej regułą walidacji: ^ [^ \_ \w] [\w-. \_ ] {0,79} (? <! [-.]) $.*  
**Przyczyna**: Nazwa galerii nie spełnia wymagań dotyczących nazewnictwa.  
**Obejście**: wybierz nazwę galerii, która spełnia następujące warunki: 
- Ma limit 80 znaków
- Zawiera tylko litery angielskie, cyfry, podkreślenia i kropki
- Zaczyna się i kończą z angielską literą lub cyframi

**Komunikat**: *podana nazwa zasobu <Galeria \> zawiera następujące nieprawidłowe znaki końcowe: <\> . Nazwa nie może kończyć się znakami: <znaku \>*  
**Przyczyna**: Nazwa galerii kończąca się kropką lub podkreśleniem.  
**Obejście**: wybierz nazwę galerii, która spełnia następujące warunki: 
- Ma limit 80 znaków
- Zawiera tylko litery angielskie, cyfry, podkreślenia i kropki
- Zaczyna się i kończą z angielską literą lub cyframi

**Komunikat**: *podana lokalizacja <region \> nie jest dostępny dla typu zasobu "Microsoft. COMPUTE/Galerie". Lista dostępnych regionów dla typu zasobu to...*  
**Przyczyna**: region określony dla galerii jest niepoprawny lub wymaga żądania dostępu.  
**Obejście**: Sprawdź, czy nazwa regionu jest poprawna. Jeśli nazwa regionu jest poprawna, Prześlij [żądanie dostępu](/troubleshoot/azure/general/region-access-request-process) do regionu.

**Komunikat**: *nie można usunąć zasobu przed usunięciem zasobów zagnieżdżonych.*  
**Przyczyna**: podjęto próbę usunięcia galerii zawierającej co najmniej jedną istniejącą definicję obrazu. Galeria musi być pusta, aby można było ją usunąć.  
**Obejście**: Usuń wszystkie definicje obrazów w galerii, a następnie wybierz pozycję Usuń galerię. Jeśli definicja obrazu zawiera wersje obrazu, należy usunąć wersje obrazu przed usunięciem definicji obrazu.

**Komunikat**: *nazwa galerii "<galleryname \> " nie jest unikatowa w ramach subskrypcji " <subscriptionID> ". Wybierz inną nazwę galerii.*  
**Przyczyna**: masz istniejącą galerię o tej samej nazwie i podjęto próbę utworzenia innej galerii o tej samej nazwie.  
**Obejście**: Wybierz inną nazwę galerii.

**Komunikat**: *zasób <galleryname \> istnieje już w lokalizacji <region \_ 1 \> w grupie zasobów <grup źródłowych \> . Nie można utworzyć zasobu o tej samej nazwie w lokalizacji <regionie \_ 2 \> . Wybierz nową nazwę zasobu.*  
**Przyczyna**: masz istniejącą galerię o tej samej nazwie i podjęto próbę utworzenia innej galerii o tej samej nazwie.  
**Obejście**: Wybierz inną nazwę galerii.

## <a name="creating-or-modifying-image-definitions"></a>Tworzenie lub modyfikowanie definicji obrazu ##

**Komunikat**: *Zmiana właściwości "galleryImage. Properties. <Właściwość \> " jest niedozwolona.*  
**Przyczyna**: podjęto próbę zmiany typu systemu operacyjnego, stanu systemu operacyjnego, generacji funkcji Hyper-V, oferty, wydawcy lub jednostki SKU. Zmiana żadnej z tych właściwości jest niedozwolona.  
**Obejście**: zamiast tego Utwórz nową definicję obrazu.

**Komunikat**: *zasób <galleryname/imageDefinitionName \> już istnieje w lokalizacji <region \_ 1 \> w grupie zasobów <grupy odsources \> . Nie można utworzyć zasobu o tej samej nazwie w lokalizacji <regionie \_ 2 \> . Wybierz nową nazwę zasobu.*  
**Przyczyna**: masz istniejącą definicję obrazu w tej samej galerii i grupie zasobów o tej samej nazwie. Podjęto próbę utworzenia kolejnej definicji obrazu o tej samej nazwie i w tej samej galerii, ale w innym regionie.  
**Obejście**: Użyj innej nazwy dla definicji obrazu lub Umieść definicję obrazu w innej galerii lub grupie zasobów.

**Komunikat**: *podana nazwa zasobu <Gallery \> /<imageDefinitionName \> ma następujące nieprawidłowe znaki końcowe: znak <\> . Nazwa nie może kończyć się znakami: <znaku \>*  
**Przyczyna**: <nazwa imageDefinitionName \> kończąca się kropką lub podkreśleniem.  
**Obejście**: wybierz nazwę definicji obrazu spełniającą następujące warunki: 
- Ma limit 80 znaków
- Zawiera tylko litery angielskie, cyfry, podkreślenia, łączniki i kropki
- Zaczyna i kończyć się literami lub cyframi w języku angielskim.

**Komunikat**: *Nazwa jednostki <imageDefinitionName \> jest nieprawidłowa zgodnie z jej regułą walidacji: ^ [^ \_ \\ W] [ \\ W-. \_ ] {0,79} (? <! [-.]) $"*  
**Przyczyna**: <nazwa imageDefinitionName \> kończąca się kropką lub podkreśleniem.  
**Obejście**: wybierz nazwę definicji obrazu spełniającą następujące warunki: 
- Ma limit 80 znaków
- Zawiera tylko litery angielskie, cyfry, podkreślenia, łączniki i kropki
- Zaczyna się i kończą z angielską literą lub cyframi

**Komunikat**: *Nazwa elementu zawartości galleryImage. Properties. Identifier. <Właściwość \> jest nieprawidłowa. Nie może być pusty. Dozwolone znaki to wielkie lub małe litery, cyfry, łącznik (-), kropka (.), znak podkreślenia ( \_ ). Nazwy nie mogą kończyć się kropką (.). Długość nazwy nie może przekraczać <znaków liczbowych \> .*  
**Przyczyna**: wartość wydawcy, oferty lub jednostki SKU nie spełnia wymagań dotyczących nazewnictwa.  
**Obejście**: wybierz wartość, która spełnia następujące warunki: 
- Ma 128-limit znaków wydawcy lub 64-znak dla oferty i jednostki SKU
- Zawiera tylko litery angielskie, cyfry, łączniki, podkreślenia i kropki
- Nie kończy się kropką

**Komunikat**: *nie można wykonać żądanej operacji na zasobie zagnieżdżonym. \> Nie odnaleziono elementu nadrzędnego <galleryname.*  
**Przyczyna**: nie istnieje Galeria o nazwie <galleryname \> w bieżącej subskrypcji i grupie zasobów.  
**Obejście**: Sprawdź, czy nazwy galerii, subskrypcji i grupy zasobów są poprawne. W przeciwnym razie Utwórz nową galerię o nazwie <galleryname \> .

**Komunikat**: *podana lokalizacja <region \> nie jest dostępny dla typu zasobu "Microsoft. COMPUTE/Galerie". Lista dostępnych regionów dla typu zasobu to...*  
**Przyczyna**: nazwa regionu <\> jest niepoprawna lub wymaga żądania dostępu.  
**Obejście**: Sprawdź, czy nazwa regionu jest wpisana poprawnie. Możesz uruchomić to polecenie, aby zobaczyć, do jakich regionów masz dostęp. Jeśli region nie znajduje się na liście, Prześlij [żądanie dostępu](/troubleshoot/azure/general/region-access-request-process).

**Komunikat**: *nie można serializować wartości: <wartość \> jako typ: "ISO-8601"., ISO8601Error: brak elementu "de" w ISO 8601. Nie można przeanalizować ciągu DateTime \> <wartość*  
**Przyczyna**: wartość podana dla właściwości nie jest prawidłowo sformatowana jako Data.  
**Obejście**: Podaj datę w formacie RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz lub [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-prawidłowy format.

**Komunikat**: nie *można przekonwertować ciągu na wartość DateTimeOffset: <\> . Ścieżka "Properties. <Właściwość \> "*  
**Przyczyna**: wartość podana dla właściwości nie jest prawidłowo sformatowana jako Data.  
**Obejście**: Podaj datę w formacie RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz lub [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-prawidłowy format.

**Komunikat**: *EndOfLifeDate musi być ustawiona na datę przyszłą.*  
**Przyczyna**: Właściwość data końca okresu istnienia nie jest poprawnie sformatowana jako data, która jest datą dzisiejszą.  
**Obejście**: Podaj datę w formacie RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz lub [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-prawidłowy format.

**Message**: *argument--<Właściwość \> : nieprawidłowa wartość int: <wartość \>*  
**Przyczyna**: Właściwość <\> akceptuje tylko wartości całkowite, a <wartość \> nie jest liczbą całkowitą.  
**Obejście**: wybierz wartość całkowitą.

**Komunikat**: *minimalna wartość właściwości <\> nie może być większa niż maksymalna wartość właściwości <\> .*  
**Przyczyna**: wartość minimalna podana dla właściwości <\> jest wyższa niż maksymalna wartość podana dla właściwości <\> .  
**Obejście**: Zmień wartości tak, aby wartość minimalna była mniejsza lub równa maksymalnej.

**Komunikat**: *zdjęcie z galerii: <imageDefinitionName \> identyfikowane przez (Wydawca: <publisher \> , oferta: <oferta \> , jednostka SKU: <SKU \> ) już istnieje. Wybierz innego wydawcę, ofertę, kombinację jednostki SKU.*  
**Przyczyna**: podjęto próbę utworzenia nowej definicji obrazu z tym samym wydawcą, ofertą i jednostką SKU tryplet jako istniejącą definicję obrazu w tej samej galerii.  
**Obejście**: w galerii wszystkie definicje obrazów muszą mieć unikatową kombinację wydawcy, oferty i jednostki SKU. Wybierz unikatową kombinację lub wybierz nową galerię i ponownie Utwórz definicję obrazu.

**Komunikat**: *nie można usunąć zasobu przed usunięciem zasobów zagnieżdżonych.*  
**Przyczyna**: podjęto próbę usunięcia definicji obrazu zawierającej wersje obrazu. Definicja obrazu musi być pusta, aby można było ją usunąć.  
**Obejście**: Usuń wszystkie wersje obrazu w definicji obrazu, a następnie wybierz pozycję Usuń definicję obrazu.

**Komunikat**: *nie można powiązać właściwości <parametru \> . Nie można przekonwertować wartości <wartości \> na typ <PropertyType \> . Nie można dopasować nazwy identyfikatora <wartość \> do prawidłowej nazwy modułu wyliczającego. Określ jedną z następujących nazw modułów wyliczających i spróbuj ponownie: <wybór \_ 1 \> , <wyboru \_ 2 \> ,.* ..  
**Przyczyna**: właściwość ma ograniczoną listę możliwych wartości, a <wartość \> nie jest jedną z nich.  
**Obejście**: Wybierz jedną z możliwych <\> wartości wyboru.

**Komunikat**: *nie można powiązać właściwości <parametru \> . Nie można przekonwertować wartości <wartość \> na typ &quot; System. &quot; DateTime*  
**Przyczyna**: wartość podana dla właściwości nie jest prawidłowo sformatowana jako Data.  
**Obejście**: Podaj datę w formacie RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz lub [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-prawidłowy format.

**Komunikat**: *nie można powiązać właściwości <parametru \> . Nie można przekonwertować wartości <wartość \> na typ &quot; System. &quot; Int32*  
**Przyczyna**: Właściwość <\> akceptuje tylko wartości całkowite, a <wartość \> nie jest liczbą całkowitą.  
**Obejście**: wybierz wartość całkowitą.

**Komunikat**: *Typ konta magazynu ZRS nie jest obsługiwany w tym regionie.*  
**Przyczyna**: wybrano magazyn strefowo nadmiarowy (ZRS) w regionie, który jeszcze go nie obsługuje.  
**Obejście**: Zmień typ konta magazynu na **\_ LRS Premium** lub **standardowa \_ LRS**. Zapoznaj się z naszą dokumentacją, aby uzyskać najnowszą [listę regionów](../storage/common/storage-redundancy.md#zone-redundant-storage) z WŁĄCZONĄ funkcją ZRS Preview.

## <a name="creating-or-updating-image-versions"></a>Tworzenie lub aktualizowanie wersji obrazu ##

**Komunikat**: *podana lokalizacja <region \> nie jest dostępny dla typu zasobu "Microsoft. COMPUTE/Galerie". Lista dostępnych regionów dla typu zasobu to...*  
**Przyczyna**: nazwa regionu <\> jest niepoprawna lub wymaga żądania dostępu.  
**Obejście**: Sprawdź, czy nazwa regionu jest wpisana poprawnie. Możesz uruchomić to polecenie, aby zobaczyć, do jakich regionów masz dostęp. Jeśli region nie znajduje się na liście, Prześlij [żądanie dostępu](/troubleshoot/azure/general/region-access-request-process).

**Komunikat**: *nie można wykonać żądanej operacji na zasobie zagnieżdżonym. \> Nie znaleziono zasobu nadrzędnego <Gallery/imageDefinitionName.*  
**Przyczyna**: nie istnieje Galeria o nazwie <Gallery/imageDefinitionName \> w bieżącej subskrypcji i grupie zasobów.  
**Obejście**: Sprawdź, czy nazwy galerii, subskrypcji i grupy zasobów są poprawne. W przeciwnym razie Utwórz nową galerię o nazwie <galleryname \> i/lub definicji obrazu o nazwie <imageDefinitionName \> w wskazanej grupie zasobów.

**Komunikat**: *nie można powiązać właściwości <parametru \> . Nie można przekonwertować wartości <wartość \> na typ &quot; System. &quot; DateTime*  
**Przyczyna**: wartość podana dla właściwości nie jest prawidłowo sformatowana jako Data.  
**Obejście**: Podaj datę w formacie RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz lub [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-prawidłowy format.

**Komunikat**: *nie można powiązać właściwości <parametru \> . Nie można przekonwertować wartości <wartość \> na typ &quot; System. &quot; Int32*  
**Przyczyna**: Właściwość <\> akceptuje tylko wartości całkowite, a <wartość \> nie jest liczbą całkowitą.  
**Obejście**: wybierz wartość całkowitą.

**Komunikat**: *wersja obrazu galerii regiony profilu publikowania <publishingRegions \> musi zawierać lokalizację wersji obrazu <sourceRegion \>*  
**Przyczyna**: Lokalizacja obrazu źródłowego (<sourceRegion \> ) musi być uwzględniona na liście <publishingRegions \> .  
**Obejście**: uwzględnij <sourceRegion \> na liście <publishingRegions \> .

**Komunikat**: *wartość <wartość \> Właściwości <parametru \> jest poza zakresem. Wartość musi należeć do przedziału od <minValue \> i <MaxValue \> włącznie.*  
**Przyczyna**: wartość <\> jest poza zakresem możliwych wartości właściwości <\> .  
**Obejście**: wybierz wartość znajdującą się w zakresie <minValue \> i <MaxValue \> włącznie.

**Komunikat**: *nie znaleziono zasobu źródłowego <ResourceID \> . Sprawdź, czy źródło istnieje i znajduje się w tym samym regionie, w którym jest tworzona wersja obrazu galerii.*  
**Przyczyna**: nie ma źródła zlokalizowanego w <ResourceID \> lub źródło w <ResourceID \> nie znajduje się w tym samym regionie, w którym jest tworzony obraz galerii.  
**Obejście**: Sprawdź, czy <\> wartość ResourceID jest poprawna i czy region źródłowy wersji obrazu galerii jest taki sam jak region <\> wartości ResourceID.

**Komunikat**: *Zmiana właściwości "galleryImageVersion. Properties. obszarze storageprofile. <diskImage \> . Source.ID" jest niedozwolona.*  
**Przyczyna**: nie można zmienić identyfikatora źródła wersji obrazu galerii po utworzeniu.  
**Obejście**: Upewnij się, że identyfikator źródła jest taki sam jak istniejący identyfikator źródła, Zmień numer wersji obrazu lub Usuń bieżącą wersję obrazu i spróbuj ponownie.

**Komunikat**: *wykryto zduplikowane numery LUN na dyskach danych wejściowych. Numer LUN musi być unikatowy dla każdego dysku z danymi.*  
**Przyczyna**: podczas tworzenia wersji obrazu przy użyciu listy dysków i/lub migawek dysku, co najmniej dwa dyski lub migawki dysków mają tę samą jednostkę LUN.  
**Obejście**: Usuń lub Zmień wszystkie zduplikowane jednostki LUN.

**Komunikat**: *zduplikowane identyfikatory źródeł znajdują się na dyskach wejściowych. Identyfikator źródła musi być unikatowy dla każdego dysku.*  
**Przyczyna**: podczas tworzenia wersji obrazu przy użyciu listy dysków i/lub migawek dysku, co najmniej dwa dyski lub migawki dysków mają ten sam identyfikator zasobu.  
**Obejście**: Usuń lub Zmień zduplikowane identyfikatory źródeł dysków.

**Komunikat**: *Identyfikator właściwości <ResourceID \> w ścieżce "properties. obszarze storageprofile. <diskImages \> . Source.ID" jest nieprawidłowy. Oczekiwano w pełni kwalifikowanego identyfikatora zasobu rozpoczynającego się od "/subscriptions/ <subscriptionID> " lub "/Providers/ <resourceProviderNamespace> /".*  
**Przyczyna**: wartość <ResourceID \> jest niepoprawnie sformatowana.  
**Obejście**: Sprawdź, czy identyfikator zasobu jest prawidłowy.

**Komunikat**: *identyfikator źródła: <ResourceID \> musi być obrazem zarządzanym, maszyną wirtualną lub inną wersją obrazu galerii*  
**Przyczyna**: wartość <ResourceID \> jest niepoprawnie sformatowana.  
**Obejście**: Jeśli używasz maszyny wirtualnej, obrazu zarządzanego lub wersji obrazu galerii jako obrazu źródłowego, sprawdź, czy identyfikator zasobu maszyny wirtualnej, obrazu zarządzanego lub wersji obrazu galerii jest prawidłowy.

**Komunikat**: *identyfikator źródła: <ResourceID \> musi być dyskiem zarządzanym lub migawką.*  
**Przyczyna**: wartość <ResourceID \> jest niepoprawnie sformatowana.  
**Obejście**: Jeśli używasz dysków i/lub migawek dysków jako źródeł dla wersji obrazu, sprawdź, czy identyfikatory zasobów dysków i/lub migawek dysku są poprawne.

**Komunikat**: *nie można utworzyć wersji obrazu galerii z: <ResourceID \> , ponieważ stan systemu operacyjnego w obrazie galerii nadrzędnej (<OsState \_ 1 \> ) nie jest <OsState \_ 2 \> .*  
**Przyczyna**: stan systemu operacyjnego (uogólniony lub wyspecjalizowany) nie jest zgodny z stanem systemu operacyjnego określonym w definicji obrazu.  
**Obejście**: Wybierz źródło na podstawie maszyny wirtualnej ze stanem systemu operacyjnego <OsState \_ 1 \> lub Utwórz nową definicję obrazu dla maszyn wirtualnych na podstawie <OsState \_ 2 \> .

**Komunikat**: *zasób o identyfikatorze "<ResourceID \> " ma inną generację funkcji hypervisor ["<V # \_ 1 \> "] niż generacja funkcji hypervisor obrazu galerii nadrzędnej ["<V # \_ 2 \> "]*  
**Przyczyna**: generowanie funkcji hypervisor wersji obrazu nie jest zgodne z generowaniem funkcji hypervisor określonej w definicji obrazu. System operacyjny definicji obrazu jest <V # \_ 1 \> , a wersja obrazu systemu operacyjnego jest <V # \_ 2 \> .  
**Obejście**: Wybierz źródło z tą samą generację funkcji hypervisor co definicja obrazu lub Utwórz/wybierz nową definicję obrazu, która ma taką samą generację funkcji hypervisor jak wersja obrazu.

**Komunikat**: *zasób o identyfikatorze "<ResourceID \> " ma inny typ systemu operacyjnego ["<OsType \_ 1 \> "] niż generacja typu systemu operacyjnego obrazów galerii nadrzędnych ["<OsType \_ 2 \> "]*  
**Przyczyna**: generowanie funkcji hypervisor wersji obrazu nie jest zgodne z generowaniem funkcji hypervisor określonej w definicji obrazu. System operacyjny definicji obrazu jest <OsType \_ 1 \> , a wersja obrazu system operacyjny to <OsType \_ 2 \> .  
**Obejście**: Wybierz źródło z tym samym systemem operacyjnym (Linux/Windows) jak definicja obrazu lub Utwórz/wybierz nową definicję obrazu, która ma taką samą generację systemu operacyjnego jak wersja obrazu.

**Komunikat**: *źródłowa maszyna wirtualna <ResourceID \> nie może zawierać dysku z systemem operacyjnym.*  
**Przyczyna**: źródło w <ResourceID \> zawiera dysk tymczasowe systemu operacyjnego. Galeria obrazów udostępnionych nie obsługuje obecnie tymczasowych dysków systemu operacyjnego.  
**Obejście**: Wybierz inne źródło w oparciu o maszynę wirtualną, która nie korzysta z dysku z systemem operacyjnym.

**Komunikat**: *źródłowa maszyna wirtualna <ResourceID \> nie może zawierać dysku ["<diskID \> "] przechowywanego w typie konta UltraSSD.*  
**Przyczyna**: dysk <diskID \> to dysk SSD w warstwie Ultra. Galeria obrazów udostępnionych nie obsługuje obecnie SSD w warstwie Ultra dysków.  
**Obejście**: Użyj źródła, które zawiera tylko SSD w warstwie Premium, SSD w warstwie Standardowa i/lub HDD w warstwie Standardowa dyski zarządzane.

**Komunikat**: *źródłowa maszyna wirtualna <ResourceID \> należy utworzyć z Managed Disks.*  
**Przyczyna**: maszyna wirtualna w <ResourceID \> używa dysków niezarządzanych.  
**Obejście**: Użyj źródła w oparciu o maszynę wirtualną, która zawiera tylko SSD w warstwie Premium, SSD w warstwie Standardowa i/lub HDD w warstwie Standardowa dysków zarządzanych.

**Komunikat**: *zbyt wiele żądań w źródle "<ResourceID \> ". Zmniejsz liczbę żądań w źródle lub zaczekaj trochę czasu przed ponowną próbą.*  
**Przyczyna**: obecnie jest ograniczana wartość źródła dla tej wersji obrazu z powodu zbyt dużej liczby żądań.  
**Obejście**: Spróbuj utworzyć wersję obrazu później.

**Komunikat**: *zestaw szyfrowania dysków "<diskEncryptionSetID \> " musi znajdować się w tej samej subskrypcji "<subskrypcji" \> jako zasób galerii.*  
**Przyczyna**: zestawy szyfrowania dysków mogą być używane tylko w tej samej subskrypcji i regionie, w której zostały utworzone.  
**Obejście**: Utwórz lub użyj szyfrowania ustawionego w tej samej subskrypcji i regionie co wersja obrazu.

**Komunikat**: *zaszyfrowane źródło: "<ResourceID \> " ma inny identyfikator subskrypcji niż bieżąca wersja obrazu galerii "<subskrypcji \_ 1 \> ". Spróbuj ponownie, używając niezaszyfrowanych źródeł lub Użyj subskrypcji "<subcriptionID \_ 2" źródła, \> Aby utworzyć wersję obrazu galerii.*  
**Przyczyna**: Galeria obrazów udostępnionych nie obsługuje obecnie tworzenia wersji obrazu w innej subskrypcji z innego obrazu źródłowego, jeśli obraz źródłowy jest szyfrowany.  
**Obejście**: Użyj nieszyfrowanego źródła lub Utwórz wersję obrazu w tej samej subskrypcji co źródło.

**Komunikat**: *\> nie znaleziono ustawienia szyfrowania dysków <diskEncryptionSetID.*  
**Przyczyna**: szyfrowanie dysku może być nieprawidłowe.  
**Obejście**: Sprawdź, czy identyfikator zasobu zestawu szyfrowania dysków jest poprawny.

**Komunikat**: *Nazwa wersji obrazu jest nieprawidłowa. Nazwa wersji obrazu powinna być zgodna z wersją główną (int). Pomocniczy (int). Format poprawki (int), na przykład: 1.0.0, 2018.12.1 itp.*  
**Przyczyna**: prawidłowy format wersji obrazu to trzy liczby całkowite oddzielone kropką. Nazwa wersji obrazu nie jest zgodna z prawidłowym formatem.  
**Obejście**: Użyj nazwy wersji obrazu, która jest zgodna z formatem "główna" (int). Pomocniczy (int). Poprawka (int). Na przykład: 1.0.0. lub 2018.12.1.

**Komunikat**: *wartość parametru galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. dataDiskImages. diskEncryptionSetId jest nieprawidłowa*  
**Przyczyna**: Identyfikator zasobu zestawu szyfrowania dysków użyty w obrazie dysku danych używa nieprawidłowego formatu.  
**Obejście**: Upewnij się, że identyfikator zasobu ustawiony na dysk jest zgodny z formatem/subscriptions/<Identyfikator subskrypcji \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.COMPUTE/<diskEncryptionSetName \> .

**Komunikat**: *wartość parametru galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. osDiskImage. diskEncryptionSetId jest nieprawidłowa.*  
**Przyczyna**: Identyfikator zasobu zestawu szyfrowania dysku użyty w obrazie dysku systemu operacyjnego ma nieprawidłowy format.  
**Obejście**: Upewnij się, że identyfikator zasobu ustawiony na dysk jest zgodny z formatem/subscriptions/<Identyfikator subskrypcji \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.COMPUTE/<diskEncryptionSetName \> .

**Komunikat**: *nie można określić nowej jednostki LUN szyfrowania obrazu dysku danych [numer <\> ] z ustawionym szyfrowaniem dysków w regionie [<region \> ] dla żądania wersji obrazu galerii aktualizacji. Aby zaktualizować tę wersję, Usuń nową jednostkę LUN. Jeśli trzeba zmienić ustawienia szyfrowania obrazu dysku danych, należy utworzyć nową wersję obrazu galerii z prawidłowymi ustawieniami.*  
**Przyczyna**: dodano szyfrowanie do dysku danych istniejącej wersji obrazu. Nie można dodać szyfrowania do istniejącej wersji obrazu.  
**Obejście**: Utwórz nową wersję obrazu galerii lub Usuń dodane ustawienia szyfrowania.

**Komunikat**: *Źródło wersji artefaktu galerii można określić bezpośrednio w obszarze storageprofile lub w ramach poszczególnych dysków systemu operacyjnego lub danych. Można podać jeden i tylko jeden typ źródła (obraz użytkownika, migawka, dysk, maszyna wirtualna).*  
**Przyczyna**: brak identyfikatora źródła.  
**Obejście**: Upewnij się, że identyfikator źródła źródła jest obecny.

**Komunikat**: *nie znaleziono źródła: <ResourceID \> . Upewnij się, że źródło istnieje.*  
**Przyczyna**: Identyfikator zasobu źródła może być nieprawidłowy.  
**Obejście**: Upewnij się, że identyfikator zasobu źródła jest prawidłowy.

**Komunikat**: *dla dysku "galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. osDiskImage. diskEncryptionSetId" w regionie docelowym "<region 1" wymagany jest zestaw szyfrowania \_ dysku \> \> , ponieważ dla odpowiedniego dysku w regionie "<region \_ 2 \> " jest używany zestaw szyfrowania dysków "<diskEncryptionSetId"* .  
**Przyczyna**: szyfrowanie zostało użyte na dysku systemu operacyjnego w <regionie \_ 2 \> , ale nie w <regionie \_ 1 \> .  
**Obejście**: Jeśli używasz szyfrowania na dysku systemu operacyjnego, użyj szyfrowania we wszystkich regionach.

**Komunikat**: *zestaw szyfrowania dysków jest wymagany dla dysku "LUN <Number \> " w regionie docelowym "<region \_ 1", \> ponieważ zestaw szyfrowania dysków "<diskEncryptionSetID \> " jest używany dla odpowiedniego dysku w regionie "<region \_ 2 \> "* .  
**Przyczyna**: szyfrowanie zostało użyte na dysku danych w numerze LUN <numerem \> w <regionie \_ 2 \> , ale nie w <regionie \_ 1 \> .  
**Obejście**: Jeśli używasz szyfrowania na dysku danych, użyj szyfrowania we wszystkich regionach.

**Komunikat**: *określono nieprawidłową jednostkę lun [numer <\> ] w elemencie Encryption. dataDiskImages. Numer LUN musi mieć jedną z następujących wartości ["0, 9"].*  
**Przyczyna**: jednostka LUN określona dla szyfrowania nie jest zgodna z żadną z numerów LUN dysków podłączonych do maszyny wirtualnej.  
**Obejście**: Zmień numer LUN w szyfrowaniu na numer LUN dysku danych znajdującego się na maszynie wirtualnej.

**Komunikat**: *określono zduplikowane jednostki LUN "<Number \> " w regionie docelowym "<" \> Encryption. dataDiskImages.*  
**Przyczyna**: w ustawieniach szyfrowania używanych w regionie <\> określono jednostkę LUN co najmniej dwa razy.  
**Obejście**: Zmień jednostkę lun w <regionie, \> Aby upewnić się, że wszystkie jednostki LUN są unikatowe w <regionie \> .

**Komunikat**: *OSDiskImage i DataDiskImage nie mogą wskazywać tego samego obiektu \> BLOB <sourceID*  
**Przyczyna**: źródła dysku systemu operacyjnego i co najmniej jednego dysku z danymi nie są unikatowe.  
**Obejście**: Zmień źródło dysku systemu operacyjnego i/lub dysków danych, aby upewnić się, że dysk systemu operacyjnego oraz każdy dysk z danymi są unikatowe.

**Komunikat**: *zduplikowane regiony są niedozwolone w docelowych regionach publikacji.*  
**Przyczyna**: region znajduje się na liście regionów publikacji więcej niż raz.  
**Obejście**: Usuń zduplikowany region.

**Komunikat**: *Dodawanie nowych dysków z danymi lub zmiana jednostki LUN dysku z danymi w istniejącym obrazie jest niedozwolone.*  
**Przyczyna**: wywołanie aktualizacji wersji obrazu zawiera nowy dysk z danymi lub ma nową jednostkę LUN dla dysku.  
**Obejście**: Użyj numerów LUN i dysków danych istniejącej wersji obrazu.

**Komunikat**: *zestaw szyfrowania dysków <diskEncryptionSetID \> musi należeć do tej samej subskrypcji <\> Identyfikator subskrypcji jako zasób galerii.*  
**Przyczyna**: Galeria obrazów udostępnionych obecnie nie obsługuje korzystania z szyfrowania dysków w innej subskrypcji.  
**Obejście**: Utwórz wersję obrazu i konfigurację szyfrowania dysków w tej samej subskrypcji.

**Komunikat**: *Replikacja nie powiodła się w tym regionie ze względu na to, że rozmiar zasobu źródłowego GalleryImageVersion 2048 przekracza maksymalny rozmiar obsługiwany przez 1024.*  
**Przyczyna**: dysk danych w źródle jest większy niż 1 TB.  
**Obejście**: Zmień rozmiar dysku z danymi na 1 TB.

## <a name="creating-or-updating-a-vm-or-scale-sets-from-an-image-version"></a>Tworzenie lub aktualizowanie maszyn wirtualnych lub zestawów skalowania z wersji obrazu ##

**Komunikat**: *nie istnieje Najnowsza wersja obrazu dla "<imageDefinitionResourceID \> "*  
**Przyczyna**: definicja obrazu użyta do wdrożenia maszyny wirtualnej nie zawiera żadnych wersji obrazu, które są zawarte w najnowszym.  
**Obejście**: Upewnij się, że istnieje co najmniej jedna wersja obrazu, która ma ustawioną wartość "Wyklucz z najnowszej" jako false. 

**Komunikat**: *Klient ma uprawnienia do wykonania akcji "Microsoft. COMPUTE/Galerie/images/Versions/Read" w zakresie <ResourceID \> , ale bieżąca dzierżawa <tenantID \> nie jest autoryzowana do uzyskiwania dostępu do połączonej subskrypcji <subskrypcji \> .*  
**Przyczyna**: maszyna wirtualna lub zestaw skalowania został utworzony za pomocą obrazu SIG w innej dzierżawie. Podjęto próbę wprowadzenia zmiany do maszyny wirtualnej lub zestawu skalowania, ale nie masz dostępu do subskrypcji, która jest właścicielem obrazu.  
**Obejście**: skontaktuj się z właścicielem subskrypcji wersji obrazu, aby przyznać dostęp do odczytu do wersji obrazu.

**Komunikat**: *obraz galerii <ResourceID \> nie jest dostępny w regionie <region \> . Skontaktuj się z właścicielem obrazu, aby przeprowadzić replikację do tego regionu lub zmienić żądany region.*  
**Przyczyna**: maszyna wirtualna jest tworzona w regionie, który nie należy do listy opublikowanych regionów obrazu galerii.  
**Obejście**: Replikuj obraz do regionu lub Utwórz maszynę wirtualną w jednym z regionów w regionach publikowania obrazu galerii.

**Komunikat**: *parametr "osProfile" jest niedozwolony.*  
**Przyczyna**: podano nazwę użytkownika administratora, hasło lub klucze SSH dla maszyny wirtualnej, która została utworzona z wyspecjalizowanej wersji obrazu.  
**Obejście**: Jeśli zamierzasz utworzyć MASZYNę wirtualną na podstawie tego obrazu, nie dołączaj nazwy użytkownika, hasła lub kluczy SSH administratora. W przeciwnym razie użyj uogólnionej wersji obrazu i podaj nazwę użytkownika, hasło lub klucze SSH administratora.

**Komunikat**: *Brak wymaganego parametru "osProfile" (null).*  
**Przyczyna**: maszyna wirtualna jest tworzona na podstawie uogólnionego obrazu i nie zawiera nazwy użytkownika administratora, hasła lub kluczy SSH. Ponieważ obrazy uogólnione nie zachowują nazwy użytkownika administratora, hasła lub kluczy SSH, te pola muszą zostać określone podczas tworzenia maszyny wirtualnej lub zestawu skalowania.  
**Obejście**: Określ nazwę użytkownika, hasło lub klucze SSH administratora lub użyj wyspecjalizowanej wersji obrazu.

**Komunikat**: *nie można utworzyć wersji obrazu galerii z: <ResourceID \> , ponieważ stan systemu operacyjnego w obrazie galerii nadrzędnej ("wyspecjalizowany") nie jest "uogólniony".*  
**Przyczyna**: wersja obrazu jest tworzona na podstawie uogólnionego źródła, ale jego definicja nadrzędna jest wyspecjalizowana.  
**Obejście**: Utwórz wersję obrazu przy użyciu wyspecjalizowanego źródła lub użyj uogólnionej definicji nadrzędnej.

**Komunikat**: *nie można zaktualizować zestawu skalowania maszyn wirtualnych <vmssName \> , ponieważ bieżący stan systemu operacyjnego zestawu skalowania maszyn wirtualnych jest ogólny, który jest inny niż zaktualizowany stan systemu operacyjnego obrazu galerii, który jest wyspecjalizowany.*  
**Przyczyna**: bieżący obraz źródła zestawu skalowania to uogólniony obraz źródłowy, ale jest aktualizowany przy użyciu obrazu źródła, który jest wyspecjalizowany. Bieżący obraz źródłowy i nowy obraz źródłowy zestawu skalowania muszą być tego samego stanu.  
**Obejście**: Aby zaktualizować zestaw skalowania, użyj uogólnionej wersji obrazu.

**Komunikat**: *ustawiono szyfrowanie dysków <diskEncryptionSetID \> w galerii obrazów udostępnionych <versionID \> należy do subskrypcji <identyfikatora subskrypcji \_ 1 \> i nie można jej użyć z zasobem "" w subskrypcji <Identyfikator subskrypcji \_ 2 \>*  
**Przyczyna**: zestaw szyfrowania dysków używany do szyfrowania wersji obrazu znajduje się w innej subskrypcji niż subskrypcja do hostowania wersji obrazu.  
**Obejście**: Użyj tej samej subskrypcji dla wersji obrazu i zestawu szyfrowania dysków.

**Komunikat**: *Tworzenie zestawu skalowania maszyn wirtualnych lub maszyny wirtualnej zajmuje dużo czasu.*  
**Obejście**: Sprawdź, czy **OSType** wersji obrazu, z którą próbujesz utworzyć maszynę wirtualną lub zestaw skalowania maszyn wirtualnych, ma ten sam **OSType** źródła, którego użyto do utworzenia wersji obrazu. 

**Komunikat**: *zasób o ID <identyfikatorze maszyny wirtualnej \> ma inny plan ["{ \" name \" : \" <name> \" , \" Publisher \" : \" <publisher> \" , \" Product \" : \" <product> \" , \" promotionCode \" : \" <promotionCode> \" }"] niż nadrzędny plan obrazu galerii ["null"].*  
**Przyczyna**: definicja obrazu nadrzędnego dla wdrażanej wersji obrazu nie ma informacji o planie zakupu.  
**Obejście**: Utwórz definicję obrazu z tym samym szczegóły planu zakupu w komunikacie o błędzie i Utwórz wersję obrazu w ramach definicji obrazu.


## <a name="creating-a-disk-from-an-image-version"></a>Tworzenie dysku na podstawie wersji obrazu ##

**Komunikat**: *wartość parametru elementu imagereference jest nieprawidłowa.*  
**Przyczyna**: próbowano wyeksportować z wersji obrazu SIG do dysku, ale użyto pozycji LUN, która nie istnieje na obrazie.    
**Obejście**: Sprawdź wersję obrazu, aby zobaczyć, które pozycje LUN są używane.

## <a name="sharing-resources"></a>Udostępnianie zasobów

Funkcja [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/rbac-and-directory-admin-roles.md)na platformie Azure umożliwia udostępnianie galerii obrazów, definicji obrazu oraz zasobów wersji obrazu w różnych subskrypcjach. 

## <a name="replication-speed"></a>Szybkość replikacji

Użyj flagi **--expand ReplicationStatus** , aby sprawdzić, czy replikacja do wszystkich określonych regionów docelowych została zakończona. Jeśli tak nie jest, poczekaj na ukończenie zadania przez maksymalnie sześć godzin. Jeśli to się nie powiedzie, wyzwól polecenie ponownie, aby utworzyć i replikować wersję obrazu. Jeśli istnieje wiele regionów docelowych, do których jest replikowana wersja obrazu, należy wziąć pod uwagę replikację w fazach.

## <a name="azure-limits-and-quotas"></a>Limity przydziału i ograniczenia platformy Azure 

[Ograniczenia i limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) mają zastosowanie do całej galerii obrazów udostępnionych, definicji obrazu i zasobów wersji obrazu. Upewnij się, że jesteś w limicie limitów dla subskrypcji. 


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [udostępnionych galeriach obrazów](./shared-image-galleries.md).
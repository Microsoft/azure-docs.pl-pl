---
title: Wydajność wielokanałowe protokołu SMB — Azure Files
description: Dowiedz się więcej o wydajności wielokanałowości SMB.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 4f4cd8189c9166ee08c1e4ccd800a1202d3b5893
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724820"
---
# <a name="smb-multichannel-performance"></a>Wydajność funkcji SMB Multichannel

Azure Files SMB wielokanałowe (wersja zapoznawcza) umożliwia klientowi SMB 3. x nawiązywanie wielu połączeń sieciowych z udziałami plików w warstwie Premium na koncie FileStorage. W protokole SMB 3,0 wprowadzono funkcję wielokanałowości SMB w systemie Windows Server 2012 i klientach z systemem Windows 8. Z tego względu każdy Azure Files klienta SMB 3. x obsługujący Wielokanałowość SMB może korzystać z funkcji udziałów plików platformy Azure w warstwie Premium. Nie ma dodatkowych kosztów włączania wielokanałowości SMB na koncie magazynu.

## <a name="benefits"></a>Korzyści

Azure Files SMB wielokanałowe umożliwia klientom korzystanie z wielu połączeń sieciowych zapewniających lepszą wydajność przy niższym obniżyć kosztów użytkowania. Zwiększona wydajność jest realizowana dzięki agregacji przepustowości dla wielu kart sieciowych i korzystaniu z obsługi skalowania po stronie odbierającej (RSS) dla kart sieciowych do dystrybucji obciążenia we/wy między wieloma procesorami.

- **Zwiększona przepływność**: wielokrotne połączenia umożliwiają transfer danych przez wiele ścieżek równolegle, a tym samym znacznie zużywają obciążenia, które korzystają z większych rozmiarów plików o większych rozmiarach we/wy, i wymagają dużej przepływności z jednej maszyny wirtualnej lub mniejszego zestawu maszyn wirtualnych. Niektóre z tych obciążeń obejmują Multimedia i rozrywkę do tworzenia i transkodowania zawartości, genomiki i analizy ryzyka usług finansowych.
- Wyższa liczba operacji we **/wy**: funkcja RSS nic umożliwia efektywną dystrybucję obciążenia wielu procesorów z wieloma połączeniami. Pozwala to osiągnąć wyższą skalę IOPS i efektywne wykorzystanie procesorów maszyn wirtualnych. Jest to przydatne w przypadku obciążeń o małych rozmiarach we/wy, takich jak aplikacje baz danych.
- **Odporność na uszkodzenia sieci**: wiele połączeń zmniejsza ryzyko zakłócenia, ponieważ klienci nie korzystają już z poszczególnych połączeń.
- **Automatyczna konfiguracja**: gdy funkcja SMB jest włączona na klientach i kontach magazynu, umożliwia dynamiczne odnajdywanie istniejących połączeń i w razie potrzeby może utworzyć dodatkowe ścieżki połączeń.
- **Optymalizacja kosztów**: obciążenia mogą osiągnąć wyższą skalę z jednej maszyny wirtualnej lub małego zestawu maszyn wirtualnych podczas łączenia z udziałami w warstwie Premium. Może to zmniejszyć całkowity koszt posiadania, zmniejszając liczbę maszyn wirtualnych potrzebnych do uruchomienia obciążenia i zarządzania nim.

Aby dowiedzieć się więcej na temat wielokanałowego protokołu SMB, zapoznaj się z [dokumentacją systemu Windows](/azure-stack/hci/manage/manage-smb-multichannel).

Ta funkcja zapewnia większe korzyści z wydajności dla aplikacji wielowątkowych, ale zazwyczaj nie pomaga aplikacjom jednowątkowym. Aby uzyskać więcej informacji, zobacz sekcję [porównanie wydajności](#performance-comparison) .

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Dostępność regionalna

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>Konfiguracja

Wielokanałowość SMB działa tylko wtedy, gdy ta funkcja jest włączona zarówno po stronie klienta (klienta), jak i po stronie usługi (konto usługi Azure Storage).

Na klientach z systemem Windows funkcja bezobsługowego protokołu SMB jest domyślnie włączona. Konfigurację można sprawdzić, uruchamiając następujące polecenie programu PowerShell: 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
Na koncie usługi Azure Storage należy włączyć Wielokanałowość protokołu SMB. Zobacz [Włączanie wielokanałowości SMB (wersja zapoznawcza)](storage-files-enable-smb-multichannel.md).

### <a name="disable-smb-multichannel"></a>Wyłącz Wielokanałowość protokołu SMB
W większości scenariuszy, szczególnie w przypadku obciążeń wielowątkowych, klienci powinni zapoznać się z lepszą wydajnością przy użyciu wielokanałowości SMB. Jednak niektóre konkretne scenariusze, takie jak obciążenia jednowątkowe lub do celów testowych, mogą chcieć wyłączyć Wielokanałowość protokołu SMB. Aby uzyskać więcej informacji, zobacz [porównanie wydajności](#performance-comparison) .

## <a name="verify-smb-multichannel-is-configured-correctly"></a>Sprawdź, czy funkcja wielokanałowe protokołu SMB została prawidłowo skonfigurowana

1. Utwórz udział plików w warstwie Premium lub Użyj istniejącego.
1. Upewnij się, że klient obsługuje wielokanałowe protokołu SMB (co najmniej jedna karta sieciowa ma włączone skalowanie po stronie odbierającej). Więcej informacji można znaleźć w [dokumentacji systemu Windows](/azure-stack/hci/manage/manage-smb-multichannel) .
1. Zainstaluj udział plików na komputerze klienckim.
1. Generuj obciążenie z aplikacją.
    Narzędzie do kopiowania, takie jak Robocopy/MT, lub dowolne narzędzie wydajności, takie jak narzędzia diskspd do plików do odczytu/zapisu, może generować obciążenie.
1. Otwórz program PowerShell jako administrator i użyj następującego polecenia: `Get-SmbMultichannelConnection |fl`
1. Poszukaj właściwości **MaxChannels** i **CurrentChannels**

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Zrzut ekranu przedstawiający wyniki Get-SMBMultichannelConnection." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>Porównanie wydajności

Dostępne są dwie kategorie wzorców obciążenia do odczytu/zapisu — pojedyncze wątki i wielowątkowość. Większość obciążeń używa wielu plików, ale mogą istnieć konkretne przypadki użycia, w których obciążenie działa z pojedynczym plikiem w udziale. Ta sekcja obejmuje różne przypadki użycia i wpływ na wydajność poszczególnych z nich. Ogólnie rzecz biorąc większość obciążeń jest wielowątkowa i dystrybuuje obciążenie na wiele plików, dzięki czemu powinny one mieć znaczny wpływ na wydajność przy użyciu wielokanałowości SMB.

- **Wielowątkowe/wielokrotne pliki**: w zależności od wzorca obciążenia powinno być widoczne znaczące zwiększenie wydajności w odniesieniu do odczytu i zapisu w systemie iOS przez wiele kanałów. Zyski z wydajności różnią się w zależności od liczby operacji wejścia/wyjścia na sekundę w zakresie operacji we/wy, przepływności i opóźnień. W przypadku tej kategorii Wielokanałowość protokołu SMB powinna być włączona w celu uzyskania najlepszej wydajności.
- **Wielowątkowy/pojedynczy plik**: w przypadku większości przypadków użycia w tej kategorii obciążenia mogą korzystać z włączonego wielokanałowości SMB, zwłaszcza jeśli obciążenie ma średni rozmiar we/wy > ~ 16 KB. Oto kilka przykładowych scenariuszy korzystających z funkcji wielokanałowe protokołu SMB — tworzenie kopii zapasowych i odzyskiwanie pojedynczego dużego pliku. Wyjątek, w którym można wyłączyć wielokanałowe protokołu SMB, jeśli obciążenie jest niewielkie w małych systemach IOs. W takim przypadku może wystąpić niewielka utrata wydajności wynosząca OK. 10%. W zależności od przypadku użycia należy rozważyć rozłożenie obciążenia między wiele plików lub wyłączenie funkcji. Szczegółowe informacje znajdują się w sekcji [Konfiguracja](#configuration) .
- **Jednowątkowe/wielokrotne pliki lub pojedynczy plik**: w przypadku większości równoległych obciążeń istnieje minimalny spadek wydajności spowodowany brakiem współbieżności, zazwyczaj istnieje niewielkie obniżenie wydajności w przypadku, gdy włączone jest wielokanałowe obsługa protokołu SMB. W takim przypadku idealnym rozwiązaniem jest wyłączenie wielokanałowości SMB z jednym wyjątkiem. Jeśli obciążenie jednowątkowe może rozłożyć obciążenie między wiele plików i używać ich na średnim większym rozmiarze we/wy (> ~ 16 KB), powinny być niewielkie korzyści z wydajności ze wielokanałowości SMB.

### <a name="performance-test-configuration"></a>Konfiguracja testu wydajnościowego

W przypadku wykresów w tym artykule użyto następującej konfiguracji: pojedynczej standardowej maszyny wirtualnej D32s v3 z jedną kartą sieciową obsługującą funkcję RSS z czterema kanałami. Ładowanie zostało wygenerowane przy użyciu diskspd.exe, wiele wątków z głębokością we/wy o wartości 10 i losowych IOs z różnymi rozmiarami we/wy.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i temp: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych|Oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="Zrzut ekranu pokazujący konfigurację testu wydajności." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>Odpowiednie — wątki/wiele plików ze wielokanałowe protokołu SMB

Załadowanie zostało wygenerowane dla 10 plików o różnych rozmiarach we/wy. Wyniki testu skalowania w górę wykazywały znaczne ulepszenia zarówno w wyniku operacji wejścia/wyjścia na sekundę, jak i wydajności z obsługą protokołu SMB. Poniższe diagramy przedstawiają wyniki:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="Diagram wydajności" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="Diagram wydajności przepływności." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- Na jednej karcie sieciowej, w przypadku operacji odczytu, został zaobserwowany wzrost wydajności, a w przypadku zapisów, zyski z 3 – 4x w zakresie zarówno IOPS, jak i przepływności.
- Wielokanałowe przepustowość protokołu SMB dozwolone operacje we/wy na sekundę, aby osiągnąć limity maszyn wirtualnych nawet z jedną kartą sieciową i limitem czterech kanałów.
- Ze względu na to, że ruch wychodzący (lub odczyt do magazynu) nie jest naliczany, przekroczenie rozmiaru opublikowanego przez maszynę wirtualną wynoszącym 16 000 MB/s (2 GiB na sekundę). Test osiągnął >2,7 GiB/s. Ruch przychodzący (lub zapis do magazynu) nadal podlega limitom maszyn wirtualnych.
- Rozproszenie obciążenia wielu plików, które są dozwolone w znaczących ulepszeniach.

Przykładowe polecenie, które zostało użyte w tym teście, to: 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>Wielowątkowe/jednowątkowe obciążenia plików z wielokanałowym protokołem SMB

Ładowanie zostało wygenerowane na podstawie pojedynczego pliku GiB 128. W przypadku włączenia funkcji połączenia wielobajtowego SMB test skalowania w górę z użyciem wielowątkowych lub pojedynczych plików pokazał ulepszenia w większości przypadków. Poniższe diagramy przedstawiają wyniki:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="Diagram wydajności operacji we/wy na sekundę." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="Diagram wydajności dotyczącej przepływności pojedynczego pliku." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- Na jednej karcie sieciowej o większym średnim rozmiarze we/wy (> ~ 16 KB) wprowadzono znaczne ulepszenia zarówno operacji odczytu, jak i zapisu.
- W przypadku mniejszych rozmiarów we/wy nastąpił niewielki wpływ ~ 10% na wydajność, gdy włączono Wielokanałowość SMB. Może to być skorygowane przez rozłożenie obciążenia na wiele plików lub wyłączenie funkcji.
- Wydajność jest nadal ograniczona przez  [limity pojedynczego pliku](storage-files-scale-targets.md#file-level-limits).

## <a name="optimizing-performance"></a>Optymalizacja wydajności

Poniższe porady mogą pomóc zoptymalizować wydajność:

- Upewnij się, że Twoje konto magazynu i Twój klient znajdują się w tym samym regionie świadczenia usługi Azure, aby zmniejszyć opóźnienie sieci.
- Korzystaj z aplikacji wielowątkowych i rozmieszczaj obciążenie między wieloma plikami.
- Korzyści wynikające z wydajności połączeń wielokanałowych SMB zwiększają liczbę plików, które dystrybuują obciążenie.
- Wydajność udziału w warstwie Premium jest ograniczona przez rozmiar udostępnianego udziału (operacje we/wy lub ruch wychodzący/ruch przychodzący) oraz limity pojedynczego pliku. Aby uzyskać szczegółowe informacje, zobacz [Opis udostępniania udziałów plików w warstwie Premium](understanding-billing.md#provisioned-billing).
- Maksymalna wydajność pojedynczego klienta maszyny wirtualnej jest nadal powiązana z limitami maszyn wirtualnych. Na przykład [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) może obsługiwać przepustowość maksymalną 16 000 MB/s (lub 2GBps), ruch wychodzący z maszyny wirtualnej (zapis do magazynu) jest mierzony, ruch przychodzący (odczyty z magazynu) nie jest. Wydajność udziału plików zależy od ograniczeń sieci maszyn, procesorów, dostępnej przepustowości sieci, rozmiarów operacji we/wy, równoległości, a także innych czynników.
- Wstępny test jest zwykle ciepły, odrzucać jego wyniki i powtarzać test.
- Jeśli wydajność jest ograniczona przez pojedynczego klienta, a obciążenie jest nadal poniżej limitów udziałów, można osiągnąć wyższą wydajność dzięki rozproszeniu obciążenia na wielu klientów.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>Relacja między liczbami operacji we/wy, przepływności i wielkościami wejścia/wyjścia

**Przepływność = rozmiar we/wy * IOPS**

Wyższe rozmiary we/wy zwiększają przepustowość i mają większe opóźnienia, co zmniejsza liczbę IOPS sieci. Mniejsza liczba operacji we/wy spowoduje zwiększenie liczby IOPS, ale powoduje obniżenie przepływności i opóźnień netto.

## <a name="next-steps"></a>Następne kroki

- [Włączanie wielokanałowości SMB na koncie FileStorage (wersja zapoznawcza)](storage-files-enable-smb-multichannel.md)
- Zapoznaj się z [dokumentacją systemu Windows](/azure-stack/hci/manage/manage-smb-multichannel) , aby dowiedzieć się więcej o wielokanałowości SMB.

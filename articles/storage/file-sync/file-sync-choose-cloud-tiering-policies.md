---
title: Wybierz Azure File Sync warstw w chmurze | Microsoft Docs
description: Szczegółowe informacje o tym, o czym należy pamiętać podczas wybierania Azure File Sync warstw w chmurze.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0bf41e1a847335a99b3e8f2e9ecbac504c3179e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797392"
---
# <a name="choose-cloud-tiering-policies"></a>Wybieranie zasad warstw w chmurze

Ten artykuł zawiera wskazówki dla użytkowników, którzy wybierają i dopasowywują swoje zasady warstw w chmurze. Przed przeczytaniem tego artykułu upewnij się, że rozumiesz sposób działania warstw w chmurze. Aby uzyskać podstawowe informacje na temat warstw w chmurze, zobacz Understand Azure File Sync cloud tiering (Informacje [na temat Azure File Sync w chmurze).](file-sync-cloud-tiering-overview.md) Aby uzyskać szczegółowe wyjaśnienie zasad warstw w chmurze z przykładami, zobacz Azure File Sync [dotyczące zasad warstw w chmurze.](file-sync-cloud-tiering-policy.md)

## <a name="limitations"></a>Ograniczenia
- Warstwy w chmurze nie są obsługiwane na woluminie systemu Windows.

- Nadal można włączyć obsługę warstw w chmurze, jeśli masz limit przydziału FSRM na poziomie woluminu. Po ustawieniu limitu przydziału FSRM interfejsy API zapytań o wolne miejsce, które są wywoływane automatycznie, zgłaszają ilość wolnego miejsca na woluminie zgodnie z ustawieniem limitu przydziału. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Minimalny rozmiar pliku do warstwy

W przypadku agentów w wersji 9 i nowszej minimalny rozmiar pliku dla pliku do warstwy zależy od rozmiaru klastra systemu plików. Minimalny rozmiar pliku kwalifikujący się do korzystania z warstw w chmurze jest obliczany przez 2 razy rozmiar klastra i co najmniej 8 KB. W poniższej tabeli przedstawiono minimalne rozmiary plików, które mogą być warstwowe, na podstawie rozmiaru klastra woluminów:

|Rozmiar klastra woluminów (w bajtach) |Pliki o takim lub większym rozmiarze mogą być warstwowe  |
|----------------------------|---------|
|4 KB lub mniej (4096)      | 8 KB    |
|8 KB (8192)                 | 16 KB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 KB   |
|64 KB (65536)    | 128 KB  |
|128 KB (131072) | 256 KB |
|256 KB (262144) | 512 KB |
|512 KB (524288) | 1 MB |
|1 MB (1048576) | 2 MB |
|2 MB (2097152) | 4 MB |

Klastry o rozmiarze do 2 MB są obsługiwane Azure File Sync agenta w wersji 12, ale w przypadku większych rozmiarów warstwy w chmurze nie działają.

Wszystkie systemy plików używane przez system Windows organizują dysk twardy na podstawie rozmiaru klastra (nazywanego również rozmiarem jednostki alokacji). Rozmiar klastra reprezentuje najmniejszą ilość miejsca na dysku, która może służyć do przechowywania pliku. Jeśli rozmiary plików nie mają nawet wielokrotności rozmiaru klastra, do przechowywania pliku należy użyć dodatkowego miejsca — aż do kolejnej wielokrotności rozmiaru klastra.

Azure File Sync jest obsługiwana na woluminach NTFS z systemem Windows Server 2012 R2 i nowsze. W poniższej tabeli opisano domyślne rozmiary klastrów podczas tworzenia nowego woluminu NTFS w systemie Windows Server 2019.

|Rozmiar woluminu    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MB–16 TB   | 4 KB                |
|16 TB – 32 TB   | 8 KB                |
|32 TB – 64 TB   | 16 KB               |
|64 TB – 128 TB  | 32 KB               |
|128 TB – 256 TB | 64 KB (wcześniej maks.) |
|256 TB – 512 TB| 128 KB              |
|512 TB – 1 PB  | 256 KB              |
|1 PB–2 PB    | 512 KB              |
|2 TB – 4 PB    | 1024 KB             |
|4 TB – 8 TB    | 2048 KB (maksymalny rozmiar)  |
|> 8 TB         | nie jest obsługiwana       |

Istnieje możliwość, że podczas tworzenia woluminu wolumin został ręcznie sformatowany z innym rozmiarem klastra. Jeśli wolumin pochodzi ze starszej wersji systemu Windows, domyślne rozmiary klastrów również mogą się różnić. [Ten artykuł zawiera więcej szczegółowych informacji na temat domyślnych rozmiarów klastrów.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Nawet jeśli wybierzesz rozmiar klastra mniejszy niż 4 KB, nadal obowiązuje limit 8 KB jako najmniejszy rozmiar pliku, który może być warstwowy. (Nawet jeśli technicznie rozmiar klastra 2x będzie równa mniej niż 8 KB).

Przyczyną bezwzględnego minimum jest sposób, w jaki system plików NTFS przechowuje bardzo małe pliki — pliki o rozmiarze od 1 KB do 4 KB. W zależności od innych parametrów woluminu istnieje możliwość, że małe pliki nie są w ogóle przechowywane w klastrze na dysku. Prawdopodobnie bardziej wydajne jest przechowywanie takich plików bezpośrednio w głównej tabeli plików woluminu lub "rekordu MFT". Punkt ponownej konfiguracji warstw w chmurze jest zawsze przechowywany na dysku i zajmuje dokładnie jeden klaster. Dzięki warstwie tak małych plików nie można zaoszczędzić miejsca. Skrajne przypadki mogą nawet skończyć się używaniem większej przestrzeni z włączoną obsługą warstw w chmurze. Aby temu zapewnić ochronę, najmniejszy rozmiar pliku, który będzie warstwowy w chmurze, to 8 KB w klastrze o rozmiarze 4 KB lub mniejszym. 

## <a name="selecting-your-initial-policies"></a>Wybieranie początkowych zasad

Ogólnie rzecz biorąc, po włączeniu obsługi warstw w chmurze w punkcie końcowym serwera należy utworzyć jeden lokalny dysk wirtualny dla każdego punktu końcowego pojedynczego serwera. Izolowanie punktu końcowego serwera ułatwia zrozumienie sposobu działania warstw w chmurze i odpowiednie dostosowanie zasad. Jednak Azure File Sync działa, nawet jeśli masz wiele punktów końcowych serwera na tym samym dysku, aby uzyskać szczegółowe informacje, zobacz sekcję Wiele punktów końcowych serwera [na woluminie](file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) lokalnym. Zalecamy również, aby po pierwszym włączeniu obsługi warstw w chmurze zachować wyłączone zasady dat i zasady ilości wolnego miejsca na poziomie od około 10% do 20%. W przypadku większości woluminów serwera plików najlepszym rozwiązaniem jest zwykle 20% wolnego miejsca.

Dla uproszczenia i zrozumienia, jak elementy będą warstwowe, zalecamy przede wszystkim dostosowanie zasad wolnego miejsca na woluminie i zachowanie zasad daty wyłączone, chyba że jest to konieczne. Zalecamy to, ponieważ większość klientów może wypełnić lokalną pamięć podręczną jak najwięcej gorących plików i posadowić resztę w chmurze. Jednak zasady daty mogą być korzystne, jeśli chcesz aktywnie wolne miejsce na dysku lokalnym i wiesz, że pliki w tym punkcie końcowym serwera, do których uzyskuje się dostęp po upływie liczby dni określonej w zasadach daty, nie muszą być przechowywane lokalnie. Ustawienie zasad daty pozwala na wykorzystanie cennej pojemności dysku lokalnego dla innych punktów końcowych na tym samym woluminie w celu buforowania większej ilości plików.

Po ustawieniu zasad należy monitorować ruch wychodzący i odpowiednio dostosować obie zasady. Zalecamy w szczególności zapoznanie się z rozmiarem **wycofywania warstw** w chmurze i rozmiarem odwoływania do warstw w chmurze według metryk **aplikacji** w Azure Monitor. Aby dowiedzieć się, jak monitorować ruch wychodzący, [zobacz Monitorowanie warstw w chmurze.](file-sync-monitor-cloud-tiering.md)

## <a name="adjusting-your-policies"></a>Dostosowywanie zasad

Jeśli liczba plików stale przywoływynych z platformy Azure jest większa niż chcesz, możesz mieć więcej gorących plików, niż masz miejsce na zapisanie ich na lokalnym woluminie serwera. Jeśli to możliwe, zwiększ rozmiar woluminu lokalnego i/lub zmniejsz wartość procentową zasad wolnego miejsca woluminu w małych przyrostach. Zmniejszenie procentu wolnego miejsca woluminu może również mieć negatywne konsekwencje. Wyższy poziom zmian w zestawie danych wymaga więcej wolnego miejsca — dla nowych plików i odwoływania "zimnych" plików. Przetwarzanie warstw rozpoczyna się z opóźnieniem do jednej godziny, a następnie wymaga czasu przetwarzania, dlatego zawsze należy mieć dużo wolnego miejsca na woluminie.

Utrzymywanie większej ilości danych lokalnych oznacza niższe koszty w przypadku odwoływania mniejszej liczby plików z platformy Azure, ale wymaga również większej ilości magazynu lokalnego, co jest kosztem własnym. 

Podczas dostosowywania zasad dotyczących wolnego miejsca na woluminie ilość danych, które należy zachować lokalnie, zależy od następujących czynników: przepustowość, wzorzec dostępu do zestawu danych i budżet. W przypadku połączenia o niskiej przepustowości możesz chcieć uzyskać więcej danych lokalnych, aby zapewnić minimalne opóźnienie dla użytkowników. W przeciwnym razie można go ustawić na podstawie współczynnika zmian w danym okresie. Jeśli na przykład wiesz, że 10% zestawu danych o rozmiarze 1 TB zmienia się lub jest aktywnie uzyskiwany dostęp każdego miesiąca, możesz zachować lokalną pojemność 100 GB, aby nie przypominać sobie często plików. Jeśli wolumin ma 2 TB, warto zachować 5% (lub 100 GB) woluminu lokalnego, co oznacza, że pozostałe 95% to procent wolnego miejsca woluminu. Należy jednak dodać bufor dla okresów większego współczynnika zmian — innymi słowy, zaczynając od większej wartości procentowej wolnego miejsca na woluminie, a następnie dostosowując go w razie potrzeby później.

## <a name="standard-operating-procedures"></a>Standardowe procedury operacyjne

- Podczas pierwszej migracji do usługi Azure Files za pośrednictwem Azure File Sync warstwy w chmurze zależą od początkowego przekazania
- Usługa Cloud Tiering sprawdza zgodność z zasadami ilości wolnego miejsca i dat co sześćdziesiąt minut
- Użycie przełącznika /LFSM w programie Robocopy podczas migrowania plików umożliwi synchronizację plików, a zastosowanie warstw w chmurze pozwoli na miejsce podczas początkowego przekazywania 
- Jeśli warstwa ma miejsce przed formularzem mapy cieplnej, pliki będą warstwowane według znacznika czasu ostatniej modyfikacji

## <a name="next-steps"></a>Następne kroki

* [Planowanie wdrażania usługi Azure File Sync](file-sync-planning.md)

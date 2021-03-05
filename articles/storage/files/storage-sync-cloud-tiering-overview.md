---
title: Informacje na temat obsługi warstw w chmurze Azure File Sync | Microsoft Docs
description: Omówienie obsługi warstw w chmurze, opcjonalnej funkcji Azure File Sync. Często używane pliki są buforowane lokalnie na serwerze; inne są w warstwach do Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9ca8df21e0221d364f75eaeb26f0b2961d7e0b08
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204469"
---
# <a name="cloud-tiering-overview"></a>Omówienie obsługi warstw w chmurze
Obsługa warstw w chmurze, opcjonalna funkcja Azure File Sync, zmniejsza ilość wymaganego magazynu lokalnego przy zachowaniu wydajności serwera plików lokalnych.

Po włączeniu tej funkcji są przechowywane tylko pliki (gorąca) na serwerze lokalnym. Rzadko używane pliki są podzielone na przestrzeń nazw (strukturę plików i folderów) oraz zawartość pliku. Przestrzeń nazw jest przechowywana lokalnie i zawartość pliku przechowywana w udziale plików platformy Azure w chmurze. 

Gdy użytkownik otwiera plik warstwowy, Azure File Sync bezproblemowo oddzwoni dane pliku z udziału plików na platformie Azure.

## <a name="how-cloud-tiering-works"></a>Jak działa Obsługa warstw w chmurze

### <a name="cloud-tiering-policies"></a>Zasady obsługi warstw w chmurze
Po włączeniu obsługi warstw w chmurze istnieją dwie zasady, które można ustawić, aby informować Azure File Sync, kiedy mają być dostępne chłodne pliki: **zasady wolnego miejsca na woluminie** i **zasady dotyczące daty**. 

#### <a name="volume-free-space-policy"></a>Zasady wolnego miejsca na woluminie
**Zasady wolnego miejsca na woluminie** informują Azure File Sync o warstwach chłodnych plików w chmurze, gdy na dysku lokalnym zostanie określona pewna ilość miejsca. 

Na przykład jeśli pojemność dysku lokalnego wynosi 200 GB i chcesz, aby pojemność dysku lokalnego była równa co najmniej 40 GB, należy ustawić zasadę wolnego miejsca na woluminie na 20%. Wolne miejsce na woluminie jest stosowane na poziomie woluminu, a nie na poziomie poszczególnych katalogów lub punktów końcowych serwera. 

Aby dowiedzieć się, jak zasady wolnego miejsca na woluminie wpływają na wstępnie pobrane pliki po dodaniu nowego punktu końcowego serwera (zobacz [zasady synchronizacji, które mają wpływ na obsługę warstw w chmurze](#sync-policies-that-affect-cloud-tiering)).

#### <a name="date-policy"></a>Zasady dotyczące daty
W przypadku **zasad daty** chłodne pliki są warstwami w chmurze, jeśli nie były dostępne (czyli są odczytywane lub zapisywane) przez x liczbę dni. Na przykład, jeśli zauważono, że pliki, które zostały utracone ponad 15 dni, są zwykle archiwizowane, należy ustawić zasady dotyczące dat na 15 dni. 

Aby uzyskać więcej przykładów współpracy między zasadami daty i ilością wolnego miejsca na woluminie, zobacz [wybieranie Azure File Sync zasad obsługi warstw w chmurze](storage-sync-choose-cloud-tiering-policies.md).

### <a name="windows-server-data-deduplication"></a>Deduplikacja danych systemu Windows Server
Funkcja deduplikacji danych jest obsługiwana na woluminach, na których włączono obsługę warstw w chmurze, począwszy od systemu Windows Server 2016. Aby uzyskać więcej informacji, zobacz [Planowanie wdrażania Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#data-deduplication).

### <a name="cloud-tiering-heatmap"></a>Obsługa warstw w chmurze mapę cieplną
Azure File Sync monitoruje dostęp do pliku (operacje odczytu i zapisu) w miarę upływu czasu i, w zależności od częstości i ostatniego dostępu, przypisuje wynik ciepła do każdego pliku. Używa tych wyników do tworzenia "mapę cieplną" przestrzeni nazw na każdym punkcie końcowym serwera. Ta mapę cieplną to lista wszystkich plików synchronizowanych w lokalizacji z włączonym obsługą warstw w chmurze, uporządkowaną według ich wyniku ciepła. Pliki, które były ostatnio otwierane, są uznawane za gorące, podczas gdy pliki, które zostały stanowią jedynie ułamek, i nie były przez jakiś czas dostępne, są uważane za chłodną. 

Aby określić względną pozycję pojedynczego pliku w tym mapę cieplną, system używa maksymalnej liczby sygnatur czasowych w następującej kolejności: MAX (godzina ostatniego dostępu, godzina ostatniej modyfikacji, godzina utworzenia). 

Zazwyczaj czas ostatniego dostępu jest śledzony i dostępny. Jednak po utworzeniu nowego punktu końcowego serwera z włączoną obsługą obsługi warstw w chmurze nie przeszedł wystarczająco dużo czasu, aby obserwować dostęp do pliku. Jeśli nie ma prawidłowego czasu ostatniego dostępu, zamiast tego zostanie użyty czas ostatniej modyfikacji, aby oszacować pozycję względną w mapę cieplną.  

Zasady daty działają w taki sam sposób. W przypadku braku czasu ostatniego dostępu zasady daty będą działać od czasu ostatniej modyfikacji. Jeśli ta wartość jest niedostępna, nastąpi powrót do czasu utworzenia pliku. W miarę upływu czasu system zaobserwuje więcej i więcej żądań dostępu do plików i automatycznie rozpocznie korzystanie z samoobsługowego śledzenia czasu ostatniego dostępu.

> [!Note]
> Obsługa warstw w chmurze nie zależy od funkcji NTFS do śledzenia czasu ostatniego dostępu. Ta funkcja systemu plików NTFS jest domyślnie wyłączona i ze względu na wydajność nie zaleca się ręcznego włączania tej funkcji. Usługa Cloud warstws śledzi czas ostatniego dostępu osobno.

### <a name="sync-policies-that-affect-cloud-tiering"></a>Zasady synchronizacji, które mają wpływ na obsługę warstw w chmurze

W programie Azure File Sync Agent w wersji 11 istnieją dwa dodatkowe zasady Azure File Sync, które mają wpływ na obsługę warstw w chmurze: **pobieranie początkowe** i **aktywne odwoływanie**.

#### <a name="initial-download"></a>Pobieranie początkowe

Gdy serwer nawiązuje połączenie z udziałem plików platformy Azure przy użyciu plików w nim, można teraz zdecydować, w jaki sposób serwer ma początkowo pobierać dane udziału plików. Po włączeniu obsługi warstw w chmurze dostępne są dwie opcje. 

![Zrzut ekranu przedstawiający początkowe pobieranie po włączeniu obsługi warstw w chmurze](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

Pierwsza opcja polega na przywróceniu przestrzeni nazw najpierw, a następnie przywróceniu zawartości pliku przez ostatnią sygnaturę czasową do momentu osiągnięcia pojemności dysku lokalnego. Jeśli masz wystarczającą ilość miejsca na dysku i wiesz, że pliki, które są ostatnio modyfikowane, powinny być buforowane lokalnie, ta opcja jest idealnym rozwiązaniem. Pliki, które nie mogą być przechowywane lokalnie z powodu braku miejsca na dysku, zostaną warstwowe.        

Druga opcja polega na początkowym przywróceniu tylko przestrzeni nazw i odwoływaniu zawartości pliku tylko wtedy, gdy jest on dostępny. Ta opcja jest przydatna, jeśli chcesz zminimalizować pojemność używaną na dysku lokalnym i zdecydować, które pliki mają być buforowane lokalnie. Wszystkie pliki zostaną uruchomione jako pliki warstwowe z tą opcją.

![Zrzut ekranu przedstawiający początkowe pobieranie w przypadku wyłączenia obsługi warstw w chmurze](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

Gdy obsługa warstw w chmurze jest wyłączona, masz trzecią opcję, która polega na tym, że wszystkie pliki warstwowe nie są jednocześnie dostępne. W takiej sytuacji pliki będą wyświetlane tylko na serwerze, gdy zostaną w pełni pobrane. Jeśli masz aplikacje wymagające obecności pełnych plików i nie można tolerować plików warstwowych w swojej przestrzeni nazw, jest to idealne rozwiązanie.      

#### <a name="proactive-recalling"></a>Aktywne odwoływanie

Po utworzeniu lub zmodyfikowaniu pliku można aktywnie odwołać plik do określonych serwerów. Dzięki temu nowy lub zmodyfikowany plik jest łatwo dostępny do użycia na każdym określonym serwerze. 

Na przykład globalnie dystrybuowana firma ma biura oddziałów w Stanach Zjednoczonych i w Indiach. W pracowniku przetwarzającym informacje o godzinie (USA) Utwórz nowy folder i nowe pliki dla zupełnie nowego projektu i pracuj codziennie. Azure File Sync zsynchronizuje foldery i pliki z udziałem plików platformy Azure (punkt końcowy w chmurze). Pracownicy przetwarzający informacje w Indiach będą nadal pracować nad projektem w ich strefach czasowych. Po nadejściu rano, lokalny Azure File Sync włączony serwer w Indiach musi mieć dostęp do tych nowych plików lokalnie, dzięki czemu zespół Indii może efektywnie pracować z lokalną pamięcią podręczną. Włączenie tego trybu zapobiega wolniejszemu dostępowi do pliku z powodu odwołania na żądanie i umożliwia serwerowi aktywne odwoływanie plików zaraz po ich zmianie lub utworzeniu w udziale plików platformy Azure.

Jeśli pliki, które są ponownie wywoływane na serwerze, nie są w rzeczywistości wymagane lokalnie, niepotrzebne odwołanie może zwiększyć ruch wychodzący i koszty. W związku z tym Włącz aktywne ponowne wywoływanie tylko wtedy, gdy wiadomo, że w pamięci podręcznej serwera z najnowszymi zmianami w chmurze będzie miało pozytywny wpływ na użytkowników lub aplikacje używające plików na tym serwerze. 

Włączenie aktywnego ponownego wywoływania może także spowodować zwiększenie użycia przepustowości na serwerze i może spowodować, że inna stosunkowo nowa zawartość na serwerze lokalnym zostanie zastosowana w sposób agresywny ze względu na wzrost liczby plików. Z kolei może to prowadzić do większej liczby wywołań, jeśli warstwowe pliki są uważane za gorącą przez serwery. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Obraz przedstawiający zachowanie pobierania udziałów plików platformy Azure dla aktualnie obowiązującego punktu końcowego serwera i przycisk umożliwiający otwarcie menu umożliwiającego jego zmianę.":::

Aby uzyskać więcej informacji na temat aktywnego odwoływania, zobacz [wdrażanie Azure File Sync](storage-sync-files-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Zachowanie w warstwach i buforowanych lokalnie plików

Obsługa warstw w chmurze polega na rozdzieleniu między przestrzenią nazw (hierarchią plików i folderów oraz właściwościami plików) i zawartością pliku. 

#### <a name="tiered-file"></a>Plik warstwowy

W przypadku plików warstwowych rozmiar na dysku wynosi zero, ponieważ sama zawartość pliku nie jest przechowywana lokalnie. Gdy plik jest warstwowy, filtr systemu plików Azure File Sync (StorageSync.sys) zastępuje plik lokalnie wskaźnikiem (punkt ponownej analizy). Punkt ponownej analizy reprezentuje adres URL pliku w udziale plików platformy Azure. Plik warstwowy ma zarówno atrybut "offline", jak i atrybut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS ustawiony w systemie plików NTFS, aby aplikacje innych firm mogły bezpiecznie identyfikować pliki warstwowe.   

![Zrzut ekranu przedstawiający właściwości pliku, gdy jest on warstwą tylko przestrzeni nazw.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>Plik buforowany lokalnie

Z drugiej strony, dla pliku przechowywanego na lokalnym serwerze plików, rozmiar na dysku jest równy rozmiarowi logicznemu pliku, ponieważ cały plik (atrybuty plików i zawartość plików) jest przechowywany lokalnie.     

![Zrzut ekranu przedstawiający właściwości pliku, gdy nie jest on warstwowy-przestrzeń nazw + zawartość pliku.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

Istnieje również możliwość, że plik jest częściowo warstwowy (lub częściowo odwołujący). W pliku częściowo warstwowego część pliku znajduje się na dysku. Taka sytuacja może wystąpić, gdy pliki są częściowo odczytywane przez aplikacje, takie jak odtwarzacze multimedialne lub narzędzia zip, które obsługują przesyłanie strumieniowe plików. Azure File Sync jest inteligentny i ponownie wywołuje tylko żądane informacje z połączonego udziału plików platformy Azure.

> [!NOTE]
> Rozmiar reprezentuje rozmiar logiczny pliku. Rozmiar na dysku reprezentuje rozmiar fizyczny strumienia pliku przechowywanego na dysku.

## <a name="next-steps"></a>Następne kroki
* [Wybierz Azure File Sync zasad obsługi warstw w chmurze](storage-sync-choose-cloud-tiering-policies.md)
* [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)


---
title: Understand Azure File Sync cloud tiering | (Opis Azure File Sync warstw w chmurze) Microsoft Docs
description: Opis warstw w chmurze, opcjonalnej Azure File Sync zarządzania. Często używane pliki są buforowane lokalnie na serwerze; Inne są warstwowe w Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1f58aa4e2aa4637dfb9fc8b29c52209975e3e367
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797384"
---
# <a name="cloud-tiering-overview"></a>Omówienie warstw w chmurze
Warstwy w chmurze, opcjonalna funkcja Azure File Sync, zmniejszają ilość magazynu lokalnego wymaganego przy zachowaniu wydajności lokalnego serwera plików.

Po włączeniu ta funkcja przechowuje tylko często używane (gorące) pliki na serwerze lokalnym. Rzadko używane pliki (chłodne) są dzielone na przestrzeń nazw (strukturę plików i folderów) i zawartość plików. Przestrzeń nazw jest przechowywana lokalnie, a zawartość plików jest przechowywana w udziałach plików platformy Azure w chmurze. 

Gdy użytkownik otwiera plik warstwowy, Azure File Sync bezproblemowo odwołuje dane plików z udziału plików na platformie Azure.

## <a name="how-cloud-tiering-works"></a>Jak działa warstwowanie w chmurze

### <a name="cloud-tiering-policies"></a>Zasady warstw w chmurze
Po włączeniu obsługi warstw w chmurze istnieją dwie zasady, które można ustawić w  celu poinformowania użytkowników Azure File Sync kiedy należy warstwie chłodnych plików: zasady wolnego miejsca na woluminie i **zasady daty**. 

#### <a name="volume-free-space-policy"></a>Zasady dotyczące wolnego miejsca na woluminie
Zasady **dotyczące wolnego miejsca na** woluminie Azure File Sync warstwy chłodnych plików w chmurze, gdy na dysku lokalnym jest zajęte określone miejsce. 

Jeśli na przykład pojemność dysku lokalnego wynosi 200 GB i chcesz, aby co najmniej 40 GB pojemności dysku lokalnego zawsze pozostawało wolne, należy ustawić zasady ilości wolnego miejsca na 20%. Wolne miejsce na woluminie ma zastosowanie na poziomie woluminu, a nie na poziomie poszczególnych katalogów lub punktów końcowych serwera. 

Aby dowiedzieć się, jak zasady dotyczące wolnego miejsca na woluminie wpływają na pliki początkowo pobierane po dodaniu nowego punktu końcowego serwera( zobacz sekcję [Zasady](#sync-policies-that-affect-cloud-tiering)synchronizacji wpływające na warstwy w chmurze).

#### <a name="date-policy"></a>Zasady dotyczące daty
W przypadku **zasad daty** pliki chłodne są warstwowe w chmurze, jeśli nie były używane (czyli odczytywane lub zapisywane) przez x liczbę dni. Jeśli na przykład zauważysz, że pliki, które przeszły ponad 15 dni bez dostępu, są zazwyczaj plikami archiwalnymi, należy ustawić zasady dat na 15 dni. 

Aby uzyskać więcej przykładów na temat współpracy zasad daty i zasad wolnego miejsca na woluminie, zobacz Choose Azure File Sync cloud tiering policies (Wybieranie zasad warstw w [chmurze).](file-sync-choose-cloud-tiering-policies.md)

### <a name="windows-server-data-deduplication"></a>Deduplikacja danych systemu Windows Server
Deduplikacja danych jest obsługiwana na woluminach z włączoną obsługą warstw w chmurze, począwszy od systemu Windows Server 2016. Aby uzyskać więcej informacji, zobacz Planning for an Azure File Sync deployment (Planowanie [wdrożenia Azure File Sync wdrożenia).](file-sync-planning.md#data-deduplication)

### <a name="cloud-tiering-heatmap"></a>Mapa cieplna warstw w chmurze
Azure File Sync monitoruje dostęp do plików (operacje odczytu i zapisu) w czasie i, w zależności od tego, jak często i niedawno jest dostęp, przypisuje ocenę cieplną do każdego pliku. Te wyniki są używane do tworzenia "mapy cieplnej" przestrzeni nazw w każdym punkcie końcowym serwera. Ta mapa cieplna to lista wszystkich plików synchronizacji w lokalizacji z włączoną obsługą warstw w chmurze, uporządkowana według ich oceny cieplnej. Często używane pliki, które były ostatnio otwierane, są uznawane za gorące, a pliki, które były bezdotykowo dotykane i do których nie uzyskiwano do nich dostępu od pewnego czasu, są uznawane za chłodne. 

Aby określić względną pozycję pojedynczego pliku na tej mapie cieplnej, system używa maksymalnego z jego sygnatur czasowych w następującej kolejności: MAX(Czas ostatniego dostępu, Czas ostatniej modyfikacji, Czas utworzenia). 

Zazwyczaj czas ostatniego dostępu jest śledzony i dostępny. Jednak po utworzeniu nowego punktu końcowego serwera z włączoną obsługą warstw w chmurze nie minęło wystarczająco dużo czasu, aby obserwować dostęp do plików. Jeśli nie ma prawidłowego czasu ostatniego dostępu, zamiast tego jest używany czas ostatniej modyfikacji do oceny względnej pozycji na mapie cieplnej.  

Zasady daty są takie same. Bez czasu ostatniego dostępu zasady daty będą działać na czas ostatniej modyfikacji. Jeśli jest ona niedostępna, wróci do czasu utworzenia pliku. Z czasem system będzie obserwować coraz więcej żądań dostępu do plików i automatycznie zacznie używać samodzielnie śledzony czas ostatniego dostępu.

> [!Note]
> Warstwy w chmurze nie zależą od funkcji systemu plików NTFS do śledzenia czasu ostatniego dostępu. Ta funkcja ntfs jest domyślnie wyłączona i ze względu na zagadnienia dotyczące wydajności nie zaleca się ręcznego włączania tej funkcji. Warstwy w chmurze oddzielnie śledzi czas ostatniego dostępu.

### <a name="sync-policies-that-affect-cloud-tiering"></a>Zasady synchronizacji, które mają wpływ na warstwy w chmurze

W Azure File Sync agenta w wersji 11 istnieją dwie dodatkowe zasady Azure File Sync, które można ustawić, które mają wpływ na warstwy w **chmurze:** pobieranie początkowe i proaktywne **odwoływanie.**

#### <a name="initial-download"></a>Pobieranie początkowe

Gdy serwer łączy się z udziałem plików platformy Azure z plikami, możesz teraz zdecydować, jak serwer ma początkowo pobierać dane udziału plików. Po włączeniu obsługi warstw w chmurze dostępne są dwie opcje. 

![Zrzut ekranu przedstawiający początkowe pobieranie po włączeniu obsługi warstw w chmurze](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

Pierwszą opcją jest najpierw przywołanie przestrzeni nazw, a następnie przywołanie zawartości pliku za pomocą znacznika czasu ostatniej modyfikacji do momentu, gdy zostanie osiągnięta pojemność dysku lokalnego. Jeśli masz wystarczającą ilość miejsca na dysku i wiesz, że ostatnio zmodyfikowane pliki powinny być buforowane lokalnie, ta opcja jest idealna. Pliki, których nie można przechowywać lokalnie z powodu braku miejsca na dysku, będą warstwowe.        

Druga opcja polega na początkowym przywołyniu tylko przestrzeni nazw i przywołyniu zawartości pliku tylko wtedy, gdy dostęp jest uzyskiwany. Ta opcja jest najlepsza, jeśli chcesz zminimalizować pojemność używaną na dysku lokalnym i chcesz, aby użytkownicy decydowali, które pliki mają być buforowane lokalnie. Wszystkie pliki będą zaczynać się od plików warstwowych z tą opcją.

![Zrzut ekranu przedstawiający początkowe pobieranie po wyłączeniu warstw w chmurze](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

Po wyłączeniu warstw w chmurze dostępna jest trzecia opcja, która pozwala uniknąć plików warstwowych. W takiej sytuacji pliki będą wyświetlane na serwerze tylko po ich pełnym pobraniu. Jeśli masz aplikacje, które wymagają, aby pełne pliki były obecne i nie tolerowały plików warstwowych w przestrzeni nazw, jest to idealne rozwiązanie.      

#### <a name="proactive-recalling"></a>Aktywne przywoływanie

Po utworzeniu lub zmodyfikowaniu pliku można aktywnie odwołać plik do serwerów, które określisz. Dzięki temu nowy lub zmodyfikowany plik będzie łatwo dostępny do użycia na każdym określonym serwerze. 

Na przykład globalnie dystrybuowana firma ma oddziały w USA i Indiach. Rano (czas w USA) pracownicy tworzący informacje tworzą nowy folder i nowe pliki dla zupełnie nowego projektu i pracują nad nim przez cały dzień. Azure File Sync synchronizuje foldery i pliki z udziałem plików platformy Azure (punktem końcowym w chmurze). Pracownicy z informacjami w Indiach będą kontynuować pracę nad projektem w swojej strefie czasowej. Po przybyciu rano lokalny serwer z włączoną obsługą usługi Azure File Sync w Indiach musi mieć te nowe pliki dostępne lokalnie, aby zespół Indie wydajnie pracować z lokalną pamięcią podręczną. Włączenie tego trybu zapobiega wolniejszemu dostępowi do początkowego pliku z powodu odwołania na żądanie i umożliwia serwerowi aktywne odwoływanie plików natychmiast po ich zmianie lub utworzeniu w udziałach plików platformy Azure.

Jeśli pliki przywołyne na serwerze nie są faktycznie potrzebne lokalnie, niepotrzebne odwoływanie może zwiększyć ruch wychodzący i koszty. W związku z tym włącz aktywne odwoływanie się tylko wtedy, gdy wiadomo, że wstępne wypełnienie pamięci podręcznej serwera najnowszymi zmianami z chmury będzie mieć pozytywny wpływ na użytkowników lub aplikacje używające plików na tym serwerze. 

Włączenie aktywnego odwoływania może również spowodować zwiększone użycie przepustowości na serwerze i spowodować agresywną warstwową warstwę innej stosunkowo nowej zawartości na serwerze lokalnym ze względu na wzrost liczby odwoływowanych plików. Z kolei może to prowadzić do większej liczby odwoływek, jeśli pliki, które są warstwowe, są uznawane za gorące przez serwery. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Obraz przedstawiający zachowanie pobierania udziału plików platformy Azure dla punktu końcowego serwera, który jest obecnie w użyciu, oraz przycisk umożliwiający otwarcie menu, które umożliwia jego zmianę.":::

Aby uzyskać więcej informacji na temat aktywnego odwoływania, zobacz [Wdrażanie Azure File Sync](file-sync-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Zachowanie plików warstwowych i lokalnie buforowanych

Warstwy w chmurze to rozdzielenie przestrzeni nazw (hierarchii plików i folderów, a także właściwości plików) i zawartości pliku. 

#### <a name="tiered-file"></a>Plik warstwowy

W przypadku plików warstwowych rozmiar dysku wynosi zero, ponieważ sama zawartość pliku nie jest przechowywana lokalnie. Gdy plik jest warstwowy, Azure File Sync systemu plików (StorageSync.sys) zastępuje plik lokalnie wskaźnikiem (punkt ponownej kontroli). Punkt ponownej wersji reprezentuje adres URL pliku w udziałach plików platformy Azure. Plik warstwowy ma zarówno atrybut "offline", jak i atrybut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS ustawiony w systemie plików NTFS, dzięki czemu aplikacje innych firm mogą bezpiecznie identyfikować pliki warstwowe.   

![Zrzut ekranu przedstawiający właściwości pliku, gdy jest on warstwowy — tylko przestrzeń nazw.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>Lokalnie buforowany plik

Z drugiej strony w przypadku pliku przechowywanego na lokalnym serwerze plików rozmiar na dysku jest około równy rozmiarowi logicznemu pliku, ponieważ cały plik (atrybuty pliku i zawartość pliku) jest przechowywany lokalnie.     

![Zrzut ekranu przedstawiający właściwości pliku, gdy nie jest on warstwowy — przestrzeń nazw i zawartość pliku.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

Plik może być również częściowo warstwowy (lub częściowo odwołany). W pliku częściowo warstwowym część pliku znajduje się na dysku. Może się to zdarzyć, gdy pliki są częściowo odczytywane przez aplikacje, takie jak odtwarzacze multimedialne lub narzędzia zip, które obsługują dostęp do plików za pomocą przesyłania strumieniowego. Azure File Sync inteligentne i przywołuje tylko żądane informacje z połączonego udziału plików platformy Azure.

> [!NOTE]
> Rozmiar reprezentuje rozmiar logiczny pliku. Rozmiar na dysku reprezentuje rozmiar fizyczny strumienia plików przechowywanego na dysku.

## <a name="next-steps"></a>Następne kroki

* [Wybieranie Azure File Sync warstw w chmurze](file-sync-choose-cloud-tiering-policies.md)
* [Planowanie wdrażania usługi Azure File Sync](file-sync-planning.md)
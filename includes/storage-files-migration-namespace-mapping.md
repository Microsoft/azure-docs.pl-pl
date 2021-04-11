---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a8dd83a9584d218d56c7350d6d634bfe4610b790
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075611"
---
W tym kroku oceniasz, ile jest potrzebnych udziałów plików platformy Azure. Pojedyncze wystąpienie serwera systemu Windows (lub klaster) może synchronizować do 30 udziałów plików platformy Azure.

Być może masz więcej folderów na woluminach, które są obecnie udostępniane lokalnie jako udziały SMB dla użytkowników i aplikacji. Najprostszym sposobem na zdjęcie tego scenariusza jest Envision udziału lokalnego, który mapuje 1:1 do udziału plików platformy Azure. Jeśli masz małą ilość poniżej 30 dla pojedynczego wystąpienia systemu Windows Server, zalecane jest mapowanie 1:1.

Jeśli masz więcej udziałów niż 30, często nie trzeba mapować lokalnego udziału 1:1 do udziału plików platformy Azure. Należy wziąć pod uwagę następujące opcje.

#### <a name="share-grouping"></a>Grupowanie udziałów

Na przykład jeśli Dział kadr (kadr) ma łącznie 15 udziałów, można rozważyć zaprzechowywanie wszystkich danych KADRy w pojedynczym udziale plików platformy Azure. Przechowywanie wielu udziałów lokalnych w jednym udziale plików platformy Azure nie uniemożliwia tworzenia zwykłych 15 udziałów SMB w lokalnym wystąpieniu systemu Windows Server. Oznacza to, że można organizować foldery główne tych 15 udziałów jako podfoldery w ramach wspólnego folderu. Następnie zsynchronizuj ten wspólny folder z udziałem plików platformy Azure. W ten sposób tylko jeden udział plików platformy Azure w chmurze jest wymagany dla tej grupy udziałów lokalnych.

#### <a name="volume-sync"></a>Synchronizacja woluminu

Azure File Sync obsługuje synchronizowanie katalogu głównego woluminu z udziałem plików platformy Azure. W przypadku synchronizacji katalogu głównego woluminów wszystkie podfoldery i pliki będą przechodzić do tego samego udziału plików platformy Azure.

Synchronizowanie katalogu głównego woluminu nie zawsze jest najlepszą odpowiedzią. Synchronizacja wielu lokalizacji ma zalety. Na przykład takie rozwiązanie pomaga zachować liczbę elementów mniejszą dla zakresu synchronizacji. Podczas testowania udziałów plików platformy Azure i Azure File Sync z 100 000 000 elementami (plikami i folderami) na udział najlepszym rozwiązaniem jest próba zachowania numeru poniżej 20 000 000 lub 30 000 000 w jednym udziale. Konfigurowanie Azure File Sync o mniejszej liczbie elementów nie jest przydatne w przypadku synchronizacji plików. Mniejsza liczba elementów korzysta również z następujących scenariuszy:

* Początkowe skanowanie zawartości w chmurze może zakończyć się szybciej, co z kolei zmniejsza czas oczekiwania na pojawienie się przestrzeni nazw na serwerze z włączonym Azure File Sync.
* Przywracanie po stronie chmury z migawki udziału plików platformy Azure będzie szybsze.
* Odzyskiwanie awaryjne serwera lokalnego może znacznie przyspieszyć.
* Zmiany wprowadzone bezpośrednio w udziale plików platformy Azure (synchronizację zewnętrzną) mogą być wykrywane i synchronizowane szybciej.

> [!TIP]
> Jeśli nie masz pewności, ile plików i folderów masz, zapoznaj się z narzędziem TreeSize w programie dżem Software GmbH.

#### <a name="a-structured-approach-to-a-deployment-map"></a>Podejście strukturalne do mapy wdrożenia

Przed wdrożeniem magazynu w chmurze w późniejszym etapie należy utworzyć mapę między folderami lokalnymi i udziałami plików platformy Azure. To mapowanie będzie informować o tym, ile zasobów *grupy synchronizacji* Azure File Sync, które mają zostać zainicjowane. Grupa synchronizacji łączy udział plików platformy Azure i folder na serwerze, a także nawiązuje połączenie synchronizacji.

Aby podjąć decyzję o liczbie potrzebnych udziałów plików platformy Azure, zapoznaj się z następującymi ograniczeniami i najlepszymi rozwiązaniami. Wykonanie tej czynności pomoże zoptymalizować mapę.

* Serwer z zainstalowanym agentem Azure File Sync może być zsynchronizowany z maksymalnie 30 udziałami plików platformy Azure.
* Udział plików platformy Azure jest wdrażany w ramach konta magazynu. Powoduje to, że konto magazynu jest celem skalowania dla numerów wydajności, takich jak operacje we/wy i przepływność.

  Jeden standardowy udział plików platformy Azure może teoretycznie zaszeregować maksymalną wydajność, jaką może dostarczyć konto magazynu. Umieszczenie wielu udziałów na jednym koncie magazynu oznacza, że tworzysz udostępnioną pulę operacji we/wy na sekundę dla tych udziałów. Jeśli planujesz dołączenie Azure File Sync do tych udziałów plików, zgrupowanie kilku udziałów plików platformy Azure na to samo konto magazynu nie spowoduje problemu. Przejrzyj cele wydajności udziału plików platformy Azure, aby uzyskać dokładniejszy wgląd w odpowiednie metryki, które należy wziąć pod uwagę. Te ograniczenia nie dotyczą usługi Premium Storage, w której wydajność jest jawnie obsługiwana i gwarantowana dla każdego udziału.

  Jeśli planujesz podnoszenie poziomu aplikacji na platformę Azure, która będzie korzystać z natywnego udziału plików platformy Azure, konieczne może być zwiększenie wydajności udziału plików platformy Azure. Jeśli tego rodzaju użycie jest możliwe, nawet w przyszłości, najlepiej utworzyć pojedynczy standardowy udział plików platformy Azure na własnym koncie magazynu.
* Istnieje limit 250 kont magazynu na subskrypcję na region platformy Azure.

> [!TIP]
> Z tych informacji często zdarza się, aby zgrupować wiele folderów najwyższego poziomu na woluminach do wspólnego, nowego katalogu głównego. Następnie zsynchronizujesz ten nowy katalog główny i wszystkie foldery, które zostały pogrupowane, do pojedynczego udziału plików platformy Azure. Ta technika pozwala zachować limit 30 synchronizacji udziałów plików platformy Azure na serwer.
>
> Takie grupowanie w ramach wspólnego elementu głównego nie ma wpływu na dostęp do danych. Listy ACL pozostają w stanie takim, w jakim się znajdują. Wystarczy dostosować wszystkie ścieżki udziałów (na przykład udziały SMB lub NFS), które mogą znajdować się w lokalnych folderach serwerowych, które zostały teraz zmienione do wspólnego katalogu głównego. Nic nie zmienia.

> [!IMPORTANT]
> Najważniejszym wektorem skali dla Azure File Sync jest liczba elementów (plików i folderów), które muszą być synchronizowane. Przejrzyj [tarcze skalowania Azure File Sync](../articles/storage/files/storage-files-scale-targets.md#azure-file-sync-scale-targets) , aby uzyskać więcej szczegółów.

Najlepszym rozwiązaniem jest utrzymywanie liczby elementów na niski zakres synchronizacji. Jest to istotny czynnik, który należy wziąć pod uwagę w mapowaniu folderów do udziałów plików platformy Azure. Azure File Sync jest testowany z 100 000 000 elementów (pliki i foldery) na udział. Często jednak najlepiej jest zachować liczbę elementów poniżej 20 000 000 lub 30 000 000 w jednym udziale. Podziel przestrzeń nazw na wiele udziałów, jeśli zaczniesz przekroczyć te liczby. Można nadal grupować wiele udziałów lokalnych w ten sam udział plików platformy Azure, jeśli pozostanie mniej niż te liczby. To rozwiązanie zapewni Ci miejsce na rozwój.

W danej sytuacji istnieje możliwość, że zestaw folderów można logicznie synchronizować z tym samym udziałem plików platformy Azure (przy użyciu nowego, wspólnego podejścia do folderu głównego wymienionego wcześniej). Jednak nadal warto ponownie zgrupować foldery, aby były synchronizowane z dwoma, a nie jednym udziałem plików platformy Azure. Tego podejścia można użyć, aby zachować liczbę plików i folderów na udziale plików, które są zrównoważone na serwerze. Można również podzielić udziały lokalne i synchronizować je na więcej serwerów lokalnych, co umożliwia synchronizację z 30 większym udziałem plików platformy Azure na dodatkowy serwer.

#### <a name="create-a-mapping-table"></a>Tworzenie tabeli mapowania

:::row:::
    :::column:::
        [![Przykład tabeli mapowania. Pobierz następujący plik do środowiska i użyj zawartości tego obrazu.](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Aby określić liczbę potrzebnych udziałów plików platformy Azure, a które części istniejących danych zostaną zakończone w tym udziale plików platformy Azure, użyj kombinacji poprzednich koncepcji.
        
        Utwórz tabelę, która rejestruje Twoje przemyślenia, aby można było do niej odwołać się w razie potrzeby. Organizacja jest ważna, ponieważ można łatwo stracić szczegóły planu mapowania w przypadku jednoczesnego udostępniania wielu zasobów platformy Azure. Aby ułatwić utworzenie kompletnego mapowania, możesz pobrać plik programu Microsoft Excel jako szablon.

[//]: # (KOD HTML jest wyświetlany jako jedyny sposób, aby wykonać Dodawanie zagnieżdżonej tabeli dwukolumnowej z funkcją analizy obrazów roboczych i tekstem/hiperłączem w tym samym wierszu.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Pobierz szablon mapowania przestrzeni nazw.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::

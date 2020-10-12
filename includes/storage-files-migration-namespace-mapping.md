---
title: Mapowanie struktury folderów do topologii Azure File Sync
description: Mapowanie istniejącej struktury plików i folderów do udziałów plików platformy Azure do użycia z Azure File Sync. Wspólny blok tekstu współużytkowany w dokumentach migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 16b9342f0374377349f338db7ce5c8389c77ea18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87425167"
---
W tym kroku oceniasz, ile jest potrzebnych udziałów plików platformy Azure. Pojedyncze wystąpienie serwera systemu Windows (lub klaster) może synchronizować do 30 udziałów plików platformy Azure.

Możesz mieć więcej folderów na woluminach, które obecnie są udostępniane lokalnie jako udziały SMB dla użytkowników i aplikacji. Najprostszym sposobem jest Envision udziału lokalnego, który mapuje 1:1 na udział plików platformy Azure. Jeśli masz małą ilość poniżej 30 dla jednego wystąpienia systemu Windows Server, zalecamy mapowanie 1:1.

Jeśli masz więcej udziałów niż 30, często nie trzeba mapować lokalnego udziału 1:1 do udziału plików platformy Azure. Należy wziąć pod uwagę następujące opcje.

#### <a name="share-grouping"></a>Grupowanie udziałów

Jeśli Dział kadr (na przykład) ma łącznie 15 udziałów, można rozważyć przechowywanie wszystkich danych KADRy w pojedynczym udziale plików platformy Azure. Przechowywanie wielu udziałów lokalnych w jednym udziale plików platformy Azure nie uniemożliwia tworzenia zwykłych 15 udziałów SMB w lokalnym wystąpieniu systemu Windows Server. Oznacza to, że można organizować foldery główne tych 15 udziałów jako podfoldery w ramach wspólnego folderu. Następnie zsynchronizuj ten wspólny folder z udziałem plików platformy Azure. W ten sposób tylko jeden udział plików platformy Azure w chmurze jest wymagany dla tej grupy udziałów lokalnych.

#### <a name="volume-sync"></a>Synchronizacja woluminu

Azure File Sync obsługuje synchronizowanie katalogu głównego woluminu z udziałem plików platformy Azure. W przypadku synchronizacji folderu głównego wszystkie podfoldery i pliki będą przechodzić do tego samego udziału plików platformy Azure.

Synchronizowanie katalogu głównego woluminu nie zawsze jest najlepszą odpowiedzią. Synchronizacja wielu lokalizacji ma zalety. Na przykład takie rozwiązanie pomaga zachować liczbę elementów mniejszą dla zakresu synchronizacji. Konfigurowanie Azure File Sync o mniejszej liczbie elementów nie jest samo przydatne w przypadku synchronizacji plików. Mniejsza liczba elementów korzysta również z następujących scenariuszy:

* Przywracanie po stronie chmury z migawki udziału plików platformy Azure można wykonać jako kopię zapasową.
* Odzyskiwanie awaryjne serwera lokalnego może znacznie przyspieszyć.
* Zmiany wprowadzone bezpośrednio w udziale plików platformy Azure (synchronizację zewnętrzną) mogą być wykrywane i synchronizowane szybciej.

#### <a name="a-structured-approach-to-a-deployment-map"></a>Podejście strukturalne do mapy wdrożenia

Przed wdrożeniem magazynu w chmurze w późniejszym etapie należy utworzyć mapę między folderami lokalnymi i udziałami plików platformy Azure. To mapowanie będzie informować o tym, ile zasobów *grupy synchronizacji* Azure File Sync, które mają zostać zainicjowane. Grupa synchronizacji łączy udział plików platformy Azure i folder na serwerze, a także nawiązuje połączenie synchronizacji.

Aby podjąć decyzję o liczbie potrzebnych udziałów plików platformy Azure, zapoznaj się z następującymi ograniczeniami i najlepszymi rozwiązaniami. Wykonanie tej czynności pomoże zoptymalizować mapę.

* Serwer z zainstalowanym agentem Azure File Sync może być zsynchronizowany z maksymalnie 30 udziałami plików platformy Azure.
* Udział plików platformy Azure jest wdrażany w ramach konta magazynu. Powoduje to, że konto magazynu jest celem skalowania dla numerów wydajności, takich jak operacje we/wy i przepływność. 

  Udziały plików platformy Azure w warstwie Standardowa (niepremium) mogą teoretycznie zamierzać maksymalną wydajność, jaką może dostarczyć konto magazynu. Jeśli planujesz dołączenie Azure File Sync do tych udziałów plików, zgrupowanie kilku udziałów plików platformy Azure na to samo konto magazynu nie spowoduje problemu. Przejrzyj cele wydajności udziału plików platformy Azure, aby uzyskać dokładniejszy wgląd w odpowiednie metryki, które należy wziąć pod uwagę. 

  Jeśli planujesz podnoszenie poziomu aplikacji na platformę Azure, która będzie korzystać z natywnego udziału plików platformy Azure, konieczne może być zwiększenie wydajności udziału plików platformy Azure. Jeśli jest to możliwe, nawet w przyszłości, mapowanie udziału plików platformy Azure na jego własne konto magazynu jest najlepszym rozwiązaniem.
* W jednym regionie platformy Azure obowiązuje limit 250 kont magazynu na subskrypcję.

> [!TIP]
> Z tych informacji często zdarza się, aby zgrupować wiele folderów najwyższego poziomu na woluminach do wspólnego, nowego katalogu głównego. Następnie zsynchronizujesz ten nowy katalog główny i wszystkie foldery, które zostały pogrupowane, do pojedynczego udziału plików platformy Azure. Ta technika pozwala zachować limit 30 synchronizacji udziałów plików platformy Azure na serwer.
>
> Takie grupowanie w ramach wspólnego elementu głównego nie ma wpływu na dostęp do danych. Listy ACL pozostają w stanie takim, w jakim się znajdują. Wystarczy dostosować wszystkie ścieżki udziałów (na przykład udziały SMB lub NFS), które mogą znajdować się w folderach na serwerze, które zostały teraz zmienione do wspólnego katalogu głównego. Nic nie zmienia.

Innym ważnym aspektem Azure File Sync i zrównoważoną wydajnością i doświadczeniem jest zrozumienie czynników skalowania dla Azure File Sync wydajności. Oczywiście, gdy pliki są synchronizowane przez Internet, większe pliki przewyższają czas i przepustowość do synchronizacji.

> [!IMPORTANT]
> Najważniejszym wektorem skali dla Azure File Sync jest liczba elementów (plików i folderów), które muszą być synchronizowane.

Azure File Sync obsługuje synchronizowanie maksymalnie 100 000 000 elementów z pojedynczym udziałem plików platformy Azure. Ten limit można przekroczyć i pokazuje tylko, co zespół Azure File Sync sprawdza w regularnych odstępach czasu.

Najlepszym rozwiązaniem jest utrzymywanie liczby elementów na niski zakres synchronizacji. Jest to istotny czynnik, który należy wziąć pod uwagę w mapowaniu folderów do udziałów plików platformy Azure.

W danej sytuacji istnieje możliwość, że zestaw folderów można logicznie synchronizować z tym samym udziałem plików platformy Azure (przy użyciu nowego, wspólnego podejścia do folderu głównego wymienionego wcześniej). Jednak nadal warto ponownie zgrupować foldery, aby były synchronizowane z dwoma, a nie jednym udziałem plików platformy Azure. Tego podejścia można użyć, aby zachować liczbę plików i folderów na udziale plików, które są zrównoważone na serwerze.

#### <a name="create-a-mapping-table"></a>Tworzenie tabeli mapowania

:::row:::
    :::column:::
        [![Przykład tabeli mapowania. Pobierz Poniższy plik do środowiska i użyj zawartości tego obrazu.](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Aby określić liczbę potrzebnych udziałów plików platformy Azure, a które części istniejących danych zostaną zakończone w tym udziale plików platformy Azure, użyj kombinacji poprzednich koncepcji.
        
        Utwórz tabelę, która rejestruje Twoje przemyślenia, więc możesz odwołać się do niej w następnym kroku. Jest to ważne, ponieważ można łatwo stracić szczegóły planu mapowania w przypadku aprowizacji wielu zasobów platformy Azure jednocześnie. Aby ułatwić tworzenie kompletnego mapowania, możesz pobrać plik programu Microsoft Excel jako szablon.

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

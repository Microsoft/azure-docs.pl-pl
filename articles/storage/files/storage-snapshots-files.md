---
title: Przegląd migawek udziałów dla Azure Files | Microsoft Docs
description: Migawka udziału jest wersją tylko do odczytu udziału Azure Files, która jest wykonywana w danym momencie, jako sposób tworzenia kopii zapasowej udziału.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d415ef165da18312a458d7d14fba18acd1bf44cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84235605"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Overview of share snapshots for Azure Files (Omówienie migawek udziałów w usłudze Azure Files)

Usługa Azure Files umożliwia tworzenia migawek udziałów plików. Migawki udziału przechwytują stan udziału w konkretnym momencie. W tym artykule opisano funkcje oferowane przez migawki udziałów i sposób ich wykorzystania w przypadku konkretnego klienta.

## <a name="when-to-use-share-snapshots"></a>Kiedy skorzystać z migawek udziałów

### <a name="protection-against-application-error-and-data-corruption"></a>Ochrona przed błędami aplikacji i uszkodzeniem danych

Aplikacje korzystające z udziałów plików wykonują operacje, takie jak zapisywanie, odczytywanie, przechowywanie, przesyłanie i przetwarzanie. Jeśli aplikacja jest nieprawidłowo skonfigurowana lub wprowadzono do niej niezamierzoną usterkę, kilka bloków może zostać przypadkowo zastąpionych lub uszkodzonych. Aby ułatwić ochronę przed takimi scenariuszami, można wykonać migawkę udziału przed wdrożeniem nowego kodu aplikacji. Jeśli wraz z nowym wdrożeniem zostanie wprowadzona usterka lub błąd aplikacji, można w takim udziale plików przywrócić wcześniejszą wersję danych. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Ochrona przed przypadkowym usunięciem lub niezamierzonymi zmianami

Załóżmy, że pracujesz nad plikiem tekstowym w udziale plików. Po zamknięciu pliku tekstowego tracisz możliwość cofnięcia zmian. W takich przypadkach jedyną możliwością jest odzyskanie poprzedniej wersji pliku. Za pomocą migawek udziałów można odzyskać poprzednie wersje pliku, jeśli został on przypadkowo usunięty lub zmieniono jego nazwę.

### <a name="general-backup-purposes"></a>Tworzenie kopii zapasowych

Po utworzeniu udziału plików można okresowo tworzyć migawki tego udziału i traktować je jako kopie zapasowe danych. Okresowo wykonywana migawka udziału pomaga zachować poprzednie wersje danych, które mogą być używane na potrzeby przyszłych wymagań dotyczących inspekcji lub odzyskiwania po awarii. Zalecamy używanie [kopii zapasowej udziału plików platformy Azure](../../backup/azure-file-share-backup-overview.md) jako rozwiązania do tworzenia kopii zapasowych w celu wykonywania migawek i zarządzania nimi. Możesz również samodzielnie tworzyć migawki i zarządzać nimi przy użyciu interfejsu wiersza polecenia lub programu PowerShell.

## <a name="capabilities"></a>Możliwości

Migawką udziału jest kopia tylko do odczytu danych w danym momencie. Migawki można tworzyć, usuwać i zarządzać nimi za pomocą interfejsu API REST. Te same możliwości są również dostępne w bibliotece klienta, interfejsie wiersza polecenia platformy Azure i Azure Portal. 

Migawki udziału można wyświetlić za pomocą interfejsu API REST i protokołu SMB. Możesz pobrać listę wersji katalogu lub pliku i można zainstalować określoną wersję bezpośrednio jako dysk (dostępne tylko w systemie Windows — Zobacz [limity](#limits)). 

Po utworzeniu migawki udziału można ją odczytywać, kopiować lub usuwać, ale nie modyfikować. Nie można skopiować całej migawki udziału na inne konto magazynu. Musisz wykonać ten plik przez plik, używając AzCopy lub innych mechanizmów kopiowania.

Możliwość udostępniania migawek jest dostępna na poziomie udziału plików. Pobieranie jest dostępne na poziomie poszczególnych plików, aby umożliwić przywracanie pojedynczych plików. Można przywrócić pełny udział plików przy użyciu protokołu SMB, interfejsu API REST, portalu, biblioteki klienta lub narzędzi PowerShell/interfejsu wiersza polecenia.

Migawka udziału plików jest identyczna z jej podstawowym udziałem plików. Jedyną różnicą jest to, że wartość **daty i godziny** jest dołączana do identyfikatora URI udziału, aby wskazać godzinę utworzenia migawki udziału. Na przykład jeśli identyfikator URI udziału plików to http: \/ /storagesample.Core.File.Windows.NET/myshare, identyfikator URI migawki udziału jest podobny do:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Migawki udziałów pozostają do momentu usunięcia ich jawnie. Migawka udziału nie może na bieżąco korzystać z podstawowego udziału plików. Można wyliczyć migawki skojarzone z podstawowym udziałem plików, aby śledzić bieżące migawki. 

Podczas tworzenia migawki udziału plików, pliki we właściwościach systemu udziału są kopiowane do migawki udziału z tymi samymi wartościami. Pliki podstawowe i metadane udziału plików są również kopiowane do migawki udziału, chyba że podczas tworzenia zostanie określone oddzielne metadane dla migawki udziału.

Nie można usunąć udziału, który ma migawki udziałów, chyba że najpierw usuniesz wszystkie migawki udziału.

## <a name="space-usage"></a>Użycie miejsca

Migawki udziałów mają charakter przyrostowy. Zapisywane są tylko te dane, które uległy zmianie po ostatniej migawce udziału. Pozwala to zminimalizować czas potrzebny na utworzenie migawki udziału i zaoszczędzenie kosztów magazynowania. Wszystkie operacje zapisu do obiektu lub właściwości lub operacji aktualizacji metadanych są zliczane do "zmienionej zawartości" i są przechowywane w migawce udziału. 

Aby zaoszczędzić miejsce, można usunąć migawkę udziału dla okresu, w którym nastąpiła najwyższa.

Mimo że migawki udziałów są zapisywane przyrostowo, należy zachować tylko najnowszą migawkę udziału, aby przywrócić udział. Po usunięciu migawki udziału usuwane są tylko te dane, które są unikatowe dla tej migawki tego udziału. Aktywne migawki zawierają wszystkie informacje potrzebne do przeglądania i przywracania danych (od momentu utworzenia migawki udziału) do oryginalnej lokalizacji lub alternatywnej lokalizacji. Można przywrócić na poziomie elementu.

Migawki nie są wliczane do limitu udziału 5 TB. Nie ma żadnego limitu ilości migawek udziałów w udziale. Nadal obowiązują limity kont magazynu.

## <a name="limits"></a>Limity

Maksymalna liczba migawek udziału, które Azure Files zezwala dzisiaj to 200. Po 200 udziałów migawek należy usunąć starsze migawki udziałów, aby utworzyć nowe. 

Nie ma żadnego limitu jednoczesnych wywołań tworzenia migawek udziałów. Nie ma żadnego limitu ilości miejsca, w którym udostępnione migawki określonego udziału plików mogą być używane. 

Obecnie nie jest możliwe Instalowanie migawek udziałów w systemie Linux. Jest to spowodowane tym, że klient SMB systemu Linux nie obsługuje instalowania migawek takich jak Windows.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopiowanie danych z migawki udziału z powrotem do udziału

Operacje kopiowania obejmujące pliki i migawki udziałów przebiegają zgodnie z następującymi regułami:

Pojedyncze pliki z migawki udziału plików można kopiować do udziału bazowego lub do dowolnej innej lokalizacji. Można przywrócić wcześniejszą wersję pliku lub przywrócić cały udział plików, kopiując poszczególne pliki z migawki udziału. Nie można podwyższyć poziomu migawki udziału do udziału bazowego. 

Migawka udziału pozostaje niezmieniona po skopiowaniu, ale bazowy udział plików jest zastępowany kopią danych, które były dostępne w migawce udziału. Wszystkie przywrócone pliki będą uwzględnione w „zmienionej zawartości”.

Plik z migawki udziału można skopiować ze zmieniona nazwą do innego miejsca docelowego. Wynikowy plik docelowy jest plikiem zapisywalnym, a nie migawką udziału. W takim przypadku bazowy udział plików pozostanie nienaruszony.

Gdy plik docelowy zostanie zastąpiony kopią, wszystkie migawki udziałów skojarzone z oryginalnym plikiem docelowym pozostaną nienaruszone.

## <a name="general-best-practices"></a>Ogólne najlepsze praktyki

Zalecamy używanie [kopii zapasowej udziału plików platformy Azure](../../backup/azure-file-share-backup-overview.md) jako rozwiązania kopii zapasowej do automatyzowania tworzenia migawek, a także zarządzania migawkami. Gdy korzystasz z infrastruktury na platformie Azure, Automatyzuj kopie zapasowe odzyskiwania danych, gdy jest to możliwe. Akcje automatyczne są bardziej niezawodne niż procesy ręczne, pomagając w ulepszaniu ochrony danych i odzyskiwania. Możesz użyć kopii zapasowej udziału plików platformy Azure, interfejsu API REST, zestawu SDK klienta lub skryptów do automatyzacji.

Przed wdrożeniem harmonogramu udziałów migawek należy uważnie uwzględnić częstotliwość tworzenia migawek i ustawienia przechowywania, aby uniknąć ponoszenia niepotrzebnych opłat.

Migawki udziałów zapewniają tylko ochronę na poziomie plików. Migawki udziałów nie uniemożliwiają usuwania przy użyciu programu FAT-Finger w udziale plików lub koncie magazynu. Aby chronić konto magazynu przed przypadkowym usunięciem, można [włączyć opcję usuwania nietrwałego](storage-files-prevent-file-share-deletion.md)lub zablokować konto magazynu i/lub grupę zasobów.

## <a name="next-steps"></a>Następne kroki
- Praca z migawkami udziałów w programie:
    - [Kopia zapasowa udziału plików platformy Azure](../../backup/azure-file-share-backup-overview.md)
    - [Program PowerShell](storage-how-to-use-files-powershell.md)
    - [Interfejs wiersza polecenia](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Często zadawane pytania dotyczące udostępniania migawek](storage-files-faq.md#share-snapshots)
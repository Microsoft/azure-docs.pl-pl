---
title: Azure Data Box Disk Rozwiązywanie problemów z kopiowaniem danych | Microsoft Docs
description: Opisuje sposób rozwiązywania problemów występujących podczas kopiowania danych w Azure Data Box Disk przy użyciu dzienników.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 5d977fe0b7459af35f678e77681d3b27c31431cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85849180"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Rozwiązywanie problemów z kopiowaniem danych w Azure Data Box Disk

Ten artykuł ma zastosowanie do Microsoft Azure Data Box Disk i opisuje sposób rozwiązywania problemów, które pojawiają się podczas kopiowania danych na dyski. W tym artykule opisano również problemy związane z używaniem narzędzia Split Copy.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problemy z kopiowaniem danych w przypadku korzystania z systemu Linux

W tej sekcji szczegółowo przedstawiono niektóre z najważniejszych problemów występujących podczas korzystania z klienta systemu Linux do kopiowania danych na dyski.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Problem: dysk jest instalowany jako tylko do odczytu
 
**Przyczyna** 

Może to być spowodowane nieczystym systemem plików.

Ponowne zainstalowanie dysku przy użyciu funkcji odczytu i zapisu nie działa z dyskami urządzenie Data Box. Ten scenariusz nie jest obsługiwany w przypadku dysków odszyfrowanych przez program Unlocker. Być może pomyślnie zainstalowano urządzenie przy użyciu następującego polecenia:

```
# mount -o remount, rw /mnt/DataBoxDisk/mountVol1
```

Mimo że ponowne zainstalowanie zakończyło się pomyślnie, dane nie zostaną zachowane.

**Rozwiązanie**

W systemie Linux wykonaj następujące czynności:

1. Zainstaluj `ntfsprogs` pakiet dla narzędzia ntfsfix.
2. Odinstaluj punkty instalacji podane dla dysku za pomocą narzędzia unlock. Liczba punktów instalacji różni się w zależności od dysków.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Uruchom `ntfsfix` na odpowiedniej ścieżce. Wyróżniona liczba powinna być taka sama jak krok 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Uruchom następujące polecenie, aby usunąć metadane hibernacji, które mogą spowodować problem z instalowaniem.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Wykonaj czystą dezinstalację.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Wykonaj czyste odblokowywanie i instalowanie.
7. Przetestuj punkt instalacji, pisząc plik.
8. Odinstaluj i zainstaluj ponownie, aby zweryfikować trwałość pliku.
9. Kontynuuj kopiowanie danych.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problem: błąd związany z danymi, które nie są utrwalane po skopiowaniu
 
**Przyczyna** 

Jeśli widzisz, że dysk nie ma danych po odinstalowaniu (Jeśli dane zostały skopiowane do niego), możliwe jest ponowne zainstalowanie dysku jako do odczytu i zapisu po zainstalowaniu dysku jako tylko do odczytu.

**Rozwiązanie**
 
W takim przypadku zapoznaj się z tematem rozdzielczość dysków, które mają zostać [zainstalowane jako tylko do odczytu](#issue-drive-getting-mounted-as-read-only).

Jeśli tak się nie dzieje, Skopiuj dzienniki z folderu, w którym znajduje się narzędzie do odblokowywania Data Box Disk i [Pomoc techniczna firmy Microsoft kontaktów](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Błędy narzędzia do dzielenia skopiowanych dysków Data Box Disk

Problemy występujące podczas korzystania z narzędzia Split Copy do dzielenia danych na wiele dysków są podsumowane w poniższej tabeli.

|Komunikat o błędzie/ostrzeżenia |Zalecenia |
|---------|---------|
|Informacje Pobieranie hasła funkcji BitLocker dla woluminu: m <br>Porn Przechwycono wyjątek podczas pobierania klucza funkcji BitLocker dla woluminu m:<br> Sekwencja nie zawiera elementów.|Ten błąd jest zwracany, jeśli docelowe dyski Data Box Disk są w trybie offline. <br> W przypadku dysków w trybie online należy użyć narzędzia `diskmgmt.msc`.|
|[Błąd] Zgłoszono wyjątek: operacja usługi WMI zakończyła się niepowodzeniem:<br> Method = UnlockWithNumericalPassword, ReturnValue = 2150694965, <br>Win32Message = format podanego hasła odzyskiwania jest nieprawidłowy. <br>Hasła odzyskiwania funkcji BitLocker są 48-cyfrowe. <br>Sprawdź, czy hasło odzyskiwania ma poprawny format, a następnie spróbuj ponownie.|Użyj narzędzia do odblokowywania dysków Data Box Disk, aby odblokować dyski i ponowić próbę wykonania polecenia. Aby uzyskać więcej informacji, przejdź na stronę <li> [Odblokowywanie dysków Data Box Disk dla klientów systemu Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Odblokowywanie dysków Data Box Disk dla klientów systemu Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Błąd] Zgłoszono wyjątek: plik DriveManifest.xml znajduje się na dysku docelowym. <br> Oznacza to, że dysk docelowy został prawdopodobnie przygotowany przy użyciu innego pliku dziennika. <br>Aby dodać więcej danych do tego samego dysku, użyj poprzedniego pliku dziennika. Aby usunąć istniejące dane i ponownie użyć dysku docelowego dla nowego zadania importu, Usuń *DriveManifest.xml* na dysku. Ponownie uruchom to polecenie, używając nowego pliku dziennika.| Ten błąd zostanie wyświetlony podczas próby użycia tego samego zestawu dysków w wielu sesjach importowania. <br> Z jedną sesją dzielenia i kopiowania można użyć tylko jednego zestawu dysków.|
|[Błąd] Zgłoszono wyjątek: CopySessionId importdata-sept-test-1 odwołuje się do poprzedniej sesji kopiowania i nie może zostać użyty ponownie w nowej sesji kopiowania.|Ten błąd jest zgłaszany w przypadku próby użycia tej samej nazwy zadania dla nowego zadania, które zostało poprzednio pomyślnie ukończone.<br> Należy przypisać unikatową nazwę dla nowego zadania.|
|[Informacje] Nazwa pliku lub katalogu docelowego przekracza limit długości dla systemu plików NTFS. |Ten komunikat jest zgłaszany, gdy nazwa pliku docelowego została zmieniona z powodu długiej ścieżki pliku.<br> Aby sterować tym zachowaniem, należy zmodyfikować opcję dyspozycji w pliku `config.json`.|
|[Błąd] Zgłoszono wyjątek: nieprawidłowa sekwencja ucieczki pliku JSON. |Ten komunikat jest zgłaszany, gdy format pliku config.json jest nieprawidłowy. <br> Przed zapisaniem pliku `config.json` zweryfikuj go przy użyciu narzędzia [JSONlint](https://jsonlint.com/).|


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [rozwiązywać problemy z narzędziem walidacji](data-box-disk-troubleshoot.md).

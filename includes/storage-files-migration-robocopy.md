---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 01512f0e37761915fcb6eeba8c162e1a3db62c48
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491687"
---
```console
Robocopy /MT:16 /R:5 /W:5 /B /MIR /IT /COPY:DATSO /DCOPY:DAT /NP /NFL /NDL /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Przełącznik                | Znaczenie |
|-----------------------|---------|
| `/MT:n`               | Zezwala na uruchamianie wielowątkowości przez RoboCopy. Wartość domyślna dla n = 8, a maksymalna to 128 wątków. Dopasuj tę wartość do liczby rdzeni procesora i liczby wątków na rdzeń. Rozważ, czy rdzenie muszą być zarezerwowane dla innych zadań, które może mieć serwer produkcyjny. |
| `/R:n`                | Szybkość przebiegu RoboCopy można ulepszyć, określając maksymalną liczbę ponownych prób dla pliku, który nie może zostać skopiowany przy pierwszej próbie. n = Maksymalna liczba ponownych prób przed nieodwracalnym skopiowaniem pliku w tym RoboCopy przebiegu. Ten przełącznik działa najlepiej w scenariuszach, w których jest już jasne, że będzie więcej RoboCopyych uruchomień. Jeśli nie można skopiować pliku w tym przebiegu, następne zadanie RoboCopy zostanie ponowione. Często pliki, które zakończyły się niepowodzeniem, ponieważ były używane lub z powodu problemów z przekroczeniem limitu czasu, mogą zostać pomyślnie skopiowane z tą metodą. |
| `/W:n`                | Ten przełącznik określa czas oczekiwania RoboCopy przed podjęciem próby skopiowania pliku, który nie został pomyślnie skopiowany podczas ostatniej próby. n = liczba sekund oczekiwania między ponownymi próbami. `/W:n` jest często używany razem z programem `/R:n` . |
| `/B`                  | Uruchamia RoboCopy w tym samym trybie, w którym będzie używana aplikacja kopii zapasowej. Umożliwia RoboCopy przenoszenie plików, do których bieżący użytkownik nie ma uprawnień. |
| `/MIR`                | *Duplikat źródła do elementu docelowego* umożliwia Robocopy tylko kopiowanie różnic między źródłem a celem. Puste podkatalogi zostaną skopiowane. Elementy (pliki lub foldery), które uległy zmianie lub nie istnieją w miejscu docelowym, zostaną skopiowane. Elementy, które istnieją w miejscu docelowym, ale nie w źródle, zostaną usunięte (usunięty) z obiektu docelowego. W przypadku korzystania z tego przełącznika należy dokładnie dopasować źródłową i docelową strukturę folderów. "Dopasowanie" oznacza skopiowanie z poprawnego poziomu źródłowego i folderu do zgodnego poziomu folderu w miejscu docelowym. Dopiero wtedy może nastąpić pomyślne przechwycenie kopii. Gdy źródło i cel są niezgodne, użycie `/MIR` go spowoduje usunięcie i ponowne skopiowanie dużej skali. |
| `/IT`                 | Zapewnia, że wierność są zachowywane w niektórych scenariuszach dublowania. </br>*Przykład* : Jeśli między dwoma RoboCopyami zostanie uruchomiony plik, wystąpił zmiana listy ACL i aktualizacja atrybutu. na przykład jest oznaczona jako *Ukryta*. Bez/IT zmiana listy ACL może zostać pominięta przez RoboCopy i nie jest przesyłana do lokalizacji docelowej. |
|`/COPY:[copyflags]`    | Wierność kopiowania plików (wartość domyślna, jeśli nie określono `/COPY:DAT` ), Kopiuj flagi: `D` = Data, `A` = Attributes, `T` = Timestamps, `S` = Security = NTFS list ACL, `O` = Informacje o właścicielu, `U` = A informacje<u>o</u>zanotowaniu. Informacje o inspekcji nie mogą być przechowywane w udziale plików platformy Azure. |
| `/DCOPY:[copyflags]`  | Wierność kopiowania katalogów (wartość domyślna, jeśli nie określono `/DCOPY:DA` ), Kopiuj flagi: `D` = Data, `A` = Attributes, `T` = Timestamps. |
| `/NP`                 | Postęp kopiowania dla każdego pliku i folderu nie zostanie wyświetlony. Wyświetlanie postępu znacząco obniża wydajność kopiowania. |
| `/NFL`                | Określa, że nazwy plików nie są rejestrowane. Zwiększa wydajność kopiowania. |
| `/NDL`                | Określa, że nazwy katalogów nie są rejestrowane. Zwiększa wydajność kopiowania. |
| `/UNILOG:<file name>` | Wyprowadza stan do pliku dziennika jako UNICODE (Zastępuje istniejący dziennik). |
| `/LFSM`               | **tylko dla celów z magazynem warstwowym** </br>Używanie żądań/LFSM RoboCopy do działania w trybie "Low Free Space". Ten przełącznik jest przydatny tylko w przypadku obiektów docelowych z magazynem warstwowym, co może spowodować wypróbowanie pojemności lokalnej przed ukończeniem RoboCopy. Ten przełącznik został dodany do użytku z Azure File Sync docelowym z obsługą warstw w chmurze. Może być używany niezależnie od Azure File Sync. W tym trybie RoboCopy będzie wstrzymywany za każdym razem, gdy kopiowanie plików spowoduje, że ilość wolnego miejsca na woluminie docelowym będzie niższa od wartości "piętro". Tę wartość można określić za pomocą `/LFSM:n` formy flagi. Parametr `n` jest określony w podstawowej 2: `nKB` , `nMB` lub `nGB` . Jeśli `/LFSM` jest określony bez jawnej wartości podłogi, podłoga jest ustawiana na 10 procent rozmiaru woluminu docelowego. Tryb małej ilości wolnego miejsca jest niezgodny z/MT,/EFSRAW,/B i/ZB. |
| `/Z`                  | **dokładne użycie** </br>Kopiuje pliki w trybie ponownego uruchamiania, użycie jest zalecane tylko w niestabilnym środowisku sieciowym. Ta opcja znacząco zmniejsza wydajność kopiowania z powodu dodatkowego rejestrowania. |
| `/ZB`                 | **dokładne użycie** </br>Używa trybu ponownego uruchamiania. W przypadku odmowy dostępu, ta opcja używa trybu tworzenia kopii zapasowej. Ta opcja znacząco zmniejsza wydajność kopiowania z powodu tworzenia punktów kontrolnych. |
   
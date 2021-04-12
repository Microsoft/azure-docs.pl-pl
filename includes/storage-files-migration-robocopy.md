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
ms.openlocfilehash: 6158964c04a689ed421f216c2910f47d7a39e2be
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081129"
---
```console
Robocopy /MT:16 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Przełącznik              | Znaczenie |
|---------------------|---------|
| /MT                 | Zezwala na uruchamianie wielowątkowości przez RoboCopy. Wartość domyślna to 8, a wartość maksymalna to 128. Dopasuj tę wartość do liczby rdzeni procesora i liczby wątków na rdzeń. Rozważ, czy rdzenie muszą być zarezerwowane dla innych zadań, które może mieć serwer produkcyjny. |
| /NP                 | Postęp kopiowania dla każdego pliku i folderu nie zostanie wyświetlony. Wyświetlanie postępu znacząco obniża wydajność kopiowania. |
| /NFL                | Określa, że nazwy plików nie są rejestrowane. Zwiększa wydajność kopiowania. |
| /NDL                | Określa, że nazwy katalogów nie są rejestrowane. Zwiększa wydajność kopiowania. |
| /B                  | Uruchamia RoboCopy w tym samym trybie, w którym będzie używana aplikacja kopii zapasowej. Umożliwia RoboCopy przenoszenie plików, do których bieżący użytkownik nie ma uprawnień. |
| /MIR                | *Duplikat źródła do elementu docelowego* umożliwia Robocopy tylko kopiowanie różnic między źródłem a celem. Puste podkatalogi zostaną skopiowane. Elementy (pliki lub foldery), które uległy zmianie lub nie istnieją w miejscu docelowym, zostaną skopiowane. Elementy, które istnieją w miejscu docelowym, ale nie w źródle, zostaną usunięte (usunięty) z obiektu docelowego. W przypadku korzystania z tego przełącznika należy dokładnie dopasować źródłową i docelową strukturę folderów. "Dopasowanie" oznacza skopiowanie z poprawnego poziomu źródłowego i folderu do zgodnego poziomu folderu w miejscu docelowym. Dopiero wtedy może nastąpić pomyślne przechwycenie kopii. Gdy źródło i cel są niezgodne, użycie `/MIR` go spowoduje usunięcie i ponowne skopiowanie dużej skali. |
| /IT                 | Zapewnia, że wierność są zachowywane w niektórych scenariuszach dublowania. </br>*Przykład* : Jeśli między dwoma RoboCopyami zostanie uruchomiony plik, wystąpił zmiana listy ACL i aktualizacja atrybutu. na przykład jest oznaczona jako *Ukryta*. Bez/IT zmiana listy ACL może zostać pominięta przez RoboCopy i nie jest przesyłana do lokalizacji docelowej. |
|Kopiowane`[copyflags]`  | Wierność kopiowania plików (wartość domyślna, jeśli nie określono `/COPY:DAT` ), Kopiuj flagi: `D` = Data, `A` = Attributes, `T` = Timestamps, `S` = Security = NTFS list ACL, `O` = Informacje o właścicielu, `U` = A informacje<u>o</u>zanotowaniu. Informacje o inspekcji nie mogą być przechowywane w udziale plików platformy Azure. |
| /DCOPY:`[copyflags]`| Wierność kopiowania katalogów (wartość domyślna, jeśli nie określono `/DCOPY:DA` ), Kopiuj flagi: `D` = Data, `A` = Attributes, `T` = Timestamps. |
| /UNILOG:<file name> | Wyprowadza stan do pliku dziennika jako UNICODE (Zastępuje istniejący dziennik). |
| /LFSM               | **tylko dla celów z magazynem warstwowym** </br>Używanie żądań/LFSM RoboCopy do działania w trybie "Low Free Space". Ten przełącznik jest przydatny tylko w przypadku obiektów docelowych z magazynem warstwowym, co może spowodować wypróbowanie pojemności lokalnej przed ukończeniem RoboCopy. Ten przełącznik został dodany do użytku z Azure File Sync docelowym z obsługą warstw w chmurze. Może być używany niezależnie od Azure File Sync. W tym trybie RoboCopy będzie wstrzymywany za każdym razem, gdy kopiowanie plików spowoduje, że ilość wolnego miejsca na woluminie docelowym będzie niższa od wartości "piętro". Tę wartość można określić za pomocą `/LFSM:n` formy flagi. Parametr `n` jest określony w podstawowej 2: `nKB` , `nMB` lub `nGB` . Jeśli `/LFSM` jest określony bez jawnej wartości podłogi, podłoga jest ustawiana na 10 procent rozmiaru woluminu docelowego. Tryb małej ilości wolnego miejsca jest niezgodny z/MT,/EFSRAW,/B i/ZB. |
| /Z                  | **dokładne użycie** </br>Kopiuje pliki w trybie ponownego uruchamiania, użycie jest zalecane tylko w niestabilnym środowisku sieciowym. Ta opcja znacząco zmniejsza wydajność kopiowania z powodu dodatkowego rejestrowania. |
| /ZB                 | **dokładne użycie** </br>Używa trybu ponownego uruchamiania. W przypadku odmowy dostępu, ta opcja używa trybu tworzenia kopii zapasowej. Ta opcja znacząco zmniejsza wydajność kopiowania z powodu tworzenia punktów kontrolnych. |
   
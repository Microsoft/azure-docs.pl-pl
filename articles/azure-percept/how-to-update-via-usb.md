---
title: Aktualizowanie usługi Azure Percept DK za pośrednictwem połączenia USB
description: Dowiedz się, jak zaktualizować usługę Azure Percept DK za pośrednictwem połączenia USB
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 7f5e5e4da9fea671fc85a55fc8cc191fa14b720f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101663006"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Jak zaktualizować usługę Azure Percept DK za pośrednictwem połączenia USB

Postępuj zgodnie z tym przewodnikiem, aby dowiedzieć się, jak wykonać aktualizację USB dla tablicy nośnej platformy Azure Percept.

## <a name="prerequisites"></a>Wymagania wstępne
- Komputer hosta z dostępnym portem USB-C lub USB-A.
- Azure Percept DK (dev Kit) tablica nośna i dostarczone USB-C do kabla USB-C. Jeśli komputer hosta ma port USB-A, ale nie port USB-C, możesz użyć kabla USB-C do USB-A (sprzedawany oddzielnie).
- Zainstaluj [program](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) (konieczny jest dostęp administratora).
- Zainstaluj narzędzie NXP UUU. [Pobierz najnowszą wersję](https://github.com/NXPmicro/mfgtools/releases) pliku uuu.exe (dla systemu Windows) lub plik Uuu (w systemie Linux) na karcie zasoby.
- [Zainstaluj 7-zip](https://www.7-zip.org/). To oprogramowanie będzie używane do wyodrębniania pierwotnego pliku obrazu z pliku skompresowanego XZ. Pobierz i zainstaluj odpowiedni plik exe.

## <a name="steps"></a>Kroki
1.  Pobierz następujące [trzy pliki aktualizacji USB](https://go.microsoft.com/fwlink/?linkid=2155734):
    - pe101 —***&lt; Numer &gt; wersji*** interfejsu UEFI. Raw. XZ
    - emmc_full.txt
    - Fast-hab-FW. Raw
 
1. Wyodrębnij do pe101-UEFI-***&lt; Numer &gt; wersji**_. Raw ze skompresowanego pliku pe101-UEFI o_ * _&lt; numerze &gt; wersji_* *. Raw. XZ. Nie wiesz, jak wyodrębnić? Pobierz i zainstaluj 7-zip, a następnie kliknij prawym przyciskiem myszy plik obrazu **. XZ** , a następnie wybierz 7-zip &gt; Wyodrębnij tutaj.

1. Skopiuj następujące trzy pliki do folderu, który zawiera narzędzie UUU:
    - Wyodrębniono plik pe101-UEFI-***&lt; Version &gt;***. RAW (z kroku 2).
    - emmc_full.txt (z kroku 1).
    - Fast-hab-FW. RAW (z kroku 1).
 
1. Włącz zestaw dev Kit.
1. [Nawiązywanie połączenia z zestawem deweloperskim za pośrednictwem protokołu SSH](./how-to-ssh-into-percept-dk.md)
1. Otwórz wiersz polecenia systemu Windows (Uruchom &gt; cmd) lub terminalu z systemem Linux i przejdź do folderu, w którym są przechowywane pliki aktualizacji. Uruchom następujące polecenie, aby zainicjować aktualizację:
    - Windows: ```uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    - Linux: ```sudo ./uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    
Po uruchomieniu tych poleceń może zostać wyświetlony komunikat z informacją "Oczekiwanie na urządzenie..." w wierszu polecenia. Jest to oczekiwane i należy przejść do następnego kroku.
    
1. Podłącz tablicę przewoźnika zestawu deweloperów do komputera hosta za pośrednictwem kabla USB. W zależności od tego, które porty są dostępne, zawsze łącz się z portu USB-C z płytą nośnymi na komputerze hosta USB-C lub USB-A. 
 
1. W terminalu SSH/wypełniania wprowadź następujące polecenia, aby ustawić zestaw deweloperski w tryb USB, a następnie ponownie uruchomić zestaw deweloperski.
    - ```flagutil    -wBfRequestUsbFlash    -v1```
    - ```reboot -f```
 
1. Można wskazać, że komputer hosta rozpoznaje urządzenie, a proces aktualizacji zostanie uruchomiony automatycznie. Przejdź z powrotem do wiersza polecenia, aby wyświetlić stan. Proces potrwa do dziesięciu minut, a po pomyślnym zakończeniu aktualizacji zostanie wyświetlony komunikat z informacją o powodzeniu 1 niepowodzenia 0
 
1. Po zakończeniu aktualizacji wyłącz zasilanie z tablicy nośnej. Odłącz kabel USB od komputera.  Podłącz moduł wizji platformy Azure Percept z powrotem do tablicy nośnej przy użyciu kabla USB.

1. Włącz ponownie przywracanie do tablicy nośnej.

## <a name="next-steps"></a>Następne kroki

Twój zestaw dev Kit został pomyślnie zaktualizowany. Możesz kontynuować rozwój i pracę z Twoim Devkit.
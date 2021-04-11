---
title: Aktualizowanie usługi Azure Percept DK za pośrednictwem połączenia USB
description: Dowiedz się, jak zaktualizować usługę Azure Percept DK za pośrednictwem połączenia USB
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: cd6e4e62123b4d4b927cf385aaf64a066eecc1e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104887754"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Jak zaktualizować usługę Azure Percept DK za pośrednictwem połączenia USB

Chociaż korzystanie z aktualizacji opartych na środowisku AIR (OTA) to najlepsza metoda zapewniania aktualności systemu operacyjnego i oprogramowania układowego zestawu deweloperskiego, istnieją scenariusze, w których jest konieczne zaktualizowanie (lub "miganie") zestawu deweloperskiego za pośrednictwem połączenia USB:

- Aktualizacja OTA nie jest możliwa z powodu łączności lub innych problemów technicznych
- Urządzenie musi zostać przywrócone z powrotem do stanu fabrycznego

W tym przewodniku pokazano, jak pomyślnie zaktualizować system operacyjny i oprogramowanie układowe zestawu deweloperskiego za pośrednictwem połączenia USB.

> [!WARNING]
> Aktualizacja zestawu deweloperskiego za pośrednictwem portu USB spowoduje usunięcie wszystkich istniejących danych z urządzenia, w tym modeli i kontenerów AI.
>
> Postępuj zgodnie ze wszystkimi instrukcjami w kolejności. Kroki pomijania mogą spowodować umieszczenie zestawu deweloperów w stanie uniemożliwiającym korzystanie z programu.

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Percept DK
- Komputer-host z systemem Windows, Linux lub OS X z możliwością Wi-Fi i dostępnego portu USB-C lub USB-A
- Kabel USB-C do USB-A (opcjonalny, sprzedawany osobno)
- Logowanie SSH utworzone podczas [instalacji usługi Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)

## <a name="download-software-tools-and-update-files"></a>Pobieranie narzędzi programowych i aktualizowanie plików

1. [Narzędzie NXP UUU](https://github.com/NXPmicro/mfgtools/releases). Pobierz **najnowszą wersję** pliku uuu.exe (dla systemu Windows) lub plik Uuu (w systemie Linux) na karcie **zasoby** .

1. [7 — zip](https://www.7-zip.org/). To oprogramowanie będzie używane do wyodrębniania pierwotnego pliku obrazu z pliku skompresowanego XZ. Pobierz i zainstaluj odpowiedni plik exe.

1. [Pobierz pliki aktualizacji](https://go.microsoft.com/fwlink/?linkid=2155734).

1. Upewnij się, że wszystkie trzy artefakty kompilacji są obecne:
    - Azure-Percept-DK —*&lt; Numer &gt; wersji*. Raw. XZ
    - Fast-hab-FW. Raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

1. Utwórz folder/katalog na komputerze hosta w lokalizacji, do której jest łatwy dostęp za pośrednictwem wiersza polecenia.

1. Skopiuj narzędzie UUU (**uuu.exe** lub **UUU**) do nowego folderu.

1. Wyodrębnij plik **. Raw usługi Azure-*&lt; &gt;* Percept-DK** z pliku skompresowanego, klikając prawym przyciskiem myszy pozycję **Azure-Percept-DK-*&lt; Numer &gt; wersji*. Raw. XZ** i wybierając **7-zip** &gt; **Wyodrębnij tutaj**.

1. Przenieś wyodrębniony plik w formacie **Azure-Percept-DK-*&lt; &gt; Version*. Raw** , **Fast-hab-FW. Raw** i **emmc_full.txt** do folderu zawierającego narzędzie UUU.

## <a name="update-your-device"></a>Aktualizowanie urządzenia

1. Użyj protokołu [SSH do Twojego zestawu deweloperskiego](./how-to-ssh-into-percept-dk.md).

1. Następnie otwórz wiersz polecenia systemu Windows (**Uruchom**  >  **cmd**) lub terminal Linux i przejdź do folderu, w którym są przechowywane pliki aktualizacji i narzędzie UUU. Wprowadź następujące polecenie w wierszu polecenia lub terminalu, aby przygotować komputer do urządzenia z Flash:

    - W systemie Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - W systemie Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. Odłącz urządzenie Percept z punktu widzenia platformy Azure od portu USB-C tablicy nośnej.

1. Podłącz dostarczony kabel USB-C do portu USB-C tablicy nośnej oraz port USB-C komputera hosta. Jeśli komputer ma tylko port USB-A, podłącz USB-C do kabla USB-A (sprzedawane oddzielnie) do tablicy nośnej i komputera hosta.

1. W monicie klienta SSH wprowadź następujące polecenia:

    1. Ustaw urządzenie na tryb aktualizacji USB:

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. Uruchom ponownie urządzenie. Rozpocznie się instalacja aktualizacji.

        ```bash
        sudo reboot -f
        ```

1. Przejdź z powrotem do innego wiersza polecenia lub terminalu. Po zakończeniu aktualizacji zostanie wyświetlony komunikat z ```Success 1    Failure 0``` :

    > [!NOTE]
    > Po aktualizacji urządzenie zostanie zresetowane do ustawień fabrycznych i utracisz połączenie Wi-Fi i dane logowania SSH.

1. Po zakończeniu aktualizacji wyłącz zasilanie z tablicy nośnej. Odłącz kabel USB od komputera.  

## <a name="next-steps"></a>Następne kroki

Aby zmienić konfigurację urządzenia, zapoznaj się z tematem [Instalacja usługi Azure PERCEPT DK](./quickstart-percept-dk-set-up.md) .
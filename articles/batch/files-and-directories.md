---
title: Pliki i katalogi w Azure Batch
description: Dowiedz się więcej o plikach i katalogach oraz sposobie ich użycia w przepływie pracy Azure Batch z punktu widzenia rozwoju.
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: eafea6c234c3b261521f8a791b7a03e25388f02a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87552644"
---
# <a name="files-and-directories-in-azure-batch"></a>Pliki i katalogi w Azure Batch

W Azure Batch każde zadanie ma katalog roboczy, w którym można tworzyć pliki i katalogi. Ten katalog roboczy może służyć do przechowywania programu uruchamianego przez zadanie podrzędne, przetwarzania danych oraz danych końcowych przetwarzania. Wszystkie pliki i katalogi zadania podrzędnego należą do użytkownika zadania podrzędnego.

Usługa Batch przedstawia część systemu plików w węźle w postaci *katalogu głównego*. Ten katalog główny znajduje się na tymczasowym dysku magazynu maszyny wirtualnej, a nie bezpośrednio na dysku systemu operacyjnego.

Zadania podrzędne mogą uzyskać dostęp do katalogu głównego, odwołując się do zmiennej środowiskowej `AZ_BATCH_NODE_ROOT_DIR`. Więcej informacji na temat korzystania ze zmiennych środowiskowych znajduje się w temacie[Environment settings for tasks](jobs-and-tasks.md#environment-settings-for-tasks) (Ustawienia środowiska dla zadań).

## <a name="root-directory-structure"></a>Struktura katalogów głównych

Katalog główny zawiera następującą strukturę katalogu:

![Zrzut ekranu struktury katalogu węzłów obliczeniowych.](media\files-and-directories\node-folder-structure.png)

- **aplikacje**: zawiera informacje o szczegółach pakietów aplikacji zainstalowanych w węźle obliczeniowym. Zadania podrzędne mogą uzyskać dostęp do tego katalogu, odwołując się do zmiennej środowiskowej `AZ_BATCH_APP_PACKAGE`.

- **fsmounts**: katalog zawiera wszystkie systemy plików, które są zainstalowane w węźle obliczeniowym. Zadania podrzędne mogą uzyskać dostęp do tego katalogu, odwołując się do zmiennej środowiskowej `AZ_BATCH_NODE_MOUNTS_DIR`. Aby uzyskać więcej informacji, zobacz [Instalowanie wirtualnego systemu plików w puli usługi Batch](virtual-file-mount.md).

- **shared**: ten katalog zapewnia prawa do odczytu i zapisu dla *wszystkich* zadań podrzędnych wykonywanych w węźle. Każde zadanie podrzędne uruchamiane w węźle może tworzyć, odczytywać, aktualizować i usuwać pliki w tym katalogu. Zadania podrzędne mogą uzyskać dostęp do tego katalogu, odwołując się do zmiennej środowiskowej `AZ_BATCH_NODE_SHARED_DIR`.

- **startup**: ten katalog jest używany jako katalog roboczy przez zadanie podrzędne uruchamiania. W tym miejscu są przechowywane wszystkie pliki pobrane do węzła przez zadanie podrzędne uruchamiania. Zadanie podrzędne uruchamiania może tworzyć, odczytywać, aktualizować i usuwać pliki w tym katalogu. Zadania podrzędne mogą uzyskać dostęp do tego katalogu, odwołując się do zmiennej środowiskowej `AZ_BATCH_NODE_STARTUP_DIR`.

- **nietrwały**: ten katalog jest przeznaczony do użytku wewnętrznego. Nie ma gwarancji, że wszystkie pliki w tym katalogu lub sam katalog będą istnieć w przyszłości.

- elementy **robocze**: ten katalog zawiera katalogi zadań i ich zadań w węźle obliczeniowym.

    W katalogu elementów **roboczych** jest tworzony katalog **zadań** dla każdego zadania, które jest uruchamiane w węźle. Dostęp do tego katalogu można uzyskać, odwołując się do `AZ_BATCH_TASK_DIR` zmiennej środowiskowej.

    W każdym katalogu **zadań** usługa Batch tworzy katalog roboczy ( `wd` ), którego unikatowa ścieżka jest określana przez `AZ_BATCH_TASK_WORKING_DIR` zmienną środowiskową. Ten katalog zapewnia prawa do odczytu i zapisu zadania. Zadanie podrzędne może tworzyć, odczytywać, aktualizować i usuwać pliki w tym katalogu. Ten katalog jest zachowywany na podstawie ograniczenia *RetentionTime* wybranego dla zadania podrzędnego.

    `stdout.txt`Pliki i `stderr.txt` są zapisywane do folderu **zadania** podczas wykonywania zadania.

> [!IMPORTANT]
> Gdy węzeł zostanie usunięty z puli, zostaną usunięte wszystkie pliki, które są przechowywane w węźle.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [obsługi błędów i wykrywania](error-handling.md) w Azure Batch.
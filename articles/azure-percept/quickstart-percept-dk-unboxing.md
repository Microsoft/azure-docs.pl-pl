---
title: Unbox i złóż składniki usługi Azure Percept DK
description: Dowiedz się, jak Unbox, łączyć i włączać platformę Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: efa255ba38f7e00785335bf458ecc0ed91da646b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608184"
---
# <a name="quickstart-unbox-and-assemble-your-azure-percept-dk-components"></a>Szybki Start: Unbox i złóż składniki usługi Azure Percept DK

Po otrzymaniu usługi Azure Percept DK odwołuje się do tego przewodnika, aby uzyskać informacje na temat łączenia składników i włączania urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Percept DK (Devkit)
- Śrubokręt P7 (opcjonalny, używany do zabezpieczania łącznika kabla zasilającego do tablicy nośnej)

## <a name="unbox-and-assemble-your-device"></a>Unbox i złóż urządzenie

1. Unbox składniki platformy Azure Percept.

    Devkit zawiera tablicę nośnej, model SoM platformy Azure Percept, pole akcesoriów zawierające antenę i kable oraz kartę powitalną z kluczem szesnastkowym.

1. Połącz składniki Devkit.

    > [!NOTE]
    > Port karty sieciowej znajduje się po prawej stronie tablicy nośnej. Pozostałe porty (2x USB-A, 1x USB-C i 1x Ethernet) i przycisk energia znajdują się po lewej stronie tablicy nośnej.

    1. Dłoń umożliwia przechodzenie między Wi-Fi anten do tablicy nośnej.

    1. Podłącz model programu Vision SoM do portu USB-C tablicy nośnej przy użyciu kabla USB-C.

    1. Podłącz przewód do zasilacza.

    1. Usuń wszystkie pozostałe opakowanie z tworzywem sztucznym z urządzeń.

    1. Podłącz transformator zasilania/kabel do tablicy nośnej i punktu wylotu ściany. Aby w pełni zabezpieczyć łącznik kabla zasilającego do tablicy nośnej, użyj śrubokrętu P7 (niezawartego w Devkit) w celu zaostrzenia miejsc łączników.

    1. Po podłączeniu kabla zasilania do gniazda ściennego urządzenie będzie automatycznie włączać. Przycisk energia po lewej stronie tablicy nośnej zostanie wyróżniony. Poczekaj chwilę na uruchomienie urządzenia.

        > [!NOTE]
        > Przycisk zasilania służy do włączania zasilania lub ponownego uruchamiania urządzenia po nawiązaniu połączenia z gniazdem zasilania. W przypadku awarii zasilanej urządzenie zostanie automatycznie uruchomione ponownie.

Dla wizualizacji zestawu Devkit należy zapoznać się z artykułem 0:00 do 0:50 następującego wideo:

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy Devkit jest podłączony i włączony, zobacz [Przewodnik po instalacji usługi Azure PERCEPT DK](./quickstart-percept-dk-set-up.md) , aby zakończyć konfigurację urządzenia. Środowisko instalacji umożliwia łączenie Devkit z siecią Wi-Fi, Konfigurowanie logowania za pomocą protokołu SSH, tworzenie IoT Hub i udostępnianie Devkit na koncie platformy Azure. Po zakończeniu konfigurowania urządzenia będzie można rozpocząć tworzenie prototypów.
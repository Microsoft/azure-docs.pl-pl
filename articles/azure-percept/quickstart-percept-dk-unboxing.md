---
title: Unbox i złóż składniki usługi Azure Percept DK
description: Dowiedz się, jak Unbox, łączyć i włączać platformę Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: 40751401760d877fe3feab39f3fea1f2fbeee54b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665495"
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
    > Port karty sieciowej znajduje się po prawej stronie tablicy nośnej. Pozostałe porty (2x USB-A, 1x USB-C, 1x HDMI i 1x Ethernet) i przycisk Reset znajdują się po lewej stronie tablicy nośnej.

    1. Dłoń umożliwia przechodzenie między Wi-Fi anten do tablicy nośnej.

    1. Podłącz model programu Vision SoM do portu USB-C tablicy nośnej przy użyciu kabla USB-C.

    1. Podłącz przewód do zasilacza.

    1. Usuń wszystkie pozostałe opakowanie z tworzywem sztucznym z urządzeń.

    1. Podłącz transformator zasilania/kabel do tablicy nośnej i punktu wylotu ściany. Aby w pełni zabezpieczyć łącznik kabla zasilającego do tablicy nośnej, użyj śrubokrętu P7 (niezawartego w Devkit) w celu zaostrzenia miejsc łączników.

    1. Po podłączeniu kabla zasilania do gniazda ściennego urządzenie będzie automatycznie włączać. Przycisk Resetuj po lewej stronie tablicy nośnej zostanie wyróżniony. Poczekaj chwilę na uruchomienie urządzenia.

        > [!NOTE]
        > Przycisk Reset służy do włączania zasilania lub resetowania urządzenia po nawiązaniu połączenia z gniazdem zasilania. W przypadku awarii zasilanej urządzenie zostanie automatycznie zresetowane i przywrócone.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy Devkit jest podłączony i włączony, zobacz Przewodnik po instalacji usługi Azure Percept DK, aby zakończyć konfigurację urządzenia. Środowisko instalacji umożliwia łączenie Devkit z siecią Wi-Fi, Konfigurowanie logowania za pomocą protokołu SSH, tworzenie IoT Hub i udostępnianie Devkit na koncie platformy Azure. Po zakończeniu konfigurowania urządzenia będzie można rozpocząć tworzenie prototypów.
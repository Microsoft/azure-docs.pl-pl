---
title: Nawiązywanie połączenia z platformą Azure Percept DK przez numery seryjne
description: Dowiedz się, jak skonfigurować połączenie szeregowe z platformą Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 93b8ab0ce53202402e86b059abe3c600590d549e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101662921"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>Nawiązywanie połączenia z platformą Azure Percept DK przez numery seryjne

Postępuj zgodnie z poniższymi instrukcjami, aby skonfigurować połączenie szeregowe z usługą Azure Percept DK [przy użyciu instrukcji](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)wypełniania.

> [!WARNING]
> **Nie** należy podejmować prób nawiązania połączenia z usługą Devkit za pośrednictwem numeru seryjnego, z wyjątkiem przypadków ekstremalnych błędów (np. urządzenie zostało zacegłne). Przełączenie obudowy do tablicy nośnej w celu podłączenia kabla szeregowego jest bardzo trudne i spowoduje przerwanie okablowania Wi-Fi anteny.

## <a name="prerequisites"></a>Wymagania wstępne

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- KOMPUTER-host
- Azure Percept DK
- [Kabel szeregowy USB do czasu wygaśnięcia](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="Kabel szeregowy USB do czasu wygaśnięcia.":::

## <a name="initiate-the-serial-connection"></a>Inicjowanie połączenia szeregowego

1. Jeśli tablica nośna jest połączona z szyną 80/20, usuń ją z szyny przy użyciu klucza szesnastkowego (zawartego w karcie powitalnej Devkit).

1. Usuń wkręty ze spodu obudowy tablicy nośnej i Wyodrębnij płytę główną.

    > [!WARNING]
    > Usunięcie płyty głównej spowoduje przerwanie kabli Wi-Fi anteny. **Nie** należy przechodzić do połączenia szeregowego, chyba że jest to ostatnia możliwość odzyskania urządzenia.

1. Usuń Heatsink.

1. Usuń tablicę zworek z kodów PIN interfejsu GPIO.

    > [!TIP]
    > Zwróć uwagę na orientację tablicy zworki przed jej usunięciem. Na przykład narysuj strzałkę na lub Dołącz naklejkę do tablicy zworki wskazującej na obwód do celów referencyjnych. Tablica zworki nie jest przedłożono i może być przypadkowo połączona z poprzednimi wersjami podczas ponownego tworzenia tablicy nośnej.

1. Podłącz [kabel szeregowy USB do](https://www.adafruit.com/product/954) numerów PIN interfejsu GPIO na płycie głównej, jak pokazano poniżej. Należy pamiętać, że czerwony przewód nie jest połączony.

    - Podłącz kabel Black (GND) do numeru PIN 6.
    - Podłącz biały kabel (RX) do numeru PIN 8.
    - Podłącz zielony kabel (TX) do numeru PIN 10.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="Połączenia szeregowego numeru PIN tablicy nośnej.":::

1. Włącz Devkit i podłącz urządzenie USB kabla szeregowego do komputera.

1. W systemie Windows przejdź do pozycji **Start**  ->  **Windows Update ustawienia**  ->  **Widok**  ->  **Aktualizacje sterowników** opcjonalnych aktualizacje. Poszukaj na liście aktualizacji typu Serial-USB, zaznacz pole wyboru obok niego, a następnie kliknij pozycję **Pobierz i zainstaluj**.  

1. Następnie otwórz Device Manager systemu Windows (**Start**  ->  **Device Manager**). Przejdź do **portów** i kliknij pozycję **USB do UART** , aby otworzyć **Właściwości**. Zwróć uwagę na port COM, z którym jest połączone urządzenie.

1. Kliknij kartę **Ustawienia portu** . Upewnij się, że **bity na sekundę** mają ustawioną wartość 115200.

1. Otwórz program PuTTY. Wprowadź następujące polecenie, a następnie kliknij przycisk **Otwórz** , aby nawiązać połączenie z usługą Devkit przy użyciu numeru seryjnego:

    1. Linia szeregowa: COM [port #]
    1. Szybkość: 115200
    1. Typ połączenia: numer seryjny

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="Podano okno sesji z wybranymi parametrami numeru seryjnego.":::

## <a name="next-steps"></a>Następne kroki

Aby zaktualizować urządzenie, które nie może zostać uruchomione za pośrednictwem połączenia szeregowego z [kablem USB do czasu wygaśnięcia](https://www.adafruit.com/product/954), zapoznaj się z przewodnikiem aktualizacji USB w przypadku sytuacji niestandardowych.

[comment]: # (Dodaj link do przewodnika aktualizacji USB, jeśli jest dostępny.)
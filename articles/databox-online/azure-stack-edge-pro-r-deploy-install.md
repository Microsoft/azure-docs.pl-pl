---
title: Samouczek instalacji Azure Stack Edge urządzenie fizyczne Pro R | Microsoft Docs
description: Drugi samouczek dotyczący instalowania programu Azure Stack EDGE Pro R obejmuje jak podłączyć urządzenie fizyczne do urządzeń i sieci.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro R in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: b67da2607d206424f69f53645eda148495ea58ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96468440"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>Samouczek: Instalowanie Azure Stack EDGE Pro R

W tym samouczku opisano sposób instalowania urządzenia fizycznego w programie Azure Stack Edge. Procedura instalacji obejmuje okablowanie urządzenia.

Instalacja może potrwać około 30 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Inspekcja urządzenia
> * Podłączanie kabli urządzenia

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące instalacji urządzenia fizycznego są następujące:

### <a name="for-the-azure-stack-edge-resource"></a>Dla zasobu brzegowego Azure Stack

Przed rozpoczęciem upewnij się, że:

* Wykonano wszystkie kroki z sekcji [przygotowanie do wdrożenia Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-prep.md).
    * Utworzono zasób Azure Stack Edge, aby wdrożyć urządzenie.
    * Klucz aktywacji został wygenerowany w celu aktywowania urządzenia przy użyciu zasobu brzegowego Azure Stack.

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>Na urządzeniu fizycznym programu Azure Stack EDGE Pro R

Przed wdrożeniem urządzenia:

- Upewnij się, że urządzenie zostało bezpiecznie umieszczone na płaskiej, stabilnej i poziomej powierzchni roboczej.
- Sprawdź, czy lokacja, w której chcesz zamontować urządzenie, ma:
    - standardowe zasilanie prądem przemiennym z niezależnego źródła

        — Lub —
    - Jednostka dystrybucji baterii (PDU). Urządzenie jest dostarczane z zasilaczem UPS
    

### <a name="for-the-network-in-the-datacenter"></a>Sieć w centrum danych

Przed rozpoczęciem:

- Zapoznaj się z wymaganiami dotyczącymi sieci w celu wdrożenia Azure Stack EDGE Pro R i skonfiguruj sieć centrum danych zgodnie z wymaganiami. Aby uzyskać więcej informacji, zobacz [Azure Stack Edge — wymagania dotyczące sieci w wersji Pro R](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements).

- Aby umożliwić optymalne działanie urządzenia, przepustowość połączenia internetowego musi wynosić co najmniej 20 Mb/s.


## <a name="inspect-the-device"></a>Inspekcja urządzenia

To urządzenie jest dostarczane jako pojedyncza jednostka. Aby rozpakować urządzenie, wykonaj następujące czynności.

1. Umieść pudełko na płaskiej, poziomej powierzchni.
2. Sprawdź przypadek urządzenia pod kątem ewentualnych szkód. Otwórz przypadek i sprawdź urządzenie. Jeśli przypadek lub urządzenie wydaje się być uszkodzone, skontaktuj się z firmą pomoc techniczna firmy Microsoft, aby pomóc ocenić, czy urządzenie jest w dobrym stanie.
3. Po otwarciu przypadku upewnij się, że masz:
    - Jedna obudowa Azure Stack Edge urządzenie R
    - Jedno zasilacz awaryjny (UPS)
    - 2 krótkie kable zasilające do łączenia urządzenia z zasilaczem UPS
    - 1 kabel sieciowy do połączenia zasilacza UPS ze źródłem napięcia

Jeśli wszystkie elementy wymienione w tym miejscu nie zostały odebrane, skontaktuj się z pomocą techniczną Azure Stack Edge. Następnym krokiem jest nawiązać połączenie z urządzeniem.


## <a name="cable-the-device"></a>Podłączanie kabli urządzenia

W poniższych procedurach opisano, jak podłączyć urządzenie do Azure Stack usługi Microsoft Edge Pro R w celu podłączenia do urządzeń i sieci.

Aby można było rozpocząć podłączanie kabli urządzenia, potrzebne są następujące elementy:

- Urządzenie fizyczne w programie Azure Stack Edge w witrynie instalacji.
- Jeden kabel sieciowy.
- Co najmniej jeden kabel sieciowy 1-GbE RJ-45 służący do łączenia z interfejsem zarządzania. Istnieją dwa interfejsy sieciowe 1-GbE: jeden do zarządzania i drugi stanowiący interfejs danych w urządzeniu.
- Jeden kabel 10/25-GbE SFP + miedziany dla każdego interfejsu sieciowego danych do skonfigurowania. Co najmniej jeden interfejs sieciowy danych między PORTem 3 lub 4 musi być połączony z Internetem (z łącznością z platformą Azure).  
- Dostęp do jednej jednostki dystrybucji (zalecane).

> [!NOTE]
> - Jeśli łączysz tylko jeden interfejs sieciowy danych, zalecamy użycie interfejsu sieciowego 25/10 GbE, takiego jak PORT 3 lub 4, aby wysyłać dane do platformy Azure. 
> - Aby uzyskiwać najlepszą wydajność i obsługiwać duże ilości danych, rozważ połączenie wszystkich portów danych.
> - Urządzenie Azure Stack EDGE Pro R powinno być połączone z siecią centrum danych, aby można było pozyskać dane z serwerów źródłowych.

Na urządzeniu Azure Stack EDGE Pro R:

- Panel przedni ma stacje dysków i przycisk potęgi.

    - Na początku urządzenia znajdują się 8 gniazd dyskowych.
    - Urządzenie ma również 2 X M. 2 dyski SATA, które są używane jako dyski systemu operacyjnego. 

- Płaszczyzna tylna obejmuje nadmiarowe jednostki zasilacza (PSUs).
- Płaszczyzna tylna ma cztery interfejsy sieciowe:

    - Interfejsy 2 1 GB/s.
    - Interfejsy 2 25 GB/s, które mogą również działać jako interfejsy 10 GB/s.
    - Kontroler zarządzania płytą główną (BMC).

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
Wykonaj poniższe kroki, aby podłączyć kable urządzenia do sieci i zasilania.

1. Zidentyfikuj różne porty na płaszczyźnie tylnej urządzenia.

    ![Płaszczyzna tylna urządzenia z kablem](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. Znajdź miejsca na dysku i przycisk energia na przedniej urządzeniu.

    ![Przedsunięta płaszczyzna urządzenia](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. Połącz jeden koniec przewodu z zasilaczem UPS. Podłącz drugi koniec przewodu zasilającego do jednostki dystrybucji baterii w stojaku (PDU). 
5. Naciśnij przycisk energia, aby włączyć urządzenie.
6. Połącz interfejs sieciowy 1 GbE PORT 1 z komputerem używanym do konfigurowania urządzenia fizycznego. PORT 1 jest dedykowanym interfejsem zarządzania.
7. Podłącz jeden lub więcej portów 2, 3 lub 4 do sieci centrum danych/Internetu.

    - W przypadku łączenia za pomocą portu PORT 2 użyj kabla sieciowego RJ-45.
    - W przypadku interfejsów sieciowych 10/25 GbE należy użyć kabli SFP + miedzianych.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje dotyczące Azure Stack Edge w języku R, jak:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia
> * Podłączanie kabli urządzenia

Przejdź do następnego samouczka, aby dowiedzieć się, jak nawiązać połączenie z urządzeniem.

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z Azure Stack EDGE Pro R](./azure-stack-edge-pro-r-deploy-connect.md)

---
title: Samouczek dotyczący instalowania programu Azure Stack Edge mini R urządzenie fizyczne | Microsoft Docs
description: Drugi samouczek dotyczący instalowania Azure Stack Edge mini R urządzenia obejmuje jak podłączyć urządzenie fizyczne do urządzeń i sieci.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Mini R device in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 4f7656337b12cf477c5c71d861d031919e0d55d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96468521"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>Samouczek: Instalowanie Azure Stack Edge mini R

W tym samouczku opisano sposób instalowania urządzenia fizycznego R Azure Stack Edge. Procedura instalacji obejmuje okablowanie urządzenia.

Instalacja może potrwać około 30 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Inspekcja urządzenia
> * Podłączanie kabli urządzenia

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące instalacji urządzenia fizycznego są następujące:

### <a name="for-the-azure-stack-edge-resource"></a>Dla zasobu brzegowego Azure Stack

Przed rozpoczęciem upewnij się, że:

* Wykonano wszystkie kroki z sekcji [przygotowanie do wdrożenia Azure Stack Edge mini R](azure-stack-edge-mini-r-deploy-prep.md).
    * Utworzono zasób Azure Stack Edge, aby wdrożyć urządzenie.
    * Klucz aktywacji został wygenerowany w celu aktywowania urządzenia przy użyciu zasobu brzegowego Azure Stack.

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>W przypadku urządzeń fizycznych w Azure Stack Edge mini R

Przed wdrożeniem urządzenia:

- Upewnij się, że urządzenie zostało bezpiecznie umieszczone na płaskiej, stabilnej i poziomej powierzchni roboczej.
- Sprawdź, czy lokacja, w której chcesz zamontować urządzenie, ma:
    - Standardowa moc AC z niezależnego źródła lub
    - Jednostka dystrybucji baterii (PDU). 
    

### <a name="for-the-network-in-the-datacenter"></a>Sieć w centrum danych

Przed rozpoczęciem:

- Zapoznaj się z wymaganiami dotyczącymi sieci w celu wdrożenia Azure Stack Edge mini R i skonfiguruj sieć centrum danych zgodnie z wymaganiami. Aby uzyskać więcej informacji, zobacz [Azure Stack Edge wymagania dotyczące sieci](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements).

- Upewnij się, że minimalna przepustowość internetowa to 20 MB/s w celu zapewnienia optymalnego działania urządzenia. <!-- engg TBC -->


## <a name="inspect-the-device"></a>Inspekcja urządzenia

To urządzenie jest dostarczane jako pojedyncza jednostka. Aby rozpakować urządzenie, wykonaj następujące czynności.

1. Umieść pudełko na płaskiej, poziomej powierzchni.
2. Sprawdź przypadek urządzenia pod kątem ewentualnych szkód. Otwórz przypadek i sprawdź urządzenie. Jeśli przypadek lub urządzenie wydaje się być uszkodzone, skontaktuj się z firmą pomoc techniczna firmy Microsoft, aby pomóc ocenić, czy urządzenie jest w dobrym stanie.
3. Po otwarciu przypadku upewnij się, że masz:
    - Jedno przenośne urządzenie mini R Azure Stack Edge z przyłączonymi zderzakami bocznymi
    - Jedna bateria i pokrycie tyłu dołączone do urządzenia. 
    - Jeden przewód zasilający, aby podłączyć baterię do źródła zasilania 

Jeśli wszystkie elementy wymienione w tym miejscu nie zostały odebrane, skontaktuj się z pomocą techniczną Azure Stack Edge. Następnym krokiem jest nawiązać połączenie z urządzeniem.


## <a name="cable-the-device"></a>Podłączanie kabli urządzenia

Poniższe procedury wyjaśniają, jak podłączyć urządzenie do usługi Azure Stack Edge w celu podłączenia do sieci.

Aby można było rozpocząć podłączanie kabli urządzenia, potrzebne są następujące elementy:

- Urządzenie fizyczne z systemem Azure Stack Edge w witrynie instalacji.
- Jeden kabel sieciowy.
- Co najmniej jeden kabel sieciowy 1-GbE RJ-45 służący do łączenia z interfejsem zarządzania. Istnieją dwa interfejsy sieciowe 1-GbE: jeden do zarządzania i drugi stanowiący interfejs danych w urządzeniu.
- 1 10-GbE SFP + kabel miedziany dla każdego interfejsu sieciowego danych do skonfigurowania. Co najmniej jeden interfejs sieciowy danych między PORTem 3 lub 4 musi być połączony z Internetem (z łącznością z platformą Azure).  
- Dostęp do jednej jednostki dystrybucji (zalecane).

> [!NOTE]
> - Jeśli łączysz tylko jeden interfejs sieciowy danych, zalecamy użycie interfejsu sieciowego 10 GbE, takiego jak PORT 3 lub 4, aby wysyłać dane do platformy Azure. 
> - Aby uzyskiwać najlepszą wydajność i obsługiwać duże ilości danych, rozważ połączenie wszystkich portów danych.
> - Urządzenie brzegowe Azure Stack powinno być połączone z siecią centrum danych, aby można było pozyskać dane z serwerów źródłowych. <!-- engg TBC -->

Na urządzeniu Azure Stack Edge:

- Panel przedni ma nośnik SSD. 

    - Urządzenie ma 1 dysk SSD w gnieździe. 
    - Urządzenie ma również kartę CFx, która służy jako magazyn dla dysku systemu operacyjnego.
    
- Panel przedni ma interfejsy sieciowe i dostęp do sieci Wi-Fi.

    - 2 X 1 GbE RJ 45 interfejsy sieciowe. Są to porty 1 i 2 dla lokalnego interfejsu użytkownika urządzenia.
    - 2 X 10 GbE SFP + interfejsy sieciowe. Są to porty 3 i 4 dla lokalnego interfejsu użytkownika urządzenia. 
    - Jeden Wi-Fi port z dołączonym odbiornikiem Wi-Fi.

- Panel przedni ma również przycisk potęgi. 

- Panel wstecz zawiera baterie i pokrycie, które są zainstalowane na urządzeniu. 


Wykonaj poniższe kroki, aby podłączyć kable urządzenia do sieci i zasilania.

1. Zidentyfikuj różne składniki sieci i magazynu na osi przedniej urządzenia.

    ![Interfejsy sieci i magazynu na urządzeniu](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. Znajdź przycisk energia w lewym dolnym rogu urządzenia. 

    ![Przedsunięta płaszczyzna urządzenia za pomocą przycisku włączania na urządzeniu](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. Bateria jest połączona z płaszczyzną tylną urządzenia. Określ drugi przycisk zasilania znajdujący się w baterii. 

    ![Przedsunięta płaszczyzna urządzenia za pomocą przycisku zasilania w baterii](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    Podłącz jeden koniec przewodu zasilania do baterii, a drugi z gniazdem zasilania. 

    ![Połączenie z przewódem zasilającym](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    W przypadku uruchamiania tylko w baterii (bateria nie jest połączona ze źródłem zasilania), zarówno przełącznik zasilania na wierzchu, jak i przełącznik na baterii należy przełączyć na pozycję na pozycji. Gdy bateria jest połączona ze źródłem zasilania, należy przełączyć tylko przycisk zasilania z przodu urządzenia do pozycji Wł. 

4. Naciśnij przycisk energia na płaszczyźnie przedniej, aby włączyć urządzenie. 
    
    > [!NOTE]
    > Aby włączyć lub wyłączyć zasilanie urządzenia, należy polecić czarny przycisk w górnej części przycisku zasilanie, a następnie włączyć lub wyłączyć przycisk Zasilanie. 

5. W przypadku konfigurowania Wi-Fi na tym urządzeniu należy użyć następującego diagramu okablowania:

    ![Okablowanie dla Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - Połącz interfejs sieciowy 1 GbE PORT 1 z komputerem używanym do konfigurowania urządzenia fizycznego. PORT 1 jest dedykowanym interfejsem zarządzania.


    Jeśli używasz konfiguracji przewodowej dla tego urządzenia, użyj następującego diagramu:
     
    ![Okablowanie dla sieci przewodowej](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - Połącz interfejs sieciowy 1 GbE PORT 1 z komputerem używanym do konfigurowania urządzenia fizycznego. PORT 1 jest dedykowanym interfejsem zarządzania.
    - Podłącz jeden lub więcej portów 2, 3 lub 4 do sieci centrum danych/Internetu.
    
        - W przypadku łączenia za pomocą portu PORT 2 użyj kabla sieciowego RJ-45.
        - W przypadku interfejsów sieciowych 10 GbE należy użyć kabli SFP + miedzianych.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje dotyczące Azure Stack krawędzi, takich jak:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia
> * Podłączanie kabli urządzenia

Przejdź do następnego samouczka, aby dowiedzieć się, jak nawiązać połączenie z urządzeniem oraz je skonfigurować i aktywować.

> [!div class="nextstepaction"]
> [Łączenie i Konfigurowanie Azure Stack Edge](./azure-stack-edge-mini-r-deploy-connect.md)

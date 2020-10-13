---
title: Samouczek dotyczący nawiązywania połączenia, konfigurowania i aktywowania urządzenia Azure Stack EDGE Pro z procesorem GPU w Azure Portal | Microsoft Docs
description: Samouczek umożliwiający wdrożenie procesora GPU w programie Azure Stack Edge powoduje nawiązanie połączenia, skonfigurowanie i aktywowanie urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: dd4078d414048d858c46f6757daa0a87a698eaab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976083"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-pro-with-gpu"></a>Samouczek: Konfigurowanie ustawień urządzenia dla Azure Stack brzeg Pro z procesorem GPU

W tym samouczku opisano sposób konfigurowania ustawień związanych z urządzeniami dla urządzeń Azure Stack EDGE Pro przy użyciu procesora GPU. Można skonfigurować nazwę urządzenia, serwer aktualizacji i serwer czasu za pomocą lokalnego interfejsu użytkownika sieci Web.

Ukończenie ustawień urządzenia może potrwać około 5-7 minut.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfiguruj ustawienia urządzenia
> * Konfigurowanie aktualizacji 
> * Skonfiguruj czas

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem ustawień związanych z urządzeniami na urządzeniu Azure Stack EDGE Pro z procesorem GPU upewnij się, że:

* Dla urządzenia fizycznego:

    - Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-install.md).
    - Skonfigurowano sieć i włączono i skonfigurowano sieć obliczeniową na urządzeniu zgodnie z opisem w [samouczku: Konfigurowanie sieci dla Azure Stack brzeg Pro z procesorem GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).


## <a name="configure-device-settings"></a>Konfiguruj ustawienia urządzenia

Wykonaj następujące kroki, aby skonfigurować ustawienia dotyczące urządzeń:

1. Na stronie **urządzenie** wykonaj następujące czynności:

    1. Wprowadź przyjazną nazwę urządzenia. Przyjazna nazwa musi zawierać od 1 do 13 znaków i może mieć literę, cyfry i łączniki.

    2. Podaj **domenę DNS** dla swojego urządzenia. Ta domena służy do konfigurowania urządzenia jako serwera plików.

    3. Aby sprawdzić poprawność i zastosować skonfigurowane ustawienia urządzenia, wybierz pozycję **Zastosuj**.

        ![Strona "urządzenie" lokalnego interfejsu użytkownika sieci Web 1](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

        Jeśli zmieniono nazwę urządzenia i domenę DNS, automatycznie Wygenerowano certyfikaty z podpisem własnym na urządzeniu. Podczas konfigurowania certyfikatów należy wybrać jedną z następujących opcji: 
        
        - Generuj i pobieraj certyfikaty urządzeń. 
        - Przenieś własne certyfikaty do urządzenia, w tym łańcuch podpisywania.
    

        ![Lokalny interfejs użytkownika sieci Web — Strona 2](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-3.png)

    4. Po zmianie nazwy urządzenia i domeny DNS zostaje utworzony punkt końcowy SMB.  

    5. Po zastosowaniu ustawień wybierz pozycję **Dalej: serwer aktualizacji**.

        ![Lokalny interfejs użytkownika sieci Web — Strona 3](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-4.png)

## <a name="configure-update"></a>Konfigurowanie aktualizacji

1. Na stronie **Aktualizacja** można teraz skonfigurować lokalizację, z której mają zostać pobrane aktualizacje dla urządzenia.  

    - Aktualizacje można pobrać bezpośrednio z **serwera Microsoft Update**.

        ![Strona lokalnego interfejsu użytkownika sieci Web "serwer aktualizacji"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-2.png)

        Możesz również wybrać opcję wdrożenia aktualizacji z programu **Windows Server Update Services** (WSUS). Podaj ścieżkę do serwera programu WSUS.
        
        ![Lokalny interfejs użytkownika sieci Web "serwer aktualizacji" — Strona 2](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > Jeśli jest skonfigurowany oddzielny serwer Windows Update i w przypadku wybrania opcji nawiązywania połączenia za pośrednictwem *protokołu HTTPS* (zamiast *protokołu HTTP*) wymagane jest Podpisywanie certyfikatów łańcucha wymaganych do nawiązania połączenia z serwerem aktualizacji. Aby uzyskać informacje na temat tworzenia i przekazywania certyfikatów, przejdź do obszaru [Zarządzanie certyfikatami](azure-stack-edge-j-series-manage-certificates.md). 

2. Wybierz przycisk **Zastosuj**.
3. Po skonfigurowaniu serwera aktualizacji wybierz pozycję **Dalej: czas**.
    

## <a name="configure-time"></a>Skonfiguruj czas

Wykonaj następujące kroki, aby skonfigurować ustawienia czasu na urządzeniu. 

> [!IMPORTANT]
> Chociaż ustawienia czasu są opcjonalne, zdecydowanie zalecamy skonfigurowanie podstawowego NTP i pomocniczego serwera NTP w sieci lokalnej dla urządzenia. Jeśli serwer lokalny jest niedostępny, można skonfigurować publiczne serwery NTP.

Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby można było uwierzytelnić się z dostawcami usług w chmurze.

1. Na stronie **Time (czas** ) można wybrać strefę czasową oraz podstawowe i pomocnicze serwery NTP dla urządzenia.  
    
    1. Z listy rozwijanej **strefa czasowa** wybierz strefę czasową odpowiadającą lokalizacji geograficznej, w której jest wdrażane urządzenie.
        Domyślna strefa czasowa urządzenia to PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.

    2. W polu **podstawowy serwer NTP** wprowadź serwer podstawowy dla urządzenia lub Zaakceptuj wartość domyślną Time.Windows.com.  
        Upewnij się, że sieć zezwala na przekazywanie ruchu NTP z centrum danych do Internetu.

    3. Opcjonalnie w polu **pomocniczy serwer NTP** wprowadź serwer pomocniczy dla urządzenia.

    4. Aby sprawdzić poprawność i zastosować skonfigurowane ustawienia czasu, wybierz pozycję **Zastosuj**.

        ![Strona "godzina" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-2.png)

2. Po zastosowaniu ustawień wybierz pozycję **Dalej: certyfikaty**.


## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfiguruj ustawienia urządzenia
> * Konfigurowanie aktualizacji 
> * Skonfiguruj czas

Aby dowiedzieć się, jak skonfigurować certyfikaty dla urządzenia z Azure Stack EDGE Pro, zobacz:

> [!div class="nextstepaction"]
> [Konfigurowanie certyfikatów](./azure-stack-edge-gpu-deploy-configure-certificates.md)

---
title: Samouczek umożliwiający nawiązanie połączenia z usługą, skonfigurowanie, aktywowanie Azure Stack urządzenia brzegowego za pomocą procesora GPU w Azure Portal | Microsoft Docs
description: Samouczek wdrażania Azure Stack Edge powoduje nawiązanie połączenia, skonfigurowanie i aktywowanie urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/11/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: cfafb47e6d5fcde2858e7329b5b8ef3a0e0b241e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89088018"
---
# <a name="tutorial-configure-device-settings-for-azure-stack-edge-with-gpu"></a>Samouczek: Konfigurowanie ustawień urządzenia dla Azure Stack Edge z procesorem GPU

W tym samouczku opisano sposób konfigurowania ustawień związanych z urządzeniami dla Azure Stack urządzenia brzegowego za pomocą dołączania procesora GPU. Można skonfigurować nazwę urządzenia, serwer aktualizacji i serwer czasu za pomocą lokalnego interfejsu użytkownika sieci Web.

Ukończenie ustawień urządzenia może potrwać około 5-7 minut.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfiguruj ustawienia urządzenia
> * Konfigurowanie aktualizacji 
> * Skonfiguruj czas

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem ustawień związanych z urządzeniami na urządzeniu brzegowym Azure Stack z procesorem GPU upewnij się, że:

* Dla urządzenia fizycznego:

    - Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
    - Skonfigurowano sieć i włączono i skonfigurowano sieć obliczeniową na urządzeniu zgodnie z opisem w [samouczku: Konfigurowanie sieci do Azure Stack Edge z procesorem GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).


## <a name="configure-device-settings"></a>Konfiguruj ustawienia urządzenia

Wykonaj następujące kroki, aby skonfigurować ustawienia dotyczące urządzeń.
 
1. Na kafelku **Konfiguracja urządzenia** wybierz pozycję **Device** **Konfiguruj**.

    ![Strona lokalnego interfejsu użytkownika sieci Web "urządzenie"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-1.png)

    Na stronie **urządzenie** wykonaj następujące czynności:

    1. Wprowadź przyjazną nazwę urządzenia. Przyjazna nazwa musi zawierać od 1 do 13 znaków i może mieć literę, cyfry i łączniki.

    2. Podaj **domenę DNS** dla swojego urządzenia. Ta domena służy do konfigurowania urządzenia jako serwera plików.

    3. Aby sprawdzić poprawność i zastosować skonfigurowane ustawienia urządzenia, wybierz pozycję **Zastosuj**.

        ![Strona lokalnego interfejsu użytkownika sieci Web "urządzenie"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

        Jeśli zmieniono nazwę urządzenia i domenę DNS, automatycznie Wygenerowano certyfikaty z podpisem własnym na urządzeniu. Należy wybrać jedną z następujących opcji: 
        
        - Generuj i pobieraj certyfikaty urządzeń. 
        - Przenieś własne certyfikaty do urządzenia, w tym łańcuch podpisywania.

        ![Strona lokalnego interfejsu użytkownika sieci Web "urządzenie"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-3.png)

    4. Po zmianie nazwy urządzenia i domeny DNS są tworzone punkty końcowe SMB i NFS. 

    4. Po zastosowaniu ustawień Wróć do pozycji **wprowadzenie**.

## <a name="configure-update"></a>Konfigurowanie aktualizacji

1. Na kafelku **Konfiguracja urządzenia** wybierz pozycję **Update** **Konfiguruj**. Teraz można skonfigurować lokalizację, z której mają zostać pobrane aktualizacje dla urządzenia.  

    ![Strona lokalnego interfejsu użytkownika sieci Web "serwer aktualizacji"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-1.png)

    - Aktualizacje można pobrać bezpośrednio z **serwera Microsoft Update**.

        ![Strona lokalnego interfejsu użytkownika sieci Web "serwer aktualizacji"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-2.png)

        Możesz również wybrać opcję wdrożenia aktualizacji z programu **Windows Server Update Services** (WSUS). Podaj ścieżkę do serwera programu WSUS.
        
        ![Strona lokalnego interfejsu użytkownika sieci Web "serwer aktualizacji"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > Jeśli jest skonfigurowany oddzielny serwer Windows Update i w przypadku wybrania opcji nawiązywania połączenia za pośrednictwem *protokołu HTTPS* (zamiast *protokołu HTTP*) wymagane jest Podpisywanie certyfikatów łańcucha wymaganych do nawiązania połączenia z serwerem aktualizacji. Aby uzyskać informacje na temat tworzenia i przekazywania certyfikatów, przejdź do obszaru [Zarządzanie certyfikatami](azure-stack-edge-j-series-manage-certificates.md). 

2. Wybierz przycisk **Zastosuj**.
3. Po skonfigurowaniu serwera aktualizacji wróć do pozycji **wprowadzenie**.
    

## <a name="configure-time"></a>Skonfiguruj czas

Wykonaj następujące kroki, aby skonfigurować ustawienia czasu na urządzeniu. 

1. Na kafelku **Konfiguracja urządzenia** wybierz pozycję **czas**. Można wybrać strefę czasową oraz podstawowy i pomocniczy serwer NTP dla urządzenia.  

    > [!IMPORTANT]
    > Chociaż ustawienia czasu są opcjonalne, zdecydowanie zalecamy skonfigurowanie podstawowego NTP i pomocniczego serwera NTP w sieci lokalnej dla urządzenia. Jeśli serwer lokalny jest niedostępny, można skonfigurować publiczne serwery NTP.
    
    Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby można było uwierzytelnić się z dostawcami usług w chmurze.

    ![Strona "godzina" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-1.png)

2. Na stronie **czas** wykonaj następujące czynności:
    
    1. Z listy rozwijanej **strefa czasowa** wybierz strefę czasową odpowiadającą lokalizacji geograficznej, w której jest wdrażane urządzenie.
        Domyślna strefa czasowa urządzenia to PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.

    2. W polu **podstawowy serwer NTP** wprowadź serwer podstawowy dla urządzenia lub Zaakceptuj wartość domyślną Time.Windows.com.  
        Upewnij się, że sieć zezwala na przekazywanie ruchu NTP z centrum danych do Internetu.

    3. Opcjonalnie w polu **pomocniczy serwer NTP** wprowadź serwer pomocniczy dla urządzenia.

    4. Aby sprawdzić poprawność i zastosować skonfigurowane ustawienia czasu, wybierz pozycję **Zastosuj**.

        ![Strona "godzina" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-2.png)

3. Po zastosowaniu ustawień Wróć do pozycji **wprowadzenie**.



## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfiguruj ustawienia urządzenia
> * Konfigurowanie aktualizacji 
> * Skonfiguruj czas

Aby dowiedzieć się, jak skonfigurować certyfikaty dla Azure Stackego urządzenia brzegowego, zobacz:

> [!div class="nextstepaction"]
> [Konfigurowanie certyfikatów](./azure-stack-edge-gpu-deploy-configure-certificates.md)

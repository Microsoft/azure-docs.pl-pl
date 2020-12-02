---
title: Samouczek dotyczący konfigurowania urządzenia, aktualizacji, godziny Azure Stack urządzenia Mini R w usłudze Azure Portal
description: Samouczek dotyczący wdrażania Azure Stack Edge mini R powoduje skonfigurowanie ustawień urządzenia, aktualizacji i czasu dla urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: ee3805d128a7b6d122f93e692291db1a387cfcf5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468492"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-mini-r"></a>Samouczek: Konfigurowanie ustawień urządzenia dla Azure Stack krawędź mini R

W tym samouczku opisano sposób konfigurowania ustawień związanych z urządzeniami dla urządzeń Azure Stack Edge mini R przy użyciu procesora GPU. Można skonfigurować nazwę urządzenia, serwer aktualizacji i serwer czasu za pomocą lokalnego interfejsu użytkownika sieci Web.

Ukończenie ustawień urządzenia może potrwać około 5-7 minut.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfiguruj ustawienia urządzenia
> * Konfigurowanie aktualizacji 
> * Skonfiguruj czas

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem ustawień związanych z urządzeniami na urządzeniu z systemem Azure Stack Edge mini R z procesorem GPU upewnij się, że:

* Dla urządzenia fizycznego:

    - Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack Edge mini R ](azure-stack-edge-mini-r-deploy-install.md).
    - Skonfigurowano sieć i włączono i skonfigurowano sieć obliczeniową na urządzeniu zgodnie z opisem w [samouczku: Konfigurowanie sieci dla Azure Stack Edge mini R z procesorem GPU](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).


## <a name="configure-device-settings"></a>Konfiguruj ustawienia urządzenia

Wykonaj następujące kroki, aby skonfigurować ustawienia dotyczące urządzeń:

1. Na stronie **urządzenie** wykonaj następujące czynności:

    1. Wprowadź przyjazną nazwę urządzenia. Przyjazna nazwa musi zawierać od 1 do 13 znaków i może mieć literę, cyfry i łączniki.

    2. Podaj **domenę DNS** dla swojego urządzenia. Ta domena służy do konfigurowania urządzenia jako serwera plików.

    3. Aby sprawdzić poprawność i zastosować skonfigurowane ustawienia urządzenia, wybierz pozycję **Zastosuj**.

        ![Strona "urządzenie" lokalnego interfejsu użytkownika sieci Web 1](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-1.png)

        Jeśli zmieniono nazwę urządzenia i domenę DNS, certyfikaty z podpisem własnym, które istniały na urządzeniu, nie będą działały. 

        ![Lokalny interfejs użytkownika sieci Web — Strona 2](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-2.png)

        Podczas konfigurowania certyfikatów należy wybrać jedną z następujących opcji: 
        
        - Generuj i pobieraj certyfikaty urządzeń. 
        - Przenieś własne certyfikaty do urządzenia, w tym łańcuch podpisywania.
    

    4. Po zmianie nazwy urządzenia i domeny DNS zostaje utworzony punkt końcowy SMB.  

        ![Lokalny interfejs użytkownika sieci Web — Strona 3](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-3.png)

    5. Po zastosowaniu ustawień wybierz pozycję **Dalej: serwer aktualizacji**.


## <a name="configure-update"></a>Konfigurowanie aktualizacji

1. Na stronie **Aktualizacja** można teraz skonfigurować lokalizację, z której mają zostać pobrane aktualizacje dla urządzenia.  

    - Aktualizacje można pobrać bezpośrednio z **serwera Microsoft Update**.

        ![Strona lokalnego interfejsu użytkownika sieci Web "serwer aktualizacji"](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/update-server-1.png)

        Możesz również wybrać opcję wdrożenia aktualizacji z programu **Windows Server Update Services** (WSUS). Podaj ścieżkę do serwera programu WSUS.
        
        ![Lokalny interfejs użytkownika sieci Web "serwer aktualizacji" — Strona 2](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/update-server-2.png)

        > [!NOTE] 
        > Jeśli jest skonfigurowany oddzielny serwer Windows Update i w przypadku wybrania opcji nawiązywania połączenia za pośrednictwem *protokołu HTTPS* (zamiast *protokołu HTTP*) wymagane jest Podpisywanie certyfikatów łańcucha wymaganych do nawiązania połączenia z serwerem aktualizacji. Aby uzyskać informacje na temat tworzenia i przekazywania certyfikatów, przejdź do obszaru [Zarządzanie certyfikatami](azure-stack-edge-j-series-manage-certificates.md). Aby pracować w trybie rozłączonym, takim jak Azure Stackowanie warstwy urządzenia brzegowego w modularne centrum danych, Włącz opcję usług WSUS. Podczas aktywacji urządzenie skanuje w poszukiwaniu aktualizacji i jeśli serwer nie jest skonfigurowany, aktywacja zakończy się niepowodzeniem. 

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

        ![Strona "godzina" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/time-settings-1.png)

2. Po zastosowaniu ustawień wybierz pozycję **Dalej: certyfikaty**.


## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfiguruj ustawienia urządzenia
> * Konfigurowanie aktualizacji 
> * Skonfiguruj czas

Aby dowiedzieć się, jak skonfigurować certyfikaty dla urządzenia z usługą Azure Stack Edge mini R, zobacz:

> [!div class="nextstepaction"]
> [Konfigurowanie certyfikatów](./azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)

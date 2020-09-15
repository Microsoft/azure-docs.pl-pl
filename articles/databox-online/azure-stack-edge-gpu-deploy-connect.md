---
title: Samouczek umożliwiający nawiązanie połączenia z usługą, skonfigurowanie, aktywowanie Azure Stack urządzenia brzegowego za pomocą procesora GPU w Azure Portal | Microsoft Docs
description: Dowiedz się, w jaki sposób można nawiązać połączenie z urządzeniem z Azure Stack Edge przy użyciu interfejsu GPU w sieci lokalnej.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: ac3ba2583e0f9a42b7f139538eee04de8980b492
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532260"
---
# <a name="tutorial-connect-to-azure-stack-edge-with-gpu"></a>Samouczek: łączenie się z Azure Stack Edge z procesorem GPU

W tym samouczku opisano, jak nawiązać połączenie z urządzeniem usługi Azure Stack Edge przy użyciu interfejsu GPU w sieci lokalnej.

Proces połączenia może potrwać około 5 minut.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem fizycznym


## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia Azure Stack Edge z procesorem GPU upewnij się, że:

* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Nawiązywanie połączenia z konfiguracją lokalnego interfejsu użytkownika sieci Web

1. Skonfiguruj kartę Ethernet na komputerze, aby nawiązać połączenie z urządzeniem Azure Stack Edge ze statycznym adresem IP 192.168.100.5 i podsiecią 255.255.255.0.

2. Podłącz komputer do portu 1 na urządzeniu. Jeśli komputer jest połączony bezpośrednio z urządzeniem (bez przełącznika), użyj kabla skrzyżowanego lub karty Ethernet USB. Użyj poniższej ilustracji, aby zidentyfikować PORT 1 na urządzeniu.

    ![Płyta montażowa okablowanego urządzenia](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    Plan przedplanu urządzenia może wyglądać nieco inaczej w zależności od modelu, który otrzymasz. Aby uzyskać więcej informacji, zobacz [kabel urządzenia](azure-stack-edge-gpu-deploy-install.md#cable-the-device).


3. Otwórz okno przeglądarki i uzyskaj dostęp do lokalnego internetowego interfejsu użytkownika urządzenia pod adresem `https://192.168.100.10`.  
    Ta akcja może potrwać kilka minut po włączeniu urządzenia.

    Zostanie wyświetlony komunikat o błędzie lub ostrzeżenie informujące o problemie z certyfikatem zabezpieczeń witryny internetowej. 
   
    ![Komunikat o błędzie dotyczący certyfikatu zabezpieczeń witryny sieci Web](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Wybierz pozycję **Kontynuuj na tej stronie sieci Web**.  
    Te kroki mogą się różnić w zależności od używanej przeglądarki.

5. Zaloguj się do internetowego interfejsu użytkownika urządzenia. Domyślne hasło to *Password1*. 
   
    ![Strona logowania do urządzenia brzegowego Azure Stack](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. W wierszu polecenia Zmień hasło administratora urządzenia.  
    Nowe hasło musi zawierać od 8 do 16 znaków. Musi zawierać trzy z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne.

Jesteś teraz na stronie **Przegląd** Twojego urządzenia. Następnym krokiem jest skonfigurowanie ustawień sieci dla urządzenia.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem fizycznym


Aby dowiedzieć się, jak skonfigurować ustawienia sieci na urządzeniu brzegowym Azure Stack, zobacz:

> [!div class="nextstepaction"]
> [Konfigurowanie sieci](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)

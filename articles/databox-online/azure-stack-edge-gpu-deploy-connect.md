---
title: Samouczek dotyczący nawiązywania połączenia z usługą, konfigurowania i aktywowania urządzenia Azure Stack EDGE Pro z procesorem GPU w Azure Portal | Microsoft Docs
description: Dowiedz się, w jaki sposób można nawiązać połączenie z urządzeniem z Azure Stack Edge przy użyciu interfejsu GPU w sieci lokalnej.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: f069f711ccf5672041ea6f3b15a52fa2b3ba2022
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900035"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-with-gpu"></a>Samouczek: Nawiązywanie połączenia z usługą Azure Stack EDGE Pro z procesorem GPU

W tym samouczku opisano, jak nawiązać połączenie z urządzeniem z programem Azure Stack EDGE Pro przy użyciu interfejsu GPU w sieci lokalnej.

Proces połączenia może potrwać około 5 minut.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem fizycznym


## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia Azure Stack EDGE Pro przy użyciu procesora GPU upewnij się, że:

* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Nawiązywanie połączenia z konfiguracją lokalnego interfejsu użytkownika sieci Web

1. Skonfiguruj kartę Ethernet na komputerze, aby nawiązać połączenie z urządzeniem Azure Stack EDGE Pro ze statycznym adresem IP 192.168.100.5 i podsiecią 255.255.255.0.

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
   
    ![Strona logowania do urządzenia z systemem Azure Stack Edge](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. W wierszu polecenia Zmień hasło administratora urządzenia.  
    Nowe hasło musi zawierać od 8 do 16 znaków. Musi zawierać trzy z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne.

Jesteś teraz na stronie **Przegląd** Twojego urządzenia. Następnym krokiem jest skonfigurowanie ustawień sieci dla urządzenia.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem fizycznym


Aby dowiedzieć się, jak skonfigurować ustawienia sieci na urządzeniu Azure Stack EDGE Pro, zobacz:

> [!div class="nextstepaction"]
> [Konfigurowanie sieci](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)

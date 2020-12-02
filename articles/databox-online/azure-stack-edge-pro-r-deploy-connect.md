---
title: Samouczek umożliwiający nawiązanie połączenia z usługą, skonfigurowanie i aktywowanie urządzenia Azure Stack EDGE Pro R w usłudze Azure Portal | Microsoft Docs
description: Dowiedz się, w jaki sposób można nawiązać połączenie z urządzeniem Azure Stack EDGE Pro przy użyciu lokalnego interfejsu użytkownika sieci Web.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: ce97c22cf4bfbe5cca01183574597706a8c239e7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468443"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-r"></a>Samouczek: Nawiązywanie połączenia z Azure Stack EDGE Pro R

W tym samouczku opisano, jak nawiązać połączenie z urządzeniem Azure Stack EDGE Pro R przy użyciu lokalnego interfejsu użytkownika sieci Web.

Proces połączenia może potrwać około 5 minut.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem fizycznym


## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia z systemem Azure Stack EDGE Pro R upewnij się, że:

* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Nawiązywanie połączenia z konfiguracją lokalnego interfejsu użytkownika sieci Web

1. Skonfiguruj kartę Ethernet na komputerze, aby połączyć się z urządzeniem Azure Stack EDGE Pro R ze statycznym adresem IP 192.168.100.5 i podsiecią 255.255.255.0.

2. Podłącz komputer do portu 1 na urządzeniu. Jeśli komputer jest połączony bezpośrednio z urządzeniem (bez przełącznika), użyj kabla skrzyżowanego lub karty Ethernet USB. Użyj poniższej ilustracji, aby zidentyfikować PORT 1 na urządzeniu.

    ![Płyta montażowa okablowanego urządzenia](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)


3. Otwórz okno przeglądarki i uzyskaj dostęp do lokalnego internetowego interfejsu użytkownika urządzenia pod adresem `https://192.168.100.10`.  
    Ta akcja może potrwać kilka minut po włączeniu urządzenia.

    Zostanie wyświetlony komunikat o błędzie lub ostrzeżenie informujące o problemie z certyfikatem zabezpieczeń witryny internetowej. 
   
    ![Komunikat o błędzie dotyczący certyfikatu zabezpieczeń witryny sieci Web](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-1.png)


4. Wybierz pozycję **Kontynuuj na tej stronie sieci Web**.  
    Te kroki mogą się różnić w zależności od używanej przeglądarki.

5. Zaloguj się do internetowego interfejsu użytkownika urządzenia. Domyślne hasło to *Password1*. 
   
    ![Strona logowania do urządzenia brzegowego Azure Stack](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-3.png)

6. W wierszu polecenia Zmień hasło administratora urządzenia.  
    Nowe hasło musi zawierać od 8 do 16 znaków. Musi zawierać trzy z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne.

Jesteś teraz na stronie **Przegląd** Twojego urządzenia. Następnym krokiem jest skonfigurowanie ustawień sieci dla urządzenia.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem fizycznym


Aby dowiedzieć się, jak skonfigurować ustawienia sieciowe na urządzeniu Azure Stack EDGE Pro R, zobacz:

> [!div class="nextstepaction"]
> [Konfigurowanie sieci](./azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)

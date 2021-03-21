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
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96468443"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-r"></a>Samouczek: Nawiązywanie połączenia z Azure Stack EDGE Pro R

W tym samouczku opisano, jak nawiązać połączenie z urządzeniem Azure Stack EDGE Pro R przy użyciu lokalnego interfejsu użytkownika sieci Web.

Proces nawiązywania połączenia może potrwać około 5 minut.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem fizycznym


## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia z systemem Azure Stack EDGE Pro R upewnij się, że:

* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Nawiązywanie połączenia z konfiguracją lokalnego interfejsu użytkownika sieci Web

1. Skonfiguruj kartę Ethernet na komputerze używanym do nawiązywania połączenia z urządzeniem Azure Stack Edge Pro R za pomocą statycznego adresu IP 192.168.100.5 i podsieci 255.255.255.0.

2. Podłącz komputer do PORTU 1 na urządzeniu. W przypadku bezpośredniego podłączenia komputera do urządzenia (bez przełącznika) użyj kabla z przeplotem lub przejściówki USB Ethernet. Skorzystaj z poniższej ilustracji, aby zidentyfikować PORT 1 na urządzeniu.

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
    Nowe hasło musi zawierać od 8 do 16 znaków. Hasło musi zawierać trzy z następujących rodzajów znaków: małe litery, wielkie litery, cyfry i znaki specjalne.

Jesteś teraz na stronie **Przegląd** Twojego urządzenia. Następnym krokiem jest skonfigurowanie ustawień sieci dla urządzenia.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem fizycznym


Aby dowiedzieć się, jak skonfigurować ustawienia sieciowe na urządzeniu Azure Stack EDGE Pro R, zobacz:

> [!div class="nextstepaction"]
> [Konfigurowanie sieci](./azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)

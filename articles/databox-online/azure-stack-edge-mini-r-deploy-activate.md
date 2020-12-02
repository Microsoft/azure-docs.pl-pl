---
title: Samouczek służący do szyfrowania i aktywowania Azure Stack Edge mini R urządzenia w Azure Portal | Microsoft Docs
description: Samouczek służący do wdrażania Azure Stack Edge mini R instruuje użytkownika o zaszyfrowaniu i aktywowaniu urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: e5076888e692ad16e89f30d455e7f8c4e22a41b9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468570"
---
# <a name="tutorial-activate-azure-stack-edge-mini-r"></a>Samouczek: aktywowanie Azure Stack Edge mini R

W tym samouczku opisano, jak można aktywować urządzenie w usłudze Azure Stack Edge mini R przy użyciu lokalnego interfejsu użytkownika sieci Web.

Proces aktywacji może potrwać około 15 minut.

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Aktywuj urządzenie fizyczne

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia z systemem Azure Stack Edge mini R upewnij się, że:

* Dla urządzenia fizycznego: 
    
    - Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack Edge mini R](azure-stack-edge-mini-r-deploy-install.md).
    - Skonfigurowano sieć i ustawienia sieci obliczeniowej zgodnie z opisem w temacie [Konfigurowanie sieci, Sieć obliczeniowa, serwer proxy sieci Web](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
    - Twoje własne certyfikaty zostały przekazane na urządzeniu w przypadku zmiany nazwy urządzenia lub domeny DNS za pośrednictwem strony **urządzenia** . Te kroki opisano szczegółowo w temacie [Konfigurowanie certyfikatów, sieci VPN i szyfrowania](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md). Jeśli ten krok nie został wykonany, aktywacja zostanie zablokowana.
    - Skonfigurowano szyfrowanie na urządzeniu. Jeśli ten krok nie został wykonany, podczas aktywacji urządzenia zostanie wyświetlony komunikat o błędzie, a aktywacja zostanie zablokowana. Aby uzyskać więcej informacji, przejdź do [pozycji Konfigurowanie certyfikatów, sieci VPN i szyfrowania](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md).
    
* Masz klucz aktywacji z usługi Azure Stack Edge, która została utworzona w celu zarządzania urządzeniem z systemem Azure Stack Edge mini R. Aby uzyskać więcej informacji, przejdź do pozycji [przygotowanie do wdrożenia Azure Stack Edge mini R](azure-stack-edge-mini-r-deploy-prep.md).


## <a name="activate-the-device"></a>Aktywuj urządzenie

1. W lokalnym interfejsie użytkownika sieci Web urządzenia **Przejdź na stronę wprowadzenie.**
2. Na kafelku **Aktywacja** wybierz pozycję **Aktywuj**. 

    ![Lokalny interfejs użytkownika sieci Web "Szczegóły chmury" Strona 1](./media/azure-stack-edge-mini-r-deploy-activate/activation-1.png)
    
3. W okienku **Aktywuj** :
    1. Wprowadź **klucz aktywacji** uzyskany w polu [Pobierz klucz aktywacji dla Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. Możesz włączyć funkcję aktywnej kolekcji dzienników, aby umożliwić firmie Microsoft zbieranie dzienników na podstawie stanu kondycji urządzenia. Dzienniki zebrane w ten sposób są przekazywane do konta usługi Azure Storage.
    
    1. Wybierz przycisk **Zastosuj**.

    ![Lokalny interfejs użytkownika sieci Web "Szczegóły chmury" Strona 2](./media/azure-stack-edge-mini-r-deploy-activate/activation-2.png)


5. Pierwsze urządzenie zostało aktywowane. Zostanie wyświetlony monit o pobranie pliku klucza.
    
    ![Lokalny interfejs użytkownika sieci Web "Szczegóły chmury" Strona 3](./media/azure-stack-edge-mini-r-deploy-activate/activation-3.png)
    
    Wybierz pozycję **Pobierz i Kontynuuj** i Zapisz *device-serial-no.jsw* pliku w bezpiecznej lokalizacji poza urządzeniem. **Ten plik klucza zawiera klucze odzyskiwania dla dysku systemu operacyjnego i dysków z danymi na urządzeniu**. Te klucze mogą być konieczne, aby ułatwić przyszłe odzyskiwanie systemu.

    Poniżej przedstawiono zawartość pliku *JSON* :

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "SEDEncryptionExternalKey": "xZM/vC7GxjqHZ3VMo7xSs/wH9rRsF/PNM+mOsZ+GaL0=",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```        
 
    W poniższej tabeli objaśniono różne klucze:
    
    |Pole  |Opis  |
    |---------|---------|
    |`Id`    | To jest identyfikator urządzenia.        |
    |`DataVolumeBitLockerExternalKeys`|Są to klucze funkcji BitLocker dla dysków z danymi, które są używane do odzyskiwania danych lokalnych na urządzeniu.|
    |`SystemVolumeBitLockerRecoveryKey`| Jest to klucz funkcji BitLocker dla woluminu systemowego. Ten klucz ułatwia odzyskiwanie konfiguracji systemu i danych systemu dla urządzenia. |
    |`SEDEncryptionExternalKey`| Ten podany użytkownik lub klucz wygenerowany przez system służy do ochrony dysków danych, które mają wbudowaną szyfrowanie. |
    |`ServiceEncryptionKey`| Ten klucz chroni dane przepływające przez usługę platformy Azure. Ten klucz gwarantuje, że naruszenie usługi platformy Azure nie spowoduje naruszenia przechowywanych informacji. |

6. Przejdź do strony **Przegląd** . Stan urządzenia powinien być widoczny jako **aktywowany**.

    ![Lokalny interfejs użytkownika sieci Web "Szczegóły chmury", Strona 4](./media/azure-stack-edge-mini-r-deploy-activate/activation-4.png)
 
Aktywacja urządzenia została zakończona. Możesz teraz dodawać udziały na urządzeniu.

Jeśli wystąpią problemy podczas aktywacji, przejdź do obszaru [Rozwiązywanie problemów z aktywacją i Azure Key Vault błędów](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Aktywuj urządzenie fizyczne

Aby dowiedzieć się, jak przesyłać dane przy użyciu urządzenia z Azure Stack Edge mini R, zobacz:

> [!div class="nextstepaction"]
> [Przenoszenie danych za pomocą Azure Stack Edge mini R](./azure-stack-edge-j-series-deploy-add-shares.md)

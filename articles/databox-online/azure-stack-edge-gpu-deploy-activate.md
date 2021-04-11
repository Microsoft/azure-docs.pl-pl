---
title: Samouczek umożliwiający aktywowanie urządzenia z programem Azure Stack Edge przy użyciu procesora GPU w Azure Portal | Microsoft Docs
description: Samouczek umożliwiający wdrożenie procesora GPU w programie Azure Stack Edge powoduje aktywację urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: c3989b3d4b148dcffccb03fb5b4279c89e5c686d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562396"
---
# <a name="tutorial-activate-azure-stack-edge-pro-with-gpu"></a>Samouczek: aktywowanie Azure Stack EDGE Pro z procesorem GPU

W tym samouczku opisano sposób aktywowania urządzenia z programem Azure Stack EDGE Pro przy użyciu interfejsu GPU w sieci lokalnej.

Proces aktywacji może potrwać około 5 minut.

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Aktywuj urządzenie fizyczne

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia Azure Stack EDGE Pro przy użyciu procesora GPU upewnij się, że:

* Dla urządzenia fizycznego: 
    
    - Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-install.md).
    - Skonfigurowano sieć i ustawienia sieci obliczeniowej zgodnie z opisem w temacie [Konfigurowanie sieci, Sieć obliczeniowa, serwer proxy sieci Web](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    - Zostały przekazane własne lub wygenerowane certyfikaty urządzenia na urządzeniu, Jeśli zmieniono nazwę urządzenia lub domenę DNS za pośrednictwem strony **urządzenia** . Jeśli ten krok nie został wykonany, podczas aktywacji urządzenia zostanie wyświetlony komunikat o błędzie, a aktywacja zostanie zablokowana. Aby uzyskać więcej informacji, przejdź do pozycji [Konfigurowanie certyfikatów](azure-stack-edge-gpu-deploy-configure-certificates.md).
    
* Masz klucz aktywacji z usługi Azure Stack Edge, która została utworzona w celu zarządzania urządzeniem Azure Stack EDGE Pro. Aby uzyskać więcej informacji, przejdź do pozycji [przygotowanie do wdrożenia Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Aktywuj urządzenie

1. W lokalnym interfejsie użytkownika sieci Web urządzenia **Przejdź na stronę wprowadzenie.**
2. Na kafelku **Aktywacja** wybierz pozycję **Aktywuj**. 

    ![Strona lokalnego interfejsu użytkownika sieci Web "Szczegóły chmury"](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. W okienku **Aktywuj** wprowadź **klucz aktywacji** uzyskany w ramach [okna Pobieranie klucza aktywacji dla Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Wybierz przycisk **Zastosuj**.

    ![Lokalny interfejs użytkownika sieci Web "Szczegóły chmury" Strona 2](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Pierwsze urządzenie zostało aktywowane. Zostanie wyświetlony monit o pobranie pliku klucza.
    
    ![Lokalny interfejs użytkownika sieci Web "Szczegóły chmury" Strona 3](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
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
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```
        
 
    W poniższej tabeli objaśniono różne klucze:
    
    |Pole  |Opis  |
    |---------|---------|
    |`Id`    | To jest identyfikator urządzenia.        |
    |`DataVolumeBitLockerExternalKeys`|Są to klucze funkcji BitLocker dla dysków z danymi, które są używane do odzyskiwania danych lokalnych na urządzeniu.|
    |`SystemVolumeBitLockerRecoveryKey`| Jest to klucz funkcji BitLocker dla woluminu systemowego. Ten klucz ułatwia odzyskiwanie konfiguracji systemu i danych systemu dla urządzenia. |
    |`ServiceEncryptionKey`| Ten klucz chroni dane przepływające przez usługę platformy Azure. Ten klucz gwarantuje, że naruszenie usługi platformy Azure nie spowoduje naruszenia przechowywanych informacji. |

6. Przejdź do strony **Przegląd** . Stan urządzenia powinien być widoczny jako **aktywowany**.

    ![Lokalny interfejs użytkownika sieci Web "Szczegóły chmury", Strona 4](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
Aktywacja urządzenia została zakończona. Możesz teraz dodawać udziały na urządzeniu.

Jeśli wystąpią problemy podczas aktywacji, przejdź do obszaru [Rozwiązywanie problemów z aktywacją i Azure Key Vault błędów](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Aktywuj urządzenie fizyczne

Aby dowiedzieć się, jak przesyłać dane przy użyciu urządzenia z Azure Stack EDGE Pro, zobacz:

> [!div class="nextstepaction"]
> [Transferowanie danych za pomocą Azure Stack EDGE Pro](./azure-stack-edge-gpu-deploy-add-shares.md)
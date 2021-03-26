---
title: Samouczek umożliwiający aktywowanie urządzenia z usługą Azure Stack EDGE Pro R w programie Azure Portal
description: Samouczek dotyczący wdrażania Azure Stack EDGE Pro R instruuje użytkownika o aktywowaniu urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Pro R device so I can use it to transfer data to Azure.
ms.openlocfilehash: 9ef9cf65b49ab578b08c3df26b98a8382403280a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565286"
---
# <a name="tutorial-activate-azure-stack-edge-pro-r-device"></a>Samouczek: aktywowanie Azure Stack Edge na urządzeniu R

W tym samouczku opisano, jak można aktywować urządzenie Azure Stack EDGE Pro R przy użyciu lokalnego interfejsu użytkownika sieci Web.

Proces aktywacji może potrwać około 5 minut.  

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Aktywuj urządzenie fizyczne

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia z systemem Azure Stack EDGE Pro R upewnij się, że:

* Dla urządzenia fizycznego: 
    
    - Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-install.md).
    - Skonfigurowano sieć i ustawienia sieci obliczeniowej zgodnie z opisem w temacie [Konfigurowanie sieci, Sieć obliczeniowa, serwer proxy sieci Web](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)
    - Zostały przekazane własne lub wygenerowane certyfikaty urządzenia na urządzeniu, Jeśli zmieniono nazwę urządzenia lub domenę DNS za pośrednictwem strony **urządzenia** . Jeśli ten krok nie został wykonany, podczas aktywacji urządzenia zostanie wyświetlony komunikat o błędzie, a aktywacja zostanie zablokowana. Aby uzyskać więcej informacji, przejdź do pozycji [Konfigurowanie certyfikatów](azure-stack-edge-placeholder.md).
    
* Masz klucz aktywacji z usługi Azure Stack Edge, która została utworzona w celu zarządzania urządzeniem Azure Stack EDGE Pro R. Aby uzyskać więcej informacji, przejdź do pozycji [przygotowanie do wdrożenia Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-prep.md).


## <a name="activate-the-device"></a>Aktywuj urządzenie

1. W lokalnym interfejsie użytkownika sieci Web urządzenia **Przejdź na stronę wprowadzenie.**
2. Na kafelku **Aktywacja** wybierz pozycję **Aktywuj**. 

    ![Lokalny interfejs użytkownika sieci Web "Szczegóły chmury" Strona 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. W okienku **Aktywuj** :
    1. Wprowadź **klucz aktywacji** uzyskany w polu [Pobierz klucz aktywacji dla Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. Możesz włączyć funkcję aktywnej kolekcji dzienników, aby umożliwić firmie Microsoft zbieranie dzienników na podstawie stanu kondycji urządzenia. Dzienniki zebrane w ten sposób są przekazywane do konta usługi Azure Storage.
    
    1. Wybierz przycisk **Zastosuj**.

    ![Lokalny interfejs użytkownika sieci Web "Szczegóły chmury" Strona 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)


5. Pierwsze urządzenie zostało aktywowane. Zostanie wyświetlony monit o pobranie pliku klucza.
    
    ![Lokalny interfejs użytkownika sieci Web "Szczegóły chmury" Strona 3](./media/azure-stack-edge-pro-r-deploy-activate/activate-3.png)
    
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

Aby dowiedzieć się, jak przesyłać dane przy użyciu urządzenia z Azure Stack EDGE Pro R, zobacz:

> [!div class="nextstepaction"]
> [Transferowanie danych za pomocą Azure Stack EDGE Pro R](./azure-stack-edge-gpu-deploy-add-shares.md)
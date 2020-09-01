---
title: Samouczek umożliwiający aktywowanie Azure Stack urządzenia brzegowego za pomocą procesora GPU w Azure Portal | Microsoft Docs
description: Samouczek wdrażania Azure Stack Edge instruuje o aktywowaniu urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: be3e7c7f9a5a8c48d8644eefef42b7020e2b37f4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89088129"
---
# <a name="tutorial-activate-azure-stack-edge-with-gpu"></a>Samouczek: aktywowanie Azure Stack Edge z procesorem GPU

W tym samouczku opisano, jak można aktywować Azure Stack Urządzenie brzegowe za pomocą dołączania procesora GPU przy użyciu lokalnego interfejsu użytkownika sieci Web.

Proces aktywacji może potrwać około 5 minut.

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Aktywuj urządzenie fizyczne

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia Azure Stack Edge z procesorem GPU upewnij się, że:

* Dla urządzenia fizycznego: 
    
    - Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
    - Skonfigurowano sieć i ustawienia sieci obliczeniowej zgodnie z opisem w temacie [Konfigurowanie sieci, Sieć obliczeniowa, serwer proxy sieci Web](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    - Zostały przekazane własne lub wygenerowane certyfikaty urządzenia na urządzeniu, Jeśli zmieniono nazwę urządzenia lub domenę DNS za pośrednictwem strony **urządzenia** . Jeśli ten krok nie został wykonany, podczas aktywacji urządzenia zostanie wyświetlony komunikat o błędzie, a aktywacja zostanie zablokowana. Aby uzyskać więcej informacji, przejdź do pozycji [Konfigurowanie certyfikatów](azure-stack-edge-gpu-deploy-configure-certificates.md).
    
* Masz klucz aktywacji z usługi Azure Stack Edge, która została utworzona w celu zarządzania Azure Stack urządzeniem brzegowym. Aby uzyskać więcej informacji, przejdź do [przygotowania do wdrożenia Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Aktywuj urządzenie

1. W lokalnym interfejsie użytkownika sieci Web urządzenia **Przejdź na stronę wprowadzenie.**
2. Na kafelku **Aktywacja** wybierz pozycję **Aktywuj**. 

    ![Strona lokalnego interfejsu użytkownika sieci Web "Szczegóły chmury"](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. W okienku **Aktywuj** wprowadź **klucz aktywacji** uzyskany w polu [Pobierz klucz aktywacji dla Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Wybierz przycisk **Zastosuj**.

    ![Strona lokalnego interfejsu użytkownika sieci Web "Szczegóły chmury"](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Pierwsze urządzenie zostało aktywowane. Zostanie wyświetlony monit o pobranie pliku klucza.
    
    ![Strona lokalnego interfejsu użytkownika sieci Web "Szczegóły chmury"](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    Wybierz pozycję **Pobierz plik klucza** i Zapisz *keys.js* w pliku w bezpiecznej lokalizacji poza urządzeniem. **Ten plik klucza zawiera klucze odzyskiwania dla dysku systemu operacyjnego i dysków z danymi na urządzeniu**. Poniżej przedstawiono zawartość *keys.jsw* pliku:

        
    ```json
    {
      "Id": "1ab3fe39-26e6-4984-bb22-2e02d3fb147e",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "C086yg1DrPo0DuZB/a7hUh+kBWj804coJfBA9LDzZqw=",
        "hcsdata": "8ohX9bG3YSZl9DZmZLkYl//L9dXi1XiQrqza+iSd64Q="
      },
      "SystemVolumeBitLockerRecoveryKey": "105347-156739-594473-151107-005082-252604-471955-439395",
      "ServiceEncryptionKey": "oEwxNJeULzGRFt6DsLgcLw=="
    }
    ```
        
 
    W poniższej tabeli objaśniono różne klucze tutaj:
    
    |Pole  |Opis  |
    |---------|---------|
    |`Id`    | To jest identyfikator urządzenia.        |
    |`DataVolumeBitLockerExternalKeys`|Są to klucze funkcji BitLocker dla dysków z danymi, które są używane do odzyskiwania danych lokalnych na urządzeniu.|
    |`SystemVolumeBitLockerRecoveryKey`| Jest to klucz funkcji BitLocker dla woluminu systemowego. Ten klucz ułatwia odzyskiwanie konfiguracji systemu i danych systemu dla urządzenia. |
    |`ServiceEncryptionKey`| Ten klucz chroni dane przepływające przez usługę platformy Azure. Ten klucz gwarantuje, że naruszenie usługi platformy Azure nie spowoduje naruszenia przechowywanych informacji. |

6. Przejdź do strony **Przegląd** . Stan urządzenia powinien być widoczny jako **aktywowany**.

    ![Strona lokalnego interfejsu użytkownika sieci Web "Szczegóły chmury"](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
Aktywacja urządzenia została zakończona. Możesz teraz dodawać udziały na urządzeniu.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Wymagania wstępne
> * Aktywuj urządzenie fizyczne

Aby dowiedzieć się, jak przesyłać dane przy użyciu urządzenia z Azure Stack Edge, zobacz:

> [!div class="nextstepaction"]
> [Przenoszenie danych za pomocą Azure Stack Edge](./azure-stack-edge-j-series-deploy-add-shares.md)

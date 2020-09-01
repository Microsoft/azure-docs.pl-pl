---
title: Samouczek dotyczący konfigurowania certyfikatów dla Azure Stack urządzenia brzegowego z procesorem GPU w Azure Portal | Microsoft Docs
description: Samouczek wdrażania Azure Stack Edge z procesorem GPU powoduje skonfigurowanie certyfikatów na urządzeniu fizycznym.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 80a857f80fd2c164637e591fbab43123659cd2f7
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268180"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-with-gpu"></a>Samouczek: Konfigurowanie certyfikatów dla Azure Stack krawędzi z procesorem GPU

W tym samouczku opisano sposób konfigurowania certyfikatów dla urządzenia brzegowego Azure Stack za pomocą dołączania procesora GPU przy użyciu lokalnego interfejsu użytkownika sieci Web.

Czas poświęcony na ten krok może się różnić w zależności od wybranej opcji i sposobu ustanowienia przepływu certyfikatów w środowisku.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfigurowanie certyfikatów dla urządzenia fizycznego

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia Azure Stack Edge z procesorem GPU upewnij się, że:

* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
* Jeśli planujesz przeprowadzenie własnych certyfikatów:
    - Certyfikaty powinny być gotowe w odpowiednim formacie, łącznie z certyfikatem łańcucha podpisywania.
<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-j-series-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>Konfigurowanie certyfikatów dla urządzenia


1. Na kafelku **zabezpieczenia** wybierz pozycję **Konfiguruj** dla certyfikatów. 

    ![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)

2. W zależności od tego, czy zmieniono nazwę urządzenia lub domenę DNS na kafelku **Konfiguracja urządzenia** , można wybrać jedną z następujących opcji certyfikatów.

    - Jeśli nie zmieniono nazwy urządzenia lub domeny DNS we wcześniejszym kroku i nie chcesz przenosić własnych certyfikatów, możesz pominąć ten krok i przejść do następnego kroku. Urządzenie automatycznie wygenerowało certyfikaty z podpisem własnym, które zaczynają się od. 

        ![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - W przypadku zmiany nazwy urządzenia lub domeny DNS, aby pomyślnie aktywować urządzenie, można wybrać jedną z następujących opcji: 
    
        - **Generuj wszystkie certyfikaty urządzeń**. Certyfikaty urządzeń powinny być używane tylko do testowania i nie są używane w przypadku obciążeń produkcyjnych. * * Aby uzyskać więcej informacji, przejdź do pozycji [Generuj certyfikaty urządzeń na Azure Stack Edge](#generate-device-certificates).

        - **Przenoszenie własnych certyfikatów**. Możesz przenieść własne podpisane certyfikaty punktów końcowych i odpowiednie łańcuchy podpisywania. Najpierw należy dodać łańcuch podpisywania, a następnie przekazać certyfikaty punktów końcowych. **Zalecane jest, aby zawsze przenosić własne certyfikaty do obciążeń produkcyjnych.** Aby uzyskać więcej informacji, przejdź do pozycji [przenoszenie własnych certyfikatów na urządzeniu Azure Stack Edge](#bring-your-own-certificates).
    
        - Możesz przenieść niektóre własne certyfikaty i wygenerować niektóre certyfikaty urządzeń. Opcja **Generuj certyfikaty** spowoduje ponowne wygenerowanie certyfikatów urządzeń.

    - Jeśli zmieniono nazwę urządzenia lub domenę DNS, a nie wygenerujesz certyfikatów ani nie przeniesiesz własnych certyfikatów, aktywacja zostanie zablokowana.


### <a name="generate-device-certificates"></a>Generuj certyfikaty urządzeń

Wykonaj następujące kroki, aby wygenerować certyfikaty urządzeń.

Wykonaj następujące kroki, aby ponownie wygenerować i pobrać Azure Stack certyfikaty urządzenia brzegowego:

1. W lokalnym interfejsie użytkownika urządzenia przejdź do pozycji **konfiguracja > certyfikaty**. Wybierz pozycję **Generuj certyfikaty**.

    ![Generowanie i pobieranie certyfikatu 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. W obszarze **Generuj certyfikaty urządzenia**wybierz pozycję **Generuj**.

    ![Generowanie i pobieranie certyfikatu 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    Certyfikaty urządzeń są teraz generowane i stosowane. 
    
    > [!IMPORTANT]
    > Podczas gdy operacja generowania certyfikatu jest w toku, nie należy przenosić własnych certyfikatów i próbować dodać je za pośrednictwem opcji **+ Dodaj certyfikat** .

    Otrzymasz powiadomienie, gdy operacja zostanie pomyślnie ukończona. Aby uniknąć potencjalnych problemów z pamięcią podręczną, należy ponownie uruchomić przeglądarkę. 
    
    ![Generowanie i pobieranie certyfikatu 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. Na stronie **Certyfikaty** zostanie wyświetlona kolumna **pobieranie** zostanie wypełniona, a linki do pobrania ponownie wygenerowane certyfikaty są dostępne. 

    ![Generowanie i pobieranie certyfikatu 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

4. Wybierz łącze pobierania certyfikatu i po wyświetleniu monitu Zapisz certyfikat. 

    ![Generuj i pobieraj certyfikat 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Powtórz ten proces dla wszystkich certyfikatów, które chcesz pobrać. 
    
    ![Generowanie i pobieranie certyfikatu 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    Certyfikaty generowane przez urządzenie są zapisywane jako certyfikaty DER o następującym formacie nazwy: 

    `<Device name>_<Endpoint name>.cer`. Te certyfikaty zawierają klucz publiczny dla odpowiednich certyfikatów zainstalowanych na urządzeniu. 

Należy zainstalować te certyfikaty w systemie klienckim, z którego korzystasz, aby uzyskać dostęp do punktów końcowych na urządzeniu ASE. Te certyfikaty ustanawiają relację zaufania między klientem a urządzeniem.

Aby zaimportować i zainstalować te certyfikaty na kliencie używanym do uzyskiwania dostępu do urządzenia, wykonaj czynności opisane w temacie [Importowanie certyfikatów na klientach uzyskujących dostęp do Azure Stack urządzenia brzegowego](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device). 

W przypadku korzystania z Eksplorator usługi Azure Storage należy zainstalować certyfikaty na kliencie w formacie PEM i należy przekonwertować certyfikaty wygenerowane przez urządzenie w formacie PEM. 

> [!IMPORTANT]
> - Link do pobierania jest dostępny tylko dla certyfikatów generowanych przez urządzenia, a nie w przypadku ich posiadania.
> - Można zdecydować, czy ma zostać utworzone połączenie certyfikatów i utworzyć własne certyfikaty, o ile są spełnione inne wymagania dotyczące certyfikatu. Aby uzyskać więcej informacji, przejdź do [wymagań dotyczących certyfikatów](azure-stack-edge-j-series-certificate-requirements.md).
    

### <a name="bring-your-own-certificates"></a>Przenoszenie własnych certyfikatów

Wykonaj następujące kroki, aby dodać własne certyfikaty, w tym łańcuch podpisywania.

1. Aby przekazać certyfikat, na stronie **certyfikat** wybierz pozycję **+ Dodaj certyfikat**.

    ![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Najpierw przekaż łańcuch podpisywania i wybierz pozycję **weryfikuj & Dodaj**.

    ![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. Teraz można przekazać inne certyfikaty. Można na przykład przekazać Azure Resource Manager i certyfikaty punktów końcowych usługi BLOB Storage.

    ![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    Możesz również przekazać certyfikat lokalnego interfejsu użytkownika sieci Web. Po przekazaniu tego certyfikatu konieczne będzie uruchomienie przeglądarki i wyczyszczenie pamięci podręcznej. Następnie należy nawiązać połączenie z lokalnym interfejsem użytkownika sieci Web urządzenia.  

    ![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    Możesz również przekazać certyfikat węzła.

    ![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    Strona certyfikat powinna zostać zaktualizowana w celu odzwierciedlenia nowo dodanych certyfikatów.

    ![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)  

    > [!NOTE]
    > Poza chmurą publiczną platformy Azure w przypadku wszystkich konfiguracji chmury (Azure Government lub Azure Stack) należy wprowadzić certyfikaty łańcucha podpisywania.


Urządzenie jest teraz gotowe do aktywacji.


## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfigurowanie certyfikatów dla urządzenia fizycznego

Aby dowiedzieć się, jak aktywować Azure Stack Urządzenie brzegowe, zobacz:

> [!div class="nextstepaction"]
> [Aktywuj urządzenie Azure Stack Edge](./azure-stack-edge-gpu-deploy-activate.md)

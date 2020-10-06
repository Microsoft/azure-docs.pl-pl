---
title: Zarządzanie obliczeniami w ramach procesora GPU w Azure Stack Edge | Microsoft Docs
description: Opisuje sposób zarządzania ustawieniami obliczeń brzegowych, takimi jak wyzwalacz, moduły, wyświetlanie konfiguracji obliczeniowej, Usuwanie konfiguracji za pośrednictwem Azure Portal w procesorze GPU Azure Stack EDGE Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 1d4d0c591640a3528b7aeec5254f2a634ee008aa
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743679"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-gpu"></a>Zarządzanie obliczeniami na Azure Stack brzegowej procesora GPU Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

W tym artykule opisano sposób zarządzania obliczeniami na Azure Stack brzegowej. Można zarządzać obliczeniami za pośrednictwem Azure Portal lub za pośrednictwem lokalnego interfejsu użytkownika sieci Web. Użyj Azure Portal do zarządzania modułami, wyzwalaczami i konfiguracją obliczeniową oraz lokalnym interfejsem użytkownika sieci Web do zarządzania ustawieniami obliczeniowymi.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Zarządzanie wyzwalaczami
> * Zarządzanie konfiguracją obliczeń


## <a name="manage-triggers"></a>Zarządzanie wyzwalaczami

Zdarzenia to rzeczy dziejące się w środowisku chmury lub na urządzeniu, względem których warto podjąć odpowiednie działania. Na przykład utworzenie pliku w udziale to zdarzenie. Wyzwalacze zgłaszają zdarzenia. W przypadku usługi Azure Stack EDGE Pro wyzwalacze mogą być w odpowiedzi na zdarzenia plików lub harmonogram.

- **Plik**: te wyzwalacze są w odpowiedzi na zdarzenia plików, takie jak tworzenie pliku, modyfikacja pliku.
- **Zaplanowane**: te wyzwalacze są w odpowiedzi na harmonogram, który można zdefiniować przy użyciu daty rozpoczęcia, godziny rozpoczęcia i interwału powtarzania.


### <a name="add-a-trigger"></a>Dodawanie wyzwalacza

Wykonaj następujące kroki w Azure Portal, aby utworzyć wyzwalacz.

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do pozycji **Oblicz compute > Trigger**. Wybierz pozycję **+ Dodaj wyzwalacz** na pasku poleceń.

    ![Wybierz pozycję Dodaj wyzwalacz](media/azure-stack-edge-j-series-manage-compute/add-trigger-1m.png)

2. W bloku **Dodawanie wyzwalacza** Podaj unikatową nazwę wyzwalacza.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Wybierz **Typ** dla wyzwalacza. Wybierz **plik** , gdy wyzwalacz jest w odpowiedzi na zdarzenie pliku. Wybierz opcję **zaplanowane** , gdy wyzwalacz ma być uruchamiany w określonym czasie i uruchamiany z upływem określonego interwału powtarzania. W zależności od dokonanego wyboru zostanie wyświetlony inny zestaw opcji.

    - **Wyzwalacz pliku** — wybierz z listy rozwijanej zainstalowany udział. Po uruchomieniu zdarzenia dotyczącego pliku w tym udziale wyzwalacz wywoła funkcję platformy Azure.

        ![Dodawanie udziału SMB](media/azure-stack-edge-j-series-manage-compute/add-file-trigger.png)

    - **Zaplanowany wyzwalacz** — Określ datę/godzinę rozpoczęcia i interwał powtarzania w godzinach, minutach lub sekundach. Wprowadź również nazwę tematu. Temat zapewnia elastyczność routingu wyzwalacza do modułu wdrożonego na urządzeniu.

        Przykładem ciągu trasy jest: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"` .

        ![Dodawanie udziału NFS](media/azure-stack-edge-j-series-manage-compute/add-scheduled-trigger.png)

4. Wybierz pozycję **Dodaj** , aby utworzyć wyzwalacz. Powiadomienie pokazuje, że trwa tworzenie wyzwalacza. Po utworzeniu wyzwalacza zostanie on zaktualizowany w celu odzwierciedlenia nowego wyzwalacza.
 
    ![Lista zaktualizowanych wyzwalaczy](media/azure-stack-edge-j-series-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Usuwanie wyzwalacza

Aby usunąć wyzwalacz, wykonaj następujące czynności w Azure Portal.

1. Z listy wyzwalaczy Wybierz wyzwalacz, który chcesz usunąć.

    ![Wybierz wyzwalacz](media/azure-stack-edge-j-series-manage-compute/delete-trigger-1.png)

2. Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Usuń**.

    ![Wybierz pozycję Usuń](media/azure-stack-edge-j-series-manage-compute/delete-trigger-2.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

    ![Potwierdzenie usunięcia](media/azure-stack-edge-j-series-manage-compute/delete-trigger-3.png)

Lista wyzwalaczy jest aktualizowana w celu odzwierciedlenia usunięcia.

## <a name="manage-compute-configuration"></a>Zarządzanie konfiguracją obliczeń

Użyj Azure Portal, aby wyświetlić konfigurację obliczeń, usunąć istniejącą konfigurację obliczeniową lub odświeżyć konfigurację obliczeniową w celu zsynchronizowania kluczy dostępu dla urządzenia IoT i urządzenia IoT Edge na potrzeby Azure Stack brzegowej usługi Pro.

### <a name="view-compute-configuration"></a>Wyświetlanie konfiguracji obliczeniowej

Wykonaj następujące kroki w Azure Portal, aby wyświetlić konfigurację obliczeń dla urządzenia.

1. W Azure Portal przejdź do Azure Stack zasobów brzegowych, a następnie przejdź do pozycji **Oblicz compute > modules**. Wybierz pozycję **Wyświetl obliczenia** na pasku poleceń.

    ![Wybierz pozycję Wyświetl obliczenia](media/azure-stack-edge-j-series-manage-compute/view-compute-1.png)

2. Zanotuj konfigurację obliczeń na urządzeniu. Podczas konfigurowania obliczeń tworzony jest zasób IoT Hub. W ramach tego IoT Hub zasobów skonfigurowano urządzenie IoT i urządzenie IoT Edge. Tylko moduły systemu Linux są obsługiwane do uruchamiania na urządzeniu IoT Edge.

    ![Wyświetl konfigurację](media/azure-stack-edge-j-series-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Usuń konfigurację obliczeń

Wykonaj następujące kroki w Azure Portal, aby usunąć istniejącą konfigurację obliczeń krawędzi dla urządzenia.

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do pozycji **Oblicz compute > Rozpocznij**. Na pasku poleceń wybierz pozycję **Usuń obliczenia** .

    ![Wybierz pozycję Usuń obliczenia](media/azure-stack-edge-j-series-manage-compute/remove-compute-1.png)

2. W przypadku usunięcia konfiguracji obliczeniowej należy ponownie skonfigurować urządzenie, jeśli będzie konieczne ponowne użycie obliczeń. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak**.

    ![Wybierz pozycję Usuń obliczenie 2](media/azure-stack-edge-j-series-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Synchronizowanie urządzeń IoT i IoT Edge kluczy dostępu do urządzeń

Podczas konfigurowania obliczeń na Azure Stack EDGE Pro zostanie utworzone urządzenie IoT i urządzenie IoT Edge. Do tych urządzeń są automatycznie przypisywane klucze dostępu symetrycznego. Ze względów bezpieczeństwa te klucze są regularnie obracane za pośrednictwem usługi IoT Hub.

Aby obrócić te klucze, możesz przejść do utworzonej usługi IoT Hub, a następnie wybrać urządzenie IoT lub urządzenie IoT Edge. Każde urządzenie ma podstawowy klucz dostępu i pomocnicze klucze dostępu. Przypisz podstawowy klucz dostępu do pomocniczego klucza dostępu, a następnie ponownie Wygeneruj podstawowy klucz dostępu.

Jeśli urządzenie IoT i IoT Edge klucze zostały obrócone, należy odświeżyć konfigurację na Azure Stack EDGE Pro, aby uzyskać najnowsze klucze dostępu. Synchronizacja pomaga urządzeniu pobrać najnowsze klucze urządzenia IoT i urządzenia IoT Edge. Azure Stack EDGE Pro używa tylko podstawowych kluczy dostępu.

Aby zsynchronizować klucze dostępu dla urządzenia, wykonaj następujące czynności w Azure Portal.

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do pozycji **Oblicz compute > Rozpocznij**. Wybierz pozycję **Odśwież konfigurację** na pasku poleceń.

    ![Wybierz konfigurację odświeżania](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-1.png)

2. Wybierz opcję **tak** po wyświetleniu monitu o potwierdzenie.

    ![Wybierz opcję tak po wyświetleniu monitu](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-2.png)

3. Po zakończeniu synchronizacji zamknij okno dialogowe.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [rozwiązywać problemy z usługą Azure Stack EDGE Pro](azure-stack-edge-gpu-troubleshoot.md).

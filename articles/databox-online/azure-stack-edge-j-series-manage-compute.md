---
title: Zarządzanie obliczeniami w ramach procesora GPU w Azure Stack Edge | Microsoft Docs
description: Opisuje sposób zarządzania ustawieniami obliczeń brzegowych, takimi jak wyzwalacz, moduły, wyświetlanie konfiguracji obliczeniowej, Usuwanie konfiguracji za pośrednictwem Azure Portal w procesorze GPU Azure Stack EDGE Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/27/2021
ms.author: alkohli
ms.openlocfilehash: bd49edcfaca781ac3d36fbf871ec146b32c64ae3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733418"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-gpu"></a>Zarządzanie obliczeniami na Azure Stack brzegowej procesora GPU Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

W tym artykule opisano sposób zarządzania obliczeniami za pośrednictwem usługi IoT Edge na urządzeniu z systemem Azure Stack EDGE Pro GPU. Można zarządzać obliczeniami za pośrednictwem Azure Portal lub za pośrednictwem lokalnego interfejsu użytkownika sieci Web. Użyj Azure Portal do zarządzania modułami, wyzwalaczami i konfiguracją IoT Edge i lokalnym interfejsem użytkownika sieci Web do zarządzania ustawieniami sieci obliczeniowej.



## <a name="manage-triggers"></a>Zarządzanie wyzwalaczami

Zdarzenia to rzeczy dziejące się w środowisku chmury lub na urządzeniu, względem których warto podjąć odpowiednie działania. Na przykład utworzenie pliku w udziale to zdarzenie. Wyzwalacze zgłaszają zdarzenia. W przypadku usługi Azure Stack EDGE Pro wyzwalacze mogą być w odpowiedzi na zdarzenia plików lub harmonogram.

- **Plik**: te wyzwalacze są w odpowiedzi na zdarzenia plików, takie jak tworzenie pliku, modyfikacja pliku.
- **Zaplanowane**: te wyzwalacze są w odpowiedzi na harmonogram, który można zdefiniować przy użyciu daty rozpoczęcia, godziny rozpoczęcia i interwału powtarzania.


### <a name="add-a-trigger"></a>Dodawanie wyzwalacza

Wykonaj następujące kroki w Azure Portal, aby utworzyć wyzwalacz.

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do **IoT Edge**. Przejdź do pozycji **wyzwalacze** i wybierz pozycję **+ Dodaj wyzwalacz** na pasku poleceń.

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

## <a name="manage-iot-edge-configuration"></a>Zarządzanie konfiguracją IoT Edge

Użyj Azure Portal, aby wyświetlić konfigurację obliczeń, usunąć istniejącą konfigurację obliczeniową lub odświeżyć konfigurację obliczeniową w celu zsynchronizowania kluczy dostępu dla urządzenia IoT i urządzenia IoT Edge na potrzeby Azure Stack brzegowej usługi Pro.

### <a name="view-iot-edge-configuration"></a>Wyświetl konfigurację IoT Edge

Wykonaj następujące kroki w Azure Portal, aby wyświetlić konfigurację IoT Edge dla urządzenia.

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do **IoT Edge**. Gdy na urządzeniu zostanie włączona usługa IoT Edge, Strona przegląd wskazuje, że usługa IoT Edge działa prawidłowo.

    ![Wybierz pozycję Wyświetl obliczenia](media/azure-stack-edge-j-series-manage-compute/view-compute-1.png)

2. Przejdź do pozycji **Właściwości** , aby wyświetlić konfigurację IoT Edge na urządzeniu. Podczas konfigurowania obliczeń tworzony jest zasób IoT Hub. W ramach tego IoT Hub zasobów skonfigurowano urządzenie IoT i urządzenie IoT Edge. Tylko moduły systemu Linux są obsługiwane do uruchamiania na urządzeniu IoT Edge.

    ![Wyświetl konfigurację](media/azure-stack-edge-j-series-manage-compute/view-compute-2.png)


### <a name="remove-iot-edge-service"></a>Usuwanie usługi IoT Edge

Wykonaj następujące czynności w Azure Portal, aby usunąć istniejącą konfigurację IoT Edge dla urządzenia.

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do **IoT Edge**. Przejdź do **omówienia** i wybierz pozycję **Usuń** na pasku poleceń.

    ![Wybierz pozycję Usuń obliczenia](media/azure-stack-edge-j-series-manage-compute/remove-compute-1.png)

2. W przypadku usunięcia usługi IoT Edge akcja jest nieodwracalna i nie można jej cofnąć. Utworzone moduły i wyzwalacze również zostaną usunięte. Musisz zmienić konfigurację urządzenia, jeśli trzeba będzie ponownie używać IoT Edge. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **OK**.

    ![Wybierz pozycję Usuń obliczenie 2](media/azure-stack-edge-j-series-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Synchronizowanie urządzeń IoT i IoT Edge kluczy dostępu do urządzeń

Podczas konfigurowania obliczeń na Azure Stack EDGE Pro zostanie utworzone urządzenie IoT i urządzenie IoT Edge. Do tych urządzeń są automatycznie przypisywane klucze dostępu symetrycznego. Ze względów bezpieczeństwa te klucze są regularnie obracane za pośrednictwem usługi IoT Hub.

Aby obrócić te klucze, możesz przejść do utworzonej usługi IoT Hub, a następnie wybrać urządzenie IoT lub urządzenie IoT Edge. Każde urządzenie ma podstawowy klucz dostępu i pomocnicze klucze dostępu. Przypisz podstawowy klucz dostępu do pomocniczego klucza dostępu, a następnie ponownie Wygeneruj podstawowy klucz dostępu.

Jeśli urządzenie IoT i IoT Edge klucze zostały obrócone, należy odświeżyć konfigurację na Azure Stack EDGE Pro, aby uzyskać najnowsze klucze dostępu. Synchronizacja pomaga urządzeniu pobrać najnowsze klucze urządzenia IoT i urządzenia IoT Edge. Azure Stack EDGE Pro używa tylko podstawowych kluczy dostępu.

Aby zsynchronizować klucze dostępu dla urządzenia, wykonaj następujące czynności w Azure Portal.

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do pozycji **IoT Edge COMPUTE**. Przejdź do **omówienia** i wybierz pozycję **Odśwież konfigurację** na pasku poleceń.

    ![Wybierz konfigurację odświeżania](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-1.png)

2. Wybierz opcję **tak** po wyświetleniu monitu o potwierdzenie.

    ![Wybierz opcję tak po wyświetleniu monitu](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-2.png)

3. Po zakończeniu synchronizacji zamknij okno dialogowe.

## <a name="change-external-service-ips-for-containers"></a>Zmień zewnętrzne adresy IP usługi dla kontenerów

Adresy IP usług zewnętrznych Kubernetes są używane do uzyskiwania dostępu do usług, które są udostępniane poza klastrem Kubernetes. Po aktywowaniu urządzenia można ustawić lub zmodyfikować adresy IP usług zewnętrznych dla obciążeń kontenerowych dla urządzenia przez uzyskanie dostępu do lokalnego interfejsu użytkownika.


1. W lokalnym interfejsie użytkownika urządzenia przejdź do pozycji **obliczenia**.
1. Wybierz port, którego sieć jest skonfigurowana do obliczeń. W bloku, który zostanie otwarty, określ (nowy) lub zmodyfikuj (jeśli istnieje) adresy IP usług zewnętrznych Kubernetes. Te adresy IP są używane dla wszystkich usług, które muszą być ujawnione poza klastrem Kubernetes. 
    - Wymagany jest co najmniej jeden adres IP usługi dla `edgehub` usługi działającej na urządzeniu i używany przez moduły IoT Edge. 
    - Wymagany jest adres IP dla każdego dodatkowego modułu IoT Edge lub kontenera, który ma zostać wdrożony. 
    - Są to statyczne, ciągłe adresy IP.

    ![Zmień adresy IP usługi Kubernetes](media/azure-stack-edge-j-series-manage-compute/change-service-ips-1.png)

1. Wybierz przycisk **Zastosuj**. Po zastosowaniu adresów IP urządzenie nie wymaga ponownego uruchomienia ani ponownego uruchomienia komputera. Nowe adresy IP zaczynają obowiązywać natychmiast.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [rozwiązywać problemy z usługą Azure Stack EDGE Pro](azure-stack-edge-gpu-troubleshoot.md).

---
title: Wdrażanie usługi Live Video Analytics na Azure Stack Edge
description: W tym artykule wymieniono kroki, które ułatwią wdrożenie analizy filmów wideo na żywo na Azure Stack Edge.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: b13bb779a5a780b21f2d5d96ed8831ef5c26564d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937716"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>Wdrażanie usługi Live Video Analytics na Azure Stack Edge

W tym artykule wymieniono kroki, które ułatwią wdrożenie analizy filmów wideo na żywo na Azure Stack Edge. Po skonfigurowaniu i aktywowaniu urządzenia będzie ono gotowe do wdrożenia analizy wideo na żywo. 

W przypadku usługi Live Video Analytics wdrożenie zostanie wdrożone za pośrednictwem IoT Hub, ale Azure Stack zasoby brzegowe uwidaczniają interfejs API Kubernetes, który umożliwia klientowi wdrożenie dodatkowych rozwiązań nieopartych na IoT Hub, które mogą być interfejsami z użyciem analizy wideo na żywo. 

> [!TIP]
> Korzystanie z interfejsu API Kubernetes (K8s) dla wdrożenia niestandardowego jest zaawansowaną. Zaleca się, aby klient utworzył moduły brzegowe i wdrażał za pośrednictwem IoT Hub do każdego Azure Stack zasobów brzegowych zamiast korzystać z interfejsu API Kubernetes. W tym artykule przedstawiono kroki wdrażania modułu analizy wideo na żywo przy użyciu IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure, do której masz [uprawnienia właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner).
* Zasób [Azure Stack Edge](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep)
   
* [IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)
* Jednostka [usługi](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/create-custom-azure-resource-manager-role-how-to#create-service-principal) dla modułu analizy wideo na żywo.

   Użyj jednego z tych regionów, w których IoT Hub jest dostępna: Wschodnie stany USA 2, środkowe stany USA, Północno-środkowe stany USA, Japonia Wschodnia, zachodnie stany USA 2, zachodnie stany USA, Kanada Wschodnia, Południowe Zjednoczone Królestwo, Francja środkowa, Francja Południowa, Szwajcaria Północna, Szwajcaria Zachodnia i Japonia Zachodnia.
* Konto magazynu

    Zalecane jest użycie kont magazynu ogólnego przeznaczenia w wersji 2 (GPv2).  
    Dowiedz się więcej na temat [konta magazynu ogólnego przeznaczenia w wersji 2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade?tabs=azure-portal).
* [Visual Studio Code](https://code.visualstudio.com/) na komputerze deweloperskim. Upewnij się, że masz [rozszerzenie narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Upewnij się, że sieć, z którą jest połączony komputer deweloperski, zezwala na zaawansowaną kolejkowanie komunikatów przez port 5671. To ustawienie umożliwia usłudze Azure IoT Tools komunikowanie się z usługą Azure IoT Hub.

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>Konfigurowanie Azure Stack Edge do korzystania z analizy filmów wideo na żywo

Azure Stack Edge to rozwiązanie typu "sprzęt jako usługa" oraz urządzenie komputerowe z systemem brzegowym z funkcją transferu danych w sieci. Przeczytaj więcej na temat [Azure Stack Edge i szczegółowe instrukcje dotyczące instalacji](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep). Aby rozpocząć, postępuj zgodnie z instrukcjami podanymi w poniższych linkach:

* [Azure Stack tworzenie zasobów i Data Box Gateway](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep)
* [Instalacja i konfiguracja](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-install)
* [Połączenie i aktywacja](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate)

### <a name="attach-an-iot-hub-to-azure-stack-edge"></a>Dołącz IoT Hub do Azure Stack Edge

1. W [Azure Portal](https://ms.portal.azure.com)przejdź do zasobu Azure Stack Edge, a następnie kliknij pozycję przegląd. W okienku po prawej stronie na kafelku Oblicz wybierz pozycję Rozpocznij.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge.png" alt-text="Azure Stack Edge":::
1. Na kafelku Konfigurowanie obliczeń krawędzi wybierz pozycję Konfiguruj obliczenia.
1. W bloku Konfigurowanie obliczeń krawędzi wprowadź następujące dane:
    
    | Pole|Wartość|
    |---|---|
    |Usługa IoT Hub|Wybierz pozycję Nowy lub istniejący.<br/>Domyślnie warstwa standardowa (S1) jest używana do tworzenia zasobu IoT. Aby użyć zasobu IoT warstwy Bezpłatna, utwórz go, a następnie wybierz istniejący zasób.<br/>W każdym przypadku zasób IoT Hub używa tej samej subskrypcji i grupy zasobów, która używa Azure Stack zasobu brzegowego.|
    |Nazwa|Wprowadź nazwę dla zasobu IoT Hub.|

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-get-started.png" alt-text="Azure Stack Edge":::
1. Wybierz przycisk **Utwórz**. Tworzenie zasobów IoT Hub trwa kilka minut. Po utworzeniu zasobu IoT Hub, **Skonfiguruj kafelek Oblicz** aktualizacje, aby pokazać konfigurację obliczeń. Aby upewnić się, że skonfigurowano rolę obliczeniową brzegową, wybierz pozycję **Wyświetl obliczenia** na kafelku **Konfiguruj obliczenia** .

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/edge-compute-config.png" alt-text="Azure Stack Edge":::

    > [!NOTE]
    > Jeśli okno dialogowe Konfigurowanie obliczeń zostanie zamknięte przed skojarzeniem IoT Hub z zasobem Azure Stack brzegowego, IoT Hub zostanie utworzony, ale nie będzie wyświetlany w konfiguracji obliczeniowej. Załaduj ponownie stronę po kilku minutach i zobaczysz ją.
    
    Po skonfigurowaniu roli funkcji obliczeniowej Edge na urządzeniu Edge tworzone są dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub. Środowisko uruchomieniowe IoT Edge jest również uruchomione na urządzeniu IoT Edge. W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux.
    
    Po wypełnieniu wszystkich informacji na karcie Konfiguracja obliczeń Edge zostanie wyświetlona informacja poniżej:
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/configure-edge-compute.png" alt-text="Azure Stack Edge":::
 
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Włącz wymagania wstępne dotyczące obliczeń w Azure Stack krawędzi lokalnego interfejsu użytkownika

Przed kontynuowaniem upewnij się, że:

* Uaktywniono zasób Azure Stack Edge.
* Masz dostęp do systemu klienta systemu Windows z uruchomionym programem PowerShell 5,0 lub nowszym w celu uzyskania dostępu do zasobu brzegowego Azure Stack.
* Aby wdrożyć klaster Kubernetes, należy skonfigurować zasób brzegowy Azure Stack przy użyciu [lokalnego interfejsu użytkownika sieci Web](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate#connect-to-the-local-web-ui-setup). 
    
    * Aby włączyć obliczenia, w lokalnym interfejsie użytkownika sieci Web urządzenia przejdź na stronę obliczenia.
    
        * Wybierz interfejs sieciowy, który chcesz włączyć dla obliczeń. Wybierz pozycję Włącz. Włączenie obliczeń powoduje utworzenie przełącznika wirtualnego na urządzeniu w tym interfejsie sieciowym.
        * Pozostaw puste adresy IP węzła testowego Kubernetes i adresy IP usług zewnętrznych Kubernetes.
        * Wybierz pozycję Zastosuj — ta operacja powinna trwać około 2 minuty.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text="Azure Stack Edge"
            * Dodaj adres IPv4 i nazwę hosta usługi Kubernetes API do pliku. (Te informacje można znaleźć w portalu Azure Stack Edge w sekcji urządzenia).
            * Zapisz i zamknij

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>Wdróż moduł usługi Microsoft Live Video Analytics Edge przy użyciu Azure Portal

W tym celu zajmiemy się wykonywaniem określonych kroków z sekcji [wdrażanie analizy wideo na żywo za pośrednictwem IoT Hub](deploy-iot-edge-device.md).

1. Pomiń kroki tworzenia użytkowników i grup i przejdź do sekcji [wdrażanie usługi Live Video Analytics Edge](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module) module. Wykonaj kroki wymienione w tym miejscu.
1. W obszarze Opcje tworzenia kontenera nie ma potrzeby ustawiania zmiennych środowiskowych. Aby to zrobić, Pomiń ten krok.
1. Otwórz kartę Opcje tworzenia kontenera.

   * Skopiuj i wklej poniższy kod JSON w polu, aby ograniczyć rozmiar plików dziennika generowanych przez moduł.
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > Jeśli używany jest protokół gRPC z transferem pamięci udostępnionej, należy użyć trybu IPC hosta na potrzeby dostępu do pamięci udostępnionej między rozwiązaniami do analizy i wnioskowania wideo na żywo.
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > Sekcja "powiązania" w notacji JSON zawiera 2 wpisy. Możesz zaktualizować powiązania urządzenia brzegowego, ale upewnij się, że te katalogi istnieją.
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices": służy do powiązania trwałych danych konfiguracji aplikacji z kontenera i zapisywania go na urządzeniu brzegowym.
    * "/var/media:/var/media": to wiąże foldery multimedialne między urządzeniem brzegowym i kontenerem. Służy do przechowywania nagrań wideo podczas uruchamiania topologii programu Media Graph, która obsługuje przechowywanie klipów wideo na urządzeniu brzegowym.
        
1. Kontynuuj kroki z dokumentu i wypełnij ustawienia sznurka modułu.
1. Wybierz pozycję **dalej**: trasy, aby przejść do sekcji trasy. Określ trasy.

    Zachowaj trasy domyślne i wybierz kolejno pozycje dalej: przegląd + Utwórz, aby przejść do sekcji Przegląd.
1. [Przeglądanie i weryfikowanie wdrożenia](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>Obowiązkowe Instalator instalacji woluminów platformy Docker

Aby wyświetlić dane w katalogach roboczych, wykonaj następujące kroki, aby skonfigurować instalacje woluminów platformy Docker przed wdrożeniem. 

Te kroki obejmują tworzenie użytkownika bramy i Konfigurowanie udziałów plików w celu wyświetlenia zawartości katalogu roboczego analizy filmów wideo na żywo oraz folderu multimediów wideo na żywo.

> [!NOTE]
> Instalacje powiązań są obsługiwane, ale instalacje woluminów umożliwiają wyświetlanie danych i w razie potrzeby zdalnie skopiowane. Istnieje możliwość użycia zarówno instalacji bind, jak i woluminu, ale nie mogą wskazywać tej samej ścieżki kontenera.

1. Otwórz Azure Portal i przejdź do zasobu Azure Stack Edge.
1. Utwórz **użytkownika bramy** , który może uzyskiwać dostęp do udziałów.
    
    1. W lewym okienku nawigacji kliknij pozycję **brama — >użytkownicy**.
    1. Kliknij pozycję **+ Dodaj użytkownika** do ustawienia Nazwa użytkownika i hasło. (Zalecane: `lvauser` ).
    1. Kliknij przycisk **Dodaj**.
    
1. Utwórz **udział lokalny** dla trwałości analizy wideo na żywo.

    1. Kliknij pozycję **brama — >udziały**.
    1. Kliknij pozycję **+ Dodaj udziały**.
    1. Ustaw nazwę udziału. (Zalecane: `lva` ).
    1. Zachowaj typ udziału jako SMB.
    1. Upewnij się, że jest zaznaczone pole wyboru **Udostępnij przy użyciu obliczeń brzegowych** .
    1. Upewnij się, że jest zaznaczone pole wyboru **Konfiguruj w miarę udziału lokalnego** .
    1. W obszarze Szczegóły użytkownika Udziel dostępu do udziału dla ostatnio utworzonego użytkownika.
    1. Kliknij pozycję **Utwórz**.
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="Azure Stack Edge":::
    
1. Utwórz udział zdalny dla magazynu synchronizacji plików.

    1. Najpierw utwórz konto magazynu obiektów BLOB w tym samym regionie.
    1. Kliknij pozycję **brama — >udziały**.
    1. Kliknij pozycję **+ Dodaj udziały**.
    1. Ustaw nazwę udziału. (Zalecane: Multimedia).
    1. Zachowaj typ udziału jako SMB.
    1. Upewnij się, że jest zaznaczone pole wyboru **Udostępnij przy użyciu obliczeń brzegowych** .
    1. Upewnij się, że nie jest zaznaczone pole wyboru **Konfiguruj, ponieważ udział lokalny** nie jest zaznaczony.
    1. Wybierz ostatnio utworzone konto magazynu.
    1. Ustaw nazwę kontenera.
    1. Ustaw typ magazynu na blokowy obiekt BLOB.
    1. W obszarze Szczegóły użytkownika Udziel dostępu do udziału dla ostatnio utworzonego użytkownika.
    1. Kliknij pozycję **Utwórz**.    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="Azure Stack Edge"
            }]
        }
    }
    ```

### <a name="verify-that-the-module-is-running"></a>Sprawdź, czy moduł jest uruchomiony

Ostatnim krokiem jest upewnienie się, że moduł jest połączony i działa zgodnie z oczekiwaniami. Stan środowiska uruchomieniowego modułu powinien pokazywać uruchomienie dla urządzenia usługi IoT Edge w zasobie usługi IoT Hub.

Wykonaj poniższe kroki, aby sprawdzić, czy moduł jest uruchomiony:

1. W Azure Portal Wróć do zasobu Azure Stack Edge
1. Wybierz kafelek moduły. Spowoduje to przejście do bloku moduły. Na liście modułów Zidentyfikuj wdrożony moduł. Powinien być uruchomiony stan środowiska uruchomieniowego modułu, który został dodany.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="Azure Stack Edge":::

### <a name="configure-the-azure-iot-tools-extension"></a>Konfigurowanie rozszerzenia narzędzi Azure IoT Tools

Postępuj zgodnie z tymi instrukcjami, aby nawiązać połączenie z Centrum IoT Hub przy użyciu rozszerzenia narzędzi Azure IoT Tools.

1. W Visual Studio Code wybierz pozycję Widok > Explorer. Lub naciśnij klawisze Ctrl + Shift + E.
1. W lewym dolnym rogu karty Eksplorator wybierz pozycję Azure IoT Hub.
1. Wybierz ikonę Więcej opcji, aby wyświetlić menu kontekstowe. Następnie wybierz pozycję Ustaw IoT Hub parametry połączenia.
1. Po wyświetleniu pola wejściowego wprowadź parametry połączenia IoT Hub. 

   * Aby uzyskać parametry połączenia, przejdź do IoT Hub w Azure Portal a następnie kliknij pozycję Zasady dostępu współdzielonego w okienku nawigacji po lewej stronie.
   * Kliknij pozycję iothubowner, aby uzyskać dostęp do kluczy dostępu współdzielonego.
   * Skopiuj parametry połączenia — klucz podstawowy i wklej je w polu wejściowym na programu vscode.

   Parametry połączenia będą wyglądać następująco:<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Jeśli połączenie zakończy się pomyślnie, zostanie wyświetlona lista urządzeń brzegowych. Powinna zostać wyświetlona Azure Stack krawędź. Teraz możesz zarządzać urządzeniami IoT Edge i korzystać z usługi Azure IoT Hub za pomocą menu kontekstowego. Aby wyświetlić moduły wdrożone na urządzeniu brzegowym, w obszarze Azure Stack urządzenie rozwiń węzeł moduły.
    
## <a name="troubleshooting"></a>Rozwiązywanie problemów

* Dostęp do interfejsu API Kubernetes (polecenia kubectl).

    * Postępuj zgodnie z dokumentacją, aby skonfigurować maszynę pod kątem [dostępu do klastra Kubernetes](https://review.docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-create-kubernetes-cluster?toc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Ftoc.json&bc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Fbreadcrumb%2Ftoc.json&branch=release-tzl#debug-kubernetes-issues).
    * Wszystkie wdrożone moduły IoT Edge używają `iotedge` przestrzeni nazw. Upewnij się, że podczas korzystania z polecenia kubectl.
* Dzienniki modułów

    `iotedge`Narzędzie nie jest dostępne do uzyskiwania dzienników. Aby wyświetlić dzienniki lub potok do pliku, należy użyć [dzienników polecenia kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)  . Przykład: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`
* Metryki na węźle i

    Użyj [polecenia kubectl góry](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)  , aby wyświetlić metryki z i węzła. (Ta funkcja będzie dostępna w następnej Azure Stack wersji brzegowej. >v2007)<br/>`kubectl top pods -n iotedge`
* Obsługa sieci modułów na potrzeby odnajdywania modułów na Azure Stack Edge wymagane jest, aby moduł miał powiązanie portów hosta w opcjach. Następnie moduł będzie adresować `moduleName:hostport` .
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* Instalowanie woluminu

    Uruchomienie modułu nie powiedzie się, jeśli kontener podejmie próbę zainstalowania woluminu do istniejącego i niepustego katalogu.
* Pamięć współdzielona

    Pamięć współdzielona na Azure Stack zasobów brzegowych jest obsługiwana w ramach każdego z przestrzeni nazw w dowolnym systemie za pomocą hosta IPC.
    Konfigurowanie pamięci współdzielonej w module krawędzi na potrzeby wdrożenia za pośrednictwem IoT Hub.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    (Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API.
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* Doświadczonych Pod wspólną lokalizacją

    W przypadku wdrażania niestandardowych rozwiązań do wnioskowania, które komunikują się z funkcją analizy filmów wideo na żywo za pośrednictwem usługi gRPC, należy upewnić się, że wszystkie moduły są wdrożone w tych samych węzłach co na żywo modułach analizy wideo.

    * Opcja 1 — Używanie koligacji węzłów i wbudowanych etykiet węzłów dla wspólnej lokalizacji.

    Obecnie NodeSelector Konfiguracja niestandardowa prawdopodobnie nie jest opcją, ponieważ użytkownicy nie mają dostępu do ustawiania etykiet w węzłach. Jednak w zależności od topologii i konwencji nazewnictwa klienta może być możliwe użycie [wbudowanych etykiet węzłów](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels). Sekcja nodeAffinity odAzure Stack wołująca się do zasobów brzegowych z funkcją analizy filmów wideo na żywo może zostać dodana do manifestu wnioskowania w celu osiągnięcia wspólnej lokalizacji.
    * Opcja 2 — Użyj koligacji pod kątem wspólnej lokalizacji (zalecane).
Kubernetes obsługuje [koligację pod](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)  , która może zaplanować zasobniki w tym samym węźle. Sekcja podAffinity odwołująca się do modułu analizy filmów wideo na żywo może zostać dodana do manifestu w celu uzyskania wspólnej lokalizacji.

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```

## <a name="next-steps"></a>Następne kroki

Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie metod bezpośrednich. [Wywoływanie metod bezpośrednich](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls) w module.

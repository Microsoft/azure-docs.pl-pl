---
title: Aprowizowanie aktualizacji urządzenia dla Azure IoT Hub Agent| Microsoft Docs
description: Aprowizowanie aktualizacji urządzenia dla Azure IoT Hub Agent
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 812de4850c6c3577346915a0072ea11c60f7ba73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365454"
---
# <a name="device-update-agent-provisioning"></a>Inicjowanie obsługi agenta aktualizacji urządzenia

Agent modułu aktualizacji urządzenia może działać razem z innymi procesami systemowym [i modułami IoT Edge,](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) które łączą się z IoT Hub jako część tego samego urządzenia logicznego. W tej sekcji opisano, jak aprowizować agenta aktualizacji urządzenia jako tożsamość modułu. 


## <a name="module-identity-vs-device-identity"></a>Tożsamość modułu a tożsamość urządzenia

W IoT Hub każdej tożsamości urządzenia można utworzyć maksymalnie 50 tożsamości modułów. Każda tożsamość modułu niejawnie generuje bliźniacze reprezentacji modułu. Po stronie urządzenia zestawy IOT HUB umożliwiają tworzenie modułów, w których każdy z nich otwiera niezależne połączenie z IoT Hub. Tożsamość modułu i bliźniacza bliźniacza bliźniacza tożsamość modułu zapewniają podobne możliwości, jak tożsamość urządzenia i bliźniacza bliźniacza reprezentacji urządzenia, ale z bardziej szczegółowymi możliwościami. [Dowiedz się więcej o tożsamościach modułów w IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>Obsługa aktualizacji urządzenia

Następujące typy urządzeń IoT są obecnie obsługiwane przez aktualizację urządzenia:

* Urządzenia z systemem Linux (IoT Edge i inne IoT Edge urządzenia):
    * Obraz aktualizacji A/B:
        - Yocto — ARM64 (obraz referencyjny), rozszerzalny za [](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) pośrednictwem open source, aby tworzyć własne obrazy dla innej architektury zgodnie z potrzebami.
        - Symulator systemu Ubuntu 18.04
       
    * Agent pakietów obsługuje kompilacje dla następujących platform/architektur:
        - Ubuntu Server 18.04 x64 Package Agent 
        - Debian 9 
        
* Urządzenia ograniczone:
    * Przykłady agenta aktualizacji urządzeń z systemem AzureRTOS: samouczek dotyczący aktualizacji [Azure IoT Hub dla systemu operacyjnego Azure-Real-Time-Operating-System](device-update-azure-real-time-operating-system.md)

* Odłączone urządzenia: 
    * [Opis obsługi aktualizacji odłączonych urządzeń](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Wymagania wstępne  

W przypadku konfigurowania urządzenia IoT/urządzenia IoT Edge aktualizacji [](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts)opartych na pakietach dodaj packages.microsoft.com do repozytoriów maszyny, następujące kroki:

1. Zaloguj się na maszynie lub urządzeniu IoT, na którym zamierzasz zainstalować agenta aktualizacji urządzenia.

1. Otwórz okno terminalu.

1. Zainstaluj konfigurację repozytorium, która odpowiada systemowi operacyjneowi urządzenia.
    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. Skopiuj wygenerowaną listę do katalogu sources.list.d.
    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Zainstaluj klucz publiczny Microsoft GPG.
    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>Jak aprowizować agenta aktualizacji urządzenia jako tożsamość modułu

W tej sekcji opisano, jak aprowizować agenta aktualizacji urządzenia jako tożsamość modułu na urządzeniach z obsługą IoT Edge, urządzeniach IoT spoza usługi Edge i innych urządzeniach IoT.


### <a name="on-iot-edge-enabled-devices"></a>Na IoT Edge włączone urządzenia

Postępuj zgodnie z tymi instrukcjami, aby aprowizować agenta aktualizacji urządzenia IoT Edge urządzeniach z [włączoną obsługą usługi](https://docs.microsoft.com/azure/iot-edge).

1. Postępuj zgodnie z [instrukcjami, aby zainstalować i aprowizować Azure IoT Edge uruchomieniowego.](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true)

1. Instalowanie agenta aktualizacji obrazu aktualizacji urządzenia
    - W artefaktach [](https://github.com/Azure/iot-hub-device-update/releases) są dostępne przykładowe obrazy, które można wypróbować w różnych wersjach przy użyciu obrazu podstawowego (adu-base-image) i jednego obrazu aktualizacji (adu-update-image). Zobacz przykład [sposobu flashować obraz na urządzeniu IoT Hub .](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)  

1. Instalowanie agenta aktualizacji pakietu aktualizacji urządzenia  
    - Najnowsze wersje agenta z programu packages.miscrosoft.com: zaktualizuj listy pakietów na urządzeniu i zainstaluj pakiet agenta aktualizacji urządzenia oraz jego zależności przy użyciu:   
    ```shell
    sudo apt-get update
    ```
    
    ```shell
    sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
    ```
    
    - W przypadku nadchodzących wersji release candidate z [usługi Artifacts:](https://github.com/Azure/iot-hub-device-update/releases) pobierz plik dep na maszynę, na której chcesz zainstalować agenta aktualizacji urządzenia, a następnie:
     ```shell
    Sudo apt-get install -y ./"<PATH TO FILE>"/"<.DEP FILE NAME>"
     ```
    
1. Teraz możesz uruchomić agenta aktualizacji urządzenia na urządzeniu IoT Edge urządzeniu. 

### <a name="on-non-edge-iot-linux-devices"></a>Na urządzeniach z systemem Linux innych niż Edge

Postępuj zgodnie z tymi instrukcjami, aby aprowizować agenta aktualizacji urządzenia na urządzeniach z systemem Linux IoT.

1. Zainstaluj usługę IoT Identity Service i dodaj najnowszą wersję do urządzenia IoT. 
    1. Zaloguj się do maszyny lub urządzenia IoT.
    1. Otwórz okno terminalu.
    1.  Zainstaluj najnowszą [usługę IoT Identity Service](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) na urządzeniu IoT przy użyciu tego polecenia:
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. Aprowizowanie usługi IoT Identity w celu uzyskania informacji o urządzeniu IoT.
    * Utwórz niestandardową kopię szablonu konfiguracji, aby można było dodać informacje dotyczące aprowizowania. W terminalu wprowadź poniższe polecenie.
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. Następnie edytuj plik konfiguracji, aby uwzględnić ciąg połączenia urządzenia, które ma działać jako aprowizator dla tego urządzenia lub maszyny. W terminalu wprowadź poniższe polecenie.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Powinien zostać wyświetlony komunikat podobny do poniższego przykładu:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="Diagram pliku konfiguracji usługi IoT Identity Service." lightbox="media/understand-device-update/config.png":::

    1. W tym samym oknie nano znajdź blok z wartością "Ręczne aprowizowanie przy użyciu parametrów połączenia".
    1. W oknie usuń symbol "#" przed "aprowizowanie"
    1. W oknie usuń symbol "#" przed symbolem "source" 
    1. W oknie usuń symbol "#" przed "connection_string"
    1. W oknie usuń ciąg w cudzysłowie z prawej strony ciągu "connection_string", a następnie dodaj w tym miejscu swoje ciągi połączenia 
    1. Zapisz zmiany w pliku za pomocą kombinacji klawiszy "Ctrl+X", a następnie naciśnij klawisz "Y", aby zapisać zmiany. 
    
1.  Teraz zastosuj i uruchom ponownie usługę IoT Identity za pomocą poniższego polecenia. Powinien zostać wyświetlony "Gotowe!" printout oznacza, że pomyślnie skonfigurowano usługę IoT Identity Service.

    > [!Note]
    > Usługa IoT Identity rejestruje tożsamości modułów w usłudze IoT Hub przy użyciu kluczy symetrycznych.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  Na koniec zainstaluj agenta aktualizacji urządzenia. W artefaktach [](https://github.com/Azure/iot-hub-device-update/releases) są dostępne przykładowe obrazy, które można wypróbować w różnych wersjach przy użyciu obrazu podstawowego (adu-base-image) i jednego obrazu aktualizacji (adu-update-image). Zobacz przykład [sposobu flashować obraz na urządzeniu IoT Hub .](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)

1.  Teraz możesz uruchomić agenta aktualizacji urządzenia na urządzeniu IoT. 

### <a name="other-iot-devices"></a>Inne urządzenia IoT

Agenta aktualizacji urządzenia można również skonfigurować bez usługi IoT Identity na potrzeby testowania lub na ograniczonych urządzeniach. Wykonaj poniższe kroki, aby aprowizować agenta aktualizacji urządzenia przy użyciu parametrów połączenia (z modułu lub urządzenia).

1.  W artefaktach [](https://github.com/Azure/iot-hub-device-update/releases) są dostępne przykładowe obrazy, które można wypróbować w różnych wersjach przy użyciu obrazu podstawowego (adu-base-image) i jednego obrazu aktualizacji (adu-update-image). Zobacz przykład [sposobu flashować obraz na urządzeniu IoT Hub .](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)

1.  Zaloguj się do maszyny lub IoT Edge/urządzenia IoT.
    
1.  Otwórz okno terminalu.

1.  Dodaj ciąg połączenia do pliku [konfiguracji aktualizacji urządzenia:](device-update-configuration-file.md)
    1. Wprowadź poniższe informacje w oknie terminalu:
        - [Użyj aktualizacji](device-update-ubuntu-agent.md) pakietu: sudo nano /etc/adu/adu-conf.txt
        - [Użyj aktualizacji obrazu:](device-update-raspberry-pi.md) sudo nano /adu/adu-conf.txt
       
    1. Powinno zostać otwarte okno z tekstem. Usuń cały ciąg po ciągu "connection_String=" przy pierwszej aprowizce agenta aktualizacji urządzenia na urządzeniu IoT. Jest to po prostu tekst właściciela.
    
    1. W terminalu zastąp ciąg "<-your-connection-string>" ciągami połączenia urządzenia dla wystąpienia agenta aktualizacji urządzenia.
    
        > [!Important]
        > Nie dodawaj cudzysłowów wokół parametrów połączenia.
        ```shell
        connection_string=<ADD CONNECTION STRING HERE>
        ```
       
    1. Wprowadź i zapisz.
    
1.  Teraz możesz uruchomić agenta aktualizacji urządzenia na urządzeniu IoT. 


## <a name="how-to-start-the-device-update-agent"></a>Jak uruchomić agenta aktualizacji urządzenia

W tej sekcji opisano sposób uruchamiania i weryfikowania agenta aktualizacji urządzenia jako tożsamości modułu uruchomionej pomyślnie na urządzeniu IoT.

1.  Zaloguj się na komputerze lub urządzeniu z zainstalowanym agentem aktualizacji urządzenia.

1.  Otwórz okno terminalu i wprowadź poniższe polecenie.
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  Stan agenta można sprawdzić za pomocą poniższego polecenia. Jeśli widzisz jakiekolwiek problemy, zapoznaj się z tym [przewodnikiem rozwiązywania problemów.](troubleshoot-device-update.md)
    ```shell
    sudo systemctl status adu-agent
    ```
    
    Powinien zostać wyświetlony stan OK.

1.  W portalu IoT Hub przejdź do urządzenia IoT lub IoT Edge, aby znaleźć urządzenie skonfigurowane za pomocą agenta aktualizacji urządzenia. W tym miejscu zobaczysz agenta aktualizacji urządzenia uruchomionego jako moduł. Na przykład:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Diagram nazwy modułu aktualizacji urządzenia." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Jak skompilować i uruchomić agenta aktualizacji urządzenia

Możesz również skompilować i zmodyfikować własnego agenta aktualizacji urządzenia klienta.

Postępuj zgodnie z [instrukcjami, aby](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) skompilować agenta aktualizacji urządzenia ze źródła.

Po pomyślnym sbudowania agenta należy uruchomić [agenta.](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md)

Teraz należy wprowadzić zmiany wymagane do uwzględnienia agenta w obrazie.  Zobacz, jak [zmodyfikować](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) agenta aktualizacji urządzenia, aby uzyskać wskazówki.


## <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

W przypadku problemów zapoznaj się z przewodnikiem rozwiązywania problemów z aktualizacją urządzenia dla usługi IoT Hub, [aby](troubleshoot-device-update.md) ułatwić rozwiązanie wszelkich możliwych problemów i zebrać informacje niezbędne do podania firmie Microsoft.


## <a name="next-steps"></a>Następne kroki

Możesz użyć następujących wstępnie sbudowaną obrazów i plików binarnych, aby uzyskać prosty pokaz aktualizacji urządzenia dla IoT Hub:

- [Aktualizacja obrazu: Wprowadzenie urządzenie Raspberry Pi 3 B+ Reference Yocto Image](device-update-raspberry-pi.md) rozszerzalne za pośrednictwem usługi open source w celu tworzenia własnych obrazów dla innej architektury zgodnie z potrzebami.

- [Wprowadzenie użyciu agenta referencyjnego symulatora systemu Ubuntu (18.04 x64)](device-update-simulator.md)

- [Aktualizacja pakietu:Wprowadzenie użyciu agenta pakietów Ubuntu Server 18.04 x64](device-update-ubuntu-agent.md)

- [Samouczek dotyczący aktualizacji Azure IoT Hub dla systemu operacyjnego Azure-Real-Time-Operating-System](device-update-azure-real-time-operating-system.md)


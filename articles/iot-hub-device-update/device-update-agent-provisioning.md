---
title: Aktualizacja urządzenia dla usługi Azure IoT Hub Agent | Microsoft Docs
description: Aktualizacja urządzenia dla usługi Azure IoT Hub Agent
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: e778c7ee14d2115bf6d8cf7f12ceaa61e364a4a2
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120190"
---
# <a name="device-update-agent-provisioning"></a>Inicjowanie obsługi administracyjnej agenta

Agent modułu aktualizacji urządzeń można uruchomić wraz z innymi procesami systemowymi i [modułami IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) , które łączą się z IoT Hub w ramach tego samego urządzenia logicznego. W tej sekcji opisano sposób aprowizacji agenta aktualizacji urządzeń jako tożsamość modułu. 


## <a name="module-identity-vs-device-identity"></a>Tożsamość modułu a tożsamość urządzenia

W IoT Hub w obszarze każdej tożsamości urządzenia można utworzyć do 50 tożsamości modułów. Każda tożsamość modułu niejawnie generuje sznurek modułu. Po stronie urządzenia zestawy SDK urządzeń IoT Hub umożliwiają tworzenie modułów, w których każdy z nich otwiera niezależne połączenie z IoT Hub. Funkcja tożsamość modułu i sznurki modułowe zapewniają podobne możliwości, jak tożsamość urządzenia i sznurki urządzeń, ale z większą szczegółowością. [Dowiedz się więcej o tożsamościach modułów w IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>Obsługa aktualizacji urządzeń

Następujące typy urządzeń IoT są obecnie obsługiwane z aktualizacją urządzenia:

* Urządzenia z systemem Linux (IoT Edge i urządzenia nieIoT Edgeowe):
    * Aktualizacja obrazu A/B:
        - Yocto-ARM64 (obraz referencyjny), rozszerzalny za pomocą funkcji Open Source, aby [tworzyć własne obrazy](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) dla innej architektury, zgodnie z potrzebami.
        - Symulator 18,04 Ubuntu
       
    * Obsługiwane są kompilacje agenta pakietu dla następujących platform/architektur:
        - Agent pakietu Ubuntu Server 18,04 x64 
        - Debian 9 
        
* Urządzenia z ograniczeniami:
    * Przykłady agenta aktualizacji urządzeń AzureRTOS: [Aktualizacja urządzenia dla IoT Hub platformy Azure — samouczek dotyczący systemu operacyjnego Azure — w czasie rzeczywistym](device-update-azure-real-time-operating-system.md)

* Odłączone urządzenia: 
    * [Omówienie obsługi aktualizacji odłączonego urządzenia](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Wymagania wstępne  

Jeśli konfigurujesz urządzenie IoT/IoT Edge na potrzeby [aktualizacji opartych na pakietach](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts), Dodaj Packages.Microsoft.com do repozytoriów maszyn, wykonując następujące czynności:

1. Zaloguj się na komputerze lub urządzeniu IoT, na którym zamierzasz zainstalować agenta aktualizacji urządzeń.

1. Otwórz okno terminalu.

1. Zainstaluj konfigurację repozytorium zgodną z systemem operacyjnym Twojego urządzenia.
    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. Skopiuj wytworzoną listę do katalogu sources. list. d.
    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Zainstaluj klucz publiczny programu Microsoft GPG.
    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>Jak zainicjować obsługę agenta aktualizacji urządzeń jako tożsamość modułu

W tej sekcji opisano, jak udostępnić agentowi aktualizacji urządzenia jako tożsamość modułu na IoT Edge włączonych urządzeniach, niebrzegowych urządzeniach IoT i innych urządzeniach IoT.


### <a name="on-iot-edge-enabled-devices"></a>Na IoT Edge włączonych urządzeniach

Postępuj zgodnie z tymi instrukcjami, aby zainicjować obsługę administracyjną agenta aktualizacji urządzeń na [IoT Edge włączonych urządzeniach](https://docs.microsoft.com/azure/iot-edge).

1. Postępuj zgodnie z instrukcjami, aby [zainstalować i udostępnić środowisko uruchomieniowe Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true).

1. Następnie Zainstaluj agenta aktualizacji urządzeń z [artefaktów](https://github.com/Azure/iot-hub-device-update/releases) , aby teraz można było uruchomić agenta aktualizacji urządzenia na urządzeniu IoT Edge.


### <a name="on-non-edge-iot-linux-devices"></a>Na urządzeniach z systemem IoT Linux bez krawędzi

Postępuj zgodnie z tymi instrukcjami, aby zainicjować obsługę administracyjną agenta aktualizacji urządzeń na urządzeniach z systemem IoT Linux.

1. Zainstaluj usługę tożsamość IoT i Dodaj najnowszą wersję do urządzenia IoT. 
    1. Zaloguj się na komputerze lub urządzeniu IoT.
    1. Otwórz okno terminalu.
    1.  Zainstaluj najnowszą [usługę usługi IoT Identity](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) na urządzeniu IoT przy użyciu tego polecenia:
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. Inicjowanie obsługi usługi tożsamości IoT w celu pobrania informacji o urządzeniu IoT.
    * Utwórz niestandardową kopię szablonu konfiguracji, aby można było dodać informacje o aprowizacji. W terminalu wprowadź poniższe polecenie.
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. Następnie edytuj plik konfiguracji w celu uwzględnienia parametrów połączenia urządzenia, które ma pełnić rolę aprowizacji dla tego urządzenia lub komputera. W terminalu wprowadź poniższe polecenie.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Powinien zostać wyświetlony komunikat podobny do następującego:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="Diagram pliku konfiguracji usługi IoT Identity Service." lightbox="media/understand-device-update/config.png":::

    1. W tym samym oknie nano Server Znajdź blok z opcją "Ręczne inicjowanie obsługi przy użyciu parametrów połączenia".
    1. W oknie Usuń symbol "#" przed "aprowizacji"
    1. W oknie Usuń symbol "#" przed "source" 
    1. W oknie Usuń symbol "#" przed "connection_string"
    1. W oknie Usuń ciąg w cudzysłowie po prawej stronie elementu "connection_string", a następnie Dodaj tam parametry połączenia 
    1. Zapisz zmiany w pliku przy użyciu klawiszy "Ctrl + X", a następnie "Y" i naciśnij klawisz ENTER, aby zapisać zmiany. 
    
1.  Teraz Zastosuj i uruchom ponownie usługę tożsamość IoT przy użyciu poniższego polecenia. Powinna zostać wyświetlona wartość "gotowe!" wydruk oznaczający, że usługa IoT Identity została pomyślnie skonfigurowana.

    > [!Note]
    > Usługa tożsamość IoT rejestruje tożsamości modułów z IoT Hub przy użyciu kluczy symetrycznych.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  Na koniec Zainstaluj agenta aktualizacji urządzeń z [artefaktów](https://github.com/Azure/iot-hub-device-update/releases) , a teraz możesz uruchomić agenta aktualizacji urządzenia na urządzeniu IoT Edge.


### <a name="other-iot-devices"></a>Inne urządzenia IoT

Agenta aktualizacji urządzeń można również skonfigurować bez usługi tożsamości IoT do testowania lub urządzeń z ograniczeniami. Wykonaj poniższe kroki, aby zainicjować obsługę administracyjną agenta aktualizacji urządzenia przy użyciu parametrów połączenia (z modułu lub urządzenia).

1.  Zainstaluj agenta aktualizacji urządzeń z [artefaktów](https://github.com/Azure/iot-hub-device-update/releases).

1.  Zaloguj się na komputerze lub urządzeniu IoT Edge/urządzeniu IoT.
    
1.  Otwórz okno terminalu.

1.  Dodaj parametry połączenia do [pliku konfiguracji aktualizacji urządzenia](device-update-configuration-file.md):
    1. Wprowadź poniżej w oknie terminalu:
        - Użycie [aktualizacji pakietów](device-update-ubuntu-agent.md) : sudo nano/etc/adu/adu-conf.txt
        - [Aktualizacje obrazów](device-update-raspberry-pi.md) : sudo nano/adu/adu-conf.txt
       
    1. Powinno zostać wyświetlone okno z otwartym tekstem. Usuń cały ciąg następujący po "connection_String =" przy pierwszym udostępnieniu agenta aktualizacji urządzenia na urządzeniu IoT. Jest to tylko tekst zastępczy.
    
    1. W terminalu Zastąp <ciągu połączenia> z parametrami połączenia urządzenia dla wystąpienia agenta aktualizacji urządzenia.
    
        > [!Important]
        > Nie należy dodawać cudzysłowów wokół parametrów połączenia.
        
        - connection_string =<ciągu połączenia>
       
    1. Wprowadź i Zapisz.
    
1.  Teraz można przystąpić do uruchamiania agenta aktualizacji urządzenia na urządzeniu IoT Edge. 


## <a name="how-to-start-the-device-update-agent"></a>Jak uruchomić agenta aktualizacji urządzeń

W tej sekcji opisano, jak uruchomić i zweryfikować agenta aktualizacji urządzenia jako tożsamość modułu pomyślnie uruchomiona na urządzeniu IoT.

1.  Zaloguj się do komputera lub urządzenia, na którym zainstalowano agenta aktualizacji urządzenia.

1.  Otwórz okno terminalu i wprowadź poniższe polecenie.
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  Stan agenta można sprawdzić przy użyciu poniższego polecenia. Jeśli zobaczysz jakiekolwiek problemy, zapoznaj się z tym [przewodnikiem rozwiązywania problemów](troubleshoot-device-update.md).
    ```shell
    sudo systemctl status adu-agent
    ```
    
    Powinien zostać wyświetlony stan OK.

1.  W portalu IoT Hub przejdź do pozycji urządzenie IoT lub IoT Edge urządzenia, aby znaleźć urządzenie skonfigurowane przy użyciu agenta aktualizacji urządzeń. Zostanie wyświetlony Agent aktualizacji urządzenia uruchomiony jako moduł. Na przykład:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Diagram nazwy modułu aktualizacji urządzenia." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Jak skompilować i uruchomić agenta aktualizacji urządzeń

Możesz również utworzyć i zmodyfikować własnego agenta aktualizacji urządzeń klientów.

Postępuj zgodnie z instrukcjami, aby [skompilować](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) agenta aktualizacji urządzenia ze źródła.

Po pomyślnym skompilowaniu agenta zostanie [uruchomiony](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) Agent programu.

Teraz wprowadź zmiany, które są konieczne do włączenia agenta do obrazu.  Zapoznaj się z tematem jak [zmodyfikować](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) agenta aktualizacji urządzeń, aby uzyskać wskazówki.


## <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Jeśli występują problemy, zapoznaj się z [przewodnikiem rozwiązywania problemów](troubleshoot-device-update.md) z aktualizacją urządzenia, aby uzyskać IoT Hub.


## <a name="next-steps"></a>Następne kroki

Możesz użyć następujących wstępnie skompilowanych obrazów i plików binarnych, aby zapoznać się z prostą prezentacją aktualizacji urządzenia dla IoT Hub:

- [Aktualizacja obrazu: wprowadzenie z Raspberry Pi 3 B + Dokumentacja yocto Image](device-update-raspberry-pi.md) rozszerzalna za pośrednictwem programu Open Source w celu utworzenia własnych obrazów dla innej architektury, zgodnie z potrzebami.

- [Wprowadzenie przy użyciu agenta referencyjnego symulatora Ubuntu (18,04 x64)](device-update-simulator.md)

- [Aktualizacja pakietu: Wprowadzenie przy użyciu agenta pakietu Ubuntu Server 18,04 x64](device-update-ubuntu-agent.md)

- [Samouczek dotyczący aktualizacji urządzeń z platformą Azure IoT Hub dla systemu operacyjnego Azure — w czasie rzeczywistym](device-update-azure-real-time-operating-system.md)


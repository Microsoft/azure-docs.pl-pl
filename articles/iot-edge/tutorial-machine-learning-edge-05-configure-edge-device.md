---
title: 'Samouczek: Konfigurowanie urządzenia Azure IoT Edgego — Uczenie maszynowe na IoT Edge'
description: W tym samouczku skonfigurujesz maszynę wirtualną platformy Azure z systemem Linux jako urządzenie Azure IoT Edge, które będzie pełnić rolę przezroczystej bramy.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, devx-track-azurecli
ms.openlocfilehash: b59f8343c9dff07a32accd471f70ddf9f5309b8d
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463089"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Samouczek: Konfigurowanie urządzenia Azure IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

W tym artykule skonfigurujemy maszynę wirtualną platformy Azure z systemem Linux jako urządzenie Azure IoT Edge, które działa jako niewidoczna brama. Konfiguracja niejawnej bramy umożliwia urządzeniom łączenie się z usługą Azure IoT Hub za pomocą bramy bez znajomości bramy. W tym samym czasie użytkownik korzystający z urządzeń w IoT Hub nie rozpoznaje pośredniego urządzenia bramy. Ostatecznie dodamy analizę Edge do naszego systemu przez dodanie modułów IoT Edge do przezroczystej bramy.

>[!NOTE]
>Koncepcje przedstawione w tym samouczku dotyczą wszystkich wersji IoT Edge, ale przykładowe urządzenie utworzone w celu wypróbowania scenariusza zostanie uruchomione IoT Edge wersji 1,1.

Kroki opisane w tym artykule są zwykle wykonywane przez dewelopera chmury.

W tej części samouczka dowiesz się, jak:

> [!div class="checklist"]
>
> * Utwórz certyfikaty, aby umożliwić urządzeniu bramy bezpieczne łączenie się z urządzeniami podrzędnymi.
> * Utwórz urządzenie IoT Edge.
> * Utwórz maszynę wirtualną platformy Azure, aby symulować urządzenie IoT Edge.

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł jest częścią serii samouczka dotyczącego używania Azure Machine Learning w IoT Edge. Każdy artykuł w serii jest oparty na pracy w poprzednim artykule. Jeśli ten artykuł został już wprowadzony bezpośrednio, zobacz [pierwszy artykuł](tutorial-machine-learning-edge-01-intro.md) w serii.

## <a name="create-certificates"></a>Tworzenie certyfikatów

Aby urządzenie działało jako brama, musi bezpiecznie połączyć się z urządzeniami podrzędnymi. Za pomocą IoT Edge można skonfigurować bezpieczne połączenia między urządzeniami przy użyciu infrastruktury kluczy publicznych (PKI). W takim przypadku zezwalamy urządzeniu IoT w obłączeniu z urządzeniem IoT Edge działającym jako niejawną bramą. Aby zachować uzasadnione zabezpieczenia, urządzenie podrzędne powinno potwierdzić tożsamość urządzenia IoT Edge. Aby uzyskać więcej informacji na temat sposobu używania certyfikatów przez urządzenia IoT Edge, zobacz [Azure IoT Edge szczegóły użycia certyfikatu](iot-edge-certs.md).

W tej sekcji tworzymy certyfikaty z podpisem własnym przy użyciu obrazu platformy Docker, który następnie kompilujemy i uruchamiamy. Wybrano użycie obrazu platformy Docker do wykonania tego kroku, ponieważ zmniejsza to liczbę kroków wymaganych do utworzenia certyfikatów na komputerze deweloperskim systemu Windows. Aby zrozumieć, co możemy zautomatyzować za pomocą obrazu platformy Docker, zobacz [Tworzenie certyfikatów demonstracyjnych do testowania funkcji urządzeń IoT Edge](how-to-create-test-certificates.md).

1. Zaloguj się do maszyny wirtualnej deweloperskiej.
1. Utwórz nowy folder o ścieżce i nazwie **c:\edgeCertificates**.

1. Jeśli jeszcze nie działa, uruchom **Docker for Windows** z menu Start systemu Windows.

1. Otwórz program Visual Studio Code.

1. Wybierz pozycję **plik**  >  **Otwórz folder**, a następnie wybierz pozycję **C: \\ Source \\ IoTEdgeAndMlSample \ \\ Certificates**.

1. W okienku **Eksplorator** kliknij prawym przyciskiem myszy pozycję **pliku dockerfile** i wybierz pozycję **kompilacja obrazu**.

1. W oknie dialogowym Zaakceptuj wartość domyślną dla nazwy i tagu obrazu: \ **Certyfikaty: Najnowsze**.

    ![Zrzut ekranu przedstawiający tworzenie certyfikatów w Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Poczekaj na zakończenie kompilacji.

    > [!NOTE]
    > Może zostać wyświetlone ostrzeżenie o brakującym kluczu publicznym. Można bezpiecznie zignorować to ostrzeżenie. Podobnie zobaczysz ostrzeżenie o zabezpieczeniach, które zaleca sprawdzenie lub zresetowanie uprawnień do obrazu, co jest bezpieczne do ignorowania dla tego obrazu.

1. W oknie terminalu Visual Studio Code Uruchom kontener servicecertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Platforma Docker wyświetli monit o dostęp do dysku **c: \\** . Wybierz pozycję **Udostępnij**.

1. Po wyświetleniu monitu podaj swoje poświadczenia.

1. Po zakończeniu działania kontenera sprawdź następujące pliki w **c: \\ edgeCertificates**:

    * c: \\ edgeCertificates \\ Certyfikaty \\ Azure-IoT-test-Only. root. ca. CERT. pem
    * c: \\ edgeCertificates \\ Certyfikaty \\ New-Edge-Device-Full-Chain. CERT. pem
    * c: \\ edgeCertificates \\ Certyfikaty \\ New-Edge-Device. CERT. pem
    * c: \\ edgeCertificates \\ Certyfikaty \\ New-Edge-Device. CERT. pfx
    * c: \\ edgeCertificates \\ Private \\ New-Edge-Device. Key. pem

## <a name="upload-certificates-to-azure-key-vault"></a>Przekaż certyfikaty do Azure Key Vault

Aby bezpiecznie przechowywać certyfikaty i udostępnić je z wielu urządzeń, przekażemy certyfikaty do Azure Key Vault. Jak widać na poprzedniej liście, istnieją dwa typy plików certyfikatów: PFX i PEM. Plik PFX będzie traktowany jako Key Vault certyfikaty, które mają zostać przekazane do Key Vault. Pliki PEM są zwykłym tekstem i traktujemy je jako wpisy tajne Key Vault. Użyjemy Key Vault wystąpienia skojarzonego z obszarem roboczym Azure Machine Learning utworzonym przez uruchomienie [notesów Jupyter](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks).

1. W [Azure Portal](https://portal.azure.com)przejdź do obszaru roboczego Azure Machine Learning.

1. Na stronie Przegląd w obszarze roboczym Machine Learning Znajdź nazwę **Key Vault**.

    ![Zrzut ekranu pokazujący Kopiowanie nazwy magazynu kluczy.](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. Na komputerze deweloperskim Przekaż certyfikaty do Key Vault. Zastąp **\<subscriptionId\>** i **\<keyvaultname\>** informacjami o zasobach.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. Jeśli zostanie wyświetlony monit, zaloguj się do platformy Azure.

1. Skrypt będzie uruchamiany przez kilka minut z danymi wyjściowymi zawierającymi nowe wpisy Key Vault.

    ![Zrzut ekranu, który pokazuje Key Vault dane wyjściowe skryptu.](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>Tworzenie urządzenia usługi IoT Edge

Aby podłączyć urządzenie Azure IoT Edge do usługi IoT Hub, najpierw tworzymy tożsamość urządzenia w centrum. Przyjmujemy parametry połączenia z tożsamości urządzenia w chmurze i używają jej do konfigurowania środowiska uruchomieniowego na naszym urządzeniu IoT Edge. Po połączeniu się ze skonfigurowanym urządzeniem z centrum można wdrażać moduły i wysyłać komunikaty. Można również zmienić konfigurację urządzenia fizycznego IoT Edge, zmieniając jego tożsamość urządzenia w IoT Hub.

W tym samouczku tworzymy nową tożsamość urządzenia przy użyciu Visual Studio Code. Te kroki można również wykonać za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.

1. Na komputerze deweloperskim Otwórz Visual Studio Code.

1. Rozwiń ramkę **IoT Hub platformy Azure** w widoku **Eksploratora** Visual Studio Code.

1. Wybierz wielokropek, a następnie wybierz pozycję **utwórz IoT Edge urządzenie**.

1. Nadaj urządzeniu nazwę. Dla wygody użyjemy nazwy **aaTurbofanEdgeDevice** , aby posortować je na górze z wymienionych urządzeń.

1. Nowe urządzenie zostanie wyświetlone na liście urządzeń.

    ![Zrzut ekranu pokazujący widok urządzenia w Eksploratorze Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Wdróż maszynę wirtualną platformy Azure

Na potrzeby tego samouczka użyjemy [Azure IoT Edge na](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) obrazie Ubuntu z IoT Edge witryny Azure Marketplace. Azure IoT Edge w obrazie Ubuntu instaluje najnowsze środowisko uruchomieniowe IoT Edge i jego zależności podczas uruchamiania. Wdrażamy maszynę wirtualną przy użyciu:

- Skrypt programu PowerShell `Create-EdgeVM.ps1` .
- Szablon Azure Resource Manager, `IoTEdgeVMTemplate.json` .
- Skrypt powłoki, `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Włącz wdrażanie programistyczne

Aby użyć obrazu z witryny Azure Marketplace w ramach wdrożenia skryptowego, musimy włączyć wdrożenie programistyczne dla obrazu.

1. Zaloguj się w witrynie Azure Portal.

1. Wybierz pozycję **Wszystkie usługi**.

1. Na pasku wyszukiwania wprowadź i wybierz pozycję **Marketplace**.

1. Na pasku wyszukiwania portalu Marketplace wprowadź i wybierz **Azure IoT Edge w Ubuntu**.

1. Wybierz hiperlink **Rozpocznij** , aby wdrożyć program programowo.

1. Wybierz przycisk **Włącz** , a następnie wybierz pozycję **Zapisz**.

    ![Zrzut ekranu pokazujący włączenie wdrożenia programistycznego dla maszyny wirtualnej.](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Zobaczysz powiadomienie o powodzeniu.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Następnie uruchom skrypt, aby utworzyć maszynę wirtualną dla urządzenia IoT Edge.

1. Otwórz okno programu PowerShell i przejdź do katalogu **EdgeVM** .

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. Uruchom skrypt, aby utworzyć maszynę wirtualną.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. Po wyświetleniu monitu podaj wartości dla każdego parametru. W przypadku subskrypcji, grupy zasobów i lokalizacji zalecamy użycie takich samych wartości jak w przypadku wszystkich zasobów w tym samouczku.

    * **Identyfikator subskrypcji platformy Azure**: znaleziono w Azure Portal.
    * **Nazwa grupy zasobów**: dopamiętanie nazwy do grupowania zasobów dla tego samouczka.
    * **Lokalizacja**: Lokalizacja platformy Azure, w której zostanie utworzona maszyna wirtualna. Na przykład westus2 lub northeurope. Aby uzyskać więcej informacji, Zobacz wszystkie [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: nazwa konta administratora, którego będziesz używać do logowania się do maszyny wirtualnej.
    * **AdminPassword**: hasło, które ma zostać ustawione dla nazwy użytkownika administratora na maszynie wirtualnej.

1. Aby skrypt mógł skonfigurować maszynę wirtualną, zaloguj się do platformy Azure przy użyciu poświadczeń skojarzonych z subskrypcją platformy Azure, której używasz.

1. Skrypt potwierdza informacje dotyczące tworzenia maszyny wirtualnej. Wybierz pozycję **y** lub **Enter** , aby kontynuować.

1. Skrypt jest uruchamiany przez kilka minut, ponieważ wykonuje następujące czynności:

    * Tworzy grupę zasobów, jeśli jeszcze nie istnieje
    * Tworzy maszynę wirtualną
    * Dodaje wyjątki sieciowej grupy zabezpieczeń dla maszyny wirtualnej dla portów 22 (SSH), 5671 (AMQP), 5672 (AMPQ) i 443 (TLS)
    * Instaluje [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli-apt)

1. Skrypt wyprowadza parametry połączenia SSH w celu nawiązania połączenia z maszyną wirtualną. Skopiuj parametry połączenia dla następnego kroku.

    ![Zrzut ekranu pokazujący kopiowanie parametrów połączenia SSH dla maszyny wirtualnej.](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Nawiązywanie połączenia z urządzeniem usługi IoT Edge

W następnych sekcjach opisano konfigurację utworzonej maszyny wirtualnej platformy Azure. Pierwszym krokiem jest nawiązanie połączenia z maszyną wirtualną.

1. Otwórz wiersz polecenia i wklej parametry połączenia SSH skopiowane z danych wyjściowych skryptu. Wprowadź własne informacje dotyczące nazwy użytkownika, sufiksu i regionu zgodnie z wartościami podanymi w skrypcie programu PowerShell w poprzedniej sekcji.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. Po wyświetleniu monitu o potwierdzenie autentyczności hosta wprowadź **wartość tak** , a następnie wybierz **klawisz ENTER**.

1. Po wyświetleniu monitu podaj hasło.

1. Ubuntu wyświetla komunikat powitalny, a następnie powinien zostać wyświetlony monit `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Pobierz certyfikaty Key Vault

Wcześniej w tym artykule zostały przekazane certyfikaty do Key Vault, aby udostępnić je dla naszych IoT Edge urządzeń i urządzeń typu liść. Urządzenie liścia jest urządzeniem podrzędnym, które używa urządzenia IoT Edge jako bramy do komunikowania się z IoT Hub.

W dalszej części tego samouczka będziemy omawiać urządzenie liścia. W tej sekcji Pobierz certyfikaty do urządzenia IoT Edge.

1. W sesji SSH na maszynie wirtualnej z systemem Linux Zaloguj się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

    ```azurecli
    az login
    ```

1. Zostanie wyświetlony monit o otwarcie przeglądarki na stronie logowania do [urządzenia firmy Microsoft](https://microsoft.com/devicelogin) i udostępnienie unikatowego kodu. Te kroki można wykonać na komputerze lokalnym. Zamknij okno przeglądarki po zakończeniu uwierzytelniania.

1. Po pomyślnym uwierzytelnieniu maszyna wirtualna z systemem Linux zaloguje się i wyświetli subskrypcje platformy Azure.

1. Ustaw subskrypcję platformy Azure, która ma być używana dla poleceń interfejsu wiersza polecenia platformy Azure.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Utwórz katalog na maszynie wirtualnej dla certyfikatów.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Pobierz certyfikaty przechowywane w magazynie kluczy: New-Edge-Device-Full-Chain. CERT. pem, New-Edge-Device. Key. pem i Azure-IoT-test-Only. root. ca. CERT. pem.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aktualizowanie konfiguracji urządzenia IoT Edge

Środowisko uruchomieniowe IoT Edge używa pliku/etc/iotedge/config.YAML, aby zachować jego konfigurację. Musimy zaktualizować trzy informacje w tym pliku:

* **Parametry połączenia urządzenia**: parametry połączenia z tożsamości tego urządzenia w IoT Hub
* **Certyfikaty**: certyfikaty do użycia na potrzeby połączeń z urządzeniami podrzędnymi
* **Nazwa hosta**: w pełni kwalifikowana nazwa domeny (FQDN) urządzenia IoT Edge maszyny wirtualnej

Azure IoT Edge na obrazie Ubuntu, który został użyty do utworzenia maszyny wirtualnej IoT Edge, zawiera skrypt powłoki, który aktualizuje plik config. YAML przy użyciu parametrów połączenia.

1. W Visual Studio Code kliknij prawym przyciskiem myszy urządzenie IoT Edge, a następnie wybierz polecenie **Kopiuj parametry połączenia urządzenia**.

    ![Zrzut ekranu pokazujący kopiowanie parametrów połączenia z Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. W sesji SSH Uruchom polecenie, aby zaktualizować plik config. YAML za pomocą parametrów połączenia urządzenia.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Następnie zaktualizujemy certyfikaty i nazwę hosta przez bezpośrednie edytowanie pliku config. YAML.

1. Otwórz plik config. YAML.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Zaktualizuj sekcję certyfikaty w pliku config. YAML, usuwając wiodącą **#** i ustawianą ścieżkę, tak aby plik wyglądał jak Poniższy przykład:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Upewnij się, że w wierszu **Certyfikaty:** nie ma poprzedniego odstępu i że każdy z zagnieżdżonych certyfikatów ma wcięcie dwa spacje.

    Kliknięcie prawym przyciskiem myszy w programie nano spowoduje wklejenie zawartości schowka do bieżącego położenia kursora. Aby zastąpić ciąg, użyj strzałek klawiatury, aby przejść do ciągu, który ma zostać zamieniony, usuń ciąg, a następnie kliknij prawym przyciskiem myszy, aby wkleić go do buforu.

1. W Azure Portal przejdź do maszyny wirtualnej. Skopiuj nazwę DNS (FQDN maszyny) z sekcji **Przegląd** .

1. Wklej nazwę FQDN do sekcji hostname pliku config. yml. Upewnij się, że nazwa zawiera tylko małe litery.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. Zapisz i zamknij plik, wybierając **klawisze Ctrl + X**, **Y** i **Enter**.

1. Uruchom ponownie demona IoT Edge.

    ```bash
    sudo systemctl restart iotedge
    ```

1. Sprawdź stan demona IoT Edge. Po poleceniu wpisz **: q** , aby wyjść.

    ```bash
    systemctl status iotedge
    ```

1. Jeśli zobaczysz błędy (kolor tekstu z prefiksem " \[ Error \] ") w stanie, Sprawdź dzienniki demona pod kątem szczegółowych informacji o błędzie.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Ten samouczek jest częścią zestawu, w którym każdy artykuł kompiluje się w pracy wykonanej w poprzednich. Poczekaj na wyczyszczenie wszystkich zasobów do momentu zakończenia ostatniego samouczka.

## <a name="next-steps"></a>Następne kroki

Właśnie zakończono konfigurowanie maszyny wirtualnej platformy Azure jako nieprzezroczystej bramy IoT Edge. Rozpoczęto od generowania certyfikatów testów przekazanych do Key Vault. Następnie użyto skryptu i szablonu Menedżer zasobów, aby wdrożyć maszynę wirtualną za pomocą obrazu środowiska uruchomieniowego Ubuntu Server 16,04 LTS + Azure IoT Edge w witrynie Azure Marketplace. Po nawiązaniu połączenia z maszyną wirtualną połączono za pośrednictwem protokołu SSH. Następnie zarejestrowano się na platformie Azure i pobrano certyfikaty z Key Vault. Wprowadziliśmy kilka aktualizacji konfiguracji środowiska uruchomieniowego IoT Edge, aktualizując plik config. YAML.

Przejdź do następnego artykułu, aby skompilować moduły IoT Edge.

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie niestandardowych modułów IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md)

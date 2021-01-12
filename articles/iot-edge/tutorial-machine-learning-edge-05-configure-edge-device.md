---
title: 'Samouczek: Konfigurowanie Machine Learning urządzenia IoT Edge na Azure IoT Edge'
description: W tym samouczku skonfigurujesz maszynę wirtualną platformy Azure z systemem Linux jako urządzenie Azure IoT Edge, które będzie pełnić rolę przezroczystej bramy.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, devx-track-azurecli
ms.openlocfilehash: 74d77d8c81455116cec861bf6704c6cb96526561
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121094"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Samouczek: Konfigurowanie urządzenia IoT Edge

W tym artykule skonfigurujemy maszynę wirtualną platformy Azure z systemem Linux jako urządzenie IoT Edge, które działa jako niewidoczna brama. Konfiguracja niejawnej bramy umożliwia urządzeniom łączenie się z usługą Azure IoT Hub za pomocą bramy bez znajomości bramy. W tym samym czasie użytkownik korzystający z urządzeń w usłudze Azure IoT Hub nie rozpoznaje pośredniego urządzenia bramy. Ostatecznie dodamy analizę Edge do naszego systemu przez dodanie modułów IoT Edge do przezroczystej bramy.

Kroki opisane w tym artykule są zwykle wykonywane przez dewelopera chmury.

W tej części samouczka dowiesz się, jak:

> [!div class="checklist"]
>
> * Utwórz certyfikaty, aby umożliwić urządzeniu bramy bezpieczne łączenie się z urządzeniami podrzędnymi.
> * Utwórz urządzenie IoT Edge.
> * Utwórz maszynę wirtualną platformy Azure, aby symulować urządzenie IoT Edge.

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł jest częścią serii samouczka dotyczącego używania Azure Machine Learning w IoT Edge. Każdy artykuł w serii jest oparty na pracy w poprzednim artykule. Jeśli ten artykuł został bezpośrednio osiągnięty, odwiedź [pierwszy artykuł](tutorial-machine-learning-edge-01-intro.md) z serii.

## <a name="create-certificates"></a>Tworzenie certyfikatów

Aby urządzenie działało jako brama, musi być w stanie bezpiecznie połączyć się z urządzeniami podrzędnymi. Azure IoT Edge umożliwia konfigurowanie bezpiecznych połączeń między urządzeniami przy użyciu infrastruktury kluczy publicznych (PKI). W takim przypadku zezwalamy urządzeniu IoT w obłączeniu z urządzeniem IoT Edge działającym jako niejawną bramą. Aby zachować uzasadnione zabezpieczenia, urządzenie podrzędne powinno potwierdzić tożsamość urządzenia IoT Edge. Aby uzyskać więcej informacji na temat sposobu używania certyfikatów przez urządzenia IoT Edge, zobacz [Azure IoT Edge szczegóły użycia certyfikatu](iot-edge-certs.md).

W tej sekcji tworzymy certyfikaty z podpisem własnym przy użyciu obrazu platformy Docker, który następnie kompilujemy i uruchamiamy. Wybrano użycie obrazu platformy Docker do wykonania tego kroku, ponieważ znacznie zmniejsza liczbę kroków niezbędnych do utworzenia certyfikatów na komputerze deweloperskim systemu Windows. Zobacz [Tworzenie certyfikatów demonstracyjnych, aby testować IoT Edge funkcje urządzenia](how-to-create-test-certificates.md) , aby zrozumieć, co możemy zautomatyzować za pomocą obrazu platformy Docker.

1. Zaloguj się do maszyny wirtualnej deweloperskiej.

2. Utwórz nowy folder o ścieżce i nazwie `c:\edgeCertificates` .

3. Jeśli jeszcze nie działa, uruchom **Docker for Windows** z menu Start systemu Windows.

4. Otwórz program Visual Studio Code.

5. Wybierz pozycję **plik**  >  **Otwórz folder...** i wybierz pozycję **C: \\ Source \\ IoTEdgeAndMlSample \ \\ Certificates**.

6. W okienku Eksplorator kliknij prawym przyciskiem myszy pozycję **pliku dockerfile** i wybierz polecenie **Kompiluj obraz**.

7. W oknie dialogowym Zaakceptuj wartość domyślną dla nazwy i tagu obrazu: \ **Certificates: Najnowsza**.

    ![Tworzenie certyfikatów w Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. Poczekaj na zakończenie kompilacji.

    > [!NOTE]
    > Może pojawić się ostrzeżenie dotyczące brakującego klucza publicznego. Można bezpiecznie zignorować to ostrzeżenie. Podobnie zobaczysz ostrzeżenie o zabezpieczeniach, które zaleca sprawdzanie/Resetowanie uprawnień do obrazu, co jest bezpieczne do ignorowania dla tego obrazu.

9. W oknie terminalu Visual Studio Code Uruchom kontener servicecertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Platforma Docker wyświetli monit o dostęp do dysku **c: \\** . Wybierz pozycję **Udostępnij**.

11. Po wyświetleniu monitu podaj swoje poświadczenia.

12. Po zakończeniu działania kontenera sprawdź następujące pliki w **c: \\ edgeCertificates**:

    * c: \\ edgeCertificates \\ Certyfikaty \\ Azure-IoT-test-Only. root. ca. CERT. pem
    * c: \\ edgeCertificates \\ Certyfikaty \\ New-Edge-Device-Full-Chain. CERT. pem
    * c: \\ edgeCertificates \\ Certyfikaty \\ New-Edge-Device. CERT. pem
    * c: \\ edgeCertificates \\ Certyfikaty \\ New-Edge-Device. CERT. pfx
    * c: \\ edgeCertificates \\ Private \\ New-Edge-Device. Key. pem

## <a name="upload-certificates-to-azure-key-vault"></a>Przekaż certyfikaty do Azure Key Vault

Aby bezpiecznie przechowywać certyfikaty i udostępnić je z wielu urządzeń, przekażemy certyfikaty do Azure Key Vault. Jak widać na liście powyżej, istnieją dwa typy plików certyfikatów: PFX i PEM. Będziemy traktować plik PFX jako Key Vault certyfikaty do przekazania do Key Vault. Pliki PEM są zwykłym tekstem, a firma Microsoft traktuje je jako wpisy tajne Key Vault. Będziemy używać Key Vault skojarzonych z obszarem roboczym Azure Machine Learning utworzonym za pomocą [notesów Jupyter](tutorial-machine-learning-edge-04-train-model.md#run-jupyter-notebooks).

1. W [Azure Portal](https://portal.azure.com)przejdź do obszaru roboczego Azure Machine Learning.

2. Na stronie Przegląd w obszarze roboczym Azure Machine Learning Znajdź nazwę **Key Vault**.

    ![Kopiuj nazwę magazynu kluczy](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Na komputerze deweloperskim Przekaż certyfikaty do Key Vault. Zastąp **\<subscriptionId\>** i **\<keyvaultname\>** informacjami o zasobach.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Jeśli zostanie wyświetlony monit, zaloguj się do platformy Azure.

5. Skrypt będzie uruchamiany przez kilka minut z danymi wyjściowymi zawierającymi nowe wpisy Key Vault.

    ![Key Vault dane wyjściowe skryptu](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Tworzenie urządzenia usługi IoT Edge

Aby podłączyć urządzenie Azure IoT Edge do usługi IoT Hub, najpierw tworzymy tożsamość urządzenia w centrum. Przyjmujemy parametry połączenia z tożsamości urządzenia w chmurze i używają jej do konfigurowania środowiska uruchomieniowego na naszym urządzeniu IoT Edge. Po nawiązaniu połączenia z centrum przez skonfigurowane urządzenie można wdrażać moduły i wysyłać komunikaty. Można również zmienić konfigurację urządzenia fizycznego IoT Edge, zmieniając jego tożsamość urządzenia w usłudze IoT Hub.

W tym samouczku utworzymy nową tożsamość urządzenia przy użyciu Visual Studio Code. Te kroki można również wykonać przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure.

1. Na komputerze deweloperskim Otwórz Visual Studio Code.

2. Rozwiń ramkę **IoT Hub platformy Azure** w widoku Eksploratora Visual Studio Code.

3. Kliknij wielokropek i wybierz pozycję **utwórz IoT Edge urządzenie**.

4. Nadaj urządzeniu nazwę. Dla wygody użyjemy nazwy **aaTurbofanEdgeDevice** , aby posortować je na górze listy urządzeń.

5. Nowe urządzenie zostanie wyświetlone na liście urządzeń.

    ![Wyświetlanie nowych aaTurbofanEdgeDevice w Eksploratorze VS Code](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Wdróż maszynę wirtualną platformy Azure

Na potrzeby tego samouczka użyjemy [Azure IoT Edge na](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) obrazie Ubuntu z IoT Edge witryny Azure Marketplace. Azure IoT Edge w obrazie Ubuntu instaluje najnowsze środowisko uruchomieniowe Azure IoT Edge i jego zależności podczas uruchamiania. Wdrażamy maszynę wirtualną przy użyciu skryptu programu PowerShell, `Create-EdgeVM.ps1` szablonu Menedżer zasobów `IoTEdgeVMTemplate.json` i skryptu powłoki `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Włącz wdrażanie programistyczne

Aby użyć obrazu z portalu Marketplace we wdrożeniu inicjowanym przez skrypty, musimy włączyć wdrożenie programistyczne dla obrazu.

1. Zaloguj się w witrynie Azure Portal.

1. Wybierz pozycję **Wszystkie usługi**.

1. Na pasku wyszukiwania wprowadź i wybierz pozycję **Marketplace**.

1. Na pasku wyszukiwania portalu Marketplace wprowadź i wybierz **Azure IoT Edge w Ubuntu**.

1. Wybierz hiperlink **Rozpocznij** , aby wdrożyć program programowo.

1. Wybierz przycisk **Włącz** , a następnie **Zapisz**.

    ![Włącz wdrażanie programistyczne dla maszyny wirtualnej](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Zostanie wyświetlone powiadomienie o powodzeniu.

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Następnie uruchom skrypt, aby utworzyć maszynę wirtualną dla urządzenia IoT Edge.

1. Otwórz okno programu PowerShell i przejdź do katalogu **EdgeVM** .

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Uruchom skrypt, aby utworzyć maszynę wirtualną.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Po wyświetleniu monitu podaj wartości dla każdego parametru. W przypadku subskrypcji, grupy zasobów i lokalizacji zalecamy użycie takich samych, jak w przypadku wszystkich zasobów w tym samouczku.

    * **Identyfikator subskrypcji platformy Azure**: znaleziono w Azure Portal
    * **Nazwa grupy zasobów**: dopamiętanie nazwy do grupowania zasobów dla tego samouczka
    * **Lokalizacja**: Lokalizacja platformy Azure, w której zostanie utworzona maszyna wirtualna. Na przykład westus2 lub northeurope. Aby uzyskać więcej informacji, Zobacz wszystkie [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: nazwa konta administratora, którego będziesz używać do logowania się do maszyny wirtualnej
    * **AdminPassword**: hasło do ustawienia dla AdminUsername na maszynie wirtualnej

4. Aby skrypt mógł skonfigurować maszynę wirtualną, należy zalogować się do platformy Azure przy użyciu poświadczeń skojarzonych z subskrypcją platformy Azure, której używasz.

5. Skrypt potwierdza informacje dotyczące tworzenia maszyny wirtualnej. Wybierz pozycję **y** lub **Enter** , aby kontynuować.

6. Skrypt jest uruchamiany przez kilka minut, ponieważ wykonuje następujące czynności:

    * Utwórz grupę zasobów, jeśli jeszcze nie istnieje
    * Tworzenie maszyny wirtualnej
    * Dodaj wyjątki sieciowej grupy zabezpieczeń dla maszyny wirtualnej dla portów 22 (SSH), 5671 (AMQP), 5672 (AMPQ) i 443 (TLS)
    * Instalowanie [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli-apt)

7. Skrypt wyprowadza parametry połączenia SSH w celu nawiązania połączenia z maszyną wirtualną. Skopiuj parametry połączenia dla następnego kroku.

    ![Kopiuj parametry połączenia SSH dla maszyny wirtualnej](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Nawiązywanie połączenia z urządzeniem usługi IoT Edge

W następnych sekcjach opisano konfigurację utworzonej maszyny wirtualnej platformy Azure. Pierwszym krokiem jest nawiązanie połączenia z maszyną wirtualną.

1. Otwórz wiersz polecenia i wklej parametry połączenia SSH skopiowane z danych wyjściowych skryptu. Wprowadź własne informacje dotyczące nazwy użytkownika, sufiksu i regionu zgodnie z wartościami podanymi w skrypcie programu PowerShell w poprzedniej sekcji.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Po wyświetleniu monitu o potwierdzenie autentyczności hosta wpisz **Yes** i wybierz **Enter**.

3. Po wyświetleniu monitu podaj hasło.

4. Ubuntu wyświetla komunikat powitalny, a następnie powinien zostać wyświetlony monit `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Pobierz certyfikaty Key Vault

Wcześniej w tym artykule zostały przekazane certyfikaty do Key Vault, aby udostępnić je dla naszych IoT Edge urządzeń i urządzeń typu liść. Urządzenie liścia jest urządzeniem podrzędnym, które używa urządzenia IoT Edge jako bramy do komunikowania się z IoT Hub.

W dalszej części tego samouczka zajmiemy się urządzeniem typu liść. W tej sekcji Pobierz certyfikaty do urządzenia IoT Edge.

1. W sesji SSH na maszynie wirtualnej z systemem Linux Zaloguj się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

    ```azurecli
    az login
    ```

1. Zostanie wyświetlony monit o otwarcie przeglądarki w celu <https://microsoft.com/devicelogin> udostępnienia unikatowego kodu. Te kroki można wykonać na komputerze lokalnym. Zamknij okno przeglądarki po zakończeniu uwierzytelniania.

1. Po pomyślnym uwierzytelnieniu maszyna wirtualna z systemem Linux zaloguje się i wyświetli subskrypcje platformy Azure.

1. Ustaw subskrypcję platformy Azure, która ma być używana dla poleceń interfejsu wiersza polecenia platformy Azure.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Utwórz katalog na maszynie wirtualnej dla certyfikatów.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Pobierz certyfikaty przechowywane w magazynie kluczy: New-Edge-Device-Full-Chain. CERT. pem, New-Edge-Device. Key. pem i Azure-IoT-test-Only. root. ca. CERT. pem

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aktualizowanie konfiguracji urządzenia IoT Edge

Środowisko uruchomieniowe IoT Edge używa pliku, `/etc/iotedge/config.yaml` Aby zachować jego konfigurację. Musimy zaktualizować trzy informacje w tym pliku:

* **Parametry połączenia urządzenia**: parametry połączenia z tożsamości tego urządzenia w IoT Hub
* **Certyfikaty:** certyfikaty do użycia na potrzeby połączeń z urządzeniami podrzędnymi
* **Nazwa hosta:** w pełni kwalifikowana nazwa domeny (FQDN) urządzenia IoT Edge maszyny wirtualnej.

*Azure IoT Edge na* obrazie Ubuntu, który został użyty do utworzenia maszyny wirtualnej IoT Edge, zawiera skrypt powłoki, który aktualizuje plik config. YAML przy użyciu parametrów połączenia.

1. W Visual Studio Code kliknij prawym przyciskiem myszy urządzenie IoT Edge, a następnie wybierz polecenie **Kopiuj parametry połączenia urządzenia**.

    ![Kopiuj parametry połączenia z Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. W sesji SSH Uruchom polecenie, aby zaktualizować plik config. YAML za pomocą parametrów połączenia urządzenia.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Następnie będziemy aktualizować certyfikaty i nazwy hostów przez bezpośrednie edytowanie pliku config. YAML.

1. Otwórz plik config. YAML.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Zaktualizuj sekcję certyfikaty w pliku config. YAML, usuwając wiodącą `#` i ustawianą ścieżkę, tak aby plik wyglądał następująco:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Upewnij się, że w wierszu **Certyfikaty:** nie ma poprzedzającego odstępu, a każdy z zagnieżdżonych certyfikatów jest wcięty o dwie spacje.

    Kliknięcie prawym przyciskiem myszy w programie nano spowoduje wklejenie zawartości schowka do bieżącego położenia kursora. Aby zastąpić ciąg, użyj strzałek klawiatury, aby przejść do ciągu, który ma zostać zamieniony, usuń ciąg, a następnie kliknij prawym przyciskiem myszy, aby wkleić go do buforu.

3. W Azure Portal przejdź do maszyny wirtualnej. Skopiuj nazwę DNS (FQDN maszyny) z sekcji **Przegląd** .

4. Wklej nazwę FQDN do sekcji hostname pliku config. yml. Upewnij się, że nazwa zawiera tylko małe litery.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Zapisz i zamknij plik ( `Ctrl + X` , `Y` , `Enter` ).

6. Uruchom ponownie demona iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Sprawdź stan demona IoT Edge (po poleceniu wpisz ": q", aby wyjść).

    ```bash
    systemctl status iotedge
    ```

8. Jeśli widzisz błędy (kolor tekstu poprzedzony prefiksem " \[ Error \] ") w dziennikach demona przeglądu stanu pod kątem szczegółowych informacji o błędzie.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Ten samouczek jest częścią zestawu, w którym każdy artykuł kompiluje się w pracy wykonanej w poprzednich. Zaczekaj na oczyszczenie wszystkich zasobów do momentu zakończenia ostatniego samouczka.

## <a name="next-steps"></a>Następne kroki

Właśnie zakończono konfigurowanie maszyny wirtualnej platformy Azure jako nieprzezroczystej bramy Azure IoT Edge. Rozpoczęto od generowania certyfikatów testów przekazanych do Azure Key Vault. Następnie używamy skryptu i szablonu Menedżer zasobów, aby wdrożyć maszynę wirtualną za pomocą obrazu "Ubuntu Server 16,04 LTS Azure IoT Edge + środowisko uruchomieniowe" w portalu Azure Marketplace. Po nawiązaniu połączenia z maszyną wirtualną za pośrednictwem protokołu SSH zarejestrowano Cię na platformie Azure i pobrano certyfikaty z Key Vault. Wprowadziliśmy kilka aktualizacji konfiguracji środowiska uruchomieniowego IoT Edge, aktualizując plik config. YAML.

Przejdź do następnego artykułu, aby skompilować moduły IoT Edge.

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie niestandardowych modułów IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md)

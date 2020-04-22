---
title: 'Samouczek: Konfigurowanie urządzenia usługi IoT Edge — uczenie maszynowe w usłudze Azure IoT Edge'
description: W tym samouczku skonfigurujesz maszynę wirtualną platformy Azure z systemem Linux jako urządzenie usługi Azure IoT Edge, które działa jako przezroczysta brama.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 353ed321ce3b6161b28bf67d852a81f809880603
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733005"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Samouczek: Konfigurowanie urządzenia IoT Edge

> [!NOTE]
> Ten artykuł jest częścią serii samouczka na temat korzystania z usługi Azure Machine Learning w usłudze IoT Edge. Jeśli dotarłeś do tego artykułu bezpośrednio, zachęcamy do rozpoczęcia [pierwszego artykułu](tutorial-machine-learning-edge-01-intro.md) z serii, aby uzyskać najlepsze wyniki.

W tym artykule konfigurujemy maszynę wirtualną platformy Azure z systemem Linux jako urządzenie usługi IoT Edge, które działa jako przezroczysta brama. Przejrzysta konfiguracja bramy umożliwia urządzeniom łączenie się z usługą Azure IoT Hub za pośrednictwem bramy bez wiedzy o istnienie bramy. W tym samym czasie użytkownik interakcji z urządzeniami w usłudze Azure IoT Hub nie jest świadomy urządzenia bramy pośredniej. Ostatecznie dodamy analizy brzegowe do naszego systemu, dodając moduły usługi IoT Edge do przezroczystej bramy.

Kroki opisane w tym artykule są zazwyczaj wykonywane przez dewelopera chmury.

## <a name="create-certificates"></a>Tworzenie certyfikatów

Aby urządzenie działało jako brama, musi mieć możliwość bezpiecznego łączenia się z urządzeniami podrzędnymi. Usługa Azure IoT Edge umożliwia konfigurowanie bezpiecznych połączeń między urządzeniami za pomocą infrastruktury kluczy publicznych (PKI). W takim przypadku zezwalamy podrzędnemu urządzeniu IoT na łączenie się z urządzeniem usługi IoT Edge działającym jako przezroczysta brama. Aby zachować odpowiednie bezpieczeństwo, urządzenie podrzędne powinno potwierdzić tożsamość urządzenia Usługi IoT Edge. Aby uzyskać więcej informacji na temat sposobu używania certyfikatów przez urządzenia usługi IoT Edge, zobacz [Szczegóły użycia certyfikatu usługi Azure IoT Edge.](iot-edge-certs.md)

W tej sekcji tworzymy certyfikaty z podpisem własnym przy użyciu obrazu platformy Docker, który następnie tworzymy i uruchamiamy. Firma Docker zdecydowała się użyć obrazu platformy Docker, aby wykonać ten krok, ponieważ znacznie zmniejsza liczbę kroków potrzebnych do utworzenia certyfikatów na komputerze deweloperskim systemu Windows. Zobacz [Tworzenie certyfikatów demonstracyjnych w celu przetestowania funkcji urządzenia usługi IoT Edge,](how-to-create-test-certificates.md) aby dowiedzieć się, co zautomatyzowaliśmy za pomocą obrazu platformy Docker.

1. Zaloguj się do swojej maszyny Wirtualnej rozwoju.

2. Utwórz nowy folder ze `c:\edgeCertificates`ścieżką i nazwą .

3. Jeśli nie jest jeszcze uruchomiony, uruchom **program Docker dla systemu Windows** z menu Start systemu Windows.

4. Otwórz program Visual Studio Code.

5. Wybierz opcję**Folder otwierania** **pliku...** > i wybierz **pozycję C:\\\\źródło IoTEdgeAndMlSample\\CreateCertificates**.

6. W okienku Eksplorator kliknij prawym przyciskiem myszy plik **dockerfile** i wybierz polecenie **Kompilacja obrazu**.

7. W oknie dialogowym zaakceptuj domyślną wartość nazwy obrazu i tagu: **createcertificates: latest**.

    ![Tworzenie certyfikatów w programie Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. Poczekaj na zakończenie kompilacji.

    > [!NOTE]
    > Może zostać wyświetlone ostrzeżenie dotyczące braku klucza publicznego. Można bezpiecznie zignorować to ostrzeżenie. Podobnie zostanie wyświetlone ostrzeżenie dotyczące zabezpieczeń, które zaleca sprawdzanie/resetowanie uprawnień do obrazu, które można bezpiecznie zignorować dla tego obrazu.

9. W oknie terminala kodu programu Visual Studio uruchom kontener createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker wyświetli monit o dostęp do dysku **c:.\\ ** Wybierz **pozycję Udostępnij .**

11. Po wyświetleniu monitu podaj poświadczenia.

12. Po zakończeniu działania kontenera sprawdź, czy w **c:\\edgeCertificates**nie ma następujących plików:

    * c:\\edgeCerertificates\\\\certs azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCerertificates\\\\certs new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\\\certs new-edge-device.cert.pem
    * c:\\edgeCerertificates\\\\certs new-edge-device.cert.pfx
    * c:\\\\edgeCerefikuje\\prywatne nowe urządzenie edge.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Przekazywanie certyfikatów do usługi Azure Key Vault

Aby bezpiecznie przechowywać nasze certyfikaty i udostępnić je z wielu urządzeń, przekażemy certyfikaty do usługi Azure Key Vault. Jak widać z powyższej listy, mamy dwa typy plików certyfikatów: PFX i PEM. PfX będzie traktował jako certyfikaty Usługi Key Vault, które mają zostać przesłane do usługi Key Vault. Pliki PEM są zwykłym tekstem i będziemy traktować je jako tajemnice Magazynu kluczy. Będziemy używać magazynu kluczy skojarzonego z obszarem roboczym usługi Azure Machine Learning utworzonym przez uruchomienie [notesów platformy Azure.](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks)

1. Z [witryny Azure Portal](https://portal.azure.com)przejdź do obszaru roboczego usługi Azure Machine Learning.

2. Na stronie przeglądowej obszaru roboczego usługi Azure Machine Learning znajdź nazwę **magazynu kluczy**.

    ![Kopiowanie nazwy magazynu kluczy](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Na komputerze deweloperskim przekaż certyfikaty do usługi Key Vault. Zastąp ** \<subscriptionId\> ** i ** \<keyvaultname\> ** informacjami o zasobach.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Jeśli zostanie wyświetlony monit, zaloguj się na platformie Azure.

5. Skrypt będzie uruchamiany przez kilka minut z wyjściem, które wyświetla nowe wpisy magazynu kluczy.

    ![Dane wyjściowe skryptu magazynu kluczy](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Tworzenie urządzenia usługi IoT Edge

Aby połączyć urządzenie usługi Azure IoT Edge z centrum IoT hub, najpierw tworzymy tożsamość urządzenia w centrum. Bierzemy ciąg połączenia z tożsamości urządzenia w chmurze i używamy go do skonfigurowania środowiska wykonawczego na naszym urządzeniu usługi IoT Edge. Gdy skonfigurowane urządzenie połączy się z koncentratorem, jesteśmy w stanie wdrożyć moduły i wysyłać wiadomości. Możemy również zmienić konfigurację fizycznego urządzenia Usługi IoT Edge, zmieniając jego odpowiednią tożsamość urządzenia w centrum IoT hub.

W tym samouczku tworzymy nową tożsamość urządzenia przy użyciu kodu programu Visual Studio. Można również wykonać te kroki przy użyciu [witryny Azure portal](how-to-register-device.md#register-in-the-azure-portal)lub [interfejsu wiersza polecenia platformy Azure.](how-to-register-device.md#register-with-the-azure-cli)

1. Na komputerze deweloperskim otwórz program Visual Studio Code.

2. Rozwiń ramkę **Usługi Azure IoT Hub** z widoku eksploratora kodu programu Visual Studio.

3. Kliknij wielokropek i wybierz **pozycję Utwórz urządzenie brzegowe IoT**.

4. Nadaj urządzeniu nazwę. Dla wygody używamy nazwy **aaTurbofanEdgeDevice,** aby sortować na górze wymienionych urządzeń.

5. Nowe urządzenie pojawi się na liście urządzeń.

    ![Zobacz nowe aaTurbofanEdgeDevice w eksploratorze kodu VS](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Wdrażanie maszyny wirtualnej platformy Azure

Używamy [usługi Azure IoT Edge na obraz Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) z portalu Azure Marketplace, aby utworzyć nasze urządzenie usługi IoT Edge dla tego samouczka. Usługa Azure IoT Edge na obrazie Ubuntu instaluje najnowsze środowisko uruchomieniowe usługi Azure IoT Edge i jego zależności podczas uruchamiania. Wdrażamy maszynę wirtualną przy użyciu `Create-EdgeVM.ps1`skryptu programu PowerShell; szablon Menedżera zasobów; `IoTEdgeVMTemplate.json` i skrypt powłoki, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Włączanie wdrażania programowego

Aby użyć obrazu z portalu Marketplace we wdrożeniu skryptowym, musimy włączyć wdrożenie programowe dla obrazu.

1. Zaloguj się do Portalu Azure.

1. Wybierz pozycję **Wszystkie usługi**.

1. Na pasku wyszukiwania wprowadź i wybierz pozycję **Marketplace**.

1. Na pasku wyszukiwania Marketplace wprowadź i wybierz **pozycję Azure IoT Edge w Ubuntu**.

1. Wybierz **hiperłącze Wprowadzenie** do programowania wdrażania.

1. Wybierz przycisk **Włącz,** a następnie **zapisz**.

    ![Włączanie wdrażania programowego dla maszyny Wirtualnej](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Zostanie wyświetlone powiadomienie o sukcesie.

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Następnie uruchom skrypt, aby utworzyć maszynę wirtualną dla urządzenia usługi IoT Edge.

1. Otwórz okno programu PowerShell i przejdź do katalogu **EdgeVM.**

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Uruchom skrypt, aby utworzyć maszynę wirtualną.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Po wyświetleniu monitu podaj wartości dla każdego parametru. W przypadku subskrypcji, grupy zasobów i lokalizacji zaleca się używanie tego samego, co dla wszystkich zasobów w tym samouczku.

    * **Identyfikator subskrypcji platformy Azure:** znaleziony w witrynie Azure portal
    * **Nazwa grupy zasobów**: pamiętna nazwa grupowania zasobów dla tego samouczka
    * **Lokalizacja:** Lokalizacja platformy Azure, w której zostanie utworzona maszyna wirtualna. Na przykład westus2 lub północnaeurope. Aby uzyskać więcej informacji, zobacz wszystkie [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: nazwa konta administratora, którego użyjesz do zalogowania się na maszynie wirtualnej
    * **AdminPassword**: hasło do ustawienia adminUsername na maszynie wirtualnej

4. Aby skrypt mógł skonfigurować maszynę wirtualną, musisz zalogować się na platformie Azure przy użyciu poświadczeń skojarzonych z używanym subskrypcją platformy Azure.

5. Skrypt potwierdza informacje dotyczące tworzenia maszyny Wirtualnej. Wybierz **y** lub **Enter,** aby kontynuować.

6. Skrypt jest uruchamiany przez kilka minut, ponieważ wykonuje następujące kroki:

    * Utwórz grupę zasobów, jeśli jeszcze nie istnieje
    * Tworzenie maszyny wirtualnej
    * Dodaj wyjątki sieciowych sieciowych dla maszyny Wirtualnej dla portów 22 (SSH), 5671 (AMQP), 5672 (AMPQ) i 443 (TLS)
    * Instalowanie [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest))

7. Skrypt wyprowadza ciąg połączenia SSH do łączenia się z maszyną wirtualną. Skopiuj ciąg połączenia dla następnego kroku.

    ![Kopiowanie ciągu połączenia SSH dla maszyny Wirtualnej](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Nawiązywanie połączenia z urządzeniem usługi IoT Edge

W następnych kilku sekcjach skonfigurować maszynę wirtualną platformy Azure, którą utworzyliśmy. Pierwszym krokiem jest połączenie z maszyną wirtualną.

1. Otwórz wiersz polecenia i wklej parametry połączenia SSH skopiowane z danych wyjściowych skryptu. Wprowadź własne informacje dotyczące nazwy użytkownika, sufiksu i regionu zgodnie z wartościami podanymi do skryptu programu PowerShell w poprzedniej sekcji.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Po wyświetleniu monitu o sprawdzenie autentyczności hosta wpisz **tak** i wybierz pozycję **Enter**.

3. Po wyświetleniu monitu podaj hasło.

4. Ubuntu wyświetla wiadomość powitalną, a następnie `<username>@<machinename>:~$`powinieneś zobaczyć monit jak .

## <a name="download-key-vault-certificates"></a>Pobieranie certyfikatów usługi Key Vault

Wcześniej w tym artykule przekazaliśmy certyfikaty do usługi Key Vault, aby udostępnić je dla naszego urządzenia Usługi IoT Edge i naszego urządzenia typu leaf. Urządzenie typu liść jest urządzeniem podrzędnym, które używa urządzenia usługi IoT Edge jako bramy do komunikowania się z centrum IoT Hub.

Zajmiemy się urządzeniem liściowym w dalszej części samouczka. W tej sekcji pobierz certyfikaty na urządzenie IoT Edge.

1. Z sesji SSH na maszynie wirtualnej systemu Linux zaloguj się do platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.

    ```azurecli
    az login
    ```

1. Zostaniesz poproszony o otwarcie przeglądarki <https://microsoft.com/devicelogin> i podanie unikatowego kodu. Te kroki można wykonać na komputerze lokalnym. Zamknij okno przeglądarki po zakończeniu uwierzytelniania.

1. Po pomyślnym uwierzytelnieniu maszyna wirtualna z systemem Linux zaloguje się i wyświetli listę subskrypcji platformy Azure.

1. Ustaw subskrypcję platformy Azure, której chcesz użyć dla poleceń interfejsu wiersza polecenia platformy Azure.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Utwórz katalog na maszynie wirtualnej dla certyfikatów.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Pobierz certyfikaty przechowywane w magazynie kluczy: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem i azure-iot-test-only.root.ca.cert.pem

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aktualizowanie konfiguracji urządzenia usługi IoT Edge

Środowisko wykonawcze usługi IoT `/etc/iotedge/config.yaml` Edge używa pliku do utrwalenia jego konfiguracji. Musimy zaktualizować trzy informacje w tym pliku:

* **Parametry połączenia urządzenia:** parametry połączenia z tożsamości tego urządzenia w Centrum IoT
* **Certyfikaty:** certyfikaty używane dla połączeń wykonanych z urządzeniami podrzędnymi
* **Nazwa hosta:** w pełni kwalifikowana nazwa domeny (FQDN) urządzenia VM IoT Edge.

*Usługa Azure IoT Edge na obrazie Ubuntu,* który użyliśmy do utworzenia maszyny Wirtualnej usługi IoT Edge, jest dostarczany ze skryptem powłoki, który aktualizuje plik config.yaml za pomocą ciągu połączenia.

1. W programie Visual Studio Code kliknij prawym przyciskiem myszy urządzenie Usługi IoT Edge, a następnie wybierz polecenie **Kopiuj ciąg połączenia urządzenia**.

    ![Kopiowanie ciągu połączenia z programu Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. W sesji SSH uruchom polecenie aktualizacji pliku config.yaml za pomocą ciągu połączenia urządzenia.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Następnie zaktualizujemy certyfikaty i nazwa hosta, edytując bezpośrednio plik config.yaml.

1. Otwórz plik config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Zaktualizuj sekcję certyfikatów pliku config.yaml, usuwając interlinia `#` i ustawiając ścieżkę, aby plik wyglądał następująco:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Upewnij się, że **certyfikaty:** wiersz nie ma poprzedniego odstępu i że każdy z zagnieżdżonych certyfikatów jest wcięci przez dwie spacje.

    Kliknięcie prawym przyciskiem myszy w nano spowoduje wklejenie zawartości schowka do bieżącej pozycji kursora. Aby zastąpić ciąg, użyj strzałek klawiatury, aby przejść do ciągu, który chcesz zastąpić, usuń ciąg, a następnie kliknij prawym przyciskiem myszy, aby wkleić z bufora.

3. W witrynie Azure portal przejdź do maszyny wirtualnej. Skopiuj nazwę DNS (FQDN urządzenia) z sekcji **Przegląd.**

4. Wklej FQDN do sekcji nazwa hosta config.yml. Upewnij się, że nazwa jest mała litera.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Zapisz i zamknij`Ctrl + X`plik `Y` `Enter`( , , ).

6. Uruchom ponownie demona iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Sprawdź stan demona IoT Edge (po poleceniu wpisz ":q", aby zakończyć).

    ```bash
    systemctl status iotedge
    ```

8. Jeśli w stanie Sprawdź dzienniki demonów,\[\]aby uzyskać szczegółowe informacje o błędzie, zostaną wyświetlone błędy (kolorowy tekst z napisem " ERROR ").

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Następne kroki

Właśnie zakończyliśmy konfigurowanie maszyny Wirtualnej platformy Azure jako przezroczystej bramy usługi Azure IoT Edge. Rozpoczęliśmy od wygenerowania certyfikatów testowych przekazanych do usługi Azure Key Vault. Następnie użyliśmy skryptu i szablonu Menedżera zasobów do wdrożenia maszyny Wirtualnej z obrazem "Ubuntu Server 16.04 LTS + Azure IoT Edge runtime" z portalu Azure Marketplace. Po uruchomieniu maszyny Wirtualnej, która została połączona za pośrednictwem protokołu SSH, zalogowaliśmy się na platformę Azure i pobraliśmy certyfikaty z usługi Key Vault. Dokonaliśmy kilku aktualizacji konfiguracji środowiska wykonawczego usługi IoT Edge, aktualizując plik config.yaml.

Aby uzyskać więcej informacji, zobacz [Jak urządzenie usługi IoT Edge może być używane jako brama](iot-edge-as-gateway.md) i [konfigurowanie urządzenia usługi IoT Edge do działania jako przezroczysta brama](how-to-create-transparent-gateway.md).

Przejdź do następnego artykułu do tworzenia modułów IoT Edge.

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie niestandardowych modułów usługi IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md)

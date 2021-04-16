---
title: 'Samouczek: konfigurowanie urządzenia Azure IoT Edge — uczenie maszynowe na IoT Edge'
description: W tym samouczku skonfigurujesz maszynę wirtualną platformy Azure z systemem Linux jako urządzenie Azure IoT Edge, które działa jako przezroczysta brama.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 65fd6e5b4d494f8e8486d72079b9fa97a175894b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376889"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Samouczek: konfigurowanie Azure IoT Edge urządzenia

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

W tym artykule skonfigurujemy maszynę wirtualną platformy Azure z systemem Linux jako urządzenie Azure IoT Edge, które działa jako przezroczysta brama. Przezroczysta konfiguracja bramy umożliwia urządzeniu łączenie się z Azure IoT Hub za pośrednictwem bramy bez znajomości, że brama istnieje. W tym samym czasie użytkownik, który wchodzi w interakcję z urządzeniami w IoT Hub nie wie o pośredniczącym urządzeniu bramy. Ostatecznie dodamy do naszego systemu analizę brzegową, dodając IoT Edge do przezroczystej bramy.

>[!NOTE]
>Pojęcia w tym samouczku dotyczą wszystkich wersji IoT Edge, ale przykładowe urządzenie, które utworzysz w celu wypróbowania scenariusza, działa IoT Edge wersji 1.1.

Kroki opisane w tym artykule są zwykle wykonywane przez dewelopera chmury.

Z tej sekcji samouczka dowiesz się, jak:

> [!div class="checklist"]
>
> * Utwórz certyfikaty, aby umożliwić urządzeniu bramy bezpieczne łączenie się z urządzeniami nadrzędnymi.
> * Utwórz IoT Edge urządzenia.
> * Utwórz maszynę wirtualną platformy Azure w celu symulowania IoT Edge wirtualnego.

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł jest częścią serii samouczków dotyczących używania Azure Machine Learning na IoT Edge. Każdy artykuł z tej serii opiera się na pracy z poprzednim artykułem. Jeśli bezpośrednio dotarliśmy do tego artykułu, zobacz [pierwszy artykuł](tutorial-machine-learning-edge-01-intro.md) z tej serii.

## <a name="create-certificates"></a>Tworzenie certyfikatów

Aby urządzenie działało jako brama, musi bezpiecznie łączyć się z urządzeniami nadrzędnymi. Dzięki IoT Edge można użyć infrastruktury kluczy publicznych (PKI) do skonfigurowania bezpiecznych połączeń między urządzeniami. W tym przypadku zezwalamy urządzeniu podrzędnego IoT na łączenie się z urządzeniem IoT Edge działającym jako przezroczysta brama. Aby zachować odpowiednie zabezpieczenia, urządzenie podrzędne powinno potwierdzić tożsamość urządzenia IoT Edge urządzenia. Aby uzyskać więcej informacji o tym, IoT Edge używają certyfikatów, zobacz [Azure IoT Edge szczegóły użycia certyfikatu](iot-edge-certs.md).

W tej sekcji utworzymy certyfikaty z podpisem własnym przy użyciu obrazu platformy Docker, który następnie utworzymy i uruchomemy. Do wykonania tego kroku wybrano obraz platformy Docker, ponieważ zmniejsza to liczbę kroków wymaganych do utworzenia certyfikatów na komputerze dewelopera z systemem Windows. Aby zrozumieć, co zautomatyzowaliśmy za pomocą obrazu platformy Docker, zobacz [Create demo certificates to test IoT Edge device features](how-to-create-test-certificates.md)(Tworzenie certyfikatów demonstracyjnych w celu IoT Edge funkcji urządzenia).

1. Zaloguj się do maszyny wirtualnej dewelopera.
1. Utwórz nowy folder o ścieżce i nazwie **c:\edgeCertificates.**

1. Jeśli nie jest jeszcze uruchomiona, uruchom **Docker for Windows** z witryny Windows menu Start.

1. Otwórz program Visual Studio Code.

1. Wybierz **pozycję Plik** Otwórz  >  **folder,** a następnie wybierz pozycję **C: źródło \\ \\ IoTEdgeAndMlSample \\ CreateCertificates.**

1. W **okienku Eksplorator** kliknij prawym przyciskiem myszy plik **dockerfile** i wybierz polecenie **Build Image (Skompilowanie obrazu).**

1. W oknie dialogowym zaakceptuj wartość domyślną dla nazwy obrazu i tagu: **createcertificates: latest**.

    ![Zrzut ekranu przedstawiający tworzenie certyfikatów w Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Poczekaj na zakończenie kompilacji.

    > [!NOTE]
    > Może zostać wyświetlony komunikat ostrzegawczy o braku klucza publicznego. To ostrzeżenie można bezpiecznie zignorować. Podobnie zobaczysz ostrzeżenie o zabezpieczeniach z rekomendacją sprawdzenia lub zresetowania uprawnień do obrazu, które można bezpiecznie zignorować dla tego obrazu.

1. W Visual Studio Code terminalu uruchom kontener createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Na platformie Docker zostanie wyświetlony monit o dostęp do **dysku c:. \\** Wybierz **pozycję Udostępnij.**

1. Po wyświetleniu monitu podaj swoje poświadczenia.

1. Po zakończeniu działania kontenera sprawdź następujące pliki w języku **c: \\ edgeCertificates**:

    * c: \\ edgeCertificates \\ certs \\ azure-iot-test-only.root.ca.cert.pem
    * c: \\ edgeCertificates \\ certs \\ new-edge-device-full-chain.cert.pem
    * c: \\ edgeCertificates \\ certs \\ new-edge-device.cert.pem
    * c: \\ edgeCertificates \\ certs \\ new-edge-device.cert.pfx
    * c: \\ edgeCertificates \\ private \\ new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Przekazywanie certyfikatów do Azure Key Vault

Aby bezpiecznie przechowywać nasze certyfikaty i udostępnić je z wielu urządzeń, przekażemy je do usługi Azure Key Vault. Jak widać na powyższej liście, mamy dwa typy plików certyfikatów: PFX i PEM. Traktujemy plik PFX jako Key Vault do przesłania do Key Vault. Pliki PEM są zwykłym tekstem i będziemy traktować je jako Key Vault tajne. Użyjemy wystąpienia usługi Key Vault skojarzonego z utworzonym Azure Machine Learning roboczym, uruchamiając [notesy Jupyter](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks).

1. Z [Azure Portal](https://portal.azure.com)przejdź do swojego Azure Machine Learning roboczego.

1. Na stronie przeglądu obszaru roboczego Machine Learning znajdź nazwę Key Vault **.**

    ![Zrzut ekranu przedstawiający kopiowanie nazwy magazynu kluczy.](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. Na komputerze dewelopera przekaż certyfikaty do Key Vault. Zastąp **\<subscriptionId\>** i **\<keyvaultname\>** informacjami o zasobach.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. Jeśli zostanie wyświetlony monit, zaloguj się do platformy Azure.

1. Skrypt będzie uruchamiany przez kilka minut z danych wyjściowych, które będą zawierały listę nowych Key Vault wpisów.

    ![Zrzut ekranu przedstawiający Key Vault danych wyjściowych skryptu.](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>Tworzenie urządzenia usługi IoT Edge

Aby połączyć urządzenie Azure IoT Edge z centrum IoT Hub, najpierw utworzymy tożsamość urządzenia w centrum. W chmurze używamy parametrów połączenia z tożsamości urządzenia i używamy ich do skonfigurowania środowiska uruchomieniowego na IoT Edge urządzenia. Gdy skonfigurowane urządzenie połączy się z centrum, możemy wdrożyć moduły i wysyłać komunikaty. Możemy również zmienić konfigurację urządzenia fizycznego IoT Edge, zmieniając jego odpowiednią tożsamość urządzenia w IoT Hub.

W tym samouczku utworzymy nową tożsamość urządzenia przy użyciu Visual Studio Code. Te kroki można również wykonać przy użyciu interfejsu Azure Portal wiersza polecenia platformy Azure.

1. Na komputerze dewelopera otwórz Visual Studio Code.

1. Rozwiń **Azure IoT Hub** ramkę z widoku Visual Studio Code **Eksploratora.**

1. Wybierz wielokropek, a następnie wybierz **pozycję Utwórz IoT Edge urządzenie.**

1. Nadaj urządzeniu nazwę. Dla wygody używamy nazwy **aaTurbofanEdgeDevice,** aby posortować ją do góry wymienionych urządzeń.

1. Nowe urządzenie zostanie wyświetlone na liście urządzeń.

    ![Zrzut ekranu przedstawiający widok urządzenia w eksploratorze Visual Studio Code Explorer.](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Wdrażanie maszyny wirtualnej platformy Azure

W tym [samouczku Azure IoT Edge obraz](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) systemu Ubuntu z Azure Marketplace do utworzenia IoT Edge urządzenia. Obraz Azure IoT Edge systemie Ubuntu instaluje najnowsze środowisko uruchomieniowe IoT Edge i jego zależności od uruchamiania. Wdrażamy maszynę wirtualną przy użyciu:

- Skrypt programu PowerShell, `Create-EdgeVM.ps1` .
- Szablon Azure Resource Manager, `IoTEdgeVMTemplate.json` .
- Skrypt powłoki, `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Włączanie wdrażania programowego

Aby użyć obrazu z Azure Marketplace we wdrożeniu skryptowym, należy włączyć wdrożenie programowe dla obrazu.

1. Zaloguj się w witrynie Azure Portal.

1. Wybierz pozycję **Wszystkie usługi**.

1. Na pasku wyszukiwania wprowadź i wybierz pozycję **Marketplace.**

1. Na pasku wyszukiwania witryny Marketplace wprowadź i wybierz pozycję **Azure IoT Edge w systemie Ubuntu**.

1. Wybierz **hiperlink Wprowadzenie,** aby wdrożyć programowo.

1. Wybierz przycisk **Włącz,** a następnie wybierz pozycję **Zapisz.**

    ![Zrzut ekranu przedstawiający włączanie wdrażania programowego dla maszyny wirtualnej.](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Zostanie wyświetlony komunikat o sukcesie.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Następnie uruchom skrypt, aby utworzyć maszynę wirtualną dla IoT Edge wirtualnej.

1. Otwórz okno programu PowerShell i przejdź do katalogu **EdgeVM.**

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. Uruchom skrypt, aby utworzyć maszynę wirtualną.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. Po wyświetleniu monitu podaj wartości dla każdego parametru. W przypadku subskrypcji, grupy zasobów i lokalizacji zalecamy używanie tych samych wartości, które są dostępne dla wszystkich zasobów w tym samouczku.

    * **Identyfikator subskrypcji platformy Azure:** znajduje się w Azure Portal.
    * **Nazwa grupy zasobów:** pamiętna nazwa do grupowania zasobów w tym samouczku.
    * **Lokalizacja:** lokalizacja platformy Azure, w której zostanie utworzona maszyna wirtualna. Na przykład westus2 lub northeurope. Aby uzyskać więcej informacji, zobacz wszystkie [lokalizacje platformy Azure.](https://azure.microsoft.com/global-infrastructure/locations/)
    * **AdminUsername:** nazwa konta administratora, które będzie służące do logowania się do maszyny wirtualnej.
    * **AdminPassword:** hasło do ustawienia dla nazwy użytkownika administratora na maszynie wirtualnej.

1. Aby uzyskać skrypt do skonfigurowania maszyny wirtualnej, zaloguj się do platformy Azure przy użyciu poświadczeń skojarzonych z używaną subskrypcją platformy Azure.

1. Skrypt potwierdza informacje dotyczące tworzenia maszyny wirtualnej. Wybierz **pozycję y lub** **Wprowadź,** aby kontynuować.

1. Skrypt jest uruchamiany przez kilka minut, ponieważ wykonuje następujące kroki:

    * Tworzy grupę zasobów, jeśli jeszcze nie istnieje
    * Tworzy maszynę wirtualną
    * Dodano wyjątki sieciowej organizacji sieciowej dla maszyny wirtualnej dla portów 22 (SSH), 5671 (AMQP), 5672 (AMPQ) i 443 (TLS)
    * Instaluje interfejs wiersza [polecenia platformy Azure](/cli/azure/install-azure-cli-apt)

1. Skrypt tworzy dane wyjściowe parametrów połączenia SSH w celu nawiązaniu połączenia z maszyną wirtualną. Skopiuj wartości parametrów połączenia do następnego kroku.

    ![Zrzut ekranu przedstawiający kopiowanie parametrów połączenia SSH dla maszyny wirtualnej.](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Nawiązywanie połączenia z urządzeniem usługi IoT Edge

W kilku następnych sekcjach skonfigurowano utworzoną przez nas maszynę wirtualną platformy Azure. Pierwszym krokiem jest połączenie się z maszyną wirtualną.

1. Otwórz wiersz polecenia i wklej ciąg połączenia SSH skopiowany z danych wyjściowych skryptu. Wprowadź własne informacje dotyczące nazwy użytkownika, sufiksu i regionu zgodnie z wartościami dostarczonymi do skryptu programu PowerShell w poprzedniej sekcji.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. Po wyświetleniu monitu o zweryfikowanie autentyczności hosta wprowadź tak i **wybierz** klawisz **Enter.**

1. Po wyświetleniu monitu podaj hasło.

1. W systemie Ubuntu zostanie wyświetlony komunikat powitalny, a następnie powinien zostać wyświetlony monit, taki jak `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Pobieranie Key Vault certyfikatów

Wcześniej w tym artykule przesłaliśmy certyfikaty do Key Vault, aby udostępnić je dla naszego IoT Edge i naszego liścia. Urządzenie liścia jest urządzeniem nadrzędnym, które używa IoT Edge jako bramy do komunikacji z IoT Hub.

Urządzenie liścia zajmiemy się w dalszej części tego samouczka. W tej sekcji pobierz certyfikaty na urządzenie IoT Edge aplikację.

1. Z sesji SSH na maszynie wirtualnej z systemem Linux zaloguj się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

    ```azurecli
    az login
    ```

1. Zostanie wyświetlony monit o otwarcie przeglądarki ze stroną logowania urządzenia firmy [Microsoft](https://microsoft.com/devicelogin) i podanie unikatowego kodu. Te kroki można wykonać na maszynie lokalnej. Po uwierzytelniania zamknij okno przeglądarki.

1. Po pomyślnym uwierzytelnieniu maszyna wirtualna z systemem Linux zaloguje się i będzie wyświetlić listę subskrypcji platformy Azure.

1. Ustaw subskrypcję platformy Azure, której chcesz użyć dla poleceń interfejsu wiersza polecenia platformy Azure.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Utwórz katalog certyfikatów na maszynie wirtualnej.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Pobierz certyfikaty przechowywane w magazynie kluczy: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem i azure-iot-test-only.root.ca.cert.pem.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aktualizowanie konfiguracji IoT Edge urządzenia

Środowisko IoT Edge używa pliku /etc/iotedge/config.yaml, aby utrwalić swoją konfigurację. Musimy zaktualizować trzy informacje w tym pliku:

* **Ciąg połączenia urządzenia:** ciąg połączenia z tożsamości tego urządzenia w IoT Hub
* **Certyfikaty:** certyfikaty do użycia dla połączeń z urządzeniami nadrzędnymi
* **Nazwa hosta:** w pełni kwalifikowana nazwa domeny (FQDN) maszyny wirtualnej IoT Edge wirtualnej

Plik Azure IoT Edge w systemie Ubuntu użyty do utworzenia maszyny wirtualnej IoT Edge jest dostarczany ze skryptem powłoki, który aktualizuje plik config.yaml przy użyciu parametrów połączenia.

1. W Visual Studio Code kliknij prawym przyciskiem myszy ikonę IoT Edge, a następnie wybierz **pozycję Kopiuj ciąg połączenia urządzenia.**

    ![Zrzut ekranu przedstawiający kopiowanie parametrów połączenia z Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. W sesji SSH uruchom polecenie , aby zaktualizować plik config.yaml przy użyciu parametrów połączenia urządzenia.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Następnie zaktualizujemy certyfikaty i nazwę hosta, bezpośrednio edytując plik config.yaml.

1. Otwórz plik config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Zaktualizuj sekcję certificates pliku config.yaml, usuwając wiodące i ustawiając ścieżkę tak, aby plik **#** wyglądał jak w poniższym przykładzie:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Upewnij **się, że** wiersz certyfikaty: nie ma poprzedzającego odstępu i że każdy z zagnieżdżonych certyfikatów jest wcięty przez dwie spacje.

    Kliknięcie prawym przyciskiem myszy w pliku nano spowoduje wklejenie zawartości schowka do bieżącej pozycji kursora. Aby zastąpić ciąg, użyj strzałek klawiatury, aby przejść do ciągu, który chcesz zamienić, usunąć ciąg, a następnie kliknij prawym przyciskiem myszy, aby wkleić z buforu.

1. W Azure Portal przejdź do maszyny wirtualnej. Skopiuj nazwę DNS (nazwę FQDN maszyny) z **sekcji** Przegląd.

1. Wklej nazwę FQDN w sekcji hostname pliku config.yml. Upewnij się, że nazwa ma tylko małe litery.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. Zapisz i zamknij plik, naciskając klawisze **Ctrl+X,** **Y** i **Enter**.

1. Uruchom ponownie IoT Edge demona.

    ```bash
    sudo systemctl restart iotedge
    ```

1. Sprawdź stan demona IoT Edge. Po poleceniu wprowadź **:q,** aby zakończyć.

    ```bash
    systemctl status iotedge
    ```

1. Jeśli w stanie widzisz błędy (kolorowy tekst poprzedzony prefiksem " ERROR ") , sprawdź dzienniki \[ \] demona, aby uzyskać szczegółowe informacje o błędzie.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Ten samouczek jest częścią zestawu, w którym każdy artykuł opiera się na pracy wykonanej w poprzednich. Poczekaj na oczyszczenie wszystkich zasobów, aż ukończysz ostatni samouczek.

## <a name="next-steps"></a>Następne kroki

Właśnie zakończyliśmy konfigurowanie maszyny wirtualnej platformy Azure jako IoT Edge przezroczystej bramy. Rozpoczęliśmy od wygenerowania certyfikatów testowych, które zostały przekazane do Key Vault. Następnie umyliśmy skrypt i szablon Resource Manager do wdrożenia maszyny wirtualnej z obrazem środowiska uruchomieniowego Ubuntu Server 16.04 LTS + Azure IoT Edge z Azure Marketplace. Po uruchomieniu maszyny wirtualnej nałączono połączenie za pośrednictwem połączenia SSH. Następnie zalogowaliśmy się do platformy Azure i pobraliśmy certyfikaty z Key Vault. Zaktualizowaliśmy kilka aktualizacji konfiguracji środowiska uruchomieniowego IoT Edge, aktualizując plik config.yaml.

Przejdź do następnego artykułu, aby skompilować IoT Edge modułów.

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie niestandardowych IoT Edge modułów](tutorial-machine-learning-edge-06-custom-modules.md)

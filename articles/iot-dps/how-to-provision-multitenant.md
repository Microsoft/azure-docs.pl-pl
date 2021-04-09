---
title: Jak udostępnić urządzenia dla wielodostępności na platformie Azure IoT Hub Device Provisioning Service
description: Jak udostępnić urządzenia do wielodostępności przy użyciu wystąpienia usługi Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: eebcfb98d4e155ca965b9e4c68a862afa08a46ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727434"
---
# <a name="how-to-provision-for-multitenancy"></a>Jak aprowizować pod kątem wielu dzierżaw 

W tym artykule pokazano, jak bezpiecznie zainicjować obsługę wielu urządzeń z kluczami symetrycznymi w grupie centrów IoT przy użyciu [zasad alokacji](concepts-service.md#allocation-policy). Zasady alokacji zdefiniowane przez usługę aprowizacji obsługują różne scenariusze alokacji. Dwa typowe scenariusze:

* **Geolokalizacja/geoopóźnienie**: w miarę przemieszczania się urządzenia między lokalizacjami opóźnienie sieci jest zwiększane, ponieważ urządzenie jest obsługiwane do centrum IoT najbliżej każdej lokalizacji. W tym scenariuszu grupa centrów IoT, obejmująca wiele regionów, jest wybierana do rejestracji. Dla tych rejestracji są wybrane zasady alokacji **najniższych opóźnień** . Te zasady powodują, że usługa Device Provisioning może oszacować opóźnienie urządzenia i ustalić Centrum IoT z grupy centrów IoT. 

* **Wielodostępność**: urządzenia używane w ramach rozwiązania IoT mogą wymagać przypisania do określonego Centrum IoT Hub lub grupy centrów IoT. Rozwiązanie może wymagać, aby wszystkie urządzenia dla określonej dzierżawy mogły komunikować się z określoną grupą centrów IoT. W niektórych przypadkach Dzierżawca może mieć własne centra IoT i wymagać przypisywania urządzeń do centrów IoT.

Często należy połączyć te dwa scenariusze. Na przykład wielodostępne rozwiązanie IoT często przypisze urządzenia dzierżawcy za pomocą grupy centrów IoT, które są rozmieszczone w różnych regionach. Te urządzenia dzierżawców można przypisać do centrum IoT w tej grupie, które ma najniższe opóźnienie na podstawie lokalizacji geograficznej.

W tym artykule przedstawiono przykład symulowanego urządzenia z [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) w celu zademonstrowania sposobu udostępniania urządzeń w scenariuszu wielodostępnym w różnych regionach. W tym artykule zostaną wykonane następujące kroki:

* Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć dwa regionalne centra IoT (**zachodnie stany USA** i **Wschodnie stany USA**)
* Tworzenie rejestracji wielodostępnej
* Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć dwie regionalne maszyny wirtualne z systemem Linux do działania jako urządzenia w tych samych regionach (**zachodnie stany USA** i **Wschodnie stany USA**)
* Skonfiguruj środowisko programistyczne dla zestawu Azure IoT C SDK na obu maszynach wirtualnych z systemem Linux
* Symuluj urządzenia, aby sprawdzić, czy są one obsługiwane dla tej samej dzierżawy w najbliższym regionie.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

- Zakończenie [konfigurowania IoT Hub Device Provisioning Service przy użyciu Azure Portal](./quick-setup-auto-provision.md) przewodnika Szybki Start.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Tworzenie dwóch regionalnych centrów IoT

W tej sekcji użyjesz Azure Cloud Shell, aby utworzyć dwa nowe regionalne centra IoT w regionach **zachodnie stany USA** i **Wschodnie stany USA** dla dzierżawy.


1. Użyj Azure Cloud Shell, aby utworzyć grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group#az-group-create) . Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

    Poniższy przykład tworzy grupę zasobów o nazwie *contoso-US-Resource-Group* w regionie *wschodnim* . Zaleca się użycie tej grupy dla wszystkich zasobów utworzonych w tym artykule. Ułatwi to oczyszczenie po zakończeniu.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Użyj Azure Cloud Shell, aby utworzyć Centrum IoT Hub w regionie **wschodnim** za pomocą polecenia [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) . Centrum IoT zostanie dodane do *grupy contoso-US-Resource-Group*.

    W poniższym przykładzie utworzono Centrum IoT o nazwie *contoso-Wschodnie-Hub* w lokalizacji *Wschodnie* . Musisz użyć własnej unikatowej nazwy centrum zamiast **contoso-Wschodnie-Hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Wykonanie tego polecenia może potrwać kilka minut.

3. Użyj Azure Cloud Shell, aby utworzyć Centrum IoT Hub w regionie **zachodnim** za pomocą polecenia [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) . To centrum IoT zostanie również dodane do *grupy contoso-US-Resource-Group*.

    W poniższym przykładzie utworzono Centrum IoT o nazwie *contoso-zachodni-Hub* w lokalizacji *zachodniej* . Musisz użyć własnej unikatowej nazwy centrum zamiast **centrum contoso-zachodni-Hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Wykonanie tego polecenia może potrwać kilka minut.



## <a name="create-the-multitenant-enrollment"></a>Tworzenie rejestracji wielodostępnej

W tej sekcji utworzysz nową grupę rejestracji dla urządzeń dzierżawców.  

Dla uproszczenia w tym artykule jest stosowane [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md) z rejestracją. Aby lepiej zabezpieczyć rozwiązanie, należy rozważyć użycie [zaświadczania certyfikatu X. 509](concepts-x509-attestation.md) z łańcuchem zaufania.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz wystąpienie usługi Device Provisioning Service.

2. Wybierz kartę **Zarządzanie rejestracjami** , a następnie kliknij przycisk **Dodaj grupę rejestracji** w górnej części strony. 

3. W obszarze **Dodaj grupę rejestracji** Wprowadź poniższe informacje, a następnie kliknij przycisk **Zapisz** .

    **Nazwa grupy**: wprowadź **contoso-US-Devices**.

    **Typ zaświadczania**: Wybierz **klucz symetryczny**.

    **Automatycznie Generuj klucze**: to pole wyboru powinno być już zaznaczone.

    **Wybierz sposób przypisywania urządzeń do centrów**: Wybierz **najniższy czas opóźnienia**.

    ![Dodaj wielodostępną grupę rejestracji na potrzeby zaświadczania klucza symetrycznego](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. W obszarze **Dodaj grupę rejestracji** kliknij pozycję **Połącz nowe centrum IoT Hub** , aby połączyć oba centra regionalne.

    **Subskrypcja**: Jeśli masz wiele subskrypcji, wybierz subskrypcję, w której utworzono regionalne centra IoT.

    **Centrum IoT**: wybierz jedno z utworzonych centrów regionalnych.

    **Zasady dostępu**: Wybierz **iothubowner**.

    ![Łączenie regionalnych centrów IoT z usługą aprowizacji](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Po połączeniu regionalnych centrów IoT należy wybrać je dla grupy rejestracji i kliknąć przycisk **Zapisz** , aby utworzyć regionalną grupę usługi IoT Hub na potrzeby rejestracji.

    ![Tworzenie grupy centrów regionalnych na potrzeby rejestracji](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Po zapisaniu rejestracji Otwórz ją ponownie i zanotuj **klucz podstawowy**. Musisz najpierw zapisać rejestrację, aby generować klucze. Ten klucz zostanie użyty do wygenerowania unikatowych kluczy urządzeń dla jednocześnie symulowanych urządzeń.


## <a name="create-regional-linux-vms"></a>Tworzenie regionalnych maszyn wirtualnych z systemem Linux

W tej sekcji utworzysz dwie regionalne maszyny wirtualne z systemem Linux. Na tych maszynach wirtualnych zostanie uruchomiony przykład symulacji urządzenia z każdego regionu, aby zademonstrować obsługę urządzenia dla urządzeń dzierżawców z obu regionów.

Aby ułatwić czyszczenie, te maszyny wirtualne zostaną dodane do tej samej grupy zasobów, która zawiera utworzone centra IoT, *contoso-US-Resource-Group*. Maszyny wirtualne będą jednak działać w oddzielnych regionach (**zachodnie stany USA** i **Wschodnie stany USA**).

1. W Azure Cloud Shell wykonaj następujące polecenie, aby utworzyć region **Wschodnie stany USA** po wprowadzeniu następujących zmian parametrów w poleceniu:

    **--name**: Wprowadź unikatową nazwę maszyny wirtualnej dla regionu **Wschodnie stany USA** . 

    **--admin-username**: Użyj własnej nazwy użytkownika administratora.

    **--Admin-Password**: Użyj własnego hasła administratora.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Wykonanie tego polecenia potrwa kilka minut. Po zakończeniu działania polecenia Zanotuj wartość **publicIpAddress** dla maszyny wirtualnej regionu Wschodnie stany USA.

1. W Azure Cloud Shell wykonaj polecenie, aby utworzyć maszynę wirtualną regionu **zachodnie stany USA** po wprowadzeniu następujących zmian parametrów w poleceniu:

    **--name**: Wprowadź unikatową nazwę maszyny wirtualnej z regionu w regionie **zachodnie stany USA** . 

    **--admin-username**: Użyj własnej nazwy użytkownika administratora.

    **--Admin-Password**: Użyj własnego hasła administratora.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Wykonanie tego polecenia potrwa kilka minut. Po zakończeniu działania polecenia Zanotuj wartość **publicIpAddress** dla maszyny wirtualnej regionu zachodnie stany USA.

1. Otwórz dwie powłoki wiersza polecenia. Połącz się z jedną z regionalnych maszyn wirtualnych w każdej powłoce przy użyciu protokołu SSH. 

    Przekaż nazwę użytkownika administratora i publiczny adres IP zanotowany dla maszyny wirtualnej jako parametry protokołu SSH. Po wyświetleniu monitu wprowadź hasło administratora.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Przygotuj środowisko deweloperskie zestawu SDK usługi Azure IoT C

W tej sekcji utworzysz zestaw SDK usługi Azure IoT C na każdej maszynie wirtualnej. Zestaw SDK zawiera przykład, który symuluje Inicjowanie obsługi administracyjnej urządzenia z każdego regionu.

1. Dla każdej maszyny wirtualnej zainstaluj **CMAKE**, **g + +**, w **zatoce** i [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) przy użyciu następujących poleceń:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Znajdź nazwę tagu dla [najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) zestawu SDK.

1. Sklonuj [zestaw SDK usługi Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) na obu maszynach wirtualnych.  Użyj znacznika znalezionego w poprzednim kroku jako wartości `-b` parametru:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

1. W przypadku obu maszyn wirtualnych Utwórz nowy folder **CMAKE** w repozytorium i przejdź do tego folderu.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. W przypadku obu maszyn wirtualnych Uruchom następujące polecenie, które kompiluje wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Gdy kompilacja zakończy się powodzeniem, kilka ostatnich wierszy danych wyjściowych będzie wyglądać podobnie do następujących danych wyjściowych:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Utwórz unikatowe klucze urządzeń

W przypadku korzystania z zaświadczania klucza symetrycznego z rejestracją grup nie można bezpośrednio używać kluczy grupy rejestracji. Zamiast tego należy utworzyć unikatowy klucz pochodny dla każdego urządzenia i wymieniony w temacie [rejestracje grup przy użyciu kluczy symetrycznych](concepts-symmetric-key-attestation.md#group-enrollments).

Aby wygenerować klucz urządzenia, użyj klucza głównego grupy do obliczenia [algorytmu HMAC-SHA256](https://wikipedia.org/wiki/HMAC) unikatowego identyfikatora rejestracji dla urządzenia i Przekształć wynik w formacie base64.

Nie dodawaj klucza głównego grupy do kodu urządzenia.

Użyj przykładu bash Shell, aby utworzyć pochodny klucz urządzenia dla każdego urządzenia przy użyciu **OpenSSL**.

- Zastąp wartość **klucza kluczem** **podstawowym** zanotowanym wcześniej dla rejestracji.

- Zastąp wartość **REG_ID** własnym UNIKATOWYm identyfikatorem rejestracji dla każdego urządzenia. Użyj znaków alfanumerycznych ("-"), aby zdefiniować obydwa identyfikatory.

Przykładowe generowanie klucza urządzenia dla *contoso-simdevice-wschód*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Przykładowe generowanie klucza urządzenia dla *contoso-simdevice-zachodni*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Każdy z tych urządzeń dzierżawców będzie używać pochodnego klucza urządzenia i unikatowego identyfikatora rejestracji do przeprowadzenia zaświadczania klucza symetrycznego z grupą rejestracji podczas aprowizacji do centrów IoT dzierżawców.




## <a name="simulate-the-devices-from-each-region"></a>Symulowanie urządzeń z każdego regionu


W tej sekcji zostanie zaktualizowany przykład aprowizacji w zestawie SDK usługi Azure IoT C dla obu regionalnych maszyn wirtualnych. 

Przykładowy kod symuluje sekwencję rozruchu urządzenia, która wysyła żądanie aprowizacji do wystąpienia usługi Device Provisioning. Sekwencja rozruchu spowoduje, że urządzenie zostanie rozpoznane i przypisane do centrum IoT Hub, które jest najbliższe na podstawie opóźnienia.

1. W witrynie Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning Service, a następnie zapisz wartość **_Zakres identyfikatorów_**.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Otwórz **~/Azure-IoT-SDK-c/Provisioning \_ Client/Samples/Prov dev Client Sample \_ \_ \_ /Prov dla \_ klienta deweloperskiego \_ \_ Sample. c** do edycji na obu maszynach wirtualnych.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Znajdź stałą `id_scope` i zastąp jej wartość wcześniej skopiowaną wartością **Zakres identyfikatorów**. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Znajdź definicję funkcji `main()` w tym samym pliku. Upewnij się, że `hsm_type` zmienna jest ustawiona na `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` tak, jak pokazano poniżej, aby dopasować metodę zaświadczania grupy rejestracji. 

    Zapisz zmiany w plikach na obu maszynach wirtualnych.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Na obu maszynach wirtualnych Znajdź wywołanie `prov_dev_set_symmetric_key_info()` w **Prov \_ dev \_ Client \_ Sample. c** , które jest oznaczone jako komentarz.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Usuń komentarz wywołań funkcji i Zastąp wartości symboli zastępczych (łącznie z nawiasami ostrymi) unikatowymi identyfikatorami rejestracji i pochodnymi kluczami urządzeń dla każdego urządzenia. Poniższe klucze są przeznaczone tylko do celów. Użyj wygenerowanych wcześniej kluczy.

    Wschodnie stany USA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    Zachodnie stany USA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Zapisz pliki.

1. Na obu maszynach wirtualnych przejdź do przykładowego folderu pokazanego poniżej i skompiluj przykład.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Po pomyślnym zakończeniu kompilacji Uruchom **Prov \_ dev \_ Client \_sample.exe** na obu maszynach wirtualnych w celu symulowania urządzenia dzierżawy z każdego regionu. Należy zauważyć, że każde urządzenie jest przydzielono do centrum IoT dzierżawców najbliżej regionów symulowanego urządzenia.

    Uruchom symulację:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Przykładowe dane wyjściowe z maszyny wirtualnej Wschodnie stany USA:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Przykładowe dane wyjściowe z maszyny wirtualnej zachodniej USA:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z zasobami utworzonymi w tym artykule, możesz je pozostawić. Jeśli nie planujesz kontynuowania korzystania z zasobu, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w tym artykule, aby uniknąć niepotrzebnych opłat.

W tym artykule przyjęto założenie, że utworzono wszystkie zasoby z tego artykułu zgodnie z instrukcjami w tej samej grupie zasobów o nazwie **contoso-US-Resource-Group**.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli usługa IoT Hub została utworzona wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób usługi IoT Hub.
>

Aby usunąć grupę zasobów według nazwy:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

2. W polu tekstowym **Filtruj według nazwy..** . Wpisz nazwę grupy zasobów zawierającej Twoje zasoby, **contoso-US-Resource-Group**. 

3. Z prawej strony grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

4. Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Ponownie wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> Aby dowiedzieć się więcej o zainicjowaniu obsługi administracyjnej, zobacz temat [IoT Hub ponowne Inicjowanie obsługi administracyjnej urządzeń](concepts-device-reprovision.md)

> [!div class="nextstepaction"]
> Aby dowiedzieć się więcej o anulowaniu aprowizacji, zobacz jak cofać obsługę administracyjną [urządzeń, które wcześniej były obsługiwane](how-to-unprovision-devices.md) .
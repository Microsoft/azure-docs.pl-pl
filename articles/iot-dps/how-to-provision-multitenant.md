---
title: Jak aprowizować urządzenia na urządzenia z wieloma Azure IoT Hub Device Provisioning Service
description: Jak aprowizować urządzenia na wiele sieci za pomocą wystąpienia usługi Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0b88923ff6447785a4ef5a7c80e1ff44d1a2b9cb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777387"
---
# <a name="how-to-provision-for-multitenancy"></a>Jak aprowizować pod kątem wielu dzierżaw 

W tym artykule pokazano, jak bezpiecznie aprowizować wiele urządzeń z kluczami symetrycznymi dla grupy centrów IoT Hub przy użyciu [zasad alokacji.](concepts-service.md#allocation-policy) Zasady alokacji zdefiniowane przez usługę aprowizowania obsługują różne scenariusze alokacji. Dwa typowe scenariusze to:

* **Geolokalizacja/GeoLatency:** gdy urządzenie przemieszcza się między lokalizacjami, opóźnienie sieci jest większe dzięki aprowizacji urządzenia w centrum IoT najbliżej każdej lokalizacji. W tym scenariuszu do rejestracji wybierana jest grupa centrów IoT, które obejmują wiele regionów. Dla **tych rejestracji wybrano** zasady alokacji najmniejszych opóźnień. Te zasady powodują, że usługa Device Provisioning Service ocenia opóźnienie urządzenia i określa zamknięcie centrum IoT Poza grupą centrów IoT. 

* **Wiele usług:** urządzenia używane w ramach rozwiązania IoT mogą wymagać przypisania do określonego centrum IoT lub grupy centrów IoT. Rozwiązanie może wymagać, aby wszystkie urządzenia dla określonej dzierżawy komunikują się z określoną grupą centrów IoT. W niektórych przypadkach dzierżawca może być właścicielem centrów IoT i wymagać przypisania urządzeń do swoich centrów IoT.

Często łączy się te dwa scenariusze. Na przykład wielodostępne rozwiązanie IoT zwykle przypisuje urządzenia dzierżawców przy użyciu grupy centrów IoT, które są rozproszone w różnych regionach. Te urządzenia dzierżawy można przypisać do centrum IoT w tej grupie, które ma najmniejsze opóźnienie w zależności od lokalizacji geograficznej.

W tym artykule użyto przykładowego urządzenia symulowanego z zestawu SDK języka [C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) w celu zademonstrowania sposobu aprowizowania urządzeń w scenariuszu wielotenerowym w różnych regionach. W tym artykule wykonasz następujące kroki:

> [!div class="checklist"]
> * Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć dwa regionalne centra IoT Hub **(Zachodnie stany USA** i **Wschodnie stany USA)**
> * Tworzenie rejestracji wielotenerowej
> * Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć dwie regionalne maszyny wirtualne z systemem Linux, które będą działać jako urządzenia w tych samych regionach **(Zachodnie stany USA** **i Wschodnie stany USA)**
> * Konfigurowanie środowiska projektowego dla zestawu SDK języka C usługi Azure IoT na obu komputerach wirtualnych z systemem Linux
> * Zasymuluj urządzenia, aby zobaczyć, że są aprowowane dla tej samej dzierżawy w najbliższym regionie.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

- Ukończenie przewodnika [Szybki start IoT Hub Device Provisioning Service konfigurowanie](./quick-setup-auto-provision.md) Azure Portal.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Tworzenie dwóch regionalnych centrów IoT

W tej sekcji użyjemy usługi Azure Cloud Shell dwóch nowych regionalnych centrów IoT w regionach Zachodnie stany **USA** i **Wschodnie** stany USA dla dzierżawy.


1. Użyj polecenia Azure Cloud Shell, aby utworzyć grupę zasobów za pomocą [polecenia az group create.](/cli/azure/group#az_group_create) Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

    Poniższy przykład tworzy grupę zasobów o *nazwie contoso-us-resource-group* w *regionie eastus.* Zaleca się używanie tej grupy dla wszystkich zasobów utworzonych w tym artykule. Ułatwi to czyszczenie po zakończeniu.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Użyj polecenia Azure Cloud Shell, aby utworzyć centrum IoT w regionie **eastus** za pomocą [polecenia az iot hub create.](/cli/azure/iot/hub#az_iot_hub_create) Centrum IoT zostanie dodane do *grupy zasobów contoso-us-resource..*

    Poniższy przykład tworzy centrum IoT o nazwie *contoso-east-hub* w *lokalizacji eastus.* Zamiast nazwy **contoso-east-hub** należy użyć własnej unikatowej nazwy centrum.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Ukończenie tego polecenia może potrwać kilka minut.

3. Użyj polecenia Azure Cloud Shell, aby utworzyć centrum IoT w regionie **westus** za pomocą [polecenia az iot hub create.](/cli/azure/iot/hub#az_iot_hub_create) To centrum IoT zostanie również dodane do *grupy zasobów contoso-us-resource-group*.

    Poniższy przykład tworzy centrum IoT o nazwie *contoso-west-hub* w *lokalizacji westus.* Należy użyć własnej unikatowej nazwy centrum zamiast **contoso-west-hub.**

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Ukończenie tego polecenia może potrwać kilka minut.



## <a name="create-the-multitenant-enrollment"></a>Tworzenie rejestracji wielotenerowej

W tej sekcji utworzysz nową grupę rejestracji dla urządzeń dzierżawców.  

Dla uproszczenia w tym artykule użyto [zaświadczenia klucza symetrycznego](concepts-symmetric-key-attestation.md) podczas rejestracji. Aby uzyskać bezpieczniejsze rozwiązanie, rozważ użycie zaświadczenia certyfikatu [X.509](concepts-x509-attestation.md) z łańcuchem zaufania.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i otwórz wystąpienie usługi Device Provisioning Service.

2. Wybierz **kartę Zarządzanie rejestracjami,** a następnie kliknij przycisk Dodaj **grupę** rejestracji w górnej części strony. 

3. Na **stronie Dodawanie grupy rejestracji** wprowadź następujące informacje, a następnie kliknij **przycisk** Zapisz.

    **Nazwa grupy:** wprowadź **nazwę contoso-us-devices.**

    **Typ zaświadczenia:** wybierz pozycję **Klucz symetryczny.**

    **Automatycznie generuj** klucze: to pole wyboru powinno być już zaznaczone.

    **Wybierz sposób przypisywania urządzeń do centrów:** wybierz pozycję **Najmniejsze opóźnienie.**

    ![Dodawanie wielotenerowej grupy rejestracji na stronie zaświadczenia klucza symetrycznego](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. Na **stronie Dodawanie grupy rejestracji** kliknij pozycję Połącz nowe centrum **IoT,** aby połączyć oba centra regionalne.

    **Subskrypcja:** Jeśli masz wiele subskrypcji, wybierz subskrypcję, w której utworzono regionalne centra IoT Hub.

    **Centrum IoT:** wybierz jedno z utworzonych centrów regionalnych.

    **Zasady dostępu:** wybierz **pozycję iothubowner.**

    ![Łączenie regionalnych centrów IoT Hub z usługą aprowizowania](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Po nawiązać obydwoma regionalnymi centrami IoT Hub  należy wybrać je dla grupy rejestracji i kliknąć przycisk Zapisz, aby utworzyć regionalną grupę centrów IoT dla rejestracji.

    ![Tworzenie regionalnej grupy centrów dla rejestracji](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Po zapisaniu rejestracji otwórz ją ponownie i zanotuj **klucz podstawowy**. Najpierw należy zapisać rejestrację, aby wygenerować klucze. Ten klucz będzie później używany do generowania unikatowych kluczy urządzeń dla obu symulowanych urządzeń.


## <a name="create-regional-linux-vms"></a>Tworzenie regionalnych maszyn wirtualnych z systemem Linux

W tej sekcji utworzysz dwie regionalne maszyny wirtualne z systemem Linux. Te maszyny wirtualne uruchamiają przykład symulacji urządzeń z każdego regionu, aby zademonstrować aprowizowanie urządzeń dla urządzeń dzierżawców z obu regionów.

Aby ułatwić czyszczenie, te maszyny wirtualne zostaną dodane do tej samej grupy zasobów zawierającej utworzone centra IoT Hubs *contoso-us-resource-group.* Jednak maszyny wirtualne będą działać w oddzielnych regionach **(Zachodnie stany USA** i **Wschodnie stany USA).**

1. W Azure Cloud Shell wykonaj następujące polecenie, aby utworzyć maszynę wirtualną w regionie **Wschodnie** usa po wymusciu następujących zmian parametrów w poleceniu:

    **--name:** wprowadź unikatową nazwę maszyny wirtualnej urządzenia regionalnego **Wschodnie** usa. 

    **--admin-username:** użyj własnej nazwy użytkownika administratora.

    **--admin-password:** użyj własnego hasła administratora.

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

    Wykonanie tego polecenia potrwa kilka minut. Po zakończeniu polecenia zanotuj wartość **publicIpAddress** maszyny wirtualnej w regionie Wschodnie usa.

1. W Azure Cloud Shell wykonaj polecenie , aby utworzyć maszynę wirtualną w regionie Zachodnie stany **USA** po wymusciu następujących zmian parametrów w poleceniu:

    **--name:** wprowadź unikatową nazwę maszyny wirtualnej urządzenia regionalnego **w** regionach Zachodnie stany USA. 

    **--admin-username:** użyj własnej nazwy użytkownika administratora.

    **--admin-password:** użyj własnego hasła administratora.

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

    Wykonanie tego polecenia potrwa kilka minut. Po zakończeniu polecenia zanotuj wartość **publicIpAddress** maszyny wirtualnej w regionie Zachodnie stany USA.

1. Otwórz dwie powłoki wiersza polecenia. Połącz się z jedną z regionalnych maszyn wirtualnych w każdej powłoki przy użyciu połączenia SSH. 

    Przekaż nazwę użytkownika administratora i publiczny adres IP zanotowyny dla maszyny wirtualnej jako parametry do protokołu SSH. Po wyświetleniu monitu wprowadź hasło administratora.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Przygotowywanie środowiska projektowego zestawu SDK języka C usługi Azure IoT

W tej sekcji sklonowany zostanie zestaw SDK języka C usługi Azure IoT na każdej maszynie wirtualnej. Zestaw SDK zawiera przykład, który symuluje aprowizowanie urządzeń dzierżawy z każdego regionu.

1. Dla każdej maszyny wirtualnej zainstaluj **narzędzia CMake,** **g++**, **gcc** i [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) przy użyciu następujących poleceń:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Znajdź nazwę tagu [najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) zestawu SDK.

1. Sklonuj [zestaw SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) na obu tych maszyn wirtualnych.  Użyj tagu znalezionego w poprzednim kroku jako wartości `-b` parametru:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

1. Dla obu maszyn wirtualnych utwórz nowy folder **cmake** w repozytorium i zmień go na ten folder.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. W przypadku obu maszyn wirtualnych uruchom następujące polecenie, aby utworzyć wersję zestawu SDK specyficzną dla platformy klienta dewelopera. 

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


## <a name="derive-unique-device-keys"></a>Wyprowadzaj unikatowe klucze urządzeń

W przypadku używania zaświadczenia klucza symetrycznego z rejestracjami grup nie należy bezpośrednio używać kluczy grupy rejestracji. Zamiast tego należy utworzyć unikatowy klucz pochodny dla każdego urządzenia i wymieniony w rejestracji [grupowych z kluczami symetrycznymi.](concepts-symmetric-key-attestation.md#group-enrollments)

Aby wygenerować klucz urządzenia, użyj klucza głównego grupy, aby obliczyć [klucz HMAC-SHA256](https://wikipedia.org/wiki/HMAC) unikatowego identyfikatora rejestracji dla urządzenia i przekonwertować wynik na format Base64.

Nie dołączaj klucza głównego grupy do kodu urządzenia.

Użyj przykładowej powłoki Bash, aby utworzyć pochodny klucz urządzenia dla każdego urządzenia przy użyciu **narzędzia openssl**.

- Zastąp wartość KLUCZA **kluczem** **podstawowym** zanotym wcześniej dla rejestracji.

- Zastąp wartość dla **REG_ID** własnym unikatowym identyfikatorem rejestracji dla każdego urządzenia. Użyj małych liter alfanumerycznych i znaków kreski ("-"), aby zdefiniować oba identyfikatory.

Przykład generowania klucza urządzenia *dla regionu contoso-simdevice-east:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Przykład generowania klucza urządzenia *dla contoso-simdevice-west:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Urządzenia dzierżawy będą używać swojego pochodnego klucza urządzenia i unikatowego identyfikatora rejestracji do przeprowadzania zaświadczenia klucza symetrycznego z grupą rejestracji podczas aprowizowania w centrach IoT dzierżawy.




## <a name="simulate-the-devices-from-each-region"></a>Symulowanie urządzeń z każdego regionu


W tej sekcji zaktualizujemy przykład aprowizowania w zestawie SDK języka C usługi Azure IoT dla obu regionalnych maszyn wirtualnych. 

Przykładowy kod symuluje sekwencję rozruchu urządzenia, która wysyła żądanie aprowizowania do wystąpienia usługi Device Provisioning Service. Sekwencja rozruchu spowoduje, że urządzenie zostanie rozpoznane i przypisane do centrum IoT, które znajduje się najbliżej, na podstawie opóźnienia.

1. W witrynie Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning Service, a następnie zapisz wartość **_Zakres identyfikatorów_**.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Otwórz **pakiet ~/azure-iot-sdk-c/provisioning \_ client/samples/prov \_ dev client \_ \_ sample/prov \_ dev client \_ \_ sample.c** do edycji na obu komputerach wirtualnych.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Znajdź stałą `id_scope` i zastąp jej wartość wcześniej skopiowaną wartością **Zakres identyfikatorów**. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Znajdź definicję funkcji `main()` w tym samym pliku. Upewnij się, `hsm_type` że zmienna jest ustawiona na wartość , jak pokazano poniżej, aby dopasować metodę zaświadczenia `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` grupy rejestracji. 

    Zapisz zmiany w plikach na obu komputerach wirtualnych.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Na obu komputerach wirtualnych znajdź wywołanie metody w przykładzie `prov_dev_set_symmetric_key_info()` **prov \_ dev client \_ \_ sample.c,** które jest oznaczane jako komentarz.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Odkomentuj wywołania funkcji i zastąp wartości symboli zastępczych (w tym nawiasy kątowe) unikatowymi identyfikatorami rejestracji i pochodnymi kluczami urządzeń dla każdego urządzenia. Klucze przedstawione poniżej są tylko w celach przykładowych. Użyj kluczy wygenerowanych wcześniej.

    Wschodnie usa:
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

1. Na obu maszynach wirtualnych przejdź do folderu przykładowego pokazanego poniżej i skompilowanie przykładu.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Po zakończeniu kompilacji uruchom **klienta deweloperskiego prov \_ \_ \_sample.exe** obu maszyn wirtualnych, aby zasymulować urządzenie dzierżawy z każdego regionu. Zwróć uwagę, że każde urządzenie jest przydzielone do centrum IoT dzierżawy znajdującego się najbliżej regionów urządzenia symulowanego.

    Uruchom symulację:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Przykładowe dane wyjściowe z maszyny wirtualnej Wschodnie usa:

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

    Przykładowe dane wyjściowe z maszyny wirtualnej w zachodnich usa:
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

Jeśli planujesz kontynuować pracę z zasobami utworzonymi w tym artykule, możesz je pozostawić. Jeśli nie planujesz dalszego używania zasobu, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w tym artykule, aby uniknąć niepotrzebnych opłat.

W krokach w tym miejscu założono, że utworzono wszystkie zasoby w tym artykule zgodnie z instrukcjami w tej samej grupie zasobów o nazwie **contoso-us-resource-group.**

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli usługa IoT Hub została utworzona wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób usługi IoT Hub.
>

Aby usunąć grupę zasobów według nazwy:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

2. W polu **tekstowym Filtruj** według nazwy... wpisz nazwę grupy zasobów zawierającej zasoby **contoso-us-resource-group**. 

3. Z prawej strony grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

4. Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Ponownie wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat ponownego aprowizowania, zobacz

> [!div class="nextstepaction"]
> [IoT Hub dotyczące ponownego aprowizowania urządzeń](concepts-device-reprovision.md)

* Aby dowiedzieć się więcej na temat coprowizowania, zobacz
> [!div class="nextstepaction"]
> [Jak coprowizować urządzenia, które były wcześniej automatycznie aprowowane](how-to-unprovision-devices.md)
---
title: Urządzenie symulowane wdrożenia IoT — platforma Azure | Microsoft Docs
description: W tym przewodniku opisano sposób wdrażania niestandardowych urządzeń symulowanych w akceleratorze rozwiązań do monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "66427571"
---
# <a name="deploy-a-new-simulated-device"></a>Wdrażanie nowego symulowanego urządzenia

Akceleratory rozwiązań zdalnego monitorowania i symulacji urządzeń pozwalają definiować własne symulowane urządzenia. W tym artykule opisano sposób wdrażania niestandardowego typu urządzenia chłodzenia i nowego typu urządzenia żarówki do akceleratora rozwiązania do monitorowania zdalnego.

W krokach przedstawionych w tym artykule przyjęto założenie, że została ukończona procedura [tworzenia i testowania nowego symulowanego urządzenia](iot-accelerators-remote-monitoring-create-simulated-device.md) .

Kroki opisane w tym przewodniku przedstawiają, jak:

1. Użyj protokołu SSH, aby uzyskać dostęp do systemu plików maszyny wirtualnej, która obsługuje Akcelerator rozwiązania do monitorowania zdalnego.

1. Skonfiguruj platformę Docker, aby załadować modele urządzeń z lokalizacji poza kontenerem platformy Docker.

1. Uruchom Akcelerator rozwiązania do monitorowania zdalnego przy użyciu niestandardowych plików modeli urządzeń.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby wykonać kroki opisane w tym przewodniku, musisz mieć aktywną subskrypcję platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać czynności opisane w tym przewodniku:

- Wdrożone wystąpienie [akceleratora rozwiązania do monitorowania zdalnego](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Lokalna powłoka **bash** do uruchamiania `ssh` poleceń i `scp` . W systemie Windows prostym sposobem instalacji usługi **bash** jest zainstalowanie usługi [git](https://git-scm.com/download/win).
- Niestandardowe pliki modelu urządzenia, takie jak opisane w temacie [Tworzenie i testowanie nowego symulowanego urządzenia](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Skonfiguruj platformę Docker

W tej sekcji skonfigurujesz platformę Docker do ładowania plików modeli urządzeń z folderu **/tmp/DeviceModels** na maszynie wirtualnej, a nie z wewnątrz kontenera Docker. Uruchom polecenia w tej sekcji w **bash** Shell na komputerze lokalnym:

W tej sekcji skonfigurujesz platformę Docker do ładowania plików modeli urządzeń z folderu **/tmp/DeviceModels** na maszynie wirtualnej, a nie z wewnątrz kontenera Docker. Uruchom polecenia w tej sekcji w **bash** Shell na komputerze lokalnym:

1. Użyj protokołu SSH, aby nawiązać połączenie z maszyną wirtualną na platformie Azure z komputera lokalnego. W poniższym poleceniu przyjęto założenie, że publiczny adres IP maszyny wirtualnej **VM-vikxv** jest **104.41.128.108** --Zamień tę wartość na publiczny adres IP maszyny wirtualnej z poprzedniej sekcji:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Postępuj zgodnie z monitami, aby zalogować się do maszyny wirtualnej przy użyciu hasła ustawionego w poprzedniej sekcji.

1. Skonfiguruj usługę symulacji urządzenia, aby załadować modele urządzeń spoza kontenera. Najpierw Otwórz plik konfiguracji platformy Docker:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Znajdź ustawienia dla kontenera **devicesimulation** i Edytuj ustawienie **woluminów** , jak pokazano w poniższym fragmencie kodu:

    ```yml
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_KEYVAULT_NAME
        - PCS_AAD_APPID
        - PCS_AAD_APPSECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    Zapisz zmiany.

1. Skopiuj istniejące pliki modelu urządzenia z kontenera do nowej lokalizacji. Najpierw Znajdź identyfikator kontenera dla kontenera symulacji urządzenia:

    ```sh
    sudo docker ps
    ```

    Następnie skopiuj pliki modelu urządzenia do folderu **tmp** na maszynie wirtualnej. W poniższym poleceniu przyjęto założenie, że identyfikator kontenera to c378d6878407 — Zamień tę wartość na identyfikator kontenera symulacji urządzenia:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Pozostaw okno **bash** z otwartą sesją SSH.

1. Skopiuj niestandardowe pliki modelu urządzenia do maszyny wirtualnej. Uruchom to polecenie w innej **bash** Shell na komputerze, na którym zostały utworzone niestandardowe modele urządzeń. Najpierw przejdź do folderu lokalnego, który zawiera pliki JSON modelu urządzenia. W poniższych poleceniach przyjęto założenie, że publiczny adres IP maszyny wirtualnej to **104.41.128.108** — Zastąp tę wartość publicznym adresem IP maszyny wirtualnej. Wprowadź hasło do maszyny wirtualnej po wyświetleniu monitu:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Uruchom ponownie kontener Docker symulacji urządzenia, aby użyć nowych modeli urządzeń. Uruchom następujące polecenia w powłoce **bash** z otwartą sesją SSH na maszynie wirtualnej:

    ```sh
    sudo /app/start.sh
    ```

    Jeśli chcesz zobaczyć stan uruchomionych kontenerów platformy Docker i ich identyfikatorów kontenerów, użyj następującego polecenia:

    ```sh
    sudo docker ps
    ```

    Jeśli chcesz zobaczyć dziennik z kontenera symulacji urządzenia, uruchom następujące polecenie. Zastąp identyfikator kontenera identyfikatorem kontenera symulacji urządzenia:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Symulacja uruchamiania

Teraz możesz używać niestandardowych modeli urządzeń w rozwiązaniu do zdalnego monitorowania:

1. Uruchom pulpit nawigacyjny monitorowania zdalnego z [Microsoft Azure akceleratorów rozwiązań IoT](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Na stronie **urządzenia** można dodać symulowane urządzenia. Po dodaniu nowego symulowanego urządzenia nowe modele urządzeń są dostępne do wyboru.

1. Możesz użyć pulpitu nawigacyjnego, aby wyświetlić dane telemetryczne urządzenia i wywoływać metody urządzenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz dowiedzieć się więcej, pozostaw wdrożony Akcelerator rozwiązania do monitorowania zdalnego.

Jeśli akcelerator rozwiązania nie jest już potrzebny, usuń go ze strony [wstępnie zainicjowanych rozwiązań](https://www.azureiotsolutions.com/Accelerators#dashboard) , wybierając ją, a następnie klikając pozycję **Usuń rozwiązanie**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku przedstawiono sposób wdrażania niestandardowych modeli urządzeń w akceleratorze rozwiązań do monitorowania zdalnego. Sugerowany następny krok to Dowiedz się, jak [połączyć rzeczywiste urządzenie z rozwiązaniem do zdalnego monitorowania](iot-accelerators-connecting-devices-node.md).

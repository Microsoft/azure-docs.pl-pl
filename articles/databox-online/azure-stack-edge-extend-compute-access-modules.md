---
title: Zarządzanie siecią obliczeniową na Azure Stack EDGE Pro w celu uzyskania dostępu do modułów | Microsoft Docs
description: Zawiera opis sposobu rozszerzenia sieci obliczeniowej na Azure Stack brzegowej Pro w celu uzyskania dostępu do modułów za pośrednictwem zewnętrznego adresu IP.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 19c92deb58ac51aa882e7123b9a90aa3eae627d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90894125"
---
# <a name="enable-compute-network-on-your-azure-stack-edge-pro"></a>Włączanie sieci obliczeniowej na Azure Stack Edge

W tym artykule opisano, w jaki sposób moduły działające w ramach programu Azure Stack EDGE Pro mogą uzyskać dostęp do sieci obliczeniowej włączonej na urządzeniu.

Aby skonfigurować sieć, wykonaj następujące czynności:

- Włącz interfejs sieciowy na urządzeniu z systemem Azure Stack Edge w wersji Pro na potrzeby obliczeń
- Dodawanie modułu w celu uzyskania dostępu do sieci obliczeniowej na Azure Stack Edge
- Sprawdź, czy moduł ma dostęp do włączonego interfejsu sieciowego

W tym samouczku użyjesz modułu aplikacji sieci WebServer, aby przedstawić ten scenariusz.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

- Ukończono Azure Stack urządzenia brzegowego Pro z konfiguracją urządzenia.
- Ukończono Konfigurowanie kroków **obliczeniowych** zgodnie z [samouczkiem: Przekształć dane przy użyciu Azure Stack EDGE Pro](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute) na urządzeniu. Urządzenie powinno mieć skojarzony zasób IoT Hub, urządzenie IoT i urządzenie IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Włącz interfejs sieciowy dla obliczeń

Aby uzyskać dostęp do modułów uruchomionych na urządzeniu za pośrednictwem sieci zewnętrznej, należy przypisać adres IP do interfejsu sieciowego na urządzeniu. Tymi ustawieniami obliczeniowymi można zarządzać z poziomu lokalnego interfejsu użytkownika sieci Web.

Aby skonfigurować ustawienia obliczeń, wykonaj następujące kroki w lokalnym interfejsie użytkownika sieci Web.

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konfiguracja > ustawienia obliczeń**.  

2. **Włącz** interfejs sieciowy, który ma być używany do nawiązywania połączenia z modułem obliczeniowym, który zostanie uruchomiony na urządzeniu.

    - W przypadku używania statycznych adresów IP wprowadź adres IP dla interfejsu sieciowego.
    - W przypadku korzystania z protokołu DHCP adresy IP są przypisywane automatycznie. Ten przykład używa protokołu DHCP.

    ![Włącz ustawienia obliczeń 1](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Wybierz pozycję **Zastosuj** , aby zastosować ustawienia. Zanotuj adres IP przypisany do interfejsu sieciowego, jeśli używany jest protokół DHCP.

    ![Włącz ustawienia obliczeń](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Dodaj moduł aplikacji WebServer

Wykonaj następujące kroki, aby dodać moduł aplikacji sieci WebServer na urządzeniu Azure Stack EDGE Pro.

1. Przejdź do zasobu IoT Hub skojarzonego z urządzeniem Azure Stack brzeg Pro, a następnie wybierz pozycję **IoT Edge urządzenie**.
2. Wybierz urządzenie IoT Edge skojarzone z urządzeniem Azure Stack brzeg Pro. Na stronie **szczegóły urządzenia**wybierz pozycję **Ustaw moduły**. W obszarze **Dodaj moduły**wybierz pozycję **+ Dodaj** , a następnie wybierz pozycję **moduł IoT Edge**.
3. W bloku **IoT Edge modułów niestandardowych** :

    1. Określ **nazwę** modułu aplikacji WebServer, który chcesz wdrożyć.
    2. Podaj **Identyfikator URI obrazu** dla Twojego obrazu modułu. Pobierany jest moduł pasujący do podanej nazwy i tagów. W takim przypadku program `nginx:stable` będzie ściągał stabilny obraz Nginx (oznaczony jako stabilny) z publicznego [repozytorium platformy Docker](https://hub.docker.com/_/nginx/).
    3. W obszarze **Opcje tworzenia kontenera**wklej następujący przykładowy kod:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Ta konfiguracja pozwala uzyskać dostęp do modułu przy użyciu protokołu IP sieci obliczeniowej za pośrednictwem *protokołu HTTP* na porcie TCP 8080 (z domyślnym portem serwera web: 80).

        ![Określ informacje o porcie w bloku modułu IoT Edge niestandardowego](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. Wybierz pozycję **Zapisz**.

## <a name="verify-module-access"></a>Weryfikuj dostęp do modułu

1. Sprawdź, czy moduł został pomyślnie wdrożony i jest uruchomiony. Na stronie **szczegóły urządzenia** na karcie **moduły** powinien być **uruchomiony**stan środowiska uruchomieniowego modułu.  
2. Połącz się z modułem aplikacji serwera sieci Web. Otwórz okno przeglądarki i wpisz:

    `http://<compute-network-IP-address>:8080`

    Powinieneś zobaczyć, że aplikacja WebServer jest uruchomiona.

    ![Sprawdź połączenie z modułem przez określony port](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [Zarządzać użytkownikami w witrynie Azure Portal](azure-stack-edge-manage-users.md).

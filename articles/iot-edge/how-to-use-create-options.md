---
title: Zapisz opcje dla modułów — Azure IoT Edge | Microsoft Docs
description: Jak używać opcji w manifeście wdrożenia do konfigurowania modułów w czasie wykonywania
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 055b89858fde901ab014e409fbe30c3438efce12
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732993"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Jak skonfigurować opcje tworzenia kontenerów dla modułów IoT Edge

Parametr **isoptions** w manifeście wdrożenia umożliwia skonfigurowanie kontenerów modułów w czasie wykonywania. Ten parametr rozszerza kontrolę nad modułami i umożliwia wykonywanie zadań, takich jak umożliwienie lub ograniczanie dostępu modułu do zasobów urządzenia hosta lub Konfigurowanie sieci.

Moduły IoT Edge są implementowane jako kontenery zgodne z platformą Docker na urządzeniu IoT Edge. Platforma Docker oferuje wiele opcji tworzenia kontenerów i te opcje mają również zastosowanie do modułów IoT Edge. Aby uzyskać więcej informacji, zobacz [Opcje tworzenia kontenera Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Formatowanie opcji tworzenia

Manifest wdrażania IoT Edge akceptuje opcje tworzenia sformatowane jako dane JSON. Na przykład Wypełnij opcje tworzenia automatycznie dołączane do każdego modułu edgeHub:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

Ten edgeHub przykład używa parametru **HostConfig. PortBindings** w celu mapowania uwidocznionych portów w kontenerze na port na urządzeniu hosta.

Jeśli używasz rozszerzeń narzędzi Azure IoT Tools for Visual Studio lub Visual Studio Code, możesz napisać opcje tworzenia w formacie JSON w **deployment.template.js** pliku. Następnie, gdy użyjesz rozszerzenia do skompilowania rozwiązania IoT Edge lub wygenerowania manifestu wdrożenia, stringify to kod JSON w formacie, którego oczekuje środowisko uruchomieniowe IoT Edge. Na przykład:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Jedną z porad dotyczących pisania opcji tworzenia jest użycie `docker inspect` polecenia. W ramach procesu deweloperskiego Uruchom moduł lokalnie za pomocą polecenia `docker run <container name>` . Gdy moduł działa prawidłowo, uruchom polecenie `docker inspect <container name>` . To polecenie wyświetla szczegóły modułu w formacie JSON. Znajdź skonfigurowane parametry i skopiuj kod JSON. Na przykład:

[![Wyniki inspekcji platformy Docker edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Typowe scenariusze

Opcje tworzenia kontenera umożliwiają korzystanie z wielu scenariuszy, ale poniżej przedstawiono niektóre, które często występują w przypadku kompilowania rozwiązań IoT Edge:

* [Zapewnianie modułom dostępu do magazynu hosta](how-to-access-host-storage-from-module.md)
* [Mapuj port hosta na port modułu](#map-host-port-to-module-port)
* [Ogranicz użycie pamięci modułu i procesora CPU](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Mapuj port hosta na port modułu

Jeśli moduł musi komunikować się z usługą spoza rozwiązania IoT Edge i nie korzysta z tej funkcji, należy zmapować port hosta na port modułu.

>[!TIP]
>To mapowanie portu nie jest wymagane w przypadku komunikacji między modułami na tym samym urządzeniu. Jeśli moduł A musi wysyłać zapytania do interfejsu API hostowanego w module B, może to zrobić bez żadnego mapowania portów. Moduł B musi uwidocznić port w pliku dockerfile, na przykład: `EXPOSE 8080` . Następnie moduł A może wysyłać zapytania do interfejsu API przy użyciu nazwy modułu B, na przykład: `http://ModuleB:8080/api` .

Najpierw upewnij się, że port wewnątrz modułu jest narażony na nasłuchiwanie połączeń. Można to zrobić przy użyciu instrukcji [uwidacznianej](https://docs.docker.com/engine/reference/builder/#expose) w pliku dockerfile. Na przykład `EXPOSE 8080`. Jeśli nie określono lub można określić UDP, uwidaczniaj instrukcję jako wartość domyślną protokołu TCP.

Następnie użyj ustawienia **PortBindings** w grupie **HostConfig** [kontenera Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) , aby zamapować uwidoczniony port w module na port na urządzeniu hosta. Na przykład jeśli w module został uwidoczniony port 8080 i chcesz zmapować port 80 urządzenia hosta, opcje tworzenia w template.jsna pliku będą wyglądać podobnie jak w poniższym przykładzie:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Po skonwertowanej dla manifestu wdrażania taka sama konfiguracja będzie wyglądać następująco:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Ogranicz użycie pamięci modułu i procesora CPU

Można zadeklarować, jaka część zasobów hosta może być używana przez moduł. Ta kontrolka pomaga upewnić się, że jeden moduł nie może zużywać zbyt dużej ilości pamięci lub użycia procesora i uniemożliwić uruchamianie innych procesów na urządzeniu. Tymi ustawieniami można zarządzać przy użyciu [opcji tworzenia kontenera platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) w grupie **HostConfig** , w tym:

* **Pamięć**: limit pamięci w bajtach. Na przykład 268435456 bajtów = 256 MB.
* **MemorySwap**: całkowity limit pamięci (pamięć + wymiana). Na przykład 536870912 bajtów = 512 MB
* **CpuPeriod**: długość okresu procesora CPU w mikrosekundach. Wartość domyślna to 100000, więc na przykład wartość 25000 ogranicza kontener do 25% zasobów procesora CPU.

W template.jsw formacie te wartości będą wyglądać podobnie jak w poniższym przykładzie:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Po skonwertowanej dla końcowego manifestu wdrażania te wartości będą wyglądać podobnie jak w poniższym przykładzie:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów dotyczących opcji tworzenia w akcji, zobacz następujące przykłady IoT Edge:

* [Custom Vision i Azure IoT Edge na Raspberry Pi 3](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi)
* [Przykład Azure IoT Edge magazynu obiektów BLOB](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)

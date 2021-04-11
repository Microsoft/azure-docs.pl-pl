---
title: Azure IoT Edge i IoT Central platformy Azure | Microsoft Docs
description: Dowiedz się, jak używać Azure IoT Edge z aplikacją IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: e0f3464420c5cb429f780999bf5983b2ab142567
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608635"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Łączenie urządzeń usługi Azure IoT Edge z aplikacją usługi Azure IoT Central

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

Azure IoT Edge przenosi analizę chmurową i niestandardową logikę biznesową na urządzenia, aby Twoja organizacja mogła skupić się na analizie biznesowej, a nie na zarządzaniu danymi. Skalowanie rozwiązania IoT przez pakowanie logiki biznesowej do standardowych kontenerów, wdrażanie tych kontenerów na urządzeniach i monitorowanie ich z poziomu chmury.

W tym artykule opisano:

* Jak urządzenia IoT Edge łączą się z aplikacją IoT Central.
* Jak używać IoT Central do zarządzania urządzeniami IoT Edge.

Aby dowiedzieć się więcej na temat IoT Edge, zobacz [co to jest Azure IoT Edge?](../../iot-edge/about-iot-edge.md)

## <a name="iot-edge"></a>IoT Edge

IoT Edge składa się z trzech składników:

* *Moduły IoT Edge* są kontenerami, na których działają usługi platformy Azure, usługi partnerskie lub własny kod. Moduły są wdrażane na IoT Edge urządzeniach i uruchamiane lokalnie na tych urządzeniach. Aby dowiedzieć się więcej, zobacz [Opis modułów Azure IoT Edge](../../iot-edge/iot-edge-modules.md).
* *Środowisko uruchomieniowe IoT Edge* działa na każdym urządzeniu IoT Edge i zarządza modułami wdrożonymi na poszczególnych urządzeniach. Środowisko uruchomieniowe składa się z dwóch modułów IoT Edge: *IoT Edge agenta* i *IoT Edge Hub*. Aby dowiedzieć się więcej, zobacz [Omówienie środowiska uruchomieniowego Azure IoT Edge i jego architektury](../../iot-edge/iot-edge-runtime.md).
* *Interfejs oparty na chmurze* umożliwia zdalne monitorowanie IoT Edge urządzeń i zarządzanie nimi. IoT Central jest przykładem interfejsu chmurowego.

Urządzenie IoT Edge może:

* Urządzenie autonomiczne składające się z modułów.
* *Urządzenie bramy* z dołączonymi do niego urządzeniami podrzędnymi.

## <a name="iot-edge-as-a-gateway"></a>IoT Edge jako brama

Urządzenie IoT Edge może działać jako brama, która zapewnia połączenie między innymi urządzeniami podrzędnymi w sieci i IoT Central aplikacji.

Istnieją dwie wzorce bramy:

* W wzorcu *bramy transparentu* moduł IoT Edge Hub zachowuje się jak IoT Central i obsługuje połączenia z urządzeń zarejestrowanych w IoT Central. Komunikaty są przekazywane z urządzeń podrzędnych w celu IoT Central tak, jakby nie ma żadnej bramy między nimi.

* W przypadku wzorca *bramy translacji* urządzenia, które nie mogą łączyć się ze IoT Central, zamiast tego łączą się z niestandardowym modułem IoT Edge. Moduł w IoT Edge przetwarza przychodzące komunikaty urządzenia podrzędnego, a następnie przekazuje je do IoT Central.

Wzorce bramy transparentu i translacji nie wykluczają się wzajemnie. Pojedyncze urządzenie IoT Edge może działać jako nieprzezroczysta brama i Brama translacji.

Aby dowiedzieć się więcej na temat wzorców bramy IoT Edge, zobacz [jak urządzenie IoT Edge może być używane jako brama](../../iot-edge/iot-edge-as-gateway.md).

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relacje urządzeń podrzędnych z bramą i modułami

Urządzenia podrzędne mogą łączyć się z urządzeniem bramy IoT Edge za pomocą modułu *IoT Edge Hub*  . W tym scenariuszu urządzenie IoT Edge jest przezroczystą bramą:

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="Diagram przezroczystej bramy" border="false":::

Urządzenia podrzędne mogą również łączyć się z urządzeniem bramy IoT Edge za pomocą modułu niestandardowego. W poniższym scenariuszu urządzenia podrzędne łączą się za pomocą modułu niestandardowego *Modbus* . W tym scenariuszu urządzenie IoT Edge jest bramą translacji:

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="Diagram połączenia modułu niestandardowego" border="false":::

Na poniższym diagramie przedstawiono połączenia z urządzeniem bramy IoT Edge za pomocą obu typów modułów. W tym scenariuszu urządzenie IoT Edge jest przezroczyste i Brama translacji:

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="Diagram łączenia przy użyciu obu modułów połączeń" border="false":::

Urządzenia podrzędne mogą łączyć się z urządzeniem bramy IoT Edge przy użyciu wielu modułów niestandardowych. Na poniższym diagramie przedstawiono urządzenia podrzędne łączące się za pomocą modułu niestandardowego Modbus, modułu niestandardowego dla sieci i modułu *IoT Edge Hub*  :

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="Diagram łączenia przy użyciu wielu modułów niestandardowych" border="false":::

## <a name="iot-edge-devices-and-iot-central"></a>IoT Edge urządzeń i IoT Central

IoT Edge urządzenia mogą używać tokenów *sygnatury dostępu współdzielonego* lub certyfikatów X. 509 do uwierzytelniania przy użyciu IoT Central. Możesz ręcznie zarejestrować urządzenia IoT Edge w usłudze IoT Central przed połączeniem po raz pierwszy lub użyć usługi Device Provisioning, aby obsłużyć rejestrację. Aby dowiedzieć się więcej, zobacz temat [połączono z usługą Azure IoT Central](concepts-get-connected.md).

IoT Central używa [szablonów urządzeń](concepts-device-templates.md) do definiowania sposobu, w jaki IoT Central współdziała z urządzeniem. Na przykład szablon urządzenia określa:

* Typy telemetrii i właściwości wysyłane przez urządzenie, które IoT Central mogą je interpretować i tworzyć wizualizacje.
* Polecenia, na które urządzenie reaguje, tak aby IoT Central mógł wyświetlić interfejs użytkownika dla operatora, który zostanie użyty do wywołania poleceń.

Urządzenie IoT Edge może wysyłać dane telemetryczne, synchronizować wartości właściwości i odpowiadać na polecenia w taki sam sposób jak w przypadku urządzenia standardowego. W związku z tym urządzenie IoT Edge musi mieć szablon urządzenia w programie IoT Central.

### <a name="iot-edge-device-templates"></a>Szablony urządzeń IoT Edge

Szablony urządzeń IoT Central używają modeli do opisywania możliwości urządzeń. Na poniższym diagramie przedstawiono strukturę modelu dla IoT Edge urządzenia:

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="Struktura modelu dla IoT Edge urządzenia połączonego z IoT Central" border="false":::

IoT Central modeluje urządzenie IoT Edge w następujący sposób:

* Każdy szablon urządzenia IoT Edge ma model możliwości.
* Dla każdego modułu niestandardowego wymienionego w manifeście wdrożenia jest generowany model możliwości modułu.
* Między każdym modelem możliwości modułu a modelem urządzenia zostaje ustanowiona relacja.
* Model możliwości modułu implementuje co najmniej jeden interfejs modułu.
* Każdy interfejs modułu zawiera dane telemetryczne, właściwości i polecenia.

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>IoT Edge manifestów wdrożenia i szablony urządzeń IoT Central

W IoT Edge można wdrożyć logikę biznesową w formie modułów i zarządzać nią. Moduły IoT Edge są najmniejszą jednostką obliczeniową zarządzaną przez IoT Edge i mogą zawierać usługi platformy Azure, takie jak Azure Stream Analytics lub własny kod specyficzny dla rozwiązania.

*Manifest wdrażania* IoT Edge zawiera listę modułów IoT Edge do wdrożenia na urządzeniu oraz sposób ich konfigurowania. Aby dowiedzieć się więcej, zobacz [informacje na temat wdrażania modułów i ustanawiania tras w programie IoT Edge](../../iot-edge/module-composition.md).

Na platformie Azure IoT Central zaimportowano manifest wdrożenia w celu utworzenia szablonu urządzenia dla IoT Edge urządzeniu.

Poniższy fragment kodu przedstawia przykład IoT Edge manifestu wdrażania:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

W poprzednim fragmencie kodu można zobaczyć:

* Istnieją trzy moduły. Moduły *IoT Edge agenta* i *IoT Edge system Hub* , które znajdują się w każdym manifeście wdrożenia. Niestandardowy moduł **SimulatedTemperatureSensor** .
* Obrazy modułu publicznego są pobierane z repozytorium Azure Container Registry, które nie wymaga żadnych poświadczeń do nawiązania połączenia. W przypadku obrazów modułu prywatnego Ustaw poświadczenia rejestru kontenerów, które mają być używane w `registryCredentials` ustawieniu dla modułu *agenta IoT Edge* .
* Niestandardowy moduł **SimulatedTemperatureSensor** ma dwie właściwości `"SendData": true` i `"SendInterval": 10` .

Po zaimportowaniu tego manifestu wdrożenia do aplikacji IoT Central generuje następujący szablon urządzenia:

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="Zrzut ekranu przedstawiający szablon urządzenia utworzony na podstawie manifestu wdrożenia.":::

Na poprzednim zrzucie ekranu można zobaczyć:

* Moduł o nazwie **SimulatedTemperatureSensor**. Moduły *IoT Edge agenta* i *IoT Edge system Hub* nie są wyświetlane w szablonie.
* Interfejs o nazwie **Zarządzanie** , który obejmuje dwie właściwości z możliwością zapisu o nazwie **SendData** i **SendInterval**.

Manifest wdrożenia nie zawiera informacji o telemetrii wysyłanej przez moduł **SimulatedTemperatureSensor** lub poleceniach, na które odpowiada. Dodaj te definicje do szablonu urządzenia ręcznie przed jego opublikowaniem.

Aby dowiedzieć się więcej, zobacz [Samouczek: Dodawanie urządzenia Azure IoT Edge do aplikacji IoT Central platformy Azure](tutorial-add-edge-as-leaf-device.md).

### <a name="update-a-deployment-manifest"></a>Aktualizowanie manifestu wdrożenia

W przypadku tworzenia nowej [wersji](howto-version-device-template.md) szablonu urządzenia można zastąpić manifest wdrożenia nową wersją:

Podczas zastępowania manifestu wdrażania wszystkie połączone IoT Edge urządzenia pobierają nowy manifest i aktualizują moduły. Jednak IoT Central nie aktualizuje interfejsów w szablonie urządzenia zmianami w konfiguracji modułu. Jeśli na przykład zastąpisz manifest przedstawiony w poprzednim fragmencie kodu z następującym manifestem, nie zobaczysz automatycznie właściwości **SendUnits** w interfejsie **zarządzania** w szablonie urządzenia. Ręcznie Dodaj nową właściwość do interfejsu **zarządzania** , aby IoT Central ją rozpoznać:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>Wdróż środowisko uruchomieniowe IoT Edge

Aby dowiedzieć się, gdzie można uruchomić środowisko uruchomieniowe IoT Edge, zobacz [Azure IoT Edge obsługiwane systemy](../../iot-edge/support.md).

Środowisko uruchomieniowe IoT Edge można również zainstalować w następujących środowiskach:

* [Instalowanie lub odinstalowywanie Azure IoT Edge dla systemu Linux](../../iot-edge/how-to-install-iot-edge.md)
* [Instalowanie i aprowizacja usługi IoT Edge dla systemu Linux w systemie Windows (wersja zapoznawcza)](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [Uruchamianie Azure IoT Edge na Ubuntu Virtual Machines na platformie Azure](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>Urządzenia bramy IoT Edge

Jeśli wybrano urządzenie IoT Edge jako urządzenie bramy, można dodać relacje podrzędne do modeli urządzeń dla urządzeń, które mają być połączone z urządzeniem bramy.

Aby dowiedzieć się więcej, zobacz [jak podłączyć urządzenia za poorednictwem niewidocznej bramy IoT Edge](how-to-connect-iot-edge-transparent-gateway.md).

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia, sugerowanym następnym krokiem jest zapoznanie się z tematem [opracowywania własnych modułów IoT Edge](../../iot-edge/module-development.md).

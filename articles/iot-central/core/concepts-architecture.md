---
title: Koncepcje architektury na platformie Azure IoT Central | Microsoft Docs
description: W tym artykule przedstawiono kluczowe pojęcia związane z architekturą IoT Central platformy Azure
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c2d5310d1a664aa2e22d4241d8066e41d9c82bd1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97796724"
---
# <a name="azure-iot-central-architecture"></a>Architektura usługi Azure IoT Central

Ten artykuł zawiera omówienie architektury IoT Central Microsoft Azure.

![Architektura najwyższego poziomu](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Urządzenia

Urządzenia wymieniają dane z aplikacją IoT Central platformy Azure. Urządzenie może:

- Wysyłaj pomiary, takie jak Telemetria.
- Zsynchronizuj ustawienia z aplikacją.

W usłudze Azure IoT Central dane, które urządzenie może wymieniać z aplikacją, są określone w szablonie urządzenia. Aby uzyskać więcej informacji na temat szablonów urządzeń, zobacz [Zarządzanie metadanymi](#metadata-management).

Aby dowiedzieć się więcej o tym, jak urządzenia nawiązują połączenie z aplikacją IoT Central platformy Azure, zobacz [łączność urządzeń](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Urządzenia usługi Azure IoT Edge

A także urządzeń utworzonych przy użyciu [zestawów SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdks), można również łączyć [Azure IoT Edge urządzeń](../../iot-edge/about-iot-edge.md) z aplikacją IoT Central. IoT Edge umożliwia uruchamianie analizy chmurowej i logiki niestandardowej bezpośrednio na urządzeniach IoT zarządzanych przez IoT Central. Środowisko uruchomieniowe IoT Edge umożliwia:

- Instaluje i aktualizuje pakiety robocze na urządzeniu.
- Zachowaj IoT Edge standardy zabezpieczeń na urządzeniu.
- Zapewnia nieprzerwane działanie modułów usługi IoT Edge.
- Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.
- Zarządza komunikacją między podrzędnymi urządzeniami liścia usługi IoT Edge, między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem usługi IoT Edge a chmurą.

![IoT Central platformy Azure z usługą Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central zapewnia następujące możliwości dla urządzeń IoT Edge:

- Szablony urządzeń opisujące możliwości urządzenia IoT Edge, takie jak:
  - Funkcja przekazywania manifestu wdrożenia, która ułatwia zarządzanie manifestem dla floty urządzeń.
  - Moduły uruchomione na urządzeniu IoT Edge.
  - Dane telemetryczne wysyłane przez każdy moduł.
  - Właściwości każdego modułu raportów.
  - Polecenia, na które odpowiada każdy moduł.
  - Relacje między urządzeniem bramy IoT Edge a urządzeniem podrzędnym.
  - Właściwości chmury, które nie są przechowywane na urządzeniu IoT Edge.
  - Dostosowania, pulpity nawigacyjne i formularze, które są częścią aplikacji IoT Central.

  Aby uzyskać więcej informacji, zobacz artykuł [łączenie Azure IoT Edge urządzeniami z aplikacją IoT Central platformy Azure](./concepts-iot-edge.md) .

- Możliwość aprowizacji IoT Edge urządzeń na dużą skalę przy użyciu usługi Azure IoT Device Provisioning
- Reguły i akcje.
- Niestandardowe pulpity nawigacyjne i analizy.
- Ciągły eksport danych telemetrycznych z urządzeń IoT Edge.

### <a name="iot-edge-device-types"></a>IoT Edge typów urządzeń

IoT Central klasyfikuje IoT Edge typów urządzeń w następujący sposób:

- Urządzenia liścia. Urządzenie IoT Edge może mieć podrzędne urządzenia liścia, ale nie są one obsługiwane w programie IoT Central.
- Urządzenia bramy z urządzeniami podrzędnymi. Obsługiwane są zarówno urządzenia bramy, jak i urządzenia podrzędne w IoT Central

![IoT Central z IoT Edge przegląd](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>Wzorce IoT Edge

IoT Central obsługuje następujące wzorce urządzeń IoT Edge:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge jako urządzenie liścia

![IoT Edge jako urządzenie liścia](./media/concepts-architecture/edgeasleafdevice.png)

Urządzenie IoT Edge jest inicjowane w IoT Central, a wszystkie urządzenia podrzędne i ich dane telemetryczne są reprezentowane jako pochodzące z urządzenia IoT Edge. Urządzenia podrzędne połączone z urządzeniem IoT Edge nie są obsługiwane w programie IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge urządzenie bramy połączone z urządzeniami podrzędnymi przy użyciu tożsamości

![IoT Edge z tożsamością urządzenia podrzędnego](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Urządzenie IoT Edge jest inicjowane w IoT Central wraz z urządzeniami podrzędnymi podłączonymi do urządzenia IoT Edge. Obsługa środowiska uruchomieniowego na potrzeby aprowizacji urządzeń podrzędnych za pomocą bramy nie jest obecnie obsługiwana.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge urządzenie bramy połączone z urządzeniami podrzędnymi przy użyciu tożsamości udostępnionej przez bramę IoT Edge

![IoT Edge z urządzeniem podrzędnym bez tożsamości](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Urządzenie IoT Edge jest inicjowane w IoT Central wraz z urządzeniami podrzędnymi podłączonymi do urządzenia IoT Edge. Obsługa przez środowisko uruchomieniowe bramy dostarczającej tożsamość do urządzeń podrzędnych i aprowizacji urządzeń podrzędnych nie jest obecnie obsługiwana. Jeśli utworzysz własny moduł tłumaczenia tożsamości, IoT Central może obsługiwać ten wzorzec.

## <a name="cloud-gateway"></a>Brama chmury

Usługa Azure IoT Central korzysta z usługi Azure IoT Hub jako bramy w chmurze, która umożliwia łączność z urządzeniem. IoT Hub umożliwia:

- Pozyskiwanie danych na dużą skalę w chmurze.
- Zarządzanie urządzeniami.
- Zabezpieczanie łączności urządzeń.

Aby dowiedzieć się więcej na temat IoT Hub, zobacz [Azure IoT Hub](../../iot-hub/index.yml).

Aby dowiedzieć się więcej o łączności urządzeń w usłudze Azure IoT Central, zobacz [łączność urządzeń](concepts-get-connected.md).

## <a name="data-stores"></a>Magazyny danych

Usługa Azure IoT Central przechowuje dane aplikacji w chmurze. Przechowywane dane aplikacji obejmują:

- Szablony urządzeń.
- Tożsamości urządzeń.
- Metadane urządzenia.
- Dane użytkowników i ról.

Usługa Azure IoT Central używa magazynu szeregów czasowych dla danych pomiarów wysyłanych z urządzeń. Dane szeregów czasowych z urządzeń używanych przez usługę analiz.

## <a name="analytics"></a>Analiza

Usługa analizy jest odpowiedzialna za generowanie niestandardowych danych raportowania wyświetlanych przez aplikację. Operator może [dostosować analizę](howto-create-analytics.md) wyświetlaną w aplikacji. Usługa Analytics została utworzona na podstawie [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) i przetwarza dane pomiarów wysyłane z urządzeń.

## <a name="rules-and-actions"></a>Reguły i akcje

[Reguły i akcje](tutorial-create-telemetry-rules.md) ściśle współpracują ze sobą w celu zautomatyzowania zadań w aplikacji. Konstruktor może definiować reguły na podstawie danych telemetrycznych urządzenia, takich jak temperatura przekraczająca określony próg. Usługa Azure IoT Central używa procesora strumienia, aby określić, kiedy są spełnione warunki reguły. Gdy warunek reguły jest spełniony, wyzwala akcję zdefiniowaną przez konstruktora. Na przykład akcja może wysłać wiadomość e-mail, aby poinformować inżyniera, że temperatura urządzenia jest zbyt wysoka.

## <a name="metadata-management"></a>Zarządzanie metadanymi

W aplikacji IoT Central platformy Azure szablony urządzeń określają zachowanie i możliwość typów urządzeń. Na przykład szablon urządzenia chłodziarkd określa dane telemetryczne wysyłane przez chłodziarkę do aplikacji.

![Architektura szablonu](media/concepts-architecture/template-architecture.png)

W IoT Central [szablonu urządzenia](concepts-device-templates.md) znajdują się następujące:

- **Model urządzenia** służący do określania możliwości urządzenia, takich jak dane telemetryczne, które wysyła, właściwości definiujące stan urządzenia oraz poleceń, na które odpowiada urządzenie. Możliwości urządzenia są zorganizowane w jeden lub więcej interfejsów.
- **Właściwości chmury** określają właściwości IoT Central magazynów dla urządzenia. Te właściwości są przechowywane tylko w IoT Central i nigdy nie są wysyłane do urządzenia.
- **Widoki** umożliwiają określanie pulpitów nawigacyjnych i formularzy tworzonych przez konstruktora, aby umożliwić operatorowi monitorowanie urządzeń i zarządzanie nimi.
- **Dostosowania** pozwalają konstruktorowi zastąpić niektóre definicje w modelu urządzenia, aby zwiększyć ich znaczenie do aplikacji IoT Central.

Aplikacja może mieć co najmniej jedno symulowane i rzeczywiste urządzenie w oparciu o każdy szablon urządzenia.

## <a name="data-export"></a>Eksport danych

W aplikacji IoT Central platformy Azure możesz [ciągle eksportować dane](howto-export-data.md) do własnych wystąpień platformy Azure Event Hubs i Azure Service Bus. Możesz również okresowo eksportować dane do konta usługi Azure Blob Storage. IoT Central mogą eksportować pomiary, urządzenia i szablony urządzeń.

## <a name="batch-device-updates"></a>Aktualizacje urządzeń wsadowych

W aplikacji IoT Central platformy Azure można [tworzyć i uruchamiać zadania](howto-run-a-job.md) w celu zarządzania podłączonymi urządzeniami. Te zadania umożliwiają wykonywanie aktualizacji zbiorczych do właściwości lub ustawień urządzenia albo uruchamianie poleceń. Na przykład można utworzyć zadanie, aby zwiększyć szybkość wentylatorów dla wielu maszyn z systemem automatu.

## <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)

Każda aplikacja IoT Central ma własny wbudowany system RBAC. [Administrator może zdefiniować reguły dostępu](howto-manage-users-roles.md) dla aplikacji IoT Central platformy Azure przy użyciu jednej ze wstępnie zdefiniowanych ról lub tworząc rolę niestandardową. Role określają obszary aplikacji, do których użytkownik ma dostęp i jakie akcje mogą wykonywać.

## <a name="security"></a>Zabezpieczenia

Funkcje zabezpieczeń w ramach usługi Azure IoT Central obejmują:

- Dane są szyfrowane podczas przesyłania i przechowywania.
- Uwierzytelnianie jest zapewnione przez Azure Active Directory lub konto Microsoft. Obsługiwane jest uwierzytelnianie dwuskładnikowe.
- Pełna izolacja dzierżawy.
- Zabezpieczenia na poziomie urządzenia.

## <a name="ui-shell"></a>Powłoka interfejsu użytkownika

Powłoka interfejsu użytkownika to nowoczesny, dynamiczny, oparty na przeglądarce HTML5 aplikację.
Administrator może dostosować interfejs użytkownika aplikacji, stosując niestandardowe motywy i modyfikując linki pomocy, aby wskazywały własne niestandardowe zasoby pomocy. Aby dowiedzieć się więcej o dostosowywaniu interfejsu użytkownika, zobacz Dostosowywanie artykułu dotyczącego [interfejsu użytkownika usługi Azure IoT Central](howto-customize-ui.md) .

Operator może tworzyć spersonalizowane pulpity nawigacyjne aplikacji. Można mieć kilka pulpitów nawigacyjnych, które wyświetlają różne dane i przełączają się między nimi.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już o architekturze platformy Azure IoT Central, sugerowanym następnym krokiem jest zapoznanie się z informacjami na temat [łączności urządzeń](concepts-get-connected.md) w usłudze Azure IoT Central.
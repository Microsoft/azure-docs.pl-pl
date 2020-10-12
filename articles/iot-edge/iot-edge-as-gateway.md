---
title: Bramy dla urządzeń podrzędnych — Azure IoT Edge | Microsoft Docs
description: Użyj Azure IoT Edge, aby utworzyć urządzenie nieprzezroczyste lub nieprzezroczyste lub bramy proxy, które wysyła dane z wielu urządzeń podrzędnych do chmury lub przetwarza je lokalnie.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0589779de2ddb0bc75dde3b57d6444634b879f86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89017026"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Jak używać urządzenia usługi IoT Edge jako bramy

Bramy w IoT Edge rozwiązania zapewniają łączność urządzeń z urządzeniami i analizą graniczną na urządzeniach IoT, które w przeciwnym razie nie będą miały tych możliwości. Azure IoT Edge może służyć do zaspokojenia potrzeb bramy IoT, niezależnie od tego, czy jest ona związana z łącznością, tożsamością czy analizą graniczną. Wzorce bramy w tym artykule odnoszą się tylko do charakterystyk łączności urządzenia podrzędnego i tożsamości urządzenia, a nie do przetwarzania danych urządzenia w bramie.

## <a name="patterns"></a>Wzorce

Istnieją trzy wzorce używania urządzenia IoT Edge jako bramy: przezroczyste, translacja protokołów i translacja tożsamości.

Kluczowa różnica między wzorcami polega na tym, że nieprzezroczysta Brama przekazuje komunikaty między urządzeniami podrzędnymi i IoT Hub bez konieczności dodatkowego przetwarzania. Translacja protokołów i translacja tożsamości, jednak wymaga przetwarzania na bramie w celu umożliwienia komunikacji.

Wszystkie bramy mogą używać modułów IoT Edge do wykonywania analiz lub wstępnego przetwarzania na brzegu przed przekazaniem komunikatów z urządzeń podrzędnych do IoT Hub.

![Wzorce diagramów — przezroczyste, protokoły i bramy tożsamości](./media/iot-edge-as-gateway/edge-as-gateway.png)

### <a name="transparent-pattern"></a>Transparent przezroczysty

W przypadku *nieprzezroczystego* wzorca bramy urządzenia, które teoretycznie mogą łączyć się z IoT Hub mogą łączyć się z urządzeniem bramy. Urządzenia podrzędne mają swoje własne tożsamości usługi IoT Hub i korzystają z protokołu MQTT, AMQP lub HTTP. Brama po prostu przekazuje komunikaty pomiędzy urządzeniami a usługą IoT Hub. Zarówno urządzenia, jak i użytkownicy korzystający z nich przy użyciu IoT Hub są nieświadome, że brama mediating ich komunikację. Brak świadomości oznacza, że brama jest uznawana za *przezroczystą*.

Środowisko uruchomieniowe IoT Edge obejmuje możliwości przezroczystej bramy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do działania jako nieprzezroczyste bramy](how-to-create-transparent-gateway.md).

### <a name="protocol-translation-pattern"></a>Wzorzec translacji protokołów

Brama *translacji protokołów* jest również znana jako *nieprzezroczysta* brama, w przeciwieństwie do nieprzezroczystego wzorca bramy. W tym wzorcu urządzenia, które nie obsługują MQTT, AMQP lub HTTP, mogą za pomocą urządzenia bramy wysyłać dane do IoT Hub w ich imieniu. Brama rozumie protokół używany przez urządzenia podrzędne i jest jedynym urządzeniem, które ma tożsamość w usłudze IoT Hub. Wszystkie informacje wyglądają tak, jak pochodzą z jednego urządzenia, bramy. Urządzenia podrzędne muszą osadzić dodatkowe informacje identyfikacyjne w swoich komunikatach, aby aplikacje w chmurze mogły przeanalizować dane według urządzenia. Co więcej typy pierwotne usługi IoT Hub, takie jak urządzenia bliźniacze czy metody, są dostępne tylko dla urządzenia bramy, a nie dla urządzeń podrzędnych.

Środowisko uruchomieniowe IoT Edge nie obejmuje możliwości translacji protokołów. Ten wzorzec wymaga modułów niestandardowych lub innych firm, które są często specyficzne dla używanego sprzętu i protokołu. [Portal Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) zawiera kilka modułów translacji protokołów do wyboru.

### <a name="identity-translation-pattern"></a>Wzorzec tłumaczenia tożsamości

W przypadku wzorca bramy *translacji tożsamości* urządzenia, które nie mogą łączyć się z IoT Hub mogą łączyć się z urządzeniem bramy. Brama zapewnia tożsamość usługi IoT Hub oraz translację protokołów w imieniu urządzeń podrzędnych. Brama jest dostatecznie inteligentna, aby rozumieć protokół używany przez urządzenia podrzędne, zapewniać im tożsamość oraz tłumaczyć typy pierwotne usługi IoT Hub. Urządzenia podrzędne pojawią się w usłudze IoT Hub jako urządzenia pierwszej klasy z urządzeniami bliźniaczymi i metodami. Użytkownik może korzystać z urządzeń w usłudze IoT Hub i nie wie o pośredniczącym urządzeniu bramy.

Środowisko uruchomieniowe IoT Edge nie obejmuje możliwości tłumaczenia tożsamości. Ten wzorzec wymaga modułów niestandardowych lub innych firm, które są często specyficzne dla używanego sprzętu i protokołu. Aby uzyskać przykład, który używa wzorca tłumaczenia tożsamości, zobacz [Azure IoT Edge LoRaWAN Start Kit](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="use-cases"></a>Przypadki zastosowań

Wszystkie wzorce bramy zapewniają następujące korzyści:

* **Analiza na brzegu** — korzystaj z usług AI lokalnie, aby przetwarzać dane pochodzące z urządzeń podrzędnych bez wysyłania danych telemetrycznych o pełnej wierności do chmury. Znajdź i reaguj na szczegółowe informacje lokalnie i wysyłaj tylko podzestaw danych do IoT Hub.
* **Izolacja urządzenia podrzędnego** — urządzenie bramy może osłonować wszystkie urządzenia podrzędne przed narażeniem na Internet. Może ona znajdować się między niedostępną siecią, która nie ma łączności i siecią IT zapewniającą dostęp do sieci Web.
* **Multiplekser połączenia** — wszystkie urządzenia łączące się z IoT Hub za pomocą bramy IoT Edge używają tego samego połączenia podstawowego.
* **Wygładzanie ruchu** — urządzenie IoT Edge automatycznie implementuje wykładniczą wycofywania, jeśli IoT Hub ogranicza ruch, przy jednoczesnym zachowaniu komunikatów lokalnie. Dzięki temu rozwiązanie jest odporne na wzrost ruchu.
* **Obsługa offline** — urządzenie bramy przechowuje komunikaty i aktualizacje bliźniaczye, których nie można dostarczyć do IoT Hub.

Brama, która obsługuje translację protokołów, może obsługiwać istniejące urządzenia i nowe urządzenia z ograniczeniami. Wiele istniejących urządzeń produkuje dane, które mogą być w stanie uzyskać szczegółowe informacje biznesowe. nie zostały one jednak zaprojektowane z myślą o łączności z chmurą. Nieprzezroczyste bramy umożliwiają odblokowywanie tych danych i używanie ich w rozwiązaniu IoT.

Brama, która obsługuje translację tożsamości, zapewnia korzyści z translacji protokołów, a ponadto umożliwia pełne zarządzanie urządzeniami podrzędnymi z chmury. Wszystkie urządzenia w rozwiązaniu IoT są wyświetlane w IoT Hub niezależnie od używanego protokołu.

## <a name="cheat-sheet"></a>Ściągawka

Oto krótki Ściągawka arkusz, który porównuje IoT Hub pierwotne w przypadku używania przezroczystych, nieprzezroczystych (protokołów) i bram proxy.

| Podstawowy | Niewidoczna Brama | Translacja protokołów | Tłumaczenie tożsamości |
|--------|-------------|--------|--------|
| Tożsamości przechowywane w rejestrze tożsamości IoT Hub | Tożsamości wszystkich połączonych urządzeń | Tylko tożsamość urządzenia bramy | Tożsamości wszystkich połączonych urządzeń |
| Bliźniak urządzenia | Każde połączone urządzenie ma własną sznurki urządzenia | Tylko brama ma bliźniaczych reprezentacji urządzenia i modułu | Każde połączone urządzenie ma własną sznurki urządzenia |
| Metody bezpośrednie i komunikaty z chmury do urządzenia | Chmura może zająć się każdym podłączonym urządzeniu osobno | Chmura może tylko zająć urządzenie bramy | Chmura może zająć się każdym podłączonym urządzeniu osobno |
| [IoT Hub ograniczenia i limity przydziału](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Zastosuj do każdego urządzenia | Zastosuj do urządzenia bramy | Zastosuj do każdego urządzenia |

W przypadku korzystania ze wzorca nieprzezroczystej bramy (tłumaczenia protokołu) wszystkie urządzenia łączące się za pośrednictwem tej bramy współużytkują tę samą kolejkę chmurową z urządzeniem, która może zawierać maksymalnie 50 komunikatów. Wynika to z tego, że wzorzec nieprzezroczystej bramy powinien być używany tylko wtedy, gdy kilka urządzeń nawiązuje połączenie przez bramę pola, a ruch z chmury do urządzenia jest niski.

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować niejawną bramę, należy zapoznać się z trzema krokami:

* [Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama](how-to-create-transparent-gateway.md)
* [Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md)

---
title: Bramy dla urządzeń podrzędnych — Azure IoT Edge | Microsoft Docs
description: Użyj Azure IoT Edge, aby utworzyć urządzenie nieprzezroczyste lub nieprzezroczyste lub bramy proxy, które wysyła dane z wielu urządzeń podrzędnych do chmury lub przetwarza je lokalnie.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 916eeaa60bc054301af039164ce1c14e77ceb91a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81733526"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Jak używać urządzenia usługi IoT Edge jako bramy

Bramy w IoT Edge rozwiązania zapewniają łączność urządzeń z urządzeniami i analizą graniczną na urządzeniach IoT, które w przeciwnym razie nie będą miały tych możliwości. Azure IoT Edge może służyć do zaspokojenia potrzeb bramy IoT, niezależnie od tego, czy jest ona związana z łącznością, tożsamością czy analizą graniczną. Wzorce bramy w tym artykule odnoszą się tylko do charakterystyk łączności urządzenia podrzędnego i tożsamości urządzenia, a nie do przetwarzania danych urządzenia w bramie.

## <a name="patterns"></a>Wzorce

Istnieją trzy wzorce używania urządzenia IoT Edge jako bramy: transparent, translacja protokołów i translacja tożsamości:

* **Przezroczyste** — urządzenia, które teoretycznie mogą połączyć się z IoT Hub mogą łączyć się z urządzeniem bramy. Urządzenia podrzędne mają własne tożsamości IoT Hub i korzystają z dowolnych protokołów MQTT, AMQP i HTTP. Brama po prostu przekazuje komunikację między urządzeniami i IoT Hub. Zarówno urządzenia, jak i użytkownicy korzystający z nich przy użyciu IoT Hub są nieświadome, że brama mediating ich komunikację. Brak świadomości oznacza, że brama jest uznawana za *przezroczystą*. Zapoznaj się z tematem [Tworzenie przezroczystej bramy](how-to-create-transparent-gateway.md) , aby uzyskać szczegółowe informacje na temat używania urządzenia IoT Edge jako przezroczystej bramy.
* **Translacja protokołów** — znana także jako wzorzec nieprzezroczystej bramy, urządzenia, które nie obsługują MQTT, AMQP lub http, mogą używać urządzenia bramy do wysyłania danych do IoT Hub w ich imieniu. Brama rozumie protokół używany przez urządzenia podrzędne i jest jedynym urządzeniem, które ma tożsamość w IoT Hub. Wszystkie informacje wyglądają tak, jak pochodzą z jednego urządzenia, bramy. Urządzenia podrzędne muszą osadzać dodatkowe informacje identyfikacyjne w swoich komunikatach, jeśli aplikacje w chmurze chcą analizować dane na poszczególnych urządzeniach. Ponadto IoT Hub typy podstawowe, takie jak bliźniaczych reprezentacji i metody, są dostępne tylko dla urządzenia bramy, a nie dla urządzeń podrzędnych.
* **Tłumaczenie tożsamości** — urządzenia, które nie mogą połączyć się z IoT Hub mogą łączyć się z urządzeniem bramy. Brama zapewnia IoT Hubą tożsamość i translację protokołów w imieniu urządzeń podrzędnych. Brama jest wystarczająco inteligentna, aby zrozumieć protokół używany przez urządzenia podrzędne, zapewnić ich tożsamość i przetłumaczyć IoT Hub elementy pierwotne. Urządzenia podrzędne są wyświetlane w IoT Hub jako urządzenia pierwszej klasy z bliźniaczych reprezentacji i metodami. Użytkownik może współdziałać z urządzeniami w IoT Hub i jest nieświadomy pośredniego urządzenia bramy.

![Wzorce diagramów — przezroczyste, protokoły i bramy tożsamości](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Przypadki zastosowań

Wszystkie wzorce bramy zapewniają następujące korzyści:

* **Analiza na brzegu** — korzystaj z usług AI lokalnie, aby przetwarzać dane pochodzące z urządzeń podrzędnych bez wysyłania danych telemetrycznych o pełnej wierności do chmury. Znajdź i reaguj na szczegółowe informacje lokalnie i wysyłaj tylko podzestaw danych do IoT Hub.
* **Izolacja urządzenia podrzędnego** — urządzenie bramy może osłonować wszystkie urządzenia podrzędne przed narażeniem na Internet. Może ona znajdować się między niedostępną siecią, która nie ma łączności i siecią IT zapewniającą dostęp do sieci Web.
* **Multiplekser połączenia** — wszystkie urządzenia łączące się z IoT Hub za pomocą bramy IoT Edge używają tego samego połączenia podstawowego.
* **Wygładzanie ruchu** — urządzenie IoT Edge automatycznie implementuje wykładniczą wycofywania, jeśli IoT Hub ogranicza ruch, przy jednoczesnym zachowaniu komunikatów lokalnie. Dzięki temu rozwiązanie jest odporne na wzrost ruchu.
* **Obsługa offline** — urządzenie bramy przechowuje komunikaty i aktualizacje bliźniaczye, których nie można dostarczyć do IoT Hub.

Brama, która obsługuje translację protokołów, może również przeprowadzać analizę brzegową, izolację urządzenia, bezproblemowe obciążanie ruchu i obsługiwać w trybie offline istniejące urządzenia oraz nowe urządzenia z ograniczeniami. Wiele istniejących urządzeń produkuje dane, które mogą być w stanie uzyskać szczegółowe informacje biznesowe. nie zostały one jednak zaprojektowane z myślą o łączności z chmurą. Nieprzezroczyste bramy umożliwiają odblokowywanie tych danych i używanie ich w rozwiązaniu IoT.

Brama, która obsługuje translację tożsamości, zapewnia korzyści z translacji protokołów, a ponadto umożliwia pełne zarządzanie urządzeniami podrzędnymi z chmury. Wszystkie urządzenia w rozwiązaniu IoT są wyświetlane w IoT Hub niezależnie od używanego protokołu.

## <a name="cheat-sheet"></a>Ściągawka

Oto krótki Ściągawka arkusz, który porównuje IoT Hub pierwotne w przypadku używania przezroczystych, nieprzezroczystych (protokołów) i bram proxy.

| &nbsp; | Niewidoczna Brama | Translacja protokołów | Tłumaczenie tożsamości |
|--------|-------------|--------|--------|
| Tożsamości przechowywane w rejestrze tożsamości IoT Hub | Tożsamości wszystkich połączonych urządzeń | Tylko tożsamość urządzenia bramy | Tożsamości wszystkich połączonych urządzeń |
| Bliźniak urządzenia | Każde połączone urządzenie ma własną sznurki urządzenia | Tylko brama ma bliźniaczych reprezentacji urządzenia i modułu | Każde połączone urządzenie ma własną sznurki urządzenia |
| Metody bezpośrednie i komunikaty z chmury do urządzenia | Chmura może zająć się każdym podłączonym urządzeniu osobno | Chmura może tylko zająć urządzenie bramy | Chmura może zająć się każdym podłączonym urządzeniu osobno |
| [IoT Hub ograniczenia i limity przydziału](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Zastosuj do każdego urządzenia | Zastosuj do urządzenia bramy | Zastosuj do każdego urządzenia |

W przypadku korzystania ze wzorca nieprzezroczystej bramy (tłumaczenia protokołu) wszystkie urządzenia łączące się za pośrednictwem tej bramy współużytkują tę samą kolejkę chmurową z urządzeniem, która może zawierać maksymalnie 50 komunikatów. Wynika to z tego, że wzorzec nieprzezroczystej bramy powinien być używany tylko wtedy, gdy kilka urządzeń nawiązuje połączenie przez bramę pola, a ruch z chmury do urządzenia jest niski.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować niejawną bramę:

* [Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama](how-to-create-transparent-gateway.md)
* [Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md)

---
title: Bramy dla urządzeń podrzędnych — Usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Usługa Azure IoT Edge umożliwia utworzenie przezroczystego, nieprzezroczystego lub serwera proxy urządzenia bramy, które wysyła dane z wielu urządzeń podrzędnych do chmury lub przetwarza je lokalnie.
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
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733526"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Jak używać urządzenia usługi IoT Edge jako bramy

Bramy w rozwiązaniach Usługi IoT Edge zapewniają łączność urządzeń i analizę brzegową na urządzeniach IoT, które w przeciwnym razie nie miałyby tych możliwości. Usługa Azure IoT Edge może służyć do zaspokojenia wszelkich potrzeb bramy IoT, niezależnie od tego, czy jest to związane z łącznością, tożsamością czy analizą brzegową. Wzorce bramy w tym artykule odnoszą się tylko do cech podrzędnej łączności urządzenia i tożsamości urządzenia, a nie sposobu przetwarzania danych urządzenia na bramie.

## <a name="patterns"></a>Wzorce

Istnieją trzy wzorce dotyczące używania urządzenia Usługi IoT Edge jako bramy: przezroczyste, translacji protokołu i tłumaczenia tożsamości:

* **Przezroczyste** — urządzenia, które teoretycznie mogą łączyć się z Usługą IoT Hub, mogą zamiast tego łączyć się z urządzeniem bramy. Urządzenia podrzędne mają własne tożsamości usługi IoT Hub i używają dowolnych protokołów MQTT, AMQP lub HTTP. Brama po prostu przekazuje komunikację między urządzeniami a centrum IoT Hub. Zarówno urządzenia, jak i użytkownicy wchodzący z nimi w interakcję za pośrednictwem usługi IoT Hub nie są świadomi, że brama pośredniczy w ich komunikacji. Ten brak świadomości oznacza, że brama jest uważana za *przejrzystą.* Zapoznaj się [z artykułem Tworzenie przezroczystej bramy](how-to-create-transparent-gateway.md) dla szczegółów dotyczących używania urządzenia usługi IoT Edge jako bramy przezroczystej.
* **Tłumaczenie protokołu** — znany również jako nieprzezroczysty wzorzec bramy, urządzenia, które nie obsługują MQTT, AMQP lub HTTP, mogą używać urządzenia bramy do wysyłania danych do usługi IoT Hub w ich imieniu. Brama rozumie protokół używany przez urządzenia podrzędne i jest jedynym urządzeniem, które ma tożsamość w centrum IoT Hub. Wszystkie informacje wyglądają tak, jakby pochodziły z jednego urządzenia, bramy. Urządzenia podrzędne muszą osadzić dodatkowe informacje identyfikacyjne w swoich wiadomościach, jeśli aplikacje w chmurze chcą analizować dane na podstawie na urządzenie. Ponadto podstawowe usługi IoT Hub, takie jak bliźniacze i metody są dostępne tylko dla urządzenia bramy, a nie dla urządzeń podrzędnych.
* **Tłumaczenie tożsamości** — urządzenia, które nie mogą połączyć się z usługą IoT Hub, mogą zamiast tego łączyć się z urządzeniem bramy. Brama zapewnia ioT Hub tożsamości i tłumaczenia protokołu w imieniu urządzeń podrzędnych. Brama jest wystarczająco inteligentny, aby zrozumieć protokół używany przez urządzenia podrzędne, podać im tożsamość i przetłumaczyć podstawowe usługi IoT Hub. Urządzenia podrzędne są wyświetlane w Uorie IoT Jako urządzenia pierwszej klasy z bliźniaczymi i metodami. Użytkownik może wchodzić w interakcje z urządzeniami w centrum IoT Hub i nie jest świadomy urządzenia bramy pośredniej.

![Diagram — przezroczyste, protokołowe i wzorce bramy tożsamości](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Przypadki zastosowań

Wszystkie wzorce bramy zapewniają następujące korzyści:

* **Analiza na urządzeniach brzegowych** — korzystaj z usług sztucznej inteligencji lokalnie do przetwarzania danych pochodzących z urządzeń podrzędnych bez wysyłania danych telemetrycznych o pełnej wierności do chmury. Znajdź i reaguj na szczegółowe informacje lokalnie i wysyłaj tylko podzbiór danych do usługi IoT Hub.
* **Izolacja urządzenia niższego rzędu** — urządzenie bramy może chronić wszystkie urządzenia podrzędne przed narażeniem na działanie Internetu. Może siedzieć pomiędzy siecią OT, która nie ma łączności, a siecią IT, która zapewnia dostęp do sieci Web.
* **Multipleksowanie połączeń** — wszystkie urządzenia łączące się z centrum IoT Hub za pośrednictwem bramy usługi IoT Edge używają tego samego połączenia źródłowego.
* **Wygładzanie ruchu** — urządzenie usługi IoT Edge automatycznie zaimplementuje wykładnicze wycofywanie, jeśli usługa IoT Hub ogranicza ruch, jednocześnie utrwalając wiadomości lokalnie. Ta korzyść sprawia, że rozwiązanie jest odporne na skoki ruchu.
* **Obsługa trybu offline** — urządzenie bramy przechowuje wiadomości i bliźniacze aktualizacje, których nie można dostarczyć do usługi IoT Hub.

Brama, która wykonuje translację protokołu, może również wykonywać analizę krawędzi, izolację urządzeń, wygładzanie ruchu i obsługę w trybie offline dla istniejących urządzeń i nowych urządzeń, które są ograniczone zasobami. Wiele istniejących urządzeń wytwarza dane, które mogą zasilać informacje biznesowe; jednak nie zostały one zaprojektowane z myślą o łączności z chmurą. Nieprzezroczyste bramy umożliwiają odblokowanie tych danych i użycie ich w rozwiązaniu IoT.

Brama, która wykonuje translację tożsamości, zapewnia korzyści z tłumaczenia protokołu i dodatkowo umożliwia pełne zarządzanie urządzeniami podrzędnymi z chmury. Wszystkie urządzenia w rozwiązaniu IoT są wyświetlane w centrum IoT Hub niezależnie od używanego protokołu.

## <a name="cheat-sheet"></a>Ściągawka

Oto szybki arkusz, który porównuje prymitywy Usługi IoT Hub podczas korzystania z przezroczystych, nieprzezroczystych (protokół) i bram proxy.

| &nbsp; | Przezroczysta brama | Tłumaczenie protokołu | Tłumaczenie tożsamości |
|--------|-------------|--------|--------|
| Tożsamości przechowywane w rejestrze tożsamości usługi IoT Hub | Tożsamość wszystkich podłączonych urządzeń | Tylko tożsamość urządzenia bramy | Tożsamość wszystkich podłączonych urządzeń |
| Bliźniak urządzenia | Każde podłączone urządzenie ma swój własny | Tylko brama ma urządzenie i bliźniacze moduły | Każde podłączone urządzenie ma swój własny |
| Metody bezpośrednie i komunikaty z chmury do urządzenia | Chmura może indywidualnie zająć się każdym podłączonym urządzeniem | Chmura może dotyczyć tylko urządzenia bramy | Chmura może indywidualnie zająć się każdym podłączonym urządzeniem |
| [Przepustnice i przydziały w centrum IoT](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Zastosuj do każdego urządzenia | Stosowanie do urządzenia bramy | Zastosuj do każdego urządzenia |

Korzystając ze wzorca bramy nieprzezroczystej (translacji protokołu), wszystkie urządzenia łączące się za pośrednictwem tej bramy współużytkują tę samą kolejkę chmury do urządzenia, która może zawierać co najwyżej 50 komunikatów. Wynika z tego, że nieprzezroczysty wzorzec bramy powinien być używany tylko wtedy, gdy niewiele urządzeń łączy się za pośrednictwem każdej bramy polowej, a ich ruch między chmurami a urządzeniami jest niski.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować przezroczystą bramę:

* [Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama](how-to-create-transparent-gateway.md)
* [Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md)

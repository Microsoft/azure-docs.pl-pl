---
title: Bramy dla urządzeń podrzędnych — Azure IoT Edge | Microsoft Docs
description: Użyj Azure IoT Edge, aby utworzyć urządzenie nieprzezroczyste lub nieprzezroczyste lub bramy proxy, które wysyła dane z wielu urządzeń podrzędnych do chmury lub przetwarza je lokalnie.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ca8f02b375420590bcf1cc732c067a165e22b3fa
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492714"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Jak używać urządzenia usługi IoT Edge jako bramy

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Urządzenia IoT Edge mogą działać jako bramy, zapewniając połączenie między innymi urządzeniami w sieci i IoT Hub.

Moduł IoT Edge Hub działa jak IoT Hub, dlatego może obsługiwać połączenia z dowolnego urządzenia, które ma tożsamość z IoT Hub, w tym inne urządzenia IoT Edge. Ten typ wzorca bramy jest wywoływany jako *przezroczysty* , ponieważ komunikaty mogą być przekazywane z urządzeń podrzędnych do IoT Hub tak, jakby nie istniały bramy między nimi.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Począwszy od wersji 1,2 IoT Edge, niewidoczne bramy mogą obsługiwać połączenia podrzędne z innych IoT Edge urządzeń.
::: moniker-end

W przypadku urządzeń, które nie łączą się ze IoT Hub, IoT Edge bramy mogą zapewnić to połączenie. Ten typ wzorca bramy nazywa się *translacją* , ponieważ urządzenie IoT Edge musi wykonać przetwarzanie na przychodzących komunikatach urządzenia podrzędnego przed przekazaniem ich do IoT Hub. Te scenariusze wymagają dodatkowych modułów w bramie IoT Edge, aby obsłużyć kroki przetwarzania.

Wzorce bramy transparentu i translacji nie wykluczają się wzajemnie. Pojedyncze urządzenie IoT Edge może działać jako nieprzezroczysta brama i Brama translacji.

Wszystkie wzorce bramy zapewniają następujące korzyści:

* **Analiza na brzegu** — korzystaj z usług AI lokalnie, aby przetwarzać dane pochodzące z urządzeń podrzędnych bez wysyłania danych telemetrycznych o pełnej wierności do chmury. Znajdź i reaguj na szczegółowe informacje lokalnie i wysyłaj tylko podzestaw danych do IoT Hub.
* **Izolacja urządzenia podrzędnego** — urządzenie bramy może osłonować wszystkie urządzenia podrzędne przed narażeniem na Internet. Może ona znajdować się między siecią z technologią operacyjną (OT), która nie ma łączności i sieci IT zapewniającej dostęp do sieci Web. Podobnie urządzenia, które nie mają możliwości łączenia się ze IoT Hubem, nie mogą nawiązywać połączenia z urządzeniem bramy.
* **Multiplekser połączenia** — wszystkie urządzenia łączące się z IoT Hub za pomocą bramy IoT Edge mogą korzystać z tego samego połączenia podstawowego. Ta funkcja multipleksowania wymaga, aby brama IoT Edge korzystała z AMQP jako protokołu nadrzędnego.
* **Wygładzanie ruchu** — urządzenie IoT Edge automatycznie implementuje wykładniczą wycofywania, jeśli IoT Hub ogranicza ruch, przy jednoczesnym zachowaniu komunikatów lokalnie. Dzięki temu rozwiązanie jest odporne na wzrost ruchu.
* **Obsługa offline** — urządzenie bramy przechowuje komunikaty i aktualizacje bliźniaczye, których nie można dostarczyć do IoT Hub.

## <a name="transparent-gateways"></a>Nieprzezroczyste bramy

W przypadku wzorca bramy przezroczystej urządzenia, które teoretycznie mogą łączyć się z IoT Hub mogą łączyć się z urządzeniem bramy. Urządzenia podrzędne mają własne tożsamości IoT Hub i nawiązują połączenie przy użyciu protokołów MQTT lub AMQP. Brama po prostu przekazuje komunikaty pomiędzy urządzeniami a usługą IoT Hub. Zarówno urządzenia, jak i użytkownicy korzystający z nich przy użyciu IoT Hub są nieświadome, że brama mediating ich komunikację. Brak świadomości oznacza, że brama jest uznawana za *przezroczystą*.

Aby uzyskać więcej informacji na temat sposobu, w jaki usługa IoT Edge Hub zarządza komunikacją między urządzeniami podrzędnymi a chmurą, zobacz [Opis środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Urządzenia IoT Edge nie mogą należeć do IoT Edge bramy.

![Diagram — przezroczysty wzorzec bramy](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

Począwszy od wersji 1.2.0 IoT Edge, urządzenia mogą łączyć się za poorednictwem przezroczystych bram.

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>Relacje nadrzędne i podrzędne

Aby zadeklarować przezroczyste relacje bramy w IoT Hub, należy ustawić bramę IoT Edge jako *nadrzędną* *podrzędną podrzędny element podrzędnego* , który nawiązuje z nim połączenie.

Relacja nadrzędny/podrzędny jest ustanowiona w trzech punktach w konfiguracji bramy:

#### <a name="cloud-identities"></a>Tożsamości w chmurze

Wszystkie urządzenia w niewidocznym scenariuszu bramy wymagają tożsamości w chmurze, dzięki czemu mogą uwierzytelniać się w IoT Hub. Podczas tworzenia lub aktualizowania tożsamości urządzenia można ustawić urządzenia nadrzędne lub podrzędne urządzenia. Ta konfiguracja autoryzuje urządzenie bramy nadrzędnej do obsługi uwierzytelniania na urządzeniach podrzędnych.

>[!NOTE]
>Ustawienie urządzenia nadrzędnego w IoT Hub używany jako opcjonalny krok dla urządzeń podrzędnych korzystających z uwierzytelniania przy użyciu klucza symetrycznego. Jednak począwszy od wersji 1.1.0 każde urządzenie podrzędne musi być przypisane do urządzenia nadrzędnego.
>
>Można skonfigurować Centrum IoT Edge, aby powrócić do poprzedniego zachowania przez ustawienie zmiennej środowiskowej **authenticationMode** na wartość **CloudAndScope**.

Urządzenia podrzędne mogą mieć tylko jeden element nadrzędny. Każdy element nadrzędny może mieć do 100 elementów podrzędnych.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Urządzenia IoT Edge mogą być elementami nadrzędnymi i podrzędnymi w ramach przezroczystych relacji bramy. Można utworzyć hierarchię wielu urządzeń IoT Edge. Górny węzeł hierarchii bramy może mieć maksymalnie pięć generacji elementów podrzędnych. Na przykład urządzenie IoT Edge może mieć pięć warstw IoT Edge urządzeń, które są połączone jako elementy podrzędne poniżej. Ale IoT Edge urządzenie w piątej generacji nie może mieć żadnych elementów podrzędnych, IoT Edge lub w inny sposób.
::: moniker-end

#### <a name="gateway-discovery"></a>Odnajdywanie bramy

Urządzenie podrzędne musi być w stanie znaleźć swoje urządzenie nadrzędne w sieci lokalnej. Skonfiguruj urządzenia bramy przy użyciu nazwy **hosta**, w pełni kwalifikowanej nazwy domeny (FQDN) lub adresu IP, która będzie używana przez jego urządzenia podrzędne do lokalizowania go.

Na podrzędnych urządzeniach IoT Użyj parametru **gatewayHostname** w parametrach połączenia, aby wskazać urządzenie nadrzędne.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Na urządzeniach podrzędnych IoT Edge Użyj parametru **parent_hostname** w pliku konfiguracji, aby wskazać urządzenie nadrzędne.
::: moniker-end

#### <a name="secure-connection"></a>Bezpieczne połączenie

Urządzenia nadrzędne i podrzędne muszą także uwierzytelniać połączenia ze sobą. Każde urządzenie wymaga kopii udostępnionego certyfikatu głównego urzędu certyfikacji, którego urządzenia podrzędne używają do sprawdzenia, czy nawiązują połączenie z odpowiednią bramą.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Gdy wiele bram IoT Edge się łączy ze sobą w hierarchii bramy, wszystkie urządzenia w hierarchii powinny używać jednego łańcucha certyfikatów.
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>Możliwości urządzenia za nieprzezroczystymi bramami

Wszystkie IoT Hub elementy podstawowe działające z potokiem obsługi komunikatów IoT Edge obsługują również niejawne scenariusze bramy. Każda Brama IoT Edge ma możliwość przechowywania i przesyłania komunikatów przesyłanych przez nią.

Skorzystaj z poniższej tabeli, aby dowiedzieć się, w jaki sposób różne możliwości IoT Hub są obsługiwane w przypadku urządzeń w porównaniu z urządzeniami za bramą.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

| Możliwość | Urządzenie IoT | IoT za bramą |
| ---------- | ---------- | -------------------- |
| [Komunikaty między urządzeniami a chmurą (D2C)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Tak — IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — podrzędny D2C IoT](./media/iot-edge-as-gateway/check-yes.png) |
| [Komunikaty z chmury do urządzenia (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Tak — IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — C2D podrzędny IoT](./media/iot-edge-as-gateway/check-yes.png) |
| [Metody bezpośrednie](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Tak — Metoda IoT Direct](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — podrzędna Metoda IoT Direct](./media/iot-edge-as-gateway/check-yes.png) |
| [Bliźniaczych reprezentacji urządzeń](../iot-hub/iot-hub-devguide-device-twins.md) i [moduł bliźniaczych reprezentacji](../iot-hub/iot-hub-devguide-module-twins.md) | ![Tak — IoT bliźniaczych reprezentacji](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — podrzędny bliźniaczych reprezentacji IoT](./media/iot-edge-as-gateway/check-yes.png) |
| [Przekazywanie plików](../iot-hub/iot-hub-devguide-file-upload.md) | ![Tak — przekazywanie plików IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Nie — przekazywanie pliku podrzędnego IoT](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| Możliwość | Urządzenie IoT | IoT za bramą | Urządzenie usługi IoT Edge | IoT Edge za bramą |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [Komunikaty między urządzeniami a chmurą (D2C)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Tak — IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — podrzędny D2C IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — podrzędne IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Komunikaty z chmury do urządzenia (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Tak — IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — C2D podrzędny IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Nie IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![IoT Edge podrzędny C2D](./media/iot-edge-as-gateway/crossout-no.png) |
| [Metody bezpośrednie](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Tak — Metoda IoT Direct](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — podrzędna Metoda IoT Direct](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — Metoda bezpośrednia IoT Edge](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — podrzędna IoT Edge Metoda bezpośrednia](./media/iot-edge-as-gateway/check-yes.png) |
| [Bliźniaczych reprezentacji urządzeń](../iot-hub/iot-hub-devguide-device-twins.md) i [moduł bliźniaczych reprezentacji](../iot-hub/iot-hub-devguide-module-twins.md) | ![Tak — IoT bliźniaczych reprezentacji](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — podrzędny bliźniaczych reprezentacji IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — IoT Edge bliźniaczych reprezentacji](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — podrzędne IoT Edge bliźniaczych reprezentacji](./media/iot-edge-as-gateway/check-yes.png) |
| [Przekazywanie plików](../iot-hub/iot-hub-devguide-file-upload.md) | ![Tak — przekazywanie plików IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Nie — przekazywanie pliku podrzędnego IoT](./media/iot-edge-as-gateway/crossout-no.png) | ![Nie IoT Edge przekazywanie plików](./media/iot-edge-as-gateway/crossout-no.png) | ![Nie IoT Edge przekazywania pliku podrzędnego](./media/iot-edge-as-gateway/crossout-no.png) |
| Ściąganie obrazów kontenerów |   |   | ![Tak — IoT Edge ściągania kontenera](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — podrzędny IoT Edge kontenera ściągania](./media/iot-edge-as-gateway/check-yes.png) |
| Przekazywanie obiektów BLOB |   |   | ![Tak — IoT Edge przekazywania obiektów BLOB](./media/iot-edge-as-gateway/check-yes.png) | ![Tak — przekazywanie obiektów BLOB IoT Edge podrzędnych](./media/iot-edge-as-gateway/check-yes.png) |

**Obrazy kontenerów** mogą być pobierane, przechowywane i dostarczane z urządzeń nadrzędnych do urządzeń podrzędnych.

**Obiekty blob**, w tym zestawy obsługi i dzienniki, można przekazać z urządzeń podrzędnych do urządzeń nadrzędnych.

::: moniker-end

## <a name="translation-gateways"></a>Bramy translacji

Jeśli urządzenia podrzędne nie mogą połączyć się z IoT Hub, Brama IoT Edge musi działać jako translator. Często ten wzorzec jest wymagany w przypadku urządzeń, które nie obsługują MQTT, AMQP lub HTTP. Ponieważ te urządzenia nie mogą połączyć się z IoT Hub, nie mogą także połączyć się z modułem Centrum IoT Edge bez konieczności wstępnego przetwarzania.

Do bramy IoT Edge należy wdrożyć moduły niestandardowe lub inne firmy, które często są specyficzne dla sprzętu lub protokołu urządzenia podrzędnego. Te moduły tłumaczenia przyjmują komunikaty przychodzące i przełączają je do formatu, który może być zrozumiały dla IoT Hub.

Istnieją dwa wzorce dla bram tłumaczeń: *translacja protokołów* i *translacja tożsamości*.

![Diagram — wzorce bramy translacji](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>Translacja protokołów

W przypadku wzorca bramy translacji protokołów tylko Brama IoT Edge ma tożsamość z IoT Hub. Moduł tłumaczenia odbiera komunikaty z urządzeń podrzędnych, tłumaczy je na obsługiwany protokół, a następnie urządzenie IoT Edge wysyła komunikaty w imieniu urządzeń podrzędnych. Wszystkie informacje wyglądają tak, jak pochodzą z jednego urządzenia, bramy. Urządzenia podrzędne muszą osadzić dodatkowe informacje identyfikacyjne w swoich komunikatach, aby aplikacje w chmurze mogły przeanalizować dane według urządzenia. Ponadto IoT Hub elementy podstawowe, takie jak bliźniaczych reprezentacji i metody bezpośrednie są obsługiwane tylko dla urządzenia bramy, a nie urządzeń podrzędnych. Bramy w tym wzorcu są uznawane za *nieprzezroczyste* w przeciwieństwie do przezroczystych bram, ponieważ zasłaniają tożsamości urządzeń podrzędnych.

Translacja protokołów obsługuje urządzenia, które są ograniczone przez zasoby. Wiele istniejących urządzeń produkuje dane, które mogą być w stanie uzyskać szczegółowe informacje biznesowe. nie zostały one jednak zaprojektowane z myślą o łączności z chmurą. Nieprzezroczyste bramy umożliwiają odblokowywanie tych danych i używanie ich w rozwiązaniu IoT.

### <a name="identity-translation"></a>Tłumaczenie tożsamości

Wzorzec bramy translacji tożsamości kompiluje translację protokołów, ale Brama IoT Edge zapewnia również tożsamość urządzenia IoT Hub w imieniu urządzeń podrzędnych. Moduł tłumaczenia jest odpowiedzialny za zrozumienie protokołu używanego przez urządzenia podrzędne, dostarczenie ich tożsamości i przetłumaczenie komunikatów na IoT Hub podstawowych. Urządzenia podrzędne pojawią się w usłudze IoT Hub jako urządzenia pierwszej klasy z urządzeniami bliźniaczymi i metodami. Użytkownik może korzystać z urządzeń w usłudze IoT Hub i nie wie o pośredniczącym urządzeniu bramy.

Translacja tożsamości zapewnia korzyści wynikające z translacji protokołów, a ponadto umożliwia pełne zarządzanie urządzeniami podrzędnymi z chmury. Wszystkie urządzenia w rozwiązaniu IoT są wyświetlane w IoT Hub niezależnie od używanego protokołu.

### <a name="device-capabilities-behind-translation-gateways"></a>Możliwości urządzenia za bramami tłumaczenia

W poniższej tabeli opisano, jak funkcje IoT Hub są rozszerzane na urządzenia podrzędne w obu wzorcach bramy translacji.

| Możliwość | Translacja protokołów | Tłumaczenie tożsamości |
| ---------- | -------------------- | -------------------- |
| Tożsamości przechowywane w rejestrze tożsamości IoT Hub | Tylko tożsamość urządzenia bramy | Tożsamości wszystkich połączonych urządzeń |
| Bliźniak urządzenia | Tylko brama ma bliźniaczych reprezentacji urządzenia i modułu | Każde połączone urządzenie ma własną sznurki urządzenia |
| Metody bezpośrednie i komunikaty z chmury do urządzenia | Chmura może tylko zająć urządzenie bramy | Chmura może zająć się każdym podłączonym urządzeniu osobno |
| [IoT Hub ograniczenia i limity przydziału](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Zastosuj do urządzenia bramy | Zastosuj do każdego urządzenia |

W przypadku używania wzorca translacji protokołu wszystkie urządzenia łączące się za pośrednictwem tej bramy współużytkują tę samą kolejkę chmurową z urządzeniem, która może zawierać co najwyżej 50 komunikatów. Tego wzorca należy używać tylko wtedy, gdy kilka urządzeń nawiązuje połączenie za pomocą każdej bramy pola, a ruch z chmury do urządzenia jest niski.

Środowisko uruchomieniowe IoT Edge nie obejmuje możliwości protokołu ani tłumaczenia tożsamości. Wzorce te wymagają modułów niestandardowych lub innych firm, które są często specyficzne dla używanego sprzętu i protokołu. [Portal Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) zawiera kilka modułów translacji protokołów do wyboru. Aby uzyskać przykład, który używa wzorca tłumaczenia tożsamości, zobacz [Azure IoT Edge LoRaWAN Start Kit](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować niejawną bramę, należy zapoznać się z trzema krokami:

* [Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama](how-to-create-transparent-gateway.md)
* [Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md)

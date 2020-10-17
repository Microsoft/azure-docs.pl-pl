---
title: Skalowanie IoT Hub platformy Azure | Microsoft Docs
description: Sposób skalowania Centrum IoT w celu obsługi przewidywanej przepływności komunikatów i pożądanych funkcji. Zawiera podsumowanie obsługiwanej przepływności dla każdej warstwy i opcji dla fragmentowania.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: Operations'
ms.openlocfilehash: c82f6eb37d542f80b6cb79ffb1fae6fe0cf0c233
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144278"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Wybieranie odpowiedniej warstwy usługi IoT Hub dla używanego rozwiązania

Każde rozwiązanie IoT jest inne, więc usługa Azure IoT Hub oferuje kilka opcji na podstawie cen i skali. Ten artykuł ma na celu pomoc w ocenie potrzeb IoT Hub. Informacje o cenach IoT Hub warstwach znajdują się w temacie [IoT Hub Cennik](https://azure.microsoft.com/pricing/details/iot-hub).

Aby zdecydować, która warstwa IoT Huba jest odpowiednia dla Twojego rozwiązania, należy zadać sobie dwa pytania:

**Jakie funkcje są planowane do użycia?**

Usługa Azure IoT Hub oferuje dwie warstwy, podstawowe i standardowe, które różnią się w zależności od liczby obsługiwanych funkcji. Jeśli Twoje rozwiązanie IoT opiera się na zbieraniu danych z urządzeń i analizowaniu ich centralnie, warstwa podstawowa prawdopodobnie jest dla Ciebie odpowiednia. Jeśli chcesz używać bardziej zaawansowanych konfiguracji w celu zdalnego kontrolowania urządzeń IoT lub dystrybucji niektórych obciążeń na samych urządzeniach, należy wziąć pod uwagę warstwę Standardowa. Aby zapoznać się ze szczegółowymi informacjami o tym, które funkcje są zawarte w poszczególnych warstwach, przejdź do [warstw Podstawowa i Standardowa](#basic-and-standard-tiers).

**Ile danych planuję przenieść codziennie?**

Każda warstwa IoT Hub jest dostępna w trzech rozmiarach, w zależności od tego, ile przepływności danych może obsłużyć w danym dniu. Te rozmiary są liczbowe identyfikowane jako 1, 2 i 3. Na przykład każda jednostka Centrum IoT Level 1 może obsłużyć 400 000 komunikatów dziennie, natomiast jednostka poziomu 3 może obsłużyć 300 000 000. Aby uzyskać więcej informacji na temat wytycznych dotyczących danych, przejdź do [przepływności komunikatów](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Warstwy Podstawowa i Standardowa

Standardowa warstwa IoT Hub włącza wszystkie funkcje i jest wymagana dla wszystkich rozwiązań IoT, które chcą korzystać z funkcji komunikacji dwukierunkowej. Warstwa Podstawowa udostępnia część funkcji i jest przeznaczona dla rozwiązań IoT, które wymagają tylko jednokierunkowej komunikacji z urządzeń do chmury. Obie warstwy oferują te same funkcje zabezpieczeń i uwierzytelniania.

Na IoT Hub można wybrać tylko jeden typ [wersji](https://azure.microsoft.com/pricing/details/iot-hub/) w ramach warstwy. Można na przykład utworzyć IoT Hub z wieloma jednostkami S1, ale nie z różnymi jednostkami, takimi jak S1 i S2.

| Możliwość | Warstwa Podstawowa | Warstwa Bezpłatna/standardowa |
| ---------- | ---------- | ------------- |
| [Dane telemetryczne z urządzenia do chmury](iot-hub-devguide-messaging.md) | Tak | Tak |
| [Tożsamość dla urządzenia](iot-hub-devguide-identity-registry.md) | Tak | Tak |
| [Routing komunikatów](iot-hub-devguide-messages-read-custom.md), [wzbogacanie komunikatów](iot-hub-message-enrichments-overview.md)i [integracja Event Grid](iot-hub-event-grid.md) | Tak | Tak |
| [Protokoły HTTP, AMQP i MQTT](iot-hub-devguide-protocols.md) | Tak | Tak |
| [Device Provisioning Service](../iot-dps/about-iot-dps.md) | Tak | Tak |
| [Monitorowanie i diagnostyka](iot-hub-monitor-resource-health.md) | Tak | Tak |
| [Obsługa komunikatów z chmury do urządzenia](iot-hub-devguide-c2d-guidance.md) |   | Tak |
| [Bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md), [moduł bliźniaczych reprezentacji](iot-hub-devguide-module-twins.md)i [Zarządzanie urządzeniami](iot-hub-device-management-overview.md) |   | Tak |
| [Strumienie urządzeń (wersja zapoznawcza)](iot-hub-device-streams-overview.md) |   | Tak |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Tak |
| [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) |   | Tak |

IoT Hub oferuje również bezpłatną warstwę, która jest przeznaczona do testowania i oceny. Ma ona wszystkie możliwości warstwy Standard, ale ograniczone są ograniczenia obsługi komunikatów. Nie można przeprowadzić uaktualnienia z warstwy Bezpłatna do wersji podstawowa lub standardowa.

## <a name="partitions"></a>Partycje

Centra usługi Azure IoT zawierają wiele podstawowych składników [Event Hubs platformy Azure](../event-hubs/event-hubs-features.md), w tym [partycji](../event-hubs/event-hubs-features.md#partitions). Strumienie zdarzeń dla centrów IoT są zwykle wypełniane przez dane telemetryczne przychodzące zgłaszane przez różne urządzenia IoT. Partycjonowanie strumienia zdarzeń służy do zmniejszania rywalizacji występujących podczas równoczesnego odczytywania i zapisywania do strumieni zdarzeń.

Limit partycji jest wybierany podczas tworzenia IoT Hub i nie można go zmienić. Maksymalny limit partycji IoT Hub warstwy Podstawowa i IoT Hub to 32. Większość centrów IoT potrzebuje tylko 4 partycji. Aby uzyskać więcej informacji na temat określania partycji, zobacz Event Hubs często zadawane pytania dotyczące [liczby potrzebnych partycji?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Uaktualnianie warstwy

Po utworzeniu usługi IoT Hub można przeprowadzić uaktualnienie z warstwy Podstawowa do warstwy Standardowa bez przerywania istniejących operacji. Aby uzyskać więcej informacji, zobacz [jak uaktualnić Centrum IoT Hub](iot-hub-upgrade.md).

Konfiguracja partycji pozostaje niezmieniona podczas migracji z warstwy Podstawowa do warstwy Standardowa.

> [!NOTE]
> Warstwa Bezpłatna nie obsługuje uaktualniania do wersji podstawowa lub standardowa.

## <a name="iot-hub-rest-apis"></a>Interfejsy API REST IoT Hub

Różnica w obsługiwanych możliwościach między warstwami podstawowa i Standardowa IoT Hub oznacza, że niektóre wywołania interfejsu API nie działają w przypadku centrów warstwy Podstawowa. W poniższej tabeli przedstawiono dostępne interfejsy API:

| Interfejs API | Warstwa Podstawowa | Warstwa Bezpłatna/standardowa |
| --- | ---------- | ------------- |
| [Usuń urządzenie](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#deletedevice-string--models-registrymanagerdeletedeviceoptionalparams-) | Tak | Tak |
| [Pobierz urządzenie](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-getdevice) | Tak | Tak |
| [Usuń moduł](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletemodule) | Tak | Tak |
| [Pobierz moduł](/java/api/com.microsoft.azure.sdk.iot.service.registrymanager.getmodule?view=azure-java-stable) | Tak | Tak |
| [Pobierz statystyki rejestru](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#getdevicestatistics-msrest-requestoptionsbase-) | Tak | Tak |
| [Pobierz statystyki usług](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#getservicestatistics-msrest-requestoptionsbase-) | Tak | Tak |
| [Utwórz lub zaktualizuj urządzenie](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#createorupdatedevice-string--device--servicecallback-device--) | Tak | Tak |
| [Utwórz lub zaktualizuj moduł](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#createorupdatemodule-string--string--module--models-registrymanagercreateorupdatemoduleoptionalparams-) | Tak | Tak |
| [IoT Hub zapytań](/dotnet/api/microsoft.azure.devices.registrymanager?view=azure-dotnet) | Tak | Tak |
| [Tworzenie identyfikatora URI SAS przekazywania plików](/rest/api/iothub/device/createfileuploadsasuri) | Tak | Tak |
| [Odbieranie powiadomienia powiązanego z urządzeniem](/rest/api/iothub/device/receivedeviceboundnotification) | Tak | Tak |
| [Wyślij zdarzenie urządzenia](/rest/api/iothub/device/senddeviceevent) | Tak | Tak |
| Wyślij zdarzenie modułu | Tylko AMQP i MQTT | Tylko AMQP i MQTT |
| [Aktualizuj stan przekazywania plików](/rest/api/iothub/device/updatefileuploadstatus) | Tak | Tak |
| [Zbiorcza operacja urządzenia](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#bulkdevicecrud-exportimportdevice----msrest-requestoptionsbase-) | Tak, z wyjątkiem możliwości IoT Edge | Tak |
| [Anuluj zadanie importowania eksportu](/rest/api/iothub/service/jobs/cancelimportexportjob) | Tak | Tak |
| [Tworzenie zadania importowania eksportu](/rest/api/iothub/service/jobs/createimportexportjob) | Tak | Tak |
| [Pobierz zadanie importowania eksportu](/rest/api/iothub/service/jobs/getimportexportjob) | Tak | Tak |
| [Pobierz zadania importowania eksportu](/rest/api/iothub/service/jobs/getimportexportjobs) | Tak | Tak |
| [Przeczyść kolejkę poleceń](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#purgecommandqueue-string--msrest-requestoptionsbase-) |   | Tak |
| [Pobierz sznurki urządzenia](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin?view=azure-java-stable) |   | Tak |
| [Pobierz sznurki modułu](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-getmoduletwin) |   | Tak |
| [Wywoływanie metody urządzenia](./iot-hub-devguide-direct-methods.md) |   | Tak |
| [Aktualizacja sznurka urządzenia](./iot-hub-devguide-device-twins.md) |   | Tak |
| [Aktualizowanie sznurka modułu](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-updatemoduletwin) |   | Tak |
| [Powiadomienie o porzucenia urządzenia](/rest/api/iothub/device/abandondeviceboundnotification) |   | Tak |
| [Ukończ powiadomienie powiązane z urządzeniem](/rest/api/iothub/device/completedeviceboundnotification) |   | Tak |
| [Anuluj zadanie](/rest/api/media/jobs/canceljob) |   | Tak |
| [Tworzenie zadania](/rest/api/media/jobs/create) |   | Tak |
| [Pobierz zadanie](/java/api/com.microsoft.azure.sdk.iot.service.jobs.jobclient.getjob?view=azure-java-stable) |   | Tak |
| [Zadania zapytań](/javascript/api/azure-iot-digitaltwins-service/jobclient?view=azure-node-latest#queryjobs-jobclientqueryjobsoptionalparams--servicecallback-queryresult--) |   | Tak |

## <a name="message-throughput"></a>Przepływność komunikatów

Najlepszym sposobem na dopasowanie rozwiązania IoT Hub jest oszacowanie ruchu w poszczególnych jednostkach. W szczególności należy wziąć pod uwagę wymaganą przepustowość szczytową dla następujących kategorii operacji:

* Komunikaty z urządzenia do chmury
* Komunikaty z chmury do urządzenia
* Operacje rejestru tożsamości

Ruch jest mierzony dla Centrum IoT w przypadku poszczególnych jednostek. Podczas tworzenia Centrum IoT wybierz jego warstwę i wersję, a następnie ustaw liczbę dostępnych jednostek. Możesz zakupić do 200 jednostek dla wersji B1, B2, S1 lub S2 lub do 10 jednostek dla wersji B3 lub S3. Po utworzeniu usługi IoT Hub można zmienić liczbę jednostek dostępnych w ramach swojej wersji, uaktualnić lub obniżyć wersje między wersjami w ramach jej warstwy (od B1 do B2) lub uaktualnić z poziomu podstawowego do warstwy Standardowa (B1 do S1) bez zakłócania istniejących operacji. Aby uzyskać więcej informacji, zobacz [jak uaktualnić Centrum IoT Hub](iot-hub-upgrade.md).  

Przykładem możliwości ruchu poszczególnych warstw, komunikaty z urządzenia do chmury są następujące:

| Wersja warstwy | Ciągła przepływność | Ciągła szybkość wysyłania |
| --- | --- | --- |
| B1, S1 |Do 1111 KB/minutę na jednostkę<br/>(1,5 GB/dzień/jednostka) |Średnia z 278 komunikatów na minutę na jednostkę<br/>(400 000 komunikatów dziennie na jednostkę) |
| B2, S2 |Do 16 MB na minutę na jednostkę<br/>(22,8 GB/dzień/jednostka) |Średnia z 4 167 komunikatów na minutę na jednostkę<br/>(6 000 000 komunikatów dziennie na jednostkę) |
| B3, S3 |Do 814 MB/minutę na jednostkę<br/>(1144,4 GB/dzień/jednostka) |Średnia z 208 333 komunikatów na minutę na jednostkę<br/>(300 000 000 komunikatów dziennie na jednostkę) |

Przepływność między urządzeniami a chmurą jest tylko jedną z metryk, które należy wziąć pod uwagę podczas projektowania rozwiązania IoT. Aby uzyskać bardziej szczegółowe informacje, zobacz [IoT Hub przydziały i ograniczenia](iot-hub-devguide-quotas-throttling.md).

### <a name="identity-registry-operation-throughput"></a>Przepływność operacji rejestru tożsamości

Operacje rejestru tożsamości IoT Hub nie powinny być operacjami w czasie wykonywania, ponieważ są one głównie związane z aprowizacji urządzeń.

Aby uzyskać określone numery wydajności, zobacz [IoT Hub przydziały i ograniczenia](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Automatyczne skalowanie

Jeśli zbliżasz się do dozwolonego limitu komunikatów w centrum IoT, możesz użyć tych [kroków do automatycznego skalowania](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) , aby zwiększyć jednostkę IoT Hub w tej samej warstwie IoT Hub.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat IoT Hub możliwości i szczegółów wydajności, zobacz [IoT Hub cennika](https://azure.microsoft.com/pricing/details/iot-hub) lub [IoT Hub przydziałów i ograniczeń](iot-hub-devguide-quotas-throttling.md).

* Aby zmienić warstwę IoT Hub, wykonaj kroki opisane w sekcji [uaktualnianie Centrum IoT Hub](iot-hub-upgrade.md).
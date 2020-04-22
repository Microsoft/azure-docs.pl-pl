---
title: Skalowanie usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Jak skalować centrum IoT hub do obsługi przewidywanej przepływności wiadomości i żądanych funkcji. Zawiera podsumowanie obsługiwane przepływności dla każdej warstwy i opcje dzielenia na fragmenty.
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
ms.openlocfilehash: 4b18878cfc5c75bf27fd46cbceaa06e0b6053ddd
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759621"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Wybieranie odpowiedniej warstwy usługi IoT Hub dla używanego rozwiązania

Każde rozwiązanie IoT jest inne, więc usługa Azure IoT Hub oferuje kilka opcji opartych na cenach i skali. Ten artykuł ma na celu pomóc w ocenie potrzeb usługi IoT Hub. Aby uzyskać informacje o cenach dotyczących warstw Usługi IoT Hub, zobacz [Cennik usługi IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub).

Aby zdecydować, która warstwa Usługi IoT Hub jest odpowiednia dla rozwiązania, zadaj sobie dwa pytania:

**Jakich funkcji zamierzam używać?**

Usługa Azure IoT Hub oferuje dwie warstwy, podstawowe i standardowe, które różnią się liczbą obsługiwanych funkcji. Jeśli rozwiązanie IoT opiera się na zbieraniu danych z urządzeń i analizowaniu ich centralnie, warstwa podstawowa jest prawdopodobnie odpowiednia dla Ciebie. Jeśli chcesz używać bardziej zaawansowanych konfiguracji do zdalnego sterowania urządzeniami IoT lub dystrybucji niektórych obciążeń na samych urządzeniach, należy wziąć pod uwagę warstwę standardową. Aby uzyskać szczegółowy podział, które funkcje są zawarte w każdej warstwie, przejdź do [warstwy podstawowej i standardowej](#basic-and-standard-tiers).

**Ile danych mam zamiar przenieść codziennie?**

Każda warstwa usługi IoT Hub jest dostępna w trzech rozmiarach, na podstawie ilości przepływności danych, które mogą obsłużyć w danym dniu. Te rozmiary są identyfikowane numerycznie jako 1, 2 i 3. Na przykład każda jednostka centrum IoT poziomu 1 może obsługiwać 400 tysięcy wiadomości dziennie, podczas gdy jednostka poziomu 3 może obsłużyć 300 milionów. Aby uzyskać więcej informacji na temat wytycznych dotyczących danych, przejdź do [przepływności wiadomości](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Warstwy podstawowe i standardowe

Warstwa standardowa usługi IoT Hub umożliwia wszystkie funkcje i jest wymagana dla wszystkich rozwiązań IoT, które chcą korzystać z możliwości komunikacji dwukierunkowej. Warstwa Podstawowa udostępnia część funkcji i jest przeznaczona dla rozwiązań IoT, które wymagają tylko jednokierunkowej komunikacji z urządzeń do chmury. Obie warstwy oferują te same funkcje zabezpieczeń i uwierzytelniania.

Tylko jeden typ [wersji](https://azure.microsoft.com/pricing/details/iot-hub/) w ramach warstwy można wybrać dla usługi IoT Hub. Na przykład można utworzyć Centrum IoT z wieloma jednostkami S1, ale nie z mieszanką jednostek z różnych wersji, takich jak S1 i S2.

| Możliwości | Warstwa Podstawowa | Warstwa bezpłatna/standardowa |
| ---------- | ---------- | ------------- |
| [Telemetria między urządzeniami a chmurami](iot-hub-devguide-messaging.md) | Tak | Tak |
| [Tożsamość na urządzenie](iot-hub-devguide-identity-registry.md) | Tak | Tak |
| [Routing wiadomości,](iot-hub-devguide-messages-read-custom.md) [wzbogacanie wiadomości](iot-hub-message-enrichments-overview.md)i [integracja z siatką zdarzeń](iot-hub-event-grid.md) | Tak | Tak |
| [Protokoły HTTP, AMQP i MQTT](iot-hub-devguide-protocols.md) | Tak | Tak |
| [Usługa inicjowania obsługi administracyjnej urządzeń](../iot-dps/about-iot-dps.md) | Tak | Tak |
| [Monitorowanie i diagnostyka](iot-hub-monitor-resource-health.md) | Tak | Tak |
| [Wiadomości między chmurami a urządzeniem](iot-hub-devguide-c2d-guidance.md) |   | Tak |
| [Bliźniacze urządzenia,](iot-hub-devguide-device-twins.md) [bliźniacze moduły](iot-hub-devguide-module-twins.md)i [zarządzanie urządzeniami](iot-hub-device-management-overview.md) |   | Tak |
| [Strumienie urządzeń (wersja zapoznawcza)](iot-hub-device-streams-overview.md) |   | Tak |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Tak |
| [Podgląd plug and play IoT](../iot-pnp/overview-iot-plug-and-play.md) |   | Tak |

Centrum IoT hub oferuje również bezpłatną warstwę przeznaczoną do testowania i oceny. Ma wszystkie możliwości warstwy standardowej, ale ograniczone uprawnienia do obsługi wiadomości. Nie można uaktualnić z warstwy bezpłatnej do podstawowej lub standardowej.

## <a name="partitions"></a>Partycje

Usługi Azure IoT Hubs zawierają wiele podstawowych składników [usługi Azure Event Hubs,](../event-hubs/event-hubs-features.md)w tym [partycje.](../event-hubs/event-hubs-features.md#partitions) Strumienie zdarzeń dla centrów IoT są zazwyczaj wypełniane przychodzących danych telemetrycznych, które są zgłaszane przez różne urządzenia IoT. Partycjonowanie strumienia zdarzeń jest używany do zmniejszenia rywalizacji, które występują podczas jednoczesnego odczytu i zapisu do strumieni zdarzeń.

Limit partycji jest wybierany podczas tworzenia usługi IoT Hub i nie można go zmienić. Maksymalny limit partycji dla centrum IoT warstwy podstawowej i centrum IoT w warstwie standardowej wynosi 32. Większość centrów IoT potrzebuje tylko 4 partycji. Aby uzyskać więcej informacji na temat określania partycji, zobacz Event Hubs często zadawane pytania [Ile partycji potrzebuję?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Uaktualnianie poziomu

Po utworzeniu centrum IoT hub, można uaktualnić z warstwy podstawowej do warstwy standardowej bez przerywania istniejących operacji. Aby uzyskać więcej informacji, zobacz [Jak uaktualnić centrum IoT hub](iot-hub-upgrade.md).

Konfiguracja partycji pozostaje niezmieniona podczas migracji z warstwy podstawowej do warstwy standardowej.

> [!NOTE]
> Warstwa bezpłatna nie obsługuje uaktualniania do poziomu podstawowego lub standardowego.

## <a name="iot-hub-rest-apis"></a>Interfejsy API REST usługi IoT Hub

Różnica w obsługiwanych możliwościach między podstawową i standardową warstwą Usługi IoT Hub oznacza, że niektóre wywołania interfejsu API nie działają z podstawowymi koncentratorami warstwy. W poniższej tabeli przedstawiono, które interfejsy API są dostępne:

| interfejs API | Warstwa Podstawowa | Warstwa bezpłatna/standardowa |
| --- | ---------- | ------------- |
| [Usuwanie urządzenia](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletedevice) | Tak | Tak |
| [Pobierz urządzenie](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevice) | Tak | Tak |
| [Usuń moduł](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletemodule) | Tak | Tak |
| [Pobierz moduł](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getmodule) | Tak | Tak |
| [Pobierz statystyki rejestru](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevicestatistics) | Tak | Tak |
| [Pobierz statystyki usług](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getservicestatistics) | Tak | Tak |
| [Tworzenie lub aktualizowanie urządzenia](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatedevice) | Tak | Tak |
| [Tworzenie lub aktualizowanie modułu](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatemodule) | Tak | Tak |
| [Centrum IoT kwerend](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/queryiothub) | Tak | Tak |
| [Tworzenie identyfikatora URI dostępu współdzielonego z przekazywaniem plików](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Tak | Tak |
| [Odbieranie powiadomień związanych z urządzeniem](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Tak | Tak |
| [Wysyłanie zdarzenia urządzenia](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Tak | Tak |
| Wyślij zdarzenie modułu | Tylko AMQP i MQTT | Tylko AMQP i MQTT |
| [Aktualizuj stan przekazywania plików](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Tak | Tak |
| [Praca urządzenia zbiorczego](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/bulkdevicecrud) | Tak, z wyjątkiem funkcji IoT Edge | Tak |
| [Anuluj zadanie eksportu importu](https://docs.microsoft.com/rest/api/iothub/service/jobclient/cancelimportexportjob) | Tak | Tak |
| [Tworzenie zadania eksportu importu](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createimportexportjob) | Tak | Tak |
| [Pobierz zadanie eksportu importu](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjob) | Tak | Tak |
| [Pobierz zadania eksportu importu](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) | Tak | Tak |
| [Przeczyść kolejkę poleceń](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) |   | Tak |
| [Pobierz bliźniaczy dwójkę urządzenia](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin) |   | Tak |
| [Pobierz bliźniaczy moduł](https://docs.microsoft.com/rest/api/iothub/service/twin/getmoduletwin) |   | Tak |
| [Wywoływanie metody urządzenia](https://docs.microsoft.com/rest/api/iothub/service/devicemethod/invokedevicemethod) |   | Tak |
| [Aktualizacja bliźniaczej reprezentacji urządzenia](https://docs.microsoft.com/rest/api/iothub/service/twin/updatedevicetwin) |   | Tak |
| [Aktualizacja modułu bliźniaczego](https://docs.microsoft.com/rest/api/iothub/service/twin/updatemoduletwin) |   | Tak |
| [Porzucenie powiadomienia związanego z urządzeniem](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Tak |
| [Kompletne powiadomienie związane z urządzeniem](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Tak |
| [Anuluj zadanie](https://docs.microsoft.com/rest/api/iothub/service/jobclient/canceljob) |   | Tak |
| [Tworzenie zadania](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createjob) |   | Tak |
| [Zdobądź pracę](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getjob) |   | Tak |
| [Zadania kwerendy](https://docs.microsoft.com/rest/api/iothub/service/jobclient/queryjobs) |   | Tak |

## <a name="message-throughput"></a>Przepływność wiadomości

Najlepszym sposobem na rozmiar rozwiązania Usługi IoT Hub jest ocena ruchu na podstawie na jednostkę. W szczególności należy wziąć pod uwagę wymaganą przepustowość szczytową dla następujących kategorii operacji:

* Komunikaty z urządzenia do chmury
* Komunikaty z chmury do urządzenia
* Operacje rejestru tożsamości

Ruch jest mierzony dla centrum IoT na podstawie jednostki. Podczas tworzenia centrum IoT hub, należy wybrać jego warstwy i wersji i ustawić liczbę dostępnych jednostek. Można zakupić do 200 jednostek dla wersji B1, B2, S1 lub S2 lub do 10 jednostek dla wersji B3 lub S3. Po utworzeniu centrum IoT hub można zmienić liczbę jednostek dostępnych w jego wersji, uaktualnić lub obniżyć między wersjami w ramach jego warstwy (B1 do B2) lub uaktualnić z warstwy podstawowej do warstwy standardowej (B1 do S1) bez przerywania istniejących operacji. Aby uzyskać więcej informacji, zobacz [Jak uaktualnić centrum IoT hub](iot-hub-upgrade.md).  

Jako przykład możliwości ruchu każdej warstwy komunikaty między urządzeniami do chmury są zgodne z następującymi wytycznymi dotyczącymi trwałej przepływności:

| Edycja warstwowa | Trwała przepustowość | Stała szybkość wysyłania |
| --- | --- | --- |
| B1, S1 |Do 1111 KB/minutę na jednostkę<br/>(1,5 GB/dzień/jednostka) |Średnio 278 wiadomości na minutę na jednostkę<br/>(400 000 wiadomości dziennie na jednostkę) |
| B2, S2 |Do 16 MB/minutę na jednostkę<br/>(22,8 GB/dzień/jednostka) |Średnia 4167 wiadomości na minutę na jednostkę<br/>(6 milionów wiadomości dziennie na jednostkę) |
| B3, S3 |Do 814 MB/minutę na jednostkę<br/>(1144,4 GB/dzień/jednostka) |Średnia 208 333 wiadomości na minutę na jednostkę<br/>(300 milionów wiadomości dziennie na jednostkę) |

Przepustowość między urządzeniami a chmurą jest tylko jedną z metryk, które należy wziąć pod uwagę podczas projektowania rozwiązania IoT. Aby uzyskać bardziej wyczerpujące informacje, zobacz [Przydziały i ograniczenia usługi IoT Hub](iot-hub-devguide-quotas-throttling.md).

### <a name="identity-registry-operation-throughput"></a>Przepływność operacji rejestru tożsamości

Operacje rejestru tożsamości usługi IoT Hub nie powinny być operacjami w czasie wykonywania, ponieważ są one głównie związane z inicjowania obsługi administracyjnej urządzeń.

Aby uzyskać określone numery wydajności serii, zobacz [Przydziały i ograniczenia usługi IoT Hub](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Automatyczne skalowanie

Jeśli zbliżasz się do dozwolonego limitu komunikatów w centrum IoT hub, możesz użyć tych [kroków, aby automatycznie skalować,](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) aby zwiększyć jednostkę Centrum IoT w tej samej warstwie Usługi IoT Hub.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat możliwości i szczegółów wydajności usługi IoT Hub, zobacz Ceny i ograniczenia i ograniczenia usługi [IoT](https://azure.microsoft.com/pricing/details/iot-hub) Hub lub [IoT Hub](iot-hub-devguide-quotas-throttling.md).

* Aby zmienić warstwę Usługi IoT Hub, wykonaj kroki opisane w [aplikacji Uaktualnianie centrum IoT hub](iot-hub-upgrade.md).

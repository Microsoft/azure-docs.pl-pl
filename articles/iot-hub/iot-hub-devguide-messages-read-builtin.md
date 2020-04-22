---
title: Opis wbudowanego punktu końcowego usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — opisuje sposób używania wbudowanego punktu końcowego zgodnego z centrum zdarzeń do odczytywania komunikatów z urządzenia do chmury.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.custom: amqp
ms.openlocfilehash: 169d926e466559bc83ba64ce9e976e0d725f614d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729985"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Odczytywanie komunikatów przesyłanych z urządzeń do chmury z wbudowanego punktu końcowego

Domyślnie wiadomości są kierowane do wbudowanego punktu końcowego skierowanego do usługi **(wiadomości/zdarzeń),** który jest zgodny z [Centrum zdarzeń](https://azure.microsoft.com/documentation/services/event-hubs/). Ten punkt końcowy jest obecnie tylko narażony przy użyciu protokołu [AMQP](https://www.amqp.org/) na porcie 5671. Centrum IoT udostępnia następujące właściwości, aby umożliwić sterowanie wbudowanymi **komunikatami/zdarzeniami**z punktumi końcowymi obsługującymi usługi Event Hub.

| Właściwość            | Opis |
| ------------------- | ----------- |
| **Liczba partycji** | Ustaw tę właściwość w tworzeniu, aby zdefiniować liczbę [partycji](../event-hubs/event-hubs-features.md#partitions) dla pozyskiwania zdarzeń urządzenia do chmury. |
| **Czas przechowywania**  | Ta właściwość określa, jak długo w dniach wiadomości są zachowywane przez Centrum IoT. Wartość domyślna to jeden dzień, ale można ją zwiększyć do siedmiu dni. |

Usługa IoT Hub umożliwia przechowywanie danych we wbudowanych centrach zdarzeń przez maksymalnie 7 dni. Można ustawić czas przechowywania podczas tworzenia centrum IoT Hub. Czas przechowywania danych w centrum IoT Hub zależy od warstwy i typu jednostki centrum IoT. Pod względem rozmiaru wbudowane centra zdarzeń mogą przechowywać wiadomości o maksymalnym rozmiarze wiadomości do co najmniej 24 godzin przydziału. Na przykład dla 1 jednostki S1 IoT Hub zapewnia wystarczającą ilość miejsca do przechowywania co najmniej 400K wiadomości o rozmiarze 4k każdy. Jeśli urządzenia wysyłają mniejsze wiadomości, mogą być przechowywane przez dłuższy czas (do 7 dni) w zależności od ilości zużywanego miejsca. Gwarantujemy zachowanie danych przez określony czas przechowywania jako minimum. Wiadomości wygasną i nie będą dostępne po upływie czasu przechowywania. 

Usługa IoT Hub umożliwia również zarządzanie grupami odbiorców we wbudowanym punkcie końcowym odbierania od urządzenia do chmury. Dla każdego centrum IoT Hub może być maksymalnie 20 grup odbiorców.

Jeśli używasz [routingu wiadomości](iot-hub-devguide-messages-d2c.md) i [trasa rezerwowa](iot-hub-devguide-messages-d2c.md#fallback-route) jest włączona, wszystkie wiadomości, które nie pasują do kwerendy w dowolnej trasie, przechodzą do wbudowanego punktu końcowego. Jeśli wyłączysz tę trasę rezerwową, wiadomości, które nie pasują do żadnej kwerendy, zostaną usunięte.

Czas przechowywania można modyfikować programowo przy użyciu [interfejsów API REST DOSTAWCY zasobów usługi IoT Hub](/rest/api/iothub/iothubresource)lub za pomocą [portalu Azure.](https://portal.azure.com)

Usługa IoT Hub udostępnia **wiadomości/zdarzenia** wbudowany punkt końcowy dla usług zaplecza do odczytu komunikatów urządzenia do chmury odebranych przez centrum. Ten punkt końcowy jest zgodny z Centrum zdarzeń, który umożliwia używanie dowolnych mechanizmów obsługiwanych przez usługę Event Hubs do odczytywania komunikatów.

## <a name="read-from-the-built-in-endpoint"></a>Odczyt z wbudowanego punktu końcowego

Niektóre integracje produktów i zestawów SDK centrów zdarzeń są świadome usługi IoT Hub i umożliwiają łączenie się z wbudowanym punktem końcowym za pomocą ciągu połączenia usługi usługi IoT hub.

Korzystając z zestawów SDK usługi Event Hubs lub integracji produktów, które nie są świadome usługi IoT Hub, potrzebujesz punktu końcowego zgodnego z Centrum zdarzeń i nazwy zgodnej z Centrum zdarzeń. Wartości te można pobrać z portalu w następujący sposób:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

2. Kliknij **pozycję Wbudowane punkty końcowe**.

3. Sekcja **Zdarzenia** zawiera następujące wartości: **Partycje,** **Nazwa zgodna z Centrum zdarzeń,** **Punkt końcowy zgodny z Centrum zdarzeń,** **Czas przechowywania**i **Grupy odbiorców**.

    ![Ustawienia urządzenia do chmury](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

W portalu pole punktu końcowego zgodnego z Centrum zdarzeń zawiera pełny ciąg połączenia usługi Event Hubs, który wygląda następująco: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/; SharedAccessKeyName=iothubowner; SharedAccessKey=keykeykey=; EntityPath=iothub-ehub-abcd-1234-123456**. Jeśli używany sdk wymaga innych wartości, będą one następujące:

| Nazwa | Wartość |
| ---- | ----- |
| Endpoint | sb://abcd1234namespace.servicebus.windows.net/ |
| Nazwa hosta | abcd1234namespace.servicebus.windows.net |
| Przestrzeń nazw | abcd1234namespace |

Następnie można użyć dowolnej zasady dostępu współdzielonego, która ma uprawnienia **ServiceConnect,** aby połączyć się z określonym Centrum zdarzeń.

Pakiety SDK, których można użyć do nawiązania połączenia z wbudowanym punktem końcowym zgodnym z Centrum zdarzeń, który udostępnia centrum IoT Hub:

| Język | SDK | Przykład | Uwagi |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Szybki start](quickstart-send-telemetry-dotnet.md) | Używa informacji zgodnych z centrum zdarzeń |
 Java | https://github.com/Azure/azure-event-hubs-java | [Szybki start](quickstart-send-telemetry-java.md) | Używa informacji zgodnych z centrum zdarzeń |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Szybki start](quickstart-send-telemetry-node.md) | Używa ciągu połączenia Usługi IoT Hub |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Używa ciągu połączenia Usługi IoT Hub |

Integracje produktów, których można używać z wbudowanym punktem końcowym zgodnym z centrum zdarzeń, który udostępnia centrum IoT Hub:

* [Usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Zobacz [Przetwarzanie danych z usługi IoT Hub za pomocą usługi Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Usługa Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Zobacz [Przesyłanie strumieniowe danych jako danych wejściowych do usługi Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Wgląd w szeregi czasowe](https://docs.microsoft.com/azure/time-series-insights/). Zobacz [Dodawanie źródła zdarzeń centrum IoT do środowiska usługi Time Series Insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Apache Burza wylewka](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Źródło [dziobka](https://github.com/apache/storm/tree/master/external/storm-eventhubs) można wyświetlić w usłudze GitHub.
* [Integracja z Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Usługa Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat punktów końcowych usługi IoT Hub, zobacz [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md).

* [Przewodniki Szybki start](quickstart-send-telemetry-node.md) pokazują, jak wysyłać wiadomości z urządzenia do chmury z symulowanych urządzeń i odczytywać wiadomości z wbudowanego punktu końcowego. 

Aby uzyskać więcej informacji, zobacz [process IoT Hub device-to-cloud messages using routes](tutorial-routing.md) tutorial.

* Jeśli chcesz kierować wiadomości z urządzenia do chmury do niestandardowych punktów końcowych, zobacz [Używanie tras komunikatów i niestandardowych punktów końcowych dla wiadomości z urządzenia do chmury](iot-hub-devguide-messages-read-custom.md).

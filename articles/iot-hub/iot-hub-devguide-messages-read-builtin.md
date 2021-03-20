---
title: Informacje o wbudowanym punkcie końcowym usługi Azure IoT Hub | Microsoft Docs
description: Przewodnik dla deweloperów — opis sposobu używania wbudowanego punktu końcowego zgodnego z centrum zdarzeń do odczytywania komunikatów z urządzenia do chmury.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom:
- amqp
- 'Role: Cloud Development'
ms.openlocfilehash: 4bb33721625f4fc752745ce2b43051c90b3aaa74
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147677"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Odczytywanie komunikatów przesyłanych z urządzeń do chmury z wbudowanego punktu końcowego

Domyślnie komunikaty są kierowane do wbudowanego punktu końcowego opartego na usłudze (**komunikaty/zdarzenia**), który jest zgodny z [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Ten punkt końcowy jest obecnie ujawniany tylko przy użyciu protokołu [AMQP](https://www.amqp.org/) na porcie 5671. Centrum IoT udostępnia następujące właściwości, które umożliwiają kontrolowanie wbudowanych **komunikatów i zdarzeń** punktu końcowego komunikacji zgodnego z centrum zdarzeń.

| Właściwość            | Opis |
| ------------------- | ----------- |
| **Liczba partycji** | Ustaw tę właściwość przy tworzeniu, aby zdefiniować liczbę [partycji](../event-hubs/event-hubs-features.md#partitions) na potrzeby pozyskiwania zdarzeń między urządzeniami a chmurą. |
| **Czas przechowywania**  | Ta właściwość określa, jak długo komunikaty w dniach są zachowywane przez IoT Hub. Wartość domyślna to jeden dzień, ale można ją zwiększyć do siedmiu dni. |

IoT Hub umożliwia przechowywanie danych w Event Hubs wbudowanym przez maksymalnie 7 dni. Czas przechowywania można ustawić podczas tworzenia IoT Hub. Czas przechowywania danych w IoT Hub zależy od warstwy i typu jednostki Centrum IoT. W obszarze Rozmiar wbudowane Event Hubs mogą zachować komunikaty o maksymalnym rozmiarze wiadomości do co najmniej 24 godzin przydziału. Na przykład w przypadku 1 IoT Hub jednostek S1 zapewnia wystarczającą ilość miejsca w magazynie, aby zachować co najmniej 400 000 komunikaty o rozmiarze 4 KB. Jeśli urządzenia wysyłają mniejsze wiadomości, mogą one być przechowywane dłużej (do 7 dni) w zależności od ilości miejsca do magazynowania. Gwarantujemy zachowanie danych dla określonego czasu przechowywania jako minimum. Komunikaty wygasną i nie będą dostępne po upływie czasu przechowywania. 

IoT Hub umożliwia również zarządzanie grupami odbiorców na wbudowanym punkcie końcowym odbioru z urządzenia do chmury. Każdy IoT Hub może mieć maksymalnie 20 grup odbiorców.

Jeśli używasz [routingu wiadomości](iot-hub-devguide-messages-d2c.md) , a [trasa rezerwowa](iot-hub-devguide-messages-d2c.md#fallback-route) jest włączona, wszystkie komunikaty, które nie pasują do zapytania w żadnej trasie, przejdą do wbudowanego punktu końcowego. Jeśli wyłączysz tę trasę alternatywną, komunikaty, które nie pasują do żadnego zapytania, zostaną usunięte.

Czas przechowywania można modyfikować programowo przy użyciu [interfejsów API REST dostawcy zasobów IoT Hub](/rest/api/iothub/iothubresource)lub z [Azure Portal](https://portal.azure.com).

IoT Hub uwidacznia wbudowane punkty końcowe **komunikatów i zdarzeń** dla usług zaplecza w celu odczytywania komunikatów z urządzenia do chmury odbieranych przez centrum. Ten punkt końcowy jest zgodny z centrum zdarzeń, co umożliwia korzystanie z dowolnego z mechanizmów obsługiwanych przez usługę Event Hubs na potrzeby odczytywania wiadomości.

## <a name="read-from-the-built-in-endpoint"></a>Odczytaj z wbudowanego punktu końcowego

Niektóre integracje produktów i zestawy SDK Event Hubs są świadome IoT Hub i umożliwiają używanie parametrów połączenia usługi IoT Hub do łączenia się z wbudowanym punktem końcowym.

W przypadku korzystania z Event Hubs zestawów SDK lub integracji produktów, które nie znają IoT Hub, wymagany jest punkt końcowy zgodny z centrum zdarzeń i nazwa zgodna z centrum zdarzeń. Te wartości można pobrać z portalu w następujący sposób:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

2. Kliknij pozycję **wbudowane punkty końcowe**.

3. Sekcja **Events** zawiera następujące wartości: **partycje**, **nazwa zgodna z centrum** zdarzeń, **punkt końcowy zgodny z centrum zdarzeń**, **czas przechowywania** i **grupy konsumentów**.

    ![Ustawienia urządzenia do chmury](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

W portalu pole punkt końcowy zgodny z centrum zdarzeń zawiera pełne parametry połączenia Event Hubs, które wyglądają następująco: **Endpoint = SB://abcd1234namespace.ServiceBus.Windows.NET/; SharedAccessKeyName = iothubowner; SharedAccessKey = keykeykeykeykeykey =; EntityPath = iothub-ehub-abcd-1234-123456**. Jeśli używany zestaw SDK wymaga innych wartości, będą one:

| Nazwa | Wartość |
| ---- | ----- |
| Punkt końcowy | sb://abcd1234namespace.servicebus.windows.net/ |
| Hostname (Nazwa hosta) | abcd1234namespace.servicebus.windows.net |
| Przestrzeń nazw | abcd1234namespace |

Następnie możesz wybrać wszystkie zasady dostępu współdzielonego z listy rozwijanej, jak pokazano na poniższym zrzucie ekranu. Pokazuje tylko zasady, które mają uprawnienia **serviceconnect** do nawiązywania połączenia z określonym centrum zdarzeń.

Zestawy SDK, których można użyć do nawiązania połączenia z wbudowanym punktem końcowym zgodnym z centrum zdarzeń, który IoT Hub uwidacznia:

| Język | SDK | Przykład |
| -------- | --- | ------ |
| .NET | https://www.nuget.org/packages/Azure.Messaging.EventHubs | [Szybki start](quickstart-send-telemetry-dotnet.md) |
| Java | https://mvnrepository.com/artifact/com.azure/azure-messaging-eventhubs | [Szybki start](quickstart-send-telemetry-java.md) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [Szybki start](quickstart-send-telemetry-node.md) |
| Python | https://pypi.org/project/azure-eventhub/ | [Szybki start](quickstart-send-telemetry-python.md) |

Integracji produktów, których można używać z wbudowanym punktem końcowym zgodnym z centrum zdarzeń, który IoT Hub uwidacznia:

* [Azure Functions](../azure-functions/index.yml). Zobacz [przetwarzanie danych z IoT Hub z Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](../stream-analytics/index.yml). Zobacz [dane przesyłane strumieniowo jako dane wejściowe do Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](../time-series-insights/index.yml). Zobacz [Dodawanie źródła zdarzeń centrum IoT Hub do środowiska Time Series Insightsowego](../time-series-insights/how-to-ingest-data-iot-hub.md).
* [Apache Storm elementu Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). [Źródło elementu Spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) można wyświetlić w witrynie GitHub.
* [Apache Spark integrację](../hdinsight/spark/apache-spark-ipython-notebook-machine-learning.md).
* [Azure Databricks](/azure/azure-databricks/).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o IoT Hub punktach końcowych, zobacz [IoT Hub punktów końcowych](iot-hub-devguide-endpoints.md).

* [Przewodniki Szybki Start](quickstart-send-telemetry-node.md) przedstawiają sposób wysyłania komunikatów z urządzenia do chmury z symulowanych urządzeń i odczytywania komunikatów z wbudowanego punktu końcowego. 

Aby uzyskać więcej szczegółów, zobacz temat [proces IoT Hub komunikatów z urządzenia do chmury przy użyciu usługi Routes](tutorial-routing.md) .

* Jeśli chcesz skierować komunikaty z urządzenia do chmury do niestandardowych punktów końcowych, zobacz [Używanie tras komunikatów i niestandardowych punktów końcowych w przypadku komunikatów z urządzenia do chmury](iot-hub-devguide-messages-read-custom.md).
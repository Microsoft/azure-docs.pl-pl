---
title: Azure Service Bus z platformą .NET i AMQP 1,0 | Microsoft Docs
description: W tym artykule opisano sposób korzystania z Azure Service Bus z aplikacji .NET przy użyciu protokołu AMQP (Advanced Messaging kolejkowania komunikatów).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7a67ab74efc700e16f5b1689e9cc1f459ecf14bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88067107"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Używanie Service Bus z platformy .NET z AMQP 1,0

Obsługa AMQP 1,0 jest dostępna w pakiecie Service Bus w wersji 2,1 lub nowszej. Możesz upewnić się, że masz najnowszą wersję, pobierając Service Bus BITS z narzędzia [NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>Konfigurowanie aplikacji platformy .NET do korzystania z AMQP 1,0

Domyślnie Biblioteka kliencka programu Service Bus .NET komunikuje się z usługą Service Bus przy użyciu protokołu AMQP. Możesz również jawnie określić AMQP jako typ transportu, jak pokazano w poniższej sekcji. 

W bieżącej wersji istnieje kilka funkcji interfejsu API, które nie są obsługiwane w przypadku korzystania z programu AMQP. Te Nieobsługiwane funkcje są wymienione w sekcji [różnice w zachowaniu](#behavioral-differences). Niektóre zaawansowane ustawienia konfiguracji mają również inne znaczenie w przypadku korzystania z AMQP.

### <a name="configuration-using-appconfig"></a>Konfiguracja przy użyciu App.config

Dobrym sposobem, aby aplikacje używały pliku konfiguracji App.config do przechowywania ustawień. W przypadku aplikacji Service Bus można użyć App.config do przechowywania parametrów połączenia Service Bus. Przykładowy plik App.config jest następujący:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

Wartość `Microsoft.ServiceBus.ConnectionString` Ustawienia to Service Bus parametry połączenia, które są używane do konfigurowania połączenia do Service Bus. Format jest następujący:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Gdzie `namespace` i `SAS key` są uzyskiwane z [Azure Portal][Azure portal] podczas tworzenia przestrzeni nazw Service Bus. Aby uzyskać więcej informacji, zobacz [Tworzenie przestrzeni nazw Service Bus przy użyciu Azure Portal][Create a Service Bus namespace using the Azure portal].

W przypadku korzystania z AMQP Dołącz parametry połączenia z `;TransportType=Amqp` . Ta notacja instruuje bibliotekę kliencką, aby nawiązać połączenie z Service Bus przy użyciu AMQP 1,0.

## <a name="message-serialization"></a>Serializacja komunikatów

W przypadku korzystania z domyślnego protokołu domyślne zachowanie serializacji biblioteki klienta .NET polega na użyciu typu [DataContractSerializer][DataContractSerializer] do serializacji wystąpienia [BrokeredMessage][BrokeredMessage] na potrzeby transportu między biblioteką klienta a usługą Service Bus. W przypadku korzystania z trybu transportu AMQP Biblioteka klienta używa systemu typu AMQP do serializacji komunikatu obsługiwanego przez [brokera][BrokeredMessage] w wiadomości AMQP. Ta Serializacja umożliwia odbieranie i Interpretowanie komunikatów przez aplikację, która może działać na innej platformie, na przykład w przypadku aplikacji Java, która korzysta z interfejsu API JMS w celu uzyskania dostępu do Service Bus.

Podczas konstruowania wystąpienia [BrokeredMessage][BrokeredMessage] można dostarczyć obiekt .NET jako parametr do konstruktora, który służy jako treść wiadomości. W przypadku obiektów, które mogą być mapowane na typy pierwotne AMQP, treść jest serializowana do typów danych AMQP. Jeśli obiektu nie można bezpośrednio mapować do typu pierwotnego AMQP; oznacza to, że typ niestandardowy zdefiniowany przez aplikację, obiekt jest serializowany przy użyciu elementu [DataContractSerializer][DataContractSerializer], a serializowane bajty są wysyłane w AMQP komunikat danych.

Aby ułatwić współdziałanie z klientami non-.NET, należy używać tylko typów .NET, które mogą być serializowane bezpośrednio do typów AMQP w treści wiadomości. Poniższa tabela zawiera szczegółowe informacje o tych typach i odpowiednim mapowaniu dla systemu typów AMQP.

| Typ obiektu treści .NET | Zamapowany typ AMQP | Typ sekcji treści AMQP |
| --- | --- | --- |
| bool |boolean |AMQP wartość |
| byte |ubyte |AMQP wartość |
| ushort |ushort |AMQP wartość |
| uint |uint |AMQP wartość |
| ulong |ulong |AMQP wartość |
| sbyte |byte |AMQP wartość |
| short |short |AMQP wartość |
| int |int |AMQP wartość |
| długi |długi |AMQP wartość |
| float |float |AMQP wartość |
| double |double |AMQP wartość |
| decimal |decimal128 |AMQP wartość |
| char |char |AMQP wartość |
| DateTime |sygnatura czasowa |AMQP wartość |
| Guid (identyfikator GUID) |uuid |AMQP wartość |
| Byte [] |binarny |AMQP wartość |
| ciąg |ciąg |AMQP wartość |
| System. Collections. IList |list |Wartość AMQP: elementy zawarte w kolekcji mogą być tylko tymi, które są zdefiniowane w tej tabeli. |
| System. Array |array |Wartość AMQP: elementy zawarte w kolekcji mogą być tylko tymi, które są zdefiniowane w tej tabeli. |
| System. Collections. IDictionary |map (mapa) |Wartość AMQP: elementy zawarte w kolekcji mogą być tylko tymi, które są zdefiniowane w tej tabeli. Uwaga: obsługiwane są tylko klucze ciągów. |
| Adresu |Opisany ciąg (patrz Poniższa tabela) |AMQP wartość |
| DateTimeOffset |Opisany czas (patrz Poniższa tabela) |AMQP wartość |
| przedział_czasu |Opisany czas (patrz poniżej) |AMQP wartość |
| Strumień |binarny |AMQP dane (może być wielokrotne). Sekcje danych zawierają nieprzetworzone Bajty odczytane z obiektu Stream. |
| Inny obiekt |binarny |AMQP dane (może być wielokrotne). Zawiera serializowaną wartość binarną obiektu, który używa elementu DataContractSerializer lub serializatora dostarczonego przez aplikację. |

| Typ .NET | Zamapowane AMQP typu | Uwagi |
| --- | --- | --- |
| Adresu |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Identyfikator URI. AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset. UtcTicks |
| przedział_czasu |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan. Ticks |

## <a name="behavioral-differences"></a>Różnice w zachowaniu

Istnieje kilka małych różnic w zachowaniu Service Bus interfejsu API .NET w przypadku używania AMQP w porównaniu z domyślnym protokołem:

* Właściwość [OperationTimeout][OperationTimeout] jest ignorowana.
* `MessageReceiver.Receive(TimeSpan.Zero)` jest zaimplementowany jako `MessageReceiver.Receive(TimeSpan.FromSeconds(10))` .
* Wykonywanie komunikatów według tokenów blokad może odbywać się tylko przez odbiorniki komunikatów, którzy początkowo otrzymali komunikaty.

## <a name="control-amqp-protocol-settings"></a>Ustawienia protokołu kontroli AMQP

[Interfejsy API platformy .NET](/dotnet/api/) uwidaczniają kilka ustawień w celu sterowania zachowaniem protokołu AMQP:

* **[MessageReceiver. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: steruje początkowym środkiem zastosowanym do łącza. Wartość domyślna to 0.
* **[MessagingFactorySettings. AmqpTransportSettings. MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: określa maksymalny rozmiar ramki AMQP, który jest oferowany podczas negocjacji w czasie otwarcia połączenia. Wartość domyślna to 65 536 bajtów.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: Jeśli transfery są przetwarzane wsadowo, ta wartość określa maksymalne opóźnienie wysyłania pozycji. Dziedziczone przez nadawców/odbiorników domyślnie. Indywidualni nadawcy/odbiornik mogą zastąpić wartość domyślną, czyli 20 milisekund.
* **[MessagingFactorySettings. AmqpTransportSettings. UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: kontroluje, czy połączenia AMQP są nawiązywane za pośrednictwem połączenia TLS. Wartość domyślna to **true**.

## <a name="next-steps"></a>Następne kroki

Chcesz dowiedzieć się więcej? Odwiedź następujące linki:

* [Service Bus AMQP — Omówienie]
* [Przewodnik dotyczący protokołu AMQP 1.0]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: /dotnet/api/system.runtime.serialization.datacontractserializer?view=netcore-3.1
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Service Bus AMQP — Omówienie]: service-bus-amqp-overview.md
[Przewodnik dotyczący protokołu AMQP 1.0]: service-bus-amqp-protocol-guide.md
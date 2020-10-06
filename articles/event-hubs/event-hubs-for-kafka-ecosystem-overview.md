---
title: Korzystanie z centrum zdarzeń w programie Apache Kafka App — Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera informacje na temat obsługi Apache Kafka przez usługę Azure Event Hubs.
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: 5c49f8f87d8d399cda33a332f7464ed340ae3a0f
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761502"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Korzystanie z Event Hubs platformy Azure z aplikacji Apache Kafka
Event Hubs udostępnia punkt końcowy zgodny z interfejsami API Apache Kafka® producenta i konsumenta, które mogą być używane przez większość istniejących aplikacji klienckich Apache Kafka jako alternatywę dla uruchamiania własnego klastra Apache Kafka. Event Hubs obsługuje klientów Apache Kafka z klientami i klienta interfejsu API w wersji 1,0 lub nowszej.

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Co zapewnia Event Hubs Kafka?

Event Hubs dla Apache Kafka funkcja udostępnia główny protokół w oparciu o Event Hubs platformy Azure, który jest zgodny z klientami Apache Kafka utworzonymi dla Apache Kafka Server w wersji 1,0 i nowszych, i obsługuje zarówno odczytywanie z i zapisywanie do Event Hubs, które są równoważne z Apache Kafka tematami. 

Możesz często korzystać z punktu końcowego Event Hubs Kafka z aplikacji bez zmian w kodzie w porównaniu z istniejącą konfiguracją Kafka i modyfikować konfigurację: zaktualizuj parametry połączenia w konfiguracjach, aby wskazywały punkt końcowy Kafka uwidoczniony przez centrum zdarzeń zamiast wskazywać na klaster Kafka. Następnie można rozpocząć przesyłanie strumieniowe zdarzeń z aplikacji korzystających z protokołu Kafka do Event Hubs. 

Koncepcyjnie, Kafka i Event Hubs są bardzo podobne: są to dzienniki partycjonowane dla danych przesyłanych strumieniowo, dzięki czemu klient kontroluje, która część przechowywanego dziennika ma być odczytana. W poniższej tabeli przedstawiono koncepcje między Kafka i Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapowanie koncepcyjne Kafka i centrum zdarzeń

| Koncepcja Kafka | Koncepcja Event Hubs|
| --- | --- |
| Klaster | Przestrzeń nazw |
| Temat | Centrum zdarzeń |
| Partycja | Partycja|
| Grupa konsumentów | Grupa konsumentów |
| Przesunięcie | Przesunięcie|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Kluczowe różnice między Apache Kafka i Event Hubs

Gdy [Apache Kafka](https://kafka.apache.org/) to oprogramowanie, które zwykle trzeba zainstalować i obsługiwać, Event Hubs to w pełni zarządzana usługa w chmurze. Nie ma serwerów, dysków ani sieci do zarządzania i monitorowania, a także braku brokerów do uwzględnienia lub konfiguracji. Tworzysz przestrzeń nazw, która jest punktem końcowym z w pełni kwalifikowaną nazwą domeny, a następnie tworzysz Event Hubs (tematów) w tej przestrzeni nazw. 

Aby uzyskać więcej informacji na temat Event Hubs i przestrzeni nazw, zobacz [Event Hubs Features](event-hubs-features.md#namespace). Jako usługa w chmurze Event Hubs używa jednego stabilnego wirtualnego adresu IP jako punktu końcowego, dlatego klienci nie muszą znać brokerów ani maszyn w klastrze. Mimo że Event Hubs implementuje ten sam protokół, różnica polega na tym, że cały ruch Kafka dla wszystkich partycji jest przewidywalnie kierowany przez ten punkt końcowy, a nie wymaga dostępu do zapory dla wszystkich brokerów klastra.   

Skalowanie w Event Hubs jest kontrolowane przez liczbę jednostek przepływności, które można kupić, a każda jednostka przepływności entitling od 1 megabajtów na sekundę lub 1000 zdarzeń na sekundę i dwa razy w ruchu wychodzącym. Event Hubs może automatycznie skalować jednostki przepływności po osiągnięciu limitu przepływności w przypadku korzystania z funkcji [automatycznego](event-hubs-auto-inflate.md) rozbudowy. Ta funkcja działa również z obsługą protokołu Apache Kafka.  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Czy Apache Kafka odpowiednie rozwiązanie dla obciążenia?

W przypadku tworzenia aplikacji przy użyciu Apache Kafka warto również zrozumieć, że usługa Azure Event Hubs jest częścią floty usług, które obejmują również [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)i [Azure Event Grid](../event-grid/overview.md). 

Niektórzy dostawcy dystrybucji komercyjnej Apache Kafka mogą zasugerować, że Apache Kafka jest jednym z nich dla wszystkich potrzeb platformy obsługi komunikatów, rzeczywistości jest to, że Apache Kafka nie implementuje, na przykład, wzorca kolejki [konkurencyjnych odbiorców](/azure/architecture/patterns/competing-consumers) , program nie obsługuje  [publikowania/subskrybowania](/azure/architecture/patterns/publisher-subscriber) na poziomie, który umożliwia subskrybentom dostęp do komunikatów przychodzących na podstawie zasad ocenionych na serwerze, innych niż zwykłe przesunięcia, i nie ma żadnych udogodnień do śledzenia cyklu życia zadania zainicjowanego przez komunikat lub sidelining uszkodzonych komunikatów do kolejki utraconych wiadomości, a wszystkie są podstawą wielu scenariuszy obsługi komunikatów w przedsiębiorstwie.

Aby zrozumieć różnice między wzorcami a wzorcem najlepiej pokrytym przez daną usługę, przejrzyj [Opcje asynchronicznej obsługi komunikatów w](/azure/architecture/guide/technology-choices/messaging) temacie Wskazówki dotyczące platformy Azure. Jako użytkownik Apache Kafka może się okazać, że ścieżki komunikacji, które zostały dotąd zrealizowane z Kafka, mogą być zrealizowane z znacznie mniejszą złożonością i jeszcze bardziej wydajnymi możliwościami przy użyciu Event Grid lub Service Bus. 

Jeśli potrzebujesz określonych funkcji Apache Kafka, które nie są dostępne za pomocą Event Hubs interfejsu Apache Kafka lub jeśli wzorzec implementacji przekracza [przydziały Event Hubs](event-hubs-quotas.md), możesz również uruchomić [natywny Klaster Apache Kafka w usłudze Azure HDInsight](../hdinsight/kafka/apache-kafka-introduction.md).  

## <a name="security-and-authentication"></a>Zabezpieczenia i uwierzytelnianie
Za każdym razem, gdy publikujesz lub zużywasz zdarzenia z Event Hubs Kafka, klient próbuje uzyskać dostęp do zasobów Event Hubs. Chcesz się upewnić, że zasoby są dostępne za pomocą autoryzowanej jednostki. W przypadku korzystania z protokołu Apache Kafka z klientami można ustawić konfigurację uwierzytelniania i szyfrowania przy użyciu mechanizmów SASL. W przypadku korzystania z Event Hubs dla Kafka wymagane jest szyfrowanie TLS (ponieważ wszystkie dane przesyłane za pomocą Event Hubs są szyfrowane przy użyciu protokołu TLS). Można to zrobić, określając opcję SASL_SSL w pliku konfiguracji. 

Usługa Azure Event Hubs udostępnia wiele opcji autoryzacji dostępu do bezpiecznych zasobów. 

- OAuth 2.0
- Sygnatura dostępu współdzielonego (SAS)

#### <a name="oauth-20"></a>OAuth 2.0
Event Hubs integruje się z usługą Azure Active Directory (Azure AD), która zapewnia scentralizowany serwer autoryzacji zgodny z protokołem **OAuth 2,0** . W usłudze Azure AD można używać kontroli dostępu opartej na rolach (RBAC) do udzielania szczegółowych uprawnień do tożsamości klientów. Tej funkcji można używać z klientami Kafka przez określenie **SASL_SSL** protokołu i  **OAUTHBEARER** dla mechanizmu. Aby uzyskać szczegółowe informacje o rolach i poziomach platformy Azure w celu uzyskania dostępu do zakresu, zobacz [Autoryzuj dostęp za pomocą usługi Azure AD](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Sygnatura dostępu współdzielonego
Event Hubs zapewnia także **sygnatury dostępu współdzielonego (SAS)** dla delegowanego dostępu do Event Hubs dla zasobów Kafka. Autoryzowanie dostępu przy użyciu mechanizmu uwierzytelniania OAuth 2,0 zapewnia doskonałe zabezpieczenia i łatwość użycia w porównaniu z SAS. Wbudowane role mogą również wyeliminować potrzebę autoryzacji opartej na listach ACL, która musi być utrzymywana i zarządzana przez użytkownika. Tej funkcji można używać z klientami Kafka przez określenie **SASL_SSL** dla protokołu i **zwykłego** dla mechanizmu. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!NOTE]
> W przypadku korzystania z uwierzytelniania SAS z klientami Kafka ustanowione połączenia nie są rozłączane po ponownym wygenerowaniu klucza SAS. 

#### <a name="samples"></a>Samples 
**Samouczek** zawierający instrukcje krok po kroku dotyczące tworzenia centrum zdarzeń i uzyskiwania dostępu do niego przy użyciu SAS lub OAuth zawiera temat [Szybki Start: przesyłanie strumieniowe danych za pomocą Event Hubs przy użyciu protokołu Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Aby uzyskać więcej **przykładów** , które pokazują, jak używać protokołu OAuth z Event Hubs for Kafka, zobacz [przykłady w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features"></a>Inne funkcje Event Hubs 

Event Hubs dla Apache Kafka funkcja jest jednym z trzech protokołów dostępnych jednocześnie na platformie Azure Event Hubs, uzupełnienie protokołu HTTP i AMQP. Możesz pisać przy użyciu dowolnego z tych protokołów i odczytywać inne, dzięki czemu obecnie Apache Kafka producenci mogą kontynuować publikowanie za pośrednictwem Apache Kafka, ale czytelnik może korzystać z natywnej integracji Event Hubs z interfejsem AMQP, takim jak Azure Stream Analytics lub Azure Functions. Odwrotnie, można łatwo zintegrować platformę Azure Event Hubs w sieci routingu AMQP jako docelowy punkt końcowy, a następnie odczytywać dane za pomocą Apache Kafka integracji.  

Ponadto Event Hubs funkcje, takie jak [przechwytywanie](event-hubs-capture-overview.md), które umożliwiają niezwykle kosztowne archiwizowanie długoterminowe za pośrednictwem Blob Storage i Azure Data Lake Storage platformy Azure, [a także z](event-hubs-geo-dr.md) funkcją Event Hubs dla Kafka.

## <a name="apache-kafka-feature-differences"></a>Różnice funkcji Apache Kafka 

Celem Event Hubs dla Apache Kafka jest zapewnienie dostępu do funkcji centrum zdarzeń platformy Azure dla aplikacji, które są zablokowane w interfejsie API Apache Kafka i w przeciwnym razie będzie musiała być wykonywana przez klaster Apache Kafka. 

Zgodnie z [powyższym](#is-apache-kafka-the-right-solution-for-your-workload)opisem, flota usługi Azure Messaging zapewnia rozbudowane i niezawodne pokrycie wielu scenariuszy obsługi komunikatów, a chociaż następujące funkcje nie są obecnie obsługiwane przez obsługę Event HUBSGO interfejsu API Apache Kafka, wskazujemy, gdzie i jak jest dostępna żądana funkcja.

### <a name="transactions"></a>Transakcje

[Azure Service Bus](../service-bus-messaging/service-bus-transactions.md) oferuje niezawodne obsługę transakcji, która umożliwia otrzymywanie i rozwiązywanie komunikatów i sesji podczas wysyłania komunikatów wychodzących wynikających z przetwarzania komunikatów do wielu jednostek docelowych w ramach ochrony spójności transakcji. Zestaw funkcji nie tylko umożliwia jednokrotne przetwarzanie poszczególnych komunikatów w sekwencji, ale również pozwala uniknąć ryzyka, że inny użytkownik przypadkowo przetworzy te same komunikaty, tak jak w przypadku Apache Kafka. Service Bus to zalecana usługa dla obciążeń komunikatów transakcyjnych.

### <a name="compression"></a>Kompresja

Funkcja [kompresji](https://cwiki.apache.org/confluence/display/KAFKA/Compression) po stronie klienta programu Apache Kafka kompresuje partię wielu wiadomości do jednej wiadomości po stronie producenta i dekompresuje partię po stronie klienta. Broker Apache Kafka traktuje partię jako wiadomość specjalną.

Ta funkcja jest ogólnie dostępna w szanse z modelem wieloprotokołowym platformy Event Hubs Azure, który umożliwia wysyłanie komunikatów, nawet tych, które są wysyłane w partiach, aby można je było osobno pobrać z brokera i za pośrednictwem dowolnego protokołu. 

Ładunek dowolnego zdarzenia centrum zdarzeń to strumień bajtów, a zawartość może być skompresowana przy użyciu wybieranego przez siebie algorytmu. Format kodowania Apache Avro obsługuje kompresję natywną.

### <a name="log-compaction"></a>Kompaktowanie dziennika

Kompaktowanie dzienników Apache Kafka to funkcja, która umożliwia wykluczenie wszystkich, ale ostatniego rekordu każdego klucza z partycji, co skutecznie włącza temat Apache Kafka w magazynie klucz-wartość, gdzie Ostatnia dodana wartość zastępuje poprzednią. Wzorzec magazynu klucz-wartość, nawet z częstymi aktualizacjami, jest znacznie lepiej obsługiwany przez usługi bazy danych, takie jak [Azure Cosmos DB](../cosmos-db/introduction.md).

Funkcja kompaktowania dzienników jest używana przez struktury klienta Kafka Connect i Kafka.

### <a name="kafka-streams"></a>Kafka strumienie

Strumienie Kafka to Biblioteka klienta usługi Stream Analytics, która jest częścią Apache Kafka projektu typu open source, ale jest oddzielona od Apache Kafka brokera strumienia zdarzeń. 

Najczęstszym powodem, dla którego klienci korzystający z platformy Azure Event Hubs poprosiły o pomoc techniczną dotyczącą strumieni Kafka, jest to, że są one interesujące w produkcie. "ksqlDB" to własny udostępniony projekt źródłowy, który jest [licencjonowany w taki](https://github.com/confluentinc/ksql/blob/master/LICENSE) sposób, że żaden dostawca "oferujący oprogramowanie jako usługa", platforma jako usługa, infrastruktura jako usługa lub inne podobne usługi online, które konkurują z produktami lub usługami, jest uprawniony do korzystania z lub oferowania pomocy technicznej "ksqlDB". Praktycznie, jeśli używasz ksqlDB, musisz samodzielnie wykonać Kafka lub musisz użyć ofert w chmurze. Postanowienia licencyjne mogą również mieć wpływ na klientów platformy Azure, którzy oferują usługi do celów wykluczonych przez licencję.

Autonomiczne i bez ksqlDB strumienie Kafka mają mniejszą liczbę możliwości niż wiele alternatywnych platform i usług, z których większość ma wbudowane interfejsy SQL przesyłania strumieniowego, a wszystkie zintegrowane z platformą Azure Event Hubs Dzisiaj:

- [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)
- [Azure Synapse Analytics (za pośrednictwem funkcji przechwytywania Event Hubs)](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](https://docs.microsoft.com/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink](event-hubs-kafka-flink-tutorial.md)
- [Akka strumienie](event-hubs-kafka-akka-streams-tutorial.md)

Wymienione na liście usługi i platformy mogą ogólnie pobierać strumienie zdarzeń i dane referencyjne bezpośrednio z różnorodnych źródeł za pośrednictwem kart sieciowych. Strumienie Kafka mogą pobierać dane tylko z Apache Kafka, a projekty analizy są w związku z tym blokowane w Apache Kafka. Aby używać danych z innych źródeł, należy najpierw zaimportować dane do Apache Kafka za pomocą platformy Kafka Connect Framework.

Jeśli musisz używać platformy strumieni Kafka na platformie Azure, [Apache Kafka w usłudze HDInsight](../hdinsight/kafka/apache-kafka-introduction.md) udostępnimy tę opcję. Apache Kafka w usłudze HDInsight zapewnia pełną kontrolę nad wszystkimi aspektami konfiguracji Apache Kafka, a jednocześnie jest w pełni zintegrowana z różnymi aspektami platformy Azure, od umieszczenia domeny błędów/aktualizacji do izolacji sieci w celu monitorowania integracji. 

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono wprowadzenie do Event Hubs Kafka. Aby dowiedzieć się więcej, zobacz [Apache Kafka Przewodnik dla deweloperów dla Event Hubs platformy Azure](apache-kafka-developer-guide.md).

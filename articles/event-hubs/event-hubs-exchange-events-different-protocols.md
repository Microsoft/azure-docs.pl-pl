---
title: Azure Event Hubs — zdarzenia programu Exchange korzystające z różnych protokołów
description: W tym artykule pokazano, jak konsumenci i producenci korzystający z różnych protokołów (AMQP, Apache Kafka i HTTPS) mogą wymieniać zdarzenia podczas korzystania z usługi Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: cbc6999e3ede73b948ce034769966922b4b0f282
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89010324"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Wymieniaj zdarzenia między konsumentami a producentami, którzy korzystają z różnych protokołów: AMQP, Kafka i HTTPS
Usługa Azure Event Hubs obsługuje trzy protokoły dla klientów i producentów: AMQP, Kafka i HTTPS. Każdy z tych protokołów ma własny sposób reprezentowania komunikatów, więc w naturalny sposób wystąpią następujące pytania: Jeśli aplikacja wysyła zdarzenia do centrum zdarzeń z jednym protokołem i zużywa je za pomocą innego protokołu, co sprawia, że różne części i wartości zdarzenia wyglądają jak w przypadku nadejścia do konsumenta? W tym artykule omówiono najlepsze rozwiązania dla producentów i konsumentów, aby zapewnić, że wartości w ramach zdarzenia są poprawnie interpretowane przez aplikację, która zużywa.

Porady przedstawione w tym artykule dotyczą tych klientów, z wymienionymi wersjami użytymi podczas opracowywania fragmentów kodu:

* Klient Java Kafka (wersja 1.1.1 z https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure Event Hubs Client for Java (wersja 1.1.0 z https://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure Event Hubs Client for .NET (wersja 2.1.0 z https://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (wersja 5.0.0 z https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (obsługuje tylko producentów)

Inni klienci AMQP mogą działać nieco inaczej. AMQP ma dobrze zdefiniowany system typów, ale informacje o serializacji typów charakterystycznych dla języka do i z tego systemu są zależne od klienta, jak w przypadku, gdy klient zapewnia dostęp do części komunikatu AMQP.

## <a name="event-body"></a>Treść zdarzenia
Wszyscy klienci programu Microsoft AMQP reprezentują treść zdarzenia jako nieinterpretowany zbiór bajtów. Aplikacja do produkcji przekazuje sekwencję bajtów do klienta, a zużywana aplikacja odbiera tę samą sekwencję od klienta. Interpretacja sekwencji bajtów odbywa się w kodzie aplikacji.

W przypadku wysyłania zdarzenia za pośrednictwem protokołu HTTPS treść zdarzenia jest ogłoszoną zawartością, która również jest traktowana jako nieinterpretowane bajty. Można łatwo osiągnąć ten sam stan w Kafka producenta lub konsumenta przy użyciu dostarczonych ByteArraySerializer i ByteArrayDeserializer, jak pokazano w poniższym kodzie:

### <a name="kafka-byte-producer"></a>Kafka Byte [] producent

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka Byte [] — odbiorca
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Ten kod umożliwia utworzenie przezroczystego potoku bajtów między dwoma połówami aplikacji i umożliwienie deweloperowi aplikacji ręcznego serializacji i deserializacji w dowolny sposób, łącznie z wykonywaniem decyzji deserializacji w czasie wykonywania, na przykład na podstawie informacji o typie lub nadawcy we właściwościach ustawionych przez użytkownika dla zdarzenia.

Aplikacje mające pojedynczy, stały typ treści zdarzenia mogą być w stanie używać innych serializatorów Kafka oraz deserializacji do nieprzezroczystego konwertowania danych. Rozważmy na przykład aplikację, która używa formatu JSON. Konstrukcja i interpretacja ciągu JSON występuje na poziomie aplikacji. Na poziomie Event Hubs treść zdarzenia jest zawsze ciągiem, sekwencja bajtów reprezentująca znaki w kodowaniu UTF-8. W takim przypadku producent lub konsument Kafka może skorzystać z podanych StringSerializer lub StringDeserializer, jak pokazano w poniższym kodzie:

### <a name="kafka-utf-8-string-producer"></a>Kafka ciąg w formacie UTF-8
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka odbiorcy ciągu UTF-8
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Po stronie AMQP, zarówno Java, jak i .NET, zapewniają wbudowane metody konwersji ciągów do i z sekwencji bajtów UTF-8. Klienci programu Microsoft AMQP reprezentują zdarzenia jako klasę o nazwie EventData. W poniższych przykładach pokazano, jak serializować ciąg UTF-8 do treści zdarzenia EventData w producencie AMQP i jak deserializować treść zdarzenia EventData do ciągu UTF-8 na odbiorcy AMQP.

### <a name="java-amqp-utf-8-string-producer"></a>Producent ciągu UTF-8 języka Java AMQP
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Odbiorca ciągu UTF-8 języka Java AMQP
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>Producent ciągu języka C# .NET w formacie UTF-8
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>Odbiorca ciągu języka C# .NET-8
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Ponieważ Kafka jest typu open source, Deweloper aplikacji może sprawdzić implementację dowolnego serializatora lub deserializacji i zaimplementować kod, który tworzy lub zużywa zgodną sekwencję bajtów na stronie AMQP.

## <a name="event-user-properties"></a>Właściwości użytkownika zdarzenia

Właściwości zestawu użytkowników można ustawiać i pobierać z obu klientów AMQP (w przypadku klientów programu Microsoft AMQP, które są nazywane właściwościami) i Kafka (gdzie są nazywane nagłówkami). Nadawcy HTTPS mogą ustawiać właściwości użytkownika dla zdarzenia przez dostarczenie ich jako nagłówków HTTP w operacji POST. Jednak Kafka traktuje zarówno treść zdarzenia, jak i wartości nagłówków zdarzeń jako sekwencje bajtów. W przypadku klientów AMQP wartości właściwości mają typy, które są przekazywane przez kodowanie wartości właściwości zgodnie z systemem typu AMQP.

Protokół HTTPS jest szczególnym przypadkiem. W momencie wysyłania wszystkie wartości właściwości są tekstem UTF-8. Usługa Event Hubs wykonuje ograniczoną liczbę interpretacji w celu przekonwertowania odpowiednich wartości właściwości na 32-bitowe i 64-bitowe liczby całkowite ze znakiem, 64-bitowe liczby zmiennoprzecinkowe i wartości logiczne. Każda wartość właściwości, która nie pasuje do jednego z tych typów jest traktowana jako ciąg.

Mieszanie tych podejścia do wpisywania właściwości oznacza, że konsument Kafka widzi nieprzetworzoną sekwencję bajtów zakodowaną w AMQP, łącznie z informacjami o typach AMQP. Natomiast konsument AMQP widzi niewpisanej sekwencji bajtów wysłanej przez producenta Kafka, który aplikacja musi interpretować.

W przypadku konsumentów Kafka, którzy otrzymują właściwości od producentów AMQP lub HTTPS, użyj klasy AmqpDeserializer, która jest modelowana po innych deserializacji w ekosystemie Kafka. Interpretuje informacje o typie w sekwencji bajtów zakodowanych AMQP w celu deserializacji bajtów danych do typu Java.

Najlepszym rozwiązaniem jest dołączenie właściwości do komunikatów wysyłanych za pośrednictwem AMQP lub HTTPS. Konsument Kafka może użyć go do ustalenia, czy wartości nagłówka wymagają deserializacji AMQP. Wartość właściwości nie jest istotna. Tylko dobrze znana nazwa, którą konsument Kafka może znaleźć na liście nagłówków i odpowiednio dostosować jej zachowanie.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP do Kafka część 1: Tworzenie i wysyłanie zdarzenia w języku C# (.NET) z właściwościami
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP do Kafka część 2: Użyj AmqpDeserializer, aby deserializować te właściwości w odbiorcy Kafka
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Jeśli aplikacja zna oczekiwany typ dla właściwości, istnieją metody deserializacji, które nie wymagają rzutowania w późniejszym stanie, ale zgłaszają błąd, jeśli właściwość nie ma oczekiwanego typu.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP do Kafka część 3: inny sposób używania AmqpDeserializer w odbiorcy Kafka
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Przechodzenie między innymi, ponieważ nagłówki ustawiane przez producenta Kafka są zawsze postrzegane przez odbiorcę AMQP jako nieprzetworzone bajty (typ org. Apache. qpid. Proton. AMQP. Binary dla klienta Microsoft Azure Event Hubs dla środowiska Java lub `System.Byte[]` dla klientów .NET AMQP firmy Microsoft). Najprostszą ścieżką jest użycie jednego z serializatorów dostarczonych przez Kafka, aby wygenerować bajty dla wartości nagłówka na stronie producenta Kafka, a następnie napisać zgodny kod deserializacji na stronie konsumenta AMQP.

Podobnie jak w przypadku AMQP-to-Kafka, najlepszym rozwiązaniem jest uwzględnienie właściwości w komunikatach wysyłanych za pośrednictwem Kafka. Konsument AMQP może użyć właściwości, aby określić, czy wartości nagłówka wymagają deserializacji. Wartość właściwości nie jest istotna. Tylko dobrze znana nazwa, którą konsument AMQP może znaleźć na liście nagłówków i odpowiednio dostosować jej zachowanie. Jeśli nie można zmienić producenta Kafka, jest on również możliwy do sprawdzenia, czy wartość właściwości jest typu binarnego lub bajtowego, i próba deserializacji na podstawie typu.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka do AMQP część 1: Tworzenie i wysyłanie zdarzenia z Kafka z właściwościami
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka do AMQP część 2: ręczne deserializacja tych właściwości w języku C# (.NET)
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka do AMQP część 3: ręczne deserializacja tych właściwości w języku Java
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób przesyłania strumieniowego do Event Hubs bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Aby dowiedzieć się więcej na temat Event Hubs i Event Hubs dla Kafka, zobacz następujące artykuły:  

* [Dowiedz się więcej na temat usługi Event Hubs](./event-hubs-about.md)
* [Dowiedz się więcej na temat usługi Event Hubs dla platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Poznaj więcej przykładów w usłudze Event Hubs dla platformy Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)
* Użyj [Narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) , aby [przesyłać strumieniowo zdarzenia z Kafka lokalnego do Event Hubs w chmurze.](event-hubs-kafka-mirror-maker-tutorial.md)
* Dowiedz się, jak przesyłać strumieniowo do Event Hubs przy użyciu [natywnych aplikacji Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)oraz strumieni [Apache Flink](event-hubs-kafka-flink-tutorial.md)lub [Akka](event-hubs-kafka-akka-streams-tutorial.md)

---
title: Koncepcje rozmowy w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Zapoznaj się z tematami dotyczącymi rozmów dotyczących usług komunikacyjnych.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: f33d7efd1c136619767c3eadd93740442ae7239a
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762046"
---
# <a name="chat-concepts"></a>Pojęcia dotyczące czatu

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Biblioteki klienckie programu Chat usługi Azure Communication Services mogą służyć do dodawania rozmów tekstowych w czasie rzeczywistym do aplikacji. Ta strona zawiera podsumowanie najważniejszych koncepcji i możliwości rozmowy.

Zobacz [Omówienie biblioteki klienta rozmowy usług komunikacyjnych](./sdk-features.md) , aby dowiedzieć się więcej o określonych językach i możliwościach biblioteki klienta.

## <a name="chat-overview"></a>Omówienie rozmowy 

Konwersacje rozmowy odbywają się w wątkach rozmowy. Wątek rozmowy może zawierać wiele komunikatów i wielu użytkowników. Każdy komunikat należy do pojedynczego wątku, a użytkownik może być częścią jednego lub wielu wątków. 

Każdy użytkownik w wątku rozmowy jest nazywany członkiem. W wątku czatu mogą znajdować się maksymalnie 250 członków. Tylko członkowie wątku mogą wysyłać i odbierać komunikaty lub dodawać/usuwać członków w wątku rozmowy. Maksymalny dozwolony rozmiar komunikatu to około 28KB. Można pobrać wszystkie komunikaty w wątku rozmowy przy użyciu `List/Get Messages` operacji. Usługi komunikacyjne przechowują historię rozmowy do momentu wykonania operacji usuwania w wątku lub komunikacie rozmowy lub do momentu, w którym żaden element członkowski nie jest pozostały w wątku rozmowy, w którym jest oddzielony i przetworzony do usunięcia.   

W przypadku wątków rozmowy z więcej niż 20 elementami członkowskimi, odczyty i funkcje wskaźnika pisania są wyłączone. 

## <a name="chat-architecture"></a>Architektura rozmowy

Istnieją dwa podstawowe części dla architektury czatu: 1) usługa zaufana i 2) aplikacja kliencka.

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagram przedstawiający architekturę czatu usług komunikacyjnych.":::

 - **Usługa zaufana:** Aby prawidłowo zarządzać sesją czatu, potrzebna jest usługa, która pomaga połączyć się z usługami komunikacyjnymi przy użyciu parametrów połączenia zasobu. Ta usługa jest odpowiedzialna za Tworzenie wątków rozmowy, zarządzanie członkostwem wątków i udostępnianie użytkownikom tokenów dostępu. Więcej informacji o tokenach [dostępu można znaleźć w naszym](../../quickstarts/access-tokens.md) przewodniku Szybki Start.

 - **Aplikacja kliencka:**  Aplikacja kliencka nawiązuje połączenie z zaufaną usługą i otrzymuje tokeny dostępu, które są używane do nawiązywania bezpośredniego połączenia z usługami komunikacyjnymi. Po ustanowieniu tego połączenia aplikacja kliencka może wysyłać i odbierać wiadomości.
    
## <a name="message-types"></a>Typy komunikatów

Czat usług komunikacyjnych udostępnia komunikaty generowane przez użytkownika, a także komunikaty generowane przez system, nazywane **działaniami wątków**. Działania wątków są generowane, gdy wątek rozmowy zostanie zaktualizowany. W przypadku wywołania `List Messages` lub `Get Messages` w wątku rozmowy wynik będzie zawierać komunikaty tekstowe generowane przez użytkownika, a także komunikaty systemowe w kolejności chronologicznej. Dzięki temu można określić, kiedy element członkowski został dodany lub usunięty albo kiedy temat wątku rozmowy został zaktualizowany. Obsługiwane typy komunikatów:  

 - `Text`: Rzeczywisty komunikat tworzony i wysyłany przez użytkownika w ramach konwersacji rozmowy. 
 - `ThreadActivity/AddMember`: Komunikat systemowy wskazujący, że co najmniej jeden element członkowski został dodany do wątku rozmowy. Przykład:

```xml

<addmember>
    <eventtime>1598478187549</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</id>
        <friendlyName>User 1</friendlyName>
    </detailedtargetinfo>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</addmember>

```  

- `ThreadActivity/DeleteMember`: Komunikat systemowy wskazujący, że element członkowski został usunięty z wątku rozmowy. Przykład:

```xml

<deletemember>
    <eventtime>1598478187642</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</deletemember>

```

- `ThreadActivity/TopicUpdate`: Komunikat systemowy wskazujący, że Zaktualizowano temat. Przykład:

```xml

<topicupdate>
    <eventtime>1598477591811</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <value>New topic</value>
</topicupdate>

```

## <a name="real-time-signaling"></a>Sygnalizowanie w czasie rzeczywistym 

Biblioteka kliencka JavaScript programu Chat obejmuje Sygnalizowanie w czasie rzeczywistym. Pozwala to klientom na nasłuchiwanie aktualizacji w czasie rzeczywistym i komunikatów przychodzących do wątku rozmowy bez konieczności sondowania interfejsów API. Dostępne są następujące zdarzenia:

 - `ChatMessageReceived` — gdy nowa wiadomość jest wysyłana do wątku rozmowy, do którego należy użytkownik. To zdarzenie nie jest wysyłane do automatycznie generowanych komunikatów systemowych, które zostały omówione w poprzednim temacie.  
 - `ChatMessageEdited` — gdy komunikat jest edytowany w wątku rozmowy, do którego należy użytkownik. 
 - `ChatMessageDeleted` -Po usunięciu komunikatu w wątku rozmowy, do którego należy użytkownik. 
 - `TypingIndicatorReceived` — gdy inny element członkowski pisze wiadomość w wątku czatu, do którego należy użytkownik. 
 - `ReadReceiptReceived` — gdy inny członek odczytał komunikat Wysłany przez użytkownika w wątku rozmowy. 

## <a name="chat-events"></a>Zdarzenia czatu 

Sygnalizowanie w czasie rzeczywistym umożliwia użytkownikom rozmowy w czasie rzeczywistym. Usługi mogą używać Azure Event Grid, aby subskrybować zdarzenia związane z rozmową. Aby uzyskać więcej informacji, zobacz temat [Omówienie obsługi zdarzeń](../event-handling.md).

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Używanie Cognitive Services z biblioteką klienta czatu do włączania funkcji inteligentnych

Korzystając z [interfejsów API poznawczej platformy Azure](https://docs.microsoft.com/azure/cognitive-services/) , można dodawać inteligentne funkcje do aplikacji przy użyciu biblioteki klienckiej rozmowy. Możesz na przykład:

- Zezwól użytkownikom na rozmowę ze sobą w różnych językach. 
- Pomóż agentowi pomocy technicznej w ustalaniu priorytetów biletów przez wykrywanie negatywnej tonacji problemu przychodzącego od klienta.
- Analizuj komunikaty przychodzące pod kątem wykrywania kluczy i rozpoznawania jednostek oraz Monituj o odpowiednie informacje dla użytkownika w aplikacji na podstawie zawartości wiadomości.

Jednym ze sposobów osiągnięcia tego celu jest to, że usługa zaufana działa jako element członkowski wątku rozmowy. Załóżmy, że chcesz włączyć tłumaczenie języka. Ta usługa będzie odpowiedzialna za nasłuchiwanie komunikatów wymienianych przez innych członków [1], wywoływanie interfejsów API poznawczej w celu przetłumaczenia zawartości na żądany język [2, 3] i wysłanie przetłumaczonego wyniku jako komunikat w wątku rozmowy [4]. 

W ten sposób historia komunikatów będzie zawierać zarówno oryginalne, jak i tłumaczone komunikaty. W aplikacji klienckiej można dodać logikę, aby wyświetlić oryginalny lub przetłumaczony komunikat. Zapoznaj się z [tym przewodnikiem Szybki Start](https://docs.microsoft.com/azure/cognitive-services/translator/quickstart-translator) , aby dowiedzieć się, jak używać interfejsów API poznawcze do tłumaczenia tekstu na różne języki. 

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagram przedstawiający architekturę czatu usług komunikacyjnych.":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do rozmowy](../../quickstarts/chat/get-started.md)

Następujące dokumenty mogą być interesujące:

- Zapoznaj się z [biblioteką klienta rozmowy](sdk-features.md)

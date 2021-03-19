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
ms.openlocfilehash: e71917d917491037100b4ee666cbeb030b0a4786
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656145"
---
# <a name="chat-concepts"></a>Pojęcia dotyczące czatu 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Biblioteki klienckie programu Chat usługi Azure Communication Services mogą służyć do dodawania rozmów tekstowych w czasie rzeczywistym do aplikacji. Ta strona zawiera podsumowanie najważniejszych koncepcji i możliwości rozmowy.    

Zobacz [Omówienie biblioteki klienta rozmowy usług komunikacyjnych](./sdk-features.md) , aby dowiedzieć się więcej o określonych językach i możliwościach biblioteki klienta.    

## <a name="chat-overview"></a>Omówienie rozmowy    

Konwersacje rozmowy odbywają się w wątkach rozmowy. Wątek rozmowy może zawierać wiele komunikatów i wielu użytkowników. Każdy komunikat należy do pojedynczego wątku, a użytkownik może być częścią jednego lub wielu wątków. Każdy użytkownik w wątku rozmowy nosi nazwę uczestnika. Tylko uczestnicy wątków mogą wysyłać i odbierać komunikaty oraz dodawać i usuwać innych użytkowników w wątku rozmowy. Usługi komunikacyjne przechowują historię rozmowy do momentu wykonania operacji usuwania w wątku lub komunikacie rozmowy lub do momentu, w którym żaden uczestnik nie jest pozostały w wątku rozmowy, a następnie wątek rozmowy zostanie oddzielony i umieszczony w kolejce do usunięcia. 
    
## <a name="service-limits"></a>Limity usługi   

- Maksymalna liczba uczestników dozwolonych w wątku rozmowy to 250.   
- Maksymalny dozwolony rozmiar wiadomości wynosi około 28 KB.  
- W przypadku wątków rozmowy z więcej niż 20 uczestników nie są obsługiwane potwierdzenia odczytu i funkcje wskaźnika wpisywania.    
- 
## <a name="chat-architecture"></a>Architektura rozmowy    

Istnieją dwa podstawowe części dla architektury czatu: 1) usługa zaufana i 2) aplikacja kliencka.    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagram przedstawiający architekturę czatu usług komunikacyjnych."::: 

 - **Usługa zaufana:** Aby prawidłowo zarządzać sesją czatu, potrzebna jest usługa, która pomaga połączyć się z usługami komunikacyjnymi przy użyciu parametrów połączenia zasobu. Ta usługa jest odpowiedzialna za Tworzenie wątków rozmowy, zarządzanie listami uczestników wątków i udostępnianie tokenów dostępu użytkownikom. Więcej informacji o tokenach [dostępu można znaleźć w naszym](../../quickstarts/access-tokens.md) przewodniku Szybki Start.   
 - **Aplikacja kliencka:**  Aplikacja kliencka nawiązuje połączenie z zaufaną usługą i otrzymuje tokeny dostępu, które są używane do nawiązywania bezpośredniego połączenia z usługami komunikacyjnymi. Po ustanowieniu tego połączenia aplikacja kliencka może wysyłać i odbierać wiadomości.   
Zalecamy generowanie tokenów dostępu przy użyciu zaufanej warstwy usług. W tym scenariuszu po stronie serwera będzie odpowiedzialna za tworzenie i zarządzanie użytkownikami oraz wystawianie ich tokenów.   
        
## <a name="message-types"></a>Typy komunikatów    

Czat usług komunikacyjnych udostępnia komunikaty generowane przez użytkownika, a także komunikaty generowane przez system, nazywane **działaniami wątków**. Działania wątków są generowane, gdy wątek rozmowy zostanie zaktualizowany. W przypadku wywołania `List Messages` lub `Get Messages` w wątku rozmowy wynik będzie zawierać komunikaty tekstowe generowane przez użytkownika, a także komunikaty systemowe w kolejności chronologicznej. Pomoże to określić, kiedy uczestnik został dodany lub usunięty lub kiedy temat wątku rozmowy został zaktualizowany. Obsługiwane typy komunikatów:  
    
 - `Text`: Wiadomość tekstowa złożona i wysyłana przez użytkownika w ramach konwersacji rozmowy. 
 - `RichText/HTML`: Sformatowana wiadomość tekstowa. Należy zauważyć, że użytkownicy usług komunikacyjnych obecnie nie mogą wysyłać komunikatów o postaci tekstu sformatowanego. Ten typ komunikatu jest obsługiwany przez komunikaty wysyłane przez zespoły użytkowników do użytkowników usług komunikacyjnych w scenariuszach międzyoperacyjności zespołów.   
 - `ThreadActivity/ParticipantAdded`: Komunikat systemowy wskazujący, że co najmniej jeden uczestnik został dodany do wątku rozmowy. Na przykład: 


``` 
{   
            "id": "1613589626560",  
            "type": "participantAdded", 
            "sequenceId": "7",  
            "version": "1613589626560", 
            "content":  
            {   
                "participants": 
                [   
                    {   
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",    
                        "displayName": "Jane",  
                        "shareHistoryTime": "1970-01-01T00:00:00Z"  
                    }   
                ],  
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"  
            },  
            "createdOn": "2021-02-17T19:20:26Z" 
        }   
``` 

- `ThreadActivity/ParticipantRemoved`: Komunikat systemowy wskazujący, że uczestnik został usunięty z wątku rozmowy. Na przykład:  

``` 
{   
            "id": "1613589627603",  
            "type": "participantRemoved",   
            "sequenceId": "8",  
            "version": "1613589627603", 
            "content":  
            {   
                "participants": 
                [   
                    {   
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",    
                        "displayName": "Jane",  
                        "shareHistoryTime": "1970-01-01T00:00:00Z"  
                    }   
                ],  
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"  
            },  
            "createdOn": "2021-02-17T19:20:27Z" 
        }   
``` 

- `ThreadActivity/TopicUpdate`: Komunikat systemowy wskazujący, że temat wątku został zaktualizowany. Na przykład:   
``` 
{   
            "id": "1613589623037",  
            "type": "topicUpdated", 
            "sequenceId": "2",  
            "version": "1613589623037", 
            "content":  
            {   
                "topic": "New topic",   
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"  
            },  
            "createdOn": "2021-02-17T19:20:23Z" 
        }   
``` 

## <a name="real-time-signaling"></a>Sygnalizowanie w czasie rzeczywistym  

Biblioteka kliencka JavaScript programu Chat obejmuje Sygnalizowanie w czasie rzeczywistym. Pozwala to klientom na nasłuchiwanie aktualizacji w czasie rzeczywistym i komunikatów przychodzących do wątku rozmowy bez konieczności sondowania interfejsów API. Dostępne są następujące zdarzenia:

 - `ChatMessageReceived` — gdy nowa wiadomość jest wysyłana do wątku rozmowy. To zdarzenie nie jest wysyłane do automatycznie generowanych komunikatów systemowych, które zostały omówione w poprzednim temacie.   
 - `ChatMessageEdited` — gdy komunikat jest edytowany w wątku rozmowy. 
 - `ChatMessageDeleted` — Po usunięciu komunikatu w wątku rozmowy.   
 - `TypingIndicatorReceived` — gdy inny uczestnik pisze komunikat w wątku rozmowy.   
 - `ReadReceiptReceived` — gdy inny uczestnik odczytał komunikat Wysłany przez użytkownika w wątku rozmowy.     
 - `ChatThreadCreated` — gdy wątek rozmowy zostanie utworzony przez użytkownika komunikacji. 
 - `ChatThreadDeleted` — gdy wątek rozmowy zostanie usunięty przez użytkownika komunikacji. 
 - `ChatThreadPropertiesUpdated` — gdy właściwości wątku rozmowy są aktualizowane; Obecnie obsługujemy tylko aktualizowanie tematu dla wątku.   
 - `ParticipantsAdded` — gdy użytkownik jest dodawany jako uczestnik do wątku rozmowy.  
 - `ParticipantsRemoved` — Gdy istniejący uczestnik zostanie usunięty z wątku rozmowy.


## <a name="chat-events"></a>Zdarzenia czatu  

Sygnalizowanie w czasie rzeczywistym umożliwia użytkownikom rozmowy w czasie rzeczywistym. Usługi mogą używać Azure Event Grid, aby subskrybować zdarzenia związane z rozmową. Aby uzyskać więcej informacji, zobacz temat [Omówienie obsługi zdarzeń](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?tabs=event-grid-event-schema).


## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Używanie Cognitive Services z biblioteką klienta czatu do włączania funkcji inteligentnych 

Korzystając z [interfejsów API poznawczej platformy Azure](../../../cognitive-services/index.yml) , można dodawać inteligentne funkcje do aplikacji przy użyciu biblioteki klienckiej rozmowy. Możesz na przykład:  

- Zezwól użytkownikom na rozmowę ze sobą w różnych językach.  
- Pomóż agentowi pomocy technicznej w ustalaniu priorytetów biletów przez wykrywanie negatywnej tonacji problemu przychodzącego od klienta.   
- Analizuj komunikaty przychodzące pod kątem wykrywania kluczy i rozpoznawania jednostek oraz Monituj o odpowiednie informacje dla użytkownika w aplikacji na podstawie zawartości wiadomości.

Jednym ze sposobów osiągnięcia tego celu jest to, że usługa zaufana działa jako uczestnik wątku rozmowy. Załóżmy, że chcesz włączyć tłumaczenie języka. Ta usługa będzie odpowiedzialna za nasłuchiwanie komunikatów wymienianych przez innych uczestników [1], wywoływanie interfejsów API poznawczej w celu przetłumaczenia zawartości na żądany język [2, 3] i wysłanie przetłumaczonego wyniku jako komunikat w wątku rozmowy [4].

W ten sposób historia komunikatów będzie zawierać zarówno oryginalne, jak i tłumaczone komunikaty. W aplikacji klienckiej można dodać logikę, aby wyświetlić oryginalny lub przetłumaczony komunikat. Zapoznaj się z [tym przewodnikiem Szybki Start](../../../cognitive-services/translator/quickstart-translator.md) , aby dowiedzieć się, jak używać interfejsów API poznawcze do tłumaczenia tekstu na różne języki. 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagram przedstawiający Cognitive Services korzystania z usług komunikacyjnych."::: 

## <a name="next-steps"></a>Następne kroki   

> [!div class="nextstepaction"] 
> [Wprowadzenie do rozmowy](../../quickstarts/chat/get-started.md)    

Następujące dokumenty mogą być interesujące:  
- Zapoznaj się z [biblioteką klienta rozmowy](sdk-features.md)
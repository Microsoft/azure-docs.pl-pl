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
ms.openlocfilehash: cf500d529eb22cdd333d796f156eedcd284ea20d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642319"
---
# <a name="chat-concepts"></a>Pojęcia dotyczące czatu 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Zestawy SDK rozmowy z usługami Azure Communication Services umożliwiają dodawanie do aplikacji funkcji rozmowy w czasie rzeczywistym. Ta strona zawiera podsumowanie najważniejszych koncepcji i możliwości rozmowy.    

Zobacz [Omówienie zestawu SDK rozmowy usług komunikacyjnych](./sdk-features.md) , aby dowiedzieć się więcej o określonych językach i możliwościach zestawu SDK.  

## <a name="chat-overview"></a>Omówienie rozmowy    

Konwersacje rozmowy odbywają się w **wątkach rozmowy**. Wątki rozmowy mają następujące właściwości:

- Wątek rozmowy jest jednoznacznie identyfikowany przez jego `ChatThreadId` . 
- Wątki rozmowy mogą mieć jednego lub wielu użytkowników jako uczestników, którzy mogą wysyłać do nich komunikaty. 
- Użytkownik może być częścią co najmniej jednego wątku rozmowy. 
- Tylko uczestnicy wątku mają dostęp do danego wątku rozmowy i tylko mogą wykonywać operacje wątku rozmowy. Operacje te obejmują wysyłanie i otrzymywanie komunikatów, Dodawanie uczestników i usuwanie uczestników. 
- Użytkownicy są automatycznie dodawani jako uczestnicy do wszystkich tworzonych przez siebie wątków czatu.

### <a name="user-access"></a>Dostęp użytkowników
Zazwyczaj twórca wątku i uczestnicy mają taki sam poziom dostępu do wątku i mogą wykonywać wszystkie powiązane operacje dostępne w zestawie SDK, włącznie z ich usunięciem. Uczestnicy nie mają dostępu do zapisu w przypadku wiadomości wysyłanych przez innych uczestników, co oznacza, że tylko nadawca wiadomości może zaktualizować lub usunąć wysyłane wiadomości. Jeśli inny uczestnik podejmie próbę wykonania tej czynności, wystąpi błąd. 

Jeśli chcesz ograniczyć dostęp do funkcji czatu dla zestawu użytkowników, możesz skonfigurować dostęp jako część zaufanej usługi. Zaufana usługa to usługa, która organizuje uwierzytelnianie i autoryzację uczestników rozmowy. Szczegółowo omówiono poniżej.  

### <a name="chat-data"></a>Dane rozmowy 
Usługi komunikacyjne przechowują historię rozmowy do momentu usunięcia ich jawnie. Uczestnicy wątku rozmowy mogą używać `ListMessages` do wyświetlania historii komunikatów dla określonego wątku. Użytkownicy usunięci z wątku rozmowy będą mogli wyświetlić poprzednią historię wiadomości, ale nie będą mogli wysyłać ani odbierać nowych komunikatów w ramach tego wątku rozmowy. W pełni bezczynny wątek bez uczestników zostanie automatycznie usunięty po upływie 30 dni. Aby dowiedzieć się więcej na temat danych przechowywanych przez usługi komunikacyjne, zapoznaj się z dokumentacją dotyczącą [ochrony prywatności](../privacy.md).  

### <a name="service-limits"></a>Limity usługi  
- Maksymalna liczba uczestników dozwolonych w wątku rozmowy to 250.   
- Maksymalny dozwolony rozmiar wiadomości wynosi około 28 KB.  
- W przypadku wątków rozmowy z więcej niż 20 uczestników nie są obsługiwane potwierdzenia odczytu i funkcje wskaźnika wpisywania.    

## <a name="chat-architecture"></a>Architektura rozmowy    

Istnieją dwa podstawowe części dla architektury czatu: 1) usługa zaufana i 2) aplikacja kliencka.    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagram przedstawiający architekturę czatu usług komunikacyjnych."::: 

 - **Usługa zaufana:** Aby prawidłowo zarządzać sesją czatu, potrzebna jest usługa, która pomaga połączyć się z usługami komunikacyjnymi przy użyciu parametrów połączenia zasobu. Ta usługa jest odpowiedzialna za Tworzenie wątków rozmowy, Dodawanie i usuwanie uczestników oraz wystawianie tokenów dostępu użytkownikom. Więcej informacji o tokenach [dostępu można znaleźć w naszym](../../quickstarts/access-tokens.md) przewodniku Szybki Start.  
 - **Aplikacja kliencka:**  Aplikacja kliencka nawiązuje połączenie z usługą zaufaną i otrzymuje tokeny dostępu, które są używane przez użytkowników do bezpośredniego nawiązywania połączenia z usługami komunikacyjnymi. Gdy zaufana usługa utworzy wątek rozmowy i dodaliśmy użytkowników jako uczestników, może używać aplikacji klienckiej do nawiązywania połączenia z wątkiem rozmowy i wysyłania komunikatów. Użyj funkcji powiadomień w czasie rzeczywistym, którą będziemy omawiać poniżej, w aplikacji klienckiej, aby subskrybować wiadomości & aktualizacje wątków od innych uczestników.
    
        
## <a name="message-types"></a>Typy komunikatów    

W ramach historii wiadomości program Chat udostępnia komunikaty generowane przez użytkownika, a także komunikaty generowane przez system. Komunikaty systemowe są generowane, gdy wątek rozmowy zostanie zaktualizowany i może pomóc w ustaleniu, kiedy uczestnik został dodany lub usunięty lub kiedy temat wątku rozmowy został zaktualizowany. W przypadku wywołania `List Messages` lub `Get Messages` w wątku rozmowy wynik będzie zawierać oba rodzaje komunikatów w kolejności chronologicznej.

W przypadku komunikatów generowanych przez użytkownika typ komunikatu można ustawić w `SendMessageOptions` czasie wysyłania komunikatu do wątku rozmowy. Jeśli nie podano wartości, usługi komunikacyjne będą domyślnie `text` typu. Ustawienie tej wartości jest ważne podczas wysyłania kodu HTML. Gdy `html` jest określony, usługi komunikacyjne będą oczyszczać zawartość, aby upewnić się, że są one bezpiecznie renderowane na urządzeniach klienckich.
 - `text`: Wiadomość tekstowa złożona i wysyłana przez użytkownika jako część wątku rozmowy. 
 - `html`: Sformatowany komunikat przy użyciu kodu HTML, składający się i wysyłany przez użytkownika jako część wątku rozmowy. 

Typy komunikatów systemowych: 
 - `participantAdded`: Komunikat systemowy wskazujący, że co najmniej jeden uczestnik został dodany do wątku rozmowy.
 - `participantRemoved`: Komunikat systemowy wskazujący, że uczestnik został usunięty z wątku rozmowy.
 - `topicUpdated`: Komunikat systemowy wskazujący, że temat wątku został zaktualizowany.

## <a name="real-time-notifications"></a>Powiadomienia w czasie rzeczywistym  

Niektóre zestawy SDK (takie jak zestaw SDK rozmowy JavaScript) obsługują powiadomienia w czasie rzeczywistym. Ta funkcja umożliwia klientom nasłuchiwanie usług komunikacyjnych na potrzeby aktualizacji w czasie rzeczywistym i komunikatów przychodzących do wątku rozmowy bez konieczności sondowania interfejsów API. Aplikacja kliencka może subskrybować następujące zdarzenia:
 - `chatMessageReceived` — gdy nowa wiadomość jest wysyłana do wątku rozmowy przez uczestnika.
 - `chatMessageEdited` — gdy komunikat jest edytowany w wątku rozmowy. 
 - `chatMessageDeleted` — Po usunięciu komunikatu w wątku rozmowy.   
 - `typingIndicatorReceived` — gdy inny uczestnik wysyła wskaźnik wpisywania do wątku rozmowy.    
 - `readReceiptReceived` — gdy inny uczestnik wysyła potwierdzenie odczytania dla wiadomości, które odczytają.  
 - `chatThreadCreated` — gdy wątek rozmowy jest tworzony przez użytkownika usług komunikacyjnych.    
 - `chatThreadDeleted` — gdy wątek rozmowy zostanie usunięty przez użytkownika usług komunikacyjnych.    
 - `chatThreadPropertiesUpdated` — gdy właściwości wątku rozmowy są aktualizowane; obecnie obsługiwane jest tylko aktualizowanie tematu dla wątku. 
 - `participantsAdded` — gdy użytkownik jest dodawany jako uczestnik wątku rozmowy.     
 - `participantsRemoved` — Gdy istniejący uczestnik zostanie usunięty z wątku rozmowy.

Powiadomienia w czasie rzeczywistym mogą być używane do udostępniania użytkownikom środowiska rozmowy w czasie rzeczywistym. Aby wysyłać powiadomienia wypychane dla komunikatów pominiętych przez użytkowników, a usługi komunikacyjne integrują się z Azure Event Grid, aby opublikować zdarzenia powiązane z czatem (operacja post), które można podłączyć do niestandardowej usługi powiadomień aplikacji. Aby uzyskać więcej informacji, zobacz [zdarzenia serwera](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fcommunication-services%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json).


## <a name="build-intelligent-ai-powered-chat-experiences"></a>Tworzenie inteligentnych i opartych na programie AI   

Możesz użyć [interfejsów API poznawczej platformy Azure](../../../cognitive-services/index.yml) z zestawem SDK rozmowy, aby skompilować przypadki użycia, takie jak:

- Zezwól użytkownikom na rozmowę ze sobą w różnych językach.  
- Pomóż agentowi pomocy technicznej w ustalaniu priorytetów biletów przez wykrywanie negatywnej tonacji komunikatu przychodzącego od klienta. 
- Analizuj komunikaty przychodzące pod kątem wykrywania kluczy i rozpoznawania jednostek oraz Monituj o odpowiednie informacje dla użytkownika w aplikacji na podstawie zawartości wiadomości.

Jednym ze sposobów osiągnięcia tego celu jest to, że usługa zaufana działa jako uczestnik wątku rozmowy. Załóżmy, że chcesz włączyć tłumaczenie języka. Ta usługa będzie odpowiedzialna za nasłuchiwanie komunikatów wymienianych przez innych uczestników [1], wywoływanie interfejsów API poznawczej w celu przetłumaczenia zawartości na żądany język [2, 3] i wysłanie przetłumaczonego wyniku jako komunikat w wątku rozmowy [4].

W ten sposób historia komunikatów będzie zawierać zarówno oryginalne, jak i tłumaczone komunikaty. W aplikacji klienckiej można dodać logikę, aby wyświetlić oryginalny lub przetłumaczony komunikat. Zapoznaj się z [tym przewodnikiem Szybki Start](../../../cognitive-services/translator/quickstart-translator.md) , aby dowiedzieć się, jak używać interfejsów API poznawcze do tłumaczenia tekstu na różne języki. 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagram przedstawiający Cognitive Services korzystania z usług komunikacyjnych."::: 

## <a name="next-steps"></a>Następne kroki   

> [!div class="nextstepaction"] 
> [Wprowadzenie do rozmowy](../../quickstarts/chat/get-started.md)    

Następujące dokumenty mogą być interesujące:  
- Zapoznaj się z [zestawem SDK rozmowy](sdk-features.md)

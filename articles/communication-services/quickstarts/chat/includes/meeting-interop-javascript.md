---
title: Szybki Start — Dołącz do spotkania zespołów
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0c41771af81989ff965098a762338216db54fd27
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578130"
---
## <a name="join-the-meeting-chat"></a>Dołącz do rozmowy o spotkaniu 

Po włączeniu współdziałania zespołów, użytkownik usług komunikacyjnych może dołączać do zespołów wywołania jako użytkownik-Gość przy użyciu biblioteki klienta wywołującego. Dołączenie do wywołania spowoduje dodanie ich jako uczestnika do rozmowy o spotkaniu, w którym mogą oni wysyłać i odbierać wiadomości z innymi użytkownikami w wywołaniu. Użytkownik nie będzie miał dostępu do komunikatów rozmowy, które zostały wysłane przed dołączeniem do wywołania. 

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Pobierz wątek rozmów dla użytkowników usług komunikacyjnych dla zespołów

Najpierw Utwórz wystąpienie `ChatThreadClient` dla wątku rozmowy o spotkaniu. Aby uzyskać identyfikator wątku, Przeanalizuj link do spotkania lub Użyj interfejsów API programu Graph z IDENTYFIKATORem spotkania. 

- Łącze do spotkania zespołów wygląda następująco: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` . `meeting_chat_thread_id`W tym łączu znajduje się identyfikator wątku. 
- Jeśli masz identyfikator spotkania, możesz użyć [interfejs API programu Graph](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta) , aby uzyskać identyfikator wątku. Odpowiedź [Get API](https://docs.microsoft.com/graph/api/onlinemeeting-get?view=graph-rest-beta&tabs=http%22%20%5C) będzie zawierać `chatInfo` obiekt, który zawiera `threadID` . 

Gdy masz identyfikator wątku rozmowy, możesz uzyskać klienta wątku rozmowy przy użyciu biblioteki klienckiej rozmowy JavaScript: 

```javascript
let chatThreadClient = await chatClient.getChatThreadClient(threadId); 

console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`); 
```
  
`chatThreadClient`Może służyć do wyświetlania listy członków w wątku rozmowy, uzyskiwania historii rozmowy i wysyłania komunikatów.  

## <a name="send-and-receive-messages"></a>Wysyłanie i odbieranie komunikatów  

Użyj, `SendMessage` Aby wysłać wiadomość do rozmowy o spotkaniu. W przypadku odbierania wiadomości przychodzących możliwość subskrybowania zdarzeń, takich jak `chatMessageReceived` nie jest obsługiwana, ponieważ Sygnalizowanie w czasie rzeczywistym nie jest jeszcze włączone dla tego scenariusza. Aby uzyskać najnowsze komunikaty, można sondować `ListMessages` interfejs API. W przypadku scenariusza interoperacyjności `ListMessages` interfejs API obsługuje teraz zwracanie trzech nowych typów komunikatów:
- `RichText/HTML`
- `ThreadActivity/MemberJoined`
- `ThreadActivity/MemberLeft` </br>

Aby uzyskać więcej informacji na temat typów komunikatów, zobacz [tutaj](../../../concepts/chat/concepts.md). 

**Uwaga** — obecnie wysyłanie i otrzymywanie komunikatów jest obsługiwane w scenariuszach współdziałania z zespołami. Inne funkcje, takie jak wpisywanie wskaźników i użytkowników usług komunikacyjnych, Dodawanie lub usuwanie innych użytkowników z spotkania zespołów nie są jeszcze obsługiwane.  

 

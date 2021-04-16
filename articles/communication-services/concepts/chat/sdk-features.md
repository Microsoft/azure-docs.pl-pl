---
title: Omówienie zestawu SDK czatu dla Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej o zestawie SDK Azure Communication Services Chat.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 561855704d157f9ad826b5db83600a79d9437fc6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500689"
---
# <a name="chat-sdk-overview"></a>Omówienie zestawu SDK czatu 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Azure Communication Services Sdk czatów mogą służyć do dodawania rozbudowanych czatów w czasie rzeczywistym do aplikacji.
    
## <a name="chat-sdk-capabilities"></a>Możliwości zestawu SDK czatu    

Na poniższej liście przedstawiono zestaw funkcji, które są obecnie dostępne w zestawach SDK Communication Services czatów.  

| Grupa funkcji | Możliwość | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Podstawowe możliwości | Tworzenie wątku czatu między co najmniej 2 użytkownikami                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Aktualizowanie tematu wątku czatu                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Dodawanie lub usuwanie uczestników z wątku czatu                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Wybieranie, czy historia wiadomości czatu ma być udostępniana dodawanej uczestnikowi                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Uzyskiwanie listy uczestników wątku czatu                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Usuwanie wątku czatu                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Jeśli użytkownik komunikuje się z użytkownikiem, pobierz listę wątków czatu, do których należy                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Uzyskiwanie informacji o konkretnym wątku czatu                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Wysyłanie i odbieranie komunikatów w wątku czatu                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Aktualizowanie zawartości wysłanej wiadomości                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Usuwanie wcześniej wysłanego komunikatu                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Odczytywanie paragonów dla wiadomości odczytanych przez innych uczestników czatu                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Otrzymuj powiadomienia, gdy uczestnicy aktywnie wpisują wiadomość w wątku czatu                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Pobierz wszystkie wiadomości w wątku czatu                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Wysyłanie emoji Unicode w ramach zawartości wiadomości                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Powiadomienia w czasie rzeczywistym (włączone przez zastrzeżony pakiet sygnałów**)|  Klienci czatu mogą subskrybować komunikaty przychodzące i inne operacje wykonywane w wątku czatu w czasie rzeczywistym. Aby wyświetlić listę obsługiwanych aktualizacji powiadomień w czasie rzeczywistym, zobacz [Pojęcia dotyczące czatu](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
| Integracja z Azure Event Grid             | Użyj zdarzeń czatu dostępnych w u Azure Event Grid, aby podłączyć niestandardowe usługi powiadomień lub opublikować to zdarzenie do webhook, aby wykonać logikę biznesową, na przykład aktualizowanie rekordów CRM po zakończeniu czatu   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Raportowanie </br>(Te informacje są dostępne na karcie Monitorowanie dla zasobu Communication Services na Azure Portal)      | Zrozumienie ruchu interfejsu API z aplikacji do czatów przez monitorowanie opublikowanych metryk w usłudze Azure Eksplorator metryk i ustawianie alertów w celu wykrywania nieprawidłowości     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Monitorowanie i debugowanie rozwiązania Communication Services przez włączenie rejestrowania diagnostycznego dla zasobu    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


**Zastrzeżony pakiet sygnałów jest implementowane przy użyciu gniazd internetowych. Jeśli gniazda internetowe nie są obsługiwane, zostanie przeprowadzone długie sondowanie.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Obsługa zestawu JAVAScript Chat SDK przez system operacyjny i przeglądarkę    

W poniższej tabeli przedstawiono zestaw obsługiwanych przeglądarek i wersji, które są obecnie dostępne.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | System operacyjny iPad|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Zestaw SDK czatu** | Firefox,*Chrome,* nowa przeglądarka Edge | Firefox,*Chrome,* Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

*Pamiętaj, że najnowsza wersja jest obsługiwana oprócz dwóch poprzednich wersji.<br/>   

## <a name="next-steps"></a>Następne kroki   

> [!div class="nextstepaction"] 
> [Wprowadzenie do czatu](../../quickstarts/chat/get-started.md)    

Następujące dokumenty mogą Cię zaciekawieć:  
- Zapoznaj się z pojęciami [czatu](../chat/concepts.md)
- Informacje na temat [sposobu działania](../pricing.md#chat) cennika na czacie

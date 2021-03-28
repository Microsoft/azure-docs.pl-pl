---
title: Omówienie zestawu SDK rozmowy dla usług Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej o zestawie SDK usługi Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 02cfb4abc4f553c8f4353d8488390ed45eb3a7e7
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642223"
---
# <a name="chat-sdk-overview"></a>Omówienie zestawu SDK rozmowy 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Zestawy SDK rozmowy z usługami Azure Communication Services mogą służyć do dodawania rozbudowanej, w czasie rzeczywistym rozmowy do aplikacji.
    
## <a name="chat-sdk-capabilities"></a>Możliwości zestawu SDK rozmowy    

Poniższa lista przedstawia zbiór funkcji, które są obecnie dostępne w zestawach SDK rozmowy usług komunikacyjnych.  

| Grupa funkcji | Możliwość | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Podstawowe możliwości | Tworzenie wątku rozmowy między 2 lub większą liczbą użytkowników                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Aktualizowanie tematu wątku rozmowy                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Dodawanie uczestników rozmowy lub usuwanie ich z wątku                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Zdecyduj, czy chcesz udostępniać historię wiadomości czatu uczestnikowi dodawanemu                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Pobierz listę uczestników w wątku rozmowy                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Usuwanie wątku rozmowy                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Użytkownik komunikacyjny otrzymuje listę wątków rozmowy, do których należy użytkownik                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Uzyskaj informacje dotyczące określonego wątku rozmowy                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Wysyłanie i odbieranie wiadomości w wątku rozmowy                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Aktualizowanie zawartości wysłanej wiadomości                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Usuwanie wcześniej wysłanej wiadomości                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Odczytaj potwierdzenia dla wiadomości, które zostały odczytane przez innych uczestników rozmowy                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Otrzymuj powiadomienia, gdy uczestnicy aktywnie wpisują komunikat w wątku rozmowy                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Pobierz wszystkie komunikaty w wątku rozmowy                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Wyślij znaki emoji Unicode jako część zawartości komunikatu                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Powiadomienia w czasie rzeczywistym (włączone przez zastrzeżony pakiet sygnalizujący * *)|  Klienci programu Chat mogą subskrybować aktualizacje w czasie rzeczywistym dla wiadomości przychodzących i innych operacji w wątku rozmowy. Aby wyświetlić listę obsługiwanych aktualizacji dla powiadomień w czasie rzeczywistym, zobacz [pojęcia dotyczące rozmowy](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  | 
| Integracja z usługą Azure Event Grid             | Użyj zdarzeń czatu dostępnych w Azure Event Grid, aby podłączyć niestandardowe usługi powiadomień lub opublikować to zdarzenie do elementu webhook, aby wykonać logikę biznesową, taką jak aktualizowanie rekordów CRM po zakończeniu rozmowy   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Raportowanie </br>(Te informacje są dostępne na karcie monitorowanie dla zasobu usług komunikacyjnych w Azure Portal)      | Informacje o ruchu interfejsu API z aplikacji czatu przez monitorowanie opublikowanych metryk w usłudze Azure Eksplorator metryk i Ustawianie alertów w celu wykrycia nieprawidłowości     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Monitoruj i Debuguj rozwiązanie usług komunikacyjnych, włączając rejestrowanie diagnostyczne dla zasobu    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


* * Zastrzeżony pakiet sygnalizowania jest implementowany przy użyciu gniazd sieci Web. Nastąpi powrót do długiego sondowania, jeśli gniazda sieci Web nie są obsługiwane.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Obsługa zestawu SDK rozmowy JavaScript według systemu operacyjnego i przeglądarki    

Poniższa tabela przedstawia zestaw obsługiwanych przeglądarek i wersji, które są obecnie dostępne.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | system operacyjny iPad|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Zestaw SDK rozmowy** | Firefox *, Chrome*, Nowa krawędź | Firefox *, Chrome*, Safari * | Własnego  | Własnego | Własnego | Safari | Safari |

* Warto zauważyć, że Najnowsza wersja jest obsługiwana oprócz poprzednich dwóch wersji.<br/>   

## <a name="next-steps"></a>Następne kroki   

> [!div class="nextstepaction"] 
> [Wprowadzenie do rozmowy](../../quickstarts/chat/get-started.md)    

Następujące dokumenty mogą być interesujące:  
- Zapoznaj się z [pojęciami dotyczącymi rozmowy](../chat/concepts.md)
- Zapoznaj się z [cennikiem](../pricing.md#chat) usługi Chat

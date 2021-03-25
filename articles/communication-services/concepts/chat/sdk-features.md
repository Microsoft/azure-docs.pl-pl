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
ms.openlocfilehash: b275c3af2e92dc5af677120b5082751d19676b2e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110818"
---
# <a name="chat-sdk-overview"></a>Omówienie zestawu SDK rozmowy 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Zestawy SDK rozmowy z usługami Azure Communication Services mogą służyć do dodawania rozbudowanej, w czasie rzeczywistym rozmowy do aplikacji.
    
## <a name="chat-sdk-capabilities"></a>Możliwości zestawu SDK rozmowy    

Poniższa lista przedstawia zbiór funkcji, które są obecnie dostępne w zestawach SDK rozmowy usług komunikacyjnych.  

| Grupa funkcji | Możliwość | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Podstawowe możliwości | Tworzenie wątku czatu między 2 lub większą liczbą użytkowników (do 250 użytkowników)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Aktualizowanie tematu wątku rozmowy                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Dodawanie uczestników rozmowy lub usuwanie ich z wątku                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Zdecyduj, czy chcesz udostępniać historię wiadomości czatu uczestnikowi dodawanemu                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Pobierz listę uczestników w wątku rozmowy                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Usuwanie wątku rozmowy                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Użytkownik komunikacyjny otrzymuje listę wątków rozmowy, do których należy użytkownik                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Uzyskaj informacje dotyczące określonego wątku rozmowy                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Wysyłanie i odbieranie wiadomości w wątku rozmowy                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Edytuj zawartość wysłanej wiadomości                                                                                | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Usuwanie wiadomości                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Odczytaj potwierdzenia dla wiadomości, które zostały odczytane przez innych uczestników rozmowy <br/> *Niedostępne, gdy w wątku rozmowy znajduje się więcej niż 20 uczestników*    | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Otrzymuj powiadomienia, gdy uczestnicy aktywnie wpisują komunikat w wątku rozmowy <br/> *Niedostępne, gdy w wątku rozmowy znajduje się więcej niż 20 elementów członkowskich*      | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Pobierz wszystkie komunikaty w wątku rozmowy <br/>                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Wyślij znaki emoji Unicode jako część zawartości komunikatu                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Sygnalizowanie w czasie rzeczywistym (włączone przez zastrzeżony pakiet sygnalizowania * *)|  Subskrybuj, aby otrzymywać aktualizacje w czasie rzeczywistym dla wiadomości przychodzących i innych operacji w aplikacji czatu. Aby wyświetlić listę obsługiwanych aktualizacji dotyczących sygnalizowania w czasie rzeczywistym, zobacz [pojęcia dotyczące rozmowy](concepts.md#real-time-signaling)                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  |    
| Obsługa Event Grid             | Korzystanie z integracji z Azure Event Grid i Konfigurowanie usługi komunikacyjnej do wykonywania logiki biznesowej na podstawie działania rozmowy lub do wtyczki niestandardowej usługi powiadomień wypychanych   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
| Monitorowanie        | Metryki żądań interfejsu API emitowane w Azure Portal umożliwiają tworzenie pulpitów nawigacyjnych, monitorowanie kondycji aplikacji czatu oraz Ustawianie alertów w celu wykrywania nieprawidłowych wartości      | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Konfigurowanie zasobu usług komunikacyjnych do odbierania dzienników operacyjnych czatu na potrzeby monitorowania i diagnostyki          | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  


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
---
title: Omówienie biblioteki klienta rozmowy dla usług Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej o bibliotece klienta czatu dotyczącej usług Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 705bd926c2ac6f414464254969b5c511c88891f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656111"
---
# <a name="chat-client-library-overview"></a>Omówienie biblioteki klienta czatu  

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Za pomocą bibliotek klienckich czatu usługi Azure Communication Services można dodawać rozbudowane rozmowy w czasie rzeczywistym do aplikacji.
    
## <a name="chat-client-library-capabilities"></a>Możliwości w bibliotece klienta rozmowy 

Na poniższej liście przedstawiono zestaw funkcji, które są obecnie dostępne w bibliotekach klienta rozmowy usług komunikacyjnych.  

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

## <a name="javascript-chat-client-library-support-by-os-and-browser"></a>Obsługa biblioteki klienta rozmowy w języku JavaScript według systemu operacyjnego i przeglądarki 

Poniższa tabela przedstawia zestaw obsługiwanych przeglądarek i wersji, które są obecnie dostępne.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | system operacyjny iPad|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Biblioteka kliencka rozmowy** | Firefox *, Chrome*, Nowa krawędź | Firefox *, Chrome*, Safari * | Własnego  | Własnego | Własnego | Safari | Safari |

* Warto zauważyć, że Najnowsza wersja jest obsługiwana oprócz poprzednich dwóch wersji.<br/>   

## <a name="next-steps"></a>Następne kroki   

> [!div class="nextstepaction"] 
> [Wprowadzenie do rozmowy](../../quickstarts/chat/get-started.md)    

Następujące dokumenty mogą być interesujące:  
- Zapoznaj się z [pojęciami dotyczącymi rozmowy](../chat/concepts.md)
---
title: Konwersacja na wielu urządzeniach (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 7c30ee2ef4a6ab0cd4241cac921a59eeadf5ce17
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401041"
---
# <a name="what-is-multi-device-conversation-preview"></a>Co to jest konwersacja na wielu urządzeniach (wersja zapoznawcza)?

**Konwersacja z wieloma urządzeniami** ułatwia tworzenie konwersacji mowy lub tekstu między wieloma klientami i koordynowanie wiadomości wysyłanych między nimi.

Dzięki **konwersacji z wieloma urządzeniami**można:

- Połącz wielu klientów w tej samej konwersacji i zarządzaj wysyłaniem i odbieraniem wiadomości między nimi.
- Łatwo transkrybować dźwięk z każdego klienta i wysłać transkrypcję do innych, z opcjonalnym tłumaczeniem.
- Łatwe wysyłanie wiadomości tekstowych między klientami dzięki opcjonalnemu tłumaczeniu.

Można utworzyć funkcję lub rozwiązanie, które działa na wielu urządzeniach. Każde urządzenie może niezależnie wysyłać wiadomości (transkrypcje wiadomości audio lub błyskawicznych) do wszystkich innych urządzeń.

Podczas gdy [**transkrypcja konwersacji**](conversation-transcription.md) działa na jednym urządzeniu z wielokanałową macierzą mikrofonów, **konwersacja** z wieloma urządzeniami jest odpowiednia dla scenariuszy z wieloma urządzeniami, z których każdy ma jeden mikrofon.

>[!IMPORTANT]
> Konwersacja na wielu urządzeniach **nie** obsługuje wysyłania plików audio między klientami: tylko transkrypcja i/lub tłumaczenie.

## <a name="key-features"></a>Najważniejsze funkcje

- **Transkrypcja w czasie rzeczywistym** — każdy otrzyma transkrypcję rozmowy, dzięki czemu będzie mógł śledzić tekst w czasie rzeczywistym lub zapisać go na później.
- **Tłumaczenie w czasie rzeczywistym** — dzięki ponad 60 [obsługiwanym językom](language-support.md#text-languages) do tłumaczenia tekstu użytkownicy mogą tłumaczyć konwersację na preferowany język(-y).
- **Czytelne transkrypcje** - Transkrypcja i tłumaczenie są łatwe do naśladowania, z przerwami interpunkcyjnymi i zdań.
- **Wprowadzanie głosu lub tekstu** — każdy użytkownik może mówić lub pisać na własnym urządzeniu, w zależności od funkcji obsługi języka dla wybranego języka uczestnika. Zapoznaj się z [obsługą języka](language-support.md#speech-to-text).
- **Przekazywanie wiadomości** — usługa konwersacji z wieloma urządzeniami będzie dystrybuować wiadomości wysyłane przez jednego klienta do wszystkich innych, w wybranym przez siebie języku(-ach).
- **Identyfikacja wiadomości** — każda wiadomość, którą użytkownicy otrzymają w konwersacji, zostanie oznaczona pseudonimem użytkownika, który ją wysłał.

## <a name="use-cases"></a>Przypadki zastosowań

### <a name="lightweight-conversations"></a>Lekkie rozmowy

Tworzenie i dołączanie do rozmowy jest łatwe. Jeden użytkownik będzie działać jako "host" i utworzyć konwersację, która generuje losowy pięcioliterowy kod konwersacji i kod QR. Wszyscy inni użytkownicy mogą dołączyć do konwersacji, wpisując kod konwersacji lub skanując kod QR. 

Ponieważ użytkownicy dołączają za pośrednictwem kodu konwersacji i nie muszą udostępniać informacji kontaktowych, łatwo jest tworzyć szybkie konwersacje na miejscu.

### <a name="inclusive-meetings"></a>Spotkania integracyjne

Transkrypcja i tłumaczenie w czasie rzeczywistym mogą pomóc w udostępnieniu rozmów osobom, które mówią różnymi językami i/lub są głuche lub niedosłyszące. Każda osoba może również aktywnie uczestniczyć w rozmowie, mówiąc preferowanym językiem lub wysyłając wiadomości błyskawiczne.

### <a name="presentations"></a>Prezentacje

Można również dostarczać napisy do prezentacji i wykładów zarówno na ekranie, jak i na urządzeniach członków grupy odbiorców. Po dołączeniu odbiorców do kodu konwersacji mogą zobaczyć transkrypcję w preferowanym języku, na własnym urządzeniu.

> [!NOTE]
> Aby zobaczyć przykład, zapoznaj się z [presentation translator](https://www.microsoft.com/translator/apps/presentation-translator/), dodatek PowerPoint, który korzysta z usługi konwersacji na wielu urządzeniach. Można go pobrać [tutaj](https://www.microsoft.com/download/details.aspx?id=55024).

## <a name="how-it-works"></a>Jak to działa

Wszyscy klienci będą używać SDK mowy do tworzenia lub dołączania do konwersacji. SDK mowy współdziała z usługą konwersacji wielu urządzeń, która zarządza okresem istnienia konwersacji, w tym listą uczestników, wybranymi językami (językami) każdego klienta i wysłanymi wiadomościami.  

Każdy klient może wysyłać wiadomości audio lub błyskawiczne. Usługa będzie używać rozpoznawania mowy do konwertowania dźwięku na tekst i wysyłania wiadomości błyskawicznych w stanie, w jakim są. Jeśli klienci wybierają różne języki, usługa przetłumaczy wszystkie wiadomości na określone języki każdego klienta.

![Diagram konwersacji na wielu urządzeniach](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Omówienie konwersacji, hosta i uczestnika

**Konwersacja** to sesja, do którą jeden użytkownik rozpoczyna dołączanie do innych użytkowników uczestniczących. Wszyscy klienci łączą się z konwersacji za pomocą pięcioliterowego **kodu konwersacji**.

Każda konwersacja tworzy metadane, które obejmują:
-    Sygnatury czasowe rozpoczęcia i zakończenia rozmowy
-    Lista wszystkich uczestników konwersacji, która zawiera wybrany pseudonim każdego użytkownika i podstawowy język do wprowadzania mowy lub tekstu.


Istnieją dwa typy użytkowników w konwersacji: **host** i **uczestnik**.

**Host** jest użytkownikiem, który rozpoczyna konwersację i działa jako administrator tej konwersacji.
- Każda konwersacja może mieć tylko jednego hosta
- Host musi być połączony z konwersacji na czas trwania konwersacji. Jeśli host opuści konwersację, konwersacja zakończy się dla wszystkich innych uczestników.
- Host ma kilka dodatkowych formantów do zarządzania konwersacji: 
    - Blokowanie konwersacji - uniemożliwianie dołączaniu dodatkowych uczestników
    - Wyciszenie wszystkich uczestników — uniemożliwienie innym uczestnikom wysyłania jakichkolwiek wiadomości do konwersacji, niezależnie od tego, czy są przepisyne z wiadomości mowy lub wiadomości błyskawicznych
    - Wyciszanie poszczególnych uczestników
    - Co nie do pracy ze wszystkimi uczestnikami
    - Odłączanie wyciszenia poszczególnych uczestników

Uczestnik **participant** jest użytkownikiem, który dołącza do konwersacji.
- Uczestnik może opuścić i ponownie dołączyć do tej samej rozmowy w dowolnym momencie, bez kończenia rozmowy dla innych uczestników.
- Uczestnicy nie mogą zablokować konwersacji ani wyciszyć/wyłączyć innych osób

> [!NOTE]
> Każda rozmowa może mieć do 100 uczestników, z czego 10 może być jednocześnie mówiąc w danym momencie.

## <a name="language-support"></a>Obsługa języków

Podczas tworzenia lub dołączania do konwersacji każdy użytkownik musi wybrać **podstawowy język**: język, w którym będzie mówił i wysyłać wiadomości błyskawiczne, a także język, w którym będą widzieć wiadomości innych użytkowników.

Istnieją dwa rodzaje języków: **zamiana mowy na tekst** i tylko **tekst:**
- Jeśli użytkownik wybierze język **mowy na tekst** jako podstawowy język, będzie mógł używać zarówno mowy, jak i tekstu w konwersacji.

- Jeśli użytkownik wybierze język **tylko w tekście,** będzie mógł używać tylko wprowadzania tekstu i wysyłać wiadomości błyskawiczne w konwersacji. Języki tylko dla tekstu są językami, które są obsługiwane do tłumaczenia tekstu, ale nie mowy na tekst. Dostępne języki można wyświetlić na stronie [pomocy technicznej języka.](supported-languages.md)

Oprócz języka podstawowego każdy uczestnik może również określić dodatkowe języki do tłumaczenia konwersacji.

Poniżej znajduje się podsumowanie tego, co użytkownik będzie mógł zrobić w konwersacji na wielu urządzeniach, w oparciu o wybrany przez nich język podstawowy.


| Co użytkownik może zrobić w konwersacji | Zamiana mowy na tekst | Tylko tekst |
|-----------------------------------|----------------|------|
| Korzystanie z wprowadzania mowy | ✔️ | ❌ |
| Wysyłanie wiadomości błyskawicznych | ✔️ | ✔️ |
| Przetłumaczyć rozmowę | ✔️ | ✔️ |

> [!NOTE]
> Aby uzyskać listę dostępnych języków mowy na tekst i tłumaczenia tekstu, zobacz [obsługiwane języki](supported-languages.md).



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tłumaczenie konwersacji w czasie rzeczywistym](quickstarts/multi-device-conversation.md)

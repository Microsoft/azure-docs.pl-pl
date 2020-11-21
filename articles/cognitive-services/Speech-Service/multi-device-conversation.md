---
title: Konwersacja na wiele urządzeń (wersja zapoznawcza) — usługa mowy
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
ms.openlocfilehash: 3c2dbaf8f65208126c52617d4b89d901b3670dac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025178"
---
# <a name="what-is-multi-device-conversation-preview"></a>Co to jest konwersacja na wiele urządzeń (wersja zapoznawcza)?

**Konwersacja z wieloma urządzeniami** ułatwia tworzenie konwersacji mowy lub tekstu między wieloma klientami i koordynowanie komunikatów wysyłanych między nimi.

W przypadku konwersacji z użyciem **kilku urządzeń** można:

- Łączenie wielu klientów z tą samą konwersacją i Zarządzanie wysyłaniem i otrzymywaniem komunikatów między nimi.
- Łatwo transkrypcja dźwięk z każdego klienta i wysyłaj transkrypcję do innych, z opcjonalnym tłumaczeniem.
- Łatwe wysyłanie wiadomości tekstowych między klientami przy użyciu opcjonalnego tłumaczenia.

Można utworzyć funkcję lub rozwiązanie, które działa w ramach tablicy urządzeń. Każde urządzenie może niezależnie wysyłać wiadomości (transkrypcje audio lub wiadomości błyskawiczne) do wszystkich innych urządzeń.

[**Transkrypcja konwersacji**](conversation-transcription.md) działa na jednym urządzeniu z wielokanałową tablicą mikrofonów, dlatego **konwersacje** dla wielu urządzeń są dostosowane do scenariuszy z wieloma urządzeniami, z których każdy ma jeden mikrofon.

>[!IMPORTANT]
> Konwersacja z różnymi urządzeniami **nie obsługuje wysyłania** plików audio między klientami: tylko transkrypcja i/lub tłumaczenie.

## <a name="key-features"></a>Najważniejsze funkcje

- **Transkrypcja w czasie rzeczywistym** — wszyscy otrzymają transkrypcję konwersacji, tak aby mogli się one pojawiać w czasie rzeczywistym lub zapisywać ją w przyszłości.
- **Tłumaczenie w czasie rzeczywistym** — dzięki więcej niż 60 [języków obsługiwanych](language-support.md#text-languages) w przypadku tłumaczenia tekstu użytkownicy mogą przetłumaczyć konwersację na preferowane języki.
- **Czytelne transkrypcje** — transkrypcja i tłumaczenie są łatwe do wykonania, z interpunkcją i podziałem zdań.
- **Wprowadzanie głosu lub tekstu** — każdy użytkownik może mówić lub wpisywać własne urządzenie, w zależności od możliwości obsługi języka dostępnego dla wybranego języka uczestnika. Zapoznaj się z [obsługą języków](language-support.md#speech-to-text).
- **Przekaźnik komunikatów** — usługa konwersacji dla wielu urządzeń będzie rozsyłać komunikaty wysyłane przez jednego klienta do wszystkich innych użytkowników w wybranych językach.
- **Identyfikacja komunikatów** — każdy komunikat, który użytkownicy otrzymują w konwersacji, będzie otagowany pseudonimem użytkownika, który go wysłał.

## <a name="use-cases"></a>Przypadki zastosowań

### <a name="lightweight-conversations"></a>Lekkie konwersacje

Tworzenie i dołączanie konwersacji jest proste. Jeden z użytkowników będzie pełnić funkcję "host" i utworzyć konwersację, która generuje losowy i dwuliterowy kod konwersacji oraz kod QR. Wszyscy inni użytkownicy mogą dołączać do konwersacji, wpisując kod w konwersacji lub skanując kod QR. 

Ze względu na to, że użytkownicy przyłączają się za pośrednictwem kodu konwersacji i nie muszą udostępniać informacji kontaktowych, można łatwo tworzyć szybkie rozmowy na miejscu.

### <a name="inclusive-meetings"></a>Załączne spotkania

Transkrypcja i tłumaczenie w czasie rzeczywistym mogą pomóc w udostępnieniu konwersacji osobom, które mówią różne języki i/lub są niesłyszące lub słabo słyszące. Każda osoba może również aktywnie uczestniczyć w konwersacji, mówiąc swój preferowany język lub wysyłając wiadomości błyskawiczne.

### <a name="presentations"></a>Prezentacje

Możesz również udostępnić napisy dla prezentacji i wykładów zarówno na ekranie, jak i na urządzeniach należących do odbiorców. Po dołączeniu do kodu konwersacji odbiorcy mogą zobaczyć transkrypcję w preferowanym języku na swoim własnym urządzeniu.

> [!NOTE]
> Aby zapoznać się z przykładem, zapoznaj się z tematem [tłumaczenie prezentacji](https://www.microsoft.com/translator/apps/presentation-translator/)— dodatek programu PowerPoint, który używa usługi konwersacji obejmującej wiele urządzeń. Można go pobrać [tutaj](https://download.cnet.com/s/powerpoint-add-in/).

## <a name="how-it-works"></a>Jak to działa

Wszyscy klienci będą używać zestawu Speech SDK do tworzenia lub przyłączania do konwersacji. Zestaw Speech SDK współdziała z usługą konwersacji obejmującej wiele urządzeń, która zarządza okresem istnienia konwersacji, w tym listy uczestników, wybranych języków i wiadomości.  

Każdy klient może wysyłać dźwięk lub wiadomości błyskawiczne. Usługa użyje funkcji rozpoznawania mowy, aby przekonwertować dźwięk na tekst i wysyłać wiadomości błyskawiczne. Jeśli klienci wybierają różne języki, usługa będzie tłumaczyć wszystkie komunikaty do określonych języków każdego klienta.

![Diagram omówienia konwersacji z obsługą kilku urządzeń](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Przegląd konwersacji, hosta i uczestnika

**Konwersacja** to sesja, którą jeden użytkownik uruchamia dla innych uczestniczących użytkowników do przyłączenia. Wszyscy klienci nawiązują połączenie z konwersacją przy użyciu 5-literowego **kodu konwersacji**.

Każda konwersacja tworzy metadane, które obejmują:
-    Sygnatury czasowe po rozpoczęciu i zakończeniu konwersacji
-    Lista wszystkich uczestników konwersacji, w tym każdego pseudonimu wybranego użytkownika i języka podstawowego dla mowy lub wprowadzania tekstu.


Istnieją dwa typy użytkowników w konwersacji:  **host** i **uczestnik**.

**Host** to użytkownik, który uruchamia konwersację i który pełni rolę administratora tej konwersacji.
- Każda konwersacja może mieć tylko jeden host
- Host musi być połączony z konwersacją w czasie trwania rozmowy. Jeśli Host opuszcza konwersację, Konwersacja zostanie zakończona dla wszystkich pozostałych uczestników.
- Host ma kilka dodatkowych kontrolek do zarządzania konwersacją: 
    - Zablokuj konwersację — Zapobiegaj dołączeniu dodatkowych uczestników
    - Wycisz wszystkich uczestników — uniemożliwiaj innym uczestnikom wysyłanie komunikatów do konwersacji, bez względu na to, czy uzyskanego z mowy czy wiadomości błyskawicznych
    - Wycisz poszczególnych uczestników
    - Wyłącz wyciszenie wszystkich uczestników
    - Wyłącz wyciszenie poszczególnych uczestników

**Uczestnikem** jest użytkownik, który dołącza do konwersacji.
- Uczestnik może opuścić tę samą konwersację i ponownie dołączyć ją w dowolnym momencie, bez kończenia rozmowy dla innych uczestników.
- Uczestnicy nie mogą zablokować konwersacji ani wyciszać/wyłączać/wyciszać innych

> [!NOTE]
> Każda konwersacja może mieć do 100 uczestników, z których 10 może jednocześnie mówić w dowolnym momencie.

## <a name="language-support"></a>Obsługa języków

Podczas tworzenia lub przyłączania konwersacji każdy użytkownik musi wybrać **język podstawowy**: język, w którym będą mówić i wysyłać wiadomości błyskawiczne, a także język, w którym będą widzieć inne komunikaty użytkowników.

Istnieją dwa rodzaje języków: **Zamiana mowy na tekst** i **tekst**:
- Jeśli użytkownik wybierze język **zamiany mowy na tekst** jako język podstawowy, będzie mógł korzystać z funkcji rozpoznawania mowy i tekstu w konwersacji.

- Jeśli użytkownik wybierze język **tekstowy** , wówczas będzie mógł korzystać tylko z wprowadzania tekstu i wysyłać wiadomości błyskawiczne w konwersacji. Języki tylko do tekstu to języki obsługiwane w przypadku tłumaczenia tekstu, ale nie zamiany mowy na tekst. Dostępne języki można zobaczyć na stronie [Obsługa języka](./language-support.md) .

Niezależnie od ich języka podstawowego każdy uczestnik może także określić dodatkowe języki do tłumaczenia konwersacji.

Poniżej znajduje się podsumowanie możliwości, które użytkownik będzie mógł wykonać w konwersacji z wielu urządzeń w oparciu o wybrany język podstawowy.


| Co użytkownik może zrobić w konwersacji | Zamiana mowy na tekst | Tylko tekst |
|-----------------------------------|----------------|------|
| Korzystanie z danych wejściowych mowy | ✔️ | ❌ |
| Wysyłanie wiadomości błyskawicznych | ✔️ | ✔️ |
| Tłumaczenie konwersacji | ✔️ | ✔️ |

> [!NOTE]
> Aby zapoznać się z listami dostępnych języków zamiany mowy na tekst i tekstu, zobacz [obsługiwane języki](./language-support.md).



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tłumacz konwersacje w czasie rzeczywistym](quickstarts/multi-device-conversation.md)
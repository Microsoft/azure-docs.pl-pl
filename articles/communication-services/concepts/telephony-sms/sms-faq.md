---
title: CZĘSTO ZADAWANE PYTANIA DOTYCZĄCE PROGRAMU SMS
titleSuffix: An Azure Communication Services concept document
description: CZĘSTO ZADAWANE PYTANIA DOTYCZĄCE PROGRAMU SMS
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: reference
ms.service: azure-communication-services
ms.openlocfilehash: 1ba7c730542adb74356d71f2482cce57e633cb65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646316"
---
# <a name="sms-faq"></a>CZĘSTO ZADAWANE PYTANIA DOTYCZĄCE PROGRAMU SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>Czy klient może używać usługi Azure Communication Services do celów awaryjnych?

Usługi komunikacyjne platformy Azure nie obsługują funkcji typu "text-to-911" w Stany Zjednoczone, ale możliwe jest, że użytkownik może mieć odpowiednie zobowiązania zgodnie z regułami Federalnej Komisji Communications.  Należy ocenić, czy reguły tekstu do 911 są stosowane do Twojej usługi lub aplikacji. W zakresie, w jakim są objęte tymi regułami, użytkownik jest odpowiedzialny za kierowanie komunikatów SMS 911 do centrów połączeń awaryjnych, które je żądają. Możesz określić własny model dostarczania tekstu na 911, ale jedno podejście zaakceptowane przez FCC obejmuje automatyczne uruchamianie natywnego numeru telefonu na urządzeniu przenośnym użytkownika w celu dostarczenia tekstu 911 za pomocą podstawowego operatora komórkowego.

## <a name="are-there-any-limits-on-sending-messages"></a>Czy istnieją jakieś ograniczenia dotyczące wysyłania komunikatów?

Aby upewnić się, że będziemy nadal oferować wysoką jakość usług spójnych z naszymi umowy SLA, usługi komunikacyjne platformy Azure stosują limity szybkości (różne dla każdego elementu podstawowego). Deweloperzy, którzy wywołują nasze interfejsy API poza limitem, otrzymają odpowiedź na kod stanu HTTP 429. Jeśli firma ma wymagania, które przekraczają limity stawki, Wyślij wiadomość e-mail na adres phone@microsoft.com .

Limity szybkości dla programu SMS:

|Operacja|Zakres|Przedziały czasu| Limit (żądanie nr) | Liczba jednostek komunikatów na minutę|
|---------|-----|-------------|-------------------|-------------------------|
|Wyślij wiadomość|Na liczbę|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Jak usługa Azure Communications Services obsługuje opłaty za bezpłatne numery telefonów?

Opłaty za bezpłatne numery telefonów US są narzucane i wymuszane przez przewoźników Stanów Zjednoczonych.
- Zatrzymaj — Jeśli odbiorca wiadomości tekstowej chce zrezygnować z akceptacji, może wysłać element "STOP" do numeru bezpłatnego. Przewoźnik wysyła następującą odpowiedź domyślną dla zatrzymywania: "NETWORK MSG: odpowiedź na słowo" Stop ", która blokuje wszystkie teksty wysyłane z tego numeru. Wróć do tyłu tekst "Cofnij zatrzymanie", aby ponownie otrzymywać komunikaty ".
- Uruchom/Cofnij — Jeśli odbiorca chce ponownie subskrybować wiadomości SMS za pośrednictwem numeru bezpłatnego, może wysłać wiadomość "Rozpocznij" lub "odtrzymywać do numeru bezpłatnego. Przewoźnik wysyła następującą domyślną odpowiedź dotyczącą uruchamiania/odtrzymywania: "komunikat sieciowy: odpowiedź" nie zatrzyma "i rozpocznie otrzymywanie komunikatów ponownie z tej liczby".
- Usługa Azure Communication Services wykryje komunikat zatrzymania i zablokuje wszystkie dalsze komunikaty odbiorcy. Raport dostarczania wskaże dostarczenie zakończone niepowodzeniem z komunikatem o stanie jako "zablokowany nadawca dla danego odbiorcy".
- Komunikaty zatrzymania, cofania i uruchamiania będą przekazywane z powrotem do użytkownika. Usługi komunikacyjne platformy Azure zachęcają do monitorowania i wdrażania tych postanowień w celu zapewnienia, że nie będą podejmowane dalsze próby wysłania komunikatów do adresatów, którzy wybiorą powiadomienie z Twojej komunikacji.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Jak mogę odbierać wiadomości za pomocą usług Azure Communications Services?

Klienci korzystający z usług Azure Communications Services mogą odbierać wiadomości przychodzące przy użyciu Azure Event Grid. Postępuj zgodnie z tym [przewodnikiem Szybki Start](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/handle-sms-events) , aby skonfigurować siatkę zdarzeń do odbierania wiadomości.

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>Czy mogę wysyłać i odbierać długie wiadomości (>2048 znaków)?

Usługi komunikacyjne platformy Azure obsługują wysyłanie i otrzymywanie długich komunikatów za pośrednictwem wiadomości SMS. Jednak niektórzy operatorzy bezprzewodowi lub urządzenia mogą działać inaczej podczas otrzymywania długich komunikatów.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>Jak wysyłane są komunikaty do numerów telefonów stacjonarnych?

W Stany Zjednoczone usługi Azure Communication Services nie sprawdzają numerów telefonów stacjonarnych i spróbują wysłać ją do przewoźników w celu dostarczenia. Klienci będą obciążani za komunikaty wysyłane do numerów telefonów stacjonarnych. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>Czy mogę wysyłać wiadomości do wielu adresatów?


Tak, możesz wykonać jedno żądanie z wieloma adresatami. Postępuj zgodnie z tym [przewodnikiem Szybki Start](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/send?pivots=programming-language-csharp) , aby wysyłać komunikaty do wielu adresatów.

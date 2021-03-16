---
title: Koncepcje programu SMS w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Zapoznaj się z tematami dotyczącymi usługi Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c866629677790447d0ed730ae9cc62cb37e338e9
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495508"
---
# <a name="sms-concepts"></a>Pojęcia dotyczące wiadomości SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Usługa Azure Communication Services umożliwia wysyłanie i odbieranie wiadomości SMS przy użyciu bibliotek klienckich programu SMS usług komunikacyjnych. Te biblioteki klienckie mogą służyć do obsługi scenariuszy obsługi klienta, przypomnień o terminie, uwierzytelniania dwuskładnikowego i innych potrzeb związanych z łącznością w czasie rzeczywistym. Usługi komunikacyjne programu SMS umożliwiają niezawodne wysyłanie komunikatów przy jednoczesnym ujawnianiu informacji o dostarczaniu i częstotliwości odpowiedzi w ramach kampanii.

Najważniejsze funkcje bibliotek klienckich programu SMS usługi Azure Communication Services obejmują:

-  **Proste** środowisko konfiguracji umożliwiające dodawanie funkcji programu SMS do aplikacji.
- Obsługa komunikatów **o wysokiej szybkości** przez wiele bezpłatnych numerów dla A2P (aplikacji do osoby) w Stany Zjednoczone.
- **Dwukierunkowe** konwersacje obsługujące scenariusze, takie jak obsługa klienta, alerty i przypomnienia o terminie.
- **Niezawodne dostarczanie** dzięki raportom dostarczania w czasie rzeczywistym dla komunikatów wysyłanych z aplikacji.
- **Analiza** umożliwiająca śledzenie wzorców użycia i zaangażowania klientów.
- **Wycofaj** obsługę techniczną, aby automatycznie wykrywać i uwzględniać rezygnację dla numerów bezpłatnych. Opłaty za bezpłatne numery telefonów US są narzucane i wymuszane przez przewoźników Stanów Zjednoczonych.
  - Zatrzymaj — Jeśli odbiorca wiadomości tekstowej chce zrezygnować z akceptacji, może wysłać element "STOP" do numeru bezpłatnego. Przewoźnik wysyła następującą odpowiedź domyślną dla zatrzymywania: *"Network MSG: odpowiedź na słowo" Stop ", która blokuje wszystkie teksty wysyłane z tego numeru. Wróć do tyłu tekst "Cofnij zatrzymanie", aby ponownie otrzymywać komunikaty ".*
  - Uruchom/Cofnij — Jeśli odbiorca chce ponownie subskrybować wiadomości SMS za pośrednictwem numeru bezpłatnego, może wysłać wiadomość "Rozpocznij" lub "odtrzymywać do numeru bezpłatnego. Przewoźnik wysyła następującą domyślną odpowiedź dotyczącą uruchamiania/odtrzymywania: *"komunikat sieciowy: odpowiedź" nie zatrzyma "i rozpocznie otrzymywanie komunikatów ponownie z tej liczby".*
  - Usługa Azure Communication Services wykryje komunikat zatrzymania i zablokuje wszystkie dalsze komunikaty odbiorcy. Raport dostarczania wskaże dostarczenie zakończone niepowodzeniem z komunikatem o stanie jako "zablokowany nadawca dla danego odbiorcy".
  - Komunikaty zatrzymania, cofania i uruchamiania będą przekazywane z powrotem do użytkownika. Usługi komunikacyjne platformy Azure zachęcają do monitorowania i wdrażania tych postanowień w celu zapewnienia, że nie będą podejmowane dalsze próby wysłania komunikatów do adresatów, którzy wybiorą powiadomienie z Twojej komunikacji.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wysyłania wiadomości SMS](../../quickstarts/telephony-sms/send.md)

Następujące dokumenty mogą być interesujące:

- Zapoznaj się z [biblioteką klienta programu SMS](../telephony-sms/sdk-features.md)
- Uzyskaj [numer telefonu](../../quickstarts/telephony-sms/get-phone-number.md) z obsługą wiadomości SMS
- [Typy numerów telefonów w usłudze Azure Communications Services](../telephony-sms/plan-solution.md)

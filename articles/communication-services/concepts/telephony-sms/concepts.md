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
ms.openlocfilehash: e879b7938eb778d4cdbbef4a970325501a6124db
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932152"
---
# <a name="sms-concepts"></a>Pojęcia dotyczące wiadomości SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Usługa Azure Communication Services umożliwia wysyłanie i odbieranie wiadomości SMS przy użyciu zestawów SDK SMS usług komunikacyjnych. Te zestawy SDK mogą służyć do obsługi scenariuszy obsługi klienta, przypomnienia o terminie, uwierzytelniania dwuskładnikowego i inne potrzeby komunikacji w czasie rzeczywistym. Usługi komunikacyjne programu SMS umożliwiają niezawodne wysyłanie komunikatów przy jednoczesnym udostępnieniu metryk dostarczania i odpowiedzi.

Najważniejsze funkcje zestawów SDK SMS usługi Azure Communication Services:

-  **Proste** środowisko konfiguracji umożliwiające dodawanie funkcji programu SMS do aplikacji.
- Obsługa komunikatów **o wysokiej szybkości** przez wiele bezpłatnych numerów dla A2P (aplikacji do osoby) w Stany Zjednoczone.
- **Obsługa komunikatów zbiorczych** umożliwia wysyłanie komunikatów jednocześnie do wielu adresatów.
- **Dwukierunkowe** konwersacje obsługujące scenariusze, takie jak obsługa klienta, alerty i przypomnienia o terminie.
- **Niezawodne dostarczanie** dzięki raportom dostarczania w czasie rzeczywistym dla komunikatów wysyłanych z aplikacji.
- **Analiza** umożliwiająca śledzenie wzorców użycia programu SMS.
- **Wycofaj** obsługę techniczną, aby automatycznie wykrywać i uwzględniać rezygnację dla numerów bezpłatnych. Opłaty za bezpłatne numery telefonów US są narzucane i wymuszane przez przewoźników Stanów Zjednoczonych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wysyłania wiadomości SMS](../../quickstarts/telephony-sms/send.md)

Następujące dokumenty mogą być interesujące:

- Zapoznaj się z [zestawem SDK programu SMS](../telephony-sms/sdk-features.md)
- Uzyskaj [numer telefonu](../../quickstarts/telephony-sms/get-phone-number.md) z obsługą wiadomości SMS
- [Typy numerów telefonów w usłudze Azure Communications Services](../telephony-sms/plan-solution.md)

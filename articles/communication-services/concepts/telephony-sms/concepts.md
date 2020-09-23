---
title: Koncepcje programu SMS w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Zapoznaj się z tematami dotyczącymi usługi Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e5cfc1e27bae10a1c67e4506afe9db825664785f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947409"
---
# <a name="sms-concepts"></a>Pojęcia dotyczące programu SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Usługa Azure Communication Services umożliwia wysyłanie i odbieranie wiadomości SMS przy użyciu bibliotek klienckich programu SMS usług komunikacyjnych. Te biblioteki klienckie mogą służyć do obsługi scenariuszy obsługi klienta, przypomnień o terminie, uwierzytelniania dwuskładnikowego i innych potrzeb związanych z łącznością w czasie rzeczywistym. Usługi komunikacyjne programu SMS umożliwiają niezawodne wysyłanie komunikatów przy jednoczesnym ujawnianiu informacji o dostarczaniu i częstotliwości odpowiedzi w ramach kampanii.

Najważniejsze funkcje bibliotek klienckich programu SMS usługi Azure Communication Services obejmują:

-  **Proste** środowisko konfiguracji umożliwiające dodawanie funkcji programu SMS do aplikacji.
- Obsługa komunikatów **o wysokiej szybkości** przez wiele bezpłatnych numerów dla A2P (aplikacji do osoby) w Stany Zjednoczone.
- **Dwukierunkowe** konwersacje obsługujące scenariusze, takie jak obsługa klienta, alerty i przypomnienia o terminie.
- **Niezawodne dostarczanie** dzięki raportom dostarczania w czasie rzeczywistym dla komunikatów wysyłanych z aplikacji.
- **Analiza** umożliwiająca śledzenie wzorców użycia i zaangażowania klientów.
- **Wycofaj** obsługę techniczną, aby automatycznie wykrywać i uwzględniać rezygnację dla numerów bezpłatnych. Usługi komunikacyjne wykrywają komunikaty zatrzymania i uruchamiania oraz wysyłają następujące domyślne odpowiedzi do użytkowników końcowych: 
  - STOP — *"pomyślnie Anulowano subskrypcję komunikatów z tej liczby. Odpowiedz na PONOWNą subskrypcję. "*
  - START — *"pomyślnie zasubskrybowano ponownie komunikaty z tej liczby. Odpowiedz na anulowanie subskrypcji ".*
  - Komunikaty zatrzymania i uruchamiania będą przekazywane z powrotem do użytkownika. Usługi komunikacyjne platformy Azure zachęcają do monitorowania i wdrażania tych postanowień, aby upewnić się, że żadne dalsze komunikaty nie są wysyłane do adresatów, którzy wybiorą powiadomienie o komunikacji.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wysyłania wiadomości SMS](../../quickstarts/telephony-sms/send.md)

Następujące dokumenty mogą być interesujące:

- Zapoznaj się z [biblioteką klienta programu SMS](../telephony-sms/sdk-features.md)
- Uzyskaj [numer telefonu](../../quickstarts/telephony-sms/get-phone-number.md) z obsługą wiadomości SMS
- [Planowanie rozwiązania programu SMS](../telephony-sms/plan-solution.md)

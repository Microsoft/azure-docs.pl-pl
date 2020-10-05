---
title: Zaplanuj rozwiązanie do obsługi połączeń telefonicznych i SMS usługi Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się, jak efektywnie zaplanować korzystanie z numerów telefonów i telefonowania.
author: stkozak
manager: rampras
services: azure-communication-services
ms.author: stkozak
ms.date: 06/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39f88ab8b735438f60d8e20513ea5cbda43d41ee
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947410"
---
# <a name="plan-your-telephony-and-sms-solution"></a>Planowanie rozwiązania do obsługi telefonii i wiadomości SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

W tym dokumencie opisano różne plany i typy numerów telefonicznych, które mają być oferowane przez usługi Azure Communication Services. Przeprowadzimy procedurę przepływu decyzji, aby ułatwić wybranie dostawcy usługi głosowej, typów numerów telefonów, planów i możliwości dostępnych za pomocą usług komunikacyjnych.

## <a name="about-phone-numbers-in-azure-communications-services"></a>Informacje o numerach telefonów w usłudze Azure Communications Services

Usługi Azure Communication Services umożliwiają używanie numerów telefonów do umieszczania i odbierania połączeń telefonicznych i wiadomości SMS. Te numery telefonów mogą służyć do konfigurowania identyfikatora rozmówcy dla wywołań wychodzących umieszczonych przez usługę.
  
Jeśli nie masz istniejącego numeru telefonu do zaimportowania do rozwiązania usług komunikacyjnych, najprostszym sposobem na rozpoczęcie pracy jest uzyskanie nowego numeru telefonu z usług Azure Communications Services w ciągu kilku minut.

Jeśli masz istniejący numer telefonu, który ma być nadal używany w rozwiązaniu (na przykład 1-800 – firma), możesz przenieść numer telefonu z istniejącego dostawcy do usług komunikacyjnych.

Poniższy diagram ułatwia nawigowanie po dostępnych opcjach:

:::image type="content" source="../media/plan-solution/decision-tree-basic.png" alt-text="Diagram przedstawiający sposób podejmowania decyzji dotyczących numerów telefonów.":::

Teraz Przejrzyjmy typy numerów telefonów i możliwości dostępne za pomocą usług komunikacyjnych. 

## <a name="microsoft-direct-offer-of-phone-numbers-and-capabilities"></a>Bezpośrednia oferta dla numerów telefonów i możliwości firmy Microsoft

Usługi komunikacyjne platformy Azure zapewniają doskonałą elastyczność dla deweloperów. W przypadku większości numerów telefonów możemy skonfigurować zestaw planów "a La koszyka". Niektórzy deweloperzy potrzebują tylko przychodzącego planu wywołującego; Niektóre z nich mogą wymagać wywołań przychodzących i wychodzących planów SMS. Te plany mogą być wybierane jako dzierżawy i/lub porty numerów telefonów w ramach usług komunikacyjnych.

Dostępne plany zależą od typu kraju i numeru telefonu, w którym pracujesz. Poniższy diagram przedstawia przepływ decyzji: dostępne plany zależą od typu kraju i numeru telefonu, w którym pracujesz.

<!-- Tami/team have rejected this multiple times despite updates, says it needs to be higher res - need to work with her to get approval for this image. Commenting out to move our staging forward. :::image type="content" source="../../media/example-decision-flow.png" alt-text="Diagram przedstawiający sposób podejmowania decyzji dotyczących numerów telefonów."::: -->

Przed wybraniem typu numeru telefonu zapoznaj się z międzynarodowym planem numerowania numerów telefonów.

### <a name="optional-reading-international-public-telecommunication-numbering-plan-e164"></a>Opcjonalny odczyt. Międzynarodowy Publiczny plan numerowania telekomunikacji (E. 164)

> [!NOTE]
> Zalecamy przejrzenie tych informacji nawet w przypadku znajomości planu numerowania telefonu E. 164, aby lepiej zrozumieć typy liczb i możliwości oferowane przez oferowaną usługę Azure Communication Services Direct.

Międzynarodowy Publiczny plan numerowania telekomunikacji jest zdefiniowany w Międzynarodowej Unii Telekomunikacyjnej (ITU). Liczby zgodne są ograniczone do maksymalnie 15 cyfr.

Numer telefonu składa się z

-   Prefiks "+"
-   Międzynarodowy Prefiks wybierania numeru lub kod kraju/regionu (jeden, dwa lub trzy cyfry) 
-   *(Opcjonalnie)* Krajowy kod docelowy lub plan numerowania, często określany jako numer kierunkowy. Długość tego kodu zależy od kraju. W Stany Zjednoczone jest to trzy cyfry. W Australii i Nowej Zelandii jest to jedna cyfra. Niemcy, Japonia, Meksyk i inne kraje mają zmienne długości kodów obszaru. Na przykład, w Niemczech, kod obszaru może znajdować się w dowolnym miejscu od dwóch do pięciu cyfr, podczas gdy Japonia może znajdować się w dowolnym miejscu od jednej do pięciu cyfr.
-   Numer abonenta

> [!NOTE]
> Powyższa klasyfikacja nie jest w pełni zgodna ze standardem ITU E. 164 i ma na celu dostarczenie uproszczonego opisu. Na przykład numer abonenta jest podzielony na Standard. Jeśli interesujesz się bardziej szczegółowo międzynarodowym planem numerowania, możesz zacząć od [normy ITU E. 164 Standard](https://www.itu.int/rec/T-REC-E.164) .  

Poniżej przedstawiono kilka przykładów, które pomogą lepiej zrozumieć plan numerowania:

Regionalny numer telefonu w Stanach Zjednoczonych:

:::image type="content" source="../media/plan-solution/regional-us.png" alt-text="Diagram przedstawiający sposób podejmowania decyzji dotyczących numerów telefonów.":::

Regionalny numer telefonu w Kanadzie:

:::image type="content" source="../media/plan-solution/regional-canada.png" alt-text="Diagram przedstawiający sposób podejmowania decyzji dotyczących numerów telefonów.":::

Numer bezpłatny w Ameryka Północna regionie:

:::image type="content" source="../media/plan-solution/tollfree-us.png" alt-text="Diagram przedstawiający sposób podejmowania decyzji dotyczących numerów telefonów.":::

Numer telefonu komórkowego w Wielkiej Brytanii:

:::image type="content" source="../media/plan-solution/mobile-uk.png" alt-text="Diagram przedstawiający sposób podejmowania decyzji dotyczących numerów telefonów.":::

Następnie Przejrzyjmy określone typy numerów telefonów dostępne w usłudze Azure Communications Services.

## <a name="phone-number-types-in-azure-communication-services"></a>Typy numerów telefonów w usłudze Azure Communications Services

Firma Microsoft oferuje regionalne i bezpłatne plany dla krótkich kodów i numerów telefonów komórkowych w krajach, w których ma to zastosowanie.

W poniższej tabeli zestawiono te typy numerów telefonów: 

| Typ numeru telefonu | Przykład                              | Dostępność kraju    | Typowy przypadek użycia                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Regionalne          | + 1 (kod obszaru geograficznego) XXX XX XX  | USA, Kanada, Portoryko | Przypisywanie numerów telefonów użytkownikom w aplikacjach lub przypisywanie do interaktywnych systemów odpowiedzi głosowych (IVR)/botów |
| Bezpłatny — bezpłatny         | + 1 (bezpłatny *numer*kierunkowy) XXX XX XX | USA, Kanada, Portoryko | Przypisywanie do interaktywnych systemów odpowiedzi głosowych (IVR)/botów, aplikacji SMS                                        |

## <a name="plans"></a>Plany 

Przyjrzyjmy się możliwościom, które można włączyć dla numerów telefonów. Te możliwości są różne w zależności od kraju ze względu na wymagania prawne. Usługi komunikacyjne platformy Azure oferują następujące możliwości:

- **Jednokierunkowe wychodzące wiadomości SMS**, przydatne w przypadku scenariuszy uwierzytelniania powiadomień i dwuskładnikowych.
- **Dwukierunkowe i wychodzące wiadomości SMS**— wstępnie zdefiniowany pakiet, który umożliwia wysyłanie i odbieranie wiadomości SMS w ramach jednego planu.
- **Połączenie PSTN**, możesz wybrać wywołanie przychodzące i użyć identyfikatora wywołującego, aby umieścić wywołania wychodzące.

## <a name="next-steps"></a>Następne kroki

### <a name="quickstarts"></a>Przewodniki Szybki start

- [Pobierz numer telefonu](../../quickstarts/telephony-sms/get-phone-number.md)
- [Umieść wywołanie](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Wysyłanie wiadomości SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Dokumentacja dotycząca pojęć

- [Pojęcia dotyczące głosu i wideo](../voice-video-calling/about-call-types.md)
- [Przepływy wywołań i przepływy SMS](../call-flows.md)
- [Cennik](../pricing.md)

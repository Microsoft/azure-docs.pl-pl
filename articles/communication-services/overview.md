---
title: Co to są usługi Azure Communication Services?
description: Dowiedz się, w jaki sposób usługi Azure Communications Services pomagają opracowywać rozbudowane środowiska użytkownika w czasie rzeczywistym.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: fe580848e3bbea701718463123a6bbcd4848a9f3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564606"
---
# <a name="what-is-azure-communication-services"></a>Co to są usługi Azure Communication Services?

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

> [!IMPORTANT]
> Aplikacje skompilowane przy użyciu usług Azure Communication Services mogą komunikować się z firmą Microsoft Teams. Aby dowiedzieć się więcej, zapoznaj się z dokumentacją [międzyoperacyjności zespołów](./quickstarts/voice-video-calling/get-started-teams-interop.md) .


Usługi Azure Communication Services umożliwiają łatwe dodawanie do aplikacji funkcji komunikacji głosowej, wideo i telefonicznych w czasie rzeczywistym. Zestawy SDK usług komunikacyjnych umożliwiają również Dodawanie funkcji rozmowy i programu SMS do rozwiązań komunikacyjnych.

<br>

> [!VIDEO https://www.youtube.com/embed/apBX7ASurgM]

<br>
<br>

Usług komunikacyjnych można używać do komunikacji głosowej, wideo, tekstu i danych w różnych scenariuszach:

- Komunikacja między przeglądarkami, przeglądarką do aplikacji i komunikacji między aplikacjami
- Użytkownicy korzystający z usługi botów lub innych usług
- Użytkownicy i botów, które współdziałają z publiczną przełączaną siecią telefonii

Obsługiwane są scenariusze mieszane. Na przykład aplikacja usług komunikacyjnych może w tym samym czasie korzystać z przeglądarek i tradycyjnych urządzeń telefonicznych. Usługi komunikacyjne mogą być również połączone z Azure Bot Service w celu tworzenia interaktywnych systemów odpowiedzi głosowych (IVR) opartych na bot.

## <a name="common-scenarios"></a>Typowe scenariusze

Następujące zasoby są doskonałym miejscem, aby rozpocząć pracę z usługami Azure Communications Services. 
<br>

| Zasób                               |Opis                           |
|---                                    |---                                   |
|**[Tworzenie zasobu usług Communication Services](./quickstarts/create-communication-resource.md)**|Możesz rozpocząć korzystanie z usług Azure Communication Services przy użyciu biblioteki klienta Azure Portal lub usług komunikacyjnych, aby zainicjować obsługę pierwszego zasobu usług komunikacyjnych. Gdy masz parametry połączenia zasobu usług komunikacyjnych, możesz udostępnić tokeny dostępu pierwszego użytkownika.|
|**[Pobierz numer telefonu](./quickstarts/telephony-sms/get-phone-number.md)**|Za pomocą usług Azure Communications Services można udostępniać i zwalniać numery telefonów. Te numery telefonów mogą służyć do inicjowania wywołań wychodzących i tworzenia rozwiązań komunikacyjnych programu SMS.|
|**[Wyślij wiadomość SMS z aplikacji](./quickstarts/telephony-sms/send.md)**|Biblioteka klienta SMS usługi Azure Communication Services umożliwia wysyłanie i odbieranie wiadomości SMS z aplikacji .NET i JavaScript.|

Po utworzeniu zasobu usług komunikacyjnych możesz zacząć tworzyć scenariusze klientów, takie jak nawiązanie głosu i wideo lub Rozmowa z tekstem.

| Zasób                               |Opis                           |
|---                                    |---                                   |
|**[Tworzenie pierwszego tokenu dostępu użytkownika](./quickstarts/access-tokens.md)**|Tokeny dostępu użytkowników są używane do uwierzytelniania usług względem zasobu usług Azure Communications Services. Te tokeny są inicjowane i ponownie wystawiane przy użyciu biblioteki klienta usług komunikacyjnych.|
|**[Wprowadzenie do wywoływania głosu i wideo](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Usługi Azure Communication Services umożliwiają dodawanie połączeń głosowych i wideo do aplikacji przy użyciu biblioteki klienta wywołującego. Ta biblioteka jest obsługiwana przez protokołu WebRTC i umożliwia ustanawianie komunikacji równorzędnej, multimedialnej w czasie rzeczywistym w aplikacjach.|
|**[Dołączanie aplikacji do obsługi połączeń głosowych do spotkania w usłudze Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Usługi Azure Communication Services mogą służyć do tworzenia niestandardowych środowisk spotkań, które współpracują z usługą Microsoft Teams. Użytkownicy rozwiązań usług komunikacyjnych mogą współdziałać z zespołami uczestników za pośrednictwem głosu, wideo, rozmowy i udostępniania ekranu.|
|**[Wprowadzenie do rozmowy](./quickstarts/chat/get-started.md)**|W celu zintegrowania rozmowy w czasie rzeczywistym z aplikacjami można użyć biblioteki klienta czatu usługi Azure Communication Services.|

## <a name="samples"></a>Samples

Poniższe przykłady przedstawiają kompleksowe wykorzystanie zestawów SDK usług Azure Communication Services. Możesz korzystać z tych przykładów do uruchamiania własnych rozwiązań usług komunikacyjnych.
<br>

| Nazwa Przykładowa                               | Opis                           |
|---                                    |---                                   |
|**[Grupa wywołująca przykład Hero](./samples/calling-hero-sample.md)**|Zobacz, jak zestawy SDK usług komunikacyjnych mogą być używane do tworzenia środowiska wywołującego grupę.|
|**[Przykład Hero rozmowy z grupą](./samples/chat-hero-sample.md)**|Zobacz, jak zestawy SDK usług komunikacyjnych mogą służyć do kompilowania środowiska rozmowy grupowej.|


## <a name="platforms-and-sdks"></a>Platformy i zestawy SDK

Następujące zasoby ułatwią zapoznanie się z zestawami SDK usług Azure Communication Services:

| Zasób                               | Opis                           |
|---                                    |---                                   |
|**[Biblioteki klienta i interfejsy API REST](./concepts/sdk-options.md)**|Możliwości usług Azure Communication Services są koncepcyjnie zorganizowane w sześć obszarów, z których każdy jest reprezentowany przez zestaw SDK. Można zdecydować, które zestawy SDK mają być używane w zależności od potrzeb komunikacji w czasie rzeczywistym.|
|**[Omówienie wywoływania zestawu SDK](./concepts/voice-video-calling/calling-sdk-features.md)**|Zapoznaj się z omówieniem zestawu SDK wywoływania usług komunikacyjnych.|
|**[Omówienie zestawu SDK rozmowy](./concepts/chat/sdk-features.md)**|Zapoznaj się z omówieniem zestawu SDK rozmowy usług komunikacyjnych.|
|**[Omówienie zestawu SDK programu SMS](./concepts/telephony-sms/sdk-features.md)**|Zapoznaj się z omówieniem zestawu SDK programu SMS dla usług komunikacyjnych.|

## <a name="compare-azure-communication-services"></a>Porównanie usług Azure Communication Services

Istnieją dwa inne produkty do komunikacji firmy Microsoft, które można wziąć pod uwagę w przypadku, gdy nie są w tej chwili bezpośrednio obsługiwane usługi komunikacyjne:

 - [Microsoft Graph interfejsy API komunikacji w chmurze](/graph/cloud-communications-concept-overview) umożliwiają organizacjom tworzenie środowisk komunikacyjnych powiązanych z Azure Active Directory użytkownikami z licencjami Microsoft 365. Jest to idealne rozwiązanie w przypadku aplikacji powiązanych z Azure Active Directoryą lub miejsca, w którym chcesz zwiększyć produktywność w programie Microsoft Teams. Istnieją także interfejsy API do kompilowania aplikacji i dostosowywania w ramach [środowiska zespołu.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - [Usługa Azure PlayFaber](/gaming/playfab/features/multiplayer/networking/) upraszcza Dodawanie rozmowy o małym opóźnieniu i komunikacji danych z grami. W przypadku korzystania z usług komunikacyjnych w programie do gier i połączeń z usługą łączności PlayFab jest to dostosowana opcja i bezpłatna na konsoli Xbox.


## <a name="next-steps"></a>Następne kroki

 - [Tworzenie zasobu usług Communication Services](./quickstarts/create-communication-resource.md)

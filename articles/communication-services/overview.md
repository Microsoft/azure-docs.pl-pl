---
title: Co to są usługi Azure Communication Services?
description: Dowiedz się, Azure Communication Services pomaga tworzyć rozbudowane środowisko użytkownika dzięki komunikacji w czasie rzeczywistym.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8c2559315e3bfffc41c138be6826adae95dd7b07
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588109"
---
# <a name="what-is-azure-communication-services"></a>Co to są usługi Azure Communication Services?

Azure Communication Services umożliwia łatwe dodawanie do aplikacji komunikacji głosowej, wideo i telefonicznej w czasie rzeczywistym. Communication Services SDK umożliwiają również dodawanie funkcji wiadomości SMS do rozwiązań komunikacyjnych. Azure Communication Services jest niezależny od tożsamości i masz pełną kontrolę nad tym, jak użytkownicy końcowi są identyfikowani i uwierzytelniani. Możesz połączyć ludzi z płaszczyzną danych komunikacyjnych lub usługami (botami).

Aplikacje obejmują:

- **Firma-konsument (B2C).** Pracownicy i usługi firmy mogą wchodzić w interakcje z użytkownikami za pomocą głosu, wideo i czatu tekstowego w niestandardowej przeglądarce lub aplikacji mobilnej. Organizacja może wysyłać i odbierać wiadomości SMS lub obsługiwać interaktywny system odpowiedzi głosowych (IVR) przy użyciu numeru telefonu nabytego za pośrednictwem platformy Azure. [Integracja z usługą Microsoft Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md) umożliwia użytkownikom dołączanie do spotkań usługi Teams hostowanych przez pracowników; Idealne rozwiązanie w przypadku scenariuszy zdalnej opieki zdrowotnej, bankowości i pomocy technicznej produktów, w których pracownicy mogą już znać usługę Teams.
- **Konsument do konsumenta.** Twórz atrakcyjne przestrzenie społecznościowe do interakcji między użytkownikami za pomocą głosu, wideo i czatu tekstowego. Dowolny typ interfejsu użytkownika można utworzyć na podstawie Azure Communication Services SDK, ale dostępne są kompletne przykłady aplikacji i zasoby interfejsu użytkownika, które ułatwiają szybkie rozpoczynanie pracy.

Aby dowiedzieć się więcej, zapoznaj się z naszym wideo [microsoft mechanics](https://www.youtube.com/watch?v=apBX7ASurgM) lub zasobami, do których link znajduje się poniżej.

## <a name="common-scenarios"></a>Typowe scenariusze

<br>

| Zasób                               |Opis                           |
|---                                    |---                                   |
|**[Tworzenie zasobu usług Communication Services](./quickstarts/create-communication-resource.md)**|Rozpocznij korzystanie Azure Communication Services użyciu zestawu SDK Azure Portal lub Communication Services, aby aprowizować pierwszy Communication Services zasobów. Gdy masz już Communication Services parametrów połączenia zasobu, możesz aprowizować tokeny dostępu pierwszego użytkownika.|
|**[Uzyskiwanie numeru telefonu](./quickstarts/telephony-sms/get-phone-number.md)**|Za pomocą Azure Communication Services numerów telefonów można aprowizować i zwalniać numery telefonów. Te numery telefonów mogą służyć do inicjowania lub odbierania połączeń telefonicznych i tworzenia rozwiązań SMS.|
|**[Wysyłanie wiadomości SMS z aplikacji](./quickstarts/telephony-sms/send.md)**|Zestaw SDK Azure Communication Services SMS jest używany do wysyłania i odbierania wiadomości SMS z aplikacji usług.|

Po utworzeniu zasobu Communication Services można rozpocząć tworzenie scenariuszy klienta, takich jak połączenia głosowe i wideo lub czat tekstowy:

| Zasób                               |Opis                           |
|---                                    |---                                   |
|**[Tworzenie pierwszego tokenu dostępu użytkownika](./quickstarts/access-tokens.md)**|Tokeny dostępu użytkownika są używane do uwierzytelniania klientów względem Azure Communication Services zasobów. Te tokeny są aprowowane i ponownie wyasyłane przy użyciu Communication Services SDK.|
|**[Wprowadzenie do połączeń głosowych i wideo](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure Communication Services umożliwia dodawanie połączeń głosowych i wideo do przeglądarki lub aplikacji natywnych przy użyciu zestawu SDK wywoływania. |
|**[Dołączanie aplikacji do obsługi połączeń głosowych do spotkania w usłudze Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Communication Services mogą służyć do tworzenia niestandardowych spotkań, które współdziałają z usługą Microsoft Teams. Użytkownicy Twoich Communication Services mogą wchodzić w interakcje z uczestnikami aplikacji Teams za pośrednictwem głosu, wideo, czatu i udostępniania ekranu.|
|**[Wprowadzenie do czatu](./quickstarts/chat/get-started.md)**|Zestaw AZURE COMMUNICATION SERVICES Chat SDK służy do dodawania rozbudowanego czatu tekstowego w czasie rzeczywistym do aplikacji.|

## <a name="samples"></a>Samples

W poniższych przykładach pokazano, jak w Azure Communication Services. Użyj tych przykładów, aby uruchamiać własne Communication Services rozwiązania.
<br>

| Nazwa przykładu                               | Opis                           |
|---                                    |---                                   |
|**[Przykład hero wywoływania grupy](./samples/calling-hero-sample.md)**| Pobierz przykładową aplikację zaprojektowaną do grupowego wywoływania dla przeglądarek, urządzeń z systemami iOS i Android. |
|**[Przykład czatów grupowych](./samples/chat-hero-sample.md)**| Pobierz przykładową aplikację dla grupowego czatu tekstowego dla przeglądarek. |


## <a name="platforms-and-sdk-libraries"></a>Platformy i biblioteki zestawu SDK

Dowiedz się więcej na temat Azure Communication Services sdk z poniższymi zasobami. Interfejsy API REST są dostępne dla większości funkcji, jeśli chcesz tworzyć własnych klientów lub w inny sposób uzyskać dostęp do usługi za pośrednictwem Internetu.

| Zasób                               | Opis                           |
|---                                    |---                                   |
|**[Biblioteki zestawu SDK i interfejsy API REST](./concepts/sdk-options.md)**|Azure Communication Services są koncepcyjnie zorganizowane w sześć obszarów, z których każdy jest reprezentowany przez zestaw SDK. W zależności od potrzeb komunikacji w czasie rzeczywistym możesz zdecydować, których bibliotek zestawu SDK użyć.|
|**[Omówienie wywoływania zestawu SDK](./concepts/voice-video-calling/calling-sdk-features.md)**|Zapoznaj się z Communication Services zestawu SDK wywoływania aplikacji.|
|**[Omówienie zestawu SDK czatu](./concepts/chat/sdk-features.md)**|Przejrzyj omówienie zestawu SDK Communication Services Chat.|
|**[Omówienie zestawu SDK programu SMS](./concepts/telephony-sms/sdk-features.md)**|Przejrzyj omówienie zestawu SDK Communication Services SMS.|

## <a name="other-microsoft-communication-services"></a>Inne rozwiązania firmy Microsoft Communication Services

Istnieją dwa inne produkty do komunikacji firmy Microsoft, których użycie można rozważyć, które nie współdziałają Communication Services obecnie:

 - [Microsoft Graph API komunikacji w chmurze](/graph/cloud-communications-concept-overview) umożliwiają organizacjom tworzenie obsługi komunikacji powiązanej z Azure Active Directory użytkownikami Microsoft 365 licencji. Jest to idealne rozwiązanie w przypadku aplikacji powiązanych Azure Active Directory lub w przypadku, gdy chcesz rozszerzyć środowisko pracy w aplikacji Microsoft Teams. Istnieją również interfejsy API do tworzenia aplikacji i dostosowywania w ramach [interfejsu użytkownika usługi Teams.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - [Usługa Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) upraszcza dodawanie do gier czatów i komunikacji danych o małych opóźnieniach. Chociaż za pomocą usługi PlayFab można Communication Services do gier i systemów sieciowych, playFab jest opcją dostosowaną i bezpłatną na konsoli Xbox.


## <a name="next-steps"></a>Następne kroki

 - [Tworzenie zasobu usług Communication Services](./quickstarts/create-communication-resource.md)

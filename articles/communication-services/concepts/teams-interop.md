---
title: Współdziałanie z zespołami
titleSuffix: An Azure Communication Services concept document
description: Dołącz do spotkań zespołów
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 10/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d369e976ab1ba4e33f5eb21edb92054678f9040f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101655738"
---
# <a name="teams-interoperability"></a>Współdziałanie usługi Teams

> [!IMPORTANT]
> Aby włączyć/wyłączyć [współdziałanie dzierżawcy dla zespołów](../concepts/teams-interop.md), Wypełnij [ten formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Usługi Azure Communication Services mogą służyć do tworzenia niestandardowych środowisk spotkań, które współpracują z usługą Microsoft Teams. Użytkownicy rozwiązań usług komunikacyjnych mogą współdziałać z zespołami uczestników za pośrednictwem głosu, wideo, rozmowy i udostępniania ekranu.

Współdziałanie zespołów pozwala tworzyć niestandardowe aplikacje łączące użytkowników z zespołami spotkań. Użytkownicy aplikacji niestandardowych nie muszą mieć Azure Active Directory tożsamości ani zespołów, aby móc korzystać z tej funkcji. Jest to idealne rozwiązanie w celu nadania pracownikom (znającym zespoły) i użytkownikom zewnętrznym (przy użyciu niestandardowego środowiska aplikacji) wraz z bezproblemowe środowisko spotkań. Na przykład:

1. Pracownicy wykorzystują zespoły do zaplanowania spotkania 
1. Szczegóły spotkania są udostępniane użytkownikom zewnętrznym za pomocą niestandardowej aplikacji.
   * **Używanie interfejs API programu Graph** Niestandardowa aplikacja usług komunikacyjnych używa interfejsów API Microsoft Graph, aby uzyskać dostęp do szczegółów spotkania do udostępnienia. 
   * **Korzystanie z innych opcji** Na przykład link do spotkania można skopiować z kalendarza w usłudze Microsoft Teams.
1. Użytkownicy zewnętrzni wykorzystują aplikację niestandardową do dołączania do spotkania zespołów (za pośrednictwem bibliotek usługi komunikacyjnej wywołujących i łączących)

Architektura wysokiego poziomu dla tego przypadku użycia wygląda następująco: 

![Architektura dla zespołów międzyoperacyjnych](./media/call-flows/teams-interop.png)

Niektóre zespoły mogą korzystać z funkcji, takich jak podniesione, tryb razem i pokoje zagadnień, będą dostępne tylko dla użytkowników zespołów, ale aplikacja niestandardowa będzie miała dostęp do podstawowych funkcji związanych z dźwiękiem, wideo, rozmową i udostępnianiem ekranu. Czat ze spotkaniem będzie dostępny dla użytkownika aplikacji niestandardowej podczas rozmowy. Nie będą one mogły wysyłać ani odbierać komunikatów przed przyłączeniem lub po opuszczeniu wywołania. 

Gdy użytkownik usług komunikacyjnych dołączają się do spotkania zespołów, nazwa wyświetlana podana przez wywołującą bibliotekę klienta będzie pokazywana użytkownikom zespołów. Użytkownik usług komunikacyjnych będzie w przeciwnym razie traktowany jak anonimowy użytkownik w zespołach.  Aplikacja niestandardowa powinna rozważyć uwierzytelnianie użytkowników i inne środki bezpieczeństwa w celu ochrony spotkań zespołów. Należy zastanowić się, że implikacje dla użytkowników anonimowych mogą dołączać do spotkań i korzystać z [przewodnika po zabezpieczeniach zespołów](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) w celu skonfigurowania możliwości dostępnych dla użytkowników anonimowych.

Usługa Communication Services Teams Interop jest obecnie w prywatnej wersji zapoznawczej. Gdy są ogólnie dostępne, użytkownicy usług komunikacyjnych będą traktowani jako "użytkownicy dostępu zewnętrznego". Dowiedz się więcej o dostępie do danych zewnętrznych w programie [Call, rozmowach i współpracy z osobami spoza organizacji w programie Microsoft Teams](/microsoftteams/communicate-with-users-from-other-organizations).

Użytkownicy usług komunikacyjnych mogą dołączać do spotkań zaplanowanych zespołów, o ile w [ustawieniach spotkania](/microsoftteams/meeting-settings-in-teams)włączono sprzężenia anonimowe.

## <a name="teams-in-government-clouds-gcc"></a>Zespoły w chmurach rządowych (w zatoce)
Współdziałanie usług komunikacyjnych platformy Azure nie jest zgodne z wdrożeniami zespołów z użyciem [Microsoft 365 chmury dla instytucji rządowych](/MicrosoftTeams/plan-for-government-gcc) . 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dołączanie aplikacji do obsługi połączeń głosowych do spotkania w usłudze Teams](../quickstarts/voice-video-calling/get-started-teams-interop.md)
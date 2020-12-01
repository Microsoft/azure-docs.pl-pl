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
ms.openlocfilehash: 914581e6610e027d9440495f56f4facfb1fb9a9a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349439"
---
# <a name="teams-interoperability"></a>Współdziałanie usługi Teams

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Usługi Azure Communication Services mogą służyć do tworzenia niestandardowych środowisk spotkań, które współpracują z usługą Microsoft Teams. Użytkownicy rozwiązań usług komunikacyjnych mogą współdziałać z zespołami uczestników za pośrednictwem głosu, wideo i udostępniania ekranu.

To współdziałanie umożliwia tworzenie niestandardowych aplikacji platformy Azure, które łączą użytkowników z zespołami spotkań. Użytkownicy aplikacji niestandardowych nie muszą mieć Azure Active Directory tożsamości ani zespołów, aby móc korzystać z tej funkcji. Jest to idealne rozwiązanie w celu nadania pracownikom (znającym zespoły) i użytkownikom zewnętrznym (przy użyciu niestandardowego środowiska aplikacji) wraz z bezproblemowe środowisko spotkań. Dzięki temu można tworzyć środowiska podobne do następujących:

1. Pracownicy wykorzystują zespoły do zaplanowania spotkania
2. Niestandardowa aplikacja usług komunikacyjnych używa Microsoft Graph interfejsów API do uzyskiwania dostępu do szczegółów spotkania
3. Szczegóły spotkania są udostępniane użytkownikom zewnętrznym za pomocą aplikacji niestandardowej
4. Użytkownicy zewnętrzni używają niestandardowej aplikacji do dołączania do spotkania zespołów (za pośrednictwem biblioteki klienta wywołującego usługi komunikacyjne)

Architektura wysokiego poziomu dla tego przypadku użycia wygląda następująco: 

![Architektura dla zespołów międzyoperacyjnych](..//media/call-flows/teams-interop.png)

Niektóre zespoły, takie jak podniesione, tryb razem i pokoje zagadnień, będą dostępne tylko dla użytkowników zespołów, aplikacja niestandardowa będzie miała dostęp do podstawowych możliwości udostępniania dźwięku, wideo i ekranu.

Gdy użytkownik usług komunikacyjnych dołączają się do spotkania zespołów, nazwa wyświetlana podana przez wywołującą bibliotekę klienta będzie pokazywana użytkownikom zespołów. Użytkownik usług komunikacyjnych będzie w przeciwnym razie traktowany jak anonimowy użytkownik w zespołach. Aplikacja niestandardowa powinna rozważyć uwierzytelnianie użytkowników i inne środki bezpieczeństwa w celu ochrony spotkań zespołów. Należy zastanowić się, że implikacje dla użytkowników anonimowych mogą dołączać do spotkań i korzystać z [przewodnika po zabezpieczeniach zespołów](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) w celu skonfigurowania możliwości dostępnych dla użytkowników anonimowych.

Użytkownicy usług komunikacyjnych mogą dołączać do spotkań zaplanowanych zespołów, o ile w [ustawieniach spotkania](/microsoftteams/meeting-settings-in-teams)włączono sprzężenia anonimowe.

## <a name="teams-in-government-clouds-gcc"></a>Zespoły w chmurach rządowych (w zatoce)
Współdziałanie usług Azure Communications Services nie jest dozwolone w tym momencie w zespołach wdrożeń korzystających z [Microsoft 365ych chmur dla instytucji rządowych](https://docs.microsoft.com/MicrosoftTeams/plan-for-government-gcc) . 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dołączanie aplikacji do obsługi połączeń głosowych do spotkania w usłudze Teams](../../quickstarts/voice-video-calling/get-started-teams-interop.md)

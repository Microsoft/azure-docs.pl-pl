---
title: Microsoft Graph API
description: Interfejs Microsoft Graph API to internetowy interfejs API RESTful, który umożliwia dostęp do zasobów usługi Microsoft Cloud.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 2e689e620a5aeb7c5028f1a1b30dd6def8e447ab
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529994"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Interfejs Microsoft Graph API to internetowy interfejs API RESTful, który umożliwia dostęp do zasobów usługi Microsoft Cloud. Po zarejestrowaniu aplikacji i uzyskania tokenów uwierzytelniania dla użytkownika lub usługi możesz rejestrować żądania do interfejsu API Microsoft Graph API. Aby uzyskać więcej informacji, [zobacz Omówienie Microsoft Graph](/graph/overview).

Microsoft Graph udostępnia interfejsy API REST i biblioteki klienckie w celu uzyskania dostępu do danych w następujących Microsoft 365 usługach:
- Microsoft 365 usług: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner i SharePoint
- Usługi Enterprise Mobility i Security: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager i Intune
- Windows 10 usługi: działania, urządzenia, powiadomienia
- Dynamics 365 Business Central

## <a name="versions"></a>Wersje

Microsoft Graph obecnie obsługuje dwie wersje: 1.0 i beta. Wersja 1.0 zawiera ogólnie dostępne interfejsy API. Użyj wersji 1.0 dla wszystkich aplikacji produkcyjnych. Wersja beta zawiera interfejsy API, które są obecnie dostępne w wersji zapoznawczej. Ze względu na to, że możemy wprowadzić istotne zmiany w naszych interfejsach API w wersji beta, zalecamy używanie wersji beta tylko do testowania aplikacji, które są w fazie projektowania. Nie używaj interfejsów API w wersji beta w aplikacjach produkcyjnych. Aby uzyskać więcej informacji, zobacz [Versioning, support, and breaking change policies for Microsoft Graph](/graph/versioning-and-support).

Aby rozpocząć korzystanie z interfejsów API w wersji beta, zobacz Microsoft Graph beta endpoint reference (Informacje o [punkcie końcowym beta)](/graph/api/overview?view=graph-rest-beta&preserve-view=true)

Aby rozpocząć korzystanie z interfejsów API w wersji 1.0, zobacz Microsoft Graph api REST 1.0 reference (Dokumentacja interfejsu API REST w wersji [1.0)](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)

## <a name="get-started"></a>Rozpoczęcie pracy

Aby odczytać lub zapisać w zasobie, takim jak użytkownik lub wiadomość e-mail, należy utworzyć żądanie, które wygląda następująco:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Aby uzyskać więcej informacji na temat elementów skonstruowanego żądania, zobacz [Używanie interfejsu API Microsoft Graph API](/graph/use-the-api)

Dostępne są przykłady szybkiego startu, które pokazują, jak uzyskać dostęp do możliwości interfejsu API Microsoft Graph API. Dostępne przykłady mają dostęp do dwóch usług z jednym uwierzytelnianiem: konto Microsoft i Outlook. Każdy przewodnik Szybki start uzyskuje dostęp do informacji konto Microsoft profilach użytkowników i wyświetla zdarzenia z kalendarza.
Przewodnik Szybki start obejmuje cztery kroki:
- Wybierz platformę
- Uzyskiwanie identyfikatora aplikacji (identyfikatora klienta)
- Kompilowanie przykładu
- Logowanie i wyświetlanie zdarzeń w kalendarzu

Po ukończeniu tego przewodnika Szybki start będziesz mieć aplikację, która jest gotowa do uruchomienia. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące Microsoft Graph Szybki start.](/graph/quick-start-faq) Aby rozpocząć pracę z przykładami, zobacz [Microsoft Graph Szybki start.](https://developer.microsoft.com/graph/quick-start)

## <a name="tools"></a>Narzędzia

Microsoft Graph Explorer to narzędzie internetowe, które umożliwia kompilowanie i testowanie żądań przy użyciu Microsoft Graph API. Dostęp do eksploratora Microsoft Graph można uzyskać pod: `https://developer.microsoft.com/graph/graph-explorer` .

Postman to narzędzie, które umożliwia również kompilowanie i testowanie żądań przy użyciu Microsoft Graph API. Program Postman można pobrać pod: `https://www.getpostman.com/` . Aby korzystać z Microsoft Graph w programie Postman, należy użyć kolekcji Microsoft Graph postman. Aby uzyskać więcej informacji, zobacz Use Postman with the Microsoft Graph API (Używanie [Microsoft Graph Postman z interfejsem API](/graph/use-postman)).

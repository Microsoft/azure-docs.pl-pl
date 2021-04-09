---
title: Microsoft Graph API
description: Interfejs API Microsoft Graph to interfejs API sieci Web RESTful, który umożliwia dostęp do Microsoft Cloud zasobów usługi.
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
ms.openlocfilehash: a799d7c32ad632303931adf838ca8e2e715b2adc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860188"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Interfejs API Microsoft Graph to interfejs API sieci Web RESTful, który umożliwia dostęp do Microsoft Cloud zasobów usługi. Po zarejestrowaniu aplikacji i uzyskaniu tokenów uwierzytelniania dla użytkownika lub usługi można wykonać żądania do interfejsu API Microsoft Graph. Aby uzyskać więcej informacji, zobacz [omówienie Microsoft Graph](/graph/overview).

Microsoft Graph udostępnia interfejsy API REST i biblioteki klienckie umożliwiające dostęp do danych w następujących usługach Microsoft 365 Services:
- Microsoft 365 Services: Delve, Excel, Microsoft Books, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner i SharePoint
- Usługi Enterprise Mobility i Security: Advanced Threat Analytics, zaawansowana ochrona przed zagrożeniami, Azure Active Directory, program Identity Manager i usługa Intune
- Windows 10 Services: działania, urządzenia, powiadomienia
- Dynamics 365 Business Central

## <a name="versions"></a>Wersje

Microsoft Graph obecnie obsługuje dwie wersje: v 1.0 i beta. Wersja v 1.0 zawiera ogólnie dostępne interfejsy API. Użyj wersji 1.0 dla wszystkich aplikacji produkcyjnych. Wersja beta zawiera interfejsy API, które są obecnie dostępne w wersji zapoznawczej. Ze względu na to, że możemy wprowadzić istotne zmiany w interfejsie API w wersji beta, zalecamy używanie wersji beta wyłącznie do testowania aplikacji, które są opracowywane. nie należy używać interfejsów API beta w aplikacjach produkcyjnych. Aby uzyskać więcej informacji, zobacz temat [przechowywanie wersji, obsługa i zmiana dotycząca nieprzerwanych zasad Microsoft Graph](/graph/versioning-and-support).

Aby rozpocząć korzystanie z interfejsów API beta, zobacz [Informacje o punkcie końcowym Microsoft Graph beta](/graph/api/overview?view=graph-rest-beta)

Aby rozpocząć korzystanie z interfejsów API protokołu v 1.0, zobacz [Microsoft Graph dokumentacja interfejsu API REST v 1.0](/graph/api/overview)

## <a name="get-started"></a>Rozpoczęcie pracy

Aby czytać lub zapisywać dane w ramach zasobu, takiego jak użytkownik lub wiadomość e-mail, należy utworzyć żądanie, które wygląda następująco:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Aby uzyskać więcej informacji o elementach konstruowanego żądania, zobacz [Korzystanie z interfejsu API Microsoft Graph](/graph/use-the-api)

Przykłady szybkiego startu są dostępne, aby zobaczyć, jak uzyskać dostęp do możliwości interfejsu API Microsoft Graph. Dostępne są przykłady dostępu do dwóch usług z jednym uwierzytelnianiem: konto Microsoft i Outlook. Każdy Przewodnik Szybki Start uzyskuje dostęp do informacji od profilów użytkowników konto Microsoft i wyświetla zdarzenia z kalendarza.
Przewodniki Szybki Start obejmują cztery kroki:
- Wybierz platformę
- Pobieranie identyfikatora aplikacji (identyfikator klienta)
- Kompiluj przykład
- Logowanie i Wyświetlanie zdarzeń w kalendarzu

Po zakończeniu przewodnika Szybki Start masz aplikację, która jest gotowa do uruchomienia. Aby uzyskać więcej informacji, zobacz [Microsoft Graph szybki start — często zadawane pytania](/graph/quick-start-faq). Aby rozpocząć pracę z przykładami, zobacz [Microsoft Graph przewodnika Szybki Start](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Narzędzia

Microsoft Graph Explorer to narzędzie oparte na sieci Web, za pomocą którego można tworzyć i testować żądania przy użyciu Microsoft Graph interfejsów API. Dostęp do Microsoft Graph Explorer można uzyskać w: `https://developer.microsoft.com/graph/graph-explorer` .

Poster to narzędzie, za pomocą którego można tworzyć i testować żądania przy użyciu Microsoft Graph interfejsów API. Możesz pobrać wpis: `https://www.getpostman.com/` . Aby móc korzystać z Microsoft Graph w programie Poster, Użyj kolekcji Microsoft Graph w programie Poster. Aby uzyskać więcej informacji, zobacz [Korzystanie z programu Poster przy użyciu interfejsu API Microsoft Graph](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta).

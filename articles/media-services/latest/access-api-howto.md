---
title: Wprowadzenie do uwierzytelniania w usłudze Azure AD
description: Dowiedz się, jak uzyskać dostęp do usługi Azure Active Directory (Azure AD) w celu korzystania z interfejsu API Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 2f55d1dcc3364f119faab0c728f6e3b7f5ecbb06
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614942"
---
# <a name="get-credentials-to-access-media-services-api"></a>Uzyskiwanie poświadczeń w celu uzyskania dostępu do interfejsu API Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W przypadku korzystania z uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu API Azure Media Services dostępne są dwie opcje uwierzytelniania:

- **Uwierzytelnianie jednostki usługi** (zalecane)

    Uwierzytelnianie usługi. Aplikacje, które często używają tej metody uwierzytelniania, to aplikacje, które uruchamiają usługi demonów, usługi warstwy środkowej lub zaplanowane zadania: aplikacje sieci Web, aplikacje funkcji, Aplikacje logiki, interfejsy API lub mikrousługi.
- **Uwierzytelnianie użytkowników**

    Uwierzytelnij osobę, która korzysta z aplikacji, aby korzystać z zasobów Media Services. Aplikacja interaktywna powinna najpierw monitować użytkownika o poświadczenia. Przykładem jest aplikacja konsoli zarządzania używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub przesyłania strumieniowego na żywo. 

W tym artykule opisano procedurę uzyskiwania poświadczeń w celu uzyskania dostępu do interfejsu API Media Services. Wybierz jedną z następujących kart.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta, Zacznij od [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta Azure Media Services przy użyciu Azure Portal](create-account-howto.md).

## <a name="portal"></a>[Portal](#tab/portal/)

### <a name="api-access"></a>Dostęp za pomocą interfejsu API

Strona **dostęp do interfejsu API** umożliwia wybranie metody uwierzytelniania, która ma być używana do nawiązywania połączenia z interfejsem API. Na stronie znajdują się również wartości potrzebne do nawiązania połączenia z interfejsem API.

1. W [Azure Portal](https://portal.azure.com/)wybierz swoje konto Media Services.
2. Wybierz blok **dostęp do interfejsu API** na lewym pasku nawigacyjnym.
3. W obszarze **Połącz z interfejsem api Media Services** wybierz Media Services wersję interfejsu API, z którą chcesz nawiązać połączenie (wersja 3 to Najnowsza wersja usługi).

### <a name="service-principal-authentication--recommended"></a>Uwierzytelnianie jednostki usługi (zalecane)

Uwierzytelnia usługę przy użyciu aplikacji Azure Active Directory (Azure AD) i wpisu tajnego. Jest to zalecane w przypadku wszystkich usług warstwy środkowej wywołujących interfejs API Media Services. Przykłady to Web Apps, funkcje, Logic Apps, interfejsy API i mikrousługi. Jest to zalecana metoda uwierzytelniania.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Zarządzanie aplikacją i wpisem tajnym usługi Azure AD

Sekcja **Zarządzaj swoją aplikacją i wpisem tajnym usługi AAD** pozwala wybrać lub utworzyć nową aplikację usługi Azure AD i wygenerować wpis tajny. Ze względów bezpieczeństwa nie można wyświetlić wpisu tajnego, gdy blok zostanie zamknięty. Aplikacja używa identyfikatora aplikacji i klucza tajnego do uwierzytelniania w celu uzyskania prawidłowego tokenu dla usługi Media Services.

Upewnij się, że masz wystarczające uprawnienia do zarejestrowania aplikacji w dzierżawie usługi Azure AD i przypisania aplikacji do roli w ramach subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [wymagane uprawnienia](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

#### <a name="connect-to-media-services-api"></a>Nawiązywanie połączenia z interfejsem API Media Services

**Interfejs API łączenia z usługą Media Services** zapewnia wartości używane do łączenia aplikacji głównej usługi. Możesz uzyskać wartości tekstowe lub skopiować bloki JSON lub XML.

### <a name="user-authentication"></a>Uwierzytelnianie użytkowników

Tej opcji można użyć do uwierzytelnienia pracownika lub członka Azure Active Directory, który korzysta z aplikacji w celu współdziałania z zasobami Media Services. Aplikacja interaktywna powinna najpierw monitować użytkownika o podanie poświadczeń użytkownika. Tej metody uwierzytelniania należy używać tylko w przypadku aplikacji do zarządzania.

#### <a name="connect-to-media-services-api"></a>Nawiązywanie połączenia z interfejsem API Media Services

Skopiuj swoje poświadczenia, aby połączyć aplikację użytkownika z sekcji **nawiązywanie połączenia z usługą Media Services API** . Możesz uzyskać wartości tekstowe lub skopiować bloki JSON lub XML.

## <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli/)

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>Następne kroki

[Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe filmów wideo z Media Services v3](stream-files-tutorial-with-api.md).

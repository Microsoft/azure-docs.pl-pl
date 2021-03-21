---
title: Wprowadzenie do uwierzytelniania w usłudze Azure AD przy użyciu Azure Portal | Microsoft Docs
description: Dowiedz się, w jaki sposób używać Azure Portal do uzyskiwania dostępu do uwierzytelniania Azure Active Directory (Azure AD) w celu korzystania z interfejsu API Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d9ad439fe3f41d9e2634fbf9a76cfd21114a5dbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013061"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Wprowadzenie do uwierzytelniania usługi Azure AD przy użyciu witryny Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Dowiedz się, jak za pomocą Azure Portal uzyskać dostęp do uwierzytelniania Azure Active Directory (Azure AD) w celu uzyskania dostępu do interfejsu API Azure Media Services.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta, Zacznij od [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta Azure Media Services przy użyciu Azure Portal](media-services-portal-create-account.md).

W przypadku korzystania z uwierzytelniania usługi Azure AD z usługą Azure Media Services dostępne są dwie opcje uwierzytelniania:

- **Uwierzytelnianie nazwy głównej usługi**. Uwierzytelnianie usługi. Aplikacje, które często używają tej metody uwierzytelniania, to aplikacje, które uruchamiają usługi demonów, usługi warstwy środkowej lub zaplanowane zadania: aplikacje sieci Web, aplikacje funkcji, Aplikacje logiki, interfejsy API lub mikrousługi.
- **Uwierzytelnianie użytkownika**. Uwierzytelnij osobę, która korzysta z aplikacji, aby korzystać z zasobów Media Services. Aplikacja interaktywna powinna najpierw monitować użytkownika o poświadczenia. Przykładem jest aplikacja konsoli zarządzania używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub przesyłania strumieniowego na żywo. 

## <a name="access-the-media-services-api"></a>Uzyskiwanie dostępu do interfejsu API usługi Media Services

Ta strona umożliwia wybranie metody uwierzytelniania, która ma być używana do nawiązywania połączenia z interfejsem API. Na stronie znajdują się również wartości potrzebne do nawiązania połączenia z interfejsem API.

1. W [Azure Portal](https://portal.azure.com/)wybierz swoje konto Media Services.
2. Wybierz sposób nawiązywania połączenia z interfejsem API Media Services.
3. W obszarze **Połącz z interfejsem api Media Services** wybierz Media Services wersję interfejsu API, z którą chcesz nawiązać połączenie.

## <a name="service-principal-authentication--recommended"></a>Uwierzytelnianie jednostki usługi (zalecane)

Uwierzytelnia usługę przy użyciu aplikacji Azure Active Directory (Azure AD) i wpisu tajnego. Jest to zalecane w przypadku wszystkich usług warstwy środkowej wywołujących interfejs API Media Services. Przykłady to Web Apps, funkcje, Logic Apps, interfejsy API i mikrousługi. Jest to zalecana metoda uwierzytelniania.

### <a name="manage-your-azure-ad-app-and-secret"></a>Zarządzanie aplikacją i wpisem tajnym usługi Azure AD

Sekcja **Zarządzaj swoją aplikacją i wpisem tajnym usługi AAD** pozwala wybrać lub utworzyć nową aplikację usługi Azure AD i wygenerować wpis tajny. Ze względów bezpieczeństwa nie można wyświetlić wpisu tajnego, gdy blok zostanie zamknięty. Aplikacja używa identyfikatora aplikacji i klucza tajnego do uwierzytelniania w celu uzyskania prawidłowego tokenu dla usługi Media Services.

Upewnij się, że masz wystarczające uprawnienia do zarejestrowania aplikacji w dzierżawie usługi Azure AD i przypisania aplikacji do roli w ramach subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [wymagane uprawnienia](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

### <a name="connect-to-media-services-api"></a>Nawiązywanie połączenia z interfejsem API Media Services

**Interfejs API łączenia z usługą Media Services** zapewnia wartości używane do łączenia aplikacji głównej usługi. Możesz uzyskać wartości tekstowe lub skopiować bloki JSON lub XML.

## <a name="user-authentication"></a>Uwierzytelnianie użytkowników

Tej opcji można użyć do uwierzytelnienia pracownika lub członka Azure Active Directory, który korzysta z aplikacji w celu współdziałania z zasobami Media Services. Aplikacja interaktywna powinna najpierw monitować użytkownika o podanie poświadczeń użytkownika. Tej metody uwierzytelniania należy używać tylko w przypadku aplikacji do zarządzania.

### <a name="connect-to-media-services-api"></a>Nawiązywanie połączenia z interfejsem API Media Services

Skopiuj swoje poświadczenia, aby połączyć aplikację użytkownika z sekcji **nawiązywanie połączenia z usługą Media Services API** . Możesz uzyskać wartości tekstowe lub skopiować bloki JSON lub XML.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z [przekazywaniem plików na konto](media-services-portal-upload-files.md).

---
title: Zarządzanie uwierzytelnianiem
titleSuffix: Azure Maps
description: Zapoznaj się z uwierzytelnianiem Azure Maps. Zobacz, które podejście najlepiej sprawdza się w tym scenariuszu. Dowiedz się, jak wyświetlać ustawienia uwierzytelniania przy użyciu portalu.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3383971b9984e6f565feebc3bdfb968fb77cbe85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311108"
---
# <a name="manage-authentication-in-azure-maps"></a>Zarządzanie uwierzytelnianiem w Azure Maps

Po utworzeniu konta Azure Maps, identyfikator klienta i klucze są tworzone w celu obsługi uwierzytelniania Azure Active Directory (Azure AD) i uwierzytelniania klucza współużytkowanego.

## <a name="view-authentication-details"></a>Wyświetlanie szczegółów uwierzytelniania

Po utworzeniu konta Azure Maps są generowane klucze podstawowe i pomocnicze. Zalecamy używanie klucza podstawowego jako klucza subskrypcji podczas [korzystania z uwierzytelniania za pomocą klucza współużytkowanego do wywoływania Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Klucza pomocniczego można używać w scenariuszach, takich jak stopniowe zmiany klawiszy. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie w Azure Maps](https://aka.ms/amauth).

Szczegóły uwierzytelniania można wyświetlić w Azure Portal. Na Twoim koncie w menu **Ustawienia** wybierz pozycję **uwierzytelnianie**.

> [!div class="mx-imgBorder"]
> ![Szczegóły uwierzytelniania](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>Odkryj kategorię i scenariusz

W zależności od potrzeb aplikacji istnieją konkretne ścieżki zabezpieczania aplikacji. Usługa Azure AD definiuje kategorie do obsługi szerokiego zakresu przepływów uwierzytelniania. Zobacz [Kategorie aplikacji](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios#application-categories) , aby zrozumieć, która kategoria mieści się w aplikacji.

> [!NOTE]
> Nawet jeśli używasz uwierzytelniania opartego na kluczu udostępnionym, zrozumienie kategorii i scenariuszy pomaga zabezpieczyć aplikację.

## <a name="determine-authentication-and-authorization"></a>Określanie uwierzytelniania i autoryzacji

W poniższej tabeli przedstawiono typowe scenariusze uwierzytelniania i autoryzacji w programie Azure Maps. Tabela zawiera porównanie typów ochrony, które oferują każdy scenariusz.

> [!IMPORTANT]
> Firma Microsoft zaleca wdrożenie Azure Active Directory (Azure AD) z kontrolą dostępu opartą na rolach (RBAC) dla aplikacji produkcyjnych.

| Scenariusz                                                                                    | Uwierzytelnianie | Autoryzacja | Nakład pracy programistycznej | Nakład pracy operacyjnej |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Zaufany demon/nieinteraktywna aplikacja kliencka](./how-to-secure-daemon-app.md)        | Klucz wspólny     | Nie dotyczy           | Średniaa             | Wysoki               |
| [Zaufany demon/nieinteraktywna aplikacja kliencka](./how-to-secure-daemon-app.md)        | Azure AD       | Wys.          | Nis.                | Średniaa             |
| [Aplikacja jednostronicowa sieci Web z interaktywnym logowaniem jednokrotnym](./how-to-secure-spa-users.md) | Azure AD       | Wys.          | Śred.             | Średnia             |
| [Aplikacja jednostronicowa sieci Web z logowaniem nieinterakcyjnym](./how-to-secure-spa-app.md)      | Azure AD       | Wys.          | Śred.             | Średnia             |
| [Aplikacja sieci Web z interaktywnym logowaniem jednokrotnym](./how-to-secure-webapp-users.md)          | Azure AD       | Wysoki          | Wys.               | Śred.             |
| [Urządzenie IoT/dane wejściowe z ograniczeniami](./how-to-secure-device-code.md)                     | Azure AD       | Wys.          | Śred.             | Średnia             |

Linki w tabeli prowadzą do szczegółowych informacji o konfiguracji dla każdego scenariusza.

## <a name="view-role-definitions"></a>Wyświetlanie definicji ról

Aby wyświetlić role platformy Azure, które są dostępne dla Azure Maps, przejdź do obszaru **Kontrola dostępu (IAM)**. Wybierz pozycję **role**, a następnie wyszukaj role zaczynające się od *Azure Maps*. Te role Azure Maps są rolami, do których można udzielić dostępu.

> [!div class="mx-imgBorder"]
> ![Wyświetl dostępne role](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>Wyświetlanie przypisań ról

Aby wyświetlić użytkowników i aplikacje, którym udzielono dostępu RBAC dla Azure Maps, przejdź do **Access Control (IAM)**. W tym miejscu wybierz pozycję **przypisania ról**, a następnie Przefiltruj według **Azure Maps**.

> [!div class="mx-imgBorder"]
> ![Wyświetlanie użytkowników i aplikacji z przyznanymi RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Tokeny żądania dla Azure Maps

Zażądaj tokenu z punktu końcowego tokenu usługi Azure AD. W żądaniu usługi Azure AD Użyj następujących szczegółów:

| Środowisko platformy Azure      | Punkt końcowy tokenu usługi Azure AD             | Identyfikator zasobu platformy Azure              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Chmura publiczna Azure     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government Cloud | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Aby uzyskać więcej informacji na temat żądania tokenów dostępu z usługi Azure AD dla użytkowników i jednostek usługi, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios) i Wyświetl konkretne scenariusze w tabeli [scenariuszy](./how-to-manage-authentication.md#determine-authentication-and-authorization).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Azure AD i Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Znajdź metryki użycia interfejsu API dla konta usługi Azure Maps:
> [!div class="nextstepaction"]
> [Wyświetlanie metryk użycia](how-to-view-api-usage.md)

Zapoznaj się z przykładami, które pokazują, jak zintegrować usługę Azure AD z Azure Maps:

> [!div class="nextstepaction"]
> [Przykłady uwierzytelniania usługi Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)

---
title: Rejestrowanie poufnej aplikacji klienckiej w usłudze Azure AD — Azure API for FHIR
description: Zarejestruj poufną aplikację kliencką w Azure Active Directory, która uwierzytelnia się w imieniu użytkownika i żąda dostępu do aplikacji zasobów.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: matjazl
ms.openlocfilehash: c10b27d375e2bfb8c64130eceb416a633241cf68
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284441"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Rejestrowanie poufnej aplikacji klienckiej w Azure Active Directory

W tym samouczku dowiesz się, jak zarejestrować poufną aplikację kliencką w Azure Active Directory (Azure AD).  

Rejestracja aplikacji klienta to reprezentacja aplikacji usługi Azure AD, której można użyć do uwierzytelniania w imieniu użytkownika i żądania dostępu do [aplikacji zasobów](register-resource-azure-ad-client-app.md). Poufna aplikacja kliencka to aplikacja, która może być zaufana do przechowywania klucza tajnego i prezentować ten klucz tajny podczas żądania tokenów dostępu. Przykładami poufnych aplikacji są aplikacje po stronie serwera. 

Aby zarejestrować nową poufną aplikację kliencką, zapoznaj się z poniższymi instrukcjami. 

## <a name="register-a-new-application"></a>Rejestrowanie nowej aplikacji

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Azure Active Directory**.

1. Wybierz pozycję **Rejestracje aplikacji**. 

    :::image type="content" source="media/how-to-aad/portal-aad-new-app-registration.png" alt-text="Azure Portal. Rejestracja nowej aplikacji.":::

1. Wybierz pozycję **Nowa rejestracja**.

1. Nadaj aplikacji nazwę wyświetlaną skierowaną do użytkownika.

1. Dla **obsługiwanych typów kont** wybierz, kto może korzystać z aplikacji lub uzyskać dostęp do interfejsu API.

1. Obowiązkowe Podaj **Identyfikator URI przekierowania**. Te szczegóły można później zmienić, ale jeśli znasz adres URL odpowiedzi aplikacji, wprowadź ją teraz.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png" alt-text="Rejestracja nowej poufnej aplikacji klienta.":::

1. Wybierz pozycję **Zarejestruj**.

## <a name="api-permissions"></a>Uprawnienia aplikacji

Po zarejestrowaniu aplikacji należy wybrać uprawnienia interfejsu API, które ta aplikacja powinna żądać w imieniu użytkowników.

1. Wybierz pozycję **uprawnienia interfejsu API**.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png" alt-text="Klient poufny. Uprawnienia interfejsu API.":::

1. Wybierz pozycję **Dodaj uprawnienie**.

    Jeśli używasz interfejsu API platformy Azure dla usługi FHIR, dodasz uprawnienie do interfejsów API usługi Azure opieki IT, wyszukując **interfejs API usługi Azure opieki** IT w obszarze **interfejsy API używane przez moją organizację**. Wynik wyszukiwania dla interfejsu API usługi Azure opieki it zwróci się tylko wtedy [, gdy wdrożono już interfejs API platformy Azure dla usługi FHIR](fhir-paas-powershell-quickstart.md).

    Jeśli odwołujesz się do innej aplikacji zasobów, wybierz [rejestrację aplikacji interfejsu API FHIR](register-resource-azure-ad-client-app.md) utworzoną wcześniej w obszarze **Moje interfejsy API**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Klient poufny. Moje interfejsy API organizacji" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. Wybierz zakresy (uprawnienia), które będzie pytać poufna aplikacja kliencka w imieniu użytkownika. Wybierz pozycję **user_impersonation**, a następnie wybierz pozycję **Dodaj uprawnienia**.

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Klient poufny. Delegowane uprawnienia":::


## <a name="application-secret"></a>Klucz tajny aplikacji

1. Wybierz pozycję **certyfikaty & wpisy tajne**, a następnie wybierz pozycję **nowy klucz tajny klienta**. 

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png" alt-text="Klient poufny. Wpis tajny aplikacji.":::

1. Wprowadź **Opis** wpisu tajnego klienta. Wybierz menu rozwijane **wygasanie** , aby wybrać przedział czasu wygaśnięcia, a następnie kliknij przycisk **Dodaj**.

   :::image type="content" source="media/how-to-aad/add-a-client-secret.png" alt-text="Dodaj klucz tajny klienta.":::

1. Po utworzeniu ciągu tajnego klienta skopiuj jego **wartość** i **Identyfikator** i Zapisz je w wybranej bezpiecznej lokalizacji.

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="Ciąg tajny klienta."::: 

> [!NOTE]
>Ciąg tajny klienta jest widoczny tylko raz w Azure Portal. Gdy opuścisz stronę internetową certyfikaty & Secret, a następnie wrócisz do niej, ciąg wartości zostanie maskowany. Ważne jest, aby utworzyć kopię tajnego ciągu klienta bezpośrednio po jego wygenerowaniu. Jeśli nie masz kopii zapasowej tajnego klienta, należy powtórzyć powyższe kroki, aby ponownie wygenerować ten wpis.
 
## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera instrukcje dotyczące sposobu rejestrowania poufnej aplikacji klienckiej w usłudze Azure AD. Zawarto również instrukcje dodawania uprawnień interfejsu API do interfejsu API usługi Azure opieki IT. Na koniec pokazano, jak utworzyć klucz tajny aplikacji. Ponadto możesz dowiedzieć się, jak uzyskać dostęp do serwera FHIR przy użyciu programu Poster.
 
>[!div class="nextstepaction"]
>[Dostęp do interfejsu API platformy Azure dla usługi FHIR za pomocą programu Poster](access-fhir-postman-tutorial.md)

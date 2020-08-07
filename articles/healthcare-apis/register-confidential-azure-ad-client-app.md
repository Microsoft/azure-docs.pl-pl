---
title: Rejestrowanie poufnej aplikacji klienckiej w usłudze Azure AD — Azure API for FHIR
description: Zarejestruj poufną aplikację kliencką w Azure Active Directory, która uwierzytelnia się w imieniu użytkownika i żąda dostępu do aplikacji zasobów.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 756645d2df22f1222c3004a44e5a46c7a3bc1a2f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852552"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Rejestrowanie poufnej aplikacji klienckiej w Azure Active Directory

W tym samouczku dowiesz się, jak zarejestrować poufną aplikację kliencką w Azure Active Directory. 

Rejestracja aplikacji klienckiej to Azure Active Directory reprezentacja aplikacji, która może służyć do uwierzytelniania w imieniu użytkownika i żądania dostępu do [aplikacji zasobów](register-resource-azure-ad-client-app.md). Poufna aplikacja kliencka to aplikacja, która może być zaufana do przechowywania klucza tajnego i prezentować ten klucz tajny podczas żądania tokenów dostępu. Przykładami poufnych aplikacji są aplikacje po stronie serwera.

Aby zarejestrować nową aplikację poufną w portalu, wykonaj poniższe kroki.

## <a name="app-registrations-in-azure-portal"></a>Rejestracje aplikacji w Azure Portal

1. W witrynie [Azure Portal](https://portal.azure.com) w panelu nawigacyjnym po lewej stronie kliknij pozycję **Azure Active Directory**.

2. W bloku **Azure Active Directory** kliknij pozycję **rejestracje aplikacji**:

    ![Azure Portal. Rejestracja nowej aplikacji.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kliknij **nową rejestrację**.

## <a name="register-a-new-application"></a>Rejestrowanie nowej aplikacji

1. Nadaj aplikacji nazwę wyświetlaną.

2. Podaj adres URL odpowiedzi. Te szczegóły można później zmienić, ale jeśli znasz adres URL odpowiedzi aplikacji, wprowadź ją teraz.

    ![Rejestracja nowej poufnej aplikacji klienta.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>Uprawnienia aplikacji

Następne Dodawanie uprawnień interfejsu API:

1. Otwórz **uprawnienia interfejsu API**:

    ![Klient poufny. Uprawnienia interfejsu API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Kliknij przycisk **Dodaj uprawnienie**

3. Wybierz odpowiedni interfejs API zasobów:

    W przypadku interfejsu API platformy Azure dla usługi FHIR (usługa zarządzana) kliknij pozycję Interfejsy API, które są **wykorzystywane przez moją organizację** , i wyszukaj ciąg "interfejsy API usługi Azure opieka W przypadku serwera FHIR "open source" na platformie Azure wybierz pozycję [Rejestracja aplikacji zasobów interfejsu API FHIR](register-resource-azure-ad-client-app.md):

    ![Klient poufny. Moje interfejsy API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Wybierz zakresy (uprawnienia), które mogą być dostępne dla aplikacji poufnej w imieniu użytkownika:

    ![Klient poufny. Delegowane uprawnienia](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Klucz tajny aplikacji

1. Utwórz klucz tajny aplikacji (klucz tajny klienta):

    ![Klient poufny. Wpis tajny aplikacji](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Podaj opis i czas trwania wpisu tajnego.

3. Po wygenerowaniu zostanie on wyświetlony w portalu tylko raz. Zanotuj ją i Zapisz bezpiecznie.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób rejestrowania poufnej aplikacji klienckiej w Azure Active Directory. Teraz można przystąpić do wdrażania [interfejsu API platformy Azure dla usługi FHIR](fhir-paas-powershell-quickstart.md).

Po wdrożeniu interfejsu API platformy Azure dla usługi FHIR możesz przejrzeć dodatkowe dostępne ustawienia.
 
>[!div class="nextstepaction"]
>[Wdrażanie usługi Azure API for FHIR](fhir-paas-powershell-quickstart.md)
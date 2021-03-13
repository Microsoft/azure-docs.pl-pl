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
ms.openlocfilehash: 8021fb3fa9f11ef895569f48a2ae21b3f7adcd36
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020135"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Rejestrowanie poufnej aplikacji klienckiej w Azure Active Directory

W tym samouczku dowiesz się, jak zarejestrować poufną aplikację kliencką w Azure Active Directory. 

Rejestracja aplikacji klienckiej to Azure Active Directory reprezentacja aplikacji, która może służyć do uwierzytelniania w imieniu użytkownika i żądania dostępu do [aplikacji zasobów](register-resource-azure-ad-client-app.md). Poufna aplikacja kliencka to aplikacja, która może być zaufana do przechowywania klucza tajnego i prezentować ten klucz tajny podczas żądania tokenów dostępu. Przykładami poufnych aplikacji są aplikacje po stronie serwera.

Aby zarejestrować nową aplikację poufną w portalu, wykonaj następujące kroki.

## <a name="register-a-new-application"></a>Rejestrowanie nowej aplikacji

1. W [Azure Portal](https://portal.azure.com)przejdź do **Azure Active Directory**.

1. Wybierz pozycję **Rejestracje aplikacji**.

    ![Azure Portal. Rejestracja nowej aplikacji.](media/how-to-aad/portal-aad-new-app-registration.png)

1. Wybierz pozycję **Nowa rejestracja**.

1. Nadaj aplikacji nazwę wyświetlaną.

1. Podaj adres URL odpowiedzi. Te szczegóły można później zmienić, ale jeśli znasz adres URL odpowiedzi aplikacji, wprowadź ją teraz.

    ![Rejestracja nowej poufnej aplikacji klienta.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)
1. Wybierz pozycję **Zarejestruj**.

## <a name="api-permissions"></a>Uprawnienia aplikacji

Po zarejestrowaniu aplikacji należy wybrać uprawnienia interfejsu API, dla których ta aplikacja powinna mieć możliwość żądania w imieniu użytkowników:

1. Wybierz pozycję **uprawnienia interfejsu API**.

    ![Klient poufny. Uprawnienia interfejsu API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. Wybierz pozycję **Dodaj uprawnienie**.

    Jeśli używasz interfejsu API platformy Azure dla usługi FHIR, dodasz uprawnienie do interfejsów API usługi Azure opieki IT, wyszukując **interfejsy API usługi Azure opieki zdrowotnej** w obszarze **interfejsy API używane przez moją organizację**. Można to sprawdzić tylko wtedy [, gdy wdrożono już interfejs API platformy Azure dla usługi FHIR](fhir-paas-powershell-quickstart.md).

    Jeśli odwołujesz się do innej aplikacji zasobów, wybierz [rejestrację aplikacji interfejsu API FHIR](register-resource-azure-ad-client-app.md) utworzoną wcześniej w obszarze **Moje interfejsy API**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Klient poufny. Moje interfejsy API organizacji" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

3. Wybierz zakresy (uprawnienia), które mogą być dostępne dla aplikacji poufnej w imieniu użytkownika:

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Klient poufny. Delegowane uprawnienia":::

## <a name="application-secret"></a>Klucz tajny aplikacji

1. Wybierz pozycję **certyfikaty & wpisy tajne**.
1. Wybierz pozycję **Nowy wpis tajny klienta**. 

    ![Klient poufny. Wpis tajny aplikacji](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Podaj opis i czas trwania wpisu tajnego (1 rok, 2 lata lub nigdy).

3. Po wygenerowaniu zostanie on wyświetlony w portalu tylko raz. Zanotuj ją i Zapisz bezpiecznie.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób rejestrowania poufnej aplikacji klienckiej w Azure Active Directory. Następnie możesz uzyskać dostęp do serwera FHIR za pomocą programu Poster
 
>[!div class="nextstepaction"]
>[Dostęp do interfejsu API platformy Azure dla usługi FHIR za pomocą programu Poster](access-fhir-postman-tutorial.md)

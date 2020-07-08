---
title: Rejestrowanie aplikacji usługi w usłudze Azure AD — Azure API for FHIR
description: Dowiedz się, jak zarejestrować aplikację klienta usługi w Azure Active Directory, która może służyć do uwierzytelniania i uzyskiwania tokenów.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 83514ea13ca51e6980a1b4b60d4555974db5a240
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871848"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Rejestrowanie aplikacji klienckiej usługi w Azure Active Directory

W tym artykule dowiesz się, jak zarejestrować aplikację klienta usługi w Azure Active Directory. Rejestracje aplikacji klienckich są Azure Active Directory reprezentacje aplikacji, których można użyć do uwierzytelniania i uzyskiwania tokenów. Klient usługi jest przeznaczony do użycia przez aplikację w celu uzyskania tokenu dostępu bez interaktywnego uwierzytelniania użytkownika. Będzie on miał pewne uprawnienia do aplikacji i używa klucza tajnego aplikacji (hasła) podczas uzyskiwania tokenów dostępu.

Wykonaj poniższe kroki, aby utworzyć nowego klienta usługi.

## <a name="app-registrations-in-azure-portal"></a>Rejestracje aplikacji w Azure Portal

1. W witrynie [Azure Portal](https://portal.azure.com) w panelu nawigacyjnym po lewej stronie kliknij pozycję **Azure Active Directory**.

2. W bloku **Azure Active Directory** kliknij pozycję **rejestracje aplikacji**:

    ![Azure Portal. Rejestracja nowej aplikacji.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kliknij pozycję **Nowa rejestracja**.

## <a name="service-client-application-details"></a>Szczegóły aplikacji klienta usługi

* Klient usługi wymaga nazwy wyświetlanej i można także podać adres URL odpowiedzi, ale zazwyczaj nie będzie używany.

    ![Azure Portal. Rejestracja nowej aplikacji klienta usługi.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>Uprawnienia aplikacji

Należy przyznać role aplikacji klienta usługi. 

1. Otwórz **uprawnienia interfejsu API** i wybierz pozycję [Rejestracja aplikacji interfejsu API FHIR](register-resource-azure-ad-client-app.md). Jeśli używasz interfejsu API platformy Azure dla usługi FHIR, dodasz uprawnienie do interfejsów API usługi Azure opieki IT, wyszukując interfejsy API usługi Azure opieki zdrowotnej w obszarze **interfejsy API używane przez moją organizację**.

    ![Azure Portal. Uprawnienia interfejsu API klienta usługi](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Wybierz role aplikacji z tych, które są zdefiniowane w aplikacji zasobów:

    ![Azure Portal. Uprawnienia aplikacji klienta usługi](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Udziel zgody aplikacji. Jeśli nie masz wymaganych uprawnień, skontaktuj się z administratorem Azure Active Directory:

    ![Azure Portal. Zgoda administratora klienta usługi](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Klucz tajny aplikacji

Klient usługi wymaga klucza tajnego (hasła), który będzie używany podczas uzyskiwania tokenów.

1. Kliknij **pozycję &amp; klucze tajne certyfikatów**

2. Kliknij pozycję **Nowy klucz tajny klienta**.

    ![Azure Portal. Klucz tajny klienta usługi](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Podaj okres istnienia klucza tajnego.

4. Po jego wygenerowaniu zostanie on wyświetlony tylko raz w portalu. Zanotuj ją i przechowuj w bezpieczny sposób.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób rejestrowania aplikacji klienckiej usługi w Azure Active Directory. Następnie wdróż interfejs API FHIR na platformie Azure.
 
>[!div class="nextstepaction"]
>[Wdróż serwer FHIR Open Source](fhir-oss-powershell-quickstart.md)
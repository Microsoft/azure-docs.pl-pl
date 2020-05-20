---
title: Przygotuj Azure Migrate do pracy z narzędziami niezależnego dostawcy oprogramowania/przenoszenia
description: W tym artykule opisano sposób przygotowania Azure Migrate do pracy z narzędziem niezależnego dostawcy oprogramowania lub przenoszenia, a następnie rozpoczęcia korzystania z narzędzia.
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: 9513e783d4f9d7be83f1434d4dd9011844af8993
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682650"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>Przygotowanie do pracy z narzędziem niezależnego dostawcy oprogramowania lub przenoszenia

Po dodaniu [narzędzia niezależnego dostawcy oprogramowania](migrate-services-overview.md#isv-integration) lub przełączeniu do projektu Azure Migrate istnieje kilka kroków, które należy wykonać przed połączeniem narzędzia i wysłanie danych do Azure Migrate. 

## <a name="check-azure-ad-permissions"></a>Sprawdź uprawnienia usługi Azure AD

Twoje konto użytkownika platformy Azure wymaga następujących uprawnień:

- Uprawnienie do rejestrowania aplikacji Azure Active Directory (Azure AD) przy użyciu dzierżawy platformy Azure
- Uprawnienie do przydzielenia roli do aplikacji usługi Azure AD na poziomie subskrypcji


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Ustawianie uprawnień do rejestrowania aplikacji usługi Azure AD

1. W usłudze Azure AD Sprawdź rolę konta.
2. Jeśli masz rolę użytkownika, wybierz pozycję **Ustawienia użytkownika** po lewej stronie i sprawdź, czy użytkownicy mogą rejestrować aplikacje. Jeśli jest ustawiona na **tak**, wszyscy użytkownicy w dzierżawie usługi Azure AD mogą zarejestrować aplikację. Jeśli ustawiono wartość **nie**, tylko użytkownicy administracyjni mogą rejestrować aplikacje.   
3. Jeśli nie masz uprawnień, administrator może podać konto użytkownika z rolą [administratora aplikacji](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) , aby można było zarejestrować aplikację.
4. Gdy narzędzie zostanie połączone z Azure Migrate, administrator może usunąć rolę z Twojego konta.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Ustawianie uprawnień do przypisywania roli do aplikacji usługi Azure AD
 
W ramach subskrypcji platformy Azure Twoje konto musi mieć dostęp do **programu Microsoft. Authorization/*/Write** , aby przypisać rolę do aplikacji usługi Azure AD. 

1. W witrynie Azure Portal otwórz pozycję **Subskrypcje**.
2. Wybierz odpowiednią subskrypcję. Jeśli go nie widzisz, wybierz **Filtr subskrypcje globalne**. 
3. Wybierz pozycję **Moje uprawnienia**. Następnie wybierz **pozycję kliknij tutaj, aby wyświetlić pełne szczegóły dostępu dla tej subskrypcji**.
4. W **widoku przypisania ról**  >  **View**Sprawdź uprawnienia. Jeśli Twoje konto nie ma uprawnień, poprosimy administratora subskrypcji o dodanie do roli [administratora dostępu użytkownika](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) lub roli [właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) .
 

## <a name="start-using-the-tool"></a>Rozpocznij korzystanie z narzędzia

1. Jeśli nie masz jeszcze licencji lub bezpłatnej wersji próbnej narzędzia, w pozycji narzędzia w Azure Migrate w polu **zarejestruj**wybierz pozycję **Dowiedz się więcej**.
2. W narzędziu postępuj zgodnie z instrukcjami, aby połączyć się z narzędziem do Azure Migrate projektu i przesłać dane do Azure Migrate.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z instrukcjami od niezależnego dostawcy oprogramowania lub Przenieś dane do Azure Migrate.

   

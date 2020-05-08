---
title: Przygotowanie Azure Migrate do pracy z narzędziami/przenoszącymi niezależnego dostawcy oprogramowania
description: Przygotowanie Azure Migrate do pracy z narzędziami/przenoszącymi niezależnego dostawcy oprogramowania
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: b2ac95743e97bce41f38a4078e5e38f6b1bf7cd9
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82906984"
---
# <a name="prepare-to-work-with-isv-toolsmovere"></a>Przygotowywanie do pracy z narzędziami ISV/przeprowadzkami

Po dodaniu [narzędzia niezależnego dostawcy oprogramowania](migrate-services-overview.md#isv-integration)lub przełączeniu do projektu Azure Migrate istnieje kilka kroków do przygotowania przed połączeniem narzędzia i wysyłania danych do Azure Migrate. 

## <a name="check-azure-ad-permissions"></a>Sprawdź uprawnienia usługi Azure AD

Twoje konto użytkownika platformy Azure wymaga kilku uprawnień:

- Uprawnienie do rejestrowania aplikacji usługi Azure AD w ramach dzierżawy platformy Azure.
- Uprawnienie do przydzielenia roli do aplikacji usługi Azure AD na poziomie subskrypcji.


## <a name="set-permissions-to-register-an-azure-ad-app"></a>Ustawianie uprawnień do rejestrowania aplikacji usługi Azure AD

1. W Azure Active Directory Sprawdź rolę konta. Jeśli masz rolę użytkownika, kliknij pozycję **Ustawienia użytkownika** po lewej stronie i sprawdź, czy użytkownicy mogą rejestrować aplikacje.
2. Jeśli jest ustawiona na **tak**, wszyscy użytkownicy w dzierżawie usługi Azure AD mogą zarejestrować aplikację. W takim przypadku tylko użytkownicy administracyjni mogą rejestrować aplikacje.
3. Jeśli nie masz uprawnień, administrator może podać konto użytkownika z rolą [administratora aplikacji](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) , aby można było zarejestrować aplikację.
4. Gdy narzędzie zostanie połączone z Azure Migrate, administrator może usunąć rolę z Twojego konta.

## <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Ustawianie uprawnień do przypisywania roli do aplikacji usługi Azure AD
 
W ramach subskrypcji platformy Azure Twoje konto musi mieć **dostęp do programu Microsoft. Authorization/*/Write**, aby przypisać rolę do aplikacji usługi Azure AD. 

1. Aby sprawdzić uprawnienia subskrypcji, w Azure Portal Otwórz pozycję **subskrypcje**.
2. Wybierz odpowiednią subskrypcję. Jeśli go nie widzisz, wybierz **Filtr subskrypcje globalne**. 
3. Wybierz pozycję **Moje uprawnienia**. Następnie wybierz **pozycję kliknij tutaj, aby wyświetlić pełne szczegóły dostępu dla tej subskrypcji**.
4. W > **widoku** **przypisania ról**i sprawdź uprawnienia.
5. Jeśli Twoje konto nie ma uprawnień, poprosimy administratora subskrypcji o dodanie do roli [administratora dostępu użytkownika](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) lub roli [właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) .
 

## <a name="start-using-the-tool"></a>Rozpocznij korzystanie z narzędzia

1. Jeśli nie masz jeszcze licencji lub bezpłatnej wersji próbnej narzędzia, w pozycji narzędzia w Azure Migrate w obszarze **zarejestruj**kliknij pozycję **Dowiedz się więcej**.
2. W narzędziu postępuj zgodnie z instrukcjami, aby połączyć się z narzędziem do Azure Migrate projektu i przesłać dane do Azure Migrate.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z instrukcjami niezależnego dostawcy oprogramowania lub Przenieś dane do Azure Migrate.

   

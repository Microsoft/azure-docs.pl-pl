---
title: Przygotuj Azure Migrate do pracy z narzędziami niezależnego dostawcy oprogramowania/przenoszenia
description: W tym artykule opisano sposób przygotowania Azure Migrate do pracy z narzędziem niezależnego dostawcy oprogramowania lub przenoszenia, a następnie rozpoczęcia korzystania z narzędzia.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 1716db0476169e12822b3f47f7199bf6e2c4ee92
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753777"
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
3. Jeśli nie masz uprawnień, administrator może podać konto użytkownika z rolą [administratora aplikacji](../active-directory/roles/permissions-reference.md#application-administrator) , aby można było zarejestrować aplikację.
4. Gdy narzędzie zostanie połączone z Azure Migrate, administrator może usunąć rolę z Twojego konta.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Ustawianie uprawnień do przypisywania roli do aplikacji usługi Azure AD
 
W ramach subskrypcji platformy Azure Twoje konto musi mieć dostęp do **programu Microsoft. Authorization/*/Write** , aby przypisać rolę do aplikacji usługi Azure AD. 

1. W witrynie Azure Portal otwórz pozycję **Subskrypcje**.
2. Wybierz odpowiednią subskrypcję. Jeśli go nie widzisz, wybierz **Filtr subskrypcje globalne**. 
3. Wybierz pozycję **Moje uprawnienia**. Następnie wybierz **pozycję kliknij tutaj, aby wyświetlić pełne szczegóły dostępu dla tej subskrypcji**.
4. W **widoku przypisania ról**  >  **View** Sprawdź uprawnienia. Jeśli Twoje konto nie ma uprawnień, poprosimy administratora subskrypcji o dodanie do roli [administratora dostępu użytkownika](../role-based-access-control/built-in-roles.md#user-access-administrator) lub roli [właściciela](../role-based-access-control/built-in-roles.md#owner) .

## <a name="allow-access-to-urls"></a>Zezwalanie na dostęp do adresów URL

W przypadku narzędzi niezależnych dostawców oprogramowania i usługi Azure Database Asystent migracji zezwalać na dostęp do adresów URL chmury publicznej podsumowujących w tabeli. Jeśli używasz serwera proxy opartego na adresie URL do łączenia się z Internetem, upewnij się, że serwer proxy rozpoznaje wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL. 

**Adres URL** | **Szczegóły**
--- | ---
*.portal.azure.com  | Przejdź do witryny Azure Portal. 
*.windows.net<br/> *.msftauth.net<br/> *.msauth.net <br/> *.microsoft.com<br/> *.live.com   | Zaloguj się do subskrypcji platformy Azure. 
*.microsoftonline.com<br/> *.microsoftonline-p.com | Utwórz aplikacje Azure Active Directory (AD) dla urządzenia, aby komunikować się z Azure Migrate. 
management.azure.com | Wykonywanie Azure Resource Manager wywołań do projektu Azure Migrate.
*.servicebus.windows.net | Komunikacja między urządzeniem a centrum EventHub na potrzeby wysyłania wiadomości.


## <a name="start-using-the-tool"></a>Rozpocznij korzystanie z narzędzia

1. Jeśli nie masz jeszcze licencji lub bezpłatnej wersji próbnej narzędzia, w pozycji narzędzia w Azure Migrate w polu **zarejestruj** wybierz pozycję **Dowiedz się więcej**.
2. W narzędziu postępuj zgodnie z instrukcjami, aby połączyć się z narzędziem do Azure Migrate projektu i przesłać dane do Azure Migrate.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z instrukcjami od niezależnego dostawcy oprogramowania lub Przenieś dane do Azure Migrate.

   

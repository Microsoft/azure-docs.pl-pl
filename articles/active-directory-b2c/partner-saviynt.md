---
title: Samouczek dotyczący konfigurowania Saviynt z Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Samouczek pozwalający skonfigurować Azure Active Directory B2C z integracją między aplikacjami, aby usprawnić modernizację i wspierać lepsze zabezpieczenia, nadzór i zgodność. 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b1889a693c08ce78a8bd06b6fe348c0c67bf3812
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103465678"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>Samouczek dotyczący konfigurowania Saviynt z Azure Active Directory B2C

W tym przykładowym samouczku przedstawiono wskazówki dotyczące integrowania Azure Active Directory (AD) B2C z [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/). Platforma Security Manager Saviynt zapewnia widoczność, bezpieczeństwo i zarządzanie współczesnymi firmami, w ramach jednej ujednoliconej platformy. Saviynt się z ryzykiem aplikacji i zarządzaniem, infrastrukturą, zarządzaniem kontami uprzywilejowanymi i analizą ryzyka dla klientów.

W tym przykładowym samouczku skonfigurujesz program Saviynt w celu zapewnienia precyzyjnej administracji delegowanej na podstawie kontroli dostępu Azure AD B2C użytkownikom. Saviynt wykonuje następujące testy, aby określić, czy użytkownik ma uprawnienia do zarządzania użytkownikami Azure AD B2C.

- Zabezpieczenia na poziomie funkcji, aby określić, czy użytkownik może wykonać określoną operację. Na przykład Utwórz użytkownika, zaktualizuj użytkownika, zresetuj hasło użytkownika i tak dalej.

- Zabezpieczenia na poziomie pola, aby określić, czy użytkownik może odczytywać i zapisywać określony atrybut innego użytkownika podczas operacji zarządzania użytkownikami. Na przykład Agent pomocy technicznej może aktualizować tylko numer telefonu, a wszystkie inne atrybuty są tylko do odczytu.

- Zabezpieczenia na poziomie danych w celu ustalenia, czy użytkownik może wykonać określoną operację na określonym użytkowniku. Na przykład administrator pomocy technicznej dla regionu Zjednoczonego Królestwa może zarządzać tylko użytkownikami Zjednoczonego Królestwa.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](./tutorial-create-tenant.md). Dzierżawca jest połączony z subskrypcją platformy Azure.

- [Subskrypcja](https://saviynt.com/contact-us/) Saviynt

## <a name="scenario-description"></a>Opis scenariusza

Integracja Saviynt obejmuje następujące składniki:

- [Azure AD B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) — tożsamość między klientami jako usługa, która umożliwia niestandardową kontrolę sposobu tworzenia konta, logowania i zarządzania swoimi profilami przez klientów.

- [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) — platforma do zarządzania tożsamościami, która zapewnia szczegółowe administrowanie delegowaniem dla użytkowników i zarządzanie cyklem życia użytkownika oraz nadzór nad dostępem Azure AD B2C użytkowników.  

- [Microsoft Graph API](/graph/use-the-api) — ten interfejs API udostępnia interfejsy Saviynt do zarządzania Azure AD B2C użytkownikami i ich dostępem w Azure AD B2C.

Na poniższym diagramie architektury przedstawiono implementację.

![Obraz przedstawiający diagram architektury saviynt](./media/partner-saviynt/saviynt-architecture-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Administrator delegowany uruchamia operację zarządzania Azure AD B2C użytkownika za pomocą Saviynt.
| 2. | Saviynt weryfikuje z aparatem autoryzacji, jeśli Administrator delegowany może wykonać konkretną operację.
| 3. | Aparat autoryzacji Saviynt wysyła odpowiedź o sukces/niepowodzenie autoryzacji.
| 4. | Saviynt umożliwia administratorowi delegowanemu wykonanie wymaganej operacji.
| 5. | Saviynt wywołuje interfejs API Microsoft Graph wraz z atrybutami użytkownika w celu zarządzania użytkownikiem w programie Azure AD B2C
| 6. | Interfejs Microsoft Graph API spowoduje włączenie tworzenia/aktualizowania/usuwania użytkownika w programie Azure AD B2C.
| 7. | Azure AD B2C wyśle odpowiedź o powodzeniu lub niepowodzeniu.
| 8. | Interfejs API Microsoft Graph zwróci odpowiedź do Saviynt.

## <a name="onboard-with-saviynt"></a>Dołączanie do Saviynt

1. Aby utworzyć konto Saviynt, skontaktuj się z [Saviynt](https://saviynt.com/contact-us/)

2. Utwórz zasady administracji delegowanej i Przypisz użytkowników jako administratorów delegowanych z różnymi rolami.

## <a name="configure-azure-ad-b2c-with-saviynt"></a>Konfigurowanie Azure AD B2C przy użyciu Saviynt

### <a name="create-an-azure-ad-application-for-saviynt"></a>Tworzenie aplikacji usługi Azure AD dla Saviynt

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/#home).

2. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

3. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.

4. Wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.

5. Wprowadź nazwę aplikacji. Na przykład Saviynt i wybierz pozycję **Utwórz**.

6. Przejdź do pozycji **uprawnienia interfejsu API** i wybierz pozycję **+ Dodaj uprawnienie.**

7. Zostanie wyświetlona strona uprawnienia do interfejsu API żądania. Wybierz kartę **interfejsy API firmy Microsoft** i wybierz pozycję **Microsoft Graph** jako często używane interfejsy API firmy Microsoft.

8. Przejdź do następnej strony i wybierz pozycję **uprawnienia aplikacji**.

9. Wybierz pozycję **katalog**, a następnie wybierz pozycję **katalog. odczyt. wszystkie** i **katalog. ReadWrite. wszystkie** pola wyboru.

10. Wybierz pozycję **Dodaj uprawnienia**. Przejrzyj dodane uprawnienia.

11. Wybierz pozycję **Udziel zgody administratora na zapisywanie domyślnego katalogu**  >  .

12. Przejdź do pozycji **Certyfikaty i wpisy tajne** i wybierz pozycję **+ Dodaj klucz tajny klienta**. Wprowadź opis wpisu tajnego klienta, wybierz opcję wygaśnięcia i wybierz pozycję **Dodaj**.

13. Klucz tajny jest generowany i wyświetlany w sekcji klucz tajny klienta.

    >[!NOTE]
    > Klucz tajny klienta będzie potrzebny później.

14. Przejdź do **omówienia** i Pobierz **Identyfikator klienta** i **Identyfikator dzierżawy**.

15. Identyfikator dzierżawy, identyfikator klienta i klucz tajny klienta będą musiały zostać wykonane w celu ukończenia instalacji w programie Saviynt.

### <a name="enable-saviynt-to-delete-users"></a>Włącz Saviynt do usuwania użytkowników

Poniższe kroki wyjaśniają, jak włączyć Saviynt do wykonywania operacji usuwania przez użytkownika w Azure AD B2C.

>[!NOTE]
>[Oceń ryzyko przed przyznaniem ról administracyjnych dostępu do nazwy głównej usługi.](../active-directory/develop/app-objects-and-service-principals.md)

1. Zainstaluj najnowszą wersję modułu MSOnline PowerShell na stacji roboczej/serwerze z systemem Windows.

2. Połącz się z modułem AzureAD PowerShell i wykonaj następujące polecenia:

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>Testowanie rozwiązania

Przejdź do swojej dzierżawy aplikacji Saviynt i przetestuj Zarządzanie cyklem życia i dostęp do ładu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](./custom-policy-get-started.md?tabs=applications)

- [Tworzenie aplikacji interfejsu API sieci Web](./add-web-api-application.md)
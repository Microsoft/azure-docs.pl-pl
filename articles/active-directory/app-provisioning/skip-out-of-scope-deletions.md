---
title: Pomiń usuwanie użytkowników spoza zakresu
description: Dowiedz się, jak zastąpić domyślne zachowanie anulowania aprowizacji użytkowników poza zakresem.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 12/10/2019
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: a6cbabe35b223020528d1cf48aa9e0ef9b9f7c05
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256123"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Pomiń usuwanie kont użytkowników, które wykraczają poza zakres

Domyślnie aparat aprowizacji usługi Azure AD nie usuwa lub wyłącza użytkowników, którzy wykraczają poza zakres. Jednak w przypadku niektórych scenariuszy, takich jak Workday do użytkownika w ramach przychodzącej aprowizacji użytkowników usługi AD, to zachowanie może nie być oczekiwane i może chcieć zastąpić to zachowanie domyślne.  

W tym artykule opisano, jak używać interfejsu API Microsoft Graph i Eksploratora interfejsu API Microsoft Graph do ustawiania flagi ***SkipOutOfScopeDeletions** _ kontrolującej przetwarzanie kont, które wykraczają poza zakres. _ Jeśli ***SkipOutOfScopeDeletions** _ ma wartość 0 (false), konta, które wykraczają poza zakres, zostaną wyłączone w miejscu docelowym.
_ Jeśli ***SkipOutOfScopeDeletions** _ ma wartość 1 (true), konta, które wykraczają poza zakres, nie będą wyłączone w miejscu docelowym. Ta flaga jest ustawiona na poziomie _Provisioning App * i można ją skonfigurować przy użyciu interfejs API programu Graph. 

Ponieważ ta konfiguracja jest szeroko używana wraz z dniem *roboczym Active Directory aplikacji aprowizacji użytkowników* , następujące kroki obejmują zrzuty ekranu aplikacji Workday. Jednak konfiguracja może być również używana ze *wszystkimi innymi aplikacjami*, takimi jak usługi ServiceNow, Salesforce i Dropbox.

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Krok 1. Pobieranie inicjowania obsługi App Service Identyfikator podmiotu zabezpieczeń (identyfikator obiektu)

1. Uruchom [Azure Portal](https://portal.azure.com)i przejdź do sekcji właściwości aplikacji aprowizacji. Na przykład jeśli chcesz wyeksportować swój *dzień roboczy do usługi AD, mapowanie aplikacji do inicjowania obsługi administracyjnej użytkowników* , przejdź do sekcji Właściwości tej aplikacji. 
1. W sekcji właściwości aplikacji aprowizacji skopiuj wartość identyfikatora GUID skojarzoną z polem *Identyfikator obiektu* . Ta wartość jest również nazywana **ServicePrincipalId** aplikacji i będzie używana w operacjach Eksploratora grafu.

   ![Identyfikator podmiotu zabezpieczeń App Service Workday](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Krok 2. Logowanie do Microsoft Graph Explorer

1. Uruchom [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Kliknij przycisk "Zaloguj się za pomocą firmy Microsoft" i zaloguj się przy użyciu poświadczeń administratora globalnego usługi Azure AD lub administratora aplikacji.

    ![Logowanie grafu](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Po pomyślnym zalogowaniu zobaczysz szczegóły konta użytkownika w okienku po lewej stronie.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Krok 3. Uzyskiwanie istniejących poświadczeń aplikacji i szczegółów łączności

W Eksploratorze Microsoft Graph Uruchom następujące polecenie GET Query zastępujące [servicePrincipalId] z **servicePrincipalId** wyodrębnionym z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![Pobierz zapytanie dotyczące zadania](./media/skip-out-of-scope-deletions/skip-03.png)

Skopiuj odpowiedź do pliku tekstowego. Będzie wyglądać podobnie do tekstu JSON pokazanego poniżej, z wartościami wyróżnionymi kolorem żółtym specyficznym dla danego wdrożenia. Dodaj wiersze wyróżnione na zielono do końca i zaktualizuj hasło połączenia z produktem Workday wyróżnione kolorem. 

   ![Pobierz odpowiedź na zadanie](./media/skip-out-of-scope-deletions/skip-04.png)

Oto blok JSON, który ma zostać dodany do mapowania. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Krok 4. aktualizowanie punktu końcowego tajemnicy przy użyciu flagi SkipOutOfScopeDeletions

W Eksploratorze grafu uruchom poniższe polecenie, aby zaktualizować punkt końcowy Secret przy użyciu flagi **_SkipOutOfScopeDeletions_* _. 

W poniższym adresie URL Zamień [servicePrincipalId] na _ *servicePrincipalId** wyodrębniony z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Skopiuj zaktualizowany tekst z kroku 3 do "treść żądania" i ustaw dla nagłówka "Content-Type" wartość "Application/JSON" w "nagłówki żądania". 

   ![Żądanie PUT](./media/skip-out-of-scope-deletions/skip-05.png)

Kliknij pozycję "uruchom zapytanie". 

Dane wyjściowe powinny zostać pobrane jako "powodzenie — kod stanu 204". 

   ![Umieść odpowiedź](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Krok 5. sprawdzenie, czy użytkownicy spoza zakresu nie są wyłączani

Możesz przetestować tę flagę w oczekiwany sposób, aktualizując reguły określania zakresu, aby pominąć określonego użytkownika. W poniższym przykładzie wykluczamy pracownika z IDENTYFIKATORem 21173 (kto wcześniej miał zakres) przez dodanie nowej reguły określania zakresu: 

   ![Zrzut ekranu przedstawiający sekcję "Dodawanie filtru określania zakresu" z wyróżnionym przykładem użytkownika.](./media/skip-out-of-scope-deletions/skip-07.png)

W następnym cyklu aprowizacji usługa Azure AD Provisioning określi, że użytkownik 21173 został wystawiony poza zakresem i jeśli właściwość SkipOutOfScopeDeletions jest włączona, reguła synchronizacji dla tego użytkownika wyświetli komunikat, jak pokazano poniżej: 

   ![Przykład zakresu](./media/skip-out-of-scope-deletions/skip-08.png)



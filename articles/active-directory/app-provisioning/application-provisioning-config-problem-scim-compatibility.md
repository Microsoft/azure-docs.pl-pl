---
title: Znane problemy z zgodnością protokołu Standard scim 2,0 — Azure AD
description: Jak rozwiązać typowe problemy ze zgodnością protokołu podczas dodawania aplikacji spoza galerii, która obsługuje standard scim 2,0 do usługi Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 12/03/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: ae08589785d8a482801c71ce3641ba0d66d11133
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782266"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Znane problemy i rozwiązania z standard scim 2,0 zgodności protokołów aprowizacji użytkowników usługi Azure AD

Azure Active Directory (Azure AD) mogą automatycznie inicjować obsługę użytkowników i grup w dowolnej aplikacji lub systemie, który jest przedsunięty przez usługę sieci Web, przy użyciu interfejsu zdefiniowanego w systemie w ramach [specyfikacji protokołu zarządzania tożsamościami (standard scim 2,0) dla wielu domen](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Obsługa protokołu Standard scim 2,0 w usłudze Azure AD jest opisana w temacie [Używanie systemu do zarządzania tożsamościami między domenami (standard scim) w celu automatycznego aprowizacji użytkowników i grup z Azure Active Directoryymi do aplikacji](use-scim-to-provision-users-and-groups.md), które zawierają listę konkretnych części protokołu, które implementuje w celu automatycznego aprowizacji użytkowników i grup z usługi Azure AD do aplikacji obsługujących standard scim 2,0.

W tym artykule opisano bieżące i wcześniejsze problemy związane z przystąpieniem usługi Azure AD User Provisioning do protokołu Standard scim 2,0 i obejście tego problemu.

> [!IMPORTANT]
> Najnowsza aktualizacja usługi Azure AD User Provisioning Standard scim Client została wprowadzona 18 grudnia 2018. Ta aktualizacja dotyczyła znanych problemów ze zgodnością wymienionych w poniższej tabeli. Aby uzyskać więcej informacji na temat tej aktualizacji, zobacz często zadawane pytania poniżej.

## <a name="scim-20-compliance-issues-and-status"></a>Problemy ze zgodnością i stanem Standard scim 2,0

| **Problem ze zgodnością w programie Standard scim 2,0** |  **FIXED?** | **Popraw datę**  |  
|---|---|---|
| Usługa Azure AD wymaga "/scim", aby znajdować się w katalogu głównym adresu URL punktu końcowego Standard scim aplikacji  | Tak  |  18 grudnia, 2018 | 
| Atrybuty rozszerzenia używają notacji kropki "." przed nazwami atrybutów zamiast dwukropka ":" notacji |  Tak  | 18 grudnia, 2018  | 
|  Żądania poprawek dla atrybutów wielowartościowych zawierają nieprawidłową składnię filtru ścieżki | Tak  |  18 grudnia, 2018  | 
|  Żądania utworzenia grupy zawierają nieprawidłowy identyfikator URI schematu | Tak  |  18 grudnia, 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Czy poprawki dotyczące usług zostały automatycznie zastosowane do mojej istniejącej aplikacji Standard scim?

Nie. Ponieważ spowodowałoby to nieprzerwaną zmianę Standard scim aplikacji, które zostały zakodowane w celu współdziałania ze starszym zachowaniem, zmiany nie zostały automatycznie zastosowane do istniejących aplikacji.

Zmiany są stosowane do wszystkich nowych [aplikacji Standard scim, które nie](../manage-apps/configure-single-sign-on-non-gallery-applications.md) są w galerii skonfigurowane w Azure Portal, po dacie poprawki.

Aby uzyskać informacje na temat migrowania istniejącego zadania aprowizacji użytkownika w celu uwzględnienia najnowszych poprawek, zobacz następną sekcję.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Czy można przeprowadzić migrację istniejącego zadania inicjowania obsługi użytkowników opartego na Standard scim w celu uwzględnienia najnowszych poprawek usługi?

Tak. Jeśli to wystąpienie aplikacji jest już używane do logowania jednokrotnego i należy przeprowadzić migrację istniejącego zadania aprowizacji w celu uwzględnienia najnowszych poprawek, wykonaj poniższą procedurę. W tej procedurze opisano, jak za pomocą interfejsu API Microsoft Graph i Eksploratora interfejsu API Microsoft Graph usunąć stare zadanie aprowizacji z istniejącej aplikacji Standard scim i utworzyć nową, która zawiera nowe zachowanie.

> [!NOTE]
> Jeśli aplikacja jest nadal w trakcie tworzenia i nie została jeszcze wdrożona w przypadku logowania jednokrotnego lub aprowizacji użytkowników, najłatwiej jest usunąć wpis aplikacji w sekcji **Azure Active Directory > Enterprise aplikacji** Azure Portal i po prostu dodać nowy wpis dla aplikacji przy użyciu opcji **tworzenia aplikacji > spoza galerii** . Jest to alternatywa dla uruchamiania poniższej procedury.
 
1. Zaloguj się do Azure Portal pod adresem https://portal.azure.com .
2. W sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw** Azure Portal Znajdź i wybierz istniejącą aplikację Standard scim.
3. W sekcji **Właściwości** istniejącej aplikacji Standard scim Skopiuj **Identyfikator obiektu**.
4. W nowym oknie przeglądarki sieci Web przejdź do https://developer.microsoft.com/graph/graph-explorer i zaloguj się jako administrator dla dzierżawy usługi Azure AD, w której aplikacja została dodana.
5. W Eksploratorze grafu uruchom poniższe polecenie, aby zlokalizować identyfikator zadania aprowizacji. Zastąp ciąg "[Object-ID]" IDENTYFIKATORem jednostki usługi (IDENTYFIKATORem obiektu) skopiowanym z trzeciego kroku.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Pobierz zadania](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Pobierz zadania") 


6. W wynikach Skopiuj pełny ciąg "ID" zaczynający się od "customappsso" lub "Standard scim".
7. Uruchom poniższe polecenie, aby pobrać konfigurację mapowania atrybutów, aby można było utworzyć kopię zapasową. Użyj tego samego [Object-ID] jak poprzednio i Zastąp wartość [ID zadania] IDENTYFIKATORem zadania aprowizacji skopiowanego z ostatniego kroku.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Pobierz schemat](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Pobierz schemat") 

8. Skopiuj dane wyjściowe JSON z ostatniego kroku i Zapisz je w pliku tekstowym. Zawiera wszystkie niestandardowe mapowania atrybutów, które zostały dodane do starej aplikacji i powinny być w przybliżeniu kilka tysięcy wierszy JSON.
9. Uruchom poniższe polecenie, aby usunąć zadanie aprowizacji:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Uruchom poniższe polecenie, aby utworzyć nowe zadanie aprowizacji z najnowszymi poprawkami usługi.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. W wynikach ostatniego kroku Skopiuj pełny ciąg "ID" zaczynający się od "Standard scim". Opcjonalnie należy ponownie zastosować stare mapowania atrybutów, uruchamiając poniższe polecenie, zastępując wartość [New-Job-ID] nowym IDENTYFIKATORem zadania, który właśnie został skopiowany, i wprowadzając dane wyjściowe JSON z kroku #7 jako treść żądania.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Wróć do pierwszego okna przeglądarki sieci Web i wybierz kartę **Inicjowanie obsługi** dla swojej aplikacji.
13. Sprawdź konfigurację, a następnie uruchom zadanie aprowizacji. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Czy mogę dodać nową aplikację inną niż Galeria, która ma stare zachowanie aprowizacji użytkowników?

Tak. Jeśli aplikacja została zakodowana w starym zachowaniu, która istniała przed poprawkami, i musi wdrożyć nowe wystąpienie, wykonaj poniższą procedurę. W tej procedurze opisano, jak używać interfejsu API Microsoft Graph i Eksploratora interfejsu API Microsoft Graph do tworzenia zadania inicjowania obsługi Standard scim, które wykazuje stare zachowanie.
 
1. Zaloguj się do Azure Portal pod adresem https://portal.azure.com .
2. w sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > Tworzenie aplikacji** dla Azure Portal, Utwórz nową aplikację w wersji **innej niż Galeria** .
3. W sekcji **Właściwości** nowej aplikacji NIESTANDARDOWEJ Skopiuj **Identyfikator obiektu**.
4. W nowym oknie przeglądarki sieci Web przejdź do https://developer.microsoft.com/graph/graph-explorer i zaloguj się jako administrator dla dzierżawy usługi Azure AD, w której aplikacja została dodana.
5. W Eksploratorze grafów uruchom poniższe polecenie, aby zainicjować konfigurację aprowizacji dla aplikacji.
   Zastąp ciąg "[Object-ID]" IDENTYFIKATORem jednostki usługi (IDENTYFIKATORem obiektu) skopiowanym z trzeciego kroku.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Wróć do pierwszego okna przeglądarki sieci Web i wybierz kartę **Inicjowanie obsługi** dla swojej aplikacji.
7. Ukończ konfigurację aprowizacji użytkowników, jak zwykle.


## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o aprowizacji i dezaktywowaniu aplikacji SaaS](user-provisioning.md)


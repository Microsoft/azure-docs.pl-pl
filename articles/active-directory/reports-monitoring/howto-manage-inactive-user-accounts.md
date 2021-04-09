---
title: Jak zarządzać nieaktywnymi kontami użytkowników w usłudze Azure AD | Microsoft Docs
description: Dowiedz się, jak wykrywać i obsługiwać konta użytkowników w usłudze Azure AD, które staną się przestarzałe
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fb517f8c50ad2c32f23542e60069a0e0a496a2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660668"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Instrukcje: Zarządzanie kontami nieaktywnych użytkowników w usłudze Azure AD

W dużych środowiskach konta użytkowników nie są zawsze usuwane, gdy pracownicy opuszczają organizację. Jako administrator IT chcesz wykryć i obsłużyć te przestarzałe konta użytkowników, ponieważ stanowią one zagrożenie dla bezpieczeństwa.

W tym artykule opisano metodę obsługi przestarzałych kont użytkowników w usłudze Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Co to są konta użytkowników nieaktywnych?

Nieaktywne konta są kontami użytkowników, które nie są już wymagane przez członków organizacji w celu uzyskania dostępu do zasobów. Jeden identyfikator klucza dla nieaktywnych kont jest nieużywany *przez pewien czas* do logowania się do środowiska. Ze względu na to, że nieaktywne konta są powiązane z działaniem logowania, można użyć sygnatury czasowej ostatniego logowania, które zostało pomyślnie wykryte. 

Wyzwaniem tej metody jest zdefiniowanie, co *w przypadku, gdy* ma to zastosowanie w przypadku środowiska. Na przykład użytkownicy mogą nie logować się do środowiska *przez pewien czas*, ponieważ znajdują się na urlopie. Podczas definiowania różnic w przypadku kont nieaktywnych użytkowników należy wziąć pod uwagę wszystkie uzasadnione przyczyny nielogowania się do środowiska. W wielu organizacjach różnica dla nieaktywnych kont użytkowników wynosi od 90 do 180 dni. 

Ostatnim pomyślnym zalogowaniem jest uzyskanie potencjalnego wglądu w ciągłe potrzeby użytkownika w celu uzyskania dostępu do zasobów.  Może pomóc w ustaleniu, czy członkostwo w grupie lub dostęp do aplikacji jest nadal potrzebne, czy może zostać usunięte. W przypadku zarządzania użytkownikami zewnętrznymi można zrozumieć, czy użytkownik zewnętrzny jest nadal aktywny w ramach dzierżawy lub powinien zostać wyczyszczony. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Wykrywanie nieaktywnych kont użytkowników

Wykryjesz nieaktywne konta, oceniając Właściwość **lastSignInDateTime** uwidocznioną przez typ zasobu **signInActivity** interfejsu API **Microsoft Graph** . Korzystając z tej właściwości, można zaimplementować rozwiązanie dla następujących scenariuszy:

- **Użytkownicy według nazwy**: w tym scenariuszu wyszukiwanie określonego użytkownika według nazwy, który umożliwia ocenę lastSignInDateTime: `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Użytkownicy według daty**: w tym scenariuszu żądasz listy użytkowników z lastSignInDateTime przed określoną datą: `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>Co musisz wiedzieć

Ta sekcja zawiera informacje o tym, co należy wiedzieć o właściwości lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Jak mogę uzyskać dostęp do tej właściwości?

Właściwość **lastSignInDateTime** jest udostępniana przez [Typ zasobu SIGNINACTIVITY](/graph/api/resources/signinactivity?view=graph-rest-beta) [interfejsu API REST Microsoft Graph](/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Czy właściwość lastSignInDateTime jest dostępna za pomocą polecenia cmdlet Get-AzureAdUser?

Nie.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Jaka wersja usługi Azure AD jest wymagana do uzyskania dostępu do właściwości?

Możesz uzyskać dostęp do tej właściwości we wszystkich wersjach usługi Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Jakie uprawnienia muszę odczytać właściwość?

Aby odczytać tę właściwość, należy udzielić następujących praw: 

- AuditLogs. Read. All
- Organizacja. Read. All  


### <a name="when-does-azure-ad-update-the-property"></a>Kiedy usługa Azure AD aktualizuje właściwość?

Każde logowanie interaktywne, które powiodło się, spowoduje zaktualizowanie bazowego magazynu danych. Zwykle pomyślne logowania są wyświetlane w powiązanym raporcie logowania w ciągu 10 minut.
 

### <a name="what-does-a-blank-property-value-mean"></a>Co oznacza wartość pustej wartości właściwości?

Aby wygenerować sygnaturę czasową lastSignInDateTime, konieczne jest pomyślne zalogowanie. Ponieważ właściwość lastSignInDateTime jest nową funkcją, wartość właściwości lastSignInDateTime może być pusta, jeśli:

- Ostatnie pomyślne Logowanie użytkownika miało miejsce przed 2020 kwietnia.
- Konto użytkownika, którego to dotyczy, nigdy nie było używane do pomyślnego logowania.

## <a name="next-steps"></a>Następne kroki

* [Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure Active Directory z certyfikatami](tutorial-access-api-with-certificates.md)
* [Dokumentacja interfejsu API inspekcji](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Dokumentacja interfejsu API raportów działań związanych z logowaniem](/graph/api/resources/signin?view=graph-rest-beta)
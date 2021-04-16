---
title: Jak zarządzać nieaktywnymi kontami użytkowników w usłudze Azure AD | Microsoft Docs
description: Dowiedz się, jak wykrywać i obsługiwać konta użytkowników w usłudze Azure AD, które stały się przestarzałe
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
ms.openlocfilehash: 2ea62a8d602cc472269b52c230529aa3f9b86ed4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535096"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>How To: Manage inactive user accounts in Azure AD

W dużych środowiskach konta użytkowników nie zawsze są usuwane, gdy pracownicy opuszczają organizację. Jako administrator IT chcesz wykrywać i obsługiwać te przestarzałe konta użytkowników, ponieważ stanowią one zagrożenie bezpieczeństwa.

W tym artykule wyjaśniono metodę obsługi przestarzałych kont użytkowników w usłudze Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Co to są nieaktywne konta użytkowników?

Nieaktywne konta to konta użytkowników, które nie są już wymagane przez członków organizacji w celu uzyskania dostępu do zasobów. Jednym z kluczowych identyfikatorów nieaktywnych  kont jest to, że przez jakiś czas nie były używane do logowania się do środowiska. Ponieważ nieaktywne konta są powiązane z działaniem logowania, możesz użyć sygnatury czasowej ostatniego pomyślnego logowania, aby je wykryć. 

Wyzwaniem tej metody jest zdefiniowanie, co *przez jakiś czas* oznacza w przypadku środowiska. Na przykład użytkownicy mogą przez jakiś czas nie logować się do środowiska *,* ponieważ są na urlopie. Podczas definiowania różnicy dla nieaktywnych kont użytkowników należy wywnorować wszystkie uzasadnione przyczyny nie logowania się do środowiska. W wielu organizacjach różnica w przypadku nieaktywnych kont użytkowników wynosi od 90 do 180 dni. 

Ostatnie pomyślne logowanie zapewnia potencjalny wgląd w dalsze potrzeby użytkownika dotyczące uzyskiwania dostępu do zasobów.  Może pomóc w ustaleniu, czy członkostwo w grupie lub dostęp do aplikacji jest nadal potrzebny, czy może zostać usunięty. W przypadku zarządzania użytkownikami zewnętrznymi można sprawdzić, czy użytkownik zewnętrzny jest nadal aktywny w dzierżawie, czy powinien zostać wyczyszczony. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Jak wykryć nieaktywne konta użytkowników

Nieaktywne konta są wykrywane przez ocenę właściwości **lastSignInDateTime** udostępnianej przez typ zasobu **signInActivity** **Microsoft Graph** API. Przy użyciu tej właściwości można zaimplementować rozwiązanie dla następujących scenariuszy:

- **Użytkownicy według nazwy:** w tym scenariuszu wyszukuje się określonego użytkownika według nazwy, co umożliwia ocenę ostatniej wartości pola LastSignInDateTime: `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Użytkownicy według daty:** w tym scenariuszu żądasz listy użytkowników z wartością lastSignInDateTime przed określoną datą: `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`

> [!NOTE]
> Może być konieczne wygenerowanie raportu z datą ostatniego logowania wszystkich użytkowników, jeśli tak, można użyć następującego scenariusza.
> **Data i godzina** ostatniego logowania dla wszystkich użytkowników: w tym scenariuszu zażądasz listy wszystkich użytkowników i ostatniego znakuInDateTime dla każdego odpowiedniego użytkownika: `https://graph.microsoft.com/beta/users?$select=displayName,signInActivity` 

## <a name="what-you-need-to-know"></a>Co musisz wiedzieć

W tej sekcji wymieniono, co należy wiedzieć o właściwości lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Jak mogę uzyskać dostęp do tej właściwości?

Właściwość **lastSignInDateTime** jest udostępniane przez typ zasobu [signInActivity](/graph/api/resources/signinactivity?view=graph-rest-beta&preserve-view=true) [interfejsu API REST Microsoft Graph](/graph/overview#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Czy właściwość lastSignInDateTime jest dostępna za pośrednictwem Get-AzureAdUser cmdlet?

Nie.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Jakiej wersji usługi Azure AD potrzebuję, aby uzyskać dostęp do właściwości?

Dostęp do tej właściwości można uzyskać we wszystkich wersjach usługi Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Jakie uprawnienia są potrzebne do odczytania właściwości?

Aby odczytać tę właściwość, musisz przyznać następujące prawa: 

- AuditLogs.Read.All
- Organizacja.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Kiedy usługa Azure AD aktualizuje właściwość?

Każde logowanie interakcyjne, które powiodło się, powoduje aktualizację bazowego magazynu danych. Zazwyczaj pomyślne logowania są wyświetlane w powiązanym raporcie logowania w ciągu 10 minut.
 

### <a name="what-does-a-blank-property-value-mean"></a>Co oznacza pusta wartość właściwości?

Aby wygenerować znacznik czasu lastSignInDateTime, potrzebne jest pomyślne zalogowanie. Ponieważ właściwość lastSignInDateTime jest nową funkcją, wartość właściwości lastSignInDateTime może być pusta, jeśli:

- Ostatnie pomyślne logowanie użytkownika miało miejsce przed kwietniem 2020 r.
- Konto użytkownika, którego dotyczy problem, nigdy nie było używane do pomyślnego logowania.

## <a name="next-steps"></a>Następne kroki

* [Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure Active Directory z certyfikatami](tutorial-access-api-with-certificates.md)
* [Dokumentacja interfejsu API inspekcji](/graph/api/resources/directoryaudit) 
* [Dokumentacja interfejsu API raportów działań logowania](/graph/api/resources/signin)

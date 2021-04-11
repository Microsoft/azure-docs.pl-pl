---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/05/2021
ms.author: mimart
ms.openlocfilehash: 5a2382146cd8b85b8eef54b924a206dda7107b0f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382468"
---
#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/) 

1. W obszarze **Zarządzaj** wybierz pozycję **uprawnienia interfejsu API**.
1. W obszarze **skonfigurowane uprawnienia** wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz kartę **interfejsy API firmy Microsoft** , a następnie wybierz pozycję **Microsoft Graph**.
1. Wybierz pozycję **Uprawnienia aplikacji**.
1. Rozwiń odpowiednią grupę uprawnień i zaznacz pole wyboru uprawnienia do udzielenia aplikacji zarządzania. Na przykład:
    * **Użytkownik**  >  **User. ReadWrite. All**: na potrzeby migracji użytkowników lub scenariuszy zarządzania użytkownikami.
    * **Grupa**  >  **Group. ReadWrite. All**: w celu tworzenia grup, odczytu i aktualizacji członkostwa w grupach oraz usuwania grup.
    * **Wyauditlog**  >  **AuditLog. Read. All**: na potrzeby odczytywania dzienników inspekcji katalogu.
    * **Zasady**  >  **Policy. ReadWrite. TrustFramework**: w scenariuszach ciągłej integracji/ciągłego dostarczania (Ci/CD). Na przykład niestandardowe wdrożenie zasad z Azure Pipelines.
1. Wybierz pozycję **Dodaj uprawnienia**. Poczekaj kilka minut, zanim przejdziesz do kolejnego kroku.
1. Wybierz pozycję **Udziel zgody administratora (nazwa dzierżawy)**.
1. Jeśli obecnie nie zalogowano się za pomocą konta administratora globalnego, zaloguj się przy użyciu konta w dzierżawie usługi Azure AD B2C, do którego przypisano co najmniej rolę *administratora aplikacji w chmurze* , a następnie wybierz pozycję **Udziel zgody administratora na (nazwę dzierżawy)**.
1. Wybierz pozycję **Odśwież**, a następnie sprawdź, czy "udzielono dla..." pojawia się w obszarze **stan**. Propagowanie uprawnień może potrwać kilka minut.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Na stronie Przegląd **zarejestrowanej aplikacji** wybierz pozycję **Ustawienia**.
1. W obszarze **dostęp do interfejsu API** wybierz pozycję **wymagane uprawnienia**.
1. Wybierz **Microsoft Graph**.
1. W obszarze **uprawnienia aplikacji** zaznacz pole wyboru uprawnienia do udzielenia aplikacji zarządzania. Na przykład:
    * **Przeczytaj wszystkie dane dziennika inspekcji**: wybierz to uprawnienie do odczytywania dzienników inspekcji katalogu.
    * **Odczytuj i zapisuj dane katalogu**: wybierz to uprawnienie do migracji użytkowników lub scenariuszy zarządzania użytkownikami.
    * **Odczytuj i zapisuj zasady struktury zaufania organizacji**: wybierz to uprawnienie do scenariuszy ciągłej integracji/ciągłego dostarczania (Ci/CD). Na przykład niestandardowe wdrożenie zasad z Azure Pipelines.
1. Wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Udziel uprawnień**, a następnie wybierz pozycję **tak**. Pełne propagowanie uprawnień może potrwać kilka minut.

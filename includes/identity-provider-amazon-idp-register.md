---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 05/04/2020
ms.author: mimart
ms.openlocfilehash: de1b67265c5c3b3f7247b7f21506eed88abfd550
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900359"
---
1. Zaloguj się do [konsoli dewelopera usługi Amazon](https://developer.amazon.com/dashboard) przy użyciu poświadczeń konta Amazon.
1. Jeśli jeszcze tego nie zrobiono, kliknij pozycję **Utwórz konto**, postępuj zgodnie z procedurami rejestracji dla deweloperów i zaakceptuj zasady.
1. Z poziomu pulpitu nawigacyjnego wybierz pozycję **Zaloguj się za pomocą usługi Amazon**.
1. Wybierz pozycję **Utwórz nowy profil zabezpieczeń**.
1. Wprowadź **nazwę profilu zabezpieczeń**, **opis profilu zabezpieczeń**i **adres URL powiadomienia o ochronie prywatności**, na `https://www.contoso.com/privacy` przykład adres URL informacji o ochronie prywatności to strona, którą zarządzasz dla użytkowników. Następnie kliknij przycisk **Zapisz**.
1. W sekcji **Logowanie za pomocą usługi Amazon konfiguracje** Wybierz utworzoną **nazwę profilu zabezpieczeń** , kliknij ikonę **Zarządzaj** i wybierz pozycję **Ustawienia sieci Web**.
1. W sekcji **Ustawienia sieci Web** Skopiuj wartości **Identyfikator klienta**. Wybierz pozycję **Pokaż klucz tajny** , aby uzyskać klucz tajny klienta, a następnie skopiuj go. Musisz mieć oba te elementy, aby skonfigurować konto Amazon jako dostawcę tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
1. W sekcji **Ustawienia sieci Web** wybierz pozycję **Edytuj**. W polu **dozwolone źródła** i **dozwolone zwrotne adresy**URL wprowadź odpowiednie adresy URL (wymienione powyżej). 
1. Kliknij przycisk **Zapisz**.

---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: c1628894205754c1b8f1fc954796009afbdc8ed4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994325"
---
#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/) 

1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację sieci Web, która powinna mieć dostęp do interfejsu API. Na przykład *webapp1*.
1. W obszarze **Zarządzaj** wybierz pozycję **uprawnienia interfejsu API**.
1. W obszarze **skonfigurowane uprawnienia** wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz kartę **Moje interfejsy API** .
1. Wybierz interfejs API, do którego aplikacja sieci Web ma otrzymać dostęp. Na przykład *webapi1*.
1. W obszarze **uprawnienie** rozwiń pozycję **Demonstracja**, a następnie wybierz zdefiniowane wcześniej zakresy. Na przykład *Demonstracja. odczyt* i *Demonstracja. Write*.
1. Wybierz pozycję **Dodaj uprawnienia**.
1. Wybierz pozycję **Udziel zgody administratora (nazwa dzierżawy)**.
1. Jeśli zostanie wyświetlony monit o wybranie konta, wybierz aktualnie zalogowane konto administratora lub Zaloguj się przy użyciu konta w dzierżawie usługi Azure AD B2C, do którego przypisano co najmniej rolę *administratora aplikacji w chmurze* .
1. Wybierz pozycję **Tak**.
1. Wybierz pozycję **Odśwież**, a następnie sprawdź, czy "udzielono dla..." pojawia się w obszarze **stan** dla obu zakresów.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Wybierz pozycję **aplikacje (starsza wersja)**, a następnie wybierz aplikację sieci Web, która powinna mieć dostęp do interfejsu API. Na przykład *webapp1*.
1. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
1. Z listy rozwijanej **Wybierz interfejs API** wybierz interfejs API, do którego aplikacja sieci Web ma otrzymać dostęp. Na przykład *webapi1*.
1. Z listy rozwijanej **Wybierz zakresy** wybierz zdefiniowane wcześniej zakresy. Na przykład *Demonstracja. odczyt* i *Demonstracja. Write*.
1. Wybierz pozycję **OK**.

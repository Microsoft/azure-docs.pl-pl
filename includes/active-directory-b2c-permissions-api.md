---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: e37419b5d568b0f60e4c6a0c8fed26eb418f501e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84297494"
---
#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Wybierz pozycję **rejestracje aplikacji (wersja zapoznawcza)**, a następnie wybierz aplikację sieci Web, która powinna mieć dostęp do interfejsu API. Na przykład *webapp1*.
1. W obszarze **Zarządzaj**wybierz pozycję **uprawnienia interfejsu API**.
1. W obszarze **skonfigurowane uprawnienia**wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz kartę **Moje interfejsy API** .
1. Wybierz interfejs API, do którego aplikacja sieci Web ma otrzymać dostęp. Na przykład *webapi1*.
1. W obszarze **uprawnienie**rozwiń pozycję **Demonstracja**, a następnie wybierz zdefiniowane wcześniej zakresy. Na przykład *Demonstracja. odczyt* i *Demonstracja. Write*.
1. Wybierz pozycję **Dodaj uprawnienia**.
1. Wybierz pozycję **Udziel zgody administratora (nazwa dzierżawy)**.
1. Jeśli zostanie wyświetlony monit o wybranie konta, wybierz aktualnie zalogowane konto administratora lub Zaloguj się przy użyciu konta w dzierżawie usługi Azure AD B2C, do którego przypisano co najmniej rolę *administratora aplikacji w chmurze* .
1. Wybierz pozycję **tak**.
1. Wybierz pozycję **Odśwież**, a następnie sprawdź, czy "udzielono dla..." pojawia się w obszarze **stan** dla obu zakresów.

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Wybierz pozycję **aplikacje**, a następnie wybierz aplikację sieci Web, która powinna mieć dostęp do interfejsu API. Na przykład *webapp1*.
1. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
1. Z listy rozwijanej **Wybierz interfejs API** wybierz interfejs API, do którego aplikacja sieci Web ma otrzymać dostęp. Na przykład *webapi1*.
1. Z listy rozwijanej **Wybierz zakresy** wybierz zdefiniowane wcześniej zakresy. Na przykład *Demonstracja. odczyt* i *Demonstracja. Write*.
1. Wybierz przycisk **OK**.

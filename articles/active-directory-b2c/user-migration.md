---
title: Podejście do migracji użytkowników
titleSuffix: Azure AD B2C
description: Przeprowadź migrację kont użytkowników z innego dostawcy tożsamości do Azure AD B2C przy użyciu metod wstępnej migracji lub bezproblemowej migracji.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d2d4a61f653c5bedb31223d2eb3d37b92a076821
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010171"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Migrowanie użytkowników do usługi Azure AD B2C

Migrowanie z innego dostawcy tożsamości do Azure Active Directory B2C (Azure AD B2C) może również wymagać migrowania istniejących kont użytkowników. W tym miejscu omówiono dwie metody migracji — *wstępna migracja* i *bezproblemowe Migrowanie*. W obu przypadkach konieczne jest napisanie aplikacji lub skryptu, który używa [interfejsu API Microsoft Graph](microsoft-graph-operations.md) do tworzenia kont użytkowników w Azure AD B2C.

## <a name="pre-migration"></a>Wstępna migracja

W przepływie przed migracją aplikacja do migracji wykonuje następujące kroki dla każdego konta użytkownika:

1. Odczytaj konto użytkownika ze starego dostawcy tożsamości, w tym jego bieżące poświadczenia (nazwa użytkownika i hasło).
1. Utwórz odpowiednie konto w katalogu Azure AD B2C z bieżącymi poświadczeniami.

Użyj przepływu przed migracją w jednej z następujących dwóch sytuacji:

- Masz dostęp do poświadczeń w postaci zwykłego tekstu użytkownika (nazwy użytkownika i hasła).
- Poświadczenia są szyfrowane, ale można je odszyfrować.

Aby uzyskać informacje na temat programistycznego tworzenia kont użytkowników, zobacz [Zarządzanie kontami użytkowników Azure AD B2C przy użyciu Microsoft Graph](microsoft-graph-operations.md).

## <a name="seamless-migration"></a>Bezproblemowe Migrowanie

Użyj bezproblemowego przepływu migracji, jeśli hasła zwykłego tekstu w starym dostawcy tożsamości są niedostępne. Może być tak na przykład, gdy:

- Hasło jest przechowywane w jednokierunkowym formacie szyfrowanym, na przykład za pomocą funkcji skrótu.
- Hasło jest przechowywane przez starszego dostawcę tożsamości w taki sposób, aby nie można było uzyskać do niego dostępu. Na przykład gdy dostawca tożsamości sprawdza poprawność poświadczeń, wywołując usługę sieci Web.

Bezproblemowy przepływ migracji nadal wymaga wstępnej migracji kont użytkowników, ale używa [zasad niestandardowych](custom-policy-get-started.md) do wysyłania zapytań do [interfejsu API REST](custom-policy-rest-api-intro.md) (tworzonego przez użytkownika) w celu ustawienia hasła każdego użytkownika podczas pierwszego logowania.

Bezproblemowy przepływ migracji ma dwie fazy: *wstępne Migrowanie* i *Ustawianie poświadczeń*.

### <a name="phase-1-pre-migration"></a>Faza 1: premigracja

1. Aplikacja do migracji odczytuje konta użytkowników ze starego dostawcy tożsamości.
1. Aplikacja migracji tworzy odpowiednie konta użytkowników w katalogu Azure AD B2C, ale ustawia wygenerowane *losowo hasła* .

### <a name="phase-2-set-credentials"></a>Faza 2: Ustawianie poświadczeń

Po zakończeniu wstępnej migracji kont zasady niestandardowe i interfejs API REST wykonują następujące czynności po zalogowaniu się użytkownika:

1. Odczytaj Azure AD B2C konto użytkownika odpowiadające wprowadzonemu adresowi e-mail.
1. Sprawdź, czy konto jest oflagowane do migracji przez obliczenie atrybutu logicznego rozszerzenia.
    - Jeśli atrybut rozszerzenia zostanie zwrócony `True` , należy wywołać interfejs API REST, aby sprawdzić poprawność hasła do starszego dostawcy tożsamości.
      - Jeśli interfejs API REST określi, że hasło jest niepoprawne, zwróć do użytkownika przyjazny błąd.
      - Jeśli interfejs API REST określi, że hasło jest poprawne, należy zapisać hasło do konta Azure AD B2C i zmienić atrybut rozszerzenia logicznego na `False` .
    - Jeśli zwraca atrybut rozszerzenia logicznego `False` , Kontynuuj proces logowania jako normalny.

Aby zapoznać się z przykładem niestandardowych zasad i interfejsu API REST, zobacz [bezproblemową próbę migracji użytkowników](https://aka.ms/b2c-account-seamless-migration) w witrynie GitHub.

![Diagram schematów blokowych podejścia do migracji użytkowników o bezproblemowej migracji](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagram: bezproblemowe przenoszenie przepływu*

## <a name="best-practices"></a>Najlepsze rozwiązania

### <a name="security"></a>Zabezpieczenia

Bezproblemowe podejście do migracji używa własnego niestandardowego interfejsu API REST do weryfikacji poświadczeń użytkownika względem starszego dostawcy tożsamości.

**Interfejs API REST należy chronić przed atakami typu "wymuszenia".** Osoba atakująca może przesłać kilka haseł w celu pożądanego odgadnięcia poświadczeń użytkownika. Aby pomóc w zapewnieniu takich ataków, Przestań obsługiwać żądania do interfejsu API REST, gdy liczba prób logowania przekroczy określony próg. Ponadto Zabezpiecz komunikację między Azure AD B2C i interfejsem API REST. Aby dowiedzieć się, jak zabezpieczyć interfejsy API usługi RESTful w środowisku produkcyjnym, zobacz [Secure RESTful API](secure-rest-api.md).

### <a name="user-attributes"></a>Atrybuty użytkownika

Nie wszystkie informacje w starszej wersji dostawcy tożsamości powinny zostać zmigrowane do katalogu Azure AD B2C. Zidentyfikuj odpowiedni zestaw atrybutów użytkownika do przechowywania w Azure AD B2C przed przeprowadzeniem migracji.

- **Przechowuj w** Azure AD B2C
  - Nazwa użytkownika, hasło, adresy e-mail, numery telefonów, numery członkostwa/identyfikatory.
  - Znaczniki zgody dla zasad ochrony prywatności i umów licencyjnych użytkownika końcowego.
- **Nie** przechowuj w Azure AD B2C
  - Dane poufne, takie jak numery kart kredytowych, numery ubezpieczenia społecznego (SSN), rejestry medyczne lub inne dane regulowane przez instytucje rządowe lub branżowe.
  - Preferencje marketingowe i komunikacyjne, zachowania użytkowników i szczegółowe informacje.

### <a name="directory-clean-up"></a>Czyszczenie katalogów

Przed rozpoczęciem procesu migracji zapoznaj się z możliwością oczyszczenia katalogu.

- Zidentyfikuj zbiór atrybutów użytkownika, które mają być przechowywane w Azure AD B2C i Migruj tylko potrzebne elementy. W razie potrzeby można utworzyć [atrybuty niestandardowe](user-flow-custom-attributes.md) , aby przechowywać więcej danych o użytkowniku.
- W przypadku migrowania ze środowiska z wieloma źródłami uwierzytelniania (na przykład każda aplikacja ma swój własny katalog użytkownika) należy przeprowadzić migrację do ujednoliconego konta w Azure AD B2C.
- Jeśli wiele aplikacji ma różne nazwy użytkowników, można je przechowywać w Azure AD B2C koncie użytkownika przy użyciu kolekcji tożsamości. W odniesieniu do hasła pozwól użytkownikowi wybrać jeden i ustawić go w katalogu. Na przykład w przypadku bezproblemowej migracji tylko wybrane hasło powinno być przechowywane na koncie Azure AD B2C.
- Usuń nieużywane konta użytkowników przed migracją lub nie Migruj starych kont.

### <a name="password-policy"></a>Zasady dotyczące haseł

Jeśli migrowane konta mają słabsze siły hasła niż [silne siły hasła](../active-directory/authentication/concept-sspr-policy.md) wymuszone przez Azure AD B2C, można wyłączyć wymaganie silnego hasła. Aby uzyskać więcej informacji, zobacz [Właściwość zasad haseł](user-profile-attributes.md#password-policy-attribute).

## <a name="next-steps"></a>Następne kroki

Repozytorium [Azure-AD-B2C/User-Migration](https://github.com/azure-ad-b2c/user-migration) w serwisie GitHub zawiera bezproblemowe przykładowe zasady dotyczące migracji i przykład kodu interfejsu API REST:

[Bezproblemowy sposób migracji użytkowników niestandardowe zasady & przykład kodu interfejsu API REST](https://aka.ms/b2c-account-seamless-migration)

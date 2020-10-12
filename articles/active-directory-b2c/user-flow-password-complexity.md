---
title: Skonfiguruj wymagania dotyczące złożoności hasła
titleSuffix: Azure AD B2C
description: Jak skonfigurować wymagania dotyczące złożoności dla haseł dostarczonych przez użytkowników w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9c7f0ead3a2f93d7f390106b8e7471d58b709023
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87481567"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurowanie wymagań dotyczących złożoności haseł w Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) obsługuje zmianę wymagań dotyczących złożoności dla haseł dostarczonych przez użytkownika końcowego podczas tworzenia konta. Domyślnie Azure AD B2C używa `Strong` haseł. Azure AD B2C obsługuje również opcje konfiguracji w celu kontrolowania złożoności haseł, które mogą być używane przez klientów.

## <a name="password-rule-enforcement"></a>Wymuszanie reguły hasła

Podczas rejestrowania lub resetowania hasła użytkownik końcowy musi podać hasło spełniające reguły złożoności. Reguły złożoności haseł są wymuszane dla przepływu użytkownika. Istnieje możliwość, że jeden przepływ użytkownika wymaga czterech cyfrowego numeru PIN podczas rejestracji, gdy inny przepływ użytkownika wymaga ośmiu ciągów znaków podczas rejestracji. Można na przykład użyć przepływu użytkownika z inną złożonością hasła dla osób dorosłych niż w przypadku dzieci.

Złożoność hasła nigdy nie jest wymuszana podczas logowania. Przed zalogowaniem użytkownicy nigdy nie będą monitowani o zmianę hasła, ponieważ nie spełnia ono wymagań dotyczących bieżącego stopnia złożoności.

Złożoność hasła można skonfigurować w następujących typach przepływów użytkownika:

- Przepływ użytkownika rejestracji lub logowania
- Przepływ użytkownika resetowania hasła

Jeśli używasz zasad niestandardowych, możesz ([skonfigurować złożoność hasła w zasadach niestandardowych](custom-policy-password-complexity.md)).

## <a name="configure-password-complexity"></a>Konfigurowanie złożoności hasła

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
3. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
4. Wybierz pozycję **przepływy użytkownika**.
2. Wybierz przepływ użytkownika, a następnie kliknij pozycję **Właściwości**.
3. W obszarze **złożoność hasła**Zmień złożoność hasła dla tego przepływu użytkownika na **prostą**, **silną**lub **niestandardową**.

### <a name="comparison-chart"></a>Wykres porównawczy

| Złożoność | Opis |
| --- | --- |
| Prostota | Hasło, które ma co najmniej 8 do 64 znaków. |
| Silna | Hasło, które ma co najmniej 8 do 64 znaków. Wymaga 3 z 4 z małych i wielkich liter, cyfr lub symboli. |
| Niestandardowy | Ta opcja zapewnia największą kontrolę nad regułami złożoności haseł.  Umożliwia skonfigurowanie długości niestandardowej.  Umożliwia również akceptowanie haseł tylko do numerów (PIN). |

## <a name="custom-options"></a>Opcje niestandardowe

### <a name="character-set"></a>Zestaw znaków

Umożliwia akceptowanie tylko cyfr (kodów PIN) lub pełnego zestawu znaków.

- **Liczba** dopuszcza tylko cyfry (0-9) podczas wprowadzania hasła.
- **All** zezwala na dowolną literę, cyfrę lub symbol.

### <a name="length"></a>Długość

Pozwala kontrolować wymagania dotyczące długości hasła.

- **Minimalna długość** musi wynosić co najmniej 4.
- **Maksymalna długość** musi być większa lub równa długości minimalnej, a maksymalnie 64 znaków.

### <a name="character-classes"></a>Klasy znaków

Umożliwia sterowanie różnymi typami znaków używanymi w haśle.

- **2 z 4: mała litera, Wielka litera, cyfra (0-9), symbol zapewnia,** że hasło zawiera co najmniej dwa typy znaków. Na przykład liczba i małe litery.
- **3 z 4: mała litera, Wielka litera, cyfra (0-9), symbol zapewnia,** że hasło zawiera co najmniej trzy typy znaków. Na przykład liczba, małe litery i wielkie litery.
- **4 z 4: mała litera, Wielka litera, cyfra (0-9), symbol** zapewnia, że hasło zawiera wszystkie typy znaków.

    > [!NOTE]
    > Wymaganie **4 z 4** może skutkować frustrację użytkownika końcowego. Niektóre badania wykazały, że to wymaganie nie poprawia entropii hasła. Zobacz [wytyczne dotyczące haseł NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)

---
title: Włącz kontroli wieku w Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się więcej na temat identyfikowania małoletnich za pomocą aplikacji.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71a3b38da6a63824a42f64052bf16a5fe0e25483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522435"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Włącz kontroli wieku w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Wiek kontroli w Azure Active Directory B2C (Azure AD B2C) umożliwia zidentyfikowanie małoletnich, które chcą korzystać z aplikacji, z lub bez zgody rodzicielskiej. Można zablokować, aby uniemożliwić logowanie do aplikacji. Lub Zezwalaj na użycie przez program do zalogowania się i podaj stan pomocniczy aplikacji. 

>[!IMPORTANT]
>Ta funkcja jest dostępna w publicznej wersji zapoznawczej. Nie używaj funkcji dla aplikacji produkcyjnych.
>

Gdy kontroli wiek jest włączony dla przepływu użytkownika, użytkownicy są proszeni o podanie daty urodzenia i kraju zamieszkania. Jeśli użytkownik zaloguje się do programu, który nie wprowadził wcześniej informacji, będzie musiał wprowadzić go przy następnym logowaniu. Reguły są stosowane za każdym razem, gdy użytkownik loguje się.

![Zrzut ekranu przedstawiający przepływ zbierania informacji o wieku kontroli](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C korzysta z informacji wprowadzonych przez użytkownika w celu ustalenia, czy są one małoletnim. Pole **grupy wiekowej** zostanie następnie zaktualizowane na swoim koncie. Wartość może być `null` ,,,, `Undefined` `Minor` `Adult` i `NotAdult` .  Pola **grupy wiekowej** i **consentProvidedForMinor** są następnie używane do obliczania wartości **legalAgeGroupClassification**.


## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>Konfigurowanie dzierżawy na potrzeby kontroli wieku

Aby korzystać z kontroli wieku w przepływie użytkownika, należy skonfigurować dzierżawcę do posiadania dodatkowych właściwości.

1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając w górnym menu pozycję **katalog i subskrypcja** . Wybierz katalog, w którym znajduje się Twoja dzierżawa.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **Właściwości** dla dzierżawy w menu po lewej stronie.
1. W obszarze **kontroli wieku** wybierz pozycję **Konfiguruj**.
1. Poczekaj na zakończenie operacji, a dzierżawca zostanie skonfigurowany na potrzeby kontroli wieku.

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>Włącz kontroli wieku w przepływie użytkownika

Po skonfigurowaniu dzierżawy do korzystania z kontroli wieku można użyć tej funkcji w [przepływach użytkowników](user-flow-versions.md) , gdzie jest ona włączona. Aby włączyć kontroli wieku, wykonaj następujące czynności:

1. Utwórz przepływ użytkownika z włączonym kontroli wieku.
1. Po utworzeniu przepływu użytkownika wybierz pozycję **Właściwości** w menu.
1. W sekcji **kontroli wieku** wybierz pozycję **włączone**.
1. W przypadku **tworzenia konta lub logowania** wybierz sposób zarządzania użytkownikami:
    - Zezwalaj małoletnim na dostęp do aplikacji.
    - Blokuj dostęp do aplikacji tylko nieletnich poniżej wieku.
    - Zablokuj wszystkim małoletnim dostęp do aplikacji.
1. **W polu blok** dla wybierz jedną z następujących opcji:
    - **Wyślij kod JSON z powrotem do aplikacji** — ta opcja wysyła odpowiedź z powrotem do aplikacji, która została zablokowana.
    - **Pokaż stronę błędu** — użytkownik jest pokazywany na stronie informującej o tym, że nie mogą uzyskać dostępu do aplikacji.

## <a name="test-your-user-flow"></a>Testowanie przepływu użytkownika

1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom przepływ użytkownika** .
1. Zaloguj się przy użyciu konta lokalnego lub społecznościowego. Następnie wybierz swój kraj pobytu i datę urodzenia, która symuluje drobną. 
1. Powtórz test i wybierz datę urodzenia, która symuluje osobę dorosłą.  

Po zalogowaniu się jako drobny powinien zostać wyświetlony następujący komunikat o błędzie: *Niestety, Twoje logowanie zostało zablokowane. Prawa do prywatności i bezpieczeństwa w trybie online w Twoim kraju uniemożliwiają dostęp do kont należących do elementów podrzędnych.*

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>Włącz kontroli wieku w zasadach niestandardowych

1. Zapoznaj się z przykładem zasad kontroli wieku w serwisie [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/age-gating).
1. W każdym pliku Zastąp ciąg `yourtenant` nazwą dzierżawy Azure AD B2C. Na przykład jeśli nazwa dzierżawy usługi B2C jest *contosob2c*, wszystkie wystąpienia `yourtenant.onmicrosoft.com` stają się dostępne `contosob2c.onmicrosoft.com` .
1. Przekaż pliki zasad.

::: zone-end

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać dostępem użytkowników w Azure AD B2C](manage-user-access.md).


---
title: Wyświetlanie i wyszukanie najnowszego działania związanego z logowaniem ze strony Moje logowanie (wersja zapoznawcza) — Azure Active Directory | Microsoft Docs
description: Szczegółowe informacje na temat sposobu wyświetlania i wyszukiwania ostatnich działań związanych z logowaniem ze strony Moje logowania w portalu My account.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: 738e6507a1642a1ab76938eeaf3294668bcea964
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422298"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Wyświetlanie i wyszukiwanie ostatnich działań związanych z logowaniem ze strony Moje logowania (wersja zapoznawcza)

Możesz wyświetlić wszystkie ostatnie działania związane z logowaniem do konta służbowego, ze strony **Moje logowania** w portalu **My Account** . Przeglądanie historii logowania ułatwia sprawdzanie nietypowego działania, pomagając w Poznaniu się z:

- Jeśli ktoś próbuje odgadnąć hasło.

- Jeśli osoba atakująca pomyślnie zalogował się na konto i z lokalizacji.

- Jakie aplikacje próbowały uzyskać dostęp osoby atakującej.

## <a name="view-your-recent-sign-in-activity"></a>Wyświetlanie ostatniej aktywności logowania

1. Zaloguj się do swojego konta służbowego, a następnie przejdź do https://myaccount.microsoft.com/ strony.

2. Wybierz pozycję **Moje logowania (wersja zapoznawcza)** w okienku nawigacji po lewej stronie lub wybierz link **Przejrzyj ostatnie działanie** z bloku **Moje logowania (wersja zapoznawcza)** .

    ![Strona Moje konto z wyróżnionymi ostatnimi linkami działania](media/my-account-portal/my-account-portal-sign-ins.png)

3. Rozwiń i Przejrzyj każdy z elementów logowania, aby upewnić się, że rozpoznajesz każdą z nich. Jeśli znajdziesz element logowania, który nie jest znany, zdecydowanie zalecamy zmianę hasła, aby chronić Twoje konto, jeśli zostało naruszone.

    ![Ostatnia strona działania z rozwiniętymi szczegółami logowania](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Jeśli zobaczysz Pomyślne logowanie

Należy rozpoznać własne działanie jako normalne. Jeśli jednak zauważysz pomyślne zalogowanie się z nietypowej lokalizacji, przeglądarki lub systemu operacyjnego, może to oznaczać, że osoba atakująca uzyska dostęp do Twojego konta. W tej sytuacji zalecamy natychmiastowe zmianę hasła, a następnie przejście do strony [informacje zabezpieczające](https://mysignins.microsoft.com/security-info) w celu zaktualizowania ustawień zabezpieczeń.

Przed określeniem elementu jest niepoprawna, upewnij się, że nie widzisz fałszywych wartości dodatnich (gdzie element wygląda na wątpliwy). Na przykład określamy przybliżoną lokalizację i mapę na podstawie adresu IP. Sieci komórkowe są szczególnie trudne do lokalizowania, ponieważ czasami kierują ruch przez odległe lokalizacje. Dlatego jeśli zalogowano się przy użyciu urządzenia przenośnego w stanie Waszyngton, w lokalizacji może być widoczne logowanie pochodzące z Kalifornii. W związku z tym zdecydowanie sugerujemy sprawdzenie więcej szczegółów, poza lokalizacją. Należy również upewnić się, że system operacyjny, przeglądarka i cała aplikacja nie mają sensu.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Jeśli zobaczysz nieudane logowanie

Logowanie nie powiodło się, bez działania sesji, oznacza, że podstawowa metoda weryfikacji (nazwa użytkownika/hasło) nie powiodła się. Może to oznaczać, że nazwa użytkownika lub hasło nie zostały wpisane, ale może to oznaczać, że osoba atakująca próbuje odgadnąć hasło. Jeśli uważasz, że osoba atakująca próbuje zrezygnować z odgadnięcia hasła, nie musisz zmieniać hasła, ale zdecydowanie zalecamy zarejestrowanie się w usłudze Azure Multi-Factor Authentication (MFA). Za pomocą usługi MFA, nawet jeśli haker ostatecznie odgadnąć hasło, nie wystarcza do uzyskania dostępu do Twojego konta.

Jeśli zalogowanie nie powiedzie się, oznacza to, że w obszarze działania sesji mówi, że **Dodatkowa weryfikacja zakończyła się niepowodzeniem, nieprawidłowy kod**lub że podstawowe uwierzytelnienie (nazwa użytkownika/hasło) zakończyło się niepowodzeniem, ale uwierzytelnianie MFA nie powiodło się. Jeśli była to osoba atakująca, prawidłowo odgadnięcia hasła, ale nadal nie można przekazać wyzwania usługi MFA. W takim przypadku zalecamy zmianę hasła, ponieważ osoba atakująca uzyskała tę część prawa, a następnie przejdź do strony [informacje zabezpieczające](https://mysignins.microsoft.com/security-info) , aby zaktualizować ustawienia zabezpieczeń.

## <a name="search-for-specific-sign-in-activity"></a>Wyszukiwanie określonego działania związanego z logowaniem

Ostatnie działania związane z logowaniem można wyszukać, korzystając z dowolnych dostępnych informacji. Można na przykład wyszukać ostatnie działania związane z logowaniem według systemu operacyjnego, lokalizacji, aplikacji i tak dalej.

1. Na stronie **Przejrzyj ostatnią aktywność** wpisz informacje, które chcesz wyszukać na pasku **wyszukiwania** . Na przykład wpisz, `My Account` Aby wyszukać całą aktywność zebraną przez aplikację My account.

2. Wybierz przycisk **Wyszukaj** , aby rozpocząć wyszukiwanie.

    ![Strona Ostatnia aktywność, pokazująca wyróżniony pasek wyszukiwania, przycisk wyszukiwania i wyniki](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Następne kroki

Po wyświetleniu najnowszego działania związanego z logowaniem można:

- Wyświetl [informacje zabezpieczające](user-help-security-info-overview.md)lub zarządzaj nimi.

- Wyświetl podłączone [urządzenia](my-account-portal-devices-page.md)lub zarządzaj nimi.

- Wyświetl swoje [organizacje](my-account-portal-organizations-page.md)lub zarządzaj nimi.

- Zobacz, w jaki sposób organizacja [używa danych związanych z prywatnością](my-account-portal-privacy-page.md).

- Zmień [Ustawienia portalu My Account](my-account-portal-settings.md)

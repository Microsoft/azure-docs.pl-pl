---
title: Konfigurowanie usługi Azure Multi-Factor Authentication dla pulpitu wirtualnego systemu Windows — Azure
description: Jak skonfigurować usługę Azure Multi-Factor Authentication w celu zwiększenia bezpieczeństwa na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a769b5584abbd6da89ccb6032e5f0c5ac8ea1cb1
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930526"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Włączanie usługi Azure Multi-Factor Authentication dla pulpitu wirtualnego systemu Windows

Klient systemu Windows dla pulpitu wirtualnego systemu Windows jest doskonałym rozwiązaniem do integrowania pulpitu wirtualnego systemu Windows z maszyną lokalną. Jednak podczas konfigurowania konta pulpitu wirtualnego systemu Windows na kliencie systemu Windows istnieją pewne miary, które należy podjąć, aby zapewnić sobie bezpieczeństwo i użytkowników.

Po pierwszym zalogowaniu klient monituje o podanie nazwy użytkownika, hasła i usługi Azure MFA. Po tym czasie przy następnym logowaniu klient będzie pamiętał token z aplikacji Azure Active Directory (AD) dla przedsiębiorstw. Po wybraniu opcji **Zapamiętaj mnie**użytkownicy mogą się zalogować po ponownym uruchomieniu klienta bez konieczności ponownego wprowadzania poświadczeń.

Zapamiętanie poświadczeń jest wygodne, ale może również sprawić, że wdrożenia w scenariuszach korporacyjnych lub na urządzeniach osobistych są mniej bezpieczne. Aby zapewnić ochronę użytkowników, należy się upewnić, że klient będzie monitować o poświadczenia usługi Azure Multi-Factor Authentication (MFA). W tym artykule opisano sposób konfigurowania zasad dostępu warunkowego dla pulpitu wirtualnego systemu Windows w celu włączenia tego ustawienia.

## <a name="prerequisites"></a>Wymagania wstępne

Oto co należy zrobić:

- Przypisz użytkownikom licencję obejmującą Azure Active Directory — wersja Premium P1 lub P2.
- Grupa Azure Active Directory z użytkownikami przypisanymi jako członkowie grupy.
- Włącz usługę Azure MFA dla wszystkich użytkowników. Aby uzyskać więcej informacji o tym, jak to zrobić, zobacz [temat jak wymagać weryfikacji dwuetapowej dla użytkownika](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> Poniższe ustawienie dotyczy również [klienta sieci Web pulpitu wirtualnego systemu Windows](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

W tej sekcji przedstawiono sposób tworzenia zasad dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego podczas nawiązywania połączenia z pulpitem wirtualnym systemu Windows.

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
2. Przejdź do **Azure Active Directory** > **Security** > **dostępu warunkowego**zabezpieczeń.
3. Wybierz pozycję **nowe zasady**.
4. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
5. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**.
   - W obszarze **dołączanie**wybierz pozycję **Wybierz użytkowników i grupy** > **Użytkownicy i grupy** > wybierz grupę utworzoną na etapie wymagania wstępne.
   - Wybierz pozycję **Done** (Gotowe).
6. W obszarze **aplikacje w chmurze lub akcje** > **Dołącz**wybierz pozycję **Wybierz aplikacje**.
   - Wybierz pozycję **pulpit wirtualny systemu Windows** (Identyfikator aplikacji 9cdead84-a844-4324-93f2-b2e6bb768d07), a następnie wybierz pozycję, a następnie **pozycję** **gotowe**.
   
     ![Zrzut ekranu strony aplikacji w chmurze lub akcji. Pulpity wirtualne systemu Windows i aplikacje klienckie pulpitu wirtualnego systemu Windows są wyróżnione kolorem czerwonym.](media/cloud-apps-enterprise.png)

     >[!NOTE]
     >Aby znaleźć identyfikator aplikacji dla aplikacji, którą chcesz wybrać, przejdź do pozycji **aplikacje dla przedsiębiorstw** i wybierz pozycję **aplikacje firmy Microsoft** z menu rozwijanego Typ aplikacji.

7. W **obszarze kontrola** > dostępu**przyznawanie**wybierz pozycję **Udziel dostępu**, **Wymagaj uwierzytelniania wieloskładnikowego**, a następnie **Wybierz opcję**.
8. W obszarze > **sesja** **kontroli dostępu**wybierz **pozycję częstotliwość logowania**, ustaw wartość **1** i jednostkę na **godziny**, a następnie wybierz pozycję **Wybierz**.
9. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
10. Wybierz pozycję **Utwórz** , aby włączyć zasady.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o zasadach dostępu warunkowego](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Dowiedz się więcej o częstotliwości logowania użytkownika](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)

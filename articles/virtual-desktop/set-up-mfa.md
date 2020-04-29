---
title: Konfigurowanie usługi Azure MFA Authentication dla pulpitu wirtualnego systemu Windows — Azure
description: Jak skonfigurować usługę Azure MFA Authentication w celu zwiększenia bezpieczeństwa na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80998476"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Konfiguracja usługi Azure Multi-Factor Authentication

Klient systemu Windows dla pulpitu wirtualnego systemu Windows jest doskonałym rozwiązaniem do integrowania pulpitu wirtualnego systemu Windows z maszyną lokalną. Jednak podczas konfigurowania konta pulpitu wirtualnego systemu Windows na kliencie systemu Windows istnieją pewne miary, które należy podjąć, aby zapewnić sobie bezpieczeństwo i użytkowników.

Po pierwszym zalogowaniu klient monituje o podanie nazwy użytkownika, hasła i usługi Azure MFA. Po tym czasie przy następnym logowaniu klient będzie pamiętał token z aplikacji Azure Active Directory (AD) dla przedsiębiorstw. Po wybraniu opcji **Zapamiętaj mnie**użytkownicy mogą się zalogować po ponownym uruchomieniu klienta bez konieczności ponownego wprowadzania poświadczeń.

Zapamiętanie poświadczeń jest wygodne, ale może również sprawić, że wdrożenia w scenariuszach korporacyjnych lub na urządzeniach osobistych są mniej bezpieczne. Aby zapewnić ochronę użytkowników, należy się upewnić, że klient będzie monitować o poświadczenia usługi Azure Multi-Factor Authentication (MFA). W tym artykule opisano sposób konfigurowania zasad dostępu warunkowego dla pulpitu wirtualnego systemu Windows w celu włączenia tego ustawienia.

## <a name="prerequisites"></a>Wymagania wstępne

Oto co należy zrobić:

- Przypisz wszystkich użytkowników jedną z następujących licencji:
  - Microsoft 365 E3 lub E5
  - Azure Active Directory — wersja Premium P1 lub P2
  - Enterprise Mobility + Security E3 lub E5
- Grupa Azure Active Directory z użytkownikami przypisanymi jako członkowie grupy.
- Włącz usługę Azure MFA dla wszystkich użytkowników. Aby uzyskać więcej informacji o tym, jak to zrobić, zobacz [temat jak wymagać weryfikacji dwuetapowej dla użytkownika](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

>[!NOTE]
>Poniższe ustawienie dotyczy również [klienta sieci Web pulpitu wirtualnego systemu Windows](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="opt-in-to-the-conditional-access-policy"></a>Zgoda na zasady dostępu warunkowego

1. Otwórz **Azure Active Directory**.

2. Przejdź do karty **wszystkie aplikacje** . Z menu rozwijanego "typ aplikacji" Wybierz opcję **aplikacje przedsiębiorstwa**, a następnie wyszukaj pozycję **Klient pulpitu wirtualnego systemu Windows**.

    ![Zrzut ekranu przedstawiający kartę wszystkie aplikacje. Użytkownik wprowadził "klienta pulpitu wirtualnego systemu Windows" na pasku wyszukiwania, a aplikacja jest wyświetlana w wynikach wyszukiwania.](media/all-applications-search.png)

3. Wybierz pozycję **dostęp warunkowy**.

    ![Zrzut ekranu przedstawiający umieszczenie kursora myszy na karcie dostęp warunkowy przez użytkownika.](media/conditional-access-location.png)

4. Wybierz pozycję **+ nowe zasady**.

   ![Zrzut ekranu strony dostępu warunkowego. Użytkownik przesuwa wskaźnik myszy nad przyciskiem nowe zasady.](media/new-policy-button.png)

5. Wprowadź **nazwę** **reguły**, a następnie **Wybierz** nazwę **grupy** , która została utworzona w sekcji wymagania wstępne.

6. Wybierz pozycję **Wybierz**, a następnie wybierz pozycję **gotowe**.

7. Następnie otwórz **aplikacje lub akcje w chmurze**.

8. Na panelu **Wybierz** wybierz aplikację Enterprise Desktop dla przedsiębiorstw **systemu Windows** .

    ![Zrzut ekranu strony aplikacji w chmurze lub akcji. Użytkownik wybrał aplikację pulpitu wirtualnego systemu Windows, zaznaczając obok niej znacznik wyboru. Wybrana aplikacja zostanie wyróżniona kolorem czerwonym.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >Po lewej stronie ekranu powinna zostać wyświetlona również aplikacja klienta pulpitu wirtualnego systemu Windows, jak pokazano na poniższej ilustracji. Aby zasady działały, potrzebne są zarówno aplikacje pulpitu wirtualnego systemu Windows, jak i klienta pulpitu wirtualnego systemu Windows.
    >
    > ![Zrzut ekranu strony aplikacji w chmurze lub akcji. Pulpity wirtualne systemu Windows i aplikacje klienckie pulpitu wirtualnego systemu Windows są wyróżnione kolorem czerwonym.](media/cloud-apps-enterprise-selected.png)

9. Wybierz pozycję **Wybierz**

10. Następnie **Otwórz** przystawkę 

11. Wybierz pozycję **Wymagaj uwierzytelniania wieloskładnikowego**, a następnie wybierz pozycję **Wymagaj jednej z wybranych kontrolek**.
   
    ![Zrzut ekranu strony dotacji. Wybrano "Wymagaj uwierzytelniania wieloskładnikowego".](media/grant-page.png)

    >[!NOTE]
    >Jeśli w organizacji znajdują się urządzenia zarejestrowane w usłudze MDM i nie chcesz, aby były wyświetlane monity usługi MFA, możesz również wybrać opcję **Wymagaj, aby urządzenie było oznaczone jako zgodne**.

12. Wybierz **sesję**.

13. Ustaw **częstotliwość logowania** na **aktywną**, a następnie zmień jej wartość na **1 godzinę**.

    ![Zrzut ekranu strony sesji. Menu sesji zawiera menu rozwijane częstotliwości logowania zostały zmienione na wartość "1" i "godziny".](media/sign-in-frequency.png)
   
    >[!NOTE]
    >Aktywne sesje w środowisku pulpitu wirtualnego systemu Windows będą nadal działały po zmianie zasad. Jeśli jednak nastąpi odłączenie lub wylogowanie, musisz podać swoje poświadczenia ponownie po 60 minutach. Podczas zmieniania ustawień można przedłużyć limit czasu (o ile jest on wyrównany do zasad zabezpieczeń organizacji).
    >
    >Ustawieniem domyślnym jest stopniowe okno z 90 dni, co oznacza, że klient zostanie poproszony o ponowne zalogowanie się, gdy spróbujesz uzyskać dostęp do zasobu po wystąpieniu nieaktywności na komputerze przez 90 dni lub dłużej.

14. Włącz zasady.

15. Wybierz pozycję **Utwórz** , aby potwierdzić zasady.

Wszystko gotowe! Przetestuj zasady, aby upewnić się, że lista dozwolonych działa zgodnie z oczekiwaniami.

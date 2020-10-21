---
title: Konfigurowanie uwierzytelniania wieloskładnikowego platformy Azure dla pulpitu wirtualnego systemu Windows — Azure
description: Konfigurowanie uwierzytelniania wieloskładnikowego platformy Azure w celu zwiększenia bezpieczeństwa na pulpicie wirtualnym systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 10/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 35af8191cfe237175cbd6669797d1744ac3ecd49
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92312646"
---
# <a name="enable-azure-multifactor-authentication-for-windows-virtual-desktop"></a>Włączanie uwierzytelniania wieloskładnikowego platformy Azure dla pulpitu wirtualnego systemu Windows

>[!IMPORTANT]
> Jeśli odwiedzasz Tę stronę z dokumentacji pulpitu wirtualnego systemu Windows (klasycznego), pamiętaj, aby [wrócić do dokumentacji pulpitu wirtualnego systemu Windows (klasycznego)](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) po zakończeniu.

Klient systemu Windows dla pulpitu wirtualnego systemu Windows jest doskonałym rozwiązaniem do integrowania pulpitu wirtualnego systemu Windows z maszyną lokalną. Jednak podczas konfigurowania konta pulpitu wirtualnego systemu Windows na kliencie systemu Windows istnieją pewne miary, które należy podjąć, aby zapewnić sobie bezpieczeństwo i użytkowników.

Po pierwszym zalogowaniu Klient prosi o podanie nazwy użytkownika, hasła i uwierzytelniania wieloskładnikowego platformy Azure. Po tym czasie przy następnym logowaniu klient będzie pamiętał token z aplikacji Azure Active Directory (AD) dla przedsiębiorstw. Po wybraniu opcji **Zapamiętaj mnie** w przypadku monitu o podanie poświadczeń dla hosta sesji użytkownicy mogą się zalogować po ponownym uruchomieniu klienta bez konieczności ponownego wprowadzania poświadczeń.

Zapamiętanie poświadczeń jest wygodne, ale może również sprawić, że wdrożenia w scenariuszach korporacyjnych lub na urządzeniach osobistych są mniej bezpieczne. Aby chronić użytkowników, należy się upewnić, że klient nadal będzie pytać o poświadczenia uwierzytelniania wieloskładnikowego platformy Azure. W tym artykule opisano sposób konfigurowania zasad dostępu warunkowego dla pulpitu wirtualnego systemu Windows w celu włączenia tego ustawienia.

## <a name="prerequisites"></a>Wymagania wstępne

Oto co należy zrobić:

- Przypisz użytkownikom licencję obejmującą Azure Active Directory — wersja Premium P1 lub P2.
- Grupa Azure Active Directory z użytkownikami przypisanymi jako członkowie grupy.
- Włącz uwierzytelnianie wieloskładnikowe Azure dla wszystkich użytkowników. Aby uzyskać więcej informacji o tym, jak to zrobić, zobacz [temat jak wymagać weryfikacji dwuetapowej dla użytkownika](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> Poniższe ustawienie dotyczy również [klienta sieci Web pulpitu wirtualnego systemu Windows](https://rdweb.wvd.microsoft.com/arm/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Poniżej przedstawiono sposób tworzenia zasad dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego podczas nawiązywania połączenia z pulpitem wirtualnym systemu Windows:

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
2. Przejdź do **Azure Active Directory**  >  **Security**  >  **dostępu warunkowego**zabezpieczeń.
3. Wybierz pozycję **nowe zasady**.
4. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
5. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**.
6. W obszarze **dołączanie**wybierz pozycję **Wybierz użytkowników i grupy**  >  **Użytkownicy i grupy** > wybierz grupę utworzoną na etapie [wymagania wstępne](#prerequisites) .
7. Wybierz pozycję **Gotowe**.
8. W obszarze **aplikacje w chmurze lub akcje**  >  **Dołącz**wybierz pozycję **Wybierz aplikacje**.
9. Wybierz jedną z następujących aplikacji, w zależności od używanej wersji pulpitu wirtualnego systemu Windows.
   
   - Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego), wybierz następujące aplikacje:
       
       - **Pulpit wirtualny systemu Windows** (Identyfikator aplikacji 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Klient pulpitu wirtualnego systemu Windows** (Identyfikator aplikacji fa4345a4-a730-4230-84a8-7d9651b86739), który umożliwia ustawianie zasad na kliencie sieci Web
       
        Następnie przejdź do kroku 11.

   - Jeśli używasz pulpitu wirtualnego systemu Windows, wybierz tę aplikację zamiast:
       
       -  **Pulpit wirtualny systemu Windows** (Identyfikator aplikacji 9cdead84-a844-4324-93f2-b2e6bb768d07)
       
        Następnie przejdź do kroku 10.

   >[!IMPORTANT]
   > Nie wybieraj aplikacji o nazwie Windows Virtual Desktop Azure Resource Manager Provider (50e95039-B200-4007-bc97-8d5790743a63). Ta aplikacja jest używana tylko do pobierania źródła danych użytkownika i nie powinna mieć uwierzytelniania wieloskładnikowego.
   > 
   > Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego), jeśli zasady dostępu warunkowego blokują dostęp i tylko nie wykluczają identyfikatorów aplikacji pulpitu wirtualnego systemu Windows, możesz rozwiązać ten problem, dodając identyfikator aplikacji 9cdead84-a844-4324-93f2-b2e6bb768d07 do zasad. Nie dodawaj tego identyfikatora aplikacji spowoduje odnalezienie źródła zasobów pulpitu wirtualnego systemu Windows (klasycznego).

10. Przejdź do pozycji **warunki**  >  **aplikacje klienckie**, a następnie wybierz miejsce, do którego chcesz zastosować zasady:
    
    - Wybierz pozycję **przeglądarka** , aby zastosować zasady do klienta sieci Web.
    - Wybierz pozycję **aplikacje mobilne i klienci stacjonarni,** Jeśli chcesz zastosować zasady do innych klientów.
    - Zaznacz oba pola wyboru, jeśli chcesz zastosować zasady do wszystkich klientów.
   
    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu strony aplikacji klienckich. Użytkownik zaznaczył pole wyboru aplikacje mobilne i klienci stacjonarni.](media/select-apply.png)

11. Po wybraniu aplikacji wybierz pozycję **Wybierz**, a następnie wybierz pozycję **gotowe**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu strony aplikacji w chmurze lub akcji. Pulpity wirtualne systemu Windows i aplikacje klienckie pulpitu wirtualnego systemu Windows są wyróżnione kolorem czerwonym.](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >Aby znaleźć identyfikator aplikacji dla aplikacji, którą chcesz wybrać, przejdź do pozycji **aplikacje dla przedsiębiorstw** i wybierz pozycję **aplikacje firmy Microsoft** z menu rozwijanego Typ aplikacji.

12. W obszarze **Kontrola dostępu**  >  **przyznawanie**wybierz pozycję **Udziel dostępu**, **Wymagaj uwierzytelniania wieloskładnikowego**, a następnie **Wybierz opcję**.
13. W obszarze sesja **kontroli dostępu**  >  **Session**wybierz pozycję **częstotliwość logowania**, ustaw wartość na żądany czas między wierszami, a następnie wybierz pozycję **Wybierz**. Na przykład ustawienie wartości **1** i jednostki na **godziny**wymaga uwierzytelniania wieloskładnikowego, jeśli połączenie zostanie uruchomione godzinę po ostatnim z nich.
14. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
15. Wybierz pozycję **Utwórz** , aby włączyć zasady.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o zasadach dostępu warunkowego](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Dowiedz się więcej o częstotliwości logowania użytkownika](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)

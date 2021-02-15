---
title: Konfigurowanie tymczasowego dostępu w usłudze Azure AD w celu rejestrowania metod uwierzytelniania bezhaseł
description: Dowiedz się, jak skonfigurować i umożliwić użytkownikom rejestrowanie metod uwierzytelniania bezhaseł przy użyciu dostępu tymczasowego (TAP)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: justinha
author: inbarckms
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d45119fa86ab47e6a625c628d8cb9763db83bd
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520991"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Konfigurowanie przekazywania tymczasowego dostępu w usłudze Azure AD w celu rejestrowania metod uwierzytelniania bezhaseł (wersja zapoznawcza)

Metody uwierzytelniania bez hasła, takie jak FIDO2 i logowanie za pomocą telefonu bezobsługowego hasłem przy użyciu aplikacji Microsoft Authenticator, umożliwiają użytkownikom bezpieczne logowanie się, nie używając hasła. Użytkownicy mogą w jeden z dwóch sposobów przeprowadzać Bootstrap metody bezhasła:

- Korzystanie z istniejących metod uwierzytelniania wieloskładnikowego usługi Azure AD 
- Używanie tymczasowego dostępu (TAP) 

NACIŚNIĘCIe jest ograniczonym do czasu kodem dostępu wystawionym przez administratora, który spełnia wymagania dotyczące silnych uwierzytelnień i może służyć do dołączania innych metod uwierzytelniania, w tym bez haseł. Program TAP ułatwia również odzyskiwanie, gdy użytkownik straci lub zapomniał silnego czynnika uwierzytelniania, takiego jak FIDO2 Security Key lub Microsoft Authenticator App, ale musi zalogować się, aby zarejestrować nowe metody silnego uwierzytelniania.


W tym artykule opisano sposób włączania i używania programu TAP w usłudze Azure AD przy użyciu Azure Portal. Te akcje można także wykonać przy użyciu interfejsów API REST. 

>[!NOTE]
>Przejściowy przebieg dostępu jest obecnie w publicznej wersji zapoznawczej. Niektóre funkcje mogą nie być obsługiwane lub mieć ograniczone możliwości. 

## <a name="enable-the-tap-policy"></a>Włączanie zasad TAP

Zasady TAP definiują ustawienia, takie jak okres istnienia przebiegów utworzonych w dzierżawie, lub użytkowników i grup, którzy mogą korzystać z programu TAP do logowania. Aby można było zalogować się przy użyciu narzędzia TAP, należy włączyć zasady metody uwierzytelniania i wybrać użytkowników, którzy mogą logować się przy użyciu NACISKu.
Chociaż można utworzyć NACIŚNIĘCIe dla dowolnego użytkownika, tylko te zawarte w zasadach mogą się z nim logować.

Posiadacze ról Administrator globalny i administrator zasad metod uwierzytelniania mogą zaktualizować zasady wybierania metody uwierzytelniania.
Aby skonfigurować zasady dotyczące wybierania metody uwierzytelniania:

1. Zaloguj się do Azure Portal jako Administrator globalny, a następnie kliknij pozycję **Azure Active Directory**  >    >  **metody uwierzytelniania** zabezpieczeń  >  **dostęp tymczasowy**.
1. Kliknij przycisk **tak** , aby włączyć zasady, wybierz użytkowników, którym zastosowano zasady, oraz wszystkie ustawienia **Ogólne** .

   ![Zrzut ekranu przedstawiający sposób włączania zasad wybierania metody uwierzytelniania](./media/how-to-authentication-temporary-access-pass/policy.png)

   Wartość domyślna i zakres dozwolonych wartości są opisane w poniższej tabeli.


   | Ustawienie          | Wartości domyślne | Dozwolone wartości               | Komentarze                                                                                                                                                                                                                                                                 |   |
   |------------------|----------------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    Minimalny okres istnienia | 1 godzina         | 10 – 43200 minut (30 dni) | Minimalna liczba minut, przez którą NACIŚNIĘCIe jest prawidłowe.                                                                                                                                                                                                                         |   |
   | Maksymalny okres istnienia | 24 godziny       | 10 – 43200 minut (30 dni) | Maksymalna liczba minut, przez którą NACIŚNIĘCIe jest prawidłowe.                                                                                                                                                                                                                         |   |
   | Domyślny okres istnienia | 1 godzina         | 10 – 43200 minut (30 dni) | Wartości domyślne mogą być przesłonięte przez poszczególne przebiegi w ramach minimalnego i maksymalnego okresu istnienia skonfigurowanego przez zasady                                                                                                                                                |   |
   | Jednorazowe użycie     | Fałsz          | PRAWDA/FAŁSZ                 | Jeśli zasady są ustawione na wartość false, w dzierżawie może być używana jedna lub więcej niż raz w okresie ważności (maksymalny okres istnienia). Wymuszając jednorazowe użycie w zasadach TAP, wszystkie przebiegi utworzone w dzierżawie zostaną utworzone jako jednorazowe użycie. |   |
   | Długość           | 8              | 8-48 znaków              | Definiuje długość kodu dostępu.                                                                                                                                                                                                                                      |   |

## <a name="create-a-tap-in-the-azure-ad-portal"></a>Tworzenie programu TAP w portalu usługi Azure AD

Po włączeniu zasad TAP można utworzyć opcję dla użytkownika w usłudze Azure AD. Role te mogą wykonywać następujące działania związane z naciśnięciem przycisku.

- Administrator globalny może tworzyć, usuwać, wyświetlać i wybierać z dowolnego użytkownika (z wyjątkiem siebie).
- Administratorzy uprzywilejowanego uwierzytelniania mogą tworzyć, usuwać, przeglądać i wybierać administratorów i członków (z wyjątkiem siebie).
- Administratorzy uwierzytelniania mogą tworzyć, usuwać, przeglądać i wybierać elementy członkowskie (z wyjątkiem siebie).
- Administrator globalny może wyświetlić szczegóły wybierania użytkownika (bez odczytywania samego kodu).

Aby utworzyć NACIŚNIĘCIe:

1. Zaloguj się do portalu jako Administrator globalny, administrator uwierzytelniania uprzywilejowanego lub administrator uwierzytelniania. 
1. Kliknij przycisk **Azure Active Directory**, przejdź do opcji użytkownicy, wybierz użytkownika, na przykład *Krzysztof zielony*, a następnie wybierz **metody uwierzytelniania**.
1. W razie potrzeby wybierz opcję, aby **wypróbować nowe metody uwierzytelniania użytkowników**.
1. Wybierz opcję, aby **dodać metody uwierzytelniania**.
1. Poniżej **Wybierz metodę**, kliknij przycisk **dostęp tymczasowy (wersja zapoznawcza)**.
1. Zdefiniuj niestandardowy czas aktywacji lub czas trwania, a następnie kliknij przycisk **Dodaj**.

   ![Zrzut ekranu przedstawiający sposób tworzenia NACISKu](./media/how-to-authentication-temporary-access-pass/create.png)

1. Po dodaniu zostaną wyświetlone szczegóły dotyczące TAP. Zanotuj rzeczywistą wartość naciśnięcia przycisku. Podajesz tę wartość użytkownikowi. Nie można wyświetlić tej wartości po kliknięciu przycisku **OK**.
   
   ![Zrzut ekranu przedstawiający szczegóły wybierania](./media/how-to-authentication-temporary-access-pass/details.png)

## <a name="use-a-tap"></a>Korzystanie z programu TAP

Najbardziej typowym zastosowaniem dla NACIŚNIĘCIa jest możliwość rejestrowania szczegółów uwierzytelniania podczas pierwszego logowania, bez konieczności wykonywania dodatkowych czynności w zakresie zabezpieczeń. Metody uwierzytelniania są rejestrowane pod adresem [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Użytkownicy mogą również w tym miejscu aktualizować istniejące metody uwierzytelniania.

1. Otwórz przeglądarkę internetową, aby [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Wprowadź nazwę UPN konta, na przykład *tapuser@contoso.com* .
1. Jeśli użytkownik zostanie uwzględniony w zasadach TAP, zobaczy ekran, aby wejść do niego.
1. Wprowadź naciśnij przycisk, który został wyświetlony w Azure Portal.

   ![Zrzut ekranu przedstawiający sposób wprowadzania NACISKu](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>W przypadku domen federacyjnych wybór jest preferowany w stosunku do Federacji. Użytkownik z programem TAP zakończy uwierzytelnianie w usłudze Azure AD i nie zostanie przekierowany do federacyjnego dostawcy tożsamości (dostawcy tożsamości).

Użytkownik jest zalogowany i może aktualizować lub rejestrować metodę, taką jak FIDO2 Security Key. Użytkownicy, którzy zaktualizują swoje metody uwierzytelniania z powodu utraty swoich poświadczeń lub urządzenia, powinni upewnić się, że usuwają stare metody uwierzytelniania.

Użytkownicy mogą również korzystać z usługi TAP, aby zarejestrować się do logowania jednokrotnego przy użyciu aplikacji uwierzytelniającej. Aby uzyskać więcej informacji, zobacz [Dodawanie konta służbowego do aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-add-work-school-account.md).

![Zrzut ekranu przedstawiający sposób wprowadzania narzędzia TAP przy użyciu konta służbowego](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-tap"></a>Usuwanie NACISKu

Nie można użyć wygasłego NACIŚNIĘCIa. W obszarze **metody uwierzytelniania** dla użytkownika kolumna **szczegółów** jest wyświetlana po wygaśnięciu elementu TAP. Możesz usunąć te wygasłe kurki, wykonując następujące czynności:

1. W portalu usługi Azure AD przejdź do pozycji **Użytkownicy**, wybierz użytkownika, na przykład *naciśnij pozycję użytkownik*, a następnie wybierz pozycję **metody uwierzytelniania**.
1. Po prawej stronie metody uwierzytelniania **dostęp przejściowy (wersja zapoznawcza)** pokazanej na liście wybierz pozycję **Usuń**.

## <a name="replace-a-tap"></a>Zastąp naciśnięcie przycisku 

- Użytkownik może mieć tylko jedno NACIŚNIĘCIe. Kod dostępu może być używany w czasie rozpoczęcia i zakończenia wybierania.
- Jeśli użytkownik wymaga nowego NACIŚNIĘCIa:
  - Jeśli istniejący program TAP jest prawidłowy, administrator musi usunąć istniejący wybór i utworzyć nowy przebieg dla użytkownika. Usunięcie prawidłowego elementu TAP spowoduje odwołanie sesji użytkownika. 
  - Jeśli istniejący program TAP utracił ważność, nowy program TAP przesłoni istniejący wybór i nie będzie odwoływać sesji użytkownika.

Aby uzyskać więcej informacji na temat standardów NIST na potrzeby dołączania i odzyskiwania, zobacz temat [specjalna publikacja NIST 63A](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Ograniczenia

Należy pamiętać o następujących ograniczeniach:

- W przypadku użycia jednorazowego NACISKu na rejestrację metody bez hasła, takiej jak FIDO2 lub logowanie za pomocą telefonu, użytkownik musi wykonać rejestrację w ciągu 10 minut od zalogowania się za pomocą jednorazowego NACISKania. To ograniczenie nie dotyczy NACISKu, którego można użyć więcej niż raz.
- Użytkownicy-Goście nie mogą zalogować się przy użyciu NACISKu.
- Użytkownicy w zakresie zasad rejestracji samoobsługowego resetowania hasła (SSPR) będą musieli zarejestrować jedną z metod SSPR po zalogowaniu się za pomocą polecenia TAP. Jeśli użytkownik chce używać tylko klucza FIDO2, wykluczać je z zasad SSPR lub wyłączyć zasady rejestracji SSPR. 
- Nie można używać programu TAP z rozszerzeniem serwera zasad sieciowych (NPS) i karty Active Directory Federation Services (AD FS).
- Gdy w dzierżawie włączono bezproblemowe logowanie jednokrotne, użytkownicy są monitowani o wprowadzenie hasła. **Zamiast tego użyj linku dostępu do danych tymczasowych** dla użytkownika, aby zalogować się za pomocą naciśnięcia przycisku.

![Zrzut ekranu przedstawiający użycie polecenia TAP](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów    

- Jeśli podczas logowania nie jest oferowany użytkownik, należy sprawdzić następujące kwestie:
  - Użytkownik należy do zakresu zasad wybierania metody uwierzytelniania.
  - Użytkownik ma prawidłowy wybór i jeśli jest jednorazowym użyciem, nie był jeszcze używany.
- **W przypadku zablokowania tymczasowego logowania dostęp z powodu zasady poświadczeń użytkownika** podczas logowania przy użyciu programu TAP należy sprawdzić następujące kwestie:
  - Użytkownik korzysta z pojedynczego NACISKu, gdy zasady metody uwierzytelniania wymagają jednorazowego NACIŚNIĘCIa.
  - Pojedynczy wybór jest już używany.

## <a name="next-steps"></a>Następne kroki

- [Planowanie wdrożenia uwierzytelniania bezhaseł w Azure Active Directory](howto-authentication-passwordless-deployment.md)


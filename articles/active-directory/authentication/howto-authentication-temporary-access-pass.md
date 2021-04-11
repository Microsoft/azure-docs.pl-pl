---
title: Konfigurowanie tymczasowego dostępu w usłudze Azure AD w celu rejestrowania metod uwierzytelniania bezhaseł
description: Informacje o konfigurowaniu i umożliwieniu użytkownikom rejestrowania metod uwierzytelniania bezhasła przy użyciu tymczasowego dostępu
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8774df6a2eee15f8b5a0c37362e5b20f14b07549
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167366"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Konfigurowanie przekazywania tymczasowego dostępu w usłudze Azure AD w celu rejestrowania metod uwierzytelniania bezhaseł (wersja zapoznawcza)

Metody uwierzytelniania bez hasła, takie jak FIDO2 i logowanie za pomocą telefonu bezobsługowego hasłem przy użyciu aplikacji Microsoft Authenticator, umożliwiają użytkownikom bezpieczne logowanie się, nie używając hasła. Użytkownicy mogą w jeden z dwóch sposobów przeprowadzać Bootstrap metody bezhasła:

- Korzystanie z istniejących metod Multi-Factor Authentication usługi Azure AD 
- Używanie tymczasowego dostępu (TAP) 

Dostęp tymczasowy to ograniczony czasowo kod dostępu wystawiony przez administratora, który spełnia wymagania dotyczące silnych uwierzytelnień i może służyć do dołączania innych metod uwierzytelniania, w tym bez haseł. Tymczasowe przekazywanie dostępu sprawia również, że odzyskiwanie jest łatwiejsze, gdy użytkownik straci lub zapomniał silnego czynnika uwierzytelniania, takiego jak FIDO2 Security Key lub Microsoft Authenticator App, ale musi zalogować się, aby zarejestrować nowe metody silnego uwierzytelniania.


W tym artykule opisano sposób włączania i używania tymczasowego dostępu w usłudze Azure AD przy użyciu Azure Portal. Te akcje można także wykonać przy użyciu interfejsów API REST. 

>[!NOTE]
>Przejściowy przebieg dostępu jest obecnie w publicznej wersji zapoznawczej. Niektóre funkcje mogą nie być obsługiwane lub mieć ograniczone możliwości. 

## <a name="enable-the-temporary-access-pass-policy"></a>Włączanie zasad dostępu tymczasowego

Zasady dostępu tymczasowego określają ustawienia, takie jak okres istnienia przebiegów utworzonych w dzierżawie, lub użytkowników i grup, którzy mogą używać tymczasowego dostępu do logowania. Aby można było zalogować się przy użyciu dostępu tymczasowego, należy włączyć zasady metody uwierzytelniania i wybrać użytkowników i grupy, którzy mogą logować się przy użyciu tymczasowego dostępu.
Chociaż można utworzyć tymczasowy dostęp dla dowolnego użytkownika, tylko te zawarte w zasadach mogą się z nim logować.

Posiadacze ról Administrator globalny i administrator zasad metod uwierzytelniania mogą zaktualizować zasady metody uwierzytelniania dostępu tymczasowego.
Aby skonfigurować zasady metody uwierzytelniania dla dostępu tymczasowego:

1. Zaloguj się do Azure Portal jako Administrator globalny, a następnie kliknij pozycję **Azure Active Directory**  >    >  **metody uwierzytelniania** zabezpieczeń  >  **dostęp tymczasowy**.
1. Kliknij przycisk **tak** , aby włączyć zasady, wybierz użytkowników, którym zastosowano zasady, oraz wszystkie ustawienia **Ogólne** .

   ![Zrzut ekranu przedstawiający sposób włączania zasad metody uwierzytelniania dostępu tymczasowego](./media/how-to-authentication-temporary-access-pass/policy.png)

   Wartość domyślna i zakres dozwolonych wartości są opisane w poniższej tabeli.


   | Ustawienie | Wartości domyślne | Dozwolone wartości | Komentarze |
   |---|---|---|---|
   | Minimalny okres istnienia | 1 godzina | 10 – 43200 minut (30 dni) | Minimalna liczba minut ważności tymczasowego dostępu. |
   | Maksymalny okres istnienia | 24 godziny | 10 – 43200 minut (30 dni) | Maksymalna liczba minut ważności tymczasowego dostępu. |
   | Domyślny okres istnienia | 1 godzina | 10 – 43200 minut (30 dni) | Wartości domyślne mogą być przesłonięte przez poszczególne przebiegi w ramach minimalnego i maksymalnego okresu istnienia skonfigurowanego przez zasady. |
   | Jednorazowe użycie | Fałsz | PRAWDA/FAŁSZ | Jeśli zasady są ustawione na wartość false, w dzierżawie może być używana jedna lub więcej niż raz w okresie ważności (maksymalny okres istnienia). Wymuszając jednorazowe użycie w ramach zasad dostępu tymczasowego, wszystkie przebiegi utworzone w dzierżawie zostaną utworzone jako jednorazowe użycie. |
   | Długość | 8 | 8-48 znaków | Definiuje długość kodu dostępu. |

## <a name="create-a-temporary-access-pass"></a>Tworzenie tymczasowego dostępu

Po włączeniu zasad można utworzyć tymczasowy dostęp dla użytkownika w usłudze Azure AD. Role te mogą wykonywać następujące działania związane z tymczasowym przebiegiem dostępu.

- Administrator globalny może tworzyć, usuwać i wyświetlać tymczasowy dostęp do wszystkich użytkowników (z wyjątkiem siebie).
- Administratorzy uprzywilejowanego uwierzytelniania mogą tworzyć, usuwać, wyświetlać tymczasowy dostęp do wszystkich administratorów i członków (poza nimi)
- Administratorzy uwierzytelniania mogą tworzyć, usuwać i wyświetlać tymczasowe dostęp do elementów członkowskich (z wyjątkiem siebie).
- Administrator globalny może wyświetlić szczegóły dotyczące tymczasowego dostępu użytkownika (bez konieczności odczytywania kodu).

1. Zaloguj się do Azure Portal jako Administrator globalny, administrator uwierzytelniania uprzywilejowanego lub administrator uwierzytelniania. 
1. Kliknij przycisk **Azure Active Directory**, przejdź do opcji użytkownicy, wybierz użytkownika, na przykład *Krzysztof zielony*, a następnie wybierz **metody uwierzytelniania**.
1. W razie potrzeby wybierz opcję, aby **wypróbować nowe metody uwierzytelniania użytkowników**.
1. Wybierz opcję, aby **dodać metody uwierzytelniania**.
1. Poniżej **Wybierz metodę**, kliknij przycisk **dostęp tymczasowy (wersja zapoznawcza)**.
1. Zdefiniuj niestandardowy czas aktywacji lub czas trwania, a następnie kliknij przycisk **Dodaj**.

   ![Zrzut ekranu przedstawiający sposób tworzenia tymczasowego dostępu](./media/how-to-authentication-temporary-access-pass/create.png)

1. Po dodaniu zostaną wyświetlone szczegółowe informacje o przebiegu dostępu tymczasowego. Zanotuj rzeczywistą tymczasową wartość dostępną. Podajesz tę wartość użytkownikowi. Nie można wyświetlić tej wartości po kliknięciu przycisku **OK**.
   
   ![Zrzut ekranu przedstawiający informacje o tymczasowym przebiegu dostępu](./media/how-to-authentication-temporary-access-pass/details.png)

Następujące polecenia pokazują, jak utworzyć i uzyskać tymczasowy dostęp do przebiegu przy użyciu programu PowerShell:

```powershell
# Create a Temporary Access Pass for a user
$properties = @{}
$properties.isUsableOnce = $True
$properties.startDateTime = '2021-03-11 06:00:00'
$propertiesJSON = $properties | ConvertTo-Json

New-MgUserAuthenticationTemporaryAccessPassMethod -UserId user2@contoso.com -BodyParameter $propertiesJSON

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM TAPRocks!

# Get a user's Temporary Access Pass
Get-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM

```

## <a name="use-a-temporary-access-pass"></a>Korzystanie z tymczasowego dostępu

Najbardziej typowym zastosowaniem do tymczasowego dostępu jest nadanie użytkownikowi rejestrowania szczegółów uwierzytelniania podczas pierwszego logowania, bez konieczności wykonywania dodatkowych wskazówek dotyczących zabezpieczeń. Metody uwierzytelniania są rejestrowane pod adresem [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Użytkownicy mogą również w tym miejscu aktualizować istniejące metody uwierzytelniania.

1. Otwórz przeglądarkę internetową, aby [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Wprowadź nazwę UPN konta, dla którego został utworzony tymczasowy dostęp, na przykład *tapuser@contoso.com* .
1. Jeśli użytkownik zostanie uwzględniony w zasadach dostępu tymczasowego, zobaczy ekran, aby wejść do tymczasowego dostępu.
1. Wprowadź tymczasowy przebieg dostępu, który został wyświetlony w Azure Portal.

   ![Zrzut ekranu przedstawiający sposób wprowadzania tymczasowego dostępu](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>W przypadku domen federacyjnych preferowany jest tymczasowy przebieg dostępu w przypadku federacji. Użytkownik z zakończeniem dostępu tymczasowego ukończy uwierzytelnianie w usłudze Azure AD i nie zostanie przekierowany do federacyjnego dostawcy tożsamości (dostawcy tożsamości).

Użytkownik jest zalogowany i może aktualizować lub rejestrować metodę, taką jak FIDO2 Security Key. Użytkownicy, którzy zaktualizują swoje metody uwierzytelniania z powodu utraty swoich poświadczeń lub urządzenia, powinni upewnić się, że usuwają stare metody uwierzytelniania.

Użytkownicy mogą również używać swojego tymczasowego dostępu do rejestracji, aby nie logować się bezpośrednio z aplikacji uwierzytelniającej. Aby uzyskać więcej informacji, zobacz [Dodawanie konta służbowego do aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-add-work-school-account.md).

![Zrzut ekranu przedstawiający sposób wprowadzania tymczasowego dostępu przy użyciu konta służbowego](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-temporary-access-pass"></a>Usuwanie tymczasowego dostępu

Nie można użyć wygasłego tymczasowego dostępu. W obszarze **metody uwierzytelniania** dla użytkownika kolumna **szczegółów** jest wyświetlana po wygaśnięciu tymczasowego dostępu. Wygasłe tymczasowe dostęp tymczasowy można usunąć, wykonując następujące czynności:

1. W portalu usługi Azure AD przejdź do pozycji **Użytkownicy**, wybierz użytkownika, na przykład *naciśnij pozycję użytkownik*, a następnie wybierz pozycję **metody uwierzytelniania**.
1. Po prawej stronie metody uwierzytelniania **dostęp przejściowy (wersja zapoznawcza)** pokazanej na liście wybierz pozycję **Usuń**.

Możesz również użyć programu PowerShell:

```powershell
# Remove a user's Temporary Access Pass
Remove-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com -TemporaryAccessPassAuthenticationMethodId c5dbd20a-8b8f-4791-a23f-488fcbde3b38
```

## <a name="replace-a-temporary-access-pass"></a>Zastępowanie tymczasowego dostępu 

- Użytkownik może mieć tylko jeden dostęp tymczasowy. Kod dostępu może być używany podczas rozpoczęcia i czasu zakończenia tymczasowego dostępu.
- Jeśli użytkownik wymaga nowego tymczasowego przebiegu dostępu:
  - Jeśli istniejące tymczasowe przekazywanie dostępu jest prawidłowe, administrator musi usunąć istniejący tymczasowy przebieg dostępu i utworzyć nowe przekazanie dla użytkownika. Usunięcie prawidłowego tymczasowego przebiegu dostępu spowoduje odwołanie sesji użytkownika. 
  - Jeśli istniejące tymczasowe wygaśnięcie dostępu zakończy się niepowodzeniem, nowe tymczasowe przejście dostępu zostanie zastąpione istniejącym tymczasowym przebiegiem dostępu i nie spowoduje odwołania sesji użytkownika.

Aby uzyskać więcej informacji na temat standardów NIST na potrzeby dołączania i odzyskiwania, zobacz temat [specjalna publikacja NIST 63A](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Ograniczenia

Należy pamiętać o następujących ograniczeniach:

- W przypadku korzystania z jednorazowego tymczasowego dostępu do rejestracji metody bez hasła, takiej jak FIDO2 lub logowanie za pomocą telefonu, użytkownik musi wykonać rejestrację w ciągu 10 minut od momentu zalogowania się za pomocą jednorazowego tymczasowego dostępu. To ograniczenie nie ma zastosowania do tymczasowego przebiegu dostępu, którego można użyć więcej niż raz.
- Użytkownicy-Goście nie mogą zalogować się przy użyciu tymczasowego dostępu.
- Użytkownicy w zakresie dla zasad rejestracji samoobsługowego resetowania hasła (SSPR) *lub* [zasady rejestracji usługi uwierzytelniania wieloskładnikowego usługi Identity Protection](../identity-protection/howto-identity-protection-configure-mfa-policy.md) będą musieli rejestrować metody uwierzytelniania po zalogowaniu się przy użyciu tymczasowego dostępu. Użytkownicy w zakresie tych zasad zostaną przekierowani do [trybu przerwania połączonej rejestracji](concept-registration-mfa-sspr-combined.md#combined-registration-modes). To środowisko nie obsługuje obecnie rejestracji FIDO2 i logowania za pomocą telefonu. 
- Nie można używać tymczasowego przebiegu dostępu z rozszerzeniem serwera zasad sieciowych (NPS) i karty Active Directory Federation Services (AD FS) lub w trakcie Instalator systemu Windows/out-of-Box-Experience (OOBE) i autopilotażu. 
- Gdy w dzierżawie włączono bezproblemowe logowanie jednokrotne, użytkownicy są monitowani o wprowadzenie hasła. **Zamiast tego użyj linku dostępu do danych tymczasowych** dla użytkownika w celu zalogowania się za pomocą tymczasowego dostępu.

  ![Zrzut ekranu przedstawiający użycie tymczasowego dostępu](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów    

- Jeśli podczas logowania nie jest oferowany dostęp tymczasowy do użytkownika, należy sprawdzić następujące kwestie:
  - Użytkownik znajduje się w zakresie dla zasad metody uwierzytelniania w ramach tymczasowego dostępu.
  - Użytkownik ma prawidłowy dostęp tymczasowy, a jeśli jest jednorazowym użyciem, nie był jeszcze używany.
- W przypadku **zablokowania tymczasowego logowania dostęp z powodu wystąpienia zasad poświadczeń użytkownika** podczas logowania przy użyciu tymczasowego przebiegu dostępu sprawdź następujące kwestie:
  - Użytkownik ma dostęp tymczasowy przy użyciu wielu operacji, podczas gdy zasady metod uwierzytelniania wymagają jednorazowego tymczasowego dostępu.
  - Jednorazowy czas dostępu tymczasowego został już użyty.

## <a name="next-steps"></a>Następne kroki

- [Planowanie wdrożenia uwierzytelniania bezhaseł w Azure Active Directory](howto-authentication-passwordless-deployment.md)


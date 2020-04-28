---
title: Planowanie zasad dostępu warunkowego w Azure Active Directory | Microsoft Docs
description: W tym artykule dowiesz się, jak planować zasady dostępu warunkowego dla Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1c75d5022432a9a57b30aabec4dd2c4f76f2f29
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78671827"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Instrukcje: Planowanie wdrożenia dostępu warunkowego w Azure Active Directory

Planowanie wdrożenia dostępu warunkowego ma kluczowe znaczenie dla zapewnienia osiągnięcia wymaganej strategii dostępu dla aplikacji i zasobów w organizacji. Spędzaj najwięcej czasu w fazie planowania wdrożenia, aby zaprojektować różne zasady wymagane do udzielenia lub zablokowania dostępu do użytkowników w wybranych warunkach. W tym dokumencie opisano kroki, które należy wykonać, aby zaimplementować bezpieczne i efektywne zasady dostępu warunkowego. Przed rozpoczęciem upewnij się, że rozumiesz, jak działa [dostęp warunkowy](overview.md) , oraz kiedy należy go używać.

## <a name="what-you-should-know"></a>Co należy wiedzieć

Zastanów się nad dostępem warunkowym jako strukturą umożliwiającą kontrolowanie dostępu do aplikacji i zasobów organizacji, a nie funkcji autonomicznej. W związku z tym niektóre ustawienia dostępu warunkowego wymagają skonfigurowania dodatkowych funkcji. Można na przykład skonfigurować zasady reagujące na określony poziom ryzyka związanego z [logowaniem](../identity-protection/howto-identity-protection-configure-risk-policies.md). Jednak zasady oparte na poziomie ryzyka związanego z logowaniem wymagają włączenia [Azure Active Directory ochrony tożsamości](../identity-protection/overview-identity-protection.md) .

Jeśli wymagane są dodatkowe funkcje, może być również konieczne uzyskanie pokrewnych licencji. Na przykład, gdy dostęp warunkowy jest Azure AD — wersja Premium funkcji P1, Ochrona tożsamości wymaga licencji na Azure AD — wersja Premium P2.

Istnieją dwa typy zasad dostępu warunkowego: Baseline i Standard. [Zasady linii bazowej](baseline-protection.md) są wstępnie zdefiniowanymi zasadami dostępu warunkowego. Celem tych zasad jest upewnienie się, że masz co najmniej włączony poziom zabezpieczeń. Zasady linii bazowej. Zasady linii bazowej są dostępne we wszystkich wersjach usługi Azure AD i udostępniają tylko ograniczone opcje dostosowywania. Jeśli scenariusz wymaga większej elastyczności, wyłącz zasady linii bazowej i zaimplementuj wymagania w niestandardowych zasadach standardowych.

W przypadku standardowych zasad dostępu warunkowego można dostosować wszystkie ustawienia w celu dostosowania zasad do wymagań firmy. Zasady standardowe wymagają licencji na Azure AD — wersja Premium P1.

>[!NOTE]
> Zalecamy korzystanie z zasad dostępu warunkowego opartego na urządzeniach z usługą Azure AD w celu uzyskania najlepszego wymuszenia po początkowym uwierzytelnianiu urządzenia. Obejmuje to zamykanie sesji, jeśli urządzenie przestanie być zgodne i przepływ kodu urządzenia.

## <a name="draft-policies"></a>Zasady wersji roboczej

Azure Active Directory dostęp warunkowy umożliwia ochronę aplikacji w chmurze na nowym poziomie. Na tym nowym poziomie, w jaki sposób można uzyskać dostęp do aplikacji w chmurze, jest oparty na dynamicznej ocenie zasad, a nie w konfiguracji dostępu statycznego. Korzystając z zasad dostępu warunkowego, należy zdefiniować odpowiedź (**zrób to**) na warunek dostępu (**gdy taka sytuacja występuje**).

![Przyczyna i odpowiedź](./media/plan-conditional-access/10.png)

Zdefiniuj wszystkie zasady dostępu warunkowego, które chcesz zaimplementować przy użyciu tego modelu planowania. Ćwiczenie planowania:

- Ułatwia tworzenie konspektu odpowiedzi i warunków dla każdej zasady.
- Wyniki w postaci dobrze udokumentowanego wykazu zasad dostępu warunkowego dla Twojej organizacji. 

Możesz użyć wykazu, aby ocenić, czy implementacja zasad odzwierciedla wymagania biznesowe organizacji. 

Użyj następującego przykładowego szablonu, aby utworzyć zasady dostępu warunkowego dla swojej organizacji:

|W *takim* przypadku:|Następnie wykonaj *następujące*czynności:|
|-|-|
|Podjęto próbę dostępu:<br>— Do aplikacji*<br>w chmurze — według użytkowników i grup*<br>Użyciu<br>-Warunek 1 (na przykład poza siecią firmową)<br>-Warunek 2 (na przykład platformy urządzeń)|Blokuj dostęp do aplikacji|
|Podjęto próbę dostępu:<br>— Do aplikacji*<br>w chmurze — według użytkowników i grup*<br>Użyciu<br>-Warunek 1 (na przykład poza siecią firmową)<br>-Warunek 2 (na przykład platformy urządzeń)|Udziel dostępu za pomocą (i):<br>-Wymaganie 1 (na przykład MFA)<br>-Wymaganie 2 (na przykład zgodność urządzenia)|
|Podjęto próbę dostępu:<br>— Do aplikacji*<br>w chmurze — według użytkowników i grup*<br>Użyciu<br>-Warunek 1 (na przykład poza siecią firmową)<br>-Warunek 2 (na przykład platformy urządzeń)|Udziel dostępu za pomocą (lub):<br>-Wymaganie 1 (na przykład MFA)<br>-Wymaganie 2 (na przykład zgodność urządzenia)|

Co najmniej wtedy, **gdy ma to miejsce** , definiuje podmiot zabezpieczeń **, który**próbuje uzyskać dostęp do aplikacji w chmurze (**co**). W razie potrzeby można również dołączać, w **jaki sposób** jest podejmowana próba dostępu. W przypadku dostępu warunkowego elementy, które określają, kto, co i jak są znane jako warunki. Aby uzyskać więcej informacji, zobacz [co to są warunki w Azure Active Directory dostęp warunkowy?](concept-conditional-access-conditions.md) 

W **tym**celu należy zdefiniować odpowiedź zasad na warunek dostępu. W odpowiedzi można zablokować lub udzielić dostępu z dodatkowymi wymaganiami, na przykład uwierzytelnianie wieloskładnikowe (MFA). Aby zapoznać się z pełnym omówieniem, zobacz [co to są kontrole dostępu w Azure Active Directory dostęp warunkowy?](controls.md)  

Kombinacja warunków z kontrolkami dostępu reprezentuje zasady dostępu warunkowego.

![Przyczyna i odpowiedź](./media/plan-conditional-access/51.png)

Aby uzyskać więcej informacji, zobacz [co jest wymagane, aby utworzyć zasady](best-practices.md#whats-required-to-make-a-policy-work).

W tym momencie dobrym czasem jest podjęcie decyzji dotyczącej standardu nazewnictwa dla zasad. Standard nazewnictwa pomaga znaleźć zasady i zrozumieć ich przeznaczenie bez otwierania ich w portalu administracyjnym platformy Azure. Nazwij zasady, aby pokazać:

- Numer sekwencyjny
- Aplikacja w chmurze, której dotyczy
- Odpowiedź
- Kto ma zastosowanie do
- Gdy ma zastosowanie (jeśli dotyczy)
 
![Standard nazewnictwa](./media/plan-conditional-access/11.png)

Nazwa opisowa pomaga zachować przegląd implementacji dostępu warunkowego, ale numer sekwencyjny jest przydatny, jeśli trzeba odwołać się do zasad w konwersacji. Na przykład jeśli porozmawiasz z administratorem na telefonie, możesz Poproś o otwarcie zasad EM063, aby rozwiązać problem.

Na przykład następująca nazwa określa, że zasady wymagają uwierzytelniania wieloskładnikowego dla użytkowników działu marketingu w sieciach zewnętrznych przy użyciu aplikacji Dynamics CRP:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Oprócz aktywnych zasad zaleca się również wdrożenie wyłączonych zasad, które działają jako pomocnicze, [odporne na błędy kontroli dostępu w przypadku awarii/scenariuszy awaryjnych](../authentication/concept-resilient-controls.md). Standard nazewnictwa dla zasad awaryjnych powinien zawierać kilka więcej elementów: 

- `ENABLE IN EMERGENCY`na początku, aby nawiązać nazwę między innymi zasadami.
- Nazwa zakłócenia, do której należy zastosować.
- Numer sekwencyjny porządkowania, który pomaga administratorowi znać, w którym należy włączyć zasady zamówień. 

Na przykład następująca nazwa wskazuje, że te zasady są pierwszymi zasadami z czterech, które należy włączyć w przypadku wystąpienia przerwy w działaniu usługi MFA:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Zasady planu

Planując rozwiązanie zasad dostępu warunkowego, należy ocenić, czy należy utworzyć zasady, aby osiągnąć poniższe wyniki. 

### <a name="block-access"></a>Blokowanie dostępu

Opcja blokowania dostępu jest zaawansowana, ponieważ:

- Ma priorytet przed wszystkie inne przypisania dla użytkownika
- Możliwość zablokowania całej organizacji logowania się do dzierżawy
 
Jeśli chcesz zablokować dostęp dla wszystkich użytkowników, należy przynajmniej wykluczyć jednego użytkownika (zazwyczaj konta dostępu awaryjnego) z zasad. Aby uzyskać więcej informacji, zobacz [Wybieranie użytkowników i grup](block-legacy-authentication.md#select-users-and-cloud-apps).  

### <a name="require-mfa"></a>Wymaganie uwierzytelniania wieloskładnikowego

Aby uprościć środowisko logowania użytkowników, możesz chcieć zezwolić na logowanie się do aplikacji w chmurze przy użyciu nazwy użytkownika i hasła. Zwykle jednak istnieją co najmniej niektóre scenariusze, dla których zaleca się wymaganie, aby była wymagana silniejsza weryfikacja konta. Za pomocą zasad dostępu warunkowego można ograniczyć wymaganie uwierzytelniania wieloskładnikowego do określonych scenariuszy. 

Typowe przypadki użycia wymagane przez uwierzytelnianie wieloskładnikowe:

- [Według administratorów](howto-baseline-protect-administrators.md)
- [Do określonych aplikacji](app-based-mfa.md) 
- [Z lokalizacji sieciowych nie ufasz](untrusted-networks.md).

### <a name="respond-to-potentially-compromised-accounts"></a>Reagowanie na potencjalnie naruszone konta

Korzystając z zasad dostępu warunkowego, można zaimplementować automatyczne odpowiedzi na potrzeby logowania z potencjalnie złamanych tożsamości. Prawdopodobieństwo naruszenia zabezpieczeń konta jest wyrażone w formie poziomów ryzyka. Istnieją dwa poziomy ryzyka obliczone przez ochronę tożsamości: ryzyko związane z logowaniem i ryzykiem użytkownika. Aby zaimplementować odpowiedź na ryzyko związane z logowaniem, dostępne są dwie opcje:

- [Warunek dotyczący ryzyka związanego z logowaniem](concept-conditional-access-conditions.md#sign-in-risk) w zasadach dostępu warunkowego
- [Zasady dotyczące ryzyka związanego z logowaniem](../identity-protection/howto-sign-in-risk-policy.md) w usłudze Identity Protection 

Rozwiązywanie ryzyka związanego z logowaniem jako warunek jest preferowaną metodą, ponieważ zapewnia więcej opcji dostosowywania.

Poziom ryzyka użytkownika jest dostępny tylko jako [zasady ryzyka dla użytkowników](../identity-protection/howto-user-risk-policy.md) w usłudze Identity Protection. 

Aby uzyskać więcej informacji, zobacz [co to jest Azure Active Directory Identity Protection?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Wymaganie urządzeń zarządzanych

Rozprzestrzenianie się obsługiwanych urządzeń w celu uzyskania dostępu do zasobów w chmurze ułatwia zwiększenie produktywności użytkowników. Na stronie odwracania prawdopodobnie nie chcesz, aby niektóre zasoby w danym środowisku były dostępne na urządzeniach z nieznanym poziomem ochrony. W przypadku zasobów, których to dotyczy, należy wymagać, aby użytkownicy mieli do nich dostęp tylko przy użyciu urządzenia zarządzanego. Aby uzyskać więcej informacji, zobacz [temat jak wymagać zarządzanych urządzeń do uzyskiwania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Wymaganie zatwierdzonych aplikacji klienckich

Jedną z pierwszych decyzji, które należy podjąć w celu przeprowadzenia swoich własnych urządzeń (BYOD), jest to, czy należy zarządzać całym urządzeniem, czy tylko danymi na nim. Pracownicy używają urządzeń przenośnych zarówno do celów służbowych, jak i prywatnych. Zapewniając, że pracownicy mogą pracować wydajnie, możesz również zapobiec utracie danych. Za pomocą dostępu warunkowego usługi Azure Active Directory (Azure AD) można ograniczyć dostęp do aplikacji w chmurze do zatwierdzonych aplikacji klienckich, które mogą chronić dane firmowe. Aby uzyskać więcej informacji, zobacz artykuł [jak wymagać zatwierdzonych aplikacji klienckich do uzyskiwania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](app-based-conditional-access.md).

### <a name="block-legacy-authentication"></a>Blokowanie starszego uwierzytelniania

Usługa Azure AD obsługuje kilka powszechnie używanych protokołów uwierzytelniania i autoryzacji, w tym starsze uwierzytelnianie. Jak można uniemożliwić aplikacjom korzystającym ze starszego uwierzytelniania dostęp do zasobów dzierżawy? Zalecenie polega na prostu zablokować je za pomocą zasad dostępu warunkowego. W razie potrzeby zezwalasz tylko określonym użytkownikom i określonym lokalizacjom sieciowym na używanie aplikacji opartych na starszej wersji uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Jak zablokować starsze uwierzytelnianie w usłudze Azure AD przy użyciu dostępu warunkowego](block-legacy-authentication.md).

## <a name="test-your-policy"></a>Testowanie zasad

Przed wdrożeniem zasad w środowisku produkcyjnym należy sprawdzić, czy działa ono zgodnie z oczekiwaniami.

1. Tworzenie użytkowników testowych
1. Utwórz plan testu
1. Konfigurowanie zasad
1. Oceń symulowane logowanie
1. Testowanie zasad
1. Czyszczenie

### <a name="create-test-users"></a>Tworzenie użytkowników testowych

Aby przetestować zasady, należy utworzyć zestaw użytkowników podobny do użytkowników w danym środowisku. Tworzenie użytkowników testowych pozwala sprawdzić, czy zasady działają zgodnie z oczekiwaniami, zanim wpłyną na rzeczywistych użytkowników i potencjalnie zakłócają dostęp do aplikacji i zasobów. 

W tym celu niektóre organizacje mają do tego celu test dzierżawców. Może jednak być trudno odtworzyć wszystkie warunki i aplikacje w dzierżawie testowej, aby w pełni przetestować wynik zasad. 

### <a name="create-a-test-plan"></a>Utwórz plan testu

Plan testu jest istotny do porównania między oczekiwanymi wynikami i rzeczywistymi wynikami. Przed przeprowadzeniem testowania należy zawsze oczekiwać. W poniższej tabeli przedstawiono przykładowe przypadki testowe. Dostosuj scenariusze i oczekiwane wyniki w zależności od sposobu skonfigurowania zasad urzędu certyfikacji.

|Zasady |Scenariusz |Oczekiwany wynik | Wynik |
|---|---|---|---|
|[Wymagaj uwierzytelniania wieloskładnikowego, gdy nie jest w pracy](/azure/active-directory/conditional-access/untrusted-networks)|Autoryzowany użytkownik loguje się do *aplikacji* w ramach zaufanej lokalizacji/pracy|Użytkownik nie jest monitowany o uwierzytelnianie wieloskładnikowe| |
|[Wymagaj uwierzytelniania wieloskładnikowego, gdy nie jest w pracy](/azure/active-directory/conditional-access/untrusted-networks)|Autoryzowany użytkownik loguje się do *aplikacji* , gdy nie znajduje się w zaufanej lokalizacji/pracy|Użytkownik jest monitowany o uwierzytelnianie wieloskładnikowe i może zalogować się pomyślnie| |
|[Wymagaj uwierzytelniania wieloskładnikowego (dla administratora)](/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|Administrator globalny loguje się do *aplikacji*|Administrator jest monitowany o uwierzytelnianie wieloskładnikowe| |
|[Ryzykowne logowania](/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Użytkownik loguje się do *aplikacji* przy użyciu [przeglądarki sieci Tor](/azure/active-directory/active-directory-identityprotection-playbook)|Administrator jest monitowany o uwierzytelnianie wieloskładnikowe| |
|[Zarządzanie urządzeniami](/azure/active-directory/conditional-access/require-managed-devices)|Autoryzowany użytkownik próbuje zalogować się z autoryzowanego urządzenia|Udzielony dostęp| |
|[Zarządzanie urządzeniami](/azure/active-directory/conditional-access/require-managed-devices)|Autoryzowany użytkownik próbuje zalogować się z nieautoryzowanego urządzenia|Dostęp zablokowany| |
|[Zmiana hasła dla ryzykownych użytkowników](/azure/active-directory/identity-protection/howto-user-risk-policy)|Autoryzowany użytkownik próbuje zalogować się przy użyciu złamanych poświadczeń (logowanie wysokiego ryzyka)|Użytkownik jest monitowany o zmianę hasła lub dostęp jest zablokowany na podstawie zasad| |

### <a name="configure-the-policy"></a>Konfigurowanie zasad

Zarządzanie zasadami dostępu warunkowego jest zadaniem ręcznym. W Azure Portal można zarządzać zasadami dostępu warunkowego w jednej centralnej lokalizacji — stronie dostępu warunkowego. Jednym punktem wejścia na stronę dostępu warunkowego jest sekcja **zabezpieczenia** w okienku nawigacji **Active Directory** . 

![Dostęp warunkowy](media/plan-conditional-access/03.png)

Jeśli chcesz dowiedzieć się więcej na temat tworzenia zasad dostępu warunkowego, zobacz [Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji z Azure Active Directory dostęp warunkowy](app-based-mfa.md). Ten przewodnik Szybki Start pomaga:

- Zapoznaj się z interfejsem użytkownika.
- Zapoznaj się z pierwszym wrażeniem, jak działa dostęp warunkowy. 

### <a name="evaluate-a-simulated-sign-in"></a>Oceń symulowane logowanie

Teraz, po skonfigurowaniu zasad dostępu warunkowego, prawdopodobnie chcesz wiedzieć, czy działa zgodnie z oczekiwaniami. Pierwszym krokiem jest użycie [Narzędzia do działania](what-if-tool.md) warunkowego, co pozwala na symulowanie logowania użytkownika testowego. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji.

>[!NOTE]
> Chociaż symulowane uruchomienie daje wrażenie wpływu na zasady dostępu warunkowego, nie zastępuje rzeczywistego przebiegu testu.

### <a name="test-your-policy"></a>Testowanie zasad

Uruchom przypadki testowe zgodnie z planem testu. W tym kroku przeprowadzisz kompleksowy test poszczególnych zasad dla użytkowników testowych, aby upewnić się, że wszystkie zasady działają prawidłowo. Użyj scenariuszy utworzonych powyżej, aby wykonać każdy test.

Należy upewnić się, że testujesz kryteria wykluczenia zasad. Na przykład możesz wykluczyć użytkownika lub grupę z zasad, które wymagają uwierzytelniania wieloskładnikowego. Przetestuj, czy wykluczeni użytkownicy są monitowani o uwierzytelnianie wieloskładnikowe, ponieważ połączenie innych zasad może wymagać uwierzytelniania MFA dla tych użytkowników.

### <a name="cleanup"></a>Czyszczenie

Procedura oczyszczania obejmuje następujące kroki:

1. Wyłącz zasady.
1. Usuń przypisanych użytkowników i grupy.
1. Usuń użytkowników testowych.  

## <a name="move-to-production"></a>Przenoszenie do środowiska produkcyjnego

Jeśli nowe zasady są gotowe dla danego środowiska, wdróż je w fazach:

- Zapewnianie wewnętrznej komunikacji między użytkownikami końcowymi.
- Zacznij od małego zestawu użytkowników i sprawdź, czy zasady działają zgodnie z oczekiwaniami.
- Po rozszerzeniu zasad w celu uwzględnienia większej liczby użytkowników należy dalej wykluczać wszystkich administratorów. Wyłączenie administratorów gwarantuje, że ktoś nadal ma dostęp do zasad, jeśli wymagana jest zmiana.
- Zastosuj zasady dla wszystkich użytkowników tylko wtedy, gdy jest to wymagane.

Najlepszym rozwiązaniem jest utworzenie co najmniej jednego konta użytkownika:

- Przeznaczone do administrowania zasadami
- Wykluczone ze wszystkich zasad

## <a name="rollback-steps"></a>Etapy wycofywania

W przypadku konieczności wycofania nowo wdrożonych zasad należy użyć co najmniej jednej z następujących opcji, aby przywrócić:

1. **Wyłączenie zasad** — wyłączenie zasad gwarantuje, że nie ma zastosowania, gdy użytkownik próbuje się zalogować. Zawsze możesz wrócić i włączyć zasady, jeśli chcesz korzystać z niej.

   ![Wyłączanie zasad](media/plan-conditional-access/07.png)

1. **Wykluczanie użytkownika/grupy z zasad** — Jeśli użytkownik nie ma dostępu do aplikacji, możesz wybrać opcję wykluczenia użytkownika z zasad

   ![Exluce użytkownicy](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Ta opcja powinna być używana oszczędnie, tylko w sytuacjach, gdy użytkownik jest zaufany. Użytkownik powinien zostać dodany z powrotem do zasad lub grupy tak szybko, jak to możliwe.

1. **Usuń zasady** — Jeśli zasady nie są już wymagane, usuń je.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [dokumentacją dostępu warunkowego usługi Azure AD](index.yml) , aby zapoznać się z dostępnymi informacjami.

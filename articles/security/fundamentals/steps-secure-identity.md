---
title: Zabezpieczanie infrastruktury tożsamości usługi Azure AD
titleSuffix: Azure Active Directory
description: Ten dokument przedstawia listę ważnych działań, które Administratorzy powinni wdrożyć, aby ułatwić im ochronę organizacji przy użyciu funkcji usługi Azure AD
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: ffc5bafca305086b0c524c76eb91b8aec2e2655d
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602450"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Pięć kroków związanych z zabezpieczaniem infrastruktury tożsamości

W przypadku odczytywania tego dokumentu masz świadomość istotności zabezpieczeń. Użytkownik jest już odpowiedzialny za zabezpieczenie organizacji. Jeśli chcesz przekonać inne o znaczeniu zabezpieczeń, wyślij je, aby przeczytać najnowszy [Raport analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/security/business/security-intelligence-report).

Ten dokument pomoże Ci lepiej zabezpieczyć stan przy użyciu możliwości Azure Active Directory przy użyciu listy kontrolnej z pięcioma krokami, aby zaszczepować organizację przed atakami cybernetycznymi.

Ta lista kontrolna ułatwi szybkie wdrożenie krytycznych zalecanych akcji w celu natychmiastowego zapewnienia ochrony organizacji przez wyjaśnienie, jak:

* Wzmocnij swoje poświadczenia.
* Zmniejsz obszar narażony na ataki.
* Automatyzowanie odpowiedzi na zagrożenia.
* Korzystaj z analizy chmury.
* Włącz samoobsługowe korzystanie z użytkowników końcowych.

Upewnij się, że śledzisz, które funkcje i kroki są kompletne podczas odczytywania tej listy kontrolnej.

> [!NOTE]
> Wiele zaleceń zawartych w tym dokumencie dotyczy tylko aplikacji skonfigurowanych do korzystania z Azure Active Directory jako ich dostawcy tożsamości. Skonfigurowanie aplikacji dla jednego Sign-On zapewnia korzyści wynikające z zasad poświadczeń, wykrywania zagrożeń, inspekcji, rejestrowania i innych funkcji Dodaj do tych aplikacji. [Zarządzanie aplikacjami w usłudze Azure AD](../../active-directory/manage-apps/what-is-application-management.md) to podstawa, na której bazują wszystkie te zalecenia.

Zalecenia zawarte w tym dokumencie są wyrównane z [wynikiem bezpiecznego tożsamości](../../active-directory/fundamentals/identity-secure-score.md), zautomatyzowanej oceny konfiguracji zabezpieczeń tożsamości dzierżawy usługi Azure AD. Organizacje mogą używać strony oceny zabezpieczeń tożsamości w portalu usługi Azure AD, aby znaleźć luki w ich bieżącej konfiguracji, aby upewnić się, że są one zgodne z bieżącymi [najlepszymi rozwiązaniami](identity-management-best-practices.md) firmy Microsoft dotyczącymi zabezpieczeń. Zaimplementowanie każdego zalecenia na stronie ze wskaźnikiem zabezpieczeń spowoduje zwiększenie oceny i umożliwi śledzenie postępu, a także ułatwia porównanie implementacji z innymi podobnymi organizacjami wielkości lub branżą.

![Ocena bezpiecznego tożsamości](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Wiele funkcji opisanych w tym miejscu wymaga subskrypcji Azure AD — wersja Premium, a niektóre z nich są bezpłatne. Aby uzyskać więcej informacji, zapoznaj się z naszymi [Azure Active Directory cennikami](https://azure.microsoft.com/pricing/details/active-directory/) i [listą kontrolną wdrożenia usługi Azure AD](../../active-directory/fundamentals/active-directory-deployment-checklist-p2.md) .

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Przed rozpoczęciem: Ochrona kont uprzywilejowanych za pomocą usługi MFA

Przed rozpoczęciem korzystania z tej listy kontrolnej upewnij się, że nie nastąpiło naruszenie zabezpieczeń podczas odczytywania tej listy kontrolnej. Najpierw musisz chronić Twoje konta uprzywilejowane.

Osoby atakujące, które uzyskują kontrolę nad kontami uprzywilejowanymi, mogą mieć ogromne szkody, dlatego należy zapewnić ochronę tych kont jako pierwsze. Włącz i wymagaj [usługi Azure ad Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (MFA) dla wszystkich administratorów w organizacji przy użyciu [domyślnych ustawień zabezpieczeń usługi Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) lub [dostępu warunkowego](../../active-directory/conditional-access/plan-conditional-access.md). Jeśli nie zaimplementowano usługi MFA, zrób to teraz! Jest to ważne.

Czy wszystko jest ustawione? Zacznijmy od listy kontrolnej.

## <a name="step-1---strengthen-your-credentials"></a>Krok 1. wzmocnienie poświadczeń

Większość naruszeń zabezpieczeń przedsiębiorstwa pochodzi z konta złamanego z jedną z kilku metod, takich jak rozpylanie hasła, odtworzenie naruszenia lub wyłudzanie informacji. Dowiedz się więcej na temat tych ataków w tym filmie wideo (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Upewnij się, że Twoja organizacja używa silnego uwierzytelniania

Uwzględniając częstotliwość, w której hasła są zgadywanie, phishing, skradziony lub ponownie używany, ma krytyczne znaczenie dla hasła przy użyciu jakiejś postaci silnego poświadczenia — Dowiedz się więcej o [usłudze Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

Aby w prosty sposób włączyć podstawowy poziom zabezpieczeń tożsamości, można użyć opcji włączenia jednego kliknięcia z [wartościami domyślnymi zabezpieczeń usługi Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md). Domyślne ustawienia zabezpieczeń wymuszają usługę Azure AD MFA dla wszystkich użytkowników w dzierżawie i blokują logowania ze starszych protokołów w całej dzierżawie.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Zacznij zakazywanie często zaatakowane hasła i Wyłącz tradycyjną złożoność oraz reguły wygasania.

W wielu organizacjach jest używana tradycyjna złożoność (wymagające znaków specjalnych, cyfr, wielkich i małych liter) oraz reguł wygasania haseł. [Badania firmy Microsoft](https://aka.ms/passwordguidance) pokazują, że te zasady powodują, że użytkownicy wybierają hasła, które są łatwiejsze do odgadnięcia.

Funkcja [dynamicznego zabronionego hasła](../../active-directory/authentication/concept-password-ban-bad.md) usługi Azure AD używa bieżącego zachowania osoby atakującej, aby uniemożliwić użytkownikom ustawianie haseł, które można łatwo złamać. Ta funkcja jest zawsze włączona w przypadku tworzenia użytkowników w chmurze, ale jest teraz również dostępna dla organizacji hybrydowych podczas wdrażania [ochrony hasłem usługi Azure AD dla systemu Windows Server Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md). Ochrona hasłem usługi Azure AD blokuje użytkownikom możliwość wybierania tych typowych haseł i można ją rozszerzyć w celu zablokowania hasła zawierającego niestandardowe słowa kluczowe. Można na przykład uniemożliwić użytkownikom wybieranie haseł zawierających nazwy produktów firmy lub lokalnego zespołu sportowego.

Firma Microsoft zaleca stosowanie następujących nowoczesnych zasad haseł opartych na [wytycznych NIST](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Wymagaj hasła, które mają co najmniej 8 znaków. Nie jest to jeszcze lepsze, ponieważ powodują, że użytkownicy wybierają przewidywalne hasła, zapisują hasła w plikach lub zapisują je w dół.
2. Wyłącz reguły wygasania, które umożliwiają użytkownikom łatwe odgadnięcie haseł, takich jak **Spring2019!**
3. Wyłącz wymagania dotyczące kompozycji znaków i uniemożliwiaj użytkownikom wybranie często zaatakowanych haseł, ponieważ powodują one, że użytkownicy wybierają przewidywalną podstawienia znaków w hasłach.

Możesz użyć [programu PowerShell, aby zapobiec wygaśnięciu haseł](../../active-directory/authentication/concept-sspr-policy.md) dla użytkowników w przypadku bezpośredniego tworzenia tożsamości w usłudze Azure AD. Organizacje hybrydowe powinny zaimplementować te zasady przy użyciu [ustawień zasad grupy domeny](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) lub [programu Windows PowerShell](/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Ochrona przed nieujawnionymi poświadczeniami i zwiększanie odporności przed awarią

Jeśli Twoja organizacja korzysta z rozwiązania do obsługi tożsamości hybrydowej z uwierzytelnianiem przekazującym lub federacyjnym, należy włączyć synchronizację skrótów haseł z następujących dwóch przyczyn:

* Raport [Użytkownicy z ujawnionymi poświadczeniami](../../active-directory/identity-protection/overview-identity-protection.md) w usłudze Azure AD Management ostrzega użytkownika o parach nazw i hasła, które zostały uwidocznione w "ciemnej sieci Web". Niezwykła ilość haseł jest wycieka za pośrednictwem wyłudzania informacji, złośliwego oprogramowania i ponownego użycia haseł w witrynach innych firm, które zostały później naruszone. Firma Microsoft odszuka wiele z tych nieujawnionych poświadczeń i poinformuje użytkownika w tym raporcie, jeśli są one zgodne z poświadczeniami w organizacji, ale tylko w przypadku [włączenia synchronizacji skrótów haseł](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) lub posiadania tożsamości tylko w chmurze.
* W przypadku awarii lokalnej (na przykład w przypadku ataku z wykorzystaniem oprogramowania wymuszającego okup) można przełączyć się na używanie [uwierzytelniania w chmurze przy użyciu funkcji synchronizacji skrótów haseł](../../active-directory/hybrid/choose-ad-authn.md). Ta metoda uwierzytelniania kopii zapasowej umożliwi kontynuowanie dostępu do aplikacji skonfigurowanych do uwierzytelniania za pomocą Azure Active Directory, w tym Microsoft 365. W takim przypadku pracownicy działu IT nie muszą korzystać z osobistych kont e-mail w celu udostępniania danych, dopóki nie zostanie rozwiązane lokalne przestoje.

Dowiedz się więcej o tym, jak działa [Synchronizacja skrótów haseł](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) .

> [!NOTE]
> W przypadku włączenia synchronizacji skrótów haseł i korzystania z usług Azure AD Domain Services skróty protokołu Kerberos (AES 256) i opcjonalnie NTLM (RC4, bez soli) również będą szyfrowane i synchronizowane z usługą Azure AD.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementowanie blokady inteligentnej AD FS ekstranetu

Organizacje, które konfigurują aplikacje do uwierzytelniania bezpośrednio w usłudze Azure AD z poziomu [inteligentnego blokowania usługi Azure AD](../../active-directory/authentication/concept-sspr-howitworks.md). Jeśli używasz AD FS w systemie Windows Server 2012R2, zaimplementuj AD FS [Ochrona blokowania ekstranetu](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Jeśli używasz AD FS w systemie Windows Server 2016, zaimplementuj [inteligentne blokady ekstranetu](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). AD FS inteligentnego blokowania ekstranetu chroni przed atakami polegającymi na rozdziałach, które są ukierunkowane AD FS, uniemożliwiając zablokowanie użytkowników w Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Korzystaj z zalet bezpiecznego, łatwiejszego w użyciu poświadczeń

Za pomocą funkcji [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)można zastąpić hasła silnym uwierzytelnianiem dwuskładnikowym na komputerach i urządzeniach przenośnych. To uwierzytelnianie obejmuje nowy typ poświadczeń użytkownika, które są bezpiecznie powiązane z urządzeniem i używa biometrycznych lub PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Krok 2. zredukowanie obszaru ataków

Z uwagi na niebezpieczeństwo hasła, zminimalizowanie obszaru ataków w organizacji ma krytyczne znaczenie. Eliminowanie używania starszych, mniej bezpiecznych protokołów, ograniczanie punktów wejścia dostępu i wykonywanie bardziej znaczącej kontroli nad dostępem administracyjnym do zasobów może pomóc w zmniejszeniu obszaru podatności na ataki.

### <a name="block-legacy-authentication"></a>Blokowanie starszego uwierzytelniania

Aplikacje korzystające z własnych starszych metod do uwierzytelniania w usłudze Azure AD i uzyskiwania dostępu do danych firmowych stanowią inne ryzyko dla organizacji. Przykładami aplikacji korzystających ze starszego uwierzytelniania są klienci POP3, IMAP4 lub SMTP. Starsze aplikacje uwierzytelniania uwierzytelniają się w imieniu użytkownika i uniemożliwiają usłudze Azure AD wykonywanie zaawansowanych ocen zabezpieczeń. Alternatywna, nowoczesne uwierzytelnianie zmniejsza zagrożenie bezpieczeństwa, ponieważ obsługuje uwierzytelnianie wieloskładnikowe i dostęp warunkowy. Zalecamy następujące trzy akcje:

1. Blokuj [starsze uwierzytelnianie, jeśli używasz AD FS](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Skonfiguruj [SharePoint Online i usługi Exchange Online do korzystania z nowoczesnego uwierzytelniania](../../active-directory/conditional-access/block-legacy-authentication.md).
3. Jeśli masz Azure AD — wersja Premium, użyj zasad dostępu warunkowego, aby [zablokować starsze uwierzytelnianie](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md), w przeciwnym razie użyj [domyślnych ustawień zabezpieczeń usługi Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="block-invalid-authentication-entry-points"></a>Blokuj nieprawidłowe punkty wejścia uwierzytelniania

Korzystając z zakładanego naruszenia warunków, należy zmniejszyć wpływ naruszenia poświadczeń użytkownika. Dla każdej aplikacji w danym środowisku należy wziąć pod uwagę prawidłowe przypadki użycia: które grupy, które sieci, urządzenia i inne elementy są autoryzowane, Zablokuj resztę. Za pomocą [dostępu warunkowego usługi Azure AD](../../active-directory/conditional-access/overview.md)można kontrolować sposób, w jaki autoryzowani użytkownicy uzyskują dostęp do swoich aplikacji i zasobów na podstawie określonych warunków.

### <a name="restrict-user-consent-operations"></a>Ogranicz operacje wyrażania zgody użytkownika

Ważne jest, aby zrozumieć różne środowiska, w których można [wyrazić zgodę na usługę Azure AD](../../active-directory/develop/application-consent-experience.md), [typy uprawnień i zgody](../../active-directory/develop/v2-permissions-and-consent.md)oraz ich wpływ na stan zabezpieczeń organizacji. Domyślnie wszyscy użytkownicy w usłudze Azure AD mogą przyznawać aplikacje korzystające z platformy tożsamości firmy Microsoft w celu uzyskania dostępu do danych organizacji. Zezwalanie użytkownikom na zgodę samodzielnie pozwala użytkownikom na łatwe uzyskiwanie przydatnych aplikacji, które integrują się z Microsoft 365, platformą Azure i innymi usługami, ale może to stanowić ryzyko, jeśli nie są one używane i monitorowane uważnie.

Firma Microsoft zaleca ograniczenie zgody użytkownika na zmniejszenie obszaru powierzchni i uniknięcie tego ryzyka. Możesz również użyć [zasad zgody aplikacji (wersja zapoznawcza)](../../active-directory/manage-apps/configure-user-consent.md) , aby ograniczyć zgodę użytkowników końcowych tylko na zweryfikowane wydawcy i tylko dla wybranych uprawnień. Jeśli zgoda użytkownika końcowego jest ograniczona, poprzednie dotacje zgody nadal będą honorowane, ale wszystkie przyszłe operacje wyrażania zgody muszą zostać wykonane przez administratora. W przypadku ograniczonych przypadków zgoda administratora może zażądać przez użytkowników za pomocą [przepływu pracy zintegrowanego żądania zgody administratora](../../active-directory/manage-apps/configure-admin-consent-workflow.md) lub przez własne procesy obsługi. Przed ograniczeniem zgody użytkownika końcowego należy skorzystać z naszych [zaleceń](../../active-directory/manage-apps/manage-consent-requests.md) w celu zaplanowania tej zmiany w organizacji. W przypadku aplikacji, dla których chcesz zezwolić wszystkim użytkownikom na dostęp, rozważ [udzielenie zgody w imieniu wszystkich użytkowników](../../active-directory/develop/v2-admin-consent.md), aby upewnić się, że użytkownicy, którzy nie zostali jeszcze zadani indywidualnie, będą mogli uzyskać dostęp do aplikacji. Jeśli nie chcesz, aby te aplikacje były dostępne dla wszystkich użytkowników we wszystkich scenariuszach, użyj [przypisywania aplikacji](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) i dostępu warunkowego, aby ograniczyć dostęp użytkowników do [określonych aplikacji](../../active-directory/conditional-access/concept-conditional-access-cloud-apps.md).

Upewnij się, że użytkownicy mogą zażądać zatwierdzenia przez administratora dla nowych aplikacji, aby zmniejszyć liczbę użytkowników, zminimalizować ilość pomocy technicznej i uniemożliwić użytkownikom rejestrowanie się w aplikacjach przy użyciu poświadczeń spoza usługi Azure AD. Po uregulowaniu operacji wyrażania zgody Administratorzy powinni regularnie przeprowadzać inspekcję aplikacji i uprawnień.


### <a name="implement-azure-ad-privileged-identity-management"></a>Implementowanie Azure AD Privileged Identity Management

Innym wpływem "założono naruszenie" jest konieczność zminimalizowania prawdopodobieństwa, że naruszone konto może działać z rolą uprzywilejowaną.

[Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) ułatwia zminimalizowanie uprawnień konta, pomagając:

* Zidentyfikuj użytkowników przypisanych do ról administracyjnych i zarządzaj nimi.
* Informacje o nieużywanych lub nadmiernych rolach uprawnień należy usunąć.
* Ustanów reguły, aby upewnić się, że uprzywilejowane role są chronione przez uwierzytelnianie wieloskładnikowe.
* Ustanów reguły, aby upewnić się, że uprzywilejowane role są przyznawane tylko wystarczająco długo, aby można było wykonać zadanie uprzywilejowane.

Włącz usługę Azure AD PIM, a następnie Wyświetl użytkowników, którym przypisano role administracyjne, i Usuń niepotrzebne konta z tych ról. W przypadku pozostałych uprzywilejowanych użytkowników Przenieś je na stałe, aby kwalifikować się do nich. Na koniec Ustanów odpowiednie zasady, aby upewnić się, że muszą oni uzyskać dostęp do ról uprzywilejowanych, dzięki czemu mogą oni bezpiecznie wykonać te czynności z wymaganą kontrolą zmian.

W ramach wdrażania procesu uprzywilejowanego konta postępuj zgodnie z najlepszymi rozwiązaniami, aby [utworzyć co najmniej dwa konta awaryjne](../../active-directory/roles/security-planning.md) , aby upewnić się, że nadal masz dostęp do usługi Azure AD, jeśli zablokujesz swoją działalność.

## <a name="step-3---automate-threat-response"></a>Krok 3 — Automatyzowanie odpowiedzi na zagrożenia

Azure Active Directory ma wiele funkcji, które automatycznie przechwytuje ataki, aby usunąć opóźnienie między wykrywaniem a odpowiedzią. Obniżenie kosztów i ryzyka pozwala zmniejszyć czas, w którym przestępcy są używane do osadzania się w środowisku. Poniżej przedstawiono konkretne czynności, które można wykonać.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementowanie zasad zabezpieczeń dotyczących ryzyka użytkownika przy użyciu Azure AD Identity Protection

Ryzyko związane z użytkownikiem wskazuje, że prawdopodobieństwo naruszenia tożsamości użytkownika zostało naruszone i jest obliczane na podstawie [wykrycia ryzyka użytkownika](../../active-directory/identity-protection/overview-identity-protection.md) , które są skojarzone z tożsamością użytkownika. Zasady ryzyka dla użytkowników to zasady dostępu warunkowego, które szacują poziom ryzyka dla określonego użytkownika lub grupy. W oparciu o niski, średni i wysoki poziom ryzyka zasady można skonfigurować w taki sposób, aby blokować dostęp lub wymagały bezpiecznej zmiany hasła przy użyciu usługi uwierzytelniania wieloskładnikowego. Zalecenie firmy Microsoft to wymaganie bezpiecznej zmiany hasła dla użytkowników na wysokim ryzyku.

![Zrzut ekranu przedstawia użytkowników oflagowanych w celu ryzyka z wybranym użytkownikiem.](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementowanie zasad dotyczących ryzyka związanego z logowaniem przy użyciu Azure AD Identity Protection

Ryzyko związane z logowaniem to prawdopodobieństwo, że ktoś inny niż właściciel konta podejmuje próbę zalogowania się przy użyciu tożsamości. [Zasady dotyczące ryzyka związanego z logowaniem](../../active-directory/identity-protection/overview-identity-protection.md) to zasady dostępu warunkowego, które szacują poziom ryzyka dla określonego użytkownika lub grupy. W oparciu o poziom ryzyka (wysoki/średni/niski) zasady można skonfigurować w taki sposób, aby blokować dostęp lub wymuszać uwierzytelnianie wieloskładnikowe. Upewnij się, że wymuszsz uwierzytelnianie wieloskładnikowe na średnim lub wyższym ryzyku.

![Logowanie się z anonimowych adresów IP](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Krok 4. Korzystanie z usługi Cloud Intelligence

Inspekcja i rejestrowanie zdarzeń związanych z zabezpieczeniami oraz powiązanych alertów są istotnymi składnikami strategii skutecznej ochrony. Dzienniki zabezpieczeń i raporty zapewniają elektroniczny rejestr podejrzanych działań i pomagają wykryć wzorce, które mogą wskazywać na próbę lub pomyślne przechodzenie do sieci, a także ataki wewnętrzne. Inspekcji można używać do monitorowania aktywności użytkowników, dokumentowania zgodności z przepisami, analizy śledczej i innych. Alerty udostępniają powiadomienia o zdarzeniach zabezpieczeń.

### <a name="monitor-azure-ad"></a>Monitorowanie usługi Azure AD

Usługi Microsoft Azure i funkcje udostępniają konfigurowalne opcje inspekcji zabezpieczeń i rejestrowania, które ułatwiają identyfikację luk w zabezpieczeniach i mechanizmach i rozwiązywanie tych luk w celu uniknięcia naruszeń. [W portalu Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)można korzystać z funkcji [rejestrowania i inspekcji na platformie Azure](log-audit.md) oraz raportów dotyczących aktywności inspekcji.

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitoruj Azure AD Connect Health w środowiskach hybrydowych

[AD FS monitorowania z Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) zapewnia lepszy wgląd w potencjalne problemy i widoczność ataków na infrastrukturę AD FS. Azure AD Connect Health dostarcza alerty z szczegółowymi informacjami, krokami rozwiązania i łączami do powiązanej dokumentacji; Analiza użycia dla kilku metryk związanych z ruchem związanym z uwierzytelnianiem; Monitorowanie wydajności i raporty.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorowanie zdarzeń Azure AD Identity Protection

[Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) to narzędzie do powiadamiania, monitorowania i raportowania, których można użyć do wykrywania potencjalnych luk w zabezpieczeniach, które mają wpływ na tożsamości w organizacji. Wykrywa ona wykrywanie ryzyka, takie jak nieujawnione poświadczenia, niemożliwa podróż i logowania z zainfekowanych urządzeń, anonimowe adresy IP, adresy IP skojarzone z podejrzanymi działaniami oraz nieznane lokalizacje. Włącz alerty powiadomień, aby otrzymywać wiadomości e-mail użytkowników z ryzykiem i/lub tygodniową pocztą e-mail.

Azure AD Identity Protection oferuje dwa ważne raporty, które należy monitorować codziennie:
1. Ryzykowne raporty logowania to działania związane z logowaniem użytkownika, które należy zbadać, ale uprawniony właściciel może nie wykonać logowania.
2. Ryzykowne raporty użytkowników to konta użytkowników, które mogły zostać naruszone, takie jak wykryto wycieki poświadczenia lub użytkownik zalogowany z różnych lokalizacji powodujących niepowodzenie podróży.

![Zrzut ekranu przedstawia okienko Ochrona tożsamości Azure A D z użytkownikami i ich poziomami ryzyka.](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Inspekcja aplikacji i przyzwoleń do nich

Użytkownicy mogą dochodzić do nawigowania do witryny lub aplikacji sieci Web, które będą uzyskiwać dostęp do informacji o profilu i danych użytkownika, takich jak ich wiadomości e-mail. Złośliwy aktor może korzystać z uzyskanych uprawnień, które otrzymały w celu zaszyfrowania zawartości skrzynki pocztowej, i uzyskania dostępu do oprogramowania wymuszającego okup w celu odzyskania danych skrzynki pocztowej. [Administratorzy powinni przeglądać i przeprowadzać inspekcję](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) uprawnień określonych przez użytkowników lub wyłączać możliwość udzielania zgody użytkownikom.

Oprócz inspekcji uprawnień określonych przez użytkowników można [lokalizować ryzykowne lub niepożądane aplikacje OAuth](/cloud-app-security/investigate-risky-oauth) w środowiskach Premium.

## <a name="step-5---enable-end-user-self-service"></a>Krok 5 — Włączanie samoobsługowego użytkownika końcowego

Tak dużo, jak to możliwe, należy zrównoważyć zabezpieczenia z wydajnością. W tych samych wierszach zbliżamy się do sposób myślenia, że tworzysz podstawę do zabezpieczeń w długim przebiegu, możesz usunąć tarcie z organizacji, zwiększając możliwości użytkowników, a pozostałe czujność.

### <a name="implement-self-service-password-reset"></a>Implementowanie samoobsługowego resetowania hasła

Funkcja samoobsługowego resetowania haseł w usłudze Azure AD [(SSPR)](../../active-directory/authentication/tutorial-enable-sspr.md) umożliwia administratorom IT łatwe Resetowanie lub odblokowanie haseł lub kont bez pomocy technicznej ani interwencji administratora. System zawiera szczegółowe raporty, które są śledzone, gdy użytkownicy zresetują swoje hasła, wraz z powiadomieniami o alertach dotyczących nieprawidłowego lub nadużycia.

### <a name="implement-self-service-group-and-application-access"></a>Implementowanie dostępu do grup i aplikacji samoobsługowej

Usługa Azure AD zapewnia innym użytkownikom możliwość zarządzania dostępem do zasobów przy użyciu grup zabezpieczeń, grup Microsoft 365, ról aplikacji i wykazów pakietów dostępu.  [Samoobsługowe zarządzanie grupami](../../active-directory/enterprise-users/groups-self-service-management.md) pozwala właścicielom grup zarządzać własnymi grupami bez konieczności przypisywania roli administracyjnej. Użytkownicy mogą również tworzyć grupy Microsoft 365 i zarządzać nimi bez polegania na administratorach do obsługi swoich żądań, a nieużywane grupy wygasną automatycznie.  [Zarządzanie prawami w usłudze Azure AD](../../active-directory/governance/entitlement-management-overview.md) umożliwia dalsze delegowanie i widoczność przy użyciu kompleksowych przepływów pracy żądań dostępu oraz automatyczne wygaśnięcie.  Można delegować do użytkowników niebędących administratorami możliwość konfigurowania własnych pakietów dostępu dla grup, zespołów, aplikacji i witryn usługi SharePoint Online, które są właścicielami, za pomocą zasad niestandardowych dla osób, które są wymagane do zatwierdzania dostępu, w tym konfigurowania menedżerów pracowników i partnerów firmy jako osób zatwierdzających.

### <a name="implement-azure-ad-access-reviews"></a>Zaimplementuj przeglądy dostępu usługi Azure AD

[Przeglądy dostępu w usłudze Azure AD](../../active-directory/governance/access-reviews-overview.md)umożliwiają zarządzanie pakietami dostępu i członkostwem w grupach, dostępem do aplikacji przedsiębiorstwa oraz przypisaniami ról uprzywilejowanych w celu zapewnienia bezpieczeństwa.  Regularnym nadzorem użytkowników, właściciele zasobów i inni recenzenci zapewniają, że użytkownicy nie zachowują dostępu przez dłuższy czas, gdy nie będą już potrzebne.

## <a name="summary"></a>Podsumowanie

Istnieje wiele aspektów bezpiecznej infrastruktury tożsamości, ale ta lista kontrolna z pięcioma krokami pomoże Ci szybko wykonać bezpieczniejsze i bezpieczne infrastruktury tożsamości:

* Wzmocnij swoje poświadczenia.
* Zmniejsz obszar narażony na ataki.
* Automatyzowanie odpowiedzi na zagrożenia.
* Korzystaj z analizy chmury.
* Włącz bardziej przewidywalne i kompletne zabezpieczenia użytkowników końcowych, korzystając z samoobsługowego rozwiązywania problemów.

Doceniamy, jak poważnie ponosisz bezpieczeństwo tożsamości i mamy nadzieję, że ten dokument jest przydatny do bardziej bezpiecznego stan organizacji.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz pomocy w zaplanowaniu i wdrożeniu zaleceń, zapoznaj się z [planami wdrażania projektu usługi Azure AD](../../active-directory/fundamentals/active-directory-deployment-plans.md) , aby uzyskać pomoc.

Jeśli masz pewność, że wszystkie te kroki są kompletne, Skorzystaj z [bezpiecznego wyniku tożsamości](../../active-directory/fundamentals/identity-secure-score.md)firmy Microsoft, co pozwoli na uzyskanie Aktualności przy użyciu [najnowszych najlepszych](identity-management-best-practices.md) rozwiązań i zagrożeń bezpieczeństwa.

---
title: Planowanie Azure Active Directory wdrożenia dostępu warunkowego
description: Dowiedz się, jak projektować zasady dostępu warunkowego i efektywnie wdrażać w organizacji.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d4ae1c9926c7ea1d18bf5c87fbed837edc2a5d5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83641489"
---
# <a name="plan--a-conditional-access-deployment"></a>Planowanie wdrożenia dostępu warunkowego

Planowanie wdrożenia dostępu warunkowego ma kluczowe znaczenie dla osiągnięcia strategii dostępu w organizacji dla aplikacji i zasobów.

W świecie w chmurze mobilnej na urządzeniach przenośnych użytkownicy uzyskują dostęp do zasobów organizacji z dowolnego miejsca przy użyciu różnych urządzeń i aplikacji. W efekcie skoncentrowanie się na tym, kto może uzyskać dostęp do zasobu, nie jest już wystarczające. Należy również wziąć pod uwagę, w jaki sposób użytkownik jest, używane urządzenie, dostęp do zasobu i nie tylko. 

Azure Active Directory (Azure AD) analizy dostępu warunkowego (CA) są sygnałami, takimi jak użytkownik, urządzenie i lokalizacja, aby zautomatyzować decyzje i wymusić zasady dostępu organizacyjnego dla zasobu. Zasad urzędu certyfikacji można użyć do zastosowania kontroli dostępu, takich jak Multi-Factor Authentication (MFA). Zasady dotyczące urzędów certyfikacji umożliwiają monitowanie użytkowników o uwierzytelnianie wieloskładnikowe, gdy jest to potrzebne w celu zapewnienia bezpieczeństwa, i niepotrzebnych użytkowników.

![Omówienie dostępu warunkowego](./media/plan-conditional-access/conditional-access-overview-how-it-works.png)

Firma Microsoft udostępnia standardowe zasady warunkowe o nazwie [zabezpieczenia domyślne](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) , które zapewniają podstawowy poziom zabezpieczeń. Jednak organizacja może potrzebować większej elastyczności niż domyślna oferta zabezpieczeń. Za pomocą dostępu warunkowego można dostosować ustawienia domyślne zabezpieczeń z większą szczegółowością i skonfigurować nowe zasady spełniające Twoje wymagania.

## <a name="learn"></a>Learn

Przed rozpoczęciem upewnij się, że rozumiesz, jak działa [dostęp warunkowy](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) , oraz kiedy należy go używać.

### <a name="benefits"></a>Korzyści

Korzyści wynikające z wdrożenia dostępu warunkowego są następujące:

* Zwiększ produktywność. Przerywaj użytkowników tylko przy użyciu stanu logowania, takiego jak MFA, gdy co najmniej jeden z tych sygnałów gwarantuje. Zasady dotyczące urzędów certyfikacji umożliwiają kontrolowanie czasu, w którym użytkownicy są monitowani o uwierzytelnianie wieloskładnikowe, gdy dostęp jest zablokowany, a kiedy muszą korzystać z zaufanego urządzenia.

* Zarządzanie ryzykiem. Automatyzacja oceny ryzyka z warunkami zasad oznacza, że ryzykowne logowania są już zidentyfikowane i skorygowane lub zablokowane. Sprzężenie dostępu warunkowego z usługą [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview), które wykrywa anomalie i podejrzane zdarzenia, umożliwia kierowanie w przypadku zablokowania lub warunkowego dostępu do zasobów. 

* Adresowanie zgodności i zarządzania. Dostęp warunkowy umożliwia inspekcję dostępu do aplikacji, obecne warunki użytkowania i ograniczanie dostępu na podstawie zasad zgodności.

* Zarządzaj kosztami. Przeniesienie zasad dostępu do usługi Azure AD ogranicza zależność od rozwiązań niestandardowych lub lokalnych na potrzeby dostępu warunkowego oraz kosztów infrastruktury.

### <a name="license-requirements"></a>Wymagania licencyjne

Zobacz [wymagania licencyjne dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

Jeśli wymagane są dodatkowe funkcje, mogą również być potrzebne powiązane licencje. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Wymagania wstępne

* Działa działająca dzierżawa usługi Azure AD z włączoną licencją Azure AD — wersja Premium lub próbną. W razie potrzeby [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Konto z uprawnieniami administratora dostępu warunkowego.

* Użytkownik niebędący administratorem z hasłem znanym, takim jak użytkownik testowy. Jeśli musisz utworzyć użytkownika, zobacz [Szybki Start: Dodawanie nowych użytkowników do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

* Grupa, której członkiem jest użytkownik niebędący administratorem. Jeśli musisz utworzyć grupę, zobacz [Tworzenie grupy i Dodawanie członków w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal).

### <a name="training-resources"></a>Zasoby szkoleniowe

Następujące zasoby mogą być przydatne podczas nauki na temat dostępu warunkowego:


**Filmy wideo**
* [Co to jest dostęp warunkowy?](https://youtu.be/ffMAw2IVO7A)
* [Jak wdrożyć dostęp warunkowy?](https://youtu.be/c_izIRNJNuk)
* [Jak wdrożyć zasady urzędu certyfikacji dla użytkowników końcowych?](https://youtu.be/0_Fze7Zpyvc)
* [Dostęp warunkowy przy użyciu kontrolek urządzeń](https://youtu.be/NcONUf-jeS4)
* [Dostęp warunkowy przy użyciu usługi Azure MFA](https://youtu.be/Tbc-SU97G-w)
* [Dostęp warunkowy w Enterprise Mobility + Security](https://youtu.be/A7IrxAH87wc)
* [Dostęp warunkowy oparty na urządzeniach](https://in.video.search.yahoo.com/search/video;_ylt=AwrPiBX0yHRcZiMAhFa7HAx.;_ylu=X3oDMTB0N2poMXRwBGNvbG8Dc2czBHBvcwMxBHZ0aWQDBHNlYwNwaXZz?p=conditional+access+videos+microsoft&fr2=piv-web&fr=mcafee)

**Kursy online w witrynie PluralSight**
* [Projektowanie zarządzania tożsamościami w Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
* [Uwierzytelnianie projektu dla Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
* [Zaprojektuj autoryzację dla Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)

**Często zadawane pytania**

[Często zadawane pytania dotyczące dostępu warunkowego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/faqs)
## <a name="plan-the-deployment-project"></a>Planowanie projektu wdrożenia

Podczas określania strategii tego wdrożenia w danym środowisku należy wziąć pod uwagę potrzeby organizacyjne.
### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich uczestników projektu
Gdy projekty technologii kończą się niepowodzeniem, zazwyczaj są to spowodowane niezgodnością oczekiwań, rezultatów i obowiązków. Aby uniknąć tych pułapek, [upewnij się, że interesują](https://aka.ms/deploymentplans) Cię odpowiednie osoby zainteresowane i że role projektu są jasne.

### <a name="plan-communications"></a>Planowanie komunikacji
Komunikacja jest niezwykle ważna dla sukcesu każdej nowej usługi. Proaktywnie Komunikuj się z użytkownikami, w jaki sposób ich środowisko zostanie zmienione, gdy ulegnie zmianie, i jak uzyskać pomoc techniczną, jeśli wystąpią problemy.

### <a name="plan-a-pilot"></a>Planowanie pilotażu
Jeśli nowe zasady są gotowe dla danego środowiska, wdróż je w fazach w środowisku produkcyjnym. Najpierw Zastosuj zasady do małego zestawu użytkowników w środowisku testowym i sprawdź, czy zasady zachowują się zgodnie z oczekiwaniami. Zapoznaj [się z najlepszymi rozwiązaniami dla pilotażu](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans).

> [!NOTE]
> W przypadku wdrażania nowych zasad, które nie są specyficzne dla administratorów, Wyklucz wszystkich administratorów. Dzięki temu administratorzy mogą nadal uzyskiwać dostęp do zasad i wprowadzać zmiany lub odwoływać je, jeśli wystąpi znaczący wpływ. Przed zastosowaniem do wszystkich użytkowników należy zawsze sprawdzać poprawność zasad z mniejszymi grupami użytkowników.

## <a name="understand-ca-policy-components"></a>Informacje o składnikach zasad urzędu certyfikacji

Zasady urzędu certyfikacji są instrukcjami if-then: Jeśli zostanie spełniony przypisany warunek, Zastosuj te kontrole dostępu. 

![Omówienie dostępu warunkowego](media/plan-conditional-access/10.png)

W przypadku konfigurowania zasad urzędu certyfikacji warunki są nazywane *przypisaniami*. Zasady dotyczące urzędów certyfikacji umożliwiają wymuszanie kontroli dostępu w aplikacjach organizacji na podstawie określonych przypisań.

![przypisania i kontrola dostępu ](media/plan-conditional-access/ca-policy-access.png)


Aby uzyskać więcej informacji, zobacz [Kompilowanie zasad urzędu certyfikacji](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies).

[Przypisania](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies) definiują

* [Użytkownicy i grupy](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-users-groups) , na które mają wpływ zasady

* [aplikacje w chmurze lub akcje](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-cloud-apps) , do których zostaną zastosowane zasady 

* [warunki](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions) , zgodnie z którymi zasady będą stosowane. 
<p>

![ekran tworzenia zasad](media/plan-conditional-access/create-policy.png)

Ustawienia [kontroli dostępu](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies) określają sposób wymuszania zasad:

* [Udziel lub Blokuj](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-grant) dostęp do aplikacji w chmurze.

* [Kontrolki sesji](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-session) umożliwiają ograniczoną liczbę środowisk w określonych aplikacjach w chmurze.

### <a name="ask-the-right-questions-to-build-your-policies"></a>Zadawaj odpowiednie pytania, aby skompilować swoje zasady

Zasady udzielają odpowiedzi na pytania dotyczące użytkowników, którzy powinni mieć dostęp do zasobów, do zasobów, do których mają dostęp, i w ramach jakich warunków. Zasady mogą być przeznaczone do udzielania dostępu lub do blokowania dostępu. Upewnij się, że zadajesz odpowiednie pytania dotyczące tego, co chcemy osiągnąć. 

Udokumentowanie odpowiedzi na pytania dotyczące poszczególnych zasad przed ich skompilowaniem. 

#### <a name="common-questions-about-assignments"></a>Często zadawane pytania dotyczące przypisań

[Użytkownicy i grupy](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-users-groups)

* Których użytkowników i grup zostaną dołączone lub wykluczone z zasad?

* Czy te zasady obejmują wszystkich użytkowników, konkretną grupę użytkowników, role katalogów lub użytkowników zewnętrznych?

[Aplikacje w chmurze lub akcje](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-cloud-apps)

* Jakie aplikacje będą stosowane dla zasad?

* Jakie akcje użytkownika będą podlegać tym zasadom?

[Warunki](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions)

* Które platformy urządzeń zostaną dołączone lub wykluczone z zasad?

* Jakie są zaufane lokalizacje organizacji?

* Które lokalizacje zostaną uwzględnione lub wykluczone z zasad?

* Jakie typy aplikacji klienckich (przeglądarki, urządzeń przenośnych, klientów klasycznych, aplikacje ze starszymi metodami uwierzytelniania) będą dołączane do zasad lub z nich wykluczone?

* Czy masz zasady, które mogą obejmować wykluczenie urządzeń przyłączonych do usługi Azure AD lub hybrydowe urządzenia z usługą Azure AD z zasad? 

* Czy w przypadku korzystania z usługi [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview)chcesz uwzględnić ochronę przed ryzykiem związanym z logowaniem?

#### <a name="common-questions-about-access-controls"></a>Często zadawane pytania dotyczące kontroli dostępu

[Udziel lub Blokuj](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-grant) 

Czy chcesz udzielić dostępu do zasobów, wymagając co najmniej jednego z następujących elementów?

* Wymaganie uwierzytelniania wieloskładnikowego

* Wymagaj, aby urządzenie było oznaczone jako zgodne

* Wymagaj hybrydowego urządzenia dołączonego do usługi Azure AD

* Wymaganie zatwierdzonej aplikacji klienckiej

* Wymaganie zasad ochrony aplikacji

[Kontrola sesji](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-session)

Czy chcesz wymusić dowolne z następujących kontroli dostępu w aplikacjach w chmurze?

* Użyj uprawnień wymuszanych przez aplikację

* Użyj Kontrola dostępu warunkowego aplikacji

* Wymuś częstotliwość logowania

* Korzystanie z trwałych sesji przeglądarki

### <a name="access-token-issuance"></a>Wystawianie tokenów dostępu

Ważne jest, aby zrozumieć, jak są wydawane tokeny dostępu. 

![Diagram wystawiania tokenów dostępu](media/plan-conditional-access/CA-policy-token-issuance.png)

**W szczególności należy zauważyć, że jeśli nie jest wymagane żadne przypisanie, a zasady urzędu certyfikacji nie są stosowane, to domyślne zachowanie ma na celu wystawienie tokenu dostępu**. 

Rozważmy na przykład zasady, w których:

Jeśli użytkownik należy do grupy 1, Wymuś dostęp MFA do aplikacji 1.

Jeśli użytkownik nie znajduje się w grupie 1 próbuje uzyskać dostęp do aplikacji, nie jest spełniony warunek "Jeśli" i zostanie wystawiony token. Aby wykluczyć użytkowników spoza grupy 1, wymagane są osobne zasady umożliwiające zablokowanie wszystkich innych użytkowników.

## <a name="follow-best-practices"></a>Postępuj zgodnie z najlepszymi rozwiązaniami

Struktura dostępu warunkowego zapewnia dużą elastyczność konfiguracji. Jednak doskonałe elastyczność oznacza, że należy uważnie przejrzeć wszystkie zasady konfiguracji przed jej zwolnieniem, aby uniknąć niepożądanych wyników.

### <a name="apply-ca-policies-to-every-app"></a>Stosowanie zasad urzędu certyfikacji do każdej aplikacji

Tokeny dostępu są domyślnie wysyłane, jeśli warunek zasad urzędu certyfikacji nie wyzwala kontroli dostępu. Upewnij się, że dla każdej aplikacji zastosowano co najmniej jedną zasadę dostępu warunkowego

> [!IMPORTANT]
> Należy zachować ostrożność w przypadku korzystania z bloków i wszystkich aplikacji w ramach jednej zasady. Może to spowodować zablokowanie administratorów z portalu administracyjnego platformy Azure, a wykluczenia nie można skonfigurować dla ważnych punktów końcowych, takich jak Microsoft Graph.

### <a name="minimize-the-number-of-ca-policies"></a>Minimalizacja liczby zasad urzędu certyfikacji

Tworzenie zasad dla każdej aplikacji nie jest wydajne i prowadzi do trudnej administracji. Dostęp warunkowy obejmuje tylko pierwsze zasady 195 dla każdego użytkownika. Zalecamy analizowanie aplikacji i grupowanie ich w aplikacje, które mają takie same wymagania dotyczące zasobów dla tych samych użytkowników. Jeśli na przykład wszystkie aplikacje pakietu Office 365 lub wszystkie aplikacje usługi kadr mają takie same wymagania dla tych samych użytkowników, należy utworzyć pojedynczą zasadę i uwzględnić wszystkie aplikacje, do których ma zastosowanie. 

### <a name="set-up-emergency-access-accounts"></a>Konfigurowanie kont dostępu awaryjnego

Jeśli zasady zostały nieprawidłowo skonfigurowane, można zablokować organizacje z Azure Portal. Aby wyeliminować wpływ przypadkowego blokowania administratora, należy utworzyć co najmniej dwa [konta dostępu awaryjnego](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) w organizacji.

* Utwórz konto użytkownika przeznaczone do administrowania zasadami i wykluczone ze wszystkich zasad.

* Scenariusz końca szkła dla środowisk hybrydowych:

  * Utwórz lokalną grupę zabezpieczeń i zsynchronizuj ją z usługą Azure AD. Grupa zabezpieczeń powinna zawierać dedykowane konto administracyjne zasad. 

   * Wyłącz tę grupę zabezpieczeń, aby utworzyć wszystkie zasady urzędu certyfikacji.

   * Po wystąpieniu awarii usługi należy dodać innych administratorów do grupy lokalnej zgodnie z potrzebami i wymusić synchronizację. Pozwala to na animowanie wykluczenia do zasad urzędu certyfikacji.

### <a name="set-up-report-only-mode"></a>Konfigurowanie trybu tylko do raportowania

Może być trudne do przewidywania liczby i nazw użytkowników, których dotyczą typowe inicjatywy wdrażania, takich jak:

* Blokowanie starszego uwierzytelniania
* wymaganie uwierzytelniania wieloskładnikowego
* Implementowanie zasad dotyczących ryzyka związanego z logowaniem

[Tryb tylko do raportowania](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) pozwala administratorom na ocenę wpływu zasad urzędu certyfikacji przed włączeniem ich w środowisku.

Dowiedz się, jak [skonfigurować tryb tylko do raportowania dla zasad urzędu certyfikacji](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-report-only).

### <a name="plan-for-disruption"></a>Planowanie przerw w działaniu

Jeśli korzystasz z pojedynczej kontroli dostępu, takiej jak MFA lub lokalizacji sieciowej, aby zabezpieczyć systemy INFORMATYCZNe, musisz mieć dostęp do błędów, jeśli taka kontrola dostępu będzie niedostępna lub nieprawidłowo skonfigurowana. Aby zmniejszyć ryzyko związane z blokadą w przypadku nieprzewidzianych zakłóceń, należy [zaplanować strategie](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) do zastosowania w organizacji.

### <a name="set-naming-standards-for-your-policies"></a>Ustawianie standardów nazewnictwa dla zasad

Standard nazewnictwa pomaga znaleźć zasady i zrozumieć ich przeznaczenie bez otwierania ich w portalu administracyjnym platformy Azure. Zaleca się, aby nazwy zasad były wyświetlane:

* Numer sekwencyjny

* Aplikacje w chmurze, których dotyczy

* Odpowiedź

* Kto ma zastosowanie do

* Gdy ma zastosowanie (jeśli dotyczy)

![Standard nazewnictwa](media/plan-conditional-access/11.png)

**Przykład**: Zasady wymagające uwierzytelniania wieloskładnikowego dla użytkowników marketingu uzyskujących dostęp do aplikacji Dynamics CRP z sieci zewnętrznych mogą być następujące:

![Standard nazewnictwa](media/plan-conditional-access/naming-example.png)

Opisowa nazwa pomaga zachować przegląd implementacji dostępu warunkowego. Numer sekwencyjny jest przydatny, jeśli musisz odwołać się do zasad w konwersacji. Na przykład podczas komunikowania się z administratorem na telefonie można Poproś o otwarcie zasad CA01, aby rozwiązać problem.

#### <a name="naming-standards-for-emergency-access-controls"></a>Normy nazewnictwa dla kontroli dostępu awaryjnego

Oprócz aktywnych zasad należy zaimplementować wyłączone zasady, które działają jako pomocnicze, [odporne na awarie i w sytuacjach awaryjnych](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls). Standard nazewnictwa dla zasad awaryjnych powinien obejmować:
* Włącz na początku, aby nawiązać nazwę między innymi zasadami.

* Nazwa zakłócenia, do której należy zastosować.

* Numer sekwencyjny porządkowania, który pomaga administratorowi znać, w którym należy włączyć zasady zamówień.

**Przykład**

Następująca nazwa wskazuje, że te zasady są pierwszymi czterema zasadami, które należy włączyć w przypadku wystąpienia przerwy w działaniu usługi MFA:

EM01 — Włącz w sytuacjach AWARYJNych: zakłócenia MFA [1/4] — Exchange SharePoint: Wymagaj hybrydowego sprzężenia usługi Azure AD dla użytkowników VIP.

### <a name="exclude-countries-from-which-you-never-expect-a-sign-in"></a>Wyklucz kraje, z których nigdy nie oczekuje się logowania.

Usługa Azure Active Directory umożliwia tworzenie [nazwanych lokalizacji](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition). Utwórz nazwę lokalizacji, która zawiera wszystkie kraje, z których nie oczekuje się logowania. Następnie utwórz zasady dla wszystkich aplikacji, które blokują logowanie z tej nazwy lokalizacji. **Upewnij się, że Administratorzy są wykluczeni z tych zasad**.

### <a name="plan-your-policy-deployment"></a>Planowanie wdrożenia zasad

Jeśli nowe zasady są gotowe dla danego środowiska, przed jego zwolnieniem upewnij się, że zostały one przejrzane, aby uniknąć niepożądanych wyników. Zapoznaj się z poniższą dokumentacją, aby zrozumieć ważne informacje dotyczące sposobu stosowania zasad i rozwiązywania problemów

* [Co należy wiedzieć](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)

* [Co należy unikać](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)

## <a name="common-policies"></a>Wspólne zasady

Planując rozwiązanie zasad urzędu certyfikacji, należy ocenić, czy należy utworzyć zasady, aby osiągnąć poniższe wyniki.

### <a name="require-mfa"></a>Wymaganie uwierzytelniania wieloskładnikowego

Typowe przypadki użycia w celu wymagania dostępu MFA:

* [Według administratorów](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-admin-mfa)

* [Do określonych aplikacji](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-mfa)

* [Dla wszystkich użytkowników](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-all-users-mfa)

* [Z lokalizacji sieciowych nie ufasz](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)

* [Zarządzanie platformą Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-azure-management)

### <a name="respond-to-potentially-compromised-accounts"></a>Reagowanie na potencjalnie naruszone konta

Korzystając z zasad urzędu certyfikacji, można zaimplementować automatyczne odpowiedzi na potrzeby logowania przez potencjalnie naruszone tożsamości. Prawdopodobieństwo naruszenia zabezpieczeń konta jest wyrażone w formie poziomów ryzyka. Istnieją dwa poziomy ryzyka obliczone przez ochronę tożsamości: ryzyko związane z logowaniem i ryzykiem użytkownika. Następujące trzy domyślne zasady, które można włączyć.

* [Wymagaj, aby wszyscy użytkownicy rejestrowali się w usłudze MFA](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk)

* [Wymagaj zmiany hasła dla użytkowników, którzy są o wysokim ryzyku](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk)

* [Wymagaj uwierzytelniania wieloskładnikowego dla użytkowników z średnim lub wysokim ryzykiem logowania](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk)

### <a name="require-managed-devices"></a>Wymaganie urządzeń zarządzanych

Rozprzestrzenianie się obsługiwanych urządzeń w celu uzyskania dostępu do zasobów w chmurze ułatwia zwiększenie produktywności użytkowników. Prawdopodobnie nie chcesz, aby niektóre zasoby w danym środowisku były dostępne dla urządzeń z nieznanym poziomem ochrony. Dla tych zasobów należy [wymagać, aby użytkownicy mieli do nich dostęp tylko przy użyciu urządzenia zarządzanego](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices).

### <a name="require-approved-client-apps"></a>Wymaganie zatwierdzonych aplikacji klienckich

Pracownicy używają urządzeń przenośnych zarówno do zadań osobistych, jak i służbowych. W przypadku scenariuszy BYOD należy zdecydować, czy zarządzać całym urządzeniem, czy tylko danymi na nim. w przypadku zarządzania tylko danymi i dostępem można [wymagać zatwierdzonych aplikacji w chmurze](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) , które mogą chronić dane firmowe. można na przykład wymagać dostępu do poczty e-mail tylko za pośrednictwem programu Outlook Mobile, a nie za pośrednictwem ogólnego programu poczty.

### <a name="block-access"></a>Blokowanie dostępu

Opcja [blokowania całego dostępu](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-block-access) jest zaawansowana. Można go użyć, na przykład podczas migrowania aplikacji do usługi Azure AD, ale nie jest to jeszcze gotowe do zalogowania się do niej. Blokuj dostęp: 

* Zastępuje wszystkie inne przypisania dla użytkownika

* Możliwość zablokowania całej organizacji logowania się do dzierżawy

> [!IMPORTANT]
> W przypadku tworzenia zasad blokowania dostępu dla wszystkich użytkowników należy wykluczyć konta dostępu awaryjnego i rozważyć wyłączenie wszystkich administratorów z zasad.

Inne typowe scenariusze, w których można zablokować dostęp dla użytkowników, to:

* [Blokuj niektóre lokalizacje sieciowe](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-location) , aby uzyskać dostęp do aplikacji w chmurze. Za pomocą tych zasad można blokować niektóre kraje, w których wiadomo, że ruch nie powinien pochodzić z usługi.

* Usługa Azure AD obsługuje starsze uwierzytelnianie. Starsza wersja uwierzytelniania nie obsługuje jednak usługi MFA, a wiele środowisk wymaga, aby zająć się zabezpieczeniami tożsamości. W takim przypadku można [zablokować aplikacjom korzystającym ze starszego uwierzytelniania](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication) dostęp do zasobów dzierżawy.

## <a name="build-and-test-policies"></a>Zasady kompilacji i testowania

Na każdym etapie wdrożenia upewnij się, że oceniasz, że wyniki są zgodnie z oczekiwaniami. 

Gdy nowe zasady są gotowe, wdróż je w fazach w środowisku produkcyjnym:

* Zapewnianie wewnętrznej komunikacji między użytkownikami końcowymi.

* Zacznij od małego zestawu użytkowników i sprawdź, czy zasady działają zgodnie z oczekiwaniami.

* Po rozszerzeniu zasad w celu uwzględnienia większej liczby użytkowników należy dalej wykluczać wszystkich administratorów. Wyłączenie administratorów gwarantuje, że ktoś nadal ma dostęp do zasad, jeśli wymagana jest zmiana.

* Zastosuj zasady dla wszystkich użytkowników dopiero po ich dokładnym przetestowaniu. Upewnij się, że masz co najmniej jedno konto administratora, do którego zasady nie są stosowane.

### <a name="create-test-users"></a>Tworzenie użytkowników testowych

Utwórz zestaw użytkowników testowych, które odzwierciedlają użytkowników w środowisku produkcyjnym. Tworzenie użytkowników testowych pozwala sprawdzić, czy zasady działają zgodnie z oczekiwaniami, zanim wpłyną na rzeczywistych użytkowników i potencjalnie zakłócają dostęp do aplikacji i zasobów.

W tym celu niektóre organizacje mają do tego celu test dzierżawców. Może jednak być trudno odtworzyć wszystkie warunki i aplikacje w dzierżawie testowej, aby w pełni przetestować wynik zasad.

### <a name="create-a-test-plan"></a>Utwórz plan testu

Plan testu jest istotny do porównania między oczekiwanymi wynikami i rzeczywistymi wynikami. Przed przeprowadzeniem testowania należy zawsze oczekiwać. W poniższej tabeli przedstawiono przykładowe przypadki testowe. Dostosuj scenariusze i oczekiwane wyniki w zależności od sposobu skonfigurowania zasad urzędu certyfikacji.

| Zasady| Scenariusz| Oczekiwany wynik |
| - | - | - |
| [Wymagaj uwierzytelniania wieloskładnikowego, gdy nie jest w pracy](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)| Autoryzowany użytkownik loguje się do aplikacji w ramach zaufanej lokalizacji/pracy| Użytkownik nie jest monitowany o uwierzytelnianie wieloskładnikowe |
| [Wymagaj uwierzytelniania wieloskładnikowego, gdy nie jest w pracy](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)| Autoryzowany użytkownik loguje się do aplikacji, gdy nie znajduje się w zaufanej lokalizacji/pracy| Użytkownik jest monitowany o uwierzytelnianie wieloskładnikowe i może zalogować się pomyślnie |
| [Wymagaj uwierzytelniania wieloskładnikowego (dla administratora)](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-baseline-protect-administrators)| Administrator globalny loguje się do aplikacji| Administrator jest monitowany o uwierzytelnianie wieloskładnikowe |
| [Ryzykowne logowania](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)| Użytkownik loguje się do aplikacji przy użyciu [przeglądarki sieci Tor](https://microsoft.sharepoint.com/azure/active-directory/active-directory-identityprotection-playbook)| Administrator jest monitowany o uwierzytelnianie wieloskładnikowe |
| [Zarządzanie urządzeniami](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)| Autoryzowany użytkownik próbuje zalogować się z autoryzowanego urządzenia| Udzielony dostęp |
| [Zarządzanie urządzeniami](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)| Autoryzowany użytkownik próbuje zalogować się z nieautoryzowanego urządzenia| Dostęp zablokowany |
| [Zmiana hasła dla ryzykownych użytkowników](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)| Autoryzowany użytkownik próbuje zalogować się przy użyciu złamanych poświadczeń (logowanie wysokiego ryzyka)| Użytkownik jest monitowany o zmianę hasła lub dostęp jest zablokowany na podstawie zasad |


 

### <a name="configure-the-test-policy"></a>Konfigurowanie zasad testów

W [Azure Portal](https://portal.azure.com/)należy skonfigurować zasady urzędu certyfikacji w obszarze Azure Active Directory > zabezpieczenia > dostęp warunkowy.

Jeśli chcesz dowiedzieć się więcej o sposobie tworzenia zasad urzędu certyfikacji, zobacz ten przykład: [zasady urzędu certyfikacji, które monitują o usługę MFA, gdy użytkownik zaloguje się do Azure Portal](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json). Ten przewodnik Szybki Start pomaga:

* Zapoznaj się z interfejsem użytkownika

* Zapoznaj się z pierwszym wrażeniem, jak działa dostęp warunkowy

### <a name="enable-the-policy-in-report-only-mode"></a>Włącz zasady w trybie tylko do raportowania

Aby ocenić wpływ zasad, Zacznij od włączenia zasad w [trybie tylko do raportowania](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only). Zasady dotyczące tylko raportów są oceniane podczas logowania, ale nie są wymuszane kontrolki Grant i Controls. Po zapisaniu zasad w trybie tylko do raportowania będzie można zobaczyć wpływ logowania w czasie rzeczywistym w dziennikach logowania. Z dzienników logowania wybierz zdarzenie i przejdź do karty tylko raport, aby zobaczyć wynik poszczególnych zasad dotyczących tylko raportów.


![tryb tylko raporty ](media/plan-conditional-access/report-only-mode.png)

Wybranie zasad pozwala także sprawdzić, jak zadania i kontroli dostępu zasad zostały ocenione przy użyciu ekranu Szczegóły zasad. Aby zasady miały zastosowanie do logowania, muszą być spełnione wszystkie skonfigurowane przypisania. 

### <a name="understand-the-impact-of-your-policies-using-the-insights-and-reporting-workbook"></a>Zapoznaj się z wpływem zasad przy użyciu szczegółowych informacji i skoroszytu raportów

Zagregowany wpływ zasad dostępu warunkowego można wyświetlić w podglądzie szczegółowe dane i raporty. Aby uzyskać dostęp do skoroszytu, potrzebna jest subskrypcja Azure Monitor i konieczne będzie [przesyłanie strumieniowe dzienników logowania do log Analytics obszaru roboczego](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). 

### <a name="simulate-sign-ins-using-the-what-if-tool"></a>Symulowanie logowania przy użyciu narzędzia do wykonania

Innym sposobem weryfikacji zasad dostępu warunkowego jest użycie [Narzędzia warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access-what-if), które symuluje, które zasady byłyby stosowane do logowania użytkownika w hipotetycznych warunkach. Wybierz atrybuty logowania, które chcesz przetestować (takie jak użytkownik, aplikacja, platforma urządzenia i lokalizacja) i zobacz, które zasady będą stosowane.

> [!NOTE] 
> Gdy symulowane uruchomienie daje dobrym pomysłom wpływ zasad urzędu certyfikacji, nie zastępuje rzeczywistego przebiegu testu.

### <a name="test-your-policy"></a>Testowanie zasad

Wykonaj każdy test w planie testów z użytkownikami testowymi.

**Upewnij się, że przetestujesz kryteria wykluczenia zasad**. Na przykład możesz wykluczyć użytkownika lub grupę z zasad, które wymagają uwierzytelniania wieloskładnikowego. Przetestuj, czy wykluczeni użytkownicy są monitowani o uwierzytelnianie wieloskładnikowe, ponieważ połączenie innych zasad może wymagać uwierzytelniania MFA dla tych użytkowników.

### <a name="roll-back-policies"></a>Wycofywanie zasad

W przypadku konieczności wycofania nowo wdrożonych zasad należy użyć co najmniej jednej z następujących opcji:

* **Wyłącz zasady.** Wyłączenie zasad gwarantuje, że nie ma zastosowania, gdy użytkownik próbuje się zalogować. Zawsze możesz wrócić i włączyć zasady, gdy wolisz korzystać z niej.

![Włącz obraz zasad](media/plan-conditional-access/enable-policy.png)

* **Wykluczanie użytkownika lub grupy z zasad.** Jeśli użytkownik nie może uzyskać dostępu do aplikacji, możesz wybrać opcję wykluczenia użytkownika z zasad.

![Wyklucz użytkowników i grupy](media/plan-conditional-access/exclude-users-groups.png)

> [!NOTE]
>  Ta opcja powinna być używana oszczędnie, tylko w sytuacjach, gdy użytkownik jest zaufany. Użytkownik powinien zostać dodany z powrotem do zasad lub grupy tak szybko, jak to możliwe.

* **Usuń zasady.** Jeśli zasady nie są już wymagane, [Usuń](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json) je.

## <a name="manage-access-to-cloud-apps"></a>Zarządzanie dostępem do aplikacji w chmurze

Poniższe opcje zarządzania umożliwiają kontrolowanie zasad urzędu certyfikacji i zarządzanie nimi:

![Zarządzanie — dostęp](media/plan-conditional-access/manage-access.png)


### <a name="named-locations"></a>Nazwane lokalizacje

Warunek lokalizacji zasad urzędu certyfikacji umożliwia powiązanie ustawień kontroli dostępu z lokalizacjami sieciowymi użytkowników. Przy użyciu [nazwanych lokalizacji](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)można tworzyć logiczne grupowania zakresów adresów IP lub krajów i regionów.

### <a name="custom-controls"></a>Kontrolki niestandardowe

[Niestandardowe kontrolki](https://docs.microsoft.com/azure/active-directory/conditional-access/controls) przekierowują użytkowników do zgodnej usługi, aby spełnić wymagania dotyczące uwierzytelniania poza usługą Azure AD. W celu zapewnienia zgodności z tym formantem przeglądarka użytkownika zostanie przekierowana do usługi zewnętrznej, program wykonuje wymagane uwierzytelnianie, a następnie nastąpi przekierowanie z powrotem do usługi Azure AD. Usługa Azure AD weryfikuje odpowiedź i, jeśli użytkownik został pomyślnie uwierzytelniony lub zweryfikowany, użytkownik będzie kontynuował pracę w przepływie dostępu warunkowego.

### <a name="terms-of-use"></a>Warunki użytkowania

Przed uzyskaniem dostępu do niektórych aplikacji w chmurze w Twoim środowisku możesz uzyskać zgodę od użytkowników, akceptując Warunki użytkowania (warunków użytkowania). Postępuj zgodnie [z tym przewodnikiem Szybki Start, aby utworzyć warunki użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou).

### <a name="classic-policies"></a>Zasady klasyczne

W [Azure Portal](https://portal.azure.com/)można znaleźć zasady dotyczące urzędu certyfikacji w obszarze Azure Active Directory > zabezpieczenia > dostęp warunkowy. Organizacja może również mieć starsze zasady urzędu certyfikacji, które nie zostały utworzone przy użyciu tej strony. Te zasady są znane jako zasady klasyczne. Zalecamy [Migrowanie zasad klasycznych w Azure Portal](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices).

## <a name="troubleshoot-conditional-access"></a>Rozwiązywanie problemów z dostępem warunkowym

Jeśli użytkownik ma problem z zasadami urzędu certyfikacji, Zbierz poniższe informacje, aby ułatwić rozwiązywanie problemów.

* Nazwa zasady użytkownika

* Nazwa wyświetlana użytkownika

* Nazwa systemu operacyjnego

* Sygnatura czasowa (przybliżona jest OK)

* Aplikacja docelowa

* Typ aplikacji klienta (przeglądarka vs Client)

* Identyfikator korelacji (jest to unikatowe dla logowania)

Jeśli użytkownik odebrał komunikat z linkiem więcej szczegółów, może zbierać większość tych informacji.

![Nie można uzyskać komunikatu o błędzie aplikacji](media/plan-conditional-access/cant-get-to-app.png)

Po zebraniu informacji zapoznaj się z następującymi zasobami:

* [Problemy z logowaniem przy użyciu dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) — informacje o nieoczekiwanych próbach logowania związane z dostępem warunkowym przy użyciu komunikatów o błędach i dziennika logowania usługi Azure AD.

* [Korzystanie z narzędzia do analizy warunkowej — informacje](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access-what-if) o tym, dlaczego zasady były lub nie zostały zastosowane do użytkownika w konkretnym przypadku lub w przypadku, gdy zasady byłyby stosowane w znanym stanie.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o uwierzytelnianiu wieloskładnikowym](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)

[Dowiedz się więcej na temat ochrony tożsamości](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

[Zarządzanie zasadami urzędu certyfikacji za pomocą interfejsu API Microsoft Graph](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)

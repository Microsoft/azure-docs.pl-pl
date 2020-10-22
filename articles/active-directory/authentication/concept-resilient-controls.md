---
title: Tworzenie odpornej strategii zarządzania kontrolą dostępu — Azure AD
description: Ten dokument zawiera wskazówki dotyczące strategii podejmowanych przez organizację w celu zapewnienia odporności na zmniejszenie ryzyka związanego z blokadą w przypadku nieprzewidzianych przerw w działaniu
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6af2f65aa2e2052a79f4c5cffd7ff4a38a9fc838
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366568"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Utwórz odporną strategię zarządzania kontrolą dostępu za pomocą Azure Active Directory

>[!NOTE]
> The information contained in this document represents the current view of Microsoft Corporation on the issues discussed as of the date of publication. Ponieważ firma Microsoft musi reagować na zmieniające się warunki rynkowe, nie powinna być interpretowana jako zobowiązanie w ramach firmy Microsoft, a firma Microsoft nie gwarantuje dokładności informacji przedstawionych po dacie publikacji.

Organizacje korzystające z pojedynczej kontroli dostępu, takie jak uwierzytelnianie wieloskładnikowe (MFA) lub pojedynczej lokalizacji sieciowej, w celu zabezpieczenia systemów IT są podatne na dostęp do błędów w aplikacjach i zasobach, jeśli ta pojedyncza kontrola dostępu będzie niedostępna lub nieprawidłowo skonfigurowana. Na przykład klęska żywiołowa może skutkować niedostępnością dużych segmentów infrastruktury telekomunikacyjnej lub sieci firmowych. Takie zakłócenia może uniemożliwić użytkownikom końcowym i administratorom możliwość zalogowania się.

Niniejszy dokument zawiera wskazówki dotyczące strategii podejmowanych przez organizację w celu zapewnienia odporności na zmniejszenie ryzyka związanego z blokadą w przypadku nieprzewidzianych przerw w następujących scenariuszach:

 1. Organizacje mogą zwiększyć odporność, aby zmniejszyć ryzyko związane z blokadą **przed zakłóceniami** przez implementację strategii ograniczenia lub planów awaryjnych.
 2. Organizacje mogą nadal uzyskiwać dostęp do aplikacji i zasobów, które wybierają **podczas przerw** w działaniu, stosując strategie zaradcze i plany awaryjne.
 3. Organizacje powinny upewnić się, że zachowują informacje, takie jak dzienniki,  **po przerwie** i przed wycofaniem wszelkich zaimplementowanych przez nie zdarzeń.
 4. Organizacje, które nie wdrożyły strategii zapobiegania lub alternatywne plany mogą być w stanie zaimplementować **Opcje awaryjne** , aby rozwiązać problem.

## <a name="key-guidance"></a>Najważniejsze wskazówki

W tym dokumencie znajdują się cztery kluczowe wnioski:

* Należy unikać blokowania administratorów przy użyciu kont dostępu awaryjnego.
* Implementowanie usługi MFA przy użyciu dostępu warunkowego (CA), a nie usługi MFA dla poszczególnych użytkowników.
* Ograniczanie blokady użytkownika przy użyciu wielu kontrolek dostępu warunkowego (CA).
* Ograniczanie blokady użytkownika przez inicjowanie obsługi wielu metod uwierzytelniania lub odpowiedników dla każdego użytkownika.

## <a name="before-a-disruption"></a>Przed zakłóceniami

Zmniejszenie rzeczywistej przerwy w działaniu musi być podstawowym punktem działania organizacji w zakresie rozwiązywania problemów z kontrolą dostępu. Środki zaradcze obejmują planowanie rzeczywistego zdarzenia oraz implementowanie strategii, aby upewnić się, że kontrolki dostępu i operacje nie mają wpływu na przerwy.

### <a name="why-do-you-need-resilient-access-control"></a>Dlaczego potrzebujesz odpornej kontroli dostępu?

 Tożsamość jest płaszczyzną kontroli użytkowników, którzy uzyskują dostęp do aplikacji i zasobów. System tożsamości decyduje, którzy użytkownicy i w jakich warunkach, takich jak kontrola dostępu lub wymagania uwierzytelniania, uzyskują dostęp do aplikacji. Jeśli co najmniej jedno wymaganie uwierzytelniania lub kontroli dostępu nie jest dostępne dla użytkowników do uwierzytelnienia z powodu nieprzewidzianych okoliczności, organizacje mogą napotkać jeden lub oba z następujących problemów:

* **Blokada administratora:** Administratorzy nie mogą zarządzać dzierżawcą ani usługami.
* **Blokada użytkownika:** Użytkownicy nie mogą uzyskać dostępu do aplikacji ani zasobów.

### <a name="administrator-lockout-contingency"></a>Sytuacje awaryjne blokady administratora

Aby odblokować dostęp administratora do dzierżawy, należy utworzyć konta dostępu awaryjnego. Te konta dostępu awaryjnego, nazywane również kontami *ze szkłami* , umożliwiają dostęp do zarządzania konfiguracją usługi Azure AD, gdy nie są dostępne normalne procedury dostępu do konta uprzywilejowanego. Po [zaleceń dotyczących konta dostępu awaryjnego]( ../users-groups-roles/directory-emergency-access.md)należy utworzyć co najmniej dwa konta dostępu awaryjnego.

### <a name="mitigating-user-lockout"></a>Ograniczanie blokady użytkownika

 Aby zmniejszyć ryzyko związane z blokadą użytkownika, należy użyć zasad dostępu warunkowego z wieloma kontrolkami, aby umożliwić użytkownikom wybór sposobu uzyskiwania dostępu do aplikacji i zasobów. Przez umożliwienie użytkownikowi wyboru między, na przykład zalogowanie się za pomocą usługi MFA **lub** zalogowanie się z urządzenia zarządzanego **lub** zalogowanie się z sieci firmowej, jeśli jedna z kontroli dostępu jest niedostępna, użytkownik ma inne opcje, aby kontynuować pracę.

#### <a name="microsoft-recommendations"></a>Zalecenia firmy Microsoft

Uwzględnij następujące kontrolki dostępu do istniejących zasad dostępu warunkowego dla organizacji:

1. Zainicjuj obsługę wielu metod uwierzytelniania dla każdego użytkownika, który korzysta z różnych kanałów komunikacyjnych, na przykład aplikacji Microsoft Authenticator (internetowej), tokenu OATH (wygenerowanego na urządzeniu) i wiadomości SMS (telephonic). Poniższy skrypt programu PowerShell pomoże Ci określić z wyprzedzeniem dodatkowe metody, które użytkownicy powinni zarejestrować: [skrypt analizy metody uwierzytelniania usługi Azure MFA](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/).
2. Wdróż usługę Windows Hello dla firm na urządzeniach z systemem Windows 10, aby spełnić wymagania usługi MFA bezpośrednio po zalogowaniu się do urządzenia.
3. Używaj zaufanych urządzeń za pośrednictwem [sprzężenia hybrydowego usługi Azure AD](../devices/overview.md) lub [Microsoft Intune zarządzanych urządzeń](/intune/planning-guide). Zaufane urządzenia będą ulepszać środowisko użytkownika, ponieważ zaufane urządzenie może spełnić wymagania dotyczące silnych uwierzytelnień zasad bez wyzwania usługi MFA dla użytkownika. Uwierzytelnianie wieloskładnikowe będzie wymagane podczas rejestrowania nowego urządzenia i uzyskiwania dostępu do aplikacji lub zasobów z niezaufanych urządzeń.
4. Użyj zasad opartych na ryzyku usługi Azure AD Identity Protection, które uniemożliwiają dostęp, gdy użytkownik lub logowanie jest zagrożone w przypadku stałych zasad usługi MFA.
5. W przypadku ochrony dostępu do sieci VPN przy użyciu rozszerzenia serwera NPS usługi Azure MFA należy rozważyć federowanie rozwiązanie sieci VPN jako [aplikację SAML](../manage-apps/view-applications-portal.md) i określić kategorię aplikacji zgodnie z zaleceniami poniżej. 

>[!NOTE]
> Zasady oparte na ryzyku wymagają licencji na [Azure AD — wersja Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) .

W poniższym przykładzie opisano zasady, które należy utworzyć, aby zapewnić odporną kontrolę dostępu dla użytkownika w celu uzyskania dostępu do aplikacji i zasobów. W tym przykładzie musisz mieć grupę zabezpieczeń **AppUsers** z użytkownikami docelowymi, do których chcesz udzielić dostępu, grupę o nazwie **CoreAdmins** z podstawowymi administratorami oraz grupę o nazwie **EmergencyAccess** z kontami dostępu awaryjnego.
Ten przykładowy zestaw zasad będzie przyznawać wybranym użytkownikom w **AppUsers**, dostęp do wybranych aplikacji, jeśli nawiązują połączenie z zaufanego urządzenia lub zapewniają silne uwierzytelnianie, na przykład MFA. Wyklucza to konta awaryjne i administratorów podstawowych.

**Zestaw zasad ograniczenia urzędu certyfikacji:**

* Zasady 1: Blokuj dostęp do osób poza grupami docelowymi
  * Użytkownicy i grupy: Uwzględnij wszystkich użytkowników. Wyklucz AppUsers, CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: Uwzględnij wszystkie aplikacje
  * Warunki: (brak)
  * Udziel kontroli: blok
* Zasady 2: udzielanie dostępu do AppUsers wymagające uwierzytelniania wieloskładnikowego lub zaufanego urządzenia.
  * Użytkownicy i grupy: Uwzględnij AppUsers. Wyklucz CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: Uwzględnij wszystkie aplikacje
  * Warunki: (brak)
  * Udziel kontroli: Udziel dostępu, wymagaj uwierzytelniania wieloskładnikowego, Wymagaj zgodności urządzenia. Dla wielu kontrolek: Wymagaj jednej z wybranych kontrolek.

### <a name="contingencies-for-user-lockout"></a>Sytuacje awaryjne dla blokady użytkownika

Alternatywnie organizacja może również tworzyć zasady awaryjne. Aby utworzyć zasady awaryjne, należy zdefiniować kryteria kompromisu między ciągłością biznesową, kosztami operacyjnymi, kosztami finansowymi i zagrożeniami bezpieczeństwa. Na przykład można aktywować zasady awaryjne tylko dla podzbioru użytkowników, dla podzestawu aplikacji, dla podzestawu klientów lub z podzestawu lokalizacji. Zasady awaryjne zapewniają administratorom i użytkownikom końcowym dostęp do aplikacji i zasobów, podczas gdy nie zaimplementowano metody zaradczej. Firma Microsoft zaleca włączenie zasad awaryjnych w [trybie tylko do raportowania](../conditional-access/howto-conditional-access-insights-reporting.md) , gdy nie jest używany, co pozwala administratorom monitorować potencjalny wpływ zasad, które należy włączyć.

 Zrozumienie narażenia użytkownika podczas przerw w działaniu pomaga ograniczyć ryzyko i stanowić krytyczną część procesu planowania. W celu utworzenia planu awaryjnego należy najpierw określić następujące wymagania biznesowe organizacji:

1. Od razu Ustal swoje aplikacje o krytycznym znaczeniu: Jakie są aplikacje, do których musisz mieć dostęp, nawet z niższym ryzykiem/stan zabezpieczeń? Utwórz listę tych aplikacji i upewnij się, że inni uczestnicy projektu (Business, Security, prawny i lider) zgadzają się, że jeśli cała kontrola dostępu zostanie przesunięta, te aplikacje nadal muszą być dalej uruchamiane. Najkorzystniej będzie można zakończyć z użyciem kategorii:
   * **Aplikacje o znaczeniu krytycznym kategorii 1** , które nie mogą być niedostępne przez więcej niż kilka minut, na przykład aplikacje, które bezpośrednio wpływają na przychód organizacji.
   * **Kategoria 2 ważne aplikacje** , które firma musi być dostępna w ciągu kilku godzin.
   * **Kategorie 3 aplikacje o niskim priorytecie** , które mogą wytrzymać zakłócenia przez kilka dni.
2. W przypadku aplikacji w kategorii 1 i 2 Firma Microsoft zaleca wstępne zaplanowanie typu dostępu, który ma być dozwolony:
   * Czy chcesz zezwolić na pełny dostęp lub sesję z ograniczeniami, np. ograniczanie pobierania?
   * Czy chcesz zezwolić na dostęp do części aplikacji, ale nie całej aplikacji?
   * Czy chcesz zezwolić na dostęp pracownika do informacji i zablokować dostęp administratora do momentu przywrócenia kontroli dostępu?
3. W przypadku tych aplikacji firma Microsoft zaleca także zaplanowanie, które drogi dostępu zostaną świadomie otwarte i które zostaną zamknięte:
   * Czy chcesz zezwolić na dostęp tylko do przeglądarki i blokować rozbudowani Klienci, którzy mogą zapisywać dane w trybie offline?
   * Czy chcesz zezwolić na dostęp tylko użytkownikom należącym do sieci firmowej i nie blokować użytkowników poza użytkownikami?
   * Czy chcesz zezwolić na dostęp z określonych krajów lub regionów tylko w trakcie przerw w działaniu?
   * Czy chcesz, aby zasady dotyczące zasad awaryjnych, szczególnie w przypadku aplikacji o znaczeniu krytycznym, kończyły się niepowodzeniem lub pomyślnie, jeśli alternatywna kontrola dostępu nie jest dostępna?

#### <a name="microsoft-recommendations"></a>Zalecenia firmy Microsoft

Zasady awaryjnego dostępu warunkowego to **zasady tworzenia kopii zapasowych** , które pomijają mechanizmy uwierzytelniania wieloskładnikowego Azure MFA, oparte na ryzyku lub na urządzeniach. Aby zminimalizować nieoczekiwane zakłócenia podczas włączania zasad awaryjnych, zasady powinny pozostać w trybie tylko do raportowania, gdy nie są używane. Administratorzy mogą monitorować potencjalny wpływ ich zasad awaryjnych za pomocą skoroszytu dostępu warunkowego usługi Insights. Gdy organizacja zdecyduje się na aktywację planu awaryjnego, Administratorzy mogą włączyć zasady i wyłączyć regularne zasady oparte na kontroli.

>[!IMPORTANT]
> Wyłączenie zasad, które wymuszają zabezpieczenia dla użytkowników, nawet tymczasowo, zmniejszy stan bezpieczeństwa w trakcie planowania awaryjnego.

* Skonfiguruj zestaw zasad powrotu, jeśli zakłócenia w jednym typie poświadczeń lub jeden mechanizm kontroli dostępu ma wpływ na dostęp do aplikacji. Skonfiguruj zasady w stanie "tylko raport", który wymaga przyłączenia do domeny jako kontrolki jako kopii zapasowej aktywnej zasady, która wymaga dostawcy MFA innej firmy.
* Ogranicz ryzyko nieprawidłowych uczestników odgadnąć hasła, gdy uwierzytelnianie wieloskładnikowe nie jest wymagane, postępując zgodnie z zasadami [zawartymi w dokumencie wytyczne dotyczące haseł](https://aka.ms/passwordguidance) .
* Wdróż [usługę Azure ad Self-Service Resetowanie hasła (SSPR)](./tutorial-enable-sspr.md) i [ochronę hasłem usługi Azure AD](./howto-password-ban-bad-on-premises-deploy.md) , aby upewnić się, że użytkownicy nie używają wspólnych haseł i postanowień dotyczących zakazu.
* Użyj zasad, które ograniczają dostęp w aplikacjach, jeśli nie zostanie osiągnięty określony poziom uwierzytelniania zamiast po prostu z powrotem do pełnego dostępu. Na przykład:
  * Skonfiguruj zasady tworzenia kopii zapasowych, które wysyłają do programów Exchange i SharePoint żądania ograniczonej sesji.
  * Jeśli Twoja organizacja używa Microsoft Cloud App Security, rozważ powracanie do zasad, które angażują MCAS, a następnie MCAS zezwala na dostęp tylko do odczytu, ale nie do przekazywania.
* Nazwij swoje zasady, aby upewnić się, że można je łatwo znaleźć w trakcie przerw w działaniu. Uwzględnij następujące elementy w nazwie zasad:
  * *Numer etykiety* dla zasad.
  * Tekst do wyświetlenia. Ta zasada dotyczy tylko sytuacji awaryjnych. Na przykład: **Włącz w sytuacjach awaryjnych**
  * *Zakłócenia* , do którego odnosi się. Na przykład: **podczas przerw w** działaniu usługi MFA
  * *Numer sekwencyjny* pokazujący kolejność, w której należy aktywować zasady.
  * *Aplikacje* , do których odnosi się.
  * *Kontrolki* , które mają zastosowanie.
  * *Warunki* , które wymaga.
  
Ten standard nazewnictwa dla zasad awaryjnych będzie następujący: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

Poniższy przykład: **przykładowo zasady dotyczące zasad urzędu certyfikacji w celu przywrócenia dostępu do aplikacji do współpracy o znaczeniu krytycznym**są typowymi zagrożeniami firmowymi. W tym scenariuszu Organizacja zwykle wymaga uwierzytelniania wieloskładnikowego dla wszystkich usług Exchange Online i SharePoint Online, a w takim przypadku zakłócenia jest w tym przypadku dostawcą usługi MFA dla klienta jest przestój (czy usługa Azure MFA, lokalny dostawca MFA, czy MFA innej firmy). Te zasady ograniczają tę awarię przez umożliwienie określonym użytkownikom mającym dostęp do tych aplikacji z zaufanych urządzeń z systemem Windows tylko wtedy, gdy uzyskują oni dostęp do aplikacji z ich zaufanej sieci firmowej. Spowoduje to również Wykluczenie kont awaryjnych i administratorów podstawowych z tych ograniczeń. Użytkownicy dokierowany do usługi Exchange Online i SharePoint Online będą mogli uzyskiwać dostęp do aplikacji, a inni użytkownicy nadal nie będą mieli dostępu do nich z powodu przestoju. Ten przykład wymaga, aby nazwana lokalizacja sieciowa **CorpNetwork** i Grupa zabezpieczeń **ContingencyAccess** z użytkownikami docelowymi, Grupa o nazwie **CoreAdmins** z podstawowymi administratorami i Grupa o nazwie **EmergencyAccess** z kontami dostępu awaryjnego. Sytuacje awaryjne wymagają czterech zasad, aby zapewnić żądany dostęp. 

**Przykład: zasady urzędu certyfikacji awaryjnej umożliwiające przywrócenie dostępu do aplikacji do współpracy o kluczowym znaczeniu.**

* Zasady 1: Wymagaj urządzeń przyłączonych do domeny dla programów Exchange i SharePoint
  * Name: EM001 — Włącz w sytuacjach AWARYJNych: zakłócenia MFA [1/4] — Exchange SharePoint — wymaga sprzężenia hybrydowego usługi Azure AD
  * Użytkownicy i grupy: Uwzględnij ContingencyAccess. Wyklucz CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: Exchange Online i SharePoint Online
  * Warunki: dowolne
  * Udziel kontroli: Wymagaj przyłączenia do domeny
  * Stan: tylko raport
* Zasady 2: Blokuj platformy inne niż Windows
  * Name: EM002 — Włącz w sytuacjach AWARYJNych: zakłócenia MFA [2/4] — Exchange SharePoint — Blokuj dostęp z wyjątkiem systemu Windows
  * Użytkownicy i grupy: Uwzględnij wszystkich użytkowników. Wyklucz CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: Exchange Online i SharePoint Online
  * Warunki: platforma urządzeń obejmuje wszystkie platformy, wykluczanie okien
  * Udziel kontroli: blok
  * Stan: tylko raport
* Zasady 3: Blokuj sieci inne niż CorpNetwork
  * Name: EM003 — Włącz w sytuacjach AWARYJNych: zakłócenia MFA [3/4] — Exchange SharePoint — Blokuj dostęp z wyjątkiem sieci firmowej
  * Użytkownicy i grupy: Uwzględnij wszystkich użytkowników. Wyklucz CoreAdmins i EmergencyAccess
  * Aplikacje w chmurze: Exchange Online i SharePoint Online
  * Warunki: lokalizacje zawierają dowolną lokalizację, wyklucza CorpNetwork
  * Udziel kontroli: blok
  * Stan: tylko raport
* Zasady 4: Blokuj jawnie blokadę EAS
  * Name: EM004 — Włącz w sytuacjach AWARYJNych: zakłócenia MFA [4/4] — program Exchange — Blokuj program EAS dla wszystkich użytkowników
  * Użytkownicy i grupy: Uwzględnij wszystkich użytkowników
  * Aplikacje w chmurze: Dołącz do usługi Exchange Online
  * Warunki: aplikacje klienckie: Exchange Active Sync
  * Udziel kontroli: blok
  * Stan: tylko raport

Kolejność aktywacji:

1. Wyklucz ContingencyAccess, CoreAdmins i EmergencyAccess z istniejących zasad usługi MFA. Sprawdź, czy użytkownik w usłudze ContingencyAccess może uzyskać dostęp do usługi SharePoint Online i Exchange Online.
2. Włączanie zasad 1: weryfikowanie użytkowników na urządzeniach przyłączonych do domeny, które nie znajdują się w grupach wykluczonych, można uzyskać dostęp do usługi Exchange Online i SharePoint Online. Sprawdź, czy użytkownicy w grupie wykluczeń mogą uzyskiwać dostęp do usługi SharePoint Online i programu Exchange z dowolnego urządzenia.
3. Włącz zasady 2: Sprawdź, czy użytkownicy, którzy nie znajdują się w grupie wykluczeń, nie mogą przejść do usługi SharePoint Online i Exchange Online z urządzeń przenośnych. Sprawdź, czy użytkownicy w grupie wykluczeń mogą uzyskiwać dostęp do programu SharePoint i programu Exchange z dowolnego urządzenia (Windows/iOS/Android).
4. Włącz zasady 3: Sprawdź, czy użytkownicy, którzy nie znajdują się w grupach wykluczeń, nie mogą uzyskać dostępu do programu SharePoint i programu Exchange z sieci firmowej nawet z przyłączonym do domeny komputerem. Sprawdź, czy użytkownicy w grupie wykluczeń mogą uzyskiwać dostęp do programu SharePoint i programu Exchange z dowolnej sieci.
5. Włącz zasady 4: Upewnij się, że wszyscy użytkownicy nie mogą uzyskać usługi Exchange Online z natywnych aplikacji poczty na urządzeniach przenośnych.
6. Wyłącz istniejące zasady MFA dla usługi SharePoint Online i Exchange Online.

W tym następnym przykładzie **przykład B — Zasady bezawaryjnego urzędu certyfikacji, aby umożliwić dostęp mobilny do usługi Salesforce**, przywracany jest dostęp do aplikacji biznesowej. W tym scenariuszu klient zazwyczaj wymaga, aby pracownicy sprzedaży mogli uzyskiwać dostęp do usług Salesforce (skonfigurowanych do logowania jednokrotnego przy użyciu usługi Azure AD) z urządzeń przenośnych wyłącznie z poziomu zgodnych urządzeń. Zakłócenia w tym przypadku polega na tym, że występuje problem z ocenami zgodności urządzeń i przestojem, gdy zespół sprzedaży potrzebuje dostępu do usługi Salesforce, aby zamknąć oferty. Te zasady awaryjne umożliwią użytkownikom dostęp do usług Salesforce z urządzenia przenośnego, aby mogli nadal zamykać oferty i nie zakłócać działania firmy. W tym przykładzie **SalesforceContingency** zawiera wszystkich pracowników działu sprzedaży, którzy muszą zachować dostęp, a **SalesAdmins** zawiera niezbędnych administratorów usługi Salesforce.

**Przykład B — Zasady urzędu certyfikacji awaryjnej:**

* Zasady 1: Blokuj wszystkie osoby niebędące członkami zespołu SalesContingency
  * Name: EM001 — Włącz w sytuacjach AWARYJNych: zakłócenia zgodności urządzenia [1/2] — Salesforce — Blokuj wszystkich użytkowników z wyjątkiem SalesforceContingency
  * Użytkownicy i grupy: Uwzględnij wszystkich użytkowników. Wyklucz SalesAdmins i SalesforceContingency
  * Aplikacje w chmurze: Salesforce.
  * Warunki: Brak
  * Udziel kontroli: blok
  * Stan: tylko raport
* Zasady 2: Blokuj zespół ds. sprzedaży na dowolnej platformie niż mobilny (aby zmniejszyć powierzchnię ataku)
  * Name: EM002 — Włącz w sytuacjach AWARYJNych: zakłócenia zgodności urządzenia [2/2]-Salesforce — Blokuj wszystkie platformy z wyjątkiem systemów iOS i Android
  * Użytkownicy i grupy: Uwzględnij SalesforceContingency. Wyklucz SalesAdmins
  * Aplikacje w chmurze: Salesforce
  * Warunki: platforma urządzeń obejmuje wszystkie platformy, Wyklucz systemy iOS i Android
  * Udziel kontroli: blok
  * Stan: tylko raport

Kolejność aktywacji:

1. Wyklucz SalesAdmins i SalesforceContingency z istniejących zasad zgodności urządzeń dla usługi Salesforce. Sprawdź, czy użytkownik w grupie SalesforceContingency może uzyskać dostęp do usługi Salesforce.
2. Włączanie zasad 1: Upewnij się, że użytkownicy spoza SalesContingency nie mogą uzyskać dostępu do usługi Salesforce. Sprawdź, czy użytkownicy w SalesAdmins i SalesforceContingency mogą uzyskiwać dostęp do usługi Salesforce.
3. Włącz zasady 2: Sprawdź, czy użytkownicy w grupie SalesContingency nie mogą uzyskać dostępu do usługi Salesforce ze swoich laptopów systemu Windows/Mac, ale nadal mogą uzyskać dostęp z urządzeń przenośnych. Sprawdź, czy SalesAdmin nadal może uzyskiwać dostęp do usług Salesforce z dowolnego urządzenia.
4. Wyłącz istniejące zasady zgodności urządzeń dla usługi Salesforce.

### <a name="contingencies-for-user-lockout-from-on-prem-resources-nps-extension"></a>Sytuacje awaryjne dla blokady użytkownika z zasobów Premium (rozszerzenie serwera NPS)

W przypadku ochrony dostępu do sieci VPN przy użyciu rozszerzenia serwera NPS usługi Azure MFA należy rozważyć federowanie rozwiązanie sieci VPN jako [aplikację SAML](../manage-apps/view-applications-portal.md) i określić kategorię aplikacji zgodnie z zaleceniami poniżej. 

Jeśli wdrożono rozszerzenie zasad sieciowych usługi Azure AD MFA w celu ochrony zasobów Premium, takich jak sieć VPN i Brama Pulpit zdalny, należy rozważyć z wyprzedzeniem, jeśli wszystko jest gotowe do wyłączenia usługi MFA w przypadku awarii.

W takim przypadku można wyłączyć rozszerzenie serwera NPS, w związku z czym serwer NPS sprawdzi tylko podstawowe uwierzytelnianie i nie będzie wymuszać uwierzytelniania wieloskładnikowego dla użytkowników.

Wyłącz rozszerzenie serwera NPS: 
-   Wyeksportuj klucz rejestru HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters jako kopię zapasową. 
-   Usuń wartości rejestru dla opcji "AuthorizationDLLs" i "ExtensionDLLs", a nie klucza parametrów. 
-   Uruchom ponownie usługę zasad sieciowych (IAS), aby zmiany zaczęły obowiązywać 
-   Ustal, czy podstawowe uwierzytelnianie sieci VPN zakończyło się pomyślnie.

Po odzyskaniu usługi i przygotowaniu do ponownego wymuszania uwierzytelniania MFA w przypadku użytkowników należy włączyć rozszerzenie serwera NPS: 
-   Zaimportuj klucz rejestru z HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters kopii zapasowej 
-   Uruchom ponownie usługę zasad sieciowych (IAS), aby zmiany zaczęły obowiązywać 
-   Ustal, czy uwierzytelnianie podstawowe oraz uwierzytelnianie pomocnicze dla sieci VPN zostały wykonane pomyślnie.
-   Przejrzyj serwer zasad sieciowych i dziennik sieci VPN, aby określić, którzy użytkownicy zostali zalogowani w oknie awaryjnym.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Wdróż synchronizację skrótów haseł, nawet jeśli jesteś federacyjnym lub użyciem uwierzytelniania przekazywanego

Blokada użytkownika może również wystąpić, jeśli są spełnione następujące warunki:

- Twoja organizacja korzysta z rozwiązania do tworzenia tożsamości hybrydowej z uwierzytelnianiem przekazywanym lub federacyjnym.
- Lokalne systemy tożsamości (takie jak Active Directory, AD FS lub składnik zależny) są niedostępne. 
 
Aby bardziej odporne na błędy, organizacja powinna [włączyć funkcję synchronizacji skrótów haseł](../hybrid/choose-ad-authn.md), ponieważ umożliwia [przełączenie na korzystanie z synchronizacji skrótów haseł](../hybrid/plan-connect-user-signin.md) , jeśli lokalne systemy tożsamości nie działają.

#### <a name="microsoft-recommendations"></a>Zalecenia firmy Microsoft
 Włącz synchronizację skrótów haseł przy użyciu kreatora Azure AD Connect, niezależnie od tego, czy organizacja używa uwierzytelniania federacyjnego, czy przekazującego.

>[!IMPORTANT]
> Nie jest wymagane przekonwertowanie użytkowników z federacyjnego na uwierzytelnianie zarządzane, aby można było używać synchronizacji skrótów haseł.

## <a name="during-a-disruption"></a>W trakcie przerwy

Jeśli zdecydujesz się na wdrożenie planu zaradczego, będzie można automatycznie przetrwać pojedyncze zakłócenia kontroli dostępu. Jednak jeśli wybrano opcję utworzenia planu awaryjnego, podczas przerw w kontroli dostępu będzie można aktywować zasady awaryjne:

1. Włącz zasady awaryjne, które przydzielą użytkownikom skierowanym dostęp do określonych aplikacji, z określonych sieci.
2. Wyłącz zasady regularnej kontroli.

### <a name="microsoft-recommendations"></a>Zalecenia firmy Microsoft

W zależności od tego, które środki zaradcze lub sytuacje awaryjne są używane w trakcie przerw w działaniu, organizacja może udzielić dostępu tylko za pomocą hasła. Żadne zabezpieczenia nie są istotnym zagrożeniem bezpieczeństwa, które należy starannie odważyć. Organizacje muszą:

1. W ramach strategii kontroli zmian należy udokumentować każdą zmianę i poprzedni stan, aby móc wycofać wszystkie sytuacje awaryjne, które zostały wdrożone natychmiast po w pełni funkcjonalnej kontroli dostępu.
2. Załóżmy, że złośliwe osoby będą próbować zebrać hasła przy użyciu napylania hasła lub ataków wyłudzania podczas wyłączania usługi MFA. W przypadku nieprawidłowych aktorów mogą już znajdować się hasła, które wcześniej nie miały dostępu do żadnych zasobów, które mogą być podejmowane w tym oknie. W przypadku użytkowników o znaczeniu krytycznym, takich jak kierownicy, można częściowo wyeliminować to zagrożenie przez zresetowanie haseł przed wyłączeniem uwierzytelniania MFA.
3. Archiwizuj wszystkie działania związane z logowaniem, aby identyfikować, kto ma dostęp do tego, co w czasie została wyłączona.
4. [Klasyfikacja wszystkie wykrycia ryzyka zgłoszone](../reports-monitoring/concept-sign-ins.md) w tym oknie.

## <a name="after-a-disruption"></a>Po przerwie

Cofnij zmiany wprowadzone w ramach aktywowanego planu awaryjnego po przywróceniu usługi, która spowodowała zakłócenie. 

1. Włącz zasady regularne
2. Wyłącz zasady awaryjne z powrotem w trybie tylko do raportowania. 
3. Wycofaj wszystkie inne zmiany wprowadzone i udokumentowane podczas przerw w działaniu.
4. Jeśli używasz konta dostępu awaryjnego, pamiętaj, aby ponownie wygenerować poświadczenia i fizycznie zabezpieczyć szczegóły nowych poświadczeń w ramach procedur konta dostępu awaryjnego.
5. Kontynuuj [Klasyfikacja wszystkie wykrycia ryzyka zgłoszone](../reports-monitoring/concept-sign-ins.md) po przerwie dla podejrzanych działań.
6. Odwołaj wszystkie tokeny odświeżania, które zostały wystawione [przy użyciu programu PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) , aby wskazać zestaw użytkowników. Odwoływanie wszystkich tokenów odświeżania jest ważne dla kont uprzywilejowanych używanych w trakcie przerw w działaniu, co spowoduje wymuszenie ponownego uwierzytelnienia i spełnienia kontroli przywróconych zasad.

## <a name="emergency-options"></a>Opcje awaryjne

 W przypadku sytuacji awaryjnej i organizacja nie wdrożyła wcześniej planu ograniczenia lub gotowości, następnie postępuj zgodnie z zaleceniami w sekcji [sytuacje awaryjne dla blokady użytkownika](#contingencies-for-user-lockout) , jeśli już używają zasad dostępu warunkowego do wymuszania uwierzytelniania MFA.
Jeśli Twoja organizacja korzysta ze starszych zasad usługi MFA dla użytkownika, możesz rozważyć następujące alternatywy:

1. W przypadku wychodzącego adresu IP sieci firmowej można je dodać jako zaufane adresy IP, aby umożliwić uwierzytelnianie tylko w sieci firmowej.
   1. Jeśli nie masz spisu wychodzących adresów IP lub musisz włączyć dostęp wewnątrz i na zewnątrz sieci firmowej, możesz dodać całą przestrzeń adresową IPv4 jako zaufane adresy IP, określając 0.0.0.0/1 i 128.0.0.0/1.

>[!IMPORTANT]
 > W przypadku rozszerzenia zaufanych adresów IP w celu odblokowania dostępu, wykrywanie ryzyka skojarzone z adresami IP (na przykład niemożliwa podróż lub nieznane lokalizacje) nie zostaną wygenerowane.

>[!NOTE]
 > Konfigurowanie [zaufanych adresów IP](./howto-mfa-mfasettings.md) dla usługi Azure MFA jest możliwe tylko przy użyciu [licencji Azure AD — wersja Premium](./concept-mfa-licensing.md).

## <a name="learn-more"></a>Więcej tutaj

* [Dokumentacja uwierzytelniania usługi Azure AD](./howto-mfaserver-iis.md)
* [Zarządzanie kontami administracyjnymi dostępu awaryjnego w usłudze Azure AD](../roles/security-emergency-access.md)
* [konfigurowanie nazwanych lokalizacji w usłudze Azure Active Directory](../reports-monitoring/quickstart-configure-named-locations.md)
  * [Set-MsolDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Jak skonfigurować dołączone urządzenia hybrydowe Azure Active Directory](../devices/hybrid-azuread-join-plan.md)
* [Przewodnik wdrażania funkcji Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Wskazówki dotyczące haseł — Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Jakie są warunki Azure Active Directory dostępu warunkowego?](../conditional-access/concept-conditional-access-conditions.md)
* [Co to są kontrole dostępu w Azure Active Directory dostęp warunkowy?](../conditional-access/controls.md)
* [Co to jest tryb tylko do raportowania dostępu warunkowego?](../conditional-access/concept-conditional-access-report-only.md)